# Hermes-Agent 每日情報 — 2026-11-26

> 資料來源涵蓋截至 2026-11-26 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **過去 24 小時無新的穩定版，目前穩定版仍為 v0.23.4**：標示為 Latest，約含 229 個已合併 PR 的修補版，完整更新說明延後到 v0.24.0。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

2. **新增一個 canary 建置**：`canary.20261125T064800Z-v0.23.4`（2026-11-25 約 08:02 UTC，預先發行版），提供 macOS（Apple Silicon）與 Windows（x64、ARM64）桌面版安裝檔，安裝與對話冒煙測試皆通過，並新增大量外掛目錄項目、修正 cron 與 doctor 檢查等。（[releases feed](https://github.com/NousResearch/hermes-agent/releases.atom)）

3. **新回報以桌面版／安裝與訊息平台為主**：例如 Microsoft Store 桌面版的 venv 無法搬移、Telegram 與 QQBot 的穩定性問題。（見第 4 節，皆為使用者回報，尚待維護者確認）

---

## 2. LLM 串接實戰回報

過去 24 小時新增 issue 中，未查到針對 Claude／GPT／Gemini／本地模型的獨立串接實戰回報，不予編造。

---

## 3. 版本與 release 動態

| 版本 | 發布時間（UTC，頁面顯示） | 說明 |
|------|----------|------|
| canary.20261125T064800Z-v0.23.4 | 2026-11-25 約 08:02 | 預先發行版；桌面版冒煙測試通過 |
| **v0.23.4** | 2026-11-24 08:17 | 目前最新穩定版（Latest） |
| canary.20261124T064706Z-v0.23.3 | 2026-11-24 約 08:06 | 新增 neocore 記憶 provider 外掛，修正 cron、Linux presence、Desktop、profile 處理 |
| v0.23.3 | 2026-11-20 | 前一個穩定版，約 370 個已合併 PR |

來源：[Releases](https://github.com/NousResearch/hermes-agent/releases)、[releases feed](https://github.com/NousResearch/hermes-agent/releases.atom)。

---

## 4. 社群討論與已知問題

新增 issue（皆為使用者回報，是否成立尚待維護者確認）：

| Issue | 說明 |
|------|------|
| [#160272](https://github.com/NousResearch/hermes-agent/issues/160272) | Microsoft Store 桌面版附帶的 venv 無法搬移（Windows，P2 bug） |
| [#160270](https://github.com/NousResearch/hermes-agent/issues/160270) | 筆電闔蓋休眠後，QQBot adapter 永久放棄重連（macOS，P2 bug） |
| [#160276](https://github.com/NousResearch/hermes-agent/issues/160276) | handler 發生錯誤時，Telegram 緩衝訊息遺失 |
| [#160254](https://github.com/NousResearch/hermes-agent/issues/160254) | Telegram 核准卡片未處理 RetryAfter（P2 bug） |
| [#160277](https://github.com/NousResearch/hermes-agent/issues/160277) | 讀取壓縮過的 session 逐頁寫入約 170 MB SQLite 暫存檔 |
| [#160278](https://github.com/NousResearch/hermes-agent/issues/160278) | 行動版 app 將已結束的 session 顯示為執行中 |
| [#160264](https://github.com/NousResearch/hermes-agent/issues/160264) | Gateway 開機時，launch-authority 建置失敗會掩蓋真正錯誤（P2 bug） |
| [#160255](https://github.com/NousResearch/hermes-agent/issues/160255) | 健康安裝被誤報「install out of sync」（P2 bug） |
| [#160249](https://github.com/NousResearch/hermes-agent/issues/160249) | Nix 安裝每次 dispatch 都警告 out of sync |
| [#160217](https://github.com/NousResearch/hermes-agent/issues/160217) | channel_skill_bindings 因 session 先被觸碰而從不自動載入 |

功能提案：[#160266](https://github.com/NousResearch/hermes-agent/issues/160266)（Desktop 代理人建議回覆草稿，Tab 接受、Enter 送出）、[#160237](https://github.com/NousResearch/hermes-agent/issues/160237)（Desktop 為 Messaging Clear 控制項加上螢幕閱讀器名稱）。

來源：[Issues](https://github.com/NousResearch/hermes-agent/issues?q=is%3Aissue+sort%3Acreated-desc)。
