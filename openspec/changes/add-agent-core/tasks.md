## 1. Rules & Knowledge Base
- [ ] 1.1 整理財物管理辦法、財物標準分類、請購規範、變賣估價程序，切塊存入 `knowledge/regulations/`
- [ ] 1.2 撰寫 `knowledge/rules.yaml`（財產定義、採購門檻、最低使用年限、報廢分級）並加上版本號
- [ ] 1.3 建立 BM25 + 向量混合索引與檢索 API

## 2. Planner
- [ ] 2.1 定義意圖集合（IMPORT, LEND, VALUE, DISPOSE, PROCURE, QUERY）與各意圖必要參數
- [ ] 2.2 實作追問迴圈與 T_max 上限
- [ ] 2.3 實作子 Agent 分派介面（Management / Procurement）

## 3. Guardrail, RBAC, Audit
- [ ] 3.1 實作 RBAC（Member / Custodian / PI）
- [ ] 3.2 實作 RuleEngine.check() 與違規清單
- [ ] 3.3 實作人工核准節點（核准 / 退回附回饋）
- [ ] 3.4 實作 append-only Audit Log

## 4. Verification
- [ ] 4.1 每個 Scenario 撰寫自動化測試
- [ ] 4.2 `openspec validate add-agent-core --strict`
