# Hermes-Agent 每日情報 — 2026-10-09

> 資料來源涵蓋截至 2026-10-09 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **v0.21.6 於 10/8 發布，為新的穩定版**：彙整自 v0.21.5 以來約 2,100 個已合併 PR，是新穩定版發布流程的第一個 release；完整的整理版說明預告將隨 v0.22.0 提供。（[Release v0.21.6](https://github.com/NousResearch/hermes-agent/releases/latest)）

2. **本次 release 包含多項安全修補**：dashboard 驗證有四項修正（偽造 `X-Forwarded-For` 可繞過登入速率限制、未驗證登入請求可無限寫入稽核日誌、公開 `/auth/` 路由缺少請求本文大小限制、原生登入可能將登入碼送往非 loopback 的轉址）；另有 git filter 強化與 email gateway 寄件者允許清單繞過的修正。（[Release v0.21.6](https://github.com/NousResearch/hermes-agent/releases/latest)）

3. **模型目錄新增 GPT-6.1 Sol、Claude Sonnet 5.5、Claude Haiku 5.5**，`hermes model` 並新增本地模型支援。（[Release v0.21.6](https://github.com/NousResearch/hermes-agent/releases/latest)）

4. **新 issue 中有一項 P1：個人 API key 被誤判為 OAuth，導致計費錯誤**（標示為 duplicate）。（[#135376](https://github.com/NousResearch/hermes-agent/issues/135376)）

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）
- v0.21.6 的模型目錄新增 Claude Sonnet 5.5 與 Claude Haiku 5.5。（[Release v0.21.6](https://github.com/NousResearch/hermes-agent/releases/latest)）

### GPT（OpenAI）
- v0.21.6 的模型目錄新增 GPT-6.1 Sol。（[Release v0.21.6](https://github.com/NousResearch/hermes-agent/releases/latest)）
- 個人 API key 被誤分類為 OAuth 而造成計費錯誤，P1，已標為 duplicate。（[#135376](https://github.com/NousResearch/hermes-agent/issues/135376)）

### Gemini（Google）
- 今日未查到新的 Gemini 專屬回報。

### 其他供應商／本地模型
- `hermes model` 新增本地模型支援，並提供 Linux x64／arm64 的 llama.cpp CUDA 建置。（[Release v0.21.6](https://github.com/NousResearch/hermes-agent/releases/latest)）

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 類型 | 重點 |
|------|----------|------|------|
| **v0.21.6** | 2026-10-08 | Patch rollup | **目前最新穩定版**；約 2,100 PR；dashboard／git filter／email gateway 安全修補；即時語音轉錄（CLI、TUI、Desktop）；語音輪次獨立模型路由；per-profile 外掛 host 與統一的 Settings ▸ Plugins 頁面；summary-first 的 `hermes status`；憑證上傳與隱形 Unicode 終端指令的核准提示；Discord 設定流程會檢查 bot token。Desktop app、Termux 套件與 Microsoft Store 版本未變動；未列出 breaking changes |
| v0.21.5（v2026.9.24） | 2026-09-24 | Patch rollup | 約 460 PR；Desktop plugin SDK、Simple／Advanced 介面模式、Connectors 頁面取代 MCP 分頁 |
| v0.21.4（v2026.9.21） | 2026-09-21 | Patch rollup | 約 1,800 PR；host-wide gateway singleton lock、`--format stream-json`、`skills.auto_load` |
| v0.21.3（v2026.9.14） | 2026-09-14 | Patch rollup | 遠端 dashboard session 穩定性、state.db writer 洩漏修正 |

來源：[Releases](https://github.com/NousResearch/hermes-agent/releases)。

---

## 4. 社群討論與已知問題

優先度標籤取自 GitHub issue 頁面。

| Issue | 說明 | 優先度 |
|------|------|------|
| [#135376](https://github.com/NousResearch/hermes-agent/issues/135376) | 個人 API key 被誤分類為 OAuth，造成計費錯誤（duplicate） | P1 |
| [#135405](https://github.com/NousResearch/hermes-agent/issues/135405) | Hermes Desktop 更新失敗，exit code 2（needs-repro） | P2 |
| [#135392](https://github.com/NousResearch/hermes-agent/issues/135392) | 全新的 Nous Cloud instance 進入 gateway 重啟迴圈 | P2 |
| [#135368](https://github.com/NousResearch/hermes-agent/issues/135368) | Desktop 頭像「Generate」回報沒有影像後端 | P2 |
| [#135366](https://github.com/NousResearch/hermes-agent/issues/135366) | `prompt.submit` 回傳 queued 卻從未執行（Windows） | P2 |
| [#135365](https://github.com/NousResearch/hermes-agent/issues/135365) | Windows 上 HERMES_HOME 使用正斜線導致路徑不一致 | P2 |
| [#135344](https://github.com/NousResearch/hermes-agent/issues/135344) | Kanban dispatcher 誤判 macOS 上運作中的 worker 已被回收 | P2 |
| [#135383](https://github.com/NousResearch/hermes-agent/issues/135383) | 內建 "solstice" 外掛因缺少 httpx 無法載入（已關閉） | P3 |
| [#135367](https://github.com/NousResearch/hermes-agent/issues/135367) | 內建外掛 toolset 間歇性回報 "Tool does not exist"（needs-repro） | P3 |
| [#135347](https://github.com/NousResearch/hermes-agent/issues/135347) | mem0 OSS + pgvector 每次更新後損壞 | P3 |
| [#135412](https://github.com/NousResearch/hermes-agent/issues/135412) | disk-cleanup 在非 git 安裝下會刪除永久性的測試腳本 | 未標示 |
| [#135411](https://github.com/NousResearch/hermes-agent/issues/135411) | 功能提案：讓 execute_code sandbox 的工具允許清單可設定 | 未標示 |

來源：[Issues](https://github.com/NousResearch/hermes-agent/issues)。
