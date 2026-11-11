# Hermes-Agent 每日情報 — 2026-11-11

> 資料來源涵蓋截至 2026-11-11 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **過去 24 小時無新版本發布，最新穩定版仍為 v0.23.1**：該版為修補版，將 plugin 目錄大小上限由 2 MiB 提高到 16 MiB，修正安裝時默默退回舊版內建目錄的問題；完整整理過的 release notes 預告將隨 v0.24.0 發布。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

2. **新增 issue 集中在 Windows gateway 與 WeCom（企業微信）**：皆為使用者回報，尚待維護者確認。（[Issues](https://github.com/NousResearch/hermes-agent/issues?q=is%3Aissue+sort%3Acreated-desc)）

---

## 2. LLM 串接實戰回報

過去 24 小時未查到 Claude／GPT／Gemini／本地模型的獨立實測回報，不予編造。

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 說明 |
|------|----------|------|
| **v0.23.1** | 2026-11-10 | 目前最新穩定版：plugin 目錄大小上限提高到 16 MiB；另有未詳列的變更（TUI／CLI Esc 回溯、`hermes gateway attach`、plugin API 新功能）（[Releases](https://github.com/NousResearch/hermes-agent/releases)） |
| v0.23.0「The Hearth Release」 | 2026-11-05 | 瀏覽器版 Desktop Webapp、單一 gateway 掌管所有 session、`/rewind`；內建 skills 由 58 減為 44，瀏覽器自動化僅剩 `browser_exec`（[release](https://github.com/NousResearch/hermes-agent/releases/tag/v0.23.0)） |
| v0.22.7 | 2026-11-03 | 修補版，約 174 個 PR（[Releases](https://github.com/NousResearch/hermes-agent/releases)） |

---

## 4. 社群討論與已知問題

新增 issue（部分，皆為使用者回報，是否成立尚待維護者確認）：

| Issue | 說明 |
|------|------|
| [#153063](https://github.com/NousResearch/hermes-agent/issues/153063) | Windows：同時跑六個 profile 時，同步 I/O 阻塞 gateway event loop，程序反覆退出重啟 |
| [#153046](https://github.com/NousResearch/hermes-agent/issues/153046) | Windows：查詢程序啟動時間的 psutil 呼叫可能讓 gateway 凍結數十秒 |
| [#153043](https://github.com/NousResearch/hermes-agent/issues/153043) | Windows：cron 腳本遺失虛擬環境套件 |
| [#153044](https://github.com/NousResearch/hermes-agent/issues/153044) | WeCom：群組白名單比對錯誤，合法群組訊息被拒 |
| [#153045](https://github.com/NousResearch/hermes-agent/issues/153045) | WeCom bot 模式傳送時媒體附件被丟棄 |
| [#153048](https://github.com/NousResearch/hermes-agent/issues/153048) | 零位元組的狀態資料庫通過檢查，導致 bot 無法送達訊息 |
| [#153079](https://github.com/NousResearch/hermes-agent/issues/153079) | Cron 執行紀錄在寫入長度標記後才被遮蔽，查詢不到內容 |
| [#153047](https://github.com/NousResearch/hermes-agent/issues/153047) | Desktop 開機時誤報 session 恢復失敗 |
| [#153073](https://github.com/NousResearch/hermes-agent/issues/153073) | Desktop／網頁版：開啟模型選單的點擊同時選取了下方項目 |

來源：[Issues](https://github.com/NousResearch/hermes-agent/issues?q=is%3Aissue+sort%3Acreated-desc)。
