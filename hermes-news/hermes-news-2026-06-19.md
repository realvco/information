# Hermes-Agent 每日情報 — 2026-06-19

> 搜尋時間範圍：過去 24 小時（UTC 基準）
> 資料來源：GitHub Issues/Releases、NousResearch 官方、技術部落格、社群討論

---

## 1. 今日重點摘要

1. **最新穩定版為 v0.16.0（2026.6.5，"The Surface Release"）**：包含 874 commits、542 merged PRs、399 closed issues；核心新增為跨平台原生桌面應用程式（macOS / Linux / Windows），提供一鍵安裝、應用內自動更新及多 profile 並行 session。
2. **桌面應用公開預覽（v0.15.2，6/2 發佈）**：Hermes Desktop 以 Electron 封裝，支援拖放檔案入對話、狀態列嵌入模型選擇器；MarkTechPost 於 6 月 3 日撰文報導，社群迴響熱烈。
3. **Feature Request #48434（6/18 新開）**：由 yuzilongleif-collab 提出，涉及核心 agent 迴圈與 prompt builder 改善，被標記為 feature request；細節尚待社群討論。
4. **180,000+ GitHub stars，自 2/25 上線至今不足 4 個月**：被多家媒體稱為「2026 年成長最快的開源 agent 框架」，社群活躍度持續攀升。
5. **Bug #22879（max_tokens 硬編碼導致 OpenRouter HTTP 402）**：Hermes 將 `max_tokens` 固定為各模型最大輸出值（如 Claude Sonnet/Haiku 4.5 為 64,000），OpenRouter 以此預留費用，導致帳戶餘額不足時出現 402 錯誤，config.yaml 的 `model.max_tokens` 設定目前無效（Bug #4404）。
6. **Bug #7230（auth 失敗不觸發 fallback_model）**：當主要 provider OAuth token 失效（HTTP 401）時，`config.yaml` 中設定的 `fallback_model` 從未被啟動，agent 直接回傳固定錯誤訊息；為已確認 bug，PR 待中。
7. **Kanban 多 agent 看板（v0.13.0）持續成熟**：heartbeat、zombie detection、自動 block on incomplete exit、per-task retry 等機制在社群中獲得正面評價，被視為 Hermes 區別於其他 agent 框架的核心差異化功能。
8. **Issue #14853：多 agent Discord 頻道協作**：社群 feature request，要求加入 history injection 與 cascade prevention 以支援多個 Hermes instance 在同一 Discord server 協同工作而不互相干擾。

---

## 2. LLM 串接實戰回報

### Claude Sonnet 4.6（Anthropic）
- **推薦場景**：整體品質最佳，被 2026 年多篇評測列為 Hermes-Agent「綜合首選」。
- **已知問題**：`max_tokens` 被 Hermes 硬編碼至模型上限（64,000），搭配 OpenRouter 使用時觸發 HTTP 402（Issue #22879）；建議暫時以 OpenRouter 的 `max_output_tokens` 覆蓋參數作為 workaround，或直接使用 Anthropic 官方 API 繞過此問題。
- 來源：[Best Hermes Agent Model 2026: Claude vs DeepSeek](https://www.remoteopenclaw.com/blog/best-models-for-hermes-agent)、[Bug #22879](https://github.com/NousResearch/hermes-agent/issues/22879)

### DeepSeek V4
- **推薦場景**：預算有限的部署首選，成本顯著低於 Claude / GPT，適合大量批次任務。
- **實戰提醒**：部分使用者回報 DeepSeek 在 Hermes 長任務鏈中偶有 tool-calling schema 不一致的問題，建議在 profile 中開啟 `strict_tool_schema: true`。
- 來源：[Hermes Agent AI 2026: Self-Hosted AI Agent Stack Guide](https://petronellatech.com/blog/hermes-agent-ai-guide/)

### Gemini 2.5 Pro（Google AI）
- **定價**：$1.25/$10 per million tokens（input/output），Google AI Studio 提供免費 tier。
- **優勢**：最高支援 1M token context，適合需要超大上下文的工作流；`video_analyze` 工具（v0.13.0 新增）目前僅在 Gemini 及相容多模態模型上原生支援。
- **整合方式**：透過 Composio 可快速接入 Hermes；官方文件有完整 provider 設定指引。
- 來源：[How to integrate Gemini with Hermes](https://composio.dev/toolkits/gemini/framework/hermes-agent)、[AI Providers | Hermes Agent](https://hermes-agent.nousresearch.com/docs/integrations/providers)

### Llama 4 Maverick（本地 Ollama）
- **推薦場景**：隱私敏感或離線部署，完全本機執行無資料外洩疑慮。
- **實戰提醒**：本地模型的 `max_tokens` 硬編碼問題同樣存在，但因無費用顧慮，402 錯誤不適用；主要限制為 GPU 記憶體與推論速度。

### OpenRouter（多模型路由）
- **優勢**：單一 API key 可存取 200+ 模型，包含 Claude、GPT、Gemini、Qwen、Grok 等。
- **踩坑紀錄**：Hermes 的 `max_tokens` 硬編碼機制與 OpenRouter 的「預留費用（collateral）」設計衝突，是目前社群最常見的費用異常根源；auth 失效時 fallback_model 不啟動（Bug #7230）也在 OpenRouter 環境中特別明顯。
- **建議 workaround**：在 Nous Portal 訂閱中設定 OpenRouter 為次要 provider，並以官方 API 作為主要 provider，降低雙重故障風險。
- 來源：[Bug #7230](https://github.com/NousResearch/hermes-agent/issues/7230)、[Fix Hermes Agent Token Limit Error](https://markaicode.com/hermes-agent-token-limit-error-fix/)

### 常見錯誤速查
| 錯誤類型 | 觸發條件 | 建議處理 |
|---|---|---|
| HTTP 402 | OpenRouter + Claude 的 max_tokens 衝突 | 手動設低 max_tokens 或換用官方 API |
| HTTP 401 不觸發 fallback | OAuth token 過期 | 等待 Bug #7230 修復；暫以重啟 agent 解決 |
| 預設 2048 token 視窗不足 | 未調整 Modelfile | 在 Modelfile 設定 `PARAMETER num_ctx` 並重啟 |
| 工具呼叫同時並發超限 | 預設迴圈無並發控制 | 設定 `max_concurrent_tools` 限制並行數 |

---

## 3. 社群反應與討論亮點

- **Hermes Desktop 受到廣泛關注**：Medium 上 Ewan Mak 的文章詳細拆解桌面應用的架構選擇，評論區有大量使用者分享初次安裝體驗，普遍反應「安裝比預期簡單」；AIToolly 等媒體同步報導，認為此舉標誌 Hermes 從開發者工具走向一般用戶。
- **多 agent 協作議題熱議**：Issue #14853（Discord 多 agent 協作）在提出後迅速累積 +1 和討論，有使用者已自行以 systemd 多 service 方式運行 3 個獨立 Hermes instance，並分享 per-profile 設定方式，但指出缺乏官方支援的 history injection 導致 agent 間容易重複回覆。
- **Kanban 看板的實際使用回饋**：v0.13.0 推出後有使用者在社群分享以 Kanban 管理 10+ 個並行 agent 任務的截圖，heartbeat 機制和 zombie detection 被認為是生產環境中最實用的功能，顯著降低「agent 卡死而無感知」的問題。
- **本地化支援受歡迎**：v0.13.0 加入 7 種語言翻譯（中文、日文、德文、西班牙文、法文、烏克蘭文、土耳其文），非英語社群活躍度明顯提升，繁體中文用戶回報 UI 翻譯品質尚有改善空間，已有 PR 草稿提交。

---

## 4. 值得追蹤的後續議題

1. **Bug #22879 修復時程**：`max_tokens` 硬編碼問題影響大量 OpenRouter 使用者，PR 狀態值得持續追蹤。
2. **Bug #7230（fallback_model 不啟動）**：影響 auth 可靠性，尤其在 OAuth provider 環境中；預計為近期高優先修復項目。
3. **Feature Request #48434（核心迴圈/prompt builder 改善）**：剛開啟的新 issue，方向涉及 agent 核心邏輯，後續討論走向值得關注。
4. **v0.17.0 版本預告**：社群推測下一個版本將聚焦 web dashboard 的進一步完善以及 Kanban 看板的穩定性改善，目前無官方公告。
5. **Issue #14853（多 agent Discord 協作）**：隨桌面應用普及，多 agent 協作需求增加，此 feature 進入里程碑規劃的可能性提高。
6. **Bug #4404（config.yaml max_tokens 設定無效）**：根本原因已確認（AIAgent constructor 未接收設定值），但修補尚未合併，持續追蹤。

---

*本報告依據公開搜尋結果撰寫，部分細節以搜尋結果摘要為準；若需確認原始資訊請點擊來源連結查閱。*
