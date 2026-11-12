# Hermes-Agent 每日情報 — 2026-11-12

> 資料來源涵蓋截至 2026-11-12 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **過去 24 小時無新版本發布，最新穩定版仍為 v0.23.1**：該版於 2026-11-10 發布，彙整約 618 個已合併 PR，並將 plugin 目錄大小上限由 2 MiB 提高到 16 MiB，修正安裝時退回舊版內建目錄、漏掉新 plugin 的問題；v0.23.0 以前的安裝需更新才會脫離該 fallback。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

2. **新增 issue 以 gateway／Telegram 與 hook 擴充需求為主**：皆為使用者回報或功能提案，尚待維護者確認。（[Issues](https://github.com/NousResearch/hermes-agent/issues?q=is%3Aissue+sort%3Acreated-desc)）

---

## 2. LLM 串接實戰回報

過去 24 小時未查到 Claude／GPT／Gemini／本地模型的獨立實測回報，不予編造。

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 說明 |
|------|----------|------|
| **v0.23.1** | 2026-11-10 | 目前最新穩定版：plugin 目錄大小上限提高到 16 MiB（[Releases](https://github.com/NousResearch/hermes-agent/releases)） |
| v0.23.0「The Hearth Release」 | 2026-11-05 | Webapp（預設關閉）、單一 gateway 掌管 session、`/rewind`、付費 MCP 工具的價格標示與花費上限；plugin 目錄由約 600 增至逾 1,050 筆；內建 skills 由 58 減為 44，瀏覽器工具改以 `browser_exec` 取代（[release](https://github.com/NousResearch/hermes-agent/releases/tag/v0.23.0)） |
| v0.22.7 | 2026-11-03 | 修補版，約 174 個 PR（[Releases](https://github.com/NousResearch/hermes-agent/releases)） |

---

## 4. 社群討論與已知問題

新增 issue（部分，皆為使用者回報或提案，是否成立尚待維護者確認）：

| Issue | 說明 |
|------|------|
| [#154195](https://github.com/NousResearch/hermes-agent/issues/154195) | Telegram adapter 不支援 clarify 工具的 multi_select，點第一個按鈕就結束 |
| [#154180](https://github.com/NousResearch/hermes-agent/issues/154180) | Telegram 群組 replay 把被點名的對話輪次歸入被動觀察 |
| [#154192](https://github.com/NousResearch/hermes-agent/issues/154192) | 從 Windows 搬到 macOS／Linux 的 session，Desktop 控制皆因 `C:\` cwd 被拒而顯示 "storage unavailable" |
| [#154162](https://github.com/NousResearch/hermes-agent/issues/154162) | 單一寫入者的聊天認領沒有到期機制，聊天會一直被標為「在其他裝置開啟」 |
| [#154134](https://github.com/NousResearch/hermes-agent/issues/154134) | `browser.cdp_url`：已附加的 CDP session 不會關閉自己建立的頁面，分頁／renderer 無限增長 |
| [#154150](https://github.com/NousResearch/hermes-agent/issues/154150) | `hermes usage` 把取得失敗（401、429、網路）與缺少憑證混為一談，且不留紀錄 |
| [#154187](https://github.com/NousResearch/hermes-agent/issues/154187) | CLI 啟動警告在非 TTY 的 stderr 寫入 ANSI 控制碼 |
| [#154142](https://github.com/NousResearch/hermes-agent/issues/154142)／[#154143](https://github.com/NousResearch/hermes-agent/issues/154143) | 功能提案：`pre_platform_send`、`transform_cron_outcome` 兩個 plugin hook |

來源：[Issues](https://github.com/NousResearch/hermes-agent/issues?q=is%3Aissue+sort%3Acreated-desc)。
