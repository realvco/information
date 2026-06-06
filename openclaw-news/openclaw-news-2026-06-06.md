# OpenClaw 每日情報 — 2026-06-06

> 搜尋範圍：過去 24 小時（2026-06-05 至 2026-06-06 UTC）

---

## 1. 今日重點摘要

1. **2026.6.2 Beta 發布**：OpenClaw 在今日週期內維持 2026.6.2 beta 列車滾動發布；穩定版 2026.6.1 約在 2 天前（6/4）正式落地。重點方向為插件安裝安全、頻道穩定性與 Skill Workshop 控制介面。
2. **插件安裝策略全面重構**：棄用舊版危險代碼掃描路徑，改採「操作員安裝策略（operator install policy）」，涵蓋 package、archive、source、upload 及 Marketplace 安裝流程，並更新 `openclaw doctor`、CLI、ClawHub 的說明頁面。
3. **Tokenjuice 與 Copilot 外部化**：Tokenjuice 正式以 `@openclaw/tokenjuice` 插件形式發布至 npm 及 ClawHub；GitHub Copilot Agent Runtime 拆成 `@openclaw/copilot` 獨立插件，避免核心臃腫。
4. **Skill Workshop 控制 UI 落地**：Control UI 新增 Dashboard 概覽、今日提案（proposal today）視圖、修訂對話框、檔案預覽 Modal、可搜尋預覽檔案清單，以及本地化字串支援；技能在進入生產工作流前須通過審核。
5. **頻道與行動推播更穩定**：Telegram、Feishu、Discord、WhatsApp 及外發通道修補了重複 transcript 鏡像、Telegram 管理回寫、串流預覽核准白名單、Discord 語音錯誤等問題；iOS 即時 Talk 也獲得修復。
6. **CLI / Gateway 中斷恢復改善**：Agent 與 CLI-backed 執行環境現在能更乾淨地從工具調用中斷、過期 session 綁定、壓縮交接（compaction handoff）及媒體重試中自動恢復。避免 `openclaw agents add` 時觸發不必要的 provider catalog 驗證。
7. **安全加固**：拒絕損壞的 shell snapshot、可疑 gateway 啟動設定、格式異常的數值限制、過大審計回應、不安全 exec precheck 環境、無效 SQLite scaffold 等，進一步縮小攻擊面。
8. **GitHub Issue #32828 — 虛假 429 問題**：社群回報即使 API 完全正常，所有模型仍觸發「API rate limit reached」的假陽性錯誤，根因指向 OAuth token 刷新失敗而非真實配額用盡；維護者建議先執行 `openclaw doctor --fix`。
9. **npm 套件持續版本更新**：`openclaw` npm 套件持續跟隨主線發版，確認 Node.js 端集成路徑可用。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）

- **推薦配置**：Sonnet 做日常 Agent 工作、Haiku 處理高量低成本任務、Opus 負責最困難推理。Claude 在企業部署中以「工具調用最可靠」知名，MCP 原生支援加分。
- **OAuth 假 429 坑**：多位用戶（Discord 及 answeroverflow.com 討論串）回報 Claude OAuth 刷新後持續出現「API rate limit reached」，實際並非配額問題。暫行解法：重新執行 `openclaw doctor --fix` 或手動重設 OAuth token。
  - 來源：[answeroverflow.com — Openclaw with claude oauth rate limit](https://www.answeroverflow.com/m/1478788645472436264)
- **費用控制**：透過 TeamoRouter 將常規任務（檔案讀取、簡單編輯、Shell 指令）路由到便宜模型，可降低 Anthropic 配額使用量 50–65%。
  - 來源：[DEV Community — Claude Code rate limit workaround 2026](https://dev.to/sophiaashi/claude-code-rate-limit-on-max-plan-the-workaround-developers-are-actually-using-in-2026-16g2)

### GPT（OpenAI）

- **GPT-5.5 適合自主 Agent**：Terminal-Bench 2.0 等評測領先，但建議單次調用控制在 128K tokens 以內，否則效能下滑明顯。
- 來源：[DEV Community — Best LLM for OpenClaw 2026](https://dev.to/matthewrevell/best-llm-for-openclaw-gemini-31-pro-vs-gpt-55-vs-claude-opus-47-2026-3na4)

### Gemini（Google）

- **Gemini 3.1 Pro 免費額度最優**：每分鐘 60 次請求、每日 1,000 次請求，適合大規模代碼審查與多模態任務，被社群稱為「2026 免費額度英雄」。
- 來源：[DEV Community — Best LLM for OpenClaw 2026](https://dev.to/matthewrevell/best-llm-for-openclaw-gemini-31-pro-vs-gpt-55-vs-claude-opus-47-2026-3na4)

### OpenRouter / 本地模型

- OpenClaw 文件確認支援 OpenRouter 做為 provider 代理；Ollama 可用於全本地推論，保護資料隱私。
- 429 排查指南：[LaoZhang AI Blog — OpenClaw Rate Limit 429 Guide](https://blog.laozhang.ai/en/posts/openclaw-rate-limit-exceeded-429)

---

## 3. 社群反應與討論亮點

- **「粗糙的一週」官方部落格文章**（[OpenClaw Blog — Rough Week](https://openclaw.ai/blog/openclaw-rough-week)）：官方罕見承認近期發版節奏下部分功能不穩，讓社群感到驚訝，也有聲音讚許透明度。
- **連續五天更新熱話**：有報導指 OpenClaw 曾在某 24 小時內連推兩次更新，部分用戶抱怨測試跟不上發版速度。（[36kr EN](https://eu.36kr.com/en/p/3763230073815814)）
- **Skill Workshop 備受期待**：開發者社群對「技能在生產前需人工審核」機制看法分歧——安全性支持者點讚，自動化派嫌程序繁瑣。
- **r/OpenClaw / r/Clawdbot**：兩個 subreddit 由社群義工維護，偶發的 Weekly Claw 活動是主要討論節點；近期版主在招募新人手。
- **API 費用異常討論**：Facebook 群組「OpenClaw Users」出現「如何繞過 / 重設 rate limit」討論，顯示仍有用戶在反覆觸發 429 錯誤。（[Facebook Group post](https://www.facebook.com/groups/openclawusers/posts/679833391845604/)）

---

## 4. 值得追蹤的後續議題

| 議題 | 狀態 | 追蹤建議 |
|------|------|---------|
| GitHub Issue #32828 — 虛假 429 / OAuth 刷新失敗 | 開放中 | 關注官方修復 PR，受影響者先用 `doctor --fix` 緩解 |
| Skill Workshop 生產審核流程實際體驗 | 新功能磨合期 | 追蹤社群回饋，留意是否有 bypass 路徑或白名單設計 |
| `@openclaw/tokenjuice` 插件首發穩定性 | 剛外部化 | 關注 npm 版本與 ClawHub 相容性問題 |
| 2026.6.2 Beta → 穩定版升級時間線 | beta 中 | 訂閱 [Releasebot](https://releasebot.io/updates/openclaw) 獲得第一手通知 |
| Gemini 3.1 Pro 免費額度長期可持續性 | 觀察中 | Google 政策隨時可能調整，備好 fallback 設定 |

---

*報告產生時間：2026-06-06 UTC｜資料來源均來自公開網路搜尋，不保證即時完整性。*
