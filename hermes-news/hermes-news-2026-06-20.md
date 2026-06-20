# Hermes-Agent 每日情報 — 2026-06-20

> 搜尋範圍：過去 24 小時（2026-06-19 ～ 2026-06-20 UTC）

---

## 1. 今日重點摘要

1. **v0.16.0「Surface Release」（2026-06-05）社群迴響持續熱烈**：距離發布已兩週，Medium、Substack、技術部落格仍大量刊出使用心得，尤其 Desktop App 的 drag-and-drop 與多 profile 並行功能受到高評價。
2. **Issue #48721、#48716、#48715（2026-06-19 開啟）**：昨日連開三個 issue，分別涉及 config system 功能請求、gateway 功能需求、以及 TUI 相關問題，顯示社群在 Desktop App 發布後積極回饋邊界問題。
3. **Issue #25267「Claude Agent SDK 訂閱 OAuth 支援」（2026-06-15）**：Anthropic 推出獨立 Agent SDK 月費方案與第三方 app 訂閱 OAuth 路徑，社群要求 Hermes-Agent 跟進支援，討論仍活躍中。
4. **DeepSeek V4 Pro via OpenRouter 已知 crash bug**（Issue #16677）：以 `deepseek/deepseek-v4-pro` 作為預設模型時，gateway 進入 crash loop，回傳 HTTP 401「User not found」，目前暫時建議改用 `deepseek/deepseek-v4` 或切換至其他 provider。
5. **Hermes Desktop App 正式上架（macOS / Linux / Windows）**：一鍵安裝、in-app 自動更新、連接遠端 gateway 支援，並完整翻譯為簡體中文；繁體中文在社群討論中呼聲高，尚未列入官方 roadmap。
6. **Kanban 多 agent 看板（v0.13 Tenacity Release）持續穩定**：heartbeat + zombie detection + hallucination recovery 機制在社群測試中通過長時間 overnight run，被認為是目前最穩定的多 agent 協作框架之一。
7. **最低 token 需求 64K**：Hermes-Agent 核心運作需至少 64,000 tokens context window，使用 context < 64K 的模型（如部分 Mistral 舊版）會觸發 hard crash，需特別注意模型選擇。
8. **NVIDIA/skills 加入 Skills Hub 受信任清單**：v0.16.0 新增，可直接安裝 NVIDIA 官方 skills；model picker 全面支援模糊搜尋（Desktop / Web / TUI / CLI 統一）。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）

- **支援版本**：Claude Opus 4.6、Sonnet 4.6、Haiku 4.5，透過 Anthropic API key 設定。
- **Claude Agent SDK 訂閱 OAuth（Issue #25267，2026-06-15）**：Anthropic 推出獨立 Agent SDK 月費信用（`sk-ant-oat01` 格式），Hermes-Agent 社群正討論是否實作支援此路徑，以降低高量使用的 per-token 成本。目前官方尚未回應 ETA。
- **實際評價**：Claude 在 Hermes-Agent 工作流中以 tool-call 可靠性與長 context 連貫性著稱，是需要高品質推理的 skill 鏈接場景首選。
- **來源**：[Issue #25267](https://github.com/NousResearch/hermes-agent/issues/25267)、[AI Providers Docs](https://hermes-agent.nousresearch.com/docs/integrations/providers)

### DeepSeek V4 Pro via OpenRouter

- **已知 crash loop（Issue #16677）**：設定 `deepseek/deepseek-v4-pro` 為預設模型後，gateway 反覆崩潰，錯誤訊息為 HTTP 401「User not found」。原因疑似 OpenRouter 端的認證 header 格式在 V4 Pro 路由上有變更。
- **暫時解法**：改用 `deepseek/deepseek-v4`（非 Pro 版），或改由 DeepSeek 官方 API 直接接入。V4 Pro 搭配低成本 overnight run 的組合吸引力高，此 bug 受到社群高度關注。
- **來源**：[Issue #16677](https://github.com/NousResearch/hermes-agent/issues/16677)、[Overnight AI Setup Guide](https://www.aifloxium.online/blog/hermes-agent-deepseek-v4-openrouter-overnight-ai-setup)

### OpenRouter（多模型整合）

- **Credential Pool 機制**：Hermes-Agent 支援多 API key pool，遇 429 rate limit 時自動輪換，plan/usage limit 立即切換，generic 429 先 retry 一次再切換，最終 fallback 至 `fallback_model`。
- **模型 fallback chain 設定**：建議設定至少兩個 provider 的 fallback，以防止單一 provider 短暫中斷影響長時間 agent 任務。
- **來源**：[How to Use Hermes with OpenRouter](https://hermes-agent.ai/how-to/use-hermes-with-openrouter)、[Credential Pools Docs](https://hermes-agent.nousresearch.com/docs/user-guide/features/credential-pools)

### Gemini

- **Google Gemini CLI OAuth（v0.11 新增）**：透過瀏覽器 OAuth 登入可使用 Gemini 免費額度，適合個人探索，企業場景仍建議使用 API key。
- **video_analyze tool（v0.13）**：Gemini 與相容多模態模型現支援 native video understanding，可在 skill 中直接呼叫影片分析。
- **Gemini-3.5-Flash（v0.16 新增）**：加入 model picker，低延遲高吞吐，適合大量 classification 任務。

### LiteLLM / 多 Provider

- **LiteLLM 作為統一 proxy**：社群推薦透過 LiteLLM 接入 100+ LLM providers，在 Hermes-Agent 中實現無縫切換、load balancing 與 budget 控制，特別適合需要跨 provider 比較的實驗場景。
- **來源**：[Configuration Docs](https://hermes-agent.nousresearch.com/docs/user-guide/configuration)

---

## 3. 社群反應與討論亮點

- **Desktop App 正面評價為主**：多篇 Medium、Substack 文章（含 Ewan Mak in Medium）評價 Desktop App 的多 profile 並行 session 為重大生產力提升，特別是需同時管理多個 agent 任務的用戶。
- **DeepSeek V4 Pro 踩坑帖熱傳**：Issue #16677 在 HackerNews 與 Reddit r/LocalLLaMA 上獲得討論，多位用戶確認同樣的 crash loop 現象。
- **繁體中文支援呼聲**：v0.16.0 加入簡體中文翻譯，台灣與香港社群用戶在 Discord 呼籲儘速加入繁體中文，目前尚無官方回應。
- **Kanban overnight run 報告**：多位用戶在 Discord 分享以 Kanban board 搭配 DeepSeek V4（非 Pro）執行 8-12 小時自動任務的成功案例，zombie detection 機制被認為是關鍵穩定因素。
- **來源**：[Medium - Hermes Agent Desktop App](https://medium.com/@tentenco/hermes-agent-desktop-app-everything-you-need-to-know-about-nous-researchs-self-improving-ai-agent-3cb59bd31e5f)、[Hermes Agent News June 2026](https://blog.mean.ceo/hermes-agent-news-june-2026/)

---

## 4. 值得追蹤的後續議題

| 議題 | 說明 | 追蹤連結 |
|------|------|---------|
| Issue #16677 | DeepSeek V4 Pro via OpenRouter crash loop，待修復 | [GitHub](https://github.com/NousResearch/hermes-agent/issues/16677) |
| Issue #25267 | Claude Agent SDK 訂閱 OAuth 支援，Anthropic 新方案跟進 | [GitHub](https://github.com/NousResearch/hermes-agent/issues/25267) |
| Issue #48721、#48716、#48715 | 2026-06-19 新開 issues，config / gateway / TUI 相關 | [Issues](https://github.com/NousResearch/hermes-agent/issues) |
| 繁體中文本地化 | 社群呼聲高，官方尚未回應 roadmap | [Discord / Issues](https://github.com/NousResearch/hermes-agent) |
| v0.17 下一版本 | Surface Release 後的下一個功能方向（可能含 admin panel 強化與更多 OAuth provider） | [Releases](https://github.com/NousResearch/hermes-agent/releases) |
| Credential Pool + fallback 最佳實踐 | 多 provider 架構下的穩定性調校，社群正在整理 cookbook | [Credential Pools Docs](https://hermes-agent.nousresearch.com/docs/user-guide/features/credential-pools) |

---

*本報告由自動化 routine 產出，資料來源為公開網路搜尋，不代表 NousResearch 或 Hermes-Agent 官方立場。*
