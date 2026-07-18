# OpenClaw 每日情報｜2026-07-18

## 1. 今日重點摘要

1. **v2026.7.1 正式發布**：OpenClaw 本週推出重大更新，帶來 Control UI 全面改版、更流暢的初次設定流程，以及 iOS、Android、macOS 官方 App 的大幅升級。
2. **v2026.7.2 進入 Beta 測試**：下一版已釋出 beta.1 / beta.2，主要新增遠端編碼工作階段（Remote Coding Sessions）、雲端 Worker 節點、引導式 Control UI 設定，以及 Linux / Windows 安裝強化。
3. **新模型與新 Provider 支援**：v2026.7.1 新增 **ClawRouter**（內建 provider 外掛，具備動態模型發現與預算回報）、GPT-5.6（作為新裝設的預設模型）、**Claude Sonnet 5 / Mythos 5**、Meta Muse Spark 1.1、Tencent Hy3，並擴充 Featherless 支援。
4. **Claude Sonnet 4.6 奪下 PinchBench 榜首**：在 OpenClaw 官方基準測試 PinchBench 中，Claude Sonnet 4.6 以 86.9% 成功率位居第一，GPT-5.4 以 86.0% 緊隨其後。
5. **Control UI 重大改版**：對話管理改為類似瀏覽器分頁的工作區體驗，新增置頂與群組功能、可調整並行工作階段的分割畫面（重載後持續保留）。
6. **已知 Bug：虛假「API 速率限制」錯誤**（Issue #32828）：部分使用者在 API 完全正常運作的情況下，仍持續收到「API rate limit reached」訊息，問題根源在於 `buildRateLimitCooldownMessage()` 未正確區分計費失敗與實際速率限制。
7. **原生行動裝置自動化強化（v2026.7.2 預覽）**：Android 新增前景語音喚醒（Voice Wake），行動端 Automations 功能與桌面版齊平，並開放攝影機、位置與通知能力給無頭式 Linux 節點。
8. **Codex 工作流強化**：GPT-5.6 使用者可在 OpenClaw 各介面統一選用支援的 Ultra 模式，子代理（child-agent）結果以追蹤任務形式回傳。
9. **Gemini 支援仍有已知缺口**：目前 Gemini 模型的 caching、embeddings 及 thinking 功能在 OpenClaw 中仍無法正常運作，官方尚未給出具體修復時程。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）
- **最佳工具呼叫可靠度**：社群普遍認為 Claude 在 tool calling 穩定性上最佳，原生 MCP 支援讓企業安全審查較易通過。
- **Claude Sonnet 5 整合方式**：v2026.7.1 起可透過 Anthropic 直接 API、Claude CLI、Vertex AI 支援的區域、Bedrock 推論設定檔（inference profile）及 Bedrock Mantle 選用 Claude Sonnet 5。
- **ClawRouter 整合**：ClawRouter 支援 Anthropic 原生傳輸協定，可跨 OpenClaw 各介面統一進行預算管理與費用回報。
- **速率限制踩坑**（[來源](https://dev.to/sophiaashi/claude-max-plan-rate-limit-getting-worse-since-march-23-2026-59di)）：3 月起 Claude Max 方案速率限制惡化的討論仍在延燒；在緊密循環的 agentic 任務中，RPM 與每日 token 上限特別容易觸發，建議加入請求間隔或採用 token bucket 節流。

### GPT（OpenAI）
- **GPT-5.6 為新裝預設**：新安裝的 OpenClaw 執行個體預設使用 GPT-5.6，Ultra 模式對 Sol、Terra 啟用 `/think`，對 Luna 啟用 `max`。
- **Codex 子代理**：Codex child-agent 結果可作為追蹤任務回傳，OpenAI 與 Codex 路由的 GPT-5.6 相容性在此版獲得改善。

### Gemini（Google）
- **已知功能缺口**（[來源](https://cowork.ink/blog/best-model-for-openclaw/)）：Gemini 模型的 caching、embeddings、thinking 功能目前在 OpenClaw 中不可用，實際使用有顯著限制，暫不建議作為主要 provider。

### 其他 Provider
- **Ollama / DeepSeek / 開源模型**：透過 Provider Manifest 可在不重建工作流程的情況下於執行期切換，若自定義 provider 需設定 `baseUrl`，缺少此欄位是最常見的切換失敗原因（[來源](https://www.getopenclaw.ai/help/switching-models-provider-config)）。

---

## 3. 社群反應與討論亮點

- **v2026.7.1 普遍好評**：社群對 Control UI 改版反應正面，多工並行工作的使用體驗被認為顯著提升。
- **ClawRouter 受到關注**：統一預算管理與跨 provider 費用追蹤功能被認為是企業部署的重要補強。
- **虛假速率限制 Bug（Issue #32828）引發討論**（[來源](https://github.com/openclaw/openclaw/issues/32828)）：已有多名使用者在 API 正常的情況下遭遇此問題，目前尚在追查中。
- **X.com（@openclaw）**：官方帳號近期宣傳了 Diffs 外掛更新，以及 v2026.7.1-beta.6 的釋出記錄（[來源](https://x.com/openclaw/status/2028338035390140883)）。
- **Gemini 不支援聲浪持續**：在各論壇中，希望 Gemini caching 與 embeddings 盡快修復的討論持續出現。

---

## 4. 值得追蹤的後續議題

| 議題 | 說明 |
|------|------|
| **v2026.7.2 正式版釋出時程** | 目前仍在 beta 階段，遠端編碼工作階段與雲端 Worker 為核心新功能 |
| **Issue #32828：虛假速率限制 Bug** | `buildRateLimitCooldownMessage()` 未區分計費失敗，多名用戶受影響，尚待官方修復 |
| **Gemini 功能缺口修復** | caching / embeddings / thinking 均無法使用，官方未給出修復時程 |
| **ClawRouter 預算回報完整功能** | credential-scoped 動態模型發現與跨介面費用整合，值得觀察實際生產環境表現 |
| **PinchBench 基準更新** | Claude Sonnet 5 與 GPT-5.6 尚未完整出現在基準測試中，後續排名值得關注 |

---

*資料來源：[docs.openclaw.ai](https://docs.openclaw.ai/releases/2026.7.1) · [github.com/openclaw/openclaw](https://github.com/openclaw/openclaw) · [releasebot.io](https://releasebot.io/updates/openclaw) · [betterclaw.io](https://www.betterclaw.io/blog/openclaw-rate-limit) · [cowork.ink](https://cowork.ink/blog/best-model-for-openclaw/) · [dev.to](https://dev.to/sophiaashi/claude-max-plan-rate-limit-getting-worse-since-march-23-2026-59di)*
