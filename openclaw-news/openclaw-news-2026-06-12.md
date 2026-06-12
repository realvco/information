# OpenClaw 每日情報 — 2026-06-12

> 資料搜尋時間範圍：過去 24 小時（UTC 基準），結合最新公開版本資訊彙整。

---

## 1. 今日重點摘要

1. **最新穩定版 2026.6.5 已於 2026-06-05 釋出**，本週無新版本發布；過去 24 小時 GitHub releases 頁面無新 tag 出現，社群主要在消化此版本的變動。
2. **QQBot 防洩漏修正**：2026.6.5 起，QQBot 在送出訊息前自動剝除模型的 reasoning/thinking 標籤，僅保留最終答案，防止內部推理過程直接曝光給頻道使用者。
3. **Plugin/Skill 安裝安全強化**：插件與 skill 安裝現採用 operator install policy，取代舊有的 dangerous-code 掃描路徑；CLI、ClawHub 及 marketplace 安裝介面均已同步更新。
4. **MCP/Provider 相容性改善**：Agents/MCP/providers 在呼叫 provider converter 前，會強制將非 text/image 的 MCP tool-result block 轉換為文字，避免產生格式異常的 image block。
5. **SQLite 遷移延期**：session-metadata 的 SQLite 遷移已從 2026.6.5 beta 列車中延後，現有 JSON-backed 路徑繼續沿用，官方表示遷移風險仍在評估中。
6. **6 大 provider 支援（可熱換）**：自 2026 年 4 月的 provider manifest 機制上線後，目前支援 GPT-5.5（Codex）、Claude API、Gemini、DeepSeek、OpenRouter、Ollama/LM Studio、Gemma 4，可在執行中工作流直接切換，無需重新建置。
7. **Parallel Web Search 內建**：2026.6.5 版本已將 Parallel Web Search 捆綁為原生功能，過去需手動配置的外部 search provider 步驟已簡化。
8. **平台依賴維護**：Android、Swift/macOS、Docker、CodeQL、Buildx 等 CI/CD 依賴已同步更新至最新版本。
9. **6 月 beta 預覽（2026.6.2-beta.1）**：beta 版本已先行測試本版安全性策略，目前 beta 通道沒有尚未合併至穩定版的重大新功能公告。

---

## 2. LLM 串接實戰回報

### Gemini — 免費額度受歡迎，但有限速坑

- Gemini 在 OpenClaw 中以「免費層英雄」著稱：免費 dev 額度為 60 RPM + 1,000 RPD，適合個人測試，但長時間生產工作流容易觸頂。
- 環境變數 `GEMINI_API_KEY` 與 `GOOGLE_API_KEY` 皆被接受（向下相容舊設定），但建議新安裝統一使用 `GEMINI_API_KEY` 以符合最新文件。
- **來源**：[Google (Gemini) · OpenClaw Docs](https://docs.openclaw.ai/providers/google)、[Using OpenClaw with Claude, GPT, Gemini, and Ollama](https://amjid.au/insights/using-openclaw-with-claude-gpt-gemini-and-ollama/)

### Claude — Provider 最穩定，但需注意 MCP 恢復問題

- 2026.6.5 更新說明中特別提及「cleaner MCP and Anthropic recovery」，指出此前 Claude provider 在 MCP 工具呼叫異常後偶發無法自動恢復的問題，已在本版修復。
- 搭配 `openclaw onboard` 精靈可快速完成 Anthropic API Key 設定。
- **來源**：[OpenClaw Release Notes - June 2026](https://releasebot.io/updates/openclaw)

### GPT-5.5 / OpenRouter — 模型切換相容性

- 2026 年 4 月起的 provider manifest 機制讓使用者可在 GPT-5.5（Codex）、OpenRouter 代理等 provider 間熱換，無需重啟 agent 或重建工作流。
- 社群有討論 GPT-5.5 在長工作流中的 token 消耗顯著高於 Gemini/DeepSeek，建議評估成本效益。
- **來源**：[OpenClaw April 2026: 6 Model Providers You Can Now Swap at Runtime](https://www.mindstudio.ai/blog/openclaw-april-2026-model-agnostic-provider-manifest)

### 最佳 LLM 比較（2026 年）

- 有社群文章對 Gemini 3.1 Pro vs GPT-5.5 vs Claude Opus 4.7 在 OpenClaw 中的表現進行比較分析。
- **來源**：[Best LLM for OpenClaw: Gemini 3.1 Pro vs GPT-5.5 vs Claude Opus 4.7 (2026)](https://dev.to/matthewrevell/best-llm-for-openclaw-gemini-31-pro-vs-gpt-55-vs-claude-opus-47-2026-3na4)

---

## 3. 社群反應與討論亮點

- **QQBot 修正獲正面回響**：thinking tag 洩漏問題困擾部分使用者已久，此次修正在 Discord 獲得明顯正面反應。
- **Plugin 安裝政策討論**：operator install policy 的引入讓企業部署場景的安全性提升，但部分個人用戶反映需額外了解 ClawHub 的 marketplace 安裝流程。
- **SQLite 遷移延後的不確定性**：有社群成員關注 session metadata 遷移何時正式推出，官方尚未給出明確時程。
- **OpenClaw-RL 子專案受關注**：[Gen-Verse/OpenClaw-RL](https://github.com/Gen-Verse/OpenClaw-RL) 提出「透過對話訓練任何 agent」的概念，近期在社群引起討論，但與主倉庫為獨立專案。
- **社群渠道**：r/OpenClaw、r/Clawdbot（Reddit），以及官方 Discord（[github.com/openclaw/community](https://github.com/openclaw/community)）。

---

## 4. 值得追蹤的後續議題

| 議題 | 狀態 | 重要程度 |
|------|------|----------|
| SQLite session-metadata 遷移的正式時程 | 暫緩，等待風險評估 | 高 |
| 2026.6.x 下一個 patch 或 2026.7 路線圖 | 未公告 | 中 |
| Provider manifest 熱換功能的穩定性反饋 | 社群持續回報中 | 中 |
| OpenClaw-RL 子專案與主倉庫的關係釐清 | 社群關注 | 低-中 |
| Gemini 免費層限速在長工作流中的應對策略 | 文件未更新 | 中 |

---

*資料來源：*
- [GitHub Releases · openclaw/openclaw](https://github.com/openclaw/openclaw/releases)
- [Release openclaw 2026.6.5](https://github.com/openclaw/openclaw/releases/tag/v2026.6.5)
- [OpenClaw Release Notes - June 2026 Latest Updates - Releasebot](https://releasebot.io/updates/openclaw)
- [Model providers · OpenClaw Docs](https://docs.openclaw.ai/concepts/model-providers)
- [Google (Gemini) · OpenClaw Docs](https://docs.openclaw.ai/providers/google)
- [OpenClaw April 2026: 6 Model Providers You Can Now Swap at Runtime](https://www.mindstudio.ai/blog/openclaw-april-2026-model-agnostic-provider-manifest)
- [Best LLM for OpenClaw (2026) - DEV Community](https://dev.to/matthewrevell/best-llm-for-openclaw-gemini-31-pro-vs-gpt-55-vs-claude-opus-47-2026-3na4)
- [GitHub - Gen-Verse/OpenClaw-RL](https://github.com/Gen-Verse/OpenClaw-RL)
