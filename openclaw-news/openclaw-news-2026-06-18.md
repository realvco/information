# OpenClaw 每日情報 — 2026-06-18

> 資料蒐集時間：2026-06-18 UTC｜搜尋範圍：過去 24 小時及近期重大更新

---

## 1. 今日重點摘要

1. **最新版本 2026.6.8**：繼 2026.6.6 安全強化版之後，2026.6.8 帶來更豐富的 Telegram/WhatsApp 訊息傳遞，包含結構化文字（表格、清單、可展開引用）渲染，以及更穩定的 agent 執行路徑（共涵蓋 221 個合併 PR）。

2. **安全邊界全面收緊（2026.6.6）**：transcript、sandbox、MCP stdio、browser、channel 與 exec-approval 路徑均改為在不安全存取、超時核准或格式錯誤的邊界輸入時「預設拒絕（fail closed）」，相依套件升級至 Hono 4.12.25 修補已知漏洞。

3. **預設模型遷移爭議（Issue #21897）**：社群正熱烈討論是否將預設 LLM 從 `anthropic/claude-opus-4-6` 切換為 `google/gemini-3.1-pro`。爭議點包括：Gemini 3.1 Pro 截至 2026 年 2 月仍未出現在 Google 公開模型列表、系統提示格式與 tool-calling 格式均需同步調整，以及視訊理解能力可能成為原生對話功能等潛在優勢。

4. **GPT-5.4 via Codex OAuth 已知 Bug（Issue #38706）**：使用 ChatGPT Plus / Codex OAuth 的用戶若期望調用 GPT-5.4，OpenClaw 實際上會呼叫 `/v1/responses`（需要 `api.responses.write` scope），但 OAuth token 不含該 scope，導致 401 錯誤並回退至 gpt-5.3-codex。官方建議明確指定 `openai-codex/gpt-5.3-codex`。

5. **PinchBench 排行榜**：Claude Sonnet 4.6 以 86.9% 成功率居 OpenClaw 官方 benchmark 首位，GPT-5.4 以 86.0% 緊隨其後，Gemini 3.1 Pro 在多模態與長 context 場景表現突出。

6. **iMessage 持久性改善**：2026.6.8 為 iMessage 加入 always-on inbound recovery、durable echo markers、block streaming，以及可在重啟與閒置後存活的 idle approval discovery。

7. **Rate Limit Cooldown 機制說明**：官方文件明確指出，當 Claude 或 GPT 收到 429 回應時，OpenClaw 會啟動 cooldown 並稍後重試，但不會自動切換 provider（除非設定 fallback chain）；持續發送請求期間 cooldown 會疊加，極端情況可達 5000+ 分鐘。

8. **GPT-5 家族與 Codex provider**：2026.4.10 引入原生 Codex provider 及對應 OAuth 認證；2026.4.14 加入 GPT-5.4-pro 前向相容支援，OpenClaw 的版本節奏在 Q1–Q2 維持每週多次發布。

9. **Hermes / OpenClaw 分辨提醒**：網路上少數文章將 OpenClaw 與 Hermes-Agent 混為一談。兩者為獨立專案：OpenClaw（`openclaw/openclaw`，總部澳洲）聚焦多平台訊息頻道 + agent；Hermes-Agent（`NousResearch/hermes-agent`）為 Nous Research 的自我成長型 agent。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）

- **效能**：Claude Sonnet 4.6 在 PinchBench 以 86.9% 居冠，tool-calling 可靠性最高、長 context 連貫度佳，且對 MCP 原生支援，通過企業安全審核較容易。
- **串接設定**：provider 設為 `anthropic`，model 設為 `claude-sonnet-4-6` 或 `claude-opus-4-6`；需注意 `baseUrl` 預設不需設定，自訂 proxy 才需覆寫。
- **已知問題**：目前無 Claude 專屬重大 Bug 回報；預設模型遷移討論若通過，Claude Opus 4.6 將退出預設位置。
- 來源：[Best LLM for OpenClaw: Gemini 3.1 Pro vs GPT-5.5 vs Claude Opus 4.7 (2026) - DEV Community](https://dev.to/matthewrevell/best-llm-for-openclaw-gemini-31-pro-vs-gpt-55-vs-claude-opus-47-2026-3na4)

### GPT / OpenAI Codex

- **GPT-5.4 OAuth Bug**：如上述 Issue #38706，ChatGPT Plus 訂閱者使用 Codex OAuth 時，GPT-5.4 請求被送至錯誤的 `/v1/responses` endpoint，返回 401 並 fallback 至 gpt-5.3-codex。官方建議明確指定版本。
- **Rate Limit 疊加**：OAuth token 的 rate limit 通常低於直接 API key；cooldown 疊加問題已被多名使用者回報，建議設定 fallback provider chain。
- 來源：[Bug: GPT-5.4 via openai-codex OAuth uses wrong API · Issue #38706 · openclaw/openclaw](https://github.com/openclaw/openclaw/issues/38706)、[OpenClaw API Rate Limit Reached — Fix & Prevention Guide](https://www.getopenclaw.ai/help/rate-limits-quota-management)

### Gemini（Google）

- **預設模型候選**：Issue #21897 提議將 Gemini 3.1 Pro 設為預設，理由包括多模態（含視訊）與長 context 優勢。
- **現有串接**：需設定 Google provider，搭配 API key 或 Google AI Studio 認證；tool-calling 格式與 Anthropic 不同，若直接遷移需重新測試 system prompt。
- 來源：[feat(models): migrate default model from Claude Opus 4.6 to Gemini 3.1 Pro · Issue #21897 · openclaw/openclaw](https://github.com/openclaw/openclaw/issues/21897)、[Google (Gemini) · OpenClaw](https://docs.openclaw.ai/providers/google)

### OpenRouter

- OpenRouter 作為多 provider 代理已有官方整合文件，適合需要快速切換模型或測試不同 provider 的使用者。
- 來源：[Integration with OpenClaw | OpenRouter Documentation](https://openrouter.ai/docs/guides/guides/openclaw-integration)

---

## 3. 社群反應與討論亮點

- **Issue #21897 持續發酵**：預設模型遷移 PR 吸引大量社群留言，主要分為「Gemini 多模態優勢論」與「Claude tool-calling 穩定性優先論」兩派；技術層面的爭議（system prompt 格式相容性、Gemini 3.1 Pro 是否公開可用）尚未有定論。

- **Discord / r/OpenClaw 社群架構**：OpenClaw 設有專職社群志工團隊，管理 r/OpenClaw、r/Clawdbot 及 Discord，並定期舉辦 Weekly Claw 活動；support channel（`#help`、`#users-helping-users`）為主要技術求助場所。

- **Codex OAuth 用戶抱怨**：在 Discord 與 GitHub 均有 ChatGPT Plus 訂閱者反映「以為在用 GPT-5.4 但實際上跑的是 5.3-codex」的困惑，Bug #38706 已獲官方確認但修復時程未定。

- **安全邊界升級獲好評**：fail-closed 設計被部分企業用戶稱為「終於達到內部 security review 門檻」，但也有開發者指出測試期間需重新測試所有核准流程。

---

## 4. 值得追蹤的後續議題

1. **Issue #21897 進展**：預設模型從 Claude Opus 4.6 遷移至 Gemini 3.1 Pro 的決策走向，將影響所有未明確設定 model 的 OpenClaw 部署，建議持續追蹤。

2. **Issue #38706 修復時程**：GPT-5.4 via Codex OAuth 的 API endpoint 路由問題尚無修復 ETA，使用 ChatGPT Plus 訂閱的用戶須注意。

3. **2026.7.x 版本方向**：官方 RELEASING 文件顯示版本節奏仍維持每週多次；預計 Q3 初會有 iMessage 與 Teams 頻道的進一步強化。

4. **PinchBench 新一輪評測**：隨 GPT-5.4 修復與 Gemini 3.1 Pro 候選，社群預期會有新一輪 benchmark 對比，結果可能影響 Issue #21897 的最終決策。

5. **OpenClaw Codex provider 文件完善度**：LumaDock 等第三方教學指出官方 Codex OAuth 設定文件仍不完整，社群正補充 wiki。

---

*本報告依據公開搜尋結果整理，過去 24 小時內未發現全新重大版本發布，主要動態集中於 2026.6.8 的追蹤討論與 Issue #21897 爭議持續進行中。*
