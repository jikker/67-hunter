# T-161 「67 獵人」反應遊戲

由 T-152「數字獵人」改版而來的純前端單檔遊戲（`index.html`，雙擊即玩、零相依、手機優先）。

## 玩法
棋盤（螢幕隨機 Voronoi 格）上**全部是誘餌 66 / 76 / 77，混入多個「67」**。
玩家要用**最快的速度找出全部 67**；計時到 0.01 秒，越快排名越前。

- 點到 66/76/77 → 扣 1 條命，錯 3 次遊戲結束（不計分）。
- 找到 67 → 黃底紅框高亮保留、更新剩餘數；命中瞬間不跳 toast、不閃 HUD，語音以非同步節流播報 `six seven`。全部找完才過關，留名進排行榜（依用時由小到大排序）。
- **四種難度＝格子數 / 67 數量**：新手 25 格找 3 個 / 簡單 50 格找 6 個 / 普通 75 格找 9 個 / 困難 100 格找 12 個。
- **v10（2026-06-15）**：從「唯一 67」改為多目標 67（約 12% 目標密度）；過程中 HUD 顯示 `剩 X/Y`，點中的 67 以醒目黃底紅框粗字留在棋盤上。
- **v11（2026-06-15）**：語音報數改為播報「還有 X 個」，移除 67「six seven」迷因彩蛋與 Ctrl 彩蛋。
- **v12（2026-06-15）**：命中 67 後不再跳成功提示或語音播報，改用 HUD `剩 X/Y` 的短暫脈衝提示，避免遮住遊戲區域。
- **v13（2026-06-15）**：命中 67 後連 HUD 脈衝也取消，完全不顯示提示；只有點錯才顯示錯誤提示。
- **v14（2026-06-15）**：手機 HUD 改為緊湊不溢出；命中 67 改播英文 `six seven`。
- **v15（2026-06-15）**：命中語音改為非同步節流，不再每次連點都同步 `cancel/speak`，修正快速連點時短暫卡住。
- **v16（2026-06-15）**：遊戲開始後保留語言切換鈕，避免進入遊戲/橫向棋盤狀態時被 CSS 隱藏。
- 67 可能藏在很小的格子裡，**兩指捏合放大 / 滑鼠滾輪縮放**找；切到背景自動暫停。
- 中英雙語、背景音樂（OpenGameArt CC0）、音效；命中只保留格子狀態與節流語音，點錯才跳提示。

## 與 T-152 的差異
| 項目 | 數字獵人 (T-152) | 67 獵人 (T-161) |
|------|------|------|
| 目標 | 依序找完 1–N | 找出全部 67 |
| 格子內容 | 1–N 各一次（不重複） | 誘餌 66/76/77＋多個 67 |
| 計分 | 找完全部的用時 | 找完全部 67 的用時（2 位小數） |
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
- `icon.svg` / `icon-180.png` / `app-icons/hunter67-*.png`：App icon（彩色填格盤 + 67 徽章，cairosvg 轉 PNG；`hunter67-appstore-1024.png` 為不透明 RGB 供 App Store）。
- `backend/Code.gs` + `backend/DEPLOY.md`：雲端排行榜後端與部署說明。
- `bgm.mp3`：背景音樂（HydroGene「8-bit Epic Space Shooter Music」，OpenGameArt，CC0）。

## 驗證（2026-06-15）
- 抽出 inline script `node --check` 通過。
- v16：遊戲中語言切換鈕不再被 `body.gaming` / `landscape-play` 樣式隱藏。
- v15：公開 repo commit `e207687`，Pages `https://jikker.github.io/67-hunter/` 已驗回 `APP_VERSION = 'v15'`，且線上檔案無 `speechSynthesis.cancel`。
- v13：靜態行為檢查通過（命中分支無 `showToast`、無 `progress-pulse`、無 Web Speech；錯點分支仍有 `wrongTap` toast；進度提示按鈕隱藏）。
- v12：成功命中不再顯示 toast、不使用 Web Speech；剩餘數改由 HUD 脈衝提示。
- v11：移除 67 語音彩蛋，開局與命中後改播剩餘 67 數量。
- v10 線上部署：公開 repo commit `b559fc8`，Pages `https://jikker.github.io/67-hunter/` 已驗回 `APP_VERSION = 'v10'`；raw 與 Pages 皆包含 `targetCountFor`。
- `makeBoardNums` 2000 局/難度驗證：25/50/75/100 格分別固定產生 3/6/9/12 個 67，其餘皆為 66/76/77。
- v7 線上部署：公開 repo commit `a127615`，Pages `https://jikker.github.io/67-hunter/index.html?v7b` 已驗回 `APP_VERSION = 'v7'`；`icon.svg?v7b` 與 `app-icons/hunter67-512.png?v7b` HTTP 200 且為新尺寸。
- 前後端 HMAC 金鑰一致（LB_KC XOR 90 == 後端 SECRET，含中文名向量）。
- `makeBoardNums` 舊版 v1-v9：恰好 1 個 67、其餘皆 66/76/77（四難度）。
- 真實 Chrome headless E2E：開始畫面渲染（emerald 主題、四難度標籤、版本 67-v1）；開局＝1×67＋74 誘餌、點誘餌扣命、點 67 過關出 win 面板；難度切新手＝25 格 1×67；連點 3 誘餌出「遊戲結束」面板；找到 67 存名「煙霧」寫入本機排行榜成功。
