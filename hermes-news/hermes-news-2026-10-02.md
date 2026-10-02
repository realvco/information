# Hermes-Agent 每日情報 — 2026-10-02

> 資料來源涵蓋截至 2026-10-02 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **過去 24 小時無新版本，目前穩定版仍為 v0.21.5（v2026.9.24）**：該版整併約 460 個 PR，包含 Desktop plugin SDK 強化、法／德／西語目錄翻譯、設定載入與 gateway 訊息處理的效能優化。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

2. **10/2 新開 issue 出現兩個 P1，皆與 session 狀態有關**：[#131104](https://github.com/NousResearch/hermes-agent/issues/131104)（compaction 重述後留下兩份使用者請求並重送上一則回覆）與 [#131075](https://github.com/NousResearch/hermes-agent/issues/131075)（`sessions export` 靜默略過已釘選／封存的 session，但 `sessions prune --include-archived` 會刪除它們）。使用 prune 前建議先自行備份。

3. **Desktop 更新機制與 Docker 版本不一致**：[#131103](https://github.com/NousResearch/hermes-agent/issues/131103) 指出 Desktop 內建更新器追蹤 `main`，而 Docker Hub 停在 release tag，導致遠端 `session.create` 因未知欄位失敗（P2）。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）
- v0.21.5 release 說明提及新增 Claude Opus 5.5 模型項目（[Releases](https://github.com/NousResearch/hermes-agent/releases)）。今日未查到實戰回報。

### GPT（OpenAI）
- v0.21.5 release 說明提及新增 GPT-6 模型項目（[Releases](https://github.com/NousResearch/hermes-agent/releases)）。今日未查到新的實戰回報。

### Gemini（Google）
- 今日未查到新的 Gemini 專屬回報。

### 其他供應商／本地模型
- [#131098](https://github.com/NousResearch/hermes-agent/issues/131098)（P2，provider/openrouter，needs-repro）：回報 142 次工具呼叫超過 max_turns=100，最終回覆退化成約 12k 字元的語意重複。尚待重現。

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 類型 | 重點 |
|------|----------|------|------|
| **v0.21.5（v2026.9.24）** | 2026-09-24 | Patch rollup | **目前最新穩定版**；約 460 PR、Desktop plugin SDK、法／德／西語翻譯、GPT-6／Claude Opus 5.5 模型項目、效能優化 |
| v0.21.4（v2026.9.21） | 2026-09-21 | Patch rollup | host-wide gateway singleton lock、`--format stream-json`、skill 自動載入、Desktop 字型選擇器、多個社群外掛 |
| v0.21.3（v2026.9.14） | 2026-09-14 | Patch rollup | 遠端 Desktop／雲端工作階段過期修復、state.db writer handle 洩漏修復、HEIF/HEIC 解碼 |

來源：[Releases](https://github.com/NousResearch/hermes-agent/releases)。過去 24 小時無新版本。

---

## 4. 社群討論與已知問題

| Issue | 說明 | 優先度 |
|------|------|------|
| [#131104](https://github.com/NousResearch/hermes-agent/issues/131104) | compaction 重述留下兩份使用者請求並重送上一則回覆 | P1 |
| [#131075](https://github.com/NousResearch/hermes-agent/issues/131075) | `sessions export` 略過已釘選／封存 session，prune 卻會刪除 | P1 |
| [#131103](https://github.com/NousResearch/hermes-agent/issues/131103) | Desktop 更新器追蹤 main、Docker 停在 release tag，遠端 session.create 失敗 | P2 |
| [#131098](https://github.com/NousResearch/hermes-agent/issues/131098) | 超過 max_turns 的工具呼叫與回覆退化（OpenRouter） | P2 |
| [#131096](https://github.com/NousResearch/hermes-agent/issues/131096) | 終端機指令輸出長串空白行會凍結 gateway | P2 |
| [#131093](https://github.com/NousResearch/hermes-agent/issues/131093) | Windows 中文使用者名稱下 hermes 指令找不到路徑 | P2 |
| [#131091](https://github.com/NousResearch/hermes-agent/issues/131091) | 串流 TTS 會朗讀程式碼區塊 | P2 |
| [#131089](https://github.com/NousResearch/hermes-agent/issues/131089) | 啟動自動恢復略過 relay 式 Discord session | P2 |
| [#131084](https://github.com/NousResearch/hermes-agent/issues/131084) | 標題為「No reply:」的 cron 報告被當成靜默標記而遭抑制 | P2 |
| [#131055](https://github.com/NousResearch/hermes-agent/issues/131055) | Linux Desktop 第二個執行個體污染 sandbox fallback 標記 | P2 |
| [#131099](https://github.com/NousResearch/hermes-agent/issues/131099) | `sessions archive` 缺少 --session-id 且略過開啟中的 session | P3 |
| [#131094](https://github.com/NousResearch/hermes-agent/issues/131094) | TTS 誤唸金額量級（$5M） | P3 |

以上來自 GitHub issue 列表，僅列最新約 15 則中的部分，未涵蓋全部；本日未查到新的官方公告或社群貼文。

---

*報告生成時間：2026-10-02 UTC*
