# Hermes-Agent 每日情報 — 2026-11-06

> 資料來源涵蓋截至 2026-11-06 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **v0.23.0「The Hearth Release」發布，成為最新穩定版**：2026-11-05 21:32 UTC 發布，新增瀏覽器版 Desktop 工作區（由使用者自己的 gateway 提供，預設關閉），並由單一 gateway 統一管理 CLI、TUI、Desktop、編輯器、機器人與 cron 的所有 session。（[Releases feed](https://github.com/NousResearch/hermes-agent/releases.atom)、[release](https://github.com/NousResearch/hermes-agent/releases/tag/v0.23.0)）

2. **v0.23.0 同時整合 v0.22.1–v0.22.7 的修補內容**：另含終端機內嵌圖片、對話回溯（rewind）、Desktop 多 gateway 管理、Group Chat 改進、plugin 自動更新、付費工具標價與花費上限、可插拔的 subagent 委派，以及縮減的內建 skill 集合。（[release](https://github.com/NousResearch/hermes-agent/releases/tag/v0.23.0)）

3. **新版發布後，Desktop／webapp 相關 issue 湧現**：2026-11-06 新增的 issue 集中在 Desktop 與 `hermes webapp`，皆為使用者回報，尚待維護者確認。（[Issues](https://github.com/NousResearch/hermes-agent/issues)）

---

## 2. LLM 串接實戰回報

過去 24 小時未查到 Claude／GPT／Gemini／本地模型的獨立實測回報，不予編造。

唯一相關者為 issue 回報：DeepSeek V4.x 的圖片 sentinel token 出現在文字訊息中，會造成持續性的 HTTP 400 錯誤（[#149807](https://github.com/NousResearch/hermes-agent/issues/149807)，使用者回報，未經確認）。

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 說明 |
|------|----------|------|
| **v0.23.0「The Hearth Release」** | 2026-11-05 | 目前最新穩定版，內容如上（[release](https://github.com/NousResearch/hermes-agent/releases/tag/v0.23.0)） |
| v0.22.7 | 2026-11-03 | 前一穩定版，已併入 v0.23.0 |

來源：[Releases feed](https://github.com/NousResearch/hermes-agent/releases.atom)。

---

## 4. 社群討論與已知問題

2026-11-06 新增 issue（部分，皆為使用者回報，是否成立尚待維護者確認）：

| Issue | 說明 |
|------|------|
| [#149810](https://github.com/NousResearch/hermes-agent/issues/149810) | `hermes webapp`：當 `~/.hermes` 為 symlink 時，誤報 gateway 未執行 |
| [#149798](https://github.com/NousResearch/hermes-agent/issues/149798) | Desktop TUI/PTY 面板因 loopback WebSocket 驗證被拒而持續斷線（1006） |
| [#149808](https://github.com/NousResearch/hermes-agent/issues/149808) | Windows Desktop 下載本機 MEDIA 檔案失敗（WinError 123） |
| [#149809](https://github.com/NousResearch/hermes-agent/issues/149809) | Desktop 未讀浮水印在 `last_read_at` 為 NULL 時不會自動啟動 |
| [#149804](https://github.com/NousResearch/hermes-agent/issues/149804) | Dashboard Webhooks 頁面忽略 `WEBHOOK_ENABLED` 與 `config.yaml` 路由 |
| [#149799](https://github.com/NousResearch/hermes-agent/issues/149799) | iOS 經 WireGuard 使用 SSH 時，dashboard 解析失敗 |
| [#149794](https://github.com/NousResearch/hermes-agent/issues/149794) | Hosted rooms：排程丟失發言順序，導致部分席位未被聽到 |
| [#149793](https://github.com/NousResearch/hermes-agent/issues/149793)／[#149792](https://github.com/NousResearch/hermes-agent/issues/149792) | 無障礙：Desktop 模型選擇器搜尋框缺少角色，NVDA 無聲；Settings 開啟後焦點未移入 |

來源：[Issues](https://github.com/NousResearch/hermes-agent/issues)。
