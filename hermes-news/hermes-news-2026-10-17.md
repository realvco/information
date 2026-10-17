# Hermes-Agent 每日情報 — 2026-10-17

> 資料來源涵蓋截至 2026-10-17 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **過去 24 小時無新版本發布，最新穩定版仍為 v0.22.2**：GitHub Releases 標示日期為 2026-10-15，修正受監管 gateway 重開機後因 PID 回收無法啟動的問題。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

2. **已知問題未變**：在 0.22.1 gateway 下建立的 session，於 v0.22.2 無法刪除；v0.22.1 係由錯誤 commit 建置，建議已安裝者升級。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

3. **今日新增 issue 出現兩個 P1**：Windows dashboard 記憶體暴增至約 22 GB（[#140246](https://github.com/NousResearch/hermes-agent/issues/140246)），以及非預設 gateway profile 取得零個工具（[#140238](https://github.com/NousResearch/hermes-agent/issues/140238)）。尚未確認官方回應或修正。

---

## 2. LLM 串接實戰回報

- Claude／GPT：過去 24 小時未查到獨立的使用者實測回報。
- Gemini：未查到。
- 其他模型的推理強度設定問題（皆為新開 issue，尚未確認處理狀態）：
  - GLM-5.3 拒絕 `medium` 推理強度而非自動調整（[#140239](https://github.com/NousResearch/hermes-agent/issues/140239)，P3）。
  - Muse Spark 1.3 支援 `max`，但 Hermes 將其限縮為 `xhigh`（[#140229](https://github.com/NousResearch/hermes-agent/issues/140229)，P3）。
  - `hermes config set agent.reasoning_effort` 被判定為不認得的設定鍵（[#140221](https://github.com/NousResearch/hermes-agent/issues/140221)，P2）。
  - 建議在 subagent plugin API 開放每次啟動的推理強度（[#140251](https://github.com/NousResearch/hermes-agent/issues/140251)）。

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 說明 |
|------|----------|------|
| **v0.22.2** | 2026-10-15 | **目前最新穩定版**；修正 gateway 重開機後無法啟動、延長筆電本地模型回合（休眠時間不計入停滯計時器）；已知問題：0.22.1 gateway 建立的 session 無法刪除 |
| v0.22.1 | 2026-10-14 | 修補版，但由錯誤 commit 建置，Microsoft Store 版可能卡在「Connecting」 |
| v0.22.0 | 2026-10-13 | 「The Threshold Release」；首次啟動引導、Connectors（Linear、Notion 等）、600 項外掛目錄、簽章桌面套件 |
| v0.21.7 | 2026-10-11 | 原始碼安裝預設追蹤已標記 release；`hermes chat --resume` 後推理內容延續 |

來源：[Releases](https://github.com/NousResearch/hermes-agent/releases)。

---

## 4. 社群討論與已知問題

2026-10-17 新增 issue（部分），優先度取自 GitHub 標籤：

| Issue | 說明 | 優先度 |
|------|------|------|
| [#140246](https://github.com/NousResearch/hermes-agent/issues/140246) | Windows dashboard 大量 asyncio assertion，記憶體成長至約 22 GB | P1 |
| [#140238](https://github.com/NousResearch/hermes-agent/issues/140238) | 非預設 gateway profile 取得零個工具 | P1 |
| [#140226](https://github.com/NousResearch/hermes-agent/issues/140226) | 近乎零產出的壓縮流程每 1–4 分鐘循環一次 | P2 |
| [#140224](https://github.com/NousResearch/hermes-agent/issues/140224) | 桌面版語音在 HTTP 403 後卡在「Preparing audio」 | P2 |
| [#140222](https://github.com/NousResearch/hermes-agent/issues/140222) | 含 monitor 的 cron agent 工作每次觸發都建立空 session | P2 |
| [#140209](https://github.com/NousResearch/hermes-agent/issues/140209) | cron 成功送達卻被記錄為「Interrupted by shutdown」 | P2 |
| [#140204](https://github.com/NousResearch/hermes-agent/issues/140204) | Windows 上遠端專案路徑被改寫（`/home/...` 變成 `C:/home/...`） | P2 |
| [#140217](https://github.com/NousResearch/hermes-agent/issues/140217) | 桌面版 SSH 首次開啟 Bot 約需 25–30 秒 | P2 |

來源：[Issues](https://github.com/NousResearch/hermes-agent/issues)。
