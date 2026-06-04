# Hermes-Agent 每日情報｜2026-06-04

> 資料涵蓋範圍：2026-06-03 至 2026-06-04（UTC）

---

## 1. 今日重點摘要

1. **v0.15.1 為目前最新穩定版**（2026-05-29）：v0.15.0（Velocity Release，5/28）發布後次日即推出同日 hotfix，修復 loopback 模式（Docker、hosted Hermes、全新安裝）下的 dashboard 無限重載迴圈，共含 28 commits、21 merged PR。
2. **v0.15.0 架構大重構**：核心 `run_agent.py` 從 16,083 行縮減至 3,821 行（-76%），拆分為 14 個內聚的 `agent/*` 模組，session_search 效能提升 4,500 倍且改為免費功能，每次對話函數呼叫次數減少 47%，啟動時間再縮短 1 秒。
3. **Kanban 演進為多 Agent 平台**：v0.15 中 Kanban 新增 orchestrator 自動分解、swarm 拓撲、排程任務、每任務模型覆寫，以及心跳偵測、殭屍 Agent 回收、任務重試、幻覺恢復等可靠性機制。
4. **GitHub 近期 Issue 群（6 月 3 日）**：Terminal UI 元件、外掛系統、gateway runner 及 cron 排程器各有新 bug 報告開啟，顯示 v0.15 重構後有若干邊緣案例仍需修補，社群回應積極。
5. **hermes-agent-desktop 生態擴展**：社群開發者 Felix-Forever 發布桌面版客戶端，20 名 AI 專家自動協作，內建視覺化 Skill Store 與 PM 協調器，支援 Kimi K2.5／Qwen／DeepSeek／GPT-4o，建基於 Hermes Agent 核心。([GitHub](https://github.com/Felix-Forever/hermes-agent-desktop))
6. **NVIDIA NIM 40 RPM 限制問題持續延燒**：大量開發者在 NVIDIA Developer Forums 提交請求，要求將免費 NIM API 速率上限從 40 RPM 提升至 200 RPM，複雜 Agent 任務單輪往往需要 20–50 次 API 呼叫，現有限制導致頻繁 429 錯誤。
7. **多語系支援**（v0.13 起）：Gateway 與 CLI 靜態訊息已翻譯至中文、日文、德語、西班牙語、法語、烏克蘭語、土耳其語共 7 種語言，降低國際用戶入門門檻。
8. **過去 24 小時無重大新版本發布**：截至 2026-06-04，最新版本仍為 v0.15.1，尚未見 v0.16 的 pre-release 或相關 PR 合併，下版本動態待觀察。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）

- **首選模型**：Claude Sonnet 4.6 在社群評測中位居推理品質與工具呼叫穩定性第一，特別適合需要多步推理的 Agent 工作流。([remoteopenclaw.com](https://www.remoteopenclaw.com/blog/best-models-for-hermes-agent))
- **串接設定**：官方文件提供 Anthropic provider 的完整配置，透過 Nous Portal 統一訂閱可存取 Claude、GPT、Gemini 等 300+ 模型，簡化多 provider 切換流程。([hermes-agent docs](https://hermes-agent.nousresearch.com/docs/integrations/providers))
- **Anthropic API 費用踩坑**：部分使用者反映 Hermes Agent 在複雜任務中的 token 消耗遠高於預期（一次 Kanban 任務可消耗數千 token），建議針對 Claude 啟用 prompt caching 以降低成本。

### DeepSeek V4

- **最佳性價比選擇**：DeepSeek V4 提供 90% 快取折扣，社群評定為「成本效益最佳」，有用戶記錄以 $0 費用取代原本每月 $200 的 AI 訂閱費用。([Medium, Emmanuel Mark Ndaliro](https://medium.com/@kram254/stop-paying-for-claude-openai-api-how-i-spent-0-with-hermes-agent-free-deepseek-v4-to-replace-d2a9e97beaff))
- **Nous Portal 免費層變動**：DeepSeek V4 Flash 原本可透過 Nous Portal 免費存取，但近期據報已不再對一般用戶開放，需確認當前方案。([iceberglakehouse.com](https://iceberglakehouse.com/posts/2026-05-hermes-agent-free-deepseek-setup/))
- **NVIDIA NIM + DeepSeek 速率限制**：多名開發者在 NVIDIA Developer Forums 回報，使用 NVIDIA NIM 搭配 DeepSeek V4 Pro 運行 Hermes Agent 時，預設 40 RPM 限制嚴重影響 Agent 工作流效率，正式請求升至 200 RPM。([NVIDIA Forums](https://forums.developer.nvidia.com/t/rate-limit-issue-can-i-get-an-upgrade-to-200-rpm-for-hermes-agent-testing/370383))

### GPT（OpenAI）

- GPT-4.1 被評為「可靠的中階選擇」，工具呼叫一致性良好；GPT-5.5 尚未在 Hermes 社群累積足夠評測數據。([remoteopenclaw.com](https://www.remoteopenclaw.com/blog/best-models-for-hermes-agent))
- 有用戶回報在 GPT 系列上使用 Hermes 的 Kanban 多 Agent 模式時，長任務中途模型上下文丟失，懷疑與 token 視窗限制有關。

### Ollama 本地模型

- 社群設定指南確認 Hermes Agent 可搭配 Ollama 離線運行，適合不希望傳送資料至外部 API 的隱私場景，但多 Agent Kanban 模式在本地模型下效果較差。([docs.bswen.com](https://docs.bswen.com/blog/2026-04-21-hermes-agent-model-configuration/))

---

## 3. 社群反應與討論亮點

- **「hardening the permission-approval bridge」**：開發者 aniruddhaadak 在 DEV Community 發文，記錄一個可能導致 Hermes Agent 崩潰的 bug——`request_permission` 函數回傳 `None` 時觸發 attribute error，並貢獻了防禦性修補（預設拒絕而非崩潰）。([DEV Community](https://dev.to/aniruddhaadak/building-with-hermes-agent-hardening-the-permission-approval-bridge-4gen))
- **NVIDIA Developer Forums 速率限制請願潮**：短時間內出現多篇格式相近的 40→200 RPM 申請帖，顯示 Hermes + NVIDIA NIM 組合在自動化與 trading bot 開發者中滲透率正在上升，但基礎設施瓶頸尚未解決。
- **v0.15.0 效能評測熱議**：「session_search 4,500 倍加速」成為社群轉發熱點；FintechExtra、techsy.io 等科技媒體陸續跟進報導此版本的架構重構意義，認為此舉有助於降低二次開發門檻。([FintechExtra](https://www.fintechextra.com/news/hermes-agent-v0150-major-speed-boost-and-code-overhaul-587))
- **Hermes-agent-desktop 社群關注度上升**：支援 Kimi K2.5 的桌面端客戶端吸引追求圖形介面使用者關注，被視為 Hermes 生態系走向主流用戶的訊號。([GitHub](https://github.com/Felix-Forever/hermes-agent-desktop))

---

## 4. 值得追蹤的後續議題

- **v0.16 發布時程**：v0.15.1 後尚無 pre-release 訊號，下一版本功能方向（預計延續 Kanban 強化與效能優化）值得持續追蹤 [GitHub Releases](https://github.com/NousResearch/hermes-agent/releases)。
- **v0.15 重構後的 bug 修補進度**：6 月 3 日新開的 Terminal UI、外掛系統、gateway、cron 等 issue 是否在下一個 patch 版本中被解決，影響生產穩定性。
- **NVIDIA NIM 速率限制政策**：多名開發者的 40→200 RPM 申請是否獲批，以及 NVIDIA 是否會調整免費層限制，直接影響低成本 Hermes Agent 部署的可行性。
- **DeepSeek V4 Flash 免費層動向**：Nous Portal 是否會恢復 DeepSeek V4 Flash 免費存取，或是否有替代方案，對希望以 $0 成本運行 Agent 的用戶至關重要。
- **hermes-agent-desktop 穩定性**：仍為早期社群項目，功能與穩定性尚待驗證，值得追蹤正式發布進度。

---

*本報告依據公開搜尋結果彙整，不代表任何官方立場。如有時效性限制，請以原始來源為準。*
