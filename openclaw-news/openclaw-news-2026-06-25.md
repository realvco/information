# OpenClaw 每日情報 — 2026-06-25

## 今日重點摘要

1. **穩定版升至 2026.6.10**：OpenClaw 於 6 月 24 日發布 2026.6.10，前一版為 2026.6.9；npm latest 仍標記 2026.6.1 為穩定參考版，GitHub 上 prerelease 已推進至 v2026.6.5-beta.2，顯示主動的 beta 強化周期正在進行。
2. **Fast Mode 正式轉穩定**：原本在 beta 的 Fast Mode 對話功能正式進入穩定線，短對話回合可自動進入快速模式，長工作回合自動還原，改善 channel-driven agent 的使用者體驗。
3. **Telegram 傳送增強**：Telegram 傳遞現支援富文字 HTML，改善 markdown 保留、貼紙路徑渲染、進度草稿與指令輸出顯示，並能安全地正規化 HTML 表格。
4. **模型路由更穩健**：供應商路由優化，包含 Zai 模型合成收緊、Zhipu GLM 過載自動切換、以及從執行期型錄直接選取原生推論層級，提升 model catalog 的準確性與安全性。
5. **Anthropic OAuth 限制政策（2026-04-04 生效）**：Anthropic 正式禁止在第三方工具使用 Claude Pro/Max 訂閱的 OAuth Token；OpenClaw 用戶現只能透過「Extra Usage（按量計費）」或獨立 API Key 使用 Claude 模型，訂閱 OAuth 僅限 Claude.ai、Claude Code、Claude Desktop 等官方產品。
6. **Agent 恢復可靠性提升**：新增重試機制、終止結果處理、壓縮後使用量追蹤、session 歷程修復、回應重整，減少因中斷或部分執行造成的卡頓。
7. **代幣費用失控問題持續被社群關注**：多名用戶反映 OpenClaw 預設無 rate limit、無 token 預算上限，一個創辦人案例顯示 agent 在一夜 retry loop 中燒掉 $47 API Credits；Gemini 2.5 Pro 架構在幾十次 API 呼叫內就可消耗 190 萬+ input tokens。
8. **GitHub Issue #58826 提案**：社群提出「在 OpenClaw 內建 token budget 與 request limit 控制」，目前仍屬功能請求階段。
9. **DEV Community 發布 LLM 比較評測**：《Best LLM for OpenClaw: Gemini 3.1 Pro vs GPT-5.5 vs Claude Opus 4.7 (2026)》成為本週最多人轉傳的技術文章之一。

---

## LLM 串接實戰回報

### Anthropic Claude
- **OAuth Token 禁用（2026-04-04）**：訂閱型 OAuth Token 已被 Anthropic 封鎖於第三方工具；必須改用 Extra Usage 或獨立 API Key。若繼續使用舊 Token 會收到「LLM request rejected: You're out of extra usage」錯誤。
  - 來源：[4 solutions to resolve the OpenClaw error LLM request rejected](https://help.apiyi.com/en/openclaw-claude-llm-request-rejected-extra-usage-fix-en.html)
- **必須使用 `anthropic-messages` 格式**：以 `openai-completions` 格式呼叫 Claude 會在多輪 tool call 時觸發 400 錯誤；正確設定為 `api: "anthropic-messages"`。常見錯誤還包含：baseUrl 漏填 `/v1`、未停用 `anthropic-beta` 與 `reasoning`、未清除 session cache。
  - 來源：[5-Step Complete Configuration for OpenClaw Claude API Integration](https://help.apiyi.com/en/openclaw-claude-api-apiyi-anthropic-messages-guide-en.html)

### OpenAI GPT
- **GPT-5.4 主動性不足**：部分用戶反映 GPT-5.4 在遭遇新問題時不願主動解決，需要人工大量介入；建議搭配更明確的 system prompt 引導行為。

### Google Gemini
- **Gemini 2.5 Pro Token 消耗極高**：幾十次 API 呼叫即可觸發 190 萬+ input tokens；開啟 thinking/reasoning 模式更會大幅增加每次請求的 token 消耗；大型 tool output 若永久存入 session file，每次後續訊息都會帶入全部歷程，導致指數級成長。
  - 來源：[OpenClaw Rate Limit Exceeded 429](https://blog.laozhang.ai/en/posts/openclaw-rate-limit-exceeded-429)

### Kimi-K2.5
- 部分用戶反映模型速度偏慢，且在簡單任務上消耗超出必要的思考 token。

### 通用建議
- 使用 Anthropic 或 OpenAI 各自提供的**硬性費用上限**作為第一道防線，避免 agent retry loop 造成費用爆炸。
- 來源：[OpenClaw Token Usage & Cost Control Guide (2026)](https://www.getopenclaw.ai/help/token-usage-cost-management)

---

## 社群反應與討論亮點

- **r/openclaw & r/Clawdbot**：官方 Discord 與 Reddit 子版均有社群志工維護，熱門討論集中於整合設定（「比想像中順很多」）以及費用比較（Claude Max 訂閱 vs 純 API，低至中流量前者更划算）。
  - 來源：[OpenClaw Reddit: What Actual Users Say in 2026](https://clawmage.ai/blog/openclaw-reddit/)
- **Medium 實測文章**《I Wired OpenClaw to Claude, ChatGPT & Telegram — Here's What Actually Works》受到廣泛轉傳，作者詳述三個平台串接的實際踩坑紀錄。
  - 來源：[Medium — Mohit Aggarwal](https://medium.com/@mohit15856/i-wired-openclaw-to-claude-chatgpt-telegram-heres-what-actually-works-8278906edccc)
- **Towards Data Science**：發布《How to Run OpenClaw with Open-Source Models》，討論搭配本地模型使用的可行性，吸引重視隱私的用戶族群。
  - 來源：[Towards Data Science](https://towardsdatascience.com/how-to-run-openclaw-with-open-source-models/)
- **DEV Community**：LLM 比較評測文章指出 Gemini 3.1 Pro、GPT-5.5、Claude Opus 4.7 各有優劣，尚無壓倒性勝者。
  - 來源：[DEV Community](https://dev.to/matthewrevell/best-llm-for-openclaw-gemini-31-pro-vs-gpt-55-vs-claude-opus-47-2026-3na4)

---

## 值得追蹤的後續議題

- **GitHub Issue #58826**：內建 token budget 與 request limit 功能進度，若合入將大幅降低費用失控風險。
  - 來源：[Issue #58826 · openclaw/openclaw](https://github.com/openclaw/openclaw/issues/58826)
- **Claude Max 訂閱整合後續**：Anthropic 於 4 月執行的 OAuth 封鎖政策是否出現例外或替代方案，社群持續關注。
- **v2026.6.5-beta.2 轉穩定時程**：本次 beta 強化周期結束後的下一個穩定版有哪些額外修復，值得追蹤 [Releases 頁面](https://github.com/openclaw/openclaw/releases)。
- **OpenClaw-RL 子專案**：[Gen-Verse/OpenClaw-RL](https://github.com/Gen-Verse/OpenClaw-RL) 主打「只需說話即可訓練任何 agent」，是否正式整合進主線尚待觀察。
- **goldmar/openclaw-code-agent**：社群衍生的程式碼 agent 實作，值得關注功能成熟度。
  - 來源：[GitHub - goldmar/openclaw-code-agent](https://github.com/goldmar/openclaw-code-agent)
