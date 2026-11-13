# Change: Add evaluation harness

## Why
期末規範要求 Dataset、Baseline、Metrics 與 Comparison；財產與採購屬高風險決策，需量測穩定度（pass^k）[6] 與法規違規率，而不只平均成功率。

## What Changes
- 建立約 50 題情境任務（管理 25、採購 25），人工標註正確答案與應引用之法規。
- 實作三個 Baseline：B1 人工 Excel 流程、B2 單一 LLM 端到端（無工具）、B3 純 RAG 問答。
- 實作指標：任務成功率、pass^k（k = 4）、欄位抽取 F1、殘值 MAPE、與人工最低價差距、商品比對 precision、法規違規率、作業時間、SUS（5–8 位實驗室成員）。
- 報告格式區分 Expected Outcomes 與實測結果。

## Impact
- Affected specs: `evaluation`（新增）
- Depends on: 前三個 changes
- Affected code: `eval/tasks.jsonl`、`eval/run_eval.py`、`eval/metrics.py`
- 時程：W9–W10
