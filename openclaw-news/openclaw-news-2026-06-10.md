# OpenClaw 每日情報 — 2026-06-10

> 搜尋時間範圍：過去 24 小時（2026-06-09 ～ 2026-06-10 UTC）

---

## 1. 今日重點摘要

1. **2026.6.5 穩定版進入發布軌道**：npm latest 仍停在 2026.6.1，但 GitHub prerelease 已推進至 v2026.6.5-beta.2，月份補丁版號改採 YYYY.M.PATCH 格式，6 月地板版本鎖定於 2026.6.5。
2. **Skill Workshop 正式上線**：可將 agent 完成的工作轉換為可複用技能（Skill），提案先以 PROPOSAL.md 保持 pending 狀態，審核通過後才生效，支援 Chat、UI、CLI、Channel、Gateway 等所有介面。
3. **Plugin 安裝安全政策調整**：改用 operator install policy 取代舊版 dangerous-code scanner，安裝介面（ClawHub、CLI、marketplace）更清楚呈現套件來源，降低惡意插件風險。
4. **Agent/CLI 恢復機制強化**：中斷的工具呼叫、過時 session binding、compaction handoff 以及媒體重傳重試，現在恢復更乾淨；Telegram、WhatsApp、iMessage、Slack、Discord、Teams、Google Chat、Google Meet 與 iOS realtime Talk 傳送穩定度提升。
5. **QQBot 隱藏模型 reasoning tag**：QQBot 現在在傳送前會過濾 `<thinking>` 等推理標籤，僅保留最終答案，避免內部推理過程外洩給終端使用者。
6. **session-metadata SQLite 遷移延期**：原定於 2026.6.5 beta 執行的 SQLite 遷移因風險評估未通過而延後，目前仍維持 JSON 後端，遷移繼續在 main 分支開發中。
7. **6 月 9 日開發重點**：memory instructions 顯式化、compaction 所有權歸屬修正、update repair 可續做（resumable）、channel 失敗處理優化，以及 provider schema 清理。
8. **6 月 9 日新增 GitHub Issues**：#91643（P3 低優先）、#91645（需 maintainer 評審與產品決策）、#91638、#91635，共 4 筆，顯示社群回報仍相當活躍。
9. **OpenClaw-RL 子專案持續發展**：`Gen-Verse/OpenClaw-RL` 主打「只需對話即可訓練任意 agent」，定位與主倉庫互補，吸引 RL 研究者關注。

---

## 2. LLM 串接實戰回報

### Anthropic / Claude

- **訂閱存取封鎖（2026-04-04）**：Anthropic 自 4 月 4 日起封鎖 Claude Pro / Max 訂閱授權用於第三方 agentic 工具（包括 OpenClaw），改為強制使用 API Key。
  > 來源：[DEV Community — Anthropic kills Claude subscription access for third-party tools like OpenClaw](https://dev.to/mcrolly/anthropic-kills-claude-subscription-access-for-third-party-tools-like-openclaw-what-it-means-for-3ipc)
- **Rate Limit 踩坑**：使用 setup-token（舊訂閱 OAuth）的 request 不顯示在 Anthropic Console 用量儀表板，因此「本月使用量 0」仍可能持續收到 `429 rate_limit_error`，Claude 瀏覽器正常運作不代表 API 額度也正常。
  > 來源：[AnswerOverflow — OpenClaw with claude oauth keeps giving rate limit](https://www.answeroverflow.com/m/1478788645472436264)
- **OAuth Token 故障排除**：舊版靜態 OAuth token 在帳單規則變更後出現 60 秒 timeout，請求掛起後失敗，解法為改用直接 API Key。
  > 來源：[Medium — Fix OpenClaw Missing Auth After Anthropic Changes](https://medium.com/@ulmeanuadrian/anthropic-just-cut-off-my-ai-agents-heres-how-i-fixed-it-in-20-minutes-741384d27080)
- **6 月 15 日計費政策再變**：2026-06-15 起，訂閱方案的 `claude -p` 用量將先扣 Agent SDK 月度額度，超出後才以標準 API 費率計費（如啟用 usage credits）。
  > 來源：[OpenClaw + Claude Code Costs 2026](https://www.shareuhack.com/en/posts/openclaw-claude-code-oauth-cost)

### GPT / OpenAI

- GPT-5.5（Codex）為自主 agent 最佳選擇，在 Terminal-Bench 2.0 等 agentic 基準測試領先，但需注意 context 限制為 128K tokens/call。

### Gemini

- Gemini 因大 context window、原生多模態（圖片、音訊、影片）以及 Google AI Studio 免費開發方案，被評為「值得認真考量」的後端選項，適合媒體豐富的工作流程。
  > 來源：[DEV Community — Using Gemini with OpenClaw](https://dev.to/matthewrevell/using-gemini-with-openclaw-setup-guide-real-use-cases-2i48)

### 模型切換相容性

- 2026 年 4 月 OpenClaw 推出 provider manifest，可在執行中的 workflow 無需重建即切換 LLM 後端（支援 GPT-5.5、Claude API、Gemini、DeepSeek、OpenRouter、Ollama、LM Studio、Gemma 4）。
  > 來源：[MindStudio — OpenClaw April 2026: 6 Model Providers You Can Now Swap at Runtime](https://www.mindstudio.ai/blog/openclaw-april-2026-model-agnostic-provider-manifest)

---

## 3. 社群反應與討論亮點

- **社群管理擴編**：OpenClaw 設有志願社群員工團隊，負責 r/OpenClaw 與 r/Clawdbot 兩個 subreddit 的版規執行與社群互動，並定期舉辦「Weekly Claw」線上活動（含講者協調與錄影）。
  > 來源：[GitHub — openclaw/community](https://github.com/openclaw/community)
- **claude-cli/* 路由問題討論**：社群回報 `claude-cli/claude-sonnet-4-6` 模型設定下，即使 `anthropic` provider token 存在，若缺少對應 backend 將 `claude-cli/*` 呼叫轉譯為實際 `claude` binary 執行，仍會出現 auth 失敗，引發多筆討論。
- **費用警覺**：多位使用者在 Anthropic 政策變更後分享新的費用試算，比較 API Key（按量）vs. Pro $20/月 vs. Max $200/月 的 OpenClaw 使用情境，社群正匯整最佳實踐指引。
  > 來源：[AnswerOverflow — OpenClaw rate limit error](https://www.answeroverflow.com/m/1480712801231048744)
- **OpenRouter 整合受歡迎**：OpenRouter 官方文件已收錄 OpenClaw 整合指引，被視為繞開各家原廠 rate limit 的可行備案。
  > 來源：[OpenRouter Docs — Integration with OpenClaw](https://openrouter.ai/docs/guides/guides/openclaw-integration)

---

## 4. 值得追蹤的後續議題

1. **session-metadata SQLite 遷移**：延期後仍在 main 分支持續開發，一旦合入 stable 將影響所有本地部署的資料庫結構，建議關注 migrate script 的向下相容設計。
2. **2026-06-15 計費政策切換**：Agent SDK 月度額度用盡後自動轉帳單 API 費率，可能造成非預期費用，使用 `claude -p` 的用戶應事先設定用量上限。
3. **OAuth ban 迴避問題**：社群有資源提到如何避免因過度使用 OAuth 路徑而被 Anthropic 封號，仍需觀察 Anthropic 後續執法力度。
  > 來源：[LearnOpenClaw — How to Eliminate OAuth Token Hell in OpenClaw](https://resources.learnopenclaw.ai/openclaw-claude-anthropic-avoid-getting-banned/)
4. **Skill Workshop 的審核機制成熟度**：PROPOSAL.md 驅動的審核流程剛上線，社群對於 skill 品管、版本衝突處理方式尚在摸索中。
5. **OpenClaw-RL 與主倉庫整合**：`Gen-Verse/OpenClaw-RL` 是否會被納入官方生態系仍不明，值得追蹤官方討論。
