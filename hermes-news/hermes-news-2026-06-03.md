# Hermes-Agent 每日情報｜2026-06-03

> 搜尋範圍：過去 24 小時（UTC 基準）。本報告依搜尋結果如實撰寫，無法確認的細節保守呈現。

---

## 1｜今日重點摘要

1. **最新版本：v0.15.1（2026-05-29）**——此為 v0.15.0「Velocity Release」的同日熱修補版本，主要修復在 loopback 模式（Docker、hosted Hermes、全新安裝）下觸發的 dashboard 無限重載循環，截至 2026-06-03 尚無 v0.16 釋出跡象。
2. **v0.15.0「Velocity Release」核心改造**——`run_agent.py` 從 16,083 行大幅精簡至 3,821 行（縮減 76%），拆分為 14 個 `agent/*` 模組；啟動時間再少一秒，每次對話的函數呼叫量減少 47%，架構可維護性大幅提升。
3. **Kanban 升級為真正的多 agent 平台**——跨 104 個 PR，Kanban 加入 orchestrator 自動分解任務、swarm topology、排程任務、per-task worktree、per-task model override，成為 v0.15 的旗艦功能。
4. **session_search 效能飛躍**——session_search 重構後速度提升 4,500 倍，且完全不產生 LLM token 費用，對 session 歷史頻繁查詢的使用情境影響顯著。
5. **computer-use 開放非 Anthropic 模型**——v0.14.0「Foundation Release」引入的 `cua-driver` 後端，讓任何具備視覺能力的模型均可驅動桌面操作，不再限於 Anthropic Claude。
6. **xAI 深度整合**——v0.15.0 加入深度 xAI（Grok）整合，同時新增 Krea 2 Medium/Large、FAL 兩家圖片生成 provider，以及 Nous 官方 MCP catalog 與互動選擇器。
7. **DeepSeek V4 Pro via OpenRouter 崩潰問題**——已有 GitHub issue（#16677）記錄透過 OpenRouter 使用 `deepseek/deepseek-v4-pro` 時，Hermes gateway 進入崩潰循環，導致 Telegram bot 及其他訊息整合完全無回應。
8. **max_tokens 硬編碼引發 HTTP 402**——Hermes 在 `agent/anthropic_adapter.py` 與 `agent/model_metadata.py` 將 max_tokens 硬編碼為各模型上限（如 Claude Sonnet/Haiku 4.5 為 64,000），透過 OpenRouter 時觸發 HTTP 402 費用錯誤（issue #22879）。
9. **成長里程碑**——Hermes-Agent 自 2026-02-25 發佈以來，七週內累積 95,600 GitHub stars，為 2026 年成長最快的 agent framework，v0.15.0 commit 數達 1,302、merged PR 達 747。

---

## 2｜LLM 串接實戰回報

### 2-1 DeepSeek V4 Pro via OpenRouter → gateway 崩潰循環

來源：[GitHub NousResearch/hermes-agent#16677](https://github.com/NousResearch/hermes-agent/issues/16677)

**症狀**：將 `deepseek/deepseek-v4-pro` 設為 OpenRouter 預設模型後，Hermes gateway 進入崩潰循環，Telegram bot 及其他訊息整合完全無回應，問題持續多天（最初記錄於 2026-04-26 至 04-27）。

**根本原因**：OpenRouter upstream provider「Io Net」對 `deepseek/deepseek-v4-pro` 施加了激進的 rate limit，導致 Hermes 循環重試並崩潰。

**暫時解法**：前往 [openrouter.ai/settings/integrations](https://openrouter.ai/settings/integrations) 加入個人 DeepSeek API key，可取得獨立 rate limit 配額而非共享池，問題即可緩解。

### 2-2 max_tokens 硬編碼引發 OpenRouter HTTP 402

來源：[GitHub NousResearch/hermes-agent#22879](https://github.com/NousResearch/hermes-agent/issues/22879)

Hermes 在 `agent/anthropic_adapter.py` 與 `agent/model_metadata.py` 中將各模型的 max_tokens 設為上限值（例如 Claude Sonnet 4.6 / Haiku 4.5 設為 64,000）。透過 OpenRouter 路由時，OpenRouter 在計費估算超出帳戶餘額時回傳 HTTP 402，而非允許請求通過。目前此問題尚未完全修復，使用 OpenRouter + Claude 的使用者需確保帳戶餘額充裕，或等待 per-profile `max_tokens` 設定功能（issue 中已有 feature request）。

### 2-3 Claude API 整合設定（官方文件）

來源：[hermes-agent.nousresearch.com/docs/integrations/providers](https://hermes-agent.nousresearch.com/docs/integrations/providers)

Hermes 支援 Claude Opus 4.6、Sonnet 4.6、Haiku 4.5，透過直接 Anthropic API 或 OpenRouter 均可設定。所有模型統一走 Chat Completions API 路由，GPT-5+ 系列（除 gpt-5-mini 外）改走 Responses API。設定方式為在設定檔中指定 API key 與模型名稱，Hermes 自動接管記憶體、技能、訊息傳遞等 agent 層，LLM 負責推理與生成。

### 2-4 token 用量異常（社群回報）

來源：[Hermes Agent Troubleshooting Guide](https://www.getopenclaw.ai/blog/hermes-agent-troubleshooting)

Reddit 社群有使用者回報短暫使用後 token 消耗異常：「兩小時輕量使用就消耗了 400 萬 token，光是問天氣就用了 21,000 token。」目前尚不確定是特定模型或設定的問題，建議搭配 OpenRouter 的 per-request cost log 或 Anthropic Console 的 usage dashboard 監控實際消耗。

### 2-5 computer-use 現支援非 Anthropic 模型

來源：[GitHub NousResearch/hermes-agent releases v0.14.0](https://github.com/NousResearch/hermes-agent/releases/tag/v2026.5.16)

v0.14.0 引入的 `cua-driver` 後端解除了 computer-use 功能對 Anthropic Claude 的綁定，任何具備視覺能力的模型（包括 GPT-4o、Gemini、Grok 等）均可用來驅動桌面自動化工作流，且具備 focus-safe 操作設計。

---

## 3｜社群反應與討論亮點

- **「取代 Cursor 與 ChatGPT」**：MindStudio 部落格文章 [The AI Tools That Got Replaced in 2026](https://www.mindstudio.ai/blog/ai-tools-replaced-2026-claude-code-hermes-agent-killed-cursor-openclaw) 指出 Hermes-Agent（搭配 Claude Code）已在部分開發者工作流中取代 Cursor 與 ChatGPT 的角色，在社群引發廣泛討論。
- **Discord thread bug 積累**：多個 GitHub issue（#12496、#12750、#25312）記錄 Hermes 在 Discord forum channel 的首則訊息需手動 @mention 才觸發回應、多 bot thread 衝突等長期未完整修復的問題，社群持續追蹤。
- **OpenRouter + Hermes 入門指引**：[openclawlaunch.com](https://openclawlaunch.com/guides/hermes-agent-openrouter) 的「一把 key 接 200+ 模型」指引在社群廣泛流傳，吸引不想為每家 provider 分別管理 key 的使用者。
- **95K stars 里程碑討論**：TokenMix blog 的 [Hermes Agent Review](https://tokenmix.ai/blog/hermes-agent-review-self-improving-open-source-2026) 詳述了從發佈到七週內 95,600 stars 的成長歷程，被多個 AI 新聞聚合器引用，社群對「自成長 agent」概念展現高度興趣。

---

## 4｜值得追蹤的後續議題

| 議題 | 追蹤重點 |
|------|----------|
| v0.16 釋出時程 | v0.15.1 於 5/29 發佈後目前無新版訊號，預計何時推出 |
| DeepSeek via OpenRouter crash 修復 | issue #16677 是否在下個版本中正式修復 |
| max_tokens per-profile 設定 | issue #22879 的 feature request 進展，避免 HTTP 402 |
| token 消耗異常根因調查 | 社群 token 暴增報告是否有具體 root cause 確認 |
| computer-use 非 Anthropic 模型實測 | cua-driver 搭配 GPT-4o、Gemini 的社群實測穩定性回報 |
| Discord thread 行為修復 | issue #12496、#12750 長期 bug 是否有修復計畫 |
| Kanban 多 agent 真實案例 | orchestrator + swarm topology 的社群實際部署案例 |

---

*資料來源（搜尋日期：2026-06-03）*
- [github.com/NousResearch/hermes-agent](https://github.com/nousresearch/hermes-agent)
- [github.com/NousResearch/hermes-agent/releases](https://github.com/NousResearch/hermes-agent/releases)
- [github.com hermes-agent releases v0.15.1](https://github.com/NousResearch/hermes-agent/releases/tag/v2026.5.29)
- [github.com hermes-agent releases v0.15.0](https://github.com/NousResearch/hermes-agent/releases/tag/v2026.5.28)
- [github.com hermes-agent releases v0.14.0](https://github.com/NousResearch/hermes-agent/releases/tag/v2026.5.16)
- [github.com hermes-agent releases v0.13.0](https://github.com/NousResearch/hermes-agent/releases/tag/v2026.5.7)
- [github.com hermes-agent issue #16677](https://github.com/NousResearch/hermes-agent/issues/16677)
- [github.com hermes-agent issue #22879](https://github.com/NousResearch/hermes-agent/issues/22879)
- [github.com hermes-agent issue #12496](https://github.com/NousResearch/hermes-agent/issues/12496)
- [github.com hermes-agent issue #12750](https://github.com/NousResearch/hermes-agent/issues/12750)
- [hermes-agent.nousresearch.com/docs/integrations/providers](https://hermes-agent.nousresearch.com/docs/integrations/providers)
- [tokenmix.ai Hermes Agent Review](https://tokenmix.ai/blog/hermes-agent-review-self-improving-open-source-2026)
- [blakecrosley.com hermes v0.15 reference](https://blakecrosley.com/guides/hermes)
- [mindstudio.ai ai-tools-replaced-2026](https://www.mindstudio.ai/blog/ai-tools-replaced-2026-claude-code-hermes-agent-killed-cursor-openclaw)
- [openclawlaunch.com hermes-agent-openrouter](https://openclawlaunch.com/guides/hermes-agent-openrouter)
- [petronellatech.com hermes-agent-ai-guide](https://petronellatech.com/blog/hermes-agent-ai-guide/)
