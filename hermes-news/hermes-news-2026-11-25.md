# Hermes-Agent 每日情報 — 2026-11-25

> 資料來源涵蓋截至 2026-11-25 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **穩定版更新為 v0.23.4**：2026-11-24 08:17 UTC 左右發布，為累計約 229 個已合併 PR 的修補版，涵蓋 CLI、Microsoft Store 應用程式、Docker 與 Hermes Cloud；完整整理過的更新說明延後到 v0.24.0。（[releases feed](https://github.com/NousResearch/hermes-agent/releases.atom)）

2. **v0.23.4 發布前先經過 rc 流程**：feed 中可見 `abandoned-rc.1-v0.23.4`（放棄）與 `rc.2-v0.23.4`（autopublish 為 false）的紀錄。（[releases feed](https://github.com/NousResearch/hermes-agent/releases.atom)）

3. **持續有自動 canary 建置**：`canary.20261124T064706Z-v0.23.3` 含桌面版下載與原生冒煙測試通過，並有大量外掛目錄新增與錯誤修正。（[releases feed](https://github.com/NousResearch/hermes-agent/releases.atom)）

4. **新回報：單一模型 429 會封鎖整個 provider 憑證，連其他額度的 fallback 模型也受影響**。（[#159870](https://github.com/NousResearch/hermes-agent/issues/159870)，使用者回報，尚待維護者確認）

---

## 2. LLM 串接實戰回報

- **跨 provider／fallback**：[#159870](https://github.com/NousResearch/hermes-agent/issues/159870) 回報某模型遇到 429 後，同一 provider 憑證下其他模型的 fallback 也被擋下（未證實）。
- **Claude／GPT／Gemini／本地模型**：過去 24 小時新增 issue 中，未查到針對這些模型的獨立實戰回報，不予編造。

---

## 3. 版本與 release 動態

| 版本 | 發布時間（UTC） | 說明 |
|------|----------|------|
| **v0.23.4** | 2026-11-24 08:17 | 目前最新穩定版；約 229 個已合併 PR 的修補版 |
| rc.2-v0.23.4 | 2026-11-24 07:06 | 第二次 release candidate 紀錄，autopublish 為 false |
| abandoned-rc.1-v0.23.4 | 2026-11-24 06:45 | 第一次 rc 嘗試，已標記放棄 |
| canary.20261124T064706Z-v0.23.3 | 2026-11-24 | 自動 canary；桌面版冒煙測試通過 |
| v0.23.3 | 2026-11-20 | 前一個穩定版 |

來源：[releases feed](https://github.com/NousResearch/hermes-agent/releases.atom)。

---

## 4. 社群討論與已知問題

新增 issue（皆為使用者回報，是否成立尚待維護者確認）：

| Issue | 說明 |
|------|------|
| [#159879](https://github.com/NousResearch/hermes-agent/issues/159879) | macOS computer-use daemon 啟動失敗被快取，殘留 socket 阻擋復原，需手動重啟 |
| [#159878](https://github.com/NousResearch/hermes-agent/issues/159878) | computer-use 的 status／doctor／permissions 寫死應用程式路徑，與安裝在 ~/.hermes/tools 不符 |
| [#159872](https://github.com/NousResearch/hermes-agent/issues/159872) | Matrix 主動串接訊息的回覆參照指向串首 |
| [#159870](https://github.com/NousResearch/hermes-agent/issues/159870) | 單一模型 429 封鎖整個 provider 憑證 |
| [#159863](https://github.com/NousResearch/hermes-agent/issues/159863) | SQLite BLOB 欄位回傳 bytes，導致 system prompt 雜湊與訊息解碼當機 |
| [#159862](https://github.com/NousResearch/hermes-agent/issues/159862) | 舊版 BLOB 資料列使頻道目錄寫入失敗；非 dict 的 MCP 項目使 `hermes mcp list` 當機 |
| [#159860](https://github.com/NousResearch/hermes-agent/issues/159860) | Slack 連結預覽與訊息認領競態，產生重複 message id 錯誤 |
| [#159855](https://github.com/NousResearch/hermes-agent/issues/159855) | Desktop：全新對話中的斜線指令被目的地防護拒絕時，草稿遺失 |

功能提案：[#159856](https://github.com/NousResearch/hermes-agent/issues/159856)（外掛 hook 回報誰啟動該回合）、[#159853](https://github.com/NousResearch/hermes-agent/issues/159853)（Desktop 新增建立 Projects 按鈕）、[#159851](https://github.com/NousResearch/hermes-agent/issues/159851)（瀏覽器自動化的可見模式）。

來源：[Issues](https://github.com/NousResearch/hermes-agent/issues?q=is%3Aissue+sort%3Acreated-desc)。
