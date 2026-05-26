# Hermes-Agent 每日情報 — 2026-05-26

> 資料來源：GitHub Releases、GitHub Issues、NousResearch 官方文件、MindStudio Blog、社群論壇  
> 搜尋範圍：過去 24 小時（UTC 2026-05-25 ～ 2026-05-26）

---

## 一、今日重點摘要

1. **v0.14.0（Foundation Release，2026-05-16）為目前最新穩定版**：過去 24 小時無新版本發佈，但 GitHub Issues 頁面顯示截至 2026-05-25 仍有多項 P1 bug 活躍中，涉及 agent core、gateway 及 OpenAI provider。

2. **v0.14.0 規模龐大**：808 commits、633 merged PRs、1393 files changed、165,061 行新增、545 issues 已關閉（含 12 個 P0、50 個 P1），是近期最大規模的單次發佈。

3. **xAI Grok 以 SuperGrok OAuth 方式正式進駐**：grok-4.3 上限擴展至 1M context window；SuperGrok 訂閱用戶可直接以 xAI 帳號登入，無需 API 金鑰、無需獨立計費。

4. **新 OpenAI-compatible 本地代理（local proxy）**：可將任何 OAuth 認證的 Hermes provider（Claude Pro、ChatGPT Pro、SuperGrok）轉換為 OpenAI 相容端點，讓 Codex、Aider、Cline、Continue 等工具直接調用，無需額外 API 金鑰管理。

5. **Computer-use 支援擴及非 Anthropic 模型**：新的 `cua-driver` 後端加入 focus-safe 操作，任何具備視覺能力的模型均可驅動桌面，打破原先僅限 Claude 的限制。

6. **Microsoft Teams 整合完成端對端串接**：Graph auth + webhook listener + pipeline runtime + outbound delivery 全數到位，成為繼 Slack、Discord、Telegram 之後的完整頻道支援。

7. **安裝大幅輕量化**：Slack / Matrix / Feishu / DingTalk adapter、hindsight client、codex app-server、Pixverse / image-gen SDK、voice/TTS provider 等現在改為「首次使用時自動安裝」，冷啟動時間縮短約 19 秒。

8. **9 項新可選技能（optional skills）**：包含 Hyperliquid 交易、Yahoo Finance、API 測試、OSINT 調查工具等，擴展 agent 工作流應用場景。

9. **v0.13.0（Tenacity Release，2026-05-07）Kanban 多代理看板正式推出**：具備心跳監控、任務回收、殭屍偵測、自動封鎖不完整退出、per-task 重試及幻覺復原機制；`/goal` 指令可讓 agent 跨輪次鎖定目標。

---

## 二、LLM 串接實戰回報

### Claude（Anthropic）

- **Nous Portal 推薦路徑**：單一 OAuth 登入涵蓋 300+ 前沿 agentic 模型（含 Claude、GPT、Gemini、DeepSeek、Qwen、Kimi、GLM、MiniMax、Grok），無需個別管理 API 金鑰。
- **HERMES.md bug 導致 Claude Max 用戶超額收費**：一篇 MindStudio 部落格文章揭露，HERMES.md 的某個 bug 讓 Claude Max 訂閱用戶額外被收取約 200 美元，引發社群對訂閱制計費透明度的廣泛討論。
  - 來源：[MindStudio Blog](https://www.mindstudio.ai/blog/hermes-md-bug-claude-max-billing-subscription-pricing)
- **CLI vs 訊息閘道 token 消耗落差**：CLI 每次請求約 6-8k input token overhead，Telegram / Discord 閘道則達 15-20k input token（多 2-3 倍）。根本原因為閘道在 hermes-agent 目錄啟動時載入了 AGENTS.md 等開發用「垃圾資料」；解法為讓 Hermes 改從用戶 home directory 啟動。
  - 來源：[Hermes-Agent Token Overhead Blog](https://hermes-agent.ai/blog/hermes-agent-token-overhead)

### Grok（xAI）

- v0.14.0 正式整合 SuperGrok OAuth，grok-4.3 支援 1M context window。
- 有社群文章比較 Hermes-Agent 中各 Grok 模型的效能表現，提供最佳模型選擇建議。
  - 來源：[Remote OpenClaw Blog](https://www.remoteopenclaw.com/blog/best-grok-models-for-hermes)

### 速率限制（Rate Limit）踩坑

- **Hermes 僅在收到 429 後才反應**：目前只在收到 HTTP 429 後才觸發 retry/failover，provider 回傳的 `X-RateLimit-Remaining` 與 `X-RateLimit-Reset` response headers（Anthropic、OpenAI、OpenRouter 均有提供）完全被忽略，導致代理陷入昂貴的重試/切換循環。
  - Issue #7489：提案改為基於 RPM 的預先節流（pre-emptive throttling）。
  - Issue #5449：追蹤速率限制 header 以主動節流。
  - 來源：[GitHub Issue #7489](https://github.com/NousResearch/hermes-agent/issues/7489) ｜ [GitHub Issue #5449](https://github.com/NousResearch/hermes-agent/issues/5449)

### Token 上限踩坑

- **model.max_tokens 設定無效（Issue #4404）**：`config.yaml` 中的 `model.max_tokens` 設定從未實際傳遞給 AIAgent，導致用戶以為有設上限但實際未生效。
  - 來源：[GitHub Issue #4404](https://github.com/NousResearch/hermes-agent/issues/4404)
- **單一複雜任務可消耗 50-100k tokens**：若在無人監督的自主模式（no approval gates）下運行，失控任務可能造成 API 費用暴增。

### 成本與效能比較

- 有使用者以每月 $8 的設定搭配特定模型，規避 Claude 速率限制的方案。
  - 來源：[Moe Lueker Blog](https://moelueker.com/blog/hermes-agent-setup-8-dollars-month-no-claude-rate-limits)
- Claude Code vs Hermes-Agent 成本與 token 使用會計比較已有專題分析。
  - 來源：[Ken Huang Substack](https://kenhuangus.substack.com/p/chapter-1-cost-and-token-usage-accounting)

---

## 三、社群反應與討論亮點

- **Discord forum channel 首則訊息被忽略（Issue #12496）**：在 Discord 討論區頻道新開一個 forum post 後，Hermes 不會回應第一則訊息，除非有人明確 @mention 機器人。一旦 Hermes 在該 thread 回應過一次，後續訊息即可正常觸發。此問題在社群中已有多人回報，等待修復。
  - 來源：[GitHub Issue #12496](https://github.com/NousResearch/hermes-agent/issues/12496)

- **Feature Request：多代理 Discord 頻道協作（Issue #14853）**：提案讓多個 Hermes 實例在同一 Discord 頻道協作，包含 history injection 與 cascade prevention 機制，避免代理間互相觸發無限循環。
  - 來源：[GitHub Issue #14853](https://github.com/NousResearch/hermes-agent/issues/14853)

- **Reddit 用戶：2 小時輕度使用消耗 400 萬 token**：有用戶回報「僅是詢問天氣就用了 21k token」，社群針對預設 SOUL.md 與 gateway 啟動路徑的 token 浪費問題展開討論，最終揪出 hermes-agent 目錄啟動時載入過多上下文為根因。

- **從 OpenClaw 流入用戶**：OpenClaw 4 月更新引發 gateway 崩潰後，部分社群成員明確表示轉以 Hermes-Agent 為主力，認為其更新穩定度與 bug 修復速度更令人滿意。

- **v0.14.0 發佈摘要文章（clawsimple.com，2026-05-中旬）**：「Kanban、Curator、Grok OAuth 以及大幅輕量化的安裝包」獲得廣泛轉貼，被社群視為里程碑版本。
  - 來源：[ClawSimple Blog](https://clawsimple.com/en/blog/hermes-agent-updates-mid-may-2026)

---

## 四、值得追蹤的後續議題

1. **v0.14.0 後的 P1 bug 修復進度**：截至 2026-05-25 仍有多項 P1 issues 活躍，涉及 agent core、gateway、OpenAI provider，下一版本（推測 v0.15.0）修復優先級。
2. **Issue #12496 — Discord forum 首則訊息被忽略**：影響 Discord 整合用戶體驗，修復方案是否納入 patch release。
3. **Issue #7489 — RPM 預先節流**：若實作，將大幅改善高頻使用者的費用控制與穩定性。
4. **Issue #4404 — max_tokens 設定無效**：已知 bug，何時修復值得追蹤。
5. **HERMES.md 超額計費 bug 的後續說明**：NousResearch 是否發佈官方聲明或補償方案。
6. **SuperGrok / grok-4.3 1M context 實際效能回報**：社群實測數據尚不充分，真實工作流中的穩定性有待觀察。
7. **本地代理（OpenAI-compatible local proxy）的擴散**：作為 Aider / Cline 等工具的後端使用的社群反饋與踩坑紀錄。
8. **v0.15.0 發佈時程**：依照近期每 2-3 週一版的節奏，預計 2026 年 6 月初發佈。
