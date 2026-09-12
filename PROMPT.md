【補習班管理系統 - 專案開發規範】  
架構：GitHub Pages (前端網頁) + GAS Web App (API 後端) + Google Sheets (多資料庫)。

一、 視覺與版面規範 (Visual & Layout)
1. 視覺色彩與材質：
   - 預設「深藍專業風」：主配色深藍色 `#1E468A` (Header Bar、主要按鈕 `btn-custom-primary`、重點標籤)。
   - 背景與卡片：頁面背景淺灰藍 `#F1F5F9`；卡片/容器純白 `#FFFFFF` 帶 1px 邊框 `#E2E8F0` 與 10px 圓角 (`rounded-[10px]`)。
   - 文字階層：主要文字深灰 `#1E293B` / 次要文字中灰 `#64748B` / 警示紅 `#EF4444` / 提醒黃 `#F59E0B`。
2. 跨平台防亂碼 (字體與圖示)：
   - 圖示：統一引入 FontAwesome CDN (`<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">`)。
   - 字體：統一宣告 Google Fonts Noto Sans TC，防止跨 OS (Windows/Mac/iOS/Android) 產生字型缺失或亂碼。
3. 模組化獨立樣式 (Modular Style Rules - 重點要求)：
   - 採用 Tailwind CSS (CDN) 進行開發。
   - 每個 UI 容器 (如 `.header-bar`, `.search-card`, `.data-table`, `.action-btn`) 必須擁有明確且獨立的 CSS Class。
   - **禁用 :root 全域變數控管細節**，所有的 margin, padding, font-size, color, border-radius 必須明確宣告在各元件自身的 Class 內，便於單獨微調而不影響全站。
4. 版面結構 (Layout Structure)：
   - Header Bar：深藍底白字置頂，左側顯示「[補習班名稱] | [子系統名稱]」，右側預留狀態/操作區。
   - Main Content：使用 `.container` 支援 RWD 自動伸縮，內部依業務邏輯彈性採用數據卡片、表格、表單或混合版面。

二、 操作與音效機制 (Input & Audio)
1. 鍵盤優先與防呆：表單 `Focus` 時呈現深藍邊框與光暈；支援 `Enter` 鍵觸發送出；送出成功後自動清空並自動聚焦 (Focus) 輸入框。
2. 互動體驗：點擊按鈕切換 Loading 載入狀態；主要按鈕 Hover 時加深底色並帶輕微位移與陰影。
3. 多模組音效系統：預設內建 Web Audio API 短嗶聲；預留使用者自訂雲端 MP3 / YouTube 網址背景播放機制。

三、 GAS 後端與多檔案 API 規範 (.gs)
1. 全域配置檔 (Config.gs)：
   - 頂端宣告全域變數 `CONFIG`，統一管理 API Key、各 Google Sheet 檔案 ID 以及各分頁 (Tab) 名稱，預留後續更換檔案與重新命名的彈性。
   - 範例格式：
     `const CONFIG = { API_KEY: "...", SHEETS: { DB_STUDENT: { ID: "...", TAB_MAIN: "學生名冊" }, DB_ATTENDANCE: { ID: "...", TAB_LOGS: "打卡紀錄" } } };`
2. 單一入口與多模組串接 (Code.gs & Sub-modules)：
   - 統一以 `doPost(e)` 作為唯一的 Web App 進入點。
   - 收到請求時先強烈驗證 `payload.apiKey`，驗證失敗安全回傳 403。
   - 依 `payload.action` 透過 `switch` 分流轉發給各功能的專屬後端檔案 (如 `Attendance.gs`, `Calendar.gs` 中的處理函式)。
3. 回應與時區處理：
   - 統一回應格式：`{ success: boolean, data: {}, message: string }`。
   - 時間統一使用 `GMT+8 yyyy-MM-dd HH:mm:ss` 格式化，所有後端邏輯必須包覆在 `try...catch` 區塊內。

四、 前端網頁 API 串接規範 (.html)
1. `fetch()` 發送 POST 請求至 GAS API 時，必須設定 `method: "POST"`、帶入 `apiKey` 與 `action` 參數，並務必加上 `redirect: "follow"` 處理 302 轉址。
2. 一般提示訊息呈現在表單下方靜態文字 Alert，發生重大異常或系統限制時彈出 `alert()` 視窗。
