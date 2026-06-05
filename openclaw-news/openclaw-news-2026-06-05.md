# OpenClaw 每日情報 — 2026-06-05

> 資料來源涵蓋截至 2026-06-05 UTC 的公開搜尋結果、GitHub releases、X.com 貼文與社群討論。

---

## 1. 今日重點摘要

1. **2026.6.2 正式穩定版發布**：OpenClaw 在 2026.6.2 推出後，npm 穩定通道已更新；此版本聚焦於 plugin 安裝策略重構、channel 穩定性（Telegram、Discord、WhatsApp、Feishu）與安全設定強化。
2. **Skill Workshop 正式落地（2026.6.1）**：全新「Skill Workshop」功能在 2026.6.1 中正式推出，為技能生命週期引入審查機制——代理工作成果進入 PROPOSAL.md 等待人工核可後才生效，防止錯誤技能影響後續工作流。
3. **NVIDIA Skill Cards 整合**：2026.6.1 亦加入 NVIDIA Skill Cards 與 Workboard 編排支援，為高算力場景提供更深度的 GPU 工作流整合。
4. **安全強化持續推進**：2026.6.2 拒絕損毀的 shell snapshot、異常 gateway 設定、過大的 audit 回應，及無效的 pending-agent SQLite scaffold，整體安全性顯著提升。
5. **Agent 恢復機制改善**：CLI 支援的 runtime 可從中斷的 tool call、過期 session binding 及 compaction handoffs 中更乾淨地恢復，減少需人工介入的卡死狀況。
6. **OAuth Token 假性 rate limit 問題仍未關閉**：GitHub issue #23996（假性 provider cooldown）與 #32828（所有模型誤報 API rate limit）仍為 open 狀態，影響使用 Claude Max OAuth token（sk-ant-oat01- 前綴）的用戶。
7. **2026.6.1-beta.2 仍在硬化週期**：npm 穩定版為 2026.5.28，changelog 已有 2026.6.2，但 beta.1 與 beta.2 標籤並存，運維方應視最新版本為積極測試階段而非靜默維護。
8. **Channel 廣度持續擴張**：目前支援 Telegram、WhatsApp、iMessage、Slack、Discord、Microsoft Teams、Google Chat、Google Meet、iOS Realtime Talk 等多平台。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）

- **OAuth Token 假性 rate limit 嚴重踩坑**（Issue #23996）：使用 Claude Max 訂閱所產生的 `sk-ant-oat01-` 開頭 OAuth Token 時，OpenClaw gateway 會誤將 provider 置於 cooldown，即使實際 API 並未達到限制。根本原因為 cooldown reason 寫死為 `"rate_limit"`，無論實際失敗原因為何，導致所有 agent 回應完全中斷且無法自動恢復。
  - 來源：[GitHub Issue #23996](https://github.com/openclaw/openclaw/issues/23996)
- **解法建議**：切換至付費 Anthropic API Key（非 Max 訂閱 token）為目前最直接的解法；或執行 `openclaw doctor --fix` 嘗試自動修復。
  - 來源：[AnswerOverflow 討論](https://www.answeroverflow.com/m/1478788645472436264)

### 多模型 rate limit 誤報

- **Issue #32828（所有模型均顯示 rate limit）**：部分用戶遇到 Claude 與 Qwen 等多個模型同時回報 rate limit，但 API 端確認正常運作。
  - 來源：[GitHub Issue #32828](https://github.com/openclaw/openclaw/issues/32828)

### 模型選用建議（2026 現況）

| 模型 | 適用場景 | 備註 |
|------|---------|------|
| Gemini 3.1 Pro | 大多數部署的預設選擇 | 大 context、最低成本、原生多模態 |
| GPT-5.5 | 自主 agent 工作流 | 適合 128K token 以內的任務 |
| Claude Opus 4.7 | 嚴格程式碼審查 | SWE-bench Pro 64.3%，指令遵循強 |
| Claude Sonnet | 日常 agent 工作 | 平衡品質與成本 |
| Claude Haiku | 高量低成本任務 | 大量呼叫場景 |

來源：
- [Best LLM for OpenClaw 比較文（DEV.to）](https://dev.to/matthewrevell/best-llm-for-openclaw-gemini-31-pro-vs-gpt-55-vs-claude-opus-47-2026-3na4)
- [OpenClaw LLM Setup 指南（LaoZhang AI Blog）](https://blog.laozhang.ai/en/posts/openclaw-llm-setup)
- [Medium：串接 Claude、ChatGPT 與 Telegram 的實戰筆記](https://medium.com/@mohit15856/i-wired-openclaw-to-claude-chatgpt-telegram-heres-what-actually-works-8278906edccc)

---

## 3. 社群反應與討論亮點

- **Skill Workshop 獲正面評價**：社群認為此功能解決了「代理自我修改技能庫」所帶來的安全顧慮，Board view 與 Today view 雙介面設計被指出適合不同使用習慣。
  - 來源：[OpenClaw Blog：Skill Workshop](https://openclaw.ai/blog/openclaw-agent-skill-workshop)
- **X.com 上 2026.5.26 仍熱議中**：官方 @openclaw 帳號貼文強調低延遲回覆與 Discord voice 整合，社群用戶紛紛分享升級體驗。
  - 來源：[X.com @openclaw 貼文](https://x.com/openclaw/status/2059619141384859939)
- **Luke The Dev 系列解析持續更新**：@iamlukethedev 持續為每次版本更新撰寫「What Actually Matters」分析，是社群中重要的非官方技術解讀來源。
  - 來源：[X.com @iamlukethedev（v2026.5.22）](https://x.com/iamlukethedev/status/2058400670730903901)
- **SEN-X 每日摘要報導 2026.6.1**：第三方媒體 SEN-X 已發布 2026.6.1 的深度分析，涵蓋 NVIDIA Skill Cards、Workboard 編排與安全強化三大面向。
  - 來源：[SEN-X OpenClaw Daily 2026-06-02](https://senx.ai/openclaw-news/2026-06-02-openclaw-news)
- **Discord 與 Reddit 社群持續活躍**：OpenClaw 維持 #help 與 #users-helping-users 頻道，並有 r/OpenClaw、r/Clawdbot 等子版塊；Weekly Claw 定期活動仍在進行。
  - 來源：[OpenClaw Community GitHub](https://github.com/openclaw/community)

---

## 4. 值得追蹤的後續議題

1. **Issue #23996 修復進度**：OAuth Token 假性 cooldown 問題尚未合併修補，影響 Claude Max 訂閱用戶；需持續追蹤是否在 2026.6.3 或後續 patch 中解決。
2. **Issue #24865 功能需求**：「在單一視圖顯示所有 auth profile 的 rate limit 狀態」功能需求仍 open，若實現將大幅改善多 provider 設定的可觀測性。
3. **npm 穩定版追趕進度**：changelog 已有 2026.6.2，但 npm stable 仍停在 2026.5.28，需關注何時穩定版同步更新。
4. **Skill Workshop 生態系成熟度**：此功能剛推出，社群自建技能的品質與審查流程是否流暢，值得未來數週觀察。
5. **NVIDIA Skill Cards 深度整合**：RTX 工作站與 Workboard 編排的整合方式尚未有詳細實戰報告，值得關注早期採用者的反饋。
6. **OpenClaw vs Hermes-Agent 定位比較**：NousResearch 官方在 X.com 上明確表示 Hermes 「介於 Claude Code 式 CLI 與 OpenClaw 式訊息平台代理之間」，兩者競合關係持續演化，值得持續觀察。

---

*本報告由自動化流程於 2026-06-05 UTC 生成，資料截止時間為當日可搜尋之公開資訊。*
