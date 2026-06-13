# Hermes-Agent 每日情報 — 2026-06-13

> 資料蒐集時間：2026-06-13 UTC｜涵蓋過去 24 小時公開資訊

---

## 1. 今日重點摘要

1. **v0.16.0「Surface Release」於 6 月 5 日正式發布**：874 commits、542 merged PRs、1,962 files changed、399 issues closed；過去 24 小時內無新版本發布。
2. **原生桌面應用正式亮相**：macOS / Linux / Windows 三平台一鍵安裝，支援 in-app 自我更新、拖放檔案至對話、狀態列內嵌 model picker、多 profile 並行 session，以及完整的簡體中文翻譯。
3. **Web Dashboard 升級為全功能管理後台**：新增 MCP 目錄、訊息頻道管理、憑證管理、Webhook 設定、Memory 管理，以及可插拔式 OIDC / 帳密登入。
4. **新增四款模型支援**：DeepSeek-V4-Flash、MiniMax-M3、Qwen3.7-Plus、Gemini-3.5-Flash 加入官方支援清單。
5. **新增 /undo 指令**：用戶可撤銷最近一次 agent 操作，降低誤操作風險。
6. **已知的 Anthropic OAuth 認證問題（Issue #15080）**：使用 Claude Max 20x 訂閱搭配 `~/.claude/.credentials.json` 的 OAuth token 時，對 `api.anthropic.com/v1/messages` 的請求持續回傳 HTTP 400，已記錄為未解決 bug。
7. **Rate limit 顯示改善需求（Issue #26889）**：當 LLM provider 回傳 429 時，Hermes 顯示的錯誤訊息未包含 reset time，社群已提出 feature request。
8. **GitHub 星標累積至 22,000+**，社群貢獻者達 242 人，MIT 授權開源。

---

## 2. LLM 串接實戰回報

### Anthropic (Claude)

**已確認的重大 Bug：HTTP 400「out of extra usage」**
- **Issue #15080**（尚未修復）：Claude Max 20x 訂閱用戶透過 OAuth 接入時，Anthropic 會將第三方 OAuth client 的請求路由至獨立的 `extra_usage` billing pool，即使主要額度充足仍觸發 400 錯誤。
- 官方暫行建議：**改用 API Key 直連**，不建議依賴 Claude Code OAuth credential 重用於有實質用量需求的場景，因為文件對此的說明具有誤導性。
- 來源：[Issue #15080](https://github.com/NousResearch/hermes-agent/issues/15080)

**OAuth credential 管理問題（Issue #12905）**
- Anthropic provider OAuth 整合存在多項 credential 管理與 subscription routing 問題，已歸類為 bug 並追蹤中。
- 來源：[Issue #12905](https://github.com/NousResearch/hermes-agent/issues/12905)

**「out of extra usage」假陽性（Issue #6475）**
- 部分用戶即使重新登入、重啟後，Hermes 仍持續顯示「You're out of extra usage」，實為 session 狀態未清除導致的誤報。
- 來源：[Issue #6475](https://github.com/NousResearch/hermes-agent/issues/6475)

### Rate Limit（通用問題）

**Rate limit reset time 未顯示（Issue #26889）**
- 任何 LLM provider 回傳 429 時，Hermes 雖顯示基本錯誤訊息，但 response header 中的 reset time 資訊未被解析並呈現給用戶，導致用戶不知道需要等待多久。
- 來源：[Issue #26889](https://github.com/NousResearch/hermes-agent/issues/26889)

### 新模型接入

- **DeepSeek-V4-Flash**：高速推論，適合高頻工具呼叫場景。
- **Gemini-3.5-Flash**：Google 最新輕量版，成本低、latency 短。
- **MiniMax-M3 / Qwen3.7-Plus**：擴大中文模型生態覆蓋。
- 官方文件：[AI Providers | Hermes Agent](https://hermes-agent.nousresearch.com/docs/integrations/providers)

### Fallback Provider 機制

- Hermes 提供 fallback provider 設定，可在主 provider 失敗（包括 429、auth error）時自動切換備援模型，建議搭配 Claude Max 問題的臨時解法使用。
- 文件：[Fallback Providers | Hermes Agent](https://hermes-agent.nousresearch.com/docs/user-guide/features/fallback-providers)

---

## 3. 社群反應與討論亮點

- **桌面應用獲高度關注**：v0.16.0 的桌面應用是 100 個 PR、159 個 commit 在一週內完成的衝刺成果，社群對「終於有真正的原生桌面體驗」反應熱烈，YouTube 上已有評測影片 [Hermes Agent v0.16 is HUGE!](https://www.youtube.com/watch?v=c3bd0HiE3pg)。
- **Claude Max 串接問題引發廣泛討論**：多名用戶在 GitHub Issues 回報 HTTP 400 問題，部分用戶因此改用 API Key 或切換至 OpenRouter 作為 Anthropic 的代理中間層。
- **Shopify 整合受到電商社群關注**：v0.13.0 中新增的 Shopify Admin + Storefront GraphQL skill 吸引電商開發者嘗試，但實際落地案例尚少。
- **FinTech 媒體報導**：[FintechExtra](https://fintechextra.com/news/hermes-agent-v0160-launches-native-desktop-app-633) 等媒體報導 v0.16.0，顯示 Hermes-Agent 在非技術圈的曝光度持續提升。
- **Changelog 文件維護**：社群維護的 [Hermes-Wiki](https://github.com/cclank/Hermes-Wiki) 提供非官方 changelog 補充，可追蹤官方文件未涵蓋的細節。

---

## 4. 值得追蹤的後續議題

1. **Issue #15080 修復進度**：Anthropic OAuth + Claude Max 的 HTTP 400 問題是目前最多用戶關注的 bug，修復時程未定，建議使用 API Key 作為替代方案。
2. **Rate limit 顯示改善（Issue #26889）**：已確認為 enhancement request，預計納入後續版本；在此之前用戶需自行估算等待時間。
3. **桌面應用穩定性反饋**：v0.16.0 桌面應用為全新開發，bug report 期望將在接下來幾週集中湧現，建議追蹤 [releases page](https://github.com/NousResearch/hermes-agent/releases)。
4. **新模型實戰評測**：DeepSeek-V4-Flash 與 Gemini-3.5-Flash 剛加入支援，社群對其在 Hermes 工作流中的效能與穩定性比較尚無充分資料，值得持續追蹤。
5. **Web Admin Panel 的安全配置**：OIDC 整合與 credential 管理功能較新，建議關注是否有相關安全 advisory 發布。

---

*本報告依據公開搜尋結果整理，過去 24 小時（2026-06-12 至 2026-06-13）未偵測到新的版本發布或重大公告，主要資訊源自 6 月 5 日的 v0.16.0 發布及持續追蹤中的 GitHub Issues。*
