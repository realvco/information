# Hermes-Agent 每日情報 — 2026-06-25

## 今日重點摘要

1. **v0.17.0「Reach Release」發布（2026-06-19）**：這是 6 月最重要的 Hermes-Agent 里程碑，開發統計顯示自 v0.16.0 以來共 ~1,475 commits、~800 merged PRs、1,693 個檔案變更、235,390 行新增、50,730 行刪除、300+ issues 關閉、245 位社群貢獻者。
2. **兩個全新訊息頻道上線**：iMessage 透過 Photon 管理的號碼池實作（無需 Mac 中繼機），以及 Raft agent 網路作為外部 agent 接入點；wake payload 僅傳遞 metadata 不含訊息本文（privacy-by-contract 設計）。
3. **桌面應用大幅升級**：可重綁定鍵盤快捷鍵、OS 原生通知（支援分類開關）、子 agent 即時監看視窗（live subagent watch-windows）、composer model selector 含 per-model 預設值、自動 RTL/bidi 文字方向、可調整大小的 VS Code 主題終端面板、per-thread composer 草稿，以及直接安裝 VS Code Marketplace 佈景主題。
4. **背景子 agent（async subagent）**：新增 `delegate_task(background=true)` API，可將工作委派給後台 subagent 並立即取得 handle 繼續主流程，不需等待完成。
5. **圖像編輯功能上線**：圖像生成功能延伸至圖像編輯；Skills Hub 瀏覽器全面重整；memory tool 獲得重大升級。
6. **Dashboard 全面翻新**：新增完整個人資料建構器與安全登入機制。
7. **64k token 最低上下文需求**：Hermes-Agent 要求所選模型至少具備 64,000 tokens 上下文視窗，以承載系統提示、技能庫、記憶檔案和對話歷程；不足的模型在啟動時即被拒絕，是最常見的首次執行錯誤。
8. **GitHub Copilot 訂閱整合**：用戶可透過 Copilot API 以 GitHub Copilot 訂閱存取 GPT-5.x、Claude、Gemini 及其他模型，無需單獨申請各家 API Key。
9. **產業影響力報告**：MindStudio 發布分析指 Claude Code 與 Hermes-Agent 已取代 Cursor、OpenClaw、ChatGPT 成為 2026 年主流 AI 工具。

---

## LLM 串接實戰回報

### Anthropic Claude
- **OAuth 整合支援**：Hermes-Agent 支援 Anthropic OAuth，優先讀取 Claude Code 自身的 credential store（而非複製 Token 到 `~/.hermes/.env`），保持 Token 可自動刷新的能力；這與 OpenClaw 因 Anthropic 政策限制的困境形成對比。
  - 來源：[AI Providers | Hermes Agent](https://hermes-agent.nousresearch.com/docs/integrations/providers)
- **最低上下文限制**：Claude 系列的小型模型若上下文視窗不足 64k tokens，啟動時會被直接拒絕；建議使用 Claude Sonnet 或 Opus 系列。

### OpenAI GPT / GitHub Copilot
- 透過 Copilot API 整合可一鍵存取 GPT-5.x，是成本控管的熱門選擇；LiteLLM 作為 proxy 支援 load balancing 與 fallback chain 設定。
  - 來源：[Configuration | Hermes Agent](https://hermes-agent.nousresearch.com/docs/user-guide/configuration)

### 本地模型（Ollama / Unsloth）
- Hermes-Agent 可對接本地執行的模型；Unsloth 文件中有專屬整合指南，適合重視隱私或網路受限環境。
  - 來源：[How to Run Local AI Models with Hermes Agent | Unsloth](https://unsloth.ai/docs/integrations/hermes-agent)

### LiteLLM Proxy（多 provider 統一管理）
- LiteLLM 以 OpenAI-compatible 格式代理 100+ LLM provider，Hermes 可設定 `http://localhost:4000/v1` 作為端點；適合需要在 provider 之間快速切換、設定 budget control 或 fallback chain 的進階用戶。
  - 來源：[AI Providers | Hermes Agent](https://hermes-agent.nousresearch.com/docs/integrations/providers)

### 通用踩坑紀錄
- **64k token 啟動錯誤**：最常見首次執行問題；確認所選模型的 context window 規格後再設定。
- **provider 切換相容性**：透過 LiteLLM 統一 API 可降低切換成本，但 per-model 行為差異（tool call 格式、停止條件）仍需逐一驗證。

---

## 社群反應與討論亮點

- **Threads（@artificiallyinfluenced）**：v0.17.0 發布公告獲得廣泛轉發，亮點是「iMessage 不需要 Mac relay」這項功能——被社群認為是訊息整合方面最重要的突破。
  - 來源：[Threads 貼文](https://www.threads.com/@artificiallyinfluenced/post/DZ0S6OPDO8W/nous-research-released-hermes-agent-v-the-reach-release-on-june-new-channels-i)
- **Medium《Hermes v0.17: 7 New Features That Make AI Agents Actually Useful》**：作者 Greek Ai 整理了 v0.17 的七大實用新功能，強調背景 subagent 與桌面 watch-window 對實際工作流程的改變。
  - 來源：[Medium — Greek Ai](https://medium.com/@greekofai/hermes-v0-17-7-new-features-that-make-ai-agents-actually-useful-bf5ccfb73b0e)
- **MEXC News 報導**：crypto/tech 媒體也注意到 Hermes-Agent v0.17，顯示其影響力已超出純開源社群範疇。
  - 來源：[MEXC News](https://www.mexc.com/news/1160097)
- **MindStudio 產業分析**：「2026 年被取代的 AI 工具」文章引發廣泛討論，多數評論認為 Hermes-Agent 的技能庫（Skills）生態是其核心優勢。
  - 來源：[MindStudio Blog](https://www.mindstudio.ai/blog/ai-tools-replaced-2026-claude-code-hermes-agent-killed-cursor-openclaw)
- **Facebook 社群**：v0.17.0 release 貼文在相關 AI 工具社團獲得相當迴響，主要討論 iMessage 整合的實際設定步驟。
  - 來源：[Facebook 貼文](https://www.facebook.com/groups/1283855437217819/posts/1351904450412917/)

---

## 值得追蹤的後續議題

- **v0.18.0 下一版預告**：依照 Nous Research 的發版節奏（約每 3-4 週一個 minor 版本），下一版預計 7 月中旬；目前 GitHub Issues 中熱門的功能請求包含更深度的桌面控制與 multi-agent 協作視覺化。
  - 來源：[Releases · NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent/releases)
- **Raft 平台整合成熟度**：v0.17 首次引入 Raft 作為 agent 接入點，目前仍屬早期階段，社群對 privacy-by-contract 設計的實際安全性仍有討論空間。
- **Skills Hub 新版生態**：Skills Hub 瀏覽器改版後的技能庫貢獻數量與品質，是判斷 Hermes-Agent 社群活躍度的關鍵指標。
- **Claude Code 整合深度**：Hermes-Agent 與 Claude Code credential store 的 OAuth 整合，後續若 Anthropic 調整政策是否影響此路徑，值得持續觀察。
  - 來源：[AI Providers | Hermes Agent](https://hermes-agent.nousresearch.com/docs/integrations/providers)
- **hermes-desktop 衍生專案**：[fathah/hermes-desktop](https://github.com/fathah/hermes-desktop) 社群衍生桌面客戶端，可追蹤其與官方 Electron app 的功能差異走向。
