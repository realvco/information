# Hermes-Agent 每日情報 — 2026-07-13

> 搜尋時間範圍：過去 24 小時（UTC）
> 資料來源：官方 X.com、GitHub、社群論壇、技術部落格

---

## 1. 今日重點摘要

1. **v0.18.0「The Judgment Release」（2026-07-01）**：Nous Research 完成史上最大規模的 issue 清理——關閉約 949 個 issue，其中包含所有 P0 與 P1 問題（共約 700 項），匯集 370+ 位社群貢獻者、~1,720 次 commit 及 998 個 merged PR，是穩定性的重要里程碑。

2. **v0.18.2 補丁版本（2026-07-07）**：針對 Desktop App 及 Windows 特定行為的 bug 修正，以及 CLI/dashboard 元件穩定性改善。

3. **GPT-5.6 支援上線（2026-07-09）**：Nous Research 官方 X 帳號宣佈 GPT-5.6 已整合至 Hermes Agent 並可透過 Nous Portal 使用，該推文獲超過 108,000 次瀏覽、89 則回覆，引發社群熱烈討論。

4. **原生 Desktop App 正式推出**：跨平台 Electron 應用程式（macOS/Linux/Windows）支援一鍵安裝、應用內自動更新、拖曳檔案入 Chat、Cmd+K 命令面板、狀態列模型切換器、多 profile 並行 session，並已完整翻譯繁體中文及簡體中文。

5. **Mixture-of-Agents（MoA）成為一等功能**：可建立命名模型組合（ensembles），像選單一樣選擇，所有 reference model 的推理過程可視，aggregator 回答實時串流，適合高可靠性或多角度驗證的工作流。

6. **新通訊管道：iMessage（via Photon）與 Raft Agent Network**：Hermes Agent 現可透過 Photon 整合 iMessage，並加入 Raft agent network，進一步擴展跨平台 agent 協作能力。

7. **Nous Portal OpenRouter 使用量排名第一**：截至 2026 年 6 月，Hermes Agent 在 OpenRouter 應用排名中以總 token 使用量奪冠，累計超過 17 兆 tokens，顯示使用規模持續擴大。

8. **Skill Hub 新增 NVIDIA/skills tap**：Curator 現可修剪未使用的內建 skill，並追蹤每個 skill 的使用紀錄；NVIDIA 加入 OpenAI、Anthropic、HuggingFace 成為預設受信任的 Skills Hub 來源。

9. **/prompt 指令與 /undo 功能**：`/prompt` 可開啟編輯器撰寫多行 Markdown 格式的 prompt；`/undo` 可撤回最近 N 個對話輪次。

---

## 2. LLM 串接實戰回報

### 2-1. GPT-5+ 模型自動使用 Responses API

- **行為說明**：GPT-5+ 系列模型（gpt-5-mini 除外）在 Hermes Agent 中自動切換至 Responses API，其他模型（GPT-4o、Claude、Gemini 等）仍使用 Chat Completions。
- **注意點**：若自行配置 OpenAI-compatible proxy，需確認 proxy 端是否支援 Responses API 格式，否則可能出現格式不相容錯誤。
- **來源**：[AI Providers 文件 - Hermes Agent](https://hermes-agent.nousresearch.com/docs/integrations/providers)

### 2-2. Claude 串接：Agent SDK OAuth 與 API Key 並行支援

- **OAuth 整合**：`hermes auth add anthropic` 可新增 Anthropic OAuth 認證；Hermes 優先使用 Claude Code 的憑證存放（credential store），維持 token 的自動更新（refreshable）能力。
- **重要變化（2026-06-15）**：Anthropic 將 Agent SDK 及 `claude -p` 的使用從互動訂閱限制中分離，引入獨立的月度 Agent SDK 額度，涵蓋透過 Agent SDK 訂閱授權的第三方應用程式使用量。
- **建議**：使用 Hermes + Claude 時應確認是否已升級至支援新 OAuth 流程的版本，避免因舊有 token 格式被拒而觸發非預期的 auth 失敗。
- **來源**：[GitHub Issue #25267 - Claude Agent SDK OAuth](https://github.com/NousResearch/hermes-agent/issues/25267) / [FAQ & Troubleshooting](https://hermes-agent.nousresearch.com/docs/reference/faq)

### 2-3. OpenRouter 整合：Rate Limit 管理與自動容錯

- **使用量**：OpenRouter 免費方案涵蓋 200+ 模型，每日 200 次請求、每分鐘 20 次請求。
- **自動 load balancing**：OpenRouter 會自動在 provider 之間平衡負載；若某 provider 返回 5xx 或觸發 rate limit，自動切換，適合長時間執行的 agent 任務。
- **DeepSeek V4 / R1 整合**：DeepSeek V4 支援 1M token context window，與 Claude Sonnet 4.6 及 GPT-4.1 相當；R1 適合推理密集型工作流，兩者均可透過 OpenRouter 或 DeepSeek API 直接接入。
- **本地模型備援**：在重度使用期間若觸及 rate limit，建議降低 subagent 並發數、避免 retry loop，或切換至本地模型（如 Ollama）處理非緊急任務。
- **來源**：[OpenRouter for Hermes Agent](https://openrouter.ai/blog/tutorials/hermes-agent/) / [Hermes Agent Model Provider Costs & Rate Limits](https://hermes-agent.ai/blog/hermes-agent-model-provider-costs-rate-limits)

### 2-4. DeepSeek 模型在 Hermes 工作流的效能

- **V4**：適合一般目的 agent 任務，1M context 適合長文件處理。
- **R1**：推理密集場景（如多步驟規劃、程式碼生成）效果顯著，但 SGLang 預設回應上限僅 128 tokens，**部署時必須在 request 中明確指定 max_tokens**，否則回應會被截斷。
- **來源**：[Best DeepSeek Models for Hermes Agent](https://www.remoteopenclaw.com/blog/best-deepseek-models-for-hermes)

---

## 3. 社群反應與討論亮點

- **X.com**：[@NousResearch](https://x.com/NousResearch) 宣佈 GPT-5.6 支援的推文（2026-07-09）獲超過 10.8 萬次瀏覽，社群評論以「終於等到了」、「MoA + GPT-5.6 組合有多強」為主要討論方向。Desktop App 在 Jensen Huang GTC 主題演講中首次展示，正式 public preview 推出後引發媒體廣泛報導。

- **GitHub**：v0.18.0 的 Judgment Release 清零所有 P0/P1 issue，社群在 PR #949 附近留下大量感謝及慶賀留言，被視為「讓 Hermes 從實驗性轉向可靠生產工具的分水嶺」。

- **Reddit & r/LocalLLaMA**：MoA 功能討論熱度高，用戶實測在 Hermes + OpenRouter 上組合多個免費模型（DeepSeek R1 + Claude Haiku + GPT-4.1 mini）的效果，部分用戶認為可達接近旗艦模型的品質但成本降低 70% 以上。

- **Nous Discord**：Desktop App 的 Simplified Chinese 翻譯被台灣及中國用戶廣泛討論，有用戶反映繁體中文支援尚不完整，正在等待後續更新。

- **Hacker News**：The Judgment Release 登上 HN，討論圍繞「一次性清空 700 個最高優先級 issue 的工程管理方法」及「開源 agent 框架的 P0 issue 定義標準」。

---

## 4. 值得追蹤的後續議題

| 議題 | 說明 |
|------|------|
| GPT-5.6 Responses API 相容性 | 新模型支援剛上線，與各 proxy / OpenRouter 路由的相容性細節待社群回報 |
| Desktop App Windows 穩定性 | v0.18.2 修正了部分 Windows 問題，但 GitHub 仍有新 issue 開啟中 |
| MoA 效能基準測試 | 社群尚無系統性基準，MoA vs 單一旗艦模型的 token 成本 / 品質比值得量化 |
| iMessage via Photon 隱私疑慮 | 新通訊管道涉及訊息路由，技術細節與資料留存政策尚未完整公開 |
| 繁體中文本地化完整度 | Desktop App 目前主要翻譯為簡體中文，繁體中文支援進度待追蹤 |
| Claude Agent SDK 月度額度 | Anthropic 新的獨立 Agent SDK 額度計算方式，使用量多的用戶需確認計費影響 |
| Skill Hub NVIDIA tap 實用性 | NVIDIA 正式成為受信任 tap，後續提供哪些 GPU 加速或推論相關 skills 值得關注 |

---

*本報告依據截至 2026-07-13 UTC 的公開資訊整理，若有錯誤或遺漏請至 [GitHub Issues](https://github.com/NousResearch/hermes-agent/issues) 反映。*
