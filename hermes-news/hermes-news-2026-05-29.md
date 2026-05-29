# Hermes-Agent 每日情報 — 2026-05-29

> 資料範圍：過去 24 小時（UTC 2026-05-28 ～ 2026-05-29）
> 專案：[NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent)

---

## 1. 今日重點摘要

1. **v0.15.0「The Velocity Release」正式釋出（2026-05-28）**：這是今日最重要的事件。此版本包含 1,302 commits、747 個合併 PR、1,746 個檔案異動，關閉 560+ 個 issue（含 15 個 P0、65 個 P1、19 個標記為 security），共 321 位社群貢獻者參與。

2. **核心程式碼大幅重構（-76% 程式碼量）**：`run_agent.py` 從 16,083 行削減至 3,821 行，拆分為 14 個內聚模組；每次對話的函式呼叫次數減少 47%，為名副其實的「Velocity」版本。

3. **啟動速度提升 63%**：`hermes --version` 冷啟動從 701ms 降至 258ms。Codex CLI 對比測試從 5/11 勝率提升至 6/11，版本說明明確以此為競爭基準。

4. **Kanban 升級為真正的多 Agent 平台**：橫跨 104 個 PR 的改動讓 Kanban 支援 orchestrator 自動任務拆解、swarm 拓撲、排程任務、每任務獨立 worktree，以及每任務 model override。

5. **Session 搜尋速度提升 4,500 倍且免費**：舊版需付費的 session 搜尋功能在此版本完全重寫，效能大幅提升並移除費用限制。

6. **Promptware 防禦機制上線**：針對「Brainworm 級」提示注入攻擊新增防禦層，這是 v0.15.0 的 security-tagged 修補重點之一。

7. **v0.14.0（2026-05-16）核心功能仍在社群熱烈討論**：OpenAI 相容本地 Proxy（可將 Claude Pro / ChatGPT Pro / SuperGrok 轉為標準 endpoint）、xAI SuperGrok OAuth（grok-4.3，1M context）及 HuggingFace Skills Hub 整合，在 v0.15.0 釋出前後持續是討論焦點。

8. **社群規模**：截至今日，相關社群內容統計涵蓋 X/Twitter 34 篇、Reddit 15 篇、GitHub 38 篇、YouTube 8 支影片、部落格 12 篇、Podcast 2 集。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）

- **Claude Pro OAuth Proxy 實戰**：v0.14.0 引入的 OpenAI 相容本地 Proxy 允許 Claude Pro 帳戶透過 OAuth 認證後，讓 Codex / Aider / Cline / Continue 直接使用同一個 endpoint。多位使用者回報設定成功，但需注意 Anthropic 官方不允許訂閱 Token 用於第三方服務的條款風險。（來源：[blakecrosley.com](https://blakecrosley.com/guides/hermes)、[hermes-agent release v0.14.0](https://github.com/NousResearch/hermes-agent/releases/tag/v2026.5.16)）
- **Fallback 機制**：Hermes 可在 Claude 遭遇 rate limit、auth 失效或伺服器過載時，自動切換備用 provider，且**不中斷對話上下文**。系統可辨識 Bedrock/LiteLLM 的「Too many tokens per day」與 Vertex AI/GCP 的「RESOURCE_EXHAUSTED」，並將其視為配額耗盡（而非暫時性錯誤）進行處理。（來源：[Fallback Providers 文件](https://hermes-agent.nousresearch.com/docs/user-guide/features/fallback-providers)）

### xAI Grok（SuperGrok）

- **grok-4.3 上線，1M Context Window**：v0.14.0 新增 SuperGrok 作為 OAuth provider，grok-4.3 支援 100 萬 token 上下文，目前社群對長文件處理場景反應正面，但 SuperGrok 訂閱費用較高，多數人仍將其設為 Claude/GPT 的備用選項。（來源：[v0.14.0 Release Notes](https://github.com/NousResearch/hermes-agent/releases/tag/v2026.5.16)）

### OpenAI GPT

- **本地 Proxy 橋接 ChatGPT Pro**：同一套 OpenAI 相容 Proxy 也可對接 ChatGPT Pro OAuth，讓現有使用 OpenAI API 格式的工具無縫接入。實作細節仍在社群整理中，OnlyTerp 的 [hermes-optimization-guide](https://github.com/OnlyTerp/hermes-optimization-guide) 有步驟說明。

### Gemini（Google）

- v0.13.0 已加入 `video_analyze` 工具，原生支援 Gemini 及相容多模態模型的影片理解，目前尚無 v0.15.0 針對 Gemini 的特別更新，但 fallback 機制同樣適用。

### 跨 Provider 通用

- **每任務 model override**：v0.15.0 Kanban 支援每個子任務指定不同模型，使用者可針對不同類型任務（如程式生成用 Opus、快速查詢用 Haiku）混用，降低整體成本。
- **錯誤分類邏輯**：Hermes 區分「容量耗盡型」與「暫時性 rate limit」，前者觸發 fallback，後者進入 retry queue，避免不必要的 provider 切換。（來源：[FAQ & Troubleshooting](https://hermes-agent.nousresearch.com/docs/reference/faq)）

---

## 3. 社群反應與討論亮點

- **Context Studios 分析文「Agent Runtimes Become Operating Systems」**：[contextstudios.ai](https://www.contextstudios.ai/blog/hermes-v014-agent-runtimes-operating-systems) 認為 v0.14.0 的 Proxy + Skills Hub + Kanban 組合意味著 Hermes 正從「AI 助手」轉型為「agent 作業系統」，此觀點在 X.com 引發廣泛轉發與討論。
- **NVIDIA Blog 報導**：Hermes 被 NVIDIA 官方部落格點名，強調其與 RTX AI PC 及 DGX Spark 的整合能力，進一步提升主流知名度。（來源：[NVIDIA Blog](https://blogs.nvidia.com/blog/rtx-ai-garage-hermes-agent-dgx-spark/)）
- **v0.15.0 速度提升數字引發熱議**：「4,500 倍搜尋加速」與「63% 啟動速度提升」的數字在 Reddit 和 X.com 獲得大量轉貼，但也有使用者要求提供更詳細的 benchmark 方法。
- **Kanban Multi-Agent 使用案例分享**：社群開始分享以 Kanban 執行並行研究任務的心得，部分使用者回報在複雜 RAG 流程中搭配 swarm 模式效果顯著。
- **darwinian-evolver skill 引關注**：v0.14.0 新增的演化式提示調優 skill 在開發者社群引起高度興趣，目前文件較少，社群正在自行整理使用範例。

---

## 4. 值得追蹤的後續議題

| 議題 | 說明 |
|------|------|
| **v0.15.0 Benchmark 方法透明度** | 社群要求公開 Codex CLI 對比測試的完整 benchmark 腳本與環境設定 |
| **Promptware 防禦技術細節** | Brainworm 級攻擊防護機制的技術說明尚未完整揭露 |
| **SuperGrok 費用評估** | grok-4.3 的 1M context 雖吸引人，但 SuperGrok 訂閱成本效益仍待社群長期使用後給出結論 |
| **OpenAI 相容 Proxy 穩定性** | 目前仍是新功能，Claude Pro / ChatGPT Pro OAuth 接入在邊緣案例的穩定性需更多使用者回報 |
| **darwinian-evolver skill 文件化** | 官方文件不足，社群自建的使用案例整理值得持續追蹤 |
| **v0.16.0 路線圖** | v0.15.0 釋出後官方尚未揭露下一版重點，預計近期會更新 |

---

*資料來源：[GitHub NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent) · [v0.15.0 Release](https://github.com/NousResearch/hermes-agent/releases/tag/v2026.5.28) · [v0.14.0 Release](https://github.com/NousResearch/hermes-agent/releases/tag/v2026.5.16) · [Hermes 官方文件](https://hermes-agent.nousresearch.com/) · [Context Studios Blog](https://www.contextstudios.ai/blog/hermes-v014-agent-runtimes-operating-systems) · [NVIDIA Blog](https://blogs.nvidia.com/blog/rtx-ai-garage-hermes-agent-dgx-spark/) · [blakecrosley.com](https://blakecrosley.com/guides/hermes) · [OnlyTerp optimization guide](https://github.com/OnlyTerp/hermes-optimization-guide)*
