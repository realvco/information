# Hermes-Agent 每日情報 — 2026-11-18

> 資料來源涵蓋截至 2026-11-18 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **目前最新穩定版仍為 v0.23.2**：2026-11-16 晚間（UTC）發布，過去 24 小時無新的穩定版。完整整理版說明將隨 v0.24.0 推出。（[Releases](https://github.com/NousResearch/hermes-agent/releases/tag/v0.23.2)）

2. **新增一個 canary 版本 v0.23.2+canary.20261117T064624Z**：2026-11-17 07:54 UTC 發布，附 macOS 與 Windows Desktop 下載，原生安裝 smoke test 通過；內容包含新的 opendockit plugin、Teams 驗證改為 plugin 形式，以及更新檢查的修正。（[Releases feed](https://github.com/NousResearch/hermes-agent/releases.atom)）

---

## 2. LLM 串接實戰回報

過去 24 小時未查到 Claude／GPT／Gemini／本地模型的獨立實測回報，不予編造。與模型串接較相關的僅有使用者提案 [#157176](https://github.com/NousResearch/hermes-agent/issues/157176)：ACP provider client 應將圖片轉送為 ACP image content block（提案，尚待維護者確認）。

---

## 3. 版本與 release 動態

| 版本 | 發布時間（UTC） | 說明 |
|------|----------|------|
| v0.23.2+canary.20261117T064624Z | 2026-11-17 | canary 版，附 Desktop 下載；新增 opendockit plugin、Teams 驗證移入 plugin、修正更新檢查 |
| **v0.23.2** | 2026-11-16 | 目前最新穩定版，彙整約 1,989 個 PR |
| rc.13-v0.23.2 | 2026-11-16 | 發布候選版（rc.11、rc.12 另有 abandoned 標記） |

來源：[releases feed](https://github.com/NousResearch/hermes-agent/releases.atom)。

---

## 4. 社群討論與已知問題

新增 issue（皆為使用者回報或提案，是否成立尚待維護者確認）：

| Issue | 說明 |
|------|------|
| [#157209](https://github.com/NousResearch/hermes-agent/issues/157209) | kanban transition 工具拒收以 JSON 字串傳入的 metadata，worker 的備援「測試呼叫」會寫入佔位用交接內容 |
| [#157169](https://github.com/NousResearch/hermes-agent/issues/157169) | gateway 重啟後 session FIFO 永久卡死 |
| [#157165](https://github.com/NousResearch/hermes-agent/issues/157165) | 「閒置時重啟」在 session FIFO 仍有排隊輸入時誤判為閒置 |
| [#157167](https://github.com/NousResearch/hermes-agent/issues/157167) | Cron 工作會遺失被 idle_timeout_seconds 停掉的 stdio MCP server 的工具 |
| [#157194](https://github.com/NousResearch/hermes-agent/issues/157194) | Teams adapter 丟失超連結目標，檔名以純文字傳給 agent |
| [#157180](https://github.com/NousResearch/hermes-agent/issues/157180) | Android/f2fs 上 `uv sync` 發生 EPERM 失敗 |
| [#157196](https://github.com/NousResearch/hermes-agent/issues/157196) | `hermes update` 未持有電源請求 |
| [#157164](https://github.com/NousResearch/hermes-agent/issues/157164) | TUI：gateway 已結束該回合時 Ctrl+C 無作用 |
| [#157199](https://github.com/NousResearch/hermes-agent/issues/157199) | 提案：為 MoA fan-out 新增 fail-closed 判定與輔助 fallback 的 plugin hook |

來源：[Issues](https://github.com/NousResearch/hermes-agent/issues?q=is%3Aissue+sort%3Acreated-desc)。
