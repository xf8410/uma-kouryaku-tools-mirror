# uma-clock-emu — 模拟器祖师爷（系数全表）

> 来源：https://github.com/urakagi/uma-clock-emu （JavaScript, 33 stars, Romulus Urakagi Tsai @urakagi）
> 血统：**race.wf-calc.net（砂井裏鍵？非也——这是 urakagi 的时钟模拟器）→ mee1080/umasim（Kotlin 移植+育成模拟，AGPL-3.0，data/ 目录含 skill_data.txt 全数据）**
> mee1080 information.md 原话："A significant portion of the race mechanics (/race directory) is ported from uma-clock-emu by Romulus Urakagi Tsai (@urakagi)"
> 本目录文件原样复制（GPL/MIT 以原仓库为准）。

## 核心资产清单

| 文件 | 大小 | 内容 |
|---|---|---|
| **src/components/data/MixinConstants.vue** | 2.9KB | **全系数表（已原样复制到本目录）** |
| src/components/data/course_data.json | **128KB** | 赛道数据（比 alpha123 的 67KB 更全！blob 8988f1256f446a256e44be495cd0699cc470922a）|
| src/components/data/skillDataGen.js | **628KB** | 生成的技能数据 |
| src/components/data/skillData.js | 207KB | 技能数据 |
| src/components/data/MixinSkills.vue | 41KB | 技能效果实现 |
| src/components/MixinRaceCore.vue | **56KB** | 竞速主循环（速度/HP数值积分）|
| src/components/MixinPositionKeeping.vue | 3.1KB | 位置保持（PDM）实现 |
| src/components/MixinKua.vue | 3.1KB | 挂かり（かかり）实现 |
| src/components/CalculatedValues.vue | 3.2KB | 计算值展示（HP换算公式）|
| src/components/CourseInfo.vue | 3.2KB | 赛道信息（再次验证相位=distance/6, 2/3）|

## ★ MixinConstants.vue 全系数表（本目录已复制原文）

### 基础
- startSpeed = 3.0, maxSpeed = 30.0, frameLength = 1/FRAME_PER_SECOND

### condCoef（やる気/mood，0=絶好調…4=不調）
`0:1.04, 1:1.02, 2:1.0, 3:0.98, 4:0.96`

### 场地惩罚（万分位）
- surfaceSpeedModify：芝/沙 不良(4) 均 -50（即 -0.5%？注意单位待核——其它全 0）
- surfacePowerModify：
  - 芝：重(2) -50, 不良(4) -50（稍重0！）
  - 沙：良(0) **-100**, 稍重(1) **-100**, 重(2) -50, 不良(3) -100

### 适性系数（S~G）
| 系数 | S | A | B | C | D | E | F | G |
|---|---|---|---|---|---|---|---|---|
| styleFitCoef（脚質，作用于HP）| 1.1 | 1.0 | 0.85 | 0.75 | 0.6 | 0.4 | 0.2 | 0.1 |
| distanceFitSpeedCoef（距離速度）| 1.05 | 1.0 | 0.9 | 0.8 | 0.6 | 0.4 | 0.2 | 0.1 |
| distanceFitAccelerateCoef | 1.0 | 1.0 | 1.0 | 1.0 | 1.0 | 0.6 | 0.5 | 0.4 |
| surfaceFitAccelerateCoef（バ場加速度）| 1.05 | 1.0 | 0.9 | 0.8 | 0.7 | 0.5 | 0.3 | 0.1 |

### styleSpCoef（脚質 HP 消耗系数）
逃0.95 / 先**0.89** / 差1.0 / 追0.995 / 大逃(ONIGE)0.86

### ★ styleSpeedCoef（脚質 × 相位 速度系数）
| 脚質 | phase0(序盤) | phase1(中盤) | phase2(終盤) |
|---|---|---|---|
| 逃(1) | 1.000 | 0.980 | 0.962 |
| 先(2) | 0.978 | 0.991 | 0.975 |
| 差(3) | 0.938 | 0.998 | 0.994 |
| 追(4) | 0.931 | 1.000 | 1.000 |
| 大逃 | 1.063 | 0.962 | 0.950 |

### ★ styleAccelerateCoef（脚質 × 相位 加速度系数）
| 脚質 | 0 | 1 | 2 | 3 |
|---|---|---|---|---|
| 逃 | 1.000 | 1.000 | 0.996 | 0.996 |
| 先 | 0.985 | 1.000 | 0.996 | 0.996 |
| 差 | 0.975 | 1.000 | 1.000 | 1.000 |
| 追 | 0.945 | 1.000 | 0.997 | 0.997 |
| 大逃 | **1.170** | 0.940 | 0.956 | 0.956 |

### spConsumptionCoef（バ場状态 HP 消耗）
- 芝：良1.0 / 稍重1.0 / 重**1.02** / 不良**1.02**
- 沙：良1.0 / 稍重1.0 / 重**1.01** / 不良**1.02**

## CalculatedValues.vue 公式
- 等价耐力：`equalStamina = spMax × value/10000 / 0.8 / styleSpCoef[style]`
  - rareHP=550（金回復?）通常HP=150 换算
- 展示 v0/a0（出发）、v1/a1（序盤）、v2/a2（中盤）、v3/a3（終盤）、maxSpurtSpeed、spurtSpCoef、skillActivateRate、temptationRate（挂かり率）
- 先头争い（leadCompetition）: targetSpeed/duration/advantage=duration×speed/2.5/leadCompetitionUsage

## 相位公式三度验证
CourseInfo.vue 直接渲染 `(distance/6).toFixed(0)` 与 `(distance*2/3)` 作为相位分隔符——urakagi/alpha123/スキルシミュ表/kouryaku **四源一致**。

## 与其他源的关系
- alpha123/uma-skill-tools（TS）：条件求值最强（ActivationConditions 54KB），course_data 67KB
- urakagi/uma-clock-emu（JS）：**系数全表+course_data 128KB 版**，是 mee1080 移植源头
- mee1080/umasim（Kotlin, AGPL）：育成模拟+race 移植，data/ 有 skill_data.txt/chara.txt/support_card.txt 全套，docs/ 是 GitHub Pages 部署（mee1080.github.io/umasim）
