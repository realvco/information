# Hermes-Agent 每日情報 — 2026-05-31

> 資料涵蓋範圍：過去 24 小時（UTC 2026-05-30 ～ 2026-05-31）

---

## 1. 今日重點摘要

1. **v0.15.2 為目前最新穩定修補版**：v0.15.2（2026.5.29.2）於 5/29 發布，修正 wheel 與 sdist 打包時未包含 bundled plugin.yaml manifest 的問題，是目前建議安裝的最新版本。
2. **v0.15.1 同日修復 dashboard 無限重載 Bug**：v0.15.1（2026.5.29）緊急修補 v0.15.0 的 dashboard 無限重載迴圈，影響所有以 loopback 模式（Docker、hosted Hermes、全新安裝）運行 v0.15.0 的用戶。
3. **v0.15.0「Velocity Release」為里程碑版本**（5/28 發布）：747 個 PR、321 位貢獻者、560+ issues 關閉（含 15 個 P0、65 個 P1、19 個安全標記）。run_agent.py 從 16,083 行縮減至 3,821 行（-76%），重構為 14 個內聚模組。
4. **效能大幅躍升**：冷啟動再減少一秒、每對話 function call 數減少 47%、`session_search` 速度提升 4,500 倍且免費，`hermes --version` 啟動基準測試首度超越 Codex CLI。
5. **Kanban 成熟為真正的多 agent 平台**：經 104 個 PR 打磨，Kanban 具備 orchestrator 自動分解、swarm 拓撲、排程任務、worktree-per-task、per-task 模型覆蓋等能力。
6. **新 LLM 支援**：v0.15.0 新增 Claude Opus 4.8、Qwen 3.7，以及 NFTY Platform 作為 gateway channel；Skill Bundles 與 MCP Catalog 讓技能管理更系統化。
7. **安全防禦升級**：Promptware defense 正式落地，防禦 Brainworm-class 提示注入攻擊。
8. **今日（5/31）無新版本發布跡象**：過去 24 小時搜尋無 v0.15.3 或更新版本資訊，目前以 v0.15.2 為最新穩定版。

---

## 2. LLM 串接實戰回報

### 2-1. xAI Grok OAuth 403 問題
- **現象**：使用 SuperGrok 訂閱進行瀏覽器 OAuth 登入成功後，部分用戶在實際推理時收到 HTTP 403，即使訂閱有效。
- **緩解方式**：改用 `XAI_API_KEY` 環境變數走 API key 路徑，繞過 OAuth 認證流程。
- **根因推測**：xAI 後端對標準 SuperGrok 訂閱的 OAuth token 驗證邏輯不一致。
- **來源**：[Hermes Agent — xAI Grok OAuth 文件](https://hermes-agent.nousresearch.com/docs/guides/xai-grok-oauth)

### 2-2. Grok 模型退役遷移
- **退役清單**：xAI 於 2026-05-15 退役 `grok-4*`、`grok-3`、`grok-code-fast-1`、`grok-imagine-image-pro`。
- **Hermes 處理方式**：自動偵測設定中指向已退役模型的 config 並印出建議替代模型名稱，降低遷移障礙。
- **來源**：[RELEASE_v0.14.0.md](https://github.com/NousResearch/hermes-agent/blob/main/RELEASE_v0.14.0.md)

### 2-3. Grok 額度快速耗盡
- **社群回報**：整合 xAI Grok 的用戶反映配額消耗速度過快，尤其在 Kanban 多 agent 並行工作流中。
- **建議**：將輕量輔助任務卸載至本地 LLM（Ollama 等），保留 Grok 配額給需要大 context 或強推理的核心任務。
- **來源**：[AI Providers — Hermes Agent 文件](https://hermes-agent.nousresearch.com/docs/integrations/providers)

### 2-4. Grok 自動啟用 prompt caching
- **機制**：Hermes 使用 xAI 時，自動在每次 API 請求加入 `x-grok-conv-id` header，將請求路由至同一伺服器，實現 system prompt 與對話歷史的快取複用，降低重複 token 費用。
- **來源**：[Hermes Agent xAI Grok OAuth 指南](https://hermes-agent.nousresearch.com/docs/guides/xai-grok-oauth)

### 2-5. Claude Opus 4.8 新增支援
- **背景**：Anthropic 於 2026-05-28 發布 Claude Opus 4.8（距 4.7 僅 41 天），定價 $10/$50 per MTok。
- **Hermes 整合**：v0.15.0 已加入 Opus 4.8 支援，可在 per-task 模型覆蓋中直接指定。Claude Code fast mode 使用同版 Opus 4.8 權重但輸出速度提升至 2.5x，社群討論是否可利用此特性加速 Hermes 的 coding 任務。
- **來源**：[TECHSY — Hermes Agent v0.15 What Actually Matters](https://techsy.io/en/blog/hermes-agent-v0-15)

### 2-6. Docker 環境 MCP server 路徑解析問題
- **現象**（v0.15.0 已知 Bug）：設定裸命令（`npx`、`npm`、`node`）的 MCP server 在 Docker container 中靜默失敗，因 agent 的 effective PATH 不含 Node toolchain 位置。
- **v0.15.1 修復方式**：MCP server 現在會對照 `/usr/local/bin` 解析裸命令，確保在 Docker image 內正確啟動。
- **來源**：[RELEASE_v0.15.1.md](https://github.com/NousResearch/hermes-agent/blob/main/RELEASE_v0.15.1.md)

### 2-7. Nous Portal 統一多模型存取
- **功能**：Nous Portal 涵蓋 300+ 前沿 agentic 模型，包含 Claude、GPT、Gemini、DeepSeek、Qwen、Kimi、GLM、MiniMax、Grok，單一 OAuth 登入統一存取，免管理多組 API key。
- **適用場景**：適合需要在同一 Hermes 工作流中跨多家 provider 比較或 fallback 的進階使用者。
- **來源**：[AI Providers — Hermes Agent 文件](https://hermes-agent.nousresearch.com/docs/integrations/providers)

---

## 3. 社群反應與討論亮點

- **Teknium（NousResearch 創辦人）X.com 推文**：v0.15.0 發布時官方推文強調「747 PRs by 321 Contributors」，引發大量轉推與社群慶賀，[原文連結](https://x.com/Teknium/status/2060088572049559893)。
- **YouTube 教學影片**：「Hermes Agent Update v0.15 is POWERFUL!」影片發布後迅速累積觀看，反映社群對 Velocity Release 的高度期待。
- **FintechExtra 報導**：聚焦 v0.15.0 的速度提升與程式碼架構重整，稱之為「迄今最重要的版本」。
- **v0.15.1 hotfix 引發討論**：dashboard 無限重載問題影響廣泛（所有 Docker 用戶），有社群成員批評主版本測試覆蓋不足；NousResearch 當日即修復，普遍獲得正面評價。
- **Skill Bundles 與 MCP Catalog**：被視為 v0.15 最受期待的功能之一，多位社群成員分享自製 skill bundle 的使用心得。

---

## 4. 值得追蹤的後續議題

1. **v0.15.3 是否即將推出**：v0.15.1 和 v0.15.2 都在 5/29 同日發布，若後續有更多 hotfix 需求，v0.15.3 可能近期出現。
2. **Kanban 多 agent 平台的實際穩定性**：v0.15.0 大幅擴充 Kanban 能力，但複雜拓撲下的 zombie 偵測、heartbeat 與 reclaim 機制的實戰可靠性仍待社群長期觀察。
3. **Promptware（Brainworm）防禦的技術細節**：v0.15.0 首度落地 Brainworm-class 攻擊防禦，技術文件尚未完整公開，值得關注後續說明。
4. **Qwen 3.7 整合效果**：新增模型支援後，Qwen 3.7 在 coding / agentic task 的實際效能比較尚缺社群系統性評測。
5. **NFTY Platform channel 使用案例**：v0.15.0 新增的 NFTY Platform gateway channel 用途尚不明確，待社群探索。
6. **computer-use 非 Anthropic 模型支援（v0.14 功能）**：cua-driver 後端讓任何支援 vision 的模型都能驅動桌面，實際可用性與限制值得深入測試。

---

*本報告由 realvco-bot 自動生成，資料來源均附連結，如有疑義請對照原始連結查核。*
