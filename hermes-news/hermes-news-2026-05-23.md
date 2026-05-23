# Hermes-Agent 每日情報 — 2026-05-23

> 搜尋範圍：過去 24 小時（UTC 基準）｜資料截止：2026-05-23

---

## 1. 今日重點摘要

1. **今日無重大新版本或緊急修補**：最新穩定版為 v0.14.0（2026-05-16，The Foundation Release），過去 24 小時內未見新版本發布，但多則 GitHub issue 仍處於活躍討論中。
2. **v0.14.0 核心亮點：xAI Grok 成為 SuperGrok OAuth 提供商**：grok-4.3 上下文視窗擴至 100 萬 token，並支援 OAuth 授權流程，不需另行申請 API Key。（[發行說明](https://github.com/NousResearch/hermes-agent/blob/main/RELEASE_v0.14.0.md)）
3. **本地 OpenAI-Compatible Proxy 上線**：任何已通過 OAuth 授權的 Hermes provider（Claude Pro、ChatGPT Pro、SuperGrok）均可透過此 proxy 轉為本地端點，讓 Codex、Aider、Cline、Continue 等工具直接對接，無需額外 API 費用。
4. **Computer-use 支援非 Anthropic 模型**：新 cua-driver 後端讓任何具備視覺能力的模型皆可驅動桌面自動化，不再限於 Claude。
5. **x_search 成為一等公民搜尋工具**：支援 OAuth 或 API Key 兩種授權方式，可直接在 agent 工作流中查詢 X（Twitter）即時內容。
6. **Microsoft Teams 整合完整落地**：Graph auth、webhook 監聽、pipeline runtime、訊息輸出全部串通，企業用戶可將 Hermes 接入 Teams 工作流程。
7. **Zed 編輯器一鍵安裝**：Hermes 正式列入 Zed 的 Agent Client Protocol registry，Zed 使用者可透過介面直接安裝，無需手動設定。
8. **供應鏈安全政策強化**：繼 Mini Shai-Hulud 蠕蟲攻擊事件後，所有依賴套件強制設定上限版本，防止惡意版本自動更新。（[Issue 討論](https://github.com/NousResearch/hermes-agent/issues)）
9. **v0.13.0 穩定性問題（Issue #22315）仍受關注**：The Tenacity Release 發布後有使用者回報不穩定，部分問題已在 v0.14.0 修正，但仍有未關閉的反饋。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）

- **Claude Sonnet 4.6 是工具呼叫首選**：定價 $3/$15（每百萬 input/output tokens），在結構化 function call 的可靠性上表現最佳，社群推薦作為 Hermes 的主力推理模型。（[來源](https://www.remoteopenclaw.com/blog/best-models-for-hermes-agent)）
- **Provider-aware 上下文限制**：claude-opus-4.6 在 Anthropic 直連為 100 萬 token，但透過 GitHub Copilot 中繼僅有 128K，Hermes 會自動偵測並調整。
- **Fallback 機制**：Hermes 支援 credential pool，遇到 401 自動輪換憑證，遇到 429 rate limit 則延遲觸發 fallback（避免過度切換）。（[Fallback 文件](https://hermes-agent.nousresearch.com/docs/user-guide/features/fallback-providers)）

### Grok（xAI）

- **Grok 4.1 Fast 是性價比之選**：200 萬 token 上下文視窗 + 自動 prompt caching，定價 $0.20/百萬 input tokens，適合需要長對話歷史或同時載入大量 skills 的場景。
- **Grok provider 相容性 Issue 開啟中**（guidryheal-create，2026-05-22）：部分使用者在切換至 grok-4.3 時遇到設定問題，細節待官方回應。（[Issues](https://github.com/NousResearch/hermes-agent/issues)）
- **SuperGrok OAuth 流程**：v0.14.0 新增，讓持有 SuperGrok 訂閱的使用者可免 API Key 直接授權，但自訂 endpoint 設定仍需手動指定。

### Gemini（Google）

- **Gemini 2.5 Pro 適合大文件分析**：定價 $1.25/$10，100 萬 token 上下文，適合整個程式碼庫或大量文件的綜合分析，每月成本比 Claude 省 50–60%。
- **工具呼叫可靠性仍低於 Claude/OpenAI**：截至 2026 年 4 月的測試數據顯示，Gemini 在 Hermes 工作流中的 function call 穩定性較差，複雜 agent 任務建議避免作為主力。（[來源](https://www.remoteopenclaw.com/blog/best-gemini-models-for-hermes)）

### DeepSeek / OpenRouter

- **OpenRouter 單 Key 存取 200+ 模型**：Hermes 原生支援，可搭配 credential pool 實現多模型自動路由。（[社群指南](https://openclawlaunch.com/guides/hermes-agent-openrouter)）
- **DeepSeek 適合批次低成本任務**：與 Claude 搭配分流使用，控制整體 API 費用。

---

## 3. 社群反應與討論亮點

- **v0.14.0 被視為里程碑版本**：808 commits、633 merged PRs、545 issues 關閉（含 12 個 P0），社群普遍給予正面評價，認為「Foundation Release」名實相符。（[Release](https://github.com/NousResearch/hermes-agent/releases/tag/v2026.5.16)）
- **OpenClaw 社群流入**：部分因 OpenClaw 4 月更新事故轉過來的使用者表示 Hermes 的更新穩定性明顯較好，但對 Hermes 的學習曲線有疑慮。
- **Plugin 規範引發討論**：Teknium 在 5 月宣告 plugin 禁止修改核心檔案（run_agent.py、cli.py 等），部分 plugin 開發者需重構既有專案，社群對此規範的邊界定義有不同看法。
- **v0.13.0 Kanban 多 agent 看板**：heartbeat、zombie detection、自動阻塞不完整任務等功能獲好評，被認為是 Hermes 在多 agent 協作上的重要一步。
- **7 語言 i18n 支援**：中文、日文、德文、西文、法文、烏克蘭文、土耳其文的 gateway 與 CLI 訊息翻譯，讓非英語使用者社群積極響應。

---

## 4. 值得追蹤的後續議題

| 議題 | 說明 | 連結 |
|------|------|------|
| Grok provider 相容性（guidryheal-create） | grok-4.3 / SuperGrok OAuth 設定問題是否在 v0.14.x patch 中修復 | [Issues](https://github.com/NousResearch/hermes-agent/issues) |
| CLI/Gateway 問題（Schonalf，2026-05-22） | 具體症狀與修復時程待官方回應 | [Issues](https://github.com/NousResearch/hermes-agent/issues) |
| Issue #22315 | v0.13.0 不穩定報告是否全數在 v0.14.0 解決 | [GitHub](https://github.com/NousResearch/hermes-agent/issues/22315) |
| cua-driver 擴展 | computer-use 後端開放給非 Anthropic 模型後，社群 plugin 生態是否跟進 | [RELEASE_v0.14.0.md](https://github.com/NousResearch/hermes-agent/blob/main/RELEASE_v0.14.0.md) |
| v0.15.0 規劃 | 下一版本 roadmap 尚未公布，關注 Kanban 功能擴充與更多 provider 支援 | [Releases](https://github.com/NousResearch/hermes-agent/releases) |
| Plugin 核心檔案規範 | 社群對禁止修改核心檔案的邊界定義是否有官方 FAQ 或更新 | [AGENTS.md](https://github.com/NousResearch/hermes-agent/blob/main/AGENTS.md) |
| HuggingFace Skills Hub | 社群 skills index 整合後，新 skill 品質與審核機制的建立進展 | [Hermes Docs](https://hermes-agent.nousresearch.com/docs) |

---

*本報告依搜尋結果整理，未加入推測或未經證實的資訊。*
*來源：[GitHub NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent) ｜ [RELEASE_v0.14.0.md](https://github.com/NousResearch/hermes-agent/blob/main/RELEASE_v0.14.0.md) ｜ [Hermes Docs](https://hermes-agent.nousresearch.com/docs) ｜ [remoteopenclaw.com](https://www.remoteopenclaw.com/blog/best-models-for-hermes-agent)*
