# Hermes-Agent 每日情報 — 2026-06-02

> 搜尋範圍：過去 24 小時（2026-06-01 ～ 2026-06-02 UTC）

---

## 1. 今日重點摘要

1. **v0.15.1（2026.5.29）為當前最新穩定版，過去 24 小時無新版本發布**：此版本是 v0.15.0 的同日熱修補（hotfix），目前社群主要在消化 v0.15.0 與 v0.15.1 的改動，尚無 v0.15.2 正式釋出（已有相關 bug issue 開啟，見後述）。

2. **v0.15.0「Velocity Release」（2026.5.28）是近期最大版本**：核心架構大幅重構，`run_agent.py` 從 16,083 行壓縮至 3,821 行（-76%），拆分為 14 個內聚模組，顯著提升效能與可維護性。

3. **Kanban 進化為真正的多 Agent 平台**：歷經 104 個 PR，Kanban 新增 orchestrator 自動任務分解（auto-decomposition）、Swarm 拓撲、排程任務、每個任務獨立 worktree、per-task 模型覆蓋（model override）等能力。每個 worker 是完整的 OS process，擁有獨立身份，取代了原本脆弱的 in-process subagent swarm。

4. **session_search 效能提升 4,500 倍且免費**：此項改進對長期大量使用者影響最大，過往 session_search 費用是使用者常見抱怨，現在完全免費且速度大幅提升。

5. **v0.15.1 修復 loopback 模式下 dashboard 無限重載 bug**：根因是 v0.15.0 的 stale-token reload guard 錯誤地把 loopback 模式下設計性的 401 回應（`/api/auth/me`）判定為 token 已輪換，造成無限 full-page-reload，影響所有 Docker/hosted Hermes/全新安裝的使用者。

6. **Docker 設定調整：綁定主機與 insecure 模式改為明確分離**：過去 Docker entrypoint 會在 dashboard 綁定到非 loopback host 時自動推斷 `--insecure`，現在需要明確設定 `HERMES_DASHBOARD_INSECURE=1`，避免混淆 LAN 存取意圖與停用同源守衛（same-origin guard）的差異。

7. **新增圖片生成 provider：Krea 2 Medium/Large、FAL**：Krea 2 Medium 與 Large 直接整合，FAL 已移植為插件（plugin），擴充了多模態工作流程的選項。

8. **Skill Bundles 功能推出**：使用者可定義「技能包」，一個 slash 指令即可同時啟用多個相關技能，例如「writing day」套件可一次載入寫作相關的所有技能。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）

- **OAuth 憑證路由至 extra_usage 計費池，導致配額不足**：GitHub Issue #12905 記錄了 Anthropic provider OAuth 整合的多個問題，核心問題在於 Anthropic 將第三方 OAuth 客戶端請求路由至獨立的 `extra_usage` 計費池，此池對大多數使用者實際上是空的。文件中暗示 OAuth 重複使用 Claude Code 憑證可共享訂閱額度，但這是誤導性描述。
  - 來源：[Bug #12905: Anthropic provider OAuth integration has multiple issues](https://github.com/NousResearch/hermes-agent/issues/12905)

- **同一模型在不同 provider 下 context 上限不同**：`claude-opus-4.6` 在 Anthropic 直連為 1M tokens，但在 GitHub Copilot 上為 128K tokens，文件建議使用者在 model config 中明確設定 context length，避免因 provider 差異而產生非預期截斷。
  - 來源：[AI Providers | Hermes Agent](https://hermes-agent.nousresearch.com/docs/integrations/providers)

- **Anthropic 計費量 Q1 2026 達 80 倍年化成長**：Dario Amodei 在 Code with Claude 活動表示 Anthropic 原規劃每年 10 倍成長，但 Q1 2026 實際出現了 80 倍年化成長。Rate limit 上調仍在持續，週限額（weekly limits）是下一個優先處理項目。
  - 來源：[Claude API Token Limits Just Jumped 10x — Every Tier's New Numbers Explained](https://www.mindstudio.ai/blog/claude-api-token-limits-increase-tier-breakdown)

- **透過 Fallback Provider 降低 Claude rate limit 成本**：有 YouTube 教學示範如何設定 Hermes fallback providers，將每月 Claude 費用控制在 8 美元以內，適合 rate limit 敏感的使用者。
  - 來源：[Hermes Agent Setup for $8/Month Cost Reduction: No Claude Rate Limits](https://www.youtube.com/watch?v=kovUM5wssAI)

### OpenAI（GPT 系列）

- **GPT-5.4 / GPT-4o 在官方文件中並列支援**：Hermes 的 provider 文件列出 GPT-5.4 與 GPT-4o 為支援的 OpenAI 模型，無特別已知的相容性問題回報。
  - 來源：[AI Providers | Hermes Agent](https://hermes-agent.nousresearch.com/docs/integrations/providers)

### Fallback 機制

- **Fallback Provider 為生產部署的關鍵防線**：當主要 LLM provider 遇到 rate limit、伺服器過載、auth 失效或連線中斷時，Hermes 可在不中斷對話狀態的情況下自動切換到備用 provider:model 組合，mid-session 切換已有實際使用者驗證。
  - 來源：[Fallback Providers | Hermes Agent](https://hermes-agent.nousresearch.com/docs/user-guide/features/fallback-providers)

---

## 3. 社群反應與討論亮點

- **@Teknium（X.com，NousResearch 核心成員）** 宣布 v0.15.1 熱修補上線，特別提及此版本對「只接受 tagged release 的平台（如 Hostinger）」特別重要，說明官方對自架部署平台的相容性有意識。
  - 來源：[Teknium on X](https://x.com/Teknium/status/2060170722459427076)

- **GitHub Issue #34701**：使用者回報升級至 v0.15.2（或 nightly 版本）後 dashboard 出現 `missing hermes_cli.dashboard_auth module` 錯誤，目前 issue 仍開啟中，尚無官方回應。此問題可能影響嘗試追蹤最新 nightly 的使用者。
  - 來源：[Issue #34701: Dashboard fails after updating to 0.15.2](https://github.com/NousResearch/hermes-agent/issues/34701)

- **GitHub Issue #35322**：當 dashboard 綁定到 `0.0.0.0` 並搭配 `--insecure` 時，WebSocket 連線被拒絕，影響需要從外部網路存取 dashboard 的使用者。
  - 來源：[Issue #35322: WebSocket connections rejected when dashboard bound to 0.0.0.0 with --insecure](https://github.com/NousResearch/hermes-agent/issues/35322)

- **NVIDIA 官方部落格** 發文介紹 Hermes 搭配 RTX PC 與 DGX Spark 的自我改進 AI Agent 架構，強調本地運算與私有資料的結合，引發硬體愛好者與企業私有部署族群的關注。
  - 來源：[Hermes Unlocks Self-Improving AI Agents, Powered by NVIDIA RTX PCs and DGX Spark](https://blogs.nvidia.com/blog/rtx-ai-garage-hermes-agent-dgx-spark/)

- **Blake Crosley 技術指南** 整理了 v0.15 Velocity Release 的詳細功能參考，包含 Kanban、Skill Bundles 的完整用法說明，成為社群自學的主要第三方資源之一。
  - 來源：[Hermes Agent v0.15 Reference: Velocity Release + Kanban + Skill Bundles](https://blakecrosley.com/guides/hermes)

---

## 4. 值得追蹤的後續議題

1. **Issue #34701 修復進度**：`hermes_cli.dashboard_auth` 模組遺失問題若影響範圍擴大，可能促使 v0.15.2 提前發布，值得密切關注官方 release 頁面。

2. **Issue #35322 WebSocket 綁定問題**：`0.0.0.0 + --insecure` 組合的 WebSocket 拒絕連線，對自架部署（home lab、VPS）使用者影響較大，官方是否在 v0.15.2 中一併修復值得追蹤。

3. **Anthropic OAuth 計費池問題（Issue #12905）**：OAuth 路由至 `extra_usage` 的根本問題需要 Anthropic 端配合修改，若無解法，文件的「OAuth 重複使用訂閱額度」說法需要更正，以免更多使用者踩坑。

4. **Kanban Swarm 拓撲的穩定性測試**：多 agent 並行工作流程在大規模任務下的 zombie detection 與 heartbeat 機制可靠性，社群實測回報仍不多，值得持續關注。

5. **session_search 4,500 倍提速的實際使用者驗證**：官方宣稱的效能數字是否在各種硬體環境下均能實現，社群獨立 benchmark 報告值得等待。

---

*資料來源整理自：GitHub Releases（NousResearch/hermes-agent）、GitHub Issues、X.com、NVIDIA Blog、YouTube、Blake Crosley Guide、AI Providers 文件、Fallback Providers 文件等，搜尋時間 2026-06-02 UTC。*
