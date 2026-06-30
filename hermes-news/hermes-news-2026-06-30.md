# Hermes-Agent 每日情報 — 2026-06-30

> 資料來源截止時間：2026-06-30 UTC｜搜尋範圍：過去 24 小時

---

## 1. 今日重點摘要

1. **v0.17.0（v2026.6.19）為目前最新穩定版**：本月 19 日發布，累積約 1,475 commits、~800 個合併 PR、300+ 個已關閉 issues，由 245 位社群貢獻者共同完成，是 Hermes-Agent 迄今規模最大的單一版本。

2. **Hermes Desktop 正式 GA**：以 Electron 架構打造的原生桌面應用（macOS/Linux/Windows）正式納入主線，支援 streaming 聊天視窗、可歸檔 session 列表、拖放檔案、剪貼板圖片貼上、`Cmd+K` 命令面板、status bar 模型選擇器，並具備自動更新功能。

3. **兩個新 Channel：iMessage（via Photon）＆ Raft 代理人網路**：iMessage 透過 Photon 橋接（無需 QR code）；Raft 讓 Hermes 加入代理人對代理人通訊網路，擴大多 agent 協作能力。

4. **Desktop 側邊欄 session 丟失 bug（Issue #53368）**：更新後 desktop app 重建 binary，原本 ~40 個 session 縮減至 27 個，13 個 session 在 `state.db` 中仍存在，可透過 `hermes --resume <session_id>` 恢復，但 UI 未提供可見切換，社群反映強烈，此 issue 仍開放中。

5. **背景子代理人（background subagent）上線**：子代理人現可在背景執行，不阻斷主 agent 工作流，為長任務並行化提供原生支援。

6. **圖像生成新增編輯功能**：`/imagine` 工具升級，可在既有生成圖片基礎上進行局部編輯，而非每次重新生成全圖。

7. **Skills Hub 瀏覽器大改版＆記憶工具升級**：Skills Hub 介面全面重新設計，搜尋與安裝流程更直覺；memory tool 獲重大更新，改善長期記憶準確性與召回速度。

8. **Curator 成本優化**：Curator（策展員模組）不再對每次例行執行消耗輔助模型（aux-model）預算，大幅降低高頻使用情境的 API 費用。

9. **i18n 擴展：7 種語言支援**：Gateway 與 CLI 訊息新增繁體中文（Chinese）、日語、德語、西班牙語、法語、烏克蘭語、土耳其語本地化。

---

## 2. LLM 串接實戰回報

### 多 Provider 支援架構

Hermes-Agent 採取 provider-agnostic 設計，支援多種主流 LLM，統一透過 `hermes model` 指令切換：

- **OpenAI**：GPT-5.4、GPT-5-codex、GPT-4.1、GPT-4o 等，透過 Chat Completions 端點接入。
- **Anthropic（Claude）**：支援直接 API Key、Anthropic OAuth（`hermes auth add anthropic`）、OpenRouter 代理，以及任何相容 proxy。OAuth 路徑優先使用 Claude Code 自身憑證存儲，保持 token 可 refresh。
- **Google（Gemini）**：透過 `gemini` provider 原生 API、OpenRouter 或相容 proxy 接入。
- **Nous Portal**：一站式訂閱涵蓋 300+ 模型，免去分別申請五家 API Key，適合多 provider 混用場景。

來源：[AI Providers | Hermes Agent - nous research](https://hermes-agent.nousresearch.com/docs/integrations/providers)｜[Hermes Agent: The Practitioner's Reference (2026)](https://blakecrosley.com/guides/hermes)

### 已知 LLM 串接問題

#### 本地 LLM 壓縮 timeout（Bug #35517）
- **問題**：輔助客戶端（auxiliary client）HTTP timeout 預設 30 秒，對慢速本地 LLM（~40 tokens/sec）執行 compression summary 時，超時導致連線中斷。
- **影響**：Ollama、LM Studio 等本地部署用戶，使用慢速模型時 session 壓縮失敗。
- **現狀**：Issue #35517 仍開放，官方暫未提供 workaround，建議暫時改用較快本地模型或調高 timeout 設定（若版本允許手動覆寫）。

來源：[Bug: Auxiliary client HTTP timeout defaults to 30s · Issue #35517 · NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent/issues/35517)

#### Z.AI / GLM 整合認證失敗（Bug #4167）
- **問題**：以 z.ai（GLM）作為 LLM provider 時，每則訊息均回傳 `Access token missing required scope: inference:mint_agent_key` 錯誤。
- **影響**：z.ai 用戶完全無法透過 Hermes-Agent 使用 GLM 系列模型。
- **現狀**：Issue #4167 開放中，疑似 z.ai API scope 設計變更，或 Hermes 端未更新對應認證流程。

來源：[Bug: Z.AI / GLM integration fails · Issue #4167 · NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent/issues/4167)

### 社群工具選模共識

社群（尤其 Nous Research Discord agent 頻道）的討論結論呈現兩極：

- **Hermes 系列模型**：在 Hermes-Agent 工具呼叫場景表現最優，模型與框架深度整合，推薦作為預設選擇。
- **Claude / GPT**：當任務需要更強推理能力或跨 provider 穩定性時，社群傾向切換為 Claude 或 GPT，以原生 API 直連為主，減少 proxy 層帶來的延遲與 auth 失效風險。

來源：[Hermes Agent Reddit & Community Discussion — Where to Find Honest Feedback](https://openclawlaunch.com/blog/hermes-agent-reddit-discussion)

---

## 3. 社群反應與討論亮點

- **Desktop 側邊欄 bug 引發熱議**：Issue #53368 在 v0.17.0 發布後數日內獲得大量關注，多名用戶反映更新後遺失 session 入口，但確認資料未刪除（在 `state.db` 可查），社群普遍認為這是新版過濾機制的 regression，要求官方優先修復。（來源：[Issue #53368 · NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent/issues/53368)）

- **Cursor Composer 透過 xAI Grok 訂閱解鎖**：v0.17.0 新增可透過 xAI Grok 訂閱使用 Cursor 的 Composer 模型，開發者社群對此組合反應熱烈，認為擴大了 coding 場景的模型選擇空間。

- **WhatsApp Business API 原生支援**：取代原本需要 QR bridge 的非官方方案，直接透過 Business Cloud API 接入，企業用戶普遍認為這是 v0.17.0 最重要的改動之一。

- **Nous Research 官方 X 發文**：@NousResearch 在 X（Twitter）上發布 v0.17.0 完整 release notes 連結，並附上 Hermes Desktop 下載連結，引發大量轉推。（來源：[Nous Research on X](https://x.com/NousResearch/status/2063075373785428083)）

- **Discord 社群活躍度**：2026-06-15 的 Discord 快照顯示 1,175 個討論串、8,030 則訊息，agent 頻道是最活躍的技術交流場所。

---

## 4. 值得追蹤的後續議題

| 議題 | 說明 |
|------|------|
| **Desktop 側邊欄 session 過濾 bug（Issue #53368）** | 影響所有更新至 v0.17.0 的 Desktop 用戶，建議持續追蹤官方修補進度 |
| **本地 LLM timeout bug（Issue #35517）** | 慢速本地模型用戶的壓縮失敗問題，暫無官方 workaround |
| **Z.AI / GLM 認證問題（Issue #4167）** | 若使用 z.ai/GLM，需等待雙方確認 scope 問題來源 |
| **v0.18.0 功能預告** | v0.17.0 規模龐大，社群期待下一版本的多代理人協作功能進一步完善 |
| **Kanban 看板穩定性**（v0.13.0 引入） | 持久化多代理人看板（heartbeat、zombie detection）在大規模部署的實際穩定性，社群仍在累積使用報告 |
| **Nous Portal 訂閱模式成熟度** | 統一 API 訂閱涵蓋 300+ 模型，實際 rate limit 與品質對比直連 API 的差異，待更多用戶回報 |

---

*本報告依公開搜尋結果彙整，搜尋時間為 2026-06-30 UTC，資訊以各來源公開內容為準，不保證完整性。*
