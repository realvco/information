# Hermes-Agent 每日情報 — 2026-11-20

> 資料來源涵蓋截至 2026-11-20 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **新穩定版 v0.23.3 發布**：2026-11-20 00:34 UTC 發布的 patch release，彙整約 370 個已合併 PR；完整整理版說明仍延到 v0.24.0。（[v0.23.3](https://github.com/NousResearch/hermes-agent/releases/tag/v0.23.3)）

2. **提示快取（prompt cache）生命週期控制器上線**：含長時間工具執行期間的快取預熱、快取到期前壓縮閒置對話，並新增 `prompt_caching.cache_ttl` 設定（預設 `auto`）；壓縮時會封存被捨棄的工具結果，主動修剪工具結果預設開啟。（[v0.23.3](https://github.com/NousResearch/hermes-agent/releases/tag/v0.23.3)）

3. **GPT-6.2 Astra／Sol（含 Pro）於 OpenRouter 與 Nous Portal 可用**，另有一題式的首次 CLI 設定（含免費方案）。（[v0.23.3](https://github.com/NousResearch/hermes-agent/releases/tag/v0.23.3)）

---

## 2. LLM 串接實戰回報

- **GPT**：v0.23.3 新增 GPT-6.2 Astra／Sol（及 Pro）經 OpenRouter 與 Nous Portal 的支援，屬 release 說明，尚未查到獨立的使用者實測回報。（[v0.23.3](https://github.com/NousResearch/hermes-agent/releases/tag/v0.23.3)）
- **本地模型**（使用者回報，尚待維護者確認）：[#158051](https://github.com/NousResearch/hermes-agent/issues/158051) 指出小視窗本地模型的 tool schema 約占全新 prompt 的 77%；[#158050](https://github.com/NousResearch/hermes-agent/issues/158050) 指出本地模型在 write_file 後常重送剛寫入的檔案（約 38–48% 的寫入）。
- **Claude／Gemini**：過去 24 小時未查到獨立實測回報，不予編造。

---

## 3. 版本與 release 動態

| 版本 | 發布時間（UTC） | 說明 |
|------|----------|------|
| **v0.23.3** | 2026-11-20 | 目前最新穩定版；約 370 個 PR；提示快取控制器、壓縮封存、SQLite 儲存層拆分、「Waiting on teammates」提示條、新 plugin 目錄項目；依賴升級 oauthlib、opentelemetry、tornado |
| rc.1-v0.23.3 | 2026-11-19 | 候選版，feed 內無可讀說明 |
| v0.23.2+canary.20261119T064637Z | 2026-11-19 | canary；plugin 目錄新增、壓縮與提示快取調整、gateway 與搜尋修正 |

更新方式：Microsoft Store 版經 Store 更新；CLI 執行 `hermes update`；Docker 拉取 `nousresearch/hermes-agent:stable`／`:latest`；桌面安裝檔（.exe/.dmg）此版無變動，隨下個 bundled release 更新。

來源：[releases feed](https://github.com/NousResearch/hermes-agent/releases.atom)、[v0.23.3](https://github.com/NousResearch/hermes-agent/releases/tag/v0.23.3)。

---

## 4. 社群討論與已知問題

新增 issue（皆為使用者回報或提案，是否成立尚待維護者確認）：

| Issue | 說明 |
|------|------|
| [#158072](https://github.com/NousResearch/hermes-agent/issues/158072) | Telegram 備援傳輸在 HTTP 502／ReadTimeout 時不切換，bot 離線約一小時 |
| [#158065](https://github.com/NousResearch/hermes-agent/issues/158065) | 啟用 dashboard.turn_isolation 時，Desktop 對話失去所有 MCP 工具 |
| [#158066](https://github.com/NousResearch/hermes-agent/issues/158066) | WSL2 互動 CLI 底部輸入框重繪時閃爍 |
| [#158064](https://github.com/NousResearch/hermes-agent/issues/158064) | 無法在 Python 3.15 下完成安裝 |
| [#158059](https://github.com/NousResearch/hermes-agent/issues/158059) | Desktop 從遠端 gateway bot 切到本機 bot 時，檔案側欄顯示 ENOENT |
| [#158054](https://github.com/NousResearch/hermes-agent/issues/158054) | Desktop 從 Home 開新對話時，把側欄區段 token 當成 cwd 傳送 |
| [#158047](https://github.com/NousResearch/hermes-agent/issues/158047) | CLI 啟動需 6–10 秒 |
| [#158043](https://github.com/NousResearch/hermes-agent/issues/158043) | gateway 背景程序完成通知被截成 0 字元 |
| [#158040](https://github.com/NousResearch/hermes-agent/issues/158040) | 文件 hooks.md 對 pre_llm_call 注入位置（append／prepend）前後矛盾 |

來源：[Issues](https://github.com/NousResearch/hermes-agent/issues?q=is%3Aissue+sort%3Acreated-desc)。
