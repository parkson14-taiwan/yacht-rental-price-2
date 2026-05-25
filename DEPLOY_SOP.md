# Yacht Calculator 穩定部署 SOP

> 目的：之後每次新增/修改 yacht 價格時，固定走同一套「高成功率」流程，避免網站再掛掉。

## 1) 固定技術策略（不要改）

1. `index.html` 只做本地跳轉到 `./yacht-calculator_vS250601.html`。
2. `yacht-calculator_vS250601.html` 只允許：
   - 同源讀取 `./yacht-calculator_base_vS250601.html`
   - 同頁渲染（`document.write`）
   - 失敗時 fallback 到 `./yacht-calculator_base_vS250601.html`
3. **禁止**以下做法：
   - `blob:` URL 轉頁
   - `raw.githack` / `raw.githubusercontent` 當主入口
   - 跨來源注入主頁內容

## 2) 新增 Yacht 的資料格式規則

新增船隻時，資料格式請遵守現有 schema：

- `id`
- `name`
- `basePax`
- `maxCapacity`
- `prices.weekday/weekend/ph`（`base4h`, `subsequentHour`, `specificExtraPaxCost`）
- `extraPaxCost`（通常 0）
- `variableExtraPax`（通常 `true`）
- `gstApplicable`
- `includedServices`
- `addons`

### 價格換算規則

若業務給的是「每 5 pax 加價」，要先換成「每 1 pax」：

- `500 per 5 pax` → `specificExtraPaxCost: 100`
- `600 per 5 pax` → `specificExtraPaxCost: 120`

## 3) 變更流程（每次都照做）

1. 從 `main` 開新分支（`codex/...`）。
2. 只做最小必要改動（優先單檔、單目的）。
3. 開 PR 前自檢：
   - 不含 `blob:`
   - 不含外部入口（`raw.githack`, `raw.githubusercontent`）
   - fallback 路徑仍是 `./yacht-calculator_base_vS250601.html`
   - 新增 yacht 可在資料內被找到
4. 開 PR，描述「改了什麼、為何安全」。
5. 合併後做 smoke test：
   - 首頁可正常進入 calculator
   - 輸入乘客數後可選船
   - 新船可選、weekday/weekend/ph 都能出價
   - GST 顯示正確（9%）

## 4) 緊急回復（站掛時）

1. 先回復到最近穩定 commit（只回復壞掉檔案）。
2. 用新分支重做一次「最小改動 PR」。
3. 合併後再驗證，確認恢復再做下一步。

---

最後更新：2026-05-25（Asia/Taipei）
