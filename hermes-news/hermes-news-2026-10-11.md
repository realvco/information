# Hermes-Agent 每日情報 — 2026-10-11

> 資料來源涵蓋截至 2026-10-11 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **過去 24 小時無重大更新，目前穩定版仍為 v0.21.6（2026-10-08 發布）**：Releases 頁面最新一筆仍是 v0.21.6，尚無新版本；完整整理版說明仍預告隨 v0.22.0 提供。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

2. **新 issue 以更新流程、效能與 Windows 平台問題為主**：有回報指出升級至 0.21.6 後開啟新舊對話變慢（#136383）、`hermes update` 在大量修改過的工作樹上會暫存本地修改卻未還原（#136394）。（[Issues](https://github.com/NousResearch/hermes-agent/issues)）

---

## 2. LLM 串接實戰回報

- Claude／GPT／Gemini／本地模型：過去 24 小時未查到新的串接回報。模型目錄的最新異動仍是 v0.21.6（GPT-6.1 Sol、Claude Sonnet 5.5、Claude Haiku 5.5）。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 說明 |
|------|----------|------|
| **v0.21.6** | 2026-10-08 | **目前最新穩定版**；約 2,100 PR；dashboard 驗證強化、git filter 封鎖、email gateway 白名單修補 |
| v0.21.5（v2026.9.24） | 2026-09-24 | 約 460 PR；Desktop plugin SDK、Connectors 頁面 |
| v0.21.4（v2026.9.21） | 2026-09-21 | 約 1,800 PR；profile 隔離、cron、kanban 等修正 |

來源：[Releases](https://github.com/NousResearch/hermes-agent/releases)。

---

## 4. 社群討論與已知問題

優先度標籤取自 GitHub issue 頁面。

| Issue | 說明 | 優先度 |
|------|------|------|
| [#136394](https://github.com/NousResearch/hermes-agent/issues/136394) | `hermes update` 在大量修改的工作樹上暫存本地修改卻未還原，HEAD 不前進 | P2 |
| [#136390](https://github.com/NousResearch/hermes-agent/issues/136390) | Windows：登入自動啟動的排程工作在 pm／Python 3.14 執行環境下靜默失敗 | P2 |
| [#136388](https://github.com/NousResearch/hermes-agent/issues/136388) | `redact_sensitive_text` 仍會破壞 `vision_analyze` 結果中的 base64 圖片（JWT 規則誤判） | P2 |
| [#136383](https://github.com/NousResearch/hermes-agent/issues/136383) | 升級至 0.21.6 後，新開或進入舊對話變慢（待重現） | P2 |
| [#136403](https://github.com/NousResearch/hermes-agent/issues/136403) | `execute_code` 沙箱沒有記憶體上限，單一失控 cell 耗用 180 GB | 未標示 |
| [#136392](https://github.com/NousResearch/hermes-agent/issues/136392) | Desktop：連線範圍的 profile 改名被「is being deleted」自我阻擋 | P3 |

來源：[Issues](https://github.com/NousResearch/hermes-agent/issues)。
