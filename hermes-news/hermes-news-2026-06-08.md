# Hermes-Agent 每日情報｜2026-06-08

> 資料截止時間：2026-06-08 UTC｜來源：GitHub、X.com、官方部落格、社群論壇

---

## 1. 今日重點摘要

1. **v0.16.0「The Surface Release」（2026-06-05）為當前最新穩定版**，本日（6/8）無新版本發布；該版本共合併 542 個 PR、關閉 399 個 issue，為近期最大幅度更新。
2. **Hermes Desktop 正式上線**：原生跨平台桌面應用（macOS / Linux / Windows）支援一鍵安裝、應用內自動更新、拖放檔案、多 Profile 並行、遠端 gateway OAuth 連線。
3. **Web 管理後台全面擴充**：新增 MCP 目錄、訊息渠道、憑證管理、Webhook、記憶體管理、OIDC 與帳密登入等模組，完整取代 CLI-only 配置流程。
4. **安全修補**：修復 CVE-2026-48710（Starlette pin）、SSRF off-loop 強化、子程序憑證遮蔽，v0.16.0 包含 16 個 security 標籤 issue 的修復。
5. **6/7 新增 GitHub bug report 群**：issues #41444（config 系統與 CLI 入口）、#41448（CLI 入口）、#41454（gateway runner / session dispatch）於過去 24 小時內被提出。
6. **Nous Portal 正式成為推薦入口**：首次設定流程納入「Quick Setup via Nous Portal」，統一 OAuth 授權可存取 300+ 前沿模型，取代分散 API key 管理。
7. **模糊搜尋 Model Picker 全面普及**：桌面版、Web 版、TUI、CLI 均已支援模型 fuzzy search，顯著改善多模型切換體驗。
8. **/undo 指令上線**：可撤銷最後 N 輪對話，降低長工作流中操作失誤的成本。
9. **NVIDIA Skills Hub 正式加入 trusted taps**：agent 可直接從 NVIDIA 技能庫自動安裝技能，無需手動配置。

---

## 2. LLM 串接實戰回報

### 2.1 Token 限制問題

- **`max_tokens` 配置無效**（[Issue #4404](https://github.com/NousResearch/hermes-agent/issues/4404)）：`config.yaml` 中的 `model.max_tokens` 設定不生效，根因為 `self.max_tokens` 為 `None`，API 請求中未帶入 `max_token` 欄位。目前 workaround 為手動注入環境變數或直接修改 provider 配置。
- **Ollama 本地模型預設 context 過短**：Hermes 3 的 `num_ctx` 預設僅 2,048 tokens，搭配多工具的工作流在 10 輪以內即會溢出。修復方式：在 Ollama 配置中手動設定 `num_ctx: 32768` 以上。（參考：[Hermes Agent Token Limit Error Fix — MarkAICode](https://markaicode.com/hermes-agent-token-limit-error-fix/)）

### 2.2 Rate Limit 問題

- **被動式 429 處理，未預先節流**（[Issue #5449](https://github.com/NousResearch/hermes-agent/issues/5449)）：目前 Hermes-Agent 在收到 429 回應後才觸發退避邏輯，Anthropic、OpenAI 等 provider 回傳的 `X-RateLimit-Remaining` / `X-RateLimit-Reset` 標頭均被忽略。
- **RPM 預先節流功能請求**（[Issue #7489](https://github.com/NousResearch/hermes-agent/issues/7489)）：社群要求根據 `x-ratelimit` 回應標頭主動限制請求頻率，尚在開發中。
- NVIDIA Developer Forums 有使用者申請將 Hermes-Agent 測試環境升至 200 RPM（[NVIDIA 論壇](https://forums.developer.nvidia.com/t/rate-limit-issue-can-i-get-an-upgrade-to-200-rpm-for-hermes-agent-testing/370383)）。

### 2.3 串流與 Auth 問題

- **Nous 推理 API 串流超時**（[Issue #29418](https://github.com/NousResearch/hermes-agent/issues/29418)）：在 agent 規模的 payload 下串流請求頻繁超時，非串流模式正常。目前建議大型任務強制使用非串流。
- **Hot memory 與停用工具狀態異常**（[Issue #26568](https://github.com/NousResearch/hermes-agent/issues/26568)）：最新版更新後出現 agent 忽略 hot memory、使用已停用工具的問題，根因仍在排查中。

### 2.4 各 LLM Provider 效能比較

| 模型 | 工具呼叫穩定性 | 費用（輸入/輸出 per 1M tokens） | 備註 |
|---|---|---|---|
| Claude Sonnet 4.6 | ★★★★★ 最佳整體品質 | $3 / $15 | 生產環境首選 |
| GPT-4.1 | ★★★★☆ 穩定 | $2 / $8 | 中端可靠選項 |
| DeepSeek V4 | ★★★★☆ 高性價比 | $0.30 / $0.50 | 成本導向首選 |
| Qwen 3.6 Plus | 工具首次成功率 ~94% | 中 | 中文場景表現佳 |
| GLM 5.1 | 工具首次成功率 ~91% | 低 | 中文場景備選 |
| Llama 4 Maverick (Ollama) | 本地零成本 | 免費 | 隱私優先場景 |

**OpenRouter** 為預設聚合器，單一 API key 可路由至 Claude、GPT、Gemini、DeepSeek、Grok、Kimi 等 200+ 模型，適合多 provider 混用場景。

來源：[Best Models for Hermes Agent 2026](https://www.remoteopenclaw.com/blog/best-models-for-hermes-agent) | [Fox in the Box Benchmark](https://foxinthebox.io/blog/best-models-for-hermes-agents/) | [Armalo AI Benchmark Guide](https://www.armalo.ai/blog/hermes-agent-benchmark-the-complete-guide)

---

## 3. 社群反應與討論亮點

- **Hermes Desktop 引爆討論**：Nous Research 於 X.com 宣布桌面版上線（[貼文連結](https://x.com/NousResearch/status/2061843507417944552)），引用 Jensen Huang GTC 主題演講 demo，獲得廣泛轉發與正面評價。
- **MarkTechPost 報導**（[2026-06-03](https://www.marktechpost.com/2026/06/03/nous-research-releases-hermes-desktop-a-native-cross-platform-front-end-for-hermes-agent-v0-15-2-with-streaming-tool-output/)）：桌面版的串流工具輸出功能被視為大幅改善使用者體驗的里程碑。
- **AIToolly 分析**（[2026-06-06](https://aitoolly.com/ai-news/article/2026-06-06-nousresearch-unveils-hermes-agent-a-new-paradigm-for-ai-entities-that-grow-with-users)）：強調 Hermes-Agent「隨使用者成長」的自我進化定位，區別於傳統 stateless agent 框架。
- **Discord 社群**（discord.gg/nousresearch）持續活躍，是目前使用者反饋與技術討論最集中的渠道，包含桌面版安裝問題、模型配置疑難排解等即時討論。
- **180,000+ GitHub Stars**：自 2026-02-25 發布以來，不到四個月累積逾 18 萬星，為 2026 年成長最快的開源 agent 框架之一。
- **Medium 完整教學**（[連結](https://medium.com/@tentenco/hermes-agent-desktop-app-everything-you-need-to-know-about-nous-researchs-self-improving-ai-agent-3cb59bd31e5f)）：詳細介紹桌面版安裝與 remote gateway 設定流程，社群反應熱烈。

---

## 4. 值得追蹤的後續議題

| 議題 | 優先度 | 追蹤連結 |
|---|---|---|
| `max_tokens` 配置無效修復進度 | 高 | [Issue #4404](https://github.com/NousResearch/hermes-agent/issues/4404) |
| Rate limit header 預先節流功能 | 高 | [Issue #7489](https://github.com/NousResearch/hermes-agent/issues/7489) |
| Nous API 串流超時根因修復 | 高 | [Issue #29418](https://github.com/NousResearch/hermes-agent/issues/29418) |
| Hot memory / 停用工具狀態異常 | 中 | [Issue #26568](https://github.com/NousResearch/hermes-agent/issues/26568) |
| v0.17.0 發布時程與功能規劃 | 中 | [GitHub Releases](https://github.com/NousResearch/hermes-agent/releases) |
| Issues #41444 / #41448 / #41454（最新 bug 群） | 中 | [Issues 頁面](https://github.com/NousResearch/hermes-agent/issues) |
| UI 檔案操作非確定性問題 | 低 | [Issue #24537](https://github.com/NousResearch/hermes-agent/issues/24537) |
| Desktop App Windows 穩定性反饋 | 低 | Nous Discord |

---

*報告生成時間：2026-06-08 UTC*
