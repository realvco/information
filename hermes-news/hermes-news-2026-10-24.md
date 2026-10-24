# Hermes-Agent 每日情報 — 2026-10-24

> 資料來源涵蓋截至 2026-10-24 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **v0.22.5 發布（2026-10-22 UTC，Releases feed 於 10-23 更新），目前最新穩定版**：彙整自 v0.22.4 以來約 81 項已合併 PR（231 個 commit、關閉 30 個 issue）。重點包含重做的 Nous 登入流程、用量視窗（usage-window）方案支援、可插拔的 delegation 後端、`hermes import-agent`，以及將 Singularity 後端移入外掛。完整整理過的說明待 v0.23.0。（[Releases feed](https://github.com/NousResearch/hermes-agent/releases.atom)）

2. **v0.22.5 發布後，桌面版 session 相關問題集中回報**：桌面版無法封存／刪除或恢復新式對話（[#143839](https://github.com/NousResearch/hermes-agent/issues/143839)）、長歷史 session 卡在「Loading session」（[#143829](https://github.com/NousResearch/hermes-agent/issues/143829)）。以上為使用者回報，尚待維護者確認。

3. **session 清單效能與 0.22.4 回歸問題**：session 清單與計數呼叫重掃所有 lineage receipt 導致 CPU 滿載（[#143833](https://github.com/NousResearch/hermes-agent/issues/143833)）；0.22.4 的對話變成永久不可寫入、背景 delegation 結果未送達（[#143818](https://github.com/NousResearch/hermes-agent/issues/143818)）。

---

## 2. LLM 串接實戰回報

過去 24 小時未查到 Claude／GPT 的獨立使用者實測回報。

**Gemini／自訂 provider**
- 在 umbrelOS 上使用自訂（Gemini）provider 時，Dashboard 引導精靈陷入迴圈（[#143814](https://github.com/NousResearch/hermes-agent/issues/143814)）。

**本地模型**：未查到新的實測回報。

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 說明 |
|------|----------|------|
| **v0.22.5** | 2026-10-22 | 目前最新；約 81 項 PR 彙整，Nous 登入重做、用量視窗方案、可插拔 delegation 後端、`hermes import-agent`、Singularity 後端改為外掛 |
| v0.22.4 | 2026-10-21 | 桌面版不再顯示建議膠囊、WhatsApp 登出後可重新配對、核准模式標籤與建置逾時修正 |
| v0.22.3 | 2026-10-21 | 彙整約 620 項 PR；完整說明待 v0.23.0 |

另有 10-23 的 rc.2／rc.3 與 canary 建置條目（無 release notes，非正式版）。來源：[Releases feed](https://github.com/NousResearch/hermes-agent/releases.atom)。

---

## 4. 社群討論與已知問題

2026-10-24 新增 issue（部分，優先度取自 GitHub 標籤）：

| Issue | 說明 | 優先度 |
|------|------|------|
| [#143839](https://github.com/NousResearch/hermes-agent/issues/143839) | 桌面版（0.22.5）無法封存／刪除或恢復新式對話 | P2 |
| [#143829](https://github.com/NousResearch/hermes-agent/issues/143829) | 桌面版長歷史 session 卡在「Loading session」 | P2 |
| [#143825](https://github.com/NousResearch/hermes-agent/issues/143825) | Windows 桌面版 admission_conflict 永久阻擋單一 session 的恢復與分支 | P2 |
| [#143818](https://github.com/NousResearch/hermes-agent/issues/143818) | 0.22.4：對話永久不可寫入、背景 delegation 結果未送達 | P2 |
| [#143810](https://github.com/NousResearch/hermes-agent/issues/143810) | TUI：agent 忙碌時送出的訊息重複顯示，最終回覆泡泡為空 | P2 |
| [#143806](https://github.com/NousResearch/hermes-agent/issues/143806) | 日誌佇列無上限，保留例外 traceback 圖造成記憶體問題 | P2 |
| [#143807](https://github.com/NousResearch/hermes-agent/issues/143807) | 內建 Chromium 145 仍信任遭入侵的 eMudhra 中繼憑證 | P3（security） |

另有 Kanban 相關：dispatcher 在任務 gave_up 轉為 blocked 時應通知受指派者（[#143826](https://github.com/NousResearch/hermes-agent/issues/143826)）、被跳過（skipped）的必要檢查被永久歸類為 infra（[#143824](https://github.com/NousResearch/hermes-agent/issues/143824)）。

以上為 issue 回報，是否成立與是否修補尚待維護者確認。來源：[Issues](https://github.com/NousResearch/hermes-agent/issues)。
