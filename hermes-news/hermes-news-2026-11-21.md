# Hermes-Agent 每日情報 — 2026-11-21

> 資料來源涵蓋截至 2026-11-21 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **過去 24 小時無重大更新，目前穩定版仍為 v0.23.3**：該版於 2026-11-20 00:34 UTC 發布（已於昨日情報報導），完整整理版說明仍延到 v0.24.0。（[v0.23.3](https://github.com/NousResearch/hermes-agent/releases/tag/v0.23.3)）

2. **新 canary 版 v0.23.3+canary.20261120T064609Z**：2026-11-20 07:49 UTC 更新，附 macOS（Apple Silicon）與 Windows（x64、ARM64）桌面安裝檔，安裝冒煙測試通過；新增多個 plugin 目錄項目，並列出 GPT-6.2 Astra／Sol 經 OpenRouter 與 Nous Portal 的支援。（[releases feed](https://github.com/NousResearch/hermes-agent/releases.atom)）

---

## 2. LLM 串接實戰回報

- **GPT**：canary 版持續列出 GPT-6.2 Astra／Sol 模型，屬 release 說明，尚未查到獨立的使用者實測回報。（[releases feed](https://github.com/NousResearch/hermes-agent/releases.atom)）
- **多模態／供應商限制**（使用者回報，尚待維護者確認）：[#158409](https://github.com/NousResearch/hermes-agent/issues/158409) 指出原生使用者圖片以完整尺寸送出且不會被預算淘汰，在約 10 MB 上限的供應商會出現 413；[#158434](https://github.com/NousResearch/hermes-agent/issues/158434) 提案新增保留 UI 細節瑕疵的高保真截圖模式（目前 2K 會被降到 720p 並以有損 JPEG 壓縮）。
- **Claude／Gemini／本地模型**：過去 24 小時未查到獨立實測回報，不予編造。

---

## 3. 版本與 release 動態

| 版本 | 發布時間（UTC） | 說明 |
|------|----------|------|
| **v0.23.3** | 2026-11-20 00:34 | 目前最新穩定版；約 370 個合併 PR；完整說明延到 v0.24.0 |
| v0.23.3+canary.20261120T064609Z | 2026-11-20 07:49 | canary；桌面安裝檔（macOS Apple Silicon、Windows x64／ARM64）、plugin 目錄擴充、GPT-6.2 Astra／Sol |

更新方式：CLI 執行 `hermes update`；Docker 拉取 `nousresearch/hermes-agent:stable`。

來源：[releases feed](https://github.com/NousResearch/hermes-agent/releases.atom)、[v0.23.3](https://github.com/NousResearch/hermes-agent/releases/tag/v0.23.3)。

---

## 4. 社群討論與已知問題

新增 issue（皆為使用者回報或提案，是否成立尚待維護者確認）：

| Issue | 說明 |
|------|------|
| [#158441](https://github.com/NousResearch/hermes-agent/issues/158441) | control socket 2 秒硬性逾時，在高併發下把「receipt not yet terminal」變成 kanban worker 崩潰 |
| [#158439](https://github.com/NousResearch/hermes-agent/issues/158439) | TUI 結束摘要期間按 Ctrl+C，會印出原始 KeyboardInterrupt traceback，而非 resume 提示 |
| [#158426](https://github.com/NousResearch/hermes-agent/issues/158426) | 提案：為寫入核准佇列提供公開 API（Desktop review plugin） |
| [#158416](https://github.com/NousResearch/hermes-agent/issues/158416) | 提案：gateway 回覆支援 `\|\|![alt](url)\|\|` 劇透圖片（Discord、Telegram） |
| [#158405](https://github.com/NousResearch/hermes-agent/issues/158405) | desktop:all 與 HUD toolchain 測試假設 x64 Linux 主機 |
| [#158404](https://github.com/NousResearch/hermes-agent/issues/158404) | ui-tui fast-echo 測試在 tmux 內因 TERM 未被 stub 而失敗 |
| [#158403](https://github.com/NousResearch/hermes-agent/issues/158403) | pm worker 測試在 macOS 27 失敗：測試伺服器 listen backlog 溢位 |

來源：[Issues](https://github.com/NousResearch/hermes-agent/issues?q=is%3Aissue+sort%3Acreated-desc)。
