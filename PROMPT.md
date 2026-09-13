【補習班管理系統 - 專案開發規範】

一、 前端視覺與 UI/UX 規範 (Frontend Spec)
1. 配色主題庫 (Default：深藍專業風)：
   - 深藍專業: 主色 `#1E468A` | 懸停 `#1E3A8A` | Header字 `#BFDBFE` | Focus `#3B82F6`
   - 暖橙活力: 主色 `#EA580C` | 懸停 `#C2410C` | Header字 `#FED7AA` | Focus `#F97316`
   - 翠綠親和: 主色 `#059669` | 懸停 `#047857` | Header字 `#A7F3D0` | Focus `#10B981`
   - 極簡石墨: 主色 `#334155` | 懸停 `#1E293B` | Header字 `#CBD5E1` | Focus `#64748B`
   - 通用材質: 背景 `#F1F5F9` | 卡片 `#FFFFFF` + 1px `#E2E8F0` + 圓角 10px (`rounded-[10px]`) + `shadow-sm`
   - 文字階層: 主標/內文 `#1E293B` | 次要中灰 `#64748B` | 警示紅 `#EF4444` | 提醒黃 `#F59E0B`
2. 資源引進 (防跨 OS 亂碼):
   - FontAwesome 6.5.1 CDN
   - Google Fonts: Noto Sans TC
3. 樣式獨立原則 (禁用 :root 全域樣式):
   - 使用 Tailwind CSS。每個 UI 容器需有獨立 Class，所有的參數都必須明確宣告於元件自身的 Class 中，利於單獨微調。
   - 全程式碼需附帶繁體中文註解。
4. 統一生態系版面結構:
   - Header Bar: 置頂高 64px (`h-16`) 深藍底白字 `px-4 sm:px-6`。
     * 左側: 字級 `text-[24px]`、字重 `font-[600]`，格式「[補習班名稱] | [子系統名稱]」。
     * 右側: 狀態燈號 (如：🟢 系統連線中) + 特殊操作按鈕 (重整、音效開關、登出)。
   - Main Content: 容器外框 `.container max-w-7xl mx-auto px-4 py-6 sm:px-6` 支援 RWD。
   - Data Table: 隔行變色 (表頭深色 / 奇數白底 / 偶數 `#F8FAFC`)，帶 1px 邊框。
   - 互動與防呆: Focus 亮主色邊框光暈；按鈕 Hover 微上浮 1-2px (`hover:-translate-y-0.5 transition-all shadow-md`)；支援 Enter 發送、送出後自動清空並 Focus 輸入框。
   - 狀態反饋: 訊息 Alert 用圓角背景框 (成功淡綠底深綠字 `#ECFDF5/#047857` / 失敗淡紅底深紅字 `#FEF2F2/#B91C1C`)，重大異常彈出 `alert()`；
   - 其他：無資料時於表格內直接輸出純文字提示；按鈕點擊顯示 Loading 轉圈

二、 後端與 API 規範 (Backend & API Protocol)
1. 配置檔 (Config.gs):
   - 宣告 `CONFIG = { API_KEY: "...", SHEETS: { DB_NAME: { ID: "...", TAB: "分頁名" } } }` 統整 ID 與分頁名。
2. 進入點與路由 (Code.gs):
   - 統一以 `doPost(e)` 為 Web App 唯一進入點。
   - 優先驗證 `payload.apiKey`，失敗回傳 HTTP 403。依 `payload.action` 透過 `switch` 分流處理邏輯。
   - 時間統一以 `GMT+8 yyyy-MM-dd HH:mm:ss` 格式化，全邏輯包覆於 `try...catch`。
3. API 通訊協定:
   - Request: 前端 `fetch(GAS_URL, { method: "POST", redirect: "follow", body: JSON.stringify({ apiKey, action, data }) })`
   - Response: 後端統一回傳 JSON `{ success: boolean, data: object/array, message: string }`
