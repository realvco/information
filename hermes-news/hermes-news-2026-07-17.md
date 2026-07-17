# Hermes-Agent 每日情報 — 2026-07-17

## 1. 今日重點摘要

1. **Hermes Cloud 正式進入 Preview**：Nous Research 近日（7 月中旬）推出 Hermes Cloud，透過 Nous Portal 兩次點擊即可部署，選好模型與伺服器規格後 60 秒內上線；閒置時自動 scale-to-zero，僅在工作時計費。目前仍為 Preview 狀態。

2. **GPT-5.6 正式支援（2026-07-09）**：Nous Research 官方在 X 平台宣布 GPT-5.6 已加入 Hermes Agent 支援清單，並可透過 Nous Portal 存取，是近一週最重要的模型更新。

3. **rabbitOS 預裝 Hermes Agent（2026-07-10）**：rabbitOS 2.3 更新後，Hermes Agent 已預裝於 r1 裝置，使用者毋需另行安裝即可從硬體端發起 agent 工作流。

4. **v0.18.1 / v0.18.2 修補版（2026-07-07）**：The Judgment Release（v0.18.0，2026-07-01）發布後一週，團隊緊跟釋出 v0.18.1（約 660 個 PR 的修復與強化）與 v0.18.2（修復 WhatsApp bridge 依賴問題），完整 changelog 將隨 v0.19.0 一同發布。

5. **網頁爬取速度大幅提升**：Nous Research 宣布 Hermes Agent 爬取網頁速度提升 60 倍、費用降低至原來 1/49，透過讓抓取後端直接傳遞乾淨內容給 agent，並在本地分頁大型網頁，減少重複處理步驟。

6. **Hackathon 結果公布**：與 NVIDIA 和 Stripe 合辦的「Hermes Agent Accelerated Business Hackathon」得獎者已公布，主題為能自主運作、收付款、執行真實業務的 agent；官方表示得獎作品展示了 Hermes Agent 的核心使用願景。

7. **v0.18.0 歷史性達成零 P0/P1 未解問題**：The Judgment Release 截至 release 時，整個 repository 的 P0/P1 未解 issue 數量歸零——共關閉 3 個 P0 issue、8 個 P0 PR、493 個 P1 issue、188 個 P1 PR，約 700 個最高優先項目在 12 天內清空。

8. **Hermes Desktop 已結束 GTC 展示階段、進入公開 Preview**：先前在 Jensen Huang GTC 主題演講中 demo 的 Hermes Desktop（原生桌面應用）現已開放 Public Preview。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）
- **推薦搭配**：Nous Portal 支援 300+ 前沿 agentic 模型，Claude Sonnet 系列與 Opus 系列均涵蓋其中，整合帳單方便管理。
- **費率提醒**：Claude Sonnet 5 限時優惠 $2/$10 per 1M tokens（至 2026-08-31）。透過 Nous Portal 訂閱可統一計費，但若直接使用 Anthropic API Key，需在 `hermes auth` 中設定 fallback provider chain，以避免達到 rate limit 後工作流中斷。
- **踩坑**：當 Anthropic API 回傳帶有 `retry-after` 與 `anthropic-ratelimit-*` header 時，Hermes Agent 能自動觸發 provider fallback；但若 response body 格式不標準，自動切換可能失效，建議測試階段手動驗證 fallback 路徑。
- **來源**：[AI Providers | Hermes Agent docs](https://hermes-agent.nousresearch.com/docs/integrations/providers) | [Hermes Agent model provider costs & rate limits](https://hermes-agent.ai/blog/hermes-agent-model-provider-costs-rate-limits)

### GPT（OpenAI）
- GPT-5.6 本週剛加入支援，社群尚無大量實測回報，但官方宣布可透過 Nous Portal 直接存取，無需另行設定 OpenAI API key。
- 先前版本的 GPT-OSS 120B 在 Groq 免費層每日上限 200K tokens，適合輕量任務但不適合高吞吐量場景。
- **來源**：[@NousResearch X 貼文（GPT-5.6 支援）](https://x.com/NousResearch/status/2075270903458373640)

### Groq（免費層限制）
- Llama 4 Scout：30 RPM、500K tokens/日；Llama 3.3 70B：100K tokens/日；GPT-OSS 120B：200K tokens/日。
- 多 channel 並行（Discord + Telegram + iMessage）場景下，Groq 免費層很快打滿，建議搭配 Nous Portal 或自托管模型。
- **來源**：[Hermes Agent Costs: Prices, Limits & Scenarios](https://www.gradually.ai/en/hermes-agent-costs/)

### Gemini（Google）
- Google AI Studio 免費層：15 RPM / 1,500 RPD，適合低頻輔助任務。
- 在 Hermes Agent 工作流中，Gemini 主要被用於多模態輸入（圖像分析、長文件閱讀），與 Claude 互補。
- **來源**：[AI Providers | Hermes Agent docs](https://hermes-agent.nousresearch.com/docs/integrations/providers)

### Nous Portal 整合（統一訂閱）
- 免費層：$0，Plus 層：$20/月，包含 300+ 模型、網頁搜尋、圖像生成、TTS、瀏覽器自動化四大工具。
- 雲端定價方案（$20–$200/月 區間）未在公開文件中詳列，需進入 portal 確認。
- **來源**：[Nous Portal pricing](https://www.autolearningagents.com/hermes-agent/hermes-pricing)

---

## 3. 社群反應與討論亮點

- **官方 X 帳號近期密集更新**：@NousResearch 在 7 月 9–17 日期間連續發文，涵蓋 GPT-5.6 支援、rabbitOS 預裝、Hermes Cloud 上線、Hackathon 結果等重要公告，是本週最活躍的官方社群頻道。

- **r/LocalLLaMA**：社群討論 Hermes Agent 主要在此子版，焦點為模型效能比較、工具呼叫可靠性、自托管成本。本週隨 Hermes Cloud 發布，有使用者討論「自托管 vs. Nous Cloud」的 trade-off，普遍認為 Cloud 適合不想維護 infra 的個人用戶，自托管則對資料隱私要求高的組織更合適。

- **Discord（Nous Research 官方）**：官方 Discord 的 agent 頻道持續有使用者分享設定、踩坑回報與模型選擇討論。6 月統計顯示社群有 1,175 個討論串，8,030 則訊息，活躍度穩定上升。

- **KOL 觀點**：@shannholmberg 在 X 平台對 Hermes Cloud 有清晰的功能摘要貼文，被社群廣泛引用；KD Agentic（Medium）發表 v0.18 深度分析：「AI Agents Now Verify, Not Just Claim」，聚焦於 Judgment Release 的架構哲學——先確認再行動。

---

## 4. 值得追蹤的後續議題

| 議題 | 說明 |
|------|------|
| **v0.19.0 完整 changelog** | v0.18.1–0.18.2 的詳細說明將隨 0.19.0 一起發布，值得關注架構層面變動 |
| **GPT-5.6 實際穩定性** | 剛加入支援，長期工具呼叫可靠性與 token 計費行為尚待社群回報 |
| **Hermes Cloud 正式版定價** | 目前 Preview 階段定價不透明，正式定價公布後需評估是否影響使用策略 |
| **rabbitOS + Hermes 整合深度** | 預裝後的實際體驗（離線能力、本地模型支援）尚無大量回報 |
| **Mixture-of-Agents 生態**（v0.18.0 新特性） | named ensemble 功能開放後，社群是否會分享高效的 MoA 設定模板 |
| **Hermes Desktop 公開 Preview 反饋** | 桌面應用穩定性與 Windows / Linux 支援情況值得持續追蹤 |

---

*資料來源：[GitHub NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent/releases) | [@NousResearch on X](https://x.com/NousResearch) | [hermes-agent.ai blog](https://hermes-agent.ai/blog/hermes-agent-model-provider-costs-rate-limits) | [Hermes Agent docs](https://hermes-agent.nousresearch.com/docs/integrations/providers) | [gradually.ai](https://www.gradually.ai/en/hermes-agent-costs/) | [Medium KD Agentic](https://medium.com/kd-agentic/hermes-v0-18-ai-agents-now-verify-not-just-claim-cb542dd0e33b)*
