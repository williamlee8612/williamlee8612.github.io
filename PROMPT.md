【補習班管理系統 創作規範】
零、系統架構：
1. 前端網頁：GitHub Pages
2. API後端：GAS Web App
3. 資料庫：Google Sheets

一、 視覺、材質與獨立樣式規範 (UI/UX Design)
1. 色彩與材質：
   - 主配色：深藍色 `#1E468A` (用於 Header Bar、主要按鈕、重點標籤)。
   - 頁面背景：淺灰藍 `#F1F5F9`。
   - 卡片容器：純白 `#FFFFFF`，帶 1px 邊框 `#E2E8F0` 與 10px 圓角 。
   - 文字階層：主要文字深灰 `#1E293B` / 次要文字中灰 `#64748B` / 警示紅 `#EF4444` / 提醒黃 `#F59E0B`。
2. 模組化獨立樣式 (Modular Style Rules)：
   - 每個 UI 容器必須擁有明確且獨立的 CSS Class。
   - **禁用 :root 全域變數控管細節**，所有的 margin, padding, font-size, color, border-radius 必須明確宣告在各元件自身的 Class 內，便於單獨微調而不影響全站。
3. 跨平台防亂碼：
   - 圖示統一引入 FontAwesome CDN (`<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">`)。
   - 字體統一宣告 Google Fonts Noto Sans TC，避免跨 OS/裝置產生字型缺失或亂碼。
4. 版面架構：
   - Header Bar：深藍底白字置頂，左側為「[補習班名稱] | [子系統名稱]」，右側預留狀態/操作區。
   - Main Content：使用 `.container` 支援 RWD 自動伸縮，內部依業務邏輯彈性採用數據卡片、表格、表單或混合版面。

二、 操作與音效機制 (Input & Audio)
1. 鍵盤優先：`Enter` 觸發送出、`Focus` 時呈現深藍邊框與光暈；資料送出後自動清空並自動聚焦輸入框。
2. 互動體驗：點擊按鈕切換 Loading 狀態；主要按鈕 Hover 時加深底色並帶輕微位移與陰影。
3. 音效系統：預設內建短嗶聲（Web Audio API）；預留使用者自訂雲端 MP3 / YouTube 網址背景播放機制。

四、 GAS 後端與 API 規範 (.gs)
1. 配置檔：頂端設 `CONFIG = { API_KEY: "...", SHEETS: { DB_1: "ID_1", DB_2: "ID_2" } }` 方便換檔。
2. 單一進入點：`doPost(e)` 為預設進入點，強烈驗證 `payload.apiKey`，失敗回傳 403。
3. 路由分流：依 `payload.action` 分流處理，回應格式統一為 `{ success: boolean, data: {}, message: string }`。
4. 時間與例外處理：時間統一為 `GMT+8 yyyy-MM-dd HH:mm:ss`，全邏輯包覆 `try...catch`。

五、 前端網頁規範 (.html)
1. `fetch()` 發送 POST 至 GAS API 時，務必加上 `redirect: "follow"` 處理 302 轉址。
2. 一般訊息呈現在表單下方靜態文字 Alert，重大異常彈出 `alert()` 視窗。

【任務目標】：請依上述規範，撰寫 [請填入功能名稱] 的 Code.gs 與 index.html。
