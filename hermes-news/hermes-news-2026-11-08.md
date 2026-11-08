# Hermes-Agent 每日情報 — 2026-11-08

> 資料來源涵蓋截至 2026-11-08 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **過去 24 小時無新版本發布，最新穩定版仍為 v0.23.0「The Hearth Release」**：該版於 2026-11-05 發布，之後未見新的 release。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

2. **今日新增 issue 集中在 `hermes cron` 指令、Windows 平台與 plugin 機制**：包含 cron 建立指令的驗證與錯誤訊息問題、Windows 上 bash 偵測與更新流程問題、plugin hub 效能問題等，皆為使用者回報，尚待維護者確認。（[Issues](https://github.com/NousResearch/hermes-agent/issues?q=is%3Aissue+sort%3Acreated-desc)）

---

## 2. LLM 串接實戰回報

過去 24 小時未查到 Claude／GPT／本地模型的獨立實測回報，不予編造。

**Gemini**：有使用者回報，當 API key 被撤銷或無效時，Gemini 回傳 HTTP 400 `API key not valid`，Hermes 不會輪替到下一組憑證（[#150942](https://github.com/NousResearch/hermes-agent/issues/150942)，使用者回報，未經確認）。

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 說明 |
|------|----------|------|
| **v0.23.0「The Hearth Release」** | 2026-11-05 | 目前最新穩定版：由自己的 gateway 提供完整 Desktop 工作區的 Webapp（預設關閉）、單一 gateway 掌管所有 session、終端機內嵌圖片與 `/rewind`、付費 MCP 工具價格標示與花費上限、plugin 自動更新；**升級注意**：舊的內建瀏覽器工具移除，改用 `browser_exec`（[release](https://github.com/NousResearch/hermes-agent/releases/tag/v0.23.0)） |
| v0.22.7 | 2026-11-03 | 修補版，彙整約 174 個 PR（[Releases](https://github.com/NousResearch/hermes-agent/releases)） |
| v0.22.6 | 2026-11-02 | 修補版，彙整約 776 個 PR（[Releases](https://github.com/NousResearch/hermes-agent/releases)） |

---

## 4. 社群討論與已知問題

2026-11-08 新增 issue（部分，皆為使用者回報，是否成立尚待維護者確認）：

| Issue | 說明 |
|------|------|
| [#151017](https://github.com/NousResearch/hermes-agent/issues/151017) | Windows：bash 偵測找不到安裝在自訂磁碟的 Git for Windows |
| [#151012](https://github.com/NousResearch/hermes-agent/issues/151012) | `hermes cron create --skill <name>` 不驗證技能是否存在 |
| [#150986](https://github.com/NousResearch/hermes-agent/issues/150986) | `hermes cron create/edit --repeat forever` 因 argparse 整數錯誤而失敗 |
| [#150973](https://github.com/NousResearch/hermes-agent/issues/150973) | `hermes cron create` 在工作其實已儲存時，仍顯示誤導性的「Failed to create job」 |
| [#150981](https://github.com/NousResearch/hermes-agent/issues/150981) | `--safe-mode` / `--ignore-user-config` 的 session 一律使用 `local` terminal |
| [#150969](https://github.com/NousResearch/hermes-agent/issues/150969) | Plugin hub 為不相關的安裝重新解析整份目錄（列表 93 秒、RPC 逾時 30 秒） |
| [#150962](https://github.com/NousResearch/hermes-agent/issues/150962) | Windows：更新時每次清理過期鎖都會啟動 `tasklist.exe`，跳出 0xc0000142 錯誤視窗 |
| [#150951](https://github.com/NousResearch/hermes-agent/issues/150951) | 安裝腳本把 Fedora（glibc）主機誤判為 linux-x64-musl |
| [#150939](https://github.com/NousResearch/hermes-agent/issues/150939) | 通訊平台上 `/retry` 會重送被合併進上一輪的鏡像使用者訊息 |
| [#150975](https://github.com/NousResearch/hermes-agent/issues/150975) | 功能請求：Plugin API 開放技能 frontmatter 解析器與 skills_guard 掃描（唯讀） |

來源：[Issues](https://github.com/NousResearch/hermes-agent/issues?q=is%3Aissue+sort%3Acreated-desc)。
