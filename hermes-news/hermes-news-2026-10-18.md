# Hermes-Agent 每日情報 — 2026-10-18

> 資料來源涵蓋截至 2026-10-18 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **過去 24 小時無重大更新，最新穩定版仍為 v0.22.2**：GitHub Releases 標示日期為 2026-10-15，修正 v0.22.1 誤用錯誤 commit 建置的問題，以及重開機後 gateway 因 PID 回收而無法啟動的問題。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

2. **已知問題未變**：在 0.22.1 gateway 下建立的 session，於 v0.22.2 無法刪除。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

3. **新增 issue 顯示 v0.22.1 起的訊息寫入疑慮**：有回報指出 WeChat／Telegram gateway 訊息自 v0.22.1 起未寫入 SQLite（[#140823](https://github.com/NousResearch/hermes-agent/issues/140823)）；尚未確認官方回應。

---

## 2. LLM 串接實戰回報

- Claude／GPT／Gemini：過去 24 小時未查到獨立的使用者實測回報。v0.22.0 的 release notes 提到新增對 Claude 5.5 與 GPT-6 系列的支援（[Releases](https://github.com/NousResearch/hermes-agent/releases)）。
- 本地模型：有人要求可自訂 llama-server 執行檔路徑（[#140818](https://github.com/NousResearch/hermes-agent/issues/140818)）；v0.22.0 已加入以 llama.cpp 一鍵安裝本地模型的功能。
- 憑證相關：已撤銷的 Codex OAuth 憑證會讓模型選單中的模型消失（[#140838](https://github.com/NousResearch/hermes-agent/issues/140838)，bug）；另有人要求憑證池支援額外的環境變數來源（[#140821](https://github.com/NousResearch/hermes-agent/issues/140821)）。

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 說明 |
|------|----------|------|
| **v0.22.2** | 2026-10-15 | **目前最新穩定版**；修正 gateway 重開機後無法啟動、提升筆電上長時間本地模型回合的穩定度；已知問題：0.22.1 gateway 建立的 session 無法刪除 |
| v0.22.1 | 2026-10-14 | 修補版，但由錯誤 commit 建置，Microsoft Store 版可能卡在「Connecting」 |
| v0.22.0 | 2026-10-13 | 「The Threshold Release」；首次啟動引導、Connectors（Linear、Notion 等）、約 600 項外掛目錄、簽章桌面套件、`hermes pm` |

來源：[Releases](https://github.com/NousResearch/hermes-agent/releases)。

---

## 4. 社群討論與已知問題

2026-10-17 至 10-18 新增 issue（部分），優先度取自 GitHub 標籤（無標籤者不標示）：

| Issue | 說明 | 優先度 |
|------|------|------|
| [#140845](https://github.com/NousResearch/hermes-agent/issues/140845) | 編輯已送出的 Telegram 訊息導致 RuntimeStoreError（admission_conflict） | — |
| [#140823](https://github.com/NousResearch/hermes-agent/issues/140823) | WeChat／Telegram gateway 訊息自 v0.22.1 起未寫入 SQLite | — |
| [#140831](https://github.com/NousResearch/hermes-agent/issues/140831) | Windows 上設定檔路徑含空格時更新失敗 | — |
| [#140805](https://github.com/NousResearch/hermes-agent/issues/140805) | 桌面版群組聊天在 app 重啟後分岔出新的 gateway room | P2 |
| [#140803](https://github.com/NousResearch/hermes-agent/issues/140803) | 訊息平台的 /kanban、/curator 遇到引號參數會當掉 | P2 |
| [#140798](https://github.com/NousResearch/hermes-agent/issues/140798) | 封存進行中的 Bot Chat，重新開啟後又復活 | P2 |
| [#140791](https://github.com/NousResearch/hermes-agent/issues/140791) | 桌面版 Profiles 畫面對所有非使用中 profile 回傳 403 | P2 |
| [#140794](https://github.com/NousResearch/hermes-agent/issues/140794) | 需求：以 Lightpanda 搭配 CDP session 綁定的安全保險庫填寫 | P3 |

來源：[Issues](https://github.com/NousResearch/hermes-agent/issues)。
