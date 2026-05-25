# OpenClaw 每日情報｜2026-05-25

> 資料蒐集時間範圍：過去 24 小時（UTC 基準）
> 來源：GitHub Releases、newreleases.io、社群論壇、技術部落格

---

## 1. 今日重點摘要

1. **v2026.5.20-beta.1/beta.2 為當前最新測試版**（發布於 5 月 21 日），目前穩定版為 v2026.5.18，今日尚無新版正式推送，過去 24 小時無新發布紀錄。
2. **Provider 預熱最佳化**：v2026.5.20-beta 中，gateway 啟動時會預先暖機 provider auth-state map，`/models` 及所有模型列表呼叫延遲從約 20 秒降至約 5 毫秒（改善幅度約 4,100 倍）。
3. **安全強化**：root npm 套件與所有 openclaw-owned npm plugin 改為附帶生成的 shrinkwrap，lockfile/shrinkwrap 變更需人工 review，確保已發布安裝版本使用鎖定相依圖。
4. **WebChat 優化**：內部 message-tool source reply 現改為摘要形式，tool card 不再重複顯示可見回覆內容。
5. **預設模型遷移提案**：GitHub issue #21897 提議將預設模型從 Claude Opus 4.6 正式切換至 Gemini 3.1 Pro，討論熱度仍在持續。
6. **Anthropic OAuth 限制影響持續發酵**：自 4 月 4 日起 Anthropic 禁止訂閱 OAuth token 用於第三方工具，OpenClaw 使用者若仍以 Claude Plus 訂閱 OAuth 存取，將持續遭遇 auth 失效問題，官方建議改用 API Key 模式。
7. **false "API rate limit reached" bug 仍未完全修復**：GitHub issue #32828 記錄了即使 upstream API 正常仍出現假性限速錯誤，社群反映 error message 具誤導性，追蹤中。
8. **六大 provider 運行時切換**：目前支援 GPT-5.5 (Codex)、Claude API、Gemini 3.1 Pro、DeepSeek、OpenRouter、Ollama / LM Studio / Gemma 4，可在不重建 workflow 的情況下動態切換。

---

## 2. LLM 串接實戰回報

### Anthropic（Claude）

- **OAuth 禁令確認生效**：自 2026-04-04 12:00 PM PT，Anthropic 正式禁止第三方工具使用 Claude.ai 訂閱 OAuth token，OpenClaw 使用 Claude 的唯一合規路徑為直接 API Key 或「Extra Usage（用量計費）」模式。
  - 來源：[4 solutions to resolve the OpenClaw error LLM request rejected - Apiyi.com](https://help.apiyi.com/en/openclaw-claude-llm-request-rejected-extra-usage-fix-en.html)
- **假性限速錯誤**：使用 Claude OAuth 時系統回傳「API rate limit reached. Please try again later.」，實為 OAuth session timeout，而非真正超過速率限制。社群對此錯誤訊息不透明感到困擾。
  - 來源：[OpenClaw rate limit error - answeroverflow.com](https://www.answeroverflow.com/m/1478788645472436264)

### Gemini

- 目前已成為 OpenClaw 最受討論的替代預設模型，issue #21897 提議以 Gemini 3.1 Pro 取代 Claude Opus 4.6 為預設。
  - 來源：[feat(models): migrate default model - GitHub issue #21897](https://github.com/openclaw/openclaw/issues/21897)

### GPT / OpenAI

- GPT-5.5 透過 Codex 介面支援，與 OpenRouter 一同被列為相對穩定的 provider 選項。

### 通用費用與限速問題

- 同一個「429 rate limit exceeded」訊息可能來自：upstream model-provider 配額、ClawHub skill 下載限制、gateway cooldown 狀態、fallback 覆蓋不足、或 agent session 過大等五種不同原因，診斷難度高。
  - 來源：[OpenClaw Rate Limit Exceeded 429 - LaoZhang AI Blog](https://blog.laozhang.ai/en/posts/openclaw-rate-limit-exceeded-429)
- 建議做法：統一切換至 API Key 模式，避免 OAuth 訂閱與 API 限速混用造成的混淆。
  - 來源：[OpenClaw LLM Setup Guide - LaoZhang AI Blog](https://blog.laozhang.ai/en/posts/openclaw-llm-setup)

---

## 3. 社群反應與討論亮點

- **GitHub Feature Request #24865**：使用者要求能在單一介面查看所有已設定 auth profile 的 rate limit / usage 狀態，目前散落各 provider 設定難以管理。
  - 來源：[Feature: Show rate limit/usage state - GitHub #24865](https://github.com/openclaw/openclaw/issues/24865)
- **Reddit r/openclaw 討論**（4 月底餘波）：v2026.4.26 引發大規模 Discord 整合崩潰與 gateway 問題，部分長期使用者表示考慮轉往 Hermes-Agent，認為後者更新更穩定。
  - 來源：[OpenClaw update triggering gateway crashes - piunikaweb.com](https://piunikaweb.com/2026/04/29/openclaw-update-triggering-gateway-crashes-issues/)
- **shrinkwrap 安全措施**引發小幅討論，部分 plugin 開發者需調整發布流程以符合新規範。

---

## 4. 值得追蹤的後續議題

| 議題 | 狀態 | 追蹤重點 |
|------|------|---------|
| 預設模型切換（Claude → Gemini 3.1 Pro）| 討論中（issue #21897）| 是否合併、時程、對現有使用者的影響 |
| 假性 rate limit 錯誤（issue #32828）| 修復中 | 下一個穩定版是否包含修正 |
| Anthropic OAuth 限制長期適應 | 持續 | Extra Usage 費用結構、API Key 替代方案成熟度 |
| v2026.5.20 正式版發布 | 測試中（beta.2）| 預計穩定版時程、新功能正式可用性 |
| 多 provider rate limit 統一視圖（issue #24865）| 特性請求 | 是否排入 roadmap |

---

*本報告根據公開搜尋結果整理，不含推測或未經確認的資訊。*
