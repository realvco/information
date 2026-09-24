# Hermes-Agent 每日情報 — 2026-09-24

> 資料來源涵蓋截至 2026-09-24 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **目前最新穩定版仍為 v0.21.4（v2026.9.21，2026-09-21 釋出）**：過去 24 小時內未查到新版本或新 tag。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

2. **Claude Opus 5.5「強制 thinking」相容性問題持續拉鋸、尚未收斂**：昨日追蹤的模型註冊 PR [#119444](https://github.com/NousResearch/hermes-agent/pull/119444) 與「關閉 thinking 觸發 HTTP 400」修復 PR [#119419](https://github.com/NousResearch/hermes-agent/pull/119419) 過去 24 小時仍為 open、無 reviewer；同一問題出現多個並行嘗試：[#119793](https://github.com/NousResearch/hermes-agent/pull/119793)（已關閉未合併）、[#119919](https://github.com/NousResearch/hermes-agent/pull/119919)（open，新增 claude-opus-5-5 至 picker 並將其排到選單第 3 位而非第 14 位）、[#120075](https://github.com/NousResearch/hermes-agent/pull/120075)（open）、[#120077](https://github.com/NousResearch/hermes-agent/pull/120077)（已關閉未合併）。重複回報的 issue [#120069](https://github.com/NousResearch/hermes-agent/issues/120069) 仍為 open。另新增一則相關 bug：[#120844](https://github.com/NousResearch/hermes-agent/issues/120844) 回報 Anthropic 模型探測完全忽略 `ANTHROPIC_BASE_URL`，picker 只會顯示靜態目錄，且探測失敗還會被誤快取為「成功」。功能請求 [#120723](https://github.com/NousResearch/hermes-agent/issues/120723) 則要求為可信的 Anthropic 相容 proxy 提供選項以保留已簽章的 thinking block。

3. **GPT-6 Terra 選項確認移除，Sol／Luna 在 Azure Foundry 上出現路由問題**：移除尚未發布的 `gpt-6-terra` picker 選項的 PR 已合併（[#119981](https://github.com/NousResearch/hermes-agent/pull/119981)，取代昨日追蹤的 #119505）。同時新回報 Azure Foundry 上的 gpt-6-luna／gpt-6-sol 在同時啟用工具與 reasoning 時，仍被送往不相容的 chat completions endpoint 而非 Responses API，導致 HTTP 400（重複開立的 [#120249](https://github.com/NousResearch/hermes-agent/issues/120249) 已關閉、[#120263](https://github.com/NousResearch/hermes-agent/issues/120263) 仍 open），修復 PR [#120327](https://github.com/NousResearch/hermes-agent/pull/120327)（open，P3）已將 `gpt-6` 加入 Responses-only 前綴清單，作者稱新測試與 171 個既有測試皆通過。

4. **Gemini 側新增 Nano Banana 圖像生成外掛**：[PR #120851](https://github.com/NousResearch/hermes-agent/pull/120851)（open）新增 `image_gen` 外掛，後端採用 Google AI Studio Gemini「Nano Banana」圖像模型。

5. **新一批 21 則安全性 issue（標記 `sweeper:risk-security-boundary`）於過去 24 小時集中湧入，均為 open**：涵蓋憑證池／OAuth 相關（[#120815](https://github.com/NousResearch/hermes-agent/issues/120815) 過期憑證池覆寫已輪替的 refresh token，永久性登出；[#120741](https://github.com/NousResearch/hermes-agent/issues/120741) Codex 共用登入 refresh 會連坐隔離獨立登入）、憑證外洩（[#120481](https://github.com/NousResearch/hermes-agent/issues/120481) TTS/STT env passthrough 把啟動 profile 的憑證轉發給被服務的 profile；[#120305](https://github.com/NousResearch/hermes-agent/issues/120305)／[#120310](https://github.com/NousResearch/hermes-agent/issues/120310) 延續昨日同類 profile 隔離問題）、身分驗證繞過（[#120604](https://github.com/NousResearch/hermes-agent/issues/120604) session-attach 握手接受 echo 回覆，本機 loopback listener 可冒充擁有者；[#120506](https://github.com/NousResearch/hermes-agent/issues/120506) 純文字核准回覆繞過 slash 管理員閘門，非管理員可寫入 allowlist）等，詳見第 4 節完整列表。**註：這批 issue 只能確認開立於過去 24 小時區間內，未逐則人工核對時間戳精確性。**

6. **昨日追蹤的其餘項目狀態均未變動**：Claude OAuth 過期欄位損毀問題（[#118714](https://github.com/NousResearch/hermes-agent/issues/118714)）與 Claude Code Keychain refresh token 誤耗修復（[#118199](https://github.com/NousResearch/hermes-agent/pull/118199)）仍為 open、無 reviewer；Vertex `notify` 參數 400 問題（[#109115](https://github.com/NousResearch/hermes-agent/issues/109115)）與 llama.cpp context 溢位訊息辨識問題（[#117793](https://github.com/NousResearch/hermes-agent/issues/117793)）也都無新進展。

7. **本地模型側出現新的 keep-alive 設定需求**：[issue #119834](https://github.com/NousResearch/hermes-agent/issues/119834) 與對應 PR [#119841](https://github.com/NousResearch/hermes-agent/pull/119841)（均 open）提出讓本地 LLM runtime 的閒置 keep-alive 時間可設定，與稍早合併的閒置自動卸載機制（[#119380](https://github.com/NousResearch/hermes-agent/pull/119380)）屬同一主題的後續精修。

8. **過去 24 小時 repo 活動量極大**：僅以 GitHub Search API 統計，新開 PR 883 個、新開 issue 293 個，其中至少 243 個 PR 已合併，但多為 desktop 多語系（新增法／德／西文介面）、格式化、測試補強等常規維運性變更，未見類似前兩日「模型目錄大改版」等級的單一重大功能發布。（[PR 列表](https://github.com/NousResearch/hermes-agent/pulls)）

9. **社群媒體（X／Reddit）過去 24 小時內查無與 Hermes-Agent 相關的新官方公告或討論串**；本節所列均為可直接查證的 GitHub 一手資料。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）
- **Opus 5.5 強制 thinking 相容性問題仍未收斂**：詳見第 1 節第 2 點；核心模型註冊 PR [#119444](https://github.com/NousResearch/hermes-agent/pull/119444) 與「關閉 thinking」修復 PR [#119419](https://github.com/NousResearch/hermes-agent/pull/119419) 過去 24 小時無新進展，同時有多個並行/重複的修復嘗試被關閉或仍卡在 open。
- **新 bug**：[#120844](https://github.com/NousResearch/hermes-agent/issues/120844)（open）Anthropic 模型探測忽略 `ANTHROPIC_BASE_URL`，picker 僅顯示靜態目錄，探測失敗還會被誤快取為成功。
- **新功能請求**：[#120723](https://github.com/NousResearch/hermes-agent/issues/120723)（open）要求為可信的 Anthropic 相容 proxy 提供選項保留已簽章 thinking block。
- **安全性修復仍待審查**：[#118714](https://github.com/NousResearch/hermes-agent/issues/118714) OAuth 過期欄位損毀問題、[#118199](https://github.com/NousResearch/hermes-agent/pull/118199) Claude Code Keychain refresh token 誤耗問題，兩者皆無新進展、無 reviewer。

### GPT（OpenAI／Codex／Azure）
- **GPT-6 Terra 移除已合併**：[#119981](https://github.com/NousResearch/hermes-agent/pull/119981)（已合併，2026-09-23）正式從 picker 移除尚未發布的 `gpt-6-terra`。
- **Azure Foundry 路由問題**：gpt-6-luna／gpt-6-sol 在啟用工具＋reasoning 時被錯誤送往 chat completions 而非 Responses API（重複 issue [#120249](https://github.com/NousResearch/hermes-agent/issues/120249) 已關閉、[#120263](https://github.com/NousResearch/hermes-agent/issues/120263) open），修復 PR [#120327](https://github.com/NousResearch/hermes-agent/pull/120327)（open，P3）已加入 `gpt-6` 至 Responses-only 前綴清單並通過新測試與既有測試。
- **Codex 相關新 issue**：[#119814](https://github.com/NousResearch/hermes-agent/issues/119814)（open）Codex Responses 回傳 `status=failed` 但帶有非空 output 時會跳過 retry／fallback；[#120177](https://github.com/NousResearch/hermes-agent/issues/120177)（open）named profile 中第一個 Codex OAuth 憑證回報成功卻未被持久化；[#120174](https://github.com/NousResearch/hermes-agent/issues/120174)（open）Codex lifecycle-only stream 繞過 watchdog，需等待 600／900 秒才重試，對應修復 PR [#120197](https://github.com/NousResearch/hermes-agent/pull/120197)（已關閉，未合併）、[#120209](https://github.com/NousResearch/hermes-agent/pull/120209)（open）。

### Gemini（Google）
- **新功能**：[#120851](https://github.com/NousResearch/hermes-agent/pull/120851)（open）新增 `image_gen` 外掛，採用 Google AI Studio 的 Gemini「Nano Banana」圖像生成後端。
- 昨日追蹤的 [#118445](https://github.com/NousResearch/hermes-agent/pull/118445)、[#117927](https://github.com/NousResearch/hermes-agent/pull/117927) 與 Vertex `notify` 參數問題（[#109115](https://github.com/NousResearch/hermes-agent/issues/109115)）過去 24 小時查無新進展，仍為 open。

### 本地模型／Local Runtime
- **keep-alive 可設定化**：[issue #119834](https://github.com/NousResearch/hermes-agent/issues/119834)／[PR #119841](https://github.com/NousResearch/hermes-agent/pull/119841)（均 open）提出讓本地 LLM runtime 的閒置 keep-alive 時間可設定，屬於閒置自動卸載機制（[#119380](https://github.com/NousResearch/hermes-agent/pull/119380)）的後續延伸。
- llama.cpp context 溢位訊息辨識問題（[#117793](https://github.com/NousResearch/hermes-agent/issues/117793)）狀態未變，仍 open、無新評論。

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 類型 | 重點 |
|------|----------|------|------|
| **v0.21.4（v2026.9.21）** | 2026-09-21 18:10 | Patch rollup | **目前最新穩定版**；host-wide gateway singleton lock、`--format stream-json`、`skills.auto_load`、12 個新社群外掛（過去 24 小時內無新版釋出） |
| v0.21.3（v2026.9.14） | 2026-09-14 | Patch rollup | remote gateway 登入失效修復、refresh token 併發合併、refresh 移出 event loop |
| v0.21.2（v2026.9.11） | 2026-09-11 | Patch rollup | state.db 可靠性戰役、multiplexed profile 隔離強化、密碼隱藏式憑證保險箱 |
| v0.21.1（v2026.9.7） | 2026-09-07 | Patch rollup | 效能、MCP 授權、cron、delegation 修復 |
| v0.21.0「The Pantheon Release」 | 2026-08-31 | 主版本 | Bot Mode、agent-to-agent 通訊、cron 記憶與連續性、MCP 集中管理面板 |

past-24h 內已合併進 main、但尚未打入新 tag 的重點變更：GPT-6 Terra picker 選項移除（[#119981](https://github.com/NousResearch/hermes-agent/pull/119981)）；桌面應用新增法／德／西文介面語言（[#120829](https://github.com/NousResearch/hermes-agent/pull/120829)）；桌面應用新增文字方向（Auto/RTL/LTR）外觀設定（[#120822](https://github.com/NousResearch/hermes-agent/pull/120822)）；webhook 回覆可見原始投遞內容（[#120825](https://github.com/NousResearch/hermes-agent/pull/120825)）。其餘合併項目多為測試補強與格式化，未見與上一版本同等級的重大功能發布。這些預期將出現在下一個 patch rollup 中。（[Releases 總覽](https://github.com/NousResearch/hermes-agent/releases)）

---

## 4. 社群討論與已知問題

**本日最值得留意的是 Claude Opus 5.5「強制 thinking」相容性問題持續拉鋸、出現多個互相競爭又陸續關閉的修復嘗試仍未收斂，以及新一批 21 則安全性 issue（涵蓋憑證池、憑證外洩、身分驗證繞過等多個面向）於過去 24 小時集中開立。**

| Issue／PR | 說明 | 優先度／狀態 |
|------|------|----------|
| [#119444](https://github.com/NousResearch/hermes-agent/pull/119444) | 新增 Claude Opus 5.5 模型註冊（過去 24 小時查無新進展） | 未標記／Open |
| [#119419](https://github.com/NousResearch/hermes-agent/pull/119419) | 修復 Opus 5.5 強制 thinking 導致「關閉 thinking」觸發 HTTP 400（過去 24 小時查無新進展） | 未標記／Open |
| [#120844](https://github.com/NousResearch/hermes-agent/issues/120844) | Anthropic 模型探測忽略 `ANTHROPIC_BASE_URL`，探測失敗被誤快取為成功 | 未標記／Open |
| [#120263](https://github.com/NousResearch/hermes-agent/issues/120263) | Azure Foundry gpt-6-luna／sol 需要 Responses API 才能同時支援工具＋reasoning | 未標記／Open |
| [#120815](https://github.com/NousResearch/hermes-agent/issues/120815) | 過期憑證池覆寫已輪替的 OAuth refresh token，永久性登出 | sweeper:risk-security-boundary／Open |
| [#120604](https://github.com/NousResearch/hermes-agent/issues/120604) | session-attach 握手接受 echo 回覆，本機 loopback listener 可冒充擁有者 | sweeper:risk-security-boundary／Open |
| [#120506](https://github.com/NousResearch/hermes-agent/issues/120506) | 純文字核准回覆繞過 slash 管理員閘門，非管理員可寫入 allowlist | sweeper:risk-security-boundary／Open |
| [#120481](https://github.com/NousResearch/hermes-agent/issues/120481) | TTS/STT env passthrough 把啟動 profile 憑證轉發給被服務的 profile | sweeper:risk-security-boundary／Open |
| [#120741](https://github.com/NousResearch/hermes-agent/issues/120741) | 共用登入下 Codex refresh 會連坐隔離獨立登入 | sweeper:risk-security-boundary／Open |
| （另有 16 則同批 `sweeper:risk-security-boundary` issue：[#120728](https://github.com/NousResearch/hermes-agent/issues/120728)、[#120704](https://github.com/NousResearch/hermes-agent/issues/120704)、[#120655](https://github.com/NousResearch/hermes-agent/issues/120655)、[#120608](https://github.com/NousResearch/hermes-agent/issues/120608)、[#120602](https://github.com/NousResearch/hermes-agent/issues/120602)、[#120576](https://github.com/NousResearch/hermes-agent/issues/120576)、[#120310](https://github.com/NousResearch/hermes-agent/issues/120310)、[#120305](https://github.com/NousResearch/hermes-agent/issues/120305)、[#120219](https://github.com/NousResearch/hermes-agent/issues/120219)、[#120216](https://github.com/NousResearch/hermes-agent/issues/120216)、[#120177](https://github.com/NousResearch/hermes-agent/issues/120177)、[#119989](https://github.com/NousResearch/hermes-agent/issues/119989)、[#119985](https://github.com/NousResearch/hermes-agent/issues/119985)、[#119928](https://github.com/NousResearch/hermes-agent/issues/119928)、[#119910](https://github.com/NousResearch/hermes-agent/issues/119910)、[#119829](https://github.com/NousResearch/hermes-agent/issues/119829)） | 涵蓋憑證外洩、OAuth 流程、金鑰隔離等多面向，均為 open | sweeper:risk-security-boundary／Open |
| [#118714](https://github.com/NousResearch/hermes-agent/issues/118714) | OAuth 過期欄位損毀導致可能重複使用一次性 refresh token（過去 24 小時查無新進展） | 未標記／Open |
| [#118199](https://github.com/NousResearch/hermes-agent/pull/118199) | Hermes 誤耗 Claude Code 專屬 Keychain refresh token（過去 24 小時查無新進展） | P2／sweeper:risk-security-boundary／Open |
| [#109115](https://github.com/NousResearch/hermes-agent/issues/109115) | Vertex-backed OpenAI 相容 endpoint 因 `anyOf` schema 拒絕 `notify` 參數（過去 24 小時查無新進展） | P2／Open |
| [#117793](https://github.com/NousResearch/hermes-agent/issues/117793) | llama.cpp context 溢位錯誤格式未被辨識（過去 24 小時查無新進展） | P2／Open |

過去 24 小時內未查到具引用來源的 X（Twitter）官方公告或 Reddit／Discord 具體討論串內容，故本節僅列出可查證的 GitHub 一手資料。上述新增的 21 則安全性 issue 僅能確認開立於過去 24 小時區間內，未逐則人工核對精確時間戳。

---

*報告生成時間：2026-09-24 UTC*
