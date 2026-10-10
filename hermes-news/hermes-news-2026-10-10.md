# Hermes-Agent 每日情報 — 2026-10-10

> 資料來源涵蓋截至 2026-10-10 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **過去 24 小時無重大更新，目前穩定版仍為 v0.21.6（2026-10-08 發布）**：Releases 頁面最新一筆仍是 v0.21.6，尚無新版本；完整整理版說明預告隨 v0.22.0 提供。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

2. **新 issue 以 Desktop、瀏覽器工具與 gateway 的 bug 為主**：例如 `hermes update` 會將 Desktop 瀏覽器降級至 Chromium 145、Weixin/iLink 在回覆視窗過期後靜默丟棄訊息。（[Issues](https://github.com/NousResearch/hermes-agent/issues)）

---

## 2. LLM 串接實戰回報

- Claude／GPT／Gemini／本地模型：過去 24 小時未查到新的串接回報。模型目錄的最新異動仍是 v0.21.6（GPT-6.1 Sol、Claude Sonnet 5.5、Claude Haiku 5.5）。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 說明 |
|------|----------|------|
| **v0.21.6** | 2026-10-08 | **目前最新穩定版**；約 2,100 PR；dashboard 驗證、git filter、email gateway 安全修補 |
| v0.21.5（v2026.9.24） | 2026-09-24 | 約 460 PR；Desktop plugin SDK、Connectors 頁面 |

來源：[Releases](https://github.com/NousResearch/hermes-agent/releases)。

---

## 4. 社群討論與已知問題

優先度標籤取自 GitHub issue 頁面（部分標籤邊界由頁面文字推斷）。

| Issue | 說明 | 優先度 |
|------|------|------|
| [#135926](https://github.com/NousResearch/hermes-agent/issues/135926) | `image_generate` 丟失 seed／steps／guidance 設定，結果無法重現 | P2 |
| [#135921](https://github.com/NousResearch/hermes-agent/issues/135921) | Weixin/iLink 在回覆視窗過期後靜默丟棄外送訊息 | P2 |
| [#135910](https://github.com/NousResearch/hermes-agent/issues/135910) | `pm repair` 在不分大小寫的檔案系統上因 copytree Errno 17 失敗 | P2 |
| [#135905](https://github.com/NousResearch/hermes-agent/issues/135905) | 核准按鈕會處理最舊的待核准項目，而非被點擊的那一個（duplicate） | P2 |
| [#135904](https://github.com/NousResearch/hermes-agent/issues/135904) | `use_real_profile` 瀏覽器路徑在 AppArmor 主機上略過自動 `--no-sandbox` 處理 | P2 |
| [#135932](https://github.com/NousResearch/hermes-agent/issues/135932) | `hermes update` 將 Desktop 瀏覽器降級至 Chromium 145，破壞持久化 profile | 未標示 |
| [#135928](https://github.com/NousResearch/hermes-agent/issues/135928) | `hermes doctor` 的 npm audit 檢查對象為根 workspace 而非 agent-browser | P3 |

來源：[Issues](https://github.com/NousResearch/hermes-agent/issues)。
