# OpenClaw 每日情報 — 2026-07-06

## 今日重點摘要

1. **v2026.7.1-beta.2 釋出（7月5日）**：帶入 GPT-5.6 model family 完整支援，覆蓋 catalog、capability 及 runtime 選擇路徑（PR #98333），為本週最大更新。
2. **openclaw attach 新工作流程**：支援針對現有 Gateway session 啟動外部 harness，讓互動式 Codex 風格工作流更容易暫停、重啟與檢查。
3. **Telegram Codex 整合強化**：可用 `/login` 啟動 Codex pairing、引導進行中的 Codex 執行、跨 transient API 故障復原最終回覆。
4. **iOS App 全面翻新**：採用 iOS 26 視覺系統，優化 Chat、Talk、onboarding 及重連流程；Apple/Android 多語系本地化同步擴展。
5. **Fast Mode 正式加入**：短對話自動啟動快速模式，長或 fallback 工作回歸正常模式，不丟失可見狀態。
6. **Cron 排程新增「on-exit」種類**：監控指定指令退出後喚醒 Agent，session-targeted runs 支援 clean detach；同步加入 in-flight job doctor 警告機制與完整 usage footer。
7. **iMessage poll 訊息支援**：豐富訊息功能擴充，加入輪詢類型。
8. **既有 Auth/Rate Limit 問題持續追蹤中**：Claude OAuth 搭配 OpenClaw 仍有用戶反映「API rate limit reached」即使帳號無實際用量；Discord 整合（v2026.4.29 regression，issue #75341）在 gateway 啟動時出現 fetch-timeout 與 HTTP 429 錯誤，`openclaw doctor --fix` 可部分緩解但尚未根治。

---

## LLM 串接實戰回報

### Claude（Anthropic）

- Claude Sonnet 4.6 在 PinchBench OpenClaw benchmark 以 **86.9% 成功率**領先所有 provider，MCPAgentBench 得分 71.6，為 MCP-based 評測最高分。
- 用戶反映工具呼叫可靠性與長上下文連貫性優於其他 provider，企業安全稽核通過率高。
- **OAuth 踩坑**：部分用戶即便在新方案零用量情況下仍持續收到 rate limit 錯誤，論壇推測是 gateway 層 token refresh 機制的快取設計問題，官方尚未正式回應根本原因。（[answeroverflow 討論](https://www.answeroverflow.com/m/1478788645472436264)）
- **建議配置**：Sonnet 做主力 agent work，Haiku 做高頻量分類／路由，Opus 用於最難推理場景。（[DEV Community 評測](https://dev.to/matthewrevell/best-llm-for-openclaw-gemini-31-pro-vs-gpt-55-vs-claude-opus-47-2026-3na4)）

### Gemini（Google）

- Gemini 3.1 Pro 被定位為「多數 OpenClaw 部署的預設推薦」，優勢：大 context 程式碼審閱、每次作業成本最低、免費開發層（60 req/min、1,000 req/day）、原生多模態支援。
- Composio 提供穩定的 Gemini MCP 橋接整合，設定門檻低。（[Composio 整合文件](https://composio.dev/toolkits/gemini/framework/openclaw)）

### GPT-5.6（OpenAI）

- 本週新版本正式加入 GPT-5.6 catalog 支援，為最新整合，社群大規模實戰回饋尚待累積。
- GPT-5.5 在 Terminal-Bench 2.0 自主 agent 評測仍居前列，但 context 超出 128K 時效能下降明顯，需留意長任務場景。
- 舊問題：v2026.5.4→v2026.5.5 升級後 `openclaw doctor --fix` 曾誤改 `openai-codex/*` model ref 為 `openai/*`，鎖定 ChatGPT-OAuth 用戶（issue #78407），建議升級前備份設定檔。（[Issue #78407](https://github.com/openclaw/openclaw/issues/78407)）

### 多模型路由策略

- 業界實戰建議：DeepSeek 或 Gemini Flash 處理簡單查詢，Claude/GPT 處理複雜多步驟任務，OpenClaw 的 multi-model 支援使切換成本極低。（[cowork.ink 評測](https://cowork.ink/blog/best-model-for-openclaw/)）
- OpenRouter 等 200+ 模型可透過 OpenAI-compatible 接口接入，彈性極高。（[官方 providers 文件](https://docs.openclaw.ai/concepts/model-providers)）

---

## 社群反應與討論亮點

- **Discord（issue #75341）**：v2026.4.29 regression 仍開放，startup 時 OAuth @me fetch-timeout + native slash command deploy PATCH 觸發 429，影響多個用戶正常使用 Discord 頻道，社群期待官方盡快修復。（[Issue #75341](https://github.com/openclaw/openclaw/issues/75341)）
- **媒體報導**：有媒體報導 OpenClaw 在五天內連續發布更新、24小時內兩次更新，顯示開發迭代節奏極快，但也引發部分用戶對穩定性的擔憂。（[36Kr 報導](https://eu.36kr.com/en/p/3763230073815814)）
- **社群整體定性**：OpenClaw 被廣泛認為是 2026 年最受矚目的開源 agent 專案之一，讓普通用戶也能使用過去需要大量工程資源才能建立的本地優先自動化架構。（[Petronella Cybersecurity](https://petronellatech.com/blog/openclaw-ai-agent-guide-2026/)）
- **PinchBench 排名熱議**：Claude Sonnet 4.6 以 86.9% 成功率登頂，引發各 LLM 支持者在論壇交叉比對自身測試結果，討論 benchmark 設計是否偏向 MCP-heavy 場景。

---

## 值得追蹤的後續議題

1. **GPT-5.6 實戰穩定性**：新 catalog 剛上線，等待社群回報 token 計費行為、rate limit 上限及模型切換相容性問題。
2. **Discord v2026.4.29 regression（issue #75341）**：startup 429 問題尚未 close，需關注官方修復 ETA。
3. **Claude OAuth rate limit 根本原因**：多位用戶確認零用量觸發 rate limit，可能為 gateway 快取設計問題，等待官方根本解法。
4. **openclaw attach 工作流實際使用心得**：新功能剛發布，值得追蹤用戶在複雜 Codex 場景的實際操作回饋與已知限制。
5. **Fast Mode 對 LLM 費用的影響**：自動模式切換是否會在用戶不知情時切換至更貴的模型，社群對成本透明度仍有疑慮。
