# OpenClaw 每日情報 — 2026-05-26

> 資料來源：GitHub Releases、Reddit r/openclaw、AnswerOverflow、部落格文章、官方文件  
> 搜尋範圍：過去 24 小時（UTC 2026-05-25 ～ 2026-05-26）

---

## 一、今日重點摘要

1. **v2026.5.24-beta.1 為目前最新版本**（發佈於 2026-05-24）：本次 beta 版重點包含更快的啟動速度、插件化的會議錄製功能（pluginized meeting capture）、Row-level session helpers、可觀測性冒煙測試（observability smoke tests）、套件完整性驗證閘道（package integrity gates）以及 SecretRef 使用說明的改善。

2. **主倉庫、clawhub（技能目錄）、clawsweeper-state 均於 2026-05-26 更新**：顯示核心生態系統維護持續進行中，但過去 24 小時尚無正式新版本發佈。

3. **GitHub Issue #86616 於 2026-05-25 新開**（作者 EthanSK）：具體內容未於公開搜尋結果中揭露，值得關注後續回應。

4. **PR 上限政策 (#38283)**：每位作者的開放 PR 數目上限已調整為 20，顯示專案在管理大量貢獻者方面持續優化流程。

5. **CLI/nodes 日誌改善**：`openclaw nodes` 指令在 JSON 模式下已將插件載入日誌路由至 stderr，確保 stdout 輸出可被程式解析；`/approve` 手動決策改由受信任的 approval runtime 處理，解決 exec 與 plugin 審核項目顯示為「未知或過期」的問題。

6. **Sub-agent 引導情境範圍縮限**：預設情況下子代理（subagent）啟動時只載入 `AGENTS.md` 與 `TOOLS.md`，排除 persona、identity、user、memory、heartbeat、setup 等檔案，降低不必要的 context 汙染風險。

7. **Provider 鑑別映射表預熱（auth-state map pre-warm）**：Gateway 啟動時預先建立 provider auth-state 映射，使 `/models` 端點及所有模型列表呼叫從每次耗費約 20 秒降至約 5 ms（加速約 4,100 倍）。

8. **Plugin SDK 新增通用輪詢訊息發送器（poll sender）**：頻道插件可公開 poll delivery 功能，無需依賴特定頻道的 SDK facade。

9. **社群關注焦點仍在穩定性**：部分長期用戶因 4 月 26 日版本更新引發的 gateway 崩潰問題而轉向 Hermes-Agent，OpenClaw 穩定版通道的缺失仍是社群討論熱點。

---

## 二、LLM 串接實戰回報

### Claude（Anthropic）

- **推薦用途**：工具調用可靠性最佳、長脈絡連貫性強、原生 MCP 支援，適合企業部署的安全預設。
  - Sonnet：大多數 agent 工作（推理、工具調用、通用任務）
  - Haiku：高量低成本任務（分類、路由、簡單轉換）
  - Opus：需要最高推理能力、成本為次要考量的任務
- **OAuth Token 踩坑**：使用 `sk-ant-oat01-` 前綴的 Claude Max OAuth Token 時，OpenClaw 會跳過 `context-1m-*` beta header，因為 Anthropic 目前對此組合回傳 HTTP 401。
- **false rate_limit 冷卻 bug（Issue #23996）**：OpenClaw 在未實際觸發 API 速率限制的情況下，仍將 provider 標記為冷卻狀態，冷卻原因被硬編碼為 `"rate_limit"`，不論實際失敗原因為何。此 bug 在 Claude OAuth 訂閱用戶中較為常見。
- **429 不自動切換 provider**：Claude 或 GPT 觸發 429 時，OpenClaw 會將該 provider 加入冷卻，但不會自動切換至備援 provider，除非使用者明確設定了 fallback。
- **OAuth 模式看不到成本**：OAuth 認證隱藏費用細節，用戶只能看到 token 數，無法看到詳細的費用分解。
- 來源：[GitHub Issue #23996](https://github.com/openclaw/openclaw/issues/23996) ｜ [AnswerOverflow - Claude OAuth rate limit](https://www.answeroverflow.com/m/1478788645472436264) ｜ [LaoZhang AI Blog](https://blog.laozhang.ai/en/posts/openclaw-llm-setup)

### GPT（OpenAI）

- GPT-5.4（2026 年 3 月發佈）被廣泛認為是 OpenClaw 自主工作流最適合的模型，原生支援 Computer Use，在 OSWorld benchmark 達到 75% 成功率。
- 2026 年 4 月起透過 provider manifest 可在 runtime 切換，支援 GPT-5.5 via Codex。
- 來源：[MindStudio Blog](https://www.mindstudio.ai/blog/openclaw-april-2026-model-agnostic-provider-manifest)

### 多 Provider 支援

- 目前 out-of-the-box 支援：Claude API、GPT-5.5 via Codex、Gemini、DeepSeek、OpenRouter、Ollama、LM Studio、Gemma 4。
- **缺乏內建 token 用量控制（Issue #58826）**：目前無法在 OpenClaw 內設定 token 用量上限或請求數上限，外部工具只能觀測，無法主動介入。
- 來源：[GitHub Feature Request #58826](https://github.com/openclaw/openclaw/issues/58826) ｜ [OpenClaw API Rate Limit Guide](https://skywork.ai/skypage/en/openclaw-api-rate-limit-guide/2048654216278003712)

---

## 三、社群反應與討論亮點

- **r/openclaw 的 4 月 26 日版本更新災難**：大量用戶確認 Discord、Telegram 等整合無回應、gateway 崩潰或啟動循環崩潰、高 CPU 使用率、高延遲，以及多個插件損毀。直接從 .5 跳至 .26 版的用戶受災最嚴重。
  - 來源：[Piunika Web 報導](https://piunikaweb.com/2026/04/29/openclaw-update-triggering-gateway-crashes-issues/)

- **缺乏穩定版通道**仍是社群最主要的抱怨。多位長期用戶表示已將 Hermes-Agent 改為主力，OpenClaw 降為備援。

- **Medium 文章 (Apr 2026)**：「我將 OpenClaw 接上 Claude、ChatGPT 和 Telegram——以下是真正有效的部分」分析了三種 LLM 串接的實際差異。
  - 來源：[Mohit Aggarwal on Medium](https://medium.com/@mohit15856/i-wired-openclaw-to-claude-chatgpt-telegram-heres-what-actually-works-8278906edccc)

- **「放棄 OpenClaw 改用原生 Claude Code」**（mager.co 部落格，2026-05-13）：作者分析從 OpenClaw 遷移至 Claude Code CLI 的理由，引發社群熱議。
  - 來源：[mager.co](https://www.mager.co/blog/2026-05-13-killing-openclaw/)

---

## 四、值得追蹤的後續議題

1. **v2026.5.24-beta.1 → 正式版時程**：beta 版何時轉正，以及是否解決了先前 gateway 穩定性問題。
2. **Issue #86616**（EthanSK，2026-05-25）：內容未公開，需追蹤回應與處理進度。
3. **Issue #58826 — 內建 token 預算控制**：功能需求已提出，待官方回應優先級。
4. **false rate_limit 冷卻 bug (#23996)**：修復是否納入下一版本。
5. **OAuth Token 與 `context-1m-*` header 相容性**：Anthropic 端是否會開放此組合，或 OpenClaw 提供繞過方式。
6. **穩定版通道計畫**：官方是否有規劃類似 `stable` channel，以解決社群長期痛點。
7. **從 OpenClaw 遷移至原生 Claude Code 的趨勢**：是否形成更大的用戶流失浪潮。
