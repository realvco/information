# OpenClaw 每日情報｜2026-06-14

---

## 1. 今日重點摘要

1. **穩定版 v2026.6.1 確立新版本命名規則**：自 6 月起，版本號第三位改為月度 Patch 計數器（而非日期），正式版為 `2026.6.1`，npm latest 維持此版本。([GitHub Releases](https://github.com/openclaw/openclaw/releases/tag/v2026.6.5))

2. **Beta 列車持續推進至 v2026.6.5-beta.2**：6 月 9 日標記 beta.6，帶入 MCP 工具結果強制轉換（coercion）、QQBot 思考標籤剝離、並行 Web 搜尋支援三項主要改動。([Releasebot](https://releasebot.io/updates/openclaw))

3. **6 月 12 日更新：治理與穩定性強化**：涵蓋 fail-closed 審批機制、逐字稿與工具邊界收緊、Channel 交付去重、iMessage 與 Telegram 可恢復路徑、MCP 傳輸行為優化、首次回覆診斷可視化。([OpenClaw Updates](https://openclaw.com.au/updates))

4. **QQBot 推理標籤剝離（Reasoning Stripping）正式上線**：送出前移除 `<thinking>` 標籤，僅保留最終答案，防止內部模型自言自語流出給使用者。([Releasebot](https://releasebot.io/updates/openclaw))

5. **MCP 工具結果強制轉換修復**：先前有效圖片可能被誤判為畸形 image block；現在 MCP tool-result block 在抵達 provider converter 前會先正確轉換。([OpenClaw Changelog - Fastio](https://fast.io/resources/openclaw-changelog-guide/))

6. **Skill Workshop 隨 v2026.6.1 正式推出**：Tokenjuice 與 Copilot agent runtime 被外部化為官方插件，Skill Workshop 作為統一的技能管理介面隨穩定版上線。([OpenClaw Release Notes - Releasebot](https://releasebot.io/updates/openclaw))

7. **指數退避內建 Bug 尚待修復**：官方確認 OpenClaw 內建的指數退避（exponential backoff）機制存在已知 bug，遭遇 429 錯誤時需依賴手動設定 fallback 或應用層自行重試。([Rate Limit Guide - aifreeapi](https://www.aifreeapi.com/en/posts/openclaw-api-key-error-troubleshooting-guide))

8. **社群調節爭議延燒**：4 月底 v2026.4.26 更新引發閘道崩潰大量回報；此外有使用者因在技術脈絡中提及比特幣區塊高度被 Discord 伺服器封禁，引發社群反彈，部分長期使用者宣布轉向 Hermes Agent。([Piunika Web](https://piunikaweb.com/2026/04/29/openclaw-update-triggering-gateway-crashes-issues/))

9. **Claude Sonnet 4.6 在 PinchBench 工具呼叫評測領先**：於 OpenClaw 的 PinchBench 基準測試中以 86.9% 成功率居首，GPT-5.4 以 86.0% 緊跟其後。([Best LLM for OpenClaw - DEV Community](https://dev.to/matthewrevell/best-llm-for-openclaw-gemini-31-pro-vs-gpt-55-vs-claude-opus-47-2026-3na4))

---

## 2. LLM 串接實戰回報

### 支援的 Provider 概覽
OpenClaw 自 2026 年 4 月起支援六大 Provider 免重建切換（provider manifest）：GPT-5.5（Codex）、Claude API（Anthropic）、Gemini、DeepSeek、OpenRouter、Ollama/LM Studio、Gemma 4。
來源：[OpenClaw April 2026 - MindStudio](https://www.mindstudio.ai/blog/openclaw-april-2026-model-agnostic-provider-manifest)

### 模型推薦與效能比較
- **Sonnet**：多數 agent 工作（推理、工具呼叫、泛用）的標準選擇
- **Haiku**：低成本高吞吐任務（分類、路由、簡單轉換）
- **Opus**：最高難度推理場景，成本為次要考量
- **Gemini 3.1 Pro**：大型 context 程式碼審查、最低每工作成本、免費開發層、原生多模態，被評為多數部署的最佳預設
來源：[Best LLM for OpenClaw - DEV Community](https://dev.to/matthewrevell/best-llm-for-openclaw-gemini-31-pro-vs-gpt-55-vs-claude-opus-47-2026-3na4)

### 常見踩坑：Rate Limit 與認證
- 最常見錯誤：HTTP 429（60%）、Context Window Exceeded（25%）、Auth/Token Expired（15%）
- **已知問題**：當 Claude API 遭遇 429 時，系統會把該 provider 放入冷卻，但**不會自動降級模型**（Opus → Sonnet → Haiku），須手動設定 `openclaw models fallbacks add`
- 遭遇 401/429/gateway token mismatch 時，建議先執行 `openclaw doctor --fix` 自動修復大多數設定問題
- 內建指數退避有已知 bug，重試邏輯需在應用層自行處理
來源：[GitHub Issue #6966](https://github.com/openclaw/openclaw/issues/6966)、[GitHub Issue #48603](https://github.com/openclaw/openclaw/issues/48603)、[aifreeapi 故障排除指南](https://www.aifreeapi.com/en/posts/openclaw-api-key-error-troubleshooting-guide)

### 多 Auth Profile 管理需求
社群對「單一視圖顯示所有 auth profile 的 rate limit 與用量狀態」有明確需求，GitHub Issue #24865 持續追蹤中，尚未在穩定版實現。
來源：[GitHub Issue #24865](https://github.com/openclaw/openclaw/issues/24865)

---

## 3. 社群反應與討論亮點

- **4 月版本崩潰事件餘波**：v2026.4.26 造成閘道崩潰的事件中，受影響最重的是從 .5 直跳至 .26 的使用者；社群普遍認為 OpenClaw 缺乏可靠的穩定發布管道。來源：[Piunika Web](https://piunikaweb.com/2026/04/29/openclaw-update-triggering-gateway-crashes-issues/)

- **Discord 封禁事件**：開發者 Steinberger 因伺服器規則禁止任何密碼貨幣提及，將在技術脈絡下提及比特幣的使用者封禁，社群批評規則過於強硬。

- **使用者向 Hermes Agent 遷移**：部分長期 OpenClaw 使用者表示將 Hermes Agent 作為主要工具，保留 OpenClaw 作備援，理由是 Hermes 更新更平順、bug 更少。

- **Skill Workshop 受期待**：社群對 Skill Workshop 作為統一技能管理入口的反應正面，但仍有人等待文件補全。

---

## 4. 值得追蹤的後續議題

| 議題 | 狀態 | 追蹤建議 |
|---|---|---|
| 內建指數退避 bug 修復 | 確認存在，未修復 | 關注 GitHub PR 動態 |
| 自動模型降級（Opus→Sonnet→Haiku）on 429 | Feature Request (#6966) | 追蹤 Issue 進度 |
| 多 Auth Profile 用量儀表板 | Feature Request (#24865) | 追蹤 Issue 進度 |
| v2026.6.5 穩定版發布時程 | Beta 階段 | 關注 beta.N 進展 |
| Skill Workshop 文件完整度 | 初步上線 | 關注官方 docs 更新 |
| Discord 社群管理改善 | 爭議中 | 觀察官方回應 |

---

*資料來源*：[GitHub openclaw/openclaw](https://github.com/openclaw/openclaw/releases)、[Releasebot OpenClaw](https://releasebot.io/updates/openclaw)、[DEV Community LLM 比較](https://dev.to/matthewrevell/best-llm-for-openclaw-gemini-31-pro-vs-gpt-55-vs-claude-opus-47-2026-3na4)、[OpenClaw 官方更新頁](https://openclaw.com.au/updates)、[Piunika Web 崩潰報導](https://piunikaweb.com/2026/04/29/openclaw-update-triggering-gateway-crashes-issues/)
