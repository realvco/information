# OpenClaw 每日情報｜2026-06-08

> 資料截止時間：2026-06-08 UTC｜來源：GitHub、X.com、官方部落格、社群論壇

---

## 1. 今日重點摘要

1. **v2026.6.1（穩定版，約 6/6 發布）為當前最新正式版**，帶入 MiniMax M3 整合、Windows 執行容器、Skill Workshop 功能及 MCP 工具回傳強制轉型機制（防止 Anthropic 400 錯誤）。
2. **v2026.6.5-beta.2 積極迭代中**：修復 `<thinking>` 標籤內容洩漏至頻道回覆的問題、強化 MCP 資源強制轉型邊界、更安全的 plugin 安裝流程。
3. **Skill Workshop 為本週最受討論的新功能**（2026-06-03 公告）：採用「先提案、再審核」機制，pending 技能以 `PROPOSAL.md` 追蹤，須人工核可後才正式生效；社群已累積 5,400+ 技能模板。
4. **GitHub Issue #91270（6/7 開立）**：涉及 session state 的增強型議題，為過去 24 小時內最新的追蹤項目。
5. **Gemini CLI 棄用緊急議題（Issue #84527）**：Google 於 5/19 宣布 Gemini CLI 將於 6/18 對個人用戶停止服務，OpenClaw 社群積極討論以 Antigravity CLI (agy) 替代的方案。
6. **Gateway ClawHub Auth Token 傳遞失敗（Issue #52949）**：Gateway 未正確轉送 ClawHub 驗證 token，導致持續出現假 429 錯誤及「Available Skills」清單空白，尚未修復。
7. **多頻道穩定性修補（v2026.6.2 beta）**：Telegram、Feishu、Discord、WhatsApp 的重複 transcript、串流預覽、語音錯誤均納入本次修補。
8. **安全強化**：拒絕損毀的 shell snapshot、可疑 gateway 啟動配置、格式錯誤的 Docker 數字限制、超大 audit response 及不安全 exec precheck 環境。
9. **「Heartbeat Tax」警示**：以 Claude Opus 4.6 執行 24/7 polling 可能每月超過 $300；將背景任務路由至 MiniMax M2.5 或 Kimi K2.5 可降低最多 95% 費用。

---

## 2. LLM 串接實戰回報

### 2.1 Rate Limit 問題

- **Issue #52949**（[GitHub](https://github.com/openclaw/openclaw/issues/52949)）：Gateway 未帶入 ClawHub auth token 進行 API 呼叫，導致 ClawHub 回傳假 429 及技能清單空白。暫時解法：改用直接付費 API key，繞過 ClawHub gateway 路徑。
- **各 provider 速率上限對照**：
  - ClawHub（未驗證）：120 req/min（全域共享池）
  - ClawHub（已驗證）：600 req/min（per user）
  - Anthropic Tier 1（消費 $5 後）：50 RPM
  - OpenAI Tier 1：500 RPM
  - Gemini 免費層：15 RPM（但免費）
- 來源：[getopenclaw.ai/help/rate-limits-quota-management](https://www.getopenclaw.ai/help/rate-limits-quota-management)

### 2.2 Auth 失效問題

- OAuth session token 在閒置、系統重啟或 provider 政策變更後會過期，造成靜默失敗。
- 建議：生產環境從 OAuth/setup-token 改用付費 Anthropic API key 以提高可靠性。
- 來源：[blog.laozhang.ai OpenClaw API key errors](https://blog.laozhang.ai/en/posts/openclaw-api-key-error)

### 2.3 本地模型（Local LLM）踩坑

- **Issue #5769**：串流協議 bug 導致 tool call 在本地模型環境中被丟棄。
- **Context window 不足**：agent 任務最低需求 64K+ tokens，許多本地模型預設值遠低於此。
- **WSL2 網路問題**：部分使用者回報 WSL2 環境下 gateway 連線異常。
- **模型能力門檻**：可靠執行 agent 任務須至少 30B 參數模型。
- **Ollama API 路徑變更**：現使用原生 `/api/chat` 端點（非 `/v1`），相容性補丁仍在進行中。
- 來源：[betterclaw.io/blog/openclaw-local-model-not-working](https://www.betterclaw.io/blog/openclaw-local-model-not-working)

### 2.4 MCP 工具回傳問題（v2026.6.1 修復）

- `resource_link`、`resource`、`audio`、格式錯誤的 image 等非 text/image 內容以前會污染 session history 並觸發 Anthropic 400 錯誤。
- v2026.6.1 起在 materialize 邊界強制轉型，徹底修復此問題。

### 2.5 各 LLM Provider 效能比較

| 模型 | SWE-Bench / 評測 | 費用（輸入 per 1M tokens） | 備註 |
|---|---|---|---|
| Claude Opus 4.6 | 80.80% SWE-Bench ✓ | 高 | 程式碼任務首選 |
| GPT-5.2 | 80.00% SWE-Bench | 高 | 強力替代選項 |
| MiniMax M2.5/M3 | 80.20% SWE-Bench | 極低（比 Claude 便宜 95%） | 背景/心跳任務首選 |
| Kimi K2.5 | 社群 6/6 票選第一 | 低 | 社群最愛 |
| DeepSeek V3.2 | ~GPT-5.4 品質 90% | $0.28/M | 高性價比 |
| Gemini 3 Flash | — | 低 | 預算方案推薦 |
| Qwen 3.6 | Terminal-Bench 59.3% | 中 | function-calling 媲美 Claude Opus 4.5 |
| Llama 3.3 70B (Ollama) | — | 免費 | 本地最佳開源選項 |

**OpenRouter**：單一 API key 可路由 315–350+ 模型，[openrouter.ai/collections/openclaw](https://openrouter.ai/collections/openclaw)。
**LiteLLM**：官方整合教學 [docs.litellm.ai/docs/tutorials/openclaw_integration](https://docs.litellm.ai/docs/tutorials/openclaw_integration)。

來源：[pricepertoken.com/leaderboards/openclaw](https://pricepertoken.com/leaderboards/openclaw) | [blog.laozhang.ai OpenClaw model selection guide](https://blog.laozhang.ai/en/posts/openclaw-best-model-selection-guide)

---

## 3. 社群反應與討論亮點

- **Skill Workshop 高度關注**：[blink.new 教學](https://blink.new/blog/openclaw-skill-workshop-guide) 與 [官方部落格](https://openclaw.ai/blog/openclaw-agent-skill-workshop) 詳細介紹審核流程；社群 awesome-openclaw-skills repo 已累積 5,400+ 技能（[github.com/VoltAgent/awesome-openclaw-skills](https://github.com/VoltAgent/awesome-openclaw-skills)）。
- **Gemini CLI 棄用引爆討論**：Issue #84527 在 r/openclaw 與 r/ClaudeAI 均有熱烈討論，使用者急尋替代方案，截止日期為 6/18。
- **Reddit 雙社群分流**：r/openclaw 聚焦實戰技術問答；由於 OpenClaw 源於 Claude Code 生態，r/ClaudeAI 是較大的溢出討論場域。
- **Claude Max 訂閱 vs 直接 API 成本爭論**：社群尚未得出統一結論，高度取決於工作負載型態。
- **X.com 最近公開貼文**：[@iamlukethedev](https://x.com/iamlukethedev/status/2049992708262174922)（4/30）介紹 v2026.4.29 的群組聊天優化、NVIDIA provider 新增及更快啟動速度；6/7–6/8 無新的高關注度 X 貼文被索引到。
- **162 個生產就緒 agent 模板**：[github.com/mergisi/awesome-openclaw-agents](https://github.com/mergisi/awesome-openclaw-agents) 持續更新。
- **相關研究項目**：[OpenClaw-RL](https://github.com/Gen-Verse/OpenClaw-RL) — 將對話轉為訓練信號的全非同步強化學習框架，正在受到關注。

---

## 4. 值得追蹤的後續議題

| 議題 | 優先度 | 截止 / 追蹤連結 |
|---|---|---|
| Issue #84527：Gemini CLI 棄用替代方案 | **緊急** 6/18 截止 | [Issue #84527](https://github.com/openclaw/openclaw/issues/84527) |
| Issue #52949：Gateway ClawHub Auth Token 失敗 | 高 | [Issue #52949](https://github.com/openclaw/openclaw/issues/52949) |
| Issue #91270：Session state 增強（6/7 開立） | 中 | [Issues 頁面](https://github.com/openclaw/openclaw/issues) |
| v2026.6.5-beta.2 → 正式版發布時程 | 中 | [GitHub Releases](https://github.com/openclaw/openclaw/releases) |
| Issue #5769：本地模型串流 tool call 遺失 | 中 | [Issue #5769](https://github.com/openclaw/openclaw/issues/5769) |
| Skill Workshop 審核機制社群反饋 | 低 | [docs.openclaw.ai/tools/skill-workshop](https://docs.openclaw.ai/tools/skill-workshop) |
| Ollama `/api/chat` 相容性補丁進度 | 低 | [docs.ollama.com/integrations/openclaw](https://docs.ollama.com/integrations/openclaw) |

---

*報告生成時間：2026-06-08 UTC*
