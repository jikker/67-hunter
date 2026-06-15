# T-161 「67 獵人」反應遊戲

由 T-152「數字獵人」改版而來的純前端單檔遊戲（`index.html`，雙擊即玩、零相依、手機優先）。

## 玩法
棋盤（A4 隨機 Voronoi 格）上**全部是誘餌 66 / 76 / 77，只有一格是「67」**。
玩家要用**最快的速度找出並點擊唯一的 67**；計時到 0.01 秒，越快排名越前。

- 點到 66/76/77 → 扣 1 條命，錯 3 次遊戲結束（不計分）。
- 找到 67 → 過關，留名進排行榜（依用時由小到大排序）。
- **四種難度＝格子數**：新手 25 格 / 簡單 50 格 / 普通 75 格 / 困難 100 格（格子越多越難找）。
- 67 可能藏在很小的格子裡，**兩指捏合放大 / 滑鼠滾輪縮放**找；切到背景自動暫停。
- 中英雙語、背景音樂（OpenGameArt CC0）、音效、語音報數（含 20% 機率「six～seven～」67 迷因彩蛋）。

## 與 T-152 的差異
| 項目 | 數字獵人 (T-152) | 67 獵人 (T-161) |
|------|------|------|
| 目標 | 依序找完 1–N | 找出唯一的 67 |
| 格子內容 | 1–N 各一次（不重複） | 誘餌 66/76/77＋唯一 67 |
| 計分 | 找完全部的用時 | 找到 67 的用時（2 位小數） |
| 排行榜欄位 | name/sec/errors/date/fast/slow | name/sec/errors/date（無 fast/slow） |
| 主題色 | 琥珀 × 灰 | 翡翠綠 × 深青 |
| App icon | 放大鏡＋數字格 | 大「67」＋放大鏡＋ghost 誘餌（emerald） |

## 雲端排行榜（Google Sheet）— ⚠️ 待部署
- 後端：`backend/Code.gs`（Apps Script Web App），新 Google Sheet **「67 獵人排行榜」**、**全新 HMAC 密鑰**（與 T-152 不同，互不汙染）。
- **v2（2026-06-15）同步 T-152 排行榜優化**：後端 `doGet` 加 `?mode=all`（一次回四難度榜）；前端 `CloudLb` 加 `prefetchAll`／`qualifies`／`top`、快取 TTL 2 分鐘，開頁與開局背景預抓全難度榜，結算前先用門檻判斷——**明確進不了前 20 就不打 POST**（省一次 API），且不再顯示「已上傳」字樣。已有的「30 秒後端共享快取＋滿榜最後一名短路」維持不變。LB_API 仍為空時全部優雅退回純本機，行為不變；部署後即生效。
- **前端版號自成一系（v3，2026-06-15）**：`APP_VERSION` 與整段 changelog **重新計算、不再繼承 T-152 數字獵人的 vNN**。檔頭只保留 67 獵人自身版本史（67-v1 fork／67-v2 排行榜優化／67-v3 版號切割），並移除散落樣式區、沿用自數字獵人的 v44/v45 inline 標號。此後版號只跟 67 獵人自身改動走。
- **前端 `LB_API` 目前留空字串＝純本機排行榜（localStorage）**，遊戲完全可玩、各難度各留前 20 名。
- 要啟用跨玩家雲端榜：依 `backend/DEPLOY.md` 部署 Apps Script（需 iiikccc02 帳號／browser，headless 工人無法代部署），把 `/exec` 網址貼回 `index.html` 的 `const LB_API`。
- localStorage key 已命名空間化為 `hunter67_*`，與 number-hunter 在同主機不互相干擾。

## 檔案
- `index.html`：遊戲本體（單檔）。
- `icon.svg` / `icon-180.png` / `app-icons/hunter67-*.png`：App icon（codex 產出 SVG，cairosvg 轉 PNG；`hunter67-appstore-1024.png` 為不透明 RGB 供 App Store）。
- `backend/Code.gs` + `backend/DEPLOY.md`：雲端排行榜後端與部署說明。
- `bgm.mp3`：背景音樂（HydroGene「8-bit Epic Space Shooter Music」，OpenGameArt，CC0）。

## 驗證（2026-06-15）
- 抽出 inline script `node --check` 通過。
- 前後端 HMAC 金鑰一致（LB_KC XOR 90 == 後端 SECRET，含中文名向量）。
- `makeBoardNums` 8000 局：恰好 1 個 67、其餘皆 66/76/77（四難度）。
- 真實 Chrome headless E2E：開始畫面渲染（emerald 主題、四難度標籤、版本 67-v1）；開局＝1×67＋74 誘餌、點誘餌扣命、點 67 過關出 win 面板；難度切新手＝25 格 1×67；連點 3 誘餌出「遊戲結束」面板；找到 67 存名「煙霧」寫入本機排行榜成功。
