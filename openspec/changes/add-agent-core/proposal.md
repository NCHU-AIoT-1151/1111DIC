# Change: Add LabSteward agent core (Planner, Guardrail, RBAC, Human-in-the-loop, Audit)

## Why
通用 LLM 直接回答數值與法規問題會產生幻覺：把 LLM 當端到端求解器時，庫存決策成本比混合式架構高約 32% [4]；多步驟工具任務的穩定度也不足（τ-bench retail 情境 pass^8 低於 25%）[6]。財產與採購屬於「錯一次就有後果」的場景，因此需要先建立一個讓 LLM 只負責理解與協調、由規則與人負責判斷的核心骨架，後續管理與採購模組才能安全地掛上去。

## What Changes
- 新增 Planner & Reasoning Agent：意圖辨識、參數抽取、追問迴圈（上限 T_max）、分派子 Agent、輸出前自我檢核。
- 新增角色權限控管（RBAC）：Member / Custodian / PI。
- 新增規則引擎與 `knowledge/rules.yaml`：金額門檻、財產定義、年限，皆可設定。
- 新增法規 RAG（BM25 + 向量混合檢索），每項建議必須附證據引用。
- 新增人工核准節點：凡會改變帳務狀態、違規或高不確定的結果都需人工核准。
- 新增 append-only 稽核紀錄（Audit Log）。

## Impact
- Affected specs: `agent-orchestration`（新增）、`compliance-guardrail`（新增）
- Affected code: `agent/planner.py`、`agent/guardrail.py`、`agent/audit.py`、`knowledge/rules.yaml`、`knowledge/regulations/`
- 時程：W1–W2
