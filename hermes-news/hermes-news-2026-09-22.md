# Hermes-Agent 每日情報 — 2026-09-22

> 資料來源涵蓋截至 2026-09-22 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **新版本 v0.21.4（v2026.9.21）於 9/21 18:10 由 teknium1 釋出，結束了 v0.21.3 以來一週的無 tag 空窗**：本次 patch rollup 涵蓋約 1,800（精確為 1,812）個合併 PR、5,071 次 non-merge commits、5,169 個變更檔案（+312,961／−62,855 行），關閉 2,116 個 issues。（[Release Notes](https://github.com/NousResearch/hermes-agent/releases/tag/v2026.9.21)）

2. **v0.21.4 重點功能**：新增 host-wide gateway singleton lock（Desktop 改為附掛既有 host backend，不再重複啟動）；CLI 新增 `--format stream-json` 結構化 JSONL 輸出；`skills.auto_load` 可將指定 skills 釘選進每個 session 的 prompt；Desktop 新增字型選擇、本地引擎一鍵更新、外掛可直接從 Plugins hub 移除；gateway 新增可設定的未授權私訊 `decline` 行為；`mcp.discovery_concurrency` 可調整 MCP 探索併發數；新增 `session_search` 的 after/before 範圍查詢與 OR-relaxed 重試；影音目錄新增 LTX 2.5 與 Kling O3；新增 12 個社群外掛（Tailscale、SSH、Shodan、Terminal、RSS 等）。release notes 註明完整版將併入 v0.22.0。（[同上](https://github.com/NousResearch/hermes-agent/releases/tag/v2026.9.21)）

3. **Claude 快取新功能：`cache_ttl: auto` 已合併（[PR #118337](https://github.com/NousResearch/hermes-agent/pull/118337)）**：依 session 類型自動選擇 Anthropic 快取層級 —— 人類互動節奏（CLI／Desktop／通訊平台）用 1 小時層級，subagent／cron／oneshot 等機器節奏用 5 分鐘層級。團隊引用兩天內 26,096 次 API 呼叫的遙測數據：63% 的 CLI 快取寫入是閒置 5–60 分鐘後的冷重寫；1 小時層級可為互動 session 省下約 42% 成本，但會讓 subagent 場景多付約 49%。282 筆測試通過。

4. **新增疑似安全性問題：OAuth 過期欄位損毀可能導致「重複使用一次性 refresh token」（[#118714](https://github.com/NousResearch/hermes-agent/issues/118714)）**：`expires_at`／`expires_in` 若為非數值（字串、NaN、Infinity、1e400 等）會在多個 credential reader（MCP token 儲存、Claude Code token、Microsoft Graph、device OAuth flow、匿名驗證）中觸發未捕捉例外，或在驗證檢查中被靜默略過，導致可能 POST 出已花費過的單次性 refresh token，有並發驗證下的競態風險。修復 PR [#118715](https://github.com/NousResearch/hermes-agent/pull/118715) 已提出（約 30 筆新測試），但尚無 reviewer 指派，仍為 open。

5. **另一則 Claude Code 憑證相關安全修復仍待審查：[#118199](https://github.com/NousResearch/hermes-agent/pull/118199)**：修復 macOS 上 Hermes 誤用 Claude Code 專屬 Keychain refresh token 的問題——當 Keychain 與 `~/.claude/.credentials.json` 兩份憑證的 token family 分岔時，先前邏輯會挑「較晚過期」的一組，可能消耗 Claude Code 自身的一次性 refresh token，迫使使用者重新登入。修復後改為檔案端 lineage 優先，且遇到刷新失敗時最多只借用 Keychain 的 access token、絕不動用其 refresh token。25 筆 keychain 相關測試通過，標記 `sweeper:risk-security-boundary`，目前尚無 reviewer。

6. **Gemini 側過去 24 小時無新增重大缺陷**，僅有既有 PR 持續推進：[#118463](https://github.com/NousResearch/hermes-agent/pull/118463)（MCP schema 的 if/then/else、oneOf 片段修復，已由 teknium1 合併）、[#118445](https://github.com/NousResearch/hermes-agent/pull/118445)（丟棄開頭非 user turn 以避免 Gemini 400，open）、[#117927](https://github.com/NousResearch/hermes-agent/pull/117927)（thought signature sentinel replay 與平行工具呼叫修復，open，6 項任務完成）。昨日追蹤的 [#109115](https://github.com/NousResearch/hermes-agent/issues/109115)（Vertex `notify` 參數 400）仍 open、無新進展。

7. **GPT／Codex 側新增數則小型穩定性修復**：[#118708](https://github.com/NousResearch/hermes-agent/pull/118708) 修復未完成工具呼叫導致無限迴圈；[#118705](https://github.com/NousResearch/hermes-agent/pull/118705)「Codex 移植」`/goal` 在連續三次空回應後改為暫停，避免靜默卡死；[#118707](https://github.com/NousResearch/hermes-agent/pull/118707) 修復 Feishu adapter 重複投遞的工具進度訊息。

8. **本地模型／local-models 側僅有社群外掛新增**：[#118340](https://github.com/NousResearch/hermes-agent/pull/118340) 新增社群 Zvec memory backend（`memory-zvec`）至 plugin catalog，標記 `area/local-models`、`provider/ollama`，open 狀態，過去 24 小時內未見核心推理路徑相關修復或新缺陷。

9. **新一輪 issue 湧入（#1187xx 區間）以 Desktop／Skills 系統為主**：包含 skill_manage 批次回滾把 symlink 目錄轉成實體目錄（[#118699](https://github.com/NousResearch/hermes-agent/issues/118699)）、失敗的 skill_manage 批次把半套用的 skill 留在 skills root（[#118691](https://github.com/NousResearch/hermes-agent/issues/118691)）、錯誤格式的 `lock.json`／`taps.json` 會讓所有 skills 指令崩潰（[#118686](https://github.com/NousResearch/hermes-agent/issues/118686)）、Desktop 背景審查 session 的 transcript 顯示失敗（[#118693](https://github.com/NousResearch/hermes-agent/issues/118693)）。這批問題與特定 LLM provider 無直接關聯，暫不展開，僅列入第 4 節追蹤。

10. **社群媒體（X／Reddit）過去 24 小時內查無新的官方公告或具引用價值的討論串**；本節所列均為可直接查證的 GitHub 一手資料。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）
- **新功能已合併**：[#118337](https://github.com/NousResearch/hermes-agent/pull/118337) `cache_ttl: auto`，依 session 節奏自動切換 1 小時／5 分鐘 Anthropic 快取層級，詳見第 1 節第 3 點。
- **安全性修復進行中**：[#118714](https://github.com/NousResearch/hermes-agent/issues/118714)／[#118715](https://github.com/NousResearch/hermes-agent/pull/118715) OAuth 過期欄位損毀問題；[#118199](https://github.com/NousResearch/hermes-agent/pull/118199) Claude Code Keychain refresh token 誤耗問題。兩者皆涉及 Anthropic 原生 Messages API 憑證路徑，目前均為 open、尚待審查。
- **其他 open PR**：[#118328](https://github.com/NousResearch/hermes-agent/pull/118328)「fix(anthropic): honor every reasoning effort on the manual thinking path」，修復手動 thinking 路徑未正確套用所有 reasoning effort 設定的問題，標記 Anthropic native Messages API、agent runtime、medium priority。

### GPT（OpenAI／Codex）
- [#118708](https://github.com/NousResearch/hermes-agent/pull/118708)（open）：未完成的工具呼叫不再導致無限迴圈。
- [#118705](https://github.com/NousResearch/hermes-agent/pull/118705)（開發中）：`/goal` 移植 Codex 的作法，連續三次空回應後自動暫停而非靜默卡住。
- 過去 24 小時內未見 Codex app-server 系列的新增重大缺陷回報，多為既有問題持續收斂。

### Gemini（Google）
- [#118463](https://github.com/NousResearch/hermes-agent/pull/118463)（已合併）：MCP schema 修復工具讓 `if/then/else`、`oneOf` 約束片段在 schema 修復流程中存活，避免部分 MCP 工具定義被破壞。
- [#118445](https://github.com/NousResearch/hermes-agent/pull/118445)（open）：丟棄對話開頭的非 user turn，避免觸發 Gemini HTTP 400。
- [#117927](https://github.com/NousResearch/hermes-agent/pull/117927)（open，6 項任務完成）：thought signature sentinel replay 與平行工具呼叫修復。
- [#114116](https://github.com/NousResearch/hermes-agent/pull/114116)（open，12/19 任務完成）：Hermes Desktop 新增 Gemini Live 即時語音模式，屬長期開發中的功能型 PR。

### 本地模型／Local Runtime
- [#118340](https://github.com/NousResearch/hermes-agent/pull/118340)（open）：plugin catalog 新增社群記憶體後端 `memory-zvec`（Zvec），標記 `area/local-models`、`provider/ollama`。
- 過去 24 小時內未見 llama.cpp 或本地推理核心路徑的新增修復或缺陷回報；昨日提及的 [#117793](https://github.com/NousResearch/hermes-agent/issues/117793)（llama.cpp context 溢位格式未被辨識）狀態未變。

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 類型 | 重點 |
|------|----------|------|------|
| **v0.21.4（v2026.9.21）** | 2026-09-21 18:10 | Patch rollup | **目前最新穩定版**；~1,812 個合併 PR、5,071 commits；host-wide gateway singleton lock、`--format stream-json`、`skills.auto_load`、Desktop 字型選擇／一鍵更新引擎／外掛移除、12 個新社群外掛 |
| v0.21.3（v2026.9.14） | 2026-09-14 | Patch rollup | 338 個合併 PR；remote gateway 登入失效修復、refresh token 併發合併、refresh 移出 event loop |
| v0.21.2（v2026.9.11） | 2026-09-11 | Patch rollup | state.db 可靠性戰役、multiplexed profile 隔離強化、密碼隱藏式憑證保險箱 |
| v0.21.1（v2026.9.7） | 2026-09-07 | Patch rollup | 5,139 commits／632 merged PR，效能、MCP 授權、cron、delegation 修復 |
| v0.21.0「The Pantheon Release」 | 2026-08-31 | 主版本 | Bot Mode、agent-to-agent 通訊、cron 記憶與連續性、即時子代理操控、MCP 集中管理面板、agent 操控桌面瀏覽器 |

past-24h 內已合併進 main、但尚未打入新 tag 的重點變更：Claude `cache_ttl: auto`（#118337）、cache 清理 kwarg 修復（#118677）、Desktop 設定頁 profile 讀寫修復（#118449）、多工複用 host 下 plugin hook 綁定作用域修復（#118624）、`write_file` 部分重讀後保留已知未變內容（#118588）、model-provider 外掛依 profile home 解析（#118582）、cache／terminal 的 24 小時閒置清理（#118575）。這些將出現在下一個 patch rollup 中。（[Releases 總覽](https://github.com/NousResearch/hermes-agent/releases)）

---

## 4. 社群討論與已知問題

**本日最值得留意的是 v0.21.4 正式釋出，以及兩則尚待審查的 OAuth／憑證安全性問題（#118714／#118715 過期欄位損毀、#118199 Claude Code Keychain refresh token 誤耗）**；Claude 快取自動分層（#118337）則是本日最實用的成本優化功能。

| Issue／PR | 說明 | 優先度／狀態 |
|------|------|----------|
| [#118714](https://github.com/NousResearch/hermes-agent/issues/118714) | OAuth `expires_at`／`expires_in` 損毀導致多個 credential reader 崩潰或靜默略過驗證，可能重複使用一次性 refresh token | 未標記／Open |
| [#118715](https://github.com/NousResearch/hermes-agent/pull/118715) | 上述問題的修復 PR，約 30 筆新測試，尚無 reviewer | 未標記／Open |
| [#118199](https://github.com/NousResearch/hermes-agent/pull/118199) | 修復 Hermes 誤耗 Claude Code 專屬 Keychain refresh token 的問題，25 筆測試通過 | P2／sweeper:risk-security-boundary／Open |
| [#118686](https://github.com/NousResearch/hermes-agent/issues/118686) | 錯誤格式的 `lock.json`／`taps.json` 導致所有 skills 指令崩潰 | 未標記／Open |
| [#118691](https://github.com/NousResearch/hermes-agent/issues/118691) | 失敗的 skill_manage 批次會把半套用的 skill 留在 skills root | 未標記／Open |
| [#118699](https://github.com/NousResearch/hermes-agent/issues/118699) | skill_manage 批次回滾會把 symlink 的 skill 目錄轉成實體目錄 | 未標記／Open |
| [#118693](https://github.com/NousResearch/hermes-agent/issues/118693) | Desktop 背景審查 session 的 transcript 顯示失敗 | 未標記／Open |
| [#109115](https://github.com/NousResearch/hermes-agent/issues/109115) | Vertex-backed OpenAI 相容 endpoint 因 `anyOf` schema 拒絕 `notify` 參數，所有 turn 收到 400（過去 24 小時查無新進展） | P2／Open |
| [#117793](https://github.com/NousResearch/hermes-agent/issues/117793) | llama.cpp context 溢位錯誤格式未被 `model_metadata.py` 辨識（過去 24 小時查無新進展） | P2／Open |

過去 24 小時內未查到具引用來源的 X（Twitter）官方公告或 Reddit／Discord 具體討論串內容，故本節僅列出可查證的 GitHub 一手資料。

---

*報告生成時間：2026-09-22 UTC*
