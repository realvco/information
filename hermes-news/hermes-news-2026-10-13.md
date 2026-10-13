# Hermes-Agent 每日情報 — 2026-10-13

> 資料來源涵蓋截至 2026-10-13 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **新版 v0.21.7 發布，目前穩定版為 v0.21.7（2026-10-11 發布，GitHub 頁面顯示 10/12）**：此版起，原始碼安裝的更新改為追蹤已標記的 release，而非 `main` 每個 commit；若要追蹤每個 commit，可執行 `hermes update --set-channel main`。桌面版也新增更新通道切換。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

2. **v0.21.7 修正 `--resume` 後簽章推理（signed reasoning）延續問題**：`hermes chat --resume` 與 gateway 下一輪對話都會帶入簽章推理；另外在部分防火牆後的更新較穩定，且穩定版檢查在有設定 GitHub token 時會使用該 token。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

3. **今日新增 issue 出現 1 則 P1：Telegram 在 `/stop` 確認期間再傳訊息會重播 `/stop` 並使 session 卡在忙碌狀態（#137545）**；另有多則 P2，包含更新程式搬移使用者模型檔（#137551）。（[Issues](https://github.com/NousResearch/hermes-agent/issues)）

---

## 2. LLM 串接實戰回報

- Claude／GPT／Gemini／本地模型：過去 24 小時未查到新的串接回報。v0.21.7 唯一與模型相關的項目是簽章推理在 `--resume` 後延續。模型目錄最近的異動仍見於 v0.21.5／v0.21.6 說明。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 說明 |
|------|----------|------|
| **v0.21.7** | 2026-10-11 | **目前最新穩定版**；約 204 個合併 PR 的穩定版彙整，涵蓋桌面版、Windows Store 封裝、安裝程式、Docker 與 Hermes Cloud；更新改追蹤 tag；桌面版更新通道切換 |
| v0.21.6 | 2026-10-08 | 首次由新穩定版發布流程產出；dashboard 驗證強化、git filter 封鎖、email gateway 白名單修補 |
| v0.21.5（v2026.9.24） | 2026-09-24 | 約 460 PR；Desktop plugin SDK、Connectors 頁面 |

完整整理版說明仍預告隨 v0.22.0 提供（v0.22.0 發布日期未證實）。來源：[Releases](https://github.com/NousResearch/hermes-agent/releases)。

---

## 4. 社群討論與已知問題

優先度標籤取自 GitHub issue 頁面。

| Issue | 說明 | 優先度 |
|------|------|------|
| [#137545](https://github.com/NousResearch/hermes-agent/issues/137545) | Telegram：`/stop` 確認期間傳訊息會重播 `/stop`，session 卡在忙碌 | P1 |
| [#137555](https://github.com/NousResearch/hermes-agent/issues/137555) | 工作執行緒的 import 檢查卡在 stdin，造成 gateway session lease 死鎖 | P2 |
| [#137552](https://github.com/NousResearch/hermes-agent/issues/137552) | gateway 為重啟而排空時，Kanban 通知與喚醒訊息遺失 | P2 |
| [#137551](https://github.com/NousResearch/hermes-agent/issues/137551) | 更新程式把使用者模型檔從設定路徑搬到安裝目錄 | P2 |
| [#137546](https://github.com/NousResearch/hermes-agent/issues/137546) | `/goal` 裁判的拒絕結果沒有傳達給 agent | P2 |
| [#137540](https://github.com/NousResearch/hermes-agent/issues/137540) | 桌面版逐字稿把壓縮摘要顯示為 assistant 訊息，且每次壓縮都重存歷史 | P2 |
| [#137519](https://github.com/NousResearch/hermes-agent/issues/137519) | Desktop／TUI 的 `/loop` 提示若以「/」開頭會被靜默丟棄 | P2 |
| [#137541](https://github.com/NousResearch/hermes-agent/issues/137541) | 設計討論：對 plugin 的 Python 相依套件強制 3 天 release age | P3 |

來源：[Issues](https://github.com/NousResearch/hermes-agent/issues)。
