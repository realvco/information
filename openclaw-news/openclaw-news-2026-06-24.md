# OpenClaw 每日情報 — 2026-06-24

> 搜尋時間範圍：過去 24 小時（UTC 基準）
> 資料來源：GitHub、社群論壇、技術部落格

---

## 1. 今日重點摘要

1. **最新穩定版 v2026.6.9（2026-06-21 發布）**：聚焦 Agent 與 Codex 執行環境穩定性與安全性強化，子代理工作空間隔離、session lock 正確釋放均有改善；同時新增 GitHub Copilot agent runtime 及 Codex Supervisor plugin package。

2. **Beta 版 v2026.6.10-beta.1 / beta.2 持續推進**：beta.1 修復 pending 子代理完成公告遺失、chat history transcript 為空、media index 對齊等問題；beta.2 加入短對話快速模式（fast mode）自動切換，並修正 Zai 模型合成與 GLM 過載 failover 的路由一致性。

3. **Telegram 頻道交付改善**：v2026.6.9 起 Telegram 支援富文本 HTML，保留 Markdown 格式與 sticker 路徑，progress draft 與命令輸出渲染更準確。

4. **Workboard 多代理協作工具上線**：v2026.6.9 在 Workboard 加入 agent coordination tools，配合 GitHub Copilot agent runtime 可在同一板面協調多個子代理任務。

5. **安全性修補**：SSH tunnel 限縮至 loopback only，不安全的 chat/tool/package/response 長度一律拒絕，device-backed pairing 移除，舊 abort marker 不再影響新 chat 事件。

6. **模型路由 policy comparison 功能新增**：可在同一介面比較不同 provider policy，簡化多供應商策略管理。

7. **Claude Sonnet 4.6 持續領跑 PinchBench**：在 OpenClaw 自家 benchmark 中 Claude Sonnet 4.6 達 86.9% 成功率，MCP-based 指標（MCPAgentBench）亦以 71.6 居首。

8. **過去 24 小時新發布/重大公告**：截至本報告截止時間，GitHub releases 頁面無 2026-06-24 的新版本標籤；最新正式版本仍為 v2026.6.9（2026-06-21）。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）
- **推薦配置**：Sonnet 4.6 做大多數 agent 工作（推理、tool use、通用任務），Haiku 4.5 處理高量低成本任務（分類、routing、簡單轉換）。
- **benchmark 表現**：PinchBench 成功率 86.9%、MCPAgentBench 71.6，兩項均居 OpenClaw 相容模型首位。
- **來源**：[Best LLM for OpenClaw: Gemini 3.1 Pro vs GPT-5.5 vs Claude Opus 4.7 (2026) - DEV Community](https://dev.to/matthewrevell/best-llm-for-openclaw-gemini-31-pro-vs-gpt-55-vs-claude-opus-47-2026-3na4)

### GPT-5.5（OpenAI）
- **適用場景**：GPT-5.5 在 Terminal-Bench 2.0 等 agentic benchmark 領先，但 standard API 限 128K context；超過 128K 的大上下文任務需改用其他模型。
- **踩坑紀錄**：當 context 超過 128K 上限時請求直接失敗，無自動降級，需在 config 設定 fallback model。
- **來源**：[Best Model for OpenClaw: Claude vs GPT vs Gemini (2026)](https://cowork.ink/blog/best-model-for-openclaw/)

### Gemini 3.1 Pro（Google）
- **推薦程度**：社群普遍推薦作為多數 OpenClaw 部署的預設選擇，適合大 context 程式碼審查（1M token），且有免費開發者額度。
- **原生多模態**：支援圖片輸入，無需額外工具橋接。
- **來源**：[Google (Gemini) · OpenClaw Docs](https://docs.openclaw.ai/providers/google)

### Rate Limit / Auth 踩坑
- **HTTP 429 佔比最高**：429 錯誤佔 OpenClaw 常見錯誤約 60%，尤其 OAuth token（ChatGPT Plus 訂閱）rate limit 比直接 API key 更嚴格。
- **Auth 失效處理**：401/403 通常需重新執行 `openclaw models auth setup-token` 重新產生憑證。
- **缺乏原生 token budget 控制**：目前 OpenClaw 無內建機制限制 token 用量，GitHub issue #58826 已提出 feature request，但尚未合併。
- **成本控制建議**：啟用 prompt caching、將 heartbeat interval 對齊 cache TTL、context 超過 50% 時設自動 reset，可有效抑制長 session 的 token 累積。
- **來源**：[OpenClaw API Rate Limit Reached — Fix & Prevention Guide](https://www.getopenclaw.ai/help/rate-limits-quota-management)、[Feature: Add built-in token budget and request limit controls · Issue #58826](https://github.com/openclaw/openclaw/issues/58826)

### April 2026 供應商切換相容性（歷史紀錄）
- v2026.4.26 更新後引發 gateway crash 與 Discord/Telegram channel 失效，大量用戶回報高 CPU、高延遲、外掛損毀。部分用戶因此轉向 Hermes Agent。
- 來源：[OpenClaw 2026.4.26 update triggering gateway crashes (Piunikaweb)](https://piunikaweb.com/2026/04/29/openclaw-update-triggering-gateway-crashes-issues/)

---

## 3. 社群反應與討論亮點

- **API 額度可視化需求強烈**：GitHub issue #24865 要求在單一介面顯示所有 auth profile 的 rate limit / usage 狀態，目前各 provider 使用量須分開查詢，管理不便。
- **Provider manifest 廣受好評**：2026 年 4 月推出的 provider manifest 功能讓使用者可在不重建的情況下切換模型，社群評價正面，視為里程碑式功能。
- **穩定性信任問題持續**：4 月的大規模 crash 事件後，部分資深用戶公開表達對 release 測試不足的不滿，要求設立穩定發布頻道（stable channel）。
- **競品比較熱度升溫**：r/openclaw 出現多篇「OpenClaw vs Hermes Agent」對比討論，Hermes 的更新頻率與穩定性被拿來對比 OpenClaw。

---

## 4. 值得追蹤的後續議題

1. **v2026.6.10 正式版何時釋出**：beta.1/beta.2 改動量大，正式版釋出時機值得關注，尤其短對話 fast mode 切換機制。
2. **Issue #58826 進度**：原生 token budget 控制功能是否納入下一個正式版，對生產環境成本管控影響重大。
3. **Issue #24865 進度**：統一 rate limit/usage 檢視介面，方便多 provider 管理。
4. **GitHub Copilot agent runtime 文件完善**：v2026.6.9 新增但文件尚不完整，後續使用者踩坑回報可能增加。
5. **穩定頻道（stable channel）提案**：社群呼聲持續，官方是否回應值得觀察。

---

*報告生成時間：2026-06-24 UTC*
*資料來源整理自 GitHub releases、OpenClaw 官方文件、DEV Community、Reddit、技術部落格*
