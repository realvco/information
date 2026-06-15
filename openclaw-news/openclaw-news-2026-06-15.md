# OpenClaw 每日情報 — 2026-06-15

> 資料蒐集範圍：過去 24 小時（2026-06-14 ～ 2026-06-15 UTC）

---

## 1. 今日重點摘要

1. **v2026.6.5 正式釋出（6 月 9 日）**：此版本包含 30+ 項改善與修復，聚焦四大面向：Session 恢復穩定性、工具呼叫格式相容性、多頻道互動強化、設定與儲存安全性。目前 npm 穩定版為 `2026.6.1`，GitHub Prerelease 則為 `v2026.6.5-beta.2`。
2. **QQBot 頻道修復**：v2026.6.5 中，QQBot 會在發送前自動剝除模型輸出中的 `<reasoning>/<thinking>` 標籤，使用者只會看到最終答案，避免內部思考過程外洩。
3. **MCP 工具呼叫改善**：非文字/圖片型 MCP tool-result block 在進入 provider converter 之前會被自動強制轉型，有效圖片保留，其餘轉為文字，修復過去產生的格式錯誤 block 問題。
4. **GitHub 活動（6/14）**：`openclaw/clawsweeper`（自動掃描並建議關閉舊 issue/PR 的工具）與 `Peekaboo`（macOS CLI，供 AI Agent 截圖用）於 6 月 14 日均有更新。同日 issue #92925 與 #92926 被開立，標記為「需 maintainer 審查後才能自動化處理」。
5. **社群模型排行（6/13 最新）**：依 Price Per Token 社群投票，OpenClaw 最佳搭配模型排行：①**Kimi K2.5**（第一）、② GLM 4.7、③ Claude Opus 4.6。大文本 Code Review 場景則以 **Gemini 3.1 Pro** 為推薦首選（大 context、最低每 job 費用、原生多模態）。
6. **Anthropic OAuth 禁令持續影響**：自 2026 年 4 月 4 日起，Anthropic 禁止在第三方工具中使用訂閱制 OAuth Token（Claude Pro/Max）。OpenClaw 使用者現須改用「Extra Usage（pay-as-you-go）」或獨立 API Key，此限制持續是新用戶最常遇到的設定障礙。
7. **Model Failover 老問題仍存**：GitHub issue #19249 與 #11972 記錄的 failover 機制問題尚未完全解決：當執行中途遇到 rate limit（429），內嵌執行層只會對同一模型重試，不會切換至 fallback chain。此問題僅在 session 建立時（閘道重啟後）能觸發 failover，執行時不行。
8. **ClawHub 積累超過 4,000 社群技能**：ClawHub 持續成長，目前可供安裝的社群 skill 已超過 4,000 個，OpenClaw 亦已設定為預設優先使用 ClawHub 而非 npm 安裝技能。
9. **GitHub Stars 維持高檔**：OpenClaw 截至 2026 年 4 月已達 **34.7 萬顆 GitHub Stars**（估計 6 月持續增長中），全球活躍用戶估計超過 200 萬。

---

## 2. LLM 串接實戰回報

### Anthropic Claude — OAuth 封禁後遷移陣痛

- **問題**：訂閱制 Claude Pro/Max 的 OAuth Token 自 4/4 起被禁，大量使用者在升級或重新安裝後遭遇 `401 Unauthorized`。
- **解法**：改用 Anthropic 後台產生的 API Key，或透過 OpenRouter 繞接。執行 `openclaw doctor --fix` 可自動修復多數設定問題。
- **來源**：[4 solutions to resolve OpenClaw error LLM request rejected](https://help.apiyi.com/en/openclaw-claude-llm-request-rejected-extra-usage-fix-en.html)

### Kimi K2.5 / DeepSeek — Rate Limit 踩坑

- **問題**：在多任務並行場景下，OpenClaw 的模型 failover 機制無法在執行時期因應 rate limit（429），只會對同一模型重試耗盡後才報錯，而不會自動切換備援模型。
- **暫解**：使用者建議手動在 `model_chain` 中預先排列多個 provider（例如 Kimi K2.5 → DeepSeek V3 → Gemini 2.0 Flash），並接受 failover 只在 session 重建時生效。
- **來源**：[Model failover does not activate on rate limit · Issue #19249](https://github.com/openclaw/openclaw/issues/19249)

### Gemini 3.1 Pro — 大 Context 場景表現

- **回報**：多位開發者在 Code Review 工作流實測中，Gemini 3.1 Pro 以最低費用支撐大 context 視窗，且原生多模態不需額外設定，整體穩定性勝過 GPT-5.5 和 Claude Opus 4.7。
- **來源**：[Best LLM for OpenClaw: Gemini 3.1 Pro vs GPT-5.5 vs Claude Opus 4.7 (2026)](https://dev.to/matthewrevell/best-llm-for-openclaw-gemini-31-pro-vs-gpt-55-vs-claude-opus-47-2026-3na4)

---

## 3. 社群反應與討論亮點

- **模型選擇論戰持續**：社群對「OpenClaw 最佳模型」討論熱度不減，Price Per Token 排行榜（6/13 更新）顯示 Kimi K2.5 超越 Claude Opus 4.6 登頂，引發不少討論——主要爭議在「工具呼叫穩定性」vs「費用效益」的取捨。
- **OpenAI 招聘議題**：有報導指出 OpenAI 一起 OpenClaw 相關招聘動作在 X 上引發大量迷因與競爭氣氛討論（連結來自 AOL/Tech 媒體），仍是社群關注話題。
- **ClawSweeper 上線**：官方推出的 `openclaw/clawsweeper` 工具（自動分析 issues/PRs 並建議關閉）在社群中獲得正面回響，有助緩解龐大 issue 積壓問題。
- **加密貨幣話題管制**：官方 Discord 明確規定任何比特幣或加密貨幣相關提及可能被移除，此政策被部分社群成員討論。

---

## 4. 值得追蹤的後續議題

1. **Model Failover 執行時期修復**：issues #19249、#11972 仍開放，待官方釋出可在執行中途切換 fallback 的修復。
2. **v2026.6.5-beta.2 轉穩定**：目前 GitHub Prerelease 版本領先 npm 穩定版（6.1 vs 6.5），需追蹤何時合併為正式穩定版。
3. **Claude OAuth 長期方案**：Anthropic 禁令是否影響 OpenClaw 後續與 Claude 整合的規劃，官方尚未公開說明。
4. **Kimi K2.5 + OpenClaw 深度相容性**：K2.5 雖登頂社群投票，但官方對其工具呼叫格式支援的測試報告尚未完整，需持續觀察。
5. **GitHub issues #92925 / #92926**：兩個 6/14 開立的新 issue 待 maintainer 確認內容與處置方向。

---

*來源整理*
- [OpenClaw Release Notes - June 2026](https://releasebot.io/updates/openclaw)
- [OpenClaw 2026.6.5 Release Notes (Efficient Coder)](https://www.xugj520.cn/en/archives/openclaw-2026-6-5-release.html)
- [Best LLM for OpenClaw 2026 (DEV Community)](https://dev.to/matthewrevell/best-llm-for-openclaw-gemini-31-pro-vs-gpt-55-vs-claude-opus-47-2026-3na4)
- [Best Model for OpenClaw — LLM Rankings (Price Per Token)](https://pricepertoken.com/leaderboards/openclaw)
- [OpenClaw LLM Request Rejected Fix (Apiyi Blog)](https://help.apiyi.com/en/openclaw-claude-llm-request-rejected-extra-usage-fix-en.html)
- [Model failover issue #19249](https://github.com/openclaw/openclaw/issues/19249)
- [False rate limit bug #32828](https://github.com/openclaw/openclaw/issues/32828)
- [OpenClaw GitHub Repositories](https://github.com/orgs/openclaw/repositories)
