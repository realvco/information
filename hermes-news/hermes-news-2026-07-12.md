# Hermes-Agent 每日情報 — 2026-07-12

> 資料涵蓋範圍：過去 24 小時（UTC 2026-07-11 ～ 2026-07-12）

---

## 1. 今日重點摘要

1. **v0.18.0「Judgment Release」後的持續修補期**：7/1 釋出的 v0.18.0 共含約 1,720 commits、998 個合併 PR、關閉 949 個 issue，且已達成「零 P0、零 P1 open issues」的里程碑；目前社群進入後 release 穩定化階段，P2 與邊緣案例的 patch 持續進行中。
2. **WhatsApp Baileys 依賴修復（7 月初 patch）**：同日補丁將 WhatsApp Baileys 依賴從釘死的 git commit 改為正式發布的 `7.0.0-rc13`，改善安裝可靠性與 Docker tagged-release 建置穩定度。
3. **Mixture-of-Agents（MoA）成為一等公民**：v0.18.0 中 MoA 正式升格為 named ensemble 虛擬模型，可像一般模型一樣選用；每個 reference model 的推理過程與 aggregator 的答案均可即時串流，社群對「跨越前沿模型上限」的討論熱度高。
4. **`/learn` 指令上線**：可指向目錄、URL 或 Workflow，自動提煉為可重用 Skill，並依 CONTRIBUTING.md 慣例自動調整，顯著降低知識轉移成本。
5. **`/journey` 記憶時間軸**：可視化 Hermes 對使用者的累積學習記錄，支援編輯與刪除記憶與 Skill，提升對 agent 行為的可控性。
6. **並行 Subagents 正式支援**：`delegate_task` 現可同時派發多個後台 Subagent，結果匯整至單一 turn，大幅提升複雜工作流程的吞吐量。
7. **Desktop App 新增 Projects 管理**：側邊欄支援多 codebase Projects，含 coding rail、review pane 與 git worktree 管理，對本機開發工作流程影響顯著。
8. **188K GitHub Stars，社群規模持續擴大**：6 月中已突破 188,000 stars，貢獻者超過 370 位，社群 Skill 超過 90,000 個，生態系統成熟度持續提升。

---

## 2. LLM 串接實戰回報

### Anthropic Claude

- **Claude Sonnet 5（2026-06-30 釋出）取代 Sonnet 4.6 成為首選**
  截至 2026-07 推薦清單更新，Claude Sonnet 5 以 $2/$10 per 1M tokens（限時定價至 8/31）成為 Hermes 的主力模型。工具呼叫穩定性優於前代，但使用者注意：透過訂閱 OAuth 路徑使用 Claude 已無法免費，建議改用直接 API key 以獲得清晰的用量回報。
  來源：[Hermes Agent Practitioner's Reference 2026](https://blakecrosley.com/guides/hermes)

- **Hermes Agent 非 Claude 訂閱免費工具**
  有部落客曾誤報「Claude 訂閱涵蓋 Hermes Agent 用量」，後發文更正：Hermes 的 API 呼叫是獨立計費，不包含在 Claude 訂閱內。建議設立獨立 API key 以隔離費用，讓 Hermes 用量在帳單中有清晰的 line item。
  來源：[Michael Lamb Blog — Hermes Agent Cost Correction](https://blog.michaellamb.dev/2026/05/hermes-agent-cost-correction/)

### OpenAI GPT

- **GPT-5.4 取代 GPT-4.1 成為 OpenAI 推薦選項**
  GPT-5+ 系列（gpt-5-mini 除外）自動改用 Responses API，其餘模型（包含 GPT-4o、Claude、Gemini）仍走 Chat Completions；切換時需注意 API 路徑差異，避免設定錯誤。

### Google Gemini

- **Gemini 3.1 Pro 取代 Gemini 2.5 Pro**
  推薦模型已更新至 Gemini 3.1 Pro；透過 LiteLLM 可統一管理多 Provider，支援 fallback chain 與 budget control，建議多 LLM 環境優先採用。
  來源：[Composio — Integrate Gemini with Hermes](https://composio.dev/toolkits/gemini/framework/hermes-agent)

### 429 Rate Limit 與 Token 超限問題

- **Token-per-minute 是最常見瓶頸**：Hermes 每次請求推送的 context 量大，容易在 token/min 層級觸發 429，而非 requests/min。需特別注意各 Provider 的 TPM 限制。
- **429 vs 402 區分**：Hermes 對 rate limit 與 out-of-credits 都回傳 429，但可透過 response header 區分；401 則為 auth 失敗。監控時應細看 header 而非只看 status code。
- **Credential Pool 自動輪換**：可為同一 Provider 設定多組 API key，任一 key 觸發 rate limit 或 billing quota 時自動切換至下一個健康 key，生產環境強烈建議使用。
  來源：[Hermes Agent RateLimitExceeded Fix](https://markaicode.com/errors/hermes-agent-rate-limit-exceeded-fix-production/)、[Credential Pools 文件](https://hermes-agent.nousresearch.com/docs/user-guide/features/credential-pools)

### 本地模型（Ollama / Groq）

- **本地部署免費可行，但需注意 context 上限**：透過 Ollama 或 Groq 可完全免費運行 Hermes Desktop；推薦 Qwen3.5 27B 作為本地模型新選項。但本地模型的 context window 往往低於雲端模型，長對話或大型 codebase 場景需評估。
  來源：[Hermes Agent Desktop Free With Local LLMs](https://www.kunalganglani.com/blog/hermes-agent-desktop-free-local-llm)

---

## 3. 社群反應與討論亮點

- **X.com（Teknium 發文）**：`@Teknium` 發文宣告 v0.18.0 正式上線，強調 MoA、`/learn`、`/journey` 為最具代表性的三大新功能，推文引發大量轉發與討論，評論中不少人對「agent 超越單一模型上限」的框架感到興奮。
  來源：[Teknium on X](https://x.com/Teknium/status/2072416088785359189)

- **NousResearch 官方 X**：`@NousResearch` 同步發文並附上完整 changelog 連結，互動量高。
  來源：[NousResearch on X](https://x.com/NousResearch/status/2072413332665962617)

- **r/LocalLLaMA 討論熱點**：社群對 Hermes models vs Claude/GPT 的工具呼叫可靠性仍有持續辯論，主流結論為「Hermes 模型用於工具呼叫，Claude/GPT 用於需要強推理的任務」；MoA 的出現使這條界線開始模糊。

- **GitHub 貢獻者社群（7/11 近況）**：dickyudhandika 與 PRATHAMESH75 等社群成員的 PR 在 7 月持續合併中；P2 優先度問題（Terminal UI、CLI config、core runtime 相關）仍有數條開放中。

- **LinkedIn — NousResearch 官方貼文**：發布 v0.18.0 完整 changelog，強調「零 P0/P1」的工程紀律，獲得大量從業者按讚與分享。
  來源：[LinkedIn NousResearch](https://www.linkedin.com/posts/nousresearch_hermes-agent-v0180-the-judgement-release-activity-7478476474526916608-Bul4)

---

## 4. 值得追蹤的後續議題

1. **MoA 實戰評測累積**：Mixture-of-Agents 剛升格為一等公民，社群實際使用後的效能、成本與延遲評測將在近期陸續出現，值得追蹤作為模型選擇參考。
2. **`/learn` 與 `/journey` 的隱私與資安討論**：agent 自動學習並持久化記憶的機制引發「資料留存在哪裡、如何稽核」的討論，後續官方說明或社群安全研究值得關注。
3. **Credential Pool 自動輪換的穩定性**：功能文件已完備，但 edge case（如多 key 同時耗盡、輪換時的 in-flight request 處理）的實戰回報尚少。
4. **Gateway scale-to-zero 的生產可靠性**：v0.18.0 新增 Gateway idle 自動縮容功能，但對長連線工作流程的影響（如 mid-turn drain）需更多使用者回報。
5. **並行 Subagent 的費用控制**：`delegate_task` 多 Subagent 並行執行可能導致 token 費用快速累積，期待社群分享實際成本控制策略。
6. **v0.18.x patch release 節奏**：零 P0/P1 是里程碑，但 P2 仍有數條開放；觀察 v0.18.1 或後續 patch 的釋出時間與修復範圍。

---

*報告產生時間：2026-07-12 UTC*
*來源參考：[GitHub NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent) / [v0.18.0 Release](https://github.com/NousResearch/hermes-agent/releases/tag/v2026.7.1) / [Teknium on X](https://x.com/Teknium/status/2072416088785359189) / [Releasebot](https://releasebot.io/updates/nousresearch/hermes-agent) / [Credential Pools 文件](https://hermes-agent.nousresearch.com/docs/user-guide/features/credential-pools)*
