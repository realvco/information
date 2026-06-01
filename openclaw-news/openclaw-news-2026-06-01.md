# OpenClaw 每日情報 — 2026-06-01

> 資料截止時間：2026-06-01 UTC｜搜尋範圍：過去 24 小時

---

## 1. 今日重點摘要

1. **v2026.5.31-beta.3 發布（昨日）**：本週最新 beta 版本於 5 月 31 日釋出，重點修復 agent 及 CLI 執行階段在工具呼叫中斷、過期 session 綁定、媒體傳遞重試時的回復邏輯，相關 PR 包含 #88129、#88136、#88141、#88162、#88182 等。
2. **多通道穩定性改善**：Telegram、WhatsApp、iMessage、Slack、Discord、Microsoft Teams、Google Chat、Google Meet 及 iOS Realtime Talk 等管道的交付穩定性均有提升，並新增 Tailscale Serve service-name 綁定與更安全的 agents 設定。
3. **Tokenjuice 正式獨立為官方外掛**：`@openclaw/tokenjuice` 現已以獨立 npm/ClawHub 套件形式發布；GitHub Copilot agent runtime 同步以 `@openclaw/copilot` 外掛發布，與核心解耦。
4. **Skill Workshop 新功能**：新增待審提案（pending proposals）、CLI/Gateway review actions、rollback metadata，以及 `skill_workshop` agent 工具，讓技能管理流程更完整。
5. **iOS 改善**：加入 hosted push relay 預設值、Realtime Talk 播放功能，以及 WebSocket ping path 守護機制，提升行動裝置 session 可靠性。
6. **Workboard 多 agent 協作**：新增 orchestration primitives 與 agent coordination tools，支援多 agent 規劃與任務追蹤（run tracking）。
7. **Code Mode 內部命名空間**：為 scoped agent/global session 新增 internal namespaces，並實作精確的 namespace tool dispatch 機制。
8. **5 月底 GitHub issues 密集湧入**：5 月 31 日當天至少有三個新 issue（#88538 標記為 bug、#88543、#88548）被開立，顯示社群在新 beta 後持續回報問題。
9. **「Rough Week」官方承認**：OpenClaw 官方部落格以「OpenClaw Had a Rough Week」為題發文，承認 4 月 24 日起 gateway 逐漸變慢、部分安裝卡在 plugin dependency repair loop 等問題，5 月底已逐步修復中。

---

## 2. LLM 串接實戰回報

### Anthropic Claude — OAuth 訂閱 token 遭封鎖
- **現象**：自 2026 年 4 月 4 日起，Anthropic 禁止第三方工具（包含 OpenClaw）使用訂閱型 OAuth token（Claude Pro $20/mo 或 Max $200/mo）。API 呼叫會收到「LLM request rejected: You're out of extra usage」錯誤。
- **解法**：改用獨立 API key（pay-as-you-go），或透過 API proxy 服務統一管理 key。
- **來源**：[apiyi.com — OpenClaw Claude LLM request rejected 解法](https://help.apiyi.com/en/openclaw-claude-llm-request-rejected-extra-usage-fix-en.html)

### 預設模型討論：Issue #21897 提議從 Claude Opus 4.6 遷移至 Gemini 3.1 Pro
- **現象**：社群在 GitHub issue #21897 討論是否應將預設模型從 Claude Opus 4.6 切換至 Gemini 3.1 Pro，主因是成本與 context window 考量。
- **注意**：Claude Opus 4.6 目前在 Anthropic direct 支援 1M context，但若透過 GitHub Copilot runtime 服務則上限降為 128K，使用者須留意部署方式。
- **來源**：[GitHub issue #21897 — migrate default model to Gemini 3.1 Pro](https://github.com/openclaw/openclaw/issues/21897)

### 多 provider 運行時切換（Provider Manifest）
- **實作**：2026 年 4 月發布的 provider manifest 讓使用者無需重建即可在執行中的 workflow 切換 LLM。目前原生支援：GPT-5.5（Codex）、Claude API、Gemini、DeepSeek、OpenRouter、Ollama、LM Studio、Gemma 4。
- **實戰建議**：搭配 TaskFlow（durable workflow）使用才能真正安全地在 workflow 中途換模型；若沒有 state 追蹤，mid-workflow 換模型容易出現斷點。
- **來源**：[MindStudio — OpenClaw April 2026: 6 Model Providers You Can Now Swap at Runtime](https://www.mindstudio.ai/blog/openclaw-april-2026-model-agnostic-provider-manifest)

### 費用預估
- API 費用會因 context 長度與模型選擇大幅波動；OpenClaw API Costs 指南（haimaker.ai）提供各模型月費估算，建議定期核對 token 用量。
- **來源**：[haimaker.ai — OpenClaw API Costs 2026](https://haimaker.ai/blog/openclaw-api-costs-pricing/)

---

## 3. 社群反應與討論亮點

- **「Openclaw useless now after update」**：GitHub community discussions #188842 出現使用者在特定更新後認為工具功能喪失的討論，顯示部分使用者對 beta 版頻繁破壞性變更感到不適應。
- **OpenClaw optimization guide**：社群成員 OnlyTerp 在 GitHub 發布 `openclaw-optimization-guide`，涵蓋速度最佳化、記憶體架構、context 管理與模型選擇的完整指引，獲得不少關注。
- **Weekly Claw 社群活動**：官方 Discord 持續舉辦 Weekly Claw 定期社群活動，負責協調演講者、錄影與社群體驗，r/OpenClaw 與 r/Clawdbot 為主要 Reddit 討論陣地。
- **「Rough Week」官方透明度獲讚**：部落格文章坦承技術問題而非隱瞞，社群反應整體正面，認為官方溝通態度好。

---

## 4. 值得追蹤的後續議題

1. **預設模型遷移（#21897）**：是否真的從 Claude Opus 4.6 切換至 Gemini 3.1 Pro？對依賴 Claude 工作流程的使用者影響待觀察。
2. **Anthropic OAuth ban 長期影響**：使用者是否大規模轉向 API key 模式，或流向其他不受此限制的 agent 框架？
3. **@openclaw/tokenjuice 外掛成熟度**：剛剛獨立為官方外掛，功能穩定性與文件完整度仍需社群驗證。
4. **Gateway 效能問題後續**：4 月底起的 gateway 變慢問題是否已在 5 月 beta 版本中完全解決？
5. **v2026.5.x stable 版發布時程**：目前仍在 beta 階段，正式穩定版的釋出時程及功能鎖定範圍值得追蹤。

---

*資料來源彙整*
- [openclaw/openclaw GitHub Releases](https://github.com/openclaw/openclaw/releases)
- [Releasebot — OpenClaw Release Notes May 2026](https://releasebot.io/updates/openclaw)
- [MindStudio — OpenClaw April 2026 Provider Manifest](https://www.mindstudio.ai/blog/openclaw-april-2026-model-agnostic-provider-manifest)
- [apiyi.com — Claude LLM request rejected fix](https://help.apiyi.com/en/openclaw-claude-llm-request-rejected-extra-usage-fix-en.html)
- [DEV Community — Best LLM for OpenClaw 2026](https://dev.to/matthewrevell/best-llm-for-openclaw-gemini-31-pro-vs-gpt-55-vs-claude-opus-47-2026-3na4)
- [GitHub issue #21897](https://github.com/openclaw/openclaw/issues/21897)
- [openclaw/openclaw AGENTS.md](https://github.com/openclaw/openclaw/blob/main/AGENTS.md)
- [OpenClaw Had a Rough Week — openclaw.ai blog](https://openclaw.ai/blog/openclaw-rough-week)
- [openclaw-optimization-guide by OnlyTerp](https://github.com/OnlyTerp/openclaw-optimization-guide)
