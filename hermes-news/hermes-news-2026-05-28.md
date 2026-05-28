# Hermes-Agent 每日情報 — 2026-05-28

> 資料時間範圍：過去 24 小時（UTC 基準）
> 來源：GitHub Releases、NousResearch 官方、社群論壇、技術部落格

---

## 1. 今日重點摘要

1. **5/27 新增 Issues 涵蓋 Docker、Modal.com、工具註冊表問題**：過去 24 小時內，GitHub 新增 issues 涉及 Docker 映像相容性、Docker Compose 配置、PyPI 封裝、Modal.com 雲端執行環境，以及工具註冊表（tool registry）異常，反映 v0.14.0 部署後的長尾問題持續浮現。

2. **目前最新穩定版：v0.14.0（Foundation Release，2026-05-16）**：本版本為過去 24 小時的基準，共納入 808 commits、633 個合併 PR、1,393 個檔案異動、165,061 行新增，關閉 545 個 issues（含 12 個 P0、50 個 P1），215 名社群貢獻者參與。

3. **Computer-Use 突破：非 Anthropic 模型現可驅動桌面**：新的 `cua-driver` 後端讓任何具備視覺能力的模型（不限 Claude）皆可操控桌面，打破 computer-use 對 Anthropic 的單一依賴，為開源模型使用者開啟完整 desktop agent 工作流。

4. **xAI Grok SuperGrok OAuth 整合上線**：`grok-4.3` 以 SuperGrok OAuth 進入 Hermes，context window 擴至 1M token，SuperGrok 訂戶無需另購 API key 即可在 Hermes 中使用 Grok。

5. **Microsoft Teams 成第 21 個訊息平台**：完整 Microsoft Graph 整合（auth、webhook 監聽器、pipeline plugin runtime、對外發送），使用者可從任意 Teams 頻道、DM 或群組與 agent 對話。

6. **HuggingFace Skills Hub 預設整合**：社群 skill 索引（`huggingface.co/skills`）預設接入 Skills Hub，使用者可在 `hermes skills browser` 中直接安裝社群 skill，無需額外配置。

7. **OpenAI-compatible 本地代理**：任何已以 OAuth 認證的 Hermes provider（Claude Pro、ChatGPT Pro、SuperGrok）皆可轉為本地端點，供 Codex、Aider、Cline、Continue 等工具直接呼叫，無需再申請獨立 API key。

8. **PyPI 版本滯後問題（已知）**：v0.14.0 發布後，PyPI 仍提供 0.13.0，截至 5/18 仍未同步。建議急需 v0.14.0 功能的使用者透過 git installer 安裝，等待 PyPI 更新。

---

## 2. LLM 串接實戰回報

### xAI Grok

- **SuperGrok OAuth HTTP 403 問題（Issue #26847，持續開啟）**：大量 SuperGrok 標準訂閱用戶（$30/月）回報 OAuth 登入流程與 token 儲存均正常，但在 xAI 後端推理時持續收到 HTTP 403。xAI 儀表板顯示 API 存取目前僅開放 **SuperGrok Heavy** 層，與 xAI 官方公告及 Hermes 文件所描述的「所有 SuperGrok 用戶可用」相矛盾。此為整合發布後的重大 day-one 問題。
  - 參考：[GitHub Issue #26847](https://github.com/NousResearch/hermes-agent/issues/26847) ｜ [xAI Grok OAuth 文件](https://hermes-agent.nousresearch.com/docs/guides/xai-grok-oauth)

### OpenAI / GPT

- **gpt-5.5 / openai-codex 在 v0.14.0 子 agent 工作流不穩定（Issue #33075）**：使用 Codex OAuth 的子 agent 工作流幾乎無法使用，但官方 Codex CLI 本身仍穩定運作。推測問題在 Hermes 的 openai-codex provider adapter，而非 OpenAI 服務端。
  - 參考：[GitHub Issue #33075](https://github.com/NousResearch/hermes-agent/issues/33075)

### Google Gemini

- **工具呼叫穩定性不足**：Hermes 缺乏原生 Google GenAI provider，Gemini 請求透過 OpenAI 相容層路由，可能導致工具呼叫格式問題。社群評估截至 2026-04，Gemini 的工具呼叫可靠性低於 Claude 或 OpenAI 模型。
  - 參考：[remoteopenclaw.com Gemini 指南](https://www.remoteopenclaw.com/blog/best-gemini-models-for-hermes)

### 推薦模型（2026-05 社群共識）

| 模型 | 費用（輸入/輸出，per 1M tokens） | 適用場景 |
|------|----------------------------------|----------|
| Claude Sonnet 4.6 | $3 / $15 | 推理品質與工具呼叫首選 |
| DeepSeek V4 | $0.30 / $0.50 | 最佳性價比，90% 快取折扣 |
| Gemini 2.5 Pro | $1.25 / $10 | 1M context，強推理，工具呼叫待改善 |
| grok-4.3（SuperGrok Heavy） | OAuth 訂閱制 | 1M context，需 Heavy 層 |

---

## 3. 社群反應與討論亮點

- **「Agent Runtime 成為作業系統」**：Context Studios 部落格（2026-05-27）以 v0.14.0 為例，指出 Hermes 的架構已超越傳統「agent 框架」定義，Kanban 看板、heartbeat、zombie detection、computer-use 多模型支援，使其更接近「agent 作業系統」的定位，吸引大量技術社群轉發討論。
  - 參考：[Context Studios Blog](https://www.contextstudios.ai/blog/hermes-v014-agent-runtimes-operating-systems)

- **xAI SuperGrok 整合爭議**：xAI 官方宣稱「所有 SuperGrok 用戶均可使用」，但實際上只有 SuperGrok Heavy（更高費用層）可用。Reddit 與 GitHub Issues 均出現大量抱怨，部分用戶認為此為誤導性廣告，要求 NousResearch 在文件中明確標注限制。

- **四版本密集發布（4/23–5/16）引發討論**：v0.11、v0.12、v0.13、v0.14 在不到 4 週內連續發布（合計 3,900+ commits、2,500+ PRs），社群對開發速度表示讚嘆，但同時擔憂穩定性與升級測試覆蓋率是否充分。

- **從 OpenClaw 遷移的使用者增加**：OpenClaw 4/26 更新後 Gateway 崩潰事件使部分用戶轉入 Hermes，社群中出現多篇「遷移指南」與比較文章。

---

## 4. 值得追蹤的後續議題

1. **SuperGrok HTTP 403 問題解決時間**：Issue #26847 影響範圍廣，xAI 是否會開放標準訂閱 API 存取，或 NousResearch 是否會更新文件修正誤導，待觀察。

2. **gpt-5.5 / openai-codex 子 agent 穩定性修復**（Issue #33075）：openai-codex OAuth adapter 問題可能需要 Hermes 端的 provider 層更新，追蹤下一個 patch release 是否包含修正。

3. **v0.15.0 預計功能**：根據 v0.14 釋出節奏（每兩週一個次版本），下個版本預計在 5/30 前後，社群已在 GitHub 討論中提議的功能包含：多 agent Kanban 視覺化、Skills Marketplace 評分系統。

4. **Gemini 原生 provider 計劃**：Hermes 目前依賴相容層處理 Gemini，若官方增加原生 Google GenAI provider 將顯著改善工具呼叫穩定性。追蹤相關 PR 或 roadmap 更新。

5. **PyPI 封裝問題**：Docker 及 PyPI 的滯後問題在 5/27 仍有 issue 開啟，關注官方是否提供正式解決方案（如自動化 release pipeline 改善）。

---

*報告產生時間：2026-05-28（UTC）*
*來源彙整：[GitHub Releases](https://github.com/NousResearch/hermes-agent/releases) ｜ [Context Studios Blog](https://www.contextstudios.ai/blog/hermes-v014-agent-runtimes-operating-systems) ｜ [Digg 報導](https://digg.com/ai/1qruxedc) ｜ [Issue #26847](https://github.com/NousResearch/hermes-agent/issues/26847) ｜ [Issue #33075](https://github.com/NousResearch/hermes-agent/issues/33075) ｜ [remoteopenclaw.com](https://www.remoteopenclaw.com/blog/best-gemini-models-for-hermes) ｜ [fav0.com xAI 整合報導](https://fav0.com/en/2026-05-17/)*
