# OpenClaw 每日情報 — 2026-06-17

> 資料範圍：過去 24 小時（UTC）｜來源：GitHub、社群論壇、部落格、技術媒體

---

## 1. 今日重點摘要

1. **穩定版維持 v2026.6.1，Beta 列車推進至 v2026.6.5-beta.2**：版本採用 `YYYY.M.PATCH` 曆法版號，六月穩定版為 2026.6.1（2026-06-03 標記），Beta 持續硬化中，尚未升至正式穩定。（來源：[GitHub Releases](https://github.com/openclaw/openclaw/releases/tag/v2026.6.5)、[Releasebot](https://releasebot.io/updates/openclaw)）

2. **v2026.6.5/6 安全強化**：v2026.6.6 大幅收緊安全邊界，涵蓋 transcript 沙盒綁定、主機環境繼承、MCP stdio、Codex HTTP 存取、原生搜尋策略及提升的 sender 驗證；v2026.6.5 則修正 QQBot 在傳遞前未過濾 model reasoning/thinking 內容導致原始思考流外洩頻道的問題。（來源：[Releasebot](https://releasebot.io/updates/openclaw)）

3. **v2026.6.1 Skill Workshop 與插件化**：穩定版引入 Skill Workshop 提案管理機制，並將 Tokenjuice 與 Copilot agent runtime 正式外部化為官方插件，降低核心耦合。（來源：[Fastio Changelog](https://fast.io/resources/openclaw-changelog-guide/)）

4. **SQLite 持久化狀態遷移**：近期合併的 PR 將 memory-wiki sync、memory-core dreams、ACP process、device-pair notify 等執行期狀態統一遷入 SQLite，提升跨重啟的一致性。（來源：[openclaw.com.au/updates](https://openclaw.com.au/updates)）

5. **OAuth token 觸發假 rate_limit 進入無限 cooldown（Issue #23996）**：使用 `sk-ant-oat01-` 前綴的 Anthropic OAuth Access Token（Claude Max 帳號）時，OpenClaw 將兩個已設定的 provider 同時誤判為 rate_limit 並鎖定，即便 Anthropic Console 顯示本月用量為零。500/503 錯誤被錯誤分類為 `rate_limit` 是已知根因；此問題在 2026.2.21-2 出現、降回 2026.2.12 可暫時迴避。（來源：[Issue #23996](https://github.com/openclaw/openclaw/issues/23996)、[Issue #32828](https://github.com/openclaw/openclaw/issues/32828)）

6. **多模型路由策略帶來最高 98% 費用節省**：社群整理出三段式路由方案：heartbeat 類任務改用 Gemini Flash-Lite（降費 98.33%）、sub-agent 平行任務改用 DeepSeek R1（降費 90.86%）、簡單查詢改用 DeepSeek V3.2（降費 98.23%），只讓 Claude / GPT 處理高複雜度任務。（來源：[VelvetShark 路由指南](https://velvetshark.com/openclaw-multi-model-routing)）

7. **PinchBench 基準：Claude Sonnet 4.6 奪冠**：OpenClaw 官方 benchmark 最新資料顯示 Claude Sonnet 4.6 以 86.9% 成功率領先，並在 MCPAgentBench 得分 71.6；GPT-5.5 在 128K context 以內的 Terminal-Bench 2.0 奪冠；Gemini 3.1 Pro 則為大型 codebase 及 multimodal CI/CD 工作流的最佳預設選擇。（來源：[DEV Community 比較文](https://dev.to/matthewrevell/best-llm-for-openclaw-gemini-31-pro-vs-gpt-55-vs-claude-opus-47-2026-3na4)）

8. **社群活躍度極高**：過去 24 小時 GitHub 更新超過 500 issues 與 500 PRs，反映專案處於快速迭代期。（來源：[agents-radar Issue #544](https://github.com/duanyytop/agents-radar/issues/544)）

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）

- **OAuth token auth 失效問題**：Claude Max 用戶使用 `sk-ant-oat01-` 前綴的 OAuth token 時，openclaw 的 cooldown 邏輯未區分 OAuth 與標準 API key，導致假 rate_limit 鎖定。臨時解法：降版至 2026.2.12，或靜候官方 hotfix。
  - 來源：[Issue #23996](https://github.com/openclaw/openclaw/issues/23996)

- **模型表現**：Claude Sonnet 4.6 在 PinchBench 以 86.9% 位居首位，MCP 工具呼叫穩定性最佳；Claude Opus 4.7 適合需要深度推理的長流程任務。
  - 來源：[DEV Community](https://dev.to/matthewrevell/best-llm-for-openclaw-gemini-31-pro-vs-gpt-55-vs-claude-opus-47-2026-3na4)

### GPT（OpenAI）

- GPT-5.5（Codex 版）在 Terminal-Bench 2.0 表現優異，但限制在 128K context 以內；超出後建議切換至 Gemini 或 Claude。
  - 來源：[DEV Community](https://dev.to/matthewrevell/best-llm-for-openclaw-gemini-31-pro-vs-gpt-55-vs-claude-opus-47-2026-3na4)

### Gemini（Google）

- Gemini 3.1 Pro 推薦用於大型 codebase 迭代及 multimodal CI/CD artifact 分析，免費額度（Gemini Flash-Lite）作為低成本路由節點效益顯著。
  - 來源：[DEV Community](https://dev.to/matthewrevell/best-llm-for-openclaw-gemini-31-pro-vs-gpt-55-vs-claude-opus-47-2026-3na4)、[VelvetShark](https://velvetshark.com/openclaw-multi-model-routing)

### OpenRouter / DeepSeek

- OpenClaw 原生支援 OpenRouter，使用 `openrouter/<author>/<slug>` 格式直接引用，無需額外設定 `models.providers`；`openrouter/openrouter/auto` 可依 prompt 複雜度自動路由。
- DeepSeek R1 適合 sub-agent 平行任務；DeepSeek V3.2 適合簡單查詢，兩者合用可大幅壓低每日 API 費用。
  - 來源：[OpenRouter 官方整合文件](https://openrouter.ai/docs/guides/guides/openclaw-integration)、[Skywork 設定指南](https://skywork.ai/skypage/en/openclaw-openrouter-configuration/2048652827442671616)

---

## 3. 社群反應與討論亮點

- **多模型路由討論熱度高**：社群指出單純使用旗艦模型處理所有任務是最常見的費用浪費；三段式分流（Flash-Lite / DeepSeek / Claude）被反覆推薦為最佳實踐。
  - 來源：[VelvetShark 指南](https://velvetshark.com/openclaw-multi-model-routing)、[Skywork 指南](https://skywork.ai/skypage/en/openclaw-openrouter-configuration/2048652827442671616)

- **OAuth token cooldown bug 引發抱怨**：多位 Claude Max 用戶反映升級後代理完全停止回應，subreddit r/OpenClaw 與 Discord #help 頻道出現相關討論串，且無自動恢復機制（Critical 等級）。
  - 來源：[Issue #32828](https://github.com/openclaw/openclaw/issues/32828)、[AnswerOverflow 討論](https://www.answeroverflow.com/m/1480712801231048744)

- **Skill Workshop 獲正向評價**：v2026.6.1 的 Skill Workshop 讓使用者能管理與外部化技能提案，社群認為這是邁向更模組化架構的重要一步。
  - 來源：[clawbot.blog](https://www.clawbot.blog/blog/openclaw-the-rise-of-an-open-source-ai-agent-framework-april-2026-update/)

---

## 4. 值得追蹤的後續議題

1. **OAuth token cooldown hotfix**：Issue #23996 標記為 Critical，官方尚未發布 patch；Claude Max 用戶應密切關注 v2026.6.x 後續 patch 版本。
   - 追蹤：[Issue #23996](https://github.com/openclaw/openclaw/issues/23996)

2. **v2026.6.5-beta → 穩定版升級時程**：Beta 列車已至 beta.6，何時切穩定值得關注，尤其安全強化變更（v2026.6.6）是否全數回移。
   - 追蹤：[GitHub Releases](https://github.com/openclaw/openclaw/releases)

3. **OpenRouter auto-routing 穩定性**：社群反映 `openrouter/openrouter/auto` 在高並發時偶有路由不穩的問題，尚無官方說明。
   - 追蹤：[OpenRouter 整合頁](https://docs.openclaw.ai/providers/openrouter)

4. **Gemini native provider 進度**：部分使用者希望直接對接 Vertex AI 而非透過 OpenRouter 轉發，以繞過 402/rate limit；目前無官方時間表。
   - 追蹤：[Feature Request 討論](https://www.mindstudio.ai/blog/openclaw-april-2026-model-agnostic-provider-manifest)

---

*報告生成時間：2026-06-17 UTC｜資料來源詳見各段落連結*
