# Hermes-Agent 每日情報 — 2026-06-27

## 今日重點摘要

1. **v0.17.0「The Reach Release」於 6/19 發佈**：由 1,475 commits、約 800 個 merged PR、245 名社群貢獻者共同完成，是今年規模最大的單次發佈之一。
2. **原生桌面應用程式正式推出**：100 個 PR、159 commits 在一週內完成，提供 macOS/Linux/Windows 三平台支援，一鍵安裝、應用程式內自動更新，支援多 profile 並發連線。
3. **新通道：iMessage（Photon）+ Raft agent 網絡**：iMessage 透過 Photon 橋接整合；Raft agent 網絡讓多個 Hermes instance 可相互通訊協作。
4. **Blank Slate 模式上線（6/20）**：使用者可從僅含 provider、model、檔案操作和終端機的最小配置開始，完全自訂工具集，不再只能依賴預設的 Quick/Full 安裝模式。
5. **GitHub 星數爆炸性成長**：從 4 月的 40,000 顆星增長至目前 188,000 顆星，峰值週增 24,000 顆星。
6. **NVIDIA 採用 Hermes 作為 Nemotron 3 Ultra（550B 參數）的參考 runtime**，大幅提升 Hermes 在企業市場的能見度。
7. **The Velocity Release（v2026.5.28）重構成果顯著**：核心 `run_agent.py` 從 16,083 行縮減至 3,821 行（削減 76%），重構為 14 個模組，可維護性大幅提升。
8. **Web dashboard 獲得完整管理面板**：含 MCP catalog、messaging channels、credentials、webhooks、memory 管理，並支援可插拔的 OIDC / username-password 登入。
9. **Nous Research 在 X 上積極宣傳**：Jensen Huang GTC keynote 首次展示 Hermes Desktop 後，Nous Research 官方 X 帳號持續推播更新，帶動社群熱度。

---

## LLM 串接實戰回報

### 多 Provider 支援架構
Hermes Agent 透過 Nous Portal 支援 300+ 前沿 agentic 模型，並提供多種接入路徑：
- **Nous Portal 直連**：Claude、GPT、Gemini、DeepSeek、Qwen、Grok、MiniMax 等
- **OpenRouter**：單一 API key 統一存取 200+ 模型，適合需頻繁切換或測試多家 provider 的用戶
- **LiteLLM proxy**：OpenAI 相容介面統一管理 100+ provider，支援負載均衡、fallback chain 與預算控制
- **GitHub Copilot 訂閱**：可透過 Copilot API 存取 GPT-5.x、Claude、Gemini
- 參考：[AI Providers | Hermes Agent](https://hermes-agent.nousresearch.com/docs/integrations/providers)

### 上下文限制：同模型不同 provider 差異大
Hermes 官方文件特別提醒：同一模型在不同 provider 上的上下文限制可能截然不同。
- `claude-opus-4.6`：Anthropic 直連為 1M tokens；GitHub Copilot 上僅 128K tokens

### HTTP 402（billing/spend limit）誤判問題
GitHub Issue #32420 記錄了一個已知問題：當使用者的 provider 帳戶觸及費用/消費上限時，Hermes 錯誤地將 HTTP 402 回應歸類為其他錯誤類型，並給出誤導性的修復建議，導致使用者花費大量時間排查錯誤根因。
- 參考：[Bug #32420 · NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent/issues/32420)

### Rate Limit（429）踩坑：並行工具呼叫的設計缺陷
Hermes 預設的 request loop 會同時送出所有工具呼叫（fire all simultaneously），在高負載情境下極易觸發 provider 的 rate limit（`RateLimitExceeded`）。Hermes 官方定位此為「設計選擇而非 bug」，將 rate limit 處理委由部署層負責。
- **社群推薦解法**：token bucket throttling、exponential backoff with jitter、多 API key 輪詢（key rotation）
- 參考：[Hermes Agent RateLimitExceeded: 3 Fixes That Work in Production | Markaicode](https://markaicode.com/errors/hermes-agent-rate-limit-exceeded-fix-production/)

### Hermes 模型 vs Claude/GPT 的使用場景分工
社群在 r/LocalLLaMA 的主流共識：
- **Hermes 系列模型**：function calling、tool use 效果最佳，與 Hermes Agent 工作流深度整合
- **Claude/GPT**：純推理任務、長文生成、複雜問題分析表現更優

---

## 社群反應與討論亮點

- **Hermes Desktop 引爆關注**：Medium 上詳細技術分析文章獲得大量轉發，Facebook AI 社群中 v0.17.0 release 帖子廣泛流傳。Nous Research X 帳號連發多則公告，提及「首次在 Jensen Huang GTC keynote 展示後立即開放公開預覽」。
  - 參考：[Medium 分析文](https://medium.com/@tentenco/hermes-agent-desktop-app-everything-you-need-to-know-about-nous-researchs-self-improving-ai-agent-3cb59bd31e5f) | [Nous Research on X](https://x.com/NousResearch/status/2061843507417944552)
- **Discord Bot 技術限制引發討論**：GitHub Issue #7137 記錄 Discord slash command 在 Hermes v0.8.0 時因註冊 skill 數超過 100 個而觸發 Discord 429 限制（約 3 分鐘後開始失敗），目前官方的建議繞解方案是限制已註冊 skill 的數量。
  - 參考：[Bug #7137 · NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent/issues/7137)
- **Blank Slate 模式受輕量用戶歡迎**：MarkTechPost 報導後，技術社群對「從零客製化」的設計表達正面回響，認為解決了預設安裝過於臃腫的痛點。
  - 參考：[MarkTechPost 報導](https://www.marktechpost.com/2026/06/20/nous-research-updates-hermes-agent-with-a-blank-slate-mode-that-pins-toolsets-via-platform_toolsets-cli-and-disabled_toolsets/)
- **22 個 releases、188K stars 的生態系回顧**：The Agent Report 的長文深度分析 2026 年 Hermes 生態系，被廣泛引用為 2026 年 open-source agent runtime 領域的標竿報告。
  - 參考：[The Hermes Agent Ecosystem in 2026 | The Agent Report](https://the-agent-report.com/2026/06/hermes-agent-ecosystem-2026-pillar/)

---

## 值得追蹤的後續議題

1. **v0.18.0 下一版本內容**：根據 v0.17.0（「Reach」）的發佈脈絡，下一版本可能聚焦在團隊協作或企業部署強化，官方尚未公布正式 roadmap。
2. **Discord skill 數量限制的官方修復**：Issue #7137 目前僅有社群繞解方案，期待官方從架構層面解決（如延遲 registration、動態 un/register skill）。
3. **HTTP 402 誤判（Issue #32420）修復狀態**：此 bug 直接影響用戶排查費用問題的效率，值得持續追蹤 PR 進度。
4. **Psyche 去中心化訓練網絡**：Nous Research 的長期投資項目，目前仍在積極開發階段，與 Hermes Agent 的整合方向尚待明確。
5. **NVIDIA Nemotron 3 Ultra + Hermes 實測報告**：NVIDIA 採用 Hermes 作為參考 runtime 後，社群對 550B 模型在 Hermes 框架下的工具呼叫效能與成本的實測資料尚屬稀缺。
6. **Portkey + Hermes 整合**：Portkey 官方已為 Hermes 提供文件，可作為多 provider 管理的中間層，值得觀察社群採用情形。
   - 參考：[Hermes Agent - Portkey Docs](https://portkey.ai/docs/integrations/libraries/hermes-agent)
