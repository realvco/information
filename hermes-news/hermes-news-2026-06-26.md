# Hermes-Agent 每日情報｜2026-06-26

> 搜尋時間範圍：過去 24 小時（UTC 2026-06-25 ～ 2026-06-26）

---

## 1. 今日重點摘要

1. **v0.17.0「The Reach Release」（6 月 19 日，持續社群發酵）**：這是 Hermes-Agent 迄今最大版本，涵蓋 ~1,475 commits、~800 merged PRs、1,693 個更動檔案、23 萬 + 行插入，300+ issues 關閉，245 位社群貢獻者參與，在過去幾天仍持續引發社群討論。

2. **iMessage 整合上線**：透過 Photon Spectrum 管理的號碼池，執行 `hermes photon login` 並以裝置碼驗證後，Hermes 即可收發 iMessage，**不需要 Mac relay 或 BlueBubbles bridge**，是社群長期期待的功能。

3. **WhatsApp Business Cloud API 支援**：改用 Meta 官方 WhatsApp Business Cloud API，不再需要 QR 碼掃描或第三方 bridge process，需持有 Business API 憑證。

4. **Raft 代理網路整合**：Raft 作為新的 Gateway Channel 加入，隱私設計採 privacy-by-contract，wake payload 僅攜帶 metadata，不暴露訊息內容。

5. **非同步 Subagents（6 月 15 日公告）**：`delegate_task(background=true)` 讓子代理在背景執行並立即回傳 handle，主對話不再被阻塞，已被 MarkTechPost 等媒體報導。

6. **`/learn` 指令（6 月 23 日 Teknium 宣布）**：使用者可傳入任意來源（URL、文件、過往對話記錄等），Hermes 自動從 0 建立可驗證、可重用的 Skill，並在建立後實地測試。

7. **已知回退（Reverted）**：`html-artifact` skill 與 cron 的 per-job profile 支援在正式發布前被拉回，未包含在 v0.17.0 中，待後續版本補上。

8. **Dashboard 與 Skills Hub 翻新**：使用者個人資料建立功能、安全登入、Skills Hub 瀏覽器重新設計，以及記憶體工具的大幅升級都在此版本一起落地。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）

- **支援版本**：Claude Opus 4.6、Sonnet 4.6、Haiku 4.5，可透過直接 Anthropic API 或 OpenRouter 接入。
- **OAuth 憑證管理**：選擇 `hermes model` Anthropic OAuth 時，Hermes 優先使用 Claude Code 自身的憑證儲存，而非複製 token 至 `~/.hermes/.env`，保持可自動刷新的狀態，降低 auth 失效風險。
- **新增 claude-fable-5**：v0.17.0 provider 清單中新增 `anthropic/claude-fable-5`，使用者可直接在 model picker 中選用。
- **效能評比**：Claude Sonnet 4.6（$3/$15 per million tokens）是 reasoning 品質與工具呼叫（tool calling）的首選推薦，社群普遍認為在需要複雜推理的任務中優於 GPT-4.1。

### GPT（OpenAI）

- **Responses API 自動路由**：GPT-5+ 系列（gpt-5-mini 除外）自動使用 Responses API；GPT-4o 及更舊模型維持 Chat Completions，切換模型時需注意 API 行為差異。
- **Copilot API 支援**：可透過 Copilot API 路由 GPT-5.x，讓持有 Copilot 訂閱的用戶存取 GPT-5.x、Claude、Gemini 等多個模型。
- **中階選擇**：GPT-4.1（$2/$8 per million tokens）被定位為工具呼叫可靠的中價位選項，適合預算敏感場景。

### Grok / xAI

- **Grok Composer 整合**：v0.17.0 新增 `grok-composer-2.5-fast`，Cursor Composer 模型可透過 xAI Grok 訂閱存取。

### 其他 Provider

- **300+ 模型支援**：Hermes 架構為模型不可知論（model-agnostic），支援 DeepSeek、Kimi、Gemini、Mistral、Ollama 等，`hermes model` 一條指令切換。
- **z-ai/glm-5.2**：Zhipu GLM 5.2 納入 v0.17.0 官方 provider 清單。

**社群討論摘要**：
- r/LocalLLaMA 的討論主要圍繞 Hermes 自家模型（Hermes 3/4）在本地部署與雲端的工具呼叫效能比較。
- 社群普遍結論：複雜推理任務用 Claude/GPT，一般工具呼叫任務用 Hermes 本家模型效益最佳。

**參考來源**：
- [Hermes Agent v0.17.0 Release — GitHub](https://github.com/NousResearch/hermes-agent/releases/tag/v2026.6.19)
- [AI Providers — Hermes Agent Docs](https://hermes-agent.nousresearch.com/docs/integrations/providers)
- [Best Hermes Agent Model 2026 — Remote OpenClaw](https://www.remoteopenclaw.com/blog/best-models-for-hermes-agent)
- [Hermes Agent Desktop Free With Local LLMs — Guide](https://www.kunalganglani.com/blog/hermes-agent-desktop-free-local-llm)

---

## 3. 社群反應與討論亮點

- **Nous Research Discord**：Teknium 等核心開發者在 Discord 的 agent 頻道最為活躍，社群普遍認為 Discord 的討論即時性與深度優於 Reddit，新手建議先潛水一週。
- **Teknium 的 `/learn` 宣告（6 月 23 日 X.com）**：發文引發廣泛轉推，示範了從 URL 與文件自動結晶成可測試 Skill 的完整流程，被評為「自我改進能力的具體落地」。
- **Nous Research 官方 X 帳號**：Hermes Desktop 在 Jensen Huang GTC 主題演講中首次展示的回顧貼文持續累積互動，公開預覽版上線後用戶回饋以「設定流程順暢」為主要正面評價。
- **MarkTechPost 報導非同步 Subagents**：主流 AI 媒體對此功能的報導帶動更多開發者關注 Hermes-Agent，認為此功能填補了長任務代理在 UX 上的關鍵空白。
- **r/selfhosted 討論**：聚焦於自架成本與運維挑戰，批評傾向於「operational concerns」（設定複雜度、gateway 維護）而非功能本身。

**參考來源**：
- [Teknium on X — /learn 宣告](https://x.com/Teknium/status/2069527900723073235)
- [Nous Research on X — Hermes Desktop](https://x.com/NousResearch/status/2061843507417944552)
- [Hermes Agent Adds Async Subagents — MarkTechPost](https://www.marktechpost.com/2026/06/16/hermes-agent-adds-asynchronous-subagents-so-delegated-work-no-longer-blocks-the-parent-chat/)
- [Hermes Agent Reddit & Community — Where to Find](https://openclawlaunch.com/blog/hermes-agent-reddit-discussion)

---

## 4. 值得追蹤的後續議題

1. **html-artifact Skill 回歸時程**：v0.17.0 中被拉回的 html-artifact skill 何時重新發布，是社群關注焦點，適合訂閱 GitHub release 通知。
2. **cron per-job profile 支援**：另一個被回退的功能，對依賴複雜排程任務的使用者影響顯著，需追蹤後續 patch。
3. **iMessage Photon 穩定性**：全新整合的 iMessage channel 尚無足夠生產環境回報，穩定性與訊息遺失率需觀察。
4. **`/learn` 技能品質評估**：自動從來源建立 Skill 的功能剛宣布，社群實測的 Skill 準確率與可重現性報告將在未來幾天陸續出現。
5. **WhatsApp Business API 門檻討論**：需要 Meta Business API 憑證使入門門檻較舊版 QR 掃描方案提高，預期社群會整理申請流程指南與常見卡關點。
6. **Raft 代理網路生態**：Raft 作為新型 agent 間通訊層的定位有別於傳統 messaging gateway，其在多代理協作場景的實際表現值得持續追蹤。
