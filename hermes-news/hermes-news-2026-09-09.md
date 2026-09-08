# Hermes-Agent 每日情報 — 2026-09-09

> 資料來源涵蓋截至 2026-09-09 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **v0.21.1 patch release 於 2026-09-07 釋出**：這是自 v0.21.0 以來的 rollup 修補版，彙整 5,139 個非合併 commits、跨 4,364 個檔案、632 個已合併 PR，涵蓋 codebase 模組化、檔案操作與啟動效能優化、provider／model 更新、桌面 session 控制與瀏覽器標註、MCP 授權強化、cron 排程與派送修復，以及 delegation 可靠性改善。官方註明完整的 curated release notes 將隨下一個 v0.22.0 一併發布。（[Release Notes](https://github.com/NousResearch/hermes-agent/releases/tag/v2026.9.7)）

2. **當前最新穩定版為 v0.21.1（2026-09-07）**，前一個功能版本為 v0.21.0「The Pantheon Release」（2026-08-31），涵蓋約 5,800 次 commits、2,475 個合併 PR、5,680+ 個修改檔案，並關閉超過 2,100 個 issues。（[Release Notes](https://github.com/NousResearch/hermes-agent/releases/tag/v2026.8.31)）

3. **v0.21.0 主要新功能回顧**：桌面應用內建 Bot Mode（多個具名 agent 組成的「社群」，彼此可群聊互動）、agent-to-agent 通訊升級為一等公民功能（含 bot 對 bot 真實回覆的 DM）、cron 排程 agent 具備跨次執行的記憶與連續性、可即時介入操控執行中的子代理、MCP 介面升級為集中管理面板，以及 agent 可直接操控桌面應用內建瀏覽器。官方亦提及此版將預設 context 用量大幅降低（約減半）。（[Release Notes](https://github.com/NousResearch/hermes-agent/releases/tag/v2026.8.31)）

4. **2026-09-08 新開 issue 集中在 Windows 平台與基礎設施穩定性**：包括終端機／檔案工具在 Windows 上因缺少 timeout 保護而長時間掛起（#105865，附修復方案）、`hermes update` 面對 GitHub HTTP 429 無重試與 fallback 機制（#105857，P2）、cron job 執行中若 heartbeat 監控中斷會被錯誤覆寫成中斷狀態（#105861）、Windows 桌面版更新檢查誤判伺服器無法連線（#105855）等。詳見第 4 節。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）
- **Auth 方式**：支援 `ANTHROPIC_API_KEY` 直接付費、透過 `hermes model` 走 OAuth（需 Claude Max 並購買額外用量額度，Claude Pro 訂閱**不支援**此路徑，需改用 API Key）、Claude Code 憑證自動偵測，以及手動 setup-token 作為 legacy fallback。
- **API 型態**：走原生 Anthropic Messages endpoint；OAuth 路徑會以「Claude Code 對你的 Anthropic 帳號」的身份運作。
- **官方文件目前列出的推薦預設模型為 `claude-sonnet-4-6`**。（[Provider 文件](https://github.com/NousResearch/hermes-agent/blob/main/website/docs/integrations/providers.md)）

### GPT（OpenAI）
- **Auth 方式**：`OPENAI_API_KEY` 直接接入、GitHub Copilot（走 Copilot API，支援 OAuth device code flow）、ChatGPT 訂閱 OAuth（對應 Codex 模型，device-code 登入）。
- **API 型態**：一般走 Chat Completions；GPT-5.x 系列透過 Copilot 使用 Responses API。
- **Copilot 路徑目前列出的預設模型為 `gpt-5.4`**。（[Provider 文件](https://github.com/NousResearch/hermes-agent/blob/main/website/docs/integrations/providers.md)）

### Gemini（Google）
- **Auth 方式**：`GOOGLE_API_KEY` / `GEMINI_API_KEY` 靜態金鑰（純 API-key 路徑），或 Vertex AI 走 OAuth2（服務帳號 JSON 或 Application Default Credentials）。
- **重要限制**：官方文件明確指出「目前沒有辦法用消費級 Gemini 訂閱登入 Hermes」，建議使用有啟用計費的 GCP 專案，而非免費額度層級。
- **Vertex 路徑目前列出的預設模型為 `google/gemini-3-flash-preview`**（需帶 `google/` 前綴）。（[Provider 文件](https://github.com/NousResearch/hermes-agent/blob/main/website/docs/integrations/providers.md)）

### Auxiliary Model
- 官方文件未提及特別的預設 override，未另行指定時，輔助任務（如摘要、壓縮）預設沿用主聊天模型。

### 過去 24 小時內查到的相關踩坑
- **Windows `hermes update` 遇 HTTP 429 無法自動復原**（#105857，P2）：GitHub 對 fetch 請求速率限制時，更新程序直接失敗退出，即使等待官方建議的 5 分鐘後重試仍持續失敗，導致安裝版本永久落後於最新版；reporter 指出新版安裝程式（PR #99480）已有的 retry/backoff 與 fallback 機制尚未回饋到既有 `hermes update` 路徑。（[Issue #105857](https://github.com/NousResearch/hermes-agent/issues/105857)）
- **Windows 終端機／檔案工具長時間掛起**（#105865）：`LocalEnvironment` 的 Windows 版 stdout drain thread 使用無 timeout 的 blocking `os.read()`，當子行程繼承 pipe write handle 時會無限期卡住，最長可能觸發約 420 秒的 tool timeout；POSIX 版已有 `select()` 與 idle-detection 保護，Windows 版缺乏對等機制。reporter 附上以 `PeekNamedPipe`（透過 `ctypes`/`msvcrt`）改寫的修復方案，測試中將 session 初始化從逾時改善至 1.25 秒。（[Issue #105865](https://github.com/NousResearch/hermes-agent/issues/105865)）

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 類型 | 重點 |
|------|----------|------|------|
| v0.21.1（v2026.9.7） | 2026-09-07 | Patch rollup | 5,139 commits／632 merged PR，效能、MCP 授權、cron、delegation 修復；完整 release notes 待 v0.22.0 一併補上 |
| v0.21.0「The Pantheon Release」 | 2026-08-31 | 主版本 | Bot Mode、agent-to-agent 通訊、cron 記憶與連續性、即時子代理操控、MCP 集中管理面板、agent 操控桌面瀏覽器、預設 context 用量約減半 |
| v0.20.6（v2026.8.27） | 2026-08-27 | Patch | ~525 merged PR：consent-gated profile browsing、獨立瀏覽器視窗、MCP 目錄擴充至 50+ servers、結果快取、keychain 加密機密資訊 |
| v0.20.5（v2026.8.19） | 2026-08-19 | Patch | ~323 merged PR：Bot Mode 細節修正、keyless web tier（免費輪替額度）、CLI 優化、cron job 記憶功能雛形 |

（[Releases 總覽](https://github.com/NousResearch/hermes-agent/releases)）

---

## 4. 社群討論與已知問題

2026-09-08 新開的 issue 中，除上方 LLM 相關的兩則外，另有以下值得留意（均為官方 GitHub repo 上的公開 issue，尚待官方處理，非已證實修復）：

| Issue | 說明 | 優先度／狀態 |
|------|------|------|
| [#105861](https://github.com/NousResearch/hermes-agent/issues/105861) | cron job 執行期間若 heartbeat 監控出現空窗，執行狀態會被錯誤覆寫成「中斷」訊息 | Open |
| [#105855](https://github.com/NousResearch/hermes-agent/issues/105855) | Windows 桌面版更新檢查機制反覆誤報伺服器無法連線，即使直接 HTTPS／Git 連線正常 | Open |
| [#105853](https://github.com/NousResearch/hermes-agent/issues/105853) | cron job 工具從 gateway 行程內部呼叫時，會誤判該 gateway 本身處於離線狀態 | Open |
| [#105841](https://github.com/NousResearch/hermes-agent/issues/105841) | OpenCode commit message 產生器遺漏必要的 `x-opencode-session` HTTP header | Open |
| [#105838](https://github.com/NousResearch/hermes-agent/issues/105838) | 完整測試套件批次執行時因跨目錄 module 快取污染與平台環境變數洩漏而失敗，單獨執行則正常；reporter 已提出 P3、可自行送 PR 修復 | P3／Open |
| [#105834](https://github.com/NousResearch/hermes-agent/issues/105834) | llama.cpp provider 偵測在 `llama-server` 使用非預設連接埠時失效 | Open |

過去 24 小時內未查到具引用來源的 X（Twitter）官方公告、Reddit 或其他社群討論串直接針對 v0.21.1 或以上 issue 展開討論，故本節僅列出可查證的 GitHub 一手資料，不臆測社群反應熱度。

---

*報告生成時間：2026-09-09 UTC*
