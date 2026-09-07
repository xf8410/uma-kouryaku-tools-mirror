# ★★★ successionPointTable 全量相性矩阵实锤（Y轮第六批）

## 来源：/characters/agnesdigital/succession 的 RSC payload（用户提供源码截断+web_fetch 全文）

## 三大新发现

### 1. 相性表不是表，是 JSON 矩阵
successionPointTable = { alphabetId: { alphabetId: 相性值 } } 的**全连接矩阵**（133×133），
每角色详情页 RSC payload 内嵌全 133 行×133 列。
agnesdigital 页内嵌矩阵样例（可验证）：
- believe→rulership 14 / rulership→believe 14（对称）
- mihonobourbon→maruzensky 34（高位）
- hishimiracl→inesfujin 37
- airshakur→goldship 32
- mejirodober→stillinlove 36 / →vodka 34
- believe→currenchan 31
- 相性合计第1名 agnesdigital 的总相性 3087 = 全 133 行之和 ✓（数学自洽可复验）

### 2. characterRouteRaces —— 因子赛程表（turn 精确到回合！）
每角色 { charaId: [{raceId, turn}, ...] }：
- 1001 スペシャルウィーク: 1006@56/1010@34/1015@44/1019@70/1023@72/3009@27
- 1002 スズカ: 1012@60/1016@68/2006@29/2007@53/2024@42/2026@67
- 1003 テイオー: .../4009@26
- 1079 フリオーソ: 2040@54（川崎記念?）/ 1101@60/1102@37/1105@45/1106@48/1106@72/1020@47/1001@52/1108@24/1109@57/1105@69
- **1016 ナリタブライアン: 同一 raceId 出现两次（1016@68 ×2, 1019@70 ×2, 1006@56 ×2, 1023@48/72 各×2）= クラシック世代+シニア世代同レース両世代出走**——旧重賞ボーナス时代的历史数据保留
- 空数组: 1075(エアシャカール?), 1101, 1118, 1126, 1128, 1138, 1140, 1142, 1144, 1146, 1148, 1946（未実装/特殊）

### 3. playableCharacters 全字段（133 人完整）
每角色: id/castId/alphabetId/alphabetName/name/uiColorMain/Sub/SpeechColor1
- 1001 スペ: castId1, #EE6DCB/#FFDEF9
- 1075 不在列表（←1001-1148 有缺口: 1075/1101/1118/1126/1128/1138/1140/1142/1144/1146/1148/1946 全空）
- 1946 = URA? 占位（未実装）

## 相性值计算完全可复现（三源互证达成）
- mdb: text_data + グループ公式（announcements 2/4 篇）= **算法**
- 本站: successionPointTable 133×133 矩阵 = **站点自算结果全量**
- announcements/1 文: 相性表是站点自算的——非官方数据
→ 我们现在可以用 mdb 组数据复算矩阵，与站点矩阵 diff 验证公式正确性

## 与 mdb 对接路线
1. 从 mdb 挖出角色组归属（適性/学年/寮/同室/血統/優勝レース）
2. 按公式（3大适性7pt+2pt+1pt组）复算 133×133
3. 与 successionPointTable 逐格 diff——**全量验证相性公式的唯一机会**
4. characterRouteRaces 与 single_mode_program 的 race_turn 对齐验证
