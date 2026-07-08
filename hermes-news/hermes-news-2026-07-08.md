# Hermes-Agent 每日情報 — 2026-07-08

> 搜尋範圍：過去 24 小時（2026-07-07 ～ 2026-07-08 UTC）

---

## 1. 今日重點摘要

1. **v0.18.0「Judgment Release」餘波持續延燒**：7 月 1 日發布的重大版本自本週起在社群廣泛討論，X.com 上 Nous Research 官帳及核心成員 Teknium 的貼文累計大量轉發，被形容為「近幾個月最有分量的一個版本」。
2. **首次達成零 P0 / 零 P1 開放 Issue**：v0.18.0 共關閉約 700 個最高優先度 issues 與 PR，距上一版本計有 ~1,720 commits、998 merged PRs、949 closed issues，創下 Hermes-Agent 有史以來單版本最大清理規模。
3. **Mixture-of-Agents 成為一等公民**：可建立命名 ensemble（多個模型組合），像選擇普通模型一樣在 model picker 中選取；每個參考模型的推理過程皆可見，aggregator 結果即時串流顯示。
4. **/learn 指令上線**：可指向目錄、URL 或剛才走過的操作流程，自動蒸餾為可重用 skill，且會自動遵守倉庫 `CONTRIBUTING.md` 規範，解決「每次告訴 agent 但下次又忘」的痛點。
5. **/journey 記憶時間軸**：以可播放的放射狀時間軸呈現 Hermes 為你累積的所有 memories 與 skills，可直接在介面中編輯或刪除任一條，配合桌面 app 的 memory graph 使用。
6. **背景子 agent 平行委派（Fan-Out）**：支援多個 subagent 同步並行執行，主 agent 彙整結果後再繼續，大幅加速需要並行調查或生成的工作流程。
7. **桌面 Coding Projects 正式登場**：桌面 app 新增側邊欄 codebase 清單、coding rail、review pane、git worktree 管理，以及 agent 可呼叫的 project tools，正式向 IDE 整合場景靠攏。
8. **7 月 2026 CVE 安全修補**：本週有 PR 合併針對 starlette、cryptography、python-multipart 套件進行版本升級，修補 7 月 2026 最新 CVE，建議盡快升級至最新版。
9. **早期 July issues 集中於 auth 與 tool registry**：7 月 7 日起陸續開立多個 issue，主題集中在本地 shell 執行、auth 系統、config 解析，以及 tool registry 在邊緣情況下的失敗行為，目前團隊尚在分流中。

---

## 2. LLM 串接實戰回報

### 2.1 Claude Sonnet 5 成為當前最推薦選擇
- **來源**：[AI Providers | Hermes Agent — Nous Research](https://hermes-agent.nousresearch.com/docs/integrations/providers)、[Best Hermes AI Model 2026 — Remote OpenClaw](https://www.remoteopenclaw.com/blog/best-models-for-hermes-agent)
- Claude Sonnet 5（2026 年 6 月 30 日發布）目前為官方及社群推薦的首選，tool calling 行為最為可靠，在生產環境中穩定性最佳。
- GPT-5.4（$2.50/$15 per 1M token）並列推薦，同樣具備可靠 tool calling 與強力通用推理能力。

### 2.2 GPT-5.6 家族（Luna/Terra/Sol）於 7 月初登場
- **來源**：[Releases · NousResearch/hermes-agent — GitHub](https://github.com/NousResearch/hermes-agent/releases)
- OpenAI GPT-5.6 家族於 7 月初上線，定價：Luna $1/$6、Terra $2.50/$15、Sol $5/$30 per 1M token；目前社群仍在觀察三個層級在 Hermes agent workflow 中的 tool calling 穩定性差異。

### 2.3 DeepSeek V4：成本最低的高品質選項
- **來源**：[How to Run AI Agents Overnight for Almost Nothing: Hermes Agent, DeepSeek V4 and OpenRouter — AIFLOXIUM](https://www.aifloxium.online/blog/hermes-agent-deepseek-v4-openrouter-overnight-ai-setup)、[Best DeepSeek Models for Hermes Agent — Remote OpenClaw](https://www.remoteopenclaw.com/blog/best-deepseek-models-for-hermes)
- DeepSeek V4（2026 年 3 月）：$0.30/$0.50 per 1M token，較 Claude Sonnet 4.6 便宜約 10 倍；SWE-bench Verified 得分 81%，支援 1M context window，並提供 90% cache 折扣（重複 context 僅 $0.03/1M）。
- 實戰回報：適合長時間、高呼叫頻率的背景 agent 任務；部分使用者提到 tool format 偶有不一致，建議搭配 `deepseek_v3` 或 `deepseek_v31` parser 設定以確保格式解析正確。

### 2.4 OpenRouter 作為統一入口的踩坑紀錄
- **來源**：[Debugging Hermes Agent: Moving to OpenRouter & DeepSeek — dcxtribe.com](https://www.dcxtribe.com/articles/moving-from-ilmu-to-openrouter-deepseek-with-hermes-better-diagnostics-but-still-not-fully-working)
- 透過 OpenRouter 可以單一 API key 存取 200+ 模型，解決多 provider key 管理問題；但部分使用者反映透過 OpenRouter 路由的 claude-opus-4.6 context window 從 1M 縮至 128K（因 GitHub Copilot 作為中繼 provider 的限制），需注意 context window 依服務商而異。
- Hermes Agent 要求模型至少支援 64,000 tokens context，低於此門檻的模型會在 first-run 時報錯，需手動確認 provider 實際提供的 context 大小。

### 2.5 Context Window 不匹配為常見首次設定錯誤
- **來源**：[AI Providers | Hermes Agent — Nous Research](https://hermes-agent.nousresearch.com/docs/integrations/providers)
- 初次設定最常見錯誤：選到 context window 小於 Hermes 需求的模型（例如部分 OpenRouter 路由版本的舊模型），導致 agent 啟動即報 context 不足；解法為在 `~/.hermes/config.yaml` 明確指定支援 64K+ context 的模型。

---

## 3. 社群反應與討論亮點

- **X.com 上的熱烈迴響**：Teknium 發文細數 v0.18.0 各項功能後，獲得大量 AI/開發者社群轉推；Luke The Dev 的分析貼文（[@iamlukethedev](https://x.com/iamlukethedev/status/2072416924009418956)）被廣泛引用，稱這次 release 讓 Hermes Agent「從強大的 CLI 工具轉向真正可以建立工作流程的平台」。
- **Mixture-of-Agents 引發架構討論**：r/LocalLLaMA 上出現多篇討論，探討 MoA 設定中各參考模型的 prompt 隔離設計；有使用者發現不同推理模型的輸出差距在 aggregator 彙整後能互補，但費用相應倍增，需要仔細評估 cost vs. quality tradeoff。
- **Nous Research Discord 活躍度上升**：v0.18.0 發布後 agent channel 討論量明顯增加，主題集中於 `/learn` 使用方式、`/journey` 記憶管理，以及 Coding Projects 與現有 git worktree 工作流的整合細節。
- **r/selfhosted 關注 CVE 修補**：本週 CVE 相關 PR 合併後，自架使用者社群對升級時機的討論增加，部分使用者建議在主要工作流程上保守升級，先在測試環境驗證。
- **「Judgment Release 之後的下一步」：社群已在詢問 v0.19.0 規劃**，特別期待 MoA 在 desktop UI 中的更深整合，以及 /journey 時間軸的匯出與備份功能。

---

## 4. 值得追蹤的後續議題

1. **v0.18.0 後的 July issues 修補進度**：auth、local shell execution、tool registry 相關問題已在 7 月 7 日後陸續開立，觀察團隊分流與修復速度。
2. **CVE 安全修補版本（v0.18.x patch）**：starlette/cryptography/python-multipart 的 CVE 修補是否會單獨發布 patch release，或等待 v0.19.0。
3. **GPT-5.6（Luna/Terra/Sol）在 Hermes agent workflow 中的穩定性評測**：目前社群數據不足，等待更多生產環境反饋。
4. **Mixture-of-Agents 的費用管控機制**：官方是否計劃加入 MoA budget cap 或 provider-per-role 指定功能，以解決費用快速倍增問題。
5. **/learn 指令的 skill 版本管理**：當程式庫規範更新後，舊 skill 是否能自動或手動刷新，目前尚未有明確文件。
6. **Hermes Desktop Coding Projects 對大型 monorepo 的效能表現**：桌面 Projects 功能剛上線，超大型 codebase 的 git worktree 管理穩定性仍待社群驗證。

---

*報告產生時間：2026-07-08 UTC｜資料來源：GitHub (NousResearch/hermes-agent)、X.com、Medium、AIFLOXIUM、DEV Community、dcxtribe.com、Nous Research 官方文件*
