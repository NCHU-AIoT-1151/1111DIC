# Deep Research：5 Cores、3 Trade-offs、10 Details

> 對應期末規範第三節：Q1 五個核心思維模式（5 cores）、Q2 三個最重要的專業爭論（3 trade-offs）、Q3 十個深度理解問題（10 details）。
> 本次 refactor 的重點是**可追溯性**：每個思維、爭論與問題都對應到 `openspec/` 中的 Requirement 或 Scenario，證明深度研究真的影響了系統設計。

## Q1. 五個核心思維模式（5 Cores）

| # | 思維模式 | 意義 | 證據 | 系統設計 | OpenSpec 對應 |
|---|---|---|---|---|---|
| 1 | 帳實相符（Record = Reality） | 帳上每件財產都必須找得到、對得上保管人與位置 | 財物管理辦法明定保管人責任、盤點與減損程序 [13] | 寫入動作只能由工具執行並留稽核紀錄；支援 QR 碼盤點 | compliance-guardrail：Append-Only Audit Log |
| 2 | 全生命週期（Lifecycle） | 取得、使用、維護、移轉、報廢是一條鏈，決策要看所處階段 | 法規分別規範取得、減損、報廢、讓與與變賣估價 [13][17] | 狀態機管理每件財產，非法轉移直接拒絕 | asset-management：Asset Lifecycle State Machine |
| 3 | 先共享，再採購（Utilize before Acquire） | 採購前先確認既有或閒置資源能否滿足需求 | 監察院發現低使用率儀器 [2]；韓國 2026 年轉向共享 [1] | 採購第一步強制 `query_inventory()` | procurement：Inventory-First Procurement |
| 4 | 合規與分級授權（Compliance by Threshold） | 同一件事因金額、年限不同，流程與核准層級不同 | 財產 1 萬元 / 2 年定義 [13]；小額 15 萬、公告 150 萬 [15]；報廢分級核定 [13] | 門檻寫成 rules.yaml，由規則引擎判定，LLM 只解釋 | compliance-guardrail：Configurable Regulation Rules；agent-orchestration：Deterministic Values Only |
| 5 | 帳面價值 ≠ 經濟價值 | 法定年限是會計工具，不代表實際價值或可用性 | 年限到仍可用者不應報廢 [14]；筆電年貶值約 14.4% [11]；GPU 年限爭議 [16] | 殘值同時輸出帳面值與市場區間，建議為選項而非單一答案 | residual-valuation：Dual-Track Residual Value；Option-Based Advice |

## Q2. 三個最重要的專業爭論（3 Trade-offs）

每個爭論依「觀點 A ↔ 觀點 B → Evidence → Consensus → Unresolved → 本專題的取捨」撰寫。

### Trade-off 1：法定 / 會計年限 ↔ 實際經濟壽命

- **觀點 A**：依法定或會計年限折舊，確保一致性與可稽核性；雲端業者普遍採 6 年折舊 GPU [16]。
- **觀點 B**：運算設備技術週期短，經濟壽命遠短於帳面年限，帳面價值會高估資產 [16]；二手資料顯示電子產品呈指數型貶值 [11]。
- **Consensus**：會計年限是政策選擇，必須揭露；維修或汰換決策應另看經濟價值。
- **Unresolved**：實驗儀器缺乏二手成交資料，經濟價值如何可靠估計？
- **本專題取捨**：兩者都算、並列呈現；資料不足時標示 LOW_CONFIDENCE，而不是硬給一個數字。

### Trade-off 2：集中共享 ↔ 實驗室自有

- **觀點 A**：集中管理可提升使用率、降低重複採購 [1][2]。
- **觀點 B**：研究時程需要隨時可用的設備；集中後若缺維護預算與人力同樣會閒置 [1]。
- **Consensus**：高價、低頻設備走共享；低價、高頻設備留在實驗室，但需有借用紀錄與使用率可見性。
- **Unresolved**：使用費如何訂定？PI 轉校時設備如何移轉 [1]？
- **本專題取捨**：不強制集中，而是讓使用率可見；低使用率設備建議移撥或共享（TRANSFER_OR_SHARE）。

### Trade-off 3：全自動 Agentic Procurement ↔ Human-in-the-loop

- **觀點 A**：Agent 可大幅提高採購效率；Gartner 預測 2028 年 90% B2B 採購由 Agent 中介 [3]。
- **觀點 B**：Hackett 指出僅 4% 能大規模部署 [3]；τ-bench 顯示穩定度不足 [6]；LLM 端到端有 hallucination tax [4]。
- **Consensus**：LLM 作為自然語言介面與協調者，關鍵計算交給確定性工具，具法律效力的動作需人工核准。
- **Unresolved**：Agent 出錯的責任歸屬？LLM 推理過程如何達到稽核要求？
- **本專題取捨**：唯讀查詢自動化；所有會改變帳務狀態的動作人工核准並寫入稽核紀錄。

## Q3. 十個深度理解問題（10 Details）

| # | 推理層次 | 問題 | 系統如何回應 | OpenSpec 對應 |
|---|---|---|---|---|
| 1 | Why | 為什麼財產門檻要同時看「金額」與「使用年限」？只看金額會出什麼問題？ | 規則檔同時定義兩個條件 | Configurable Regulation Rules：Asset versus item classification |
| 2 | What-if | 一台已超過最低使用年限、仍可正常運作的示波器，PI 想換新機，系統該怎麼建議？為什麼不能直接建議報廢？ | 建議繼續使用或移撥，不起草一般報廢 | Disposal Eligibility Check：Expired but working |
| 3 | Trade-off | 14 萬元與 16 萬元的設備價差 2 萬，但流程差異是什麼？何時多花錢反而更划算？ | 依總金額判定流程，並以 TCO 排序 | Threshold Routing；Traceable Purchase List |
| 4 | Failure Case | 比價 Agent 找到比市價便宜 40% 的商品，可能原因是什麼？系統如何處理？ | 標記異常低價並說明可能原因，人工確認前不列為首選 | Abnormal Price Detection |
| 5 | Evidence | 如何證明實驗室真的需要再買一台 GPU 主機，而不是使用率不足？ | 以借用紀錄計算使用率，先查庫存 | Inventory-First Procurement；Option-Based Advice：Low utilization |
| 6 | Comparison | 直線法與指數型折舊分別適合哪些設備？各自在哪個階段高估或低估殘值？ | 帳面值用直線法，市場值用 log-linear / GBM | Dual-Track Residual Value |
| 7 | Design Decision | 為什麼門檻判定用規則引擎而不是讓 LLM 讀法規後判斷？法規修訂時維護成本有何不同？ | 只改 rules.yaml 並重跑測試 | Deterministic Values Only；School-specific threshold |
| 8 | Real-world Scenario | 學生畢業離校，名下保管 3 件財產且有 1 件借出未還，系統應觸發哪些流程？ | 產生移交與歸還任務給管理人與 PI | Custodian Departure Check：Graduating student |
| 9 | What-if | 同一需求被拆成兩張 8 萬元請購單，可能違反什麼原則？Agent 如何偵測？ | 偵測同計畫、相近規格、合計跨門檻的請購單 | Threshold Routing and Split-Order Detection：Split order |
| 10 | Trade-off | 借用登記越嚴格資料越完整，但使用者越可能不登記，如何取捨？ | 衝突檢查與到期提醒自動化，降低登記成本 | Lending with Conflict Check |
