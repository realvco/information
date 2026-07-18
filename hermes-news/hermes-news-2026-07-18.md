# Hermes-Agent 每日情報｜2026-07-18

## 1. 今日重點摘要

1. **v0.18.2 為目前最新穩定版**（2026-07-07）：此版修復了 WhatsApp bridge 的相依套件問題，改為從已發布的 npm 正式版安裝，解決安裝與 Docker image 建置失敗的問題。
2. **v0.18.0「Judgment Release」全面清零高優先問題**（2026-07-01）：此版在發布前解決了 repo 中所有開放的 P0 / P1 issues 與 PRs，涵蓋 3 個 P0 issues、8 個 P0 PRs、493 個 P1 issues、188 個 P1 PRs，共約 700 個最高優先項目在 12 天內全數關閉。
3. **Mixture-of-Agents（MoA）升格為一級模型**：v0.18.0 讓 MoA 成為可直接選用的「虛擬模型」，使用者可建立命名 ensemble，選用方式與單一模型相同，每個參考模型的推理過程均可見，最終答案即時串流輸出。
4. **`/learn` 指令上線**：可將目錄、URL 或操作流程提煉為可重用技能（reusable skill），大幅降低重複情境設定的成本。
5. **`/journey` 記憶時間軸可視化**：提供完整記憶與技能累積的時間軸，可在介面內直接編輯或刪除，桌面版搭配互動式放射狀記憶圖。
6. **`delegate_task` 支援背景平行子代理**：多個子代理可同時在背景執行，主對話不被阻塞；CLI / TUI 狀態列即時追蹤整體執行艦隊，全部完成後以單一合併回應呈現。
7. **桌面版新增 Projects 功能**：每個設定檔可建立專屬的程式碼專案，包含側邊欄、Coding Rail、Review Pane 及 Git worktree 管理。
8. **Issue #25267：請求支援 Claude 訂閱 OAuth 串接**（[連結](https://github.com/NousResearch/hermes-agent/issues/25267)）：目前使用 Claude 作為後端仍需額外的 Developer API Key，即便已有 Claude Pro / Max 訂閱亦然；社群提議建立類似 OpenAI Codex OAuth 的整合方式。
9. **v0.18.1 同週補丁**（2026-07-07）：整合約 660 個 PR 的修復與強化，包含安裝程式 / 更新工具在 Windows 上的自我修復、Dashboard 與 Gateway 修復、WhatsApp Dashboard 配對，以及大量穩定性改善。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）
- **功能缺口：訂閱 OAuth 仍未支援**（[Issue #25267](https://github.com/NousResearch/hermes-agent/issues/25267)）：截至目前，即使 Anthropic 自 2026-06-15 起已將 Agent SDK 用量與互動訂閱上限脫鉤，並推出獨立的 Agent SDK 月度額度，Hermes 仍無法透過此額度路由請求；使用 Claude 的用戶仍需在訂閱以外額外付費取得 Developer API Key。此 issue 目前處於開放狀態，社群期待官方建立 Claude 訂閱 OAuth provider 外掛。
- **直接 API / OpenRouter / Proxy 均支援**：Claude 可透過 Anthropic 直接 API、OpenRouter，或相容代理進行設定，並支援 fallback provider chain（主要 provider 故障時自動切換）。
- **Auxiliary LLM 消耗**：Hermes 內部將視覺、網頁摘要、壓縮、記憶刷新等功能委派給輔助 LLM（預設 Gemini Flash，透過 OpenRouter 自動偵測），使用 Claude 作為主模型時仍需留意輔助模型的 API 費用。

### GPT（OpenAI）
- **最廣泛支援**：GPT-5.4、GPT-5-codex、GPT-4.1、GPT-4o 等均受支援，Codex OAuth 憑證池讓 OpenAI 訂閱者無需額外 API Key 即可串接。
- **工具呼叫可靠性**：社群討論中，Hermes 自家模型（Hermes 3/4）搭配 Hermes Agent 被普遍認為工具使用準確性最高，GPT 系列則適合需要廣泛知識的通用任務。

### Gemini（Google）
- **輔助 LLM 角色**：Gemini Flash 為 Hermes 輔助 LLM（vision、摘要、壓縮等）的預設選擇，透過 OpenRouter 自動偵測；作為主要對話模型亦受支援。

### 速率限制與 Token 踩坑
- **多重 API 呼叫堆疊**（[來源](https://markaicode.com/errors/hermes-agent-rate-limit-exceeded-fix-production/)）：單次 Hermes 回合通常包含 3–8 次 LLM 呼叫（tool decision → tool execution × N → final synthesis），在生產環境中曾發生 40% 請求因速率限制失敗；導入 token bucket 節流後降為 0%，強烈建議部署節流機制。
- **Context Window 溢出**（[來源](https://markaicode.com/hermes-agent-token-limit-error-fix/)）：系統訊息 + 對話歷史 + tool schemas + tool results 合計容易超過預設的 2,048 token 上下文視窗，此為 Hermes 生產部署最常見的崩潰原因，建議明確設定較大的 context window 並考慮對話歷史壓縮。
- **Issue #57275：總是超過 context limit 導致模型崩潰**（[連結](https://github.com/NousResearch/hermes-agent/issues/57275)）：上述問題已有對應的 GitHub issue，社群正在追蹤修復進度。

---

## 3. 社群反應與討論亮點

- **v0.18.0 廣受注目**：共同創辦人 Teknium 在 X.com 發文介紹 Judgment Release（[連結](https://x.com/Teknium/status/2072416088785359189)），YouTube 上亦出現多支功能介紹影片，LinkedIn 官方貼文獲得大量互動。
- **MoA 功能引發熱議**：Mixture-of-Agents 成為討論焦點，尤其是「用自己建立的 ensemble 取代單一模型」的工作流程，r/LocalLLaMA 有多個討論串評比 MoA 與單一前沿模型的效能差異。
- **Hermes 模型 vs Claude/GPT 工具使用之爭**：r/LocalLLaMA 上持續有討論認為 Hermes 原生 function-calling 格式在 Hermes Agent 中表現最穩，Claude 與 GPT 雖然訓練資料更新，但在 Hermes 工作流中的工具使用準確率相對不穩定。
- **自架 vs 託管辯論**：r/selfhosted 上的討論結論趨向「若將自身時間成本計入，託管成本通常更划算」，但對資料主權有需求的使用者仍傾向自架。
- **Issue #25267 社群支持度高**：Claude 訂閱 OAuth 的請求在 GitHub 上已獲得多名使用者的 +1 支持，Nous Research 尚未給出明確時程回應。

---

## 4. 值得追蹤的後續議題

| 議題 | 說明 |
|------|------|
| **Issue #25267：Claude 訂閱 OAuth** | 能否讓 Pro/Max 訂閱者透過 Agent SDK 額度使用 Hermes，待官方回應 |
| **Issue #57275：Context limit 導致崩潰** | 長對話 / 複雜工具鏈的穩定性，是生產部署的核心痛點 |
| **MoA 在實際任務的效能評測** | Mixture-of-Agents 已上線，社群真實任務的效能回報尚在累積 |
| **v0.19.0 功能預覽** | v0.18.0/1/2 快速迭代後，下一個 minor 版本的方向值得關注 |
| **Anthropic Agent SDK 額度整合進度** | Anthropic 已開放獨立 Agent SDK 月度額度，第三方 agent（如 Hermes）何時能接入是關鍵時間點 |
| **Raft 代理網路整合（v0.17.0 新增）** | iMessage via Photon 與 Raft 代理網路整合的實際用例報告持續出現，值得追蹤成熟度 |

---

*資料來源：[github.com/NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent) · [hermesagents.net](https://hermesagents.net/blog/hermes-agent-v0-18-0-judgment) · [releasebot.io](https://releasebot.io/updates/nousresearch/hermes-agent) · [x.com/Teknium](https://x.com/Teknium/status/2072416088785359189) · [markaicode.com](https://markaicode.com/errors/hermes-agent-rate-limit-exceeded-fix-production/) · [github issue #25267](https://github.com/NousResearch/hermes-agent/issues/25267) · [github issue #57275](https://github.com/NousResearch/hermes-agent/issues/57275)*
