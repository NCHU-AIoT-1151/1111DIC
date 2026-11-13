# Abstract（A–F Refactor）

> 修改重點：(1) 每段只講一件事，B 段收斂成三個 Gap 並明確寫出 Research Gap；(2) C 段用一句話說清楚「誰負責什麼」；(3) D 段加入 spec-driven 開發，讓需求可驗證；(4) F 段全部改為可檢驗的預期，不寫數字結果。

## A. Attention Getter — Motivation

大學實驗室持有大量以公帑購置的儀器與資訊設備，「買了不用、用了不登記、壞了不報廢」卻長期存在。監察院調查國科會補助之貴重儀器時發現，多所國立大學的部分設施使用率偏低，部分核心設施全年服務次數不到 100 次，且計畫結束後缺乏退場機制 [2]。十多年後，這仍是政策焦點：韓國政府於 2026 年 5 月宣布將研究設備由「教授個人實驗室管理」轉為「全校共享制度」，並自 2027 年起全國普查閒置設備 [1]。與此同時，採購端正快速走向 Agentic AI，但 2024 年雖有 49% 的採購團隊試行 AI，僅 4% 達到大規模部署 [3]——需求與技術都在，落地仍然困難。

## B. But — Challenge

現有工具無法直接套用在實驗室場景，主要有三個缺口：

1. **管理缺口**：校內財產系統以登帳與盤點為主，無法回答「這台現在還值多少」「實驗室裡有沒有閒置的可以先借」。
2. **可靠度缺口**：通用 LLM 直接當求解器會產生「hallucination tax」，在庫存決策上比混合式架構多出約 32% 成本 [4]；在多步驟工具任務中，同一任務重跑 8 次都成功的比例低於 25% [6]。
3. **領域缺口**：網購比價 Agent 在多屬性、篩選與排序條件下成功率仍低 [5]，而且不懂政府採購金額門檻、財物最低使用年限與報廢分級核定。

**Research Gap**：目前沒有一個系統把「財產生命週期」與「政府採購法規」整合到同一個可稽核的 Agent 中。

## C. Cure — Proposed Solution

本專題提出 **LabSteward**，一個實驗室財產管理與採購 AI Agent，涵蓋「管理」（財產導入、借用 / 租用登記、殘值預估、財產報廢）與「採購」（確認需求、查詢庫存、上網比價、生成採購清單）兩大模組。其核心原則是：**LLM 負責理解與協調，確定性工具負責計算，規則引擎負責合規，人負責核准。**

## D. Development — Method

系統以 LLM Planner（ReAct 式推理與 Tool Calling）理解需求、追問缺漏資訊並拆解任務，分派給管理子 Agent 與採購子 Agent。需要正確答案的部分交給確定性元件：SQLite 財產資料庫與生命週期狀態機、結合法定折舊與二手市場資料的雙軌殘值模型、以結構化規格欄位加 LLM 判斷的商品實體比對，以及由法規條文建立的混合檢索知識庫（BM25 + 向量）與可設定的規則檔（rules.yaml）。採購流程強制「先查庫存、再比價」；所有會改變帳務狀態的動作都需人工核准並寫入稽核紀錄，最後輸出 Word / Excel 採購清單、報廢申請草稿與借用單。系統需求以 OpenSpec 撰寫成 Requirement 與 Scenario，作為實作與測試的共同依據。

## E. Experiments — Evaluation

預計以本實驗室去識別化財產清冊為主、合成資料為輔，建立約 50 個情境任務（管理 25、採購 25），比較三種 Baseline：人工 Excel 流程、單一 LLM 端到端回答（無工具）、純 RAG 問答。評估指標包含任務成功率與 pass^k（k = 4）穩定度、欄位抽取 F1、殘值預估 MAPE、與人工最低價差距、商品比對 precision、法規違規率、作業時間與 SUS 使用者滿意度。

## F. Findings — Expected Outcomes

本專題尚未進行實驗，以下為預期成果：

1. 因法規判斷由規則引擎而非 LLM 決定，法規違規率預期明顯低於單一 LLM baseline。
2. 「先查庫存再採購」的流程預期能找出部分可借用或移撥的閒置設備，減少重複購置。
3. 殘值模組預期能同時提供帳面價值與市場價值區間，並在資料不足時誠實標示低信心。
4. 預期產出可直接送審的文件草稿，降低財產管理人員的文書時間。

---

## 300 字中文摘要

大學實驗室以公帑購置大量儀器，但閒置、未登記與未適時報廢的問題長期存在；監察院曾指出貴重儀器使用率偏低，韓國亦於 2026 年轉向全校共享並普查閒置設備。現有校內財產系統只做登帳，通用 LLM 直接計算會產生幻覺且多步驟任務不穩定，網購比價 Agent 則不懂政府採購門檻與財物年限規範。本專題提出 LabSteward，一個實驗室財產管理與採購 AI Agent，核心原則為「LLM 負責理解與協調，確定性工具負責計算，規則引擎負責合規，人負責核准」。系統以 LLM Planner 拆解任務，分派管理（導入、借用、殘值、報廢）與採購（需求、查庫存、比價、清單）兩個子 Agent，整合 SQLite 財產資料庫、雙軌殘值模型、商品實體比對、法規混合檢索與可設定規則檔，並強制「先查庫存、再採購」與人工核准。預計以約 50 個情境任務，比較人工 Excel、單一 LLM 與純 RAG 三種 Baseline，評估任務成功率、pass^k 穩定度、法規違規率與殘值誤差。預期 LabSteward 能降低法規違規、減少重複採購，並產出可追溯、可送審的文件。

---

## IEEE Transactions Style Abstract

**Title**—LabSteward: A Regulation-Grounded, Human-in-the-Loop AI Agent for Laboratory Asset Lifecycle Management and Procurement

**Abstract**—Publicly funded research equipment in university laboratories is frequently underutilized, unregistered, or retained beyond its economic life, and recent national policies have begun to shift such equipment from individual ownership toward institution-wide sharing. Existing campus property systems, however, primarily support bookkeeping and cannot answer questions about current asset value or idle availability. Meanwhile, large language models (LLMs) used as end-to-end solvers incur measurable decision costs and exhibit low reliability in multi-step tool use, and shopping agents remain error-prone on multi-attribute filtering while lacking knowledge of public procurement regulations. This paper proposes LabSteward, an AI agent for laboratory asset management and procurement in which the LLM interprets requests, asks clarification questions, and coordinates tools, while deterministic components perform all calculations and compliance decisions. The architecture comprises eight modules, a management sub-agent for asset import, lending, residual valuation, and disposal, and a procurement sub-agent that enforces an inventory-first workflow before price comparison. LabSteward integrates an SQLite asset database with a lifecycle state machine, a dual-track residual value model combining statutory depreciation with second-hand market data, two-stage product entity matching, hybrid retrieval over regulation texts, and a configurable rule engine. All state-changing actions require human approval and are recorded in an append-only audit log. The evaluation plan uses approximately 50 annotated laboratory scenarios and compares LabSteward with a manual spreadsheet workflow, a tool-free LLM, and retrieval-only question answering in terms of task success, pass^k reliability, regulation violation rate, valuation error, and task time. LabSteward is expected to reduce regulatory violations and duplicate purchases while producing traceable, review-ready documents.

**Index Terms**—AI agents, asset management, human-in-the-loop, large language models, procurement, residual value estimation, retrieval-augmented generation, tool calling.
