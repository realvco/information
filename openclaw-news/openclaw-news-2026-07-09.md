# OpenClaw 每日情報 — 2026-07-09

## 今日重點摘要

1. **最新版本 2026.7.1-beta.2 於 7 月 5 日釋出**：主要新增 OpenAI GPT-5.6 的支援，跨 catalog、capability 與 runtime 選擇路徑均已整合（Issue #98333）。

2. **新功能：External Harness Attachment**：`openclaw attach` 可掛載至既有 Gateway session，讓 Codex 風格的互動工作流更容易續接與檢視（Issue #96454）。

3. **Telegram Codex 工作流強化**：Telegram 頻道現可用 `/login` 啟動 Codex pairing，操控進行中的 Codex run，並在短暫 API 失敗後自動復原最終回應（Issues #98006、#98126、#98786）。

4. **事件驅動排程新增 `on-exit` 類型**：當監聽的指令結束時自動喚醒 agent，session 目標的 run 也可以乾淨地 detach。

5. **iOS 26 視覺系統更新**：iOS 原生 App 採用 iOS 26 設計語言，Chat、Talk、onboarding 與 reconnect 流程全面更新；Android 亦擴展了多語系在地化。

6. **Ollama 本地推理自動探索**：新增對 Ollama 推理節點的自動發現功能，附帶推理控制與指令選擇 UI，讓本地推理更易用。

7. **Repository 活躍度高**：截至 7 月 9 日，openclaw/openclaw 共有 379k+ stars、3,300+ open issues，過去 24 小時內有兩個高優先 Issue 被開啟（#102324 by yetval、#102345 by reply2future，均與 ClawSweeper fix-shape-clear 相關）。

8. **診斷功能增強**：新版本暴露更多診斷項目，包含 auth-profile 狀態、workspace 設定、device-pairing、channel-plugin、memory-provider、systemd 資源耗盡、以及 Windows LAN 防火牆問題。

9. **Anthropic 訂閱 OAuth token 封鎖政策持續有效**：自 2026 年 4 月 4 日起，Anthropic 禁止在第三方工具中使用訂閱型 OAuth token，OpenClaw 使用者持續受到影響（詳見 LLM 串接章節）。

---

## LLM 串接實戰回報

### Claude / Anthropic 整合

- **OAuth Token 封鎖**：Anthropic 自 2026/04/04 起限制訂閱 OAuth token 僅限官方產品（Claude.ai、Claude Code、Claude Desktop），OpenClaw 等第三方工具必須改用「Extra Usage」點數或獨立 API Key。來源：[XDA Developers 報導](https://www.xda-developers.com/claude-subscribers-just-lost-access-to-openclaw-and-other-third-party-toolsunless-they-pay-more/)、[Medium 修復教學](https://medium.com/@ulmeanuadrian/anthropic-just-cut-off-my-ai-agents-heres-how-i-fixed-it-in-20-minutes-741384d27080)

- **必要設定：`api: "anthropic-messages"` 格式**：使用 Claude API 時，若採用 `openai-completions` 格式，多輪 tool call 會觸發 400 錯誤；必須指定 `anthropic-messages` 格式，並確認 `baseUrl` 含 `/v1`、停用 `anthropic-beta` 及 `reasoning`、清除既有 session cache。來源：[Apiyi.com 配置指南](https://help.apiyi.com/en/openclaw-claude-api-apiyi-anthropic-messages-guide-en.html)

- **Rate Limit 429 錯誤多重根因**：同樣顯示「rate limit exceeded」的錯誤，可能來自上游模型 provider 配額、ClawHub skill 下載限制、gateway cooldown 狀態、未設定 fallback provider，或 agent session 過大產生過多 API call。來源：[Laozhang AI Blog](https://blog.laozhang.ai/en/posts/openclaw-rate-limit-exceeded-429)

### GPT / OpenAI 整合

- OpenClaw 429 冷卻後不會自動切換 provider，除非事先設定 fallback；GPT-5.6 已在 2026.7.1-beta.2 版本中獲得支援。

### 費用與 Token 控制

- **缺乏內建預算機制（已知問題）**：OpenClaw 目前沒有內建 token budget、每日費用上限，或重試迴圈的熔斷機制，有用戶反映 agent 在無限重試下造成費用暴增。GitHub Issue [#58826](https://github.com/openclaw/openclaw/issues/58826) 正在追蹤此功能需求。來源：[GetOpenClaw 費用管理指南](https://www.getopenclaw.ai/help/token-usage-cost-management)

- **多 auth profile 可見性不足**：當同時設定多個 auth profile（如 Anthropic OAuth + API Token + OpenAI Codex），目前無法在單一介面檢視各 profile 的使用量與 rate limit 狀態。GitHub Issue [#24865](https://github.com/openclaw/openclaw/issues/24865) 追蹤此功能請求。

---

## 社群反應與討論亮點

- 關於 Anthropic OAuth 封鎖的後續討論在社群中持續延燒，部分使用者選擇改用 API proxy 服務（如 Apiyi）以繞開訂閱限制，同時統一管理多家模型的 API key。

- 過去 24 小時 Reddit/Discord 上沒有找到特別集中的新討論，但關於「如何 24/7 運行 OpenClaw 而不超預算」的教學文章仍是社群熱門主題，強調透過 Smart Model Manager 搭配本地 Ollama 節省費用。來源：[Perelweb Blog](https://perelweb.be/blog/openclaw-token-management-smart-model-manager/)

- 技術社群對 External Harness Attachment 與 Telegram Codex 整合反應正面，認為這兩個功能讓開發者工作流程更彈性。

- iOS 26 視覺更新被部分使用者認為是值得期待的體驗改善，但目前尚無大量公開評論。

---

## 值得追蹤的後續議題

1. **內建 Token Budget 功能**（Issue #58826）：目前無費用熔斷機制，是長時間 agent 運行的主要風險點，後續進展值得持續關注。

2. **多 Auth Profile 可視化**（Issue #24865）：有助於同時管理 Claude、GPT 等多 provider 的用量，功能需求已提交但尚未排入 roadmap。

3. **Anthropic 訂閱政策動向**：Anthropic 持續調整第三方 token 存取規則，未來是否有官方 OpenClaw 支援方案或授權機制尚不明朗。

4. **ClawSweeper fix-shape-clear 問題**（Issues #102324、#102345）：這兩個於 7 月 9 日新開的 issue 目前尚無回應，值得追蹤是否為新的 regression。

5. **GPT-5.6 實際使用表現**：2026.7.1-beta.2 新增 GPT-5.6 支援，社群對其在 agent 工作流中的實際效能比較尚待累積回饋。

---

*資料來源*：
- [GitHub openclaw/openclaw](https://github.com/openclaw/openclaw)
- [Releasebot OpenClaw July 2026](https://releasebot.io/updates/openclaw)
- [XDA Developers — Claude OAuth 封鎖報導](https://www.xda-developers.com/claude-subscribers-just-lost-access-to-openclaw-and-other-third-party-toolsunless-they-pay-more/)
- [Medium — Anthropic Auth Fix](https://medium.com/@ulmeanuadrian/anthropic-just-cut-off-my-ai-agents-heres-how-i-fixed-it-in-20-minutes-741384d27080)
- [Apiyi — Claude API 配置指南](https://help.apiyi.com/en/openclaw-claude-api-apiyi-anthropic-messages-guide-en.html)
- [Laozhang AI Blog — Rate Limit 429 排解](https://blog.laozhang.ai/en/posts/openclaw-rate-limit-exceeded-429)
- [GetOpenClaw — Token 費用管理](https://www.getopenclaw.ai/help/token-usage-cost-management)
- [GitHub Issue #58826](https://github.com/openclaw/openclaw/issues/58826)
- [GitHub Issue #24865](https://github.com/openclaw/openclaw/issues/24865)
