# Hermes-Agent 每日情報 — 2026-10-31

> 資料來源涵蓋截至 2026-10-31 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **過去 24 小時無新的正式版發布，目前穩定版仍為 v0.22.5**：v0.22.5 於 2026-10-23 發布，之後僅有每日 canary 建置（最新為 10-30 的 v0.22.5+canary.20261030T064851Z，非正式版，release notes 只有時間戳）。（[Releases feed](https://github.com/NousResearch/hermes-agent/releases.atom)）

2. **新增 issue 以 gateway、session 與桌面版更新體驗為主**：Gateway 多工環境下記憶體 provider 的 OAuth 路由回傳 500（[#147035](https://github.com/NousResearch/hermes-agent/issues/147035)）；自訂路由重送 reasoning 時欄位重複，在嚴格的主機上造成 HTTP 400（[#147026](https://github.com/NousResearch/hermes-agent/issues/147026)）。以上為使用者回報，尚待維護者確認。

---

## 2. LLM 串接實戰回報

過去 24 小時未查到 Claude／GPT／Gemini／本地模型的獨立實測回報。

**Provider 相關 issue**
- **Claude／Anthropic**：`hermes usage` 在 Anthropic 用量百分比 ≤ 1 時誤乘 100 顯示（[#147006](https://github.com/NousResearch/hermes-agent/issues/147006)）。
- **自訂 provider**：reasoning replay 在自訂路由送出重複欄位，嚴格主機回 HTTP 400（[#147026](https://github.com/NousResearch/hermes-agent/issues/147026)）。
- **語音**：功能請求，讓 xAI TTS 每則語音回覆重用同一條 WebSocket（[#147002](https://github.com/NousResearch/hermes-agent/issues/147002)）。

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 說明 |
|------|----------|------|
| **v0.22.5** | 2026-10-23 | 目前最新穩定版；patch release，涵蓋約 81 個 PR（自 v0.22.4 起），範圍含 CLI 與安裝程式、Microsoft Store 應用、Docker、Hermes Cloud；完整整理過的 notes 預告隨 v0.23.0 提供（[release](https://github.com/NousResearch/hermes-agent/releases/tag/v0.22.5)） |

另有每日 canary 建置（最新 [v0.22.5+canary.20261030T064851Z](https://github.com/NousResearch/hermes-agent/releases/tag/v0.22.5%2Bcanary.20261030T064851Z)），僅有建置時間戳，非正式版。來源：[Releases feed](https://github.com/NousResearch/hermes-agent/releases.atom)。

---

## 4. 社群討論與已知問題

2026-10-30／31 新增 issue（部分）：

| Issue | 說明 |
|------|------|
| [#147051](https://github.com/NousResearch/hermes-agent/issues/147051) | copilot-acp 依目錄存在與否載入，無憑證時顯示 BrokenPipeError traceback（bug） |
| [#147041](https://github.com/NousResearch/hermes-agent/issues/147041) | 殘留的 steer 遺失「預期回覆」旗標，靜默標記會收到錯誤訊息（bug） |
| [#147039](https://github.com/NousResearch/hermes-agent/issues/147039) | 忙碌／steer 路徑略過寄件者前綴，同一寄件者被以不同方式標示（bug） |
| [#147037](https://github.com/NousResearch/hermes-agent/issues/147037) | 關閉串流時 interim_assistant_messages 設定無效（bug） |
| [#147025](https://github.com/NousResearch/hermes-agent/issues/147025) | platform_toolsets 為空清單時仍載入所有 MCP server（bug） |
| [#147014](https://github.com/NousResearch/hermes-agent/issues/147014)／[#147015](https://github.com/NousResearch/hermes-agent/issues/147015) | 更新橫幅殘留於 TUI 聊天畫面；更新後橫幅誤報另一更新進行中 |
| [#147008](https://github.com/NousResearch/hermes-agent/issues/147008) | session_search 無法限制單一 profile 讀取其他 profile 的 session |
| [#147004](https://github.com/NousResearch/hermes-agent/issues/147004) | adapter 當機後的「unknown」turn 被隔離，session 跨重開機卡死 |
| [#147049](https://github.com/NousResearch/hermes-agent/issues/147049)／[#147048](https://github.com/NousResearch/hermes-agent/issues/147048) | 功能請求：桌面版專案內巢狀聊天資料夾；以自然語言建立追蹤目標／子目標 |

以上為 issue 回報，是否成立與是否修補尚待維護者確認。來源：[Issues](https://github.com/NousResearch/hermes-agent/issues)。
