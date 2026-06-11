# Hermes-Agent 每日情報｜2026-06-11

> 資料蒐集範圍：過去 24 小時（UTC 基準）；最新可索引資訊截至 2026-06-11

---

## 1. 今日重點摘要

1. **v0.16.0「Surface Release」為最新穩定版**（發布日：2026-06-05）。過去 24 小時內無新版本（v0.16.1 hotfix）釋出，官方 GitHub releases 頁面最後一條仍為 v0.16.0。
2. **原生桌面 App 正式推出**：Electron 架構，支援 macOS / Linux / Windows，單週內由 100 PRs、159 commits 構建完成。功能包含串流式聊天視窗、Session 列表（可封存與全文搜尋）、拖放上傳檔案、剪貼簿圖片貼上、Cmd+K 指令面板、狀態列模型快速切換器。
3. **Web 管理儀表板**：全瀏覽器 admin panel，涵蓋 MCP 目錄管理、訊息頻道、憑證管理、Webhook、記憶體、以及可插拔的 OIDC / 帳號密碼登入，大幅降低 VPS 部署的管理門檻。
4. **v0.16.0 規模統計**：874 commits、542 merged PRs、1,962 files changed、+205,216 / -399 行變更，170 位社群貢獻者，共關閉 399 個 issues（含 2 個 P0、62 個 P1、16 個安全標籤）。
5. **安全修補**：
   - 修補 Starlette ≥1.0.1 以對應 CVE-2026-48710（BadHost 漏洞）
   - SSRF URL 檢查移出 event loop 執行
   - Bedrock inference bearer token 從子程序環境變數中移除
   - `docker restart/stop/kill` 被加入危險指令黑名單
6. **模型快速切換**：model picker 現在支援全域模糊搜尋；`/undo` 指令可回溯最近 N 輪對話。
7. **已知重大 Bug 仍開放中**：
   - Issue #38804：Gemini 短暫 rate limit 被誤報為「quota exhausted」，應區分日/月配額耗盡與短暫 RPM/TPM 限速。
   - Issue #32849：OAuth token 被撤銷後冷卻 1 小時再重回輪換池，反覆失敗；正確行為應為永久標記。
   - Issue #7230：主 provider OAuth token 過期時，`config.yaml` 中的 `fallback_model` 未被觸發，直接回傳固定錯誤訊息。
8. **Hermes Desktop 首度在 Jensen Huang GTC Keynote 公開展示**，現已進入 public preview。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）

- 支援 Claude Opus 4.8、Sonnet 4.6、Haiku 4.5，透過直連 Anthropic API 或 OpenRouter 皆可。
- **Claude Code OAuth 模式限制**：透過 `hermes model → Anthropic OAuth` 驗證時，Hermes 會以 Claude Code 身份路由請求，**僅限 Claude Max 訂閱並需購買額外 usage credits**。普通 API key 用戶不受此限制。
- **2026 推薦搭配**（來自社群測試）：Claude Sonnet 4.6 整體品質最佳，工具呼叫可靠性高，為生產環境首選。

### OpenAI（GPT）

- GPT-5.4 在 Hermes 配置中常被選為「效能與費用平衡點」；GPT-4.1 在工具呼叫可靠性測試中表現穩定。
- Issue #15167：`/usage` 指令在 Telegram gateway 中無法顯示 ChatGPT 訂閱 rate-limit 視窗，原因是 `_read_codex_tokens()` 僅讀取舊版 `auth.json` slot，而 OAuth 實際寫入的是 `credential_pool`。

### Gemini（Google）

- Issue #38804（開放中）：Gemini 短暫流量限速（RPM/TPM/concurrency）被 Hermes 分類為 `quota_exhausted`，導致整個 provider 被標記失效。社群反應此 bug 在高並發場景極為擾人，呼籲盡快區分「短暫限速」與「真正配額耗盡」。來源：[GitHub Issue #38804](https://github.com/NousResearch/hermes-agent/issues/38804)

### OpenRouter

- OpenRouter 整合文件已更新，提供 200+ 模型的單一 API key 統一入口，為官方推薦的新手上手路徑。
- Composio 提供 OpenRouter MCP 與 Hermes 的整合教學。來源：[OpenRouter Hermes 整合文件](https://openrouter.ai/docs/cookbook/coding-agents/hermes-integration)

### 憑證輪換問題（跨 Provider）

- Issue #32849（開放中）：任何 provider 的 OAuth token 被撤銷後，Hermes 以 1 小時 TTL 標記為 `exhausted`，TTL 到期後破損憑證重回輪換池繼續失敗，表現為「Failed to generate context summary」並中斷 session compaction。來源：[GitHub Issue #32849](https://github.com/NousResearch/hermes-agent/issues/32849)
- Issue #14038：Z.AI 等 provider 的「temporarily overloaded」錯誤被分類為 `rate_limit`，啟動憑證輪換後僅 2 次失敗即耗盡 credential pool。來源：[GitHub Issue #14038](https://github.com/NousResearch/hermes-agent/issues/14038)
- Issue #7230：主 provider auth 失敗時 `fallback_model` 未觸發，agent 直接在 Discord 回傳固定錯誤而非切換備援。來源：[GitHub Issue #7230](https://github.com/NousResearch/hermes-agent/issues/7230)

**參考來源：**
- [Hermes Agent AI Providers 官方文件](https://hermes-agent.nousresearch.com/docs/integrations/providers)
- [Best Hermes Agent Model 2026 比較](https://www.remoteopenclaw.com/blog/best-models-for-hermes-agent)
- [Hermes Agent v0.16 參考指南](https://blakecrosley.com/guides/hermes)

---

## 3. 社群反應與討論亮點

- **X（前 Twitter）熱度**：Nous Research 官方帳號 [@NousResearch](https://x.com/NousResearch/status/2063075092859637888) 發文宣告 v0.16.0，附 changelog 連結；獨立 YouTube 評論影片「Hermes Agent Update v0.16 is HUGE!」已上線，顯示社群對 Desktop App 推出反應熱烈。
- **GTC Keynote 曝光效應**：Jensen Huang 在 GTC 上公開展示 Hermes Desktop 帶來顯著流量，GitHub stars 持續成長（已突破 10,000 stars）。
- **桌面 App 功能需求呼聲高**：社群長期呼籲 Telegram/Discord bot 管理介面、排程任務、記憶體視覺化、VPS 託管一鍵安裝，v0.16 的 Desktop + Web UI 方向被認為直接回應了這些需求。
- **安全修補受肯定**：CVE-2026-48710 修補與 Bedrock token 環境變數清除獲資安社群正面評價，但也有聲音認為危險指令黑名單應開放使用者自定義。
- **多語言支援**：Desktop App 內建完整簡體中文翻譯，部分繁體中文社群使用者詢問是否計劃加入繁體中文支援。

---

## 4. 值得追蹤的後續議題

| 議題 | 狀態 | 優先度 |
|------|------|--------|
| Issue #38804：Gemini rate limit 誤分類修復 | 開放中 | 高 |
| Issue #32849：撤銷 token 應永久標記，而非 1 小時冷卻 | 開放中 | 高 |
| Issue #7230：fallback_model 未在 auth 失敗時觸發 | 開放中 | 高 |
| Desktop App 繁體中文 i18n 支援 | 社群反映中 | 中 |
| v0.16.1 hotfix 釋出時間（桌面 App 首發後常見修補週期） | 待觀察 | 中 |
| NVIDIA/skills 加入 Skills Hub 後的實際可用技能清單更新 | 已上線，待社群驗證 | 低 |
| Hermes Desktop 公開預覽期回饋整合進度 | 進行中 | 中 |

---

*本報告根據公開可索引的 GitHub releases、issues、官方文件、X 貼文及社群討論彙整，不含未經驗證的資訊。*
