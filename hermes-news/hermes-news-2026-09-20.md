# Hermes-Agent 每日情報 — 2026-09-20

> 資料來源涵蓋截至 2026-09-20 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **穩定版仍為 v0.21.3（v2026.9.14），過去 24 小時內無新 release**；GitHub search API 回報同一時段新建立的 issue／PR 總數已達到單次查詢上限（total_count 顯示 999，實際數字可能更高），顯示專案活動量持續處於極高量級。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

2. **Gemini「AQ.」金鑰誤判問題大幅升級**：繼昨日回報的 [#115306](https://github.com/NousResearch/hermes-agent/issues/115306)／[#115356](https://github.com/NousResearch/hermes-agent/issues/115356) 後，今日新增第三則重複回報 [#116053](https://github.com/NousResearch/hermes-agent/issues/116053)，並出現範圍更完整的候選修復 PR [#116068](https://github.com/NousResearch/hermes-agent/pull/116068)——不再只是修正前綴判斷邏輯，而是直接把「AQ.」開頭金鑰的預設路由改成 Google AI Studio，使用者須明確設定 `gemini_base_url` 才會走 Vertex AI Express；PR 已附 232 筆 Gemini 測試與 ruff 檢查通過紀錄，但尚無 reviewer 指派、尚未合併。同時還送出 [#116009](https://github.com/NousResearch/hermes-agent/pull/116009)（在日誌／錯誤訊息中遮蔽 AQ. 金鑰避免外洩，標記重複）與 [#116202](https://github.com/NousResearch/hermes-agent/pull/116202)（文件更新，說明 AQ. 前綴不再代表特定 surface）。

3. **昨日 [#113023](https://github.com/NousResearch/hermes-agent/issues/113023)（自動借用 Codex／Claude Code 登入憑證）已合併修復**：PR [#115586](https://github.com/NousResearch/hermes-agent/pull/115586) 已於 9/19 合併，新增 `auth.adopt_external_logins` 開關；同日另一則後續 PR [#115690](https://github.com/NousResearch/hermes-agent/pull/115690) 也已合併，修正 macOS 上 Hermes 執行 OAuth refresh 後，會在 Claude Code 的 Keychain 裡留下已作廢 refresh token 的問題。

4. **昨日三則 P2／Open 缺陷均已出現候選修復 PR，但尚未合併**：ACP session 卡死（[#115588](https://github.com/NousResearch/hermes-agent/issues/115588)）對應 PR [#115593](https://github.com/NousResearch/hermes-agent/pull/115593)（把 `is_running` 釋放搬進 finally block）；macOS 冷啟動卡在「Connecting」（[#115583](https://github.com/NousResearch/hermes-agent/issues/115583)）對應 PR [#115589](https://github.com/NousResearch/hermes-agent/pull/115589)（fd 掃描加上 1 秒逾時、fail-open，掃描時間從 20 秒以上降到 0.01 秒）；Windows session lock 效能（[#115578](https://github.com/NousResearch/hermes-agent/issues/115578)）對應 PR [#115591](https://github.com/NousResearch/hermes-agent/pull/115591)（Windows 上跳過無意義的 POSIX zombie 偵測）。三者皆尚無 reviewer 指派。

5. **昨日 P1 issue [#115571](https://github.com/NousResearch/hermes-agent/issues/115571)（transcript 資料庫結構性損毀）已被關閉為「not planned」**（回報者手上僅有私有資料庫、未能透過公開管道提供重現用資料），但同一損毀機制的一般性修復仍在進行：PR [#115655](https://github.com/NousResearch/hermes-agent/pull/115655) 提出離線 salvage 策略（重建 B-tree 索引、atomic file swap），6 筆修復測試已通過，尚未合併。

6. **新增 P1 issue [#115692](https://github.com/NousResearch/hermes-agent/issues/115692)**：既有的「dead-owner cron 回收」機制只在持有者行程確實死亡時才會釋放 claim，若 worker 行程存活但已死鎖（deadlocked），claim 會永遠卡住、cron job 無法復原；候選修復 PR [#116549](https://github.com/NousResearch/hermes-agent/pull/116549) 加入 wall-clock 逾時（`claimed_at` 超過 7200 秒視為 unknown），1,503 筆既有 cron 測試通過，尚未合併。

7. **Windows 平台可靠性修復一次合併五筆（均為 P1，9/19 全數合併）**：終端機在 grandchild 行程持有 stdout pipe 時會卡住不回傳（[#115671](https://github.com/NousResearch/hermes-agent/pull/115671)）；Desktop 重新建置因缺少 `get-windows` 而中止（[#115673](https://github.com/NousResearch/hermes-agent/pull/115673)）；更新失敗時 ZIP fallback 未保留 Desktop／web build 產物（[#115678](https://github.com/NousResearch/hermes-agent/pull/115678)）；安裝程式對搬移過的 uv shim 拒絕安裝、現已可自我修復（[#115679](https://github.com/NousResearch/hermes-agent/pull/115679)）；更新時的 shim 交接行程現會等待 `hermes.exe` 真正結束才繼續（[#115688](https://github.com/NousResearch/hermes-agent/pull/115688)）。

8. **串流層 P1 缺陷已合併修復（[#115712](https://github.com/NousResearch/hermes-agent/pull/115712)）**：走完整輪工具呼叫的 turn，回應結尾偶爾會被截斷成單一亂碼片段（範例為西里爾字母「пар」），影響 OpenRouter／OpenAI 路由，9/19 已合併。

9. **壓縮層 P1 缺陷已合併修復（[#115906](https://github.com/NousResearch/hermes-agent/pull/115906)）**：當負責摘要的 LLM provider 過載時，原本的 fallback 機制會直接把整份 transcript 清空覆寫，屬於資料遺失等級的缺陷，9/19 已合併修復。

10. **Codex／OpenAI 認證與 Responses API 穩定性批次修復**：9/19 一口氣合併十餘筆與 Codex CLI 相關的修復，詳見第 2 節。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）
- 見第 1 節第 3 點：PR #115586、#115690 已合併，分別解決「借用 Claude Code 登入憑證互搶刷新」與「macOS Keychain 殘留作廢 token」問題。
- PR [#115795](https://github.com/NousResearch/hermes-agent/pull/115795)（已合併）：provider 端純程式碼錯誤（code-only errors）現在會被分類成結構化的 failover 原因，而非全部歸類為 unknown，同時涵蓋 Anthropic／OpenAI／Gemini 三家 provider。
- PR [#115812](https://github.com/NousResearch/hermes-agent/pull/115812)（已合併）：WAF 攔截造成的 403 現在會正確回報為「firewall block」而非誤判為其他錯誤；`anthropic_messages` 路由也開始支援送出自訂 `extra_headers`。
- PR [#115860](https://github.com/NousResearch/hermes-agent/pull/115860)（已合併）：rate-limit 重試狀態訊息會標明對應 provider 的重置時間窗。
- 開放中但尚未合併：[#115708](https://github.com/NousResearch/hermes-agent/pull/115708)（跨 transport 保留 seeded system prompt，同時影響 Anthropic／OpenAI）、[#115842](https://github.com/NousResearch/hermes-agent/pull/115842)（從 Anthropic-backed 的 OpenAI 相容 bridge 讀取 `cache_creation_tokens`）、[#116526](https://github.com/NousResearch/hermes-agent/pull/116526)（具現化 Anthropic 探測用憑證）、[#116542](https://github.com/NousResearch/hermes-agent/pull/116542)（記錄 token resolver 選用了哪個憑證來源，方便除錯）。

### GPT（OpenAI／Codex）
- 見第 1 節第 10 點，9/19 合併的 Codex 相關修復完整清單：第二組 Codex 帳號互相覆寫（[#115626](https://github.com/NousResearch/hermes-agent/pull/115626)）、`accountId` 解析失敗誤判為驗證失敗（[#115725](https://github.com/NousResearch/hermes-agent/pull/115725)）、Azure endpoint 自動偵測時的緩衝區溢位（[#115727](https://github.com/NousResearch/hermes-agent/pull/115727)）、reasoning 被拒絕後自我修復重試而非直接中止（[#115729](https://github.com/NousResearch/hermes-agent/pull/115729)）、`status`／`doctor` 從未採用或持久化 Codex 登入（[#115730](https://github.com/NousResearch/hermes-agent/pull/115730)）、GPT-5.6 Responses 續接在重播訊息時回傳 400（[#115733](https://github.com/NousResearch/hermes-agent/pull/115733)）、enum 風格與結構化 `reasoning_effort` 400 錯誤可復原（[#115736](https://github.com/NousResearch/hermes-agent/pull/115736)）、明確宣告的 tool strict 設定在所有 Responses 路由上都能保留（[#115737](https://github.com/NousResearch/hermes-agent/pull/115737)）、外掛 401 雜訊不再蓋掉真正的 turn 錯誤（[#115739](https://github.com/NousResearch/hermes-agent/pull/115739)）、residency-enforced 的 ChatGPT workspace 不再誤報 401（[#115741](https://github.com/NousResearch/hermes-agent/pull/115741)）。
- 亦見第 1 節第 8 點：串流層 collapsed fragment 缺陷（#115712）同時影響 OpenRouter／OpenAI，已合併。

### Gemini（Google）
- 見第 1 節第 2 點，「AQ.」金鑰誤判問題今日大幅升級，新增重複回報 #116053 與範圍更完整的候選修復 PR #116068。
- PR [#115859](https://github.com/NousResearch/hermes-agent/pull/115859)（開放中）：當 Vertex Express surface 被停用時，改為自動在 AI Studio 上重試。
- PR [#116252](https://github.com/NousResearch/hermes-agent/pull/116252)（開放中）：重試時可從失效／過期的 thought signature 中復原。
- PR [#116509](https://github.com/NousResearch/hermes-agent/pull/116509)（開放中）：補完原生 Gemini `ListModels` 探索機制。

### 本地模型／Auxiliary Model
- PR [#115693](https://github.com/NousResearch/hermes-agent/pull/115693)（已合併）：Ollama 模型 metadata 回報的 context window 小於 64K 時，建構邏輯的修復。
- PR [#115885](https://github.com/NousResearch/hermes-agent/pull/115885)（已合併）：自訂 provider 的 `key_env` 現在會正確透過 profile 範圍解析，影響 Ollama 等本地 provider。
- PR [#115886](https://github.com/NousResearch/hermes-agent/pull/115886)（已合併）：本地 Responses endpoint 的串流現在有寬限期，減少誤判逾時。
- PR [#115897](https://github.com/NousResearch/hermes-agent/pull/115897)（已合併）：Azure／SGLang 輸出長度上限拒絕時會自動重試。
- Issue [#115814](https://github.com/NousResearch/hermes-agent/issues/115814)（Windows 上 `OLLAMA_PROVIDER` batch adapter 被忽略）已被標記為 `invalid`，暫無後續修復動作。
- 9/18 提出的 MoA fan-out opt-out feature request（[#114576](https://github.com/NousResearch/hermes-agent/issues/114576)）過去 24 小時查無可確認的新進展。

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 類型 | 重點 |
|------|----------|------|------|
| **v0.21.3（v2026.9.14）** | 2026-09-14 | Patch rollup | 338 個合併 PR／1,036 次 commits；**目前最新穩定版**（過去 24 小時內無新 tag） |
| v0.21.2（v2026.9.11） | 2026-09-11 | Patch rollup | state.db 可靠性戰役、multiplexed profile 隔離強化、密碼隱藏式憑證保險箱 |
| v0.21.1（v2026.9.7） | 2026-09-07 | Patch rollup | 5,139 commits／632 merged PR，效能、MCP 授權、cron、delegation 修復 |
| v0.21.0「The Pantheon Release」 | 2026-08-31 | 主版本 | Bot Mode、agent-to-agent 通訊、cron 記憶與連續性、即時子代理操控、MCP 集中管理面板、agent 操控桌面瀏覽器 |

過去 24 小時內合併進 main 的變更中，安全相關項目值得留意：外掛目錄安裝的外掛現在會在具能力閘門的 SDK 橋接後方、以沙箱化 realm 執行（[#115619](https://github.com/NousResearch/hermes-agent/pull/115619)）；npm 相依套件更新以清除多項 audit 發現，涵蓋 browser／web dashboard（[#116021](https://github.com/NousResearch/hermes-agent/pull/116021)）、WhatsApp bridge（[#116022](https://github.com/NousResearch/hermes-agent/pull/116022)）與 root lockfile 中的 browserslist 相依鏈（[#116096](https://github.com/NousResearch/hermes-agent/pull/116096)）。其餘仍是零散修復，尚未打入新 tag；第 1、2 節提到的多項候選修復（PR #116068、#115655、#116549、#115593、#115589、#115591 等）仍在 open review 階段，須等下一個 patch rollup 才會出現在 tagged 版本中。（[Releases 總覽](https://github.com/NousResearch/hermes-agent/releases)）

---

## 4. 社群討論與已知問題

**本日最值得留意的是 Gemini「AQ.」金鑰誤判問題的升級與範圍更完整的候選修復（#116053／#116068），以及新增的 P1 cron 死鎖回收缺陷（#115692）**；另外昨日回報的三則 P2 缺陷（ACP 卡死、macOS 冷啟動、Windows lock）均已有候選修復 PR 但尚未合併，值得持續追蹤。

| Issue／PR | 說明 | 優先度／狀態 |
|------|------|------|
| [#116053](https://github.com/NousResearch/hermes-agent/issues/116053) | Google AI Studio 新發的 AQ.* 金鑰被誤路由到 Vertex AI；候選 PR #116068 提出完整重新設計（預設 AI Studio、須明確指定 Vertex），尚待合併 | P2／Open（標記重複） |
| [#115692](https://github.com/NousResearch/hermes-agent/issues/115692) | dead-owner cron 回收機制無法處理存活但死鎖的 worker；候選 PR #116549（wall-clock 逾時）尚待合併 | P1／Open |
| [#115588](https://github.com/NousResearch/hermes-agent/issues/115588) | ACP `_finish_turn` 的 `is_running` 釋放不在 try/finally 內，尾端例外會讓 session 永久卡死；候選 PR #115593 尚待合併 | P2／Open |
| [#115583](https://github.com/NousResearch/hermes-agent/issues/115583) | macOS Desktop 冷啟動因無上限的 fd 掃描卡在「Connecting」；候選 PR #115589（加 1 秒逾時）尚待合併 | P2／Open |
| [#115578](https://github.com/NousResearch/hermes-agent/issues/115578) | Windows session registry lock 不公平且夾帶無意義的 zombie 偵測；候選 PR #115591 尚待合併 | P2／Open |
| [#115646](https://github.com/NousResearch/hermes-agent/issues/115646) | Docker egress collision guard 只保護 `*_API_KEY`／`*_TOKEN` 命名的環境變數；候選 PR #115647 尚待合併 | P2／Open |
| [#115887](https://github.com/NousResearch/hermes-agent/issues/115887) | Docker egress collision 掃描漏掉合併寫法的 `-eNAME=VALUE` 環境變數；候選 PR #115888 尚待合併 | P2／Open |
| [#116012](https://github.com/NousResearch/hermes-agent/issues/116012) | approval gate 未攔截 bun／deno 的內嵌腳本執行；候選 PR #116017 尚待合併 | P2／Open |
| [#115977](https://github.com/NousResearch/hermes-agent/pull/115977) | cron 排程的 bot-chat 提醒可能因遞送逾時而遺失；修復 PR 開放中尚待合併 | P1／Open |
| [#115657](https://github.com/NousResearch/hermes-agent/pull/115657) | MCP stdio 子行程當機時會使 lifecycle task 陷入忙迴圈；修復 PR 開放中尚待合併 | P1／Open |
| [#115571](https://github.com/NousResearch/hermes-agent/issues/115571) | worker transcript 寫入時資料庫結構性損毀；issue 已關閉為 not planned，但一般性離線修復 PR #115655 仍在進行 | P1／Closed（not planned） |
| [#109115](https://github.com/NousResearch/hermes-agent/issues/109115) | Vertex-backed OpenAI 相容 endpoint 因 `anyOf` schema 拒絕 `notify` 參數，所有 turn 收到 400（過去 24 小時查無可確認新進展） | P2／Open |
| [#108656](https://github.com/NousResearch/hermes-agent/issues/108656) | Gemini quota 誤判、`RetryInfo.retryDelay` 被忽略（過去 24 小時查無可確認新進展） | Open |

過去 24 小時內未查到具引用來源的 X（Twitter）官方公告或 Reddit／Discord 具體討論串內容，故本節僅列出可查證的 GitHub 一手資料。

---

*報告生成時間：2026-09-20 UTC*
