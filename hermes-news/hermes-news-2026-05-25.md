# Hermes-Agent 每日情報｜2026-05-25

> 資料蒐集時間範圍：過去 24 小時（UTC 基準）
> 來源：GitHub Releases、NousResearch 官方 X 帳號、Digg、社群部落格

---

## 1. 今日重點摘要

1. **過去 24 小時無新版本發布**：最新穩定版為 v0.14.0（2026-05-16），前一版 v0.13.0（2026-05-07），今日無新 release 推送。
2. **v0.14.0「The Foundation Release」持續發酵**：本版包含 808 commits、633 merged PRs、1,393 檔案變更、215 位社群貢獻者，是 Hermes-Agent 迄今最大規模單一版本。
3. **xAI Grok 正式整合**：v0.14.0 新增 SuperGrok OAuth provider，grok-4.3 上下文視窗擴充至 1M tokens，並加入 tool-use enforcement 支援。
4. **本地 OpenAI 相容代理伺服器（Local Proxy）上線**：任何已透過 OAuth 認證的 Hermes provider（Claude Pro、ChatGPT Pro、SuperGrok）均可透過本地端點被 Codex、Aider、Cline、Continue 等工具呼叫，無需另行設定 API Key。
5. **Computer Use（cua-driver）擴展至非 Anthropic provider**：v0.14.0 新後端支援任何具備 vision 能力的模型驅動桌面，並修正 focus-safe 操作問題，`hermes update` 後會自動更新。
6. **HuggingFace Skills Hub 整合**：社群 skills index（huggingface.co/skills）已預設連入 Skills Hub，使用者可直接從 hermes skills browser 安裝社群發布的技能，毋需額外設定。
7. **v0.13.0 Kanban 多代理人架構**（5 月 7 日）持續被社群廣泛討論：支援心跳、zombie 偵測、自動阻塞（incomplete exit）、per-task retry、幻覺恢復等，被視為 production-grade 多代理人排程的重要里程碑。
8. **9 個新選用技能（Optional Skills）**新增：Hyperliquid、Yahoo Finance、api-testing、unified EVM multi-chain、darwinian-evolver、osint-investigation、pinggy-tunnel、watchers、全面翻新的 Notion 整合。
9. **/goal 指令**：v0.13.0 新增，可讓 agent 跨對話輪次鎖定目標，避免長任務中途偏離。

---

## 2. LLM 串接實戰回報

### Anthropic（Claude）

- **OAuth 憑證整合優化**：透過 Anthropic OAuth 認證 Hermes 時，系統優先使用 Claude Code 自身的 credential store 而非複製 token，確保 refreshable 憑證的更新機制保持正常運作，避免 token 意外失效。
  - 來源：[AI Providers - hermes-agent.nousresearch.com](https://hermes-agent.nousresearch.com/docs/integrations/providers)
- **Local Proxy 整合**：Claude Pro 帳號可透過 `hermes proxy` 指令將訂閱轉為本地 OpenAI 相容端點，供外部工具（Codex CLI、Aider 等）使用，無需單獨購買 Anthropic API Key。
  - 來源：[What's New in Hermes Agent - clawsimple.com](https://clawsimple.com/en/blog/hermes-agent-updates-mid-may-2026)

### xAI Grok

- v0.14.0 正式加入 Grok 支援，SuperGrok OAuth 認證後可使用 grok-4.3（1M context），並同步加入 tool-use enforcement 白名單。
  - 來源：[Release Hermes Agent v0.14.0 - GitHub](https://github.com/NousResearch/hermes-agent/releases/tag/v2026.5.16)
- 社群評測：Grok 1M context 對長文件分析任務效果顯著，但部分使用者反映 SuperGrok OAuth 初次設定流程較複雜。
  - 來源：[Best Grok Models for Hermes Agent - remoteopenclaw.com](https://www.remoteopenclaw.com/blog/best-grok-models-for-hermes)

### OpenAI（ChatGPT Pro）

- Local Proxy 同樣支援 ChatGPT Pro OAuth，與 Grok 並列為「不需 API Key 即可使用的高階模型」選項。

### Nous Portal / OpenRouter

- Nous Portal 為統一訂閱閘道，涵蓋 300+ 前沿 agentic 模型（包含 Claude、GPT、Gemini、DeepSeek、Qwen、Kimi、GLM、MiniMax、Grok），是官方推薦的多模型統一管理方案。
  - 來源：[AI Providers - hermes-agent.nousresearch.com](https://hermes-agent.nousresearch.com/docs/integrations/providers)
- OpenRouter、Anthropic、GitHub Copilot、z.ai、Kimi、MiniMax、DeepSeek、Alibaba、Hugging Face、Google、自架端點均在支援清單內，可搭配 OpenAI-compatible 介面切換。
  - 來源：[Hermes Agent v0.13 Reference - blakecrosley.com](https://blakecrosley.com/guides/hermes)

---

## 3. 社群反應與討論亮點

- **Digg AI 版位報導**：v0.14.0 規模（808 commits、215 貢獻者）獲 Digg 等媒體關注，強調「Foundation Release」定位。
  - 來源：[NousResearch releases Hermes Agent v0.14.0 - Digg](https://digg.com/ai/1qruxedc)
- **X（Twitter）官方公告**：@NousResearch 發文宣布 v0.14.0，附上完整 changelog 連結，留言區使用者對 Grok 整合與 Local Proxy 功能反應熱烈。
  - 來源：[Nous Research on X](https://x.com/NousResearch/status/2056110234309939330)
- **Kanban 架構深度討論**：v0.13.0 的多代理人 Kanban 受多位技術部落客詳細解析，強調 heartbeat + zombie detection 設計讓長時間 unattended 任務更可靠；自架 LLM 工作流的使用者反映效果尤佳。
  - 來源：[Kanban in Hermes Agent - glukhov.org](https://www.glukhov.org/ai-systems/hermes/kanban-in-hermes/)、[Multi-Agent Kanban board - theunwindai.com](https://www.theunwindai.com/p/multi-agent-kanban-board-in-hermes-agent)
- **hermes-workspace 第三方專案**：社群成員發布原生 Web workspace（`outsourc-e/hermes-workspace`），整合 chat、terminal、memory、skills、inspector 於單一介面，獲社群關注。
  - 來源：[GitHub - outsourc-e/hermes-workspace](https://github.com/outsourc-e/hermes-workspace)

---

## 4. 值得追蹤的後續議題

| 議題 | 狀態 | 追蹤重點 |
|------|------|---------|
| v0.15.0 下一版內容 | 未公告 | Roadmap 揭露、issue 標籤動向 |
| Local Proxy 穩定性 | 新功能（v0.14.0）| 用戶長期使用回報、OAuth refresh 是否穩定 |
| cua-driver 非 Anthropic 模型相容性 | 持續測試 | Vision 模型的桌面驅動正確率與穩定性報告 |
| GitHub issue #29652（行為約束研究）| 開放討論 | AI agent 框架如何落實行為邊界，對 Hermes 設計方向有指標意義 |
| HuggingFace Skills Hub 品質管控 | 新機制 | 社群 skill 審核機制、惡意 skill 防護 |
| darwinian-evolver skill 社群實測 | 新功能 | 自我優化技能的實際效果與安全邊界 |

---

*本報告根據公開搜尋結果整理，不含推測或未經確認的資訊。*
