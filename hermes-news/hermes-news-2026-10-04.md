# Hermes-Agent 每日情報 — 2026-10-04

> 資料來源涵蓋截至 2026-10-04 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **過去 24 小時無新版本，目前穩定版仍為 v0.21.5（v2026.9.24）**：該版整併約 460 個 PR，包含 Desktop plugin SDK 擴充、法／德／西語目錄翻譯與效能優化。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

2. **出現一則 P1 問題：OpenRouter 回傳 403「prompt injection patterns detected」**：內建 skills 若含 tool 標籤，可能被 OpenRouter 判定為 prompt injection 而擋下請求。（[#132504](https://github.com/NousResearch/hermes-agent/issues/132504)）

3. **Telegram 與多工 gateway 相關 P2 問題增加**：Telegram DM topic 在 adapter 重建時產生重複項目（[#132522](https://github.com/NousResearch/hermes-agent/issues/132522)）、exec-approval 提示以靜音方式送出（[#132516](https://github.com/NousResearch/hermes-agent/issues/132516)）、multiplexed gateway 未參照 served profile 自己的 quick_commands（[#132517](https://github.com/NousResearch/hermes-agent/issues/132517)）。

4. **中文環境安裝問題**：[#132532](https://github.com/NousResearch/hermes-agent/issues/132532) 提出針對 GitHub 受限網路（中國大陸）的安裝器強化需求；[#132531](https://github.com/NousResearch/hermes-agent/issues/132531) 回報繁中／簡中語系 Windows 上安裝記錄檔的非 ASCII 輸出全部亂碼。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）
- 今日未查到新的實戰回報。

### GPT（OpenAI）
- 今日未查到新的 GPT 專屬回報。（搜尋結果仍提到 Nous Portal 支援「Sign in with ChatGPT」，但發布時間不在 24 小時內，不列為新動態。來源：[Nous Research](https://nousresearch.com/)）

### Gemini（Google）
- 今日未查到新的 Gemini 專屬回報。

### 其他供應商／本地模型
- OpenRouter：[#132504](https://github.com/NousResearch/hermes-agent/issues/132504)（P1，provider/openrouter）如上所述，bundled skills 內含 tool 標籤觸發 403。
- 設定檢查：[#132536](https://github.com/NousResearch/hermes-agent/issues/132536) 回報 `hermes doctor` 把文件記載的 auxiliary provider `main` 判為無法解析。

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 類型 | 重點 |
|------|----------|------|------|
| **v0.21.5（v2026.9.24）** | 2026-09-24 | Patch rollup | **目前最新穩定版**；約 460 PR、Desktop plugin SDK、法／德／西語翻譯、效能優化 |
| v0.21.4（v2026.9.21） | 2026-09-21 | Patch rollup | 約 1,800 PR；host-wide gateway 改進、結構化 CLI 輸出、新增模型與多個社群外掛 |
| v0.21.3（v2026.9.14） | 2026-09-14 | Patch rollup | 遠端 gateway 登入修復、state.db 可靠性改善、MCP OAuth 強化 |

來源：[Releases](https://github.com/NousResearch/hermes-agent/releases)。過去 24 小時無新版本。

---

## 4. 社群討論與已知問題

| Issue | 說明 | 優先度 |
|------|------|------|
| [#132504](https://github.com/NousResearch/hermes-agent/issues/132504) | OpenRouter 403：bundled skills 含 tool 標籤被判定為 prompt injection | P1 |
| [#132522](https://github.com/NousResearch/hermes-agent/issues/132522) | Telegram DM topics 在 adapter 重建時產生重複 | P2 |
| [#132517](https://github.com/NousResearch/hermes-agent/issues/132517) | multiplexed gateway 未參照 served profile 的 quick_commands | P2 |
| [#132516](https://github.com/NousResearch/hermes-agent/issues/132516) | exec-approval 提示在 Telegram 以 disable_notification 送出 | P2 |
| [#132515](https://github.com/NousResearch/hermes-agent/issues/132515) | managed scope 設定的 `skills.external_dirs` 被 skill discovery 忽略（標為 duplicate） | P2 |
| [#132508](https://github.com/NousResearch/hermes-agent/issues/132508) | Desktop SSH 連線與 forward 時限固定 15 秒且無法覆寫 | P2 |
| [#132541](https://github.com/NousResearch/hermes-agent/issues/132541) | `browser_vault_fill` 無法填入跨來源付款 iframe 內的卡號／CVV | 未標示 |
| [#132542](https://github.com/NousResearch/hermes-agent/issues/132542) | served profile 的 sessions 區塊缺少 auto_archive／auto_prune 時，housekeeping 靜默不執行 | 未標示 |

以上來自 GitHub issue 列表最新約 15 則中的部分，未涵蓋全部；未查到新的官方公告。社群面可見的背景：專案 GitHub star 數據稱約 25 萬（第三方彙整，未經官方證實，[來源](https://hermesatlas.com/guide/)）；NousCon 2026 訂於 10 月 30 日在紐約舉行（沿用前日已引用之[官方 X 貼文](https://x.com/NousResearch/status/2105043777706754213)）。

---

*報告生成時間：2026-10-04 UTC*
