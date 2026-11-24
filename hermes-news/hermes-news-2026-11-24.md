# Hermes-Agent 每日情報 — 2026-11-24

> 資料來源涵蓋截至 2026-11-24 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **過去 24 小時無重大更新，目前穩定版仍為 v0.23.3**：該版於 2026-11-20 00:34 UTC 發布。（[latest release](https://github.com/NousResearch/hermes-agent/releases/latest)）

2. **新增自動 canary 建置**：`canary.20261123T065600Z-v0.23.3`（11-23 約 08:06 UTC 更新），內容包含新增外掛目錄項目、外掛更新與錯誤修正，桌面版安裝程式（macOS Apple Silicon、Windows x64／arm64）通過安裝與聊天冒煙測試。（[releases feed](https://github.com/NousResearch/hermes-agent/releases.atom)）

3. **有使用者回報 Vertex 的 OpenAI 相容端點，跨模型 fallback 到 Gemini 時回傳 400**：尚待維護者確認。（[#159444](https://github.com/NousResearch/hermes-agent/issues/159444)）

---

## 2. LLM 串接實戰回報

- **Gemini**：[#159444](https://github.com/NousResearch/hermes-agent/issues/159444) 回報在 Vertex OpenAI 相容端點上，跨模型 fallback 到 Gemini 會出現 400 錯誤（使用者回報，未證實）。
- **本地模型**：[#159434](https://github.com/NousResearch/hermes-agent/issues/159434) 懷疑在 Ryzen AI Halo 系統上記憶體偵測有誤（標記 `area/local-models`，使用者回報）。
- **Claude／GPT**：過去 24 小時新增 issue 中未查到相關獨立回報，不予編造。

---

## 3. 版本與 release 動態

| 版本 | 發布時間（UTC） | 說明 |
|------|----------|------|
| canary.20261123T065600Z-v0.23.3 | 2026-11-23 | 自動 canary；外掛目錄新增與更新、錯誤修正、桌面安裝程式冒煙測試通過 |
| **v0.23.3** | 2026-11-20 00:34 | 目前最新穩定版 |

來源：[releases feed](https://github.com/NousResearch/hermes-agent/releases.atom)、[latest release](https://github.com/NousResearch/hermes-agent/releases/latest)。

---

## 4. 社群討論與已知問題

新增 issue（皆為使用者回報，是否成立尚待維護者確認）：

| Issue | 說明 |
|------|------|
| [#159444](https://github.com/NousResearch/hermes-agent/issues/159444) | Vertex OpenAI 相容端點 fallback 到 Gemini 回傳 400 |
| [#159442](https://github.com/NousResearch/hermes-agent/issues/159442) | 掃描兩個 profile home 時，行程全域旗標發生競態，provider 可能遺失 |
| [#159438](https://github.com/NousResearch/hermes-agent/issues/159438) | gateway 在指令執行中途死亡後，恢復的工作階段會遺失該呼叫，模型重跑指令 |
| [#159428](https://github.com/NousResearch/hermes-agent/issues/159428) | Desktop 在自動 context 耗盡重置後隱藏先前對話 |
| [#159425](https://github.com/NousResearch/hermes-agent/issues/159425) | 本地語音轉文字的 lazy-install 無法觸發，一律回報無可用 provider |
| [#159414](https://github.com/NousResearch/hermes-agent/issues/159414) | Provider 外掛快照超過 Windows 路徑長度上限 |
| [#159396](https://github.com/NousResearch/hermes-agent/issues/159396) | 主機記憶體壓力導致所有受管 Kanban worker 同時被終止 |
| [#159388](https://github.com/NousResearch/hermes-agent/issues/159388) | 一次持久化回執遺失，造成 Kanban worker 成批死亡 |

功能提案：[#159435](https://github.com/NousResearch/hermes-agent/issues/159435)（webhook 依路由設定外部記憶 provider 模式）、[#159379](https://github.com/NousResearch/hermes-agent/issues/159379)（Desktop 聊天顯示每則訊息的 token 數）。

來源：[Issues](https://github.com/NousResearch/hermes-agent/issues?q=is%3Aissue+sort%3Acreated-desc)。
