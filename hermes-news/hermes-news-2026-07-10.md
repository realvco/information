# Hermes-Agent 每日情報 — 2026-07-10

> 搜尋範圍：過去 24 小時（UTC）  
> 專案：[NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent)

---

## 1. 今日重點摘要

1. **v0.18.0「Judgment Release」持續發酵（7 月 1 日正式釋出）**：本週社群討論主軸仍圍繞此版本，X.com 上 Teknium（Nous Research 共同創辦人）的宣告推文引發大量轉發，評論集中於 Mixture-of-Agents 實際效益與 /learn 指令的應用場景。

2. **Mixture-of-Agents 成為一等公民**：v0.18 最受矚目的功能——可在 CLI、TUI、桌面版、Gateway 所有介面的模型選單中，像選 Claude 或 GPT 一樣直接選擇命名 MoA 集成。每個參考模型的完整推理過程以獨立標籤區塊呈現，最終聚合答案即時串流，無需等待批次結果。

3. **自我驗證引擎（Self-Verification）上線**：Hermes 的「完成」現在意味著真正執行了專案的測試腳本，而非模型「自我感覺良好」。`/goal` 指令支援完成契約（completion contract），驗證通過才回報任務結束，並記錄執行證據。

4. **新增 /learn 與 /journey 指令**：`/learn` 可指向目錄、URL 或工作流程，自動萃取可重用技能並遵循 CONTRIBUTING.md 規範；`/journey` 展示 Hermes 對使用者的累積學習時間線，桌面版另提供記憶圖譜可視化。

5. **背景 subagent 平行執行**：`delegate_task` 現在可同時派送多個 subagent 在後台執行，主聊天介面不再阻塞，所有 subagent 完成後結果合併至單一回覆。

6. **PR #30845 修補 Kimi K2 + DeepSeek 推理控制**：主幹 PR 修正 Kimi K2 僅傳送 `extra_body.thinking` 而遺漏 `reasoning_effort` 的問題；同步修正 DeepSeek 模型（deepseek-v4-flash/pro）透過 opencode-go provider 時，`reasoning_effort` 未作為 top-level 參數傳送的 bug（DeepSeek 要求最高推理等級為 `"max"`，非 OpenAI 的 `"xhigh"`）。
   - 來源：[PR #30845](https://github.com/NousResearch/hermes-agent/pull/30845)

7. **v0.18 規模統計**：自 v0.17.0 以來，累積約 1,720 commits、998 merged PRs、2,215 個檔案變動（251,000 行插入、41,000 行刪除），共 370+ 社群貢獻者參與，所有 P0 與 P1 issue 已全數清零。

8. **Issue #26889：速率限制重置時間未對使用者呈現**：provider 回應中常包含速率限制重置時間，已解析進 `error_context` 但從未顯示給使用者，社群請求在狀態訊息中直接呈現，讓使用者知道要等多久而非反覆重試。

9. **Telegram Token 膨脹問題修補中**：同一對話透過 Telegram 閘道的 token 消耗約為 CLI 的 2–3 倍（CLI ~6–8k input tokens vs Telegram ~15–20k），根本原因是 gateway 在 `hermes-agent` 目錄啟動時，將 `AGENTS.md` 等開發檔案載入 context。社群反映 v0.18 後此問題有所改善，但完整修補尚在確認中。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）

- **LiteLLM 代理整合**：最常見的 Hermes + Claude 設定方式為透過 LiteLLM proxy，指令：`litellm --model anthropic/claude-sonnet-4 --port 4000`，再將 Hermes custom endpoint 指向該 port。免去直接管理 Anthropic SDK 版本相容性問題。
  - 來源：[LLM Gateway Hermes Agent 整合文件](https://docs.llmgateway.io/guides/hermes-agent)

- **`hermes proxy` 指令**：可將 Claude Pro（OAuth 授權）轉換為 OpenAI 相容的本地端點，讓 Codex、Aider、Cline、Continue 等工具直接對接，無需額外改寫 auth 邏輯。

- **claude-fable-5 模型支援**：v0.17.0 起 catalog 新增 `anthropic/claude-fable-5`，可在模型選單直接選用。

### DeepSeek

- **reasoning_effort 參數傳遞問題（已有 PR 修復）**：使用 deepseek-v4-flash 或 deepseek-v4-pro 透過 opencode-go provider 時，`reasoning_effort` 未正確傳至 DeepSeek API。DeepSeek 需在 request body 的頂層傳入此參數，且最高等級為 `"max"`（非 OpenAI 的 `"xhigh"`）。PR #30845 已提交修復，建議等待合入後更新。
  - 來源：[Issue #21577](https://github.com/NousResearch/hermes-agent/issues/21577)、[PR #30845](https://github.com/NousResearch/hermes-agent/pull/30845)

### Kimi K2（Moonshot AI）

- **`extra_body.thinking` 遺漏 `reasoning_effort`**：Kimi K2 分支只送出 `extra_body.thinking`，沒有同步帶上 `reasoning_effort`，而 KimiProfile（`api.moonshot.ai/v1`）需要兩個欄位同時存在，opencode-go 作為中介 proxy 時也必須鏡像此行為。同樣由 PR #30845 處理。

- **Hermes adapter 失效報告（第三方）**：使用 `adapter_type: hermes_local` 搭配 deepseek-v4-pro 或 kimi-k2.6 的 agent 設定無法執行任務，切換至其他 adapter 則正常，疑似相容性問題。
  - 來源：[paperclipai/paperclip Issue #5332](https://github.com/paperclipai/paperclip/issues/5332)

### GPT（OpenAI）與 Grok（xAI）

- Nous Portal 統一訂閱閘道涵蓋 Claude、GPT、Gemini、DeepSeek、Qwen 等 300+ 模型，單一帳號計費。
- Grok Composer 2.5 Fast（xAI OAuth）已在 v0.17 加入，可透過 xAI 訂閱取用 Cursor Composer 功能。

### 速率限制（Rate Limit）通用踩坑

- Hermes 每次 request 預設將大量 context 一次推送，token-per-minute (TPM) 限制最容易成為瓶頸，遠比 requests-per-minute 更快觸發 429。
- 預設工具呼叫迴圈同步觸發所有 tool calls，集中爆發請求，建議手動加入 concurrency 控制或使用背景 subagent 分散壓力。
- 來源：[Hermes Agent 429 rate limit fix - LumaDock](https://lumadock.com/tutorials/hermes-agent-429-rate-limit-fix)

---

## 3. 社群反應與討論亮點

- **X.com**：Teknium 宣告 v0.18 的推文（[@Teknium](https://x.com/Teknium/status/2072416088785359189)）與 Nous Research 官方帳號（[@NousResearch](https://x.com/NousResearch/status/2072413332665962617)）的公告均獲得高度互動，討論集中於 Mixture-of-Agents 在實際編碼任務中的可靠性，以及 /learn 指令能否替代手動 prompt engineering。

- **Medium**：KD Agentic 的文章《[Hermes v0.18: AI Agents Now Verify, Not Just Claim](https://medium.com/kd-agentic/hermes-v0-18-ai-agents-now-verify-not-just-claim-cb542dd0e33b)》引發轉發，核心觀點是自我驗證機制改變 agent 的「完成語義」，被視為方法論上的重要進展。

- **Nous Research Discord**：社群反映本次 v0.18 release notes 異常清晰（完整 changelog），對比過去版本大幅改善，但也有人指出 /journey 指令在大型記憶庫上渲染緩慢，尤其 Desktop 記憶圖譜在 1000+ 記憶條目時有 UI 卡頓。

- **Digg 轉載**：[《Nous Research co-founder Teknium releases Hermes Agent v0.18》](https://digg.com/tech/9s4chp64) 使 Hermes Agent 曝光至一般科技社群，非 AI 開發者圈也開始注意此專案。

---

## 4. 值得追蹤的後續議題

| 議題 | 說明 |
|------|------|
| **PR #30845 合入狀態** | Kimi K2 + DeepSeek reasoning_effort 修補，建議追蹤何時並入 stable |
| **Telegram token 膨脹完整修補** | v0.18 後改善但未完全解決，影響使用 Telegram 閘道的費用控制 |
| **Issue #26889：速率限制重置時間呈現** | 低複雜度改善需求，對生產環境很實用，值得關注何時落地 |
| **v0.19 規劃方向** | v0.18 清零所有 P0/P1 後，社群期待下一版聚焦方向，官方尚未公告 |
| **/journey 記憶圖譜效能優化** | 大量記憶條目下 Desktop UI 卡頓，若使用 Hermes 時間較長需留意 |
| **Nous Portal 統一訂閱正式化** | 300+ 模型單一計費閘道，詳細定價與穩定性待觀察 |
