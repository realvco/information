# Hermes-Agent 每日情報 — 2026-10-30

> 資料來源涵蓋截至 2026-10-30 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **過去 24 小時無新的正式版發布，目前穩定版仍為 v0.22.5**：v0.22.5 於 2026-10-23 發布，之後僅有每日 canary 建置（最新為 10-29 的 v0.22.5+canary.20261029T064840Z，非正式版，release notes 只有時間戳）。（[Releases feed](https://github.com/NousResearch/hermes-agent/releases.atom)）

2. **新增 issue 以 Anthropic 認證／技能索引、session 與 gateway 為主**：Claude 方案 OAuth 登入會把 hermes-agent 技能在索引中改名為 claude-code（[#146711](https://github.com/NousResearch/hermes-agent/issues/146711)）；具名 profile 共用 root 的 Anthropic OAuth 資料列時，可能同時刷新而撤銷授權（[#146683](https://github.com/NousResearch/hermes-agent/issues/146683)）。以上為使用者回報，尚待維護者確認。

---

## 2. LLM 串接實戰回報

過去 24 小時未查到 Claude／GPT／Gemini／本地模型的獨立實測回報。

**Provider 相關 issue**
- **Claude／Anthropic**：
  - Claude 方案 OAuth 登入使 hermes-agent 技能在索引中被改名為 claude-code（[#146711](https://github.com/NousResearch/hermes-agent/issues/146711)，P2）。
  - 具名 profile 與 root 共用 Anthropic OAuth 資料列，並行刷新可能撤銷授權（[#146683](https://github.com/NousResearch/hermes-agent/issues/146683)，P2）。
  - Kanban worker 丟失 provider transport／api_mode，導致自訂 Anthropic-wire provider 回傳 404（[#146698](https://github.com/NousResearch/hermes-agent/issues/146698)，P3）。

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 說明 |
|------|----------|------|
| **v0.22.5** | 2026-10-23 | 目前最新穩定版（[release](https://github.com/NousResearch/hermes-agent/releases/tag/v0.22.5)） |

另有每日 canary 建置（最新 [v0.22.5+canary.20261029T064840Z](https://github.com/NousResearch/hermes-agent/releases/tag/v0.22.5%2Bcanary.20261029T064840Z)），僅有建置時間戳，非正式版。來源：[Releases feed](https://github.com/NousResearch/hermes-agent/releases.atom)。

---

## 4. 社群討論與已知問題

2026-10-29／30 新增 issue（部分，優先度取自 GitHub 標籤）：

| Issue | 說明 | 優先度 |
|------|------|------|
| [#146696](https://github.com/NousResearch/hermes-agent/issues/146696) | 非同步 delegation 結果在 session 無 origin 時無法送達（Telegram） | P2 |
| [#146691](https://github.com/NousResearch/hermes-agent/issues/146691) | Bot Chat 投遞因已關閉的桌面版資料列佔用標題而永久 not_found | P2 |
| [#146686](https://github.com/NousResearch/hermes-agent/issues/146686) | Windows：殘留的 hermes.exe.deleteme 使 store-pip 啟動器失效 | P2 |
| [#146682](https://github.com/NousResearch/hermes-agent/issues/146682) | 桌面版顯示「resume failed」並卡在「backend checking」 | P2 |
| [#146678](https://github.com/NousResearch/hermes-agent/issues/146678) | Linux：「clear」透明度忽略平台支援，視窗啟動即全透明且無切換選項 | P2 |
| [#146716](https://github.com/NousResearch/hermes-agent/issues/146716) | Telegram 關閉時 admission claim 未釋放，洩漏已排隊取消的 handler coroutine | P3 |
| [#146712](https://github.com/NousResearch/hermes-agent/issues/146712) | 分支（fork）session 沿用父 session 的時間戳 | P3 |
| [#146709](https://github.com/NousResearch/hermes-agent/issues/146709) | 功能請求：uv 預設使用系統憑證 | P3 |
| [#146699](https://github.com/NousResearch/hermes-agent/issues/146699)／[#146700](https://github.com/NousResearch/hermes-agent/issues/146700) | 功能請求：讓 agent 查看 cron 執行紀錄、並可自行暫停或結束 cron 任務 | P3 |

以上為 issue 回報，是否成立與是否修補尚待維護者確認。來源：[Issues](https://github.com/NousResearch/hermes-agent/issues)。
