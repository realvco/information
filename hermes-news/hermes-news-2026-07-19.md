# Hermes-Agent 每日情報｜2026-07-19

## 1. 今日重點摘要

1. **Nous Research 啟動 7,500 萬美元 B 輪融資，估值達 15 億美元**（[TechCrunch](https://techcrunch.com/2026/07/13/hermes-agent-maker-nous-research-in-talks-for-new-funding-at-1-5b-valuation/)）：由 Robot Ventures 領投，USV 及其他知名投資人跟投；上一輪融資總計 7,000 萬美元，投資方包含 Paradigm、North Island Ventures、OSS Capital 及 Balaji Srinivasan。此輪融資尚未正式宣布關閉，但業界廣泛視此為對開源 agent 生態系的重大信心投票。
2. **Hermes-Agent 突破 21.4 萬 GitHub Stars**：截至本週，倉庫已累積逾 214,000 顆星、近 40,000 個 fork，根據 The Agent Report 的分析，自 2025 年底至今共釋出 22 個版本，為開源 agent 領域的最大規模倉庫之一。
3. **Skill Curator 預設行為調整**：近期版本更新後，技能策展器（Skill Curator）在預設情況下仍會修剪過時技能，但 LLM 驅動的整合（consolidation）階段已改為 **opt-in**；一般背景例行策展不再消耗任何 token，對高頻使用者來說可顯著降低 API 費用。
4. **Issue #38804：Gemini 暫時性速率限制被誤判為「配額耗盡」**（[連結](https://github.com/NousResearch/hermes-agent/issues/38804)）：使用者反映 Hermes 在收到 HTTP 429 並附有「將於 N 秒後重設」訊息時，錯誤地回報為「Gemini quota exhausted」（配額用盡），而非短暫速率限制；此問題屬 P2（中優先），標記「狀態降級但有暫時繞行方式」，官方尚未提供修復版本，手動重試為目前建議做法。
5. **最新穩定版 v0.18.2（2026-07-07）持續運行**：此版主要修復 WhatsApp Baileys bridge 從釘頂 Git commit 改為正式 npm 版本的相依問題，Docker image 建置穩定性改善；v0.18.1 同日補丁整合約 660 個 PR，含 Windows 安裝自我修復、Dashboard / Gateway 穩定性強化。
6. **桌面版（v0.16.0+）新功能持續受到社群採用**：可重新綁定的快速鍵（rebindable keyboard shortcuts）、原生系統通知（可個別切換通知類型）、以及「子代理即時觀察視窗」（live subagent watch-windows，在獨立窗格串流委派代理的執行過程）是近期討論最熱烈的功能。
7. **技能生態系規模爆炸性成長**：Skills Hub 社群貢獻技能數量已突破 90,000 個，自 v0.18.0 發布後的 40 天內從數百個成長至近十萬個可重用 agent 行為，成為 Hermes 與其他 agent 框架差異化的重要護城河。
8. **r/LocalLLaMA：Hermes 原生模型 vs Claude/GPT 工具使用之爭仍在延燒**：社群持續比較 Hermes 3/4 原生 function-calling 格式與 Claude、GPT 在 Hermes Agent 工作流中的工具使用準確性，多數測試者認為 Hermes 原生模型在工具呼叫穩定性上仍具優勢，但 Claude 知識廣度與多語言能力較強。
9. **v0.19.0 尚未公告**：自 v0.18.0（7 月 1 日）、v0.18.1、v0.18.2（7 月 7 日）快速迭代後，目前 GitHub 尚無 v0.19.0 的功能預覽或 milestone，後續方向待關注。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）
- **訂閱 OAuth 仍缺失（Issue #25267）**（[連結](https://github.com/NousResearch/hermes-agent/issues/25267)）：使用 Claude Pro / Max 訂閱的使用者在 Hermes 中仍需另行取得 Developer API Key；即便 Anthropic 已推出獨立 Agent SDK 月度額度，Hermes 目前無法透過此額度路由請求。此 issue 持續獲得社群 +1，Nous Research 尚未給出時程。
- **ClaudeOAuth 繞行方式**：目前社群建議透過 Anthropic 直接 API、OpenRouter（支援 fallback chain）或相容 proxy 設定；OpenRouter 在 Claude 服務不穩定時可自動切換至備用 provider。
- **cache-read token 不計入 ITPM**（[來源](https://byteiota.com/claude-api-july-2026-rate-limits-key-expiry/)）：Anthropic 近期調整後，快取讀取 token 不再計入輸入 token 速率限制（ITPM），對在 Hermes 中使用長系統提示或技能文件的使用者來說，實際可用額度有所提升。

### GPT（OpenAI）
- **最廣泛支援，Codex OAuth 免額外費用**：GPT-5.4、GPT-5-codex、GPT-4.1、GPT-4o 均受支援；OpenAI 訂閱者可透過 Codex OAuth 憑證池無需額外 API Key 即可串接。
- **工具呼叫適合通用任務**：社群評測中 GPT 系列在廣泛知識問答與通用任務表現穩定，但在 Hermes 特有的多步驟工具鏈中，部分使用者反映工具選擇準確性稍遜於 Hermes 原生模型。

### Gemini（Google）
- **短暫速率限制誤判問題（Issue #38804）**（[連結](https://github.com/NousResearch/hermes-agent/issues/38804)）：Hermes 將 HTTP 429 含「N 秒後重設」的回應誤判為配額用盡，導致使用者以為需要升級方案或等待很長時間，實際上僅需等待數十秒即可重試。建議暫時手動補充重試邏輯或使用 OpenRouter 作為中間層，讓 OpenRouter 處理 Gemini 的速率限制回應。
- **輔助 LLM 角色**：Gemini Flash 仍是 Hermes 輔助功能（視覺、摘要、壓縮、記憶刷新）的預設選項，透過 OpenRouter 自動偵測與切換。

### 速率限制與 Token 踩坑
- **單回合多次 LLM 呼叫堆疊**（[來源](https://markaicode.com/errors/hermes-agent-rate-limit-exceeded-fix-production/)）：單次 Hermes 回合通常包含 3–8 次 LLM 呼叫（工具決策 → 工具執行 × N → 最終合成），生產環境中有案例顯示 40% 請求因速率限制失敗；導入 token bucket 節流後降至 0%。
- **Context Window 溢出仍是常見崩潰原因**（[Issue #57275](https://github.com/NousResearch/hermes-agent/issues/57275)）：系統訊息 + 對話歷史 + tool schemas + tool results 累加容易超出模型上下文視窗，建議明確設定較大的 context window 並啟用對話歷史壓縮。
- **HTTP 402 計費錯誤誤分類（Issue #32420）**（[連結](https://github.com/NousResearch/hermes-agent/issues/32420)）：HTTP 402（billing/spend limit 超過）被 Hermes 誤判並給出錯誤建議，此問題與 Gemini 429 誤判類似，均屬上游錯誤碼解析不精確的同一類問題。

---

## 3. 社群反應與討論亮點

- **$1.5B 估值新聞引發廣泛討論**：TechCrunch 報導在 AI 社群引發大量轉發，多位開源 AI 倡導者認為這是「開源 agent 模式商業化可行性」的重要里程碑；另有部分聲音擔心大規模融資是否會改變 Hermes 目前 MIT 授權的開放政策。
- **Skills Hub 規模討論**：9 萬個社群技能的爆炸性成長讓不少人對「技能品質把關」產生疑慮，Discord 中有討論串提議增加技能評分與審核機制。
- **Skill Curator token-free 預設受到歡迎**：高頻使用者在 Reddit 和 Discord 對此改動給予正面回饋，認為大幅降低了「背景維護成本」。
- **r/LocalLLaMA：MoA 實際效能回報累積中**：v0.18.0 引入的 Mixture-of-Agents 功能吸引了多篇 benchmark 分享，結果喜憂參半——高難度推理任務中 MoA 表現優於單一模型，但 token 成本也成倍增加，成本效益比是討論焦點。
- **Anthropic 估值對比**：有趣的是，Nous Research 以「開源 agent」定位追求 15 億估值，同一週 Anthropic 的代理能力也在持續擴張，社群對兩者定位差異有不少討論。

---

## 4. 值得追蹤的後續議題

| 議題 | 說明 |
|------|------|
| **Nous Research B 輪融資正式關閉** | 目前仍在「finalizing」階段，正式宣布後的戰略方向（特別是 MIT 授權政策）值得密切追蹤 |
| **Issue #38804：Gemini 速率限制誤判** | P2 優先，有暫行繞行方式，但正式修復需要改善錯誤碼解析邏輯，尚無 fix PR |
| **Issue #25267：Claude 訂閱 OAuth** | 社群支持度持續累積，Nous Research 是否會在新一輪融資後加速對接 Anthropic Agent SDK 額度 |
| **v0.19.0 功能方向** | v0.18.x 系列快速迭代後，下一個 minor 版本的主軸功能尚未公告 |
| **Skills Hub 品質治理機制** | 9 萬技能的快速成長帶來品質把關需求，評分或審核機制的討論值得追蹤 |
| **MoA 實際任務效能評測累積** | Mixture-of-Agents 已上線，社群真實任務 benchmark 持續產出，成本效益比是關鍵評估維度 |

---

*資料來源：[techcrunch.com](https://techcrunch.com/2026/07/13/hermes-agent-maker-nous-research-in-talks-for-new-funding-at-1-5b-valuation/) · [github.com/NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent) · [github.com/NousResearch/hermes-agent/issues/38804](https://github.com/NousResearch/hermes-agent/issues/38804) · [github.com/NousResearch/hermes-agent/issues/25267](https://github.com/NousResearch/hermes-agent/issues/25267) · [the-agent-report.com](https://the-agent-report.com/2026/07/nous-research-hermes-agent-1-5-billion-funding-july-2026/) · [releasebot.io](https://releasebot.io/updates/nousresearch/hermes-agent) · [markaicode.com](https://markaicode.com/errors/hermes-agent-rate-limit-exceeded-fix-production/) · [byteiota.com](https://byteiota.com/claude-api-july-2026-rate-limits-key-expiry/)*
