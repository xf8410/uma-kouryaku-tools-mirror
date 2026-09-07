# コーナーロス計算機 — 原理与数据源说明

> 来源：Google Sheets `1G5rxdj_2Abgba1heRFmxA4WTYRhv-f_LpFgBLmEuO0Q`（ver.1.0，2025-06-05，作者 @pixy_for_ever）
> 用途：输入终盘3-4コーナー离内栏距离 → 算出到最终直线入口为止的膨出距离损失（米/バ身/相当于几个速度技能）

## 算出原理（几何近似）
- 把コーナー看作**椭圆**：内栏沿线、目标马、比较马三条椭圆
- 圆周差 = 膨出损失
- 4コーナー→最终直线：取圆周差的 **1/4**
- 3コーナー→最终直线：圆周差的 **1/2** 或两椭圆圆周差 1/4 之和

## 数据源（dateset sheet 注明）
- **3角~4角距離：引用 u-tools（kouryaku.tools/race/tracks）的終盤3コーナー開始~4コーナー終了米数**
- 3角~4角直径/2：谷歌地图航空照片量测 3コーナー开始点与 4コーナー终点点连线 ÷2（JRA 影像补 A コース内栏）
- もう一方の径：用 casio keisan 椭圆周长反解另一径
- 假设 3/4 コーナー左右对称（中山芝外回 3角>4角 等极端不对称不适用）

## 结论
- 该表依赖 u-tools 的弯道数据 → 印证我们 course_data.json 的 corners 字段即可替代
- 膨出损失几何模型：ΔC = 2π(Δr)，4弯道出口取 1/4 —— 可以直接进模拟器的「外回膨らみ损失」模块
- 本表无逐赛道数值表（dateset 里内嵌），核心方法已记录

## 附：スキルシミュレーター ver4.9.1 作者参考链接
- 使用说明 note：https://note.com/uma_analysis66/n/nf28500c27b13
- u-tools：https://ウマ娘.攻略.tools/
- レースエミュレータ移植版：https://mee1080.github.io/umasim/race/（注意：umasim 路径，非 uma-musume-race-emu！）
