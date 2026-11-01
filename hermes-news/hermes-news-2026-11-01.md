# Hermes-Agent 每日情報 — 2026-11-01

> 資料來源涵蓋截至 2026-11-01 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **過去 24 小時無重大更新，目前穩定版仍為 v0.22.5**：v0.22.5 於 2026-10-23 發布，之後僅有每日 canary 建置（feed 中最新為 10-31 的 v0.22.5+canary.20261031T064409Z，非正式版，說明僅有時間戳）。（[Releases feed](https://github.com/NousResearch/hermes-agent/releases.atom)）

2. **新增 issue 以桌面版、gateway 與記憶體 provider 為主**：包含智慧核准（Smart Approval）自動放行一段 PowerShell 指令的安全性回報（[#147373](https://github.com/NousResearch/hermes-agent/issues/147373)），以及記憶體 provider 初始化失敗後仍維持「已啟用」狀態（[#147383](https://github.com/NousResearch/hermes-agent/issues/147383)）。以上為使用者回報，尚待維護者確認。

---

## 2. LLM 串接實戰回報

過去 24 小時未查到 Claude／GPT／Gemini／本地模型的獨立實測回報，也未見與特定 LLM provider 直接相關的新 issue。

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 說明 |
|------|----------|------|
| **v0.22.5** | 2026-10-23 | 目前最新穩定版（[release](https://github.com/NousResearch/hermes-agent/releases/tag/v0.22.5)） |

另有每日 canary 建置（最新可查為 [v0.22.5+canary.20261031T064409Z](https://github.com/NousResearch/hermes-agent/releases/tag/v0.22.5%2Bcanary.20261031T064409Z)），僅有建置時間戳，非正式版。來源：[Releases feed](https://github.com/NousResearch/hermes-agent/releases.atom)。

---

## 4. 社群討論與已知問題

2026-10-31／11-01 新增 issue（部分）：

| Issue | 說明 |
|------|------|
| [#147373](https://github.com/NousResearch/hermes-agent/issues/147373) | 智慧核准自動放行具破壞性的 PowerShell 指令（bug） |
| [#147383](https://github.com/NousResearch/hermes-agent/issues/147383)／[#147384](https://github.com/NousResearch/hermes-agent/issues/147384) | 記憶體 provider 初始化失敗後仍顯示啟用；plugin config_schema.py 單獨載入時相對匯入失效 |
| [#147379](https://github.com/NousResearch/hermes-agent/issues/147379) | Gateway 投遞紀錄：mark_attempting 失敗會讓已送達的回覆停留在 pending |
| [#147361](https://github.com/NousResearch/hermes-agent/issues/147361) | 行動版 app 連遠端 gateway 時聊天 WebSocket 一律 403 |
| [#147346](https://github.com/NousResearch/hermes-agent/issues/147346) | Telegram adapter 備援流程遺失 SEED_FALLBACK_IPS |
| [#147342](https://github.com/NousResearch/hermes-agent/issues/147342) | Slack 重複事件導致重複處理（double admission） |
| [#147386](https://github.com/NousResearch/hermes-agent/issues/147386)／[#147387](https://github.com/NousResearch/hermes-agent/issues/147387) | 桌面版：處理與回應間分隔線未顯示；gated dashboard middleware 拒絕桌面 token 模式憑證 |
| [#147393](https://github.com/NousResearch/hermes-agent/issues/147393)／[#147385](https://github.com/NousResearch/hermes-agent/issues/147385) | 功能請求：桌面版核准逾時復原機制；側邊欄更清楚顯示 bot 狀態 |

以上為 issue 回報，是否成立與是否修補尚待維護者確認。來源：[Issues](https://github.com/NousResearch/hermes-agent/issues)。
