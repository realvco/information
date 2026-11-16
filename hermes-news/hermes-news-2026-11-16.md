# Hermes-Agent 每日情報 — 2026-11-16

> 資料來源涵蓋截至 2026-11-16 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **過去 24 小時無新的正式版本，最新穩定版仍為 v0.23.1**：該版於 2026-11-10 發布，彙整約 618 個已合併 PR，並將 plugin 目錄大小上限由 2 MiB 提高到 16 MiB，修正安裝時退回舊目錄資料的問題。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

2. **新增 canary 預覽建置 v0.23.1+canary.20261115T064406Z**：標示為 pre-release，plugin 目錄新增 dosbox-mcp、Hermes-eye-memory 等項目，macOS 與 Windows 桌面安裝檔冒煙測試通過。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

3. **新增 issue 集中在 doctor／CLI 細節、A2A、Kanban 與 cron**：皆為使用者回報或提案，尚待維護者確認。（[Issues](https://github.com/NousResearch/hermes-agent/issues?q=is%3Aissue+sort%3Acreated-desc)）

---

## 2. LLM 串接實戰回報

過去 24 小時未查到 Claude／GPT／Gemini／本地模型的獨立實測回報，不予編造。僅有一則相關 issue 屬設定問題：[#156334](https://github.com/NousResearch/hermes-agent/issues/156334) 回報舊式 `custom_providers` 設定在輔助任務（vision、標題、壓縮）遺失 `key_cmd`，導致送出無金鑰請求而回 401（使用者回報，未證實）。

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 說明 |
|------|----------|------|
| v0.23.1+canary.20261115T064406Z | 2026-11-15 | 預覽（pre-release）：plugin 目錄新增 dosbox-mcp、Hermes-eye-memory；桌面安裝檔通過冒煙測試（[Releases](https://github.com/NousResearch/hermes-agent/releases)） |
| v0.23.1+canary.20261114T064334Z | 2026-11-14 | 前一個 canary 建置（[Releases](https://github.com/NousResearch/hermes-agent/releases)） |
| v0.23.1+canary.20261113T064447Z | 2026-11-13 | 更早的 canary 建置（[Releases](https://github.com/NousResearch/hermes-agent/releases)） |
| **v0.23.1** | 2026-11-10 | 目前最新穩定版：plugin 目錄上限提高到 16 MiB（[Releases](https://github.com/NousResearch/hermes-agent/releases)） |
| v0.23.0「The Hearth Release」 | 2026-11-05 | 瀏覽器 Webapp、單一 gateway 掌管 session、`/rewind` 對話回溯、可插拔 delegation（[Releases](https://github.com/NousResearch/hermes-agent/releases)） |
| v0.22.7 | 2026-11-03 | 修補版，約 174 個 PR（[Releases](https://github.com/NousResearch/hermes-agent/releases)） |

---

## 4. 社群討論與已知問題

新增 issue（皆為使用者回報或提案，是否成立尚待維護者確認）：

| Issue | 說明 |
|------|------|
| [#156347](https://github.com/NousResearch/hermes-agent/issues/156347) | doctor 連線檢查的進度字元在重新導向輸出中以 `\r` 覆寫 |
| [#156345](https://github.com/NousResearch/hermes-agent/issues/156345) | a2a_orchestrate 遺失已設定 peer 的 tenant |
| [#156344](https://github.com/NousResearch/hermes-agent/issues/156344) | teams_pipeline：逐字稿內容 404 時直接失敗，未退回錄影＋STT |
| [#156343](https://github.com/NousResearch/hermes-agent/issues/156343) | computer_use：同回合去重會丟掉第二次相同按鍵（如連按 Tab） |
| [#156342](https://github.com/NousResearch/hermes-agent/issues/156342) | terminal：spawn 後完成訊號遺失，重試迴圈會重複執行指令 |
| [#156339](https://github.com/NousResearch/hermes-agent/issues/156339) | Gateway 在多個 profile home 下閒置時佔用約 0.5–1.2 核心 |
| [#156335](https://github.com/NousResearch/hermes-agent/issues/156335) | Kanban：移除看板不應讓進行中的寫入端變成孤兒 |
| [#156324](https://github.com/NousResearch/hermes-agent/issues/156324) | 瀏覽器版 Desktop 中 iOS 上滑誤觸底部選單 |
| [#156323](https://github.com/NousResearch/hermes-agent/issues/156323) | cron：gateway 排空時，同步觸發被誤記為任務失敗 |

來源：[Issues](https://github.com/NousResearch/hermes-agent/issues?q=is%3Aissue+sort%3Acreated-desc)。
