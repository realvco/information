# OpenClaw 每日情報 — 2026-05-24

> 搜尋範圍：過去 24 小時；資料截止時間 UTC 2026-05-24

---

## 1. 今日重點摘要

1. **v2026.5.20-beta.1 為目前最新 beta**：本週稍早發布，將語音追蹤（voice-following）、有界語音啟動上下文（bounded voice bootstrap context）、Policy 插件綁定、per-agent 本地模型精簡模式（lean mode）、xAI device-code OAuth、OpenRouter 路由策略、cron 交付、task-maintenance 等改動整合進 beta 串流。
2. **v2026.5.18 為目前最新 stable**：為 2026.5.12 後的穩定彙整版，包含 2026.5.14 及 2026.5.17 beta 的全部修正。此版本被視為近期最重要的穩定里程碑。
3. **CLI/Gateway 協議偏差修正**：v2026.5.20-beta 修正 CLI 與 Gateway 版本差一時 restart health check 失效的問題，並確保 `openclaw update` 不再因多 Node 安裝而靜默切換到錯誤的 gateway binary。
4. **記憶體與本地嵌入模型清理**：active-memory 搜尋逾時時，本地嵌入 provider 現在會正確關閉並釋放 pending 模型載入與嵌入上下文，避免記憶體洩漏。
5. **模型供應商執行期熱切換**（四月功能、持續討論中）：六個 provider 現可在不重新建置工作流程的情況下於執行期切換，涵蓋 GPT-5.5（Codex）、Claude API、Gemini、DeepSeek、OpenRouter、Ollama / LM Studio、Gemma 4。
6. **Android Talk Mode 遷移至 Gateway 中繼**：語音工作階段改由 realtime Gateway relay 處理，提升行動端穩定性；Discord / OpenAI realtime 的多輪追蹤聆聽同步修正。
7. **Telegram 隔離輪詢通道**：Telegram 輪詢與主題通道現獨立運行，解決媒體 / forum-topic 交付、`/stop`、`/btw` 指令及進度草稿的一系列可靠性問題。
8. **社群對測試前發布頻率表示不滿**：4 月底 v2026.4.26 更新引發 gateway 崩潰與 Discord 整合失效事件，連帶 4.24、4.25 同樣有類似問題，社群持續呼籲強化 QA 流程。
9. **「token 預算與請求限制」功能請求持續追蹤中**（Issue #58826）：OpenClaw 目前仍缺乏原生 per-request token 上限、per-session 用量限額或每日 cap 機制，為高頻用戶的主要痛點。

---

## 2. LLM 串接實戰回報

### 2-1 模型比較（OpenClaw 部署建議）

| 模型 | 上下文視窗 | 建議場景 | 來源 |
|------|-----------|---------|------|
| Gemini 3.1 Pro | 1M tokens | 大型程式碼審查、低成本、有免費開發層、原生多模態；**多數 OpenClaw 部署的預設首選** | [DEV Community](https://dev.to/matthewrevell/best-llm-for-openclaw-gemini-31-pro-vs-gpt-55-vs-claude-opus-47-2026-3na4) |
| GPT-5.5 | 128K（標準 API） | 自主型 Agent、Terminal-Bench 2.0 領先分數；上下文超過 128K 需付費升級 | [DEV Community](https://dev.to/matthewrevell/best-llm-for-openclaw-gemini-31-pro-vs-gpt-55-vs-claude-opus-47-2026-3na4) |
| Claude Opus 4.7 | 1M tokens | 嚴格程式碼審查、SWE-bench Pro 64.3%、指令遵循度高 | [DEV Community](https://dev.to/matthewrevell/best-llm-for-openclaw-gemini-31-pro-vs-gpt-55-vs-claude-opus-47-2026-3na4) |

### 2-2 已知踩坑紀錄

- **500/503 被誤判為 rate limit（Issue #32828）**：伺服器錯誤觸發全域冷卻，導致所有設定的模型同時進入等待。暫解：重啟 gateway 或切換至 OpenRouter provider pool。來源：[LaoZhang AI Blog](https://blog.laozhang.ai/en/posts/openclaw-rate-limit-exceeded-429)
- **token 用量顯示為「unknown」（Issue #39620）**：升級至 v2026.3.7 後 `session_status` 與 `openclaw sessions` 輸出中的 token 數回退為「unknown」，影響成本監控。暫解：降版至 v2026.3.2。來源：[GitHub Issue #39620](https://github.com/openclaw/openclaw/issues/39620)
- **大型工具輸出造成上下文指數膨脹**：`gateway config.schema` 輸出 396KB+ JSON 被永久存入工作階段 `.jsonl`，後續每條訊息都拖帶這份資料，導致成本爆炸。建議：手動清理或限制工具輸出大小。來源：[OpenClaw Token Usage Guide](https://www.getopenclaw.ai/help/token-usage-cost-management)
- **缺乏原生 token 預算機制（Issue #58826）**：目前社群以外部 proxy 或 OpenRouter 的用量上限來填補，非官方解法。來源：[GitHub Issue #58826](https://github.com/openclaw/openclaw/issues/58826)

---

## 3. 社群反應與討論亮點

- **連續版本品質事件（4 月底）**：v2026.4.24、4.25、4.26 三個版本接連在 gateway 崩潰與 Discord 整合上出問題，[Piunika Web](https://piunikaweb.com/2026/04/29/openclaw-update-triggering-gateway-crashes-issues/) 報導後社群迴響熱烈；多名用戶主動整理回退步驟與診斷修復流程分享至 Discord。
- **MindStudio 部落格深度解析**：[四月 provider manifest 文章](https://www.mindstudio.ai/blog/openclaw-april-2026-model-agnostic-provider-manifest) 獲得大量轉發，詳細說明六家 provider 的熱切換實作方式，被社群視為「實用教學的標杆」。
- **Medium 實戰串文熱門**：[@mohit15856 的 Medium 文章](https://medium.com/@mohit15856/i-wired-openclaw-to-claude-chatgpt-telegram-heres-what-actually-works-8278906edccc)「我把 OpenClaw 接到 Claude、ChatGPT 和 Telegram——哪些真的有效」持續獲得回覆，讀者在留言補充各自遇到的 auth token 失效與 Telegram bot 重啟問題。
- **Skywork.ai 社群整理**：[OpenClaw 2026 綜合指南](https://skywork.ai/skypage/en/openclaw-latest-updates-2026/2037437426655117312) 被多個討論串引用，涵蓋功能對比與替代方案，適合新用戶快速上手。

---

## 4. 值得追蹤的後續議題

| 議題 | 說明 | 追蹤連結 |
|------|------|---------|
| Issue #58826：token 預算與請求限制 | 官方原生限流機制何時落地，是當前最多人追蹤的 feature request | [GitHub](https://github.com/openclaw/openclaw/issues/58826) |
| v2026.5.20-beta 穩定化進度 | beta 串流中的 OpenRouter 路由策略與 xAI OAuth 何時進 stable | [GitHub Releases](https://github.com/openclaw/openclaw/releases) |
| Gateway crash QA 流程改進 | 社群持續施壓要求加強 pre-release 測試；官方是否會公開 QA roadmap | [Piunika Web 報導](https://piunikaweb.com/2026/04/29/openclaw-update-triggering-gateway-crashes-issues/) |
| Issue #39620：token 顯示 unknown 修正狀態 | 此 regression 是否已在 v2026.5.x 修復，官方尚未明確確認 | [GitHub Issue #39620](https://github.com/openclaw/openclaw/issues/39620) |
| Gemini native MCP 整合完整度 | Composio 的 [Gemini MCP + OpenClaw 指南](https://composio.dev/toolkits/gemini/framework/openclaw) 顯示整合仍依賴 workaround，原生支援進度值得持續關注 | [Composio](https://composio.dev/toolkits/gemini/framework/openclaw) |

---

*本報告由自動化流程依據網路公開資訊彙整，不代表任何官方立場。搜尋結果無法核實的技術細節已保守標注。*
