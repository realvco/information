# Hermes-Agent 每日情報 — 2026-11-10

> 資料來源涵蓋截至 2026-11-10 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **發布修補版 v0.23.1，最新穩定版為 v0.23.1**：將 plugin 目錄大小上限提高到 16 MiB，讓 `hermes plugins install` 能再次使用線上目錄。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

2. **新增 issue 集中在 Telegram／gateway、Windows gateway 與 Desktop**：皆為使用者回報，尚待維護者確認。（[Issues](https://github.com/NousResearch/hermes-agent/issues?q=is%3Aissue+sort%3Acreated-desc)）

---

## 2. LLM 串接實戰回報

過去 24 小時未查到 Gemini／本地模型的獨立實測回報，不予編造。以下為使用者回報的 issue（未經確認）：

**Claude**：有人回報 Claude DirectSDK 重試時，請求中帶有 `extra_headers`（[#152313](https://github.com/NousResearch/hermes-agent/issues/152313)）。

**GPT／OpenAI 相容端點**：
- 在 Windows gateway 上，OpenAI 相容串流卡住時，stale-stream watchdog 延遲約 19,076 秒才觸發（[#152337](https://github.com/NousResearch/hermes-agent/issues/152337)）。
- Desktop GPT-Live：回覆在串流尚未結束時就被附加到語音，連工具呼叫前的旁白也包含在內（[#152345](https://github.com/NousResearch/hermes-agent/issues/152345)）。

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 說明 |
|------|----------|------|
| **v0.23.1** | 2026-11-10 | 目前最新穩定版：plugin 目錄大小上限提高到 16 MiB（[Releases](https://github.com/NousResearch/hermes-agent/releases)） |
| v0.23.0「The Hearth Release」 | 2026-11-05 | 瀏覽器版 Desktop Webapp、單一 gateway 掌管所有 session、終端機內嵌圖片與 `/rewind`（[release](https://github.com/NousResearch/hermes-agent/releases/tag/v0.23.0)） |
| v0.22.7 | 2026-11-03 | 修補版，約 174 個 PR，含 Group Chat lead routing、cron 可發訊到聊天室、固定 Kanban 看板（[Releases](https://github.com/NousResearch/hermes-agent/releases)） |

---

## 4. 社群討論與已知問題

新增 issue（部分，皆為使用者回報，是否成立尚待維護者確認）：

| Issue | 說明 |
|------|------|
| [#152380](https://github.com/NousResearch/hermes-agent/issues/152380) | 追蹤：Telegram／gateway 錯誤清查的待審 PR 批次 |
| [#152364](https://github.com/NousResearch/hermes-agent/issues/152364) | Windows gateway：多個 Kanban worker 並行時 event loop 停滯、worker 遺失原因誤導 |
| [#152330](https://github.com/NousResearch/hermes-agent/issues/152330) | Telegram 私人 topic：回覆失敗或 API 回報成功但 Android 看不到 |
| [#152365](https://github.com/NousResearch/hermes-agent/issues/152365) | 微信語音訊息導致 session_admissions 卡在 'started' |
| [#152347](https://github.com/NousResearch/hermes-agent/issues/152347) | `UV_INDEX_URL` 被默默移除，造成鏡像站無法使用 |
| [#152344](https://github.com/NousResearch/hermes-agent/issues/152344) | Desktop：遠端 gateway 的 Bot Chat 中 `/new` 會在本機開新 session |
| [#152328](https://github.com/NousResearch/hermes-agent/issues/152328) | ACP gateway viewer：自動化回合被當成回合外更新，T3 Code 中回覆遺失 |
| [#152373](https://github.com/NousResearch/hermes-agent/issues/152373) | plugins：記憶提供者缺少公開的 `call_mcp` |
| [#152315](https://github.com/NousResearch/hermes-agent/issues/152315) | 每次載入 MCP 設定都記錄「Portable MCP server conflicts with native config」警告 |

來源：[Issues](https://github.com/NousResearch/hermes-agent/issues?q=is%3Aissue+sort%3Acreated-desc)。
