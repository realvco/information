# OpenClaw 每日情報 — 2026-06-07

> 搜尋範圍：過去 24 小時內關於 OpenClaw 的最新資訊
> 產製時間：2026-06-07 UTC

---

## 1. 今日重點摘要

1. **2026.6.2 正式釋出**：OpenClaw 本週推送 2026.6.2 版本，聚焦於插件安裝安全性強化、頻道/UI 穩定性修復、安全性與設定驗證提升，以及 Gateway、Agent 與 Provider 的錯誤恢復能力改善。
2. **Skill Workshop 新功能**：引入「審核優先」的技能建立流程 — agent 完成工作後，建議技能會停在 `PROPOSAL.md` 等待人工核准，再轉為可複用技能，支援 chat、UI、CLI、頻道與 Gateway 全通路。
3. **插件安全策略調整**：插件與技能安裝改用「operator install policy」，取代舊有危險程式碼掃描路徑，並改善 doctor CLI、ClawHub、疑難排解的說明介面。
4. **頻道穩定性提升**：Telegram、WhatsApp、iMessage、Slack、Discord、Microsoft Teams、Google Chat、Google Meet 及 iOS 即時語音傳訊皆獲穩定性修補；轉錄鏡像失敗時頻道傳送仍可維持持久性。
5. **QQBot 思考內容外洩修復**：QQBot 現在在原生傳送前會過濾模型 reasoning/thinking scaffolding，避免原始思考內容洩漏至頻道回覆。
6. **MCP 工具結果強型態轉換**：MCP 工具回傳的 `resource_link`、`resource`、`audio`、畸形圖片等非文字/圖片內容，現在在 materialize 邊界統一強制轉型，預防 Anthropic 400 錯誤與污染 session 歷史。
7. **Microsoft Scout 借鑑 OpenClaw 設計**：TechCrunch 於 6 月 2 日報導 Microsoft 在 Build 2026 發表 Scout 個人助理，業界將其定位為「受 OpenClaw 啟發」的產品，顯示 OpenClaw 架構理念的市場影響力持續擴大。
8. **Tokenjuice 與 Copilot 插件外部化**：`@openclaw/tokenjuice`（token 使用追蹤）與 `@openclaw/copilot`（GitHub Copilot agent runtime）正式外部化為獨立 npm/ClawHub 插件。
9. **連五日更新節奏**：據 36kr 英文版報導，OpenClaw 本週呈現連續五日推送、單日兩更的高頻更新節奏，穩定度與功能修補同步進行。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）
- **API 格式陷阱**：使用 OpenClaw 串接 Claude API 時，必須指定 `anthropic-messages` 格式；若誤用 `openai-completions` 格式，多輪工具呼叫會觸發 HTTP 400 錯誤。（來源：[Apiyi.com Blog](https://help.apiyi.com/en/openclaw-claude-api-apiyi-anthropic-messages-guide-en.html)）
- **OAuth Token 偽冷卻 Bug（Issue #23996）**：OpenClaw 對設定了 OAuth token（`sk-ant-oat01-` 前綴）的 provider 出現「假冷卻」問題 — 即使未真正觸發速率限制，系統仍硬編碼 `rate_limit` 原因並進入無限冷卻循環。（來源：[GitHub Issue #23996](https://github.com/openclaw/openclaw/issues/23996)）
- **2026 年推薦模型配置**：社群普遍共識為：Sonnet 用於一般 agent 工作（推理、工具呼叫）、Haiku 用於高頻低成本任務、Opus 用於需要最高推理能力且成本次要的場景。（來源：[Meta Intelligence](https://www.meta-intelligence.tech/en/insight-openclaw-model-guide)）

### AWS Bedrock
- **每日 Token 限制未被偵測（Issue #38822）**：AWS Bedrock 在超出每日 token 配額時回傳 `"Too many tokens per day"` 訊息，但 OpenClaw 的速率限制偵測模式無法識別此錯誤，導致主模型持續失敗卻未觸發 fallback。（來源：[GitHub Issue #38822](https://github.com/openclaw/openclaw/issues/38822)）

### GPT / Gemini / 其他
- **模型無關設計優勢**：OpenClaw 的 Gateway、agents、skills、channels 設計上不依賴底層 LLM，支援 Anthropic、OpenAI 或本地模型（Ollama 等），換模型不影響工作流程。（來源：[Model providers docs](https://docs.openclaw.ai/concepts/model-providers)）
- **實戰評測（GPT-5.5 vs Claude Opus 4.7 vs Gemini 3.1 Pro）**：Dev.to 有開發者撰文比較三家模型在 OpenClaw 工作流中的效能與穩定性，結論依任務類型而異，推薦依使用情境選擇。（來源：[DEV Community](https://dev.to/matthewrevell/best-llm-for-openclaw-gemini-31-pro-vs-gpt-55-vs-claude-opus-47-2026-3na4)）

---

## 3. 社群反應與討論亮點

- **社群分布**：主要討論場所為 r/openclaw（官方社群，以安裝教學、show-and-tell 為主）、r/ClaudeAI（因 OpenClaw 源於 Claude Code 生態，最大討論地），以及 r/selfhosted（基礎設施與安裝問題）。
- **Discord 支援結構**：官方 Discord 設有 `#help`、`#users-helping-users` 等支援頻道，由 Community Staff 志工團隊維運，處理產品支援問題並分流回報。
- **Telegram Bot 自動化分享熱潮**：有用戶分享將 OpenClaw 與 Claude + ChatGPT 同時串接並接入 Telegram，實現「從手機發訊息觸發 Claude Code 自動修 bug 並重新部署」的完整流程，引發高度關注。（來源：[Medium — Mohit Aggarwal](https://medium.com/@mohit15856/i-wired-openclaw-to-claude-chatgpt-telegram-heres-what-actually-works-8278906edccc)）
- **OpenClaw vs Claude Code 比較討論**：claudefa.st 發布深度比較文，探討兩者定位差異，社群對「代理框架 vs 直接使用 Claude Code」的適用情境持續熱議。（來源：[claudefa.st](https://claudefa.st/blog/tools/extensions/openclaw-vs-claude-code)）

---

## 4. 值得追蹤的後續議題

1. **Issue #23996 修復進度**：OAuth token 偽冷卻問題尚未在 2026.6.2 中完整修復，需持續追蹤官方回應與 patch 時程。
2. **Issue #38822 AWS Bedrock 支援**：每日 token 限制錯誤模式未被識別，AWS Bedrock 用戶受影響，等待 PR 合併。
3. **Skill Workshop 採用情況**：新的技能建立流程（PROPOSAL.md 審核制）目前仍屬新功能，社群實際採用情況與反饋值得觀察。
4. **Microsoft Scout 生態影響**：Microsoft 發表 OpenClaw 啟發的 Scout，是否會帶動企業用戶評估 OpenClaw 作為自架方案，或反而加速競品功能收斂。
5. **Tokenjuice 插件完整文件**：外部化為獨立插件後的使用教學尚待補充，社群對 token 使用追蹤功能的完整設定方式有需求。

---

*資料來源：[GitHub openclaw/openclaw](https://github.com/openclaw/openclaw) · [Releasebot](https://releasebot.io/updates/openclaw) · [GitHub Issue #23996](https://github.com/openclaw/openclaw/issues/23996) · [GitHub Issue #38822](https://github.com/openclaw/openclaw/issues/38822) · [Apiyi.com](https://help.apiyi.com/en/openclaw-claude-api-apiyi-anthropic-messages-guide-en.html) · [DEV Community](https://dev.to/matthewrevell/best-llm-for-openclaw-gemini-31-pro-vs-gpt-55-vs-claude-opus-47-2026-3na4) · [TechCrunch](https://techcrunch.com/2026/06/02/microsoft-launches-scout-an-openclaw-inspired-personal-assistant/) · [Medium](https://medium.com/@mohit15856/i-wired-openclaw-to-claude-chatgpt-telegram-heres-what-actually-works-8278906edccc)*
