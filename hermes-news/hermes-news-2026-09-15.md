# Hermes-Agent 每日情報 — 2026-09-15

> 資料來源涵蓋截至 2026-09-15 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **新穩定版 v0.21.3（v2026.9.14）於 UTC 2026-09-14 16:04 釋出**：patch rollup，收攏自 v0.21.2 以來 338 個合併 PR、1,036 次 non-merge commits、2,642 個變更檔案。主要修復 remote dashboard session 因連續刷新（refresh burst）而提前過期，以及長駐程序（gateway／dashboard backend／ACP／CLI reader）重複洩漏 state.db writer handle 的問題；另新增 server-to-client JSON-RPC、OpenRouter OAuth PKCE 登入、HEIF/HEIC/AVIF 圖片解碼，以及 Gemini Omni Flash 1.1、Meta Muse、Kling 3.0 等新模型支援。（[Release](https://github.com/NousResearch/hermes-agent/releases/tag/v2026.9.14)）

2. **最重大進展：纏訟一週以上的 `DeletedWalGenerationError` 永久卡死回歸，根因修復已合併**。PR [#110544](https://github.com/NousResearch/hermes-agent/pull/110544)（teknium1 提交）於 09-14 合併，改用 OFD（open file description）鎖取代 per-process 鎖，讓鎖定綁在連線本身而非行程，避免旁支行程關閉檔案時誤將鎖一併取消、進而讓其他行程把仍在使用的 WAL/SHM 判定為「已刪除」並整組拒絕寫入。此修復一次關閉了 [#109727](https://github.com/NousResearch/hermes-agent/issues/109727)、[#110042](https://github.com/NousResearch/hermes-agent/issues/110042)、[#110276](https://github.com/NousResearch/hermes-agent/issues/110276)、[#109641](https://github.com/NousResearch/hermes-agent/issues/109641)、[#109823](https://github.com/NousResearch/hermes-agent/issues/109823)、[#109928](https://github.com/NousResearch/hermes-agent/issues/109928)、[#110082](https://github.com/NousResearch/hermes-agent/issues/110082)、[#110106](https://github.com/NousResearch/hermes-agent/issues/110106)，昨日報告列為代表案例的 [#109809](https://github.com/NousResearch/hermes-agent/issues/109809) 也一併關閉。**但**官方 pain-miner 彙整 issue [#110054](https://github.com/NousResearch/hermes-agent/issues/110054)（要求補齊使用者可見的復原流程與文件）目前仍為 Open；且 v0.21.3 的 release notes 並未明確列出 #110544 或上述 issue 編號（僅提及「state.db WAL refusal on cross-VM filesystems」等旁支修復），無法確認此次根因修復是否已趕上 v0.21.3 的 tag 切點，建議下一版再次確認是否已完全收斂。

3. **另一起延燒多日的 P1 也在今日合併修復**：[#107830](https://github.com/NousResearch/hermes-agent/issues/107830)（Anthropic 串流工具呼叫遇畸形 JSON 永久失敗）的整合修復 PR [#109324](https://github.com/NousResearch/hermes-agent/pull/109324) 於 09-14 19:53 UTC 合併，改採「緩衝工具輸入＋重試」取代直接停用 fine-grained streaming beta，issue 現已關閉。

4. **新增一起與 SQLite CVE 修補機制相關、但根因不同的問題**（[#111417](https://github.com/NousResearch/hermes-agent/issues/111417)，影響 v0.21.2）：`hermes update` 偵測到易受攻擊的 SQLite（3.7.0–3.51.2）版本時會嘗試更新，但官方 CI 用 uv 0.9.28 產生的 `uv.lock` 與實際出貨的 uv 0.12.9 不相容，導致 `--locked` 同步失敗；更新流程僅回報「partially complete」、無明確錯誤訊息，使用者的漏洞版本因此無法被實際修補。候選修復 PR [#111419](https://github.com/NousResearch/hermes-agent/pull/111419) 已提出，尚待合併。

5. **新增一起 P1 cron 議題**（[#111414](https://github.com/NousResearch/hermes-agent/issues/111414)，影響 v0.20.4）：排程到期時被靜默跳過——不建立任何新的執行紀錄或錯誤訊息，`next_run_at` 卻照常往後推進，且舊的執行紀錄列被回填了本次錯過的時間戳記，殘留的 `.fire-*.lock` 檔案亦未清除。根因疑似與 gateway 重啟時的競態有關，官方尚未定位確切原因，目前無已知暫時解法。

6. **[#108656](https://github.com/NousResearch/hermes-agent/issues/108656)（Gemini 配額誤判）過去 24 小時內查無新進展**，issue 與對應 PR [#108661](https://github.com/NousResearch/hermes-agent/pull/108661) 狀態維持不變，詳見第 2 節。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）
- **[#107830](https://github.com/NousResearch/hermes-agent/issues/107830) 已解決**：見第 1 節，PR [#109324](https://github.com/NousResearch/hermes-agent/pull/109324) 於 09-14 合併，改用緩衝重試機制處理畸形 JSON，issue 已關閉。此前的暫時解法（手動移除 `fine-grained-tool-streaming-2025-05-14` beta header）理論上已不再需要，但目前查無官方文件更新此建議，仍建議升級到含此修復的版本後再驗證。
- **[#109111](https://github.com/NousResearch/hermes-agent/issues/109111)（`auxiliary.vision.provider`／`.model` 對 Anthropic 被靜默忽略）過去 24 小時內查無新進展**，對應 PR [#109149](https://github.com/NousResearch/hermes-agent/pull/109149) 狀態未變。
- 官方 provider 文件無更新，`ANTHROPIC_API_KEY` 直接付費、`hermes model` 走 Claude Code OAuth 等安排維持不變。（[Provider 文件](https://github.com/NousResearch/hermes-agent/blob/main/website/docs/integrations/providers.md)）

### GPT（OpenAI）
- **[#109198](https://github.com/NousResearch/hermes-agent/issues/109198)（Codex 手動憑證跨帳號採用他人 token）過去 24 小時內查無新進展**，issue 仍為 Open，官方仍傾向以涵蓋範圍更廣的替代方案處理，尚無新提案 PR。
- **[#110157](https://github.com/NousResearch/hermes-agent/issues/110157)（未設定本地 Codex 憑證的 profile 借用 global-root 憑證成功推論，但 UI 誤報「未驗證」）過去 24 小時內查無新進展**，仍為 Open、無修復 PR。

### Gemini（Google）
- **[#108656](https://github.com/NousResearch/hermes-agent/issues/108656) 過去 24 小時內查無新進展**：quota 誤判與 `RetryInfo.retryDelay` 被忽略的問題仍未修復，PR [#108661](https://github.com/NousResearch/hermes-agent/pull/108661) 狀態未變。
- **[#109115](https://github.com/NousResearch/hermes-agent/issues/109115)（Vertex-backed OpenAI 相容 endpoint 拒絕 `terminal.notify` 的 `anyOf` schema）過去 24 小時內查無新進展**。
- v0.21.3 新增 Gemini Omni Flash 1.1 支援，其餘 provider 設定無其他變動。

### Auxiliary Model
- 過去 24 小時未查到新的 auxiliary model 選型討論。

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 類型 | 重點 |
|------|----------|------|------|
| **v0.21.3（v2026.9.14）** | 2026-09-14 | Patch rollup | 338 個合併 PR／1,036 次 commits；修復 remote dashboard session 過早過期、state.db writer handle 洩漏；新增 server-to-client JSON-RPC、OpenRouter OAuth PKCE、HEIF/HEIC/AVIF 解碼、多款新模型支援；**目前最新穩定版** |
| v0.21.2（v2026.9.11） | 2026-09-11 | Patch rollup | state.db 可靠性戰役、multiplexed profile 隔離強化、密碼隱藏式憑證保險箱；該版本引入的 `DeletedWalGenerationError` 永久卡死回歸已於 09-14 由 PR #110544 修復根因 |
| v0.21.1（v2026.9.7） | 2026-09-07 | Patch rollup | 5,139 commits／632 merged PR，效能、MCP 授權、cron、delegation 修復 |
| v0.21.0「The Pantheon Release」 | 2026-08-31 | 主版本 | Bot Mode、agent-to-agent 通訊、cron 記憶與連續性、即時子代理操控、MCP 集中管理面板、agent 操控桌面瀏覽器 |

（[Releases 總覽](https://github.com/NousResearch/hermes-agent/releases)）

---

## 4. 社群討論與已知問題

**DeletedWalGenerationError 的根因修復（PR #110544）是本日最大進展**，但官方追蹤 issue [#110054](https://github.com/NousResearch/hermes-agent/issues/110054) 仍要求補齊使用者可見的復原流程與文件，尚未關閉，建議持續觀察是否併入下一版：

| Issue／PR | 說明 | 狀態 |
|------|------|------|
| [#110054](https://github.com/NousResearch/hermes-agent/issues/110054) | 官方 pain-miner 彙整追蹤 issue：要求補齊 deleted-WAL guard 觸發後的產品內復原路徑與文件 | P1／**Open**（根因已由 #110544 修復，但 UX／文件缺口未關閉） |
| [#110544](https://github.com/NousResearch/hermes-agent/pull/110544) | 根因修復：改用 OFD 鎖，一次關閉 8 起 WAL 相關 regression issue | **Merged（09-14）** |

其他今日新增、值得留意的項目：

| Issue／PR | 說明 | 優先度／狀態 |
|------|------|------|
| [#111417](https://github.com/NousResearch/hermes-agent/issues/111417) | `hermes update` 因 CI 用 uv 版本與出貨 uv 版本 lockfile 不相容，SQLite CVE 修補靜默失敗；候選修復 PR [#111419](https://github.com/NousResearch/hermes-agent/pull/111419) 待合併 | P／Open |
| [#111414](https://github.com/NousResearch/hermes-agent/issues/111414) | 排程到期時被靜默跳過，無執行紀錄、無錯誤訊息，`next_run_at` 照常推進；根因未定位 | P1／Open |
| [#111420](https://github.com/NousResearch/hermes-agent/issues/111420) | Secret scoping 變更後，群組訊息被靜默丟棄 | P／Open |
| [#111436](https://github.com/NousResearch/hermes-agent/issues/111436) | Status endpoint 在切換模型後顯示的 context 資訊與實際不一致 | P3／Open |
| [#111429](https://github.com/NousResearch/hermes-agent/issues/111429) | ZAI vision fallback 模型在 coding endpoint 回傳 404 | Open |

過去 24 小時內未查到具引用來源的 X（Twitter）官方公告或 Reddit／Discord 具體討論串內容，故本節僅列出可查證的 GitHub 一手資料。

---

*報告生成時間：2026-09-15 UTC*
