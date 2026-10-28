# Hermes-Agent 每日情報 — 2026-10-28

> 資料來源涵蓋截至 2026-10-28 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **過去 24 小時無新的正式版發布，目前穩定版仍為 v0.22.5**：v0.22.5 於 2026-10-23 發布（約 81 項 PR 彙整，完整 release notes 延到 v0.23.0），之後只有每日 canary 建置（最新為 10-27 的 v0.22.5+canary.20261027T070237Z，非正式版）。（[Releases feed](https://github.com/NousResearch/hermes-agent/releases.atom)）

2. **新增 issue 以 session 管理、桌面版與 A2A 為主**：例如 `active_sessions.json` 被多餘的 TLS close_notify 記錄寫壞（[#145807](https://github.com/NousResearch/hermes-agent/issues/145807)）、A2A 任務超過回覆逾時就被標為失敗（[#145789](https://github.com/NousResearch/hermes-agent/issues/145789)）。以上為使用者回報，尚待維護者確認。

---

## 2. LLM 串接實戰回報

過去 24 小時未查到 Claude／GPT／Gemini／本地模型的獨立實測回報。

**Provider 相關 issue**
- Profile 範圍的 auth 優先順序會繼承全域順序（標籤 provider/openai，[#145786](https://github.com/NousResearch/hermes-agent/issues/145786)）。

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 說明 |
|------|----------|------|
| **v0.22.5** | 2026-10-23 | 目前最新穩定版；約 81 項 PR 彙整，完整策展 notes 延至 v0.23.0（[release](https://github.com/NousResearch/hermes-agent/releases/tag/v0.22.5)） |

另有每日 canary 建置（最新 [v0.22.5+canary.20261027T070237Z](https://github.com/NousResearch/hermes-agent/releases/tag/v0.22.5%2Bcanary.20261027T070237Z)），僅有建置時間戳，無 release notes，非正式版。來源：[Releases feed](https://github.com/NousResearch/hermes-agent/releases.atom)。

---

## 4. 社群討論與已知問題

2026-10-28 新增 issue（部分，優先度取自 GitHub 標籤）：

| Issue | 說明 | 優先度 |
|------|------|------|
| [#145807](https://github.com/NousResearch/hermes-agent/issues/145807) | active_sessions.json 被多餘的 TLS close_notify 記錄寫壞 | P2 |
| [#145808](https://github.com/NousResearch/hermes-agent/issues/145808) | 已斷線 WS 用戶端的 clarify 讓 session lease 占用 3600 秒 | P2 |
| [#145789](https://github.com/NousResearch/hermes-agent/issues/145789) | A2A：超過回覆逾時的任務被標為失敗 | P2 |
| [#145815](https://github.com/NousResearch/hermes-agent/issues/145815) | Windows `hermes update` 與分離的 MCP 子行程衝突 | P2 |
| [#145820](https://github.com/NousResearch/hermes-agent/issues/145820) | kanban：stale-claim 釋放把受速率限制而結束的 worker 視為失敗 | P2 |
| [#145791](https://github.com/NousResearch/hermes-agent/issues/145791) | cron 監控任務留下空的、未關閉的 session | P2 |
| [#145780](https://github.com/NousResearch/hermes-agent/issues/145780) | api_server 儲存失敗訊息而非部分回覆 | P2 |
| [#145797](https://github.com/NousResearch/hermes-agent/issues/145797) | 桌面版 markdown 表格把短標籤拆成單字母 | P2 |
| [#145790](https://github.com/NousResearch/hermes-agent/issues/145790) | 功能請求：A2A Agent Card 讓營運者宣告 skills 與 documentationUrl | P3 |

以上為 issue 回報，是否成立與是否修補尚待維護者確認。來源：[Issues](https://github.com/NousResearch/hermes-agent/issues)。
