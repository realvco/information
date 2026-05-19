# OpenClaw 每日情報｜2026-05-19

> 搜尋時間範圍：過去 24 小時（UTC 2026-05-18 ～ 2026-05-19）

---

## 1. 今日重點摘要

1. **Beta 版本發佈**：`v2026.5.19-beta.1` 於 2026-05-18 22:58 UTC 發布，主要更新為修復預設邊界重構邏輯，以及明確化 Plugin SDK / API 的棄用路徑。
2. **社群票選最佳模型**：截至 2026-05-18，社群票選 OpenClaw 最適搭配模型依序為：Kimi K2.5、Claude Opus 4.6、GLM 4.7，Kimi K2.5 首次奪冠，反映社群對高 CP 值模型的偏好。
3. **Anthropic 封鎖訂閱層**：Anthropic 已封鎖 OpenClaw 透過訂閱方案使用 Claude，現在只能走 API 付費（Input $5 / Output $25 per 1M tokens），促使部分用戶轉向其他模型或 OpenRouter。
4. **高優先議題追蹤**：GitHub 上近 24 小時新增高優先 Issues：#83657（2026-05-18，bottenbenny 提報）、#83666（2026-05-18，24Play-Artur-Chebaniuk 提報），#83474 涉及 upstream main 的路徑問題仍待處理。
5. **主倉庫規模**：openclaw/openclaw 目前擁有 77,366 stars，3,557 個 Pull Requests，社群活躍度持續上升。
6. **Kimi K2.5 via OpenRouter 最佳實踐**：社群有多篇實作文章記錄在 VPS 上搭配 OpenRouter 運行 Kimi K2.5 的流程，包含 `openclaw.json` 中以 `openrouter/moonshotai/kimi-k2.5` 格式指定模型的設定方式。
7. **OpenClaw-RL 子專案**：`Gen-Verse/OpenClaw-RL` 持續維護中，允許透過對話訓練任意 agent，為生態系增加強化學習研究向的分支。
8. **安全邊界修復**：最新 beta 版本的 PR 風險分類包含 `auth-provider`、`security-boundary`、`session-state`、`message-delivery` 等類別，顯示開發團隊持續強化安全性管控。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）

- **訂閱層封鎖**：Anthropic 已正式封鎖 OpenClaw 使用 Claude Pro 訂閱方案，必須使用 API Key。目前 API 費率：Input $5、Output $25（per 1M tokens）。
- **長上下文優勢**：Claude 在多步驟工具呼叫（multi-step tool use）與長上下文任務中仍被評為優於 GPT-4o，尤其在 prompt injection 抵抗性上表現較佳。
- **實戰案例**：有開發者成功整合 Claude + ChatGPT + Telegram，透過 Telegram 訊息觸發 Claude Code 更新並重新部署 Vercel 網站。
- 來源：[Medium - I Wired OpenClaw to Claude, ChatGPT & Telegram](https://medium.com/@mohit15856/i-wired-openclaw-to-claude-chatgpt-telegram-heres-what-actually-works-8278906edccc)

### GPT（OpenAI）

- GPT-4o 在簡單任務與程式碼生成速度上仍有優勢，但在複雜多步驟 agent 工作流中略遜於 Claude。
- 來源：[DEV Community - Best LLM for OpenClaw](https://dev.to/matthewrevell/best-llm-for-openclaw-gemini-31-pro-vs-gpt-55-vs-claude-opus-47-2026-3na4)

### Kimi K2.5（Moonshot AI）via OpenRouter

- 目前社群票選第一名，主因是相對低廉的費用與 OpenRouter 串接的便利性。
- 設定方式：`openclaw.json` 中指定 `openrouter/moonshotai/kimi-k2.5`，需先設定 OpenRouter API Key。
- 有使用者反映在 OpenClaw v2.3 以上版本支援直接選擇 Kimi K2.5（Mainland China / International 分開提供）。
- 踩坑記錄：X.com 用戶 @code_rams 分享：「想把每月 AI 花費壓在 $10 以內，嘗試 Kimi 2.5 + OpenRouter 免費模型，但 Kimi 2.5 需要最新版 OpenClaw 才能正常運作。」
- 來源：[OpenRouter 官方文件 - OpenClaw 整合](https://openrouter.ai/docs/guides/guides/openclaw-integration)、[X.com @code_rams](https://x.com/code_rams/status/2018070016864792693)

### DeepSeek

- 支援 OpenAI-compatible 端點，在 `openclaw.json` 中設定 provider 並加上模型前綴即可使用。
- 目前社群排名在 Kimi K2.5 之後，但在中文語境任務中仍有支持者。
- 來源：[Towards Data Science - How to Run OpenClaw with Open-Source Models](https://towardsdatascience.com/how-to-run-openclaw-with-open-source-models/)

---

## 3. 社群反應與討論亮點

- **費用焦慮**：Anthropic 封鎖訂閱層後，社群對 Claude API 費用討論熱度上升，不少用戶開始評估切換到 Kimi K2.5 或 DeepSeek via OpenRouter 以降低成本。
- **社群規模比較**：根據對 Reddit 25 個討論串（逾 1,300 則留言）的分析，約 35% 的用戶即使面對其缺陷也仍選擇留在 OpenClaw，主因是其整合數量（50+ 服務）與技能生態系在同類工具中最為完整。
- **安全性疑慮**：社群提醒，若將 OpenClaw 部署在伺服器上，需自行審查預設的 sandboxing 與 approval flow 設定，官方並未假設生產環境已充分強化（hardened）。
- **OpenClaw-RL 關注**：強化學習子專案 OpenClaw-RL 受到部分研究者關注，被視為未來 agent 自我優化的潛在方向。

---

## 4. 值得追蹤的後續議題

| 議題 | 說明 |
|------|------|
| GitHub Issue #83657、#83666 | 2026-05-18 新增的高優先問題，細節待官方回應 |
| `v2026.5.19-beta.1` 穩定版發布進度 | Beta 版已出，正式版時程未確認 |
| Anthropic API 費率對用戶遷移的影響 | 封鎖訂閱層後，Kimi K2.5 / OpenRouter 用量是否持續增長 |
| Plugin SDK 棄用路徑 | 最新 beta 明確化棄用路徑，開發者需確認自有 plugin 是否受影響 |
| OpenClaw-RL 進展 | 強化學習訓練 agent 的實際效果與社群採用率 |
| GPT-5.5 / Claude Opus 4.7 實測比較 | 社群正在進行多模型 benchmark，結果值得關注 |

---

*本報告依據公開搜尋結果彙整，資訊截至 2026-05-19 UTC。如有遺漏或錯誤，歡迎回報修正。*
