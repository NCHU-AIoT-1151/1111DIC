# Design: add-asset-management

## Residual Value Model (M3)
- 帳面價值：`V_book(t) = C × max(0, 1 − t / L)`；C 為取得成本、t 為已使用年數、L 為財物標準分類之最低使用年限。校方若採其他公式，可於設定檔替換。
- 市場價值：`V_mkt(t) = C × (1 − d)^t × q`；d 為同類別二手掛牌資料以 log-linear 迴歸估計之年貶值率（參考 [11]），q 為狀況係數（正常 / 小瑕疵 / 故障）。
- 類別資料量 ≥ N_min 時改用 Gradient Boosting，並以 bootstrap 產生 80% 預測區間；不足時退回類別預設值並標示 LOW_CONFIDENCE。

## Decisions
- 殘值結果一律以「帳面值 + 市場區間 + 差異說明 + 選項」呈現，而非單一數字，因為實驗儀器缺乏成交資料，假精確比沒有數字更危險。
- 建議值為選項（KEEP_USING / REPAIR_OR_KEEP / TRANSFER_OR_SHARE / DISPOSE），報廢必須同時符合狀態與成本條件。
- 狀態機在資料庫層強制，LLM 無法繞過。

## Risks
- 掛牌價高於成交價 → 標示資料來源為掛牌價，並以區間呈現。
- OCR 抽取錯誤 → 欄位低信心時要求使用者確認，抽取以 F1 評估。
