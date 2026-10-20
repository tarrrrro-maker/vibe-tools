# vibe-tools
系排菜單大師

# 軟體需求規格說明書 (Software Requirements Specification, SRS)

**專案名稱**：VolleyPlan — 響應式排球訓練排程與視覺化菜單生成系統  
**文件版本**：v2.1.0  
**目標部署環境**：GitHub Pages (Static Web Hosting Environment)  

---

## 1. 系統架構與技術棧 (System Architecture & Technology Stack)

本系統採 **JAMstack (JavaScript, APIs, Markup)** 純前端靜態架構，所有排程邏輯、狀態計算與 DOM 繪圖渲染均於客戶端瀏覽器（Client-Side Rendering, CSR）完成，無需伺服器端支援。

- **佈局與標記**：HTML5 語意化標籤架構
- **樣式引擎**：Tailwind CSS (via JIT CDN Engine)
- **視覺規範**：Neo-brutalism 輕量活潑卡通風（大圓角 `rounded-2xl`、2px 粗黑邊框、實心立體硬陰影 `3px 3px 0px #1E293B`）
- **互動引擎**：ECMAScript 2022+ (Vanilla JS Native)
- **手勢與拖曳**：`SortableJS v1.15.0`（Touch & Pointer Events 觸控手柄隔離機制）
- **影像轉譯**：`html2canvas v1.4.1`（安全容器點陣圖算繪引擎，避免文字重疊溢出）
- **持久化層**：Web Storage API (`localStorage`)

---

## 2. 核心功能規格 (Functional Specifications)

### 2.1 參數控制與時間推算引擎
- **起訖時間連動**：依起始時間（如 `18:00`）與各項目前置時間自動累加推算。
- **動態進度條狀態監控**：
  - 加總剛好等於總長：Emerald 標籤提示。
  - 加總超時：Amber 標籤顯示超時分鐘數。
  - 尚餘空檔：Sky Blue 標籤提示剩餘分鐘數。

### 2.2 隊長專屬熱身五部曲 (Warmup Suite)
一鍵生成菜單時，置頂注入以下專屬訓練序列（總計 25 分鐘）：
1. 慢跑排球場 (兩圈) — 3m
2. 靜態暖身與伸展 — 5m
3. 動態暖身 (開合跳/側併步) — 5m
4. 關節活動度 (髖胸伸展) — 5m
5. 基礎球操 (指力/手腕/對空) — 7m

### 2.3 訓練菜單庫 (Drill Library) 與詳細操作說明
- **分類標籤滑動容器**：支援手機端水平觸控滑動 (`overflow-x-auto` + `flex-shrink-0`)，點擊標籤具備自動平滑置中滾動效果。
- **操作要領彈窗 (Drill Details)**：點擊任一項目之「📖 說明」按鈕，即可檢視並編輯該項目的跑位規則、練習要點與分組模式。
- **自由加選與自訂**：可隨時從分類庫加選項目至今日菜單，或建立全新訓練項目。
- **本地持久化**：所有新增、編輯內容即時序列化為 JSON 儲存於 `localStorage`。

### 2.4 自動出席人數分組計算 (Auto-Grouping Engine)
- `pair` (兩人對打)：$N \div 2$，若有餘數提示包含 1 組 3 人。
- `trio` (三人防守)：$N \div 3$，若有餘數提示包含 4 人輪轉。
- `six` (實戰 Play)：滿 12 人提示分兩隊，未滿則提示全員輪替。

### 2.5 雙軌非同步匯出端點 (Export Pipelines)
1. **LINE 文字序列化器**：一鍵將工整排版包含日期、時間軸、分組提示之純文字寫入剪貼簿，並觸發 Toast 提示。
2. **安全容器圖片算繪器**：以 `visibility: hidden` 安全容器渲染封閉式格狀排版，透過 `html2canvas` 以雙倍（2x Retina）解析度匯出為清晰 PNG 圖檔。
