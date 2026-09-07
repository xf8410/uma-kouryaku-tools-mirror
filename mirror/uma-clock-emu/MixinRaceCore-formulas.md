# MixinRaceCore.vue 主循环全公式（urakagi/uma-clock-emu，blob e7e5ed1f，56KB）

> 本文件为核心公式提取笔记；完整源码请 clone https://github.com/urakagi/uma-clock-emu （GPL 类，以仓库 LICENSE 为准）

## 基础速度公式 ★
```
baseSpeed = 20 - (distance - 2000) / 1000
```
- 2200m → 19.8（与スキルシミュレーター表「基準速度19.800」互证！）
- 1200m → 20.8 / 3600m → 16.4

## 目标速度 targetSpeed（优先级）
1. sp <= 0 → vMin（枯竭）
2. currentSpeed < v0 → v0 = 0.85 × baseSpeed（起步段）
3. spurt 参数已定且即将到终点 → spurtParameters.speed
4. phase 0/1 → baseSpeed × styleSpeedCoef[style][phase]
5. **phase 2/3 → baseSpeed × styleSpeedCoef[style][2] + √(modifiedSpeed/500) × distanceFitSpeedCoef + (modifiedGuts×450)^0.597 × 0.0001**
6. + baseSpeed × sectionTargetSpeedRandoms[section]（24 分区随机）
7. × progressPositionKeeping()（PDM 压速）
8. 上坡：-= |slope|×200/power；下坡モード：+= |slope|/10 + 0.3
9. + 持续中技能的 targetSpeed / speedWithDecel / speed(负)

## 加速度 acceleration
```
c = 上坡中 ? 0.0004 : 0.0006
a = c × √(500×modifiedPower) × styleAccelerateCoef[style][phase] × surfaceFitAccelerateCoef × distanceFitAccelerateCoef
スタート时 + 24
```

## 减速度 deceleration
- sp<=0: -1.2 / phase0: -1.2 / phase1: -0.8 / phase2+: -1.0

## maxSpurtSpeed ★
```
(baseSpeed × (styleSpeedCoef[style][2] + 0.01) + √(speed/500) × distFit) × 1.05
+ √(500×speed) × distFit × 0.002
+ (450×guts)^0.597 × 0.0001
```

## HP（sp）体系 ★
- **spMax = distance + 0.8 × stamina × styleSpCoef[style]**
- **spurtSpCoef = 1 + 200/√(600×guts)**（phase≥2 消耗系数）
- **consumePerSecond = 20 × spConsumptionCoef[surface][condition] × (v - baseSpeed + 12)² / 144**（phase≥2 再 × spurtSpCoef）
- 下り坂モード中消耗 ×0.4；掛かり中 ×1.6；先头争い中 ×1.4（+掛かり ×3.6 / 大逃 ×3.5 / 大逃+掛かり ×7.7）

## 下り坂加速モード（下坡加速）
- 触发概率 = wiz × 0.0004（每整秒判定）；退出概率 0.2/秒
- 速度 += |slope|/10 + 0.3；期望模式 expectedDownSlopeCoef = 4/(4 + 1/(wiz×0.0004))

## 掛かり（temptation/かかり）
- 发动率 = (6.5/log10(0.1×modifiedWisdom+1))²（%）
- 触发 section：1+floor(rand×8)（分区随机）
- 结束：持续 3/6/9 秒各 55% 概率，12s 必结束；多余消耗 temptationWaste 记录

## 技能发动率 / 其他
- **skillActivateRate = 100 - 9000/wisdom**（智力对白技能发动率）
- セクション制：getSection = position×24/courseLength（24 分区）
- PDM（位置保持）作用于前 45% 区间（chartMiddle=0.45）
- status 超过 1200 减半：exceed = stat>1200 ? 1200+(stat-1200)/2 : stat
- modifiedWisdom 额外 ×styleFitCoef（脚質適性作用在智力上）
- スタート遅延 0~0.1s 随机（expected 模式 0.05s）
- 天气概率：晴 57.5% / 曇 30% / 雨 12.5%；冬雨 1/3 变雪；季节：春 40%、夏秋各 20%、冬 20%
- spurt 不足时：0.1 步长从 maxSpurtSpeed 向 v3 搜索可用速度，按 15+0.05×wiz 概率采样首个可行解
- raceTime 换算：displayTime = raceTime × 1.18，限制在 finishTimeMin/Max ±1s 随机内

## spurt 几何（解方程）
- calcSpurtDistance(v) 与 calcRequiredSp(v) 基于 (v-baseSpeed+12)²/144/v 二次式，反解"以速度 v 冲刺可达的距离"
- maxSpurt 判定：spurtDistance ≥ 剩余距离 且 位置 ≤ 2/3×全长+5m

## 与スキルシミュレーター表对照
| 项 | 本引擎 | スキル表(阪神2200) | 一致 |
|---|---|---|---|
| 基準速度 | 20-(2200-2000)/1000=19.8 | 19.800 | ✓ |
| 序盤目標(先马) | 19.8×0.978=19.36 | 19.364 | ✓ |
| 中盤目標(先马) | 19.8×0.991=19.62 | 19.622 | ✓ |
| スパート | maxSpurtSpeed 公式 | 24.974 | 需代入具体素质核 |
| 序盤加速度 | 0.0006√(500×power)×... | 0.505 | 核对通过 |
| PDM解除 | 0.45×全长=990m | 91m(PDM解除地点) | ⚠ 语义不同（表指解除后9m余量？待核）|
