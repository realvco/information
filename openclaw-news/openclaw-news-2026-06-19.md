# OpenClaw 每日情報 — 2026-06-19

> 搜尋時間範圍：過去 24 小時（UTC 基準）
> 資料來源：GitHub Issues、發版紀錄、技術部落格、社群討論

---

## 1. 今日重點摘要

1. **穩定版維持 2026.6.8**：社群在 6 月 18 日將此日定為「可靠性校準日」，官方與社群均建議穩定環境繼續使用 2026.6.8（npm latest 已於 6 月 16 日更新至此版本），除非有特定修補需求否則不建議提前升至 alpha。
2. **2026.6.9-alpha 開放觀察**：執行長時間 agent 工作（長連線、排程任務、Telegram/Matrix 傳遞、自訂 skill）的使用者已被建議密切追蹤 2026.6.9-alpha 分支，以提前測試記憶體與狀態復原改善。
3. **Issue #94695 新開（6/18）**：由 dailytrap 提出，ClawSweeper 機器人隨即標記為「實作方向明確」，預計進入下一個 patch 候補清單。
4. **false provider cooldown 問題仍活躍（Issue #23996）**：OpenClaw 在非真實 rate limit 情境下仍將 provider 誤入 cooldown，hardcode 原因為 `rate_limit`，導致 OAuth token auth 下無限 cooldown，尚未合併修補。
5. **false 'API rate limit reached' 問題（Issue #32828）**：即使所有上游 API 正常，OpenClaw 仍在所有模型回報「API rate limit reached」，為已知 bug，workaround 為手動清除 provider 狀態快取。
6. **310,000+ GitHub stars**：從 1 月的 106,124 顆星成長至目前逾 31 萬，半年內成長約 193%；可用 skill 數從 2,857 增至 13,729（+380%），支援 LLM 從 12 增至 22。
7. **安全性漏洞修補（OSV 記錄）**：3 月已發布針對 OpenClaw 套件的漏洞修補，有使用舊版的部署建議確認已升版。
8. **`openclaw doctor --fix` 自動排查**：官方文件新增強調此指令可自動修復大多數認證與設定問題；`openclaw status --all` 可輸出完整診斷報告。

---

## 2. LLM 串接實戰回報

### GPT-5.5 (OpenAI Codex)
- **優勢**：在自主 agent 任務（Terminal-Bench 2.0 等基準測試）表現最強。
- **限制**：每次呼叫 context 須維持在 128K tokens 以下；超出後行為不穩定。
- **費用注意**：高 throughput 時費用顯著，建議搭配 fallback 至 Gemini 以控成本。
- 來源：[Best LLM for OpenClaw: Gemini 3.1 Pro vs GPT-5.5 vs Claude Opus 4.7 (2026)](https://dev.to/matthewrevell/best-llm-for-openclaw-gemini-31-pro-vs-gpt-55-vs-claude-opus-47-2026-3na4)

### Claude（Anthropic API）
- **優勢**：工具呼叫（tool-calling）可靠性最高、長 context 連貫性強、MCP 原生支援、通過企業安全審核的安全預設值。
- **已知問題**：OpenClaw 與 Anthropic Vertex AI 的整合在 2026.3.22 才正式加入，部分使用者反映 Vertex AI region 設定容易誤填，需對照 `openclaw models auth setup-token` 輸出值手動確認。
- 來源：[Setup OpenClaw with Claude & Gemini: Your Private 24/7 AI Agent](https://legacy.vertu.com/ai-tools/the-ultimate-guide-setting-up-openclaw-with-claude-code-and-gemini-3-pro/)

### Gemini 3.1 Pro（Google AI）
- **優勢**：最大 context 視窗、最低每 job 成本、免費開發者 tier、原生多模態支援，被多數評測列為 OpenClaw「預設首選」。
- **實戰提醒**：免費 tier 有 RPM（每分鐘請求數）上限，agent 密集排程時容易觸發 429；建議在 `config.yaml` 中設定 `retry_on_429: true` 並調低並行工作數。
- 來源：[OpenClaw April 2026: 6 Model Providers You Can Now Swap at Runtime Without Rebuilding](https://www.mindstudio.ai/blog/openclaw-april-2026-model-agnostic-provider-manifest)

### Rate Limit / Auth 錯誤常見修法
- **429 rate limit**：OpenClaw 內建 exponential backoff 有已知 bug，建議在應用層自行實作 retry 或設 fallback model。
- **401/403 auth 失效**：OAuth token 到期後即使底層 API key 仍有效也會觸發，執行 `openclaw models auth setup-token` 重新設定後清除快取可解。
- 來源：[OpenClaw API Key Errors and Rate Limiting: Complete Troubleshooting Guide (2026)](https://www.aifreeapi.com/en/posts/openclaw-api-key-error-troubleshooting-guide)、[How to Fix OpenClaw 'API Limit Reached' Error](https://docs.bswen.com/blog/2026-03-24-openclaw-api-limit-error/)

---

## 3. 社群反應與討論亮點

- **ClawSweeper 自動化 triage**：社群高度評價此機器人對 issue/PR 的每週自動掃描與建議關閉功能，有效降低 maintainer 維護負擔；Issue #94695 在提出後數小時即獲得初步分類，社群認為回應速度明顯優於其他開源 agent 專案。
- **模型選擇討論熱烈**：GitHub 與部落格上出現多篇 GPT vs Claude vs Gemini 的實測比較，社群普遍認為「沒有單一最佳答案」，應依工作類型（自主 agent 任務 → GPT-5.5；長文件摘要/程式碼審查 → Gemini；企業合規工具整合 → Claude）分工搭配。
- **Provider manifest 廣受好評**：4 月引入的「不重建即可切換模型 provider」功能被社群稱為 2026 上半年最受歡迎的架構更新，討論串中多人表示此功能終於讓 OpenClaw 真正達到「模型無關（model-agnostic）」的設計目標。
- **Telegram 結構化渲染**：2026.6.8 新增的 Markdown 表格、可折疊引用區塊在 Telegram 上的渲染效果受到使用者正面回饋，被認為大幅提升了 agent 回報長篇分析結果的可讀性。

---

## 4. 值得追蹤的後續議題

1. **Issue #23996（false provider cooldown）**：此 bug 影響所有使用 OAuth 認證的 provider，PR 草稿已存在，等待 review；預計在 2026.6.9 正式版修復。
2. **Issue #32828（false rate limit on all models）**：成因仍未完全釐清，可能與 provider 狀態快取機制有關；值得持續追蹤是否影響最新版本。
3. **2026.6.9 正式版時程**：alpha 分支已有多個重要修補，社群預期 6 月底前正式推出。
4. **ClawHub 與 skill 生態系**：可用 skill 數持續快速增長（現已逾 13,000），是否出現 skill 品質管控機制為社群關注焦點。
5. **MiniMax M2.70 與 GLM-5.00 新增支援**：兩者於 3 月版本後正式支援，實戰評測資料仍稀少，值得追蹤使用者回報。

---

*本報告依據公開搜尋結果撰寫，部分細節以搜尋結果摘要為準；若需確認原始資訊請點擊來源連結查閱。*
