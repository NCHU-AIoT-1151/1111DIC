# DESIGN.md — LabSteward

> 本文件依期末規範的交接順序撰寫：Problem → Requirements → Architecture → Components → Data → AI/Analytics → Domain Knowledge → Algorithm → Implementation Mapping → How to Run → Results → Limitations。
> 可驗證的需求以 OpenSpec 管理（`openspec/`），本文件負責解釋「為什麼這樣設計」。

## 1. Problem

1. **資訊斷裂**：財產清冊、借用紀錄、採購文件分散於 Excel、紙本與學校系統。
2. **價值不透明**：帳面價值只反映法定年限，無法回答「修還是換」。
3. **先買再說**：請購前缺少「先查庫存與閒置財產」的強制步驟，造成重複購置。
4. **法規門檻高**：金額門檻、報價份數、最低使用年限與報廢分級核定不易掌握，文件常被退件。

## 2. Requirements（摘要；完整規格見 OpenSpec）

| 能力（capability） | 重點需求 | Change |
|---|---|---|
| agent-orchestration | 意圖辨識、追問迴圈（上限 T_max）、RBAC、分派子 Agent、數值與法規判斷不得由 LLM 直接產生 | add-agent-core |
| compliance-guardrail | `rules.yaml` 可設定門檻、證據引用、人工核准、稽核紀錄 | add-agent-core |
| asset-management | M1 導入、M2 借用、M4 報廢、財產生命週期狀態機、離校交接 | add-asset-management |
| residual-valuation | M3 雙軌殘值、區間、低信心標示、不自動建議報廢 | add-asset-management |
| procurement | P1–P4、先查庫存、≥3 筆有效報價、異常低價、拆單偵測、TCO 排序 | add-procurement |
| evaluation | 50 題任務、B1–B3、pass^k、法規違規率、MAPE 等 | add-evaluation-harness |

## 3. Architecture

```text
① User / Application Scenario（成員 / 財產管理人 / PI，RBAC）
        ↓
② Query / Input（自然語言、發票 / 報價單 PDF、財產標籤照片；Streamlit，可延伸 LINE Bot）
        ↓
③ Planner & Reasoning Agent（LLM，ReAct + Tool Calling，追問、分派、自我檢核）
        ├── Management Agent：M1 導入｜M2 借用｜M3 殘值｜M4 報廢
        └── Procurement Agent：P1 需求｜P2 查庫存｜P3 比價｜P4 採購清單
        ↓
④ Data Acquisition / Retrieval（SQLite、借用紀錄、財物標準分類表、電商 / 共同供應契約、二手掛牌價）
        ↓
⑤ Analytics Engine（使用率、殘值迴歸、Entity Matching、TCO）
        ↓
⑥ Domain Knowledge（法規 RAG：BM25 + 向量；rules.yaml）
        ↓
⑦ Decision & Recommendation（Guardrail + 證據引用 + 不確定度 → Human-in-the-loop）
        ↓
⑧ Output & Interaction（Word / Excel 文件、借用單、通知、Audit Log）
```

分層責任：

| 層 | 負責 | 不負責 |
|---|---|---|
| LLM（③） | 理解、追問、拆解任務、選工具、解釋結果 | 計算金額、判定門檻、決定報廢 |
| 確定性工具（④⑤） | 查資料、計算殘值、比對商品、排序 | 法規解釋 |
| 規則引擎（⑥⑦） | 門檻、年限、拆單、狀態轉移合法性 | 自然語言互動 |
| 人（⑦） | 核准會改變帳務狀態或具法律效力的動作 | — |

## 4. Components

| 編號 | 元件 | 工具函式（預定） |
|---|---|---|
| M1 | 財產導入 | `ocr_extract()`、`suggest_category()` |
| M2 | 借用 / 租用登記 | `check_conflict()`、`create_lending()`、`remind_due()` |
| M3 | 殘值預估 | `book_value()`、`market_value()` |
| M4 | 財產報廢 | `check_min_life()`、`draft_disposal()` |
| P1 | 確認需求 | `normalize_spec()` |
| P2 | 查詢庫存 | `query_inventory()` |
| P3 | 上網比價 | `web_search()`、`entity_match()` |
| P4 | 採購清單 | `route_by_threshold()`、`build_purchase_list()` |

## 5. Data

| 資料 | 來源 | 用途 |
|---|---|---|
| 財產清冊、借用紀錄 | 本實驗室（去識別化）+ 合成資料 | M1–M4、P2 |
| 財物標準分類表 | 行政院主計總處 [14] | 最低使用年限 L、分類 |
| 二手掛牌價 | 二手平台（筆電、螢幕、GPU、量測儀器） | M3 市場價值 |
| 電商 / 共同供應契約 | 網頁擷取（Playwright，遵守使用條款） | P3 |
| 法規條文 | 財物管理辦法 [13]、請購規範 [15]、變賣估價程序 [17] | RAG + rules.yaml |

## 6. AI / Analytics

- **Planner**：LLM（支援 Tool Calling），ReAct 式推理 [12]；以 LangGraph 或自寫狀態機控制迴圈上限與人工核准節點。
- **殘值**：log-linear 迴歸（參考 [11]）；資料量 ≥ N_min 時改用 Gradient Boosting + bootstrap 80% 區間。
- **商品比對**：結構化規格欄位比對 + LLM 判斷，因 LLM 比對對 prompt 敏感 [7]。
- **檢索**：BM25 + 向量混合檢索並加數值過濾（參考 [9]）。

## 7. Domain Knowledge

- 財產：單價 1 萬元以上且使用年限 2 年以上；未達者為物品 [13][14]。
- 最低使用年限是「正常使用下的最低年限」，到期仍可用者應延後報廢 [14]。
- 採購：15 萬元以下小額採購；150 萬元以上公告金額（以臺大規範為例，各校可不同）[15]。
- 所有門檻寫在 `knowledge/rules.yaml`，校方規定不同時只改設定檔。

## 8. Algorithm

- Algorithm 1：LabSteward Main Loop（意圖 → 追問 → 授權 → 子 Agent → 引用 → 規則檢核 → 人工核准 → commit + audit）
- Algorithm 2：Dual-track Residual Value Estimation（M3）
- Algorithm 3：Procurement Agent（P1–P4）

完整虛擬碼見 Proposal 第 4.3 節；各演算法的行為約束已轉成 OpenSpec Requirement 與 Scenario。

## 9. Implementation Mapping

| 模組 | OpenSpec capability | 程式路徑（預定） |
|---|---|---|
| ③ Planner | agent-orchestration | `agent/planner.py` |
| M1–M4 | asset-management、residual-valuation | `agent/management_agent.py`、`tools/inventory.py`、`tools/lending.py`、`tools/valuation.py` |
| P1–P4 | procurement | `agent/procurement_agent.py`、`tools/web_search.py` |
| ⑥⑦ | compliance-guardrail | `knowledge/rules.yaml`、`knowledge/regulations/*.md`、`agent/guardrail.py` |
| ⑧ | compliance-guardrail | `tools/docgen.py`、`agent/audit.py` |
| 評估 | evaluation | `eval/tasks.jsonl`、`eval/run_eval.py`、`eval/metrics.py` |

## 10. How to Run

尚未實作。預定：`pip install -r requirements.txt` → `streamlit run app/streamlit_app.py`；評估：`python eval/run_eval.py --k 4`。

## 11. Results

尚無實驗結果。預期成果見 `docs/01-abstract-A2F.md` 的 F 段。

## 12. Limitations

- 實驗儀器缺乏二手成交資料，市場價值以掛牌價近似，需標示低信心。
- 各校財物與請購規範不同，規則檔需由各校設定與驗證。
- 50 題任務規模有限，pass^k 結果的統計意義需保守解讀。
- Agent 出錯的責任歸屬未解決；因此具法律效力的動作一律人工核准。
