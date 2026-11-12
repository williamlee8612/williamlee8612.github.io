【承智教育管理系統 - 開發規範】
架構：GitHub Pages (HTML) + 單一 GAS Web App + Google Sheets。全程式碼需附繁體中文註解。

一、 前端規範 (HTML / Tailwind CSS)
1. 配色 (預設深藍)：
   - 深藍(預設): 主色`#1E468A` 懸停`#1E3A8A` Header字`#BFDBFE` Focus`#3B82F6`
   - 暖橙: `#EA580C` / `#C2410C` / `#FED7AA` / `#F97316` | 翡翠綠: `#059669` / `#047857` / `#A7F3D0` / `#10B981` | 石墨: `#334155` / `#1E293B` / `#CBD5E1` / `#64748B`
   - 通用: 背景`#F1F5F9` | 卡片`#FFFFFF`+`1px #E2E8F0`+`rounded-[10px]`+`shadow-sm` | 文字: 主`#1E293B` 次`#64748B` 警`#EF4444` 提`#F59E0B`
2. 資源與樣式獨立：
   - 引入 FontAwesome 6.5.1 CDN 與 Google Fonts Noto Sans TC。
   - **禁用 `:root` 全域變數**，各 UI 容器需設獨立 Class 並直接宣告樣式，方便單獨微調。
3. 統一版面與互動：
   - Header Bar：置頂高 64px (`h-16`) 深藍底白字 `px-4 sm:px-6`；左側 `text-[24px] font-[600]` 顯示「承智教育 | [子系統名稱]」；右側放狀態燈號 + 操作鈕(重整/音效/登出)。
   - Main Content：`.container max-w-7xl mx-auto px-4 py-6 sm:px-6` RWD 佈局。
   - Table：隔行變色 (奇數白/偶數 `#F8FAFC`) 帶 1px 邊框；無資料直接顯示純文字。
   - 互動狀態：Focus 亮主色邊框光暈；按鈕 Hover 微上浮 (`hover:-translate-y-0.5 transition-all shadow-md`)；支援 Enter 送出後清空並 Focus 輸入框；點擊切換 Loading 轉圈；一般訊息用圓角背景 Alert，重大異常彈 `alert()`；預留 Web Audio / `<audio>` 音效。

二、 後端極簡模組架構 (GAS)
1. 兩段式直線架構 (無 Config 檔、無 API Key)：
   - **總機 (`Code.gs`)**：頂端設 `const DB = { 鍵: { id: "Sheet_ID", tab: "分頁" } };`（前端嚴禁出現 ID）。`doGet()` 回傳 `{ success: true, code: 200, message: "API 運作正常" }`。`doPost()` 內建 `LockService.getScriptLock()`，透過 `actions` 字典物件直接呼叫對應函式。
   - **功能檔 (`[功能].gs`)**：撰寫獨立 `handleXxx(data)` 函式，並標明需貼入 `Code.gs` (`DB` 與 `actions`) 的擴充程式碼。
2. 安全與效能：
   - 個資最小化：後端篩選後只回傳前端必要欄位，嚴禁整表輸出。
   - 批次讀寫：嚴禁迴圈內單格讀寫，一律用 `getValues()` / `setValues()` / `appendRow()`。
   - 統一回傳 `{ success, code, data, message, timestamp }` (`GMT+8 yyyy-MM-dd HH:mm:ss`)，全邏輯包覆 `try...catch...finally`。

三、 API 串接協定
- 前端：`fetch(GAS_URL, { method: "POST", headers: { "Content-Type": "text/plain;charset=utf-8" }, redirect: "follow", body: JSON.stringify({ action, data }) })`
- 分流：`code === 200` 顯示成功 Alert；`400/404` 顯示警告 Alert；`500` 或斷線彈 `alert()` 並更新狀態燈號。

【任務目標】：撰寫 [請填入功能名稱] 的前端 HTML、後端 `[功能].gs`，並列出需貼入 `Code.gs` 的擴充程式碼。
