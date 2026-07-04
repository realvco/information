# OpenClaw 每日情報｜2026-07-04

> 資料涵蓋範圍：過去 24 小時（UTC 基準）。搜尋來源包含 GitHub、X.com、社群部落格與官方文件。

---

## 1. 今日重點摘要

1. **2026.7.1-beta.1 於 7 月 2 日發布**：最新 beta 版本新增 OpenAI GPT-5.6 系列模型支援，涵蓋 catalog、capability 及 runtime 選擇路徑的識別。目前穩定版仍為 2026.6.x 系列。
2. **外部 Harness 附加（External Harness Attachment）**：新指令 `openclaw attach` 可對已存在的 Gateway session 啟動外部 harness，方便 Codex 風格的工作流程繼續執行與即時檢視，而無需重新建立 session。
3. **Telegram Codex 工作流增強**：Telegram 用戶現可透過 `/login` 啟動 Codex 配對、主動引導 Codex 執行，並在 API 短暫失敗時自動恢復最後回覆，降低人工介入需求。
4. **新模型加入 OpenCode Go 目錄**：GLM-5.2 與 Kimi K2.7 Code 已加入 catalog，用戶可直接在 OpenClaw 中選用這兩個模型。
5. **7 月 4 日 GitHub 新 Issue**：過去 24 小時內出現數個新 issue，包括 #99734（RomneyDa 回報）、#99732（高優先用戶端 bug）、#99730（agent runtime 與 tooling 相關），反映社群持續活躍回報。
6. **PR #99105 合入**：修正「多 agent 的 active-memory 召回序列化至單一共用 lane」問題，改善多 agent 並行場景下的記憶讀取效能，由 gorkem2020 於 7 月 2 日提交。
7. **2026.6.11（目前穩定版）可靠性強化**：涵蓋 Telegram、WhatsApp、Matrix、Google Chat、iMessage、Feishu、Mattermost、WebChat、Control UI 及 Terminal UI 的訊息送達穩定性修復，並改善安全預設值與安裝/更新/Docker/Windows 路徑的穩健性。
8. **Claude OAuth 訂閱配額政策異動（持續追蹤中）**：自 2026 年 4 月起，Claude Code CLI、claude.ai、Claude Desktop 可使用 Pro/Max 訂閱配額，但 OpenClaw 等第三方工具已不再包含於訂閱配額內。Anthropic 於 6 月 15 日暫停原先公告的獨立 Agent SDK 計費方案，相關社群討論持續延燒。

---

## 2. LLM 串接實戰回報

### Claude 串接與訂閱配額問題
- **重大政策異動**：2026 年 4 月後，OpenClaw 串接 Claude 已無法使用 Pro/Max 訂閱配額，須改用 API Key 計費。Anthropic 於 6 月 15 日進一步暫停 Agent SDK 專屬計費方案，目前 Agent SDK 及 `claude -p` 用量仍從登入帳號的訂閱上限扣除，但 OpenClaw 不在此列。
  - 來源：[OpenClaw + Claude Code Costs 2026: API Key vs Pro $20 vs Max $200 (After Subscription Cutoff)](https://www.shareuhack.com/en/posts/openclaw-claude-code-oauth-cost)
  - 來源：[What LLM Subscription Should You Use If You're Moving Off Claude OAuth for OpenClaw?](https://resources.learnopenclaw.ai/what-llm-subscription-should-you-use-if-youre-moving-off-claude-oauth-for-openclaw/)

### Rate Limit 處理機制
- OpenClaw 遭遇 Claude 或 GPT 回傳 429 錯誤時，會將該 provider 進入冷卻期並稍後重試，**但不會自動切換至其他 provider**，除非使用者已預先設定 fallback 路由。
- Rate limit 計量方式：RPM（每分鐘請求數）、TPM（每分鐘 token 數）、TPD（每日 token 數），免費帳號以 TPD 限制最常見。
- 多 provider 路由方案：可設定相同模型搭配不同 API key、fallback 至 OpenAI GPT-4o，或改用 Google Gemini，以確保 agent 工作流不中斷。
  - 來源：[OpenClaw API Rate Limit Reached — Fix & Prevention Guide](https://www.getopenclaw.ai/help/rate-limits-quota-management)
  - 來源：[OpenClaw hitting API rate limits in 2026? The workaround that actually works - DEV Community](https://dev.to/sophiaashi/claude-code-rate-limit-on-max-plan-the-workaround-developers-are-actually-using-in-2026-16g2)

### GPT-5.6 新支援
- 2026.7.1-beta.1 正式新增 GPT-5.6 支援，稍早的 v2026.4.23 已加入 GPT-5.5，顯示 OpenClaw 與 OpenAI 最新模型的整合持續迭代。
  - 來源：[Releases · openclaw/openclaw](https://github.com/openclaw/openclaw/releases)

---

## 3. 社群反應與討論亮點

- **@openclaw 官方 X 帳號**持續於每個版本釋出後發布簡要更新，社群用戶（如 @iamlukethedev、@iAmHenryMascot、@vincent_koc）會同步整理重點功能摘要，觸及率穩定。
- **Claude OAuth 替代方案討論熱烈**：社群圍繞「離開 Claude OAuth 後該用哪個 LLM 訂閱？」展開廣泛討論，OpenRouter 及直接 API key 方案成為主流建議。
- **GitHub Discussion #188842**：有用戶反映 OpenClaw 在特定更新後出現功能異常，標題為「Openclaw useless now after update」，已引發多人回覆。目前尚不清楚是否為普遍性問題或個案設定問題，值得追蹤後續官方回應。
  - 來源：[Openclaw useless now after update · community · Discussion #188842](https://github.com/orgs/community/discussions/188842)
- **GitHub + CI/CD 整合指南**受到關注，有多篇社群教學討論 OpenClaw 自動化 PR 管理與 issue 追蹤的實作場景。
  - 來源：[OpenClaw GitHub Integration: Automate Issues, PRs & CI/CD [2026] | ClawTank](https://clawtank.dev/blog/openclaw-github-automation-guide)

---

## 4. 值得追蹤的後續議題

1. **2026.7.1 正式版釋出時程**：目前仍為 beta.1 狀態，正式版的穩定性修復範圍與發布時間點值得關注。
2. **Claude API Key 成本影響評估**：訂閱配額取消後，大量使用 Claude 作為主力模型的用戶實際 API 費用變化，以及社群是否形成新的最佳實踐指引。
3. **GitHub Discussion #188842 後續**：官方是否確認為 bug、影響範圍為何，以及修復版本預計何時推出。
4. **GLM-5.2 與 Kimi K2.7 Code 實測回報**：新加入的中國廠商模型在 OpenClaw 工作流中的實際表現，預期社群將陸續產出比較心得。
5. **GPT-5.6 在 agent 工作流中的穩定性**：新模型版本的 function calling 相容性與 rate limit 行為，對於依賴 OpenAI 的用戶尤其重要。
6. **外部 Harness Attachment 使用案例分享**：`openclaw attach` 的實際應用情境（如恢復長時間 Codex session）預計將在社群引發討論。

---

*本報告由自動化搜尋程序彙整，資訊以搜尋結果為準，不涉及人工推測或補充。*
