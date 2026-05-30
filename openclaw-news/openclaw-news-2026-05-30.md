# OpenClaw 每日情報 — 2026-05-30

> 搜尋範圍：過去 24 小時（UTC 2026-05-29 ～ 2026-05-30）

---

## 1. 今日重點摘要

1. **v2026.5.28-beta.2/3 持續推出**：OpenClaw 在 5 月 29 日 UTC 04:46 發布 beta.1、12:19 發布 beta.2，29 日稍晚再推出 beta.3，主軸為代理與 Codex 執行時的穩定性修復。
2. **子代理工作區隔離修復**：`subagent` 現在能正確保持各自的 `cwd`/`workspace`，防止任務互相污染；hook 上下文也限定於當前 prompt，不再洩漏到其他 session。
3. **Session 鎖定逾時釋放**：修正 session lock 在 timeout abort 時未自動釋放的問題，解決了若干需手動重啟的卡頓情境。
4. **Codex app-server 穩定性提升**：共享 app-server 客戶端現在可存活於 startup 與衍生 helper 的失敗，消除先前因 helper 崩潰導致整個 runtime 下線的問題。
5. **Gateway 啟動加速**：啟動流程移除了對 plugin、channel、session、usage-cost、scheduled-service 及 filesystem 的重複掃描，冷啟動與熱啟動均顯著加快。
6. **安全邊界強化**：群組 prompt 文字不再注入 system prompt；會重複點號的主機名稱被正規化；危險的 Node runtime 環境變數覆寫被封鎖；未授權的 no-auth Tailscale 暴露遭到拒絕。
7. **依賴更新**：`@openclaw/proxyline` 升至 0.3.3；Pi packages 升至 0.75.1；最低支援的 Node.js 版本提升至 22.19。
8. **多通道修復**：Matrix room ID、iMessage reactions/approvals、Slack final replies、Discord 工具警告、WhatsApp profile auth、Telegram polling 及 Microsoft Teams service URL 的信任檢查均已修補。

---

## 2. LLM 串接實戰回報

### 2-1. Claude OAuth 觸發假 rate limit（持續中）

- **現象**：使用 Claude OAuth 授權的使用者收到「API rate limit reached. Please try again later.」，但 Claude API 本身完全正常。
- **根因**：provider cooldown 的失敗原因被硬編碼為 `rate_limit`，無論實際失敗是 timeout、billing 或 auth 失效，均顯示同一訊息，導致無限 cooldown 迴圈。
- **來源**：[Issue #23996 — False provider cooldown on OAuth token auth](https://github.com/openclaw/openclaw/issues/23996)、[Issue #32828 — False 'API rate limit reached'](https://github.com/openclaw/openclaw/issues/32828)

### 2-2. OAuth token 靜默過期問題

- **現象**：長時間閒置或系統重開機後，OAuth session token 失效，OpenClaw 無法代為發 API 請求，但錯誤訊息與 rate limit 相同，讓使用者誤以為是配額問題。
- **處理方式**：需重新執行 `openclaw models auth setup-token` 重新授權。
- **來源**：[How to Fix OpenClaw 'API Limit Reached' Error - OAuth Refresh Solution](https://docs.bswen.com/blog/2026-03-24-openclaw-api-limit-error/)

### 2-3. 多模型切換：Claude / GPT-5.5 / Gemini 3.1 Pro 比較

- **Claude Sonnet**：工具呼叫可靠性最高，長上下文連貫、MCP-native，能通過企業安全審查；建議用於大多數 agent 任務。
- **GPT-5.5 via Codex**：目前 frontier 模型裡 CP 值最高，適合複雜 PR review 與深度推理任務。
- **Gemini 3.1 Pro**：多模態與長上下文表現突出，在 OpenClaw 工作流中的多媒體處理場景佔優。
- **來源**：[Best LLM for OpenClaw: Gemini 3.1 Pro vs GPT-5.5 vs Claude Opus 4.7 (2026)](https://dev.to/matthewrevell/best-llm-for-openclaw-gemini-31-pro-vs-gpt-55-vs-claude-opus-47-2026-3na4)

### 2-4. April 2026 Provider Manifest：runtime 切換模型不需重建

- OpenClaw 在 4 月推出 provider manifest，允許在執行中的工作流裡直接切換底層 LLM，支援 GPT-5.5（Codex）、Claude API、Gemini 等六個主流 provider，不需重新打包或部署。
- **來源**：[OpenClaw April 2026: 6 Model Providers You Can Now Swap at Runtime](https://www.mindstudio.ai/blog/openclaw-april-2026-model-agnostic-provider-manifest)

### 2-5. 無統一 auth profile 用量儀表板（Feature Request 持續開放）

- 目前無法在單一畫面檢視所有已設定 auth profile 的 rate limit 與使用量狀態，使用者須逐一切換確認。
- **來源**：[Feature: Show rate limit / usage state for ALL configured auth profiles](https://github.com/openclaw/openclaw/issues/24865)

---

## 3. 社群反應與討論亮點

- **X.com（Luke The Dev）**：[@iamlukethedev](https://x.com/iamlukethedev/status/2049992708262174922) 發文整理 v2026.4.29 重點，並指出「Active-run steering 讓代理在執行中仍可接受人類介入調整方向」是本週最受關注的功能，轉推熱度高。
- **Medium 實作文章**：《[I Wired OpenClaw to Claude, ChatGPT & Telegram — Here's What Actually Works](https://medium.com/@mohit15856/i-wired-openclaw-to-claude-chatgpt-telegram-heres-what-actually-works-8278906edccc)》詳細記錄多 provider 同時配置的實際踩坑流程，社群反映「終於有人完整寫出來了」。
- **OAuth 錯誤誤導問題**：[AnswerOverflow 討論串](https://www.answeroverflow.com/m/1478788645472436264) 顯示多位使用者對 Claude OAuth 的假 rate limit 感到困惑，社群建議在錯誤訊息中區分「auth 失效」與「配額耗盡」。
- **安全議題受關注**：beta.2/3 強化 group prompt 注入與 Tailscale 暴露的安全邊界，在 Discord 社群引發正面反應，有使用者表示「這些是企業導入的關鍵障礙，終於修了」。

---

## 4. 值得追蹤的後續議題

| 議題 | 說明 | 相關連結 |
|------|------|----------|
| Provider cooldown 根本修復 | 目前僅有 workaround，`rate_limit` 硬編碼問題尚未在 beta 中根治 | [Issue #23996](https://github.com/openclaw/openclaw/issues/23996) |
| 統一 auth 用量儀表板 | Feature request 已有一定追蹤數，預計下個 milestone 處理 | [Issue #24865](https://github.com/openclaw/openclaw/issues/24865) |
| v2026.5.28 穩定版發布時程 | 目前仍為 beta stream，何時升為 stable 尚未公告 | [Releases · openclaw/openclaw](https://github.com/openclaw/openclaw/releases) |
| NVIDIA provider 整合成熟度 | 4 月加入 NVIDIA provider，社群反映文件不足，實際使用案例待補充 | [OpenClaw April 2026 Update](https://www.clawbot.blog/blog/openclaw-the-rise-of-an-open-source-ai-agent-framework-april-2026-update/) |
| Node.js 22.19 最低版本影響 | 升版後部分老舊部署環境可能需要更新，影響範圍待評估 | — |
