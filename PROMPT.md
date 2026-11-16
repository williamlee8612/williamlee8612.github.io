# 承智教育管理系統 - 專案開發規範

## 核心架構與共用設定
- **系統架構**：GitHub Pages (前端 HTML) + 單一 GAS Web App (後端 API) + Google Sheets (資料庫)
- **共用 GAS 網址**：`[請填入 GAS Web App 網址]`（預留設定常數 `const GAS_URL = "";`）
- **註解規範**：全程式碼（HTML/CSS/JS/GS）皆需附帶簡潔繁體中文註解（`//` 或單行 `/* ... */`）

## 一、 前端視覺與版面規範 (HTML / Tailwind CSS)

### 1. 配色主題庫（Default：深藍專業風）
- **深藍專業**：主色 `#1E468A` | 懸停 `#1E3A8A` | Header字 `#BFDBFE` | Focus `#3B82F6`
- **暖橙活力**：主色 `#EA580C` | 懸停 `#C2410C` | Header字 `#FED7AA` | Focus `#F97316`
- **翠綠親和**：主色 `#059669` | 懸停 `#047857` | Header字 `#A7F3D0` | Focus `#10B981`
- **極簡石墨**：主色 `#334155` | 懸停 `#1E293B` | Header字 `#CBD5E1` | Focus `#64748B`
- **通用材質**：頁面背景 `#F1F5F9` | 卡片純白 `#FFFFFF` + 1px邊框 `#E2E8F0` + 圓角 `rounded-[10px]` + 微陰影 `shadow-sm`
- **文字階層**：主標/內文 `#1E293B` | 次要中灰 `#64748B` | 警示紅 `#EF4444` | 提醒黃 `#F59E0B`

### 2. 跨平台資源與獨立樣式原則
- **圖示引入**：FontAwesome 6.5.1 CDN (`https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css`)
- **字體引入**：Google Fonts Noto Sans TC（防跨 OS 亂碼）
- **樣式獨立微調**：**禁用 `:root` 全域變數**。各 UI 容器需設獨立 Class，所有的 margin、padding、color、font-size 明確宣告於元件自身的 Class 中

### 3. 全站單頁滿版與全元素自適應結構
- **視窗鎖定**：`html, body` 固定 `height: 100dvh; overflow: hidden;`（嚴禁整頁出現外部捲軸）
- **Header Bar (頂部列)**：
  - 高度與底色：置頂高 64px (`h-16`)、深藍底白字、內邊距 `px-4 sm:px-6`
  - 左側標題：字級 `text-[24px]`、字重 `font-[600]`，格式為「承智教育 | [子系統名稱]」
  - 右側區塊：狀態燈號（如：🟢 系統連線中） + 特殊操作按鈕（重整 / 音效開關 / 登出）
- **Main Content (主內容區)**：
  - 外框容器：`w-[98%] max-w-[98%] mx-auto`、高度 `calc(100dvh - 64px)`、緊湊內距 `py-3`
  - 局部捲動：各功能卡片依畫面比例彈性伸縮，僅開放特定資料清單內部（如紀錄列表、表格）上下捲動（`overflow-y-auto`）
- **全元素字體自適應**：所有標題、卡片內文、表格與數據皆採用 `clamp(最小值, min(vw, vh), 最大值)` 或動態響應級距
- **Data Table (資料表格)**：隔行變色（奇數列白底 / 偶數列 `#F8FAFC`），外框帶 1px 邊框；無資料時直接顯示純文字提示

## 二、 UX 優先互動、效能與前端資安規範

### 1. 文案與流暢操作體驗
- **使用者視角文案**：所有標題、按鈕、提示、彈窗皆採使用者易懂的文字，工程術語、技術說明僅出現於程式碼的註解
- **輸入與編輯**：
  - Focus 亮主色邊框光暈；表單送出支援 `Enter` 鍵，送出後自動清空並 Focus 輸入框
  - 行內編輯（Inline Edit）按下 `Enter` 觸發 `element.blur()` 自動確認存檔並顯示輕量提示，不清空輸入框
- **按鈕防連點與局部刷新**：Hover 微上浮 (`hover:-translate-y-0.5 transition-all shadow-md`)；點擊送出期間鎖定按鈕並顯示 Loading 轉圈防連點；資料更新後僅局部刷新對應 DOM 節點，嚴禁整頁重載
- **提示訊息**：一般訊息用圓角背景 Alert（成功 `#ECFDF5/#047857`、失敗 `#FEF2F2/#B91C1C`）；重大異常彈出 `alert()`
- **瀏覽器音效解鎖**：需自動響鈴／播報的系統，Header 右側配置「點擊啟用音效（提醒黃 `#F59E0B`）」按鈕，點擊解鎖瀏覽器限制後切換為「音效已就緒（翠綠 `#10B981`）」

### 2. 隱私與共用設備資安
- **本機儲存分級管制**：
  - 純前端工具（無個資、無後端 API）：允許使用 `localStorage` 存設定，讀寫與 `JSON.parse` 需包覆 `try...catch`
  - 含帳密、學生個資或機密資源之系統：**嚴禁使用 `localStorage` 或 `sessionStorage`**，關閉分頁即清空
- **機密頁面防護（按需啟用）**：涉及版權或機密資料時，停用文字反白（`user-select: none`）、禁用右鍵（`contextmenu`），並攔截 `F12`、`Ctrl+Shift+I/J/C`、`Ctrl+U/S/P/A`

## 三、 後端極簡模組架構與安全加速 (GAS)

### 1. 兩段式直線架構
- **總機入口 (`Code.gs`)**：
  - 資料庫設定：頂端設 `const DB = { 鍵: { id: "Sheet_ID", tab: "分頁" } };`（前端嚴禁出現 ID）
  - 健康檢查 (`doGet`)：回傳 `{ success: true, code: 200, message: "API 運作正常" }`
  - 請求分流 (`doPost`)：內建 `LockService.getScriptLock()` 防並發衝突，透過 `actions` 字典物件直接呼叫對應函式
- **功能模組檔 (`[功能].gs`)**：
  - 直接依功能直覺命名獨立函式（如 `CheckIn`、`Makeup`）
  - 產出時需明確標明要貼入 `Code.gs` (`DB` 與 `actions`) 的擴充程式碼

### 2. 個資隔離與極致速度優化
- **個資最小化回傳**：後端比對篩選後，僅組裝前端畫面真正需要的最小欄位與筆數回傳，嚴禁將整張工作表傳送至前端
- **精準批次讀寫**：嚴禁在迴圈內單格讀寫；讀取時僅抓取實際有資料的精準欄列範圍（`getRange(2, 1, lastRow - 1, neededCols).getValues()`），寫入使用 `setValues()` 或 `appendRow()`
- **伺服器端安全快取 (`CacheService`)**：針對高頻讀取的靜態對照表（如學生學號姓名對照），優先透過 GAS 內部 `CacheService.getScriptCache()` 暫存（設定短效期如 600 秒）加速查詢；資料存於 Google 伺服器記憶體中不落地、不外流，兼顧秒級反應與資安隔離（資料有異動時同步清除對應快取鍵值）
- **統一回應格式**：回傳 `{ success, code, data, message, timestamp }`（時區 `GMT+8 yyyy/MM/dd HH:mm:ss`），全邏輯包覆於 `try...catch...finally`

---

## 四、 前後端 API 串接協定（有後端系統適用；純前端工具嚴禁引入）
- **前端請求（避開 CORS 預檢）**：
  ```javascript
  fetch(GAS_URL, {
    method: "POST",
    headers: { "Content-Type": "text/plain;charset=utf-8" },
    redirect: "follow",
    body: JSON.stringify({ action, data })
  })
