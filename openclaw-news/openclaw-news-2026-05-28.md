# OpenClaw 每日情報 — 2026-05-28

> 資料時間範圍：過去 24 小時（UTC 基準）
> 來源：GitHub Releases、社群論壇、技術部落格、X.com

---

## 1. 今日重點摘要

1. **v2026.5.26-beta.2 釋出（約 19 小時前）**：OpenClaw 發布最新 beta 版本，主打回覆可靠性、語音控制、多頻道穩定度、安全修補與可觀測性全面升級，是本週最大的更新節點。

2. **回覆速度與啟動效能大幅改善**：visible reply delivery 現已將用戶端即時發送與後端較慢的後續處理分離；指令/模型/插件 metadata 改採熱路徑快取；Gateway 啟動時避免重複掃描插件、頻道、session、費用及檔案系統，啟動時間明顯縮短。

3. **Voice / Talk 體驗升級**：realtime Talk 執行中可從 Web UI 及 Discord 語音介面即時「檢查、導向、取消、追蹤」；喚醒詞（wake-name）邏輯加寬容忍度，但不允許環境音誤觸發 agent。

4. **多頻道生產就緒改善**：Telegram 保留 typing/progress context 與論壇主題；iMessage 修正附件根目錄與重複本機來源問題；WhatsApp 恢復群組/媒體行為；Discord 語音播放與模型選擇問題修正。

5. **圖像後端替換（Sharp → Rastermill）**：新後端支援 metadata、resize、EXIF 方向校正、PNG alpha 保留最佳化，移除對 Sharp 與 WhatsApp Jimp fallback 的依賴，安裝體積縮小。

6. **安全修補：symlink 憑證拒絕恢復**：`tryReadSecretFileSync` 的 fail-closed 合約重新上線，傳入 `rejectSymlink: true` 的憑證載入器（Telegram、LINE、Zalo、IRC、Nextcloud Talk）現在會拒絕 symlink 憑證，不再靜默接受，修補早前被回歸的安全漏洞。

7. **ClawChain CVE 安全通報（本週）**：OpenClaw Weekly（2026-05-26）提示生產環境需緊急處理本週 Claw Chain 相關 CVE，具體 CVE 編號尚待官方完整公告，管理員應密切關注 [GitHub issues](https://github.com/openclaw/openclaw/issues)。

8. **NanoClaw 獲 $12M 融資**：同週生態系新聞，輕量化 OpenClaw 衍生專案 NanoClaw 完成 1,200 萬美元融資，預計強化邊緣設備與低資源環境下的 agent 部署。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）

- **OAuth token rate limit 誤報**（常見問題）：使用 Claude Pro 訂閱授權時（`sk-ant-oat01-` token），用戶頻繁收到「API rate limit reached」錯誤，但 Anthropic Console 用量儀表板並未顯示任何用量記錄。根本原因是 OAuth token 的限速邏輯與 API key 不同，且實際使用量不計入 Console。解法：執行 `openclaw doctor --fix` 或手動刷新 OAuth token。
  - 參考：[answeroverflow 社群討論](https://www.answeroverflow.com/m/1478788645472436264)

- **假性 provider cooldown（Issue #23996）**：OpenClaw 在任何 provider 失敗後，會將冷卻原因硬編碼為 `"rate_limit"`，導致即便 API 完全正常也可能觸發無限冷卻迴圈。此 bug 仍開啟中，暫解方案為設定多組 fallback provider。
  - 參考：[GitHub Issue #23996](https://github.com/openclaw/openclaw/issues/23996)

- **全模型假性 rate limit（Issue #32828）**：另一回報指出所有模型同時出現「API rate limit reached」，但各家 API 均正常運作，確認為 OpenClaw 本身的 cooldown 邏輯問題。
  - 參考：[GitHub Issue #32828](https://github.com/openclaw/openclaw/issues/32828)

- **Claude Opus 4.7 推薦用法**：1M token context 窗口採平坦計費，900K token 的請求與 100K token 的請求以同一費率計算，無長 context 附加費，對持續超過 200K token/call 的工作流具有顯著價格優勢。
  - 參考：[DEV.to LLM 比較文](https://dev.to/matthewrevell/best-llm-for-openclaw-gemini-31-pro-vs-gpt-55-vs-claude-opus-47-2026-3na4)

### 模型選配建議（社群共識）

| 用途 | 推薦模型 |
|------|----------|
| 一般 agent 工作（推理、工具呼叫） | Claude Sonnet（主流選擇） |
| 高量低複雜任務（分類、路由） | Claude Haiku |
| 超長 context 工作流 | Claude Opus 4.7（1M context 平坦計費） |
| 本地/離線部署 | Ollama（任意模型） |

---

## 3. 社群反應與討論亮點

- **4 月 26 日更新後大規模 Gateway 崩潰**：Reddit r/openclaw 帖子收到大量回覆確認相同問題，包括 Gateway 啟動崩潰/無限重啟迴圈、Discord/Telegram/其他頻道無回應、高 CPU 使用率、多個插件失效。部分長期用戶因此轉向 Hermes Agent，稱其「更新更穩定、bug 更少」。
  - 參考：[Piunikaweb 報導](https://piunikaweb.com/2026/04/29/openclaw-update-triggering-gateway-crashes-issues/)

- **Context Studios 技術評論**（5/26 週報）：評估 OpenClaw 已從「個人 AI 助理」定位演進為「多平台 agent 協調框架」，對比 Hermes Agent，OpenClaw 在頻道廣度（12+）與現有用戶基礎上具備優勢，但穩定性與可預測更新節奏仍是主要疑慮。

- **ClawSweeper 更新**（5/27）：自動掃描並建議關閉 issues/PR 的工具 ClawSweeper 於 5/27 更新，顯示 OpenClaw issues 管理工具鏈仍活躍維護。

---

## 4. 值得追蹤的後續議題

1. **ClawChain CVE 正式公告**：本週週報提及的 Claw Chain CVE 細節尚未完整公開，生產環境管理員應持續監看 [GitHub Issues](https://github.com/openclaw/openclaw/issues) 與官方部落格。

2. **v2026.5.26-beta.2 轉正式版時間點**：beta 版核心改動大（效能、安全、頻道），正式版釋出後值得評估是否升級。

3. **OAuth / provider cooldown bug 修復進度**：Issue #23996 與 #32828 均屬高影響度，追蹤何時有正式 fix 進入 release。

4. **假性 rate limit 根本解決方案**：目前官方建議 `openclaw doctor --fix` 或手動刷新 token，仍屬繞路解法，關注是否有架構層面修正。

5. **NanoClaw $12M 後續產品規劃**：邊緣部署方向是否會影響主線 OpenClaw 的資源分配與功能優先序。

---

*報告產生時間：2026-05-28（UTC）*
*來源彙整：[GitHub Releases](https://github.com/openclaw/openclaw/releases) ｜ [releasebot.io](https://releasebot.io/updates/openclaw) ｜ [Piunikaweb](https://piunikaweb.com/2026/04/29/openclaw-update-triggering-gateway-crashes-issues/) ｜ [DEV.to LLM 比較](https://dev.to/matthewrevell/best-llm-for-openclaw-gemini-31-pro-vs-gpt-55-vs-claude-opus-47-2026-3na4) ｜ [answeroverflow 討論](https://www.answeroverflow.com/m/1478788645472436264) ｜ [OpenClaw Weekly](https://www.bighatgroup.com/blog/openclaw-weekly-2026-05-26/)*
