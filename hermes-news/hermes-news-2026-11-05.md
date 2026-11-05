# Hermes-Agent 每日情報 — 2026-11-05

> 資料來源涵蓋截至 2026-11-05 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **過去 24 小時無新的正式版本，穩定版仍為 v0.22.7**：v0.22.7（2026-11-03 發布）為涵蓋約 174 個已合併 PR 的修補版本；官方表示完整的精選發行說明將隨 v0.23.0 提供。（[Releases feed](https://github.com/NousResearch/hermes-agent/releases.atom)）

2. **僅有自動化 canary 建置**：`v0.22.7+canary.20261104T064347Z` 於 2026-11-04 產生，內容僅含建置名稱，無發行說明。（[Releases feed](https://github.com/NousResearch/hermes-agent/releases.atom)）

3. **新增 issue 以 TUI、Gateway、Hosted rooms 與 Windows 更新問題為主**，皆為使用者回報，尚待維護者確認。（[Issues](https://github.com/NousResearch/hermes-agent/issues)）

---

## 2. LLM 串接實戰回報

過去 24 小時未查到 Claude／GPT／Gemini／本地模型的獨立實測回報，不予編造。

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 說明 |
|------|----------|------|
| **v0.22.7** | 2026-11-03 | 目前最新穩定版，約 174 個 PR 的修補版本，可透過 `hermes update`、Microsoft Store 或 Docker `:stable` 取得（[release](https://github.com/NousResearch/hermes-agent/releases/tag/v0.22.7)） |
| v0.22.7+canary | 2026-11-04 | 自動 canary 建置，無說明 |
| v0.22.6 | 2026-11-02 | 前一穩定版，約 776 個 PR |

來源：[Releases feed](https://github.com/NousResearch/hermes-agent/releases.atom)。

---

## 4. 社群討論與已知問題

2026-11-05 新增 issue（部分，皆為使用者回報，是否成立尚待維護者確認）：

| Issue | 說明 |
|------|------|
| [#149217](https://github.com/NousResearch/hermes-agent/issues/149217) | Windows：更新鎖與 `.deleteme` 殘留，需要安全的復原方式 |
| [#149201](https://github.com/NousResearch/hermes-agent/issues/149201) | `skills install`：因 URL 安全檢查（fake-IP DNS）被擋的下載，錯誤訊息誤導 |
| [#149169](https://github.com/NousResearch/hermes-agent/issues/149169) | Gateway：啟動後 `/loop`、`/heartbeat` 喚醒不會觸發 |
| [#149195](https://github.com/NousResearch/hermes-agent/issues/149195)／[#149196](https://github.com/NousResearch/hermes-agent/issues/149196) | Hosted rooms：房間互相無限重排喚醒；每 5 秒輪詢為每個終態任務開一條 DB 連線 |
| [#149181](https://github.com/NousResearch/hermes-agent/issues/149181)／[#149176](https://github.com/NousResearch/hermes-agent/issues/149176)／[#149168](https://github.com/NousResearch/hermes-agent/issues/149168) | TUI：steer 模式仍顯示排隊訊息；`/insights` 顯示原始 markdown 符號；工具面板缺少 token 統計 |
| [#149182](https://github.com/NousResearch/hermes-agent/issues/149182) | WhatsApp：群組中依使用者分 session 時，他人的投票被忽略而逾時 |
| [#149167](https://github.com/NousResearch/hermes-agent/issues/149167) | `uv lock` 在啟用可建置 plugin 時失敗（workspace 成員同名） |
| [#149219](https://github.com/NousResearch/hermes-agent/issues/149219)／[#149215](https://github.com/NousResearch/hermes-agent/issues/149215) | 功能請求：建立 skill 時記錄來由；以 board 作為 profile 之上的組合層 |

來源：[Issues](https://github.com/NousResearch/hermes-agent/issues)。
