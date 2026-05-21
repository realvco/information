# OpenClaw 每日情報 — 2026-05-21

> 搜尋範圍：過去 24 小時（UTC 2026-05-20 ～ 2026-05-21）

---

## 1. 今日重點摘要

1. **近 24 小時無重大版本釋出**：OpenClaw 最近一個正式版為 `2026.5.19`（Android Talk Mode 即時化、Mac 設定介面優化、xAI headless 登入修正、Telegram topics 處理改善），今日尚無新版本公告。
2. **GitHub 持續高活躍**：截至 2026-05-21，主庫開放中的 PR 達 3,677 個。5 月 20 日新增 PR 包括：修復孤立 Codex 自訂工具輸出（PR #84733）、處理 launchd 並發啟動 race condition（PR #84728）、保留 webhook 請求計數器（PR #84719）、`doctor` 指令新增明文 secret 警告（PR #84715）。
3. **Issue #84599、#84746 待關注**：分別涉及 Linux/Windows Clawdbot App 行為異常，目前仍為 open 狀態（5-20、5-21 回報）。
4. **安全性路線圖更新**：5 月 15 日發布的安全強化計畫持續延燒討論，重點為引入 VirusTotal + 靜態分析 + 人工審核三層機制，惡意外掛將被拒絕安裝，社群對此反應兩極（部分用戶憂心自訂外掛受限）。
5. **競爭壓力加劇**：MarkTechPost（2026-05-10）報導 Hermes-Agent 在 OpenRouter 全球日流量排行超越 OpenClaw（Hermes 224B token/天 vs OpenClaw 186B），引發社群廣泛討論。
6. **Provider 切換能力獲肯定**：4 月底推出的 provider manifest（支援六大 provider 熱切換）在社群持續獲得正面評價，已成為 OpenClaw 對抗競品的核心賣點。
7. **DEV.to 模型比較文章熱度不減**：〈Best LLM for OpenClaw: Gemini 3.1 Pro vs GPT-5.5 vs Claude Opus 4.7〉在過去 24 小時仍持續被引用與討論，為新用戶選型的主要參考資源。
8. **Plugin 生態工具鏈完善**：`defineToolPlugin` + `openclaw plugins build/validate/init` CLI 指令讓社群開發者建立類型安全的自訂工具外掛，是近期 ClawHub 新外掛量增長的重要推力。

---

## 2. LLM 串接實戰回報

### 2-1. OAuth Token 導致偽 429 問題（持續中）

- **現象**：使用 Claude OAuth（而非 API Key）設定時，部分使用者在 Anthropic Console 的用量顯示為 0，卻不斷收到「API rate limit reached」報錯。
- **根因**：Subscription Auth 的用量不計入 Console 儀表板，導致誤判；實際問題為 OpenClaw 在某些版本中將兩組 provider 同時錯誤地送入 cooldown（Issue #32828）。
- **因應**：執行 `openclaw doctor --fix` 可自動修復多數狀況；或在設定中加入 fallback model，讓一個 provider 進入 cooldown 後自動切換。
- **來源**：[GitHub Issue #32828](https://github.com/openclaw/openclaw/issues/32828)、[AnswerOverflow 討論](https://www.answeroverflow.com/m/1478788645472436264)

### 2-2. xAI Grok Reasoning Effort 參數衝突（已修復）

- **現象**：在 v2026.5.5 之前，OpenClaw 對 xAI 的 Grok 原生模型送出 OpenAI 格式的 `reasoning_effort` 控制參數，導致 `xai/grok-4.3` 在 Docker 和 Gateway 環境中拋出 `Invalid reasoning effort` 錯誤。
- **修復**：v2026.5.5 已修正；同時 bundled xAI thinking profile 預設改為 off。
- **來源**：[What's New in OpenClaw May 2026 — Blink Blog](https://blink.new/blog/openclaw-whats-new-may-2026)

### 2-3. 多 Provider 成本策略心得

- **Gemini 3.1 Pro** 因具備 1M token context、最低成本（$2/$12 per 1M tokens）、免費開發層，已成為 OpenClaw 用戶的主流首選。
- **Claude Opus 4.7** 被推薦用於「需要精細判斷或多步推理」的任務，搭配 Gemini Flash 做高頻監控任務，可有效壓低整體 API 費用。
- **GPT-5.5** 標準 API 僅 128K context，不適合需要大量上下文的 OpenClaw workflow；使用者建議僅在需要特定 Codex 功能時才切換。
- **來源**：[Best LLM for OpenClaw — DEV.to](https://dev.to/matthewrevell/best-llm-for-openclaw-gemini-31-pro-vs-gpt-55-vs-claude-opus-47-2026-3na4)、[OpenClaw LLM Setup Guide — LaoZhang AI Blog](https://blog.laozhang.ai/en/posts/openclaw-llm-setup)

---

## 3. 社群反應與討論亮點

- **OpenClaw vs Hermes-Agent 論戰**：Teknium（NousResearch 核心貢獻者）在 X.com 貼文表示 Hermes-Agent 日 token 量已近乎 2 倍於 OpenClaw，引發 OpenClaw 粉絲防禦性討論；OpenClaw 社群強調「本地部署、隱私優先」的差異定位，認為不是同一個戰場。
  - [Teknium 推文](https://x.com/Teknium/status/2055125356554899865)
- **外掛安全機制爭議**：5 月 15 日安全公告後，「Friends of the Crustacean 🦞🤝」Discord 伺服器出現多則討論，部分進階用戶認為三層審查可能減慢社群外掛上架速度。
  - [OpenClaw 安全路線圖](https://blockchain.news/news/openclaw-security-roadmap-2026)
- **Oliver Henry 免費 Skill 計畫**：知名 OpenClaw 推廣者 Oliver Henry 宣布釋出免費的 ClawHub Skill，聲稱可「一鍵實作 viral playbook」，在社群引發一波討論與跟風嘗試。
  - [X 貼文](https://x.com/oliverhenry/status/2023151724140134862)
- **Android Talk Mode 反應**：2026.5.19 釋出的 Android 即時語音功能（先前 iOS/macOS 先行）在社群引發正面迴響，多位用戶在 Discord 分享實測影片。

---

## 4. 值得追蹤的後續議題

| 議題 | 說明 |
|------|------|
| GitHub Issue #84599、#84746 | Linux/Windows Clawdbot App 異常，後續修復時程待觀察 |
| Issue #32828（偽 429 cooldown） | False rate limit 問題修復狀況，是否進入正式版 |
| Issue #23996（OAuth hardcoded rate_limit） | OAuth token 導致的無限 cooldown，已開 issue 但尚未 close |
| 外掛安全三層審查機制上線 | 預計影響 ClawHub 新外掛上架速度，正式實施時程未定 |
| OpenClaw vs Hermes-Agent 日流量差距 | 持續追蹤 OpenRouter 全球排行，觀察 OpenClaw 是否有反攻策略 |
| 下一個版本釋出 | 依照近期節奏（約每 5-7 天一版），預計 2026-05-22~25 前後 |

---

*報告生成時間：2026-05-21 UTC*
*資料來源索引*

- [OpenClaw 官方 X 帳號](https://x.com/openclaw)
- [GitHub openclaw/openclaw](https://github.com/openclaw/openclaw)
- [GitHub Issues](https://github.com/openclaw/openclaw/issues)
- [GitHub Pull Requests](https://github.com/openclaw/openclaw/pulls)
- [OpenClaw Release v2026.5.5](https://github.com/openclaw/openclaw/releases/tag/v2026.5.5)
- [Best LLM for OpenClaw — DEV.to](https://dev.to/matthewrevell/best-llm-for-openclaw-gemini-31-pro-vs-gpt-55-vs-claude-opus-47-2026-3na4)
- [OpenClaw LLM Setup — LaoZhang AI Blog](https://blog.laozhang.ai/en/posts/openclaw-llm-setup)
- [OpenClaw April 2026: Model-Agnostic Provider Manifest — MindStudio](https://www.mindstudio.ai/blog/openclaw-april-2026-model-agnostic-provider-manifest)
- [GitHub Issue #32828](https://github.com/openclaw/openclaw/issues/32828)
- [GitHub Issue #23996](https://github.com/openclaw/openclaw/issues/23996)
- [AnswerOverflow：Claude OAuth 429 討論](https://www.answeroverflow.com/m/1478788645472436264)
- [What's New in OpenClaw May 2026 — Blink Blog](https://blink.new/blog/openclaw-whats-new-may-2026)
- [OpenClaw 安全路線圖](https://blockchain.news/news/openclaw-security-roadmap-2026)
- [OpenClaw vs Hermes-Agent — MarkTechPost](https://www.marktechpost.com/2026/05/10/openclaw-vs-hermes-agent-why-nous-researchs-self-improving-agent-now-leads-openrouters-global-rankings/)
- [Teknium X 推文](https://x.com/Teknium/status/2055125356554899865)
