# Hermes-Agent 每日情報 — 2026-09-18

> 資料來源涵蓋截至 2026-09-18 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **穩定版仍為 v0.21.3（v2026.9.14），過去 24 小時內無新 release**，但 issue／PR 活動量明顯高於平日（單日新增與更新逾 20 筆）。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

2. **新增 P1 issue [#114592](https://github.com/NousResearch/hermes-agent/issues/114592)**：`hermes update --yes` 在 pre-update backup 失敗（更新回執顯示 `"pre_update_backup": {"ok": false}`）時仍繼續執行，導致 `~/.hermes/SOUL.md` 被覆寫成指向自身的 symlink，觸發「Too many levels of symbolic links」錯誤，8 分鐘內把 gateway 反覆重啟 7 次；目前尚無對應 PR，建議修法是備份失敗即中止更新，並在更新後驗證 `SOUL.md` 仍為一般文字檔。

3. **新增 issue [#114594](https://github.com/NousResearch/hermes-agent/issues/114594)**：當 session context 超出模型視窗上限時，preflight compression 的 120 秒無進度 watchdog 比摘要本身的 180 秒逾時還短，任何需要走一次摘要的工作必定失敗；系統還會誤導使用者重跑 `/compress`（此時根本送不出 provider 請求），而非直接使用既有的 LLM-free emergency-prune 機制。此結構性缺陷是從 9/17 一起真實事故中追出的。

4. **P2 issue [#114579](https://github.com/NousResearch/hermes-agent/issues/114579) 當日即有修復 PR [#114583](https://github.com/NousResearch/hermes-agent/pull/114583)**：Web Dashboard 聊天分頁貼上圖片（Ctrl+V）後 xterm 完全當機，根因是前端 100ms 硬編碼延遲與後端 RPC 之間的 race condition，加上 WebSocket 重連時舊的 Node PTY worker 未被清除、每個約占 150MB 記憶體並持續累積。

5. **Closed：[#114590](https://github.com/NousResearch/hermes-agent/issues/114590)**：Desktop／TUI 正常退出時只 flush 訊息，未寫入 `sessions.ended_at`，導致近半數 session 記錄（204 筆中 99 筆）要等到下次開機的 orphan-reap 才會結案，最長延遲超過 17 小時，影響 session 時長／活躍數等分析指標；頁面上未附可見的修復 PR 或維護者關閉說明，實際處理方式待確認。

6. **P2 issue [#114571](https://github.com/NousResearch/hermes-agent/issues/114571) 已有備妥的修復 PR [#114577](https://github.com/NousResearch/hermes-agent/pull/114577)**：macOS 上 `_launchd_degrade_or_raise()` 誤把 launchctl exit code 5（常見於強制重裝／重啟一個仍存活的 gateway 時的過期 job 註冊）當成「系統不支援 launchd 管理」，導致對一個健康、有 launchd 監督的 gateway 額外起一個 detached 副本，並寫入 `launchd-unsupported` 標記，讓 auto-start／crash-restart 從此失效。

7. **P2 issue [#114574](https://github.com/NousResearch/hermes-agent/issues/114574)**：OpenRouter provider 在所有模型（Claude／GPT／DeepSeek／NVIDIA／Stealth 等）上都回傳空白的 HTTP 400，只有 Gemini provider 正常且錯誤訊息完整；使用者已排除金鑰、餘額、網路、VPN 等常見原因，OpenRouter 端 log 也是空的，懷疑是 request payload 格式或未記載的預設參數問題，目前標記 `needs-repro`，尚無對應 PR。

8. **P2 issue [#114572](https://github.com/NousResearch/hermes-agent/issues/114572) 已有對應修復 PR [#114573](https://github.com/NousResearch/hermes-agent/pull/114573)**：Desktop 刪除自訂 endpoint 時會把 ID slugify 後才查找，導致含點號的舊版 provider key（如 `local-127.0.0.1:8283` → `local-127-0-0-1-8283`）刪不掉、回傳 404；修法是先精確比對原始 ID，找不到才 fallback 到 slug 比對。

9. **P3 issue [#114526](https://github.com/NousResearch/hermes-agent/issues/114526) 已有候選修復 PR [#114545](https://github.com/NousResearch/hermes-agent/pull/114545)**：`hermes plugins install` 對公開 catalog repo 也走了需要憑證的 clone 路徑，觸發「could not read Username for 'https://github.com': terminal prompts disabled」，即使同一個 repo 用一般 `git clone`／`git ls-remote` 都能正常匿名存取；目前只能手動 clone 固定 commit 再 `hermes plugins enable` 繞過。

10. **新增 P3 feature request [#114576](https://github.com/NousResearch/hermes-agent/issues/114576)**：使用者希望在啟用 Mixture-of-Agents 的 session 裡能針對單一 prompt（如 `/solo <prompt>`）跳過 reference fan-out，避免簡單請求（例如把已產出的文件轉存 PDF）也要跑一輪完整的多模型聚合、浪費 token；目前只有「切到純模型 preset 再切回來」或改用子代理委派等繞路解法。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）
- 過去 24 小時內查無 Anthropic／Claude 專屬的新 issue 或 PR 進展。既有的 [#109111](https://github.com/NousResearch/hermes-agent/issues/109111)（`auxiliary.vision` 對 Anthropic 被靜默忽略，PR #109149 待合併）狀態維持不變。

### GPT（OpenAI）
- PR [#114593](https://github.com/NousResearch/hermes-agent/pull/114593)：把「`reasoning_effort 'none'` unsupported」這種措辭加入 retry ladder 的參數拒絕比對清單，讓走 OpenAI 相容自訂 provider、且用非標準錯誤字串拒絕 `reasoning_effort: "none"` 的情境，也能被既有機制辨識並自動移除該參數重試，而不是直接讓 turn 失敗；此 PR 被標記為 PR #114461（較廣版本）的重複／窄版，兩者合併決策尚未定案。
- 既有 [#109198](https://github.com/NousResearch/hermes-agent/issues/109198)（Codex 手動憑證跨帳號誤用他人 token）、[#110157](https://github.com/NousResearch/hermes-agent/issues/110157)（未設定本地 Codex 憑證借用 global-root 卻誤報「未驗證」）過去 24 小時內查無新進展。

### Gemini（Google）
- 今日 OpenRouter 全模型 400 錯誤（[#114574](https://github.com/NousResearch/hermes-agent/issues/114574)，見第 1 節第 7 點）的回報中，唯一維持正常運作的正是 Gemini provider，且錯誤訊息完整可讀，可作對照組；但問題根源在 OpenRouter 端而非 Gemini 整合本身。
- 既有 [#108656](https://github.com/NousResearch/hermes-agent/issues/108656)（quota 誤判、`RetryInfo.retryDelay` 被忽略）與 [#109115](https://github.com/NousResearch/hermes-agent/issues/109115)（Vertex-backed OpenAI 相容 endpoint 因 `notify` 參數的 `anyOf` union 被拒，全 turn 收到 400）過去 24 小時內皆查無新進展。

### 本地模型／Auxiliary Model
- 過去 24 小時內查無本地模型／auxiliary LLM 專屬的新進展。第 1 節第 10 點的 MoA fan-out opt-out 提案若實作，將連帶降低以本地模型作為 reference 時的無謂算力消耗，但目前仍停留在 feature request 階段。

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 類型 | 重點 |
|------|----------|------|------|
| **v0.21.3（v2026.9.14）** | 2026-09-14 | Patch rollup | 338 個合併 PR／1,036 次 commits；**目前最新穩定版**（過去 24 小時內無新 tag） |
| v0.21.2（v2026.9.11） | 2026-09-11 | Patch rollup | state.db 可靠性戰役、multiplexed profile 隔離強化、密碼隱藏式憑證保險箱 |
| v0.21.1（v2026.9.7） | 2026-09-07 | Patch rollup | 5,139 commits／632 merged PR，效能、MCP 授權、cron、delegation 修復 |
| v0.21.0「The Pantheon Release」 | 2026-08-31 | 主版本 | Bot Mode、agent-to-agent 通訊、cron 記憶與連續性、即時子代理操控、MCP 集中管理面板、agent 操控桌面瀏覽器 |

過去 24 小時內合併進 main 的修復包含 Slack 原生任務卡片重開（PR #113524）、目錄型外掛與同名 pip 套件的身分衝突修正（PR #114539）、Bedrock 上 Grok context 與快取上限修復（PR #111219）、Slack Enterprise Grid 檔案重導向 bearer 保留（PR #114483）、state.db 多重可寫 handle 警告具名化（PR #114415）、MCP 動態工具刷新在 server session 已消失時跳過（PR #114413）、`.env` 自我參照值不再隨進程重複膨脹（PR #114427）等十餘筆，但均屬既有 v0.21.3 之後的零散修復，尚未打入新 tag。第 1 節提到的多項候選修復（PR #114583、#114577、#114573、#114545、#114593、#114591）仍在 open review 階段，須等下一個 patch rollup 才會出現在 tagged 版本中。（[Releases 總覽](https://github.com/NousResearch/hermes-agent/releases)）

---

## 4. 社群討論與已知問題

**本日最值得留意的是新增的 P1 更新流程資料損毀 issue（#114592）與結構性的 context compression 缺陷（#114594）**，兩者都還沒有對應 PR；另有三筆已附修復 PR 的平台穩定性 issue（dashboard 圖片貼上當機、macOS launchd 誤判、自訂 endpoint 刪除失敗）正待合併。

| Issue／PR | 說明 | 優先度／狀態 |
|------|------|------|
| [#114592](https://github.com/NousResearch/hermes-agent/issues/114592) | `hermes update --yes` 在備份失敗時仍繼續，導致 `SOUL.md` 損毀為自我指向 symlink、gateway 反覆重啟；尚無 PR | P1／Open |
| [#114594](https://github.com/NousResearch/hermes-agent/issues/114594) | preflight compression watchdog（120s）短於摘要逾時（180s），超限 context 應 emergency-prune 而非誤導使用者重跑 `/compress`；尚無 PR | Open |
| [#114579](https://github.com/NousResearch/hermes-agent/issues/114579) | Web Dashboard 貼上圖片後聊天分頁完全當機，PTY race＋Node worker 洩漏；候選 PR #114583 待合併 | P2／Open |
| [#114571](https://github.com/NousResearch/hermes-agent/issues/114571) | macOS launchctl exit 5 被誤判為不支援 launchd，額外起 detached 副本並關閉 auto-restart；候選 PR #114577 待合併 | P2／Open |
| [#114574](https://github.com/NousResearch/hermes-agent/issues/114574) | OpenRouter provider 全模型回傳空白 HTTP 400，僅 Gemini 正常；標記 `needs-repro`，尚無 PR | P2／Open |
| [#114572](https://github.com/NousResearch/hermes-agent/issues/114572) | 含點號的自訂 endpoint provider key 因 slugify 比對失敗無法刪除；候選 PR #114573 待合併 | P2／Open |
| [#114526](https://github.com/NousResearch/hermes-agent/issues/114526) | `hermes plugins install` 對公開 catalog repo 誤走需憑證的 clone 路徑；候選 PR #114545 待合併 | P3／Open |
| [#109115](https://github.com/NousResearch/hermes-agent/issues/109115) | Vertex-backed OpenAI 相容 endpoint 因 `anyOf` schema 拒絕 `notify` 參數，所有 turn 收到 400 | P2／Open |
| [#108656](https://github.com/NousResearch/hermes-agent/issues/108656) | Gemini quota 誤判、`RetryInfo.retryDelay` 被忽略 | Open |
| [#81437](https://github.com/NousResearch/hermes-agent/issues/81437) | Kanban `blocker_auth` 重生守衛讓失敗永不被記錄、circuit breaker 失效；作者稱本地已測過修復但仍未開 PR | Open |
| [#110374](https://github.com/NousResearch/hermes-agent/issues/110374) | Slack 狀態指示器因 slack-sdk ≥ 3.44 改用僅接受封閉列舉的新 API 而靜默失效；暫時解法為釘選 slack-sdk < 3.44 | P3／Open |

過去 24 小時內未查到具引用來源的 X（Twitter）官方公告或 Reddit／Discord 具體討論串內容，故本節僅列出可查證的 GitHub 一手資料。

---

*報告生成時間：2026-09-18 UTC*
