# Hermes-Agent 每日情報 — 2026-09-23

> 資料來源涵蓋截至 2026-09-23 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **目前最新穩定版仍為 v0.21.4（v2026.9.21，2026-09-21 釋出）**：過去 24 小時內未查到新版本或新 tag。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

2. **重大合併：GPT-6 Sol／Terra／Luna 系列模型正式加入 Codex／OpenRouter／Nous 目錄（[PR #119410](https://github.com/NousResearch/hermes-agent/pull/119410)，已合併）**：取代先前的 5.6 系列，同時新增 Codex 900k context 變體與對應 OAuth 支援；Sol、Luna 已公開定價，Terra 因尚未正式發布故暫無定價資訊。此變更同時衍生數個目錄相容性 bug：client_version 為 `0.0.0` 的舊版 CLI 收到凍結舊清單而看不到 Sol／Luna（[#119412](https://github.com/NousResearch/hermes-agent/issues/119412)，已關閉）；修復 PR [#119436](https://github.com/NousResearch/hermes-agent/pull/119436)、[#119494](https://github.com/NousResearch/hermes-agent/pull/119494) 均已關閉（後者由作者自行關閉，被 #119412 的修復取代）；[#119505](https://github.com/NousResearch/hermes-agent/pull/119505)（open）則是移除 picker 中尚未發布的 `gpt-6-terra` 選項。

3. **Claude 側新增 Claude Opus 5.5 模型註冊（[PR #119444](https://github.com/NousResearch/hermes-agent/pull/119444)，open）**：支援 1M context／128K output，涵蓋 Anthropic 原生 API 與 AWS Bedrock，並強制啟用 adaptive thinking、調整 OAuth 版本需求。伴隨修復 PR [#119419](https://github.com/NousResearch/hermes-agent/pull/119419)（open）：Opus 5.5 因強制 thinking，先前「關閉 thinking」設定會觸發 HTTP 400，此 PR 改為在該情境下省略 disable 參數。另一衛星 repo `hermes-plugin-claude-subscription-directsdk` 也新開 [issue #22](https://github.com/NousResearch/hermes-plugin-claude-subscription-directsdk/issues/22)，回報透過 admission relay 使用 Opus 5.5 時遇到相同的強制 thinking 問題並觀察到安全防護拒答（該 repo 非 hermes-agent 本體，僅供參考）。

4. **新一批安全性相關 issue（標記 `sweeper:risk-security-boundary`）於 9/22 集中湧入，聚焦 secret scope／profile 隔離漏洞，共 12 則，均為 open**：包含 `get_secret` 在 profile-scoped 讀取失敗時會 fallback 到 `os.environ`（[#119458](https://github.com/NousResearch/hermes-agent/issues/119458)、[#119279](https://github.com/NousResearch/hermes-agent/issues/119279)）、profile-scope binder 於 scope setup 失敗時外洩 `HERMES_HOME` override（[#119485](https://github.com/NousResearch/hermes-agent/issues/119485)）、allowlist 誤判「共用 `@` 前段字串的任意 user_id」即視為通過（[#119446](https://github.com/NousResearch/hermes-agent/issues/119446)）、Docker 共享模式下所有 session 共用同一個掛載於 `/workspace` 的 `$HOME`（[#119170](https://github.com/NousResearch/hermes-agent/issues/119170)）等。**註：這批 issue 只能確認開立日期為 9/22，無法精確驗證到小時，不排除其中一兩則其實早於昨日回報的截止時間即已存在。**

5. **昨日追蹤的安全性／相容性項目狀態均未變動**：Claude OAuth 過期欄位損毀問題（[#118714](https://github.com/NousResearch/hermes-agent/issues/118714)／[#118715](https://github.com/NousResearch/hermes-agent/pull/118715)）與 Claude Code Keychain refresh token 誤耗修復（[#118199](https://github.com/NousResearch/hermes-agent/pull/118199)）仍為 open、無 reviewer；Gemini 側 [#118445](https://github.com/NousResearch/hermes-agent/pull/118445)、[#117927](https://github.com/NousResearch/hermes-agent/pull/117927) 及 Vertex `notify` 參數 400 問題（[#109115](https://github.com/NousResearch/hermes-agent/issues/109115)）、llama.cpp context 溢位訊息辨識問題（[#117793](https://github.com/NousResearch/hermes-agent/issues/117793)）也都無新進展。

6. **新功能合併：Setup agent 可在 approval card 中直接搜尋外掛目錄並安裝，工具同一 turn 內即可使用（[PR #119724](https://github.com/NousResearch/hermes-agent/pull/119724)，已合併）**，伴隨一批外掛目錄／MCP 即時啟用相關 PR 合併（[#119644](https://github.com/NousResearch/hermes-agent/pull/119644)、[#119633](https://github.com/NousResearch/hermes-agent/pull/119633)、[#119605](https://github.com/NousResearch/hermes-agent/pull/119605)、[#119365](https://github.com/NousResearch/hermes-agent/pull/119365)、[#119349](https://github.com/NousResearch/hermes-agent/pull/119349)）。

7. **新 issue 湧入以 Desktop／CLI 使用體驗類 bug 為主**：`/v1/responses` stream 缺少 `content_part.added`／`done` 事件，導致 OpenAI SDK 的 `responses.stream()` 每次都丟 `IndexError`（[#119758](https://github.com/NousResearch/hermes-agent/issues/119758)）；官方文件中的 CLI 範例指令實測會出現 argparse 錯誤（[#119756](https://github.com/NousResearch/hermes-agent/issues/119756)）；Desktop「取消安裝」流程會遺留 git／uv 背景程序並卡死（[#119753](https://github.com/NousResearch/hermes-agent/issues/119753)）；新 worktree 的 base-branch 選單無限重新列出分支（[#119745](https://github.com/NousResearch/hermes-agent/issues/119745)）。其中 webhook 環境變數（`WEBHOOK_SECRET`／`WEBHOOK_PORT`）在透過 `config.yaml` 啟用平台時被忽略的問題（[#119763](https://github.com/NousResearch/hermes-agent/issues/119763)）已有同日修復 PR（[#119765](https://github.com/NousResearch/hermes-agent/pull/119765)）。這批問題與特定 LLM provider 無直接關聯，僅列入第 4 節追蹤。

8. **社群媒體（X／Reddit）過去 24 小時內查無與 Hermes-Agent 相關的新官方公告或討論串**；本節所列均為可直接查證的 GitHub 一手資料。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）
- **新模型 open PR**：[#119444](https://github.com/NousResearch/hermes-agent/pull/119444) 新增 Claude Opus 5.5 模型註冊（1M context／128K output、Anthropic 原生＋Bedrock、強制 adaptive thinking）；[#119419](https://github.com/NousResearch/hermes-agent/pull/119419) 修復因強制 thinking 導致「關閉 thinking」設定觸發 HTTP 400 的問題。相關的 [PR #119304](https://github.com/NousResearch/hermes-agent/pull/119304)（在 picker 中加入 claude-opus-5.5）已由 teknium1 關閉，推測與 #119444 整合或取代。
- **安全性修復仍待審查**：[#118714](https://github.com/NousResearch/hermes-agent/issues/118714)／[#118715](https://github.com/NousResearch/hermes-agent/pull/118715) OAuth 過期欄位損毀問題、[#118199](https://github.com/NousResearch/hermes-agent/pull/118199) Claude Code Keychain refresh token 誤耗問題，兩者皆無新進展、無 reviewer。
- **其他 open PR**：[#118985](https://github.com/NousResearch/hermes-agent/pull/118985) 新增 cache-safe reasoning effort policy 外掛；[#119238](https://github.com/NousResearch/hermes-agent/pull/119238) 修復 Desktop 在配額重置後 fallback chain 未正確恢復的問題。
- 衛星 repo `hermes-plugin-claude-subscription-directsdk` 新開 [issue #22](https://github.com/NousResearch/hermes-plugin-claude-subscription-directsdk/issues/22)，回報 Opus 5.5 強制 thinking 相關問題（非 hermes-agent 本體 issue，僅供參考）。

### GPT（OpenAI／Codex）
- **重大合併**：[#119410](https://github.com/NousResearch/hermes-agent/pull/119410) GPT-6 Sol／Terra／Luna 加入模型目錄，取代 5.6 系列，含 Codex 900k context 變體與 OAuth 支援，詳見第 1 節第 2 點。
- 合併後的目錄相容性收斂：[#119412](https://github.com/NousResearch/hermes-agent/issues/119412)（已關閉）、[#119436](https://github.com/NousResearch/hermes-agent/pull/119436)（已關閉）、[#119494](https://github.com/NousResearch/hermes-agent/pull/119494)（已關閉，被取代）；[#119505](https://github.com/NousResearch/hermes-agent/pull/119505)（open）移除尚未發布的 `gpt-6-terra` 選項。
- [#119156](https://github.com/NousResearch/hermes-agent/pull/119156)（open）：修復 Desktop provider 設定流程中連線設定未被保留的問題。

### Gemini（Google）
- [#119277](https://github.com/NousResearch/hermes-agent/pull/119277)（open）：將 `response_format` 正確傳入原生 Gemini `generationConfig`。
- [#118948](https://github.com/NousResearch/hermes-agent/pull/118948)（open）：reasoning tokens 應計入 `completion_tokens`。
- [#118882](https://github.com/NousResearch/hermes-agent/pull/118882)（open）：`gemini-live-bridge` 加入外掛目錄。
- [#83882](https://github.com/NousResearch/hermes-agent/pull/83882)（已關閉）：`MALFORMED_FUNCTION_CALL` turn 應直接失敗，而非回傳空的成功結果。
- [#119036](https://github.com/NousResearch/hermes-agent/pull/119036)（已關閉）：Gemini image generation 外掛。
- 昨日追蹤的 [#118445](https://github.com/NousResearch/hermes-agent/pull/118445)、[#117927](https://github.com/NousResearch/hermes-agent/pull/117927) 與 [#109115](https://github.com/NousResearch/hermes-agent/issues/109115) 均無新進展，仍為 open。

### 本地模型／Local Runtime
- [#119380](https://github.com/NousResearch/hermes-agent/pull/119380)（open）：本地輔助壓縮模型閒置一段 TTL 後自動卸載，節省記憶體。
- [#119432](https://github.com/NousResearch/hermes-agent/pull/119432)（open）：修復 Halogen 模型 max_tokens 上限／剩餘空間錯誤訊息的解析。
- [#119228](https://github.com/NousResearch/hermes-agent/pull/119228)（open）：llama.cpp 別名正確解析回受管理的本地 endpoint。
- [#119198](https://github.com/NousResearch/hermes-agent/pull/119198)（open）：本地推理路徑支援 ternary（1.58-bit）GGUF tensor。
- 昨日提及的 [#117793](https://github.com/NousResearch/hermes-agent/issues/117793)（llama.cpp context 溢位訊息未被辨識）狀態未變。

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 類型 | 重點 |
|------|----------|------|------|
| **v0.21.4（v2026.9.21）** | 2026-09-21 18:10 | Patch rollup | **目前最新穩定版**；host-wide gateway singleton lock、`--format stream-json`、`skills.auto_load`、12 個新社群外掛（過去 24 小時內無新版釋出） |
| v0.21.3（v2026.9.14） | 2026-09-14 | Patch rollup | remote gateway 登入失效修復、refresh token 併發合併、refresh 移出 event loop |
| v0.21.2（v2026.9.11） | 2026-09-11 | Patch rollup | state.db 可靠性戰役、multiplexed profile 隔離強化、密碼隱藏式憑證保險箱 |
| v0.21.1（v2026.9.7） | 2026-09-07 | Patch rollup | 效能、MCP 授權、cron、delegation 修復 |
| v0.21.0「The Pantheon Release」 | 2026-08-31 | 主版本 | Bot Mode、agent-to-agent 通訊、cron 記憶與連續性、MCP 集中管理面板 |

past-24h 內已合併進 main、但尚未打入新 tag 的重點變更：GPT-6 Sol／Terra／Luna 模型目錄（[#119410](https://github.com/NousResearch/hermes-agent/pull/119410)）；Setup agent 可在對話中直接搜尋並安裝外掛（[#119724](https://github.com/NousResearch/hermes-agent/pull/119724)）；一批外掛目錄／MCP 即時啟用相關合併（[#119644](https://github.com/NousResearch/hermes-agent/pull/119644)、[#119633](https://github.com/NousResearch/hermes-agent/pull/119633)、[#119605](https://github.com/NousResearch/hermes-agent/pull/119605)、[#119365](https://github.com/NousResearch/hermes-agent/pull/119365)、[#119349](https://github.com/NousResearch/hermes-agent/pull/119349)）；Gemini `MALFORMED_FUNCTION_CALL` 失敗處理修復（[#83882](https://github.com/NousResearch/hermes-agent/pull/83882)）。這些預期將出現在下一個 patch rollup 中。（[Releases 總覽](https://github.com/NousResearch/hermes-agent/releases)）

---

## 4. 社群討論與已知問題

**本日最值得留意的是 GPT-6 Sol／Terra／Luna 正式合併進模型目錄（連帶引發並快速修復數個目錄相容性 bug），以及 Claude Opus 5.5 模型註冊 PR 尚待合併；此外新一批 12 則 secret-scope／profile 隔離安全性 issue 集中於 9/22 開立，值得持續觀察後續處理進度。**

| Issue／PR | 說明 | 優先度／狀態 |
|------|------|----------|
| [#119444](https://github.com/NousResearch/hermes-agent/pull/119444) | 新增 Claude Opus 5.5 模型註冊（1M context／128K output，Anthropic＋Bedrock） | 未標記／Open |
| [#119419](https://github.com/NousResearch/hermes-agent/pull/119419) | 修復 Opus 5.5 強制 thinking 導致「關閉 thinking」觸發 HTTP 400 | 未標記／Open |
| [#119505](https://github.com/NousResearch/hermes-agent/pull/119505) | 移除 picker 中尚未發布的 gpt-6-terra 選項 | 未標記／Open |
| [#119458](https://github.com/NousResearch/hermes-agent/issues/119458) | `get_secret` 於 profile-scoped 讀取失敗時 fallback 到 `os.environ` | sweeper:risk-security-boundary／Open |
| [#119446](https://github.com/NousResearch/hermes-agent/issues/119446) | Allowlist 誤判共用 `@` 前段字串的任意 user_id 即通過 | sweeper:risk-security-boundary／Open |
| [#119485](https://github.com/NousResearch/hermes-agent/issues/119485) | Profile-scope binder 於 scope setup 失敗時外洩 `HERMES_HOME` override | sweeper:risk-security-boundary／Open |
| [#119170](https://github.com/NousResearch/hermes-agent/issues/119170) | Docker 共享模式下所有 session 共用同一 `$HOME` 掛載於 `/workspace` | sweeper:risk-security-boundary／Open |
| （另有 8 則同批 `sweeper:risk-security-boundary` issue：[#119521](https://github.com/NousResearch/hermes-agent/issues/119521)、[#119350](https://github.com/NousResearch/hermes-agent/issues/119350)、[#119279](https://github.com/NousResearch/hermes-agent/issues/119279)、[#119242](https://github.com/NousResearch/hermes-agent/issues/119242)、[#119239](https://github.com/NousResearch/hermes-agent/issues/119239)、[#119217](https://github.com/NousResearch/hermes-agent/issues/119217)、[#119163](https://github.com/NousResearch/hermes-agent/issues/119163)、[#119162](https://github.com/NousResearch/hermes-agent/issues/119162)） | 均為 secret scope／profile 隔離相關，均為 open | sweeper:risk-security-boundary／Open |
| [#119758](https://github.com/NousResearch/hermes-agent/issues/119758) | `/v1/responses` stream 缺少 content_part 事件，OpenAI SDK `responses.stream()` 丟 IndexError | 未標記／Open |
| [#119753](https://github.com/NousResearch/hermes-agent/issues/119753) | Desktop「取消安裝」遺留 git／uv 程序並卡死 | 未標記／Open |
| [#118714](https://github.com/NousResearch/hermes-agent/issues/118714) | OAuth 過期欄位損毀導致可能重複使用一次性 refresh token（過去 24 小時查無新進展） | 未標記／Open |
| [#118199](https://github.com/NousResearch/hermes-agent/pull/118199) | Hermes 誤耗 Claude Code 專屬 Keychain refresh token（過去 24 小時查無新進展） | P2／sweeper:risk-security-boundary／Open |
| [#109115](https://github.com/NousResearch/hermes-agent/issues/109115) | Vertex-backed OpenAI 相容 endpoint 因 `anyOf` schema 拒絕 `notify` 參數（過去 24 小時查無新進展） | P2／Open |
| [#117793](https://github.com/NousResearch/hermes-agent/issues/117793) | llama.cpp context 溢位錯誤格式未被辨識（過去 24 小時查無新進展） | P2／Open |

過去 24 小時內未查到具引用來源的 X（Twitter）官方公告或 Reddit／Discord 具體討論串內容，故本節僅列出可查證的 GitHub 一手資料。上述新增的 12 則安全性 issue 因無法精確驗證到小時等級的開立時間，不排除其中一兩則實際上早於昨日回報截止時間，特此註明。

---

*報告生成時間：2026-09-23 UTC*
