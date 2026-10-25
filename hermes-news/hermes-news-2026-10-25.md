# Hermes-Agent 每日情報 — 2026-10-25

> 資料來源涵蓋截至 2026-10-25 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **過去 24 小時無新的正式版發布，目前穩定版仍為 v0.22.5**：v0.22.5（2026-10-22 發布，Releases feed 時間戳 10-23）之後，只出現 10-24 的 canary 建置（非正式版）。（[Releases feed](https://github.com/NousResearch/hermes-agent/releases.atom)）

2. **新增 issue 以安裝／更新、gateway 與 Windows 桌面版為主**：例如 install.sh 在 IPv6 路由失效的主機上每個 URL 卡約 24 分鐘（[#144208](https://github.com/NousResearch/hermes-agent/issues/144208)）、Relay 原生 pipeline 執行緒在 socketpair 上空轉佔用約 80% CPU（[#144223](https://github.com/NousResearch/hermes-agent/issues/144223)）。以上為使用者回報，尚待維護者確認。

---

## 2. LLM 串接實戰回報

過去 24 小時未查到 Claude／Gemini／本地模型的獨立實測回報。

**GPT／OpenAI**
- Codex CLI 的 OAuth 恢復流程，在 IdP 重新登入導致 `sub` claim 改變時，會拒絕有效的 token 組合（[#144236](https://github.com/NousResearch/hermes-agent/issues/144236)，標籤 provider/openai、P2）。

**Provider 通用**
- 功能請求：讓外掛能將模型 fallback 標示為「預期內」以減少警告（[#144206](https://github.com/NousResearch/hermes-agent/issues/144206)）；以及提供外掛 API 於當輪回覆顯示短暫通知（例如是哪個模型回答，[#144205](https://github.com/NousResearch/hermes-agent/issues/144205)）。
- API server 對於派送前即被拒絕的回合仍回傳 HTTP 200，可能影響 OpenAI 相容用戶端（[#144227](https://github.com/NousResearch/hermes-agent/issues/144227)）。

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 說明 |
|------|----------|------|
| **v0.22.5** | 2026-10-22 | 目前最新穩定版；約 81 項 PR 彙整，Nous 登入重做、用量視窗方案、可插拔 delegation 後端、`hermes import-agent`，`hermes mcp serve` 與 Singularity 後端移為外掛 |
| v0.22.4 | 2026-10-21 | 桌面版移除建議膠囊、WhatsApp 登出後可安全重新配對、核准模式標籤跟隨 profile |
| v0.22.3 | 2026-10-21 | 約 620 項 PR 彙整；MCP 進度通知、`hermes plugins update`、`/loop` 倒數等；完整說明待 v0.23.0 |

另有 10-24 canary 建置（v0.22.5+canary.20261024T065630Z），無 release notes，非正式版。來源：[Releases](https://github.com/NousResearch/hermes-agent/releases)、[Releases feed](https://github.com/NousResearch/hermes-agent/releases.atom)。

---

## 4. 社群討論與已知問題

2026-10-25 新增 issue（部分，優先度取自 GitHub 標籤）：

| Issue | 說明 | 優先度 |
|------|------|------|
| [#144237](https://github.com/NousResearch/hermes-agent/issues/144237) | 桌面版 Bots 分頁把未受管 profile 顯示為正常 bot，訊息功能被靜默停用 | P2 |
| [#144236](https://github.com/NousResearch/hermes-agent/issues/144236) | Codex CLI 恢復流程在 OAuth `sub` 改變時拒絕有效 token | P2 |
| [#144227](https://github.com/NousResearch/hermes-agent/issues/144227) | API server 對派送前被拒絕的回合回傳 HTTP 200 | P2 |
| [#144226](https://github.com/NousResearch/hermes-agent/issues/144226) | `gen-` schema 之前的 side environment 選擇無法完成更新 | P2 |
| [#144223](https://github.com/NousResearch/hermes-agent/issues/144223) | Relay 原生 pipeline 執行緒空轉，各約 80% CPU | P2 |
| [#144208](https://github.com/NousResearch/hermes-agent/issues/144208) | IPv6 路由失效時安裝程式每個 URL 卡約 24 分鐘 | P2 |
| [#144200](https://github.com/NousResearch/hermes-agent/issues/144200) | Kanban spawn callback 在拋出 TypeError／ValueError 時執行兩次 | P2 |
| [#144233](https://github.com/NousResearch/hermes-agent/issues/144233) | Cron 外部 worker session 未註冊已設定的 shell hooks | P3（security） |
| [#144213](https://github.com/NousResearch/hermes-agent/issues/144213) | Windows Store 版內含有嚴重公告的 brotlicffi 1.2.0.2 | P3（security） |

以上為 issue 回報，是否成立與是否修補尚待維護者確認。來源：[Issues](https://github.com/NousResearch/hermes-agent/issues)。
