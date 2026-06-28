# Hermes-Agent 每日情報｜2026-06-28

> 搜尋區間：2026-06-27 至 2026-06-28（UTC）

---

## 1. 今日重點摘要

1. **v0.17.0「The Reach Release」（2026-06-19 發布）** — 本週最大里程碑，1,475 commits、~800 merged PRs、245 位社群貢獻者，關閉 300+ issues；重心在「觸及更多渠道」。
2. **iMessage 無需 Mac Relay** — 新增 Photon Spectrum 通道（gRPC-native），裝置 code 登入即可，舊 BlueBubbles 路徑並行保留，Mac 中繼機不再是必要條件。
3. **Raft Agent Network 整合** — 新捆綁 Raft platform adapter，透過 wake-channel bridge 讓 Raft 喚醒 Hermes 處理訊息；隱私設計：wake payload 只帶 metadata（event ID、timestamp），不傳訊息本體。
4. **影像生成進化：可編輯** — 圖片生成功能新增「上傳圖片並給出編輯指令」的工作流，直接在對話中完成影像修改。
5. **背景 Subagent** — Subagent 現可背景執行，主對話不需等待長任務完成，非阻塞委派工作成為可能。
6. **2026-06-28 最新 PR（今日）** — update snapshot pruning 修正、subagent prompt optimization、skills lock file issues 修復，均已提出 PR 等待合入。
7. **2026-06-27 Issues** — 新增 session state management 問題、Windows Copilot 整合 Bug、CLI 相關問題，正在排查中。
8. **認證 Bug Issue #44710（2026-06-12 報告）** — Cron job 因 token refresh 使用不存在的 `api.nousresearch.com` 而失敗（DNS NXDOMAIN），正確端點應為 `portal.nousresearch.com`；影響所有依賴排程的場景。

---

## 2. LLM 串接實戰回報

### 認證 Bug：Cron Job 全面失效（Issue #44710）

- **症狀**：Cron job 觸發時拋出 `getaddrinfo failed / Could not resolve host: api.nousresearch.com`，導致定時任務完全無法執行。
- **根本原因**：`hermes_cli/auth.py` 約第 4848 行的 `_refresh_access_token()` 函數把 token refresh 端點寫死為 `api.nousresearch.com`，而該 domain 根本不存在於 DNS。正確的 Nous Portal 端點 `portal.nousresearch.com` 已正確存於 `auth.json` 中，但程式碼未讀取它。
- **影響範圍**：Windows 10 + hermes-setup.exe（~2026-06-11 安裝）確認受影響；Linux/macOS 自行編譯版本狀況待確認。
- **臨時解法**：手動在 `hermes_cli/auth.py` 中將 hardcoded `api.nousresearch.com` 改為讀取 `credentials["portal_base_url"]`；或等待官方 patch。

> 來源：[Bug Issue #44710 · NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent/issues/44710)

### OAuth Token Refresh 歷史問題

- Issue #15099（v0.11.0）：Refresh token rotation 未持久化，RT 複用觸發跨 profile session 全數撤銷。
- Issue #2962：無頭 gateway 的 OAuth token refresh 指向錯誤端點 + 無恢復機制，導致 Anthropic Max 連線持續 401。
- **建議**：在無頭/server 部署中，定期手動觸發 `hermes auth refresh` 確認憑證狀態，並監控 `cron_scheduler.log` 的 auth 錯誤。

> 來源：[Issue #15099](https://github.com/NousResearch/hermes-agent/issues/15099) ｜ [Issue #2962](https://github.com/NousResearch/hermes-agent/issues/2962)

### 多 Provider 串接架構

- **Nous Portal** — 單一訂閱覆蓋 300+ frontier agentic models（Claude、GPT、Gemini、DeepSeek、Qwen、Kimi、GLM、MiniMax、Grok），無需分別管理多家帳號。
- **LiteLLM 整合** — OpenAI-compatible proxy 統一 100+ providers，最適合需要跨 provider 切換、load balancing、fallback chain 及預算控制的場景。
- **OpenRouter** — 透過單一 API key 存取 200+ 模型，含 Claude、GPT-4、Gemini 及開源選項；需注意 Hermes 要求最低 **64K token context**。
- **Fallback Provider** — Hermes 可在主要 provider 觸發 rate limit、伺服器過載、auth 失效或連線中斷時自動切換備用 provider:model pair，中途不遺失對話。

> 來源：[AI Providers | Hermes Agent](https://hermes-agent.nousresearch.com/docs/integrations/providers) ｜ [Fallback Providers | Hermes Agent](https://hermes-agent.nousresearch.com/docs/user-guide/features/fallback-providers)

### 模型選擇社群共識

- 最佳 tool-use 效果：使用 **Hermes 自家模型**（Hermes 3/4）搭配 Hermes Agent，function-calling 最可靠。
- 需要強力推理：外掛 Claude 或 GPT，必要時切換，兩者共存為多數進階用戶的配置。
- 最低上下文需求 **64K tokens**，長文脈場景建議選 Gemini 或 Claude Opus 4.7（各提供 1M context）。

---

## 3. 社群反應與討論亮點

- **官方 Nous Research Discord** 的 `#agent` 頻道是最活躍的即時討論場所，Bug 回報、設定分享、模型對比討論均聚集於此，v0.17.0 發布後討論量明顯升高。
- **r/LocalLLaMA** 討論焦點：Hermes 3/4 在本地硬體的效能表現、tool-use 可靠性、function-calling 實測；社群整體對本機推理表示正面但仍有穩定性疑慮。
- **r/selfhosted** 聚焦 homelab 及小型團隊部署，Hermes Desktop（Electron 版）的出現讓更多非技術背景的用戶開始嘗試自架。
- **Hermes Desktop 熱議**：Electron 桌面 app 支援 macOS / Linux / Windows、內建 Cmd+K 命令面板、模糊搜尋 model picker、串流輸出、session 列表，被視為 v0.17.0 最具可見度的功能亮點。
- 安全審查 Issue #40889 公開，社群關注 repository 安全態勢，維護者回應中。

---

## 4. 值得追蹤的後續議題

| 議題 | 說明 | 追蹤位置 |
|------|------|----------|
| Issue #44710 Cron Auth Bug | token refresh 打錯端點，影響所有排程任務；等待官方 patch | [NousResearch/hermes-agent#44710](https://github.com/NousResearch/hermes-agent/issues/44710) |
| 2026-06-27 Session State Issues | session 狀態管理問題，具體影響範圍待確認 | [Issues · NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent/issues) |
| Windows Copilot 整合 Bug | Copilot 連線在 Windows 環境不穩定 | [Issues · NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent/issues) |
| Raft Network 穩定性 | 新 Raft adapter 尚屬 v0.17.0 新功能，成熟度待社群實測驗證 | Nous Research Discord #agent |
| Hermes Desktop 自動更新 | Electron app 的 in-app 自動更新機制於多平台的兼容性 | [fathah/hermes-desktop Releases](https://github.com/fathah/hermes-desktop/releases) |
| Photon Spectrum（iMessage）穩定性 | 新 gRPC 通道在不同網路環境下的可靠性尚待社群廣泛測試 | [NousResearch/hermes-agent Issues](https://github.com/NousResearch/hermes-agent/issues) |

---

*本報告依搜尋結果編寫，未收錄 24 小時內有確認來源的資訊不列入；如有遺漏或錯誤請至 Issues 回報。*
