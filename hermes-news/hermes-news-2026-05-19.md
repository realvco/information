# Hermes-Agent 每日情報｜2026-05-19

> 搜尋時間範圍：過去 24 小時（UTC 2026-05-18 ～ 2026-05-19）

---

## 1. 今日重點摘要

1. **v0.14.0「Foundation Release」發布**：於 2026-05-16 發布的最新正式版，共包含 808 次 commits、633 個合併 PR、1393 個檔案變更（新增 165,061 行、關閉 545 個 issues，其中 P0 問題 12 個、P1 問題 50 個）。
2. **xAI Grok 正式登場**：Grok 以 SuperGrok OAuth provider 身份加入，grok-4.3 上下文視窗從舊版提升至 **1M tokens**，與 Claude Sonnet 同級。
3. **本地 OpenAI-compatible Proxy**：v0.14 新增本地 Proxy 功能，可將任何已取得 OAuth 授權的 Hermes provider（Claude Pro、ChatGPT Pro、SuperGrok）包裝成 OpenAI-compatible endpoint，讓 Codex、Aider、Cline、Continue 等工具直接調用。
4. **LSP 語意診斷整合**：Agent 每次寫檔（`write_file` / `patch`）後，Hermes 會自動執行 Language Server Protocol 診斷，將新出現的語法或型別錯誤即時回饋給 agent，在下一輪對話前完成修正。
5. **新訊息平台**：LINE 與 SimpleX Chat 正式成為第一類支援平台（first-class platform），Microsoft Teams 整合也已完整串接（Graph auth + webhook listener + pipeline runtime + outbound delivery）。
6. **X（Twitter）搜尋工具**：`x_search` 以 first-class 工具身份加入，支援 OAuth 或 API Key 兩種認證方式，可在 agent 工作流中直接搜尋 X 平台。
7. **9 個新選用技能（skills）**：包含 Hyperliquid 交易、Yahoo Finance、api-testing、統一 EVM 多鏈、darwinian-evolver、osint-investigation、pinggy-tunnel、watchers，以及 Notion 全面重構。
8. **電腦使用（Computer Use）跨模型化**：`cua-driver` backend 現在不再綁定 Anthropic，任何具備視覺能力的模型都能驅動桌面操作，擴大非 Claude 用戶的使用可能性。
9. **Claude API Token 限額大幅提升**：Anthropic 近期宣布 Tier 1 output tokens per minute 從 8,000 提升至 80,000（10 倍），對 multi-agent 工作流影響顯著——以每次回應平均 400 tokens 計算，每分鐘可支撐 200 次完成（舊限制僅 20 次）。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）

- **Context 上限差異**：同一模型在不同 provider 上下文視窗不同——`claude-opus-4.6` 在 Anthropic 直連為 1M tokens，但透過 GitHub Copilot 僅有 128K。Hermes 在 CLI 啟動時會顯示偵測到的 context 長度（例：`📊 Context limit: 128000 tokens`），幫助用戶快速識別。
- **Token 限額 10 倍提升**：Tier 1 Output tokens per minute 從 8,000 → 80,000，大幅改善多 agent 並發場景的瓶頸。
- **費用核算**：Hermes 將各 provider 回傳的使用量標準化為 `CanonicalUsage`，透過 per-million Decimal 數學計算成本，解決不同 provider 回傳格式不一致的問題。
- 來源：[Claude API Token Limits 說明 - MindStudio](https://www.mindstudio.ai/blog/claude-api-token-limits-increase-tier-breakdown)、[Ken Huang Substack - Cost & Token-Usage Accounting](https://kenhuangus.substack.com/p/chapter-1-cost-and-token-usage-accounting)

### xAI Grok

- v0.14.0 正式加入 SuperGrok OAuth provider，grok-4.3 支援 1M context window。
- 屬首次整合，目前穩定性與實際效能的社群回饋仍在累積中。
- 來源：[Hermes Agent v0.14.0 Release Notes](https://github.com/NousResearch/hermes-agent/releases/tag/v2026.5.16)

### OpenRouter（多模型路由）

- Hermes 支援 OpenRouter，可存取 200+ 模型，包含 Anthropic、OpenAI、Google、DeepSeek 等。
- Rate limit 超過時，建議透過多 provider 路由來分散請求，避免單一 provider 觸發限流。
- 來源：[Hermes Agent 官方文件 - AI Providers](https://hermes-agent.nousresearch.com/docs/integrations/providers)

### 本地模型（Ollama / vLLM / SGLang）

- v0.14 的本地 proxy 功能讓本地部署的模型可透過 OpenAI-compatible endpoint 被 Hermes 及其他工具使用。
- 適合在意資料隱私或希望避免雲端 API 費用的用戶。
- 來源：[Hermes Agent v0.14.0 Release Notes](https://github.com/NousResearch/hermes-agent/blob/main/RELEASE_v0.14.0.md)

### Computer Use 跨模型化

- `cua-driver` 現已支援非 Anthropic 視覺模型，開放任何 vision-capable 模型驅動桌面。
- 此前 Computer Use 功能僅限 Claude 系列，現在 GPT、Gemini 等具視覺能力的模型亦可使用。

---

## 3. 社群反應與討論亮點

- **「Foundation Release」定位**：v0.14 被官方定位為「讓 Hermes 能在任何地方安裝並運行」的基礎版本，社群普遍正面評價其廣泛平台支援（LINE、SimpleX、Teams）與開放 Computer Use 給其他模型的決定。
- **假帳號爭議**：部分有經驗的社群用戶公開表示，懷疑 Hermes 有協調性假帳號在各平台推廣，並明確表態拒絕嘗試，使信任度受到一定影響。根據對 Reddit 1,300+ 則留言的分析，此疑慮確實存在於部分用戶之間。
- **OpenClaw vs Hermes 論戰**：兩個工具的社群在 Reddit 持續進行比較討論，Hermes 在新功能迭代速度與 provider 廣度上獲肯定，OpenClaw 則以整合數量（50+ 服務）與技能生態系見長。
- **安全性警示**：社群指出 Hermes 目前零報告的 agent CVE 反映的是曝光度有限而非經過完整安全審計，建議自架用戶檢查預設 sandboxing 設定。
- **Portkey 整合**：Portkey 已官方支援 Hermes Agent，可作為 LLM 閘道（gateway）使用，提供監控、快取、fallback 等功能。
- 來源：[Kilo AI - OpenClaw vs Hermes Reddit 分析](https://kilo.ai/articles/openclaw-vs-hermes-what-reddit-says)、[Portkey 文件 - Hermes Agent](https://portkey.ai/docs/integrations/libraries/hermes-agent)

---

## 4. 值得追蹤的後續議題

| 議題 | 說明 |
|------|------|
| v0.14 穩定性觀察 | 發布於 2026-05-16，目前進入社群實測期，Bug 回報可能陸續出現 |
| Grok SuperGrok OAuth 實測 | 首次整合，用戶對 1M context 的實際體驗與穩定性尚未有大量回饋 |
| 本地 Proxy 與 Codex / Aider 相容性 | 新功能允許 Codex 等工具透過 Hermes provider 路由，相容性邊界案例值得關注 |
| LSP 語意診斷的語言支援範圍 | 目前支援哪些程式語言尚不明確，社群測試結果待觀察 |
| Microsoft Teams 整合穩定度 | 整合已「接通」但為首版，實際生產環境穩定性尚需驗證 |
| 假帳號爭議後續 | 是否影響社群對 Hermes 的採用意願，NousResearch 是否會正式回應 |
| Claude Tier 1 10x Token 提升的實際效益 | 多 agent 工作流的實際吞吐量改善是否如理論計算般顯著 |

---

*本報告依據公開搜尋結果彙整，資訊截至 2026-05-19 UTC。如有遺漏或錯誤，歡迎回報修正。*
