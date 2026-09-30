# Hermes-Agent 每日情報 — 2026-09-30

> 資料來源涵蓋截至 2026-09-30 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **目前穩定版仍為 v0.21.5（v2026.9.24）**：過去 24 小時查無新版本或新 tag。該版整併約 460 個 PR，包含 Desktop plugin SDK 一批更新、法／德／西語目錄翻譯，以及設定載入與 gateway 訊息處理的效能優化。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

2. **9/30 新開兩則 P0 Discord 相關 Bug，皆與 prompt cache 失效有關**：[#128787](https://github.com/NousResearch/hermes-agent/issues/128787) 指出 Discord slash command、thread starter 與語音輸入建構事件的方式與一般訊息不同（缺少 `auto_skill`、`chat_name` 格式與 topic 來源不一致），在同一對話於訊息與 slash 之間切換時會造成 A→B→A 的 prompt-cache 破壞；[#128796](https://github.com/NousResearch/hermes-agent/issues/128796) 指出 relay adapter 的 slash／按鈕互動使用原始 username，與文字訊息的顯示名稱不一致，同樣破壞快取。兩則作者皆表示願意提交修復，後者已有相關 PR [#128797](https://github.com/NousResearch/hermes-agent/pull/128797)。

3. **9/30 新開 P1：Windows 安裝程式 Hermes-Setup.exe 於 bootstrap 成功後，「Launch」按鈕偶發卡死**：[#128804](https://github.com/NousResearch/hermes-agent/issues/128804) 回報前端呼叫 Tauri 後端的 invoke promise 從未結束，Hermes.exe 不會啟動，CI 上約每四次出現一次，9/23、9/27、9/28 皆曾觀察到；根因尚不明，無 workaround。

4. **9/30 新開 P2：context compression 摘要會遺失已落地工具輸出的檔案路徑**：[#128786](https://github.com/NousResearch/hermes-agent/issues/128786) 指出壓縮後的一行摘要沒有保留原本的 persisted-output 路徑，模型之後無法再讀取完整輸出，只能重跑指令。已有修復 PR [#128790](https://github.com/NousResearch/hermes-agent/pull/128790)。

5. **其他 9/30 新開 issue 多為安裝／CLI／平台小問題**：`hermes update` 遇到大量未追蹤檔案時卡在 autostash（[#128782](https://github.com/NousResearch/hermes-agent/issues/128782)）、`hermes doctor --live` 誤判 Browser 檢查失敗（[#128759](https://github.com/NousResearch/hermes-agent/issues/128759)）、Kanban 釘選任務的 runtime 可能靜默退回其他模型（[#128751](https://github.com/NousResearch/hermes-agent/issues/128751)）等，詳見第 4 節。

6. **社群媒體與第三方報導：查無過去 24 小時內的新發文**。搜尋結果僅有既有的官方頁面、Releasebot 彙整頁與較早的報導（如 [TechCrunch 7/13：Nous Research 洽談新一輪融資、估值 15 億美元](https://techcrunch.com/2026/07/13/hermes-agent-maker-nous-research-in-talks-for-new-funding-at-1-5b-valuation/)），非新動態，不作為今日事實來源。

> 註：昨日情報所列的 #127260、#127234 等追蹤項目，今日未逐一重新查核其進展，請以各 issue 頁面為準。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）
- 今日未查到新的 Anthropic 專屬回報。

### GPT（OpenAI／Azure／Bedrock）
- 今日未查到新的 OpenAI 專屬回報。

### Gemini（Google）
- 今日未查到新的 Gemini 專屬回報。

### 本地模型／Local Runtime
- 今日未查到新的本地模型專屬回報。

### 與模型行為間接相關
- Discord 的兩則 P0（[#128787](https://github.com/NousResearch/hermes-agent/issues/128787)、[#128796](https://github.com/NousResearch/hermes-agent/issues/128796)）與壓縮摘要問題（[#128786](https://github.com/NousResearch/hermes-agent/issues/128786)）會影響 prompt cache 命中與模型可取得的上下文，使用任何模型供應商的 Discord gateway 使用者皆可能受影響。
- Kanban 釘選任務的 runtime 可能靜默退回不同模型：[#128751](https://github.com/NousResearch/hermes-agent/issues/128751)（P3）。

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 類型 | 重點 |
|------|----------|------|------|
| **v0.21.5（v2026.9.24）** | 2026-09-24 | Patch rollup | **目前最新穩定版**；約 460 PR、Desktop plugin SDK、法／德／西語翻譯、效能優化 |
| v0.21.4（v2026.9.21） | 2026-09-21 | Patch rollup | host-wide gateway singleton lock、JSONL 輸出、skill 自動載入、LTX 2.5／Kling O3 影片模型 |
| v0.21.3（v2026.9.14） | 2026-09-14 | Patch rollup | refresh token rotation 與遠端 dashboard 工作階段修復、state db writer handle 洩漏修復 |

來源：[Releases](https://github.com/NousResearch/hermes-agent/releases)。過去 24 小時無新版本。

---

## 4. 社群討論與已知問題

| Issue／PR | 說明 | 優先度／狀態 |
|------|------|------|
| [#128796](https://github.com/NousResearch/hermes-agent/issues/128796) | relay 的 Discord slash／按鈕使用原始 username，破壞 prompt cache | P0／Open（PR [#128797](https://github.com/NousResearch/hermes-agent/pull/128797)） |
| [#128787](https://github.com/NousResearch/hermes-agent/issues/128787) | Discord slash／thread／語音事件遺失頻道 skill、名稱不一致 | P0／Open |
| [#128804](https://github.com/NousResearch/hermes-agent/issues/128804) | Windows Hermes-Setup.exe 偶發於 Launch 卡死 | P1／Open |
| [#128786](https://github.com/NousResearch/hermes-agent/issues/128786) | 壓縮摘要遺失 persisted-output 路徑 | P2／Open（PR [#128790](https://github.com/NousResearch/hermes-agent/pull/128790)） |
| [#128799](https://github.com/NousResearch/hermes-agent/issues/128799) | 以套件管理員建置的安裝環境缺少 locale | P2／Open |
| [#128782](https://github.com/NousResearch/hermes-agent/issues/128782) | `hermes update` 遇大量未追蹤檔案卡在 autostash | P2／Open |
| [#128759](https://github.com/NousResearch/hermes-agent/issues/128759) | `hermes doctor --live` 誤判 Browser 檢查失敗 | P2／Open |
| [#128758](https://github.com/NousResearch/hermes-agent/issues/128758) | `hermes tools --summary` 被互動式 TTY 檢查擋下 | P2／Open |
| [#128770](https://github.com/NousResearch/hermes-agent/issues/128770) | 無 WSL 的 Windows 上桌面版跳出 wsl.exe 安裝提示 | P3／Open |
| [#128769](https://github.com/NousResearch/hermes-agent/issues/128769) | `hermes pm doctor` 把已拒絕的預設項目誤報為過期 | P3／Open |
| [#128766](https://github.com/NousResearch/hermes-agent/issues/128766) | Slack 群組 DM 的 slash command 被當成獨立頻道執行 | P3／Open |
| [#128751](https://github.com/NousResearch/hermes-agent/issues/128751) | Kanban 釘選任務的 runtime 可能靜默退回其他模型 | P3／Open |

以上資訊來自 GitHub issue 列表與個別 issue 頁面，列表僅顯示最新約 12 則，未涵蓋全部。

---

*報告生成時間：2026-09-30 UTC*
