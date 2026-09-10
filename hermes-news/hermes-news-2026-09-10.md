# Hermes-Agent 每日情報 — 2026-09-10

> 資料來源涵蓋截至 2026-09-10 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **過去 24 小時無新版本釋出，目前最新穩定版仍為 v0.21.1（v2026.9.7，2026-09-07）**。官方 release notes 重申完整的 curated release notes（涵蓋 v0.21.0 以來所有變更與貢獻者名單）將隨下一個 v0.22.0 一併發布，目前尚未有 v0.22.0 的時程訊息。（[Release Notes](https://github.com/NousResearch/hermes-agent/releases/tag/v2026.9.7)）

2. **OpenAI Codex image_gen 外掛內寫死的 `gpt-5.5` 模型已下線，導致圖片產生功能全面 404**（[#106683](https://github.com/NousResearch/hermes-agent/issues/106683)）：`plugins/image_gen/openai-codex/__init__.py` 硬編碼呼叫已棄用的 `gpt-5.5`，多個 v0.21.1 部署回報收到 OpenAI「model does not exist」錯誤；該功能目前也沒有可覆寫模型名稱的設定項。issue 已被標記為 duplicate、P3。

3. **Codex app-server 執行環境仍會被 90 秒 post-tool watchdog 誤判逾時而中止 TUI 任務**（[#107028](https://github.com/NousResearch/hermes-agent/issues/107028)，P2）：reporter 於 2026-09-09 補送新的重現紀錄，懷疑成因是 `CodexEventProjector.project` 對部分通知產生空白 projection、或活動事件已排入佇列但未即時重置 watchdog 計時器，目前尚未定案根因。

4. **多起 macOS／Windows 平台底層穩定性問題**：Darwin 上 `_kill_process_group_posix` 未檢查目標 process group 是否就是 gateway 自身，導致終端機工具清理時誤殺 gateway 本身、被 launchd 反覆重啟（[#107029](https://github.com/NousResearch/hermes-agent/issues/107029)，P2）；kanban worker 自行拆分子任務時可能與既有的「父卡片需等所有子卡片完成」邏輯互相死鎖，需人工介入（[#106994](https://github.com/NousResearch/hermes-agent/issues/106994)，P3）。詳見第 4 節。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）
- 與前日相同，官方文件未見更新：`ANTHROPIC_API_KEY` 直接付費、`hermes model` 走 Claude Code OAuth（限 Claude Max 並購買額外用量額度，Claude Pro 訂閱不支援，需改用 API Key）、Claude Code 憑證自動偵測、手動 setup-token 作為 legacy fallback。**推薦預設模型仍為 `claude-sonnet-4-6`**。（[Provider 文件](https://github.com/NousResearch/hermes-agent/blob/main/website/docs/integrations/providers.md)）
- 過去 24 小時未查到與 Claude 串接相關的新 issue 或踩坑回報。

### GPT（OpenAI）
- **踩坑：OpenAI Codex image_gen 外掛硬編碼模型下線**（[#106683](https://github.com/NousResearch/hermes-agent/issues/106683)）：見第 1 節，`gpt-5.5` 已被 OpenAI 下架，圖片生成工具回傳 `model_not_found`，社群建議改用 `gpt-5.6-luna` 等現行可用模型，並呼籲加入可設定的模型覆寫參數。
- **踩坑：Codex app-server watchdog 誤判逾時**（[#107028](https://github.com/NousResearch/hermes-agent/issues/107028)）：見第 1 節，互動式開發 session 可能在工具呼叫完成後被錯誤標記為「靜默 90 秒」而提前中止。
- **功能請求：GPT Image 2.5 支援**（[#106708](https://github.com/NousResearch/hermes-agent/issues/106708)）：社群要求 OpenAI Codex OAuth 圖片供應商支援新款 GPT Image 2.5。
- Copilot 路徑目前列出的預設模型仍為 `gpt-5.4`，文件未見異動。（[Provider 文件](https://github.com/NousResearch/hermes-agent/blob/main/website/docs/integrations/providers.md)）

### Gemini（Google）
- 與前日相同，官方文件未見更新：`GOOGLE_API_KEY` / `GEMINI_API_KEY` 靜態金鑰，或 Vertex AI 走 OAuth2（服務帳號 JSON 或 ADC）；官方仍明確表示「目前沒有辦法用消費級 Gemini 訂閱登入 Hermes」。**Vertex 路徑預設模型仍為 `google/gemini-3-flash-preview`**。（[Provider 文件](https://github.com/NousResearch/hermes-agent/blob/main/website/docs/integrations/providers.md)）
- 過去 24 小時未查到與 Gemini 串接相關的新 issue 或踩坑回報。

### Auxiliary Model
- 官方文件未提及特別的預設 override，未另行指定時仍沿用主聊天模型。
- 社群提出「模型 failover 與依任務類型智慧路由」功能請求（[#106740](https://github.com/NousResearch/hermes-agent/issues/106740)，P3）：希望新增 `model.failover` 設定區塊，可設定逾時秒數與備援模型，並依 vision／coding／文字等任務類型自動選擇最適模型，動機是多模型／多供應商環境下常遇到 rate limit 或服務中斷，需手動切換。

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 類型 | 重點 |
|------|----------|------|------|
| v0.21.1（v2026.9.7） | 2026-09-07 | Patch rollup | 5,139 commits／632 merged PR，效能、MCP 授權、cron、delegation 修復；完整 release notes 待 v0.22.0 一併補上（目前仍為此狀態，過去 24 小時無新版） |
| v0.21.0「The Pantheon Release」 | 2026-08-31 | 主版本 | Bot Mode、agent-to-agent 通訊、cron 記憶與連續性、即時子代理操控、MCP 集中管理面板、agent 操控桌面瀏覽器、預設 context 用量約減半 |
| v0.20.6（v2026.8.27） | 2026-08-27 | Patch | ~525 merged PR：consent-gated profile browsing、獨立瀏覽器視窗、MCP 目錄擴充至 50+ servers、結果快取、keychain 加密機密資訊 |
| v0.20.5（v2026.8.19） | 2026-08-19 | Patch | ~323 merged PR：Bot Mode 細節修正、keyless web tier（免費輪替額度）、CLI 優化、cron job 記憶功能雛形 |

（[Releases 總覽](https://github.com/NousResearch/hermes-agent/releases)）

---

## 4. 社群討論與已知問題

過去 24 小時新開的 issue 數量龐大（此 repo 開發節奏極快），以下僅列出除上方 LLM 相關議題外，較值得留意的非 LLM 基礎設施／平台問題（均為官方 GitHub repo 上的公開 issue，尚待官方處理，非已證實修復）：

| Issue | 說明 | 優先度／狀態 |
|------|------|------|
| [#107029](https://github.com/NousResearch/hermes-agent/issues/107029) | macOS：`_kill_process_group_posix` 未區分目標 process group 是否為 gateway 自身，終端機工具清理時誤殺 gateway，被 launchd 反覆重啟 | P2／Open |
| [#106994](https://github.com/NousResearch/hermes-agent/issues/106994) | Kanban worker 自行拆分子任務時，「父卡片需等所有子卡片完成」的邏輯與「子卡片需等父卡片完成才可派工」互相死鎖，曾造成生產環境卡片卡住逾一小時 | P3／Open |
| [#107012](https://github.com/NousResearch/hermes-agent/issues/107012) | `BASE_URL` 環境變數覆寫與既有 provider 位址衝突時，自訂 provider 會被靜默去重隱藏，無任何提示或日誌 | P3／Open |
| [#107026](https://github.com/NousResearch/hermes-agent/issues/107026) | Windows 桌面版 Dashboard 顯示異常回報 | Closed |
| [#107002](https://github.com/NousResearch/hermes-agent/issues/107002) | Windows：`hermes update` 顯示更新成功，但 gateway 重啟驗證失敗 | Open |
| [#107000](https://github.com/NousResearch/hermes-agent/issues/107000) | Windows 桌面版逐字打字時 transcript 畫面持續閃爍／重繪 | Open |
| [#107044](https://github.com/NousResearch/hermes-agent/issues/107044) | Gateway 語音（TTS）朗讀時會把推理標籤（reasoning label）開頭一併唸出來 | Open |
| [#107041](https://github.com/NousResearch/hermes-agent/issues/107041) | 社群提出降低 CI runner 成本、同時維持完整測試覆蓋率的功能請求 | Open |

過去 24 小時內未查到具引用來源的 X（Twitter）官方公告或 Reddit 討論串直接針對上述 issue 或 v0.21.1 展開新一輪討論，故本節僅列出可查證的 GitHub 一手資料，不臆測社群反應熱度。

---

*報告生成時間：2026-09-10 UTC*
