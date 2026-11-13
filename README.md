# LabSteward：實驗室財產管理與採購 AI Agent

> DIC3 Project Proposal Refactor using OpenSpec
> 組員：潘兆邠、邱冠霖、林浚偉、張皓崴、黃翊豪｜資訊工程學系
> 狀態：**Proposal stage**。本 Repository 中所有數值目標皆為 Expected Outcomes，尚非實驗結果。

## 核心定位

LabSteward 是一個結合**財產法規知識**與**工具調用**的 Domain AI Agent，涵蓋實驗室財產的「管理」（財產導入、借用登記、殘值預估、財產報廢）與「採購」（確認需求、查詢庫存、上網比價、生成採購清單）兩大流程。

設計原則可濃縮為一句話：

> **LLM 負責理解與協調，確定性工具負責計算，規則引擎負責合規，人負責核准。**

## Four Core Innovations（C1–C4）與可驗證假設

| 編號 | 核心創新 | 研究假設 | 驗證指標 |
|---|---|---|---|
| C1 | LLM 只協調、不判斷（Hybrid LLM + Deterministic Tools） | 金額門檻、年限、折舊交給規則引擎與程式後，法規違規率低於單一 LLM baseline | 法規違規率、任務成功率、pass^4 |
| C2 | 管理與採購打通（Utilize before Acquire） | 採購前強制查庫存，可找出可借用或移撥的閒置設備 | 庫存命中率、避免之重複採購金額 |
| C3 | 雙軌殘值（Book vs. Market Value） | 同時輸出帳面價值與市場估值區間，比單一帳面價值更貼近二手市場行情 | 殘值 MAPE、區間涵蓋率 |
| C4 | 法規可追溯（Traceable Compliance） | 每項建議附法規條文或資料來源並寫入稽核紀錄，可降低文件退件 | 引用正確率、稽核紀錄完整率 |

## 本次作業對照（DIC3 Refactor）

| # | 作業要求 | 對應檔案 |
|---|---|---|
| 1 | 安裝 OpenSpec，熟悉 spec-driven development | 下方「OpenSpec 使用方式」、`openspec/` |
| 2 | 整理 A–F 摘要、5 cores、3 trade-offs、10 details | `docs/01-abstract-A2F.md`、`docs/04-deep-research.md` |
| 3 | 將 design.md 轉為 OpenSpec workflow | `DESIGN.md` → `openspec/config.yaml` + `openspec/changes/*` |
| 4 | Introduction refactor | `docs/02-introduction.md` |
| 5 | Related Work refactor | `docs/03-related-work.md` |

## Repository 結構

```text
labsteward/
├── README.md
├── DESIGN.md                      # 人讀的系統設計文件（Problem → ... → Limitations）
├── docs/
│   ├── 01-abstract-A2F.md         # A–F 摘要、300 字中文摘要、IEEE 英文摘要
│   ├── 02-introduction.md         # 緒論 refactor
│   ├── 03-related-work.md         # 相關研究 refactor + Article Summary Table
│   └── 04-deep-research.md        # 5 Mindsets / 3 Controversies / 10 Questions → spec 追溯
└── openspec/
    ├── config.yaml                # OpenSpec 2.x 專案設定：context、rules、operations
    ├── specs/                     # 已實作行為的 source of truth（proposal 階段為空）
    └── changes/                   # 依時程拆成四個 change proposal
        ├── add-agent-core/        # W1–W2：Planner、Guardrail、RBAC、人工核准、稽核
        ├── add-asset-management/  # W3–W6：M1–M4、狀態機、雙軌殘值
        ├── add-procurement/       # W7–W8：P1–P4、先查庫存、比價、門檻判定
        └── add-evaluation-harness/# W9–W10：50 題任務、Baseline、指標
```

## OpenSpec 使用方式

```bash
# 1. 安裝（需要 Node.js）
npm install -g @fission-ai/openspec@latest
openspec --version

# 2. 在 repo 根目錄初始化（會依你使用的 AI 工具產生對應設定檔；已存在的 openspec/ 內容會保留）
openspec init

# 3. 檢視與驗證
openspec list                    # 列出進行中的 changes
openspec show add-agent-core     # 檢視單一 change
openspec validate --all --strict   # 結果：4 passed, 0 failed

# 4. 實作完成後歸檔：把 change 的 spec delta 合併進 openspec/specs/
openspec archive add-agent-core
```

Spec-driven 流程：**Propose（proposal.md + spec deltas）→ Review → Apply（依 tasks.md 實作）→ Archive（合併進 specs/）**。

本專題目前尚未實作，因此所有需求都放在 `openspec/changes/` 中；每完成一個時程階段就 archive 一個 change，`openspec/specs/` 會逐步成為系統行為的正式規格。每個 Requirement 的 Scenario（WHEN / THEN）同時就是 `eval/` 的測試案例來源。
