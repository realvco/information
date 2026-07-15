# Hermes-Agent 每日情報 — 2026-07-15

## 1. 今日重點摘要

1. **v0.18.2 為目前最新穩定版**：繼 7 月 1 日的 v0.18.0「The Judgment Release」後，7 月 7 日同日釋出 v0.18.1（約 660 個 PR 的修復與強化）與 v0.18.2（修復 WhatsApp Baileys 套件依賴問題），目前 v0.18.2 為推薦安裝版本。

2. **Mixture-of-Agents（MoA）成為一等公民模型**：v0.18.0 起，MoA 不再只是可切換的模式，而是作為獨立 `moa` provider 出現在所有模型選擇器（CLI、TUI、Desktop、Gateway）中，可與 Claude、GPT、Grok 並列選擇；各參考模型的完整輸出獨立顯示，最終結果支援即時串流。

3. **所有 P0/P1 backlog 清空**：The Judgment Release 的核心里程碑是清空整個 repo 的 P0 與 P1 問題，共關閉約 700 個最高優先級 issue/PR，是 Hermes-Agent 歷史上最大規模的品質衝刺。

4. **新增 `/learn` 指令**：`/learn <任何內容>` 可將指向的目錄、URL 或操作流程萃取為可重複使用的技能（skill），讓 agent 能持續累積知識。

5. **新增 `/journey` 時間線**：`/journey` 以時間軸呈現 agent 累積的所有記憶與技能，可直接在介面中編輯或刪除；Desktop 版本搭配可互動的放射狀記憶圖。

6. **背景 subagent 並行執行**：`delegate_task` 現在支援同時啟動多個背景 subagent，主對話不被阻塞；CLI 與 TUI 狀態列即時追蹤 agent 艦隊進度，所有結果統一在完成後合併回傳。

7. **Desktop Projects 上線**：Desktop app 新增以 profile 為單位的 Projects 功能，包含程式碼庫側欄、Coding Rail、Review Pane、git worktree 管理，以及 agent 可感知的 project 工具。

8. **Gateway 支援 scale-to-zero**：針對團隊或托管服務部署，Gateway 現在可在閒置時縮減至零，並在重啟、遷移或自動更新前協調外部 drain，確保不中斷進行中的對話。

9. **社群與 X 上的反應正面**：Nous Research 創辦人 Teknium 在 X（[@Teknium](https://x.com/Teknium/status/2072416088785359189)）親自宣布發布，強調 MoA、/learn、/journey 三大亮點；[@NousResearch](https://x.com/NousResearch/status/2072413332665962617) 官方帳號同步推文並附 Changelog 連結，社群討論熱度高。

---

## 2. LLM 串接實戰回報

### GitHub Copilot API（推薦方式）
- Hermes-Agent 官方建議透過 GitHub Copilot 訂閱存取 GPT-5.x、Claude、Gemini 等模型：GPT-5.x 使用 Responses API，其他模型使用 Chat Completions。
- 優點：單一訂閱覆蓋多模型，不需個別管理 API key。
- **來源**：[AI Providers — Hermes Agent Docs](https://hermes-agent.nousresearch.com/docs/integrations/providers)

### Nous Portal（統一訂閱閘道）
- Nous Research 自家的 Nous Portal 是另一推薦入口，覆蓋 300+ 前沿 agentic 模型（Claude、GPT、Gemini、DeepSeek、Qwen、Kimi、GLM、MiniMax、Grok 等），並整合 Tool Gateway（網路搜尋、圖片生成、TTS、瀏覽器自動化）。
- 社群評價適合不想分別管理多個 provider key 的使用者。
- **來源**：[Hermes Agent: The Practitioner's Reference (2026) — blakecrosley.com](https://blakecrosley.com/guides/hermes)

### LiteLLM Proxy
- LiteLLM 作為 OpenAI 相容代理，支援 100+ LLM provider，適合需要跨 provider 切換、負載平衡或 fallback chain 的進階場景。
- 使用者反映在 provider 切換時無需更改 Hermes-Agent 本身設定，靈活性高。
- **來源**：[Hermes Agent Integration — llmgateway.io](https://docs.llmgateway.io/guides/hermes-agent)

### Context 限制踩坑
- Hermes-Agent 啟動時強制要求模型至少提供 **64,000 tokens** context window；context 不足的模型直接在啟動階段被拒絕，無法進入工作流程。
- 社群討論中有使用者誤用較小的本地模型（context < 32K）觸發此限制，需特別注意。
- **來源**：[FAQ & Troubleshooting — Hermes Agent Docs](https://hermes-agent.nousresearch.com/docs/reference/faq)

### 模型選用社群共識
- r/LocalLLaMA 與 Nous Discord 中的反覆辯論結論趨向一致：**Hermes 自家模型用於工具呼叫效果最佳，但需要深度推理時換用 Claude 或 GPT**。
- MoA 功能上線後，部分使用者開始嘗試「Hermes 模型負責 tool-use + Claude/GPT 負責最終彙整」的混搭組合。
- **來源**：[Hermes Agent Reddit & Community Discussion — openclawlaunch.com](https://openclawlaunch.com/blog/hermes-agent-reddit-discussion)

### WhatsApp 整合修復（v0.18.2）
- v0.18.1 的 WhatsApp bridge 依賴 Baileys 從 git commit 固定版本改為使用已發布的 `7.0.0-rc13` npm 版本，v0.18.2 同日修復確保 Docker 映像建置可靠。
- 之前使用 Docker 部署並啟用 WhatsApp 的使用者建議盡快升級至 v0.18.2。
- **來源**：[Release Hermes Agent v0.18.2 — GitHub](https://github.com/NousResearch/hermes-agent/releases/tag/v2026.7.7.2)

---

## 3. 社群反應與討論亮點

- **Teknium 親自宣傳 MoA 功能**：在 [X 貼文](https://x.com/Teknium/status/2072416088785359189)中，Teknium 特別點出 Mixture-of-Agents 讓使用者「超越任何單一模型的前沿」，引發社群對 MoA 配置最佳化的大量討論。

- **零 P0/P1 里程碑獲廣泛認可**：社群普遍認為這是 Nous Research 對工程品質承諾的具體展現，對比同期 OpenClaw v2026.7.1 的穩定性問題，Hermes-Agent 的品質衝刺引發不少跨社群討論。

- **`/learn` 指令討論熱**：使用者分享各種 `/learn` 使用情境，包括讓 agent 學習私有 API 文件、內部 Runbook，以及從操作示範中萃取工作流程技能。

- **Discord 整合設定常見問題**：[Hermes Agent Discord](https://hermes-agent.ai/integrations/discord) 文件中有使用者持續反映「bot 看不到訊息」的問題，社群標準解法是在 Developer Portal 啟用 Message Content Intent 並設定頻道白名單。

- **Desktop Projects 獲開發者好評**：git worktree 管理與 Coding Rail 被認為是 Hermes-Agent 在「AI 輔助開發」場景的重大進展，有望吸引原本在 OpenClaw 上做程式開發工作流的使用者。

- **Phot​on（iMessage）與 Raft 網路**：v0.17.0 引入的兩個新頻道繼續被社群測試，Raft agent 網路尤其受到分散式 agent 研究者關注。

---

## 4. 值得追蹤的後續議題

1. **v0.19.0 的完整 Changelog**：官方表示 v0.18.1 的正式說明將隨 v0.19.0 一起發布，下一個主版本的功能方向值得持續關注。參考：[GitHub Releases](https://github.com/NousResearch/hermes-agent/releases)

2. **MoA 在生產環境的成本與延遲測試**：MoA 成為一等公民後，多模型並行推論的成本與響應時間對實際部署的影響尚待系統性評估。

3. **`/learn` 技能的跨 session 持久化機制**：社群正在探索 `/learn` 萃取的技能如何管理版本、在多裝置或多使用者場景下共享，以及如何安全地刪除或更新。

4. **scale-to-zero Gateway 在雲端托管的實際效果**：此功能對 AWS Lambda、Fly.io、Railway 等平台的冷啟動延遲影響，是社群接下來的重點測試方向。

5. **Hermes 模型 + Claude MoA 混搭效能**：「Hermes 負責 tool-use、Claude 負責彙整」的 MoA 組合是否在真實工作負載中勝過單一模型，需要更多對照實驗數據支持。

6. **Raft agent 網路的生態發展**：目前 Raft 仍處於早期階段，其去中心化 agent 協作模式是否能形成可用的應用生態，值得長期觀察。

---

*報告日期：2026-07-15 ｜ 資料來源截止：UTC 今日*
