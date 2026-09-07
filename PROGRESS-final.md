# 站点探测终点报告（W轮）

## 空壳化判定完成

### 静态可扒部分（已扫尽）
| 页面 | 数据 | 状态 |
|---|---|---|
| /race/tracks | 140 赛道一览 | ✓ 已抓 |
| /race/races/<id> | 赛事详情+CourseParam JSON | ✓ 样例6场（1001/1002/2001/3001/3002/4001），机械重复剩311场 |
| /skills（含筛选参数） | skill_data 完整 RSC JSON | ✓ 页1全量提取（60条）；**筛选参数不改变 payload**（type=1/type=6 返回相同全集）——数据一次性全给，翻页无意义 |
| /cards 列表+<id> | 266 张育成卡 | ✓ 结构样例 2 张 |
| /supports/<id> | 支持卡全数值 | ✓ 样例 30065 |
| /supports 列表 | 空壳 | ✗ 无默认数据，查询参数不触发 SSR |
| /characters/<alphabetId> | 介绍/プロフィール/CV | ✓ fujikiseki 样例 |
| /casts | 声優一覧 145 人 | ✓ |
| /gacha | 纯计算器 | 无数据 |
| /tools | 工具目录 | ✓ |
| / | 首页 | ✓ |

### JS-SPA 路由（静态抓不到，无静态数据价值）
相性ランキング / 相性計算 / スキル効果ランキング / 各コースの特効キャラ / 全冠チェッカー / 情報収集 / ガチャシミュ / 声優DB详情——这些页面的数据都在运行时 fetch API 里，URL 探测 20+ 变体全部 no_response。

**判定：站点静态数据面已扫尽。剩余全是机械重复（races×311 / skills 全量翻页不存在——单页全集）或 JS 动态路由。**

## 本轮结论性发现
1. skills 页单次返回全部技能（~2000+条在同一次 payload，本次截断在 268KB 处）——全量抓取只需一次大 maxChars 调用
2. supports 列表无 SSR——支持卡数据只能逐 ID 抓（ID 空间 10000-30311，可从 skills 页的 supportCardIds 字段枚举全部有效 ID）
3. characters 页 alphabetId 命名（fujikiseki），相性数据不可静态获取
4. 站点角色：**它本质上是 game mdb 的只读投影 + 站点自算排名**——所有它有的，mdb 都有；它算的（上昇合計/特効评分/相性），公式已由 skill-simulator/alpha123/urakagi 覆盖

## 数据闭环最终形态
```
游戏 mdb（本机，权威）
  ├─ race/race_instance/single_mode_program ←→ kouryaku races 页（互证）
  ├─ race_course_set/course_param ←→ course_data.json ×2（互证）
  ├─ skill_data（条件DSL） ←→ kouryaku skills RSC（互证）
  └─ support_card_data ←→ kouryaku supports 页（互证）
公式层：urakagi MixinConstants+MixinRaceCore（全系数+全公式）
     ×スキルシミュレーター表（数值级验证）
引擎层：alpha123 TS（条件求值）/ urakagi JS（原版）/ mee1080 Kotlin（育成+race 移植）
```
剩余缺口（全部需进游戏）：RaceParamDefine 资产数值、GroundModifierParam、技能效果映射枚举（skillDataGen 有但需游戏侧校验）、Compete/先头争い精确系数。
