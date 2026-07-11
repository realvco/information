# OpenClaw 每日情報 — 2026-07-11

> 資料來源：GitHub、社群論壇、部落格文章；涵蓋範圍以過去 24 小時內可索引的公開資訊為主。

---

## 1. 今日重點摘要

1. **Beta 持續推進**：OpenClaw 最新 beta 版為 `v2026.7.1-beta.3`（7 月 9 日更新），距正式 stable 發布尚未有明確時間表，主倉庫與 docs 均在 7 月 11 日有提交紀錄，開發節奏活躍。
2. **GPT-5.6 支援上線**：本次 beta 系列新增對 OpenAI GPT-5.6 的支援，model picker 可在 catalog、capability 與 runtime 三個層面無縫切換，毋需重建 workflow。
3. **多項 CLI / 路由修復**：7 月 10 日合入多個高優先 PR，含 `#103910`（refresh lock 洩漏修復）、`#103961`（LLM input/output hook 升級）、`#103960`（MCP disabled servers 修復）、`#103970`（macOS exec 授權撤銷安全修復）。
4. **GitHub Issues 活躍**：7 月 10 日新開 Issues #103602、#103599、#103590、#103588、#103587，多數被標記 bug，已有 maintainer 接手 PR。
5. **Gemini 相容性問題仍未完全解決**：Google Gemini 的 image recognition 在 OpenAI-compatible mode 下，因 JSON Schema 欄位格式不一致仍有已知失敗；cache、embeddings、thinking 功能目前支援程度有限，官方尚未給出完整時間表。
6. **Telegram Codex 整合**：Telegram 可透過 `/login` 觸發 Codex pairing，可引導進行中的 Codex run 並在暫時性 API 失敗後恢復最終回應。
7. **ClawHub 強制簽名安全政策持續推行**：自 2026.3.22 起，所有 plugin 必須通過 ClawHub registry 安裝，sideload 任意 JS 不再受支援，強制要求 signed manifest。
8. **社群規模**：GitHub 累積 Star 數截至 4 月統計達 347,000，為該平台史上最多星專案；主社群討論集中於 r/openclaw 與 r/ClaudeAI。
9. **下一個值得追蹤的里程碑**：`v2026.7.1` stable 版何時從 beta 轉正，以及 Gemini 完整支援路線圖。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）

- OpenClaw 對 Claude 的支援來自 ClaudeAI 生態系，官方文件提供 OAuth 與 API key 兩種認證路徑。
- 目前無過去 24 小時內的新踩坑報告；社群普遍認為 Claude 是 OpenClaw 最穩定的 provider 選項。
- 來源：[Using OpenClaw with Claude, GPT, Gemini, and Ollama — amjid.au](https://amjid.au/insights/using-openclaw-with-claude-gpt-gemini-and-ollama/)

### GPT（OpenAI）

- GPT-5.6 本輪 beta 正式上線，是本月最大 provider 更新；model picker 可運行時切換，不需重建 workflow。
- 來源：[OpenClaw Release Notes - July 2026 — Releasebot](https://releasebot.io/updates/openclaw)

### Gemini（Google）

- **已知問題**：OpenAI-compatible mode 下，tool schema 格式與 Gemini 要求不符，導致 image recognition 呼叫失敗。
- 根因為 JSON Schema 欄位的不相容，官方已記錄但尚未修復；cache、embeddings、thinking 功能亦未完全支援。
- **Gemini API Key 輪換 bug 已修復**：本輪 beta 修了 LLM request 時的 Gemini API key rotation 問題，以及 Google OAuth token error response 的 bounded read 問題。
- 來源：[OpenClaw Gemini image recognition fix guide — apiyi.com](https://help.apiyi.com/en/openclaw-gemini-image-recognition-fix-openai-compatible-mode-guide-en.html)

### DeepSeek / Ollama / Gemma 4

- Provider manifest 已支援 DeepSeek、Ollama、Gemma 4 在 runtime 層級切換，本次 beta 無新的踩坑報告。
- Ollama inference node 現支援 auto-discover，Control UI 新增 session-first navigation 與 reasoning controls。
- 來源：[OpenClaw April 2026: 6 Model Providers — MindStudio](https://www.mindstudio.ai/blog/openclaw-april-2026-model-agnostic-provider-manifest)

### 效能與穩定性比較（社群觀察）

| Provider | 穩定性 | 備註 |
|---|---|---|
| Claude | 高 | 最早深度整合，社群首推 |
| GPT-5.6 | 高（新） | 剛上線，尚無大量回報 |
| Gemini | 中低 | 相容性問題待解 |
| Ollama / 本地 | 中 | auto-discover 改善體驗 |

---

## 3. 社群反應與討論亮點

- **r/openclaw**：以 release notes、setup 幫助、show-and-tell 為主；友善、動手派氛圍，同樣幾個活躍 ID 在解答問題。
- **r/ClaudeAI**：因 OpenClaw 源自 Claude Code 生態系，此為最大討論聚集地；討論涵蓋 Claude 作為 default provider 的評價與成本分析。
- **r/selfhosted**：著重安裝、基礎設施與本地 Ollama 部署問題。
- **社群整體觀感**：部分使用者對成本（特別是雲端 API 費用）、設定複雜度、vendor lock-in 持保留態度；另有使用者高度滿意工作流自動化效果。
- **ClawHub registry 政策**：部分開發者對強制中心化分發有意見，擔心影響自訂 plugin 靈活度；官方以安全性與可驗證性作為理由。
- 來源：[OpenClaw Reddit — ClawMage](https://clawmage.ai/blog/openclaw-reddit/)

---

## 4. 值得追蹤的後續議題

1. **`v2026.7.1` stable 版何時發布**：目前停在 beta.3，觀察是否有顯著 bug 延後正式版。
2. **Gemini 完整支援路線圖**：image recognition、cache、embeddings、thinking 功能的修復時間表尚未公開。
3. **外部 harness attach 流程**：beta 系列引入的 external harness attach 屬全新架構，值得觀察穩定後的使用方式與文件。
4. **Event-driven cron runs**：本次 beta 新增的事件驅動排程，是否帶來穩定性或資源消耗問題有待社群驗證。
5. **macOS exec 授權安全修復（#103970）**：影響範圍與升級必要性值得追蹤，特別是 macOS 使用者。

---

*本報告依據公開搜尋結果彙整，不含臆測或補充資訊。如需查閱原始 Issue / PR，請至 [github.com/openclaw/openclaw](https://github.com/openclaw/openclaw)。*
