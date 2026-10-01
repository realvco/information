# Hermes-Agent 每日情報 — 2026-10-01

> 資料來源涵蓋截至 2026-10-01 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **目前穩定版仍為 v0.21.5（v2026.9.24）**：過去 24 小時查無新版本。該版整併約 460 個 PR，包含 Desktop plugin SDK 一批更新、法／德／西語目錄翻譯與效能優化。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

2. **Nous Portal 支援「Sign in with ChatGPT」**：官方宣布與 OpenAI 合作，用戶可用 ChatGPT 登入 Nous Portal，在 Hermes Agent 中使用自己的 ChatGPT 方案額度，並可在 ChatGPT 設定中檢視與控制。（[Nous Research 官方貼文](https://x.com/NousResearch/status/2104996715501904173)；相關 feature issue [#128880](https://github.com/NousResearch/hermes-agent/issues/128880)；社群示範 [tonbi 貼文](https://x.com/tonbistudio/status/2105010298352799879)）

3. **Hermes Desktop 即時串流 bot 工作階段畫面**：據搜尋結果摘要，Desktop 可即時觀看 bot 操作瀏覽器、終端機等，並可隨時接手再交還。此項僅見於搜尋摘要，未能直接開啟原貼文核對，視為未完全證實。

4. **10/1 新開 issue 以 Desktop、gateway 多 bot、檔案工具的 P2/P3 錯誤為主**，詳見第 4 節。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）
- 今日未查到新的 Anthropic 專屬回報。

### GPT（OpenAI）
- Sign in with ChatGPT 整合：見上方摘要第 2 點（[官方貼文](https://x.com/NousResearch/status/2104996715501904173)、[#128880](https://github.com/NousResearch/hermes-agent/issues/128880)）。

### Gemini（Google）
- [#129878](https://github.com/NousResearch/hermes-agent/issues/129878)（P2）：Gemini native streaming 會靜默接受串流中內嵌的 API 錯誤。

### 本地模型／Local Runtime
- 今日未查到新的本地模型專屬回報。

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 類型 | 重點 |
|------|----------|------|------|
| **v0.21.5（v2026.9.24）** | 2026-09-24 | Patch rollup | **目前最新穩定版**；約 460 PR、Desktop plugin SDK、法／德／西語翻譯、效能優化 |
| v0.21.4（v2026.9.21） | 2026-09-21 | Patch rollup | host-wide gateway singleton lock、CLI 結構化 JSON 輸出、skill 自動載入、LTX 2.5／Kling O3 |
| v0.21.3（v2026.9.14） | 2026-09-14 | Patch rollup | 遠端 dashboard 工作階段修復、state.db writer handle 洩漏修復 |

來源：[Releases](https://github.com/NousResearch/hermes-agent/releases)。過去 24 小時無新版本。

---

## 4. 社群討論與已知問題

| Issue | 說明 | 優先度 |
|------|------|------|
| [#129878](https://github.com/NousResearch/hermes-agent/issues/129878) | Gemini native streaming 靜默接受內嵌 API 錯誤 | P2 |
| [#129886](https://github.com/NousResearch/hermes-agent/issues/129886) | 路由式重啟確認選到 runtime bot 而非接收訊息的 bot | P2 |
| [#129884](https://github.com/NousResearch/hermes-agent/issues/129884) | 一個 Telegram bot 的重啟標記抑制另一個 bot 的重啟 | P2 |
| [#129880](https://github.com/NousResearch/hermes-agent/issues/129880) | skill_view 對同名但不同的 skill 回傳未變更的 stub | P2 |
| [#129858](https://github.com/NousResearch/hermes-agent/issues/129858) | cron Bot Chat CLI fallback 接管已恢復的 Desktop 對話 | P2 |
| [#129888](https://github.com/NousResearch/hermes-agent/issues/129888) | 含非 BMP 字元檔名的 UTF-16 檔案刪除／讀取失敗 | P2 |
| [#129900](https://github.com/NousResearch/hermes-agent/issues/129900) | 取消圖片準備後遺留暫存檔 | P2 |
| [#129843](https://github.com/NousResearch/hermes-agent/issues/129843) | Desktop 因自身喇叭播放誤觸 barge-in 而截斷回覆 | P2 |
| [#129902](https://github.com/NousResearch/hermes-agent/issues/129902) | 較舊的目錄讀取結果覆蓋已刷新的 Desktop 檔案樹 | P3 |
| [#129861](https://github.com/NousResearch/hermes-agent/issues/129861) | 含點號的外掛設定顯示預設值而非已存值 | P3 |
| [#129819](https://github.com/NousResearch/hermes-agent/issues/129819) | 喚醒詞一律在最左側／主要對話啟動語音 | P3 |

以上來自 GitHub issue 列表，僅列最新約 12 則，未涵蓋全部；昨日追蹤項目未逐一重新查核。

---

*報告生成時間：2026-10-01 UTC*
