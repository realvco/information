# OpenClaw 每日情報 — 2026-05-23

> 搜尋範圍：過去 24 小時（UTC 基準）｜資料截止：2026-05-23

---

## 1. 今日重點摘要

1. **Issue #85559（P1 高優先）今日開啟**：由 Jimmy-j-tcg 回報，頻道訊息可能遺失、重複或錯誤路由，影響 session、memory、transcript、context 及 agent state 等核心機制，已標記為高優先用戶端 bug。（[來源](https://github.com/openclaw/openclaw/issues)）
2. **同日另有 #85557 與 #85552 開啟**：兩則新 issue 均於 2026-05-23 提交，細節尚待釐清，可能與近期 2026.5.18 穩定版更新有關。
3. **2026.5.18 為目前最新穩定版**：整合 2026.5.14 與 2026.5.17 beta 修正，主要改善：Control UI 效能、Android Talk Mode 切換至 Gateway relay 即時語音、Telegram/Discord 傳訊可靠性、Codex/OpenAI runtime 上下文投影。（[發行紀錄](https://github.com/openclaw/openclaw/releases)）
4. **Codex 動態工具呼叫卡死 bug（#83474）仍未關閉**：動態 tool call 在特定條件下會使 session 卡住，涉及 MCP projection 與 native tool progress 問題。
5. **記憶體/搜尋修復（#83858）**：在本地嵌入式模型搜尋超時時，正確關閉 embedding context，防止資源洩漏。
6. **Anthropic 封鎖第三方 OAuth（4 月 4 日起）持續影響**：訂閱 OAuth token 已被限制只能用於 Claude.ai、Claude Code、Claude Desktop 等官方產品；OpenClaw 必須改用「Extra Usage（用量付費）」或獨立 API Key，此政策改變持續在社群引發討論。（[來源](https://help.apiyi.com/en/openclaw-claude-llm-request-rejected-extra-usage-fix-en.html)）
7. **虛假 rate limit 錯誤（Issue #32828）仍是熱門回報**：所有已設定模型均觸發「API rate limit reached」警告，但在 OpenClaw 外部測試 API 完全正常，懷疑為 OpenClaw 內部計數器邏輯問題。
8. **6 大模型提供商支援執行時切換**：4 月更新後，可在不重建工作流程的情況下動態切換 GPT-5.5（Codex）、Claude API、Gemini、DeepSeek、OpenRouter、Ollama/LM Studio、Gemma 4，引發社群對多模型路由策略的熱烈討論。
9. **TeamoRouter 社群 skill 受到關注**：自動將檔案操作與簡易查詢分流至 DeepSeek 或 Gemini，保留 Claude Sonnet 處理需要推理的任務，作為節省費用的主流解法。

---

## 2. LLM 串接實戰回報

### Claude API / Subscription OAuth 問題

- **OAuth 失效（2026-04-04 起）**：Anthropic 正式封鎖第三方工具使用訂閱 OAuth，大量使用者在更新後發現 Claude 整合突然失效。解法：改用 Extra Usage（credit 計費）或另開獨立 API Key。（[詳細說明](https://help.apiyi.com/en/openclaw-claude-llm-request-rejected-extra-usage-fix-en.html)）
- **費用異常**：切換至 Extra Usage 後，部分使用者反映費用高於預期；建議搭配 TeamoRouter 降低 Claude 的呼叫比例。（[討論](https://www.shareuhack.com/en/posts/openclaw-claude-code-oauth-cost)）
- **虛假 rate limit（Issue #32828）**：即使 API 實際可用，OpenClaw 仍顯示 429 錯誤並進入冷卻。社群暫時解法是重啟 OpenClaw 或手動清除 provider 冷卻狀態。（[Issue](https://github.com/openclaw/openclaw/issues/32828)）

### DeepSeek / Gemini / OpenRouter 替代方案

- **OpenRouter 整合成熟**：直接設定 API Key，使用 `openrouter/<author>/<slug>` 格式即可引用 200+ 模型，前綴 `~` 可追蹤最新版本（如 `openrouter/~anthropic/claude-sonnet-latest`）。（[OpenRouter 官方文件](https://openrouter.ai/docs/guides/guides/openclaw-integration)）
- **DeepSeek 低成本方案盛行**：適合大量簡易任務，搭配 TeamoRouter 可節省 50-60% 成本，但需注意 DeepSeek 的上下文視窗限制在複雜 agent 工作流中可能不足。
- **Gemini 多模態應用**：適合圖片與文件處理場景，在 OpenRouter 上的使用量排名持續上升。

---

## 3. 社群反應與討論亮點

- **2026.4.26 更新事件後遺症仍存**：該版本更新引發大規模 gateway 崩潰與 Discord/Telegram 傳訊中斷，部分長期使用者表示已將主力切換至 Hermes Agent，OpenClaw 降為備用。（[報導](https://piunikaweb.com/2026/04/29/openclaw-update-triggering-gateway-crashes-issues/)）
- **「缺乏穩定發行通道」**是社群批評焦點：使用者希望能有 LTS 或穩定頻道，避免每次更新帶來不確定的穩定性風險。
- **多模型路由成為主流話題**：隨著 Anthropic OAuth 政策收緊，如何平衡各家 LLM 的費用、可靠性與功能，成為 r/openclaw 及相關 Discord 伺服器最活躍的討論主題。
- **CLI JSON-mode 改善獲好評**：將 lazy plugin-registration log 導向 stderr，讓 stdout 保持可解析的純 JSON 輸出，開發者反映這大幅改善了腳本整合體驗。

---

## 4. 值得追蹤的後續議題

| 議題 | 說明 | 連結 |
|------|------|------|
| Issue #85559 | P1 頻道訊息遺失/重複/誤路由，是否會觸發緊急修補版本 | [GitHub](https://github.com/openclaw/openclaw/issues) |
| Issue #83474 | Codex 動態 tool call 卡死，影響複雜 agent 工作流 | [GitHub](https://github.com/openclaw/openclaw/issues/83474) |
| Issue #32828 | 虛假 rate limit 根本原因是否已找到修法 | [GitHub](https://github.com/openclaw/openclaw/issues/32828) |
| OAuth 政策後續 | Anthropic 是否有計劃為授權第三方工具提供官方合作方案 | [Feature Request #16365](https://github.com/openclaw/openclaw/issues/16365) |
| 穩定發行通道 | 社群持續推動 LTS 頻道提案，是否會在 roadmap 中出現 | [OpenClaw Issues](https://github.com/openclaw/openclaw/issues) |
| OpenClaw-RL | Gen-Verse 的 OpenClaw-RL（透過對話訓練 agent）專案進展 | [GitHub](https://github.com/Gen-Verse/OpenClaw-RL) |

---

*本報告依搜尋結果整理，未加入推測或未經證實的資訊。*
*來源：[GitHub openclaw/openclaw](https://github.com/openclaw/openclaw) ｜ [Releases](https://github.com/openclaw/openclaw/releases) ｜ [OpenRouter Docs](https://openrouter.ai/docs/guides/guides/openclaw-integration) ｜ [piunikaweb](https://piunikaweb.com/2026/04/29/openclaw-update-triggering-gateway-crashes-issues/)*
