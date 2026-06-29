# Hermes-Agent 每日情報 — 2026-06-29

> 資料來源截止時間：2026-06-29 UTC｜搜尋範圍：過去 24 小時

---

## 1. 今日重點摘要

1. **v0.17.0「The Reach Release」（2026.6.19）仍為最新穩定版**：本次搜尋未發現 6/29 當日有新版本釋出；v0.17.0 於 2026-06-19 發布，包含約 1,475 次 commits、約 800 個合併 PR 與 300+ 已關閉 issues。
2. **iMessage 支援上線（免 Mac relay）**：透過 Photon 平台的 managed line pool，執行 `hermes photon login` 並以設備碼認證後，即可直接收發 iMessage，無需在機器上長期執行 Mac relay 或 BlueBubbles bridge。
3. **新增 WhatsApp Business Cloud API 頻道**：官方 WhatsApp Business Cloud API 整合正式推出，Telegram 亦獲得富文字（rich text）增強。
4. **Raft agent 網路作為 gateway**：Hermes 現可做為 Raft agent network 的 gateway 節點，開啟多 agent 協作的新路徑。
5. **背景 subagent 執行**：subagent 可在背景執行，桌面應用提供即時 subagent 監控視窗（live subagent watch window）。
6. **圖像生成支援編輯模式**：image generation 功能新增編輯能力；Cursor Composer 模型現可透過 xAI Grok 訂閱存取。
7. **Desktop 應用大幅強化**：macOS / Windows / Linux 三平台桌面應用新增每類通知獨立開關、可重新綁定快捷鍵、可調整大小的終端機面板，以及可安裝的 VS Code 主題包。
8. **Rate Limit 透明度問題（Issue #26889）**：當 LLM provider 回傳 429 錯誤時，Hermes 顯示「Rate limited. Waiting 60.0s (attempt 2/3)」；Provider 回應中內含重置時間但未顯示給使用者。Codex/ChatGPT 的 `resets_in_seconds` 欄位已被 `_extract_api_error_context()` 靜默忽略，issue 尚未修復。
9. **Skills Hub 改版 + 記憶工具重大升級**：Skills Hub 瀏覽器介面全面改版；memory tool 獲得重大更新，Curator 亦停止在每次例行執行時消耗 aux-model 預算。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）
- 整合方式：Hermes 支援 **Anthropic OAuth**，優先使用 Claude Code 的 credential store（保留 token 可刷新性），避免手動複製 token。
- API routing：Claude 使用 Chat Completions 端點（與 GPT-4o 同路徑）。
- 實際體驗：在 r/LocalLLaMA 等社群，以 Claude / GPT 對比 Hermes 自家模型的討論結論多指向「工具呼叫（tool use）可靠性以 Hermes 模型為佳」，但 Claude 在一般對話品質上有優勢。
- 來源：[AI Providers | Hermes Agent](https://hermes-agent.nousresearch.com/docs/integrations/providers)

### GPT-5+（OpenAI）
- GPT-5 及以上版本（gpt-5-mini 除外）**自動切換至 Responses API**，其餘（如 GPT-4o）仍走 Chat Completions。
- Rate Limit 問題：Codex / ChatGPT 429 回應體中有 `resets_in_seconds` 欄位，但 `_extract_api_error_context()` 只檢查 `resets_at`、`reset_at`、`retry_after`，導致 `resets_in_seconds` 被靜默忽略，使用者看不到實際重置時間（Issue #26889 開放中）。
- 來源：[Surface rate limit reset time — Issue #26889](https://github.com/NousResearch/hermes-agent/issues/26889)、[Hermes Agent RateLimitExceeded: 3 Fixes That Work in Production](https://markaicode.com/errors/hermes-agent-rate-limit-exceeded-fix-production/)

### Fallback Provider（跨 provider 自動切換）
- 當主要 provider 遭遇 rate limit、伺服器過載、auth 失效或連線中斷時，Hermes 可在**不中斷對話**的情況下自動切換至備用 provider:model 配對。
- 此功能是緩解 rate limit 問題的官方推薦方式，設定說明見官方文件。
- 來源：[Fallback Providers | Hermes Agent](https://hermes-agent.nousresearch.com/docs/user-guide/features/fallback-providers)

### xAI Grok（Cursor Composer）
- v0.17.0 新增：Cursor 的 Composer 模型可透過 xAI Grok 訂閱存取，為程式碼編輯場景提供新選擇。
- 實戰經驗尚少，社群討論中僅見初步嘗試，穩定性有待觀察。

---

## 3. 社群反應與討論亮點

- **Discord**：官方 Nous Research Discord 的 agent 頻道持續活躍，6/15 快照顯示 1,175 個討論串、8,030 則訊息；近期話題包含 bot 邀請設定、AI agent 與 Discord 互動配置、gateway 重啟修復等。
- **Reddit**：
  - **r/LocalLLaMA**：模型效能 benchmark、工具呼叫可靠性、self-hosting 成本為主要討論焦點；多數結論認為搭配 Hermes 自家模型能獲得最佳工具呼叫能力。
  - **r/selfhosted**：聚焦運營面，討論自架成本、部署穩定性、與其他框架（CrewAI、AutoGPT）的比較。
  - **r/AIAgents**：use-case 導向討論，Hermes-Agent 被拿來與多個 agent 框架比較。
- **Threads 貼文**（@artificiallyinfluenced）：v0.17.0 發布貼文獲得廣泛轉發，iMessage 免 Mac relay 功能被視為「終於做到」的里程碑，評論正面居多。
- **整體氛圍**：社群對「The Reach Release」反應積極；Desktop 功能的成熟度與新頻道整合（iMessage、WhatsApp）讓非技術用戶的採用門檻明顯降低。

---

## 4. 值得追蹤的後續議題

| 議題 | 說明 | 追蹤連結 |
|------|------|----------|
| Issue #26889 | rate limit 重置時間未顯示給使用者（含 `resets_in_seconds` 靜默忽略） | [GitHub Issue #26889](https://github.com/NousResearch/hermes-agent/issues/26889) |
| v0.18.x 下一版本 | v0.17.0 後無新 release，下一個里程碑功能尚未公告 | [Releases · NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent/releases) |
| Photon iMessage 穩定性 | 新功能上線，社群實戰回報尚少，需觀察 managed line pool 可靠性 | — |
| Cursor Composer via Grok | 新整合，效能與穩定性有待更多社群回報 | — |
| memory tool 升級後遷移 | 重大更新可能影響現有 memory 資料格式，建議關注官方遷移指南 | [Changelog — Hermes Agent](https://hermes-ai.net/changelog/) |

---

*本報告依據公開搜尋結果撰寫，不代表 Nous Research 或 Hermes-Agent 官方立場。如有資訊誤差請以官方 GitHub 及文件為準。*
