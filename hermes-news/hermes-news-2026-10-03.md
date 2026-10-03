# Hermes-Agent 每日情報 — 2026-10-03

> 資料來源涵蓋截至 2026-10-03 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **過去 24 小時無新版本，目前穩定版仍為 v0.21.5（v2026.9.24）**：該版整併約 460 個 PR，包含 Desktop plugin SDK 擴充、法／德／西語目錄翻譯、自訂模型項目與效能優化。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

2. **Windows 平台問題集中出現**：[#131934](https://github.com/NousResearch/hermes-agent/issues/131934)（Desktop 開機卡 35 秒以上並出現後端連線逾時）、[#131884](https://github.com/NousResearch/hermes-agent/issues/131884)（`hermes update` 安裝 ffmpeg／agent-browser 時出現 WinError 5）、[#131864](https://github.com/NousResearch/hermes-agent/issues/131864)（更新清理會停掉無 unit 的 serve／dashboard 且無法重新啟動），皆為 P2。

3. **Desktop SDK 連續出現三則功能需求**：[#131943](https://github.com/NousResearch/hermes-agent/issues/131943)、[#131944](https://github.com/NousResearch/hermes-agent/issues/131944)、[#131945](https://github.com/NousResearch/hermes-agent/issues/131945)，分別針對側欄密度、輸入區呈現偏好與對話記錄密度的 host 端設定。

4. **NousCon 2026 訂於 10 月 30 日於紐約舉行**，依官方 X 貼文。（[X](https://x.com/NousResearch/status/2105043777706754213)）

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）
- 今日未查到新的實戰回報。

### GPT（OpenAI）
- 搜尋結果提到 Nous Research 與 OpenAI 合作，Nous Portal 支援「Sign in with ChatGPT」，可用 ChatGPT 方案於 Hermes Agent 使用；此為搜尋摘要所載，發布日期未能確認是否在 24 小時內。（[Nous Research](https://nousresearch.com/)）

### Gemini（Google）
- 今日未查到新的 Gemini 專屬回報。

### 其他供應商／本地模型
- [#131869](https://github.com/NousResearch/hermes-agent/issues/131869)（P2，local-models）：Windows 的 llamacpp-cuda 套件管理仍釘選 CUDA 13.3 資產，上游回傳 404。
- [#131947](https://github.com/NousResearch/hermes-agent/issues/131947)：Desktop 回報長時間「Summarizing thread」、循環無結果，以及切換 reasoning 時逾時。

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 類型 | 重點 |
|------|----------|------|------|
| **v0.21.5（v2026.9.24）** | 2026-09-24 | Patch rollup | **目前最新穩定版**；約 460 PR、Desktop plugin SDK、法／德／西語翻譯、效能優化 |
| v0.21.4（v2026.9.21） | 2026-09-21 | Patch rollup | 約 1,800 PR；host-wide gateway singleton lock、結構化 JSONL CLI 輸出、多個社群外掛 |
| v0.21.3（v2026.9.14） | 2026-09-14 | Patch rollup | 遠端 gateway 登入問題修復、state.db writer handle 洩漏修復 |

來源：[Releases](https://github.com/NousResearch/hermes-agent/releases)。過去 24 小時無新版本。

---

## 4. 社群討論與已知問題

| Issue | 說明 | 優先度 |
|------|------|------|
| [#131934](https://github.com/NousResearch/hermes-agent/issues/131934) | Windows Desktop 開機卡 35 秒以上，後端連線逾時（needs-repro） | P2 |
| [#131924](https://github.com/NousResearch/hermes-agent/issues/131924) | `hermes send`／獨立 Telegram 傳送忽略 notifications 設定 | P2 |
| [#131884](https://github.com/NousResearch/hermes-agent/issues/131884) | Windows `hermes update` 安裝 ffmpeg／agent-browser 失敗（WinError 5） | P2 |
| [#131875](https://github.com/NousResearch/hermes-agent/issues/131875) | macOS launchd 下 `hermes gateway restart` 可能讓 gateway 停擺 | P2 |
| [#131869](https://github.com/NousResearch/hermes-agent/issues/131869) | llamacpp-cuda win32-x64 仍指向 CUDA 13.3（上游 404） | P2 |
| [#131864](https://github.com/NousResearch/hermes-agent/issues/131864) | Windows 更新清理停掉 serve／dashboard 且無法重啟 | P2 |
| [#131863](https://github.com/NousResearch/hermes-agent/issues/131863) | cron job 被封存後不會重新建立 | P3 |
| [#131876](https://github.com/NousResearch/hermes-agent/issues/131876) | 兩個經典 CLI 核准提示不會觸發 hooks | P3 |

以上來自 GitHub issue 列表最新約 15 則中的部分，未涵蓋全部；未查到新的官方公告。

---

*報告生成時間：2026-10-03 UTC*
