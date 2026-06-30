# OpenClaw 每日情報 — 2026-06-30

> 資料來源截止時間：2026-06-30 UTC｜搜尋範圍：過去 24 小時

---

## 1. 今日重點摘要

1. **v2026.6.10 穩定版仍有多處關鍵回退（regression）**：ClawStat.us 明確建議暫緩升級，受影響區域涵蓋 memory、模型切換、session 管理及 channel 通訊，官方已在 v2026.6.11-beta.1 進行分批修補，但尚未發布穩定版。

2. **v2026.6.11-beta.1 新功能搶先看**：Slack relay mode（中繼轉送模式）、native Mattermost `/oc_queue` 指令、以及 per-DM 模型覆寫（per-DM model override），讓 channel 自動化操作更靈活。

3. **最近 GitHub Issue＆PR 活動（6/29）**：
   - Issue #97655 由 Leonchen3352 於 2026-06-29 開立，優先級 P2，狀態 Open。
   - PR #97958（修正 Codex computer-use 文件）、#97955（memory-wiki 修正）、#97954（acp 修正）於 6/29 同日合併。

4. **TaskFlow 作為核心排程抽象層**：官方文件正式定義 TaskFlow 為「background tasks 之上的編排層」，支援持久化多步驟工作流並具備獨立狀態與版本追蹤，代表 OpenClaw 往企業級 agentic runtime 邁進。

5. **六家 provider 可在執行期熱切換**：4 月版本起支援 Provider Manifest，讓 GPT-5.5（Codex）、Claude API、Gemini、DeepSeek、OpenRouter、Ollama/LM Studio/Gemma 4 均可無需重建即可切換，已成 2026 年最受社群歡迎的功能之一。

6. **模型預設提案引發爭議（Issue #21897）**：社群討論是否將預設模型從 Claude Opus 4.6 改為 Gemini 3.1 Pro，目前仍屬開放討論，未有定案。

7. **工具呼叫基準測試（PinchBench）**：Claude Sonnet 4.6 以 86.9% 成功率領先，GPT-5.4 以 86.0% 緊追，Gemini 表現略遜。此數據成為社群選模主要依據。

8. **快速模式（fast mode）已進入穩定版**：v2026.6.10 正式納入自動快速模式，短對話優先快速回應，長任務或 fallback 才切回一般模式，且不影響 visible state。

9. **Codex partial delta 與 prompt-cache 穩定性改善**：長上下文場景下的 lost progress 與 inconsistent run 問題在近期版本已有顯著改善，對大型 coding agent 工作流影響正面。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）

- **建議配置**：Sonnet 4.6 用於大多數 agent 任務（工具呼叫、推理、通用）；Haiku 用於高量低成本任務（分類、路由）；Opus 留給最複雜推理場景。
- **工具呼叫穩定性**：PinchBench 測試中 Claude Sonnet 4.6 以 86.9% 排名第一，已成社群首選。
- **OAuth credential 管理**：OpenClaw 支援直接 Anthropic API Key，亦可透過 OpenRouter proxy 接入，文件推薦優先使用官方 API 以保留 refreshable credentials。

來源：[Best LLM for OpenClaw: Gemini 3.1 Pro vs GPT-5.5 vs Claude Opus 4.7 (2026) - DEV Community](https://dev.to/matthewrevell/best-llm-for-openclaw-gemini-31-pro-vs-gpt-55-vs-claude-opus-47-2026-3na4)｜[Model providers · OpenClaw](https://docs.openclaw.ai/concepts/model-providers)

### GPT（OpenAI）

- GPT-5.5 透過 Codex 端點串接，PinchBench 成功率 86.0%，緊追 Claude。
- 已是六家預設 provider 之一，可在 runtime 直接切換。

來源：[OpenClaw April 2026: 6 Model Providers You Can Now Swap at Runtime Without Rebuilding | MindStudio](https://www.mindstudio.ai/blog/openclaw-april-2026-model-agnostic-provider-manifest)

### Gemini（Google）

- **已知 bug**：OpenAI 相容模式（openai-compatibility mode）下，JSON Schema 欄位不相容導致圖像辨識失敗，需改用 native Gemini format 設定。
- **Gemini 3.1 Pro 爭議**：截至 2026-02，Google 公開模型列表仍只到 Gemini 2.5 Pro／2.5 Flash，「Gemini 3.1 Pro」來源與可用性有待確認，建議謹慎使用。
- **模型切換常見失敗點**：缺少 `baseUrl`、gateway 未重啟、session 快取舊模型、Docker 容器網路問題、模型名稱格式錯誤、Ollama 缺少 `api: "openai-responses"` 設定。

來源：[3 ways to solve OpenClaw Gemini image recognition failures](https://help.apiyi.com/en/openclaw-gemini-image-recognition-fix-openai-compatible-mode-guide-en.html)｜[feat(models): migrate default model from Claude Opus 4.6 to Gemini 3.1 Pro · Issue #21897](https://github.com/openclaw/openclaw/issues/21897)

### Rate Limit 修補

- PR #91911 已實作 rate limit retry 機制，對短 rate-limit 視窗內重試同一模型，已合併進 v2026.6.6。

來源：[Release openclaw 2026.6.6 · openclaw/openclaw](https://github.com/openclaw/openclaw/releases/tag/v2026.6.6)

---

## 3. 社群反應與討論亮點

- **「跳過 v2026.6.10」警告**：ClawStat.us 明確標示此版本有多個 critical regression，社群普遍建議等待 v2026.6.11 穩定版或回退至 v2026.6.9。（來源：[Should you update OpenClaw v2026.6.10? Skip this version — ClawStat.us](https://clawstat.us/)）

- **預設模型爭議**：Issue #21897 關於將預設模型從 Claude Opus 4.6 換成 Gemini 3.1 Pro 引發熱烈討論，反對者認為 Gemini 3.1 Pro 可用性不確定，且工具呼叫穩定性未經充分驗證。

- **Provider Manifest 正面評價**：4 月上線的熱切換 provider 功能受到企業用戶高度好評，多名社群成員表示已在生產環境部署並用於不同任務按 provider 路由。

- **GitHub + OpenClaw 整合文章熱傳**：多篇教學文章探討如何利用 OpenClaw 自動化 GitHub Issues 管理，為 agentic DevOps 工作流提供參考案例。（來源：[OpenClaw + GitHub: AI-Powered Code and Issue Management — The Dench Blog](https://www.dench.com/blog/openclaw-github-integration)）

---

## 4. 值得追蹤的後續議題

| 議題 | 說明 |
|------|------|
| **v2026.6.11 穩定版發布時程** | 目前 beta.1 修補 v2026.6.10 的 regression，穩定版預計近期發布，建議持續關注 [Releases · openclaw/openclaw](https://github.com/openclaw/openclaw/releases) |
| **預設模型決策（Issue #21897）** | Claude Opus 4.6 → Gemini 3.1 Pro 的切換提案，可能影響所有新安裝用戶的預設體驗 |
| **TaskFlow 文件完整化** | TaskFlow 編排層剛正式定義，實際使用案例與最佳實踐文件尚在建立中 |
| **Gemini 原生模式 bug fix** | JSON Schema 相容性問題影響圖像辨識，官方 fix PR 狀態待追蹤 |
| **Android 設定面板改善** | 近期版本強化 mobile 操作，後續 iOS 支援規劃尚未公開 |

---

*本報告依公開搜尋結果彙整，搜尋時間為 2026-06-30 UTC，資訊以各來源公開內容為準，不保證完整性。*
