# Hermes-Agent 每日情報 — 2026-07-01

> 搜尋時間範圍：2026-06-30 至 2026-07-01（過去 24 小時）
> 資料來源：GitHub Releases、官方文件、Releasebot、社群討論、科技媒體

---

## 1. 今日重點摘要

1. **過去 24 小時無新版本發布**：截至 2026-07-01，最新穩定版為 **v0.17.0（2026.6.19）**，上一個主要版本為 v0.16.0（2026.6.5，The Surface Release）。今日未見新 release。
2. **v0.17.0 效能優化成為近期熱議焦點**：本版本包含三輪深度優化：延遲 `openai._base_client` 匯入節省 −240ms/−17MB、熱路徑優化削減 47% 函式呼叫（399k→213k，31 輪對話），及延遲壓縮可行性檢查（−170 至 −290ms），對頻繁使用 CLI 的使用者影響顯著。
3. **iMessage 頻道上線（Photon）**：v0.17.0 新增 iMessage 平台 plugin，透過 Photon 管理的號碼池接收與回覆訊息，成為繼 Discord、Telegram、Slack 後第四個主要訊息頻道。
4. **Skills Hub 全面改版**：儀表板的 Skills Hub 重構，新增連接 Hub、精選區（Featured）、安裝前完整預覽，以及每個 skill 的安全掃描機制，降低惡意 skill 的安裝風險。
5. **多 Profile 管理統一視圖**：v0.17.0 引入機器範圍的全域 Profile 切換器（global profile switcher），可在單一介面管理多個 Hermes profile，不再需要手動切換設定檔。
6. **Session Search 重建**：舊版 `session_search` 每次呼叫需花費約 $0.30 及 30 秒（需呼叫輔助 LLM），已完全重建為高效本地化搜尋，大幅降低頻繁查詢的 API 費用。
7. **Memory Tool 大幅升級**：v0.17.0 的 memory tool 完成重要更新，curator 停止在每次例行運行時消耗輔助模型預算，節省後台持續成本。
8. **Kanban 多 Agent 看板持續成熟（v0.15.0 延續）**：The Tenacity Release 引入的 Kanban 多 Agent 協作看板（含心跳檢測、序號回收、zombie 偵測、重試預算及幻覺恢復）仍是討論熱點，使用者正積極測試於複雜工作流中的穩定性。
9. **安全更新追蹤**：The Surface Release（v0.16.0）修補了 CVE-2026-48710（Starlette 版本釘定）、SSRF off-loop 強化及 subprocess 認證憑證剝離，建議尚未升級至 v0.16.0 以上的使用者儘速更新。

---

## 2. LLM 串接實戰回報

### 2-1. 多 Provider 並存與 Fallback 機制

來源：[AI Providers | Hermes Agent Documentation](https://hermes-agent.nousresearch.com/docs/integrations/providers) | [Hermes Agent: The Practitioner's Reference (2026)](https://blakecrosley.com/guides/hermes)

- Hermes-Agent 支援有序 fallback provider chain，主 provider 失效時自動切換至下一個，對穩定性要求高的部署場景特別實用。
- **支援的 provider 包含**：Claude（Anthropic API 直連、OAuth、OpenRouter 或相容 Proxy）、GPT-5.5（Codex OAuth）、Gemini（Google AI API、OpenRouter 或相容 Proxy）、AWS Bedrock（原生支援，自 The Interface Release 起）。
- **Nous Portal**：Nous Research 推出統一訂閱閘道，涵蓋 300+ 前沿 Agent 模型（含 Claude、GPT、Gemini），並附帶 Tool Gateway（網路搜尋、圖片生成、TTS、瀏覽器自動化）。

### 2-2. 工具呼叫穩定性實測

來源：[Best Hermes Agent Model 2026: Claude vs DeepSeek](https://www.remoteopenclaw.com/blog/best-models-for-hermes-agent) | [FAQ & Troubleshooting | Hermes Agent](https://hermes-agent.nousresearch.com/docs/reference/faq/)

- **Claude Sonnet 4.6 與 GPT-4.1** 在生產環境中被評為工具呼叫行為最穩定的組合。
- Gemini 2.5 Pro 費率為 $1.25/$10（每百萬 token，輸入/輸出），支援高達 100 萬 token 輸入，適合長文件分析場景。
- DeepSeek 在中文任務或低成本場景中有使用者回報，但在複雜工具呼叫鏈路中偶有不穩定情形。

### 2-3. OpenRouter 整合

來源：[How to integrate Gemini with Hermes | Composio](https://composio.dev/toolkits/gemini/framework/hermes-agent)

- OpenRouter 提供 200+ 模型的統一 API Key 存取（含 Claude、GPT-4、Gemini、Llama），Hermes 的 Provider 設定支援 OpenRouter 作為統一閘道。
- 此方式對需要頻繁切換模型或做 A/B 測試的使用者更方便，但需注意 OpenRouter 自身的 rate limit 政策（依 tier 不同）。

### 2-4. Session Search 費用改善（舊版踩坑）

來源：[Release Hermes Agent v0.17.0 (v2026.6.19) · NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent/releases/tag/v2026.6.19)

- 舊版 `session_search` 每次呼叫耗費約 $0.30 及 30 秒（呼叫輔助 LLM 摘要 3 個 sessions），高頻使用時費用可觀。
- v0.17.0 已完全重建此工具，升級後費用與延遲大幅改善，建議舊版使用者優先升級。

### 2-5. GitHub Copilot API 存取路徑

來源：[AI Providers | Hermes Agent Documentation](https://hermes-agent.nousresearch.com/docs/integrations/providers)

- 持有 GitHub Copilot 訂閱的使用者可透過 Copilot API 存取 GPT-5.x、Claude、Gemini 等模型，成本計入 Copilot 訂閱內，對學生或企業方案用戶是低門檻選項。

---

## 3. 社群反應與討論亮點

- **Discord 多 Bot 共存問題**：GitHub Issue #25312 提出的 `DISCORD_THREAD_REQUIRE_MENTION` opt-in 機制正在討論中。現有問題為：Hermes 在 Discord 線程中預設對所有訊息回應，多 Bot 並存時容易產生交叉回覆、無謂消耗 Token 及頻道洗版。社群期待此 flag 進入正式版本。
  - 來源：[Issue #25312 · NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent/issues/25312)
- **多 Agent Discord 協作**：Issue #14853 討論多 Agent 於共享頻道協作時的歷史注入與 cascade 防止機制，為企業多 Bot 部署場景的核心需求。
  - 來源：[Issue #14853 · NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent/issues/14853)
- **原生桌面 App 評價正面**：The Surface Release 引入的 Electron 桌面應用（macOS/Linux/Windows）受社群歡迎，模型選擇器支援模糊搜尋、Cmd+K 命令面板及拖放檔案等功能被列為亮點。
- **Kanban 多 Agent 看板穩定性測試**：部分進階使用者正在測試 v0.15.0 的 Kanban 功能於長時間運行工作流中的 zombie 偵測與 retry budget 行為，相關回報持續流入。
- **TrueNAS 社群支援**：Hermes-Agent 已出現在 TrueNAS Apps Market 的社群應用目錄中，NAS 使用者的自架需求增加。
  - 來源：[Hermes-Agent | TrueNAS Apps Market](https://apps.truenas.com/catalog/hermes-agent_community/)

---

## 4. 值得追蹤的後續議題

| 議題 | 狀態 | 建議追蹤方向 |
|------|------|-------------|
| Discord `DISCORD_THREAD_REQUIRE_MENTION` | PR 討論中 | 追蹤 Issue #25312 合併進度 |
| 多 Agent 頻道協作 cascade 防止 | 提案階段 | 追蹤 Issue #14853 設計決策 |
| v0.18.0 下一版本內容 | 未公告 | 觀察 GitHub milestone 及 CHANGELOG |
| Kanban 看板長時間穩定性 | 使用者測試中 | 追蹤 zombie 偵測與 retry budget 回報 |
| Nous Portal 定價與模型覆蓋 | 持續更新 | 關注 300+ 模型清單是否包含最新 Claude 5 系列 |
| Raft Agent Network 整合 | 剛上線（v0.17.0）| 追蹤 Raft 網路的使用案例與社群回饋 |
| iMessage Plugin 穩定性 | 剛上線（v0.17.0）| 追蹤 Photon 號碼池可靠性與費用結構 |

---

*本報告由自動化每日情報系統生成，資訊截至 2026-07-01 UTC。如有時效性疑慮，請參照各來源連結確認最新狀態。*
