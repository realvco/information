# Hermes-Agent 每日情報｜2026-07-04

> 資料涵蓋範圍：過去 24 小時（UTC 基準）。搜尋來源包含 GitHub、X.com、社群部落格與官方文件。

---

## 1. 今日重點摘要

1. **v0.18.0「The Judgment Release」於 7 月 1 日正式發布**：這是 Hermes-Agent 迄今規模最大的單一版本，涵蓋約 1,720 個 commits、998 個合入 PR、2,215 個檔案異動，共關閉 949 個 issues，370+ 位社群貢獻者參與其中。
2. **100% 清零所有 P0/P1 issue**：本次發布的核心承諾是關閉 repo 中所有最高優先問題，共約 700 項 P0/P1 issue 全數清零，並宣示未來將維持 P0/P1 數量為零。整體共關閉約 1,950 個 issues 與 PR。
3. **Mixture-of-Agents（MoA）成為正式一級功能**：用戶可在 CLI、TUI、Desktop、Gateway 的模型選擇器中，像選擇單一模型一樣選用「模型集成組合」（ensemble）。各參考模型（如 GPT-5、Claude、Grok）的完整推理輸出會以獨立標記區塊呈現，最終答案改為串流輸出而非一次全部顯示。
4. **自我工作驗證與 /goal 完成合約**：agent 學會對照證據驗證自身輸出，`/goal` 指令新增完成合約機制，`/learn` 與 `/journey` 將自我改進歷程視覺化，用戶可直接觀察與引導 agent 的學習軌跡。
5. **全新 Quick Setup 降低入門門檻**：首次使用者可透過「Quick Setup」（使用 Nous Portal 登入後即可對話）或「Full Setup」兩條路徑完成設定，目標是讓新用戶無需閱讀文件即可發送第一則訊息。模型選擇器支援模糊搜尋，例如輸入「v4fl」即可找到 deepseek-v4-flash。
6. **精簡預設 Skill 集合**：移除 spotify、linear、kanban-codex-lane 等已無實際用途的 Skill，以及多個空分類標記，減少初始安裝的冗餘組件。
7. **Desktop 一級編碼 Projects**：Desktop 應用新增 sidebar 顯示程式碼庫、coding rail、review pane、git worktree 管理，以及供 agent 使用的 project tools，定位與 Coding Projects 功能對齊。
8. **issue #16677 DeepSeek V4 Pro via OpenRouter 導致 gateway 崩潰**：此 bug 首見於 4 月底，DeepSeek V4 Pro 透過 OpenRouter 作為預設模型時，會導致 Hermes Agent gateway 進入 crash loop 並造成 Telegram bot 無回應，目前需確認是否已在 v0.18.0 中修復。

---

## 2. LLM 串接實戰回報

### Claude 整合
- Hermes-Agent 支援直接 Anthropic API 金鑰、`hermes auth add anthropic` OAuth 方式、OpenRouter 代理，以及任何相容 proxy。
- 推薦主力模型：**Claude Sonnet 4.6**（$3/$15 per million tokens 輸入/輸出），在推理品質與 tool calling 的綜合表現上被社群評為首選。
  - 來源：[Best Hermes Agent Model 2026: Claude vs DeepSeek](https://www.remoteopenclaw.com/blog/best-models-for-hermes-agent)
  - 來源：[AI Providers | Hermes Agent - nous research](https://hermes-agent.nousresearch.com/docs/integrations/providers)

### DeepSeek 整合與已知問題
- **Issue #16677（重要）**：使用 `deepseek/deepseek-v4-pro` 作為 OpenRouter 預設模型，會觸發 Hermes Agent gateway 崩潰迴圈，造成 Telegram bot 完全失去回應，且有多種不同失敗模式（首現於 2026 年 4 月 26-27 日）。建議暫時改用其他 DeepSeek 版本（如 deepseek-v4-flash）或直連 DeepSeek API。
  - 來源：[DeepSeek V4 Pro via OpenRouter causes gateway crash loop · Issue #16677 · NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent/issues/16677)
- Hermes 對 DeepSeek 有專屬 tool-call parser（`deepseek_v3`、`deepseek_v31`），可最佳化 function calling 格式相容性。

### OpenRouter 整合
- OpenRouter 提供統一 API key 存取 200+ 模型（含 Llama 4、DeepSeek、Gemma、Mistral），免費方案涵蓋 27+ 模型，每日 200 次請求、每分鐘 20 次請求上限。
- 社群低成本方案推薦：DeepSeek V4（非 Pro 版）透過 OpenRouter 作為夜間長時間任務的主力，token 成本低且路由彈性高。
  - 來源：[How to Run AI Agents Overnight for Almost Nothing: Hermes Agent, DeepSeek V4 and OpenRouter](https://www.aifloxium.online/blog/hermes-agent-deepseek-v4-openrouter-overnight-ai-setup)
  - 來源：[Free Model Providers to Use with Hermes Agent - DEV Community](https://dev.to/dalenguyen/free-model-providers-to-use-with-hermes-agent-13l9)

### Gemini 整合
- Hermes 完整支援 Gemini MCP 整合，包含 structured tool calling、message history handling 及 model orchestration。
- 推薦成本優先選擇：**Google Gemini 2.5 Pro**（$1.25/$10 per million tokens，200K context 以內），性價比在長 context 任務中具優勢。
  - 來源：[How to integrate Gemini with Hermes | Composio](https://composio.dev/toolkits/gemini/framework/hermes-agent)

### Mistral 整合
- Hermes 內建 `mistral` parser 以優化 Mistral 模型的 tool calling 相容性，支援透過 OpenRouter 或直接 Mistral API 存取。

---

## 3. 社群反應與討論亮點

- **「The Judgment Release」發布後社群反應熱烈**：「100% 清零 P0/P1」的承諾引發大量討論，被視為 Hermes-Agent 從「快速迭代」轉向「穩定性優先」的重要里程碑。
  - 來源：[Release Hermes Agent v0.18.0 (2026.7.1) — The Judgment Release · NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent/releases/tag/v2026.7.1)
- **r/LocalLLaMA 討論焦點**：討論集中於三個方向——Hermes 3/4 本地模型在自有硬體上的效能表現、tool-use 可靠性（尤其非 Hermes base model 的 function calling 是否穩定），以及自架成本比較。
- **r/AIAgents 社群**：多有與 AutoGPT 舊框架、Manus AI、CrewAI 及 OpenAI Agents SDK 的比較討論，問題導向較強，偏重特定使用情境下的模型選擇。
- **Mixture-of-Agents 功能討論**：MoA 成為正式功能後，社群開始討論最佳 ensemble 組合（如 GPT-5 + Claude + Grok）的實際效果差異，以及串流輸出延遲是否可接受。
- **Nous Research 官方 Discord**：目前是最即時的討論場所，agent 頻道中有用戶持續分享設定踩坑、模型比較心得與 bug 回報，速度快於 Reddit。
  - 來源：[Hermes Agent Reddit & Community Discussion — Where to Find Honest Feedback](https://openclawlaunch.com/blog/hermes-agent-reddit-discussion)

---

## 4. 值得追蹤的後續議題

1. **Issue #16677 修復確認**：DeepSeek V4 Pro via OpenRouter 的 crash loop 問題是否已在 v0.18.0 中修復，需等待社群實測回報。
2. **MoA 實戰效果評估**：Mixture-of-Agents ensemble 在實際任務（尤其是 coding、research）的準確率與延遲，預計近期將出現系統性比較測試。
3. **P0/P1 零 bug 承諾的持續性**：團隊宣示維持 P0/P1 為零，後續幾週內新 issue 的優先分類與關閉速度是觀察指標。
4. **Desktop Coding Projects 穩定性**：全新的 git worktree 管理與 review pane 功能是 v0.18.0 的重大新增，預期會有使用者陸續回報相容性問題。
5. **/journey 學習時間軸的實際應用**：記憶與 Skill 積累的視覺化功能對長期使用者的實際價值，需觀察社群分享的長期使用報告。
6. **模型選擇最佳實踐更新**：v0.18.0 加入 MoA 後，社群推薦的「預設模型」組合可能出現重新評估，尤其對希望平衡成本與品質的用戶。

---

*本報告由自動化搜尋程序彙整，資訊以搜尋結果為準，不涉及人工推測或補充。*
