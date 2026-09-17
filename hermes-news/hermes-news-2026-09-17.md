# Hermes-Agent 每日情報 — 2026-09-17

> 資料來源涵蓋截至 2026-09-17 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **穩定版仍為 v0.21.3（v2026.9.14），過去 24 小時內無新 release**。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

2. **昨日報告列為 Critical 的 issue [#112430](https://github.com/NousResearch/hermes-agent/issues/112430) 今日終於關閉**：`_find_hermes_md()` 未防護 `PermissionError`、同時打垮 terminal／TUI／dashboard／gateway 所有介面的問題，擱置逾五個月後經 PR [#113143](https://github.com/NousResearch/hermes-agent/pull/113143) 與 [#112432](https://github.com/NousResearch/hermes-agent/pull/112432) 合併修復；修法比照既有 `_exists_or_denied()` 模式加上例外防護，並將保護範圍擴及同樣有此缺陷的 `_cursorrules_candidates()`。

3. **Codex 高努力推理被 watchdog 錯殺的問題 [#112909](https://github.com/NousResearch/hermes-agent/issues/112909) 今日關閉**：小提示（small prompt）搭配 `high` 推理努力時，原本套用最短的逾時門檻（12s／90s／120s），但現代推理模型常在 100–170 秒內都不吐出第一個 byte，導致健康的推理被判定逾時中斷。PR [#113169](https://github.com/NousResearch/hermes-agent/pull/113169) 把有效努力等級下限訂在 `high`，並改用 run-budget cap 取代小提示 watchdog 的短逾時。

4. **昨日剛新增的 MoA fallback 命名 bug [#112525](https://github.com/NousResearch/hermes-agent/issues/112525) 今日關閉**：PR [#113131](https://github.com/NousResearch/hermes-agent/pull/113131)／[#112528](https://github.com/NousResearch/hermes-agent/pull/112528) 讓 `try_activate_fallback` 把已解析出的真實 `(provider, model)` 正確寫回 `agent.model`／`agent.provider`，不再殘留未解析的 preset 名稱（如 `"default"`）送到聚合端點。

5. **新增 P3 issue [#113673](https://github.com/NousResearch/hermes-agent/issues/113673)**：`disk-cleanup` 外掛的空目錄清掃會誤刪 `$HERMES_HOME/.pg0` 底下的 PostgreSQL 維護目錄（如 `pg_logical/snapshots`），造成 checkpoint 反覆失敗、Hindsight daemon 離線且 pg0 無法重啟；候選修復 PR [#113675](https://github.com/NousResearch/hermes-agent/pull/113675)（把 `.pg0` 加入 `_EMPTY_DIR_SWEEP_PRUNE_DIRS`）尚待合併。

6. **新增 P2 issue [#113677](https://github.com/NousResearch/hermes-agent/issues/113677)**：dashboard 的 `/api/dashboard/plugins/hub` 端點在安裝較多外掛（實測 58 個）時，於主 event loop 同步逐一對每個外掛發 HTTPS 請求，阻塞 90–300 秒，導致整個 dashboard 無回應、WebSocket 斷線；候選修復 PR [#113682](https://github.com/NousResearch/hermes-agent/pull/113682) 把作業移到 worker thread，尚待合併。

7. **新增 P2 issue [#113670](https://github.com/NousResearch/hermes-agent/issues/113670)**：2026-06-23（commit `433db17c0a`）之前建立的 Windows Scheduled Task 從未被追溯套用後續的 restart-on-failure 安全強化，且 `wscript.exe` launcher 不等待就直接結束，使 Task Scheduler 偵測不到 gateway 當機、`RestartCount` 永遠是 0；已知造成一次約 15 分鐘的全平台中斷。候選修復 PR [#113674](https://github.com/NousResearch/hermes-agent/pull/113674) 尚待合併。

8. **新增 issue [#113683](https://github.com/NousResearch/hermes-agent/issues/113683)**：使用者回報每次透過 SSH 對 Linux 後端跑 `hermes update` 後，Windows GUI 就會出現「Hermes could not safely reserve this session. Try again.」，需反覆排錯且每次解法不盡相同；目前尚無對應 PR，回報者表示願意自行提交修復。

9. **新增 meta issue [#113672](https://github.com/NousResearch/hermes-agent/issues/113672)**：盤點出 5 起修復已實際合併進 main、但對應 issue 本身尚未關閉的案例（含 [#113133](https://github.com/NousResearch/hermes-agent/issues/113133)／[#111526](https://github.com/NousResearch/hermes-agent/issues/111526)／[#112568](https://github.com/NousResearch/hermes-agent/issues/112568)／[#113022](https://github.com/NousResearch/hermes-agent/issues/113022)／[#112600](https://github.com/NousResearch/hermes-agent/issues/112600)），屬 issue 衛生整理而非程式缺陷，待維護者確認後關閉。

10. **cron 失敗重複告警的 issue [#113684](https://github.com/NousResearch/hermes-agent/issues/113684) 今日被關閉為「not planned」**：原提案希望比照既有 incident ledger 加上 cooldown（預設 6 小時提醒一次），避免一分鐘一次的失敗任務每次都重新告警，逼得操作者乾脆把 `failure_deliver` 設為 `local` 整個關閉通知；頁面未附維護者說明關閉理由，實際是否有其他形式處理尚不確定。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）
- 過去 24 小時內查無 Anthropic／Claude 專屬的新 issue 或 PR 進展。既有的 [#109111](https://github.com/NousResearch/hermes-agent/issues/109111)（`auxiliary.vision` 對 Anthropic 被靜默忽略，PR #109149 待合併）狀態維持不變。

### GPT（OpenAI）
- **今日焦點即第 1 節第 3 點的 [#112909](https://github.com/NousResearch/hermes-agent/issues/112909)**：Codex `codex_responses` 模式下，high-effort 推理在小提示情境會被 non-stream watchdog 誤殺；PR #113169 合併後改用有效努力等級下限與 run-budget cap 處理逾時，取代原本按提示長度分級的短逾時。
- [#109198](https://github.com/NousResearch/hermes-agent/issues/109198)（Codex 手動憑證跨帳號誤用他人 token）、[#110157](https://github.com/NousResearch/hermes-agent/issues/110157)（未設定本地 Codex 憑證借用 global-root 卻誤報「未驗證」）過去 24 小時內查無新進展。

### Gemini（Google）
- [#108656](https://github.com/NousResearch/hermes-agent/issues/108656)（quota 誤判、`RetryInfo.retryDelay` 被忽略）過去 24 小時內查無新進展，對應 PR #108661 狀態未變。
- [#109115](https://github.com/NousResearch/hermes-agent/issues/109115)（Vertex-backed OpenAI 相容 endpoint 因 `notify` 參數的 `anyOf` union 被拒，導致所有 turn 收到 400）同樣查無新進展；建議修法是把 `anyOf` 拆成 `notify`（boolean）與 `watch_patterns`（array）兩個獨立參數。

### 本地模型／Auxiliary Model
- 昨日提到的 MoA fallback 命名 bug（[#112525](https://github.com/NousResearch/hermes-agent/issues/112525)）今日已關閉，詳見第 1 節第 4 點；該修復對所有透過 MoA 走 fallback 的 provider 組合（含本地模型 ensemble）皆有效。

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 類型 | 重點 |
|------|----------|------|------|
| **v0.21.3（v2026.9.14）** | 2026-09-14 | Patch rollup | 338 個合併 PR／1,036 次 commits；**目前最新穩定版**（過去 24 小時內無新 tag） |
| v0.21.2（v2026.9.11） | 2026-09-11 | Patch rollup | state.db 可靠性戰役、multiplexed profile 隔離強化、密碼隱藏式憑證保險箱 |
| v0.21.1（v2026.9.7） | 2026-09-07 | Patch rollup | 5,139 commits／632 merged PR，效能、MCP 授權、cron、delegation 修復 |
| v0.21.0「The Pantheon Release」 | 2026-08-31 | 主版本 | Bot Mode、agent-to-agent 通訊、cron 記憶與連續性、即時子代理操控、MCP 集中管理面板、agent 操控桌面瀏覽器 |

本日合併但尚未打入任何 tag 的修復包括：`_find_hermes_md()` PermissionError 防護（PR #113143／#112432）、Codex high-effort watchdog 誤殺修復（PR #113169）、MoA fallback 命名修復（PR #113131／#112528）等；另有多筆待合併中的候選修復（disk-cleanup `.pg0` 誤刪 PR #113675、dashboard plugins hub 阻塞 PR #113682、Windows Scheduled Task 追溯強化 PR #113674）。須等下一個 patch rollup 才會出現在 tagged 版本中。（[Releases 總覽](https://github.com/NousResearch/hermes-agent/releases)）

---

## 4. 社群討論與已知問題

**本日最值得留意的是三個新增的平台穩定性 issue**（disk-cleanup 誤刪 PostgreSQL 目錄、dashboard 外掛較多時整個卡死 90–300 秒、Windows Scheduled Task 追溯強化缺口），三者都已有候選 PR 但尚未合併：

| Issue／PR | 說明 | 優先度／狀態 |
|------|------|------|
| [#113673](https://github.com/NousResearch/hermes-agent/issues/113673) | disk-cleanup 空目錄清掃誤刪 `.pg0` PostgreSQL 維護目錄，導致 checkpoint 失敗、Hindsight 離線；候選 PR #113675 待合併 | P3／Open |
| [#113677](https://github.com/NousResearch/hermes-agent/issues/113677) | dashboard plugins hub 端點同步逐一發請求，外掛數多時阻塞 event loop 90–300 秒；候選 PR #113682 待合併 | P2／Open |
| [#113670](https://github.com/NousResearch/hermes-agent/issues/113670) | 舊版 Windows Scheduled Task 從未追溯套用 restart-on-failure 強化，崩潰偵測失效；候選 PR #113674 待合併 | P2／Open |
| [#113683](https://github.com/NousResearch/hermes-agent/issues/113683) | 每次 SSH 更新 Linux 後端後 Windows GUI 隨機無法連線（session reserve 失敗）；尚無對應 PR | Open |
| [#109115](https://github.com/NousResearch/hermes-agent/issues/109115) | Vertex-backed OpenAI 相容 endpoint 因 `anyOf` schema 拒絕 `notify` 參數，所有 turn 收到 400 | Open |
| [#108656](https://github.com/NousResearch/hermes-agent/issues/108656) | Gemini quota 誤判、`RetryInfo.retryDelay` 被忽略 | P2／Open |
| [#81437](https://github.com/NousResearch/hermes-agent/issues/81437) | Kanban `blocker_auth` 重生守衛讓失敗永不被記錄、circuit breaker 失效；已知案例 29 天內產生 42,663 次無效 respawn；作者稱已在本地測過修復但尚未開 PR | Open |
| [#110374](https://github.com/NousResearch/hermes-agent/issues/110374) | Slack 狀態指示器因 slack-sdk ≥ 3.44 改用僅接受封閉列舉的新 API 而靜默失效；暫時解法為釘選 slack-sdk < 3.44 | P3／Open |

過去 24 小時內未查到具引用來源的 X（Twitter）官方公告或 Reddit／Discord 具體討論串內容，故本節僅列出可查證的 GitHub 一手資料。

---

*報告生成時間：2026-09-17 UTC*
