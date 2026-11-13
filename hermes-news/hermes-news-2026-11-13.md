# Hermes-Agent 每日情報 — 2026-11-13

> 資料來源涵蓋截至 2026-11-13 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **過去 24 小時無新版本發布，最新穩定版仍為 v0.23.1**：該版於 2026-11-10 發布，彙整約 618 個已合併 PR，並將 plugin 目錄大小上限由 2 MiB 提高到 16 MiB。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

2. **新增 issue 以 Desktop、gateway 與記憶（memory）相關回報為主**：皆為使用者回報或功能提案，尚待維護者確認。（[Issues](https://github.com/NousResearch/hermes-agent/issues?q=is%3Aissue+sort%3Acreated-desc)）

---

## 2. LLM 串接實戰回報

過去 24 小時未查到 Claude／GPT／Gemini／本地模型的獨立實測回報，不予編造。

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 說明 |
|------|----------|------|
| **v0.23.1** | 2026-11-10 | 目前最新穩定版：plugin 目錄大小上限提高到 16 MiB，修正安裝時退回內建舊目錄的問題（[Releases](https://github.com/NousResearch/hermes-agent/releases)） |
| v0.23.0「The Hearth Release」 | 2026-11-05 | Webapp（預設關閉）、單一 gateway 掌管 session、`/rewind`、付費 MCP 工具價格與花費上限；內建瀏覽器工具改以 `browser_exec` 取代（[release](https://github.com/NousResearch/hermes-agent/releases/tag/v0.23.0)） |
| v0.22.7 | 2026-11-03 | 修補版，約 174 個 PR（[Releases](https://github.com/NousResearch/hermes-agent/releases)） |

---

## 4. 社群討論與已知問題

新增 issue（部分，皆為使用者回報或提案，是否成立尚待維護者確認）：

| Issue | 說明 |
|------|------|
| [#154787](https://github.com/NousResearch/hermes-agent/issues/154787) | Desktop 連到遠端 `hermes serve`，session 被拒後的 follow-up 失敗 |
| [#154786](https://github.com/NousResearch/hermes-agent/issues/154786) | Gateway session 未解析預設模型的別名 |
| [#154784](https://github.com/NousResearch/hermes-agent/issues/154784) | 重複出現的記憶 patch 不一致，阻擋記憶整併 |
| [#154782](https://github.com/NousResearch/hermes-agent/issues/154782) | 慢速機器上 web UI 建置被中止，導致 install.sh 無法完成 |
| [#154758](https://github.com/NousResearch/hermes-agent/issues/154758) | Open WebUI 對話因結尾多出空的 assistant 訊息而回傳 HTTP 400 |
| [#154768](https://github.com/NousResearch/hermes-agent/issues/154768) | Weixin adapter 在 120 欄位強制換行，破壞程式碼與文字 |
| [#154750](https://github.com/NousResearch/hermes-agent/issues/154750) | 背景 review 的工具白名單未套用於 worker thread，可能執行 terminal 工具 |
| [#154751](https://github.com/NousResearch/hermes-agent/issues/154751) | 功能提案：讓 agent 的 shell 指令在獨立 systemd scope 執行 |

來源：[Issues](https://github.com/NousResearch/hermes-agent/issues?q=is%3Aissue+sort%3Acreated-desc)。
