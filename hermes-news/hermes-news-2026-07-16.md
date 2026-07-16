# Hermes-Agent 每日情報 — 2026-07-16

## 1. 今日重點摘要

1. **v0.18.0「The Judgment Release」發布（2026.7.1）**：Hermes-Agent 於 7 月 1 日發布最新版本，包含約 1,720 commits、998 個合併 PR、2,215 個檔案變更，共有 370+ 位社群貢獻者參與。
2. **史上最大規模技術債清理**：團隊解決了 repo 中所有 P0 與 P1 級別的 issues 和 PR，共清理約 700 個最高優先項目，並承諾未來維持 P0/P1 清零原則。其中 P0 issues 共 3 個、P0 PRs 共 8 個全數關閉；P1 issues 493 個、P1 PRs 188 個全數合併。
3. **Mixture-of-Agents（MoA）成為一等公民**：MoA 現在可在所有模型選擇器（CLI、TUI、desktop、gateway）中作為獨立 provider「moa」選取，與 Claude、GPT、Grok 並列，可直接在任務中指定使用。
4. **Background subagent fan-out**：Hermes 現支援在對話不阻斷的情況下將多個任務委派給背景 subagent 並行執行，大幅提升複雜工作流效率。
5. **/journey 記憶時間線**：新功能 `/journey` 提供可編輯的技能與記憶時間線，桌面版還新增記憶圖譜可視化（memory graph）；`/learn` 可從目錄、URL 或工作流中萃取可重用技能。
6. **Agent 自驗證機制**：Hermes 現可在完成 coding 任務後自動執行專案檢查來驗證結果，`/goal` 功能也支援設定「完成合約」（completion contracts），讓 agent 明確知道什麼狀態算「完成」。
7. **桌面版 coding Projects 正式上線**：桌面應用新增一等公民的 coding project 支援，整合 codebase 瀏覽、git worktree 管理，以及供 agent 操作的 project 工具。
8. **GPT-5.6 整合出現 catalog 落差**：OpenAI 於 7 月 9 日正式公開 GPT-5.6 Sol/Terra/Luna，但 Hermes 的 openai-api 直連 catalog 目前仍停留在 GPT-5.5/5.4；若需使用 GPT-5.6，需改走 OpenRouter 或 Nous catalog。
9. **費用警告：GPT-5.6 的 token 消耗遠超預期**：開發者 Theo 記錄到 GPT-5.6 Sol 單一任務耗費高達 $569 美元的案例；Fast mode 燃燒 token 速率為正常速率的 2.5 倍，一個 Ultra prompt 可能消耗整週配額的 37.5%。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）

- **Chat Completions API 路由**：Hermes 使用 Chat Completions API 路由 Claude 模型。官方建議搭配 Claude Code 的憑證存儲（credential store）存放 Anthropic OAuth token，而非複製到 `~/.hermes/.env`，以保持 token 可刷新。
  - 來源：[AI Providers — Hermes Agent 官方文件](https://hermes-agent.nousresearch.com/docs/integrations/providers)
- **Anthropic Agent SDK 積分分離**：自 2026 年 6 月 15 日起，Anthropic 將 Agent SDK 與 claude-p 使用量從互動式訂閱限制中分離，引入獨立的月費 Agent SDK 積分，涵蓋「透過 Agent SDK 認證的第三方應用」。這對以 subscription OAuth 接入 Hermes 的用戶影響重大。
  - 來源：[GitHub Issue #25267 — Claude Agent SDK 訂閱 OAuth](https://github.com/NousResearch/hermes-agent/issues/25267)

### GPT-5.6（OpenAI）

- **catalog 落差問題**：GPT-5.6 Sol/Terra/Luna 於 7 月 9 日 GA，但 Hermes 的 openai-api 直連 catalog 尚未更新（仍停在 GPT-5.5/5.4）。目前解法：改用 OpenRouter 或 Nous catalog 路由至 GPT-5.6。
  - 來源：[GitHub Issue #61623 — 新增 GPT-5.6 至 openai-api catalog](https://github.com/NousResearch/hermes-agent/issues/61623)
- **費用失控警告**：GPT-5.6（尤其 Sol）訓練為主動調用 subagent，比 GPT-5.5 更積極；在 Hermes 的 fan-out 架構下，一個任務可能觸發大量子任務。建議設置費用上限或改用 Terra/Luna 進行一般工作。
  - 定價（截至 2026.7）：Sol $5/$30、Terra $2.50/$15、Luna $1/$6（每 100 萬 token）
  - 來源：[BigGo Finance — 開發者 Theo 費用紀錄](https://finance.biggo.com/news/6fcddcdb28464798)
  - 來源：[Moe Lueker — GPT-5.6 Sol + Hermes 完整設定](https://moelueker.com/blog/gpt-5-6-sol-hermes-agent-no-api-bill)

### Hermes 模型（本地 / Nous Research）

- **工具呼叫格式優勢**：社群共識認為 Hermes 模型（Hermes 3 / Hermes 4）的 function-calling 格式專為工具使用優化，在 Hermes Agent 工作流中可靠性高於 Claude 或 GPT 等通用模型，尤其在 structured tool output 的場景。
  - 來源：[r/LocalLLaMA 社群討論整理](https://openclawlaunch.com/blog/hermes-agent-reddit-discussion)
- **Mixture-of-Agents 最佳實踐**：MoA 現為一等公民後，社群開始探索「Hermes 模型負責 tool use + Claude/GPT 負責高推理任務」的混合配置，早期測試結果顯示費用與效能均可改善。

---

## 3. 社群反應與討論亮點

- **v0.18.0「Judgment Release」廣受好評**：社群對本次零 P0/P1 的發布目標讚譽有加，認為是開源 agent 框架少見的工程紀律展現。TonyReviewsThings 評測文章標題即為「Ships With Zero Open Critical Bugs」。
  - 來源：[v0.18.0 評測](https://www.tonyreviewsthings.com/hermes-agent-v0180-judgement-release/)
- **Reddit 各社群關注焦點分工明確**：
  - `r/LocalLLaMA`：聚焦 Hermes 3/4 模型在本地硬體的表現與工具呼叫可靠性
  - `r/selfhosted`：聚焦部署運維層面（scale-to-zero、gateway drain 等）
  - `r/AIAgents`：聚焦產品用途與實際應用場景
  - `r/singularity`：聚焦 Nous Research 長期路線圖與開放 vs 閉源辯論
- **費用爭議最熱**：GPT-5.6 在 Hermes fan-out 架構下的費用問題引發最多討論。社群建議「用 GPT-5.6 Sol 做規劃決策，用 Terra/Luna 做執行層」，以平衡能力與費用。
  - 來源：[AI Automation Society — 如何提高 rate limit](https://www.skool.com/ai-automation-society/hermes-agent-how-do-i-increase-rate-limits)
- **MoA 功能的社群期待**：Mixture-of-Agents 成為一等公民後，社群正在測試各種 ensemble 配置，但目前文件尚未完整，社群討論集中在如何命名 preset 及設定路由規則。

---

## 4. 值得追蹤的後續議題

1. **GPT-5.6 直連 catalog 更新**（Issue #61623）— Hermes openai-api 直連 catalog 何時納入 GPT-5.6 Sol/Terra/Luna，是當前用戶最關注的功能缺口。
2. **Anthropic Agent SDK 積分額度細節** — 新的月費積分制度對重度 Hermes + Claude 用戶的實際費用影響，社群尚待更多實測數據。
3. **MoA 文件與 preset 設定規範** — Mixture-of-Agents 成為一等公民，但配置文件仍不完整；後續官方文件補充值得追蹤。
4. **GPT-5.6 fan-out 費用控制最佳實踐** — 社群正在形成 budget cap 與模型分層策略，值得持續追蹤是否出現官方建議文件。
5. **v0.19.0 方向** — The Judgment Release 宣稱清零 P0/P1，下一版本的功能規劃（尤其 scale-to-zero gateway 與 /journey 擴展）值得關注。
6. **iMessage via Photon 與 Raft agent network** — v0.17.0 引入的兩個新頻道的穩定性與使用體驗，社群回饋仍在累積中。

---

*資料來源*：
- [NousResearch/hermes-agent GitHub Releases](https://github.com/NousResearch/hermes-agent/releases)
- [v0.18.0 The Judgment Release](https://github.com/NousResearch/hermes-agent/releases/tag/v2026.7.1)
- [Hermes Agent Blog — v0.18.0](https://hermesagents.net/blog/hermes-agent-v0-18-0-judgment)
- [AI Providers 官方文件](https://hermes-agent.nousresearch.com/docs/integrations/providers)
- [GitHub Issue #61623 — GPT-5.6 catalog](https://github.com/NousResearch/hermes-agent/issues/61623)
- [GitHub Issue #25267 — Claude Agent SDK OAuth](https://github.com/NousResearch/hermes-agent/issues/25267)
- [BigGo Finance — GPT-5.6 費用案例](https://finance.biggo.com/news/6fcddcdb28464798)
- [Releasebot — Hermes Agent July 2026](https://releasebot.io/updates/nousresearch/hermes-agent)
