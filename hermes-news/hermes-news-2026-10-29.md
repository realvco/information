# Hermes-Agent 每日情報 — 2026-10-29

> 資料來源涵蓋截至 2026-10-29 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **過去 24 小時無新的正式版發布，目前穩定版仍為 v0.22.5**：v0.22.5 於 2026-10-23 發布（約 81 項 PR 彙整，完整 release notes 延到 v0.23.0），之後只有每日 canary 建置（最新為 10-28 的 v0.22.5+canary.20261028T064834Z，非正式版）。（[Releases feed](https://github.com/NousResearch/hermes-agent/releases.atom)）

2. **新增 issue 涵蓋 provider 串接、桌面版與 session 管理**：例如 Anthropic 用量 ≤1% 被顯示為 100%（[#146280](https://github.com/NousResearch/hermes-agent/issues/146280)）、Opencode Go 訂閱無法使用 deepseek v4.1 flash（[#146304](https://github.com/NousResearch/hermes-agent/issues/146304)）。以上為使用者回報，尚待維護者確認。

---

## 2. LLM 串接實戰回報

過去 24 小時未查到 Claude／GPT／Gemini／本地模型的獨立實測回報。

**Provider 相關 issue**
- **Claude／Anthropic**：用量 utilization ≤1% 被回報為 100%（標籤 provider/anthropic，[#146280](https://github.com/NousResearch/hermes-agent/issues/146280)）；Windows 上 claude-subscription-directsdk 結束時出現 PermissionError WinError 32（[#146288](https://github.com/NousResearch/hermes-agent/issues/146288)）。
- **DeepSeek**：Opencode Go 訂閱無法使用 deepseek v4.1 flash，標為 cause/upstream（[#146304](https://github.com/NousResearch/hermes-agent/issues/146304)）。

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 說明 |
|------|----------|------|
| **v0.22.5** | 2026-10-23 | 目前最新穩定版；約 81 項 PR 彙整，涵蓋 Nous 登入改版、usage-window 方案支援、可插拔 delegation 後端、Singularity 後端移入 plugins、Electron 44 升級；完整 notes 延至 v0.23.0（[release](https://github.com/NousResearch/hermes-agent/releases/tag/v0.22.5)） |

另有每日 canary 建置（最新 [v0.22.5+canary.20261028T064834Z](https://github.com/NousResearch/hermes-agent/releases/tag/v0.22.5%2Bcanary.20261028T064834Z)），僅有建置時間戳，無 release notes，非正式版。來源：[Releases feed](https://github.com/NousResearch/hermes-agent/releases.atom)。

---

## 4. 社群討論與已知問題

2026-10-28／29 新增 issue（部分，優先度取自 GitHub 標籤）：

| Issue | 說明 | 優先度 |
|------|------|------|
| [#146302](https://github.com/NousResearch/hermes-agent/issues/146302) | 桌面版「Move to project」失敗，出現 Session revision unavailable | P2 |
| [#146301](https://github.com/NousResearch/hermes-agent/issues/146301) | Docker 後端：/root/.hermes 被 Docker 以 root 建立 | P2 |
| [#146297](https://github.com/NousResearch/hermes-agent/issues/146297) | state：隔離的 handle 仍執行 SQLite 關閉時的 checkpoint | P2 |
| [#146291](https://github.com/NousResearch/hermes-agent/issues/146291) | 桌面版 Windows 啟動麥克風時視窗凍結 1–7 秒 | P2 |
| [#146278](https://github.com/NousResearch/hermes-agent/issues/146278) | cron：agent 任務被「Gateway draining: update_paused」拒絕 | P2 |
| [#146272](https://github.com/NousResearch/hermes-agent/issues/146272) | 使用者 plugin 的 transform_tool_result hook 註冊後從未被呼叫 | P2 |
| [#146275](https://github.com/NousResearch/hermes-agent/issues/146275) | TUI 的模型清單過長難以選取 | P2 |
| [#146293](https://github.com/NousResearch/hermes-agent/issues/146293) | Memory：claim_instance 讓同一 provider 實例可被重新初始化（type/security） | P3 |
| [#146265](https://github.com/NousResearch/hermes-agent/issues/146265) | 功能請求：在執行時頁尾與 CLI 狀態列顯示路由後的後端模型 | P3 |

以上為 issue 回報，是否成立與是否修補尚待維護者確認。來源：[Issues](https://github.com/NousResearch/hermes-agent/issues)。
