# Hermes-Agent 每日情報 — 2026-09-14

> 資料來源涵蓋截至 2026-09-14 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **穩定版仍為 v0.21.2（v2026.9.11），過去 24 小時內無新版釋出**。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

2. **本日最值得關注的是非 LLM 層級但影響全體使用者的重大回歸：v0.21.2 引入的 `DeletedWalGenerationError` 在特定情境下會永久卡死，且無法自我復原**（[#109928](https://github.com/NousResearch/hermes-agent/issues/109928)，P1）。v0.21.2 原本主打「state.db 可靠性戰役」，但新版的 WAL 世代安全檢查一旦觸發，會讓所有後續資料庫寫入以相同 FATAL 錯誤永久失敗，官方回報此行為是相對 v2026.6.5（會自動重試一次並自癒）的退化。過去 24 小時內至少有 13 起獨立 issue 回報同一根因，涵蓋 Desktop App（[#109809](https://github.com/NousResearch/hermes-agent/issues/109809)）、Gateway（[#110082](https://github.com/NousResearch/hermes-agent/issues/110082)）、多 profile 併發（[#109946](https://github.com/NousResearch/hermes-agent/issues/109946)）、fleet 重啟（[#109966](https://github.com/NousResearch/hermes-agent/issues/109966)）等場景，官方自動化 pain-miner cron 已將其彙整為集中追蹤 issue（[#110054](https://github.com/NousResearch/hermes-agent/issues/110054)）。截至目前**尚無修復 PR**，唯一已知暫時解法是降版回 v2026.6.5。詳見第 4 節。

3. **[#107830](https://github.com/NousResearch/hermes-agent/issues/107830)（Anthropic 串流工具呼叫遇畸形 JSON 永久失敗，P1）持續推進**：整合方案 [#109324](https://github.com/NousResearch/hermes-agent/pull/109324) 經審查後修正了 `partial_tool_names` 會在重試路徑間殘留、導致錯誤訊息顯示錯誤工具名稱的問題，但截至目前仍未合併，issue 仍為 Open。

4. **新增一起 Windows 專屬的 Anthropic OAuth 憑證永久鎖死問題**（[#109799](https://github.com/NousResearch/hermes-agent/issues/109799)，P2）：Windows 上因暫時性檔案存取被拒（WinError 5）導致 OAuth token 刷新後的憑證提交失敗，系統會將已作廢的舊 token 標記為不可恢復，造成使用者被完全鎖在外面、需要重新整個登入。修復 PR [#109801](https://github.com/NousResearch/hermes-agent/pull/109801) 已提出（改用具重試機制的 `utils.atomic_replace`），待合併。

5. **昨日回報的 Codex 跨帳號憑證污染問題（[#109198](https://github.com/NousResearch/hermes-agent/issues/109198)，P2）修復方向改變**：對應 PR [#109200](https://github.com/NousResearch/hermes-agent/pull/109200) 已被標記為 duplicate of #100423，官方轉向採用範圍更廣的替代方案，問題本身仍未解決。另新增一起相關但不同根因的 Codex 憑證議題：無本地 Codex 憑證的 profile 實際上會借用 global-root 憑證成功推論，但 model picker UI 卻誤報該 provider「未驗證」（[#110157](https://github.com/NousResearch/hermes-agent/issues/110157)，P2，尚無修復 PR）。

6. **Gemini 配額誤判問題（[#108656](https://github.com/NousResearch/hermes-agent/issues/108656)，P2）修復 PR 持續推進**：[#108661](https://github.com/NousResearch/hermes-agent/pull/108661) 經審查抓出三個復原路徑上的迴歸問題，作者已在後續 commit 修正並通過 427 項測試，但 PR 仍未合併。

7. **[#109495](https://github.com/NousResearch/hermes-agent/issues/109495)（Gateway `/yolo` 未重新驗證 admin 權限）仍未解決**：修復 PR [#109503](https://github.com/NousResearch/hermes-agent/pull/109503)、[#109500](https://github.com/NousResearch/hermes-agent/pull/109500) 均仍為 Open。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）
- **[#107830](https://github.com/NousResearch/hermes-agent/issues/107830) 進度**：見第 1 節，PR [#109324](https://github.com/NousResearch/hermes-agent/pull/109324) 修正了 `_start_stream_attempt` 未重置 `partial_tool_names` 的問題，方案本身逐漸成熟，但尚未合併。暫時解法仍是手動移除 `fine-grained-tool-streaming-2025-05-14` beta header。
- **新回報：Windows OAuth 刷新遇 WinError 5 會永久鎖死憑證**（[#109799](https://github.com/NousResearch/hermes-agent/issues/109799)，P2）：根因是 `agent/anthropic_credentials.py` 用裸的 `os.replace()` 取代憑證檔，Windows 上若有其他 handle（如防毒軟體或 Hermes 自身行程）正在讀取該檔案，rename 會直接失敗；由於 server 端 token 已刷新成功、舊 token 已作廢，一旦本地寫入失敗，使用者會完全被鎖在外面，唯一復原方式是用 `hermes auth add anthropic --type oauth` 或 `claude setup-token` 重新登入。修復 PR [#109801](https://github.com/NousResearch/hermes-agent/pull/109801) 待合併。
- **[#109111](https://github.com/NousResearch/hermes-agent/issues/109111)（`auxiliary.vision.provider`／`.model` 對 Anthropic 被靜默忽略）仍未修復**，對應 PR [#109149](https://github.com/NousResearch/hermes-agent/pull/109149) 待合併。
- 官方 provider 文件無更新，`ANTHROPIC_API_KEY` 直接付費、`hermes model` 走 Claude Code OAuth 等安排維持不變。（[Provider 文件](https://github.com/NousResearch/hermes-agent/blob/main/website/docs/integrations/providers.md)）

### GPT（OpenAI）
- **[#109198](https://github.com/NousResearch/hermes-agent/issues/109198)（Codex 手動憑證跨帳號採用他人 token）修復方向轉向**：原修復 PR [#109200](https://github.com/NousResearch/hermes-agent/pull/109200) 已被標記為 duplicate of #100423，官方傾向採用範圍更廣的替代修法，問題本身仍為 Open。
- **新回報：Codex 憑證解析與 UI 顯示不一致**（[#110157](https://github.com/NousResearch/hermes-agent/issues/110157)，P2）：未設定本地 Codex 憑證的 profile 實際上會沿用 global-root 憑證成功完成推論，但 model picker UI 卻錯誤顯示該 provider 為「未驗證」，造成使用者困惑；社群提出的候選修法（來自第三方 fork `jumper-lab/jumper-hermes#3`）已被官方明確判定不適合合併，issue 尚無指派、無修復 PR。

### Gemini（Google）
- **[#108656](https://github.com/NousResearch/hermes-agent/issues/108656) 進度**：修復 PR [#108661](https://github.com/NousResearch/hermes-agent/pull/108661) 經審查者 BearHuddleston 抓出三個復原路徑迴歸問題，作者已在 commit `d302a5e` 修正並通過全部 427 項測試，但 PR 仍未合併，quota 誤判與 `RetryInfo.retryDelay` 被忽略的原始問題仍未修復。
- **[#109115](https://github.com/NousResearch/hermes-agent/issues/109115)（Vertex-backed OpenAI 相容 endpoint 拒絕 `terminal.notify` 的 `anyOf` schema）仍未修復**，對應 PR [#109145](https://github.com/NousResearch/hermes-agent/pull/109145) 待合併；建議修法是將 `notify`／`watch_patterns` 拆為兩個獨立參數以避開 union schema。
- Vertex 路徑預設模型仍為 `google/gemini-3-flash-preview`，provider 文件無其他更新。

### Auxiliary Model
- 過去 24 小時未查到新的 auxiliary model 選型討論（[#109111](https://github.com/NousResearch/hermes-agent/issues/109111) 屬 Anthropic vision 設定失效，已列入上方 Claude 小節）。

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 類型 | 重點 |
|------|----------|------|------|
| v0.21.2（v2026.9.11） | 2026-09-11 | Patch rollup | state.db 可靠性戰役、multiplexed profile 隔離強化、密碼隱藏式憑證保險箱、SHA 鎖定 plugin 目錄；**過去 24 小時內為最新穩定版，無新版釋出**，但該版本自身引入了第 1、4 節所述的 `DeletedWalGenerationError` 永久卡死回歸 |
| v0.21.1（v2026.9.7） | 2026-09-07 | Patch rollup | 5,139 commits／632 merged PR，效能、MCP 授權、cron、delegation 修復 |
| v0.21.0「The Pantheon Release」 | 2026-08-31 | 主版本 | Bot Mode、agent-to-agent 通訊、cron 記憶與連續性、即時子代理操控、MCP 集中管理面板、agent 操控桌面瀏覽器 |
| v0.20.6（v2026.8.27） | 2026-08-27 | Patch | ~525 merged PR：consent-gated profile browsing、MCP 目錄擴充 |

（[Releases 總覽](https://github.com/NousResearch/hermes-agent/releases)）

---

## 4. 社群討論與已知問題

**本日最大宗的社群回報集中在 v0.21.2 的 `DeletedWalGenerationError` 永久卡死回歸**，官方自動化 pain-miner cron 已將其列為集中追蹤項目（[#110054](https://github.com/NousResearch/hermes-agent/issues/110054) 標題直接寫明「4 個 Discord 討論串、本週 13 起 issue」），以下列出代表性項目：

| Issue | 說明 | 優先度／狀態 |
|------|------|------|
| [#110054](https://github.com/NousResearch/hermes-agent/issues/110054) | 官方 pain-miner 彙整追蹤 issue：deleted-WAL guard 觸發後產品內無復原路徑，使用者重啟／詢問 agent／執行 `doctor --fix` 反而讓情況惡化 | P1／Open |
| [#109928](https://github.com/NousResearch/hermes-agent/issues/109928) | 2026.9.11：`DeletedWalGenerationError` 變成永久卡死，相對 2026.6.5（會自癒）的迴歸；官方暫時解法為降版 | P1／Open |
| [#109809](https://github.com/NousResearch/hermes-agent/issues/109809) | Desktop App 因 state.db 每約 5 分鐘的 WAL 世代輪替而完全無法使用（CLI／Telegram 不受影響） | P1／Open |
| [#110082](https://github.com/NousResearch/hermes-agent/issues/110082) | Gateway 反覆觸發 `DeletedWalGenerationError` 並持續處於降級狀態，需手動重啟 | P1／Open |
| [#109946](https://github.com/NousResearch/hermes-agent/issues/109946) | Desktop／dashboard 的「全部 profile」側欄會在正在運作的 profile gateway 上觸發 deleted WAL generation | P1／Open |
| [#109966](https://github.com/NousResearch/hermes-agent/issues/109966) | Fleet 重啟時交接的 WAL 世代會留下長時間持有者，導致新開啟者被阻擋數小時 | P1／Open |

另有數起非 WAL 相關但值得留意的項目：

| Issue／PR | 說明 | 優先度／狀態 |
|------|------|------|
| [#109495](https://github.com/NousResearch/hermes-agent/issues/109495) | Gateway `/yolo` 未在 side-effect boundary 重新驗證 admin 權限；修復 PR [#109503](https://github.com/NousResearch/hermes-agent/pull/109503)／[#109500](https://github.com/NousResearch/hermes-agent/pull/109500) 仍待合併 | P2／Open |
| [#109904](https://github.com/NousResearch/hermes-agent/issues/109904) | Gateway 入站日誌仍會從多行訊息中洩漏 `Password: <值>` | P2／Open |
| [#110416](https://github.com/NousResearch/hermes-agent/issues/110416) | Session store 未經 redact 就保存憑證內容（含 tool_calls、reasoning） | P3／Open |
| [#110464](https://github.com/NousResearch/hermes-agent/issues/110464) | 寫入用憑證黑名單與讀取用憑證黑名單已產生偏移 | P3／Open |
| [#110472](https://github.com/NousResearch/hermes-agent/issues/110472) | electron 40.x 仍存在未修復的 HIGH 等級安全公告，但 `npm audit` 誤報為已修復 | P3／Open |

第三方部落格 Julian Goldie 已針對 state.db 損毀問題發布非官方「2 分鐘修復」操作指南，內容未經 Nous Research 官方驗證，僅供參考，不代表官方立場或已證實有效（[連結](https://juliangoldie.com/hermes-agent-state-db-corruption-fix/)）。過去 24 小時內未查到具引用來源的 X（Twitter）官方公告，針對上述回歸展開新一輪説明或回應，故本節僅列出可查證的 GitHub 一手資料。

---

*報告生成時間：2026-09-14 UTC*
