# Hermes-Agent 每日情報 — 2026-10-22

> 資料來源涵蓋截至 2026-10-22 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **v0.22.3 與 v0.22.4 於 2026-10-21 先後發布，目前最新穩定版為 v0.22.4**：v0.22.3 為彙整約 620 項已合併 PR 的大型修補版，完整整理後的說明預計隨 v0.23.0 發布；v0.22.4 則為小幅修補。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

2. **v0.22.3 清單中提到 MCP 進度通知、改版的經典 CLI 版面、外掛 OAuth 處理，以及 Nemotron 3.5 Bolt 本地執行選項**：release 頁面僅以摘要列出，細節待 v0.23.0 說明。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

3. **v0.22.2 的已知問題（0.22.1 gateway 建立的 session 無法刪除）在 v0.22.3、v0.22.4 的 release 頁面未再列為已知問題**，兩版均標示「無已知問題」；是否確實已修復未見獨立說明，請自行驗證。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

---

## 2. LLM 串接實戰回報

過去 24 小時未查到 Claude／GPT／Gemini 的獨立使用者實測回報。與模型設定相關的新 issue：`model.default` 若帶有 provider 斜線前綴，會被原樣轉送上游而觸發 fallback（[#142839](https://github.com/NousResearch/hermes-agent/issues/142839)）。

本地模型方面，v0.22.3 新增 Nemotron 3.5 Bolt 本地執行選項（見 [Releases](https://github.com/NousResearch/hermes-agent/releases)），尚無使用者實測回報。

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 說明 |
|------|----------|------|
| **v0.22.4** | 2026-10-21 | **目前最新穩定版**；桌面版不再於輸入框上方顯示建議膠囊；未宣告相依的外掛啟用時不再跳出空的同意提示；dashboard 登出後可安全重新配對 WhatsApp；桌面版核准模式標籤跟隨目前 profile；新增外掛目錄項目（bubblewrap、Singularity/Apptainer、mcp-serve、agent-importers、inbox、workflow-record），Telegram Business 移至 Hermes Labs 社群層級；無已知問題 |
| v0.22.3 | 2026-10-21 | 彙整約 620 項已合併 PR（2,911 個非合併 commit、4,206 個變更檔案）；完整說明待 v0.23.0；無已知問題 |
| v0.22.2 | 2026-10-15 | 修正 gateway 重開機後無法啟動、長時間本地模型回合在筆電上更穩定；已知問題：0.22.1 gateway 建立的 session 無法刪除 |
| v0.22.1 | 2026-10-14 | 由錯誤 commit 建置，已被 0.22.2 取代 |

來源：[Releases](https://github.com/NousResearch/hermes-agent/releases)。

---

## 4. 社群討論與已知問題

2026-10-22 新增 issue（部分），優先度取自 GitHub 標籤：

| Issue | 說明 | 優先度 |
|------|------|------|
| [#142843](https://github.com/NousResearch/hermes-agent/issues/142843) | Windows 的 verify-on-stop 只接受系統 Temp，不接受受管理的暫存區 | P2 |
| [#142841](https://github.com/NousResearch/hermes-agent/issues/142841) | 關閉經典 CLI 視窗後，gateway 內的回合仍持續執行 | P2 |
| [#142839](https://github.com/NousResearch/hermes-agent/issues/142839) | `model.default` 帶 provider 斜線前綴會被轉送上游，造成 fallback | P2 |
| [#142835](https://github.com/NousResearch/hermes-agent/issues/142835) | Dashboard「最近 session」項目看似可點擊卻無反應 | P2 |
| [#142822](https://github.com/NousResearch/hermes-agent/issues/142822) | 遇到 root 擁有的 systemd unit 時，gateway status／migrate 因 PermissionError 當機 | P2 |
| [#142806](https://github.com/NousResearch/hermes-agent/issues/142806) | 企業微信 AI 機器人的影片與語音訊息被當成空訊息靜默丟棄 | P2 |
| [#142820](https://github.com/NousResearch/hermes-agent/issues/142820) | 全域緊急停止啟用時，`cron run` 應拒絕執行 | P3 |

功能需求例：一般寫檔動作的核准閘門（[#142834](https://github.com/NousResearch/hermes-agent/issues/142834)，標籤含安全類）、透過 Desktop SDK 設定控制狀態列顯示（[#142817](https://github.com/NousResearch/hermes-agent/issues/142817)）。

以上為 issue 回報，是否成立與是否修補尚待維護者確認。來源：[Issues](https://github.com/NousResearch/hermes-agent/issues)。
