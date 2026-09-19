# Hermes-Agent 每日情報 — 2026-09-19

> 資料來源涵蓋截至 2026-09-19 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **穩定版仍為 v0.21.3（v2026.9.14），過去 24 小時內無新 release**，但單日新增 issue 編號已跳過千筆量級（昨日報告止於 #114594，今日新增討論多落在 #115568～#115590 區間），顯示專案整體活動量極高。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

2. **新增 P1 issue [#115571](https://github.com/NousResearch/hermes-agent/issues/115571)**：v0.21.0 起 worker transcript 寫入偶發失敗，錯誤訊息為「Session storage could not be written; the transcript would be lost on restart」；完整性檢查發現 `messages` 表格 row id 亂序，且 `idx_messages_session_active`、`idx_messages_session_id`、`idx_messages_session` 三個索引全部不一致，離線重建也過不了驗證。候選修復 PR [#115582](https://github.com/NousResearch/hermes-agent/pull/115582) 提出「唯讀完整性檢查 → 重建 messages 表與索引至新檔 → atomic swap → 重試一次寫入」的自動修復機制，但作者自承 snapshot 與 swap 之間若有其他並發寫入仍可能遺失，尚未合併。

3. **新增 issue [#115588](https://github.com/NousResearch/hermes-agent/issues/115588)**：ACP adapter 的 `_finish_turn()` 仍把 `is_running` flag 的釋放寫在 try/finally 之外，只要 `save_session()`、`session_update()` 或 `message_ids` 相關的收尾呼叫任何一個拋例外，flag 就會永久卡在 True，後續所有 prompt 都會被 `_claim_turn_or_queue` 無限期排隊、且過程通常沒有可見錯誤；先前的 [#114912](https://github.com/NousResearch/hermes-agent/issues/114912) 只修了其中一種當機情境，並未處理結構性問題，目前尚無對應 PR。

4. **新增 issue [#115583](https://github.com/NousResearch/hermes-agent/issues/115583)**：macOS 版 Desktop 冷啟動卡在「Connecting」，根因是 `refuse_deleted_wal_generation()` 呼叫的 `_iter_darwin_fd_targets()` 會對系統上所有行程做無時間上限的 file descriptor 掃描；只要有任一行程（例如 Docker Desktop 的虛擬化層）持有上萬個 fd，掃描就會卡住事件迴圈 20 秒以上，導致 WebSocket ready frame 逾時、應用程式直接退出。建議修法是加上約 1 秒的掛鐘時間預算、逾時就 fail-open 放行，目前尚無對應 PR。

5. **P2 issue [#115578](https://github.com/NousResearch/hermes-agent/issues/115578)**：Windows 上 `active_sessions.lock` 每次取用約耗時 80ms，肇因有二——`gateway/status.py` 的 `_pid_exists()` 在 Windows 上仍執行對 Windows 毫無意義的 POSIX zombie 偵測（`psutil` 呼叫約 6.9ms），明明已有微秒級的 `_pid_exists_win32_ctypes()` 卻未採用；再加上 `msvcrt.LK_LOCK` 重試機制不具公平性。回報者實測 13 個並行 session 時鎖佔用率達 213%，poller 執行緒約 9 秒逾時後回報 `OSError [Errno 36]`，整條 session 的背景遞送因而凍結。提出的修法預估可把持鎖時間從 81ms 降到 0.3ms、佔用率從 213% 降到 1%，目前尚無對應 PR。

6. **P2 issue [#115572](https://github.com/NousResearch/hermes-agent/issues/115572) 已有現成修復 PR [#115581](https://github.com/NousResearch/hermes-agent/pull/115581)**：`tui_gateway/session_compression.py` 的 `_apply_live_compression_config()` 呼叫了未定義的 `is_truthy_value`，導致所有壓縮引擎都無法即時套用 `config.yaml` 裡的壓縮設定變更（僅記警告、靜默失敗）；PR 修法是補上缺漏的 import，改動極小，已附兩筆迴歸測試，正等待合併。

7. **P3 issue [#115570](https://github.com/NousResearch/hermes-agent/issues/115570)**：Desktop backend 並行執行多個 Hermes turn 時，`agent/relay_runtime.py` 的 `pop_relay_scope()` 在收尾階段無條件 pop scope handle、未檢查是否位於堆疊頂端，觸發 nemo-relay 的「scope handle is not at the top of the stack」錯誤，40 分鐘內重現 3 次以上；影響僅止於 metrics／instrumentation 遺漏，不影響對話本身，目前尚無對應 PR。

8. **P3 issue [#115568](https://github.com/NousResearch/hermes-agent/issues/115568)**：Kanban dashboard 在觸控裝置上點擊卡片會直接觸發拖曳而非開啟任務，根因是 `attachTouchDrag()` 在 `pointerdown` 就立刻 `preventDefault()` 並開始拖曳、完全沒有移動門檻（約 3px 手指晃動就會誤觸），建議加入約 8px 的觸控容許誤差；目前僅在桌面瀏覽器模擬觸控環境驗證，尚未在實體 Android 裝置測試，也無對應 PR。

9. **PR [#115586](https://github.com/NousResearch/hermes-agent/pull/115586)（對應 issue #113023）**：新增設定 `auth.adopt_external_logins`（預設維持 `true` 以相容既有行為），關閉後 Hermes 不再自動讀取／借用 `~/.claude/.credentials.json`（Claude Code）與 `~/.codex/auth.json`（Codex CLI）的憑證；背景是這兩個工具都使用單次性、會自動輪替的 refresh token，誰先刷新誰就會把另一個工具登出，且會默默吃掉共用額度。關閉此選項後，實際的驗證錯誤會直接顯示而非被自動回退機制蓋掉；審查者 whyyagswhy 認可作法，但建議預設值維持寬鬆以相容既有使用者，PR 尚待合併。

10. **Gemini「AQ.」開頭金鑰誤判叢集**：[#115306](https://github.com/NousResearch/hermes-agent/issues/115306)（9/18 回報，已有開放中的修復 PR [#115323](https://github.com/NousResearch/hermes-agent/pull/115323)）與較晚回報、標記為重複的 [#115356](https://github.com/NousResearch/hermes-agent/issues/115356) 都指出：`agent/gemini_native_adapter.py` 目前把所有「AQ.」開頭的金鑰一律當成 Vertex AI Express key 處理，但 Google AI Studio 近期核發的一般 Gemini API 金鑰同樣以「AQ.」開頭，導致原本能在 `generativelanguage.googleapis.com` 正常運作的金鑰被誤導到 `aiplatform.googleapis.com`、回傳 401／403，`hermes doctor` 也會誤報金鑰無效；兩則回報都指出問題源頭是先前修復 [#114335](https://github.com/NousResearch/hermes-agent/issues/114335) 時引入的簡單前綴判斷邏輯。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）
- 見第 1 節第 9 點：PR #115586（對應 issue #113023）讓使用者可關閉 Hermes 自動借用 `~/.claude/.credentials.json` 的行為，避免與 Claude Code 本身的登入互相搶刷新、造成靜默登出或額度誤耗；預設值仍為相容既有行為的 `true`。
- 既有 [#109111](https://github.com/NousResearch/hermes-agent/issues/109111)（`auxiliary.vision` 對 Anthropic 被靜默忽略）過去 24 小時內查無新進展，對應 PR #109149 仍為 open、尚未合併。

### GPT（OpenAI）
- PR [#115590](https://github.com/NousResearch/hermes-agent/pull/115590)：修正 empty-response 復原機制——工具呼叫後的空白回應遺留的 assistant 佔位訊息／合成提示會殘留在對話歷史中並外洩進後續請求，且純 thinking 回應留下的 `_thinking_prefill` row 會被請求清理機制移除，導致重試請求與失敗請求完全相同、無法真正重試；修法改為只附加在「外送 copy」的最新使用者訊息／工具結果上，於回應收到與新 turn 開始時清除，測試涵蓋真實 OpenAI SDK／HTTP streaming／SQLite 路徑共 129 筆迴歸案例，PR 尚待合併。
- 既有 [#109198](https://github.com/NousResearch/hermes-agent/issues/109198)（Codex 手動憑證跨帳號誤用他人 token）、[#110157](https://github.com/NousResearch/hermes-agent/issues/110157)（未設定本地 Codex 憑證借用 global-root 卻誤報「未驗證」）過去 24 小時內查無新進展。

### Gemini（Google）
- 見第 1 節第 10 點，「AQ.」前綴金鑰誤判為 Vertex Express key 的問題持續延燒，已有兩則獨立回報（#115306／#115356），前者已附修復 PR #115323 但尚未合併；受影響使用者需暫時改用 Vertex 專用金鑰或回退到引入此迴歸前的版本繞過。
- 既有 [#108656](https://github.com/NousResearch/hermes-agent/issues/108656)（quota 誤判、`RetryInfo.retryDelay` 被忽略）與 [#109115](https://github.com/NousResearch/hermes-agent/issues/109115)（Vertex-backed OpenAI 相容 endpoint 因 `notify` 參數的 `anyOf` union 被拒）過去 24 小時內皆查無新進展。

### 本地模型／Auxiliary Model
- 過去 24 小時內查無本地模型／auxiliary LLM 專屬的新進展。9/18 提出的 MoA fan-out opt-out feature request（[#114576](https://github.com/NousResearch/hermes-agent/issues/114576)）仍停留在提案階段，無新討論。

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 類型 | 重點 |
|------|----------|------|------|
| **v0.21.3（v2026.9.14）** | 2026-09-14 | Patch rollup | 338 個合併 PR／1,036 次 commits；**目前最新穩定版**（過去 24 小時內無新 tag） |
| v0.21.2（v2026.9.11） | 2026-09-11 | Patch rollup | state.db 可靠性戰役、multiplexed profile 隔離強化、密碼隱藏式憑證保險箱 |
| v0.21.1（v2026.9.7） | 2026-09-07 | Patch rollup | 5,139 commits／632 merged PR，效能、MCP 授權、cron、delegation 修復 |
| v0.21.0「The Pantheon Release」 | 2026-08-31 | 主版本 | Bot Mode、agent-to-agent 通訊、cron 記憶與連續性、即時子代理操控、MCP 集中管理面板、agent 操控桌面瀏覽器 |

過去 24 小時內合併進 main 的變更包含外掛目錄新增 Hermes Talk（PR #108798）與 hermes-muse-code（PR #112065）、bot-screen 模組多項修復（PR #113438、#113439：即時 X server 顯示保留、CLI 狀態遮蔽 viewer id、profile 路徑展開、`SetDesktopSize` 轉發、已刪除連線的 bot 不再拋錯）、background-review 預算改用本地模型也能符合（PR #114870）、壓縮摘要保留單一 task-snapshot 區塊（PR #114757），以及外掛目錄新增 search1api、you、prism、aihubmix（PR #115529）等，但均屬既有 v0.21.3 之後的零散修復／功能，尚未打入新 tag。第 1 節提到的多項候選修復（PR #115582、#115581、#115586、#115590、#115323）仍在 open review 階段，須等下一個 patch rollup 才會出現在 tagged 版本中。（[Releases 總覽](https://github.com/NousResearch/hermes-agent/releases)）

---

## 4. 社群討論與已知問題

**本日最值得留意的是新增的 P1 transcript 資料庫結構性損毀 issue（#115571）與 ACP session 永久卡死缺陷（#115588）**，兩者目前都還沒有可合併的完整修復；另有一則已附現成修復 PR 的 P2 issue（compression config NameError）等待合併，以及持續延燒的 Gemini「AQ.」金鑰誤判問題。

| Issue／PR | 說明 | 優先度／狀態 |
|------|------|------|
| [#115571](https://github.com/NousResearch/hermes-agent/issues/115571) | worker transcript 寫入時 `messages` 表格與多個索引結構性損毀，離線重建也過不了驗證；候選 PR #115582 待合併 | P1／Open |
| [#115588](https://github.com/NousResearch/hermes-agent/issues/115588) | ACP `_finish_turn` 的 `is_running` 釋放不在 try/finally 內，尾端例外會讓 session 永久卡死；尚無 PR | Open |
| [#115583](https://github.com/NousResearch/hermes-agent/issues/115583) | macOS Desktop 冷啟動因無上限的 fd 掃描卡在「Connecting」20 秒以上；尚無 PR | Open |
| [#115578](https://github.com/NousResearch/hermes-agent/issues/115578) | Windows session registry lock 不公平且夾帶無意義的 zombie 偵測，導致並行 session 遞送凍結；尚無 PR | P2／Open |
| [#115572](https://github.com/NousResearch/hermes-agent/issues/115572) | `_apply_live_compression_config` 缺少 import 導致 NameError，壓縮設定變更全部套用失敗；候選 PR #115581 待合併 | P2／Open |
| [#115570](https://github.com/NousResearch/hermes-agent/issues/115570) | 並行 turn 下 relay scope handle pop 未檢查堆疊頂端，觸發 metrics 收尾錯誤；尚無 PR | P3／Open |
| [#115568](https://github.com/NousResearch/hermes-agent/issues/115568) | Kanban 觸控裝置點擊卡片誤觸拖曳，缺少移動門檻；尚無 PR | P3／Open |
| [#113023](https://github.com/NousResearch/hermes-agent/issues/113023) | Hermes 自動借用 Codex CLI／Claude Code 登入憑證造成互相搶刷新登出；候選 PR #115586 待合併 | Open |
| [#115306](https://github.com/NousResearch/hermes-agent/issues/115306) | 「AQ.」開頭的 Google AI Studio Gemini 金鑰被誤判為 Vertex Express key 而遭拒；候選 PR #115323 待合併 | P2／Open |
| [#115356](https://github.com/NousResearch/hermes-agent/issues/115356) | 同一問題的重複回報，補充三種可能修法方向 | P2／Open（標記重複） |
| [#109115](https://github.com/NousResearch/hermes-agent/issues/109115) | Vertex-backed OpenAI 相容 endpoint 因 `anyOf` schema 拒絕 `notify` 參數，所有 turn 收到 400 | P2／Open |
| [#108656](https://github.com/NousResearch/hermes-agent/issues/108656) | Gemini quota 誤判、`RetryInfo.retryDelay` 被忽略 | Open |
| [#81437](https://github.com/NousResearch/hermes-agent/issues/81437) | Kanban `blocker_auth` 重生守衛讓失敗永不被記錄、circuit breaker 失效；作者稱本地已測過修復但仍未開 PR | Open |
| [#110374](https://github.com/NousResearch/hermes-agent/issues/110374) | Slack 狀態指示器因 slack-sdk ≥ 3.44 改用僅接受封閉列舉的新 API 而靜默失效；暫時解法為釘選 slack-sdk < 3.44 | P3／Open |

過去 24 小時內未查到具引用來源的 X（Twitter）官方公告或 Reddit／Discord 具體討論串內容，故本節僅列出可查證的 GitHub 一手資料。

---

*報告生成時間：2026-09-19 UTC*
