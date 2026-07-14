# Hermes-Agent 每日情報 — 2026-07-14

## 1. 今日重點摘要

1. **v0.18.0「Judgment Release」為本週最重要版本（7/1 釋出）**：此版本清空了所有被追蹤的 P0/P1 backlog，新增 **Evidence-Led Completion**（任務完成需附帶可驗證依據）、**Mixture-of-Agents (MoA)** 原生支援、可見自我改進流程、背景 fan-out subagent、Desktop Coding Projects，以及生產環境導向的 gateway 控制功能。

2. **GPT-5.6 正式支援（7/9 宣布）**：Nous Research 官方 X 帳號宣布 GPT-5.6 已加入 Hermes Agent，並可透過 **Nous Portal** 存取，無需用戶自行管理 OpenAI API 金鑰。

3. **v2026.7.7.2 補丁（7/7）**：將 WhatsApp Baileys bridge 的依賴從釘定的 Git commit 改為已發布的 npm 套件，修復 Docker build 可靠性問題，對自架 Docker 部署用戶影響較大。

4. **Hermes Desktop 公開預覽**：原生 macOS/Linux/Windows 桌面應用程式上線，功能包含一鍵安裝、應用內自動更新、拖放檔案至對話框、狀態列內嵌模型選擇器、多 profile 並行 session，以及透過 OAuth 或帳密連線遠端 Hermes Gateway。

5. **Google Vertex AI 成為第一級 Provider**：Gemini 模型現可透過 Google Cloud 的 Vertex AI 接入，Hermes 會自動申請並刷新短效 OAuth2 Access Token，無需手動管理，適合企業 Google Cloud 環境。

6. **Nous Portal 擴展至 300+ 模型**：統一訂閱閘道涵蓋 Claude、GPT、Gemini、DeepSeek、Qwen、Kimi、GLM、MiniMax、Grok 等 300+ 前沿代理模型，降低多 provider 管理複雜度。

7. **「Reach Release」（6/19）功能陸續成熟**：iMessage（透過 Photon）、Raft 代理網路、背景 subagent、圖片編輯、Automation Blueprints、瀏覽器 profile builder 等功能在社群中持續收到回饋與問題回報，修復持續進行中。

8. **Hermes Accelerated Business Hackathon**：由 NVIDIA、Stripe 與 NousResearch 聯合主辦，聚焦「能賺錢、能花錢、能在任意規模運作真實業務」的代理應用，吸引開發者關注 Hermes 商業化落地場景。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）
- **支援直接 API 或 OAuth 兩種方式**：可直接設定 Anthropic API Key，或透過 OAuth 流程授權；Nous Portal 亦提供統一入口，可避免直接管理 Anthropic 金鑰。
- **Context 長度管理**：當對話超過模型 context window 上限時，Hermes 可自動壓縮 session，或由用戶手動切換至 context window 更大的模型（如 Claude 系列的長 context 變體）。官方 FAQ 建議遇到此類問題先確認所用模型的 context 限制再決定策略。
- 來源：[AI Providers | Hermes Agent Docs](https://hermes-agent.nousresearch.com/docs/integrations/providers) ／ [FAQ & Troubleshooting](https://hermes-agent.nousresearch.com/docs/reference/faq)

### GPT（OpenAI）
- **GPT-5+ 自動切換至 Responses API**：GPT-5.4、GPT-5-codex、GPT-5.6 等模型（gpt-5-mini 除外）自動使用 OpenAI Responses API，其他模型（GPT-4o 等）仍走 Chat Completions；用戶無需手動設定，但需留意兩套 API 在 token 計費與 streaming 行為上的差異。
- **GPT-5.6 即時可用**：透過 Nous Portal 可直接使用，無需等待 OpenAI API 個別開放。
- Rate limit 問題：官方建議遇到 rate limit 時等待片刻後重試；Nous Portal 內建排隊機制可緩解個人用戶的配額限制。
- 來源：[Nous Research X 公告](https://x.com/NousResearch/status/2075270903458373640) ／ [AI Providers | Hermes Agent Docs](https://hermes-agent.nousresearch.com/docs/integrations/providers)

### Gemini（Google）
- **Vertex AI 整合為主要新亮點**：企業用戶可透過 Vertex AI 使用 Gemini，短效 Token 自動管理是對 GCP 環境的重要改善；個人用戶仍可透過直接 API 或 OpenRouter 接入。
- **OpenRouter 相容 proxy 支援**：Gemini 模型亦可透過 OpenRouter 或相容 proxy 接入，對無法直接使用 Google API 的地區用戶有幫助。
- 來源：[How to integrate Gemini with Hermes | Composio](https://composio.dev/toolkits/gemini/framework/hermes-agent)

### 本地模型（Local LLM）
- **Hermes Desktop 支援免費本地 LLM 執行**：搭配 Hermes Desktop，用戶可在不消費任何 API 費用的情況下，以本地 LLM（如 Ollama 後端模型）執行 Hermes Agent，適合隱私敏感或預算有限的場景。
- 來源：[Hermes Agent Desktop Free With Local LLMs 2026 Guide](https://www.kunalganglani.com/blog/hermes-agent-desktop-free-local-llm)

---

## 3. 社群反應與討論亮點

- **「Judgment Release」P0/P1 清零獲高度肯定**：一次性解決所有最高優先級問題的作法引發社群積極反應，有開發者評論「這才是真正的 quality release」，但也有人好奇原本 backlog 積壓的根因。
- **Mixture-of-Agents 實際效能討論**：MoA 功能讓多個模型協同推理，社群正在測試不同 provider 組合的效果，初步反饋顯示在複雜任務上確有提升，但 token 消耗顯著增加。
- **Hermes Desktop 中文支援受歡迎**：Desktop 版內建完整簡體中文翻譯（100 PRs、159 commits 一週完成），引發對繁體中文與其他語系後續支援的期待。
- **Hackathon 吸引商業導向開發者**：NVIDIA 與 Stripe 的加持讓此次 Hackathon 備受關注，社群討論從純技術擴展至代理的商業模式設計與支付流程整合。
- **自架 Docker 用戶對 Baileys 修復表示感謝**：v2026.7.7.2 的 WhatsApp bridge 修復解決了多位用戶反映的 Docker build 失敗問題，評為「小但重要」的補丁。
- 來源：[Releases · NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent/releases) ／ [Nous Research X](https://x.com/NousResearch/status/2075271466703106384)

---

## 4. 值得追蹤的後續議題

1. **Mixture-of-Agents 效能基準**：MoA 在不同 provider 組合下的實際效能與成本比較，目前缺乏系統性測試數據。
2. **Hermes Desktop 正式版時程**：目前仍為公開預覽（Public Preview），正式版與 Windows 穩定度是社群關注焦點。
3. **繁體中文及多語系支援**：Desktop 版目前僅有簡體中文，繁體中文支援是否列入路線圖尚不明確。
4. **Nous Portal 計費透明度**：300+ 模型統一訂閱的費率結構尚未完整公開，重度使用者需關注後續定價公告。
5. **Evidence-Led Completion 實際驗收標準**：新功能要求任務完成需附帶可驗證依據，但「依據」的定義與驗收邏輯細節尚待社群實測釐清。
6. **iMessage Photon bridge 穩定性**：「Reach Release」引入的 iMessage 整合目前仍有穩定性回報，後續修復進度值得關注。
