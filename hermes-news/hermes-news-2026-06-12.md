# Hermes-Agent 每日情報 — 2026-06-12

> 資料搜尋時間範圍：過去 24 小時（UTC 基準），結合最新公開版本資訊彙整。

---

## 1. 今日重點摘要

1. **最新穩定版 v0.16.0「Surface Release」已於 2026-06-05 釋出**，過去 24 小時 GitHub 無新版本 tag；社群主要在測試與反饋本版新功能。
2. **原生桌面應用程式正式登場**：v0.16.0 在一週內以 100 個 PR、159 個 commit 完成 Electron 桌面 app，支援 macOS/Linux/Windows，提供拖放上傳、行內模型選擇器、多 profile 並行 session、一鍵更新等功能，大幅降低非技術用戶的入門門檻。
3. **Web 管理面板（Admin Panel）上線**：全瀏覽器操作的後台管理介面，涵蓋 MCP catalog、messaging channels、credentials、webhooks、memory、OIDC / username-password 登入等管理功能。
4. **安全性修補（v0.16.0）**：修復 CVE-2026-48710（Starlette 版本釘定）、SSRF off-loop 強化、subprocess credential stripping（防止憑證洩漏至子程序環境）。
5. **GitHub Stars 突破 180,000**：自 2026-02-25 發布後不到四個月達到此里程碑，是 2026 年成長最快的開源 agent 框架。
6. **v0.15.0「Velocity Release」架構大翻修**：run_agent.py 從 16,083 行縮減至 3,821 行（-76%），Kanban 進化為真實多 agent 平台（orchestrator 自動分解任務、swarm topology、per-task model override）；session_search 效能提升 4,500 倍。
7. **xAI Grok（SuperGrok OAuth）與本地 proxy 支援**：v0.15.0（Foundation Release，2026-05-16）新增 xAI Grok via SuperGrok OAuth、OpenAI-compatible local proxy、x_search 整合。
8. **多平台閘道擴展**：單一 Hermes 實例可同時服務 Telegram、Discord、Slack、WhatsApp、Signal、CLI，所有平台共享同一 session 與 memory。

---

## 2. LLM 串接實戰回報

### 已知 Bug：Rate Limit 錯誤顯示完整 traceback（Issue #1826）

- 觸發 API rate limit（HTTP 429）時，Hermes 目前將完整 Python traceback 直接輸出給使用者，UX 極差。
- 期望行為：顯示友善訊息並依設定進行 configurable retry，而非直接噴出堆疊追蹤。
- **來源**：[Issue #1826 · NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent/issues/1826)

### Gemini 429 錯誤被誤判為「配額耗盡」（Issue #38804）

- Gemini 短暫速率限制（窗口性的 429）在 Hermes 中被標記為「quota exhausted」，實際上配額並未耗盡，只是暫時限流。
- 錯誤訊息中標示「Your quota will reset after XXs」，秒級重置暗示這是限速窗口，而非真正配額耗盡，但 Hermes 的錯誤分類邏輯有誤。
- 此問題已知，尚未在 v0.16.0 中修復。
- **來源**：[Issue #38804 · NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent/issues/38804)

### 「overloaded」伺服器錯誤誤觸 Credential Rotation（Issue #14038）

- 當 provider 回傳暫時性「server overloaded」時，Hermes 將其分類為 `rate_limit`，並設定 `should_rotate_credential=True`。
- 這導致 credential pool 在僅發生 2 次錯誤後就將 API key 標記為「耗盡」，使後續所有 retry 失效。
- 在 Claude、OpenAI 等偶有過載的 provider 上，這個問題在高流量時段尤為明顯。
- **來源**：[Issue #14038 · NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent/issues/14038)

### fallback_model 在 Auth 失效時不會自動觸發（Issue #7230）

- 當 primary provider 的 OAuth token 過期（如 openai-codex 回傳 HTTP 401），`config.yaml` 中設定的 `fallback_model` 不會被觸發。
- 問題根因：fallback 邏輯在 credential resolution 階段之後才執行，AuthError 不會傳遞到 fallback 路徑。
- 目前暫行解法：手動切換模型或定期刷新 token。
- **來源**：[Issue #7230 · NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent/issues/7230)

### Setup Wizard 靜默跳過 API Key 重設（已知 UX 問題）

- 執行 `hermes setup` 重新設定時，若 `~/.hermes/.env` 已存在任何值，精靈會靜默跳過 API key 輸入提示。
- 後果：使用者以為重設成功，實際上仍使用舊的（可能無效的）API key。
- 暫行解法：手動編輯 `~/.hermes/.env` 刪除舊值後再執行 `hermes setup`。
- **來源**：[Hermes Auth Error Authenticating: 6 Fixes (2026) - betterclaw.io](https://www.betterclaw.io/blog/hermes-auth-error-fix)

### Copilot API 不支援 Classic PAT（ghp_* token）

- GitHub Copilot API 不接受傳統 Personal Access Token（`ghp_*` 格式），需使用 OAuth 方式認證。
- 若 `gh auth token` 回傳 `ghp_*`，必須改以 `hermes model` 透過 OAuth 認證。
- **來源**：[FAQ & Troubleshooting | Hermes Agent](https://hermes-agent.nousresearch.com/docs/reference/faq)

### Gemini 整合現況（無原生 Provider）

- 截至 2026 年 4 月，Hermes 尚無原生 Google GenAI provider，Gemini 只能透過 OpenRouter 或 Google 的 OpenAI-compatible endpoint 接入。
- 兩條路徑均增加了一層轉換層，在複雜多工具鏈中偶發參數格式問題或 streaming token 遺失。
- **來源**：[Best Gemini Models for Hermes Agent - remoteopenclaw.com](https://www.remoteopenclaw.com/blog/best-gemini-models-for-hermes)

---

## 3. 社群反應與討論亮點

- **桌面 App 引爆討論**：v0.16.0 的原生桌面 app 被多篇 Medium 文章分析，認為這是 Hermes-Agent「走向主流」的關鍵節點，不再依賴終端機降低了非工程師的使用門檻。
  - 參考：[Hermes Agent Desktop App: Everything You Need to Know (Medium)](https://medium.com/@tentenco/hermes-agent-desktop-app-everything-you-need-to-know-about-nous-researchs-self-improving-ai-agent-3cb59bd31e5f)
- **180K Stars 里程碑**：社群在多個討論區慶祝 4 個月達到 180,000 GitHub Stars，部分人認為是「2026 年成長最快的 OSS agent 框架」。
- **Bug 追蹤活躍**：Rate limit、credential rotation 等問題在 GitHub Issues 上有活躍討論，社群成員也貢獻了多個 workaround。
- **NVIDIA 開發者社群提問**：有開發者在 NVIDIA Developer Forums 詢問是否能申請提升 RPM 上限（200 RPM）以進行 Hermes-Agent 測試，顯示有 GPU/加速場景的整合需求。
  - 參考：[Rate limit issue on NVIDIA Developer Forums](https://forums.developer.nvidia.com/t/rate-limit-issue-can-i-get-an-upgrade-to-200-rpm-for-hermes-agent-testing/370383)

---

## 4. 值得追蹤的後續議題

| 議題 | 狀態 | 重要程度 |
|------|------|----------|
| Issue #1826：Rate limit 顯示 traceback，需 UX 改善 | Open，未列入 v0.16.0 | 高 |
| Issue #38804：Gemini 429 誤判為 quota exhausted | Open | 高 |
| Issue #14038：overloaded 錯誤誤觸 credential rotation | Open | 高 |
| Issue #7230：fallback_model 在 AuthError 時不觸發 | Open | 中-高 |
| 原生 Google GenAI provider 支援計畫 | 未公告 | 中 |
| 桌面 App（Electron）正式版與 public preview 的差異 | v0.16.0 為公開預覽 | 中 |
| Admin Panel 的 RBAC / 多用戶管理功能 | 路線圖未公開 | 低-中 |
| CVE-2026-48710（Starlette）上游修補追蹤 | 已釘定版本，待上游 patch | 低 |

---

*資料來源：*
- [GitHub Releases · NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent/releases)
- [Release Hermes Agent v0.16.0 (2026.6.5) — The Surface Release](https://github.com/NousResearch/hermes-agent/releases/tag/v2026.6.5)
- [Release Timeline | Hermes Agent](https://hermesagents.net/evolution/)
- [Hermes Agent v0.16 Reference Guide](https://blakecrosley.com/guides/hermes)
- [Changelog — Hermes Agent](https://hermes-ai.net/changelog/)
- [AI Providers | Hermes Agent Docs](https://hermes-agent.nousresearch.com/docs/integrations/providers)
- [Credential Pools | Hermes Agent Docs](https://hermes-agent.nousresearch.com/docs/user-guide/features/credential-pools)
- [Issue #1826 - Rate limit errors traceback](https://github.com/NousResearch/hermes-agent/issues/1826)
- [Issue #38804 - Gemini rate limits misclassified](https://github.com/NousResearch/hermes-agent/issues/38804)
- [Issue #14038 - overloaded errors exhaust credential pool](https://github.com/NousResearch/hermes-agent/issues/14038)
- [Issue #7230 - fallback_model not triggered on AuthError](https://github.com/NousResearch/hermes-agent/issues/7230)
- [Nous Research on X - v0.16.0 announcement](https://x.com/NousResearch/status/2063075092859637888)
- [Hermes Agent Desktop App (Medium)](https://medium.com/@tentenco/hermes-agent-desktop-app-everything-you-need-to-know-about-nous-researchs-self-improving-ai-agent-3cb59bd31e5f)
