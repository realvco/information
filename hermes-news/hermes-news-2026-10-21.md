# Hermes-Agent 每日情報 — 2026-10-21

> 資料來源涵蓋截至 2026-10-21 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **過去 24 小時無重大更新，最新穩定版仍為 v0.22.2**：GitHub Releases 標示日期為 2026-10-15，修正受監管環境（systemd、launchd、Docker）重開機後 gateway 無法啟動的問題。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

2. **已知問題未變**：在 0.22.1 gateway 下執行過的 session，於 v0.22.2 無法刪除。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

3. **今日新增 issue 以安全與依賴固定版本為主**：Pillow 固定版本含已於後續版本修補的 OpenJPEG heap overflow（[#142324](https://github.com/NousResearch/hermes-agent/issues/142324)）、certifi 固定版本仍信任遭入侵的 eMudhra 中繼憑證（[#142307](https://github.com/NousResearch/hermes-agent/issues/142307)），皆為 P3、安全類，僅為 issue 回報，尚未見修補。

---

## 2. LLM 串接實戰回報

過去 24 小時未查到 Claude／GPT／Gemini／本地模型的獨立使用者實測回報。與模型呼叫相關的新 issue：API server 無法在 fallback 前收回失敗模型呼叫已串流的文字（[#142333](https://github.com/NousResearch/hermes-agent/issues/142333)）。

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 說明 |
|------|----------|------|
| **v0.22.2** | 2026-10-15 | **目前最新穩定版**；修正 gateway 重開機後無法啟動、讓長時間本地模型回合在筆電上更穩定；已知問題：0.22.1 gateway 建立的 session 無法刪除 |
| v0.22.1 | 2026-10-14 | 由錯誤 commit 建置，Microsoft Store 版可能卡在「Connecting」，已被 0.22.2 取代 |
| v0.22.0 | 2026-10-13 | 「The Threshold Release」；穩定 SemVer 通道、簽章桌面套件、llama.cpp 一鍵本地模型、約 600 項外掛目錄、Claude 5.5 與 GPT-6 支援 |
| v0.21.7 | 2026-10-11 | 約 204 項 PR 的彙整修補版；原始碼安裝預設更新至已標記版本 |

來源：[Releases](https://github.com/NousResearch/hermes-agent/releases)。

---

## 4. 社群討論與已知問題

2026-10-21 新增 issue（部分），優先度取自 GitHub 標籤：

| Issue | 說明 | 優先度 |
|------|------|------|
| [#142316](https://github.com/NousResearch/hermes-agent/issues/142316) | 外掛目錄有四個項目的 tier 被客戶端拒絕 | P2 |
| [#142314](https://github.com/NousResearch/hermes-agent/issues/142314) | macOS 上若 /Applications 內裝有其他 CuaDriver 應用，`computer_use` 會失敗 | P2 |
| [#142313](https://github.com/NousResearch/hermes-agent/issues/142313) | 共用 session 清單篩選漏掉隱藏的 session，導致其出現在行動裝置端 | P2 |
| [#142311](https://github.com/NousResearch/hermes-agent/issues/142311) | 跨主機 session lease 永不過期，被拒絕的機器無法釋放 | P2 |
| [#142301](https://github.com/NousResearch/hermes-agent/issues/142301) | `reclaim_task` 釋放仍在運作的 worker 的 claim，造成重複啟動 worker | P2 |
| [#142298](https://github.com/NousResearch/hermes-agent/issues/142298) | Windows 本機 gateway 宣告桌面專用工具，但未接上 desktop_ui emitter | P2 |
| [#142302](https://github.com/NousResearch/hermes-agent/issues/142302) | 桌面版影片附件「更多選項」選單非全螢幕時被裁切 | P2 |
| [#142330](https://github.com/NousResearch/hermes-agent/issues/142330) | `begin_runtime_epoch()` 缺少 profile 擁有者檢查（安全類） | P3 |

功能需求例：跨所有 profile 執行 `hermes` 指令（[#142331](https://github.com/NousResearch/hermes-agent/issues/142331)）、桌面版 Weixin QR 碼登入（[#142323](https://github.com/NousResearch/hermes-agent/issues/142323)）。

來源：[Issues](https://github.com/NousResearch/hermes-agent/issues)。
