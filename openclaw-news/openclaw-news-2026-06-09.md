# OpenClaw 每日情報 — 2026-06-09

> 搜尋時間範圍：過去 24 小時（UTC 2026-06-08 ～ 2026-06-09）

---

## 1. 今日重點摘要

1. **2026.6.2 正式發布**：OpenClaw 釋出 2026.6.2，涵蓋更安全的外掛安裝機制、更廣泛的 channel 及 UI 可靠性修正、強化安全性與設定檢查，以及改善 gateway、agent 與 provider 的崩潰恢復能力。
2. **Skill Workshop 上線**：新增「審核優先（review-first）」的技能建立流程——agent 工作成果先作為「提案」存入 PROPOSAL.md，需手動核准才會正式納入技能庫，降低自動化操作帶來的誤入技能風險。
3. **版本命名改制為 YYYY.M.PATCH**：從本月起版本號切換至每月 patch 編號制，舊版標籤保持向下相容，6 月基準版本定為 2026.6.5（beta 後正式確認）。
4. **Provider 與 MCP 穩健性提升**：修正 MCP tool-result 回傳非文字/圖片 block 時的格式問題，現在會自動轉換為文字或保留有效圖片，避免送進 provider converter 產生格式錯誤的 image block。
5. **Auth 原子寫入與強制重新登入恢復**：agents/auth 現在以原子方式寫入 auth profiles、依失敗類型分發 auth 錯誤，並新增強制重新登入的恢復路徑；uninstall 時也可保留 workspace 狀態。
6. **ClawHub auth token 遺漏 bug 仍開啟中（Issue #52949）**：Gateway 的 `searchSkillsFromClawHub` 函式呼叫 API 時未附帶 ClawHub auth token，導致持續收到 `ClawHubRequestError: Rate limit exceeded (429)`，ClawControl 介面的「Available Skills」一片空白。此 issue 截至今日仍在追蹤中。
7. **偽 rate_limit 觸發無限冷卻 bug（Issue #23996）**：OAuth Access Token（`sk-ant-oat01-` 前綴）用戶反映，provider 冷卻原因被硬編碼為 `rate_limit`，即使 API 完全正常也會同時觸發所有 provider 進入無限冷卻。
8. **Anthropic 訂閱政策衝擊依舊**：自 2026-04-04 起，Anthropic Claude 訂閱不再涵蓋 OpenClaw 等第三方 API 呼叫；Max/Pro 訂閱僅適用於 Claude.ai、Claude Code、Claude Cowork，OpenClaw 使用另計費用（pay-as-you-go Extra Usage）。社群仍有用戶在討論遷移策略。
9. **CLI 新增 agent 時避免不必要的 catalog 驗證**：`openclaw agents add` 指令不再依賴 provider catalog 即時可用性，新增第二個 agent 不會因 catalog 暫時下線而失敗。

---

## 2. LLM 串接實戰回報

### Anthropic Claude（OAuth / API Key）

- **訂閱切斷踩坑**：多位用戶反映，升級後遇到 CLI 遷移指令雖改寫 model 路徑，但卻未設定新的 backend 服務路徑，導致持續出現 `Missing Auth` 錯誤，需手動重新設定後端。解法文章見 Medium：[Fix OpenClaw Missing Auth After Anthropic Changes](https://medium.com/@ulmeanuadrian/anthropic-just-cut-off-my-ai-agents-heres-how-i-fixed-it-in-20-minutes-741384d27080)
- **OAuth token 冷卻 bug**：使用 Claude Max OAuth（`sk-ant-oat01-` 前綴）的用戶特別容易觸發 Issue #23996 的偽冷卻問題，建議暫時改用 API Key 直連以規避。
- 參考：[XDA 報導 — Claude 訂閱失去 OpenClaw 存取](https://www.xda-developers.com/claude-subscribers-just-lost-access-to-openclaw-and-other-third-party-toolsunless-they-pay-more/)

### OpenAI GPT-5.x

- **GPT-5.4 懈怠問題**：部分用戶回報 GPT-5.4 在執行複雜多步任務時，只嘗試數輪就放棄，即使任務理論上可完成。目前社群建議在 prompt 中明確要求持續執行，或改用 GPT-4.1。
- 參考：[I Wired OpenClaw to Claude, ChatGPT & Telegram — Here's What Actually Works](https://medium.com/@mohit15856/i-wired-openclaw-to-claude-chatgpt-telegram-heres-what-actually-works-8278906edccc)

### 多 provider 並用經驗

- 使用者同時設定 `anthropic:default`（OAuth）、`anthropic:silverberry`（API token）、`openai-codex:default` 三組 auth profile，反映無法在單一畫面查看所有 profile 的 rate limit / 使用量狀態；Feature Request #24865 正在追蹤此需求。
- 參考：[Feature Request #24865](https://github.com/openclaw/openclaw/issues/24865)

---

## 3. 社群反應與討論亮點

- **r/openclaw 與 r/ClaudeAI** 目前討論焦點轉向「運維可信度」（operational trust），包含安裝來源驗證、provider 身分確認、session 存活偵測、訊息持久性投遞等議題，反映社群已從「如何玩起來」進入「如何穩定跑」的成熟階段。
- 部分 Discord 用戶認為 Skill Workshop 的「提案審核制」增加了不少手動步驟，討論是否應提供「信任的 agent 可自動核准」的快速模式；目前官方態度偏向保守，維持人工審核。
- 有用戶在 Telegram 群組分享透過 OpenClaw trigger Claude Code 自動更新並重新部署網站的工作流，獲得較多正面回應。

---

## 4. 值得追蹤的後續議題

1. **Issue #52949（ClawHub token 遺漏）**：修復進度，預計影響所有依賴 ClawControl skills 清單的用戶。
2. **Issue #23996（偽 rate_limit 冷卻）**：OAuth token 用戶的核心痛點，關注 fix 何時進入 patch。
3. **2026.6.x patch 連續釋出**：目前已有 2026.6.1、2026.6.2-beta，關注正式版 patch 節奏與下一個 minor release。
4. **Anthropic 計費政策後續影響**：是否有更多第三方 harness 被列入限制，或 Anthropic 是否提供 OpenClaw 友善的授權方案。
5. **Skill Workshop 自動信任模式討論**：社群對「受信任 agent 可自動核准技能」的需求是否納入 roadmap。

---

*資料來源：*
- [GitHub — openclaw/openclaw Releases](https://github.com/openclaw/openclaw/releases)
- [OpenClaw Release Notes June 2026 — Releasebot](https://releasebot.io/updates/openclaw)
- [Skill Workshop 文件](https://docs.openclaw.ai/tools/skill-workshop)
- [Issue #52949 — ClawHub auth token 遺漏](https://github.com/openclaw/openclaw/issues/52949)
- [Issue #23996 — 偽 rate_limit 冷卻](https://github.com/openclaw/openclaw/issues/23996)
- [Issue #24865 — 多 profile 使用量視圖](https://github.com/openclaw/openclaw/issues/24865)
- [Medium — Fix Missing Auth After Anthropic Changes](https://medium.com/@ulmeanuadrian/anthropic-just-cut-off-my-ai-agents-heres-how-i-fixed-it-in-20-minutes-741384d27080)
- [XDA Developers — Claude 訂閱失去 OpenClaw 存取](https://www.xda-developers.com/claude-subscribers-just-lost-access-to-openclaw-and-other-third-party-toolsunless-they-pay-more/)
- [Medium — I Wired OpenClaw to Claude, ChatGPT & Telegram](https://medium.com/@mohit15856/i-wired-openclaw-to-claude-chatgpt-telegram-heres-what-actually-works-8278906edccc)
- [ClawMage — OpenClaw Reddit 2026 社群分析](https://clawmage.ai/blog/openclaw-reddit/)
