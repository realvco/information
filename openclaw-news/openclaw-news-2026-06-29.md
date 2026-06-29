# OpenClaw 每日情報 — 2026-06-29

> 資料來源截止時間：2026-06-29 UTC｜搜尋範圍：過去 24 小時

---

## 1. 今日重點摘要

1. **最新測試版 2026.6.10-beta.1 上線**：新增 `/fast auto` 自動快速模式，短對話自動切換至快速通道，長任務或需要 fallback 時再回歸正常模式，不中斷可見狀態（PR #85104，Issue #85087）。
2. **Z.ai GLM-5 模型整合**（PR #94461、Issue #94269）：官方 provider 目錄新增支援 Z.ai 旗下 GLM-5 系列模型，擴大多模型選擇空間。
3. **首次啟動憑證提示 Bug 修復**（PR #95792、Issue #95765）：修正首次 setup 期間錯誤彈出憑證輸入視窗的問題，改善新手 onboarding 體驗。
4. **PinchBench 排行更新**：Claude Sonnet 4.6 以 86.9% 成功率位居 OpenClaw 基準測試榜首，GPT-5.4 緊追在後（86.0%）；Gemini 3.1 Pro 因大 context 程式碼審查能力與最低每任務成本，仍是多數部署的推薦首選。
5. **Gemini 整合問題持續未解**：caching、embeddings、thinking 功能在 OpenClaw 上仍無法正常運作；背景 agent（Heartbeat）在初次回應後停止運作、無聲無息，為當前高票 issue。
6. **未內建費率限制導致成本暴增**：OpenClaw 預設不含 token 預算、每日消費上限或重試迴圈熔斷機制；有用戶回報 agent 在電子郵件整理任務卡住後持續重試，單夜燒掉 $47 API 費用。
7. **Gemini 2.5 Pro token 消耗問題**：已知行為，在少數幾十次 API 呼叫中即可消耗超過 190 萬 input tokens；並非 OpenClaw bug，但目前缺乏官方緩解措施。
8. **大型工具輸出造成 session 膨脹**：gateway `config.schema` 返回 396 KB+ JSON 的問題一旦被寫入 session `.jsonl`，後續每條訊息都會帶著整個 blob，導致 token 指數增長。
9. **Repository 規模**：目前 379k+ GitHub Stars，仍有約 3,300+ 個 open issues 和數千筆 open PR，顯示開發活躍但 backlog 龐大。

---

## 2. LLM 串接實戰回報

### Claude Sonnet 4.6（Anthropic）
- 在 PinchBench OpenClaw 基準測試中以 **86.9%** 成功率排名第一。
- 整合方式與其他 provider 一致（API key），報告中未見特殊 auth 問題。
- 來源：[Best LLM for OpenClaw: Gemini 3.1 Pro vs GPT-5.5 vs Claude Opus 4.7 (2026)](https://dev.to/matthewrevell/best-llm-for-openclaw-gemini-31-pro-vs-gpt-55-vs-claude-opus-47-2026-3na4)

### GPT-5.4（OpenAI）
- 成功率 86.0%，與 Claude 差距甚小；在長上下文任務中可靠性高。
- 來源：同上

### Gemini 3.1 Pro（Google）
- 推薦理由：**最低每任務成本 + 原生多模態 + 大 context 程式碼審查**。
- 實際踩坑：caching、embeddings、thinking 三項功能皆**無法正常運作**；Heartbeat 背景 agent 啟動後無聲終止，為已知高票 issue。
- Gemini 2.5 Pro 另有 token 暴食問題（190 萬+ input tokens / 數十次呼叫），已確認為模型本身行為。
- 來源：[OpenClaw Review 2026: Testing GPT, Gemini, Opus, MiniMax](https://nek12.dev/blog/en/openclaw-review-2026-testing-gpt-gemini-opus-minimax/)、[Model providers · OpenClaw](https://docs.openclaw.ai/concepts/model-providers)

### 費用與 Rate Limit 通用問題
- **無內建費率保護**：沒有 token 預算或每日上限；agent 卡在重試迴圈可造成 $47–數千美元的意外費用。
- **gateway config.schema 396KB 膨脹**：寫入 session 後造成 token 指數增長，手動清理 `.jsonl` 是目前唯一已知緩解方式。
- 來源：[OpenClaw hitting API rate limits in 2026? The workaround that actually works](https://dev.to/sophiaashi/claude-code-rate-limit-on-max-plan-the-workaround-developers-are-actually-using-in-2026-16g2)、[How to Run OpenClaw 24/7 Without Breaking the Bank](https://perelweb.be/blog/openclaw-token-management-smart-model-manager/)、[Feature Request Issue #58826](https://github.com/openclaw/openclaw/issues/58826)

---

## 3. 社群反應與討論亮點

- **正面**：379k Stars 顯示社群基礎龐大；`/fast auto` 新功能受到使用者肯定，減少了短對話的等待感。
- **負面**：約 4,000 個 open issues 與 3,000+ open PRs 讓部分用戶抱怨「核心概念很好，但實作問題太多」。Gemini 整合被多名用戶形容為「幾乎無法使用（practically not supported）」。
- **費用驚嚇**：無內建熔斷機制的設計持續引發討論，有用戶在論壇分享「隔夜 agent 燒掉 $47」的親身經歷，呼籲官方盡快加入 token budget 功能（Issue #58826 已獲大量 upvote）。
- **NGINX/Kong 緩解方案**：社群整理出透過 reverse proxy 實作費率控管可降低高達 80% 基礎成本的作法，但門檻較高，非一般用戶友善。

---

## 4. 值得追蹤的後續議題

| 議題 | 說明 | 追蹤連結 |
|------|------|----------|
| Issue #58826 | 內建 token budget / 請求數限制功能請求 | [GitHub](https://github.com/openclaw/openclaw/issues/58826) |
| Gemini caching/thinking 不支援 | 高票 bug，官方尚未給出修復時程 | [Issues · openclaw/openclaw](https://github.com/openclaw/openclaw/issues) |
| PR #94461 | Z.ai GLM-5 模型整合合併進度 | [Releases](https://github.com/openclaw/openclaw/releases) |
| `/fast auto` 穩定性 | 2026.6.10-beta.1 新功能，正式版前需觀察迴歸狀況 | [openclaw/openclaw releases](https://github.com/openclaw/openclaw/releases) |
| 大型 session `.jsonl` 清理機制 | 尚無官方自動清理工具，社群正在討論 workaround | — |

---

*本報告依據公開搜尋結果撰寫，不代表 OpenClaw 官方立場。如有資訊誤差請以官方 GitHub 及文件為準。*
