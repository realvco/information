# OpenClaw 每日情報 — 2026-07-13

> 搜尋時間範圍：過去 24 小時（UTC）
> 資料來源：官方 X.com、GitHub、社群論壇、技術部落格

---

## 1. 今日重點摘要

1. **ClawRouter 正式整合**：最新主要 beta 版本新增內建 ClawRouter 提供者插件，支援 credential-scoped 動態模型探索，同時支援 OpenAI 相容傳輸與原生 Anthropic/Gemini 傳輸，並提供跨 OpenClaw 使用面的預算報告功能。

2. **GPT-5.6 支援上線**：OpenClaw 新版本已加入 GPT-5.6 的支援，配合 ClawRouter 可在執行時期動態切換，無需重新建置。

3. **對話式安裝引導（Conversational Onboarding）**：新增 Crestodian 設定流程橫跨 CLI、Gateway、Web 安裝及 macOS App，提供類型化操作、精確審批綁定、遮罩憑證提示及隔離 session transcript。

4. **Apple Watch 語音支援**：使用者可直接從 Apple Watch 發送語音訊息並在 Watch 上聆聽 OpenClaw 回覆，支援靜音訊息及停止語音的明確控制。

5. **Meta Provider（muse-spark-1.1）支援**：新增 muse-spark-1.1 Responses API 支援，含串流（streaming）、工具呼叫（tool calls）、加密推理重播（encrypted reasoning replay）及獨立 npm/ClawHub 發行。

6. **Control UI 重新設計 + 多語言文件**：Session 標題由首次訊息自動生成，Control UI 及文件已支援 12 種語言，並新增預設 / 每 agent 的 utilityModel 路由，降低 session 標題生成成本。

7. **GitHub 開放 PR 數量上限調整**：GitHub 儲存庫近日更新規則，每位作者最多可同時開啟 20 個 PR，反映社群貢獻量持續上升；7 月 12 日有多位社群成員（steipete、tsroten、HirokiKobayashi-R）開啟新 issue。

8. **Android 多 agent 切換強化**：在 Android 上可直接從對話螢幕切換使用中的 agent，同一 canonical session 保留 chat、Talk mode 及 home canvas。

9. **穩定性與安全性改善**：本週期聚焦 Gateway 路由 & 配額強化、SSRF 防護補強、Docker 及 Windows 路徑修復，以及離線與語音行動聊天穩定度提升。

---

## 2. LLM 串接實戰回報

### 2-1. Gemini 圖片識別失敗（OpenAI 相容模式）

- **問題**：在 OpenClaw 的 OpenAI 相容模式下呼叫 Gemini 進行圖片識別時，會發生 JSON Schema 欄位不相容錯誤，導致圖片傳送失敗。
- **根因**：OpenAI-compatible 模式的 JSON Schema 欄位格式與 Gemini 原生格式存在差異。
- **解法**：升級至 OpenClaw 2026.2.21 或更新版本（該版本新增 Gemini 3.1 model refs），並確認切換至 Gemini 原生傳輸而非 OpenAI 相容模式。
- **來源**：[Apiyi.com Blog - OpenClaw Gemini 圖片識別修復指南](https://help.apiyi.com/en/openclaw-gemini-image-recognition-fix-openai-compatible-mode-guide-en.html) / [DEV Community - Using Gemini with OpenClaw](https://dev.to/matthewrevell/using-gemini-with-openclaw-setup-guide-real-use-cases-2i48)

### 2-2. 虛假 Rate Limit 報錯（GitHub Issue #32828）

- **問題**：OpenClaw 對所有已設定的模型顯示「API rate limit reached」，但實際 API 在外部測試時完全正常。
- **根因**：cooldown 觸發原因被硬編碼為 `rate_limit`，不論實際失敗原因（逾時、認證錯誤、帳單問題等），一律觸發 cooldown。
- **影響**：使用者即便 API 額度充足，也會被錯誤地進入無限 cooldown 循環。
- **來源**：[GitHub Issue #32828](https://github.com/openclaw/openclaw/issues/32828) / [GitHub Issue #23996](https://github.com/openclaw/openclaw/issues/23996)

### 2-3. Anthropic OAuth Token 被禁用（2026.04.04 起）

- **問題**：從 2026 年 4 月 4 日起，Anthropic 禁止在第三方工具中使用訂閱 OAuth token（`sk-ant-oat01-` 前綴）。
- **影響**：原先透過 Claude Max 訂閱授權 OpenClaw 的使用者，必須改用獨立 API key。
- **注意**：cooldown 邏輯可能對 OAuth token 認證失敗的處理方式與一般 429 不同，須確認設定。
- **來源**：[OpenClaw API Rate Limit 管理指南](https://www.getopenclaw.ai/help/rate-limits-quota-management)

### 2-4. 多模型設定常見錯誤

- 缺少 `baseUrl` 欄位（自訂 provider 必填）
- Gateway 設定更改後未重啟
- stale session state 快取舊模型
- Docker 網路問題
- 模型名稱格式錯誤
- provider API 相容性設定遺漏

- **來源**：[OpenClaw 模型切換指南](https://www.getopenclaw.ai/en/help/switching-models-provider-config) / [Model providers 文件](https://docs.openclaw.ai/concepts/model-providers)

---

## 3. 社群反應與討論亮點

- **X.com 官方帳號**（[@openclaw](https://x.com/openclaw)）持續活躍，近期版本公告（2026.5.4、2026.5.26）獲得社群廣泛轉發，討論聚焦於 Gateway 速度提升與 Windows 穩定性改善。
- 社群成員 [@joshavant](https://x.com/joshavant/status/2027177555191583175) 宣佈成為 OpenClaw maintainer，其首個主要功能「external secrets management」已合併。
- [@chrysb](https://x.com/chrysb/status/2030526852146549140) 評論 context engine plugins 的架構突破，指出此前 context 管理完全硬編碼於 core，插件無法提供替代 context 策略，此改變意義重大。
- **Discord** 社群設有 `#help`、`#users-helping-users` 等頻道，定期舉辦 **Weekly Claw** 活動。
- **Reddit**：社群由 r/OpenClaw 及 r/Clawdbot 兩個子版塊維護，由專屬 Reddit Lead（VACInc）負責管理。

---

## 4. 值得追蹤的後續議題

| 議題 | 說明 |
|------|------|
| GitHub Issue #23996 | 虛假 `rate_limit` cooldown 硬編碼問題，尚待完整修正 |
| ClawRouter 穩定性 | 新上線功能，多 provider 動態路由在大量並發場景下的穩定性待觀察 |
| Gemini 原生 multimodal 模式 | OpenAI 相容模式下的 Schema 相容性問題是否有官方修正計畫 |
| muse-spark-1.1 擴大測試 | Meta provider 目前處於 beta，實際用戶回報的穩定性與工具呼叫相容性值得追蹤 |
| Apple Watch 語音品質 | 新功能上線，社群實際使用體驗（延遲、識別準確率）尚待回報 |
| Anthropic OAuth 替代方案 | 禁用後是否有官方提供更簡便的 API key 管理機制 |

---

*本報告依據截至 2026-07-13 UTC 的公開資訊整理，若有錯誤或遺漏請至 [GitHub Issues](https://github.com/openclaw/openclaw/issues) 反映。*
