# Hermes-Agent 每日情報 — 2026-07-20

> 資料來源涵蓋截至 2026-07-20 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **當前穩定版為 v0.18.2（2026-07-07）**：這是 v0.18.0「The Judgment Release」的補丁版，主要修復 WhatsApp Baileys 依賴項問題，確保 tagged-release Docker build 的穩定性。團隊目前約每兩週發布一個新版本。（[GitHub Release](https://github.com/NousResearch/hermes-agent/releases/tag/v2026.7.7.2)）

2. **v0.18.0「The Judgment Release」是近期最重大版本**：於 2026-07-01 釋出，涵蓋約 1,720 次 commits、998 個合併 PR、2,215 個修改檔案，來自 370+ 位社群貢獻者，並宣告 100% 關閉所有 P0 與 P1 issues。（[Release Notes](https://github.com/NousResearch/hermes-agent/releases/tag/v2026.7.1)）

3. **Mixture-of-Agents 成為一等公民**：用戶可建立命名的模型 ensemble，像選擇單一模型一樣選取，每個 reference 模型的推理過程可見，aggregator 的最終答案即時串流輸出。

4. **背景並行子代理（Background Fan-out）**：`delegate_task` 現在可同時啟動多個後台子代理，讓用戶在主對話繼續工作的同時處理平行任務，大幅提升多任務處理效率。

5. **桌面應用 Coding Projects 首次亮相**：官方 Electron 桌面應用新增 Coding Projects 側邊欄，支援 codebase 列表、coding rail、review pane，以及 git worktree 管理，對開發者工作流有顯著改善。

6. **Google Vertex AI 支援 Gemini**：Hermes 現可透過 GCP 服務帳號接入 Vertex AI 的 Gemini 模型，無需靜態 API Key，改以自動刷新的短期 OAuth2 access token 運作。（[Provider 文件](https://hermes-agent.nousresearch.com/docs/integrations/providers/)）

7. **Nous Portal 統一訂閱閘道**：支援 300+ 前沿 agentic 模型（含 Claude、GPT、Gemini），透過單一 Nous 訂閱計費，降低多 provider 管理成本。

8. **工具呼叫（Tool Calling）行為收緊**：v0.18.2 的 tool calling 更嚴格，消除「近似工具呼叫」的模糊行為，改為明確的呼叫或不呼叫。社群提醒依賴特定 tool schema 的工作流需重新測試。（[來源](https://blog.meetneura.ai/hermes-agent-update-whats-new-in-v0-18-2-and-how-to-use-hermes-safely/)）

9. **Context 需求提醒**：Hermes Agent 要求模型至少具備 64,000 tokens 的 context window，小於此限制的模型將在啟動時被拒絕，確保多步驟 tool-calling 工作流有足夠的 working memory。（[FAQ](https://hermes-agent.nousresearch.com/docs/reference/faq)）

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）
- **Auth 方式**：支援直接 API Key、`hermes auth add anthropic` 的 OAuth 授權、OpenRouter，或任何相容 proxy。
- **Agent SDK Credit（2026-06-15 起）**：Anthropic 推出獨立的月費 Agent SDK credit，明確涵蓋透過 Agent SDK 驗證的第三方應用（如 Hermes），解決此前授權灰色地帶的問題。
- **Rate Limit 已知 Bug**：GitHub issue [#31668](https://github.com/NousResearch/hermes-agent/issues/31668) 記錄了搭配 Anthropic 模型時出現 rate limit 錯誤及異常用量的問題，v0.18.0 已列為 P1 並宣告關閉，但社群仍有少數用戶反映偶發。
- **Claude 模型 Ensemble 應用**：在新 Mixture-of-Agents 功能中，Claude 常被選為 aggregator 角色，搭配其他 Hermes 模型作為 reference，社群評價其整合結果品質最高。

### GPT（OpenAI）
- GPT-5+ 模型（除 gpt-5-mini 外）自動使用 Responses API，其餘模型（含 Claude、Gemini）使用 Chat Completions API。（[Provider 文件](https://github.com/NousResearch/hermes-agent/blob/main/website/docs/integrations/providers.md)）
- 社群在 r/LocalLLaMA 的討論普遍建議：使用 Hermes 模型進行 tool use 取得最佳相容性，需要複雜推理時切換 Claude 或 GPT。

### Gemini（Google）
- Vertex AI 支援（v0.18.0 新增）是目前最穩定的接入方式，透過 GCP 服務帳號自動管理短期 OAuth2 token。
- 直接 API 或 OpenRouter 亦可使用，但部分功能（如 vision 輔助 LLM 任務）預設使用 Gemini Flash 作為 auxiliary model。
- **Auxiliary Model 設計**：vision、web 摘要、壓縮、記憶刷新等操作使用獨立的 auxiliary LLM，預設自動偵測順序為 OpenRouter → Nous → Codex，Gemini Flash 常為最終選項。

### DeepSeek / Mistral / 其他
- 均可透過 OpenRouter 或 Nous Portal 接入，並使用 Chat Completions 相容介面。
- `hermes config set rate_limit_rpm` 可設定本地請求佇列上限，防止 429 錯誤直接傳給 provider。

### 踩坑紀錄
- **`max_tokens` 設定無效**：GitHub issue [#4404](https://github.com/NousResearch/hermes-agent/issues/4404) 記錄 `config.yaml` 中的 `model.max_tokens` 從未被傳遞給 AIAgent，導致模型忽略自訂 token 上限設定。此 issue 在 v0.18.0 前為 P1，宣告已關閉，建議升級後驗證。
- **Telegram gateway 無限 tool-call 迴圈**：部分用戶在 Telegram 通道遭遇 token 爆增與 tool-call 無限迴圈，v0.16 起持續修復，v0.18.x 已大幅改善。
- **雙 gateway 搶占同一 bot token**：若兩個 profile 同時使用相同 bot token，第二個 gateway 將連線失敗，為常見設定錯誤。

### Credential Pools（進階）
- 官方支援 Credential Pools 功能，可設定多組 API Key 輪替，進一步緩解 rate limit 壓力。（[文件](https://hermes-agent.nousresearch.com/docs/user-guide/features/credential-pools)）

---

## 3. 社群反應與討論亮點

- **Nous Research X 宣告**：[@NousResearch](https://x.com/NousResearch) 在 v0.18.0 釋出後宣布 Hermes Desktop 公開預覽，首次在 NVIDIA GTC keynote 展示，已在 macOS/Linux/Windows 的 RTX Spark 超晶片上測試，整合 OpenShell runtime。（[X 貼文](https://x.com/NousResearch/status/2061843507417944552)）
- **Hermes Agent Hackathon 結果**：Nous Research 在 X 上宣布 hackathon 三位獲獎者，並指出獲獎作品展現了「強大且實用的 agent 工具」，符合 Hermes 的設計初衷。（[X 貼文](https://x.com/NousResearch/status/2077517430457602471)）
- **r/selfhosted 討論熱絡**：自架用戶高度關注資料隱私，Hermes 的自架選項受到正面評價，討論聚焦在搭配本地 Hermes 模型（搭配 Ollama）的私有部署。
- **工具呼叫行為改變需注意**：v0.18.2 更嚴格的 tool calling 引發部分用戶回報工作流中斷，社群建議在升級後重新測試所有依賴工具呼叫的 skill 與 automation。
- **Feature Request #25267**：有用戶提交 issue 要求支援「Claude Agent SDK model provider with subscription OAuth（Codex 風格）」，目前為 open 狀態，受到一定關注。（[Issue](https://github.com/NousResearch/hermes-agent/issues/25267)）

---

## 4. 值得追蹤的後續議題

| 議題 | 說明 | 追蹤連結 |
|------|------|----------|
| v0.19.0 下一版本動向 | 根據約兩週一版的節奏，預計八月初釋出 | [Releases](https://github.com/NousResearch/hermes-agent/releases) |
| Issue #31668 Claude rate limit 偶發問題 | 已列 P1 並宣告關閉，但仍有用戶回報，需持續觀察 | [Issue](https://github.com/NousResearch/hermes-agent/issues/31668) |
| Issue #4404 max_tokens 設定失效 | v0.18.0 宣告修復，建議升級後以實際 token 用量驗證 | [Issue](https://github.com/NousResearch/hermes-agent/issues/4404) |
| Feature #25267 Claude Agent SDK OAuth | 社群關注度高，官方尚未表態 | [Issue](https://github.com/NousResearch/hermes-agent/issues/25267) |
| Hermes Desktop 公開預覽穩定性 | 首次亮相即整合 RTX Spark，需追蹤多平台實際穩定度 | [X 宣告](https://x.com/NousResearch/status/2061843507417944552) |
| Mixture-of-Agents 實戰心得 | 新功能正在被社群廣泛測試，LLM 組合效能比較心得持續累積 | [Changelog](https://hermes-ai.net/changelog/) |

---

*報告生成時間：2026-07-20 UTC*
