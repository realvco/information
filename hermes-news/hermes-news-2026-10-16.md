# Hermes-Agent 每日情報 — 2026-10-16

> 資料來源涵蓋截至 2026-10-16 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **v0.22.2 發布，成為目前最新穩定版**：GitHub Releases 標示日期為 2026-10-15，為修正版，取代 v0.22.1。修正受監管的 gateway（systemd、launchd、Docker）重開機後因 PID 被回收而拒絕啟動的問題，並改善筆電上本地模型的回合穩定度。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

2. **v0.22.1 被官方說明為「從錯誤的 commit 建置」**：v0.22.2 的說明指出 v0.22.1 夾帶了尚未釋出的 gateway 工作，Microsoft Store 版 app 可能卡在「Connecting」。已安裝 v0.22.1 的使用者建議升級。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

3. **已知問題**：v0.22.2 說明提到，在 0.22.1 gateway 下建立的 session，於 0.22.2 無法刪除。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

4. **商業面消息（媒體報導）**：WSJ 於 2026-10-07 報導 Nous Research 以約 15 億美元估值募得 9,000 萬美元，資金將用於 Hermes for Businesses；估值數字出自媒體報導，Nous 未正式公開。（[TNW](https://thenextweb.com/news/nous-research-90m-1-5bn-hermes-agent-wsj)）此消息非過去 24 小時內新事件，僅供背景參考。

---

## 2. LLM 串接實戰回報

- Claude／GPT／Gemini：過去 24 小時未查到獨立的使用者實測回報。
- 本地模型：v0.22.2 針對筆電上的本地模型回合改善進度顯示、休眠感知的停滯計時器與壓縮行為。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）
- 供應商相關新 issue：
  - 建議更容易支援 OpenRouter 的 `:nitro`、`:floor` 後綴（[#139557](https://github.com/NousResearch/hermes-agent/issues/139557)，P3）。
  - MoA 參考模型即使系統提示明確禁止，仍會捏造 tool-call／tool-result 區塊（[#139568](https://github.com/NousResearch/hermes-agent/issues/139568)）。

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 說明 |
|------|----------|------|
| **v0.22.2** | 2026-10-15 | **目前最新穩定版**；修正 gateway 重開機後因 PID 回收無法啟動、改善筆電本地模型回合可靠度；已知問題：0.22.1 gateway 建立的 session 無法刪除 |
| v0.22.1 | 2026-10-14 | 修補版，但據 v0.22.2 說明係由錯誤 commit 建置，Microsoft Store 版可能卡在「Connecting」 |
| v0.22.0 | 2026-10-13 | 「The Threshold Release」；首次啟動引導、Connectors（Linear、Notion 等）、`hermes pm`、約 600 個審核外掛、簽章桌面套件、Claude 5.5 與 GPT-6 系列支援 |
| v0.21.7 | 2026-10-11 | 原始碼安裝預設追蹤已標記 release；`hermes chat --resume` 後推理內容延續 |

來源：[Releases](https://github.com/NousResearch/hermes-agent/releases)。

---

## 4. 社群討論與已知問題

以下為 2026-10-16 新增的部分 issue，優先度標籤取自 GitHub issue 頁面。

| Issue | 說明 | 優先度 |
|------|------|------|
| [#139567](https://github.com/NousResearch/hermes-agent/issues/139567) | arm64 Windows 11 多項錯誤 | P2 |
| [#139564](https://github.com/NousResearch/hermes-agent/issues/139564) | 桌面版連到遠端 bot 時，會把僅限本機的群組聊天同步到遠端 gateway（標為重複） | P2 |
| [#139556](https://github.com/NousResearch/hermes-agent/issues/139556) | pm2 下 Dashboard Chat PTY 立即結束（繼承的 `NODE_CHANNEL_FD` 使 node 子行程中止） | P2 |
| [#139550](https://github.com/NousResearch/hermes-agent/issues/139550) | 桌面版「Repair install」前三次僅重啟後端，無 venv 完整性檢查 | P2 |
| [#139542](https://github.com/NousResearch/hermes-agent/issues/139542) | Linux 桌面版 renderer 當機迴圈（setuid-sandbox 無法建立 /dev/shm，需重現） | P2 |
| [#139540](https://github.com/NousResearch/hermes-agent/issues/139540) | 桌面版安裝／更新寫入錯誤的磁碟區路徑大小寫，造成大小寫不敏感 APFS 上「dependency environment is missing」 | P2 |
| [#139538](https://github.com/NousResearch/hermes-agent/issues/139538) | 壓縮流程在格式錯誤的完成回覆之前接受了 meta-summary（需重現） | P2 |
| [#139536](https://github.com/NousResearch/hermes-agent/issues/139536) | TUI 在 canonical gateway 上無法關閉作用中 session | P2 |
| [#139549](https://github.com/NousResearch/hermes-agent/issues/139549) | `doctor` 沒有 gateway 存活檢查（gateway 已死仍全綠） | P3 |
| [#139552](https://github.com/NousResearch/hermes-agent/issues/139552) | 缺少 profile alias 列表指令 | P3 |

來源：[Issues](https://github.com/NousResearch/hermes-agent/issues)。
