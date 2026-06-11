# OpenClaw 每日情報｜2026-06-11

> 資料蒐集範圍：過去 24 小時（UTC 基準）；最新可索引資訊截至 2026-06-11

---

## 1. 今日重點摘要

1. **穩定版 2026.6.5 仍為最新正式釋出**（發布日：2026-06-05）。過去 24 小時內官方未推出新 stable 或 hotfix，npm latest 維持 2026.6.1，GitHub release feed 則顯示 `v2026.6.5-beta.2` 持續在 beta 硬化階段。
2. **QQBot 思考標籤過濾**：2026.6.5 讓 QQBot 在傳送前自動剝除 `<reasoning>` / `<thinking>` 標籤，對話視窗只顯示最終答案，避免內部模型旁白洩露給終端使用者。
3. **MCP 工具結果強制轉型**：Agent/MCP/provider 層現在會在非 text/image 的 MCP tool-result block 抵達 provider converter 之前進行強制轉型，保留有效圖片並將豐富內容轉為純文字，解決舊版因 content type 不符產生的 malformed image block 錯誤。
4. **SQLite 狀態遷移持續推進**：memory-wiki sync、memory-core dreams、ACP process、device-pair notify 等狀態模組已移入 SQLite；session-metadata SQLite 遷移因風險評估**刻意延後**至 main 分支繼續觀察，目前仍沿用 JSON 路徑。
5. **OpenRouter 費用計算修正**：main 分支合併了修正串流式 OpenRouter 世代費用核算的 PR，過去因 streaming chunk 累加邏輯錯誤導致費用顯示偏低的問題獲修正。
6. **Provider 模組六合一切換**（2026-04 回顧）：provider manifest 允許在不重建工作流程的情況下執行期動態切換模型後端，支援 GPT-5.5、Claude API、Gemini、DeepSeek、OpenRouter、Ollama、LM Studio、Gemma 4 共八個 provider。
7. **已知重大 Bug 仍開放中**：
   - Issue #23996：硬編碼 `rate_limit` 原因造成 OAuth token auth 場景下的假冷卻（false cooldown），兩個 provider 同時被封鎖，降版至 2026.2.12 可暫時規避。
   - Issue #52949：gateway 呼叫 ClawHub API 時未帶入 auth token，導致持續 429 且「Available Skills」空白。
8. **依賴更新**：Android、Swift/macOS、Docker、CodeQL、Buildx 等 CI 依賴完成 2026.6.5 train 的例行更新。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）

- **推薦用途分層**（來自官方文件）：Sonnet 做大多數 agent 推理與工具呼叫；Haiku 用於高量低成本任務（分類、路由）；Opus 用於最高難度推理且對費用較不敏感的場景。
- **OAuth token 失效錯誤被誤報為 rate limit**：社群回報使用 `claude oauth` 驗證時頻繁收到 "API rate limit reached. Please try again later."，實際根因是 OAuth token 過期而非真正 API 限速；解法為重新執行 `openclaw models auth setup-token` 刷新憑證。來源：[AnswerOverflow 社群討論](https://www.answeroverflow.com/m/1478788645472436264)
- **假 rate_limit 冷卻（Issue #23996）**：當 Claude Opus 4.6 或 GPT-5.3-codex 的 provider 發生任何錯誤時，OpenClaw 將冷卻原因硬編碼為 `rate_limit` 並同時封鎖兩個 provider，無自動恢復機制，只能等待手動重啟。來源：[GitHub Issue #23996](https://github.com/openclaw/openclaw/issues/23996)

### Gemini（Google）

- Google plugin 透過 Google AI Studio 提供 Gemini 模型存取，支援圖片生成、多模態理解（圖片/音訊/影片）、TTS、以及 Gemini Grounding 的網路搜尋功能。
- 文件指出 Gemini grounding metadata 現已接受空值回應，修正了過往 Grounding 搜尋無結果時崩潰的問題。

### GPT（OpenAI）

- GPT-5.5 可透過 native Codex app-server harness（預設為 `openai/gpt-5.5`）或在 provider/model runtime policy 明確選擇 `openclaw` 時透過 OpenClaw runtime 存取。
- ClawHub auth token 未帶入 API 呼叫的 Bug（Issue #52949）在 GPT 與 ClawHub skill 混合場景下尤為明顯。

### 其他 Provider

- **DeepSeek / Ollama / LM Studio**：均已納入 provider manifest，可執行期熱切換，適合隱私或離線場景。
- OpenRouter 費用計算 bug 已在 main 修正，串流場景下的費用追蹤更準確。

**參考來源：**
- [OpenClaw Model Providers 官方文件](https://docs.openclaw.ai/concepts/model-providers)
- [Best LLM for OpenClaw 2026 — DEV Community](https://dev.to/matthewrevell/best-llm-for-openclaw-gemini-31-pro-vs-gpt-55-vs-claude-opus-47-2026-3na4)
- [OpenClaw Rate Limit 完整排查指南](https://blog.laozhang.ai/en/posts/openclaw-rate-limit-exceeded-429)
- [OpenClaw API Key 錯誤修復指南](https://blog.laozhang.ai/en/posts/openclaw-api-key-error)

---

## 3. 社群反應與討論亮點

- **Reddit（r/OpenClaw、r/Clawdbot）**：社群 staff 團隊持續維護兩個子板，定期舉辦 Weekly Claw 活動，提供 #help 與 #users-helping-users 支援頻道。過去 24 小時內未見重大爆料貼文。
- **2026.6.5 beta 評價正面**：社群對 QQBot 思考標籤過濾功能普遍正面，認為終端 bot 不再「自言自語」是體驗提升。
- **SQLite 遷移策略受關注**：部分進階使用者對 session-metadata 遷移延後表示理解，但呼籲官方盡快確認 main 分支的穩定時間表；PR #78595（Refactor runtime state into SQLite）持續被追蹤。
- **OpenClaw 成長速度**：截至 2026 年初已達 200,000+ GitHub stars，被認為是近年成長最快的開源 AI agent 框架之一。

---

## 4. 值得追蹤的後續議題

| 議題 | 狀態 | 優先度 |
|------|------|--------|
| Issue #23996：假冷卻 bug 修復進度 | 開放中 | 高 |
| Issue #52949：ClawHub auth token 未帶入 | 開放中 | 高 |
| Session-metadata SQLite 遷移上線時間表 | 延後至 main | 中 |
| npm latest 從 2026.6.1 升至 2026.6.5 的時間點 | 待發布 | 中 |
| OpenRouter streaming 費用計算修正是否進入 stable | PR 已合併 main | 中 |
| Gemini Grounding 空 metadata 修正驗證 | 已修復，待觀察 | 低 |

---

*本報告根據公開可索引的 GitHub releases、issues、官方文件及社群討論彙整，不含未經驗證的資訊。*
