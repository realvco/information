# Hermes-Agent 每日情報 — 2026-06-23

## 1. 今日重點摘要

1. **v0.17.0「Reach Release」（2026-06-19）里程碑**：過去 24 小時內社群持續討論本次大版本，1,475 commits、245 位貢獻者，是 Hermes 迄今規模最大的單次版本。
2. **iMessage 頻道正式支援（無需 Mac Relay）**：透過 Photon 的 managed line pool，只需執行 `hermes photon login` 完成裝置碼驗證，Hermes 即可收發 iMessage，不再需要一台 Mac 常駐中繼。
3. **WhatsApp Business Cloud API 整合**：v0.17.0 新增官方 WhatsApp Business Cloud API 頻道，擴展至即時通訊平台的覆蓋範圍再升級。
4. **Raft 代理人網路閘道**：新增 Raft platform adapter，Hermes 可作為外部 agent 接入 Raft 網路，透過 wake-channel bridge 被喚醒，採用 privacy-by-contract 設計。
5. **Blank Slate 模式（2026-06-20 公告）**：最輕量安裝模式，僅啟用 provider/model、檔案操作與終端機，其餘工具（Web、瀏覽器、程式執行、視覺、記憶、委派、cron、skills、plugins、MCP）全部停用，設定寫入 `platform_toolsets.cli` 與 `agent.disabled_toolsets`，`hermes update` 後依然有效。
6. **非同步子代理人（Async Subagents，2026-06-16 發布）**：委派任務後主對話不再阻塞等待，子代理人在背景完成工作後自動回傳結果，是多代理人工作流的關鍵架構改進。
7. **Rate Limit 可視性改進（Issue #26889）**：社群回報 LLM 廠商回傳 429 時，Hermes 顯示的等待訊息缺乏 reset 時間點，功能請求已開 issue，目前尚待合併。
8. **Claude Agent SDK OAuth 支援請求（Issue #25267）**：社群希望 Hermes 能以類似 Codex 的 subscription OAuth 方式整合 Claude Agent SDK，而非純 API key，目前為 feature request 狀態。

---

## 2. LLM 串接實戰回報

### Claude 整合
- **Context Window 差異陷阱**：claude-opus-4.6 在 Anthropic 直連提供 1M token 上下文，但透過 GitHub Copilot 路徑接入時只有 128K。使用 GitHub Copilot 作為 provider 時，長文本任務可能靜默截斷。
  - 來源：[AI Providers | Hermes Agent](https://hermes-agent.nousresearch.com/docs/integrations/providers)
- **Claude Agent SDK OAuth（#25267）**：使用者希望以 subscription 式 OAuth 取代 API key，使 Hermes 能在不暴露密鑰的情況下整合 Claude Agent SDK。
  - 來源：[Issue #25267 · NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent/issues/25267)

### GPT 整合
- **Token Per Minute（TPM）上限是主要瓶頸**：GPT-4 搭配長 system prompt 與 tool schema，每個 agent turn 消耗 3,000–8,000 tokens，40k TPM 上限在 5–13 位並發使用者時即觸頂。建議措施包括 token bucket 節流、指數退避、API key 輪換。
  - 來源：[Hermes Agent RateLimitExceeded: 3 Fixes That Work in Production | Markaicode](https://markaicode.com/errors/hermes-agent-rate-limit-exceeded-fix-production/)

### Rate Limit 通用問題
- **429 等待訊息缺少 reset 時間（#26889）**：Hermes 回傳類似「⏱️ Rate limited. Waiting 60.0s (attempt 2/3)...」但不顯示廠商回傳的 reset 時間戳記，導致使用者無從判斷實際等待時長。此問題已在 issue 中被多個廠商 API 使用者確認。
  - 來源：[Issue #26889 · NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent/issues/26889)

### Fallback Provider 機制
- Hermes 支援在主 LLM 遇到 rate limit、伺服器過載、auth 失敗或連線中斷時，自動切換到備援 provider:model 組合，mid-session 也可切換。詳見官方文件。
  - 來源：[Fallback Providers | Hermes Agent](https://hermes-agent.nousresearch.com/docs/user-guide/features/fallback-providers)

### 最佳模型選用（2026 社群參考）
- 根據 [Best Hermes Agent Model 2026](https://www.remoteopenclaw.com/blog/best-models-for-hermes-agent) 社群比較：Claude 在推理與工具使用任務表現最穩定；DeepSeek 在低成本高吞吐場景有競爭力；OpenRouter 作為路由層可提高容錯率。

---

## 3. 社群反應與討論亮點

- **Blank Slate 模式獲得高度正面迴響**：X 上 `@sudoingX` 表示「這是我最近看到最喜歡的功能決策之一」，強調「從幾乎什麼都沒有開始，自行選擇要加入的工具」的哲學與主流 agent 框架的全量安裝做法形成鮮明對比。
  - 來源：[Sudo su on X](https://x.com/sudoingX/status/2068413679360700801)
- **Teknium（NousResearch）親自在 X 宣布 Blank Slate**：官方創始人層級的推文確認功能已出貨，社群信任度高。
  - 來源：[Teknium on X](https://x.com/Teknium/status/2068405612514554153)
- **Hermes Desktop 在 Jensen Huang GTC keynote 首次 Demo**：NousResearch 官方 X 貼文指出 Hermes Desktop 曾在 Jensen 的 GTC 主題演講中亮相，為專案帶來顯著的曝光度。
  - 來源：[Nous Research on X](https://x.com/NousResearch/status/2061843507417944552)
- **v0.17.0 Reach Release 的規模震驚社群**：1,475 commits 與 245 位貢獻者讓不少觀察者表示「這不像一個小型開源專案的節奏」，顯示 Hermes 生態圈正在快速擴張。
  - 來源：[Hermes Agent v0.17.0: 1,475 Commits, 245 Contributors](https://www.tonyreviewsthings.com/hermes-agent-v0170-reach-release/)

---

## 4. 值得追蹤的後續議題

- **v0.18.0 下一個版本走向**：v0.17.0 擴展了通訊頻道，預期下一版本可能著重在 Raft 網路的穩定化、非同步子代理人的進一步優化，或更多 LLM provider 整合。追蹤：[Releases · NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent/releases)
- **Rate Limit Reset 時間顯示（#26889）**：此 issue 對多 LLM provider 用戶實用性高，關注是否在近期版本中合入。
- **Claude Agent SDK OAuth 整合（#25267）**：若實現，將允許 Hermes 以更安全的方式串接 Claude，對企業級用戶尤其重要，值得持續關注進展。
- **Hermes Desktop 後續迭代**：Electron app 在 v0.16.0 首次正式推出，目前仍在 public preview 階段，後續穩定版與企業部署支援值得追蹤。
- **Blank Slate 模式的工具精選生態**：隨著 Blank Slate 模式推出，社群可能開始分享各種「最小化工具組合」範本（如「只加 Web 搜尋」、「只加程式執行」），形成新的最佳實踐沈澱。
- **iMessage + WhatsApp + Raft 三大新頻道的穩定性報告**：v0.17.0 中三個全新頻道同時推出，實際生產穩定性有待社群回報，尤其是 iMessage via Photon 在帳號認證與訊息延遲方面的表現。
