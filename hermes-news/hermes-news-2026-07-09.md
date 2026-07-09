# Hermes-Agent 每日情報 — 2026-07-09

## 今日重點摘要

1. **v0.18.0「The Judgment Release」於 7 月 1 日正式釋出**：此版本的核心目標是清零所有 P0 和 P1 issue，共計約 700 個最高優先問題全數關閉，整體包含 ~1,720 commits、998 merged PR、2,215 個檔案變更，超過 370 位社群貢獻者參與。來源：[GitHub Release](https://github.com/NousResearch/hermes-agent/releases/tag/v2026.7.1)

2. **Mixture-of-Agents 成為一等公民**：可將多個模型組合成具名 ensemble（如同選擇單一模型般操作），每個參考模型的推理過程完整顯示，aggregator 的答案即時串流。這是 v0.18.0 最受矚目的新功能。

3. **Agent 驗證機制上線（Verification Evidence）**：Hermes 現在會記錄 coding 工作的驗證證據，並可實際執行專案的 check 後再宣告完成，不再只是「斷言成功」，顯著提升自動化可信度。來源：[Medium — KD Agentic](https://medium.com/kd-agentic/hermes-v0-18-ai-agents-now-verify-not-just-claim-cb542dd0e33b)

4. **新指令 `/learn` — 動態技能學習**：執行 `/learn <任何描述>` 即可讓 Hermes 從指定來源提煉出可重複使用的 skill，讓使用者能自訂 agent 能力。

5. **Desktop App 新增 Projects 功能**：原生 Electron 桌面 App 獲得完整 Project 管理，含程式碼庫側欄、coding rail、review pane、git worktree 管理，以及面向 agent 的 project 工具。

6. **Gateway Scale-to-Zero 與 Drain 協調**：Gateway 可在閒置時自動休眠，並在重啟或自動更新前乾淨地 quiesce（清空），不中斷進行中的對話，適合團隊或 SaaS 部署情境。

7. **`/journey` 指令與記憶圖譜**：新的 `/journey` 指令呈現可互動的記憶與技能累積時間軸，桌面 App 同時附有記憶關係圖，讓使用者直觀了解 agent 的成長歷程。

8. **Background Fan-Out（平行子 agent 執行）**：多個 subagent 可在背景同步執行，主要對話繼續進行，互不阻塞，大幅提升複雜任務的效率。

9. **P0/P1 清零後的品質維護承諾**：官方宣示以 v0.18.0 為基準，持續維持零 P0/P1 open issue 的狀態，並以此作為後續版本的品質指標。

---

## LLM 串接實戰回報

### DeepSeek + OpenRouter 踩坑

- **DeepSeek V4 Pro 透過 OpenRouter 觸發 Gateway 崩潰迴圈**：Issue [#16677](https://github.com/NousResearch/hermes-agent/issues/16677) 記錄了以 `deepseek/deepseek-v4-pro` 為預設模型時，gateway 陷入崩潰迴圈，連帶造成 Telegram bot 完全無回應的問題。根本原因是 OpenRouter 上游 "Io Net" provider 對此模型設有極嚴格的 rate limit。來源：[GitHub Issue #16677](https://github.com/NousResearch/hermes-agent/issues/16677)

- **解決方案：BYOK（Bring Your Own Key）**：在 OpenRouter 設定頁面加入個人 DeepSeek API Key，可繞開共享池的 rate limit 限制，改用個人配額。同時，OpenRouter 具備跨 provider 自動負載均衡機制，在 provider 回傳 5xx 或 rate limit 時自動切換。來源：[AIFLOXIUM 深夜 agent 指南](https://www.aifloxium.online/blog/hermes-agent-deepseek-v4-openrouter-overnight-ai-setup)

### Claude / Anthropic 整合

- **Anthropic 為 auxiliary LLM 的備用選項**：Hermes Agent 的視覺、web summarization、memory 等輔助功能預設使用 Gemini Flash（自動偵測），但可替換為 Claude 或其他模型。主 LLM 可透過 Copilot API、Nous Portal 或直接 API key 指定 Claude 模型。

- **Context Window 超限處理**：官方 FAQ 指出，對話超過模型 context window 時可選擇壓縮目前 session、開新 session，或改用 context window 更大的模型，但並無自動 token 截斷機制，需要使用者主動介入。來源：[Hermes Agent FAQ](https://hermes-agent.nousresearch.com/docs/reference/faq)

### Grok / xAI 整合

- **Cursor Composer 透過 xAI Grok 訂閱可用**：v0.17.0 起支援透過 xAI Grok 訂閱存取 Cursor Composer 模型，為有 xAI 訂閱的使用者提供額外選項。

### Mixture-of-Agents 實戰

- v0.18.0 引入的 Mixture-of-Agents 讓使用者可將 Claude、GPT、Hermes 系列等模型組合為 ensemble，社群對此功能在 tool-use 任務中的表現持觀望態度，尚待累積實測回饋。

---

## 社群反應與討論亮點

- **Teknium 在 X.com 的發布公告獲廣泛轉發**：Nous Research 核心成員 Teknium 在 X（原 Twitter）上發布 v0.18.0 的詳細說明，引發技術社群熱烈討論。來源：[X.com @Teknium](https://x.com/Teknium/status/2072416088785359189)

- **官方 Nous Research Discord 是最即時的討論場所**：社群在 agent 頻道最為活躍，包含 bug 回報、config 分享與模型選擇辯論，速度與深度均優於 Reddit。

- **Reddit 社群三大辯論主題**（r/LocalLLaMA 與 r/selfhosted）：
  1. Hermes 系列模型 vs Claude/GPT 的 tool-use 可靠性
  2. 自架成本與 Nous Portal 訂閱的 CP 值比較
  3. 本地 LLM（如 DeepSeek 自建）與雲端 API 在 overnight agent 任務的穩定性對比
  多數討論結論為「Hermes 系列模型搭配 Hermes Agent 在 tool-use 表現最佳」。來源：[社群討論彙整](https://openclawlaunch.com/blog/hermes-agent-reddit-discussion)

- **Discord 整合討論**：GitHub 上的 Discord scrape 資料（2026-06-15，含 1,175 threads、8,030 訊息）顯示，社群對 Discord bot 設定的討論量持續增加，尤其是 gateway restart 後 bot 失聯的問題。來源：[Hermes Discord 設定文章](https://hermes-agent.ai/blog/hermes-agent-discord-setup)

- **「Zero-Touch Agents」評論**：技術媒體對 v0.18.0 的驗證機制與 background fan-out 組合給予正面評價，稱其為「真正接近無人值守的 AI agent」里程碑。來源：[Neura AI Blog](https://blog.meetneura.ai/claude-code-v2-1-198-and-hermes-agent-what-zero-touch-agents-mean-for-real-work/)

---

## 值得追蹤的後續議題

1. **Mixture-of-Agents 實戰評測**：v0.18.0 的核心新功能，目前社群尚未有大量對比測試結果，後續在 coding、research、tool-use 等不同任務類型的表現比較值得持續追蹤。

2. **P0/P1 維持承諾的可行性**：官方宣示維持零高優先 issue，但隨著功能快速疊加，能否長期維持此品質目標是觀察重點。

3. **DeepSeek V4 Pro + OpenRouter 穩定性**（Issue #16677）：此問題已有 workaround（BYOK），但是否會在 Hermes 端有原生 fallback 機制仍待確認。

4. **iMessage via Photon 與 Raft Agent Network**：v0.17.0 引入的兩個新整合管道，目前使用者實測回饋尚少，未來穩定性與實用性值得觀察。

5. **`/learn` 技能學習的品質與可靠性**：動態學習是高度實用的功能，但使用者回報所提煉技能的準確度如何仍需社群測試資料支撐。

6. **v0.19.0 時程**：根據 Hermes Agent 每隔 2-3 週的發布節奏，下一個版本預計 7 月下旬釋出，可留意官方公告。

---

*資料來源*：
- [GitHub — Hermes Agent v0.18.0 Release](https://github.com/NousResearch/hermes-agent/releases/tag/v2026.7.1)
- [GitHub — Hermes Agent Releases](https://github.com/NousResearch/hermes-agent/releases)
- [Releasebot — Hermes Agent July 2026](https://releasebot.io/updates/nousresearch/hermes-agent)
- [X.com @Teknium — v0.18.0 公告](https://x.com/Teknium/status/2072416088785359189)
- [Medium — KD Agentic v0.18 評論](https://medium.com/kd-agentic/hermes-v0-18-ai-agents-now-verify-not-just-claim-cb542dd0e33b)
- [Neura AI Blog — Zero-Touch Agents 評論](https://blog.meetneura.ai/claude-code-v2-1-198-and-hermes-agent-what-zero-touch-agents-mean-for-real-work/)
- [GitHub Issue #16677 — DeepSeek OpenRouter 崩潰](https://github.com/NousResearch/hermes-agent/issues/16677)
- [AIFLOXIUM — DeepSeek + OpenRouter 過夜 agent 指南](https://www.aifloxium.online/blog/hermes-agent-deepseek-v4-openrouter-overnight-ai-setup)
- [Hermes Agent FAQ](https://hermes-agent.nousresearch.com/docs/reference/faq)
- [社群討論彙整 — Reddit & Discord](https://openclawlaunch.com/blog/hermes-agent-reddit-discussion)
