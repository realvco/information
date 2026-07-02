# Hermes-Agent 每日情報｜2026-07-02

> 資料蒐集區間：UTC 2026-07-01 00:00 ～ 2026-07-02 00:00

---

## 1. 今日重點摘要

1. **過去 24 小時無新版本釋出**：最新版本為 v0.17.0（2026-06-19），本日無新 release。以下摘要以近期最重要動態為主軸，並標注值得追蹤的後續議題。
2. **v0.17.0「The Reach Release」仍為當前熱門討論焦點**：發布於 6/19，1,475 commits、~800 merged PRs、245 位貢獻者，社群持續消化與測試新功能。
3. **iMessage 支援 Photon 整合（無需 Mac relay）**：透過 `hermes photon login` 與裝置碼驗證即可接收/傳送 iMessage，取代原 BlueBubbles 方案，免費且無需自架中繼節點。
4. **Raft Agent Network 整合**：新的 Raft Platform Adapter 讓 Hermes 以外部 Agent 身分接入 Raft，wake payload 僅含 metadata（事件 ID、時間戳），不傳送訊息本文，符合 privacy-by-contract 設計。
5. **背景 Subagent 非同步執行**：委派給 subagent 的任務不再阻塞主對話，可在背景持續運行，主流程照常進行。
6. **圖片生成新增編輯功能**：影像生成工具現支援對已生成圖片進行局部編輯，並可透過 Cursor Composer（xAI Grok 訂閱）接入模型。
7. **Claude Code OAuth + Max Plan 費用異常問題（Issue #40014）**：使用 Claude Max 訂閱的 OAuth 憑證時，Hermes 仍透過 pay-per-token 端點計費，消耗「extra usage」點數而非訂閱配額，維護者確認為已知問題，修復中。
8. **NVIDIA RTX / DGX Spark 官方合作**：Nous Research 與 NVIDIA 深度合作，確保 Hermes Agent 在 RTX AI 與 DGX Spark 超晶片上順暢運行，並整合 OpenShell runtime 以接入 Microsoft 安全機制。
9. **model.max_tokens 設定無效問題（Issue #4404）**：config.yaml 中的 `model.max_tokens` 不會傳至 AIAgent，目前需透過 provider 層設定。

---

## 2. LLM 串接實戰回報

### 2-1. 主要接入方式比較

| 方式 | 優點 | 限制與注意事項 |
|---|---|---|
| **Nous Portal** | 涵蓋 300+ 模型（Claude、GPT、Gemini、DeepSeek、Qwen、Kimi、GLM、MiniMax、Grok） + Tool Gateway（網路搜尋、圖片生成、TTS、瀏覽器自動化）| 需要訂閱；模型可用性隨供應商政策變動 |
| **LiteLLM Proxy** | 100+ 供應商統一 API，支援 load balancing、fallback chains、預算控制 | 需自行維護 proxy 實例 |
| **GitHub Copilot API** | 可用 Copilot 訂閱存取 GPT-5.x、Claude、Gemini | 需 Copilot 訂閱；部分模型功能受限 |
| **直接 API Key** | 最彈性，可精細控制每個 provider 設定 | 需管理多組金鑰；配合 Credential Pools 使用效果最佳 |

**來源：**
- [AI Providers - Hermes Agent Docs](https://hermes-agent.nousresearch.com/docs/integrations/providers)
- [Credential Pools - Hermes Agent Docs](https://hermes-agent.nousresearch.com/docs/user-guide/features/credential-pools)

### 2-2. 已知踩坑：Claude OAuth + Max Plan 費用異常

- **Issue #40014**：以 Claude Code OAuth 憑證（Max Plan）執行 Hermes 時，API 呼叫仍走 pay-per-token 端點，導致帳單中顯示「extra usage」扣款，而非消耗訂閱內含配額。
  - 目前建議：優先讓 Hermes 使用 Claude Code 本身的憑證儲存（`hermes login --provider anthropic`），不要手動複製 token 至 `~/.hermes/.env`，以維持 OAuth token 的可刷新性。

**來源：**
- [Issue #40014：Claude Code OAuth (Max/Pro plan) still hits pay-per-token API endpoint](https://github.com/NousResearch/hermes-agent/issues/40014)

### 2-3. Rate Limit 429 錯誤處理

- Token-per-minute（TPM）限制是 Hermes 最常見的瓶頸，因為每次請求帶入大量 context。
- Hermes 內建對 429 錯誤的 exponential backoff 重試。
- 使用者可透過 `rate_limit_rpm` 設定主動限流，在 provider 側 429 之前於本地排隊。
- 若受 TPM 限制，減少輸入 Token（技能精簡、頻繁 `/compress`）比降低 RPM 更有效。
- **Credential Pools**：當某組 API Key 觸及速率限制或帳單配額，Hermes 自動切換至下一個健康 Key，保持 session 不中斷。

社群 Medium 文章分享：透過 **Provider Stacking + Failover Chains**（組合 5 個免費服務）實現 24/7 免費運行 Hermes。

**來源：**
- [Fix Hermes Agent 429 rate limit errors](https://lumadock.com/tutorials/hermes-agent-429-rate-limit-fix)
- [Stop Burning Through API Credits: Provider Stacking + Failover Chains](https://medium.com/@kram254/stop-burning-through-api-credits-how-provider-stacking-failover-chains-let-me-run-hermes-agent-08eee8b7c0f6)

### 2-4. model.max_tokens 設定無效（Issue #4404）

- config.yaml 中 `model.max_tokens` 的設定值不會被傳至 AIAgent，實際不生效。
- 短期解法：在 provider 層（如 LiteLLM、Nous Portal 後台）設定 max_tokens 上限。

**來源：**
- [Issue #4404：model.max_tokens in config.yaml has no effect](https://github.com/NousResearch/hermes-agent/issues/4404)

---

## 3. 社群反應與討論亮點

- **Discord Bot 問題連環爆（Issues #12750、#12496、#47360）**：v0.17.0 前後陸續出現 Discord 閘道連線成功但收不到 `MESSAGE_CREATE` 事件、Thread 中不回應、Forum 頻道首訊需 @mention 才觸發等問題，社群回報量較大，維護者已逐一確認並列入修復計畫。
- **MarkTechPost 技術報導（6/20）**：報導 Hermes 新增 blank slate 模式，透過 `platform_toolsets.cli` 與 `disabled_toolsets` 精確鎖定工具集，受技術社群好評。
- **NVIDIA Blog（6/19）**：Nous Research 與 NVIDIA 合作公告，Hermes 在 Jensen Huang GTC 主題演講中演示 DGX Spark 整合，大幅提升品牌曝光度。
- **「The Agent Report」分析文（6 月）**：統計 Hermes 2026 年 22 次版本、188K Stars，定位為開源 Agent 經濟的核心 Runtime 之一。
- **X.com 討論（@iamlukethedev）**：Reach Release 後 X 上熱議 iMessage + WhatsApp + Raft 三線整合，許多使用者認為這讓 Hermes 從「工具」進化為「真實使用的通訊入口」。

**來源：**
- [Issue #12750：Discord no longer responds in threads](https://github.com/NousResearch/hermes-agent/issues/12750)
- [Issue #47360：Discord gateway never receives MESSAGE_CREATE events](https://github.com/NousResearch/hermes-agent/issues/47360)
- [MarkTechPost：Blank Slate Mode](https://www.marktechpost.com/2026/06/20/nous-research-updates-hermes-agent-with-a-blank-slate-mode-that-pins-toolsets-via-platform_toolsets-cli-and-disabled_toolsets/)
- [NVIDIA Blog：Hermes Agent + DGX Spark](https://blogs.nvidia.com/blog/rtx-ai-garage-hermes-agent-dgx-spark/)
- [X.com @iamlukethedev：Reach Release thread](https://x.com/iamlukethedev/status/2068059502180479268)

---

## 4. 值得追蹤的後續議題

| 議題 | 追蹤理由 |
|---|---|
| **Issue #40014 Claude OAuth Max Plan 費用異常** | 已確認為 bug，修復 PR 尚未合入，影響所有使用 Max/Pro 訂閱的使用者 |
| **Issue #5449 Rate Limit Header 主動節流** | 功能請求，若合入可讓 Hermes 在觸及 429 前即主動降速 |
| **Discord Bot 連線問題（#12750、#47360）** | 多個 Discord 相關 issue 集中爆發，等待 v0.17.x patch |
| **iMessage Photon 整合穩定性** | 屬全新整合，實戰使用案例尚少，預期後續 bug report 增加 |
| **Raft Agent Network 工作流** | 新功能，社群文件與教學仍稀少，值得持續追蹤 |
| **v0.18.0 下一個 release 方向** | 依目前節奏（約每 2-3 週一版），預期 7 月上旬或中旬釋出 |

---

*報告產生時間：2026-07-02 UTC｜資料來源：GitHub NousResearch/hermes-agent、hermes-agent.nousresearch.com、MarkTechPost、NVIDIA Blog、X.com 等*
