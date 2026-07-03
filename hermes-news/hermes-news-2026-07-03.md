# Hermes-Agent 每日情報 — 2026-07-03

> 資料涵蓋範圍：2026-07-02 ～ 2026-07-03（UTC）

---

## 1. 今日重點摘要

1. **v0.18.0「The Judgment Release」於 7 月 1 日正式發布，社群反應持續發酵**：這是 Hermes-Agent 迄今規模最大的單次釋出——~1,720 commits、998 個合併 PR、2,215 個檔案變更、~251,000 行新增、~41,000 行刪除、949 個 issue 關閉，370+ 位社群貢獻者參與。

2. **100% P0/P1 issue 清零**：官方將本版核心成就定義為「完整清除所有 repo 內高優先度問題」，共關閉約 700 項最高優先議題，總計約 1,950 個 issues/PR，被社群稱為「feel different」的里程碑式版本。

3. **Mixture-of-Agents（MoA）晉升為一等公民模型**：MoA 功能不再是外掛技能，而是可在 CLI、TUI、桌面 app、Gateway 的所有模型選擇器中直接挑選的「moa 提供者」底下的命名 ensemble。每個 preset 由多個 reference models 先行輸出，再由 aggregator model 整合出最終答案。

4. **Work Verification 機制上線**：Hermes 現在可記錄 coding 工作的驗證證據（執行測試、型別檢查等），並結合 `/goal` 的 completion contracts，讓 agent 根據可觀察的結果自主判斷任務是否完成，減少無效迴圈。

5. **`/learn` 指令：任意轉技能**：用自然語言描述一個目錄、URL 或工作流程，Hermes 即可將其轉換為可複用的 skill，進一步降低使用者客製化 agent 行為的門檻。

6. **Desktop Projects 正式推出**：桌面 app 獲得真實的 per-profile Projects 功能，包含程式庫側欄、coding rail、review pane 與 git worktree 管理，對開發者工作流整合更完整。

7. **`/journey` 時間軸 + memory graph**：`/journey` 呈現記憶與技能的學習時間線；配套的桌面 memory graph 提供視覺化，讓使用者可以檢視 agent 的知識積累狀態。

8. **v0.18.0 發布後，新 issue 於 7/2 湧現**：發布次日即有多筆 bug 回報，包括 #57385（CLI entry point 與 setup wizard）、#57381（plugin system）、#57405 與 #57395（核心 agent runtime 及 TUI 元件），顯示大型版本後的初期穩定化工作已開始。

9. **MoA 成本預警引發社群關注**：雖然 HermesBench 顯示兩模型 MoA preset（Claude Opus 4.8 彙整 GPT-5.5）可達 +7.82% 的品質提升，但實際使用者回報在 OpenRouter 上的費用約為單獨使用 Opus 的 **80 倍**，成本效益比正在被社群審視中。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）
- **claude-opus-4.6 context 限制差異**：同一個模型在不同服務路徑下 context 上限不同——Anthropic 直連為 **1M tokens**，而透過 GitHub Copilot 則降至 **128K tokens**。Hermes-Agent 文件已明確說明這是 provider-aware 的行為，使用者需依路徑調整工作流設計。
- **Fable 5 與 Mythos 5 已於 6/12 暫停**：Anthropic 自 2026 年 6 月 12 日起對所有客戶暫停 Fable 5 與 Mythos 5 的 API 存取，造成部分使用高階模型的 Hermes 工作流需緊急切換替代模型，也被認為是 MoA 功能需求升溫的背景因素之一。
- **MoA 實測**：以 Claude Opus 4.8 作為 aggregator、GPT-5.5 作為 reference 的 two-model preset，HermesBench 得分 0.8202 vs. Opus 單獨的 0.7607（提升約 6 個百分點），但 OpenRouter 費用約為 80 倍，需評估是否划算。
- 來源：[AI Providers | Hermes Agent docs](https://hermes-agent.nousresearch.com/docs/integrations/providers)、[Hermes Mixture of Agents benchmarks - AI Success Lab](https://aisuccesslabjuliangoldie.com/blog/hermes-mixture-of-agents/)

### OpenAI / GPT
- **GPT-5.5 作為 MoA reference model**：在 benchmark 中表現良好，搭配 Opus 4.8 aggregator 可達到超越單一模型的品質，但成本問題（見上）需特別留意。
- 來源：[I tried Hermes Agent's MoA - DevelopersIO](https://dev.classmethod.jp/en/articles/hermes-agent-moa-first-touch/)

### OpenRouter 與多 provider 場景
- Hermes-Agent 支援 OpenRouter（200+ 模型）、Nous Portal、MiniMax、Kimi，以及任何 OpenAI 相容端點（自架 Ollama、vLLM、SGLang 均可）。
- **Rate limit 處理**：目前官方策略為「等待後重試」，尚無內建自動退避（exponential backoff）機制，高並行工作流需自行處理。
- 來源：[FAQ & Troubleshooting | Hermes Agent docs](https://hermes-agent.nousresearch.com/docs/reference/faq)、[Configuration | Hermes Agent docs](https://hermes-agent.nousresearch.com/docs/user-guide/configuration)

---

## 3. 社群反應與討論亮點

- **X.com 開發者 @iamlukethedev 貼文廣傳**：「Hermes Agent v0.18.0 just dropped — This is The Judgement Release. 0 open P0/P1 issues. The team went full cleanup mode and closed roughly 700 highest-priority issues and PRs. That alone makes this release feel different.」引起大量轉發與討論。
  - 來源：[X.com @iamlukethedev](https://x.com/iamlukethedev/status/2072416924009418956)

- **YouTube 影片「Hermes Agent Update v0.18 is HUGE! (Judgment Release)」**：已有評測影片發布，聚焦 MoA、Work Verification 與 Desktop Projects 三大功能的實際示範，社群在留言區積極詢問 MoA 費用控制方式。

- **r/LocalLLaMA 持續為主討論陣地**：關於 Hermes 3/Hermes 4 在本地硬體上的效能、tool-use 可靠性，以及自架成本與雲端 API 的比較，在此 subreddit 有最高密度的技術討論。

- **MoA 成本討論熱度上升**：+7.82% 品質換 80x 成本，社群開始出現「selective MoA」討論——只在最難任務中啟用 MoA，其餘維持單一模型。

- **Hermes Agent Community on X（6.9K 成員）**：官方 X Community 持續活躍，setup 問題、config 分享與 bug 回報為主要討論類型。

---

## 4. 值得追蹤的後續議題

| 議題 | 說明 |
|------|------|
| v0.18.0 後續 hotfix | #57385/#57381/#57405/#57395 等 7/2 新開 issue，是否會有快速 patch release 值得留意 |
| MoA 成本最佳化 | 官方是否提供 MoA 費用估算工具或 token budget 限制機制，社群需求明確 |
| Work Verification 實際效果 | completion contract 在複雜任務中的假陽性/假陰性率，目前實測回報不多 |
| Fable 5 / Mythos 5 解禁時間 | Anthropic 暫停至今已超過 3 週，後續何時恢復或有替代模型，直接影響部分 Hermes 工作流 |
| `/learn` 技能品質評估 | 自動生成技能的可靠度與可維護性，社群尚無系統性測試結果 |
| Desktop Projects git worktree 整合穩定性 | 新功能，邊界情況（衝突、大型 repo）的實際回報待觀察 |

---

*來源參考：*
- [GitHub: NousResearch/hermes-agent releases](https://github.com/NousResearch/hermes-agent/releases)
- [Release v0.18.0 (The Judgment Release)](https://github.com/NousResearch/hermes-agent/releases/tag/v2026.7.1)
- [Changelog — Hermes Agent | Nous Research](https://hermes-ai.net/changelog/)
- [AI Providers | Hermes Agent docs](https://hermes-agent.nousresearch.com/docs/integrations/providers)
- [FAQ & Troubleshooting | Hermes Agent docs](https://hermes-agent.nousresearch.com/docs/reference/faq)
- [Hermes Mixture of Agents - AI Success Lab](https://aisuccesslabjuliangoldie.com/blog/hermes-mixture-of-agents/)
- [I tried Hermes Agent's MoA - DevelopersIO](https://dev.classmethod.jp/en/articles/hermes-agent-moa-first-touch/)
- [X.com: @iamlukethedev](https://x.com/iamlukethedev/status/2072416924009418956)
- [Hermes Agent v0.18.0 Ships With Zero Open Critical Bugs](https://www.tonyreviewsthings.com/hermes-agent-v0180-judgement-release/)
- [GitHub: NousResearch/hermes-agent issues](https://github.com/NousResearch/hermes-agent/issues)
