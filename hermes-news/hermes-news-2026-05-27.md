# Hermes-Agent 每日情報｜2026-05-27

> 資料來源：NousResearch GitHub Releases、OpenRouter 排名資料、社群論壇、dev.to、Towards AI、CySecurity News（搜尋時間範圍：過去 24 小時及近期相關討論）

---

## 1. 今日重點摘要

1. **v0.14.0「Foundation Release」（5 月 16 日）仍是近期最重磅更新**：808 次 commit、633 個合入 PR、1,393 個檔案異動、165,061 行新增、545 個 issue 關閉（含 12 個 P0 嚴重問題），是迄今規模最大的單版本更新。（[Digg](https://digg.com/ai/1qruxedc)）
2. **Computer Use 跨 provider 突破**：`cua-driver` 後端現支援任何具備視覺能力的模型驅動桌面操作，不再綁定 Anthropic 獨家；搭配 OpenAI 相容本地代理，可讓 Claude Pro、ChatGPT Pro、SuperGrok 訂閱帳號直接作為 Codex / Aider / Cline 的 API 端點。（[GitHub RELEASE_v0.14.0.md](https://github.com/NousResearch/hermes-agent/blob/main/RELEASE_v0.14.0.md)）
3. **SuperGrok OAuth 登場，grok-4.3 升至 1M context**：xAI SuperGrok 訂閱者無需另外申請 API Key，直接以 xAI 帳號登入即可在 Hermes 內使用 grok-4.3，並享有 1M token 超長上下文視窗，適合整份程式碼庫或研究語料的單一提示分析。（[Basenor](https://www.basenor.com/blogs/news/grok-now-works-inside-nousresearch-hermes-agent)）
4. **X（Twitter）搜尋成為一等工具**：`x_search` 工具支援 OAuth 或 API Key 兩種認證方式，X Premium 訂閱者可直接在 Hermes 工作流中即時搜尋 X 貼文，無需離開 agent 環境。
5. **HuggingFace/skills 成為預設可信 tap**：社群技能庫現在無需額外設定即可安裝，大幅降低技能擴充門檻，預計帶動 Skill Hub 生態系快速成長。
6. **冷啟動提速 19 秒、Browser CDP 快 180 倍**：v0.14.0 顯著改善啟動效能，瀏覽器自動化任務延遲大幅降低，日常使用體驗明顯提升。（[Blake Crosley Guide](https://blakecrosley.com/guides/hermes)）
7. **超越 OpenClaw 登上 OpenRouter 日用量第一**：2026 年 5 月 10 日，Hermes-Agent 日 token 用量達 224B，超越 OpenClaw 的 186B，成為 OpenRouter 全球日排名第一的 agent 工具。（[MarkTechPost](https://www.marktechpost.com/2026/05/10/openclaw-vs-hermes-agent-why-nous-researchs-self-improving-agent-now-leads-openrouters-global-rankings/)）
8. **Microsoft Teams 原生支援、Windows 原生 Beta 上線**：Hermes 版圖從 Linux/macOS 正式延伸至 Windows 原生環境，企業用戶接受度預計提升。
9. **安全波關閉 8 個 P0 漏洞**：v0.13.0（5 月 7 日）啟動安全強化，資料遮蔽（redaction）預設開啟，Google Chat 成為第 20 個支援的訊息平台。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）
- **推薦配置**：Claude Sonnet 4.6 被社群評為整體品質最佳的 Hermes 搭配模型，工具呼叫行為最為穩定可預測。（[remoteopenclaw.com](https://www.remoteopenclaw.com/blog/best-models-for-hermes-agent)）
- **串接方式**：直接使用 Anthropic API Key，或透過 OpenRouter 路由；v0.14.0 後，Claude Pro 訂閱亦可透過新本地代理（OpenAI 相容端點）給第三方工具使用。
- **踩坑記錄（Token 暴增）**：一位 Reddit 使用者回報在 2 小時輕度使用中消耗 400 萬 token，僅詢問天氣便用掉 21,000 tokens。根因：gateway 在 hermes-agent 開發目錄啟動，讀入了不應被加載的開發期設定檔，導致 context 異常膨脹。修正方式：將啟動目錄改為使用者家目錄（`~/`）。（[Hermes Troubleshooting Guide](https://www.getopenclaw.ai/blog/hermes-agent-troubleshooting)）

### DeepSeek
- **預算部署首選**：DeepSeek V4 被評為最佳預算方案，適合 self-hosted 或對 API 成本敏感的場景，功能覆蓋面與成本比最高。v0.14.0 提供免費 DeepSeek V4 的存取管道。（[Geeky Gadgets](https://www.geeky-gadgets.com/deepseek-v4-free-hermes/)）

### xAI Grok
- **SuperGrok OAuth 整合（新）**：grok-4.3 以 1M context 窗口進入 Hermes，適合超長文件分析。無需 API 費用，以 SuperGrok 訂閱帳號登入即可使用。

### OpenAI GPT
- **間歇性 API 錯誤**：部分使用者回報透過 Codex 後端使用 GPT-5 系列時，偶發 API 錯誤——多數情況 agent 會自動重試，但少數情況會卡住需手動介入。目前建議配合 fallback model 設定使用。

### Ollama（本地模型）
- **隱私場景推薦**：Llama 4 Maverick via Ollama 是隱私優先本地部署的推薦組合，資料完全不離開本機。

### OpenRouter
- **使用量重大里程碑**：5 月 10 日 Hermes-Agent OpenRouter 日 token 用量首次超越 OpenClaw，達 271B tokens，印證社群遷移趨勢。（[Explainx.ai](https://explainx.ai/blog/hermes-agent-openrouter-number-one-ranking-nous-research-2026)）
- **⚠️ 5 月 OpenRouter 使用模式異動**：社群注意到 5 月份 OpenRouter 路由行為有所調整，部分模型的 rate limit 觸發頻率上升，建議設定多 provider fallback。

---

## 3. 社群反應與討論亮點

- **「Hermes 超越 OpenClaw」成為本月最熱討論**：多個科技媒體（MarkTechPost、explainx.ai、roborhythms.com）均以此為封面議題，強調這是開源 agent 生態的結構性轉變——自我改善型 agent 對比功能廣度型 agent 的市場較量。（[revolutioninai.com](https://www.revolutioninai.com/2026/05/hermes-agent-vs-openclaw-openrouter-2026.html)）
- **Tirith 安全模組阻擋正常指令**：社群回報 Tirith 安全層對 `curl | sh` 類指令採硬阻擋而非互動確認，使用者希望改為可審批流程。目前開 issue 討論中，尚未合入修正。
- **v0.13.0 Kanban 功能受好評**：多 agent 任務看板（支援 heartbeat、zombie 偵測、自動阻擋不完整退出）被評為長期自動化任務管理的重大突破，`/goal` 指令跨回合鎖定目標也獲得正面回饋。
- **比較評測文章熱傳**：《I Tested Hermes Agent vs Claude Code vs OpenClaw on 18 Real Tasks》（Towards AI）指出 Hermes 獨特的跨 session 記憶機制讓它能「作弊式地」讀取昨天的工作紀錄，引發關於 agent 持久記憶合理性的討論。（[Towards AI](https://pub.towardsai.net/i-tested-hermes-agent-vs-claude-code-vs-openclaw-on-18-real-tasks-the-10-week-old-one-cheats-by-0f2881a10213)）
- **CySecurity News 聚焦**：資安媒體 CySecurity 以「Hermes-Agent 作為 OpenClaw 強力挑戰者」為題報導，強調其自我改善（self-improving）機制是差異化關鍵。（[CySecurity News](https://www.cysecurity.news/2026/05/hermes-agent-emerges-as-strong.html)）

---

## 4. 值得追蹤的後續議題

| 議題 | 說明 |
|------|------|
| **下一版本（v0.15.0）動向** | v0.14.0 後下一個里程碑版本內容尚未公佈，關注 NousResearch/hermes-agent Milestone |
| **Tirith 安全模組調整** | `curl \| sh` 硬阻擋 → 互動確認的 PR 進度，影響自動化腳本場景 |
| **Windows 原生 Beta 穩定性** | Windows 支援剛上線，邊界案例多，追蹤社群回饋 |
| **OpenRouter 排名持續性** | Hermes 日用量超越 OpenClaw 是短期衝刺還是趨勢？追蹤 5 月底數據 |
| **HuggingFace Skill Hub 生態** | 預設可信 tap 開放後，技能品質與安全性管控是否跟得上成長速度 |
| **SuperGrok 1M context 實測** | grok-4.3 超長上下文在實際 agent 工作流中的效能與穩定性，等待社群實測報告 |
| **Token 暴增 Bug** | 開發目錄啟動導致 context 膨脹問題是否有更系統化的防護措施 |

---

*報告生成時間：2026-05-27 UTC｜資料範圍：過去 24 小時搜尋結果與近期公開討論*
