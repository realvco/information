# OpenClaw 每日情報｜2026-06-28

> 搜尋區間：2026-06-27 至 2026-06-28（UTC）

---

## 1. 今日重點摘要

1. **穩定版 2026.6.10（2026-06-24 發布）** — Fast Mode（自動快速模式）從 Beta 正式進入穩定線；短對話自動切換快速通道，完成後無縫回到一般模式，不影響可見狀態。
2. **Beta 2026.6.11-beta.1 進入測試** — 涵蓋 Slack relay 模式、原生 Mattermost `/oc_queue`（PR #95546）、`openclaw agent --message-file` 新 CLI 選項，以及 RAFT CLI wake bridge 整合。
3. **頻道強化：Telegram 改版** — Telegram 現可傳送富文字 HTML，保留 Markdown 格式與貼圖路徑，進度草稿與指令輸出渲染更忠實。
4. **長文脈壓縮 Bug（Issue #94391）** — 超大 session（60 秒聚合壓縮等待）在 143–190 秒完成後的摘要會被遺棄，導致 overflow 與重試風暴；目前社群建議暫時縮短 session 長度。
5. **Issue #97152 與 #97163（2026-06-27 新開）** — 兩個在 GitHub 上最新提出的 Issue，細節尚在確認中，社群持續關注。
6. **假性 Rate Limit Bug（Issue #32828）** — 儘管底層 API 正常回應，OpenClaw 仍對所有模型顯示「⚠️ API rate limit reached」；ZAI provider 因誤分類進入無限冷卻（Issue #40989）。重啟 gateway（`openclaw restart`）可暫時解除。
7. **Plugin 分發架構優化** — 官方 plugin 外化更乾淨，捆綁 icon metadata 現可供已安裝的用戶端取用。
8. **Android 操作改善** — 設定細節面板在手機端的可見度與控制能力提升。
9. **GitHub 專案規模** — openclaw/openclaw 目前累積 **379k+ Stars**、3,300+ 個 open issues，顯示社群規模持續擴大但維護壓力也高。

---

## 2. LLM 串接實戰回報

### 模型選擇與定價（2026 年現況）

| 模型 | 上下文視窗 | 輸入 / 輸出（每 1M tokens） |
|------|------------|---------------------------|
| Gemini 3.1 Pro | 1M tokens（>200K 以上加價） | $2 / $12（$4 / $12 超過 200K） |
| GPT-5.5（標準 API） | 128K tokens | $5 / $30 |
| Claude Opus 4.7 | 1M tokens | $5 / $25 |

> 來源：[Best LLM for OpenClaw: Gemini 3.1 Pro vs GPT-5.5 vs Claude Opus 4.7 (2026) - DEV Community](https://dev.to/matthewrevell/best-llm-for-openclaw-gemini-31-pro-vs-gpt-55-vs-claude-opus-47-2026-3na4)

### Fallback Chain 配置

OpenClaw 支援 `agents.defaults.model.fallbacks` 設定多層備援：Claude 觸發 rate limit → 自動切換 GPT-5.5 → 再降至 Gemini。適合 24/7 無人值守的場景，但需同時持有多家 API Key。

> 來源：[Model providers · OpenClaw](https://docs.openclaw.ai/concepts/model-providers)

### 假性 Rate Limit 踩坑

- **症狀**：所有模型顯示 rate limit 警告，但外部測試 API 正常。
- **根本原因**：OpenClaw 將 provider 的 529（過載）誤分類為 429（Rate Limit），進入冷卻後不自動恢復。
- **緊急修法**：`openclaw restart` 清除卡住的冷卻狀態；ZAI provider 的無限冷卻（Issue #40989）尚無官方 patch，建議暫時排除 ZAI 路由。

> 來源：[Bug Issue #32828 · openclaw/openclaw](https://github.com/openclaw/openclaw/issues/32828) ｜ [Bug Issue #40989 · openclaw/openclaw](https://github.com/openclaw/openclaw/issues/40989)

### Claude Max vs API 成本討論

社群主流意見：低至中流量使用 Claude Max 訂閱透過 OpenClaw 路由，成本遠低於 per-token API；重度流量（高頻 batch 工作）才划算用 API 計費。

---

## 3. 社群反應與討論亮點

- **r/openclaw** 是官方討論基地，涵蓋版本公告、安裝協助與 show-and-tell；**r/ClaudeAI** 是最大的 OpenClaw 外部討論社群，因 OpenClaw 脫胎自 Claude Code 生態系。
- **Weekly Claw** 定期社群活動持續進行，本週有展示記錄釋出。
- Discord 社群近期熱議：Slack/Mattermost 的權限設定與 WhatsApp gateway 接線正確性，不少用戶在這兩個渠道卡關。
- 有用戶抱怨 2026.4.1 的更新曾完全破壞既有 exec 環境（Issue #59006），要求更穩健的 migration guide。

---

## 4. 值得追蹤的後續議題

| 議題 | 說明 | 追蹤位置 |
|------|------|----------|
| Issue #94391 長文脈壓縮 Bug | 大型 session 摘要被遺棄，可能造成資料遺失 | [openclaw/openclaw#94391](https://github.com/openclaw/openclaw/issues/94391) |
| Issue #32828 假性 Rate Limit | 影響所有模型可用性，官方尚未合入修復 | [openclaw/openclaw#32828](https://github.com/openclaw/openclaw/issues/32828) |
| Beta 2026.6.11 穩定化進度 | Slack relay / RAFT wake bridge 何時進穩定版 | [openclaw/openclaw Releases](https://github.com/openclaw/openclaw/releases) |
| Issue #97152 & #97163 | 2026-06-27 新開，細節待揭露 | [openclaw/openclaw Issues](https://github.com/openclaw/openclaw/issues) |
| OpenRouter 整合成熟度 | 社群開始測試透過 OpenRouter 路由多家模型的穩定性 | r/openclaw |

---

*本報告依搜尋結果編寫，未收錄 24 小時內有確認來源的資訊不列入；如有遺漏或錯誤請至 Issues 回報。*
