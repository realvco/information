# Hermes-Agent 每日情報 — 2026-06-18

> 資料蒐集時間：2026-06-18 UTC｜搜尋範圍：過去 24 小時及近期重大更新｜專案：[NousResearch/hermes-agent](https://github.com/nousresearch/hermes-agent)

---

## 1. 今日重點摘要

1. **v0.16.0「The Surface Release」（2026-06-05）**：Hermes-Agent 最新正式版，含 874 commits、542 merged PRs、399 issues closed（含 2 P0、62 P1、16 安全標記），170 位社群貢獻者參與，是迄今規模最大的單次發布。

2. **原生桌面應用正式上線**：macOS / Linux / Windows 三平台 Desktop App 在一週內以 100 PRs、159 commits 完成，提供一鍵安裝、應用內自動更新、拖放上傳檔案、剪貼板圖片貼上、Cmd+K 指令面板、狀態列 model picker、多 profile 並行 session，以及完整繁體中文以外的簡體中文翻譯。

3. **NVIDIA GTC 主題演講亮相**：Nous Research 於 X.com 宣布 Hermes Desktop 「首次在 Jensen 的 GTC 主題演講展示，現已公開預覽」，為該專案帶來大量曝光，GitHub Stars 截至 2026 年 4 月已超過 57,000。

4. **瀏覽器管理面板（Admin Panel）**：v0.16.0 同步推出全瀏覽器式管理介面，涵蓋 MCP catalog 管理、訊息頻道、憑證（credentials）、webhooks、記憶體（memory）及可插拔的 OIDC / 帳號密碼登入。

5. **安全修補**：本次發布修補 CVE-2026-48710（Starlette 版本固定）、SSRF off-loop hardening，以及 subprocess credential stripping。

6. **v0.14.0「The Velocity Release」（2026-05-28）回顧**：1,302 commits、747 PRs，核心 `run_agent.py` 從 16,083 行縮減至 3,821 行（-76%），拆分為 14 個模組；Kanban 升級為真正的多 agent 平台，支援 orchestrator 自動分解、swarm topology、排程任務與 per-task model 覆寫。

7. **Gemini 原生 provider 仍在開發中**：目前 Gemini 串接需透過 OpenRouter 或 Google 的 OpenAI 相容 endpoint，兩者皆有額外的格式轉換層，在複雜多工具鏈上有機率出現參數格式問題或 streaming token 遺漏；原生 Google GenAI provider 預計改善 tool-calling 可靠性，但尚無確定發布日期。

8. **過去 24 小時無全新重大版本**：v0.16.0 為目前最新正式版（2026-06-05），過去 13 天（至 2026-06-18）社群主要在消化桌面應用體驗反饋，尚無新版本發布。

9. **Context window 不足為最常見首次啟動錯誤**：官方 FAQ 指出，當選用的模型 context window 小於 Hermes 系統提示 + 啟用 skill + 記憶體檔案 + 對話歷史所需的總量時，會出現最典型的初次啟動錯誤，建議新手優先選擇大 context 模型（如 Claude Sonnet 4.6 或 GPT-4.1）。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）

- **最推薦選擇**：多篇 2026 年實測文章一致指出 Claude Sonnet 4.6 的 tool-calling 品質最穩定，在 Hermes 工作流中極少出現格式錯誤的函式呼叫。官方文件亦列出 Claude Opus 4.6、Sonnet 4.6、Haiku 4.5 皆通過 Anthropic API 支援。
- **適用場景**：整體品質優先、複雜多步驟 agent 任務，企業部署首選。
- 來源：[AI Providers | Hermes Agent](https://hermes-agent.nousresearch.com/docs/integrations/providers)、[Best Hermes Agent Model 2026: Claude vs DeepSeek | Remote OpenClaw](https://www.remoteopenclaw.com/blog/best-models-for-hermes-agent)

### GPT / OpenAI

- **GPT-4.1 tool-calling 可靠**：與 Claude Sonnet 4.6 並列為生產環境最穩定選擇，token 消耗與響應速度表現均衡。
- **Context window 注意**：如使用大量 skills 或長對話歷史，仍需確認所選 GPT 型號的 context 上限。
- 來源：[Best Hermes Agent Model 2026: Claude vs DeepSeek | Remote OpenClaw](https://www.remoteopenclaw.com/blog/best-models-for-hermes-agent)

### Gemini（Google）

- **原生 provider 尚未就緒**：目前必須經由 OpenRouter 或 Google 的 OpenAI 相容 endpoint 使用，存在額外轉換層；在複雜 tool-chain 場景下偶有參數格式問題或 streaming token 丟失。
- **已知限制**：Hermes 的 tool-calling 格式與 Gemini 原生 function-calling API 不同，目前轉換層偶有相容性問題，建議在生產前充分測試。
- 來源：[AI Providers | Hermes Agent](https://hermes-agent.nousresearch.com/docs/integrations/providers)

### DeepSeek

- **預算首選**：DeepSeek V4 被多位用戶推薦為成本效益最高的選項，適合預算有限的部署場景，tool-calling 品質次於 Claude 但費用顯著較低。
- 來源：[The Best Free LLM Setup for Hermes Agent in 2026 | Medium](https://medium.com/activated-thinker/the-best-free-llm-setup-for-hermes-agent-in-2026-heres-my-hermes-agent-setup-7114e0feff85)

### 本地模型（Ollama / LM Studio）

- **Llama 4 Maverick via Ollama**：隱私優先場景的本地部署主流選擇；需注意本地模型 context window 普遍偏小，啟動時易觸發 context 不足錯誤。
- **LM Studio 整合**：用戶 Peter Falkingham 實測 LM Studio + Hermes 組合，評價「真正可用的本地 AI 替代方案」。
- 來源：[Getting Local AI Working for Me: LM Studio, OpenCode, and Hermes](https://peterfalkingham.com/2026/05/08/getting-local-ai-working-for-me-lm-studio-opencode-and-hermes/)、[Hermes Agent Desktop Free With Local LLMs](https://www.kunalganglani.com/blog/hermes-agent-desktop-free-local-llm)

### Web 自動化踩坑

- 所有依賴「瀏覽網頁」的任務都面臨 bot-detection 攔截，此為 2026 年通用 agent 的結構性問題，並非 Hermes 特有。Substack 文章《I Used Hermes Every Day for 2 Weeks. Here's What Broke.》詳細記錄了這一挫折。
- 來源：[I Used Hermes Every Day for 2 Weeks. Here's What Broke.](https://cjonsystems.substack.com/p/i-used-hermes-every-day-for-2-weeks)

---

## 3. 社群反應與討論亮點

- **NVIDIA GTC 曝光效應**：NousResearch 在 X.com 宣布 Hermes Desktop 首次亮相 Jensen Huang 的 GTC 主題演講，帖子獲得大量轉推與正面回應，社群認為這標誌著 Hermes 從開發者工具走向主流用戶。

- **Desktop App 正反評價**：Medium 作者 Ewan Mak 評論「Hermes 終於成為可以推薦給非技術用戶的 agent 平台」；但部分技術用戶指出 Desktop App 連線遠端 gateway 的 OAuth 流程需要額外測試，且舊有 built-in skills 被刪減後「令習慣舊版的用戶措手不及」。

- **Dev.to Hermes Agent Challenge**：2026 年 5–6 月的社群挑戰賽帶動數十個真實場景實作，涵蓋多模型協同決策系統到 Raspberry Pi 上的部署，展示社群活躍度。

- **「兩週啟動，幾乎沒完成任何事」現象**：Substack 長文引發廣泛共鳴，指出 agent 平台的設定門檻仍是主流用戶的重大障礙，這也是 Desktop App 被視為關鍵里程碑的背景。

- **v0.14.0 重構評價**：run_agent.py 從 16,083 行縮至 3,821 行的重構被開發者社群稱為「終於可以讀懂的 codebase」，貢獻者門檻顯著降低。

---

## 4. 值得追蹤的後續議題

1. **Gemini 原生 provider 發布時程**：一旦推出，將消除目前的格式轉換層，顯著改善 Gemini 在 Hermes 工作流中的 tool-calling 可靠性。

2. **Desktop App 穩定性反饋**：v0.16.0 的 Desktop App 目前仍是 Public Preview；隨用戶基數擴大，預計將出現 OAuth session 邊界、多 profile 切換等 Bug 回報，值得追蹤 GitHub issues。

3. **v0.17.0 方向**：根據 Kanban 多 agent 平台的發展軌跡，預計下一版本將深化 orchestrator 自動分解與 swarm topology，可能帶來更複雜的 LLM provider 路由需求。

4. **CVE-2026-48710 後續**：Starlette 版本固定已在 v0.16.0 修補，但 SSRF off-loop 與 subprocess credential stripping 的完整審計報告尚未公開，值得關注是否有後續 CVE 揭露。

5. **本地模型相容性改善**：社群有聲音希望 Hermes 自動偵測本地模型 context window 並在啟動時給予明確警告，而非等到第一次對話才報錯；相關 issue 可能在 v0.17.x 納入。

---

*本報告依據公開搜尋結果整理。過去 24 小時（2026-06-17 至 2026-06-18）未發現新版本發布，社群主要活動為消化 v0.16.0 桌面應用體驗。最新正式版為 v0.16.0（2026-06-05）。*
