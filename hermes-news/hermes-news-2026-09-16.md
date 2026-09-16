# Hermes-Agent 每日情報 — 2026-09-16

> 資料來源涵蓋截至 2026-09-16 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **穩定版仍為 v0.21.3（v2026.9.14），過去 24 小時內無新 release**。目前主線（main）已累積多筆尚未打包進 tag 的修復，詳見下方各點。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

2. **新增一起嚴重 Open issue：`_find_hermes_md()` 未防護 `PermissionError`，可同時打垮 terminal、TUI、dashboard 與 gateway 訊息通道**（[#112430](https://github.com/NousResearch/hermes-agent/issues/112430)）。當設定的 terminal 工作目錄解析到程序無權限存取的路徑時，該函式呼叫 `.is_file()` 未做例外防護（同檔案中其他函式已用 `_exists_or_denied()` 保護），導致整個系統所有使用者介面同時崩潰、無任何降級復原路徑。v0.21.3 與目前 main 均可重現。修法其實已存在於 [#8767](https://github.com/NousResearch/hermes-agent/pull/8767)，但該 PR 已開了超過五個月仍未合併；另一相關 PR [#104060](https://github.com/NousResearch/hermes-agent/pull/104060) 未涵蓋此缺口。建議密切關注是否盡快合併。

3. **纏訟已久的 Kanban worker 配額訊號問題，今日合併關鍵修復**：PR [#112500](https://github.com/NousResearch/hermes-agent/pull/112500) 讓非安靜模式（`hermes chat -q`，dispatcher 實際使用的路徑）在遇到 rate-limit／billing 牆時正確回傳 exit code 75，取代先前直接吃掉、回傳 0 的行為，解決「protocol-violation loop 持續燒錢」的問題，並一次關閉 [#41805](https://github.com/NousResearch/hermes-agent/issues/41805) 等舊 issue。**但**相關議題 [#81437](https://github.com/NousResearch/hermes-agent/issues/81437) 指出還有第二個交互缺陷未修：`blocker_auth` 重生守衛會在真正執行前就攔下任務，導致失敗從未被記錄、circuit breaker 無法啟動；該 issue 目前仍為 Open（P3），尚無對應 PR。

4. **新增一起monorepo 環境下的 `hermes update` 自我循環失敗 bug**（[#112378](https://github.com/NousResearch/hermes-agent/issues/112378)，影響 v0.21.3）：`_discard_lockfile_churn` 只以「同層目錄」比對 lockfile 與 manifest 是否骯髒，卻忽略根目錄 `package-lock.json` 實際上是所有 workspace manifest 的父層；當只有 workspace 的 `package.json` 被改動、根 manifest 乾淨時，根 lockfile 會被誤還原，造成 spec 與 lock 不同步，最終讓 npm 10 因 `edgesOut` 空值直接崩潰。更糟的是失敗的刷新不會記錄雜湊值，導致後續每次 `hermes update` 都重複同一個「偵測到變更→還原→失敗」迴圈。候選修復 PR [#112396](https://github.com/NousResearch/hermes-agent/pull/112396)、[#112380](https://github.com/NousResearch/hermes-agent/pull/112380) 已提出，尚待合併。

5. **新增一起 Mixture-of-Agents（MoA）fallback 命名 bug**（[#112525](https://github.com/NousResearch/hermes-agent/issues/112525)）：主要 provider 失敗、切換到 `provider=moa` 的聚合器 fallback 時，`try_activate_fallback` 雖然正確解析出真實的 `(provider, model)` 組合，卻把未解析的 preset 名稱（如 `"default"`）寫回 `agent.model`／`agent.provider`，導致後續請求把 `model=default` 直接送到 xAI 等聚合端點，收到 404。作者已表示修復 PR 即將提出，目前仍為 Open。

6. **昨日報告列為 P1 的 cron 靜默跳過問題（[#111414](https://github.com/NousResearch/hermes-agent/issues/111414)）今日關閉**：PR [#112224](https://github.com/NousResearch/hermes-agent/pull/112224) 為排程到期被跳過的情況補上 WARNING 等級 log（記錄工作名稱、被跳過的時刻與涵蓋它的執行紀錄），讓此類事件不再無聲無息。作者說明真正導致誤判跳過的底層機制已由先前合併到 main 的其他 commit 處理，此 PR 主要是補上稽核軌跡。

7. **Nous Portal 計費問題關閉**（[#110912](https://github.com/NousResearch/hermes-agent/issues/110912)）：原先懷疑是訂閱額度用盡才恢復全額計費，後續有第二位使用者回報在訂閱額度仍充足的情況下，`glm-5.3`／`glm-5.3-flash`／`kimi-k3` 等特定模型路由仍被以全額列表價計費（而 `openai/gpt-5.6-sol` 計價正常），顯示根因其實是「特定 model／provider 組合的折扣路由失效」而非額度耗盡；issue 已標記關閉，但公開頁面未附完整修復細節，建議實際計費使用者持續留意帳單。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）
- 過去 24 小時內查無 Anthropic／Claude 專屬的新 issue 或 PR 進展。既有的 [#107830](https://github.com/NousResearch/hermes-agent/issues/107830)（畸形 JSON 串流工具呼叫，已於 09-14 由 PR #109324 修復）與 [#109111](https://github.com/NousResearch/hermes-agent/issues/109111)（`auxiliary.vision` 對 Anthropic 被靜默忽略，PR #109149 待合併）狀態維持不變。

### GPT（OpenAI）
- **新增效能修復 PR [#112231](https://github.com/NousResearch/hermes-agent/pull/112231)（已合併）**：走預設 chat-completions 路徑（OpenRouter、Nous、xAI、DeepSeek、Kimi、llama.cpp、Ollama、LM Studio、LiteLLM 等，**不含 Anthropic 與 Gemini**，兩者走各自專屬 API）的請求，先前每次呼叫都會被 OpenAI SDK 重新以完整 `CompletionCreateParams` union 校驗一次已正規化過的訊息／工具內容，在持鎖（GIL）狀態下耗費可觀時間；修法把相關欄位移入 `extra_body` 略過校驗，同時保持 request body 逐位元組一致以維持 prompt cache 命中。官方實測：1600 則訊息（646 KB）情境下延遲從 241.0 ms 降到 5.8 ms。
- [#109198](https://github.com/NousResearch/hermes-agent/issues/109198)（Codex 手動憑證跨帳號誤用他人 token）、[#110157](https://github.com/NousResearch/hermes-agent/issues/110157)（未設定本地 Codex 憑證借用 global-root 卻誤報「未驗證」）過去 24 小時內查無新進展。

### Gemini（Google）
- **[#108656](https://github.com/NousResearch/hermes-agent/issues/108656) 過去 24 小時內查無新進展**：quota 誤判與 `RetryInfo.retryDelay` 被忽略的問題仍未修復，對應 PR [#108661](https://github.com/NousResearch/hermes-agent/pull/108661) 狀態未變。
- [#109115](https://github.com/NousResearch/hermes-agent/issues/109115)（Vertex-backed OpenAI 相容 endpoint 拒絕 `anyOf` schema）同樣查無新進展。

### 本地模型／Auxiliary Model
- 過去 24 小時未查到新的本地模型或 auxiliary model 選型討論；第 1 節提到的 MoA fallback bug（[#112525](https://github.com/NousResearch/hermes-agent/issues/112525)）雖以 xAI 為例，但屬於聚合器邏輯本身的缺陷，理論上也會影響其他透過 MoA 走 fallback 的 provider 組合，值得同時追蹤。

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 類型 | 重點 |
|------|----------|------|------|
| **v0.21.3（v2026.9.14）** | 2026-09-14 | Patch rollup | 338 個合併 PR／1,036 次 commits；修復 remote dashboard session 過早過期、state.db writer handle 洩漏、`DeletedWalGenerationError` 永久卡死回歸根因；**目前最新穩定版**（過去 24 小時內無新 tag） |
| v0.21.2（v2026.9.11） | 2026-09-11 | Patch rollup | state.db 可靠性戰役、multiplexed profile 隔離強化、密碼隱藏式憑證保險箱 |
| v0.21.1（v2026.9.7） | 2026-09-07 | Patch rollup | 5,139 commits／632 merged PR，效能、MCP 授權、cron、delegation 修復 |
| v0.21.0「The Pantheon Release」 | 2026-08-31 | 主版本 | Bot Mode、agent-to-agent 通訊、cron 記憶與連續性、即時子代理操控、MCP 集中管理面板、agent 操控桌面瀏覽器 |

本日合併但尚未打入任何 tag 的修復包括：Kanban exit-code 契約（PR #112500）、cron 跳過稽核 log（PR #112224）、`hermes update --no-gateway-restart`（PR #112510）、terminal 工具呼叫 pre-exec guard 卡死修復（PR #112185）、OpenAI chat-completions 效能優化（PR #112231）等，須等下一個 patch rollup 才會出現在 tagged 版本中。（[Releases 總覽](https://github.com/NousResearch/hermes-agent/releases)）

---

## 4. 社群討論與已知問題

**本日最值得留意的是尚未修復的嚴重 issue [#112430](https://github.com/NousResearch/hermes-agent/issues/112430)**（所有介面同時當機、修法已存在但擱置超過五個月未合併），以及 [#81437](https://github.com/NousResearch/hermes-agent/issues/81437) 揭露的 Kanban 第二層缺陷（`blocker_auth` 守衛讓失敗永遠不被記錄）：

| Issue／PR | 說明 | 優先度／狀態 |
|------|------|------|
| [#112430](https://github.com/NousResearch/hermes-agent/issues/112430) | `_find_hermes_md()` 未防護 `PermissionError`，同時打垮所有使用者介面；修法已存在於 PR #8767 但擱置逾五個月 | Critical／Open |
| [#81437](https://github.com/NousResearch/hermes-agent/issues/81437) | `blocker_auth` 重生守衛攔下任務使其永不執行，失敗無法被記錄、circuit breaker 失效 | P3／Open |
| [#112378](https://github.com/NousResearch/hermes-agent/issues/112378) | monorepo 下 `hermes update` 誤還原根 `package-lock.json`，形成自我循環失敗；候選 PR #112396、#112380 待合併 | Open |
| [#112525](https://github.com/NousResearch/hermes-agent/issues/112525) | MoA fallback 把未解析的 preset 名稱寫回 `agent.model`，造成聚合端點回傳 404；作者稱修復 PR 即將提出 | Open |
| [#110374](https://github.com/NousResearch/hermes-agent/issues/110374) | Slack 狀態指示器因 slack-sdk ≥ 3.44 改用僅接受封閉列舉的新 API 而靜默失效；暫時解法為釘選 slack-sdk < 3.44 | P3／Open |
| [#112522](https://github.com/NousResearch/hermes-agent/issues/112522) | `hermes update` 偶發 `ImportError`（`file_signature` 無法從 `utils` 匯入），疑為模組快取殘留參照所致；重跑一次即可成功 | Low／Open |

過去 24 小時內未查到具引用來源的 X（Twitter）官方公告或 Reddit／Discord 具體討論串內容，故本節僅列出可查證的 GitHub 一手資料。

---

*報告生成時間：2026-09-16 UTC*
