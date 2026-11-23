# Hermes-Agent 每日情報 — 2026-11-23

> 資料來源涵蓋截至 2026-11-23 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **過去 24 小時無重大更新，目前穩定版仍為 v0.23.3**：該版於 2026-11-20 00:34 UTC 發布，約 370 個合併 PR，完整整理版說明延到 v0.24.0。（[v0.23.3](https://github.com/NousResearch/hermes-agent/releases/tag/v0.23.3)）

2. **新增自動 canary 建置**：`canary.20261122T064429Z-v0.23.3`（11-22 06:44 UTC），僅有 commit 中繼資料，沒有 release 說明。（[releases feed](https://github.com/NousResearch/hermes-agent/releases.atom)）

3. **有使用者回報 v0.23.3 的 Anthropic OAuth（Claude Max）請求回傳 429**：回報者指出 v0.23.1 正常，尚待維護者確認。（[#159068](https://github.com/NousResearch/hermes-agent/issues/159068)）

---

## 2. LLM 串接實戰回報

- **Claude**：[#159068](https://github.com/NousResearch/hermes-agent/issues/159068) 回報 v0.23.3 使用 Anthropic OAuth（Claude Max）時請求回傳 429 `rate_limit_error`，v0.23.1 則正常（使用者回報，未證實為迴歸）。
- **通用錯誤處理**：[#159038](https://github.com/NousResearch/hermes-agent/issues/159038) 指出單純的 403 預設被當成認證問題，供應商政策拒絕會被誤判為 API key 錯誤。
- **GPT／Gemini／本地模型**：過去 24 小時未查到獨立實測回報，不予編造。

---

## 3. 版本與 release 動態

| 版本 | 發布時間（UTC） | 說明 |
|------|----------|------|
| canary.20261122T064429Z-v0.23.3 | 2026-11-22 06:44 | 自動 canary，無 release 說明 |
| canary.20261122T000948Z-v0.23.3 | 2026-11-22 00:09 | 自動 canary，無 release 說明 |
| canary.20261121T064420Z-v0.23.3 | 2026-11-21 06:44 | 自動 canary，無 release 說明 |
| **v0.23.3** | 2026-11-20 00:34 | 目前最新穩定版；約 370 個合併 PR、關閉 60 個 issue；完整說明延到 v0.24.0 |

更新方式：CLI 執行 `hermes update`；Docker 拉取 `nousresearch/hermes-agent:stable`。

來源：[releases feed](https://github.com/NousResearch/hermes-agent/releases.atom)、[v0.23.3](https://github.com/NousResearch/hermes-agent/releases/tag/v0.23.3)。

---

## 4. 社群討論與已知問題

新增 issue（皆為使用者回報，是否成立尚待維護者確認）：

| Issue | 說明 |
|------|------|
| [#159068](https://github.com/NousResearch/hermes-agent/issues/159068) | v0.23.3 Anthropic OAuth（Claude Max）回傳 429，v0.23.1 正常 |
| [#159067](https://github.com/NousResearch/hermes-agent/issues/159067) | Gateway 重啟（SIGUSR1）在 stop() 拋例外時卡在 draining |
| [#159043](https://github.com/NousResearch/hermes-agent/issues/159043) | 已移除的憑證會被過期行程以記憶體內的 pool 重新寫回 |
| [#159039](https://github.com/NousResearch/hermes-agent/issues/159039) | 開機維護的 integrity_check 可能阻塞 gateway 啟動 |
| [#159035](https://github.com/NousResearch/hermes-agent/issues/159035) | v0.23.3 更新後 Ark 防竄改警報無限循環 |
| [#159034](https://github.com/NousResearch/hermes-agent/issues/159034) | Bot-to-Bot 傳送被以「profile authority is not ready」拒絕 |
| [#159031](https://github.com/NousResearch/hermes-agent/issues/159031) | ACP 的「Deny always」實際只是單次拒絕 |
| [#159047](https://github.com/NousResearch/hermes-agent/issues/159047) | kanban：未指定 workspace 路徑的 dir/worktree 任務被接受後兩次 spawn_failed |
| [#159060](https://github.com/NousResearch/hermes-agent/issues/159060) | Desktop：工作階段搜尋失敗時顯示「No sessions match」，沒有重新驗證提示 |
| [#159046](https://github.com/NousResearch/hermes-agent/issues/159046) | 提案：Desktop（Windows）提供不建立開始功能表／桌面捷徑的選項 |

來源：[Issues](https://github.com/NousResearch/hermes-agent/issues?q=is%3Aissue+sort%3Acreated-desc)。
