# Hermes-Agent 每日情報 — 2026-06-24

> 搜尋時間範圍：過去 24 小時（UTC 基準）
> 資料來源：GitHub、社群論壇、技術部落格

---

## 1. 今日重點摘要

1. **最新版本 v0.17.0（v2026.6.19，2026-06-19 發布）**：代號「Reach Release」，包含約 1,475 commits、800+ merged PR、1,693 個檔案異動，245 位社群貢獻者參與，關閉 300+ issues。

2. **iMessage 原生整合上線**：基於 Photon managed line pool 實作，無需 Mac relay 或 BlueBubbles bridge，隱私設計上 wake payload 只攜帶 metadata、不含訊息本體。

3. **Raft agent 網路整合**：內建 platform adapter，採隱私合約設計，Hermes 可接入去中心化 agent 網路。

4. **背景子代理（Background Subagents）**：`delegate_task(background=true)` 立即返回 handle，子代理在背景非同步執行，不阻塞主對話流程。

5. **桌面應用大幅強化（延續 v0.16.0）**：可重新綁定快捷鍵、原生 OS 通知（含分類開關）、子代理即時監看視窗、composer model selector（含 per-model preset）、RTL/bidi 文字自動方向、可調整大小的 VS Code 終端機面板、per-thread 草稿保留、支援安裝 VS Code Marketplace 主題。

6. **圖片生成支援編輯功能**：image generation 升級，可對生成圖片進行後續編輯操作。

7. **Cursor Composer 模型可透過 xAI Grok 訂閱存取**：擴展模型接入選項，使用 Grok 訂閱的使用者可在 Hermes 內使用 Cursor Composer 模型。

8. **過去 24 小時新發布/重大公告**：截至本報告截止時間（2026-06-24），GitHub releases 頁面最新正式版仍為 v0.17.0（2026-06-19）；尚未出現新標籤，v0.18.0 預計下週或更晚釋出。

9. **GitHub Stars 超過 57,000**：截至 2026 年 4 月已突破 57,000 stars，為開源 AI agent 框架中增速最快的專案之一。

---

## 2. LLM 串接實戰回報

### 多 Provider 支援概況
- Hermes-Agent 支援 200+ 模型，覆蓋：OpenRouter、OpenAI（GPT 系列）、Google AI Studio（Gemini）、Nous Portal（300+ 模型含私有模型）、Ollama（本地模型）、Kimi、MiniMax、xAI Grok 等。
- **Nous Portal** 整合後可用單一帳號計費，無需各家分別儲值。
- **GitHub Copilot 訂閱**：可透過 Copilot API 存取 GPT-5.x、Claude、Gemini，節省多帳號管理成本。
- 來源：[AI Providers | Hermes Agent - Nous Research](https://hermes-agent.nousresearch.com/docs/integrations/providers)

### Claude（Anthropic）
- 支援 Claude Opus 4.6、Sonnet 4.6、Haiku 4.5（透過 Anthropic API）。
- Sonnet 4.6 為社群最常推薦的預設選擇，在 agent 工作流中推理能力與 tool use 穩定性受認可。
- 來源：[Best Hermes Agent Model 2026: Claude vs DeepSeek | Remote OpenClaw](https://www.remoteopenclaw.com/blog/best-models-for-hermes-agent)

### GPT-5.5 / OpenAI Codex 踩坑
- **Issue #33075**：v0.14.0 中 openai-codex/gpt-5.5 子代理幾乎必定遭遇 `APIConnectionError` / TTFB timeout，同等提示在 Codex CLI 直接執行卻正常；問題被定位為 Hermes 的 subagent API 連線管理層，非模型本身問題。
- **Issue #26388**：Codex `usage_limit_reached` 429 錯誤時，pool credential 只輪換一次就停止重試，應繼續輪換整個 pool 後才宣告失敗。
- 來源：[Issue #33075](https://github.com/NousResearch/hermes-agent/issues/33075)、[Issue #26388](https://github.com/NousResearch/hermes-agent/issues/26388)

### Rate Limit / Token Limit 踩坑
- **Token limit 根因**：Hermes 每次請求推送大量 context（system message + 對話歷史 + tool schema + tool result），是觸發 token-per-minute 限制的主因。
- **8B 本地模型（GGUF）**：透過 Ollama 執行的 8B Q4_K_M / Q5_K_M 最高支援 8,192 token context；Hermes 3 70B 則可達 131,072 token。
- **429 → 402 的區別**：429 代表 rate limit，需 throttle 或換 credential pool；402 代表餘額耗盡，需加值或切換 fallback provider，兩者修復路徑不同。
- **HTTP 429 友善訊息**：Issue #1826 提案讓 429 顯示可設定 retry 的友善提示而非 traceback，已有實作草案但尚未合併。
- 來源：[Fix Hermes Agent 429 rate limit errors - LumaDock](https://lumadock.com/tutorials/hermes-agent-429-rate-limit-fix)、[Hermes Agent RateLimitExceeded: 3 Fixes That Work in Production | Markaicode](https://markaicode.com/errors/hermes-agent-rate-limit-exceeded-fix-production/)

### Auth 失效踩坑
- **Issue #7230**：primary provider 發生 `AuthError`（例如 OAuth token 過期 → HTTP 401）時，`config.yaml` 中設定的 `fallback_model` 不會被觸發；fallback 只在模型請求完成後出現錯誤才生效，credential 解析階段的 auth 錯誤被跳過。此為已確認 bug，尚未修復。
- **Credential Pool 機制**：正確做法是設定 `credential_pools`，讓 pool 輪換處理 auth 失效，而非仰賴 `fallback_model`。
- 來源：[Issue #7230 · NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent/issues/7230)、[Credential Pools | Hermes Agent Docs](https://hermes-agent.nousresearch.com/docs/user-guide/features/credential-pools)

---

## 3. 社群反應與討論亮點

- **Discord 多機器人衝突問題熱議**：Issue #25312 提案新增 `DISCORD_THREAD_REQUIRE_MENTION` 設定，讓多機器人共存的 thread 中，Hermes 只在被 @mention 時才回應，避免對其他機器人的訊息誤觸發，社群呼聲高。
- **Discord 論壇頻道 first message bug**：Issue #12496 指出在 Discord Forum Channel 建立新 thread 時，第一則訊息 Hermes 不會回應，需先 @mention 一次才會啟動；此行為讓新用戶摸不著頭緒，已標記為 bug。
- **v0.15.0 dashboard 無限重載熱修**：v0.15.0 發布後同日釋出 v0.15.1 緊急修復 loopback mode 的 dashboard 無限重載迴圈，社群對「重大版本同日出 hotfix」的品管流程有所批評。
- **原生桌面 App（v0.16.0）廣受好評**：The Surface Release 讓原本只能 CLI 使用的用戶獲得完整 GUI，拖放檔案、內聯 model picker、session 歸檔搜尋等功能被評為「終於補上」。
- **v0.17.0「Reach」受矚目**：iMessage 整合與 Raft 網路接入是討論焦點，部分用戶詢問企業版隱私合規性。

---

## 4. 值得追蹤的後續議題

1. **Issue #7230 修復時程**：`fallback_model` 在 auth 失效時不觸發的 bug，影響生產穩定性，值得追蹤 PR 進度。
2. **Issue #25312 合併進度**：`DISCORD_THREAD_REQUIRE_MENTION` 設定項目對多機器人環境用戶至關重要。
3. **Issue #33075 GPT-5.5 subagent 穩定性**：若 GPT-5.5 成為主流選擇，此 TTFB timeout 問題需盡快修復。
4. **v0.18.0 功能預告**：v0.17.0 release notes 是否提及下版本 roadmap，可關注 GitHub 討論串。
5. **Nous Portal 定價透明度**：300+ 模型整合後費用結構複雜，社群對計費明細的討論持續。
6. **iMessage 整合穩定性回報**：新功能上線初期，踩坑紀錄預計將在社群陸續出現。

---

*報告生成時間：2026-06-24 UTC*
*資料來源整理自 GitHub releases、NousResearch/hermes-agent issues、技術部落格、社群論壇*
