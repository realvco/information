# Hermes-Agent 每日情報 — 2026-07-05

> 涵蓋時段：2026-07-04 ～ 2026-07-05（UTC）

---

## 1. 今日重點摘要

1. **v0.18.0「Judgment Release」後續 bug 潮湧現（2026-07-04 起）**：7 月 1 日剛發布的大版本在 4 天內已累積多個新 issue，社群密切追蹤回歸問題，其中至少兩個涉及費用異常。
2. **Envelope-layout 快取 bug 導致費用暴增（issue #57845/57893）**：在 OpenRouter + Claude 的工具呼叫密集 session 中，快取標記點計算錯誤，工具訊息與空 assistant 標記被略過，導致約 15 萬 token 在 7 次連續呼叫中以全價重複計費，估計約 2 倍成本。目前已有社群成員提交連結關閉參考。
3. **Gemini Vertex provider 初始化失敗（issue #56906）**：v0.18.0 新增的 Vertex 整合在執行 `hermes doctor` 時無法識別 `vertex` 為有效 provider，影響剛設定 Gemini Vertex 的用戶診斷工作流程。
4. **Homebrew / Linuxbrew 安裝 TUI 無法啟動（issue #57031）**：透過 homebrew 或 linuxbrew 安裝的 v0.18.0 無法正常啟動終端 UI，macOS 與 Linux 桌面使用者受影響，尚無官方 hotfix。
5. **網頁抓取速度 60 倍提升、成本降 49 倍（官方公告）**：Nous Research 官方 X 帳號宣布網頁閱讀後端重構，clean content 直接傳送給代理，大型頁面本地分頁讀取，大幅減少重複處理步驟。
6. **NVIDIA × Stripe × NousResearch Hackathon 啟動**：三方聯合舉辦「Hermes Agent Accelerated Business Hackathon」，Stripe Skills 讓代理可自主購買服務、開通 SaaS 訂閱、支付使用費用，聚焦能實際產生交易的代理應用。
7. **記憶系統 holographic memory 自動抽取 bug（issue #57682）**：即使用戶設定關閉 `auto_extract`，holographic memory 仍會將 context 壓縮摘要意外收入 `fact_store`，導致事實庫被污染。
8. **上下文超限崩潰（issue #57275）**：多種模型大小下均出現 Hermes 超過最大 context 限制後停止回應的情況，需手動開新對話，已確認為 v0.18.0 引入的回歸問題。
9. **Telegram 語音訊息格式錯誤（issue #57049）**：啟用 `auto_tts` 後，Telegram 語音回覆預設輸出 `.mp3` 檔案而非原生語音氣泡，用戶體驗退化。

---

## 2. LLM 串接實戰回報

### OpenRouter + Claude — 快取標記計算錯誤導致費用加倍
- **問題**：Envelope-layout 快取斷點在工具訊息迴圈中靜默失效，工具訊息與空 assistant 標記被忽略，造成約 15 萬 token 在 7 次連續呼叫中以全價重複計費。
- **估計影響**：單一密集 session 約 2 倍 input token 成本，使用 OpenRouter + Claude 組合的用戶風險最高。
- **狀態**：issue #57845 已有連結關閉參考（#57893），修補進度待確認。
- 來源：[GitHub issue #57845/57893](https://github.com/NousResearch/hermes-agent/issues)

### Gemini Vertex 整合初始化失敗
- **問題**：v0.18.0 新增 Gemini Vertex 作為 provider，但 `hermes doctor` 診斷指令目前無法識別 `vertex` 關鍵字，導致剛設定 Vertex 後執行診斷時回傳錯誤，無法驗證設定是否正確。
- **建議暫時做法**：手動測試實際呼叫確認 Vertex 是否正常運作，不依賴 `hermes doctor` 結果。
- 來源：[GitHub issue #56906](https://github.com/NousResearch/hermes-agent/issues/56906)

### Provider 感知上下文限制差異
- **文件揭露**：相同模型透過不同 provider 提供時，上下文視窗可能有顯著差異。例如 `claude-opus-4.6` 在 Anthropic 直接呼叫為 1M token，但透過 GitHub Copilot 僅限 128K token。
- **實戰建議**：在工作流設計時明確指定 provider 來源，避免因 provider routing 造成非預期的上下文截斷。
- 來源：[Hermes Agent Practitioner's Reference](https://blakecrosley.com/guides/hermes)

### 上下文壓縮器的 provider 設定
- Hermes 的自動上下文壓縮（context compaction）使用獨立的 LLM 呼叫，可指向任意 provider 或 endpoint，建議設定為低成本模型以控制壓縮成本（如本地 Ollama 或 OpenRouter 上的 Mistral）。
- 來源：[AI Providers 文件](https://hermes-agent.nousresearch.com/docs/integrations/providers)

---

## 3. 社群反應與討論亮點

- **v0.18.0「Judgment Release」發布反應熱烈**：998 個合併 PR、949 個關閉 issue、370+ 貢獻者，社群稱這是「把技術債還清的里程碑」，但後續 bug 潮顯示大版本快速合併帶來的回歸風險。
- **Nous Research X 帳號（@NousResearch）**持續以技術細節深度公告著稱，網頁抓取速度公告獲大量轉發，量化指標（60x 快、49x 便宜）引發廣泛討論。
- **Hackathon 社群動員**：NVIDIA × Stripe 聯名活動吸引大量「agent 創業者」關注，Stripe Skills 讓代理真正能「自主花錢」的概念在 r/LocalLLaMA 與官方 Discord agent 頻道引發激烈辯論（代理自主財務是否有安全風險）。
- **Hermes Desktop 使用者回饋**：6 月公開預覽以來，Electron 桌面版的 git worktree 管理、fuzzy-searchable model picker 與 `/undo` 指令獲得工程師族群好評；Homebrew 安裝 TUI 失敗問題則令部分 macOS 用戶感到受挫。
- **r/LocalLLaMA 討論主軸**：Hermes 3/4 模型本地運行效能與 Claude/GPT 工具呼叫可靠性比較持續為熱門話題；function-calling 格式差異（Hermes 專用格式 vs 通用訓練模型）是主要討論焦點。
- 來源：[Hermes Agent Reddit 社群討論匯整](https://openclawlaunch.com/blog/hermes-agent-reddit-discussion)

---

## 4. 值得追蹤的後續議題

| 議題 | 說明 |
|------|------|
| **issue #57845 快取費用 bug** | 影響 OpenRouter + Claude 重度使用者，修補版本時程未定，建議暫時監控每次呼叫的 token 計費 |
| **Gemini Vertex provider 穩定性** | 新增功能尚未完全成熟，`hermes doctor` 問題外是否有其他初始化邊界案例待觀察 |
| **Homebrew TUI 失敗（issue #57031）** | 影響以 brew 安裝的 macOS/Linux 用戶，若官方短期未修復，可考慮改用直接二進位安裝 |
| **holographic memory 污染（issue #57682）** | `auto_extract` 設定無效問題，對依賴長期記憶準確性的工作流影響較大 |
| **Hackathon 成果展示** | Stripe Skills 與自主財務代理的實際落地案例，是否引發更多安全或政策討論值得追蹤 |
| **v0.18.1 hotfix 時程** | 多個 P1 級 bug 集中在 7 月初，預期 hotfix 版本將在短期內釋出 |
| **網頁抓取後端細節** | 60x 速度提升的技術細節尚未公開，社群正在測試實際效果與可能的副作用 |

---

*資料來源：[NousResearch/hermes-agent GitHub](https://github.com/NousResearch/hermes-agent) ／ [v0.18.0 Judgment Release](https://github.com/NousResearch/hermes-agent/releases/tag/v2026.7.1) ／ [Hermes Agent 官方文件](https://hermes-agent.nousresearch.com/docs/integrations/providers) ／ [Nous Research X.com](https://x.com/NousResearch) ／ [Hermes Changelog](https://hermes-ai.net/changelog/) ／ [Practitioner's Reference](https://blakecrosley.com/guides/hermes)*
