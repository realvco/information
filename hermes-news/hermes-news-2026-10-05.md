# Hermes-Agent 每日情報 — 2026-10-05

> 資料來源涵蓋截至 2026-10-05 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **過去 24 小時無新版本，目前穩定版仍為 v0.21.5（v2026.9.24）**：該版整併約 460 個 PR，包含 Desktop plugin SDK 擴充、法／德／西語完整目錄、自訂模型輸入與每個 profile 的啟停控制。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

2. **Multi-profile host 出現設定邊界問題（P2，標記為 security-boundary）**：served profile 在未限定範圍讀取設定時，`${VAR}` 會用啟動 profile 的環境變數展開。（[#133044](https://github.com/NousResearch/hermes-agent/issues/133044)）

3. **設定與記憶相關 P2 錯誤**：`hermes config set agent.reasoning_effort none` 會存成 null（[#133026](https://github.com/NousResearch/hermes-agent/issues/133026)）；調低 `memory_char_limit` / `user_char_limit` 會讓檔案被鎖住（[#133028](https://github.com/NousResearch/hermes-agent/issues/133028)）。

4. **本地模型與 OpenRouter 相關回報**：mmproj／音訊編解碼 GGUF 被當成可服務模型列出（[#133037](https://github.com/NousResearch/hermes-agent/issues/133037)）；`hermes prompt-size` 明明文件寫離線卻仍會抓 OpenRouter 模型清單（[#132998](https://github.com/NousResearch/hermes-agent/issues/132998)）。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）
- 今日未查到新的實戰回報。

### GPT（OpenAI）
- 今日未查到新的 GPT 專屬回報。

### Gemini（Google）
- 今日未查到新的 Gemini 專屬回報。

### 其他供應商／本地模型
- OpenRouter：[#132998](https://github.com/NousResearch/hermes-agent/issues/132998)（P2）`hermes prompt-size` 與文件描述不符，仍會連線取得模型清單。
- 本地模型：[#133037](https://github.com/NousResearch/hermes-agent/issues/133037)（P2）本地 runtime 把 mmproj／audio-codec GGUF 當成可服務模型提供選擇。

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 類型 | 重點 |
|------|----------|------|------|
| **v0.21.5（v2026.9.24）** | 2026-09-24 | Patch rollup | **目前最新穩定版**；約 460 PR、Desktop plugin SDK、法／德／西語翻譯、自訂模型輸入 |
| v0.21.4（v2026.9.21） | 2026-09-21 | Patch rollup | 約 1,800 PR；host-wide gateway singleton lock、結構化 JSONL CLI 輸出、LTX 2.5 與 Kling O3 影片支援 |
| v0.21.3（v2026.9.14） | 2026-09-14 | Patch rollup | 約 338 PR；遠端 dashboard session 修復、state.db 重複 writer 修正、OpenRouter OAuth PKCE 登入 |

來源：[Releases](https://github.com/NousResearch/hermes-agent/releases)。過去 24 小時無新版本。

---

## 4. 社群討論與已知問題

| Issue | 說明 | 優先度 |
|------|------|------|
| [#133044](https://github.com/NousResearch/hermes-agent/issues/133044) | Multi-profile host：served profile 的 `${VAR}` 以啟動 profile 的環境展開 | P2 |
| [#133037](https://github.com/NousResearch/hermes-agent/issues/133037) | mmproj／audio-codec GGUF 被列為可服務模型 | P2 |
| [#133028](https://github.com/NousResearch/hermes-agent/issues/133028) | 調低 memory_char_limit / user_char_limit 會鎖住檔案 | P2 |
| [#133026](https://github.com/NousResearch/hermes-agent/issues/133026) | `config set agent.reasoning_effort none` 存成 null | P2 |
| [#132999](https://github.com/NousResearch/hermes-agent/issues/132999) | ChatSidebar 無限 WebSocket 重連使 CPU 100%（標為 duplicate） | P2 |
| [#132998](https://github.com/NousResearch/hermes-agent/issues/132998) | `hermes prompt-size` 仍抓 OpenRouter 模型清單 | P2 |
| [#133052](https://github.com/NousResearch/hermes-agent/issues/133052) | Dashboard chat 重連更換 PTY key 形態時，會關閉前一個 PTY 並中斷進行中的回合 | 未標示 |
| [#133025](https://github.com/NousResearch/hermes-agent/issues/133025) | 功能提案：在共享持久紀錄上提供選用的 Feed／Ideas／Goals 檢視 | P3 |

來源：[Issues](https://github.com/NousResearch/hermes-agent/issues)。
