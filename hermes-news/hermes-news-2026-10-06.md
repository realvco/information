# Hermes-Agent 每日情報 — 2026-10-06

> 資料來源涵蓋截至 2026-10-06 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **過去 24 小時無新版本，目前穩定版仍為 v0.21.5（v2026.9.24）**：該版整併約 460 個 PR，包含 Desktop plugin SDK 擴充與法／德／西語目錄。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

2. **Desktop 端出現重連無退避問題**：renderer 在後端埠已失效時仍持續連線、沒有 backoff。（[#133628](https://github.com/NousResearch/hermes-agent/issues/133628)）

3. **終端機工具 P2 錯誤**：背景程序中的 `sudo` 無法驗證也無法提示輸入密碼。（[#133622](https://github.com/NousResearch/hermes-agent/issues/133622)）

4. **新功能提案**：內建閒置 profile 自動關閉與 state.db 清理設定。（[#133623](https://github.com/NousResearch/hermes-agent/issues/133623)）

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）
- 今日未查到新的實戰回報。

### GPT（OpenAI）
- 今日未查到新的 GPT 專屬回報。

### Gemini（Google）
- 今日未查到新的 Gemini 專屬回報。

### 其他供應商／本地模型
- 今日未查到新的相關回報。

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 類型 | 重點 |
|------|----------|------|------|
| **v0.21.5（v2026.9.24）** | 2026-09-24 | Patch rollup | **目前最新穩定版**；約 460 PR、Desktop plugin SDK、法／德／西語目錄 |
| v0.21.4（v2026.9.21） | 2026-09-21 | Patch rollup | 約 1,800 PR；host-wide gateway singleton lock、結構化 JSONL 輸出、skill 自動載入 |
| v0.21.3（v2026.9.14） | 2026-09-14 | Patch rollup | 遠端 dashboard session 修復、state.db writer 洩漏修正、MCP OAuth 改進 |

來源：[Releases](https://github.com/NousResearch/hermes-agent/releases)。過去 24 小時無新版本。

---

## 4. 社群討論與已知問題

| Issue | 說明 | 優先度 |
|------|------|------|
| [#133628](https://github.com/NousResearch/hermes-agent/issues/133628) | Desktop renderer 對已失效的後端埠持續連線、無 backoff | 未標示 |
| [#133622](https://github.com/NousResearch/hermes-agent/issues/133622) | 背景程序中的 `sudo` 無法驗證或提示 | P2 |
| [#133620](https://github.com/NousResearch/hermes-agent/issues/133620) | 切換 profile 時側邊欄狀態異常 | P3 |
| [#133616](https://github.com/NousResearch/hermes-agent/issues/133616) | 獨立 MCP probe 未載入已啟用的 plugin secret source | P3 |
| [#133623](https://github.com/NousResearch/hermes-agent/issues/133623) | 功能提案：閒置 profile 關閉與 state.db 清理的內建設定 | P3 |

來源：[Issues](https://github.com/NousResearch/hermes-agent/issues)。
