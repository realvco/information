# OpenClaw 每日情報 — 2026-06-23

## 1. 今日重點摘要

1. **v2026.6.9 建議跳過**：社群監控站 ClawStat.us 評估（2026-06-22）指出，v2026.6.9 存在三項關鍵回歸缺陷，建議生產環境使用者暫緩升級，等待 v2026.6.10 修復版。
2. **v2026.6.10-beta.2 釋出（2026-06-21）**：beta.2 修正了自動快速模式（短對話輪次啟用 fast mode、長任務恢復正常模式）、模型路由一致性問題（Zai 模型合成、GLM 過載切換）、以及 cron 排程交付的 session 狀態錯置問題。
3. **記憶體儲存靜默搬遷缺陷（#95495）**：v2026.6.9 的記憶模組在升級後靜默遷移儲存路徑，且不執行自動資料轉移，導致現有語料庫必須全量重新向量化，影響所有啟用 Active Memory 的使用者。
4. **memory_search 工具間歇性失敗（#90361）**：搜尋與重新索引之間存在競爭條件（race condition），導致 `memory_search` 工具回報「index metadata is missing」錯誤，屬不穩定的間歇性問題。
5. **模型 fallback 鏈繞過缺陷（#95489）**：當 Claude CLI 回傳「out-of-credits」錯誤時，OpenClaw 不會觸發 fallback 模型鏈，而是直接將錯誤訊息作為最終回應輸出，導致 Claude 計費耗盡時整個 agent 工作流靜默失敗。
6. **預設模型遷移討論進行中（Issue #21897）**：社群正在討論是否將預設模型從 Claude Opus 4.6 改為 Gemini 3.1 Pro，核心爭論在於成本效益與多廠商相容性，目前尚無定案。
7. **模型 fallback 永久覆寫 agent 設定缺陷（#47705）**：fallback 模型觸發後會永久寫入 `openclaw.json`，導致主要模型在後續執行中不被重試，需要手動修復設定檔。
8. **Active Memory 記憶體硬編碼 fallback 預設 OpenAI（#64884）**：Active Memory 的 fallback 模型硬編碼為 OpenAI，未使用者設定的 model catalog，對非 OpenAI 用戶造成非預期費用。
9. **v2026.6.10-alpha.3 同步測試中**：據 Releasebot 顯示，alpha.3 亦在同步測試管線中，顯示開發團隊正積極衝刺 v2026.6.10 穩定版。

---

## 2. LLM 串接實戰回報

### Claude 整合
- **計費耗盡不觸發 fallback（#95489）**：當 Anthropic API 回傳 out-of-credits 錯誤，OpenClaw 不走 fallback 鏈而直接輸出錯誤文字為結果，屬嚴重生產環境缺陷。目前建議手動設置 `fallback_providers` 並確保主帳戶有足夠額度。
  - 來源：[Issue #95489 · openclaw/openclaw](https://github.com/openclaw/openclaw/issues)
- **Active Memory 拖慢 Claude 回應速度（#66708）**：啟用 Active Memory 後，在 2026.4.14 版本起出現明顯的回應延遲與 run 中斷，關閉 Active Memory 立即改善。此問題在 v2026.6.9 尚未完全修復。
  - 來源：[Issue #66708 · openclaw/openclaw](https://github.com/openclaw/openclaw/issues/66708)

### 多模型支援現況
- OpenClaw 在 2026 年 4 月引入 provider manifest，可在執行中動態切換 LLM 而無需重建工作流。目前官方支援：GPT-5.5（Codex）、Claude API、Gemini、DeepSeek、OpenRouter、Ollama、LM Studio、Gemma 4。
  - 來源：[OpenClaw April 2026: 6 Model Providers | MindStudio](https://www.mindstudio.ai/blog/openclaw-april-2026-model-agnostic-provider-manifest)
- **Gemini 整合穩定**：Gemini MCP 與 OpenClaw 的整合透過 Composio 已有正式文件支援。
  - 來源：[How to integrate Gemini MCP with OpenClaw | Composio](https://composio.dev/toolkits/gemini/framework/openclaw)

### 模型切換常見踩坑
據 [Using OpenClaw with Claude, GPT, Gemini, and Ollama](https://amjid.au/insights/using-openclaw-with-claude-gpt-gemini-and-ollama/) 整理，常見問題包括：
1. 自訂 provider 缺少 `baseUrl` 欄位
2. 更換設定後未重啟 Gateway
3. 舊 session 快取了舊模型設定
4. Docker 網路隔離導致 container 間無法通訊
5. 模型名稱格式不正確（如缺少 provider prefix）
6. Provider API 相容性設定遺漏

---

## 3. 社群反應與討論亮點

- **ClawStat.us 的「Skip This Version」警告**引發社群熱議：有使用者指出這類評估機制本身就有價值，建議 OpenClaw 官方建立更透明的版本穩定度標示。
- **預設模型遷移討論（#21897）** 反映出社群對「廠商鎖定」的敏感度上升，部分進階使用者明確表示希望預設模型保持中立，不強綁特定廠商。
- **OpenClaw 社群 Discord** 有志願者分工的結構（Admin、Moderation、Help、Reddit、Events Lead），社群運作模式相對成熟，且公開了 [community 政策文件](https://github.com/openclaw/community)。
- **OpenClaw Rough Week** 部落格文章（openclaw.ai/blog）坦承本週版本問題，顯示開發團隊有透明溝通的文化，但也有用戶抱怨 QA 流程應在 release 前就攔截這些回歸缺陷。

---

## 4. 值得追蹤的後續議題

- **v2026.6.10 正式版釋出時程**：beta.2 已於 6/21 釋出，關注記憶體儲存遷移修復（#95495、#90361）與 Claude fallback 修復（#95489）是否完整納入。
  - 追蹤：[Releases · openclaw/openclaw](https://github.com/openclaw/openclaw/releases)
- **預設模型決策（#21897）**：若改為 Gemini 3.1 Pro 作為預設，可能影響大量依賴 Claude 的現有 agent 工作流，需關注社群反饋走向。
- **Active Memory 架構重構**：記憶體 fallback 硬編碼 OpenAI（#64884）與記憶體管理失序（#43747）等積壓問題，顯示 Active Memory 模組需要更深層的架構調整。
- **GitHub Copilot agent runtime 整合**：作為 v2026 年第二季新增功能，後續使用者實戰經驗與穩定性值得持續觀察。
- **Codex Supervisor 插件套件**：2026 年上半年新增，Workboard 中的 agent 協調工具實際效能仍待社群大量回報。
