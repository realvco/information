# Hermes-Agent 每日情報 — 2026-11-28

> 資料來源涵蓋截至 2026-11-28 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **過去 24 小時無新的穩定版，目前穩定版仍為 v0.23.4**（2026-11-24 發布，標示為 Latest）。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

2. **出現 v0.23.5 的 release candidate**：`rc.1-v0.23.5`（2026-11-28 約 02:09 UTC），feed 中僅有建置中繼資料，未附變更說明，內容尚不可知。（[releases feed](https://github.com/NousResearch/hermes-agent/releases.atom)）

3. **Anthropic 相關新回報兩則**：429 的 `retry-after` 被忽略並誤報為缺少憑證、用量 1% 被放大成 100%（皆為使用者回報，尚待維護者確認）。（見第 2 節）

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）

- [#160978](https://github.com/NousResearch/hermes-agent/issues/160978)：Anthropic 用量查詢遇到 429 時忽略 `retry-after`，且被誤報為「缺少憑證」（P2 bug）。
- [#160977](https://github.com/NousResearch/hermes-agent/issues/160977)：Anthropic 用量顯示中，實際為 1% 的數值被縮放成 100%（P2 bug）。

### GPT／Gemini／本地模型

- 過去 24 小時新增 issue 中，未查到針對 GPT／Gemini／本地模型的串接實戰回報，不予編造。
- 與代理層相關：[#160926](https://github.com/NousResearch/hermes-agent/issues/160926) 回報在 LiteLLM 後方缺少 session affinity header 會造成 400 錯誤（P3 bug）。

---

## 3. 版本與 release 動態

| 版本 | 發布時間（UTC） | 說明 |
|------|----------|------|
| rc.1-v0.23.5 | 2026-11-28 約 02:09 | v0.23.5 候選版；feed 無變更說明 |
| canary.20261127T064803Z-v0.23.4 | 2026-11-27 約 06:48 | 預先發行版；feed 無變更說明 |
| canary.20261126T064809Z-v0.23.4 | 2026-11-26 約 06:48 | 預先發行版；feed 無變更說明 |
| **v0.23.4** | 2026-11-24 | 目前最新穩定版（Latest） |

來源：[releases feed](https://github.com/NousResearch/hermes-agent/releases.atom)、[Releases](https://github.com/NousResearch/hermes-agent/releases)。

---

## 4. 社群討論與已知問題

新增 issue（皆為使用者回報，是否成立尚待維護者確認）：

| Issue | 說明 |
|------|------|
| [#160970](https://github.com/NousResearch/hermes-agent/issues/160970) | 次要 profile 的最終回覆經由預設 profile 的 bot 送出（Telegram，標為 security，P3） |
| [#160968](https://github.com/NousResearch/hermes-agent/issues/160968) | CLI 在訊息同時含文字與工具呼叫時遺失助理文字（P2 bug） |
| [#160963](https://github.com/NousResearch/hermes-agent/issues/160963) | Headless Kanban worker 因同意被拒而停止，卻被標記為當機（P2 bug） |
| [#160952](https://github.com/NousResearch/hermes-agent/issues/160952) | 回合中途使用者介入（steering）導致該回合沒有任何助理輸出（P2 bug） |
| [#160945](https://github.com/NousResearch/hermes-agent/issues/160945) | Cron agent session 缺少外部記憶提供者的工具（P2 bug） |
| [#160944](https://github.com/NousResearch/hermes-agent/issues/160944) | 過期的 prompt-token 計數導致新的對話內容被重複壓縮（P2 perf） |
| [#160935](https://github.com/NousResearch/hermes-agent/issues/160935) | Android 搭配 Gboard 聊天時，每個送出的字都重複（P2 bug） |
| [#160932](https://github.com/NousResearch/hermes-agent/issues/160932) | 特製輸入使圖片連結轉換呈二次方時間，阻塞程序（P2 perf） |
| [#160911](https://github.com/NousResearch/hermes-agent/issues/160911) | 並行工具呼叫會讓閒置回收的 stdio MCP server 被重複啟動（P2 bug） |

功能提案：[#160967](https://github.com/NousResearch/hermes-agent/issues/160967)（CLI 內建每回合頁尾資訊）、[#160914](https://github.com/NousResearch/hermes-agent/issues/160914)（網頁儀表板可編輯既有 MCP server）、[#160948](https://github.com/NousResearch/hermes-agent/issues/160948)（桌面版可把 session 記錄摘錄附為聊天脈絡）。

來源：[Issues](https://github.com/NousResearch/hermes-agent/issues?q=is%3Aissue+sort%3Acreated-desc)。
