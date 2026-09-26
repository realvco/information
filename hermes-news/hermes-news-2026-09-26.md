# Hermes-Agent 每日情報 — 2026-09-26

> 資料來源涵蓋截至 2026-09-26 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **目前穩定版仍為 v0.21.5（v2026.9.24）**：過去 24 小時內查無更新版本。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

2. **Claude Opus 5.5 模型註冊 PR 已合併進 main**：昨日追蹤的 [PR #119444](https://github.com/NousResearch/hermes-agent/pull/119444) 已於 9/25 由作者 salvage 進新 PR [#122633](https://github.com/NousResearch/hermes-agent/pull/122633)，並已於 **2026-09-26 由 kshitijk4poor 合併進 main**（commit `cda2374`）。#122633 讓 Opus 5.5 出現在 Anthropic／Bedrock 選單，並讓 Bedrock 上的 Opus 5／5.5 取得 1M context；PR 說明明確排除「強制 thinking」相關變更，該部分留給 [#121016](https://github.com/NousResearch/hermes-agent/pull/121016) 處理。#121016 本身過去 24 小時仍為 open、CI 全過、尚無 maintainer 核准。

3. **新回報：CometAPI 搭配 Claude Opus 5.5 thinking 觸發驗證錯誤**：[issue #122672](https://github.com/NousResearch/hermes-agent/issues/122672)（open，P2，9/25 開立）指出 v0.21.5+2168 上呼叫 CometAPI 時出現 `'_.enabled' is not supported for this model. Use '_.adaptive' and 'output_config.effort'` 驗證錯誤，屬於 Opus 5.5 thinking 參數格式相容性問題的延伸案例，過去 24 小時尚無對應 PR。

4. **AWS Bedrock GPT-6 路由修復與另一 PR 衝突，待 maintainer 整併**：[PR #122116](https://github.com/NousResearch/hermes-agent/pull/122116)（open，9/25 開立）已被標記與既有 [PR #115931](https://github.com/NousResearch/hermes-agent/pull/115931) 有衝突，需 maintainer 決定如何整併後才能合併；另有使用者於 9/25 獨立驗證回報 Bedrock 測試套件全過、無異議。Azure Foundry 端的同類修復 [PR #120327](https://github.com/NousResearch/hermes-agent/pull/120327)（P3）過去 24 小時無新進展。

5. **本地模型：Ollama 閒置 VRAM 自動釋放 PR 脫離 draft 狀態**：[PR #120944](https://github.com/NousResearch/hermes-agent/pull/120944) 已於 9/24 由作者標示為「ready for review」，但完整 Python 測試套件仍有 3 項未分類失敗、型別檢查未能完整跑完（exit 137），過去 24 小時尚無 reviewer 回應。

6. **Gemini 圖像生成路線分歧未解**：Nano Banana 外掛提案 [#120851](https://github.com/NousResearch/hermes-agent/pull/120851) 遭政策駁回一案維持原狀；同類的原生 Google AI Studio 圖像後端提案 [#121183](https://github.com/NousResearch/hermes-agent/pull/121183) 過去 24 小時仍未見 maintainer 表態是否適用相同「vendor 外掛不進核心樹」政策。

7. **新一批 `sweeper:risk-security-boundary` 安全性 issue 於 9/25～9/26 開立**：至少 12 則，涵蓋 cron 失敗時 stderr／主機路徑外洩至聊天通知（[#123264](https://github.com/NousResearch/hermes-agent/issues/123264)）、Telegram 模型選單按鈕未做群組白名單驗證（[#123261](https://github.com/NousResearch/hermes-agent/issues/123261)）、Mattermost 附件在寄件者授權前即快取（[#123231](https://github.com/NousResearch/hermes-agent/issues/123231)）、MCP OAuth state 僅以伺服器名稱為 key 導致改指向新 URL 時被重用（[#123226](https://github.com/NousResearch/hermes-agent/issues/123226)）、`profiles.get_asset` 未檢查資產名稱可讀取範圍外圖片（[#123222](https://github.com/NousResearch/hermes-agent/issues/123222)）、macOS Keychain 鏡射在 `security -i` 行數限制處截斷 Claude Code 憑證（[#123184](https://github.com/NousResearch/hermes-agent/issues/123184)，與長期追蹤的 [PR #118199](https://github.com/NousResearch/hermes-agent/pull/118199) 主題相關）等，詳見第 4 節完整列表。**註：清單受頁面顯示筆數限制，實際總數可能更多，未逐則人工核對時間戳精確性。**

8. **昨日追蹤的其餘項目多數無新進展**：Anthropic 模型探測忽略 `ANTHROPIC_BASE_URL`（[#120844](https://github.com/NousResearch/hermes-agent/issues/120844)）、Claude OAuth 過期欄位損毀（[#118714](https://github.com/NousResearch/hermes-agent/issues/118714)）、可信 proxy 保留簽章 thinking block 功能請求（[#120723](https://github.com/NousResearch/hermes-agent/issues/120723)）、Vertex `notify` 參數 400 問題（[#109115](https://github.com/NousResearch/hermes-agent/issues/109115)）、llama.cpp context 溢位訊息辨識問題（[#117793](https://github.com/NousResearch/hermes-agent/issues/117793)），以及前一批 6 則指標性安全性 issue（[#121841](https://github.com/NousResearch/hermes-agent/issues/121841)、[#121756](https://github.com/NousResearch/hermes-agent/issues/121756)、[#121705](https://github.com/NousResearch/hermes-agent/issues/121705)、[#121665](https://github.com/NousResearch/hermes-agent/issues/121665)、[#121649](https://github.com/NousResearch/hermes-agent/issues/121649)、[#121021](https://github.com/NousResearch/hermes-agent/issues/121021)）皆仍為 open、查無新評論。

9. **社群媒體：一則未經直接驗證的 X 貼文提及 Opus 5.5 上線 Hermes Agent**：據搜尋結果，Nous Research 創辦人 teknium1 疑似發文宣布「Claude Opus 5.5 現已可透過 Nous Portal 於 Hermes Agent 使用」，但 x.com 原始貼文因網路政策無法直接擷取確認，精確時間與內文皆未證實，僅供參考（[鏡像頁面](https://www.techtwitter.com/tweet/f2626d69-2354-49e4-aa4a-9908de198a4e)）。過去 24 小時查無 r/LocalLLaMA、r/selfhosted 或其他部落格對 Hermes-Agent 的具體討論串。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）
- **Opus 5.5 模型註冊已落地**：[#119444](https://github.com/NousResearch/hermes-agent/pull/119444) 經 salvage 後以 [#122633](https://github.com/NousResearch/hermes-agent/pull/122633) 之姿於 2026-09-26 合併進 main，Opus 5.5 出現在 Anthropic／Bedrock 選單、Bedrock Opus 5/5.5 取得 1M context。「強制 thinking」修復留給 [#121016](https://github.com/NousResearch/hermes-agent/pull/121016)（open，CI 全過，仍待 maintainer 核准）獨立處理。
- **新 bug**：[#122672](https://github.com/NousResearch/hermes-agent/issues/122672)（open，P2）CometAPI 呼叫 Opus 5.5 thinking 時因 `_.enabled` 參數已棄用（應改用 `_.adaptive`／`output_config.effort`）而觸發驗證錯誤，尚無對應 PR。
- **無新進展**：[#120844](https://github.com/NousResearch/hermes-agent/issues/120844) Anthropic 模型探測忽略 `ANTHROPIC_BASE_URL`；[#120723](https://github.com/NousResearch/hermes-agent/issues/120723) 可信 proxy 保留簽章 thinking block 功能請求；[#118714](https://github.com/NousResearch/hermes-agent/issues/118714) OAuth 過期欄位損毀；[#118199](https://github.com/NousResearch/hermes-agent/pull/118199) Claude Code Keychain refresh token 誤耗修復（惟新開的安全性 issue [#123184](https://github.com/NousResearch/hermes-agent/issues/123184) 涉及同一 macOS Keychain 憑證主題，尚待確認是否與此 PR 有直接關聯）。

### GPT（OpenAI／Azure／Bedrock）
- **Bedrock 修復與既有 PR 衝突**：[#122116](https://github.com/NousResearch/hermes-agent/pull/122116)（open）將 GPT-6 系列改走 Mantle Responses endpoint，但與 [#115931](https://github.com/NousResearch/hermes-agent/pull/115931) 存在衝突，需 maintainer 決定整併方式；另有使用者 9/25 獨立驗證 Bedrock 測試套件全過。
- **無新進展**：Azure Foundry 修復 [#120327](https://github.com/NousResearch/hermes-agent/pull/120327)（open，P3）過去 24 小時無新評論。

### Gemini（Google）
- **圖像生成政策爭議延續**：Nano Banana 外掛提案 [#120851](https://github.com/NousResearch/hermes-agent/pull/120851) 政策駁回維持原狀；原生 Google AI Studio 圖像後端提案 [#121183](https://github.com/NousResearch/hermes-agent/pull/121183)（open）過去 24 小時仍未見 maintainer 表態。

### 本地模型／Local Runtime
- **Ollama 閒置 VRAM 自動釋放脫離 draft**：[PR #120944](https://github.com/NousResearch/hermes-agent/pull/120944) 已於 9/24 轉為「ready for review」，惟完整測試套件仍有 3 項未分類失敗、型別檢查未跑完，過去 24 小時無 reviewer 回應。
- llama.cpp context 溢位訊息辨識問題（[#117793](https://github.com/NousResearch/hermes-agent/issues/117793)）狀態未變，仍 open、無新評論。

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 類型 | 重點 |
|------|----------|------|------|
| **v0.21.5（v2026.9.24）** | 2026-09-24 10:09 UTC | Patch rollup | **目前最新穩定版**；過去 24 小時內查無更新版本 |
| v0.21.4（v2026.9.21） | 2026-09-21 18:10 | Patch rollup | host-wide gateway singleton lock、`--format stream-json`、`skills.auto_load`、12 個新社群外掛 |
| v0.21.3（v2026.9.14） | 2026-09-14 | Patch rollup | remote gateway 登入失效修復、refresh token 併發合併、refresh 移出 event loop |
| v0.21.2（v2026.9.11） | 2026-09-11 | Patch rollup | state.db 可靠性戰役、multiplexed profile 隔離強化、密碼隱藏式憑證保險箱 |
| v0.21.1（v2026.9.7） | 2026-09-07 | Patch rollup | 效能、MCP 授權、cron、delegation 修復 |

past-24h 內已合併進 main、但尚未打入下一個 tag 的重點變更：Claude Opus 5.5 模型註冊（Anthropic／Bedrock 選單顯示、Bedrock 1M context，[#122633](https://github.com/NousResearch/hermes-agent/pull/122633)）。同日另有多筆合併項目（gateway／Slack、桌面應用、更新機制、approval-timeout、keychain CA、背景結果、stale-breaker 等修復），但過去 24 小時內具體合併總量未能從公開頁面精確核對，暫不列出估計數字。官方仍表示 v0.21.0 以來累積的完整功能列表與貢獻者名單將於 v0.22.0 一併補齊。（[Releases 總覽](https://github.com/NousResearch/hermes-agent/releases)）

---

## 4. 社群討論與已知問題

**本日最值得留意的是 Claude Opus 5.5 模型註冊（#122633）正式合併進 main，以及新一批至少 12 則 `sweeper:risk-security-boundary` 安全性 issue 集中在 Telegram／Mattermost／MCP OAuth／本機憑證等多個管道開立。**

| Issue／PR | 說明 | 優先度／狀態 |
|------|------|----------|
| [#122633](https://github.com/NousResearch/hermes-agent/pull/122633) | Opus 5.5 模型註冊 salvage（catalog＋Bedrock 1M context） | 已合併（2026-09-26） |
| [#121016](https://github.com/NousResearch/hermes-agent/pull/121016) | Opus 5.5「強制 thinking」修復，CI 全過、待核准 | ci-reviewed／Open |
| [#122672](https://github.com/NousResearch/hermes-agent/issues/122672) | CometAPI＋Opus 5.5 thinking 參數驗證錯誤 | P2／Open |
| [#120844](https://github.com/NousResearch/hermes-agent/issues/120844) | Anthropic 模型探測忽略 `ANTHROPIC_BASE_URL` | 未標記／Open |
| [#122116](https://github.com/NousResearch/hermes-agent/pull/122116) | AWS Bedrock GPT-6 Responses 路由修復，與 #115931 衝突待整併 | P2／Open |
| [#120327](https://github.com/NousResearch/hermes-agent/pull/120327) | Azure Foundry gpt-6-luna／sol 需 Responses API | P3／Open |
| [#120851](https://github.com/NousResearch/hermes-agent/pull/120851) | Nano Banana image_gen 外掛遭 maintainer 政策駁回 | Closed（未合併） |
| [#121183](https://github.com/NousResearch/hermes-agent/pull/121183) | 原生 Google AI Studio 圖像生成後端提案 | Open（政策待表態） |
| [#120944](https://github.com/NousResearch/hermes-agent/pull/120944) | 閒置 Ollama 模型記憶體壓力下自動釋放 VRAM | Open（ready for review，3 項測試待釐清） |
| [#123264](https://github.com/NousResearch/hermes-agent/issues/123264) | cron 失敗 stderr／主機路徑外洩至聊天通知 | sweeper:risk-security-boundary／Open |
| [#123261](https://github.com/NousResearch/hermes-agent/issues/123261) | Telegram 模型選單按鈕未做群組白名單驗證 | sweeper:risk-security-boundary／Open |
| [#123231](https://github.com/NousResearch/hermes-agent/issues/123231) | Mattermost 附件在寄件者授權前即下載快取 | sweeper:risk-security-boundary／Open |
| [#123226](https://github.com/NousResearch/hermes-agent/issues/123226) | MCP OAuth state 僅以伺服器名稱為 key，改指向新 URL 遭重用 | sweeper:risk-security-boundary／Open |
| [#123222](https://github.com/NousResearch/hermes-agent/issues/123222) | `profiles.get_asset` 未檢查資產名稱，可讀取範圍外圖片 | sweeper:risk-security-boundary／Open |
| [#123184](https://github.com/NousResearch/hermes-agent/issues/123184) | macOS Keychain 鏡射於 `security -i` 行數限制處截斷憑證 | sweeper:risk-security-boundary／Open |
| （另有至少 6 則同批 9/25 開立的 `sweeper:risk-security-boundary` issue：[#122932](https://github.com/NousResearch/hermes-agent/issues/122932)、[#122750](https://github.com/NousResearch/hermes-agent/issues/122750)、[#122665](https://github.com/NousResearch/hermes-agent/issues/122665)、[#122614](https://github.com/NousResearch/hermes-agent/issues/122614)、[#122601](https://github.com/NousResearch/hermes-agent/issues/122601)、[#122600](https://github.com/NousResearch/hermes-agent/issues/122600)） | 涵蓋 sudo stdin guard 繞過、終端機／背景日誌洩漏密鑰、multiplexed profile MCP 連線劫持、RPC 緩衝區溢位風險、憑證池復原誤隔離、MoA session 因 OAuth 輪替死亡等 | sweeper:risk-security-boundary |
| [#121841](https://github.com/NousResearch/hermes-agent/issues/121841) | 憑證池於 5xx 不輪替（過去 24 小時查無新進展） | sweeper:risk-security-boundary／Open |
| [#121756](https://github.com/NousResearch/hermes-agent/issues/121756) | `auto` 模式輔助呼叫誤用已撤銷 OAuth token（過去 24 小時查無新進展） | sweeper:risk-security-boundary／Open |
| [#121705](https://github.com/NousResearch/hermes-agent/issues/121705) | Multiplex gateway 忽略次要 profile 的 slash 權限閘門（過去 24 小時查無新進展） | sweeper:risk-security-boundary／Open |
| [#121665](https://github.com/NousResearch/hermes-agent/issues/121665) | device-code 登入推論 URL 未做 host allowlist 檢查（過去 24 小時查無新進展） | sweeper:risk-security-boundary／Open |
| [#121649](https://github.com/NousResearch/hermes-agent/issues/121649) | `nous` 缺漏於單次使用 refresh token 池（過去 24 小時查無新進展） | sweeper:risk-security-boundary／Open |
| [#121021](https://github.com/NousResearch/hermes-agent/issues/121021) | Bitwarden 端點可注入終端機跳脫序列（過去 24 小時查無新進展） | sweeper:risk-security-boundary／Open |
| [#118714](https://github.com/NousResearch/hermes-agent/issues/118714) | OAuth 過期欄位損毀（過去 24 小時查無新進展） | P2／Open |
| [#118199](https://github.com/NousResearch/hermes-agent/pull/118199) | Hermes 誤耗 Claude Code 專屬 Keychain refresh token（過去 24 小時查無新進展，惟新 issue #123184 涉及同一憑證主題） | P2／sweeper:risk-security-boundary／Open |
| [#120723](https://github.com/NousResearch/hermes-agent/issues/120723) | 要求為可信 Anthropic 相容 proxy 保留已簽章 thinking block（過去 24 小時查無新進展） | 未標記／Open |
| [#109115](https://github.com/NousResearch/hermes-agent/issues/109115) | Vertex-backed OpenAI 相容 endpoint 拒絕 `notify` 參數（過去 24 小時查無新進展） | P2／Open |
| [#117793](https://github.com/NousResearch/hermes-agent/issues/117793) | llama.cpp context 溢位錯誤格式未被辨識（過去 24 小時查無新進展） | P2／Open |

社群媒體方面，僅查到一則未經原始頁面驗證的 X 貼文（疑似 teknium1 宣布 Opus 5.5 上線 Nous Portal／Hermes Agent），因平台存取限制無法直接核對時間與內文，列為未證實資訊；過去 24 小時查無 r/LocalLLaMA、r/selfhosted 或其他部落格對 Hermes-Agent 的具體討論串。上述新增安全性 issue 清單受頁面顯示筆數限制，可能仍有未列出的項目，且未逐則人工核對精確時間戳。

---

*報告生成時間：2026-09-26 UTC*
