# OpenClaw 每日情報｜2026-07-19

## 1. 今日重點摘要

1. **v2026.7.2-beta.2 持續推進**：v2026.7.1 正式版釋出後不到 48 小時即推出 beta.1，目前已迭代至 beta.2，主軸為遠端編碼工作階段（Remote Coding Sessions）、雲端 Worker 節點支援、引導式 Control UI 設定、以及 Linux／Windows 安裝強化；本版也針對 v2026.7.1 遷移後出現的崩潰問題提供修復。
2. **Issue #110590（July 18）：工作階段狀態飄移與損毀**：由使用者 K2Mn 於 7 月 18 日提報，症狀為 session、memory、transcript、context 或 agent state 在長時間運行後出現飄移或損毀，ClawSweeper 標記需補充更多重現資訊，目前已連結一個開放的 pull request。
3. **Issue #110586（July 18）：agent 合成媒體 payload 問題**：由社群活躍用戶 steipete 提報，涉及 agent 中僅含合成媒體的 payload 處理，ClawSweeper 同樣標記「需更多資訊」，尚在確認根因。
4. **安全漏洞 CVE-2026-25253（CVSS 8.8）仍值得關注**：1 月底揭露的嚴重漏洞允許攻擊者透過 URL 參數竊取 auth token 並取得執行個體完整控制權，研究人員發現目前仍有逾 3 萬個 OpenClaw 執行個體暴露於公開網際網路；如尚未升級至含安全修補的版本，應優先處理。
5. **v2026.7.2 預覽：遠端程式碼工作階段**：使用者可在雲端 Worker 上執行 Control UI 工作階段，直接在 Worker 所在主機的終端機中開啟 Codex 與 Claude 目錄工作階段，並在終端機內恢復 OpenCode 與 Pi 工作階段，大幅降低本機依賴。
6. **UTF-8 串流解析修正（近期 PR）**：有 PR 針對 ChatGPT 串流回應中的 UTF-8 處理提出修正，顯示在使用非 ASCII 語言（含中文、日文）進行多輪工作流時，仍有潛在亂碼風險。
7. **Gemini 支援缺口持續未解**：caching、embeddings、thinking 功能在 OpenClaw 中仍無法正常運作，官方未公告修復時程；社群繼續以 Claude 或 GPT 為主要 provider。
8. **專案整體規模**：截至目前，OpenClaw 主倉庫仍有約 4,000 個 open issues 與 3,000 個 open PRs，v2026.7.1 版本共匯聚 3,063 次貢獻，來自 532 名貢獻者，說明社群規模龐大但議題積壓量也相當顯著。
9. **ClawRouter 預算管理穩定上線**：v2026.7.1 新增的 ClawRouter provider 已在實際部署中被使用者採用，單一 API key 可對應多個授權模型，並在狀態視圖中呈現請求次數、token 用量、費用與月度預算。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）
- **Claude Sonnet 5 整合路徑**（[來源](https://docs.openclaw.ai/concepts/model-providers)）：v2026.7.1 起可透過 Anthropic 直接 API、Claude CLI、Vertex AI 區域、Bedrock 推論設定檔（inference profile）及 Bedrock Mantle 選用 Claude Sonnet 5 / Mythos 5；ClawRouter 亦支援 Anthropic 原生傳輸協定。
- **工具呼叫穩定性最佳**：社群評測持續顯示 Claude 在 OpenClaw 內的 tool calling 成功率最高，MCP 原生支援讓企業安全合規稽核相對容易。
- **速率限制踩坑**：在緊密循環的 agentic 任務中（如批量爬取、多步驟分析），RPM 與每日 token 上限仍是主要瓶頸；建議於請求間加入指數退避（exponential backoff）或採用 token bucket 節流，避免整批任務失敗。

### GPT（OpenAI）
- **GPT-5.6 相容性強化**：v2026.7.1 針對 OpenAI 與 Codex 路由的 GPT-5.6 相容性做了改善；Sol、Terra 啟用 `/think`（Ultra 模式），Luna 啟用 `max` 模式；新安裝預設選用 GPT-5.6。
- **UTF-8 串流問題**（見近期 PR）：部分使用者反映 ChatGPT 串流在處理多位元組字元時有截斷現象，相關修正 PR 已提交但尚未合併。

### Gemini（Google）
- **已知功能缺口**（[來源](https://help.apiyi.com/en/openclaw-gemini-image-recognition-fix-openai-compatible-mode-guide-en.html)）：在 OpenAI 相容模式下使用 Gemini 做圖片辨識時，根源問題為 JSON Schema 欄位不相容，而非 Gemini 本身視覺能力的問題。若需使用 Gemini 圖片功能，建議改用 Gemini 原生格式設定，繞過 OpenAI 相容層。
- **版本要求**：需確認執行 OpenClaw 2026.2.21 或更新版本，否則 Gemini 3.1 模型參考無法被識別。

### Ollama / 開源模型
- **最常見設定失敗原因**（[來源](https://www.getopenclaw.ai/help/switching-models-provider-config)）：自訂 provider 遺漏 `baseUrl` 欄位是最常見的模型切換失敗原因；其次為 Gateway 未在設定更改後重啟，以及舊工作階段快取了舊模型設定。

---

## 3. 社群反應與討論亮點

- **Issue #110590 受到追蹤**：工作階段狀態損毀問題被社群視為長時間自主運行的核心痛點，已有使用者在討論串分享重現步驟，期待 v2026.7.2 正式版能帶來修復。
- **Remote Coding Sessions 引發期待**：beta.2 中的雲端 Worker 功能讓「在遠端主機上執行 Codex 或 Claude 工作階段」成為可能，社群對此功能評價正面，尤其是 VPS 部署的使用者。
- **CVE-2026-25253 安全討論**：部分安全研究人員在 X.com 與論壇繼續提醒 3 萬多個暴露執行個體的風險，強調應避免將 OpenClaw 直接暴露於公開 IP 而不設防火牆規則。
- **4000 open issues 現象討論**：有社群成員在 Discord 討論 OpenClaw 目前積壓的 issue 數量，認為 ClawSweeper 自動化分診工具雖然有助加速，但仍需更多人力投入核心 bug 修復。
- **Gemini 功能缺口聲浪持續**：多個論壇帖子希望 Gemini caching 與 embeddings 問題盡快獲得官方回應，部分開發者已選擇透過外部 proxy 繞行。

---

## 4. 值得追蹤的後續議題

| 議題 | 說明 |
|------|------|
| **v2026.7.2 正式版釋出時程** | beta.2 已上線，遠端編碼工作階段與雲端 Worker 為核心功能，正式版預計近期釋出 |
| **Issue #110590：工作階段狀態損毀** | 長時間自主任務的穩定性關鍵，ClawSweeper 標記需補充重現資訊，待 PR 合併 |
| **CVE-2026-25253 修補覆蓋率** | 仍有 3 萬多個公開暴露執行個體，升級合規率值得持續關注 |
| **Gemini 功能修復時程** | caching / embeddings / thinking 均未支援，官方尚未公告修復計畫 |
| **UTF-8 串流 PR 合併進度** | 多語言工作流的穩定性依賴此修復，預計在 v2026.7.2 正式版或 beta.3 中落地 |
| **ClawRouter 在生產環境的實際表現** | credential-scoped 動態模型發現功能剛正式上線，社群真實費用與穩定性回報尚在累積 |

---

*資料來源：[docs.openclaw.ai/releases/2026.7.1](https://docs.openclaw.ai/releases/2026.7.1) · [github.com/openclaw/openclaw/releases](https://github.com/openclaw/openclaw/releases) · [github.com/openclaw/openclaw/issues](https://github.com/openclaw/openclaw/issues) · [releasebot.io](https://releasebot.io/updates/openclaw) · [help.apiyi.com](https://help.apiyi.com/en/openclaw-gemini-image-recognition-fix-openai-compatible-mode-guide-en.html) · [getopenclaw.ai](https://www.getopenclaw.ai/help/switching-models-provider-config) · [petronellatech.com](https://petronellatech.com/blog/openclaw-ai-agent-guide-2026/)*
