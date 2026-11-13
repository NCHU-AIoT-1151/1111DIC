## 1. Data Layer
- [ ] 1.1 建立 SQLite schema（assets, lendings, custodians, audit）與狀態機約束
- [ ] 1.2 匯入去識別化財產清冊與合成資料
- [ ] 1.3 匯入財物標準分類表（類別 → 最低使用年限）

## 2. M1 Import / M2 Lending
- [ ] 2.1 發票 / 報價單 OCR 與欄位抽取
- [ ] 2.2 財產 / 物品判定與分類建議
- [ ] 2.3 借用衝突檢查、到期提醒、使用率紀錄
- [ ] 2.4 保管人離校交接檢查

## 3. M3 Valuation
- [ ] 3.1 蒐集二手掛牌資料（筆電、螢幕、GPU、量測儀器）
- [ ] 3.2 實作帳面價值與 log-linear 市場價值
- [ ] 3.3 實作 GBM + bootstrap 區間與 LOW_CONFIDENCE 退回邏輯
- [ ] 3.4 實作建議規則（Algorithm 2 第 12–15 行）

## 4. M4 Disposal
- [ ] 4.1 年限與狀態檢核
- [ ] 4.2 報廢申請 Word 草稿產生

## 5. Verification
- [ ] 5.1 每個 Scenario 撰寫自動化測試
- [ ] 5.2 `openspec validate add-asset-management --strict`
