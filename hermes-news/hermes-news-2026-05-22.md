# Hermes-Agent 每日情報 — 2026-05-22

> 資料來源截止時間：2026-05-22 UTC｜搜尋範圍：過去 24 小時及近期重要更新

---

## 1. 今日重點摘要

1. **v0.14.0「The Foundation Release」為目前最新版本**（2026-05-16 發布），共 808 commits、633 merged PRs、1,393 個檔案變更，215 位社群貢獻者參與，關閉 545 個 issues（含 12 個 P0、50 個 P1）。
2. **PyPI 版本延遲警告**：截至 5 月 18 日，PyPI 仍提供 v0.13.0；需使用 git installer 安裝 v0.14.0，官方建議指令為 `hermes update`。
3. **xAI Grok 正式加入**：以 SuperGrok OAuth provider 整合，`grok-4.3` 提升至 **1M context window**；X Premium 訂閱者可直接透過 Hermes 使用，無需另外設定 API key。
4. **OpenAI-compatible 本地代理**：新功能將任何 OAuth 授權的 Hermes provider（Claude Pro、ChatGPT Pro、SuperGrok）轉為本地端點，Codex / Aider / Cline / Continue 等工具可直接接入，實現訂閱帳號統一管理。
5. **Computer Use 解鎖多 provider**：computer-use（agent 控制滑鼠鍵盤驅動 GUI）原先鎖定 Anthropic SDK，新 `cua-driver` 後端支援任何視覺能力模型，並加入 focus-safe 操作與自動更新機制。
6. **訊息平台擴展至 22 個**：本次新增 LINE 與 SimpleX Chat，合計支援 22 個訊息平台；`/handoff` 指令支援即時 session 轉移（含完整訊息、工具呼叫與 context），不中斷當前任務。
7. **Skills Hub 接入 HuggingFace**：社群 skills index（`huggingface.co/skills`）預設整合進 Skills Hub，用戶無需額外設定即可瀏覽、安裝社群發布的新 skill。
8. **採用規模達歷史新高**：截至 5 月 10 日，Hermes 在 OpenRouter 全球每日排名第一（橫跨生產力、coding agent、personal agent、CLI agent 等類別），每日處理 **224 億 tokens**，領先 OpenClaw 的 186 億。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic OAuth / API Key）

- **驗證機制**：Hermes 優先使用 Claude Code 自有的 credential store（Anthropic OAuth），保持 refresh token 可更新；亦支援純 API Key 方式。
- **工具呼叫品質**：Claude 與 GPT-4.1 在 Hermes 的工具呼叫格式兼容性最高，複雜多步驟任務中 malformed tool call 重試率最低。
- **Computer Use**：新 `cua-driver` 解除 Anthropic SDK 綁定後，Claude 仍為 computer-use 工作流的首選（視覺能力與指令解析最穩定）。
- 來源：[AI Providers | Hermes Agent Docs](https://hermes-agent.nousresearch.com/docs/integrations/providers)、[Best Hermes Agent Model 2026](https://www.remoteopenclaw.com/blog/best-models-for-hermes-agent)

### xAI Grok

- **Grok 4.3**：SuperGrok OAuth 整合，1M context window，支援 X 即時搜尋（`x_search` 工具），適合需要即時社群資料的 agent 工作流。
- **Grok 4.1 Fast**：速度優先但推理深度不足；多工具呼叫的複雜任務建議改用 Grok 3 或 Claude。
- **踩坑**：Grok 工具呼叫格式對某些 Hermes skill 的相容性不如 Claude/GPT，複雜任務中偶有 malformed call 需重試。
- 來源：[Best Grok Models for Hermes Agent](https://www.remoteopenclaw.com/blog/best-grok-models-for-hermes)、[Grok Now Works Inside Hermes Agent](https://www.basenor.com/blogs/news/grok-now-works-inside-nousresearch-hermes-agent)

### 本地模型（LM Studio / Ollama / vLLM / SGLang）

- Hermes 支援任何 OpenAI-compatible 端點；v0.14.0 的 OpenAI-compatible local proxy 進一步讓本地模型可以用訂閱帳號授權透傳。
- 實測（Peter Falkingham 部落格，5 月 8 日）：LM Studio + Hermes 搭配使用，在 Raspberry Pi 3B / 32-bit ARM 上安裝仍有問題（Issue #8397，`faster-whisper` 無 armv7l wheel），建議 ARM 用戶改用 x86_64 環境或跳過語音相關套件。
- 來源：[Getting Local AI Working for Me](https://peterfalkingham.com/2026/05/08/getting-local-ai-working-for-me-lm-studio-opencode-and-hermes/)

### Token 用量異常（重要踩坑）

- **高 token 消耗問題**：Reddit 用戶回報「輕度使用 2 小時消耗 400 萬 tokens，僅問天氣就用掉 2.1 萬 tokens」。
- **根本原因**：在 `hermes-agent` 原始碼目錄啟動 gateway 時，會載入開發用 `AGENTS.md` 及其他測試設定，導致 context 暴漲。v0.14.0 修正為預設從使用者家目錄啟動。
- **建議**：確認 Hermes 啟動目錄為 `~`（非 clone 目錄），並定期檢查 SOUL.md 大小；CLI 下正常 input token 約 6–8K，Telegram 下約 15–20K。
- 來源：[Hermes Agent Troubleshooting Guide](https://www.getopenclaw.ai/blog/hermes-agent-troubleshooting)

### 供應鏈安全（新增）

- v0.14.0 對所有 dependency 採用 **exact version pinning**，直接回應 2026 年 5 月 12 日的 `mistralai` 套件蠕蟲事件（惡意 payload 透過浮動版本注入）。
- 升級 v0.14.0 前建議清空 venv 重新安裝，避免繼承受污染的舊版依賴。

---

## 3. 社群反應與討論亮點

- **NVIDIA Blog 官方背書**：NVIDIA 發布文章《Hermes Unlocks Self-Improving AI Agents, Powered by NVIDIA RTX PCs and DGX Spark》，深度介紹 Hermes 在 RTX AI Garage 的整合方式，代表企業端關注度顯著提升。（[NVIDIA Blog](https://blogs.nvidia.com/blog/rtx-ai-garage-hermes-agent-dgx-spark/)）
- **X.com 官方公告**：Nous Research 官方帳號推文宣布 v0.14.0，Teknium（共同創辦人）親自列出重點功能，獲 **197,500 次**觀看，社群留言「This changes everything.」（[@NousResearch](https://x.com/NousResearch/status/2056110234309939330)）
- **成長數字引發討論**：36kr 英文版報導《Hermes Agent Hits 47,000 Stars in Two Months》，討論其是否會複製 OpenClaw 的敘事，還是建立全新定位。（[36kr EN](https://eu.36kr.com/en/p/3760771429958403)）
- **Discord Issue #12496（已知 bug）**：Discord forum channel 中新建論壇貼文時，Hermes 不會回應第一則訊息，除非先 @mention 機器人；後續訊息在同一 thread 中正常。此問題在社群中被頻繁討論，修復預計在下一個版本。
- **OpenClaw 本地代理的意外整合**：部分 OpenClaw 使用者開始透過 Hermes 的 OpenAI-compatible local proxy 接入 Claude Pro，繞過 Anthropic 取消 OpenClaw OAuth 的限制，引發兩個社群的交叉討論。

---

## 4. 值得追蹤的後續議題

| 議題 | 原因 |
|------|------|
| **PyPI v0.14.0 正式上線** | 截至 5 月 18 日仍為 v0.13.0，影響 `pip install` 用戶的版本一致性 |
| **Discord Issue #12496 修復進度** | Forum thread 首則訊息無回應的 bug，影響以 Discord 作為主要介面的用戶 |
| **v0.15.0 功能預告** | 目前社群討論中可見 native Windows 支援（v0.14.0 早期 beta）的後續完善，以及 LSP diagnostics 擴展計畫 |
| **mistralai 套件蠕蟲事件後續** | 5 月 12 日供應鏈攻擊事件，Hermes 已防禦，但生態系其他工具的曝險範圍仍在評估中 |
| **Kanban 多 agent 板穩定性** | v0.13.0 引入的 heartbeat/zombie detection 機制，長時間大型任務的可靠性需更多實測驗證 |
| **224B token/day OpenRouter 排名** | 是否維持 #1 地位，以及 OpenRouter 如何管理如此高流量的 cost allocation，值得觀察 |

---

*報告生成時間：2026-05-22 UTC*
