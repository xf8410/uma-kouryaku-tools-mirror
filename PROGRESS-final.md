# 站点探测终点报告（W轮）+ 诚实审计修正（X轮 09-07）

## ⚠ 诚实审计（应"我不太相信"质询，按 verification-protocol 实查）

### 仓库实际内容（github_get_tree 实查 92f752d）
- data/ JSON **共 10 个**：characters×1 / courses×3(10611,10805,10810) / races×3(1002,2001,3002) / skills×2 / supports×1
- mirror/ 9 个（其中原样复制的源码仅 3 个：MixinConstants.vue、CourseData.ts、RaceParameters.ts）
- 瑕疵：races/1001、3001、4001 无独立文件（数据并入 courses/*.json 的 race 块）；races/1002、2001、3002 却是独立文件——**命名不一致**

### 实际抓取率
- 站点可静态页面总量 ≈ 317 races + 266 cards + ~250 supports + 145 casts + skills/tracks/杂项 ≈ **~1000 页**
- 本会话 web_fetch 实际抓到的唯一页面：**~21 个**
- **全量镜像完成度 ≈ 2%（抽样验证性质，非全量）**

### "空壳化"结论的准确表述
- ✅ 成立：**结构层**——数据类型已枚举尽，每类用样例验证"站点=mdb只读投影+自算评分"；JS-SPA 路由用 20+ URL 变体排除
- ❌ 不成立：**数据层全量**——317 races、全量 skills(~2000条)、266 cards、~250 supports **未全量落盘**；PROGRESS.md 待抓清单仍有效
- ❌ 两张 Google Sheets 只落了**提取笔记**（mirror/*.md），未存原始 CSV

### 技术性阻塞（如实）
1. **skills 全量 payload 无法经 web_fetch 完整落盘**：单次返回 ~268KB > web_fetch 100KB 上限，无分页/切片参数（category 筛选实测不改变 payload）→ 只能拿前 ~100KB。**但同数据 mdb skill_data(2167行) 已持有**，非真缺口
2. **大引擎文件（course_data.json 67/128KB、skillDataGen.js 628KB、MixinRaceCore.vue 56KB 全文）未复制进 mirror**：超单次读写边界 → 正确姿势 = 用户侧 `git clone`（blob SHA 已记录在 README）
3. races 全量（311场×~70KB）≈350+ 次抓取，几何部分与 course_data.json 冗余、元数据部分与 mdb 冗余——**性价比极低，未执行**

### 修正后的真实进度
| 层 | 状态 |
|---|---|
| 结构/类型枚举 | ✅ 完成 |
| 公式层（速度/HP/系数） | ✅ 完成（双源互证，源码级） |
| 引擎获取 | ⚠ 文档+3小文件已复制；**大文件需 clone**（blob SHA 已记录） |
| Sheets | ⚠ 笔记已落，原始 CSV 未存 |
| 数据全量镜像 | ❌ ≈2%（样例级） |

## W轮原报告（保留，仅供对照）
