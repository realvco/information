# Hermes-Agent 每日情報 — 2026-11-07

> 資料來源涵蓋截至 2026-11-07 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **過去 24 小時無新版本發布，最新穩定版仍為 v0.23.0「The Hearth Release」**：該版於 2026-11-05 發布，之後未見新的 release。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

2. **今日新增 issue 集中在 managed scope（管理員設定）、gateway 與升級流程**：包含設定儲存會覆蓋 managed scope 的鍵值、gateway 在 macOS 與 systemd 下的啟動問題等，皆為使用者／貢獻者回報，尚待維護者確認。（[Issues](https://github.com/NousResearch/hermes-agent/issues)）

---

## 2. LLM 串接實戰回報

過去 24 小時未查到 Claude／GPT／Gemini／本地模型的獨立實測回報，不予編造。

與 Claude 相關的唯一項目為 issue 回報：managed worker 在初始化時若 fallback 落到 `anthropic_messages`，會因 `_anthropic_api_key` 屬性錯誤而當機（[#150445](https://github.com/NousResearch/hermes-agent/issues/150445)，使用者回報，未經確認）。

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 說明 |
|------|----------|------|
| **v0.23.0「The Hearth Release」** | 2026-11-05 | 目前最新穩定版：瀏覽器版 Desktop 工作區（預設關閉）、每台機器單一 gateway、終端機內嵌圖片與 `/rewind`、Desktop 多 gateway、付費 MCP 工具花費上限；**破壞性變更**：舊的內建瀏覽器工具移除，改用 `browser_exec`（[release](https://github.com/NousResearch/hermes-agent/releases/tag/v0.23.0)） |
| v0.22.7 | 2026-11-03 | 前一版，彙整約 174 個 PR（[Releases](https://github.com/NousResearch/hermes-agent/releases)） |

---

## 4. 社群討論與已知問題

2026-11-07 新增 issue（部分，皆為使用者回報，是否成立尚待維護者確認）：

| Issue | 說明 |
|------|------|
| [#150445](https://github.com/NousResearch/hermes-agent/issues/150445) | Managed worker 初始化 fallback 到 `anthropic_messages` 時當機 |
| [#150436](https://github.com/NousResearch/hermes-agent/issues/150436) | 任何設定儲存（如「一律允許」）會刪除使用者在 managed scope 也有設定的鍵值 |
| [#150431](https://github.com/NousResearch/hermes-agent/issues/150431) | 功能請求：managed scope 讓管理員鎖定 `mcp_servers` |
| [#150427](https://github.com/NousResearch/hermes-agent/issues/150427) | Gateway systemd unit 覆蓋 PATH，找不到以 mise 安裝的 CLI |
| [#150424](https://github.com/NousResearch/hermes-agent/issues/150424) | macOS 上環境變數含控制字元時 gateway 無法啟動 |
| [#150420](https://github.com/NousResearch/hermes-agent/issues/150420) | `delegate_task` 的 `output_schema`：首次失敗仍觸發 schema 重試，且重試時無歷史 |
| [#150414](https://github.com/NousResearch/hermes-agent/issues/150414) | 含 pyproject 的 plugin 無法在兩個 profile 同時啟用 |
| [#150403](https://github.com/NousResearch/hermes-agent/issues/150403) | v0.21.6 起的更新器匯入了未凍結的新目錄結構名稱 |
| [#150426](https://github.com/NousResearch/hermes-agent/issues/150426) | macOS 設定包裝程式缺少 `NSAudioCaptureUsageDescription`，導致系統音效被靜音 |

來源：[Issues](https://github.com/NousResearch/hermes-agent/issues)。
