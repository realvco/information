# Hermes-Agent 每日情報 — 2026-11-22

> 資料來源涵蓋截至 2026-11-22 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **過去 24 小時無重大更新，目前穩定版仍為 v0.23.3**：該版於 2026-11-20 00:34 UTC 發布，完整整理版說明仍延到 v0.24.0。（[v0.23.3](https://github.com/NousResearch/hermes-agent/releases/tag/v0.23.3)）

2. **新增兩個自動 canary 建置**：`canary.20261121T064420Z-v0.23.3`（11-21 06:44 UTC）與 `canary.20261122T000948Z-v0.23.3`（11-22 00:09 UTC），皆僅有中繼資料（commit 標記），沒有 release 說明。（[releases feed](https://github.com/NousResearch/hermes-agent/releases.atom)）

---

## 2. LLM 串接實戰回報

- **本地／開放模型**（使用者回報，尚待維護者確認）：[#158734](https://github.com/NousResearch/hermes-agent/issues/158734) 指出 kimi-k3 的 deferred 工具呼叫會以空參數物件送達 bridge。
- **串流工具呼叫**：[#158731](https://github.com/NousResearch/hermes-agent/issues/158731) 指出當單一 chunk 同時含有被暫留的文字與第一個 tool_calls delta 時，工具呼叫會遺失名稱而被視為 unknown。
- **輔助 LLM 回應格式**：[#158737](https://github.com/NousResearch/hermes-agent/issues/158737) 指出輔助模型回傳 list 形式內容區塊時，智慧核准（smart approvals）會因 AttributeError 當掉。
- **Claude／GPT／Gemini**：過去 24 小時未查到獨立實測回報，不予編造。

---

## 3. 版本與 release 動態

| 版本 | 發布時間（UTC） | 說明 |
|------|----------|------|
| canary.20261122T000948Z-v0.23.3 | 2026-11-22 00:09 | 自動 canary，無 release 說明 |
| canary.20261121T064420Z-v0.23.3 | 2026-11-21 06:44 | 自動 canary，無 release 說明 |
| **v0.23.3** | 2026-11-20 00:34 | 目前最新穩定版；約 370 個合併 PR；完整說明延到 v0.24.0 |

更新方式：CLI 執行 `hermes update`；Docker 拉取 `nousresearch/hermes-agent:stable`。

來源：[releases feed](https://github.com/NousResearch/hermes-agent/releases.atom)、[v0.23.3](https://github.com/NousResearch/hermes-agent/releases/tag/v0.23.3)。

---

## 4. 社群討論與已知問題

新增 issue（皆為使用者回報或提案，是否成立尚待維護者確認）：

| Issue | 說明 |
|------|------|
| [#158746](https://github.com/NousResearch/hermes-agent/issues/158746) | 提案：Desktop 的 Skills 頁面預設像商店，建議讓已安裝技能與開關更容易找到 |
| [#158744](https://github.com/NousResearch/hermes-agent/issues/158744) | 提案：managed config scope 遺失或格式錯誤時，提供 opt-in 的 fail-closed 行為 |
| [#158738](https://github.com/NousResearch/hermes-agent/issues/158738) | 共用瀏覽器預設以 headless 啟動，導致 Open Screen 空白、dock 的 Browser 失效（SingletonLock 衝突） |
| [#158736](https://github.com/NousResearch/hermes-agent/issues/158736) | Windows 上 WhatsApp 次要 profile 的 bridge 因 WinError 5 無法啟動 |
| [#158735](https://github.com/NousResearch/hermes-agent/issues/158735) | 提案：TTS 依語言自動切換語音 |
| [#158729](https://github.com/NousResearch/hermes-agent/issues/158729) | 提案：停用語音轉文字時隱藏 Desktop 的 Dictate 按鈕 |
| [#158726](https://github.com/NousResearch/hermes-agent/issues/158726) | Gateway session 快取失效會中斷進行中的 agent 串流 |
| [#158723](https://github.com/NousResearch/hermes-agent/issues/158723) | `hermes update` 後仍存活的舊 worker 會因延遲匯入已更名函式而失敗 |
| [#158715](https://github.com/NousResearch/hermes-agent/issues/158715) | Desktop composer 在即時進度持續更新時，仍殘留過期的 subagent 列 |

來源：[Issues](https://github.com/NousResearch/hermes-agent/issues?q=is%3Aissue+sort%3Acreated-desc)。
