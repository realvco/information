# Hermes-Agent 每日情報 — 2026-10-12

> 資料來源涵蓋截至 2026-10-12 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **過去 24 小時無重大更新，目前穩定版仍為 v0.21.6（2026-10-08 發布）**：Releases 頁面最新一筆仍是 v0.21.6，尚無新版本；完整整理版說明仍預告隨 v0.22.0 提供。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

2. **新 issue 以 Skills 內容缺陷、gateway 狀態與遠端沙箱媒體路徑為主**：今日新增的 issue 多數是 Skills 行為問題（P3），另有 gateway 重啟後狀態檔沿用舊 hermes_home（#136909）與遠端終端沙箱下 API server 回傳原始 `MEDIA:` 路徑（#136892），皆為 P2。（[Issues](https://github.com/NousResearch/hermes-agent/issues)）

---

## 2. LLM 串接實戰回報

- Claude／GPT／Gemini／本地模型：過去 24 小時未查到新的串接回報。模型目錄的最新異動仍是 v0.21.6（GPT-6.1 Sol、Claude Sonnet 5.5、Claude Haiku 5.5，以及 `hermes model` 支援本地模型）。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 說明 |
|------|----------|------|
| **v0.21.6** | 2026-10-08 | **目前最新穩定版**；約 2,100 PR；首次由新的穩定版發布流程產出；dashboard 驗證強化、git filter 封鎖、email gateway 白名單修補 |
| v0.21.5（v2026.9.24） | 2026-09-24 | 約 460 PR；Desktop plugin SDK、Connectors 頁面 |
| v0.21.4（v2026.9.21） | 2026-09-21 | 約 1,800 PR；gateway 單例鎖、`--format stream-json`、`skills.auto_load` |

來源：[Releases](https://github.com/NousResearch/hermes-agent/releases)。

---

## 4. 社群討論與已知問題

優先度標籤取自 GitHub issue 頁面。

| Issue | 說明 | 優先度 |
|------|------|------|
| [#136909](https://github.com/NousResearch/hermes-agent/issues/136909) | `gateway_state.json` 在重啟後仍保留前一個寫入者的 hermes_home | P2 |
| [#136892](https://github.com/NousResearch/hermes-agent/issues/136892) | 遠端終端沙箱（ssh／modal／daytona）下，API server 對圖片回傳原始 `MEDIA:` 路徑 | P2 |
| [#136914](https://github.com/NousResearch/hermes-agent/issues/136914) | Desktop：有 3 列缺少顯示索引時，對話視窗中途跳到上半部 | 未標示 |
| [#136902](https://github.com/NousResearch/hermes-agent/issues/136902) | 功能需求：tui_gateway 為同步對話提供每則訊息的來源前端資訊 | P3 |
| [#136908](https://github.com/NousResearch/hermes-agent/issues/136908) | Skills：shop 因選用的登入提示而阻擋未登入的目錄搜尋 | P3 |
| [#136896](https://github.com/NousResearch/hermes-agent/issues/136896) | Skills：simplify-code 把一般的 review 請求視為允許修改檔案 | P3 |

來源：[Issues](https://github.com/NousResearch/hermes-agent/issues)。
