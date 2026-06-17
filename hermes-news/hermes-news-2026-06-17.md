# Hermes-Agent 每日情報 — 2026-06-17

> 資料範圍：過去 24 小時（UTC）｜來源：GitHub、NousResearch、社群論壇、技術媒體

---

## 1. 今日重點摘要

1. **v0.16.0「The Surface Release」於 2026-06-05 正式發布**：此版本為 Hermes-Agent 迄今規模最大的單次釋出，共 874 commits、542 merged PRs、1,962 檔案變動、205,216 行新增、46,217 行刪除，關閉 399 issues（含 2 個 P0、62 個 P1、16 個資安標記），來自 170 位社群貢獻者。（來源：[GitHub Release v2026.6.5](https://github.com/NousResearch/hermes-agent/releases/tag/v2026.6.5)）

2. **原生桌面應用程式正式推出**：歷時一週、100 PRs、159 commits 打造的 Electron 桌面應用程式支援 macOS、Linux、Windows，具備一鍵安裝、應用內自動更新、拖曳附檔、狀態列內嵌模型切換器、並行多 profile 工作階段、完整繁體簡體中文介面，及連線至遠端 Hermes gateway（OAuth 或帳密驗證）。（來源：[Decrypt 報導](https://decrypt.co/369952/hermes-ai-agent-official-app-terminal)、[官方桌面應用文件](https://hermes-agent.nousresearch.com/docs/user-guide/desktop)）

3. **Web Dashboard 全面升級**：v0.16.0 新增全瀏覽器管理介面，涵蓋 MCP catalog 管理、訊息頻道設定、憑證管理、webhook、memory 管理，以及可插拔式 OIDC / 帳密登入。（來源：[hermes-agent.ai blog](https://hermes-agent.ai/blog/hermes-agent-v0-16-surface-release)）

4. **三個資安 CVE 修補**：CVE-2026-48710（Starlette 版本釘選）、SSRF off-loop 強化、subprocess 憑證遮蔽（credential stripping）。（來源：[GitHub Release](https://github.com/NousResearch/hermes-agent/releases/tag/v2026.6.5)）

5. **DeepSeek V4 Pro via OpenRouter 導致 gateway 崩潰迴圈（Issue #16677）**：以 `deepseek/deepseek-v4-pro` 為預設模型時，OpenRouter 上游供應商「Io Net」的激進 rate limit 使 Hermes gateway 進入 crash loop，導致 Telegram bot 及所有訊息整合完全無回應。（來源：[Issue #16677](https://github.com/NousResearch/hermes-agent/issues/16677)）

6. **輔助任務靜默回退付費模型（Issue #24029）**：即使使用者明確設定 `fallback_providers` 只使用 OpenRouter 免費模型，輔助任務仍會靜默呼叫硬編碼的 `google/gemini-3-flash-preview`，導致帳單異常或被月度限額封鎖。（來源：[Issue #24029](https://github.com/NousResearch/hermes-agent/issues/24029)）

7. **OpenRouter token 用量全平台第一**：截至 2026 年 6 月，Hermes-Agent 在 OpenRouter 應用排行榜以累計超過 17 兆 tokens 位居首位，反映龐大的活躍使用者基礎。（來源：[betterclaw.io 分析](https://www.betterclaw.io/blog/hermes-response-truncated-fix)）

8. **GitHub 累計超過 180,000 顆星**：自 2026-02-25 發布以來不足四個月即突破此里程碑，是近年成長速度最快的開源 AI agent 框架之一。（來源：[Medium 深度評測](https://medium.com/@tentenco/hermes-agent-desktop-app-everything-you-need-to-know-about-nous-researchs-self-improving-ai-agent-3cb59bd31e5f)）

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）

- **最佳整體品質選擇**：Claude Sonnet 4.6 為 2026 年 Hermes-Agent 社群公認的最佳整體選擇，工具呼叫（tool calling）行為在生產環境中最為穩定；GPT-4.1 並列可靠性最高的 provider。
  - 來源：[Petronella Tech 指南](https://petronellatech.com/blog/hermes-agent-ai-guide/)

- **Anthropic OAuth 整合**：Hermes-Agent 透過 Anthropic OAuth 授權時，優先使用 Claude Code 自身的 credential store 而非將 token 複製至設定檔，使 OAuth 可重新整理的憑證保持可刷新狀態，避免 token 過期導致工作流中斷。
  - 來源：[AI Providers 文件](https://hermes-agent.nousresearch.com/docs/integrations/providers)

### OpenRouter（多模型代理）

- **DeepSeek V4 Pro crash loop**：Issue #16677 確認使用 `deepseek/deepseek-v4-pro` 時，Io Net 上游 rate limit 觸發 gateway 崩潰迴圈，無自動恢復；建議改用 `deepseek/deepseek-v4` 或切換至其他 provider。
  - 來源：[Issue #16677](https://github.com/NousResearch/hermes-agent/issues/16677)

- **HTTP 402 / context window 費用問題**：Hermes 預設要求大型 context window（如 `max_tokens: 65536`），觸發 OpenRouter 餘額不足 402 拒絕；建議依需求動態調整 `max_tokens`，或為免費使用者配置嚴格的 token 上限。
  - 來源：[Issue #12639](https://github.com/NousResearch/hermes-agent/issues/12639)

- **輔助任務靜默呼叫付費模型**：Issue #24029 指出即使 `fallback_providers` 設為純免費模型，背景輔助任務仍會呼叫 `google/gemini-3-flash-preview`（付費），導致帳單異常，且無任何日誌警示。目前官方尚未推出 hotfix。
  - 來源：[Issue #24029](https://github.com/NousResearch/hermes-agent/issues/24029)

### DeepSeek（本地 / 直連）

- DeepSeek V4（非 Pro 版）在 budget 部署情境表現穩定，是大量處理任務的高 CP 值選擇；V4 Pro 因 OpenRouter 轉發問題暫不建議生產使用。
  - 來源：[remoteopenclaw.com 比較文](https://www.remoteopenclaw.com/blog/best-models-for-hermes-agent)

### Llama（本地 / Ollama）

- Llama 4 Maverick via Ollama 為隱私優先場景的首選本地模型，免 API 費用且資料不離本機。
  - 來源：[nxcode.io 安裝教學](https://www.nxcode.io/resources/news/hermes-agent-tutorial-install-setup-first-agent-2026)

### 回應截斷（Response Truncation）

- Issue #26425（2026-05 開）確認部分設定下即使先前修補已合入，回應截斷問題（`finish_reason='length'`）仍持續發生，尤其在長流程 agentic 任務中。
  - 來源：[betterclaw.io 分析](https://www.betterclaw.io/blog/hermes-response-truncated-fix)

---

## 3. 社群反應與討論亮點

- **桌面應用被視為里程碑**：Decrypt、Medium 等多家媒體以「結束終端機時代」為標題報導 v0.16.0，社群反應熱烈，大量使用者表示桌面版顯著降低上手門檻。Windows SmartScreen 未簽章警告是最常見的初次安裝困惑點。
  - 來源：[Decrypt](https://decrypt.co/369952/hermes-ai-agent-official-app-terminal)、[Medium](https://medium.com/@tentenco/hermes-agent-desktop-app-everything-you-need-to-know-about-nous-researchs-self-improving-ai-agent-3cb59bd31e5f)

- **免費模型使用者對隱性費用感到不滿**：Issue #24029 在社群引發討論，使用者認為「免費只配置卻靜默收費」的行為不透明，呼籲官方增加明確的 fallback 警示或嚴格遵守 `fallback_providers` 設定。
  - 來源：[Issue #24029](https://github.com/NousResearch/hermes-agent/issues/24029)

- **NousResearch X 帖推廣 v0.16.0**：官方 X 帳號（[@NousResearch](https://x.com/NousResearch/status/2063075092859637888)）貼出 v0.16.0 changelog 連結，引發社群廣泛轉發，Surface Release 主題受到正面迴響。

- **/undo 指令深受歡迎**：v0.16.0 加入的 `/undo` 可撤回最後 N 輪對話，社群反應比預期更受歡迎，多位使用者指出這是長期以來呼聲最高的功能之一。
  - 來源：[illmethinks.io 報導](https://illmethinks.io/stories/2026-06-06/hermes-agent-ships-v0-16-0-2026-6-5-the-surface-release)

---

## 4. 值得追蹤的後續議題

1. **DeepSeek V4 Pro crash loop 修復進度**：Issue #16677 影響範圍廣，官方尚未合入修補；建議追蹤 PR 狀態，暫時繞過方式是改用 `deepseek/deepseek-v4`（非 Pro）。
   - 追蹤：[Issue #16677](https://github.com/NousResearch/hermes-agent/issues/16677)

2. **輔助任務強制使用 free-only provider**：Issue #24029 是目前免費使用者的核心痛點，官方需設計嚴格的 `fallback_providers` 遵守機制並加入費用警示日誌。
   - 追蹤：[Issue #24029](https://github.com/NousResearch/hermes-agent/issues/24029)

3. **原生 Google / Vertex AI Provider（Feature Request）**：Issue #12639 呼籲直連 Vertex AI 以繞過 OpenRouter 的 402 rate limit 加成，官方尚無時程。
   - 追蹤：[Issue #12639](https://github.com/NousResearch/hermes-agent/issues/12639)

4. **Windows 安裝程式程式碼簽署**：目前 Windows 版未簽章，SmartScreen 警告影響企業部署；社群已回報，官方態度待觀察。
   - 追蹤：[官方桌面文件](https://hermes-agent.nousresearch.com/docs/user-guide/desktop)

5. **v0.17.0 下一版規劃**：v0.16.0 規模龐大，社群期待官方公布下一版路線圖，尤其是 Kanban 多 agent 排程與 Curator 技能修剪功能的進一步強化。
   - 追蹤：[GitHub Releases](https://github.com/NousResearch/hermes-agent/releases)

---

*報告生成時間：2026-06-17 UTC｜資料來源詳見各段落連結*
