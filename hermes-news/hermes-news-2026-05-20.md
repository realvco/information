# Hermes-Agent 每日情報 — 2026-05-20

> 搜尋範圍：過去 24 小時（UTC 2026-05-19 ～ 2026-05-20）
> 來源：GitHub Issues/Releases、Releasebot、Phemex News、dev.to、NousResearch X 等

---

## 1. 今日重點摘要

1. **最新版本**：**v0.14.0「The Foundation Release」**（2026-05-16 發布）為目前最新穩定版，距今 4 天；v0.15 尚未釋出。本版本共 808 commits、633 merged PRs、1393 個檔案異動、165,061 行新增，修閉 545 個 issues（含 12 個 P0、50 個 P1）。
2. **xAI Grok OAuth 正式上線**：Grok 以 **SuperGrok OAuth** 方式整合，支援 grok-4.3（1M context window），可無 API key 直接使用訂閱帳號，同時開放 Grok TTS（語音）與 Grok Imagine（圖片/影片生成）。
3. **OpenAI-compatible 本地 Proxy**：任何 OAuth 授權的 Hermes provider（Claude Pro、ChatGPT Pro、SuperGrok）均可透過新的本地 proxy 轉為 OpenAI 相容端點，供 Codex CLI、Aider、Cline、Continue 等工具直接串接，**無需 API key**。
4. **Computer Use 跨模型支援**：`cua-driver` 後端重構，加入 focus-safe 操作機制，任何具備視覺能力的模型均可驅動桌面操控（不再限於 Anthropic 模型）。
5. **新訊息平台**：LINE 與 SimpleX Chat 加入，Hermes 現支援 **22 個訊息平台**；Microsoft Teams 端到端整合（Graph auth + webhook + pipeline + outbound）也於本版完工。
6. **LSP 語意診斷**：每次寫入觸發型別錯誤、未定義符號、缺少 import 的即時偵測，透過真正的語意分析而非僅靠 lint 規則。
7. **x_search 工具**：X（Twitter）搜尋成為一等工具，支援 OAuth 或 API key 兩種認證方式。
8. **今日已知 Issue**：#22908（macOS CLI，Shift+Enter 停止插入換行、Enter 行為改變，破壞多行輸入體驗），今日被標記為需要修復。
9. **新增可選 Skills（共 9 個）**：Hyperliquid、Yahoo Finance、api-testing、unified EVM multi-chain、darwinian-evolver、osint-investigation、pinggy-tunnel、watchers、Notion 大幅改版；HuggingFace 社群 skills index 預設整合至 Skills Hub。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic OAuth / Pro 訂閱）

- **推薦做法**：使用 Anthropic OAuth（`hermes model` 選擇 Anthropic OAuth），Hermes 會優先使用 Claude Code 自身的 credential store，保持 token 自動刷新，避免 `.env` 中 token 過期問題。
- **跨 session 提示快取**：Hermes 支援 1 小時跨 session Claude prompt caching，可顯著降低長對話的 token 成本。
- **新用途**：v0.14 的本地 proxy 讓 Aider、Cline 等工具可透過 Claude Pro 訂閱運作，繞過直接申請 Claude API 的門檻。
- **踩坑**：token bloat 問題（舊版在 hermes-agent 目錄啟動會載入開發用檔案），已由 teknium 修復為從 home directory 啟動；更新前若未注意版本可能造成費用異常。
- 來源：[AI Providers | Hermes Agent Docs](https://hermes-agent.nousresearch.com/docs/integrations/providers)、[Hermes Agent Troubleshooting Guide](https://www.getopenclaw.ai/blog/hermes-agent-troubleshooting)

### GPT-5.x（GitHub Copilot / OpenAI API）

- 透過 GitHub Copilot 訂閱，可存取 GPT-5.x、Claude、Gemini 等模型，Hermes 視模型自動選擇 Responses API（GPT-5+，gpt-5-mini 除外）或 Chat Completions（GPT-4o、Claude、Gemini 等）。
- v0.14 Codex 作為 OpenAI 模型的 runtime backend，可在 Hermes 工作流內以 agentic 方式執行程式碼。
- 來源：[Hermes Agent v0.14.0 Release Notes](https://github.com/NousResearch/hermes-agent/blob/main/RELEASE_v0.14.0.md)

### xAI Grok（SuperGrok OAuth）

- **整合方式**：以 SuperGrok 或 Premium+ 帳號 OAuth 登入，無需 API key，免費取用 grok-4.3（1M context）、Grok TTS、Grok Imagine。
- **實際優勢**：Grok 原生支援 X posts、trending topics、open web 即時資訊查詢，無需額外工具設定。
- **踩坑**：有社群反映「best Grok models for Hermes」的設定文件分散，建議以 [remoteopenclaw.com](https://www.remoteopenclaw.com/blog/best-grok-models-for-hermes-agent) 整理的指南為參考。
- 來源：[Hermes v0.14 Boosts AI with Native Grok — Phemex News](https://phemex.com/news/article/hermes-v014-enhances-ai-integration-with-native-grok-support-and-api-proxy-83063)

### Discord Rate Limit 踩坑（跨模型通用）

- **Issue #7137**：Gateway 在 Discord 上以 slash commands 運作時，約 3 分鐘後觸發 **429 rate limit**，進入重連迴圈。
- **根本原因**：Hermes 向 Discord 註冊的 slash command 數量超過 100 個上限（Discord 平台限制）。
- **解法**：縮減已啟用 skills 數量至 100 以下，或等待官方針對 command 數量動態管理的修復。
- 來源：[Bug #7137 · NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent/issues/7137)

### Token 異常消耗

- 社群回報：「2 小時輕量使用消耗 400 萬 tokens，光問天氣就用掉 21K tokens」，問題追溯至 Gateway 在 hermes-agent 開發目錄啟動，載入大量額外上下文。
- **已修復**：新版改為從 home directory 啟動，context 大幅縮減。若仍觀察到異常 token 消耗，需確認版本是否為最新。
- 來源：[Hermes Agent Troubleshooting Guide](https://www.getopenclaw.ai/blog/hermes-agent-troubleshooting)

---

## 3. 社群反應與討論亮點

- **NousResearch X 官方貼文**（[@NousResearch](https://x.com/NousResearch/status/2056110234309939330)）宣告 v0.14.0 發布，強調「安裝在任何地方並附帶你真正想用的東西」，社群反應熱烈，轉推討論聚焦在本地 proxy 與跨模型 computer use 兩大功能。
- **本地 Proxy 話題**：v0.14 最受討論的功能。開發者表示可用 Claude Pro 訂閱直接餵給 Aider/Cline，繞過 API 費用；也有人擔憂訂閱條款是否允許此類程式化使用。
- **Computer Use 跨模型**：cua-driver 重構讓 Gemini 等視覺模型也能操控桌面，社群對非 Anthropic 模型的 computer use 穩定性持觀望態度，等待更多實測回報。
- **Issue #22908**（macOS CLI 換行 bug）：本地 CLI 用戶反映 Shift+Enter 失效，Enter 行為改變，多行輸入困難，影響日常使用體驗，期待快速修補。
- **Skills Hub + HuggingFace 整合**：社群對第三方 skills index 整合表達期待，有用戶已開始發布自製 skills 到 HuggingFace 社群索引。
- 來源：[Releases · NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent/releases)、[Digg — Nous Research releases Hermes Agent v0.14.0](https://digg.com/ai/1qruxedc)

---

## 4. 值得追蹤的後續議題

| 議題 | 說明 | 追蹤優先度 |
|------|------|-----------|
| Issue #22908 macOS CLI 換行 bug | 影響本地 CLI 日常使用，等待熱修復 | 高 |
| Issue #7137 Discord 429 rate limit | Skills 超過 100 時觸發，等待官方動態管理方案 | 高 |
| v0.15 時程與功能預告 | v0.14 發布 4 天，觀察下一版節奏與重點 | 中 |
| 本地 Proxy 訂閱條款風險 | Claude Pro / ChatGPT Pro 訂閱協議是否允許程式化調用，待釐清 | 中 |
| Computer Use 非 Anthropic 模型穩定性 | cua-driver 新後端的實戰可靠性報告 | 中 |
| May 2026 OpenRouter usage flip | 部分用戶切換至 OpenRouter 的體驗與費用對比 | 低 |
| Skills 社群生態（HuggingFace 索引） | 第三方 skills 品質與安全性審核機制 | 低 |

---

*本報告依搜尋結果如實撰寫，未收錄的功能或數據不予推測。*
