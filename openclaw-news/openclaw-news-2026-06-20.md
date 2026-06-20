# OpenClaw 每日情報 — 2026-06-20

> 搜尋範圍：過去 24 小時（2026-06-19 ～ 2026-06-20 UTC）

---

## 1. 今日重點摘要

1. **2026.6.6-beta.1 發布**：修復 memory search timeout（#91742）、CLI gateway RPC timeout 驗證問題（#92158），並改進 release 流程（#92150）。此 beta 版可視為 2026.6.8 穩定版的前驅。
2. **2026.6.8 穩定版為最新里程碑**（上週發布，社群熱議持續）：Telegram 現可渲染帶表格、列表、可折疊 blockquote 的結構化文字；WhatsApp 正確套用 ACP binding 設定；Gateway 與 Agent 的 recovery 機制更健壯；Usage footer hooks 上線；managed plugin 更新修復。
3. **Issue #95131（2026-06-19 開啟）**：由 a-m-a-r-a 回報，標記需要「live reproduction」，細節尚待社群確認，屬昨日最新 open issue。
4. **Issue #90456 持續追蹤**：OpenAI OAuth Realtime Talk sessions 在 `/v1/realtime/calls` 路由回傳 HTTP 500，影響使用 OpenAI Realtime API 的工作流，目前仍未關閉。
5. **安全漏洞 CVE-2026-34505 補丁確認**：2026.3.12 以前版本存在 rate limiting bypass（webhook secret 暴力猜測），目前已修補，用戶應確保升級。
6. **GitHub Stars 突破 250,000**：從 1 月的 106K 增至 6 月的 250K+，136% 成長，社群規模持續擴大。
7. **ClawHub 優先級提升**：npm 安裝路徑已退為備選，ClawHub 成為 skill 安裝的主要入口，目前收錄 4,000+ 社群 skills。
8. **Skill Workshop 待確認 Bug**（Issue #90388）：提案「tweet-argument-analysis」在 agent 回合中超時後 Skill Workshop UI 仍顯示為等待中，屬 UI 狀態同步問題。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）

- **認證格式區分**：OpenClaw 使用兩種 token 格式：`sk-ant-api03-` 為直接 API key（推薦個人開發），`sk-ant-oat01-` 為訂閱型 setup token（用於團隊部署）。混用會導致 401 Unauthorized。
- **Rate Limit 踩坑**：即使 Anthropic Console 顯示「0 usage this month」，setup token 路徑仍可能觸發 429 `rate_limit_error`，原因是訂閱 token 請求路徑與一般 API 請求路徑分開計算。建議切換為直接 API key 並執行 `openclaw doctor --fix` 自動診斷。
- **Claude Sonnet 4.6 為主流首選**：大多數 agent 工作流（reasoning、tool use、長文處理）以 Sonnet 為主；Haiku 4.5 用於高量低延遲分類任務；Opus 保留給需要最強推理的場景。
- **來源**：[OpenClaw API Key Error](https://blog.laozhang.ai/en/posts/openclaw-api-key-error)、[OpenClaw Rate Limit 429](https://blog.laozhang.ai/en/posts/openclaw-rate-limit-exceeded-429)

### Gemini

- **Gemini 3.1 Pro 被評為 OpenClaw 最佳預設模型**：1M token context window、最低成本/job、免費開發 tier、原生多模態支援，適合大型程式碼 review 與 multimodal pipeline。
- **來源**：[Best LLM for OpenClaw: Gemini 3.1 Pro vs GPT-5.5 vs Claude Opus 4.7](https://dev.to/matthewrevell/best-llm-for-openclaw-gemini-31-pro-vs-gpt-55-vs-claude-opus-47-2026-3na4)

### GPT-5.5 / OpenAI

- **Realtime Talk sessions HTTP 500 未修復**（Issue #90456）：使用 OpenAI OAuth Realtime Talk 功能的用戶目前面臨 `/v1/realtime/calls` 500 錯誤，暫時建議改用 standard chat completion 路徑。
- **GPT-5.5 context window 128K**：相較 Gemini 3.1 Pro 的 1M，在長程 agent 工作流中有明顯劣勢。
- **來源**：[Issue #90456](https://github.com/openclaw/openclaw/issues/90456)

### 模型切換相容性

- **OpenAI Vertex AI、MiniMax M2.70、GLM-5.00 已新增支援**（2026.3 大版本）：切換時注意 system prompt 格式差異，部分模型對 tool use schema 的嚴格程度不同，建議在切換後執行 agent dry-run 確認 tool call 回傳格式。
- **`openclaw doctor --fix`**：可自動偵測 API key 格式、provider 設定錯誤、模型名稱錯誤，是切換 provider 後的標準排錯第一步。

---

## 3. 社群反應與討論亮點

- **Discord「Friends of the Crustacean 🦞🤝」頻道**：昨日討論集中在 rate limit 繞過修復確認，部分用戶報告升級 2026.6.8 後 429 錯誤頻率下降。
- **r/OpenClaw 與 r/Clawdbot**：Reddit 上持續有新用戶詢問 ClawHub 與 npm 的差異，社群管理員正在整理 FAQ 更新。
- **Substack 文章「The OpenClaw Saga」**：回顧兩週內 OpenClaw 如何從小眾 AI agent 框架變成星數突破 25 萬的現象級專案，社群熱度高。
- **Weekly Claw 定期活動**：Community Staff 持續主辦，內容涵蓋 skill 開發展示與 LLM 串接心得分享。
- **來源**：[The OpenClaw Saga - Substack](https://thesingularitypoint.substack.com/p/the-openclaw-saga-how-two-weeks-changed)

---

## 4. 值得追蹤的後續議題

| 議題 | 說明 | 追蹤連結 |
|------|------|---------|
| Issue #95131 | 昨日新開，需 live reproduction，性質未明 | [GitHub](https://github.com/openclaw/openclaw/issues/95131) |
| Issue #90456 | OpenAI OAuth Realtime Talk HTTP 500，尚未關閉 | [GitHub](https://github.com/openclaw/openclaw/issues/90456) |
| 2026.6.6 正式版 | beta.1 已修復 memory timeout 等問題，正式版預計近期發布 | [Releases](https://github.com/openclaw/openclaw/releases) |
| Anthropic Vertex AI 整合穩定性 | 新加入 provider，社群尚在測試 tool call 相容性 | [Docs](https://docs.openclaw.ai) |
| ClawHub 4000+ skills 品質把關 | Skills Hub 快速成長，社群呼籲建立 review 機制防止低品質 skill 上架 | [ClawHub](https://openclaw.com.au/updates) |

---

*本報告由自動化 routine 產出，資料來源為公開網路搜尋，不代表 OpenClaw 官方立場。*
