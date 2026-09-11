# Hermes-Agent 每日情報 — 2026-09-11

> 資料來源涵蓋截至 2026-09-11 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **過去 24 小時無新版本釋出，目前最新穩定版仍為 v0.21.1（v2026.9.7，2026-09-07）**。官方尚未公布 v0.22.0 時程。（[Release Notes](https://github.com/NousResearch/hermes-agent/releases/tag/v2026.9.7)）

2. **新增一起 P1 等級嚴重 bug：Anthropic 串流工具呼叫在特定情況下會永久失敗**（[#107830](https://github.com/NousResearch/hermes-agent/issues/107830)）：啟用 `fine-grained-tool-streaming-2025-05-14` beta header 時，模型偶爾會輸出未加引號的畸形 JSON（例如 `{"names": cronjob_manage}`），Anthropic SDK 的增量解析器無法容錯，導致整輪對話解析失敗並重試三次後直接放棄，且不會顯示任何錯誤訊息給使用者。影響 Telegram／Discord／Slack／WhatsApp 等所有啟用串流的 gateway，官方估計約 1% 對話會觸發，且對特定 prompt 具重現性。目前已知暫時解法是從 Anthropic adapter 的 `_COMMON_BETAS` 設定中移除該 beta header（犧牲增量串流換取伺服器端 JSON 修復）。標記為 P1、無官方修復流程，屬今日最值得關注的技術性問題。

3. **多起桌面／平台層級小型 bug 集中在 profile 與 session 管理**：桌面版在非預設 profile 下輸入 `/model --global` 會誤將設定寫回預設 profile（[#107860](https://github.com/NousResearch/hermes-agent/issues/107860)）；Discord 主動轉址（active redirect）未帶入同一 session 已附加的 cron context（[#107856](https://github.com/NousResearch/hermes-agent/issues/107856)，P2）；桌面版登入殼層的 PATH 探測邏輯僅支援 POSIX shell，fish shell 使用者會偵測失敗（[#107845](https://github.com/NousResearch/hermes-agent/issues/107845)，P2）。詳見第 4 節。

4. **OpenAI Codex image_gen 硬編碼模型下線問題再度被回報**（[#107076](https://github.com/NousResearch/hermes-agent/issues/107076)，P3）：與前日 [#106683](https://github.com/NousResearch/hermes-agent/issues/106683) 為同一根因（`gpt-5.5` 已下線導致 404），新 issue 已被標記為 duplicate，官方尚未釋出修復。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）
- **重大踩坑：串流工具呼叫遇畸形 JSON 會永久失敗**（[#107830](https://github.com/NousResearch/hermes-agent/issues/107830)）：見第 1 節。提案中的修復方向包含（a）攔截 ValueError 並改用 Anthropic 回傳的已緩衝／修復過的 `input_json` 欄位、（b）預設關閉該 beta header、（c）將 beta header 開放為可設定項讓使用者自行決定是否啟用增量串流。目前使用者可自行移除該 beta header 作為暫時解法。
- 官方 provider 文件未見更新，`ANTHROPIC_API_KEY` 直接付費、`hermes model` 走 Claude Code OAuth（限 Claude Max，Claude Pro 不支援）等安排維持不變，**推薦預設模型仍為 `claude-sonnet-4-6`**。（[Provider 文件](https://github.com/NousResearch/hermes-agent/blob/main/website/docs/integrations/providers.md)）

### GPT（OpenAI）
- **踩坑重複回報：Codex image_gen 硬編碼 `gpt-5.5` 已下線**（[#107076](https://github.com/NousResearch/hermes-agent/issues/107076)，duplicate of #106683）：問題與昨日相同，尚無修復進度。
- **Codex OAuth 圖片路由未落實 image_generation 工具規格**（[#107233](https://github.com/NousResearch/hermes-agent/issues/107233)，P3）：社群回報 Codex OAuth 圖片產生路徑未依官方 tool spec 驗證參數。
- **Codex provider 錯誤中斷 Pro Full／Pro Light 帳號使用**（[#107307](https://github.com/NousResearch/hermes-agent/issues/107307)，P3，求助中）：部分訂閱等級用戶回報 Codex provider 偶發錯誤導致工作中斷，官方尚未回應根因。

### Gemini（Google）
- 過去 24 小時未查到與 Gemini／Vertex AI 串接相關的新 issue 或踩坑回報，官方 provider 文件亦無更新。Vertex 路徑預設模型仍為 `google/gemini-3-flash-preview`。（[Provider 文件](https://github.com/NousResearch/hermes-agent/blob/main/website/docs/integrations/providers.md)）

### Auxiliary Model
- 過去 24 小時未查到與 auxiliary model 相關的新討論或設定變更。

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 類型 | 重點 |
|------|----------|------|------|
| v0.21.1（v2026.9.7） | 2026-09-07 | Patch rollup | 5,139 commits／632 merged PR，效能、MCP 授權、cron、delegation 修復；完整 release notes 待 v0.22.0 一併補上（過去 24 小時無新版） |
| v0.21.0「The Pantheon Release」 | 2026-08-31 | 主版本 | Bot Mode、agent-to-agent 通訊、cron 記憶與連續性、即時子代理操控、MCP 集中管理面板、agent 操控桌面瀏覽器、預設 context 用量約減半 |
| v0.20.6（v2026.8.27） | 2026-08-27 | Patch | ~525 merged PR：consent-gated profile browsing、獨立瀏覽器視窗、MCP 目錄擴充至 50+ servers、結果快取、keychain 加密機密資訊 |
| v0.20.5（v2026.8.19） | 2026-08-19 | Patch | ~323 merged PR：Bot Mode 細節修正、keyless web tier（免費輪替額度）、CLI 優化、cron job 記憶功能雛形 |

（[Releases 總覽](https://github.com/NousResearch/hermes-agent/releases)）

---

## 4. 社群討論與已知問題

過去 24 小時新開的 issue 數量龐大（此 repo 開發節奏極快），以下僅列出除上方 LLM 相關議題外，較值得留意的非 LLM 基礎設施／平台問題（均為官方 GitHub repo 上的公開 issue，尚待官方處理，非已證實修復）：

| Issue | 說明 | 優先度／狀態 |
|------|------|------|
| [#107860](https://github.com/NousResearch/hermes-agent/issues/107860) | 桌面版在非預設 profile 下執行 `/model --global` 會誤將設定寫回預設 profile 設定檔 | Open |
| [#107856](https://github.com/NousResearch/hermes-agent/issues/107856) | Discord 主動轉址（active redirect）未帶入同一 session 已附加的 cron context | P2／Open |
| [#107854](https://github.com/NousResearch/hermes-agent/issues/107854) | Windows 11 25H2（build 26200）上「真實 profile」偵測出現偽陰性 | P3／Open |
| [#107850](https://github.com/NousResearch/hermes-agent/issues/107850) | Background Review 同一 session 內注入的回合未正確設定 write_origin | P2／Open |
| [#107845](https://github.com/NousResearch/hermes-agent/issues/107845) | 桌面版登入殼層 PATH 探測邏輯僅支援 POSIX shell，fish shell 使用者偵測失敗 | P2／Open |
| [#107828](https://github.com/NousResearch/hermes-agent/issues/107828) | Backend pool 中 profile 名稱混淆問題 | P2／Open |
| [#107827](https://github.com/NousResearch/hermes-agent/issues/107827) | SSH 連線更新後復原流程卡住無法恢復 | P2／Open |
| [#107807](https://github.com/NousResearch/hermes-agent/issues/107807) | Replay 清理機制可能損毀既有工具呼叫結果 | Open |
| [#107780](https://github.com/NousResearch/hermes-agent/issues/107780) | 啟動 TUI 時，最上層的 `--reasoning` 覆寫參數會被忽略 | P2／Open |

過去 24 小時內未查到具引用來源的 X（Twitter）官方公告或 Reddit 討論串直接針對上述 issue 或 v0.21.1 展開新一輪討論，故本節僅列出可查證的 GitHub 一手資料，不臆測社群反應熱度。

---

*報告生成時間：2026-09-11 UTC*
