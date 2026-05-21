# Hermes-Agent 每日情報 — 2026-05-21

> 搜尋範圍：過去 24 小時（UTC 2026-05-20 ～ 2026-05-21）

---

## 1. 今日重點摘要

1. **v0.14.0「The Foundation Release」仍是最新穩定版**：2026-05-16 釋出，808 commits、633 merged PR、165,061 insertions、545 issues closed（含 12 P0、50 P1）。今日（2026-05-21）尚無新版本公告，但 GitHub issues 與 PR 仍持續有新活動。
2. **OpenAI-compatible 本地 Proxy 是本次最受矚目功能**：新的本地 proxy 可讓 Codex CLI、Aider、Continue 等工具直接借用 Claude Pro / ChatGPT Pro / SuperGrok 訂閱額度發請求，無需額外 API Key，大幅降低開發者成本。
3. **SuperGrok OAuth + grok-4.3（1M context）正式上線**：xAI 的 Grok 成為 Hermes-Agent 的 OAuth provider，context window 擴展至 1M tokens；同時 `x_search` 工具以 OAuth 或 API Key 兩種方式整合 X 平台搜尋能力。
4. **Microsoft Teams 全端支援**：Graph auth + webhook listener + pipeline runtime + 輸出交付一次到位，Teams 成為 Hermes-Agent 的第 21 個訊息平台；同版本亦加入 LINE 及 SimpleX Chat（無 user ID 的去中心化隱私通訊工具）。
5. **Kanban 多代理人看板（v0.13.0）持續獲用戶好評**：heartbeat 機制、zombie 偵測、per-task retry、hallucination recovery 等功能，讓多代理人長時間任務的穩定性顯著提升，是近期討論度最高的功能。
6. **GitHub Issues 顯示多項 Credential 管理 Bug 待修**：包括 fallback model 在 auth 失敗時不觸發（Issue #7230）、Codex OAuth 額度耗盡時無錯誤提示（Issue #1765）、「overloaded」伺服器錯誤被誤分類為 rate_limit（Issue #14038）等，均尚未 close。
7. **採用率持續高速增長**：2026-02-25 正式推出後七週達 95,600 GitHub stars，為 2026 年成長最快的開源 agent 框架；MarkTechPost（2026-05-10）報導其 OpenRouter 日 token 量（224B）已超越 OpenClaw（186B）。
8. **v0.15.0 路線圖訊號**：GitHub 開放 PR 顯示 spec-kit 初始化、MCP event loop 修復、gateway 訊息丟失防護、Claude Code provider bridge 復原等工作正在進行，預計下一版本聚焦穩定性。

---

## 2. LLM 串接實戰回報

### 2-1. Fallback Model 在 Auth 失敗時不觸發（Bug，開放中）

- **現象**：當主 provider 的 OAuth token 過期（如 openai-codex HTTP 401），即使 `config.yaml` 中設有 `fallback_model`，agent 仍直接對 Discord/Telegram 回傳固定 78 字元錯誤訊息，而非切換至備用模型。
- **影響範圍**：使用 Copilot / Codex OAuth 作為主 provider 的用戶，生產環境有無聲失敗風險。
- **暫時因應**：使用 Credential Pool 搭配多個同類型帳號，可在單帳號 auth 失效時自動輪換，但 fallback 至不同 provider 的功能仍待修復。
- **來源**：[Issue #7230](https://github.com/NousResearch/hermes-agent/issues/7230)

### 2-2. Codex OAuth 額度耗盡時無錯誤回饋（Bug，開放中）

- **現象**：當 OpenAI Codex OAuth 帳號用量耗盡，WebUI / Mac App 收不到任何錯誤提示，訊息看似送出卻無回應；`/usage` 指令無法正確顯示 Codex 帳號的額度（`_read_codex_tokens()` 只讀 legacy `auth.json` 的 `providers` 欄位，忽略 `credential_pool` 的新寫入路徑）。
- **來源**：[hermes-webui Issue #1765](https://github.com/nesquena/hermes-webui/issues/1765)、[Issue #15167](https://github.com/NousResearch/hermes-agent/issues/15167)

### 2-3. 「Server Overloaded」被誤判為 Rate Limit，造成憑證池快速耗盡

- **現象**：provider 回傳 HTTP 200 + `code: 1305`（temporarily overloaded）時，Hermes 將其分類為 `rate_limit` 並設 `should_rotate_credential=True`，導致 API key 在僅 2 次錯誤後即被標記為「已耗盡」，credential pool 快速消耗。
- **影響**：高峰期 Claude / Anthropic API 偶發的 overloaded 回應會無端消耗備用憑證，實際服務可用率下降。
- **來源**：[Issue #14038](https://github.com/NousResearch/hermes-agent/issues/14038)

### 2-4. 多組 OAuth Token 相互覆寫（Bug，開放中）

- **現象**：credential pool 中有多個手動新增的 `openai-codex` OAuth 帳號時，`_sync_codex_entry_from_cli()` 在其中一個帳號遇到 429 時，會以同一個 token 覆寫所有條目，導致備援憑證全數失效。
- **來源**：[Issue #11364](https://github.com/NousResearch/hermes-agent/issues/11364)

### 2-5. 各 LLM Provider 在 Hermes-Agent 的實戰評比

| Provider | 工具呼叫穩定性 | 適用場景 | 備注 |
|----------|------------|---------|------|
| **Claude Sonnet 4.6** | ★★★★★ | 生產首選、複雜推理 | 官方推薦最佳整體品質 |
| **GPT-4.1 (via Codex OAuth)** | ★★★★★ | 工具呼叫密集型任務 | 與 Claude 並列最佳 tool calling |
| **DeepSeek V4** | ★★★★☆ | 預算部署 | 低成本高 CP 值 |
| **Llama 4 Maverick (Ollama)** | ★★★☆☆ | 本地隱私需求 | 無需外部 API；效能略遜 |
| **grok-4.3 (SuperGrok OAuth)** | ★★★★☆ | 1M context 長文件處理 | 需 SuperGrok 訂閱 |

- **來源**：[Best AI Models for Hermes Agent 2026 — Remote OpenClaw](https://www.remoteopenclaw.com/blog/best-models-for-hermes-agent)、[Hermes Agent Docs — AI Providers](https://hermes-agent.nousresearch.com/docs/integrations/providers)

---

## 3. 社群反應與討論亮點

- **v0.14.0「Foundation Release」被社群稱為「Crazy Update」**：Daily.dev 的報導標題直接用「CRAZY Foundation Release: THIS UPDATE IS AWESOME」，反映社群對本次功能密度的高度興奮；OpenAI-compatible 本地 proxy 與 Teams 整合被票選為最受歡迎的兩項新功能。
  - [Daily.dev 報導](https://app.daily.dev/posts/hermes-agent-4-0-crazy-foundation-release-this-update-is-awesome--h0k1eeqg5)
- **Nous Research 官方 X 推文引爆討論**：`@NousResearch` 發布 v0.14.0 公告後，轉推與留言量明顯高於前幾版，尤其本地 proxy 功能「讓 Codex CLI 走你的 Claude Pro」這一訴求被廣泛截圖分享。
  - [Nous Research X 推文](https://x.com/NousResearch/status/2056110234309939330)
- **Teknium 隔空嗆聲 OpenClaw**：Nous Research 核心人物 Teknium 發文表示 Hermes-Agent 日 token 量近乎 2 倍於 OpenClaw，引發跨社群論戰；OpenClaw 社群反駁「兩者目標用戶不同」。
  - [Teknium X 推文](https://x.com/Teknium/status/2055125356554899865)
- **Kanban 多代理人看板使用心得持續湧現**：v0.13.0 的 Kanban 功能在 Discord 及 Reddit 引發大量實測分享，使用者反映 `/goal` 指令配合 zombie detection 讓長達數小時的多步驟任務不再中途失聯。
- **「自我改善 Curator」被部分用戶視為雙刃劍**：v0.12.0 的自主 Curator 背景執行技能整理功能受到技術用戶歡迎，但也有用戶反映「不清楚 Curator 刪了哪些 skill」，要求新增審計日誌（已在 GitHub 有相關 feature request）。

---

## 4. 值得追蹤的後續議題

| 議題 | 說明 |
|------|------|
| Issue #7230（fallback model 不觸發） | 生產環境高優先度 bug，修復時程未定 |
| Issue #14038（overloaded 誤判為 rate_limit） | 高峰期影響憑證池穩定性，等待 patch |
| Issue #11364（多 OAuth token 覆寫） | credential pool 多帳號策略的核心 bug |
| Issue #15167（/usage 指令讀取路徑錯誤） | 影響使用量監控，預計在下個版本修復 |
| v0.15.0 路線圖 | spec-kit init、MCP event loop、Claude Code provider bridge 為已知方向 |
| OpenRouter 日流量排行 | 持續觀察 Hermes-Agent 是否穩固 #1 地位，以及 OpenClaw 的反應策略 |
| Curator 審計日誌 feature request | 若合入則自我改善功能透明度大幅提升 |
| SimpleX Chat 整合成熟度 | v0.14.0 新加，使用者回饋仍在累積中 |

---

*報告生成時間：2026-05-21 UTC*
*資料來源索引*

- [Hermes Agent 官網](https://hermes-agent.nousresearch.com/)
- [GitHub NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent)
- [v0.14.0 Release Notes](https://github.com/NousResearch/hermes-agent/releases/tag/v2026.5.16)
- [v0.13.0 Release Notes](https://github.com/NousResearch/hermes-agent/releases/tag/v2026.5.7)
- [RELEASE_v0.14.0.md](https://github.com/NousResearch/hermes-agent/blob/main/RELEASE_v0.14.0.md)
- [Nous Research X 推文 v0.14.0](https://x.com/NousResearch/status/2056110234309939330)
- [Issue #7230 — fallback_model not triggered on auth fail](https://github.com/NousResearch/hermes-agent/issues/7230)
- [hermes-webui Issue #1765 — Codex OAuth quota no error](https://github.com/nesquena/hermes-webui/issues/1765)
- [Issue #14038 — overloaded classified as rate_limit](https://github.com/NousResearch/hermes-agent/issues/14038)
- [Issue #11364 — multiple OAuth tokens overwrite](https://github.com/NousResearch/hermes-agent/issues/11364)
- [Issue #15167 — /usage ignores credential_pool](https://github.com/NousResearch/hermes-agent/issues/15167)
- [Best AI Models for Hermes Agent 2026 — Remote OpenClaw](https://www.remoteopenclaw.com/blog/best-models-for-hermes-agent)
- [AI Providers Docs](https://hermes-agent.nousresearch.com/docs/integrations/providers)
- [OpenClaw vs Hermes-Agent — MarkTechPost](https://www.marktechpost.com/2026/05/10/openclaw-vs-hermes-agent-why-nous-researchs-self-improving-agent-now-leads-openrouters-global-rankings/)
- [Daily.dev — Foundation Release 報導](https://app.daily.dev/posts/hermes-agent-4-0-crazy-foundation-release-this-update-is-awesome--h0k1eeqg5)
- [Teknium X 推文](https://x.com/Teknium/status/2055125356554899865)
- [Hermes Agent Review 2026 — TokenMix](https://tokenmix.ai/blog/hermes-agent-review-self-improving-open-source-2026)
