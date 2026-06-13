# OpenClaw 每日情報 — 2026-06-13

> 資料蒐集時間：2026-06-13 UTC｜涵蓋過去 24 小時公開資訊

---

## 1. 今日重點摘要

1. **穩定版 v2026.6.5 於 6 月 9 日正式發布**：採用新版本號命名規則（YYYY.M.PATCH），本月第 5 個 patch，含逾 30 項修正與改善。（過去 24 小時內無新 stable 版本）
2. **GitHub Prerelease 推進至 v2026.6.5-beta.2**：beta 持續迭代，為下一個 stable 版本累積修正，是目前最新可用的預覽構建。
3. **QQBot 輸出優化**：v2026.6.5 中，QQBot 在發送前自動剝除模型的 reasoning/thinking 標籤，使用者只收到最終回答，內部推理過程不外露。
4. **MCP 工具結果塊相容性修正**：非文字/圖片類型的 MCP tool-result block 現已在進入 provider converter 之前完成型別強制轉換，消除了先前可能產生的畸形 image block 問題。
5. **六大 LLM Provider 支援、Runtime 切換無需重建**：自 4 月起，provider manifest 允許在運行中的工作流直接切換模型大腦，支援 GPT-5.5 (Codex)、Claude API、Gemini、DeepSeek、OpenRouter、Ollama/LM Studio、Gemma 4。
6. **依賴更新**：Android、Swift/macOS、Docker、CodeQL、Buildx、Docker build/push、Codex Action 等平台依賴均在此版本同步更新。
7. **session 恢復穩定性提升**：runtime 狀態管理改善，provider 穩定性加強，auth 與 storage 路徑更可靠。
8. **ClawHub 達 4,000+ 社群 Skill**：ClawHub 已成為首選 skill 安裝來源，npm 為次要路徑。
9. **GitHub 星標累積至 347,000+**（4 月數據），仍是近期成長最快的 AI Agent 框架之一。

---

## 2. LLM 串接實戰回報

### Claude (Anthropic)
- **推薦起點**：Claude Sonnet 4.6 被多數社群評測列為「最佳起步選擇」，工具呼叫準確率高，與 ClawHub skill 相容性佳。
- **Anthropic Vertex AI** 支援已於 3 月版本加入，企業用戶可透過 GCP 路由 Anthropic 請求。
- 設定方式：`openclaw onboard` 引導介面中輸入 Anthropic API Key 即可完成對接。
- 來源：[Setup OpenClaw with Claude & Gemini](https://vertu.com/ai-tools/the-ultimate-guide-setting-up-openclaw-with-claude-code-and-gemini-3-pro)

### Gemini (Google)
- **Gemini 3.1 Pro** 在社群評測中被評為「最適合大多數 OpenClaw 部署場景」：大 context 程式碼審查、每 job 成本最低、免費 dev tier、原生多模態。
- Google Gemini TTS 支援已在 4 月版本整合。
- 來源：[Best LLM for OpenClaw: Gemini 3.1 Pro vs GPT-5.5 vs Claude Opus 4.7](https://dev.to/matthewrevell/best-llm-for-openclaw-gemini-31-pro-vs-gpt-55-vs-claude-opus-47-2026-3na4)

### GPT-5.5 (OpenAI / Codex)
- 透過 Codex provider 接入，適合已有 OpenAI 訂閱的用戶。
- 4 月版本將預設模型升級為 GPT-5.40，並加入 Codex-native 工具鏈。

### DeepSeek
- **DeepSeek V3.2** 被定位為「預算敏感場景首選」，成本顯著低於主流閉源模型。
- 來源：[Best Models for OpenClaw in 2026](https://similarlabs.com/blog/best-models-for-openclaw)

### 踩坑紀錄
- **小型模型（Llama 3.1 8B、Mistral 7B）易出現 skill call 格式錯誤**：tool-calling accuracy 不足，ClawHub skill 呼叫時可能產生畸形 JSON，導致 agent 卡住或無限重試。建議生產環境避免使用 7B/8B 等級模型執行複雜 skill。
- 來源：[OpenClaw April 2026: 6 Model Providers](https://www.mindstudio.ai/blog/openclaw-april-2026-model-agnostic-provider-manifest)

---

## 3. 社群反應與討論亮點

- **Reddit (r/OpenClaw、r/Clawdbot)**：持續討論 Discord 權限設定與 WhatsApp gateway 配置；費用討論聚焦於 Claude Max 訂閱透過 OpenClaw 路由（低至中流量）vs 直接 API 計費（高流量）的性價比比較。
- **Discord #help / #users-helping-users**：常見問題圍繞多 messaging layer 的 auth 設定，社群互助氣氛活躍。
- **Weekly Claw 社群活動**：定期舉辦線上分享，由志願者協調主講人與錄影，是了解最新實戰心得的主要管道之一。
- **Releasebot 追蹤**：[OpenClaw Release Notes - Releasebot](https://releasebot.io/updates/openclaw) 提供自動化版本追蹤，方便快速掌握最新 patch。
- 來源：[OpenClaw Community GitHub](https://github.com/openclaw/community)、[OpenClaw Reddit](https://clawmage.ai/blog/openclaw-reddit/)

---

## 4. 值得追蹤的後續議題

1. **v2026.6.5-beta.2 → stable 進程**：目前 npm latest 仍停在 2026.6.1，beta-2 的修正預計將成為下一個 stable；可追蹤 [openclaw/openclaw releases](https://github.com/openclaw/openclaw/releases)。
2. **小型模型 skill call 相容性**：社群期待官方提供更強的 fallback 機制或 schema 驗證層，降低 7B 等級模型的錯誤率。
3. **Claude Max 訂閱透過 OpenClaw 的成本效益**：目前社群對於「訂閱路由 vs API key 直連」的費用計算仍有分歧，建議持續觀察官方文件更新。
4. **ClawHub skill 生態擴張**：4,000+ skills 的品質差異大，社群呼籲建立更完善的 rating 機制。
5. **多 provider 熱切換穩定性**：runtime 切換 LLM provider 功能仍在持續穩定化中，建議生產環境謹慎使用。

---

*本報告依據公開搜尋結果整理，過去 24 小時（2026-06-12 至 2026-06-13）未偵測到新的 stable 版本發布或重大公告，主要資訊源自 6 月上旬發布的版本與社群討論。*
