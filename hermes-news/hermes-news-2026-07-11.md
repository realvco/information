# Hermes-Agent 每日情報 — 2026-07-11

> 資料來源：GitHub、Releasebot、X（前 Twitter）、社群論壇；涵蓋範圍以過去 24 小時內可索引的公開資訊為主。

---

## 1. 今日重點摘要

1. **v0.18.1 與 v0.18.2 Patch 已發布（7 月 7 日）**：距本報告撰寫約 4 天，v0.18.1 彙整了 v0.18.0（7 月 1 日）以來約 660 個 PR，v0.18.2 為同日補丁，修復 WhatsApp Baileys dependency 問題（影響 tagged-release Docker build）。今日（7 月 11 日）尚未出現新的正式 release，但倉庫仍持續活躍。
2. **「The Judgement Release」v0.18.0 標誌重大里程碑**：7 月 1 日發布，清空 100% 的 P0 / P1 issue（約 700 項高優先問題），共合入 ~1,720 commits、998 個 PR，涉及 2,215 個檔案。
3. **Mixture-of-Agents（MoA）成為一級 model**：MoA ensemble 可在所有 model picker 中直接選取，與 Claude、GPT、Grok 並列；各參考模型輸出獨立顯示，最終答案改為串流輸出。
4. **Agent 驗證引擎（Verification Engine）**：coding 工作現在記錄驗證證據，`/goal` 命令支援「完成合約（completion contract）」，agent 依據此合約自我判斷是否已完成任務。
5. **`/learn` 新命令**：可指向目錄、URL 或工作流，讓 Hermes 自動提煉為可重用技能（skill）。
6. **Google Vertex AI 成為一級 provider**：Hermes 自動 mint 並刷新 OAuth2 access token，組織使用 GCP service account 即可無縫串接 Gemini，不再需要手動貼 token 或擔心 mid-session 失效。
7. **安全強化**：本輪修補了 MCP config 持久化攻擊面、cron base_url 覆蓋洩漏 provider 憑證、Slack xapp- token 遮罩、aiohttp CVE floor 等多個安全問題。
8. **v0.18.1 修復亮點**：Windows installer/updater 自癒機制、dashboard 與 gateway 修復、WhatsApp dashboard pairing、MCP 與多個 provider 修復。
9. **社群規模**：GitHub Stars 達 188,000，社群最活躍的討論管道為 Nous Research 官方 Discord（agent 頻道），X / Twitter 速度快但討論深度較淺。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）

- 支援四種接入方式：直接 API key、`hermes auth add anthropic`（OAuth）、OpenRouter、自訂 compatible proxy。
- 目前無過去 24 小時內的新踩坑報告；社群普遍視 Claude 為 Hermes 在 coding 與 long-context reasoning 任務的首選。
- 來源：[AI Providers | Hermes Agent Docs](https://hermes-agent.nousresearch.com/docs/integrations/providers/)

### GPT（OpenAI） / GitHub Copilot

- Hermes 可透過 GitHub Copilot 訂閱接入 GPT-5.x 及 Claude、Gemini 等模型（使用 Copilot API）。
- v0.18.1 對 Windows 平台的 installer/updater 進行自癒修復，間接影響使用 Copilot 方案的 Windows 使用者體驗。
- 來源：[Hermes Agent Updates — Releasebot](https://releasebot.io/updates/nousresearch/hermes-agent)

### Gemini（Google）

- **Vertex AI 整合**（v0.16.0 起正式支援）：透過 GCP service account，Hermes 自動處理 OAuth2 token lifecycle，不再需要手動管理 token expiry；mid-session 失效問題已解決。
- **直接 API** 亦支援（`gemini` provider 或 OpenRouter）。
- 社群對 Vertex 整合評價正面，特別是企業用戶對 token 自動刷新功能有需求。
- 來源：[Release Hermes Agent v0.18.0 — GitHub](https://github.com/NousResearch/hermes-agent/releases/tag/v2026.7.1)

### Mixture-of-Agents（MoA）

- MoA 本身作為虛擬 model 使用，可在 model picker 選擇，底層可混搭 Claude、GPT、Grok 等多家模型。
- 各模型輸出改為即時串流，不再等待所有模型完成後才顯示，實際使用體感明顯改善。
- 來源：[Teknium on X — Hermes v0.18.0 announcement](https://x.com/Teknium/status/2072416088785359189)

### Provider 穩定性比較（社群觀察）

| Provider | 接入方式 | 穩定性 | 備註 |
|---|---|---|---|
| Claude（直接） | API key / OAuth | 高 | 長上下文推薦 |
| GPT（Copilot） | Copilot subscription | 高 | 企業方案常用 |
| Gemini（Vertex AI） | GCP service account | 高（新） | 自動 token 刷新 |
| Gemini（直接 API） | API key | 中 | 視 quota 而定 |
| OpenRouter | 代理 | 中 | 靈活但有額外延遲 |

---

## 3. 社群反應與討論亮點

- **X（前 Twitter）**：NousResearch 官方帳號在 v0.18.0 發布時有推文，Teknium 的 announcement 貼文獲得社群廣泛轉發；討論焦點集中在 MoA 功能與 Verification Engine 的實際效用。
  - 來源：[Teknium on X](https://x.com/Teknium/status/2072416088785359189) / [NousResearch on X](https://x.com/NousResearch/status/2075271466703106384)
- **官方 Discord**：agent 頻道有 1,175 個討論串、8,030+ 則訊息，是最即時的問題反饋與 config 分享管道；v0.18.x 發布期間 bug 回報與 model 選擇討論最為熱絡。
- **r/LocalLLaMA**：討論集中在本地模型效能、tool-use 可靠性、self-hosting 成本；MoA 功能受到關注，有人討論不同 ensemble 組合的實際輸出品質差異。
- **LinkedIn**：NousResearch 官方頁面發布 v0.18.0 長文介紹，觸及企業受眾。
  - 來源：[NousResearch LinkedIn — v0.18.0](https://www.linkedin.com/posts/nousresearch_hermes-agent-v0180-the-judgement-release-activity-7478476474526916608-Bul4)
- **社群整體觀感**：Judgement Release 的「清空所有 P0/P1」策略獲得正面反響，被認為顯示出專案的成熟度；`/learn` 命令與 MoA 是最受討論的新功能。

---

## 4. 值得追蹤的後續議題

1. **v0.18.x 系列後續 patch**：v0.18.0 到 v0.18.2 僅花 6 天，觀察接下來是否有更多 patch 或直接推進至 v0.19。
2. **MoA 實際工作負載表現**：MoA 作為一級 model 上線後，社群的 benchmark 與真實工作流測試結果值得持續追蹤。
3. **Agent Verification Engine 採用情況**：`/goal` 完成合約機制對 coding agent 的實際影響，以及誤判（過早或過晚認定完成）的 edge case 回報。
4. **安全修補後續**：本輪安全強化涉及 MCP config、cron base_url、Slack token redaction、aiohttp CVE 等，升級建議是否發布、是否影響舊版相容性值得確認。
5. **`/learn` 技能提煉機制**：此功能屬新引入，社群尚無大量實測回饋，未來使用案例與 edge case 值得累積觀察。

---

*本報告依據公開搜尋結果彙整，不含臆測或補充資訊。如需查閱原始 Issue / PR，請至 [github.com/NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent)。*
