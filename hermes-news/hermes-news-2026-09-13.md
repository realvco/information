# Hermes-Agent 每日情報 — 2026-09-13

> 資料來源涵蓋截至 2026-09-13 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **穩定版仍為 v0.21.2（v2026.9.11），過去 24 小時內無新版釋出**。開發主線持續高速產出修復性 commit 與 PR（issue／PR 編號已推進至 #1095xx 區間），但尚未累積到下一個 patch rollup 的門檻。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

2. **[#107830](https://github.com/NousResearch/hermes-agent/issues/107830)（Anthropic 串流工具呼叫遇畸形 JSON 永久失敗，P1）出現整合性新方案 [#109324](https://github.com/NousResearch/hermes-agent/pull/109324)**：該 PR 收斂先前三個互相競爭的修復提案（[#107833](https://github.com/NousResearch/hermes-agent/pull/107833)、[#107864](https://github.com/NousResearch/hermes-agent/pull/107864)、[#108583](https://github.com/NousResearch/hermes-agent/pull/108583)，均已被標記為 superseded）與另一個新提案 [#109056](https://github.com/NousResearch/hermes-agent/pull/109056)，改採「偵測到畸形 JSON 時，僅對該次請求關閉 `eager_input_streaming`、以緩衝模式重試一次」的策略，避免全面關閉串流造成延遲，但截至目前四個相關 PR 均尚未合併，issue 仍為 Open。

3. **新增兩起安全邊界類議題**：Codex 手動設定的憑證會無條件套用 `auth.json` 內另一個帳號 refresh 後的 token，導致跨帳號憑證污染（[#109198](https://github.com/NousResearch/hermes-agent/issues/109198)，P2，標記 `sweeper:risk-security-boundary`，已有對應修復 PR [#109200](https://github.com/NousResearch/hermes-agent/pull/109200) 待合併）；以及 Gateway `/yolo` 指令未在實際變更 session 狀態的「side-effect boundary」重新驗證 admin 權限，若被納入 `user_allowed_commands`，非 admin 使用者可繞過原本應僅限 admin 的危險指令核准機制（[#109495](https://github.com/NousResearch/hermes-agent/issues/109495)，P2，已有修復 PR [#109503](https://github.com/NousResearch/hermes-agent/pull/109503)、[#109500](https://github.com/NousResearch/hermes-agent/pull/109500) 待合併）。

4. **昨日回報的 Gemini 配額誤判問題（[#108656](https://github.com/NousResearch/hermes-agent/issues/108656)，P2）仍未修復**：對應的修復 PR [#108661](https://github.com/NousResearch/hermes-agent/pull/108661) 仍未合併，issue 本身也無新進度留言。

5. **新增兩起 provider 層 LLM 串接問題**：`auxiliary.vision.provider` / `auxiliary.vision.model` 設定對 Anthropic 被靜默忽略，實際仍套用預設 vision 模型（[#109111](https://github.com/NousResearch/hermes-agent/issues/109111)，P2）；透過 Vertex 轉發的 OpenAI 相容 endpoint 會拒絕 `terminal.notify` 工具內含多分支 `anyOf` 的 schema，導致該工具在 Vertex-backed 路徑上直接以 400 錯誤中斷整輪對話（[#109115](https://github.com/NousResearch/hermes-agent/issues/109115)，P2）。詳見第 2、4 節。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）
- **[#107830](https://github.com/NousResearch/hermes-agent/issues/107830) 進度**：見第 1 節，新整合方案 [#109324](https://github.com/NousResearch/hermes-agent/pull/109324) 為目前最可能被採用的修復路線，但尚未合併；在此之前，暫時解法仍是手動關閉 `fine-grained-tool-streaming-2025-05-14` beta header。
- **新回報：`auxiliary.vision.provider` / `.model` 對 Anthropic 無效**（[#109111](https://github.com/NousResearch/hermes-agent/issues/109111)，P2）：使用者將輔助 vision 模型指定為 Anthropic 端點後，實際請求仍走預設 vision provider，設定被靜默忽略、無任何警告或錯誤訊息。
- 官方 provider 文件無更新，`ANTHROPIC_API_KEY` 直接付費、`hermes model` 走 Claude Code OAuth 等安排維持不變。（[Provider 文件](https://github.com/NousResearch/hermes-agent/blob/main/website/docs/integrations/providers.md)）

### GPT（OpenAI）
- **新回報：Codex 手動憑證跨帳號採用他人 token**（[#109198](https://github.com/NousResearch/hermes-agent/issues/109198)，P2，安全邊界類）：當憑證池中存在多個 `chatgpt_account_id` 不同的手動 Codex 帳號、且其中一個帳號的 token 被刷新寫回單例 `auth.json` 時，另一個帳號設定會無條件同步、採用錯誤帳號的 token，造成配額隔離失效與非預期的重新登入需求。修復 PR [#109200](https://github.com/NousResearch/hermes-agent/pull/109200) 已提出，待合併。
- 過去 24 小時未查到其他 GPT／Codex 相關的新 provider 層問題。

### Gemini（Google）
- **[#108656](https://github.com/NousResearch/hermes-agent/issues/108656) 進度**：見第 1 節，配額誤判與 `RetryInfo.retryDelay` 被忽略的問題仍未修復，修復 PR [#108661](https://github.com/NousResearch/hermes-agent/pull/108661) 尚未合併。
- **新回報：Vertex-backed OpenAI 相容 endpoint 拒絕 `terminal.notify` 的多分支 `anyOf` schema**（[#109115](https://github.com/NousResearch/hermes-agent/issues/109115)，P2）：部分透過 Vertex 轉發的 OpenAI 相容路徑對工具 schema 的驗證較嚴格，遇到 `anyOf` 多分支定義會直接以 400 拒絕請求，導致該工具呼叫整輪失敗。
- Vertex 路徑預設模型仍為 `google/gemini-3-flash-preview`，provider 文件無其他更新。

### Auxiliary Model
- 過去 24 小時未查到與 auxiliary model 選型相關的新討論或設定變更（[#109111](https://github.com/NousResearch/hermes-agent/issues/109111) 屬 Anthropic vision 設定失效，已列入上方 Claude 小節）。

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 類型 | 重點 |
|------|----------|------|------|
| v0.21.2（v2026.9.11） | 2026-09-11 | Patch rollup | state.db 可靠性戰役、multiplexed profile 隔離強化、密碼隱藏式憑證保險箱、SHA 鎖定 plugin 目錄；**過去 24 小時內為最新穩定版，無新版釋出** |
| v0.21.1（v2026.9.7） | 2026-09-07 | Patch rollup | 5,139 commits／632 merged PR，效能、MCP 授權、cron、delegation 修復 |
| v0.21.0「The Pantheon Release」 | 2026-08-31 | 主版本 | Bot Mode、agent-to-agent 通訊、cron 記憶與連續性、即時子代理操控、MCP 集中管理面板、agent 操控桌面瀏覽器 |
| v0.20.6（v2026.8.27） | 2026-08-27 | Patch | ~525 merged PR：consent-gated profile browsing、MCP 目錄擴充 |

（[Releases 總覽](https://github.com/NousResearch/hermes-agent/releases)）

---

## 4. 社群討論與已知問題

過去 24 小時新開 issue／PR 量依然龐大（repo 開發節奏極快），以下為除上方 LLM 相關議題外，值得留意的 P1／P2 等級非 LLM 基礎設施問題（均為官方 GitHub repo 上的公開 issue，尚待官方處理，非已證實修復）：

| Issue／PR | 說明 | 優先度／狀態 |
|------|------|------|
| [#109495](https://github.com/NousResearch/hermes-agent/issues/109495) | Gateway `/yolo` 未在 side-effect boundary 重新驗證 admin 權限，非 admin 可能繞過危險指令核准機制；修復 PR [#109503](https://github.com/NousResearch/hermes-agent/pull/109503)／[#109500](https://github.com/NousResearch/hermes-agent/pull/109500) 待合併 | P2／Open |
| [#109198](https://github.com/NousResearch/hermes-agent/issues/109198) | Codex 手動憑證跨帳號採用他人 token（詳見第 2 節） | P2／Open |
| [#109422](https://github.com/NousResearch/hermes-agent/issues/109422) | 共用同一 OAuth MCP server URL 的多個 multiplexed profile 會靜默互相套用彼此憑證 | P1／Open |
| [#109440](https://github.com/NousResearch/hermes-agent/issues/109440) | `hermes chat -q -m <direct alias>` 會用該別名對應的 API key 送出請求，可能誤用非預期帳號 | P1／Open |
| [#109397](https://github.com/NousResearch/hermes-agent/issues/109397) | Dashboard TUI 在已設定有效 model provider 的情況下仍顯示「需要設定 model provider」 | P1／Open |
| [#109357](https://github.com/NousResearch/hermes-agent/issues/109357) | Desktop 應用會把用戶端筆電的出廠設定覆寫回同步的設定檔 | P1／Open |
| [#109247](https://github.com/NousResearch/hermes-agent/issues/109247) | `_config_model_provider()` 無法辨識 custom／openrouter 等 provider 類型 | P1／Open |
| [#108862](https://github.com/NousResearch/hermes-agent/issues/108862) | Cron 任務若單一投遞環節耗時超過 30 秒，會使該次執行自身的 fire-claim 心跳被判定逾時 | P1／Open |

過去 24 小時內未查到具引用來源的 X（Twitter）官方公告或 Reddit 討論串，針對 v0.21.2 或上述 issue 展開新一輪討論，故本節僅列出可查證的 GitHub 一手資料，不臆測社群反應熱度。

---

*報告生成時間：2026-09-13 UTC*
