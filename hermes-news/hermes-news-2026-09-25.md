# Hermes-Agent 每日情報 — 2026-09-25

> 資料來源涵蓋截至 2026-09-25 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **新版 v0.21.5（v2026.9.24）已釋出**：2026-09-24 10:09 UTC 發布，一次收斂約 460 個已合併 PR、1,610 個非合併 commit、4,828 個變更檔案（+164,132／-149,440 行）。官方僅列出「未整理重點」：Desktop 外掛 SDK 強化（composer API、側欄導覽偏好）、新增取代 MCP 分頁的 Connectors 頁面、支援 RTL/LTR 的多語系 Desktop 目錄、語音快捷鍵可重新綁定、設定載入與工具註冊表效能優化，以及擴充含 GPT-6 系列與 Claude Opus 5.5 的模型目錄。完整、附貢獻者名單的正式 release notes 官方表示將延後至 v0.22.0 一併發布。（[Release 頁面](https://github.com/NousResearch/hermes-agent/releases/tag/v2026.9.24)）

2. **Claude Opus 5.5「強制 thinking」修復進入收斂階段**：舊 PR [#119419](https://github.com/NousResearch/hermes-agent/pull/119419) 已被 maintainer teknium1 「salvage（保留原作者掛名、解決與 main 的衝突後重新提交）」整併進新 PR [#121016](https://github.com/NousResearch/hermes-agent/pull/121016)（open，CI 110 項測試全過，已貼 `ci-reviewed` 標籤，但尚無 maintainer 核准），修復對應 issue [#120069](https://github.com/NousResearch/hermes-agent/issues/120069)；#119419 本身待 #121016 合併後即會關閉。模型註冊 PR [#119444](https://github.com/NousResearch/hermes-agent/pull/119444)（open）據 teknium1 說明與 #121016 有重疊，但仍保留額外範圍（catalog／context／Bedrock 註冊、Claude Code 版本回退 clamp、tool_choice→auto 降級）；使用者 pawelsu8 於 9/23 實測回報確認「原生 Claude 用戶端已提供 Opus 5.5 但 Hermes 未顯示」的根因正是 provider 模型目錄缺項。

3. **新回報：Anthropic 模型探測完全忽略 `ANTHROPIC_BASE_URL`**：[issue #120844](https://github.com/NousResearch/hermes-agent/issues/120844)（open）詳細指出探測永遠打向寫死的 Anthropic 官方端點，導致使用 relay／gateway 的使用者看到錯誤的模型清單；且探測失敗時會把靜態目錄快取為「成功」達 7 天無法重新探測。回報者已附根因分析、重現步驟與提案修補，但過去 24 小時尚無 PR 對應。

4. **Gemini「Nano Banana」圖像生成外掛提案遭 maintainer 以政策理由駁回**：[PR #120851](https://github.com/NousResearch/hermes-agent/pull/120851) 已於 9/24 由 teknium1 關閉（未合併），理由是「vendor 圖像生成 provider 不會被加進核心樹」，並建議作者改以獨立外掛 repo 發布、再送交外掛目錄（審核流程較快）。值得留意的是同日另有 [PR #121183](https://github.com/NousResearch/hermes-agent/pull/121183)（open）提出以原生方式串接 Google AI Studio（直接使用 `GOOGLE_API_KEY`／`GEMINI_API_KEY`）的圖像生成後端，對應舊功能請求 [#97474](https://github.com/NousResearch/hermes-agent/issues/97474)；此案定位為「原生後端」而非「外掛」，是否會遭遇與 #120851 相同的政策疑慮，過去 24 小時內尚無 maintainer 表態。

5. **GPT-6 路由問題從 Azure Foundry 擴散到 AWS Bedrock**：Azure Foundry 修復 [PR #120327](https://github.com/NousResearch/hermes-agent/pull/120327)（open，P3）過去 24 小時無新進展；新回報／修復 [PR #122116](https://github.com/NousResearch/hermes-agent/pull/122116)（open，剛於 9/25 02:05 UTC 開立）則為 AWS Bedrock 平台提出同類修復，將 GPT-6 系列改走 Mantle Responses endpoint，顯示這類 provider-side routing 缺陷並非單一雲端獨有。

6. **本地模型新功能：閒置 Ollama 模型可在記憶體壓力下自動釋放 VRAM**：[PR #120944](https://github.com/NousResearch/hermes-agent/pull/120944)（open，作者標示應維持 draft 待進一步驗證，P3）新增預設關閉的 `local_runtime.ollama_idle_release` 選項，於 NVIDIA GPU／Apple Silicon 上每 15 秒監測記憶體壓力，需連續兩次偵測到閒置逾 60 秒且可用記憶體低於 5% 才觸發卸載，對應功能請求 [#120495](https://github.com/NousResearch/hermes-agent/issues/120495)，屬於先前 idle 自動卸載機制的後續延伸。作者回報 45 項新測試通過、`ruff` 與型別檢查皆過，但完整 Python 測試套件仍有 3 項未分類失敗待釐清。

7. **新一批 18 則安全性 issue（標記 `sweeper:risk-security-boundary`）於過去 24 小時開立，數量較前一日（21 則）略降，均為 open**：涵蓋憑證池未在 5xx 時輪替（[#121841](https://github.com/NousResearch/hermes-agent/issues/121841)）、`auto` 模式輔助呼叫在主 agent 靜默刷新後仍持續使用已撤銷的 Anthropic OAuth token（[#121756](https://github.com/NousResearch/hermes-agent/issues/121756)）、multiplex gateway 忽略次要 profile 的 slash 權限閘門設定（[#121705](https://github.com/NousResearch/hermes-agent/issues/121705)）、device-code 登入儲存 Portal 回傳推論 URL 時未做 host allowlist 檢查（[#121665](https://github.com/NousResearch/hermes-agent/issues/121665)）、`nous` 缺漏於單次使用 refresh token 池清單導致複製 profile 互相拖垮（[#121649](https://github.com/NousResearch/hermes-agent/issues/121649)）、Bitwarden 端點可透過 Rich markup 注入終端機跳脫序列（[#121021](https://github.com/NousResearch/hermes-agent/issues/121021)）等，詳見第 4 節完整列表。**註：這批 issue 只能確認開立於過去 24 小時區間內，未逐則人工核對時間戳精確性。**

8. **昨日追蹤的其餘項目狀態均未變動**：Claude OAuth 過期欄位損毀問題（[#118714](https://github.com/NousResearch/hermes-agent/issues/118714)）、Claude Code Keychain refresh token 誤耗修復（[#118199](https://github.com/NousResearch/hermes-agent/pull/118199)）、可信 Anthropic 相容 proxy 保留簽章 thinking block 的功能請求（[#120723](https://github.com/NousResearch/hermes-agent/issues/120723)）、Vertex `notify` 參數 400 問題（[#109115](https://github.com/NousResearch/hermes-agent/issues/109115)）與 llama.cpp context 溢位訊息辨識問題（[#117793](https://github.com/NousResearch/hermes-agent/issues/117793)）皆無新進展，仍為 open、無 reviewer。

9. **過去 24 小時 repo 活動量持續龐大**：以 GitHub Search API 統計，新開 issue 276 個、新開 PR 885 個，其中至少 198 個 PR 已合併；本日最大單一事件是 v0.21.5 版釋出本身，其餘合併項目多為前述 Gemini／GPT-6／安全性修復與常規維運性變更。（[PR 列表](https://github.com/NousResearch/hermes-agent/pulls)）

10. **社群媒體（X／Reddit）過去 24 小時內查無與 Hermes-Agent 相關的新官方公告或討論串**；本節所列均為可直接查證的 GitHub 一手資料。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）
- **Opus 5.5 強制 thinking 修復進入收斂**：#119419 已 salvage 進 [#121016](https://github.com/NousResearch/hermes-agent/pull/121016)（open，CI 全過，待核准），修復 [#120069](https://github.com/NousResearch/hermes-agent/issues/120069)；模型註冊 PR [#119444](https://github.com/NousResearch/hermes-agent/pull/119444)（open）保留額外範圍（catalog／Bedrock 註冊等），現場使用者已確認目錄缺項為根因（詳見第 1 節第 2 點）。
- **新 bug**：[#120844](https://github.com/NousResearch/hermes-agent/issues/120844)（open）Anthropic 模型探測忽略 `ANTHROPIC_BASE_URL`，探測失敗被誤快取為成功，尚無對應 PR。
- **功能請求無新進展**：[#120723](https://github.com/NousResearch/hermes-agent/issues/120723)（open）要求為可信 Anthropic 相容 proxy 保留已簽章 thinking block，過去 24 小時查無新評論。
- **安全性修復仍待審查**：[#118714](https://github.com/NousResearch/hermes-agent/issues/118714) OAuth 過期欄位損毀問題、[#118199](https://github.com/NousResearch/hermes-agent/pull/118199) Claude Code Keychain refresh token 誤耗問題，兩者皆無新進展、無 reviewer。

### GPT（OpenAI／Codex／Azure／Bedrock）
- **GPT-6 路由問題擴散到 Bedrock**：Azure Foundry 修復 [#120327](https://github.com/NousResearch/hermes-agent/pull/120327)（open，P3）無新進展；新開 [#122116](https://github.com/NousResearch/hermes-agent/pull/122116)（open）為 AWS Bedrock 補上同類修復，將 GPT-6 系列改走 Mantle Responses endpoint。
- 昨日已合併的 GPT-6 Terra picker 移除（[#119981](https://github.com/NousResearch/hermes-agent/pull/119981)）、Codex 相關 issue（[#119814](https://github.com/NousResearch/hermes-agent/issues/119814)、[#120177](https://github.com/NousResearch/hermes-agent/issues/120177)、[#120174](https://github.com/NousResearch/hermes-agent/issues/120174)）過去 24 小時查無新進展更新，暫不重複列出細節。

### Gemini（Google）
- **兩項 Gemini 修復已合併**：[#121046](https://github.com/NousResearch/hermes-agent/pull/121046)（已合併，9/24）修正 thinking tokens 未計入 usage／成本統計；[#121047](https://github.com/NousResearch/hermes-agent/pull/121047)（已合併，9/24）讓原生 Gemini adapter 正確轉譯 `response_format`，修復 pre-Gemini-3 模型「工具＋JSON 格式」觸發 400 的問題。
- **圖像生成路線分歧**：Nano Banana 外掛提案 [#120851](https://github.com/NousResearch/hermes-agent/pull/120851) 遭 maintainer 以「vendor 外掛不進核心樹」政策駁回（已關閉未合併）；同日另有原生後端提案 [#121183](https://github.com/NousResearch/hermes-agent/pull/121183)（open）對應舊功能請求 #97474，尚待 maintainer 表態是否也會被視為同類政策問題。
- **新增修復**：[#121479](https://github.com/NousResearch/hermes-agent/pull/121479)（open）修正 `promptFeedback` 區塊被誤判為 content filter 而中止串流；[#122060](https://github.com/NousResearch/hermes-agent/pull/122060)（open，port 自 opencode#51009）修正含 orphan required／draft-04 邊界／tuple／`$ref` 迴圈的 tool schema 觸發 400 的問題。

### 本地模型／Local Runtime
- **新功能（draft）**：[PR #120944](https://github.com/NousResearch/hermes-agent/pull/120944) 新增可選的閒置 Ollama VRAM 自動釋放機制，對應功能請求 [#120495](https://github.com/NousResearch/hermes-agent/issues/120495)；作者自陳測試套件仍有 3 項未分類失敗，建議先維持 draft。
- llama.cpp context 溢位訊息辨識問題（[#117793](https://github.com/NousResearch/hermes-agent/issues/117793)）狀態未變，仍 open、無新評論。

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 類型 | 重點 |
|------|----------|------|------|
| **v0.21.5（v2026.9.24）** | 2026-09-24 10:09 UTC | Patch rollup | **目前最新穩定版**；一次收斂約 460 個 PR、1,610 個 commit、4,828 個變更檔案；Desktop 外掛 SDK 強化、新 Connectors 頁面（取代 MCP 分頁）、多語系 RTL/LTR 目錄、GPT-6／Claude Opus 5.5 模型目錄擴充；完整 release notes 延後至 v0.22.0 一併發布 |
| v0.21.4（v2026.9.21） | 2026-09-21 18:10 | Patch rollup | host-wide gateway singleton lock、`--format stream-json`、`skills.auto_load`、12 個新社群外掛 |
| v0.21.3（v2026.9.14） | 2026-09-14 | Patch rollup | remote gateway 登入失效修復、refresh token 併發合併、refresh 移出 event loop |
| v0.21.2（v2026.9.11） | 2026-09-11 | Patch rollup | state.db 可靠性戰役、multiplexed profile 隔離強化、密碼隱藏式憑證保險箱 |
| v0.21.1（v2026.9.7） | 2026-09-07 | Patch rollup | 效能、MCP 授權、cron、delegation 修復 |

past-24h 內已合併進 main、但尚未打入下一個 tag 的重點變更：Gemini thinking tokens 計費修復（[#121046](https://github.com/NousResearch/hermes-agent/pull/121046)）；Gemini `response_format`＋工具 400 修復（[#121047](https://github.com/NousResearch/hermes-agent/pull/121047)）。其餘合併項目多為測試補強、docs 更新與常規維運性變更。官方明確表示 v0.21.0 以來累積的完整功能列表與貢獻者名單將於 v0.22.0 一併補齊。（[Releases 總覽](https://github.com/NousResearch/hermes-agent/releases)）

---

## 4. 社群討論與已知問題

**本日最值得留意的是 v0.21.5 大型 patch 版釋出、Claude Opus 5.5「強制 thinking」修復進入 salvage 收斂階段，以及 Gemini 圖像生成功能因核心樹政策問題出現路線分歧（vendor 外掛遭拒、原生後端提案待觀察）。**

| Issue／PR | 說明 | 優先度／狀態 |
|------|------|----------|
| [#121016](https://github.com/NousResearch/hermes-agent/pull/121016) | Salvage 整併 Opus 5.5 強制 thinking 修復，CI 全過、待核准 | ci-reviewed／Open |
| [#119444](https://github.com/NousResearch/hermes-agent/pull/119444) | Claude Opus 5.5 模型註冊（catalog／Bedrock 等額外範圍） | 未標記／Open |
| [#120844](https://github.com/NousResearch/hermes-agent/issues/120844) | Anthropic 模型探測忽略 `ANTHROPIC_BASE_URL`，探測失敗誤快取為成功 | 未標記／Open |
| [#120851](https://github.com/NousResearch/hermes-agent/pull/120851) | Nano Banana image_gen 外掛遭 maintainer 政策駁回 | Closed（未合併） |
| [#121183](https://github.com/NousResearch/hermes-agent/pull/121183) | 原生 Google AI Studio 圖像生成後端提案 | P3／Open |
| [#120327](https://github.com/NousResearch/hermes-agent/pull/120327) | Azure Foundry gpt-6-luna／sol 需 Responses API | P3／Open |
| [#122116](https://github.com/NousResearch/hermes-agent/pull/122116) | AWS Bedrock GPT-6 系列同類 Responses 路由修復 | 未標記／Open |
| [#120944](https://github.com/NousResearch/hermes-agent/pull/120944) | 閒置 Ollama 模型記憶體壓力下自動釋放 VRAM | P3／Open（draft） |
| [#121841](https://github.com/NousResearch/hermes-agent/issues/121841) | 憑證池於 5xx（500/502/503/529）不輪替，直接跳過 provider fallback | sweeper:risk-security-boundary／Open |
| [#121756](https://github.com/NousResearch/hermes-agent/issues/121756) | `auto` 模式輔助呼叫在主 agent 靜默刷新後仍用已撤銷 Anthropic OAuth token | sweeper:risk-security-boundary／Open |
| [#121705](https://github.com/NousResearch/hermes-agent/issues/121705) | Multiplex gateway 忽略次要 profile 的 slash 權限閘門設定 | sweeper:risk-security-boundary／Open |
| [#121665](https://github.com/NousResearch/hermes-agent/issues/121665) | device-code 登入儲存推論 URL 未做 host allowlist 檢查 | sweeper:risk-security-boundary／Open |
| [#121649](https://github.com/NousResearch/hermes-agent/issues/121649) | `nous` 缺漏於單次使用 refresh token 池，複製 profile 互相拖垮登出 | sweeper:risk-security-boundary／Open |
| [#121021](https://github.com/NousResearch/hermes-agent/issues/121021) | 惡意 Bitwarden 端點可透過 Rich markup 注入終端機跳脫序列 | sweeper:risk-security-boundary／Open |
| （另有 12 則同批 `sweeper:risk-security-boundary` issue：[#122072](https://github.com/NousResearch/hermes-agent/issues/122072)、[#122024](https://github.com/NousResearch/hermes-agent/issues/122024)、[#122013](https://github.com/NousResearch/hermes-agent/issues/122013)、[#121966](https://github.com/NousResearch/hermes-agent/issues/121966)、[#121932](https://github.com/NousResearch/hermes-agent/issues/121932)、[#121486](https://github.com/NousResearch/hermes-agent/issues/121486)、[#121402](https://github.com/NousResearch/hermes-agent/issues/121402)、[#121323](https://github.com/NousResearch/hermes-agent/issues/121323)、[#121278](https://github.com/NousResearch/hermes-agent/issues/121278)、[#121254](https://github.com/NousResearch/hermes-agent/issues/121254)、[#121002](https://github.com/NousResearch/hermes-agent/issues/121002)（closed）、[#120976](https://github.com/NousResearch/hermes-agent/issues/120976)（closed）） | 涵蓋憑證外洩、OAuth 流程、金鑰隔離等多面向 | sweeper:risk-security-boundary |
| [#118714](https://github.com/NousResearch/hermes-agent/issues/118714) | OAuth 過期欄位損毀導致可能重複使用一次性 refresh token（過去 24 小時查無新進展） | P2／Open |
| [#118199](https://github.com/NousResearch/hermes-agent/pull/118199) | Hermes 誤耗 Claude Code 專屬 Keychain refresh token（過去 24 小時查無新進展） | P2／sweeper:risk-security-boundary／Open |
| [#120723](https://github.com/NousResearch/hermes-agent/issues/120723) | 要求為可信 Anthropic 相容 proxy 保留已簽章 thinking block（過去 24 小時查無新進展） | 未標記／Open |
| [#109115](https://github.com/NousResearch/hermes-agent/issues/109115) | Vertex-backed OpenAI 相容 endpoint 因 `anyOf` schema 拒絕 `notify` 參數（過去 24 小時查無新進展） | P2／Open |
| [#117793](https://github.com/NousResearch/hermes-agent/issues/117793) | llama.cpp context 溢位錯誤格式未被辨識，回報者已有本地補丁待送 PR（過去 24 小時查無新進展） | P2／Open |

過去 24 小時內未查到具引用來源的 X（Twitter）官方公告或 Reddit／Discord 具體討論串內容，故本節僅列出可查證的 GitHub 一手資料。上述新增的 18 則安全性 issue 僅能確認開立於過去 24 小時區間內，未逐則人工核對精確時間戳。

---

*報告生成時間：2026-09-25 UTC*
