# OpenClaw 每日情報 — 2026-07-12

> 資料涵蓋範圍：過去 24 小時（UTC 2026-07-11 ～ 2026-07-12）

---

## 1. 今日重點摘要

1. **v2026.7.1-beta.3 持續迭代中**：繼 beta.1（7/2）與 beta.2（7/5）之後，本週 beta.3 已上線，主要帶來 GPT-5.6 模型支援、`openclaw attach` 外部 Harness 掛載流程，以及 Telegram Codex 配對與引導功能。
2. **GPT-5.6 納入 Catalog**：OpenClaw 現已在 catalog、capability 與 runtime 選擇路徑全面識別 GPT-5.6 模型系列，讓使用者可直接從設定介面切換至最新 OpenAI 模型。
3. **`openclaw attach` 新工作流程**：支援將外部 Harness 掛載至現有 Gateway Session，讓 Codex 風格的互動工作流程更容易恢復與檢查，顯著降低斷線重連成本。
4. **Event-Driven Cron 上線**：新增 `on-exit` 排程種類，可在指定指令結束後喚醒 Agent；Session 目標 run 亦可乾淨地 detach，無需強制中斷。
5. **GitHub 近日 Issue/PR 活躍（7/11）**：單日開出 Issue #104517～#104524 共多條，其中 #104518 標記 security + P2；PR #104718「統一外部 Session 與原生 Catalog 分頁 Agent」帶入多個相容性與 session state 風險，仍在審查中。
6. **iOS 原生 App 翻新**：更新導覽、設定、展示與 Talk 控制介面，新增 Gateway 語音 Provider，並加入瑞典語行動端在地化；Android 端同步跟進。
7. **Telegram 功能增強**：支援豐富 HTML 輸出、進度草稿更忠實渲染、HTML Table 安全正規化、提及（mentions）與 spooled handler 路由修正。
8. **已知 bug — 虛假 Provider Cooldown（Issue #23996）**：Provider 未真正觸發 rate limit 卻被同時放入 cooldown，且 cooldown 原因被寫死為 `rate_limit`，無論實際失敗類型為何，尚未修復。
9. **Anthropic 訂閱付費路徑變更仍有後續影響**：4 月起 Claude 訂閱已無法涵蓋 OpenClaw API 呼叫，改走 Extra Usage 計費；部分使用者仍因 OAuth token（`sk-ant-oat01-`）無法在 Anthropic Console 看到用量，導致誤判「沒有使用」卻遭 rate limit。

---

## 2. LLM 串接實戰回報

### Anthropic Claude

- **訂閱轉 Extra Usage 後的 auth 困境**
  4 月 4 日 Anthropic 正式切斷透過訂閱跑第三方 Harness 的路徑，OpenClaw 改採 Extra Usage 獨立計費。使用者回報 OAuth token 不顯示於 Console 用量儀表板，導致收到 rate limit 錯誤時難以判斷是真的超量還是 token 失效。
  解法：定期手動 refresh OAuth key；或改用 API key（`sk-ant-api03-`）以取得 Console 可視的用量紀錄。
  來源：[Medium — Fix OpenClaw Missing Auth After Anthropic Changes](https://medium.com/@ulmeanuadrian/anthropic-just-cut-off-my-ai-agents-heres-how-i-fixed-it-in-20-minutes-741384d27080)、[XDA Developers](https://www.xda-developers.com/claude-subscribers-just-lost-access-to-openclaw-and-other-third-party-toolsunless-they-pay-more/)

- **工具呼叫格式錯誤 → 400 Error**
  使用 `openai-completions` 格式進行多輪工具呼叫時，會收到 400 錯誤。必須改用 `anthropic-messages` 格式。常見踩雷：baseUrl 缺少 `/v1`、未停用 `anthropic-beta` 與 `reasoning` 參數、未清除舊 session cache。
  來源：[apiyi.com — OpenClaw Claude API 設定指南](https://help.apiyi.com/en/openclaw-claude-api-apiyi-anthropic-messages-guide-en.html)

### OpenAI GPT

- **GPT-5.6 剛進 catalog，穩定性待觀察**：v2026.7.1-beta.3 正式支援 GPT-5.6，但部分使用者在 beta 期間遇到 runtime 選擇路徑偶發性回退至舊模型版本的問題，官方尚未有明確說明。

### 多 Provider 通用問題

- **Provider 同時進入假性 Cooldown（#23996）**：即使 upstream API 未真正回傳 429，OpenClaw 的 provider-level throttling 有機率把多個 provider 同時放入冷卻，造成全部 fallback 失效。暫行解法：手動重啟 Gateway 以清除錯誤狀態。
  來源：[GitHub Issue #23996](https://github.com/openclaw/openclaw/issues/23996)

- **Provider 切換相容性差異**：不同 Provider 的參數名稱與工具呼叫格式各異，runtime 切換時容易因 format mismatch 失敗。建議在 provider manifest 中明確指定 format，而非依賴自動偵測。
  來源：[MindStudio — OpenClaw April 2026: 6 Model Providers](https://www.mindstudio.ai/blog/openclaw-april-2026-model-agnostic-provider-manifest)

---

## 3. 社群反應與討論亮點

- **r/OpenClaw、r/Clawdbot 及官方 Discord**：`openclaw attach` 流程獲得正面評價，被認為是 Codex 工作流程的重要補強，但也有聲音指出文件不夠完整，新手難以直接上手。
- **GitHub Discussion #188842**：有使用者抱怨某次更新後 OpenClaw「變得無用」，引發討論；維護團隊回應確認為 beta 版本的已知問題，正式版將修正。
- **PR #104718 審查爭議**：「統一外部 Session 與 Catalog 分頁」的 PR 因涉及 security boundary 與 session state 相容性風險，正在收到多條審查意見，合併時程不確定。
- **iOS 在地化討論**：瑞典語支援的新增引發社群詢問「下一個是哪個語言」，有多位貢獻者表示願意協助新增語系。

來源：[openclaw-community/openclaw-hub Discussions](https://github.com/openclaw-community/openclaw-hub/discussions)、[Releasebot — OpenClaw July 2026](https://releasebot.io/updates/openclaw)

---

## 4. 值得追蹤的後續議題

1. **PR #104718 合併結果**：涉及 security 邊界的重大 PR，需持續追蹤審查進度與最終合併策略。
2. **Issue #23996 修復時程**：虛假 Provider Cooldown 影響多 LLM 切換穩定性，屬高頻踩雷問題，等待官方 patch。
3. **GPT-5.6 在 beta 的穩定性**：目前為 beta.3，正式版行為仍有可能調整，建議觀察 runtime 選擇路徑是否有回退現象。
4. **`openclaw attach` 文件補充**：功能已上線但社群反映文件不足，後續是否有官方教學或範例發布值得關注。
5. **Event-Driven Cron `on-exit` 使用案例累積**：新排程機制上線不久，社群實戰案例與潛在 edge case 尚待浮現。
6. **Extra Usage 計費爭議**：Anthropic 訂閱改付費後，OpenClaw 的 Claude 使用成本透明度問題持續引發討論，後續是否有官方計費整合方案待觀察。

---

*報告產生時間：2026-07-12 UTC*
*來源參考：[GitHub openclaw/openclaw](https://github.com/openclaw/openclaw) / [Releasebot](https://releasebot.io/updates/openclaw) / [Medium auth fix](https://medium.com/@ulmeanuadrian/anthropic-just-cut-off-my-ai-agents-heres-how-i-fixed-it-in-20-minutes-741384d27080) / [GitHub Issue #23996](https://github.com/openclaw/openclaw/issues/23996) / [MindStudio](https://www.mindstudio.ai/blog/openclaw-april-2026-model-agnostic-provider-manifest)*
