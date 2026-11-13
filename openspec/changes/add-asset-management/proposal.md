# Change: Add asset management (M1–M4) and dual-track residual valuation

## Why
校內財產系統多半只做登帳，無法回答「這台還值多少」「有沒有閒置的可以先借」；監察院調查也指出貴重儀器使用率偏低、缺乏退場機制 [2]。同時，法定最低使用年限只是「正常使用下的最低年限」，到期不代表應報廢 [14]，帳面價值與經濟價值可能相差甚大 [11][16]。

## What Changes
- M1 財產導入：從發票 / 報價單抽取品名、規格、金額、日期、廠商，建議財物分類並區分財產與物品。
- M2 借用 / 租用登記：時段衝突檢查、到期提醒、使用率紀錄。
- M3 雙軌殘值：帳面價值（直線法）與市場價值區間（log-linear 或 GBM + bootstrap）。
- M4 財產報廢：年限與狀態檢核、報廢申請草稿。
- 財產生命週期狀態機：IN_STOCK → LENT → MAINTENANCE → PENDING_DISPOSAL → DISPOSED，非法轉移直接拒絕。
- 保管人離校交接檢查。

## Impact
- Affected specs: `asset-management`（新增）、`residual-valuation`（新增）
- Depends on: `add-agent-core`
- Affected code: `agent/management_agent.py`、`tools/inventory.py`、`tools/lending.py`、`tools/valuation.py`、`tools/docgen.py`
- 時程：W3–W6
