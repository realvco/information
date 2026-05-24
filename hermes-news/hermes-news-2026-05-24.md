# Hermes-Agent 每日情報 — 2026-05-24

> 搜尋範圍：過去 24 小時；資料截止時間 UTC 2026-05-24

---

## 1. 今日重點摘要

1. **v0.14.0「The Foundation Release」（2026-05-16）為目前最新穩定版**：808 次 commit、633 個合併 PR、1,393 個檔案變更、165,061 行新增，545 個 issue 關閉（含 12 個 P0、50 個 P1）。這是 NousResearch 迄今規模最大的單一版本。
2. **Computer-use（cua-driver）開放非 Anthropic 模型**：任何具備視覺能力的模型現在均可驅動桌面操作，不再綁定 Claude。此改動大幅降低 computer-use 功能的供應商門檻。
3. **xAI Grok 成為原生 SuperGrok OAuth 供應商**：grok-4.3 上下文視窗擴至 1M tokens；同時新增 OpenAI 相容本地 proxy，可將任何已 OAuth 認證的 Hermes 供應商（Claude Pro、ChatGPT Pro、SuperGrok）轉成標準端點，供 Codex / Aider / Cline / Continue 直接存取。
4. **Microsoft Teams 與 LINE / SimpleX Chat 上線**：Teams 完整整合（Graph auth + webhook + pipeline runtime）；LINE 透過 Messaging API 原生接入；隱私優先的 SimpleX Chat 亦成為第一類平台，Hermes 現支援共 22 個訊息平台。
5. **LSP 語義診斷即時回饋**：每次 write_file / patch 操作後，Hermes 自動對修改的檔案執行語言伺服器診斷，並在下一輪對話前將錯誤回傳給 Agent，提升程式碼品質閉環。
6. **HuggingFace Skills Hub 預設整合**：HuggingFace/skills 已內建於 Skills Hub，無需額外設定即可安裝發布的社群技能。
7. **9 個新可選技能發布**：包含 Hyperliquid、Yahoo Finance、api-testing、統一 EVM 多鏈、darwinian-evolver、osint-investigation、pinggy-tunnel、watchers，以及全面重構的 Notion 整合。
8. **v0.13.0「The Tenacity Release」（2026-05-07）Kanban 多 Agent 排程**：引入具心跳偵測、殭屍任務自動回收、per-task 重試的 Kanban 持久化多 Agent 看板；`/goal` 指令使 Agent 在多輪對話中鎖定目標不偏移。
9. **OpenRouter max_tokens 收費 bug 持續未修（Issue #22879）**：Hermes 將 max_tokens 硬編碼為模型最大值（如 Claude Sonnet/Haiku 4.5 的 64,000），觸發 OpenRouter 402 錯誤（預先保留全額 token 費用）。此問題在 v0.13.0 後仍開放中。

---

## 2. LLM 串接實戰回報

### 2-1 模型推薦（2026 年 5 月社群共識）

| 模型 | 定價（輸入/輸出，每百萬 token） | 推薦原因 | 來源 |
|------|-------------------------------|---------|------|
| Claude Sonnet 4.6 | $3 / $15 | 推理品質與工具呼叫最穩定，P1 issue 比例最低 | [Hermes Providers Docs](https://hermes-agent.nousresearch.com/docs/integrations/providers) |
| GPT-4.1 | $2 / $8 | 通用推理可靠，工具呼叫行為穩定 | [Hermes Providers Docs](https://hermes-agent.nousresearch.com/docs/integrations/providers) |
| Gemini 2.5 Pro（via OpenRouter） | $1.25 / $10 | 1M 上下文、強推理；原生 Google GenAI provider 仍在開發中 | [Remote OpenClaw Blog](https://www.remoteopenclaw.com/blog/best-gemini-models-for-hermes) |

### 2-2 已知踩坑紀錄

- **max_tokens 硬編碼觸發 OpenRouter 402（Issue #22879）**：OpenRouter 在呼叫前以 `max_tokens × 輸出費率` 預保留費用，Hermes 傳入 64,000 時即觸發餘額不足錯誤。暫解：以 per-profile config 覆寫 max_tokens 為實際需求值（官方 patch 尚未合併）。來源：[GitHub Issue #22879](https://github.com/NousResearch/hermes-agent/issues/22879)
- **輔助任務靜默使用付費模型（Issue #24029，2026-05-11 開啟）**：即使用戶只設定 OpenRouter `:free` fallback，title_generation、compression、vision 等輔助任務仍會硬編碼回退至 `google/gemini-3-flash-preview`（付費模型），造成非預期帳單。來源：[GitHub Issue #24029](https://github.com/NousResearch/hermes-agent/issues/24029)
- **費用報告只支援 OpenRouter（Issue #19469）**：定價層僅從 OpenRouter metadata API 取得資料，任何非 OpenRouter 路徑的部署均顯示 `estimated_cost: 0`，無法正確追蹤成本。來源：[GitHub Issue #19469](https://github.com/NousResearch/hermes-agent/issues/19469)
- **DeepSeek V4 Pro via OpenRouter 觸發 gateway 崩潰（Issue #16677）**：Io Net upstream 對 `deepseek/deepseek-v4-pro` 施加激進 rate limit，HTTP 429 引發 Hermes gateway crash loop，連帶 Telegram bot 失效。暫解：換用其他 DeepSeek 路由或改用 OpenRouter 其他供應商節點。來源：[GitHub Issue #16677](https://github.com/NousResearch/hermes-agent/issues/16677)
- **互動式 CLI 不自動 fallback Codex 429（Issue #20465）**：cron job 模式有 fallback chain，但互動式 CLI 工作階段遇到 `usage_limit_reached` 時不觸發相同邏輯，需手動切換 provider。來源：[GitHub Issue #20465](https://github.com/NousResearch/hermes-agent/issues/20465)
- **token 爆量實例**：社群 Discord 用戶回報「輕度使用 2 小時消耗 400 萬 token，光問天氣就用掉 21K token」；根本原因為 gateway 在 hermes-agent 目錄啟動時載入開發用 AGENTS.md 等垃圾資料，已在近期版本修正（改為從使用者 home 目錄啟動）。來源：[Hermes Troubleshooting Guide](https://www.getopenclaw.ai/blog/hermes-agent-troubleshooting)

---

## 3. 社群反應與討論亮點

- **v0.14.0 在 X.com 引發熱議**：Nous Research 官方帳號 [@NousResearch](https://x.com/NousResearch/status/2056110234309939330) 發文宣布，推文獲得大量轉發，社群對 computer-use 開放非 Anthropic 模型與 xAI Grok OAuth 反應最為熱烈。
- **Digg 報導規模數據**：[Digg 報導](https://digg.com/ai/1qruxedc) 強調 v0.14.0 的 808 commits / 633 merged PRs 規模，被多位開源關注者引用，讚許 NousResearch 的高頻交付節奏。
- **Discord forum thread 首則訊息 bug（Issue #12496）**：Discord 論壇頻道中，新建帖子的第一則訊息若未 @mention Hermes 則會被忽略；後續追蹤訊息則正常。此 bug 在社群中被多次回報，已有 workaround（強制 @mention 觸發）但官方尚未修復。
- **多 Agent Discord 協作功能請求（Issue #14853）**：多個專門 Hermes 實例共用頻道時，彼此看不見對方的訊息，無法協作且容易觸發回應連鎖；功能請求持續獲得 upvote。
- **OpenRouter 使用量統計翻轉（2026 年 5 月）**：社群觀察到 Hermes-Agent 在 OpenRouter 上的使用量呈現顯著增長，多名運營者分享配置心得與費用控制技巧，相關討論在 r/LocalLLaMA 持續活躍。

---

## 4. 值得追蹤的後續議題

| 議題 | 說明 | 追蹤連結 |
|------|------|---------|
| Issue #22879：max_tokens 可配置化 | OpenRouter 402 問題的根本修復，預計在 v0.15.x 解決，但尚未確認里程碑 | [GitHub Issue #22879](https://github.com/NousResearch/hermes-agent/issues/22879) |
| Issue #24029：輔助任務付費模型靜默 fallback | 影響所有只使用免費模型的用戶，5 月 11 日才開啟，修復優先級待確認 | [GitHub Issue #24029](https://github.com/NousResearch/hermes-agent/issues/24029) |
| Issue #12639：原生 Google / Vertex AI Provider | 繞過 OpenRouter 402 的長期解決方案，進度待 NousResearch 確認時程 | [GitHub Issue #12639](https://github.com/NousResearch/hermes-agent/issues/12639) |
| v0.15.0 發布計畫 | v0.14.0 後的下一個里程碑，社群期待 Kanban 多 Agent 穩定性與費用報告完整化 | [GitHub Releases](https://github.com/NousResearch/hermes-agent/releases) |
| Issue #14853：多 Agent Discord 協作 | 多實例協作的 history injection 與 cascade prevention，功能請求熱度持續上升 | [GitHub Issue #14853](https://github.com/NousResearch/hermes-agent/issues/14853) |

---

*本報告由自動化流程依據網路公開資訊彙整，不代表任何官方立場。搜尋結果無法核實的技術細節已保守標注。*
