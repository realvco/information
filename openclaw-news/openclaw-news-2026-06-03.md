# OpenClaw 每日情報｜2026-06-03

> 搜尋範圍：過去 24 小時（UTC 基準）。本報告依搜尋結果如實撰寫，無法確認的細節保守呈現。

---

## 1｜今日重點摘要

1. **最新版本：2026.6.1-beta.2**——六月版本系列正式啟動，聚焦於 agent 與 CLI 執行期從中斷的工具呼叫、過時 session binding、compaction handoff、媒體傳遞重試等情境的復原穩定性，前一個 alpha 為 2026.6.1-alpha.3。
2. **Tokenjuice 獨立為官方外掛**——`@openclaw/tokenjuice` 已從主倉庫正式外部化，成為獨立發佈的官方外掛；GitHub Copilot agent runtime 同樣獨立為 `@openclaw/copilot` 外掛。
3. **Skills Workshop 登場**——新增 Skill Workshop 控制介面，包含待審提案清單、CLI/Gateway 審核動作、回滾 metadata、`skill_workshop` agent 工具，以及搜尋預覽、修訂對話框、檔案預覽模態等 UI 元件。
4. **Workboard 多 agent 協作原語**——Workboard 加入 orchestration primitives 與 agent coordination 工具，支援多 agent 規劃與執行追蹤，為跨 agent 工作流協調奠定基礎。
5. **iOS 推播與 WebSocket 改善**——iOS 端新增 hosted push relay 預設值、realtime Talk 播放，以及 guarded WebSocket ping 路徑，行動端 session 穩定性提升。
6. **多通訊平台覆蓋持續擴大**——Telegram、WhatsApp、iMessage、Slack、Discord、Microsoft Teams、Google Chat、Google Meet 均在 2026.6.x 系列中取得更穩定的訊息傳遞，iOS realtime Talk 也一併改善。
7. **社群評選最佳模型：Kimi K2.5**——pricepertoken.com 社群票選顯示，截至 2026 年 6 月，開發者最推薦搭配 OpenClaw 使用的模型為 Kimi K2.5，其次為 GLM 4.7 與 Claude Opus 4.6；Sonnet 系列仍是 agent 工作的穩健選項。
8. **Anthropic 封鎖訂閱方案**——Anthropic 已封鎖 OpenClaw 使用訂閱制（Claude.ai Plus 等），現在只能透過 Anthropic API（付費 API key）存取 Claude，影響習慣以訂閱帳號驅動 OpenClaw 的使用者。
9. **4.26 更新崩潰餘波**——部分社群成員仍在消化 2026.4.26 更新引發的 gateway 崩潰與 Discord/Telegram 無回應問題，版本 2026.4.23 被認為是最後一個穩定版，少數使用者已轉移至 Hermes-Agent。

---

## 2｜LLM 串接實戰回報

### 2-1 Anthropic 封鎖訂閱後的 API 設定衝擊

Anthropic 封鎖 OpenClaw 使用 Claude 訂閱方案，強制改用 API key。社群部落格 [mager.co](https://www.mager.co/blog/2026-05-13-killing-openclaw/) 記錄了一位使用者因此改採原生 Claude Code 設定的完整歷程（2026-05-13）。影響範圍：所有透過 Claude 帳號 OAuth 驅動 OpenClaw 的使用者需重新設定 API key 並承擔 token 費用。

### 2-2 OAuth Token 失效與假冷卻觸發（GitHub Issue #23996）

來源：[GitHub openclaw/openclaw#23996](https://github.com/openclaw/openclaw/issues/23996)

已回報的嚴重 bug：OpenClaw 將所有已設定的 provider 同時強制進入 cooldown，即使實際上未達 API rate limit。冷卻原因被硬編碼為 `rate_limit`，無論實際失敗原因為何。OAuth token 每 8 小時過期一次，refresh endpoint 有嚴格的 rate limit；一旦被限制，將有長達 6 小時無法使用（HTTP 401 `authentication_error`）。
- **暫時解法**：降版至 2026.2.12 可立即解決問題，無需更改設定。

### 2-3 rate limit 回退（backoff）未如文件運作

來源：[fast.io OpenClaw rate limiting guide](https://fast.io/resources/openclaw-rate-limiting-guide/)

文件描述對 provider 429 錯誤採用指數退避（exponential backoff），但實際量測顯示重試間隔遠低於文件所述。已有使用者在社群提交相關 issue。

### 2-4 max_tokens 請求設定建議

來源：[docs.openclaw.ai/reference/token-use](https://docs.openclaw.ai/reference/token-use)

OpenClaw 提供兩層 rate limit 管理：gateway 認證限制器與 provider 層 API 節流。社群建議在 `provider` 設定中明確指定 `max_tokens` 以避免不必要的過載費用，尤其在搭配 Claude Sonnet/Haiku 時，每次工具呼叫的 token 消耗可能超出預期。

### 2-5 Claude + Telegram 實測：可行但需 API key

來源：[Medium @mohit15856](https://medium.com/@mohit15856/i-wired-openclaw-to-claude-chatgpt-telegram-heres-what-actually-works-8278906edccc)

使用者實測透過 OpenClaw 將 Claude 與 ChatGPT 接入 Telegram bot，並以 Telegram 指令觸發 Claude Code 更新及重新部署網站。結論：技術上可行，但 Anthropic 封鎖訂閱方案後，必須使用 API key，月費需單獨計算。

---

## 3｜社群反應與討論亮點

- **2026.4.26 崩潰後遺症**：Reddit r/openclaw 社群中大量使用者確認在 4.26 更新後出現 gateway 崩潰循環、Discord/Telegram 無回應、高 CPU 佔用、延遲暴增，部分長期使用者已轉移至 Hermes-Agent 並將 OpenClaw 僅作備援。（來源：[piunikaweb.com](https://piunikaweb.com/2026/04/29/openclaw-update-triggering-gateway-crashes-issues/)）
- **best model 社群討論**：dev.to 文章 [Best LLM for OpenClaw: Gemini 3.1 Pro vs GPT-5.5 vs Claude Opus 4.7](https://dev.to/matthewrevell/best-llm-for-openclaw-gemini-31-pro-vs-gpt-55-vs-claude-opus-47-2026-3na4) 持續被引用，顯示開發者對模型選擇的高度關注。
- **OpenClaw vs Claude Code 比較**：[claudefa.st](https://claudefa.st/blog/tools/extensions/openclaw-vs-claude-code) 的比較文章在社群流傳，部分使用者在 Anthropic 封鎖訂閱後重新評估是否直接改用 Claude Code。
- **feature request #24865**：使用者要求 OpenClaw 在單一頁面顯示所有已設定 auth profile 的 rate limit 與用量狀態，目前需逐一查詢，管理多 provider 設定不便。

---

## 4｜值得追蹤的後續議題

| 議題 | 追蹤重點 |
|------|----------|
| 2026.6.x 正式版時程 | 目前仍在 beta.2，正式版（stable）何時釋出尚無明確時間 |
| OAuth / rate_limit bug 修復進度 | Issue #23996 是否在 6 月系列中修復 |
| Tokenjuice 外掛文件完整度 | 獨立後是否提供完整的設定與 token 預算控制文件 |
| Anthropic API-only 政策影響 | 社群是否進一步遷移至其他 LLM provider |
| Skill Workshop 穩定性 | 新 UI 元件在實際部署中的可靠度回報 |
| Workboard 多 agent 實測 | 社群對 orchestration primitives 的真實使用案例 |

---

*資料來源（搜尋日期：2026-06-03）*
- [github.com/openclaw/openclaw](https://github.com/openclaw/openclaw)
- [github.com/openclaw/openclaw/releases](https://github.com/openclaw/openclaw/releases)
- [releasebot.io/updates/openclaw](https://releasebot.io/updates/openclaw)
- [newreleases.io openclaw 2026.5.31-beta.1](https://newreleases.io/project/npm/openclaw/release/2026.5.31-beta.1)
- [piunikaweb.com 2026.4.26 崩潰報告](https://piunikaweb.com/2026/04/29/openclaw-update-triggering-gateway-crashes-issues/)
- [github.com openclaw issue #23996](https://github.com/openclaw/openclaw/issues/23996)
- [github.com openclaw issue #24865](https://github.com/openclaw/openclaw/issues/24865)
- [github.com openclaw issue #58826](https://github.com/openclaw/openclaw/issues/58826)
- [pricepertoken.com/leaderboards/openclaw](https://pricepertoken.com/leaderboards/openclaw)
- [mager.co killing-openclaw](https://www.mager.co/blog/2026-05-13-killing-openclaw/)
- [medium.com @mohit15856](https://medium.com/@mohit15856/i-wired-openclaw-to-claude-chatgpt-telegram-heres-what-actually-works-8278906edccc)
- [dev.to best LLM for OpenClaw](https://dev.to/matthewrevell/best-llm-for-openclaw-gemini-31-pro-vs-gpt-55-vs-claude-opus-47-2026-3na4)
- [fast.io rate limiting guide](https://fast.io/resources/openclaw-rate-limiting-guide/)
- [sfailabs.com OpenClaw OAuth token expired](https://sfailabs.com/guides/openclaw-oauth-token-expired)
- [docs.openclaw.ai token-use](https://docs.openclaw.ai/reference/token-use)
