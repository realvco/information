# Hermes-Agent 每日情報 — 2026-06-22

## 今日重點摘要

1. **"Reach Release" v2026.6.19 是迄今最大版本**（6 月 19 日發布）：包含約 1,475 commits、800 個合併 PR、關閉 300+ issues、245 位社群貢獻者，是 Hermes-Agent 有史以來規模最大的單次發布。

2. **iMessage 與 Raft agent network 兩條新頻道上線**：iMessage 透過 Photon 整合，讓用戶可透過 Apple 訊息與 Hermes 對話；Raft 為去中心化 agent 網路，擴展跨 agent 協作能力。

3. **原生桌面應用（Electron）正式可用**（6 月 5 日 v2026.6.5）：macOS、Linux、Windows 三平台一鍵安裝，提供串流聊天視窗、Session 列表搜尋/封存、拖曳上傳檔案、剪貼簿圖片貼上、Cmd+K 指令面板、狀態列即時 Model Picker，以及應用程式內自動更新，並於 Reach Release 中加入完整簡體中文翻譯。

4. **Subagent 背景執行支援上線**：子 agent 現可在背景非同步執行，不再阻塞主 agent 工作流。

5. **Kanban 多 agent 任務板（v2026.5.7）**：具備心跳偵測、殭屍偵測、每任務重試、幻覺恢復等韌性機制；`/goal` 指令讓 agent 在多輪對話間鎖定目標（Ralph loop）。

6. **Cursor Composer 透過 xAI Grok 訂閱可達**：Reach Release 新增整合，讓具有 xAI Grok 訂閱的用戶可將 Cursor Composer 的模型能力引入 Hermes 工作流。

7. **目前有兩個 P2 bug 待修**：Issue [#49985](https://github.com/NousResearch/hermes-agent/issues/49985)（核心 agent loop 與 OpenRouter 互動問題）與 Issue [#49983](https://github.com/NousResearch/hermes-agent/issues/49983)（gateway 與 TUI 元件問題），均於 6 月 21 日標記為 P2（中優先），尚待修復。

8. **Image generation 新增編輯功能**：Reach Release 中圖片生成不再限於從無到有，已支援對現有圖片的局部編輯操作。

9. **遠端 gateway 跨裝置連線（OAuth / 帳密）**：桌面應用現可透過 OAuth 或帳號密碼連線至遠端 Hermes gateway，支援多 profile 並行 session。

---

## LLM 串接實戰回報

### Claude（Anthropic）

- **首選推薦**：Claude Sonnet 4.6（$3/$15 per million tokens）在推理品質與工具呼叫穩定性上評價最高，為社群中 Hermes-Agent 部署的主流選擇。
- **踩坑：max_tokens 硬編碼導致 OpenRouter 402 錯誤**（Issue [#22879](https://github.com/NousResearch/hermes-agent/issues/22879)）：Hermes 在 `agent/anthropic_adapter.py` 與 `agent/model_metadata.py` 中將 max_tokens 硬編碼為模型最大輸出值（如 Claude Sonnet/Haiku 4.5 為 64,000）。OpenRouter 採用「預飛預留」機制，即使實際使用量遠低於此，仍因餘額不足觸發 HTTP 402（`This request requires more credits, or fewer max_tokens. You requested up to 64000 tokens, but can only afford 17176.`）。目前 workaround 是在 config.yaml 手動設定較低的 max_tokens，但另一個 bug（Issue [#4404](https://github.com/NousResearch/hermes-agent/issues/4404)）顯示此設定值在部分版本中根本不會被傳遞至 AIAgent，等同無效。
- **踩坑：Anthropic 模型 rate limit / 超額用量**（Issue [#31668](https://github.com/NousResearch/hermes-agent/issues/31668)）：部分用戶反映使用 Anthropic 模型時觸發 rate limit 的頻率高於預期，且計費有時出現異常超額，疑似與 Hermes 的重試邏輯在 rate limit 期間持續發送請求有關。

來源：
- [Issue #22879 — max_tokens hardcoded, breaks OpenRouter](https://github.com/NousResearch/hermes-agent/issues/22879)
- [Issue #4404 — model.max_tokens in config.yaml has no effect](https://github.com/NousResearch/hermes-agent/issues/4404)
- [Issue #31668 — Anthropic models ratelimit/extra usage](https://github.com/NousResearch/hermes-agent/issues/31668)
- [Best Hermes Agent Model 2026: Claude vs DeepSeek](https://www.remoteopenclaw.com/blog/best-models-for-hermes-agent)

### OpenRouter（多模型路由）

- **核心 agent loop 穩定性問題**（Issue [#49985](https://github.com/NousResearch/hermes-agent/issues/49985)）：目前標記為 P2，為 Reach Release 後浮現的新問題，影響透過 OpenRouter 路由的 agent 主要執行迴圈穩定性，建議暫時切換為直連 provider。
- **DeepSeek V4 Pro via OpenRouter 導致 gateway 崩潰**（Issue [#16677](https://github.com/NousResearch/hermes-agent/issues/16677)）：使用 DeepSeek V4 Pro 路由時可能觸發 gateway crash loop，並連帶導致 Telegram bot 失效，已有多名用戶確認此問題；OpenRouter 端的 provider 負載平衡在 5xx 時會自動切換，但 Hermes gateway 崩潰後無法自我恢復。
- **Context window 驗證 bug（2026 年 4 月）**：部分 OpenRouter 模型（如 deepseek-chat-v3-0324，context 16,384 tokens）因低於 Hermes 預設 64K 最低要求而觸發 ValueError，需手動調整設定規避。
- **Fallback Provider 機制**：Hermes 支援在主模型觸及 rate limit 時自動切換至備用模型，但官方文件建議明確設定 fallback 清單以確保 gateway 不崩潰。

來源：
- [Issue #16677 — DeepSeek V4 Pro via OpenRouter crash loop](https://github.com/NousResearch/hermes-agent/issues/16677)
- [Issue #12639 — Native Google/Vertex AI provider bypass OpenRouter 402](https://github.com/NousResearch/hermes-agent/issues/12639)
- [Hermes Agent + OpenRouter: Setup, Model Choice & Routing Config](https://openrouter.ai/blog/tutorials/hermes-agent/)
- [Hermes Agent RateLimitExceeded: 3 Fixes That Work in Production](https://markaicode.com/errors/hermes-agent-rate-limit-exceeded-fix-production/)

### Gemini（Google）

- **目前透過 OpenRouter 或 Google OpenAI 相容端點使用**：原生 Google GenAI provider 尚在開發中（Issue [#12639](https://github.com/NousResearch/hermes-agent/issues/12639)），預期完成後可繞過 OpenRouter 的 402 費用預留問題，提升工具呼叫穩定性。
- **Gemini 2.5 Pro**（$1.25/$10 per million tokens）為目前成本效益最佳選項，提供 1M token context window，適合長文件處理與多輪複雜任務。

來源：
- [Best Gemini Models for Hermes Agent — Google AI Setup Guide](https://www.remoteopenclaw.com/blog/best-gemini-models-for-hermes)
- [AI Providers | Hermes Agent docs](https://hermes-agent.nousresearch.com/docs/integrations/providers)

### Nous Portal（統一訂閱閘道）

- 覆蓋 300+ 前沿 agentic 模型（Claude、GPT、Gemini、DeepSeek、Qwen、Kimi、GLM、MiniMax、Grok 等）加 Tool Gateway（網路搜尋、圖片生成、TTS、瀏覽器自動化），是希望使用單一訂閱管理多模型的用戶最省力的選項。

---

## 社群反應與討論亮點

- **Reach Release 規模震撼社群**：245 位貢獻者、1,475 commits 的統計數字在 GitHub 討論區引發熱議，被形容為「Hermes 有史以來最具野心的 release」。
- **桌面應用好評不斷**：原生 Electron App（v2026.6.5）推出後，社群反應極為正面，特別是拖曳檔案、剪貼簿圖片貼上與 Model Picker 功能，減少了大量原先需要 CLI 指令的操作。
- **OpenRouter 用戶受 max_tokens bug 困擾**：Issue #22879 的 comment 區已累積大量用戶確認遇到相同問題，部分人轉而使用 Nous Portal 或直連 Anthropic/Google API 規避。
- **iMessage Photon 整合引發隱私討論**：iMessage 頻道需透過 Photon 服務中轉，部分用戶質疑訊息是否經過第三方伺服器，社群中有關資料隱私的討論持續進行。
- **Raft agent network 早期探索者分享**：已有數位用戶在 GitHub Discussions 分享 Raft 多 agent 協作的初步測試結果，整體評價積極但穩定性尚待驗證。

---

## 值得追蹤的後續議題

1. **Issue #49985 修復進度**：核心 agent loop 與 OpenRouter 互動的 P2 bug 何時解決，直接影響 OpenRouter 用戶的日常穩定性。
2. **Issue #22879 修復進度**：max_tokens 硬編碼問題修復後，OpenRouter 用戶的 402 錯誤與費用預留問題將大幅改善。
3. **原生 Google GenAI Provider（Issue #12639）**：預期將提升 Gemini 工具呼叫穩定性並解決 OpenRouter 402 費用預留問題，進度值得關注。
4. **Issue #4404 修復進度**：config.yaml 中 max_tokens 設定不生效的 bug，影響所有希望手動控制 token 上限的用戶。
5. **iMessage Photon 整合的隱私保障**：官方是否會公布資料處理方式，社群正在要求更多透明度。
6. **Raft agent network 穩定性與文件完整度**：目前屬早期功能，缺乏完整文件，是否在後續版本中成為一等公民功能值得追蹤。
7. **下一個 release 時程**：Reach Release（v2026.6.19）發布後，下一個版本的方向與時程尚未公布，可訂閱 [releasebot.io](https://releasebot.io/updates/nousresearch/hermes-agent) 追蹤。

---

*資料來源：GitHub NousResearch/hermes-agent、releasebot.io、newreleases.io、openrouter.ai、hermes-agent.nousresearch.com、markaicode.com 等，搜尋時間 2026-06-22 UTC*
