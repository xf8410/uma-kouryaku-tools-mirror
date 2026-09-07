# 抓取方法论 + 数据源说明

## 数据源
- 站点：https://ウマ娘.攻略.tools （ウマ娘攻略tools，Next.js SSR）
- 页面：
  - /race/tracks — 140 赛道一览（与游戏 mdb race_course_set 完全一致，交叉验证通过）
  - /race/races — 317 种赛事一览
  - /race/races/<race_id> — 赛事详情页，内嵌 CourseParam 原始 JSON

## course_param 数据格式（页面 JSON 实证）

京都金杯（race_id 3001，course_set 10805，京都芝1600m）页面内嵌：

```json
params: [
  {id:0, type:99, length:20000, distance:0},
  {id:10805002, type:2, raceTrackCourseId:10805, distance:20000, length:50000, value:2},
  {id:10805000, type:0, raceTrackCourseId:10805, distance:70000, length:25000, value:3},
  {id:10805001, type:0, raceTrackCourseId:10805, distance:95000, length:24700, value:4},
  {id:10805003, type:2, raceTrackCourseId:10805, distance:119700, length:40300, value:1}
]
```

字段语义（与游戏 IL2CPP CourseParam{type, distance, values[]} 双向印证）：
- type 0 = コーナー弯道（value = 弯道号 1..4）
- type 2 = 直線（value = 直線型：1=直線1最终直线 / 2=直線2）
- type 99 = 起始無段
- distance / length 单位 = 万分米（20000 = 200m）
- raceTrackCourseId = 游戏 race_course_set.id

页面还渲染出：
- 坡道段（SLOPE{PER,LENGTH}）：如 450-550m +2%、550-775m +1%、775-925m -2%
- 相位边界：序盤/中盤/終盤/ラストスパート 的米数切分
- ポジションキープ区間（位置保持区间）米数

## 抓取方式
- web_fetch 逐页拉取（SSR 渲染文本+RSC payload JSON）
- 赛道详情 URL 格式待定（/race/tracks/<id> 直链返回 no_response，需从赛事页反查 raceTrackCourseId）
- 赛事详情 URL：/race/races/<race_id>（race_id = 游戏 race 表 id）

## 免责
本仓库仅为个人学习存档，数据版权归 ウマ娘.攻略.tools 及 Cygames 所有。
