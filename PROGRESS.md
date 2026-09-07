# mirror 进度索引

## 已落盘
| 文件 | 内容 |
|---|---|
| METHOD.md | 抓取方法+course_param 格式说明 |
| data/courses/10805.json | 京都芝1600（京都金杯）完整分段/坡道/相位 |
| data/courses/10611.json | 東京沙1600（フェブラリーS）——与京都同距离但分段完全不同 |
| data/skills/sample-page1.json | skill_data RSC JSON 样例（effectPatterns/conditions 结构）|
| data/characters/100503.json | 白フジ角色页（进化技全开条件/目标赛程/best_courses）|

## 已验证的结构结论
1. 赛事详情页（/race/races/<race_id>）JSON 内嵌 CourseParam 原始数组，race_id 与 mdb race 表同键
2. tracks 无单赛道直链；course 数据从赛事页提取，按 raceTrackCourseId 去重
3. 同距离同场地不同地面 = 完全不同分段（10611 vs 10805 对照实证）
4. skills 列表页 RSC payload 内嵌完整 skill_data（含条件 DSL 结构化形式）
5. 角色数据在 /cards/<id>（育成ウマ娘一覧 266 种）；/characters/<id> 无直链响应

## 待抓取清单
- [ ] races：317 场赛事详情 → data/races/<id>.json（course 已含在 race JSON 内，或单独 data/courses/）
- [ ] courses：按 raceTrackCourseId 去重后 140 条 → data/courses/<id>.json
- [ ] skills：分批抓列表页（每页约 50 条，2000+ 条全量）→ data/skills/
- [ ] characters：266 张育成卡 → data/characters/<id>.json
- [ ] 相性一覧 / スキル効果ランキング / 各コースの特効キャラ（派生工具，低优先）
