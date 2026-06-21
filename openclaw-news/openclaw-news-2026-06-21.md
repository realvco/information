# OpenClaw 每日情報 — 2026-06-21

> 資料蒐集時間：2026-06-21 UTC｜涵蓋過去 24 小時動態

---

## 1. 今日重點摘要

1. **穩定版維持 2026.6.8**：今日（6/20）官方定性為「主線強化日（mainline hardening day）」，不發布新穩定版，建議正式環境繼續留在 2026.6.8，除非刻意測試 beta 通道。

2. **2026.6.9-beta.1 正在測試中**：Beta 重點涵蓋 Telegram 富文字交付、代理恢復機制、Cron 交付目標、MCP Server 設定驗證、SDK 審批／session 行為，以及 Provider 定價中繼資料的改善。

3. **Telegram 交付大幅強化**：新版以 Rich HTML 傳送、保留 Markdown 與貼圖路徑、更正確渲染進度草稿與指令輸出，並確保 mentions 和 spooled handlers 走在正確的交付路徑上。

4. **代理恢復機制改進**：在中斷或部分執行後，透過重試、終態確認、壓縮後使用情況、session 歷史修復及回覆調和（reply reconciliation）讓更多 turn 能走向可見的最終結果。

5. **Gemini CLI 整合隔離**：新版 Gemini CLI 在隔離的 runtime home 中使用所選 OpenClaw OAuth／API 金鑰 auth profile，防止機器環境的 Google 憑證覆蓋指定 profile。

6. **iMessage 可靠性提升**：iMessage 新增常駐 inbound recovery、持久 echo markers、block streaming 及 outbound transport，可在重啟與閒置期間存活。

7. **預設模型遷移爭議（Issue #21897）**：社群正在討論是否將預設 LLM 從 Claude Opus 4.6 換成 Gemini 3.1 Pro，牽涉 system prompt 格式、tool calling 格式、token 計算方式及模型別名體系的多方相容性問題，目前尚無定案。

8. **模型別名版本號更新（Issue #38312）**：`gemini` 別名預計升至 `3.1-pro-preview`（2026/3/9 前舊版棄用），`gpt` 別名升至 5.4，提醒仍依賴預設別名的使用者評估影響。

9. **廣泛六月版功能全貌**：整個六月版本涵蓋更豐富的 Telegram 交付、更強的代理恢復、更深的 Codex 整合、新的 hosted web search、擴充的 Plugin 處理，以及橫跨 Web、Mobile、CLI、安全、儲存與 Cron 工作流的更新。

---

## 2. LLM 串接實戰回報

### Gemini 圖像辨識失敗問題

在使用 OpenAI 相容模式呼叫 Gemini 時，常見錯誤為：

```
Invalid JSON payload received. Unknown name 'patternProperties' at
'tools[0].function_declarations[3].parameters.properties[4].value':
Cannot find field.
```

**根本原因**：OpenAI 相容模式下的 JSON Schema 欄位與 Gemini 原生格式不相容，與 Gemini 視覺能力無關。

**三種解法**：
| 方案 | 適用情境 | 說明 |
|---|---|---|
| 原生格式（`google-generative-ai`） | 純 Gemini 環境 | 最推薦 |
| API Proxy Service | 混合多模型 | 中間層轉換 |
| 手動 Schema 清理 | 自訂系統 | 最彈性但費工 |

來源：[Apiyi Blog — OpenClaw Gemini 圖像辨識修復指南](https://help.apiyi.com/en/openclaw-gemini-image-recognition-fix-openai-compatible-mode-guide-en.html)

---

### 預設模型遷移：Claude vs Gemini 技術差異

從 Anthropic 切換至 Google 需同步調整：
- **System prompt 格式**：Claude 使用 `system` role + XML 風格 `tool_use` block；Gemini 使用 `systemInstruction` + `function_declaration/function_call`
- **Context 大小**：Gemini 2.5 Pro 支援 1M token（Claude 為 200K）
- **模型別名**：`ANTHROPIC_MODEL_ALIASES` 已有 `opus-4.6 → claude-opus-4-6` 等對應，Google 一側尚無等效體系

來源：[Issue #21897 · openclaw/openclaw](https://github.com/openclaw/openclaw/issues/21897)

---

### 模型推薦（2026 當前社群共識）

| Provider | 推薦用途 |
|---|---|
| Claude Sonnet | 大多數 agent 工作 |
| Claude Haiku | 高量廉價任務 |
| Claude Opus | 最難推理任務 |
| Gemini 3.1 Pro | 需要長 context 或 Google Search grounding |

來源：[Best LLM for OpenClaw 2026 — DEV Community](https://dev.to/matthewrevell/best-llm-for-openclaw-gemini-31-pro-vs-gpt-55-vs-claude-opus-47-2026-3na4)、[Model providers · OpenClaw 文件](https://docs.openclaw.ai/concepts/model-providers)

---

## 3. 社群反應與討論亮點

- **r/OpenClaw / Discord**：OpenClaw 有活躍的 Community Staff 志工團隊，管理 r/OpenClaw 與 r/Clawdbot 兩個 subreddit，並定期舉辦 Weekly Claw 等社群活動。今日（6/20）主要討論圍繞 beta.1 測試回報，社群整體對 Telegram 富文字的改善反應正面。

- **Gemini 預設模型討論**：Issue #21897 引發廣泛討論，部分使用者擔心切換後的工具呼叫相容性，也有人認為 1M context 是關鍵誘因。目前意見分歧，官方尚未公告定案時程。

- **模型切換排坑**：社群整理出常見切換失敗原因清單：缺少 `baseUrl`、gateway 未重啟、session 快取舊模型、Docker 網路問題、模型名稱格式錯誤，以及 Ollama 缺少 `api: "openai-responses"` 設定。

來源：[GitHub openclaw/community](https://github.com/openclaw/community)、[How to Change Your AI Model in OpenClaw](https://www.getopenclaw.ai/help/switching-models-provider-config)

---

## 4. 值得追蹤的後續議題

- **2026.6.9 正式發布時程**：目前仍在 beta，觀察何時升為 stable 及最終 changelog。
- **Issue #21897 預設模型定案**：OpenClaw 是否正式從 Claude 切換為 Gemini 3.1 Pro 作為預設，將影響所有新安裝用戶的 out-of-box 體驗。
- **Issue #38312 別名棄用期限**：`gemini 3.0-x` 與 `gpt 5.3-x` 別名將於 2026/3/9 前棄用，確認遷移時程。
- **iMessage 穩定性後續**：新的常駐 iMessage 整合是否在各平台表現穩定，預期會有更多社群測試回報。
- **Hosted Web Search**：六月版新增的 hosted web search 功能尚待更多使用評測。

---

*報告由自動化 routine 生成。搜尋無法覆蓋 Discord 私人頻道及 X.com 付費牆內容，如有遺漏請參閱官方管道。*
