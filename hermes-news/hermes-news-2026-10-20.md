# Hermes-Agent 每日情報 — 2026-10-20

> 資料來源涵蓋截至 2026-10-20 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **過去 24 小時無重大更新，最新穩定版仍為 v0.22.2**：GitHub Releases 標示日期為 2026-10-15，修正 v0.22.1 誤用錯誤 commit 建置的問題，以及受監管環境（systemd、launchd、Docker）重開機後 gateway 無法啟動的問題。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

2. **已知問題未變**：在 0.22.1 gateway 下執行過的 session，於 v0.22.2 無法刪除。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

3. **v0.22.2 之後仍有回報**：有使用者指出 Onboarding 的 Path import 在 Python 3.12 下仍然出錯（[#141803](https://github.com/NousResearch/hermes-agent/issues/141803)）；另有回報指出設定輔助 provider 時舊 provider 的 base URL 未被清除（[#141832](https://github.com/NousResearch/hermes-agent/issues/141832)，標示為安全類、P3）。

---

## 2. LLM 串接實戰回報

- Claude／GPT／Gemini：過去 24 小時未查到獨立的使用者實測回報。與 provider 相關的新 issue 有：ChatGPT OAuth 的圖片請求約 60 秒即斷線，即使已設定更長逾時（[#141807](https://github.com/NousResearch/hermes-agent/issues/141807)）；HTTP 400/404 錯誤後會無限 fallback 到不存在的模型（[#141820](https://github.com/NousResearch/hermes-agent/issues/141820)）；機器人聊天中的 `/model` 檢查使用過期的 provider 清單，導致較新的 provider 失敗（[#141826](https://github.com/NousResearch/hermes-agent/issues/141826)）。
- 本地模型：Linux CUDA 引擎建置需要較新的 glibc，舊版發行版無法安裝（[#141819](https://github.com/NousResearch/hermes-agent/issues/141819)，標示為上游成因）。

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 說明 |
|------|----------|------|
| **v0.22.2** | 2026-10-15 | **目前最新穩定版**；修正 gateway 重開機後無法啟動、讓長時間本地模型回合在筆電上保持運作；已知問題：0.22.1 gateway 建立的 session 無法刪除 |
| v0.22.1 | 2026-10-14 | 由錯誤 commit 建置，Microsoft Store 版可能卡在「Connecting」，已被 0.22.2 取代 |
| v0.22.0 | 2026-10-13 | 「The Threshold Release」；穩定 SemVer 通道、簽章桌面套件、llama.cpp 一鍵本地模型、約 600 項外掛目錄、Claude 5.5 與 GPT-6 模型支援 |
| v0.21.7 | 2026-10-11 | 約 204 項 PR 的彙整修補版；原始碼安裝預設更新至已標記版本 |

來源：[Releases](https://github.com/NousResearch/hermes-agent/releases)。

---

## 4. 社群討論與已知問題

2026-10-20 新增 issue（部分），優先度取自 GitHub 標籤：

| Issue | 說明 | 優先度 |
|------|------|------|
| [#141803](https://github.com/NousResearch/hermes-agent/issues/141803) | Onboarding 的 Path import 在 v0.22.2 後於 Python 3.12 仍出錯 | P2 |
| [#141813](https://github.com/NousResearch/hermes-agent/issues/141813) | 桌面版切換分頁時對話可能卡在「Loading session」 | P2 |
| [#141822](https://github.com/NousResearch/hermes-agent/issues/141822) | 真人訊息被內部背景程序喚醒訊息延遲 | P2 |
| [#141824](https://github.com/NousResearch/hermes-agent/issues/141824) | Discord 中總用量偏高，且委派任務交接不完整 | P2 |
| [#141833](https://github.com/NousResearch/hermes-agent/issues/141833) | 委派的子任務只重用 system prompt 快取，未重用歷史 | P2 |
| [#141806](https://github.com/NousResearch/hermes-agent/issues/141806) | 串流回覆略過圖片擷取，Discord 只顯示圖片替代文字 | P2 |
| [#141821](https://github.com/NousResearch/hermes-agent/issues/141821) | Slack 檔案說明文字未格式化，且提及未跳脫 | P2 |
| [#141832](https://github.com/NousResearch/hermes-agent/issues/141832) | `config set` 輔助 provider 時沿用舊 provider 的 base URL（安全類） | P3 |

來源：[Issues](https://github.com/NousResearch/hermes-agent/issues)。
