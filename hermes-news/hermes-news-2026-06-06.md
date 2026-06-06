# Hermes-Agent 每日情報 — 2026-06-06

> 搜尋範圍：過去 24 小時（2026-06-05 至 2026-06-06 UTC）

---

## 1. 今日重點摘要

1. **Hermes Desktop 公開預覽熱度持續**：Nous Research 於 6 月 2 日發布 Hermes Desktop（基於 v0.15.2），本週期社群討論度高漲；這是 Hermes Agent 首款官方原生跨平台 GUI，支援 macOS 12+、Windows 10/11、Linux，MIT 授權。
2. **GTC 主題演講加持**：Hermes Desktop 曾在 Jensen Huang GTC 主題演講中首次展示，進一步拉高品牌曝光；NousResearch 官方 X 帳號同步公告。（[@NousResearch on X](https://x.com/NousResearch/status/2061843507417944552)）
3. **v0.15.x 系列最新狀態**：目前最新穩定版為 v0.15.2（5/30 發布），v0.15.1 修補了 Docker/loopback 模式下的 Dashboard 無限重載循環（在 v0.15.0 發布翌日即推出 hotfix）。
4. **The Velocity Release 架構重構成果**：v0.15.0 將 16,083 行的 run_agent.py 縮減至 3,821 行（-76%），拆分為 14 個模組；session_search 提速 4,500 倍且不再計費；啟動時間再減少一秒。
5. **Kanban 多 Agent 平台成熟**：v0.15.0 中 Kanban 完成 104 個 PR，具備 Orchestrator 自動分解、Swarm 拓撲、排程任務、per-task worktree 及 per-task 模型覆蓋功能；v0.13.0 已奠基心跳、殭屍偵測、幻覺復原等機制。
6. **Brainworm 類攻擊防禦（Promptware Defense）上線**：v0.15.0 新增針對 Brainworm-class prompt injection 攻擊的防禦層，是 Agent 安全強化的重要里程碑。
7. **hermes-agent-desktop 第三方版本崛起**：GitHub 上有 `Felix-Forever/hermes-agent-desktop`（Kimi K2.5/Qwen/DeepSeek/GPT-4o 支援，Visual Skill Store，PM Orchestrator），已積累一定星數，社群開始比較官方版與第三方版的差異。（[Felix-Forever/hermes-agent-desktop](https://github.com/Felix-Forever/hermes-agent-desktop)）
8. **GitHub 社群規模**：截至 5 月底 NousResearch/hermes-agent 倉庫約 180,000 stars、30,900 forks，v0.15.0 有 321+ 貢獻者。
9. **DeepSeek V4 Pro via OpenRouter 崩潰迴圈問題持續**：Issue #16677 回報 OpenRouter 上游「Io Net」的 DeepSeek V4 Pro 在觸發 rate limit 後 gateway 陷入崩潰重啟迴圈，官方建議改用個人 DeepSeek API Key 繞過共享池限制。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）

- **工具調用最穩定**：Claude Sonnet 4.6 在生產環境中工具調用可靠性最高，被社群評選為 Hermes Agent 首選模型之一。工具調用品質不佳會導致格式錯誤的函式調用，造成重試與 token 浪費。
  - 來源：[BSWEN — Hermes Agent LLM Configuration Guide](https://docs.bswen.com/blog/2026-04-21-hermes-agent-model-configuration/)
- **Issue #31668 — Anthropic 模型 rate limit / 異常用量**：部分用戶回報 Hermes 在使用 Anthropic 模型時觸發比預期更高的 API 用量，疑與 context compaction 頻率有關，issue 尚開放。
  - 來源：[GitHub Issue #31668](https://github.com/NousResearch/hermes-agent/issues/31668)
- **Context Window 匹配注意**：輔助摘要模型（summary model）的 context window 必須 ≥ 主模型，否則摘要調用失敗時中間對話靜默丟棄，難以察覺。

### GPT（OpenAI）

- GPT-4.1 與 Claude Sonnet 4.6 並列為工具調用可靠性最高的模型，適合對工具調用正確率要求高的場景。
- 來源：[remoteopenclaw.com — Best Models for Hermes Agent](https://www.remoteopenclaw.com/blog/best-models-for-hermes-agent)

### Gemini（Google）

- Gemini 原生 `video_analyze` 工具支援已在 v0.13.0 上線，適合媒體分析工作流。
- 來源：[GitHub Releases — v0.13.0 Tenacity](https://github.com/NousResearch/hermes-agent/releases/tag/v2026.5.7)

### DeepSeek

- **OpenRouter 共享池 rate limit 崩潰**：Issue #16677 確認 DeepSeek V4 Pro 透過 OpenRouter 使用「Io Net」上游時，rate limit 後 gateway 不能優雅恢復，進入重啟迴圈。解法：在 OpenRouter 整合設定中加入個人 DeepSeek API Key 取得獨立配額。
  - 來源：[GitHub Issue #16677](https://github.com/NousResearch/hermes-agent/issues/16677)
- **NVIDIA NIM 配額申請**：有用戶在 NVIDIA Developer Forums 申請從 40 RPM 提升至 200 RPM，用於 Hermes Agent + DeepSeek V4 Pro 的交易機器人持續運作。
  - 來源：[NVIDIA Developer Forums](https://forums.developer.nvidia.com/t/requesting-a-rate-limit-increase-from-40-to-200-rpm-for-sustained-hermes-agent-deepseek-v4-pro-trading-bot-usage-on-nvidia-nim/370290)

### Kimi（Moonshot）/ 多模型組合

- 第三方 `hermes-agent-desktop` 展示了 Kimi K2.5 為主模型、搭配 Qwen 做輔助的組合配置，吸引中文社群興趣。
- Nous Portal 統一訂閱可涵蓋 300+ 模型（Claude、GPT、Gemini、DeepSeek、Qwen、Kimi、GLM、MiniMax、Grok…），單一 OAuth 管理。
  - 來源：[Hermes Agent Providers Docs](https://hermes-agent.nousresearch.com/docs/integrations/providers)

---

## 3. 社群反應與討論亮點

- **Hermes Desktop 離開終端機里程碑**：Decrypt、The Decoder、MarkTechPost 等媒體以「終結 AI Agent 終端機時代」等標題報導，引發大量討論，不少過去因命令列門檻放棄的用戶表示會重新嘗試。
  - 來源：[Decrypt — Hermes Ends AI Agent Terminal Era](https://decrypt.co/369952/hermes-ai-agent-official-app-terminal)
- **GTC 背書效應**：Jensen Huang 主題演講首秀讓 Hermes Desktop 在 AI 圈外也有曝光，吸引非開發者族群關注。（[@NousResearch on X](https://x.com/NousResearch/status/2061843507417944552)）
- **官方 vs. 第三方 Desktop 比較**：`Felix-Forever/hermes-agent-desktop` 早已整合 Kimi K2.5 與 Qwen，官方 Desktop 剛出，社群開始討論功能差異與維護持續性。
- **v0.15.0 架構重構評價**：run_agent.py 縮減 76% 受到廣泛好評，多位貢獻者在 X 上分享模組化帶來的可讀性提升；也有人質疑此次大幅重寫是否引入新 bug。
- **DeepSeek 崩潰迴圈**：Issue #16677 在社群中引發較大迴響，部分用戶建議 Hermes 應內建自動 provider 降級邏輯，不應依賴用戶手動調整 API Key。

---

## 4. 值得追蹤的後續議題

| 議題 | 狀態 | 追蹤建議 |
|------|------|---------|
| Hermes Desktop 公開預覽穩定性 | 預覽中（v0.15.2） | 追蹤 GitHub Releases 及官方 Discord，觀察 bug 修復速度 |
| GitHub Issue #31668 — Anthropic 異常用量 | 開放中 | 使用 Claude 模型者需關注帳單，定期確認 token 消耗 |
| GitHub Issue #16677 — DeepSeek via OpenRouter 崩潰 | 開放中 | 確認使用個人 API Key 的 workaround 是否已被整合為預設行為 |
| v0.16.0 下一版本路線圖 | 尚未公布 | 關注 NousResearch GitHub Milestones 頁面 |
| Brainworm 防禦機制實效評估 | 新上線 | 等待安全研究社群的獨立測試報告 |
| Kanban 多 Agent 平台穩定性 | 持續迭代 | 觀察大規模 swarm 使用場景的回饋 |

---

*報告產生時間：2026-06-06 UTC｜資料來源均來自公開網路搜尋，不保證即時完整性。*
