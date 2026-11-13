# Change: Add inventory-first procurement agent (P1–P4)

## Why
請購前缺乏「先查庫存與閒置財產」的步驟，導致重複購置；韓國 2026 年的研究設備政策也轉向共享優先 [1]。網購比價 Agent 在多屬性、篩選與排序需求上成功率低 [5]，LLM 商品比對對 prompt 高度敏感 [7]，且這些 Agent 都不懂政府採購法的金額門檻與報價份數要求。

## What Changes
- P1 確認需求：用途、規格、數量、預算、期限的正規化與追問。
- P2 查詢庫存：採購前強制查詢在庫與閒置財產；可滿足時建議借用或移撥。
- P3 上網比價：電商與共同供應契約查詢、規格欄位 + LLM 的商品實體比對、去重、異常低價偵測；有效報價不足 3 筆時放寬條件重搜，最多 K_max 次。
- P4 採購清單：TCO 排序、依金額判定採購流程、拆單偵測、預算檢查、輸出含報價來源的 Word / Excel 清單。

## Impact
- Affected specs: `procurement`（新增）
- Depends on: `add-agent-core`、`add-asset-management`（P2 需要財產資料庫）
- Affected code: `agent/procurement_agent.py`、`tools/web_search.py`、`tools/docgen.py`
- 時程：W7–W8
