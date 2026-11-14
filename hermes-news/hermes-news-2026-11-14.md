# Hermes-Agent 每日情報 — 2026-11-14

> 資料來源涵蓋截至 2026-11-14 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **過去 24 小時無新的正式版本，最新穩定版仍為 v0.23.1**：該版於 2026-11-10 發布，彙整約 618 個已合併 PR，並將 plugin 目錄大小上限由 2 MiB 提高到 16 MiB。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

2. **出現 canary 預覽建置 v0.23.1+canary.20261113T064447Z**：標示為 pre-release，附 macOS（Apple Silicon）與 Windows（x64、ARM）桌面安裝檔，頁面註明原生安裝與聊天冒煙測試通過。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

3. **新增 issue 集中在 plugin 目錄／驗證、webapp 與 session 管理**：皆為使用者回報或提案，尚待維護者確認。（[Issues](https://github.com/NousResearch/hermes-agent/issues?q=is%3Aissue+sort%3Acreated-desc)）

---

## 2. LLM 串接實戰回報

過去 24 小時未查到 Claude／GPT／Gemini／本地模型的獨立實測回報，不予編造。

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 說明 |
|------|----------|------|
| v0.23.1+canary.20261113T064447Z | 2026-11-13 | 預覽（pre-release）自動建置，附桌面安裝檔（[Releases](https://github.com/NousResearch/hermes-agent/releases)） |
| **v0.23.1** | 2026-11-10 | 目前最新穩定版：plugin 目錄大小上限提高到 16 MiB，修正安裝時退回內建舊目錄的問題；部分功能留待 v0.24.0 說明（[Releases](https://github.com/NousResearch/hermes-agent/releases)） |
| v0.23.0「The Hearth Release」 | 2026-11-05 | Webapp（預設關閉）、單一 gateway 掌管 session、`/rewind`、付費 MCP 工具價格與花費上限；舊瀏覽器工具改以 `browser_exec` 取代（[release](https://github.com/NousResearch/hermes-agent/releases/tag/v0.23.0)） |
| v0.22.7 | 2026-11-03 | 修補版，約 174 個 PR（[Releases](https://github.com/NousResearch/hermes-agent/releases)） |

---

## 4. 社群討論與已知問題

新增 issue（部分，皆為使用者回報或提案，是否成立尚待維護者確認）：

| Issue | 說明 |
|------|------|
| [#155416](https://github.com/NousResearch/hermes-agent/issues/155416) | disk-cleanup plugin 的並行 hook 因缺乏鎖定而互相衝突 |
| [#155410](https://github.com/NousResearch/hermes-agent/issues/155410) | Webapp 朗讀功能把系統中斷通知當成助理所說的話唸出 |
| [#155409](https://github.com/NousResearch/hermes-agent/issues/155409) | Android webapp/PWA 無法貼上剪貼簿圖片 |
| [#155393](https://github.com/NousResearch/hermes-agent/issues/155393) | scratch 清理僅看修改時間，會終止仍在執行的唯讀程序 |
| [#155384](https://github.com/NousResearch/hermes-agent/issues/155384) | 功能提案：Desktop 模型下拉選單縮短或別名化過長的本地模型路徑 |
| [#155371](https://github.com/NousResearch/hermes-agent/issues/155371) | 執行中硬重啟後，hosted room 的准入狀態卡在未知，阻擋該成員後續回合 |
| [#155367](https://github.com/NousResearch/hermes-agent/issues/155367) | plugin 目錄驗證器對未知頂層欄位只警告不拒絕 |
| [#155366](https://github.com/NousResearch/hermes-agent/issues/155366) | 核准傳輸失敗時遺失缺少設定的原因，導致所有被標記指令遭封鎖 |

來源：[Issues](https://github.com/NousResearch/hermes-agent/issues?q=is%3Aissue+sort%3Acreated-desc)。
