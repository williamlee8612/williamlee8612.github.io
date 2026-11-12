【承智教育管理系統 - 專案開發規範 (PROMPT.md)】

一、 前端視覺與 UI/UX 規範 (Frontend Spec)
1. 配色主題庫 (預設：深藍專業風)：
   - 深藍專業 (Default): 主色 `#1E468A` | 懸停 `#1E3A8A` | Header字 `#BFDBFE` | Focus `#3B82F6`
   - 暖橙活力: 主色 `#EA580C` | 懸停 `#C2410C` | Header字 `#FED7AA` | Focus `#F97316`
   - 翡翠綠親和: 主色 `#059669` | 懸停 `#047857` | Header字 `#A7F3D0` | Focus `#10B981`
   - 極簡石墨: 主色 `#334155` | 懸停 `#1E293B` | Header字 `#CBD5E1` | Focus `#64748B`
   - 通用材質: 背景 `#F1F5F9` | 卡片 `#FFFFFF` + 1px `#E2E8F0` + 圓角 10px (`rounded-[10px]`) + `shadow-sm`
   - 文字階層: 主標/內文 `#1E293B` | 次要中灰 `#64748B` | 警示紅 `#EF4444` | 提醒黃 `#F59E0B`
2. 資源引進 (防跨 OS 亂碼):
   - FontAwesome 6.5.1 CDN (`<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">`)
   - Google Fonts: Noto Sans TC
3. 樣式獨立與註解原則 (禁用 :root 全域樣式):
   - 使用 Tailwind CSS。每個 UI 容器需有獨立 Class，所有的 margin, padding, color, font-size 必須明確宣告於元件自身的 Class 中，利於單獨微調。
   - **所有前端 HTML / CSS / JS 均需附帶詳細繁體中文註解**，標示區塊功能與可微調參數。
4. 統一生態系版面結構:
   - Header Bar: 置頂高 64px (`h-16`) 深藍底白字 `px-4 sm:px-6`。
     * 左側: 字級 `text-[24px]`、字重 `font-[600]`，格式「承智教育 | [子系統名稱]」。
     * 右側: 狀態燈號 (如：🟢 系統連線中) + 特殊操作按鈕 (重整、音效開關、登出)。
   - Main Content: 容器外框 `.container max-w-7xl mx-auto px-4 py-6 sm:px-6` 支援 RWD。
   - Data Table: 隔行變色 (表頭深色 / 奇數白底 / 偶數 `#F8FAFC`)，帶 1px 邊框。
   - 互動與防呆: Focus 亮主色邊框光暈；按鈕 Hover 微上浮 1-2px (`hover:-translate-y-0.5 transition-all shadow-md`)；支援 Enter 發送、送出後自動清空並 Focus 輸入框。
   - 狀態反饋: 訊息 Alert 用圓角背景框 (成功淡綠底深綠字 `#ECFDF5/#047857` / 失敗淡紅底深紅字 `#FEF2F2/#B91C1C`)，重大異常彈出 `alert()`；無資料時於表格內直接輸出純文字提示；按鈕點擊顯示 Loading 轉圈；預留 Web Audio / HTML5 `<audio>` 音效機制。

二、 後端極簡與模組化規範 (Simplified Backend Spec)
1. 單一專案、兩段式直線架構 (免 Config 檔、免無效 API Key):
   - **總機入口 (`Code.gs`)**：
     * 頂端集中宣告全域 `DB` 物件管理 Sheet ID 與分頁名（例：`const DB = { STUDENT: { id: "...", tab: "學生名冊" } };`），前端絕對不可出現任何 Sheet ID。
     * `doGet(e)`: 僅作 API 連線健康檢查，回傳 `{ success: true, code: 200, message: "API 運作正常" }`。
     * `doPost(e)`: 唯一資料進入點，內建 `LockService.getScriptLock()` 防並發寫入衝突；透過 `actions` 字典物件對照 `req.action`，直接呼叫對應的功能函式執行。
   - **功能模組檔 (`[功能名稱].gs`，如 `Attendance.gs`)**：
     * 每次新增功能，只需撰寫獨立的 `handleXxx(data)` 函式檔案，並標明需貼入 `Code.gs` 的 `DB` 與 `actions` 兩行設定，方便跨對話快速擴充合併。
2. 個資最小化與效能原則:
   - **資料最小化回傳**：嚴禁將整張 Google Sheet 倒回前端；後端必須先依條件過濾，僅組裝前端畫面真正需要的欄位與筆數回傳。
   - **批次讀寫效能**：嚴禁在迴圈內單格讀寫 Sheet，一律使用 `getValues()` 批次讀取與 `setValues()` / `appendRow()` 寫入。
   - **完整中文註解**：所有 GAS 函式、變數用途、擴充插槽皆需附上繁體中文註解。
3. 標準化回應與時區:
   - 統一回傳 JSON：`{ success: boolean, code: number, data: object/array, message: string, timestamp: string }`（`code`: 200成功 / 400參數錯 / 404無資料 / 500系統錯）。
   - 時間統一以 `GMT+8 yyyy-MM-dd HH:mm:ss` 格式化，全邏輯包覆於 `try...catch...finally`。

三、 前後端 API 串接協定 (API Communication Protocol)
1. 請求規格 (Request - 避開 CORS 預檢):
   - 前端統一使用 `fetch(GAS_URL, { method: "POST", headers: { "Content-Type": "text/plain;charset=utf-8" }, redirect: "follow", body: JSON.stringify({ action, data }) })`。
2. 錯誤分流處理:
   - `code === 200` 顯示靜態成功 Alert；`code === 400 / 404` 顯示靜態警告 Alert；`code === 500` 或網路異常則觸發 `alert()` 彈窗並更新 Header 狀態燈號。

【任務目標】：請依上述規範，撰寫 [請填入功能名稱] 的：
1. 前端 HTML 程式碼 (含詳細中文註解與獨立 CSS Class)
2. 後端 GAS 對應功能模組檔 (`[功能].gs`)，並明確列出需貼入 `Code.gs` (`DB` 與 `actions`) 的擴充程式碼。
