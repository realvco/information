# OpenClaw 每日情報 — 2026-07-20

> 資料來源涵蓋截至 2026-07-20 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **v2026.7.2-beta.2 搶先釋出**：最新 beta 於 2026-07-17 釋出，帶入「遠端 Coding Session」功能，Control UI sessions 可在雲端 worker 上運行，並可在擁有主機的終端機直接開啟 Codex/Claude catalog sessions。此版本標記為 breaking changes，建議開發者仔細閱讀遷移說明。（[GitHub Release](https://github.com/openclaw/openclaw/releases/tag/v2026.7.2-beta.2)）

2. **穩定版 v2026.7.1 帶入 532 位貢獻者的 3,063 次提交**：這是近期最大規模的穩定版本，囊括 Control UI 全面翻新、iOS/Android/macOS 官方應用大更新、GPT-5.6 相容性支援、Tencent Hy3、Meta Muse Spark 1.1 等新模型，以及強化的 Codex 與 coding-agent 工作流。（[Release Notes](https://github.com/openclaw/openclaw/releases/tag/v2026.7.1)）

3. **Control UI 大改版**：對話管理更直覺，支援側邊並排顯示，新增即時 Tasks、清晰的 chat controls、費用視圖、多 session 並排可調整大小面板，reload 後狀態自動恢復。

4. **`openclaw attach` 指令**：允許將 Claude Code 暫時接入選定的 OpenClaw session，強化與外部 coding agent 的協作，Codex 委派與原生子代理回傳結果更為可靠。

5. **移動端 Automation 新功能**：Android 新增前景 Voice Wake，Telegram durable-ingress 在重啟後不再遺失，Slack progress indicator 改為輪播訊息，插件安裝新增 `--force` 安全確認機制。

6. **Claude CLI max-turn 診斷強化**：新增診斷工具以保全終端機的 max-turn 結果，對可能產生副作用的操作發出 auth profile 或 model replay 警告，防止誤操作。

7. **已知假陽性 Rate Limit Bug 仍受關注**：GitHub issue [#32828](https://github.com/openclaw/openclaw/issues/32828) 記錄了即使底層 API 正常運作，OpenClaw 仍對所有模型顯示「API rate limit reached」的問題，目前狀態仍在追蹤中。

8. **Provider 網路重試對齊**：v2026.7.2-beta.2 統一了 provider read/poll/download 與 agent-wait 在暫時性連線錯誤時的恢復行為，提升大型 agent workflow 的穩定性。

9. **社群規模持續成長**：X (Twitter) 上的 OpenClaw Community 已達 34.6K 成員，官方 Discord「Friends of Clawd」持續活躍，為使用者提供設定分享與問題解答的交流空間。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）
- **表現最佳**：根據 PinchBench OpenClaw Benchmark，Claude Sonnet 4.6 以 **86.9%** 的成功率領跑所有模型，為 OpenClaw 複雜工作流的首選。（[來源](https://nek12.dev/blog/en/openclaw-review-2026-testing-gpt-gemini-opus-minimax/)）
- **OAuth Rate Limit 問題**：使用 ChatGPT Plus 等 OAuth token 串接的用戶反映，相較於直接 API Key，rate limit 更嚴格且更容易觸發，出現「API rate limit reached. Please try again later.」錯誤。建議改用直接 API Key 並以 `openclaw models auth setup-token` 重設。（[AnswerOverflow 討論](https://www.answeroverflow.com/m/1478788645472436264)）
- **Codex 整合強化**：v2026.7.1 進一步穩定 Codex delegation 與 native subagent 的結果回傳，claude Code 可透過 `openclaw attach` 短暫接管 session。

### GPT（OpenAI）
- GPT-5.6 為新安裝預設模型，兼容性已於 v2026.7.1 全面修正。PinchBench 上 GPT-5.4 以 86.0% 緊追 Claude。（[來源](https://cowork.ink/blog/best-model-for-openclaw/)）
- 對於複雜工作流，Claude 與 GPT 被社群評為最安全的選擇。

### Gemini（Google）
- **實質上不被支援**：官方文件與社群均確認，Gemini 模型在 OpenClaw 中缺乏 caching、embeddings 與 thinking 支援，實際使用限制較多。若需使用 Gemini，建議透過相容 proxy 接入。（[Model Providers 文件](https://docs.openclaw.ai/concepts/model-providers)）

### Ollama / 本地模型
- 可透過 baseUrl 設定接入 Ollama，但模型切換後需重啟 gateway，且 session 快取可能保留舊模型設定，需手動清除。（[來源](https://amjid.au/insights/using-openclaw-with-claude-gpt-gemini-and-ollama/)）

### ClawRouter（新增）
- v2026.7.1 新增 ClawRouter 作為內建 routing provider，支援跨多個上游 provider 進行負載分散與 fallback。

---

## 3. 社群反應與討論亮點

- **X 社群高度正向**：多位用戶在 [@openclaw](https://x.com/openclaw) 下分享使用心得，代表性評價包括「使用一週，感覺像 early AGI」、「它幫我檢查、整理、提醒，已成為日常必需品」。（[Shoutouts](https://openclaw.ai/shoutouts/)）
- **False Rate Limit 討論熱度高**：GitHub [#32828](https://github.com/openclaw/openclaw/issues/32828) 在社群中被廣泛轉發，反映 OpenClaw 自身的 rate limit 偵測邏輯存在誤判，建議執行 `openclaw doctor --fix` 作為初步排查步驟。
- **Control UI 改版評價兩極**：部分用戶稱讚多 session 並排的工作效率提升，也有用戶反映遷移初期的學習曲線。Discord 上有頻繁的設定分享討論。
- **v2026.7.2-beta.2 破壞性變更**：beta 通道用戶需特別注意 plugin install 現在需要明確的 `--force`，以及遠端 coding session 的架構變更。

---

## 4. 值得追蹤的後續議題

| 議題 | 說明 | 追蹤連結 |
|------|------|----------|
| GitHub #32828 假陽性 Rate Limit | 預計在 v2026.7.2 穩定版修復 | [連結](https://github.com/openclaw/openclaw/issues/32828) |
| v2026.7.2 穩定版釋出時程 | beta.2 已於 7/17 出，穩定版預計近期跟進 | [Releases](https://github.com/openclaw/openclaw/releases) |
| Gemini 支援改善計畫 | 社群持續要求官方改善 caching/embeddings 支援 | [Model Providers 文件](https://docs.openclaw.ai/concepts/model-providers) |
| ClawRouter 穩定性驗證 | 新功能 ClawRouter 實際生產環境表現有待觀察 | [Release Notes](https://github.com/openclaw/openclaw/releases/tag/v2026.7.1) |
| 遠端 Coding Session 成熟度 | beta.2 的核心新功能，待穩定版確認可靠性 | [Release](https://github.com/openclaw/openclaw/releases/tag/v2026.7.2-beta.2) |

---

*報告生成時間：2026-07-20 UTC*
