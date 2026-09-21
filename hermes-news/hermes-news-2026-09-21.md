# Hermes-Agent 每日情報 — 2026-09-21

> 資料來源涵蓋截至 2026-09-21 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **穩定版仍為 v0.21.3（v2026.9.14），過去 24 小時內無新 release**；GitHub 上 issue／PR 編號已從昨日的 #116xxx 區間跳升到 #117xxx 區間，單日新增量再度達到極高量級。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

2. **拖了數日的 Gemini「AQ.」金鑰誤路由問題，源頭 issue 已合併修復**：PR [#116820](https://github.com/NousResearch/hermes-agent/pull/116820) 於 9/20 由 teknium1 合併，修復最早的 [#115306](https://github.com/NousResearch/hermes-agent/issues/115306)，並整合了先前擱置的 [#115323](https://github.com/NousResearch/hermes-agent/pull/115323)（salvage）與文件 PR [#116202](https://github.com/NousResearch/hermes-agent/pull/116202)。做法與先前報導的方向一致：`normalize_gemini_base_url` 不再靠金鑰前綴判斷路由，改以使用者設定的 base URL 決定要打 AI Studio 還是 Vertex Express，且對兩種誤設都給出對應的錯誤提示。**但值得注意的是**，另一則重複回報 [#116053](https://github.com/NousResearch/hermes-agent/issues/116053) 已關閉為 duplicate，指向的候選修復 PR [#116068](https://github.com/NousResearch/hermes-agent/pull/116068) 昨日雖已完成審查意見回覆，但目前仍是 open 狀態，尚不清楚是否會在 #116820 合併後被視為多餘而關閉；另一則遮蔽 AQ. 金鑰避免洩漏日誌的 PR [#116009](https://github.com/NousResearch/hermes-agent/pull/116009) 也仍 open（已開啟 auto-merge）。

3. **昨日新增的 P1 cron 死鎖回收缺陷（[#115692](https://github.com/NousResearch/hermes-agent/issues/115692)）已合併修復，但走了一輪重做**：原候選 PR #116549（固定 7200 秒逾時）被 reviewer @ehz0ah 抓到會誤殺合法的長執行任務（示範一則跑滿 3 小時的正常任務被誤判），因此改由 [#117185](https://github.com/NousResearch/hermes-agent/pull/117185) 重新設計並於 9/20 合併：逾時門檻改用動態算式 `max(3 × HERMES_CRON_TIMEOUT, 腳本自身逾時, 7200 秒)`，並保留 `HERMES_CRON_TIMEOUT=0`（無限模式）不回收存活 owner 的例外，14 筆新增不變式測試通過。

4. **昨日兩則 P2 Open 缺陷分別以「cherry-pick 到新 PR」的方式合併**：ACP session 卡死（[#115588](https://github.com/NousResearch/hermes-agent/issues/115588)）的修復經 [#116784](https://github.com/NousResearch/hermes-agent/pull/116784) 合併；Windows session lock 效能問題（[#115578](https://github.com/NousResearch/hermes-agent/issues/115578)）的修復經 [#116998](https://github.com/NousResearch/hermes-agent/pull/116998) 合併，原作者掛名保留。

5. **macOS 冷啟動卡在「Connecting」（[#115583](https://github.com/NousResearch/hermes-agent/issues/115583)）與 transcript 資料庫損毀離線修復（[#115655](https://github.com/NousResearch/hermes-agent/pull/115655)）仍未合併**：前者候選 PR #115589 尚無任何 reviewer 指派；後者已有第三方貢獻者驗證 6/6 修復測試在 Linux 上通過，但仍未進入合併流程。

6. **新增疑似資料遺失等級的缺陷 [#117802](https://github.com/NousResearch/hermes-agent/issues/117802)**：turn-recovery 機制用簡陋的字串比對（`"'ascii'" in error_text`）判斷是否為 ASCII-only 系統，一旦誤判就會把整段對話歷史中的非 ASCII 字元（包含中日韓文字）永久刪除並直接寫回 `state.db`；觸發條件其實只需要自訂 provider 的 API 金鑰或標頭中含一個非 ASCII 字元即可誤觸發，影響 CLI 互動模式，回報者已表示願意送 PR，目前尚無標籤與後續動作。

7. **新增 [#117806](https://github.com/NousResearch/hermes-agent/issues/117806)：session 明確指定（pin）model 時會完全略過 `fallback_providers`**，429 用量耗盡後只在同一個 provider 上重試 3 次就直接失敗，不會像未指定 model 的預設 session 一樣自動切換到設定好的備援 provider；回報環境為 macOS Desktop、`codex-dev` profile，nous／openrouter 備援皆健康卻未被使用，目前尚無標籤與修復 PR。

8. **新增 P2 issue [#117796](https://github.com/NousResearch/hermes-agent/issues/117796)：Windows CLI 執行檔遭 Defender 誤判為 `Trojan:Win32/Pomal!rfn` 並隔離**，即使重新安裝、更新病毒碼後仍會發生；回報者特別註明這不是 Electron Desktop 執行檔，也未斷言是否為誤判，目前尚無修復方向。

9. **Anthropic／Claude Code 整合面兩則 P2 修復已合併**：自訂 provider 使用 `key_cmd`（可執行憑證來源）打原生 `api.anthropic.com` 時會遺失 OAuth 身分、被誤判為需付費額度而收到 429（[#117044](https://github.com/NousResearch/hermes-agent/pull/117044)，743 筆測試通過）；桌面版在 macOS 上以 GUI 啟動時，因繼承受限 PATH 抓不到 Claude Code CLI，退回硬編碼的舊版號導致 API 拒絕（[#117188](https://github.com/NousResearch/hermes-agent/pull/117188)）。詳見第 2 節。

10. **本地模型／local runtime 相關新增 P2 issue [#117793](https://github.com/NousResearch/hermes-agent/issues/117793)**：`agent/model_metadata.py` 認不出 llama.cpp 的 context 溢位錯誤格式（`"exceeds the available context size (32768 tokens)"`），導致溢位被誤判為「未回報上限」，壓縮邏輯繼續用錯誤假設運作；回報者已本地測試好修復 regex 並表示願意送 PR。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）
- PR [#117044](https://github.com/NousResearch/hermes-agent/pull/117044)（已合併）：新增 `anthropic_route_is_oauth()` 統一判斷邏輯，修復自訂 provider 用 `key_cmd` 打原生 Anthropic API 時遺失 OAuth 身分、被誤判為 429 rate-limit 的問題；743 筆測試通過。
- PR [#117188](https://github.com/NousResearch/hermes-agent/pull/117188)（已合併）：修復桌面版 GUI 啟動時 Claude Code 版本偵測失效（受限 PATH 抓不到 CLI）、退回舊版號被 API 拒絕的問題，改為兩階段搜尋（PATH 優先、再查常見安裝路徑）。
- PR [#117108](https://github.com/NousResearch/hermes-agent/pull/117108)（已合併）：輔助模型呼叫與 iteration-limit 摘要呼叫先前繞過了 SDK request transform，影響上下文壓縮效能，現已修復。

### GPT（OpenAI／Codex）
- PR [#117635](https://github.com/NousResearch/hermes-agent/pull/117635)（已合併，9/21）：非可編輯訊息平台（如企業微信 adapter）的 Codex 長回覆現在只會送出一次，不會重複投遞。
- 昨日提及的 Codex app-server 系列穩定性修復（帳號互搶、Azure 偵測緩衝區溢位、reasoning 拒絕重試、GPT-5.6 Responses 續接 400 等）持續在 main 上累積，過去 24 小時內無新增重大 Codex 缺陷回報。

### Gemini（Google）
- 見第 1 節第 2 點：源頭 issue #115306 已透過 PR #116820 合併修復「AQ. 金鑰誤路由到 Vertex Express」問題；但重複回報 #116053 對應的候選 PR #116068 仍為 open，是否會被 #116820 取代待觀察。
- PR [#116009](https://github.com/NousResearch/hermes-agent/pull/116009)（開放中，已開啟 auto-merge）：日誌／錯誤訊息中裸露的 AQ. 前綴金鑰現在會被遮蔽。
- PR [#115859](https://github.com/NousResearch/hermes-agent/pull/115859)（開放中）：Vertex Express surface 被停用時自動改在 AI Studio 重試；reviewer 已建議「CI 過了就合併」。
- PR [#116252](https://github.com/NousResearch/hermes-agent/pull/116252)（開放中）：從失效／過期的 thought signature 中復原重試。
- PR [#116509](https://github.com/NousResearch/hermes-agent/pull/116509)（開放中）：補完原生 Gemini `ListModels` 探索機制。

### 本地模型／Auxiliary Model
- 新增 issue [#117793](https://github.com/NousResearch/hermes-agent/issues/117793)：llama.cpp context 溢位錯誤格式未被辨識，見第 1 節第 10 點。
- PR [#117568](https://github.com/NousResearch/hermes-agent/pull/117568)（已合併）：gpt-oss 的 MXFP4 量化 GGUF 現在會套用對應 preset，不再回報「unknown ggml tensor type 39」。
- PR [#117600](https://github.com/NousResearch/hermes-agent/pull/117600)（已合併）：兩個本地 backend 同時啟動時，改為共用同一個 managed router（原本會在 18434 埠各自起一個，互相衝突）。
- PR [#117623](https://github.com/NousResearch/hermes-agent/pull/117623)（已合併）：Windows 上 bash 呼叫改用 Git Bash 而非 WSL stub，且 shell 執行失敗時不再靜默吞掉錯誤。

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 類型 | 重點 |
|------|----------|------|------|
| **v0.21.3（v2026.9.14）** | 2026-09-14 | Patch rollup | 338 個合併 PR／1,036 次 commits；**目前最新穩定版**（過去 24 小時內無新 tag） |
| v0.21.2（v2026.9.11） | 2026-09-11 | Patch rollup | state.db 可靠性戰役、multiplexed profile 隔離強化、密碼隱藏式憑證保險箱 |
| v0.21.1（v2026.9.7） | 2026-09-07 | Patch rollup | 5,139 commits／632 merged PR，效能、MCP 授權、cron、delegation 修復 |
| v0.21.0「The Pantheon Release」 | 2026-08-31 | 主版本 | Bot Mode、agent-to-agent 通訊、cron 記憶與連續性、即時子代理操控、MCP 集中管理面板、agent 操控桌面瀏覽器 |

過去 24 小時內合併進 main 的變更中，最值得留意的仍是第 1 節提到的幾筆：Gemini AQ. 金鑰路由（#116820）、cron 死鎖回收重做（#117185）、ACP／Windows lock 的 cherry-pick 合併（#116784、#116998），以及 Anthropic OAuth／版本偵測兩則修復（#117044、#117188）。這些都尚未打入新 tag，須等下一個 patch rollup 才會出現在正式版本號中。（[Releases 總覽](https://github.com/NousResearch/hermes-agent/releases)）

---

## 4. 社群討論與已知問題

**本日最值得留意的是拖了數日的 Gemini AQ. 金鑰路由問題終於在源頭合併修復（#116820），以及新增的疑似資料遺失缺陷 #117802（turn-recovery 誤刪非 ASCII 對話內容）**；另外 pin model 略過 fallback 的 #117806、Windows Defender 誤殺 CLI 的 #117796 也值得追蹤。

| Issue／PR | 說明 | 優先度／狀態 |
|------|------|------|
| [#117802](https://github.com/NousResearch/hermes-agent/issues/117802) | turn-recovery 誤判 ASCII-only 系統，永久刪除對話歷史中的非 ASCII 字元並寫回資料庫；回報者願送 PR | 未標記／Open |
| [#117806](https://github.com/NousResearch/hermes-agent/issues/117806) | 明確 pin model 的 session 在 429 時完全略過 `fallback_providers`，只在同一 provider 重試 3 次後失敗 | 未標記／Open |
| [#117796](https://github.com/NousResearch/hermes-agent/issues/117796) | Windows Defender 將 uv 產生的 `hermes.exe` 誤判為 Trojan 並隔離 | P2／Open |
| [#117793](https://github.com/NousResearch/hermes-agent/issues/117793) | llama.cpp context 溢位錯誤格式未被 `model_metadata.py` 辨識，壓縮邏輯用錯誤假設運作；回報者已備妥修復 regex | P2／Open |
| [#116068](https://github.com/NousResearch/hermes-agent/pull/116068) | Gemini AQ. 金鑰路由重複回報（#116053）對應的候選修復，審查意見已回覆但尚未合併；源頭問題已由 #116820 合併，本 PR 後續處理待觀察 | P2／Open |
| [#116009](https://github.com/NousResearch/hermes-agent/pull/116009) | 日誌／錯誤訊息中裸露的 AQ. 金鑰尚未遮蔽；已開啟 auto-merge 尚待合併 | Open |
| [#115583](https://github.com/NousResearch/hermes-agent/issues/115583) | macOS Desktop 冷啟動因無上限的 fd 掃描卡在「Connecting」；候選 PR #115589 尚無 reviewer 指派 | P2／Open |
| [#115571](https://github.com/NousResearch/hermes-agent/issues/115571) | worker transcript 寫入時資料庫結構性損毀；issue 已關閉為 not planned，一般性離線修復 PR #115655 已通過第三方驗證但尚未合併 | P1／Closed（not planned） |
| [#109115](https://github.com/NousResearch/hermes-agent/issues/109115) | Vertex-backed OpenAI 相容 endpoint 因 `anyOf` schema 拒絕 `notify` 參數，所有 turn 收到 400（過去 24 小時查無可確認新進展） | P2／Open |
| [#108656](https://github.com/NousResearch/hermes-agent/issues/108656) | Gemini quota 誤判、`RetryInfo.retryDelay` 被忽略（過去 24 小時查無可確認新進展） | Open |

過去 24 小時內未查到具引用來源的 X（Twitter）官方公告或 Reddit／Discord 具體討論串內容，故本節僅列出可查證的 GitHub 一手資料。

---

*報告生成時間：2026-09-21 UTC*
