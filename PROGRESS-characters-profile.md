# characters/specialweek 详情页 + casts URL 探测结果

## characters/specialweek（URL = 角色英文名小写）
```
紹介文: 北海道生まれ…（全文）
プロフィール:
  誕生日 5月2日 / 身長158 / 体重 微減（レース前で緊張気味）/ 三サイズ B81・W56・H81
  寮 栗東寮 / 同室 サイレンススズカ / 学年 中等部3年
トピック ヒミツ×2（利き牛乳/おんぶ）
得意/苦手/耳/尻尾/靴サイズ(左23.5右23.0)/家族/マイルール/壁紙/出走前/得意科目/自慢/よく買う物 —— 全 13 项 profile
声優 CV: 和氣あず未（東京俳優生活協同組合）
  代表作评分: S+ 20056 神谷奈緒(デレマスSS+) / A+ 4939 ヒグマ? / B+ 2827 桜ノ宮苺香(ブレンドS)
  （注：specialweek 页给的是 片桐早苗A+8617/仙狐A3866/苺香B+2827；fujikiseki页给松井恵理子S+20056神谷奈绪——**代表作评分=声優DB的匹配度数据**）
パーソナルカラー: メイン#EE6DCB / サブ#FFDEF9 / その他#FF7FDD
元になった馬: スペシャルウィーク / 1995-05-02 / 獲得賞金 10億9,262万円
育成ウマ娘×3: 通常スペ/水着スペ/総大将スペ
サポートカード 9 枚
ひとコマ 14 枚（含他角色）
```
★profile 全字段 = mdb text_data/character_profile 的玩家投影；同室字段（サイレンススズカ）= 相性 2pt 组（同室1）的数据来源

## casts 子页探测
- /casts/wakazu-azumi ✗ /casts/1 ✗ —— casts 详情页 URL 未命中（可能用 hash id 或纯 JS 路由）
- 声優 145 人名单已在 /casts 列表页抓到；详情需 JS 交互，静态无数据

## URL 命名规则汇总（站点三套 ID 体系）
| 页面 | URL 键 | 例 |
|---|---|---|
| 育成卡 | card_id（ mdb chara_card id）| /cards/100101, /cards/100503 |
| 支援卡 | support_card_id | /supports/10044, /supports/30311 |
| 角色 | 英文名小写 | /characters/specialweek, /characters/fujikiseki |
| 技能 | skill_id | /skills/200022, /skills/100131 |
| 声優 | ？（未探明）| — |
