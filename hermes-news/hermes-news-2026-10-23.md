# Hermes-Agent 每日情報 — 2026-10-23

> 資料來源涵蓋截至 2026-10-23 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **過去 24 小時無新版本發布，目前最新穩定版仍為 v0.22.4（2026-10-21）**：Releases 頁面最新項目未變。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

2. **新增 issue 以 `hermes update` 與桌面版問題為主**：`hermes update` 後 release tag 過期導致 `--version` 顯示舊版（[#143361](https://github.com/NousResearch/hermes-agent/issues/143361)）、Windows 上因殘留 gateway PID 檔而更新失敗（[#143331](https://github.com/NousResearch/hermes-agent/issues/143331)）。以上為使用者回報，尚待維護者確認。

3. **壓縮（compression）設定出現兩項回報**：門檻假設 524,288 token 視窗（[#143316](https://github.com/NousResearch/hermes-agent/issues/143316)）、provider 設為 `auto` 時繼承聊天模型的 provider 而失敗（[#143315](https://github.com/NousResearch/hermes-agent/issues/143315)）。

---

## 2. LLM 串接實戰回報

過去 24 小時未查到 Claude／GPT／Gemini 的獨立使用者實測回報。與模型／provider 設定相關的新 issue：

- 桌面版模型選擇器切換模型時沿用舊 provider（[#143323](https://github.com/NousResearch/hermes-agent/issues/143323)）。
- 具名自訂 provider 無法被視覺（vision）探測解析（[#143347](https://github.com/NousResearch/hermes-agent/issues/143347)）。
- 模型選擇在近期更新後出現延遲（[#143326](https://github.com/NousResearch/hermes-agent/issues/143326)）。
- 功能需求：從登入 shell 環境解析 provider API 金鑰（[#143330](https://github.com/NousResearch/hermes-agent/issues/143330)，標籤含 xAI）。

本地模型方面，未查到新的實測回報。

---

## 3. 版本與 release 動態

過去 24 小時無重大更新。目前最新穩定版：**v0.22.4**（2026-10-21）。

| 版本 | 發布日期 | 說明 |
|------|----------|------|
| **v0.22.4** | 2026-10-21 | 目前最新；桌面版不再顯示建議膠囊、無相依外掛略過相依提示、WhatsApp 登出後可重新配對、核准模式標籤與建置逾時修正 |
| v0.22.3 | 2026-10-21 | 彙整約 620 項 PR；完整說明待 v0.23.0 |
| v0.22.2 | 2026-10-15 | 修正 gateway 重開機後無法啟動；已知問題：0.22.1 gateway 建立的 session 無法刪除 |

來源：[Releases](https://github.com/NousResearch/hermes-agent/releases)。

---

## 4. 社群討論與已知問題

2026-10-23 新增 issue（部分，優先度取自 GitHub 標籤）：

| Issue | 說明 | 優先度 |
|------|------|------|
| [#143361](https://github.com/NousResearch/hermes-agent/issues/143361) | `hermes update` 後 release tag 過期，`--version` 顯示舊版 | P2 |
| [#143331](https://github.com/NousResearch/hermes-agent/issues/143331) | Windows 上 `hermes update` 因殘留 gateway PID 檔失敗 | P2 |
| [#143313](https://github.com/NousResearch/hermes-agent/issues/143313) | Python 3.14 下 `pip install -e .` 因 Pillow 版本衝突失敗 | P2 |
| [#143349](https://github.com/NousResearch/hermes-agent/issues/143349) | 桌面版群組聊天輸入框在送出驗證失敗後重試時被鎖住 | P2 |
| [#143348](https://github.com/NousResearch/hermes-agent/issues/143348) | 過期的 session 擁有者提示使 session 固定在預設 profile | P2 |
| [#143333](https://github.com/NousResearch/hermes-agent/issues/143333) | `hermes profile rename` 使本地聊天失聯 | P2 |
| [#143320](https://github.com/NousResearch/hermes-agent/issues/143320) | 回合結束提示音由其他 session 觸發 | P2 |

功能需求例：Kanban dispatcher 在卡住警告中回報 cap-hold 原因（[#143346](https://github.com/NousResearch/hermes-agent/issues/143346)）、clarify 工具支援每個選項的預覽與描述欄位（[#143338](https://github.com/NousResearch/hermes-agent/issues/143338)）。

以上為 issue 回報，是否成立與是否修補尚待維護者確認。來源：[Issues](https://github.com/NousResearch/hermes-agent/issues)。
