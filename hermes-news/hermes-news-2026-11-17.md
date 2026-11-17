# Hermes-Agent 每日情報 — 2026-11-17

> 資料來源涵蓋截至 2026-11-17 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **v0.23.2 穩定版發布，為目前最新穩定版**：2026-11-16 晚間（UTC）發布，彙整自 v0.23.1 以來約 1,989 個已合併 PR；完整的整理版說明將延到 v0.24.0。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

2. **v0.23.2 修正多項 Desktop 與 Telegram 問題**：使用裸 `custom` provider 的 Desktop 對話可正常送出、Desktop／TUI 在 todo 或 memory 呼叫後回覆能正確收尾、Telegram 在串流中途當機後不再重複顯示「Recovered reply」、CLI 結束時不再遺留 `git` 行程。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

3. **發布過程可見反覆重試**：同日出現 rc.11、rc.12、rc.13，其中 rc.11、rc.12 被標為 abandoned，最終才定版 v0.23.2。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

---

## 2. LLM 串接實戰回報

過去 24 小時未查到 Claude／GPT／Gemini／本地模型的獨立實測回報，不予編造。僅有 [#156750](https://github.com/NousResearch/hermes-agent/issues/156750) 與 prompt-cache 相關：回報同一個 desktop 對話的兩種 `source` 標籤被雜湊成不同的 cache scope（使用者回報，未證實）。

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 說明 |
|------|----------|------|
| **v0.23.2** | 2026-11-16 | 目前最新穩定版：約 1,989 個 PR 的彙整修補版（[Releases](https://github.com/NousResearch/hermes-agent/releases)） |
| rc.13-v0.23.2 | 2026-11-16 | 發布候選版（rc 命名），無說明內容 |
| rc.12 / rc.11 | 2026-11-16 | 發布候選版；其中 rc.11、rc.12 另有 abandoned 標記 |
| v0.23.1 | 2026-11-10 | 前一穩定版：plugin 目錄上限提高到 16 MiB |

> 註：本日 release 資訊取自官方 [releases feed](https://github.com/NousResearch/hermes-agent/releases.atom)，feed 未明確標示 pre-release 欄位，rc 的判斷依命名而來。

---

## 4. 社群討論與已知問題

新增 issue（皆為使用者回報或提案，是否成立尚待維護者確認）：

| Issue | 說明 |
|------|------|
| [#156768](https://github.com/NousResearch/hermes-agent/issues/156768) | kanban dispatch 在 macOS 當機：fd 整數洩漏進 `_spawn_macos_worker` |
| [#156767](https://github.com/NousResearch/hermes-agent/issues/156767) | edge-tts extra 的過期版本鎖定（7.2.7）使每次 `uv sync` 降級原本可用的安裝 |
| [#156760](https://github.com/NousResearch/hermes-agent/issues/156760) | 背景行程喚醒時，輸出為超過 2000 字元的單行會顯示空白內容 |
| [#156757](https://github.com/NousResearch/hermes-agent/issues/156757) | Desktop：plugin 頁面在重新載入或以網址直接開啟時遺失 |
| [#156745](https://github.com/NousResearch/hermes-agent/issues/156745) | API server 遇到含孤立 surrogate 的 chat-completions 請求回 500 |
| [#156739](https://github.com/NousResearch/hermes-agent/issues/156739) | Desktop 把內部 context 壓縮的交接摘要顯示成一般使用者訊息 |
| [#156748](https://github.com/NousResearch/hermes-agent/issues/156748) | 具名 browser_exec session 會把有畫面的瀏覽器視窗拉到最前 |
| [#156729](https://github.com/NousResearch/hermes-agent/issues/156729) | 提案：受保護指令檔（AGENTS.md／CLAUDE.md／SOUL.md）的核准畫面應顯示擬修改內容 |

來源：[Issues](https://github.com/NousResearch/hermes-agent/issues?q=is%3Aissue+sort%3Acreated-desc)。
