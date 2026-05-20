# OpenClaw 每日情報 — 2026-05-20

> 搜尋範圍：過去 24 小時（UTC 2026-05-19 ～ 2026-05-20）
> 來源：GitHub Issues/Releases、Releasebot、dev.to、blink.new 等

---

## 1. 今日重點摘要

1. **最新版本狀態**：截至 2026-05-20，穩定版最新為 **v2026.5.18**，預發布版為 **v2026.5.19-beta.1**，主 repo 依然維持高頻率迭代節奏。
2. **今日新開 Issues**：#84386（標籤：`needs-live-repro`、`impact:message-loss`，由用戶 wangwllu 提交）、#84384、#84376 均於 2026-05-20 建立，目前進入 triage 流程。
3. **Codex 動態工具呼叫卡住問題**：Issue #83474 紀錄「Codex dynamic tool calls can leave sessions stuck」，仍為開放狀態，影響 Codex 工作流中動態工具呼叫的可靠性。
4. **依賴版本更新**：`@openclaw/proxyline` 升至 0.3.3，Pi 套件升至 0.75.1，Node.js 最低支援版本提升至 22.19。
5. **安全修補**：本週修補 GitHub Copilot 工作階段中 Responses API 非法 reasoning replay item 導致 `invalid_request_body` 的問題（修復 #83220），另修補 Docker harness 將 port 綁定至 loopback-only 的安全缺口。
6. **Provider 架構調整**：WhatsApp、Slack、Amazon Bedrock、Anthropic Vertex 等 provider 已從 core runtime 移出為獨立 plugin dependency，降低核心體積。
7. **Agents/Codex fail-close 行為修正**：當明確指定的 Codex harness 未被註冊時，現在會主動失敗，而非靜默 fallback 到其他模型（修復 #83349），提升工作流可預測性。
8. **新 CLI 外掛工具**：`defineToolPlugin` 及 `openclaw plugins build / validate / init` 指令正式可用，支援帶型別的簡單工具外掛、自動生成 manifest metadata 及 context factory。
9. **Mac app 修復**：修補 SwiftUI 在渲染 Cron Jobs 設定面板時發生的 metadata crash。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic API）

- OpenClaw 4 月正式加入 **Anthropic Vertex AI** 支援，可透過 GCP 憑證驗證，無需直接使用 Anthropic API key。
- 社群推薦 **Claude Opus 4.7** 用於嚴格程式碼審查工作流，其 SWE-bench Pro 成績達 64.3%，同時具備自我檢查（self-checking）行為，適合需要高精確度的最終 review 階段。
- **踩坑**：Codex + Anthropic Vertex 混用時，`approval-runtime` 憑證未正確傳遞導致 async 命令卡住，已於近期 commit 修復（approval call 現會 forward 憑證）。
- 來源：[OpenClaw LLM Setup Guide](https://blog.laozhang.ai/en/posts/openclaw-llm-setup)、[Best LLM for OpenClaw — DEV Community](https://dev.to/matthewrevell/best-llm-for-openclaw-gemini-31-pro-vs-gpt-55-vs-claude-opus-47-2026-3na4)

### GPT-5.5（Codex）

- OpenClaw 預設模型已從 GPT-4 升級至 **GPT-5.40**；Codex agentic runtime 是目前 GPT-5 系列的主要接入路徑。
- 社群反映 GPT-5.5 在自主 agent 工作流表現最佳，Terminal-Bench 2.0 排名領先，但 **context 上限 128K tokens/call** 是實際使用瓶頸，超出後需手動分段。
- **踩坑**：Codex harness 若未正確註冊，舊版本會靜默 fallback 至 configured model，導致不預期行為；新版修復為 fail-close，需確認 harness 已存在。
- 來源：[OpenClaw April 2026 Model Agnostic Provider Manifest — MindStudio](https://www.mindstudio.ai/blog/openclaw-april-2026-model-agnostic-provider-manifest)

### Gemini（Google API）

- **Gemini 3.1 Pro** 被社群視為「性價比最高」的 OpenClaw 搭配選項：大 context 程式碼審查、最低成本、免費 dev tier、原生 multimodal。
- Gemini MCP 整合已透過 Composio 工具包支援，可直接在 OpenClaw 工作流中調用 Gemini 能力。
- 來源：[Best LLM for OpenClaw — DEV Community](https://dev.to/matthewrevell/best-llm-for-openclaw-gemini-31-pro-vs-gpt-55-vs-claude-opus-47-2026-3na4)、[How to Integrate Gemini MCP with OpenClaw — Composio](https://composio.dev/toolkits/gemini/framework/openclaw)

### 多模型路由策略

- 社群分享的實戰模式：同一工作流內以本地 **Gemma 4**（Ollama）處理初步 triage → **GPT-5.5 via Codex** 執行實作 → **Claude API** 進行最終架構審查，透過 provider manifest 在執行期動態切換，無需重建工作流。
- 來源：[OpenClaw April 2026: 6 Model Providers — MindStudio](https://www.mindstudio.ai/blog/openclaw-april-2026-model-agnostic-provider-manifest)

---

## 3. 社群反應與討論亮點

- **Issue #84386 `impact:message-loss`**：今日最受關注的新 issue，標籤標示為訊息遺失等級影響，尚待官方確認是否可重現。社群等待後續 triage 結果。
- **Codex fail-close 修復**（#83349）受到開發者正面評價，認為「靜默 fallback 是隱性 bug 的溫床」，此修復使工作流更具可追蹤性。
- **Plugin SDK 方向**：`openclaw plugins` CLI 工具的加入被視為生態系成熟的訊號，部分開發者開始討論將既有 bash 腳本遷移為正式 plugin 的可行性。
- 安全修補（Docker port binding、Copilot session replay item）獲社群注意，部分自架用戶更新後反映 Gateway 穩定性有所改善。
- 來源：[Issues · openclaw/openclaw](https://github.com/openclaw/openclaw/issues)、[Releases · openclaw/openclaw](https://github.com/openclaw/openclaw/releases)

---

## 4. 值得追蹤的後續議題

| 議題 | 說明 | 追蹤優先度 |
|------|------|-----------|
| Issue #84386 `impact:message-loss` | 訊息遺失等級 bug，今日建立，待官方 triage | 高 |
| Issue #83474 Codex 動態工具卡住 | 仍開放，影響 Codex 工作流可靠性 | 高 |
| v2026.5.19-beta.1 正式化 | 觀察預發布版何時升為穩定版 | 中 |
| Provider plugin 分離架構 | Bedrock/Vertex 等已移出 core，觀察後續是否有 breaking change | 中 |
| GPT-5.5 128K context 限制 | 社群是否提出分段或 streaming 解法 | 中 |
| Claude Opus 4.7 Vertex 憑證流程穩定性 | 修復後的實戰驗證回報 | 低 |

---

*本報告依搜尋結果如實撰寫，未收錄的功能或數據不予推測。*
