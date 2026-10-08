# Hermes-Agent 每日情報 — 2026-10-08

> 資料來源涵蓋截至 2026-10-08 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **過去 24 小時無新版本，目前穩定版仍為 v0.21.5（v2026.9.24）**：Releases 頁面最新一筆仍是 9/24 的 patch rollup。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

2. **state.db / SQLite 可靠性問題持續出現**：有回報指出四個儲存檔反覆發生 SQLite header 損毀，另有 Desktop 的 `projects.db` 錯誤導致健康的 `state.db` 被誤隔離（quarantine）。（[#134866](https://github.com/NousResearch/hermes-agent/issues/134866)、[#134865](https://github.com/NousResearch/hermes-agent/issues/134865)）

3. **兩項 P1 錯誤**：cron 的 no-agent 每日工作被靜默略過；還原訊息時重複寫入，產生重複資料列。（[#134858](https://github.com/NousResearch/hermes-agent/issues/134858)、[#134800](https://github.com/NousResearch/hermes-agent/issues/134800)）

4. **外掛核准機制的安全性議題**：外掛核准畫面可能顯示過期的參數，且格式錯誤的核准決定應改為 fail closed。（[#134849](https://github.com/NousResearch/hermes-agent/issues/134849)、[#134850](https://github.com/NousResearch/hermes-agent/issues/134850)）

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）
- **opencode-go**：Claude Haiku 被路由到錯誤的端點。（[#134844](https://github.com/NousResearch/hermes-agent/issues/134844)）

### GPT（OpenAI）
- 今日未查到新的 GPT 專屬回報。

### Gemini（Google）
- 今日未查到新的 Gemini 專屬回報。

### 其他供應商／本地模型
- 今日未查到新的回報。（昨日的 DeepSeek 內容審查 400 問題 [#134261](https://github.com/NousResearch/hermes-agent/issues/134261) 本次未查到進展。）

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 類型 | 重點 |
|------|----------|------|------|
| **v0.21.5（v2026.9.24）** | 2026-09-24 | Patch rollup | **目前最新穩定版** |
| v0.21.4（v2026.9.21） | 2026-09-21 | Patch rollup | 約 1,800 PR；host-wide gateway singleton lock、結構化 JSONL 輸出、skill 自動載入 |
| v0.21.3（v2026.9.14） | 2026-09-14 | Patch rollup | 遠端 dashboard session 穩定性、state.db writer 洩漏修正 |

來源：[Releases](https://github.com/NousResearch/hermes-agent/releases)。過去 24 小時無新版本。

---

## 4. 社群討論與已知問題

優先度標籤取自 GitHub issue 頁面。

| Issue | 說明 | 優先度 |
|------|------|------|
| [#134858](https://github.com/NousResearch/hermes-agent/issues/134858) | Cron：no-agent 每日工作被靜默略過 | P1 |
| [#134800](https://github.com/NousResearch/hermes-agent/issues/134800) | 還原的訊息被重新寫入，產生重複資料列 | P1 |
| [#134866](https://github.com/NousResearch/hermes-agent/issues/134866) | 四個儲存檔反覆出現 SQLite header 損毀（needs-repro） | P2 |
| [#134865](https://github.com/NousResearch/hermes-agent/issues/134865) | Desktop `projects.db` 錯誤使健康的 `state.db` 被隔離 | P2 |
| [#134843](https://github.com/NousResearch/hermes-agent/issues/134843) | Release-swap 自我更新後的 checkout 缺少 origin | P2 |
| [#134837](https://github.com/NousResearch/hermes-agent/issues/134837) | 日誌時間戳與作業系統時區有偏移 | P2 |
| [#134809](https://github.com/NousResearch/hermes-agent/issues/134809) | Desktop SSH：非預設 profile 的 session 無法開啟 | P2 |
| [#134805](https://github.com/NousResearch/hermes-agent/issues/134805) | `skills install` 安裝了官方 skill 而非指定的那個 | P2 |
| [#134849](https://github.com/NousResearch/hermes-agent/issues/134849) | 外掛核准可能描述過期的參數（type/security） | P3 |
| [#134850](https://github.com/NousResearch/hermes-agent/issues/134850) | 格式錯誤的外掛核准決定應 fail closed | P3 |
| [#134833](https://github.com/NousResearch/hermes-agent/issues/134833) | RFC：為 Hermes TUI 提供實驗性 Tern/TSP 介面 | P3 |
| [#134826](https://github.com/NousResearch/hermes-agent/issues/134826) | 功能提案：ACP 外部 runtime 一致性規範 | P4 |

來源：[Issues](https://github.com/NousResearch/hermes-agent/issues)。
