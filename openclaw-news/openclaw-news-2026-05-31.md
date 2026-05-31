# OpenClaw 每日情報 — 2026-05-31

> 資料涵蓋範圍：過去 24 小時（UTC 2026-05-30 ～ 2026-05-31）

---

## 1. 今日重點摘要

1. **最新版本為 v2026.5.29-alpha.1**：截至本報告發布時，OpenClaw GitHub 上最新的 alpha 版本為 2026.5.29-alpha.1，beta 通道則為 2026.5.28-beta.3。過去 24 小時未見新的 stable 正式版發布，但 alpha/beta 更新持續進行中。
2. **v2026.5.27 穩定版奠定 Gateway 基礎**：5/27 穩定版著重 Gateway 啟動優化，避免重複掃描 plugin、channel、session 與 filesystem；transcript 機制正式成為平台核心，驅動會議摘要、source-provider chunk 等多項功能。
3. **Beta 版加強 Codex 與 subagent 穩定性**：v2026.5.28-beta.3 修復 subagent cwd/workspace 隔離、hook context 限於 prompt 本地、session lock 超時後正確釋放，以及 Codex app-server 崩潰不再拖垮 shared runtime state。
4. **安全邊界強化**：beta 通道新版本實作 group prompt text 排除於 system prompt 之外、重複點符號 hostname 正規化、command wrapper 封鎖，強化 channel 安全邊界。
5. **發布節奏極密集**：OpenClaw 平均每兩天橫跨三個通道（stable / beta / alpha）發布一版，為目前最活躍的開源 agent 框架之一。
6. **模型切換不需重建（April 功能仍熱議）**：4 月推出的 provider manifest 讓使用者在執行中的 workflow 動態換模型，六家 provider 開箱即用（GPT-5.5 via Codex、Claude API、Gemini、DeepSeek、OpenRouter、Ollama/LM Studio/Gemma 4），社群討論持續熱絡。
7. **OpenRouter 整合文件更新**：OpenRouter 官方文件加入 OpenClaw 整合指南，降低多 provider 切換門檻。

---

## 2. LLM 串接實戰回報

### 2-1. Claude OAuth (setup-token) 限速問題
- **現象**：使用 `mode: token`（claude.ai OAuth）的用戶，即使瀏覽器端 Claude.ai 帳號正常，仍頻繁收到 `API rate limit reached`（HTTP 429）。
- **根因**：Anthropic Console 顯示「0 usage this month」不代表沒有觸發限速，OAuth subscription 的配額計算方式與 Console API key 分開。
- **社群建議**：切換至付費 Anthropic API key（`mode: api_key`）是目前最可靠的生產路徑。
- **來源**：[AnswerOverflow — OpenClaw claude oauth rate limit](https://www.answeroverflow.com/m/1478788645472436264)

### 2-2. 假冷卻（False Cooldown）Bug
- **影響版本**：v2026.2.21-2（已知，較舊）
- **現象**：即使沒有實際觸發 API rate limit，OpenClaw 仍把所有已設定的 provider 同時放入 cooldown，hardcoded reason 為 `"rate_limit"`。
- **緩解**：降版至 v2026.2.12 可立即解決，官方已追蹤此 bug（[Issue #23996](https://github.com/openclaw/openclaw/issues/23996)）。
- **來源**：[GitHub Issue #23996 — False provider cooldown](https://github.com/openclaw/openclaw/issues/23996)

### 2-3. 指數退避（Exponential Backoff）未如文件運作
- **現象**：文件描述 provider 遇到 429 時應有指數退避，但實測重試間隔遠短於文件標示的分鐘級遞增，效果有限。
- **影響**：高頻 agent 任務易反覆撞限速，建議在 `models.providers` 設定手動 cooldown 時間。
- **來源**：[LaoZhang AI Blog — OpenClaw Rate Limit 429 Fix](https://blog.laozhang.ai/en/posts/openclaw-rate-limit-exceeded-429)

### 2-4. Gemini 3.1 Pro 成本最低、最受推薦
- **社群評比**（Gemini 3.1 Pro vs GPT-5.5 vs Claude Opus 4.7）：Gemini 3.1 Pro 以大 context 程式碼審查、最低每任務成本、有免費開發者額度、原生多模態優勢，成為多數 OpenClaw 工作負載的首選預設模型。
- **來源**：[DEV.to — Best LLM for OpenClaw 2026](https://dev.to/matthewrevell/best-llm-for-openclaw-gemini-31-pro-vs-gpt-55-vs-claude-opus-47-2026-3na4)

### 2-5. 模型選用慣例（2026 Claude）
- 社群共識：Claude Sonnet 適合一般 agent 推理與 tool use；Haiku 適合高量分類/路由等輕量任務；Opus 僅在複雜推理且成本非優先時採用。
- **來源**：[Amjid Ali — Using OpenClaw with Claude, GPT, Gemini](https://amjid.au/insights/using-openclaw-with-claude-gpt-gemini-and-ollama/)

---

## 3. 社群反應與討論亮點

- **Discord「Friends of the Crustacean 🦞🤝」**：rate limit / OAuth 問題佔據最多討論，社群對 OAuth 模式下的不可預測行為頗為不滿，主流建議轉用 API key。
- **GitHub Releases 頁面**：多位用戶關注 beta/alpha 版本的每日變動，部分使用者跟著 beta 通道追求最新功能，但偶爾被不穩定的 Codex provider 流程所困。
- **MindStudio 部落格**：對 4 月推出的「6 Provider 動態切換」功能有深度解析，吸引許多從 LangChain 或 AutoGen 遷移的開發者評估 OpenClaw。
- **OpenRouter 文件新增整合指南**：降低使用者透過 OpenRouter 代理多家 LLM 的設定複雜度，引發一波討論如何把 OpenRouter 當作統一 fallback layer。

---

## 4. 值得追蹤的後續議題

1. **v2026.5.30 / v2026.5.31 是否推出新 stable 版**：依目前每兩天一版的節奏，今明可能出現新 stable，建議持續關注 [GitHub Releases](https://github.com/openclaw/openclaw/releases)。
2. **假冷卻 Bug（Issue #23996）修復進度**：此問題在較新版本中是否已被靜默修復，待確認。
3. **GPT-5.5 via Codex 的穩定性**：新增的 Codex provider 搭配 GPT-5.5 定價與限制顯示，實際使用成本與穩定性尚待更多社群回報。
4. **Android Talk Mode 後續反饋**：v2026.5.18 加入 Android Talk Mode，目前缺乏實際用戶體驗回報。
5. **Copilot 作為 embedding provider 的效果**：2026.4.15 beta 引入 Copilot 用於記憶與 retrieval，尚無系統性評測。

---

*本報告由 realvco-bot 自動生成，資料來源均附連結，如有疑義請對照原始連結查核。*
