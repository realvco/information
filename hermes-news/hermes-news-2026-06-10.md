# Hermes-Agent 每日情報 — 2026-06-10

> 搜尋時間範圍：過去 24 小時（2026-06-09 ～ 2026-06-10 UTC）

---

## 1. 今日重點摘要

1. **v0.16.0「Surface Release」於 2026-06-05 正式發布**：本次為過去 24 小時最鄰近的重要里程碑，帶來原生 Desktop App、全瀏覽器 Admin Panel、/undo 指令，以及多個新模型支援。
2. **原生 Desktop App（Electron）全平台發布**：支援 macOS、Linux、Windows，內建一鍵安裝與應用內自動更新，提供串流聊天視窗、拖放檔案、剪貼簿圖片貼上、Cmd+K 指令面板、session 歸檔與搜尋，以及狀態列模型切換器。
3. **全瀏覽器 Admin Panel 上線**：MCP catalog 啟用/停用切換、憑證管理、webhook 與 hook 建立、記憶體設定、gateway 控制，以及系統頁面（含 check-before-update 與一鍵 Debug Share），不再需要 SSH 進入機器修改 config.yaml。
4. **新增四個模型**：DeepSeek-V4-Flash、MiniMax-M3、Qwen3.7-Plus、Gemini-3.5-Flash（透過 OpenRouter 路由）正式加入支援清單。
5. **/undo 指令上線**：使用者可在對話中撤銷上一步動作，降低 agent 誤操作後的復原成本。
6. **CVE-2026-48710（BadHost）Starlette 安全漏洞修補**：v0.16.0 已針對此高危漏洞升級 Starlette 至 1.0.1+，影響所有使用 FastAPI / Starlette 的 MCP server，Hermes 在此次修補中屬積極應對。
7. **6 月 9 日 GitHub PR 活動**：包含 update 基礎設施修復、Desktop sidebar session 排序切換、cron UTF-8 解碼修正、MCP discovery timeout 可設定化、leaf subagent 移除 skill toolset、子代理 system prompt 精簡，多筆 P2/P3 issues 同日開出。
8. **前次「Velocity Release」v0.15 大幅瘦身**：run_agent.py 從 16,083 行壓縮至 3,821 行（-76%），拆分為 14 個 agent/* 模組，啟動時間再減 1 秒，每次對話函式呼叫數減少 47%，session_search 效能提升 4,500 倍且免費開放。
9. **Kanban 多 agent 協作看板穩定化**（自 v0.13）：心跳機制、zombie 偵測、任務自動重試、幻覺恢復，以及 /goal 指令（Ralph loop）持續鎖定目標跨多輪對話，仍為社群高關注功能。

---

## 2. LLM 串接實戰回報

### Anthropic Claude

- Hermes-Agent 支援 Claude Opus 4.6、Sonnet 4.6、Haiku 4.5，可透過 Anthropic 直接 API 或 OpenRouter 存取，API Key 設定後即可在 model 欄位指定使用。
  > 來源：[Hermes-Agent AI Providers 文件](https://hermes-agent.nousresearch.com/docs/integrations/providers)
- 社群有實測比較：Claude 在 Hermes 工作流中表現穩定，Opus 4.6 仍是高品質任務的首選，但成本明顯高於 DeepSeek 系列。
  > 來源：[Remote OpenClaw — Best Hermes Agent Model 2026: Claude vs DeepSeek](https://www.remoteopenclaw.com/blog/best-models-for-hermes-agent)

### Gemini

- **原生 Google GenAI provider 尚未支援**：feature request 仍處於開啟狀態（active），目前 Gemini 僅能透過 OpenRouter 或 OpenAI 相容層（兼容 endpoint）連接，Gemini-3.5-Flash 為新增支援模型之一。
  > 來源：[Remote OpenClaw — Best Gemini Models for Hermes Agent](https://www.remoteopenclaw.com/blog/best-gemini-models-for-hermes)
- Gemini 透過 OpenRouter 路由的穩定性被社群評為「可用但偶有延遲波動」，尚無官方 SLA 保證。

### GitHub Copilot 訂閱路徑

- Hermes 支援透過 GitHub Copilot 訂閱存取 GPT-5.x、Claude、Gemini 等模型，適合已有 Copilot 企業授權的用戶，可節省額外 API 費用。

### Discord Rate Limit（踩坑）

- **Issue #7137**：Gateway 在 Discord slash command 429 錯誤（約 3 分鐘後觸發）時會進入重連迴圈，根本原因為已註冊 skill 數量超過 100 個。建議精簡 skill 數量或依 channel 分組部署。
  > 來源：[GitHub — Bug: Fix Discord connectivity (need to reduce registered skills to <100) #7137](https://github.com/NousResearch/hermes-agent/issues/7137)

### Token 消耗異常（踩坑）

- Reddit 用戶 u/Typical_Ice_3645 回報：輕度使用 2 小時即消耗 400 萬 tokens，其中「詢問天氣」單次就用掉 21,000 tokens，社群判斷主因為 skill toolset 未精簡導致 system prompt 過大。
  > 來源：[Hermes Agent Troubleshooting Guide — Real Fixes](https://www.getopenclaw.ai/blog/hermes-agent-troubleshooting)
- **Ollama + Hermes 3 的 num_ctx 陷阱**：Ollama 對 Hermes 3 預設 num_ctx 僅 2,048 tokens，而每個 tool schema 約佔 150 tokens，10 輪對話搭配兩個工具即可能在第一次回覆前就 overflow，務必手動調高 num_ctx。
  > 來源：[Markaicode — Fix Hermes Agent Token Limit Error: Python Guide 2026](https://markaicode.com/hermes-agent-token-limit-error-fix/)

### OpenRouter 多模型切換

- Hermes 支援 OpenRouter（200+ 模型）、Nous Portal、MiniMax、Kimi，以及任何 OpenAI 相容端點（含自架 Ollama、vLLM、SGLang），被視為最靈活的多 provider 解決方案。

---

## 3. 社群反應與討論亮點

- **Desktop App 獲主流媒體報導**：FintechExtra、Digg、Medium 均刊出 v0.16 Desktop App 報導，標題多以「AI Agent 走向大眾化」為主軸，顯示 Hermes 正從開發者圈向更廣泛受眾滲透。
  > 來源：[FintechExtra — Hermes Agent v0.16.0 Launches Native Desktop App](https://fintechextra.com/news/hermes-agent-v0160-launches-native-desktop-app-633) | [Medium — Hermes Agent Desktop App Everything You Need to Know](https://medium.com/@tentenco/hermes-agent-desktop-app-everything-you-need-to-know-about-nous-researchs-self-improving-ai-agent-3cb59bd31e5f)
- **CVE-2026-48710（BadHost）廣泛影響討論**：此 Starlette Host Header 認證繞過漏洞波及所有使用 FastAPI 的 MCP server，Hacker News 上引發大量討論，Hermes 積極修補獲社群好評。
  > 來源：[Hacker News — BadHost CVE-2026-48710](https://news.ycombinator.com/item?id=48277107) | [CVEReports — CVE-2026-48710](https://cvereports.com/reports/CVE-2026-48710)
- **「Velocity Release」重構被稱為里程碑**：-76% 程式碼量與 4,500 倍 session_search 效能提升在社群引發廣泛討論，被視為 Hermes 從「快速成長期」進入「成熟穩定期」的信號。
- **國際化（i18n）反應熱烈**：v0.13 加入中文、日文、德文、西班牙文、法文、烏克蘭文、土耳其文等 7 種語言，非英語社群使用者積極回饋翻譯品質與本地化問題。
- **awesome-hermes-agent 社群資源庫持續擴充**：[SamurAIGPT/awesome-hermes-agent](https://github.com/SamurAIGPT/awesome-hermes-agent) 收錄 skill、plugin、工具、整合與資源，是快速上手的重要入口。

---

## 4. 值得追蹤的後續議題

1. **原生 Gemini Provider 開發進度**：feature request 已開啟，社群需求明確，預計為未來某個版本的重點功能，建議訂閱相關 issue 通知。
   > 追蹤：[Hermes-Agent 官方 Releases](https://github.com/NousResearch/hermes-agent/releases)
2. **Desktop App 遠端 Gateway 認證穩定性**：透過 WebSocket 連線遠端 gateway（OAuth 或帳密認證）功能剛上線，跨 profile concurrent session 的邊緣案例尚待社群壓測，預計後續 patch 版本會陸續修正。
3. **/undo 指令的 edge case 處理**：/undo 剛上線，對於涉及外部工具呼叫（如檔案寫入、API 呼叫）的動作能否真正「回滾」，仍需觀察實作邊界與官方說明。
4. **Discord Skill 數量限制（Issue #7137）的根本修復**：目前 workaround 為手動精簡 skill，若官方不從架構層面解決 Discord 429 限制，大型部署仍會持續遇到重連迴圈。
5. **CVE-2026-48710 後續影響**：BadHost 漏洞波及所有 MCP server，各 agent 框架的修補進度不一，建議持續關注自架 MCP 服務的 Starlette 版本是否已升至 1.0.1+。
   > 來源：[SC Media — High-severity Starlette vulnerability 'BadHost'](https://www.scworld.com/brief/high-severity-starlette-vulnerability-badhost-could-expose-sensitive-data)
6. **Kanban 多 agent 看板的生產穩定性**：zombie 偵測與自動重試機制在 v0.13 引入，但複雜多 agent 拓撲下的邊緣案例仍持續有用戶回報，值得觀察後續修補速度。
