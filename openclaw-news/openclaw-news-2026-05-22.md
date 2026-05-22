# OpenClaw 每日情報 — 2026-05-22

> 資料來源截止時間：2026-05-22 UTC｜搜尋範圍：過去 24 小時及近期重要更新

---

## 1. 今日重點摘要

1. **v2026.5.19 為目前最新穩定版本**（發布於 5 月 20 日），帶來更快的 Gateway 重啟速度、擴充的 Skills/Plugins 清單、更豐富的瀏覽器與 CLI 控制項，以及強化的 QA 與 runtime 工具鏈。
2. **v2026.5.18 正式 rollup**：彙整 5.12、5.14、5.17 beta 列車的所有修正；重點包括 QA-Lab 大幅強化（Codex vs Pi runtime parity）、首小時 runtime 情境測試、release gate 防止 drift 流入正式版本。
3. **Mac App 設定頁全面重設計**：Settings 採統一 card layout、快取導覽、更清晰的 permissions/voice/skills/cron/exec/debug 分頁，操作一致性大幅改善。
4. **Android Talk Mode 遷移至 Realtime Gateway relay**：語音會話不再走舊有路徑，Discord/OpenAI realtime 追蹤對話回合的持久性也已修正。
5. **Telegram 與 Discord 可靠性持續改善**：本次修正隔離 Telegram polling/topic 通道、媒體與 forum-topic 傳遞，並修復 `/stop`、`/btw` 指令處理、進度草稿與最終回覆恢復邏輯。
6. **Node.js 22 最低版本要求提升至 22.19**：同步更新 `@openclaw/proxyline` 至 0.3.3、Pi packages 至 0.75.1；Docker/Podman 新增 `OPENCLAW_IMAGE_APT_PACKAGES` 環境變數以供 runtime 中性化映像建置。
7. **代理重構指引更新**：官方文件明確要求修正預設採用「clean bounded refactors」，並設立明確的 plugin SDK/API deprecation 路徑，顯示團隊在大型 codebase 管理上趨向規範化。
8. **4 月底 gateway crash 事件後續觀察**：v2026.4.26 引發的 Discord 與 gateway 連鎖崩潰問題已由後續版本修復，但社群對於缺乏穩定 release channel 的批評持續存在，建議關注官方是否推出 LTS 頻道。

---

## 2. LLM 串接實戰回報

### Claude Opus 4.7（Anthropic API）

- **優勢**：SWE-bench Pro 得分 64.3%（Anthropic 自評），嚴格程式碼審查場景表現最佳；1M token context window，採固定計價。
- **重要變更**：自 2026 年 1 月起，Anthropic 關閉了透過 Claude Pro/Max 訂閱授權使用 OpenClaw 的路徑，現僅接受 API Key 方式。社群對此反彈強烈，部分用戶被迫轉向 OpenRouter 中介。
- **踩坑**：已知 Bug #32828 — 伺服器端 500/503 錯誤被誤判為 rate limit，觸發全域 cooldown，最嚴重案例 cooldown 累計長達 **5,365 分鐘**（約 3.7 天），需手動清除設定檔重置。
- 來源：[OpenClaw + Claude Code Costs 2026](https://www.shareuhack.com/en/posts/openclaw-claude-code-oauth-cost)、[Fix Every OpenClaw Error](https://blog.laozhang.ai/en/posts/openclaw-api-key-error)

### GPT-5.5（OpenAI API）

- **context window**：128K tokens（standard API），適合 context 不超限的自主 agent 任務，速度與工具呼叫品質均佳。
- **4 月更新**：v2026.4.23 起正式支援 GPT-5.5 與 `gpt-image-2` 圖像生成，agent 視覺能力升級。
- 來源：[X.com — @iamlukethedev](https://x.com/iamlukethedev/status/2047724771543056748)

### Gemini 3.1 Pro（Google API）

- **免費方案**：每分鐘 60 次請求、每日 1,000 次，為「免費層英雄」；付費方案也支援 1M token context。
- **整體評價**：在工作負載適配性、成本、多模態覆蓋與開發者體驗綜合評比中，Gemini 3.1 Pro 為 2026 年度綜合最高分。
- 來源：[Best LLM for OpenClaw: Gemini 3.1 Pro vs GPT-5.5 vs Claude Opus 4.7](https://dev.to/matthewrevell/best-llm-for-openclaw-gemini-31-pro-vs-gpt-55-vs-claude-opus-47-2026-3na4)

### DeepSeek V4-Pro

- DeepSeek 官方 X 貼文（4 月）：DeepSeek-V4-Pro API 75% 折扣期間，OpenClaw 使用者可設定 model 為 `deepseek-v4-pro[1m]` 解鎖 1M context window，需升級至 v2026.4.24+。
- 來源：[DeepSeek on X](https://x.com/deepseek_ai/status/2048062777357750316)

### Rate Limit 與費用風險（通用）

- OpenClaw **預設不含任何 API rate limit 防護**：無 token budget、無每日費用上限、無重試迴圈熔斷器。
- 真實案例：一位創辦人的 agent 在 overnight retry loop 中燒掉 **$47 API 費用**；完整一日自主 agent 使用在無防護設定下估計 **$1,000–$5,000**。
- 建議：自行在 provider 端設定每日 spending cap，並在 OpenClaw 設定中啟用手動 token budget。
- 來源：[OpenClaw API Rate Limit Reached](https://www.getopenclaw.ai/help/rate-limits-quota-management)、[Mastering OpenClaw API Rate Limit](https://skywork.ai/skypage/en/openclaw-api-rate-limit-guide/2048654216278003712)

---

## 3. 社群反應與討論亮點

- **「Sleep Is a Feature OpenClaw Haven't Shipped Yet」**：X.com 上廣傳的反諷推文，指出 always-on agent 在意外情境下的資源耗盡問題，引發大量共鳴。（[@degensing](https://x.com/degensing/status/2028784446057640147)）
- **v2026.4.26 事件後的社群情緒**：部落格報導「OpenClaw Had a Rough Week」，社群對連續三個版本（.24/.25/.26）破壞穩定性的測試不足感到疲乏，有用戶呼籲建立 stable / beta 雙軌制。（[piunikaweb.com](https://piunikaweb.com/2026/04/29/openclaw-update-triggering-gateway-crashes-issues/)）
- **MindStudio 分析**：2026 年 4 月釋出的 provider manifest，允許在執行中的 workflow 不重建即可熱切換 LLM brain（支援 6+ providers），社群視為架構成熟的重要里程碑。（[MindStudio Blog](https://www.mindstudio.ai/blog/openclaw-april-2026-model-agnostic-provider-manifest)）
- **GitHub 架構討論**：[agents-radar Issue #544](https://github.com/duanyytop/agents-radar/issues/544) 整理 OpenClaw 生態系 Digest（4 月 12 日），追蹤 Task Brain、SQLite-backed ledger 等新架構設計，社群對 ACP/subagent/cron/CLI 統一到同一執行層表示興趣。

---

## 4. 值得追蹤的後續議題

| 議題 | 原因 |
|------|------|
| **穩定 Release Channel 是否推出** | 連續 release 破壞穩定性事件後，社群需求明確；官方尚未公告 LTS 計畫 |
| **Bug #32828（500/503 誤判 rate limit）** | 影響多 provider 設定，修復進度需持續追蹤 |
| **Anthropic OAuth 政策後續** | Pro/Max 訂閱授權已關閉，API Key only 是否為長期政策？是否影響其他 OAuth provider？ |
| **OpenClaw-RL（Gen-Verse）** | 獨立 fork 「透過對話訓練任何 agent」，與主線 OpenClaw 關係與定位值得觀察 |
| **Node.js 22.19 最低版本衝擊** | 部分舊版部署環境可能需要升級；Docker image 重建成本需評估 |

---

*報告生成時間：2026-05-22 UTC*
