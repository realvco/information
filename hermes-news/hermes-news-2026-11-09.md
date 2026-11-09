# Hermes-Agent 每日情報 — 2026-11-09

> 資料來源涵蓋截至 2026-11-09 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **過去 24 小時無重大更新，最新穩定版仍為 v0.23.0「The Hearth Release」**：該版於 2026-11-05 發布，之後未見新的 release。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

2. **新增 issue 集中在 Windows 安裝（非 ASCII 使用者路徑）、cron 與 Desktop 體驗**：皆為使用者回報，尚待維護者確認。（[Issues](https://github.com/NousResearch/hermes-agent/issues?q=is%3Aissue+sort%3Acreated-desc)）

---

## 2. LLM 串接實戰回報

過去 24 小時未查到 Claude／GPT／本地模型的獨立實測回報，不予編造。

**Gemini**：有使用者回報，當工具有名為 `in` 的參數時，Gemini 會在每條路徑上回傳 `MALFORMED_FUNCTION_CALL`，導致無法呼叫該工具（[#151609](https://github.com/NousResearch/hermes-agent/issues/151609)，使用者回報，未經確認）。

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 說明 |
|------|----------|------|
| **v0.23.0「The Hearth Release」** | 2026-11-05 | 目前最新穩定版：由自己的 gateway 提供瀏覽器版 Desktop 工作區（預設關閉）、單一 gateway 掌管 session、終端機內嵌圖片與 `/rewind`、付費 MCP 工具價格標示與花費上限；plugin 目錄超過 1,050 筆，內建技能由 58 減為 44（[release](https://github.com/NousResearch/hermes-agent/releases/tag/v0.23.0)） |
| v0.22.7 | 2026-11-03 | 修補版，彙整約 174 個 PR（[Releases](https://github.com/NousResearch/hermes-agent/releases)） |
| v0.22.6 | 2026-11-02 | 修補版，彙整約 776 個 PR（[Releases](https://github.com/NousResearch/hermes-agent/releases)） |

---

## 4. 社群討論與已知問題

新增 issue（部分，皆為使用者回報，是否成立尚待維護者確認）：

| Issue | 說明 |
|------|------|
| [#151600](https://github.com/NousResearch/hermes-agent/issues/151600) | Windows 安裝程式在使用者資料夾路徑含非 ASCII 字元時，git 解壓縮失敗 |
| [#151581](https://github.com/NousResearch/hermes-agent/issues/151581) | Windows 安裝在系統前置需求階段，因非 ASCII 路徑失敗 |
| [#151586](https://github.com/NousResearch/hermes-agent/issues/151586) | 暫停的 cron 工作在 firer 記錄自身終端結果後不會恢復 |
| [#151585](https://github.com/NousResearch/hermes-agent/issues/151585) | cron 的 canonical-dispatch 路徑沒有「模型無法連線」的重試行為 |
| [#151582](https://github.com/NousResearch/hermes-agent/issues/151582) | session 刪除與重新命名因計數器不同步而持續 409 revision 衝突 |
| [#151584](https://github.com/NousResearch/hermes-agent/issues/151584) | 傳統 CLI 的 session 列表與 resume 指令看不到 Desktop（gui）session |
| [#151606](https://github.com/NousResearch/hermes-agent/issues/151606) | 多工 gateway：重新連線 profile 的 Telegram bot 經由預設 bot 發訊 |
| [#151608](https://github.com/NousResearch/hermes-agent/issues/151608) | Kanban：不可重試的失敗卻顯示「retries exhausted」且未列原因 |
| [#151560](https://github.com/NousResearch/hermes-agent/issues/151560) | 功能請求：ACP 提供 `_session/steering`，讓編輯器能引導執行中的回合 |
| [#151613](https://github.com/NousResearch/hermes-agent/issues/151613) | 功能請求：Desktop 寬視窗下，滑鼠移到邊緣即展開收合的左側欄 |

來源：[Issues](https://github.com/NousResearch/hermes-agent/issues?q=is%3Aissue+sort%3Acreated-desc)。
