# Hermes-Agent 每日情報 — 2026-09-12

> 資料來源涵蓋截至 2026-09-12 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **v0.21.2（v2026.9.11）今日釋出，主打「state.db 可靠性」與「multi-profile 隔離」兩大修復戰役**。距上一版 v0.21.1 僅 4 天，累積 947 個非合併 commit、312 個已合併 PR、140 位貢獻者。核心是六個 PR 組成的 state.db 修復戰役（[#108067](https://github.com/NousResearch/hermes-agent/pull/108067)、[#108076](https://github.com/NousResearch/hermes-agent/pull/108076)、[#108082](https://github.com/NousResearch/hermes-agent/pull/108082)、[#108074](https://github.com/NousResearch/hermes-agent/pull/108074)、[#108086](https://github.com/NousResearch/hermes-agent/pull/108086)、[#108130](https://github.com/NousResearch/hermes-agent/pull/108130)），解決 v0.21.0 引入的 session store 連線層問題，共關閉 44 個 0.21.x 資料庫損毀類 issue，寫入鎖等待時間據稱由 4–20 秒降到 0.01 秒。（[Release Notes](https://github.com/NousResearch/hermes-agent/releases/tag/v2026.9.11)）

2. **同批次另有超過 10 個 PR 集中修復 multiplexed profile（多帳號／多身分並行）互相污染的問題**：涵蓋 cron 觸發、MCP server 命名衝突、adapter 憑證與 fallback chain、session key 與 state.db 綁定、gateway 路由等面向（例如 [#108453](https://github.com/NousResearch/hermes-agent/pull/108453)、[#108440](https://github.com/NousResearch/hermes-agent/pull/108440)、[#108428](https://github.com/NousResearch/hermes-agent/pull/108428)、[#108406](https://github.com/NousResearch/hermes-agent/pull/108406)、[#108352](https://github.com/NousResearch/hermes-agent/pull/108352)、[#108319](https://github.com/NousResearch/hermes-agent/pull/108319)、[#108294](https://github.com/NousResearch/hermes-agent/pull/108294)），代表多副本／多身分同時掛載的使用情境正在被大幅收斂。

3. **昨日回報的 Anthropic 串流工具呼叫永久失敗問題（[#107830](https://github.com/NousResearch/hermes-agent/issues/107830)，P1）尚未修復，但已出現三個互相競爭的修復提案 PR**：[#107833](https://github.com/NousResearch/hermes-agent/pull/107833)、[#107864](https://github.com/NousResearch/hermes-agent/pull/107864)、[#108583](https://github.com/NousResearch/hermes-agent/pull/108583)（提議預設關閉 `fine-grained-tool-streaming-2025-05-14` 並在遇到畸形 JSON 時回退），三者皆尚未合併，v0.21.2 也未納入修復，issue 仍為 Open。

4. **新增一起 Gemini 配額處理設計缺陷**（[#108656](https://github.com/NousResearch/hermes-agent/issues/108656)，P2）：單一模型的 429 配額錯誤會被誤判為整個 API 金鑰的配額耗盡，導致本可正常運作的 fallback 模型也被連坐輪替；同時 Google 回傳的 `RetryInfo.retryDelay`（結構化重試秒數）被忽略，改用內部更短的通用重試間隔，導致對同一組金鑰重複觸發限流、逐步升級到 480 秒等級的冷卻。

5. **新增數起 P1 等級平台層 bug**：`hermes gateway start|stop --system` 在 sudo 下尋找錯誤的 systemd unit 名稱而失敗（[#108674](https://github.com/NousResearch/hermes-agent/issues/108674)，屬 [#105525](https://github.com/NousResearch/hermes-agent/issues/105525) 修復的回歸）；cron 的 fire-claim heartbeat 會把單純送達較慢的情況誤判為「已失去所有權」而記錄失敗（[#108341](https://github.com/NousResearch/hermes-agent/issues/108341)，標記重複）；git 淺層 checkout 的 graft 清理邏輯會誤刪仍被 reflog 引用的 commit 對應 graft，導致淺層倉庫損毀（[#108286](https://github.com/NousResearch/hermes-agent/issues/108286)，屬 [#105951](https://github.com/NousResearch/hermes-agent/issues/105951) 修復的二次回歸）。詳見第 4 節。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）
- **[#107830](https://github.com/NousResearch/hermes-agent/issues/107830) 進度更新**：見第 1 節，三個修復提案 PR 均待合併，官方尚未定案採用哪個方案（攔截 ValueError 回退到已修復的 `input_json`、預設關閉 beta header，或開放 `anthropic.beta_headers` 讓使用者自行設定）。目前唯一可行的暫時解法仍是手動從 `_COMMON_BETAS` 移除該 beta header。
- **新回報：模型中途切換（Haiku→Sonnet）會導致語言跑掉**（[#108392](https://github.com/NousResearch/hermes-agent/issues/108392)，P3，需要重現步驟）：使用者於全程繁體中文的 session 中，執行期由 `claude-haiku-4-5` 切換至 `claude-sonnet-4-6` 後，模型輸出混入韓文字元（如「확인됐습니다」）。推測與 context compression 後系統提示中的語言指示不再顯著、且新模型從近期 tool 輸出而非 system prompt 推斷語言有關。標記為需要重現，尚未被官方確認為真正缺陷。
- 官方 provider 文件無更新，`ANTHROPIC_API_KEY` 直接付費、`hermes model` 走 Claude Code OAuth（限 Claude Max）等安排維持不變，推薦預設模型仍為 `claude-sonnet-4-6`。（[Provider 文件](https://github.com/NousResearch/hermes-agent/blob/main/website/docs/integrations/providers.md)）

### GPT（OpenAI）
- **新回報：推理模型在有內部輸出上限的 OpenAI-相容 gateway 上會被「餓」到回傳空白**（[#108558](https://github.com/NousResearch/hermes-agent/issues/108558)，P2）：部分第三方 OpenAI-相容 gateway 對輸出 token 數設有內部上限，推理模型的思考過程耗盡該上限後就沒有餘量輸出最終答案，導致收到空回應。
- **新回報：token 計數器誤把 Responses API `function_call_output.output` 裡的 base64 圖片當成文字計算**（[#108320](https://github.com/NousResearch/hermes-agent/issues/108320)，P2）：會導致 usage／context 用量估算嚴重失準。
- **新回報：openai-codex 圖片產生工具忽略 `aspect_ratio` 參數、且不驗證 model id**（[#108171](https://github.com/NousResearch/hermes-agent/issues/108171)，P3）：要求直式圖片卻回傳橫式，屬於獨立於前幾日 [#107076](https://github.com/NousResearch/hermes-agent/issues/107076)（`gpt-5.5` 下線 404）的另一個問題。

### Gemini（Google）
- **新回報：model-scoped 配額錯誤誤判為整組金鑰耗盡，且忽略結構化 RetryInfo**（[#108656](https://github.com/NousResearch/hermes-agent/issues/108656)，P2）：見第 1 節。issue 內附有完整程式碼路徑分析（`gemini_native_adapter.py`、`turn_recovery.py`、`credential_pool.py`）與離線回歸測試建議，尚待官方採納修復方向。
- Vertex 路徑預設模型仍為 `google/gemini-3-flash-preview`，provider 文件無其他更新。

### Auxiliary Model
- 過去 24 小時未查到與 auxiliary model 相關的新討論或設定變更。

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 類型 | 重點 |
|------|----------|------|------|
| v0.21.2（v2026.9.11） | 2026-09-11 | Patch rollup | state.db 可靠性戰役（消除雙寫鎖競爭、修復 WAL 卡死、FTS 錯誤容錯、防跨 profile 資料庫綁定，寫鎖等待由 4–20 秒降至 0.01 秒）；multiplexed profile 隔離強化（bot allow-list、adapter 憑證、MCP vault 機密、webhook 路由各自獨立）；Desktop 後端「重複啟動風暴」修復；新增密碼隱藏式憑證保險箱（1Password／Bitwarden／本地保險箱代理登入）；新增經 SHA 鎖定的 plugin 目錄與 Desktop 統一外掛管理；Nous 免費推理層與新手導覽 |
| v0.21.1（v2026.9.7） | 2026-09-07 | Patch rollup | 5,139 commits／632 merged PR，效能、MCP 授權、cron、delegation 修復 |
| v0.21.0「The Pantheon Release」 | 2026-08-31 | 主版本 | Bot Mode、agent-to-agent 通訊、cron 記憶與連續性、即時子代理操控、MCP 集中管理面板、agent 操控桌面瀏覽器、預設 context 用量約減半 |
| v0.20.6（v2026.8.27） | 2026-08-27 | Patch | ~525 merged PR：consent-gated profile browsing、獨立瀏覽器視窗、MCP 目錄擴充至 50+ servers、結果快取、keychain 加密機密資訊 |

（[Releases 總覽](https://github.com/NousResearch/hermes-agent/releases)）

---

## 4. 社群討論與已知問題

過去 24 小時新開 issue 約 250+ 件（此 repo 開發節奏極快），以下僅列出除上方 LLM 相關議題外，較值得留意的 P1 等級非 LLM 基礎設施問題（均為官方 GitHub repo 上的公開 issue，尚待官方處理，非已證實修復）：

| Issue | 說明 | 優先度／狀態 |
|------|------|------|
| [#108674](https://github.com/NousResearch/hermes-agent/issues/108674) | `sudo hermes gateway start\|stop --system` 尋找錯誤的 systemd unit 名稱（`hermes-gateway-.service`）而失敗，屬 [#105525](https://github.com/NousResearch/hermes-agent/issues/105525) 修復的回歸 | P1／Open |
| [#108341](https://github.com/NousResearch/hermes-agent/issues/108341) | cron 的 fire-claim heartbeat 把單純送達較慢誤判為「已失去所有權」而記錄失敗 | P1／Open（標記重複） |
| [#108286](https://github.com/NousResearch/hermes-agent/issues/108286) | git 淺層 checkout 的 graft 清理邏輯誤刪仍被 reflog 引用的 commit graft，導致淺層倉庫損毀；屬 [#105951](https://github.com/NousResearch/hermes-agent/issues/105951) 修復的二次回歸 | P1／Open |
| [#107918](https://github.com/NousResearch/hermes-agent/issues/107918) | Web Dashboard／TUI 在已設定有效自訂 provider 的情況下仍顯示「Setup Required」 | P1／Open |
| [#108530](https://github.com/NousResearch/hermes-agent/issues/108530) | 官網 model-catalog.json 停留在 8/21 的舊快照，缺少 `gpt-6-astra` 等新模型資訊 | P2／Open |

過去 24 小時內未查到具引用來源的 X（Twitter）官方公告或 Reddit 討論串直接針對 v0.21.2 或上述 issue 展開新一輪討論，故本節僅列出可查證的 GitHub 一手資料，不臆測社群反應熱度。

---

*報告生成時間：2026-09-12 UTC*
