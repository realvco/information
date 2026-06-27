# OpenClaw 每日情報 — 2026-06-27

## 今日重點摘要

1. **穩定版 2026.6.10 於 6/24 發佈**：npm 於 UTC 03:01 正式發布，Fast talks（短對話自動進入快速模式後再回切正常模式）從 beta 正式畢業進入穩定版，改善 channel 驅動 agent 的回應延遲體驗。
2. **開發版 2026.6.11 持續推進**：帶入 Slack relay mode、原生 Mattermost `/oc_queue` 指令及每個 DM 獨立的模型覆蓋（per-DM model override）設定，預計近期正式發佈。
3. **Anthropic Agent SDK 點數新政策（6/15 生效）**：付費訂閱者使用第三方 agent（包括 OpenClaw）時，先從月度 Agent SDK 點數扣抵，超出後才按標準 API 費率計費——部分緩解了 4 月份 Claude 訂閱被全面封禁的衝擊。
4. **RAFT CLI wake bridge 加入**：`openclaw agent --message-file` 搭配 RAFT 遠端喚醒橋，開放檔案驅動與遠端喚醒兩條實用工作流路徑。
5. **Plugin 生態系更穩健**：額外官方 plugin 完成外部化，bundled plugin icon metadata 已可供已安裝用戶端取用；ClawHub 的 artifact metadata 問題亦已修復。
6. **後端穩定性提升**：Codex partial deltas、harness activation 及長上下文 prompt-cache 穩定性改善，減少 agent 工作流中進度遺失與不一致執行的狀況。
7. **Mobile 設定強化**：Android 設定詳細頁面重構，提升在行動裝置上對 OpenClaw 配置的可視性與控制力。
8. **2026 年 Q1–Q2 發佈節奏極為密集**：每週多個版本，5 月份 19 天內共發佈 15 個版本（v2026.5.1–v2026.5.18），涵蓋 plugin 外部化、語音 agent 改善、typed plugin SDK、Docker 安全強化及 Mac app 全面重設計。
9. **OpenClaw-RL 衍生項目持續活躍**：Gen-Verse 的 [OpenClaw-RL](https://github.com/Gen-Verse/OpenClaw-RL) 致力於「透過對話訓練任意 agent」，值得持續關注。

---

## LLM 串接實戰回報

### 模型無關架構 — 執行時切換
OpenClaw 採完全 provider-agnostic 設計，April 2026 推出的 provider manifest 讓使用者可於執行時切換 GPT-5.5、Claude Opus/Sonnet/Haiku、Gemini 3.1 Pro、DeepSeek、Ollama、Gemma 4 等，無需重建工作流。
- 參考：[OpenClaw April 2026: 6 Model Providers You Can Now Swap at Runtime](https://www.mindstudio.ai/blog/openclaw-april-2026-model-agnostic-provider-manifest)

### 常見生產部署混用模式
成熟的 OpenClaw 部署鮮少單一模型全包：
- **Planner agent**：Claude Sonnet 4 或 Opus 4（高推理能力）
- **Worker agent**：Claude Haiku 4.5 或 GPT-5 mini（降低執行成本）
- **分類 / 路由**：本機 Ollama（零 API 費用）
- **多模態文件分析**：Gemini（長上下文優勢）
- 參考：[Best LLM for OpenClaw: Gemini 3.1 Pro vs GPT-5.5 vs Claude Opus 4.7 (2026)](https://dev.to/matthewrevell/best-llm-for-openclaw-gemini-31-pro-vs-gpt-55-vs-claude-opus-47-2026-3na4)

### Anthropic 訂閱封禁事件（4/4–6/15）帶來的費用衝擊
4 月 4 日 Anthropic 封鎖 Claude Pro/Max 訂閱用於第三方工具（包括 OpenClaw）的 OAuth 認證，部分用戶原本以 $20–$200/月固定訂閱費換取大量 token，封禁後被迫轉為按 token 計費 API，費用暴增數十倍。6 月 15 日新政策生效後略有緩解。
- 參考：[Anthropic reinstates OpenClaw and third-party agent usage on Claude subscriptions — with a catch | VentureBeat](https://venturebeat.com/technology/anthropic-reinstates-openclaw-and-third-party-agent-usage-on-claude-subscriptions-with-a-catch)
- 參考：[Tell HN: Anthropic no longer allowing Claude Code subscriptions to use OpenClaw | Hacker News](https://news.ycombinator.com/item?id=47633396)

### Rate Limit 處理的已知缺陷
OpenClaw 內建的 exponential backoff 有已知 bug（GitHub issue #5159，已被關閉標記為「not planned」），社群建議於應用層自行實作 retry logic 或在 provider 配置加入 fallback model。
- 參考：[OpenClaw API Key Errors and Rate Limiting: Complete Troubleshooting Guide](https://www.aifreeapi.com/en/posts/openclaw-api-key-error-troubleshooting-guide)

### 上下文長度限制對照
| Provider | 模型 | 標準 API 上下文 |
|---|---|---|
| Anthropic 直連 | Claude Opus 4.7 | 1M tokens |
| Google 直連 | Gemini 3.1 Pro | 1M tokens |
| OpenAI 直連 | GPT-5.5 標準層 | 128K tokens |

---

## 社群反應與討論亮點

- **「Rough Week」事件餘波**：4 月底 v2026.4.29 造成 gateway 效能下降、plugin 依賴修復迴圈、Discord/Telegram/WhatsApp 通道行為異常，OpenClaw 官方於部落格正式道歉並說明根因。事件在 Hacker News 引發廣泛討論，但隨後 5 月的快速修復展現了專案韌性。
  - 參考：[OpenClaw Had a Rough Week - OpenClaw Blog](https://openclaw.ai/blog/openclaw-rough-week) | [Hacker News 討論串](https://news.ycombinator.com/item?id=48056003)
- **Discord Rate Limit 踩坑**：社群反映 OpenClaw 呼叫 Discord API 時（送訊息、建立 thread、加 reaction）遭遇 HTTP 429，解法為限制單位時間內的 API 呼叫頻率。
  - 參考：[AnswerOverflow 討論](https://www.answeroverflow.com/m/1476368939645931590)
- **Anthropic 封禁事件在科技媒體引爆**：TechCrunch、TNW、SecurityWeek 等媒體相繼報導，社群對「按 token 計費是否公平」的爭論持續延燒。
  - 參考：[Anthropic temporarily banned OpenClaw's creator from accessing Claude | TechCrunch](https://techcrunch.com/2026/04/10/anthropic-temporarily-banned-openclaws-creator-from-accessing-claude/)
- **OpenClaw 死了嗎？**：部分媒體以「Is OpenClaw Dead?」為題發文，但觀察 2026 年的發佈節奏後，普遍結論是「非常活躍，只是顛簸前行」。
  - 參考：[Is OpenClaw Dead? The Honest 2026 Assessment](https://www.betterclaw.io/blog/is-openclaw-dead-2026)

---

## 值得追蹤的後續議題

1. **2026.6.11 正式發佈**：含 Slack relay mode、Mattermost `/oc_queue`、per-DM 模型覆蓋等功能的正式 changelog 及用戶實測反饋。
2. **Agent SDK 點數用量上限**：Anthropic 新政策的月度 Agent SDK 點數上限是否充足，以及超出後的費用曲線是否會再度引發社群不滿。
3. **RAFT CLI wake bridge 實戰案例**：`openclaw agent --message-file` 搭配 RAFT 遠端喚醒的實際部署案例與效能數據。
4. **OpenClaw-RL 進展**：Gen-Verse 的「透過對話訓練 agent」項目後續 release 節奏及社群採用情形。
5. **SecureClaw 開源工具**：SecurityWeek 報導提及的 SecureClaw 項目值得觀察其與 OpenClaw 主線的整合計畫。
