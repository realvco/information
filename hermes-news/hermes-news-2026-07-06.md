# Hermes-Agent 每日情報 — 2026-07-06

## 今日重點摘要

1. **v0.18.0「The Judgment Release」（7月1日）社群討論持續延燒**：~1,720 commits、998 merged PRs、949 issues closed、370+ 社群貢獻者，X.com 相關貼文在 7月5-6日仍維持高轉推量。
2. **Hermes MoA 2.0 成媒體頭條（7月5日）**：Tech Times 等媒體大幅報導，MoA 組合 GPT、Claude、DeepSeek 在 HermesBench 超越 Claude Opus 4.8（+8%）和 GPT-5.5（+11%），引發廣泛討論。
3. **零 P0/P1 Bug 里程碑**：v0.18.0 達成所有最高優先度問題全數關閉（約 700 項），社群稱為「罕見的真正品質里程碑」，開源 agent 生態中首見。
4. **/learn 指令正式上線**：可將任意開放來源內容轉化為可重用 skill，無需撰寫程式碼即可擴充 agent 能力。
5. **/journey 記憶時間軸**：可互動的記憶圖，展示 agent 累積的所有記憶與技能，助用戶理解 agent 知識演進軌跡。
6. **Coding Projects 一級功能（Desktop App）**：側欄支援多 codebase 管理，整合 git worktree 管理與 review pane，定位為「真正的程式設計駕駛艙」，直接對標 GitHub Copilot。
7. **NVIDIA 合作曝光**：NVIDIA Blog 刊文介紹 Hermes Agent 在 RTX PC 與 DGX Spark 上的本地部署能力，為硬體生態整合帶來重要背書。
8. **Context limit 崩潰問題（issue #57275）持續受關注**：32k～256k 各規格模型均有用戶回報未達半數 context 時停止壓縮、超限崩潰，v0.18.0 未列 P0/P1 但社群關注度不減。
9. **GitHub star 突破 140,000**：不足三個月達成，OpenRouter 數據顯示 Hermes Agent 為目前全球使用量最高的 agent。

---

## LLM 串接實戰回報

### Nous Portal（官方推薦整合路徑）

- Nous Research 自家的統一訂閱 gateway，OAuth 登入一次涵蓋 300+ frontier agentic models（Claude、GPT、Gemini、DeepSeek、Qwen、Kimi 等）及 Tool Gateway（web search、image gen、TTS、browser automation）。
- 多數社群用戶推薦新手優先使用 Nous Portal，避免個別 provider API key 管理複雜度。（[官方 providers 文件](https://hermes-agent.nousresearch.com/docs/integrations/providers)）

### Claude（Anthropic）

- 社群評選：**Claude Sonnet 4.6 為 Hermes Agent 整體品質最佳模型**。
- MoA 2.0 架構中 Claude 通常扮演 aggregator 角色，因工具呼叫可靠性高，整合多個 reference model 輸出的穩定性最佳。
- **踩坑回報**：context 壓縮 bug（issue #57275）在搭配較低 context 視窗的 Claude 版本時更容易觸發；反壓縮鎖定機制（anti-thrashing lock）設計導致連續兩次壓縮效果不足時鎖死，需手動開新對話。（[Bug Issue #57275](https://github.com/NousResearch/hermes-agent/issues/57275)）
- **建議**：設置 context 下限告警，監控 prompt token 用量，避免超限後 agent 靜默停止。

### GPT-5.x（OpenAI）

- MoA 2.0 設計中 GPT 常作為 reference model 之一，與 Claude、DeepSeek 並列。
- GitHub Copilot 訂閱者可透過 Copilot API 取用 GPT-5.x，無需另備 API key，受預算有限用戶歡迎。
- **踩坑回報**：holographic memory auto_extract 在 MoA 多模型場景下有 bug（issue #57682）——即便在設定中關閉，仍會執行並誤將 context-compaction 摘要注入 fact_store，導致記憶汙染。建議暫時完全停用 holographic memory 功能直到 patch 釋出。（[Bug Issue #57682](https://github.com/NousResearch/hermes-agent/issues/57682)）

### DeepSeek

- 定位為「預算部署首選」，配合 Ollama 使用的 DeepSeek V4 被評為本地化部署成本最低方案。
- MoA reference model 組合中 DeepSeek 擔任 cost-efficient 邏輯推理角色，覆蓋重複性高的子任務。

### Gemini（Google）

- Composio 提供穩定的 Hermes + Gemini MCP 整合，支援結構化工具呼叫、訊息歷史處理及模型協作。（[Composio 整合文件](https://composio.dev/toolkits/gemini/framework/hermes-agent)）
- 免費開發層使 Gemini 成為低成本 reference model 的熱門選擇。

### 本地模型（Ollama/vLLM/SGLang）

- **Llama 4 Maverick via Ollama** 被評為「隱私優先本地部署最佳選擇」。
- Hermes 支援任何 OpenAI-compatible endpoint，包括 vLLM、SGLang 自架，設定方式與雲端 provider 相同。（[Practitioner's Reference](https://blakecrosley.com/guides/hermes)）

---

## 社群反應與討論亮點

- **X.com（7月1日至今）**：@Teknium（Nous Research 創辦人）與 @iamlukethedev 在 v0.18.0 發布後引爆轉推；@NousResearch 官方帳號持續追蹤 MoA 2.0 相關討論。（[Teknium X](https://x.com/Teknium/status/2072416088785359189) ／ [Luke X](https://x.com/iamlukethedev/status/2072416924009418956) ／ [Nous Research X](https://x.com/NousResearch/status/2072413332665962617)）
- **Tech Times（7月5日）**：MoA 2.0 超越單一模型的性能聲稱受媒體廣泛報導；部分社群成員對 HermesBench 為自家評測持保留態度，期待第三方中立基準測試驗證。（[Tech Times](https://www.techtimes.com/articles/319754/20260705/hermes-moa-20-combines-gpt-claude-deepseek-outscore-any-one-model.htm)）
- **NVIDIA Blog**：NVIDIA 刊文介紹 Hermes Agent 在 RTX AI Garage 與 DGX Spark 上的本地自我改進能力，為 Hermes 帶來硬體生態背書。（[NVIDIA Blog](https://blogs.nvidia.com/blog/rtx-ai-garage-hermes-agent-dgx-spark/)）
- **The Agent Report**：報告 Hermes Agent 生態系在 2026 年累計 22 次 release、188k stars（近期更新至 140k+ 數據），稱其為「驅動 agent economy 的開源 runtime」。（[The Agent Report](https://the-agent-report.com/2026/06/hermes-agent-ecosystem-2026-pillar/)）
- **Context 崩潰 issue 社群熱議**：issue #57275 回應熱烈，多人確認 workaround：手動開啟新對話，或調低 agent context 警告門檻至 50%。社群呼籲將此問題升至 P1。

---

## 值得追蹤的後續議題

1. **MoA 2.0 第三方基準測試**：HermesBench 為自家評測，社群期待 MMLU、GPQA、AgentBench 等中立評測驗證 MoA 超越 Claude Opus 4.8 的聲稱。
2. **Context limit 崩潰修復進度（issue #57275）**：目前標 P2 但影響廣泛，預期社群壓力將推動升至 P1 並加速修復。
3. **holographic memory auto_extract bug（issue #57682）**：MoA 多模型場景下 fact_store 汙染問題，需追蹤 patch 釋出時程，影響所有使用 MoA + holographic memory 的用戶。
4. **/learn 指令 skill 品質評估**：非技術用戶建立 skill 的品質參差不齊，值得觀察社群 curation 與品質管控機制如何演進。
5. **Coding Projects 功能深度評測**：作為對標 GitHub Copilot 的定位，等待開發者社群的完整 workflow 評測，特別是 git worktree 管理在大型 monorepo 的穩定性。
6. **Scale-to-zero 企業部署案例**：大規模 drain 協調功能剛上線，尚無公開生產環境案例，追蹤是否有企業用戶分享實際使用結果。
