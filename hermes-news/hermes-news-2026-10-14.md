# Hermes-Agent 每日情報 — 2026-10-14

> 資料來源涵蓋截至 2026-10-14 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **過去 24 小時無新版本發布，目前穩定版仍為 v0.21.7**：GitHub Releases 頁面最新仍是 v0.21.7（說明標示 2026-10-11，頁面時間戳為 10/12）。完整整理版說明預告隨 v0.22.0 提供，v0.22.0 尚未發布。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

2. **今日新增 issue 以 Telegram、gateway 重啟與桌面版問題為主**：例如 Telegram DM 執行核准卡片使用者看不到、終端機逾時而未取得同意（#138133），以及重啟排空前才收下的內部喚醒仍被丟棄（#138123）。（[Issues](https://github.com/NousResearch/hermes-agent/issues)）

3. **出現一則使用者回饋指出日常使用中目標持續性（goal persistence）變弱、需要更多人工監督（#138135）**：屬使用者個案回報，尚未見官方回應或證實。（[#138135](https://github.com/NousResearch/hermes-agent/issues/138135)）

---

## 2. LLM 串接實戰回報

- Claude／GPT／Gemini／本地模型：過去 24 小時未查到新的串接實測回報。
- 與模型供應商相關的僅一則 issue：`redeem_codex_reset_credit` 會清除所有 Codex 憑證池項目的冷卻時間（#138121，P2）。（[#138121](https://github.com/NousResearch/hermes-agent/issues/138121)）

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 說明 |
|------|----------|------|
| **v0.21.7** | 2026-10-11 | **目前最新穩定版**；約 204 個合併 PR 的彙整；原始碼安裝改追蹤已標記 release（可用 `hermes update --set-channel main` 切回每個 commit）；桌面版新增更新通道切換；簽章推理在 session 恢復後延續 |
| v0.21.6 | 2026-10-08 | 新穩定版流程首發；dashboard 驗證強化、git filter 封鎖、email gateway 寄件者白名單修補 |
| v0.21.5（v2026.9.24） | 2026-09-24 | 約 460 PR；Desktop plugin SDK、Connectors 頁面 |

來源：[Releases](https://github.com/NousResearch/hermes-agent/releases)。

---

## 4. 社群討論與已知問題

優先度標籤取自 GitHub issue 頁面，以下為 2026-10-14 新增的部分 issue。

| Issue | 說明 | 優先度 |
|------|------|------|
| [#138154](https://github.com/NousResearch/hermes-agent/issues/138154) | 桌面版在 omarchy 上啟動時卡住 | P2 |
| [#138143](https://github.com/NousResearch/hermes-agent/issues/138143) | MCP：以 ssh 作為遠端 stdio 傳輸的子行程不在孤兒回收／watchdog 保證範圍內 | P2 |
| [#138141](https://github.com/NousResearch/hermes-agent/issues/138141) | `web_extract`：長 PDF 的中段無法從儲存的副本讀取 | P2 |
| [#138133](https://github.com/NousResearch/hermes-agent/issues/138133) | Telegram DM 執行核准卡片未顯示，終端機逾時而未取得同意 | P2 |
| [#138131](https://github.com/NousResearch/hermes-agent/issues/138131) | update：相依套件同步在使用者機器上仍套用 exclude-newer | P2 |
| [#138123](https://github.com/NousResearch/hermes-agent/issues/138123) | 重啟排空前才收下的內部喚醒仍被丟棄 | P2 |
| [#138118](https://github.com/NousResearch/hermes-agent/issues/138118) | 桌面版「Install from Git」拒絕僅含 manifest 的語言包 | P2 |
| [#138162](https://github.com/NousResearch/hermes-agent/issues/138162) | 桌面版 Cloud 登入開啟 Portal 行銷頁而非完成探索流程 | 未標示 |
| [#138160](https://github.com/NousResearch/hermes-agent/issues/138160) | Cron 回合遇暫時性 SQLite 寫入鎖即中止且不重試 | 未標示 |
| [#138148](https://github.com/NousResearch/hermes-agent/issues/138148) | 工具進度重複計數器破壞 Telegram 程式碼區塊，反引號以跳脫字元顯示 | P3 |
| [#138139](https://github.com/NousResearch/hermes-agent/issues/138139) | 建議將內建 Python 由 3.14.7 升至 3.15（Windows 改用 tail-calling 直譯器） | P3 |

來源：[Issues](https://github.com/NousResearch/hermes-agent/issues)。
