# Hermes-Agent 每日情報 — 2026-11-04

> 資料來源涵蓋截至 2026-11-04 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **v0.22.7 於 2026-11-03 發布，成為新的穩定版**：這是涵蓋 v0.22.6 以來約 174 個已合併 PR 的修補版本，範圍包含 GitHub release、Microsoft Store app、Docker 與 Hermes Cloud；官方表示完整的精選發行說明將隨 v0.23.0 提供。（[Releases feed](https://github.com/NousResearch/hermes-agent/releases.atom)）

2. **前一版 v0.22.6（2026-11-02）已確認內容**：涵蓋約 776 個已合併 PR、關閉 385 個 issue，提及 `hermes webapp` 指令、`/model reset` 與可選擇啟用的 prepared compaction。（[Releases feed](https://github.com/NousResearch/hermes-agent/releases.atom)）

3. **新增 issue 中有數則與模型／provider 相關**：包含切換到較小模型後 context window 上限未更新、MoA aggregator 在 openai-codex 上 prompt cache 命中率為 0%，皆為使用者回報，尚待維護者確認。（[Issues](https://github.com/NousResearch/hermes-agent/issues)）

---

## 2. LLM 串接實戰回報

過去 24 小時未查到 Claude／Gemini／本地模型的獨立實測回報，僅有以下 issue 回報（使用者回報，未證實）：

### GPT／OpenAI
- [#148675](https://github.com/NousResearch/hermes-agent/issues/148675)：MoA aggregator 走 openai-codex 時 prompt cache 命中率為 0%（輔助路由缺少 session headers）。

### 通用
- [#148714](https://github.com/NousResearch/hermes-agent/issues/148714)：切換到較小模型後，context window 上限仍維持舊值。

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 說明 |
|------|----------|------|
| **v0.22.7** | 2026-11-03 | 目前最新穩定版，約 174 個 PR 的修補版本（[release](https://github.com/NousResearch/hermes-agent/releases/tag/v0.22.7)） |
| v0.22.6 | 2026-11-02 | 前一穩定版，約 776 個 PR |

v0.22.7 另有 rc.1 標籤與 v0.22.6 的 canary 建置，僅有建置中繼資料，無發行說明。完整精選說明預告隨 v0.23.0 提供。來源：[Releases feed](https://github.com/NousResearch/hermes-agent/releases.atom)。

### 公司動態（背景）
據報導 Nous Research 於 2026-10-07 完成 9,000 萬美元募資、估值 15 億美元（[The Next Web](https://thenextweb.com/news/nous-research-90m-1-5bn-hermes-agent-wsj)）。此為既有消息，非過去 24 小時新增。

---

## 4. 社群討論與已知問題

2026-11-03／11-04 新增 issue（部分，皆為使用者回報，是否成立尚待維護者確認）：

| Issue | 說明 |
|------|------|
| [#148710](https://github.com/NousResearch/hermes-agent/issues/148710) | 更新流程：autostash 還原發生衝突時，已合併的檔案全數被丟棄 |
| [#148672](https://github.com/NousResearch/hermes-agent/issues/148672) | Install.ps1 被 Kaspersky 判定為木馬（疑似誤報，未證實） |
| [#148689](https://github.com/NousResearch/hermes-agent/issues/148689) | Windows：job object 禁止 breakaway 時 `hermes acp` 無法啟動 session |
| [#148691](https://github.com/NousResearch/hermes-agent/issues/148691) | Nix 安裝：`hermes desktop` 找不到 Desktop GUI 原始碼 |
| [#148682](https://github.com/NousResearch/hermes-agent/issues/148682)／[#148686](https://github.com/NousResearch/hermes-agent/issues/148686) | 桌面版丟失圖片附件；webapp 斷線後前景對話紀錄缺漏訊息 |
| [#148698](https://github.com/NousResearch/hermes-agent/issues/148698) | Feishu 在 5 分鐘去重 TTL 後重送同一 message_id，會中斷進行中的回合 |
| [#148703](https://github.com/NousResearch/hermes-agent/issues/148703)／[#148701](https://github.com/NousResearch/hermes-agent/issues/148701) | Kanban：未知 provider 可能退回 fallback 而非拒絕；功能請求暫停執行中的 worker |
| [#148663](https://github.com/NousResearch/hermes-agent/issues/148663)／[#148657](https://github.com/NousResearch/hermes-agent/issues/148657) | Docker backend：MCP 結果路徑與 MEDIA 檔案權限問題 |

來源：[Issues](https://github.com/NousResearch/hermes-agent/issues)。
