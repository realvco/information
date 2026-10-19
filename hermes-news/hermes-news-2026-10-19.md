# Hermes-Agent 每日情報 — 2026-10-19

> 資料來源涵蓋截至 2026-10-19 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **過去 24 小時無重大更新，最新穩定版仍為 v0.22.2**：GitHub Releases 標示日期為 2026-10-15，修正 v0.22.1 誤用錯誤 commit 建置的問題，以及重開機後 gateway 因 PID 殘留而無法啟動的問題。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

2. **已知問題未變**：在 0.22.1 gateway 下執行過的 session，於 v0.22.2 無法刪除。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

3. **新增一則安全類回報值得留意**：有使用者指出「智慧核准」模式自動核准了 osascript 指令，導致 PowerPoint 未存檔的內容被刪除（[#141346](https://github.com/NousResearch/hermes-agent/issues/141346)，標籤 type/security、P3）；尚未確認官方回應。

---

## 2. LLM 串接實戰回報

- Claude／GPT／Gemini：過去 24 小時未查到獨立的使用者實測回報。v0.22.0 的 release notes 提到新增 Claude 5.5 與 GPT-6 系列的模型支援（[Releases](https://github.com/NousResearch/hermes-agent/releases)）。
- 本地模型：v0.22.2 改善長時間本地模型回合在筆電上的穩定度，狀態列會顯示進度，且逾時計時器會略過休眠時間（[Releases](https://github.com/NousResearch/hermes-agent/releases)）。

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 說明 |
|------|----------|------|
| **v0.22.2** | 2026-10-15 | **目前最新穩定版**；修正 gateway 重開機後無法啟動、提升本地模型長回合穩定度；已知問題：0.22.1 gateway 建立的 session 無法刪除 |
| v0.22.1 | 2026-10-14 | 由錯誤 commit 建置，Microsoft Store 版可能卡在「Connecting」，已被 0.22.2 取代 |
| v0.22.0 | 2026-10-13 | 「The Threshold Release」；穩定 SemVer 發行通道、簽章桌面套件、`hermes pm`、llama.cpp 一鍵本地模型、Connectors、約 600 項外掛目錄 |

來源：[Releases](https://github.com/NousResearch/hermes-agent/releases)。

---

## 4. 社群討論與已知問題

2026-10-18 至 10-19 新增 issue（部分），優先度取自 GitHub 標籤：

| Issue | 說明 | 優先度 |
|------|------|------|
| [#141346](https://github.com/NousResearch/hermes-agent/issues/141346) | 智慧核准自動核准 osascript 指令，造成未存檔的 PowerPoint 內容遭刪除（安全類） | P3 |
| [#141329](https://github.com/NousResearch/hermes-agent/issues/141329) | 桌面版長時間使用後畫面停止繪製，renderer CPU／記憶體持續成長 | P2 |
| [#141323](https://github.com/NousResearch/hermes-agent/issues/141323) | Team Gateway 的 Slack scope 申請遇到 502 connector 錯誤 | P2 |
| [#141320](https://github.com/NousResearch/hermes-agent/issues/141320) | Linux 語音：打斷 TTS 時會播出大聲雜音，barge-in 門檻無法設定 | P2 |
| [#141313](https://github.com/NousResearch/hermes-agent/issues/141313) | CLI 語音模式：外部程序 provider 的回覆不會被朗讀 | P2 |
| [#141339](https://github.com/NousResearch/hermes-agent/issues/141339) | `hermes plugins validate` 拒絕舊版 `hooks:` manifest 鍵 | P2 |
| [#141305](https://github.com/NousResearch/hermes-agent/issues/141305) | Linux 解除安裝後，dashboard systemd unit 仍持續重試已移除的執行檔 | P2 |
| [#141328](https://github.com/NousResearch/hermes-agent/issues/141328) | 非操作員 dashboard 使用者的權限強化（安全類） | P3 |

來源：[Issues](https://github.com/NousResearch/hermes-agent/issues)。
