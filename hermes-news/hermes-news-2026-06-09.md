# Hermes-Agent 每日情報 — 2026-06-09

> 搜尋時間範圍：過去 24 小時（UTC 2026-06-08 ～ 2026-06-09）

---

## 1. 今日重點摘要

1. **v0.16.0「The Surface Release」於 2026-06-05 正式發布**：本次為近期最大規模版本，874 次 commits、542 個合併 PR、1,962 個檔案變更（+205,216 / -46,217 行），170 位社群貢獻者，關閉 399 個 issue（含 2 個 P0、62 個 P1、16 個安全標籤）。
2. **原生 Electron 桌面應用程式正式登場**：Hermes Agent 不再只是終端工具——macOS、Linux、Windows 均可一鍵安裝，支援應用內自動更新。桌面版提供串流聊天視窗、拖放上傳檔案、剪貼板圖片貼上、Cmd+K 命令調色盤、session 清單（含封存與搜尋），以及狀態列模型選擇器。
3. **管理面板（Admin Panel）加入 Web Dashboard**：新增完整的瀏覽器端管理介面，並提供「透過 Nous Portal 快速設定」的初始化路徑，降低非技術用戶的上手門檻。
4. **簡體中文在地化**：桌面 UI 完整支援簡體中文（透過型別安全的 i18n 層實作），為首個官方本地化語言。
5. **安全性修補**：釘選已修補的 Starlette 版本以對應 CVE-2026-48710；將 SSRF URL 檢查移出事件迴圈；從子程序環境中移除 Bedrock inference bearer token；清除技能內容中的隱形 Unicode 字元。
6. **模糊搜尋模型選擇器全面覆蓋**：桌面版、Web、TUI、CLI 四端的模型選擇器均支援模糊搜尋，以及 `/undo` 指令可撤銷最後 N 輪對話。
7. **默認技能集精簡 + NVIDIA/skills 加入受信任 Hub**：移除不常用的預設技能，NVIDIA 官方技能包加入 Skills Hub 信任清單。
8. **DeepSeek V4 Pro 透過 OpenRouter 導致 gateway 崩潰迴圈（Issue #16677）**：使用 `deepseek/deepseek-v4-pro` 為預設模型（透過 OpenRouter）時，gateway 進入崩潰迴圈，Telegram bot 等所有 messaging 整合完全無法回應。根因為 OpenRouter 上游供應商「Io Net」對該模型套用積極的 rate limit，回傳 HTTP 401（User not found），而此非重試錯誤會使 fallback 機制失效。

---

## 2. LLM 串接實戰回報

### Anthropic Claude（Anthropic API / OAuth）

- **OAuth 憑證整合改進**：透過 `hermes model` 選擇 Anthropic OAuth 後，Hermes 優先使用 Claude Code 的憑證存儲（而非複製 token 至 `~/.hermes/.env`），保持 OAuth token 可自動更新，大幅減少手動重新登入問題。
- **最穩定的 tool calling 模型**：Claude Sonnet 4.6 在生產環境的 tool calling 可靠性最高，適合需要頻繁調用工具的 agent 工作流，較少出現格式錯誤的 function call。
- 參考：[AI Providers — Hermes Agent 官方文件](https://hermes-agent.nousresearch.com/docs/integrations/providers)

### OpenAI GPT-4.1 / GPT-5.x

- GPT-4.1 與 Claude Sonnet 4.6 並列為生產環境最推薦模型，tool calling 穩定性高。GPT-5.x 系列尚在觀察中，社群部分用戶反映某些版本偶有「中途放棄」行為。
- 參考：[Best Hermes Agent Model 2026: Claude vs DeepSeek — Remote OpenClaw](https://www.remoteopenclaw.com/blog/best-models-for-hermes-agent)

### DeepSeek V4 Pro via OpenRouter（踩坑重點）

- **Issue #16677（仍開啟中）**：`deepseek/deepseek-v4-pro` 透過 OpenRouter 路由時，Io Net 上游對共享池套用高頻 rate limit；rate limit 發生時 OpenRouter 錯誤回傳 HTTP 401，gateway 視為不可重試錯誤而崩潰。
- **建議解法**：在 [OpenRouter 整合設定](https://openrouter.ai/settings/integrations) 綁定個人 DeepSeek API key，獲得獨立 rate limit 而非共享池；或暫時改用 Claude / GPT-4.1 作為主模型。
- 參考：[Issue #16677 — DeepSeek V4 Pro via OpenRouter causes gateway crash loop](https://github.com/NousResearch/hermes-agent/issues/16677)

### 最低 Context Window 要求

- Hermes Agent 系統提示、活躍技能、記憶檔案加上對話歷史合計需要至少 **64,000 tokens** 的 context window。使用較小模型（例如部分 Ollama 本地模型的預設設定）會立即出現「context window too small」錯誤。
- Claude、GPT-4 同級、Gemini、Qwen 及大部分主流開源模型均可達標。
- 參考：[Configuration — Hermes Agent 官方文件](https://hermes-agent.nousresearch.com/docs/user-guide/configuration)

### GitHub Copilot 作為多模型閘道

- GitHub Copilot 可作為 GPT-5.x、Claude、Gemini 等模型的統一接入點，部分用戶採用此方式管理跨 provider 的費用與存取授權。

---

## 3. 社群反應與討論亮點

- **X.com（Nous Research 官方）**發布 v0.16.0 公告，獲得較多轉發；桌面應用程式是最受關注的新功能，Reddit 和社群論壇紛紛討論「終端不再是唯一入口」的意義。
- FintechExtra、dev.to 等媒體均發文介紹 Hermes Desktop，將其定位為「Claude Code 的低費用替代方案」——搭配 Ollama 本地模型可做到完全免費執行。
- MindStudio 發布對比分析文章，主張 Claude Code 和 Hermes Agent 在 2026 年已取代 Cursor 和 ChatGPT 等工具的特定使用情境。

---

## 4. 值得追蹤的後續議題

1. **Issue #16677（DeepSeek + OpenRouter 崩潰）**：gateway 應對不可重試錯誤的降級策略修復進度，以及是否會加入模型層級的 fallback 保護。
2. **桌面應用程式穩定性**：v0.16.0 是 Electron app 首發，後續 patch 版本可能出現各平台特定 bug，值得追蹤 issue tracker。
3. **CVE-2026-48710 後續影響**：Starlette 安全修補是否有其他相依套件也需要版本升級。
4. **多語言在地化路徑**：簡體中文上線後，是否會有繁體中文或其他語言的跟進計畫。
5. **v0.17.0 roadmap**：v0.15.0 Kanban 多 agent 平台、v0.16.0 桌面端後，下一個版本的方向（推測可能聚焦行動端或更深的 MCP 整合）。

---

*資料來源：*
- [GitHub — NousResearch/hermes-agent Releases](https://github.com/NousResearch/hermes-agent/releases)
- [Release v0.16.0 — The Surface Release](https://github.com/NousResearch/hermes-agent/releases/tag/v2026.6.5)
- [Nous Research X.com 發布公告](https://x.com/NousResearch/status/2063075092859637888)
- [AI Providers — Hermes Agent 官方文件](https://hermes-agent.nousresearch.com/docs/integrations/providers)
- [Configuration — Hermes Agent 官方文件](https://hermes-agent.nousresearch.com/docs/user-guide/configuration)
- [Issue #16677 — DeepSeek V4 Pro via OpenRouter crash loop](https://github.com/NousResearch/hermes-agent/issues/16677)
- [Best Hermes Agent Model 2026: Claude vs DeepSeek](https://www.remoteopenclaw.com/blog/best-models-for-hermes-agent)
- [Hermes Agent Desktop Free With Local LLMs — DEV Community](https://dev.to/kunal_d6a8fea2309e1571ee7/hermes-agent-desktop-free-with-local-llms-the-claude-code-alternative-nobodys-billing-you-for-33f4)
- [FintechExtra — Hermes Agent v0.16.0 Launches Native Desktop App](https://fintechextra.com/news/hermes-agent-v0160-launches-native-desktop-app-633)
- [MindStudio — AI Tools That Got Replaced in 2026](https://www.mindstudio.ai/blog/ai-tools-replaced-2026-claude-code-hermes-agent-killed-cursor-openclaw)
