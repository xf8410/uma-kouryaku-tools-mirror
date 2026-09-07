# mirror/uma-skill-tools — 真正的模拟器引擎（alpha123）

> 来源：https://github.com/alpha123/uma-skill-tools （GPL-3.0，70 stars，最后更新 2026-06-25）
> 这是 umalator（alpha123.github.io/uma-tools/umalator-global/）的计算内核，也是 kouryaku.tools 在 tools 页介绍的「レースエミュレータ移植版」（mee1080）的血统源头。
> kouryaku.tools 本身**没有**模拟器，只有数据展示页。

## 为什么这是 jackpot

1. **`data/course_data.json`（67KB）= 全部 138 条赛道的完整几何**——正是我们在 kouryaku.tools 逐页 scrape 的东西，且已与攻略站双向验证一致：
   - 10805 京都芝1600：slopes {450,100,200%e}{550,225,1%}{775,150,-2%} ↔ 攻略站 450-550/550-775/775-925 ✓
   - 10810 京都芝3000：双坡组 11/111/336 + 1850/1950/2175 ✓
   - 10701 中京芝1200：{100,775,-1%}{875,100,2%} ✓（含 laneMax=14400、finishTimeMin/Max，攻略站没有的）
   - 10611 東京沙1600：slope 15000 = 1.5% ✓（攻略站 1.5%）
2. **含 corners/straights/slopes/laneMax/finishTime/course 全字段**，键 = race_course_set.id
3. 附带 RaceSolver.ts（31KB 数值积分内核）/ RaceSolverBuilder.ts / ActivationConditions.ts（54KB 条件求值）/ ConditionParser.ts / HpPolicy.ts —— 条件 DSL 的完整解析器，与我们的 skill_data 条件变量表完全对应

## 本目录已复制的核心文件（GPL-3.0，保留原版权声明）

| 文件 | 说明 |
|---|---|
| CourseData.ts | CourseData 结构 + **相位公式** + **赛道补正公式**（见下）|
| RaceParameters.ts | Mood/GroundCondition/Weather/Season/Grade 枚举 |

其余大文件（RaceSolver.ts 31KB / RaceSolverBuilder.ts 27KB / ActivationConditions.ts 54KB / ActivationSamplePolicy.ts 12KB / HpPolicy.ts 5KB / ConditionParser.ts 8KB）**未逐字复制**，请直接：
```
git clone https://github.com/alpha123/uma-skill-tools.git
```
引擎锁定版本：uma-tools 主仓 submodule 指向 commit `6ba5ca07fcfdca96d9fc03d58a50a751bcebddd2`。
course_data.json 精确 blob：`bfd12a069f03087804bc57ebde5500f22032d2a4`（67,295 bytes）。

## ★★ 两大公式实锤（双源交叉验证）

### 相位公式（phase boundaries）
```
phase 0: 0          ~ distance × 1/6
phase 1: distance×1/6 ~ distance × 2/3
phase 2: distance×2/3 ~ distance × 5/6
phase 3: distance×5/6 ~ distance
```
- 验证：1600m → 267 / 1067 / 1333，与 kouryaku.tools 每条赛道展示的 序盤/中盤/終盤/ラストスパート 完全一致
- **中盤起点 = 1/6，結盤起点 = 2/3，ラストスパート起点 = 5/6** —— 全赛道通用公式，无需每赛道硬编码

### 赛道补正公式（courseSpeedModifier，race_course_set_status 语义破解！）
```
modifier = 1 + Σ( (1 + floor(min(stat,901)/300.01)) × 0.05 ) / len(courseSetStatus)
```
- courseSetStatus = 该赛道吃补正的属性阈值数组（1=Speed 2=Stamina 3=Power 4=Guts 5=Wiz，0=无）
- 单属性最大 +20%（stat≥901 时 floor=3 → (1+3)×0.05）
- 与 mdb race_course_set_status 完全对应：10805=[1]（Speed）↔ status id1=(1,0)；10810=[3,5]（Power,Wiz）↔ status id9=(3,5)；10506 中山2500=[2,4] ↔ id8=(2,4) ✓
- kouryaku.tools 的「補正ステータス: スピード」字段就是这个表的展示——**之前"语义未定案"的 race_course_set_status 彻底破解**
- 这解释了"赛马娘赛道补正最高+20%"的社区共识，且给出了精确公式（含 901 封顶、300.01 除数的防浮点细节）

## ★ 枚举勘误（修正我们 mdb 分析的猜测）

| 枚举 | 实锤值 | 修正 |
|---|---|---|
| Grade | G1=100, G2=200, G3=300, OP=400, PreOP=700, Maiden(未勝利)=800, Debut(デビュー)=900, Daily=999 | ~~100=PreOP~~ → **100=G1**（skill 条件 GⅠ苦手 grade==100 也吻合）|
| Season | Spring=1, Summer=2, Autumn=3, Winter=4, **Sakura=5** | 解释春ウマ娘条件 `season==1@season==5`（春=樱花季）|
| GroundCondition | Good=1, Yielding(稍重), Soft(重), Heavy(不良) | 与我们 1/2/3/4 一致 |
| Mood | -2..2（絶不調~絶好調）| 与 race_motivation_rate 5 档一致 |

## 与游戏 IL2CPP 结构的对应

| uma-skill-tools | 游戏内（我们挖掘的）|
|---|---|
| CourseData.slopes{start,length,slope} | CourseSlope{StartDistance,EndDistance(=start+length),SlopePer(slope/10000)} |
| CourseData.corners{start,length} | CourseCorner{StartDistance,EndDistance}（cornerNumber 需按出现序推）|
| CourseData.straights{start,end,frontType} | CourseStraight{Start,End,StraightFrontType}（frontType 1=Front 2=AcrossFront? 3=?）|
| phaseStart/End | RacePhaseCalculator |
| courseSpeedModifier | RaceUtil/CourseSetStatus 消费链 |

## GPL-3.0 合规说明
本目录复制及引用遵守 GNU GPL v3（LICENSE 35KB 见原仓库）。衍生模拟器若分发需同以 GPL-3.0 开源。
