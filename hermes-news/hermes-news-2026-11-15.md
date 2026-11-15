# Hermes-Agent 每日情報 — 2026-11-15

> 資料來源涵蓋截至 2026-11-15 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **過去 24 小時無新的正式版本，最新穩定版仍為 v0.23.1**：該版約於 2026-11-10 發布，彙整約 618 個已合併 PR，並將 plugin 目錄大小上限提高到 16 MiB。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

2. **出現新一輪 canary 預覽建置 v0.23.1+canary.20261114T064334Z**：標示為 pre-release，附 macOS（Apple Silicon）與 Windows（x64、ARM64）桌面安裝檔，頁面註明原生安裝與聊天冒煙測試通過，Linux 套件目前停用。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

3. **新增 issue 集中在 cron、Desktop 與訊息平台行為**：包含無法編輯訊息的平台之首段提早送出提案、cron 任務與建議卡片的關聯、各頻道獨立工作目錄等，皆為使用者回報或提案，尚待維護者確認。（[Issues](https://github.com/NousResearch/hermes-agent/issues?q=is%3Aissue+sort%3Acreated-desc)）

---

## 2. LLM 串接實戰回報

過去 24 小時未查到 Claude／GPT／Gemini／本地模型的獨立實測回報，不予編造。

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 說明 |
|------|----------|------|
| v0.23.1+canary.20261114T064334Z | 2026-11-14 | 預覽（pre-release）自動建置，附桌面安裝檔（[Releases](https://github.com/NousResearch/hermes-agent/releases)） |
| v0.23.1+canary.20261113T064447Z | 2026-11-13 | 前一個 canary 建置（[Releases](https://github.com/NousResearch/hermes-agent/releases)） |
| **v0.23.1** | 約 2026-11-10 | 目前最新穩定版：plugin 目錄大小上限提高到 16 MiB，修正安裝時靜默退回內建舊目錄的問題（[Releases](https://github.com/NousResearch/hermes-agent/releases)） |
| v0.23.0「The Hearth Release」 | 2026-11-05 | Webapp、單一 gateway 掌管 session、對話 rewind、付費 MCP 工具價格與花費上限；舊內建瀏覽器工具改以 `browser_exec` 取代（[release](https://github.com/NousResearch/hermes-agent/releases/tag/v0.23.0)） |
| v0.22.7 | 2026-11-03 | 修補版，約 174 個 PR（[Releases](https://github.com/NousResearch/hermes-agent/releases)） |

---

## 4. 社群討論與已知問題

新增 issue（部分，皆為使用者回報或提案，是否成立尚待維護者確認）：

| Issue | 說明 |
|------|------|
| [#155941](https://github.com/NousResearch/hermes-agent/issues/155941) | 功能提案：無法編輯訊息的平台支援首段內容提早送出 |
| [#155940](https://github.com/NousResearch/hermes-agent/issues/155940) | 每次帳本異動後都掃描所有已掛載 session 的效能疑慮 |
| [#155938](https://github.com/NousResearch/hermes-agent/issues/155938) | 提案：記錄是哪個建議建立了 cron 任務，並允許更新待處理建議 |
| [#155937](https://github.com/NousResearch/hermes-agent/issues/155937) | 提案：cron 任務建立的卡片自動訂閱該任務的投遞目標 |
| [#155932](https://github.com/NousResearch/hermes-agent/issues/155932) | 回報：對已讓出（yielded）的前景指令進行中途 steer，會遺失替換回覆 |
| [#155930](https://github.com/NousResearch/hermes-agent/issues/155930) | 回報：`desktop_project` 即使 session 工作區未移動也回報成功 |
| [#155928](https://github.com/NousResearch/hermes-agent/issues/155928) | 功能提案：各頻道可覆寫工作目錄，讓訊息 session 在專案內工作 |
| [#155926](https://github.com/NousResearch/hermes-agent/issues/155926) | 回報：顯示名稱過長導致收到的檔案訊息因 ENAMETOOLONG 被丟棄 |
| [#155923](https://github.com/NousResearch/hermes-agent/issues/155923) | 功能提案：可信任整個目錄樹的專案 skill（一次同意涵蓋其下 repo） |

來源：[Issues](https://github.com/NousResearch/hermes-agent/issues?q=is%3Aissue+sort%3Acreated-desc)。
