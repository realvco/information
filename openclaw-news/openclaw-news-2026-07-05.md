# OpenClaw 每日情報 — 2026-07-05

> 涵蓋時段：2026-07-04 ～ 2026-07-05（UTC）

---

## 1. 今日重點摘要

1. **v2026.7.1-beta.1 搶先預覽（2026-07-02 釋出）**：加入 GPT-5.6 跨目錄、能力偵測、運行時選擇的全面支援；新增 `openclaw attach` 指令，可掛載到現有 Gateway session，讓 Codex 風格互動工作流更易於恢復與檢查。
2. **GitHub issue #99734 新開（2026-07-04）**：由用戶 RomneyDa 回報，ClawSweeper 自動標記多個分析標籤，目前為 P1 優先級，細節尚在調查中。
3. **Telegram Codex 配對大幅強化**：Telegram 現可透過 `/login` 指令啟動 Codex 配對、操控進行中的 Codex 執行，並在暫時 API 失敗後自動恢復最終回覆。
4. **事件驅動排程新增 `on-exit` 觸發**：新的排程類型可在監控指令退出時喚醒代理，讓 session 目標式執行可乾淨地脫離，為 CI/CD 整合提供更細緻的控制。
5. **iOS 26 視覺系統全面翻新**：原生 iOS app 採用 iOS 26 設計語言，Chat、Talk、設定與入門流程導覽均更清晰；Android 本地化也同步擴展。
6. **快速模式（Fast Mode）上線**：短對話自動切換快速模式，較長或 fallback 工作自動回歸正常模式，不會遺失可見狀態，整體使用者體感延遲顯著降低。
7. **Gemini 3.5 Flash 完整 1M token 上下文視窗修復**：先前選擇 Gemini 3.5 Flash 時會觸發 missing-model 或 prompt-size 拒絕錯誤，現已正確支援 1,048,576 token 上下文。
8. **v2026.6.11 穩定版可靠性修補**：修復跨 Telegram、WhatsApp、Matrix、Google Chat、iMessage、Feishu、Mattermost、WebChat 及終端 UI 的錯置回覆、卡死傳送、重連失敗、模型設定錯誤等問題，並強化管理員預設安全值。
9. **OpenRouter 整合文件更新**：官方文件補充 OpenClaw 透過 OpenRouter 使用 200+ 模型的設定方式，CLI 後端可用 `provider/model` 格式指定 `anthropic/claude-*` 或 `google/gemini-*`。

---

## 2. LLM 串接實戰回報

### Anthropic Claude — OAuth / 訂閱 token 被封問題
- **問題**：使用 Claude Max/Pro 訂閱 token（非 API Key）在 OpenClaw 中頻繁出現「API rate limit reached」，用戶在 AnswerOverflow 社群持續回報。
- **根本原因**：Anthropic 持續調整封鎖規則，明確不允許訂閱 token 用於第三方軟體；OAuth token 的速率限制遠低於直接 API Key。
- **建議做法**：改用 Anthropic 官方 API Key，並在設定中配置 fallback 模型。
- 來源：[AnswerOverflow 討論串](https://www.answeroverflow.com/m/1478788645472436264)

### 費用異常 — 無內建支出上限
- **問題**：OpenClaw 預設不提供 token 預算、每日消費上限或 retry 迴圈熔斷機制。用戶反映在正常使用中數分鐘內即消耗 100–300 萬 token，瞬間耗盡配額。
- **已知 Bug**：GitHub issue #5159（指數退避實作有誤，已關閉標記為 "not planned"），建議在應用層自行實作重試邏輯。
- **緩解方案**：啟用 fallback 模型鏈，避免單一 provider 耗盡；建議搭配 OpenRouter 的 cost cap 功能。
- 來源：[OpenClaw Token Usage & Cost Control Guide](https://www.getopenclaw.ai/help/token-usage-cost-management) ／ [Rate Limit 429 修復指南](https://blog.laozhang.ai/en/posts/openclaw-rate-limit-exceeded-429)

### Gemini — OpenAI 相容模式圖像識別 schema 不符
- **問題**：透過 OpenClaw 的 OpenAI 相容模式呼叫 Gemini 進行圖像識別時，tool schema 格式與 Gemini API 要求不符，導致失敗。
- **狀態**：官方文件已記錄此相容性問題，建議改用原生 Gemini 格式設定。
- 來源：[Gemini 圖像識別修復指南](https://help.apiyi.com/en/openclaw-gemini-image-recognition-fix-openai-compatible-mode-guide-en.html)

### Gemini freshness 過濾修復
- **修復**：使用 `freshness: "day"` 或 `pd` 的 Gemini 網路搜尋先前會觸發 provider 400 錯誤，現已修復；較寬泛的 freshness 選項與明確日期範圍保留更嚴格的過濾行為。

---

## 3. 社群反應與討論亮點

- **X.com 官方帳號（@openclaw）**自 4 月起維持每週多次釋出節奏，社群暱稱開發團隊為「cracked crustaceans（瘋狂龍蝦）」，氣氛活躍。
- 新貢獻者 [@joshavant](https://x.com/joshavant/status/2027177555191583175) 宣布成為 OpenClaw 維護者，首個重大功能「外部 secrets 管理」已合併。
- 多位社群成員（如 @iamlukethedev、@vincent_koc、@GenAI_Now）定期在 X.com 發布版本摘要，補充官方 release note 未覆蓋的使用情境解讀。
- 部落格文章（MindStudio、Petronella Tech）針對 4 月的 6 個 model provider 動態切換功能進行深度解析，認為 OpenClaw 正從「個人助理」轉型為「正式 Agentic Runtime」。
- [Releasebot.io](https://releasebot.io/updates/openclaw) 持續追蹤每次釋出的細項，為不看 GitHub changelog 的使用者提供友善摘要。

---

## 4. 值得追蹤的後續議題

| 議題 | 說明 |
|------|------|
| **v2026.7.1 正式版** | beta.1 已釋出，正式版時程未公告；GPT-5.6 runtime 穩定性待觀察 |
| **issue #99734** | P1 級別，7 月 4 日開出，後續修補進度值得追蹤 |
| **Anthropic 訂閱 token 政策** | Anthropic 持續收緊第三方使用限制，OpenClaw 是否會推出官方 workaround 尚未確定 |
| **issue #5159（指數退避）** | 已標記 "not planned"，但社群反彈聲音持續，是否重新評估值得關注 |
| **OpenRouter 整合文件完整性** | 官方文件剛更新，實際使用者反饋尚少，踩坑紀錄預計近期陸續出現 |
| **iOS 26 新版 app 穩定性** | 視覺系統大幅翻新後的 bug 回報期，建議持續觀察 iOS 用戶社群 |

---

*資料來源：[openclaw/openclaw GitHub](https://github.com/openclaw/openclaw) ／ [Releasebot OpenClaw](https://releasebot.io/updates/openclaw) ／ [OpenClaw 官方文件](https://docs.openclaw.ai/concepts/model-providers) ／ [DEV.to LLM 比較](https://dev.to/matthewrevell/best-llm-for-openclaw-gemini-31-pro-vs-gpt-55-vs-claude-opus-47-2026-3na4) ／ [AnswerOverflow](https://www.answeroverflow.com/m/1478788645472436264) ／ [@openclaw X.com](https://x.com/OpenClaw)*
