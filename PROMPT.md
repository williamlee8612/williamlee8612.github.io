【承智教育管理系統 - 開發規範】
- 架構：GitHub Pages (前端 HTML) + 單一 GAS Web App (後端 API) + Google Sheets (資料庫)
- 註解要求：全程式碼（HTML / CSS / JS / GS）皆需附帶詳細繁體中文註解

一、 前端視覺與 UI/UX 規範 (HTML / Tailwind CSS)
1. 配色主題庫 (預設：深藍專業風)：
   - 深藍專業 (Default)：主色 `#1E468A` | 懸停 `#1E3A8A` | Header字 `#BFDBFE` | Focus `#3B82F6`
   - 暖橙活力：主色 `#EA580C` | 懸停 `#C2410C` | Header字 `#FED7AA` | Focus `#F97316`
   - 翡翠綠親和：主色 `#059669` | 懸停 `#047857` | Header字 `#A7F3D0` | Focus `#10B981`
   - 極簡石墨：主色 `#334155` | 懸停 `#1E293B` | Header字 `#CBD5E1` | Focus `#64748B`
   - 通用背景與卡片：頁面背景 `#F1F5F9` | 卡片純白 `#FFFFFF` + 1px邊框 `#E2E8F0` + 圓角 `rounded-[10px]` + 微陰影 `shadow-sm`
   - 文字階層：主標/內文 `#1E293B` | 次要中灰 `#64748B` | 警示紅 `#EF4444` | 提醒黃 `#F59E0B`

2. 跨平台資源與樣式獨立原則：
   - 圖示引入：FontAwesome 6.5.1 CDN (`https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css`)
   - 字體引入：Google Fonts Noto Sans TC（防跨 OS 亂碼）
   - 獨立樣式：**禁用 `:root` 全域變數**，各 UI 容器需設獨立 Class
   - 個別微調：所有的 margin、padding、color、font-size 必須明確宣告於元件自身的 Class 中

3. 統一生態系版面結構：
   - Header Bar (頂部列)：
     * 高度與底色：置頂高 64px (`h-16`)、深藍底白字、內邊距 `px-4 sm:px-6`
     * 左側標題：字級 `text-[24px]`、字重 `font-[600]`，格式為「承智教育 | [子系統名稱]」
     * 右側區塊：狀態燈號（如：🟢 系統連線中） + 特殊操作按鈕（重整 / 音效開關 / 登出）
   - Main Content (主內容)：
     * 外框容器：`.container max-w-7xl mx-auto px-4 py-6 sm:px-6`（支援 RWD）
   - Data Table (資料表格)：
     * 視覺設計：隔行變色（奇數列白底 / 偶數列 `#F8FAFC`），外框帶 1px 邊框
     * 空資料狀態：無資料時直接在表格內顯示純文字提示

4. 互動體驗與狀態反饋：
   - 輸入操作：Focus 亮主色邊框光暈；支援 `Enter` 送出；送出後自動清空並 Focus 輸入框
   - 按鈕動態：Hover 微上浮 (`hover:-translate-y-0.5 transition-all shadow-md`)；點擊切換 Loading 轉圈
   - 提示訊息：一般訊息用圓角背景 Alert（成功 `#ECFDF5/#047857`、失敗 `#FEF2F2/#B91C1C`）；重大異常彈出 `alert()`
   - 音效預留：預留 Web Audio / `<audio>` 背景音效播放機制

二、 後端極簡模組架構 (GAS)
1. 兩段式直線架構 (無 Config 檔、無 API Key)：
   - 總機入口 (`Code.gs`)：
     * 資料庫集中管理：頂端設 `const DB = { 鍵: { id: "Sheet_ID",
