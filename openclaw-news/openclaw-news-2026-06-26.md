# OpenClaw 每日情報｜2026-06-26

> 搜尋時間範圍：過去 24 小時（UTC 2026-06-25 ～ 2026-06-26）

---

## 1. 今日重點摘要

1. **2026.6.11-beta.1 上線（6 月 25 日）**：新版本帶來更強的頻道控制能力，包含 Slack relay 模式、原生 Mattermost `/oc_queue` 指令，以及 per-DM 模型覆寫（per-DM model overrides），讓每個對話頻道能獨立指定使用的 LLM，不需重新建構整個工作流。

2. **2026.6.10 穩定版確認推廣**：Fast Talks 模式（短對話自動切換快速模式，結束後回復正常）從 Beta 畢業進入穩定線，Zai 模型合成與 Zhipu GLM 過載自動 failover 同步跟進。

3. **已知 Bug 清單（6 月下旬）**：目前追蹤中的問題包含：thinking-signature repair 在 pre-stream API rejection 時失敗、Discord CDN 過期後附件遺失、LINE 音訊轉寫漏失、cron 恢復時標籤錯誤、記憶體索引提前退出、OAuth 批次音訊轉寫回退、以及 Telegram inline button 在 2026.6.9 後失效等。

4. **計費 / Rate Limit 混淆 Bug**：`buildRateLimitCooldownMessage()` 函式未偵測 billing 餘額耗盡，導致 API 餘額不足時顯示「⚠️ All models are temporarily rate-limited」而非要求充值的正確訊息，已有多位使用者受影響。

5. **Issue #96743（6 月 25 日開啟）**：使用者 xuwenhao 回報新 Bug，標記待 maintainer 審核並補充資訊，細節尚未公開。

6. **Exponential Backoff Bug 關閉（#5159）**：OpenClaw 內建的指數退避邏輯已知有缺陷，但該 issue 被標記為「not planned」關閉，建議使用者在應用層自行實作 retry 邏輯。

7. **RAFT CLI Wake Bridge 加入**：新版加入了 `openclaw agent --message-file` 指令與 RAFT CLI wake bridge，讓遠端喚醒與以檔案驅動的代理工作流更易整合。

8. **Android 設定面板強化**：Android 客戶端的設定詳情頁面大幅改善，提升移動端設定可視性與操控性。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）

- **PinchBench 排名第一**：Claude Sonnet 4.6 在 OpenClaw 的 PinchBench 基準測試中以 **86.9%** 成功率居冠，GPT-5.4 以 86.0% 緊隨其後。
- **per-DM 模型覆寫**：2026.6.11 新增的 per-DM model overrides 讓用戶對每個對話頻道指定 Claude 版本，無需更動整體設定。
- **費用注意**：計費失敗時系統誤報 rate limit，若出現「所有模型暫時限速」提示，應先檢查 API 餘額而非等待冷卻。

### GPT（OpenAI）

- **GPT-5.5 via Codex**：已整合至 provider manifest，ChatGPT Plus/Pro 訂閱用戶可在不額外付費 API 帳單的情況下路由 OpenClaw 任務至 GPT-5.5。
- **PinchBench 86.0%**：效能與 Claude Sonnet 4.6 非常接近，適合已持有 OpenAI 訂閱的用戶。

### 其他 Provider

- **Gemini、DeepSeek、OpenRouter、Ollama、LM Studio、Gemma 4**：六大 Provider 均已納入 Provider Manifest，支援執行期切換，毋需重建 workflow。
- **Zhipu GLM failover**：GLM 過載時 OpenClaw 現可自動切換，降低中文語境任務的服務中斷率。

**參考來源**：
- [I Wired OpenClaw to Claude, ChatGPT & Telegram — Medium](https://medium.com/@mohit15856/i-wired-openclaw-to-claude-chatgpt-telegram-heres-what-actually-works-8278906edccc)
- [Best LLM for OpenClaw: Gemini 3.1 Pro vs GPT-5.5 vs Claude Opus 4.7 — DEV Community](https://dev.to/matthewrevell/best-llm-for-openclaw-gemini-31-pro-vs-gpt-55-vs-claude-opus-47-2026-3na4)
- [OpenClaw Rate Limit Exceeded 429 — LaoZhang AI Blog](https://blog.laozhang.ai/en/posts/openclaw-rate-limit-exceeded-429)
- [OpenClaw April 2026: 6 Model Providers at Runtime — MindStudio](https://www.mindstudio.ai/blog/openclaw-april-2026-model-agnostic-provider-manifest)

---

## 3. 社群反應與討論亮點

- **Discord 社群**：OpenClaw 社群在官方 Discord 持續活躍，每週舉辦 Weekly Claw 活動，並設有專屬的 `r/OpenClaw` 與 `r/Clawdbot` 子版。
- **Telegram inline button 失效議題**：2026.6.9 後的 Telegram inline button 問題在社群引發討論，Telegram 使用者在升版前建議先在測試環境驗證。
- **混淆計費訊息**：rate limit 與帳單耗盡的混淆 Bug 是近期社群抱怨頻率最高的問題之一，有用戶指出等待數小時後才意識到是帳單問題而非真正的限速。
- **GitHub PR 活躍**：6 月 21 日 stable-upgrade 日後，社群紛紛回報各自整合情境的測試結果，Telegram、Slack、WhatsApp、Discord 及 cron 任務均是重點測試對象。

**參考來源**：
- [OpenClaw Community GitHub — Policies & Documentation](https://github.com/openclaw/community)
- [OpenClaw Release Notes — Releasebot](https://releasebot.io/updates/openclaw)
- [OpenClaw Bug Issue #79396](https://github.com/openclaw/openclaw/issues/79396)

---

## 4. 值得追蹤的後續議題

1. **計費混淆 Bug 修復進度**：`buildRateLimitCooldownMessage()` 的計費偵測問題目前尚無明確修復版本，預計後續 patch 版本會修正，建議關注 release notes。
2. **Telegram inline button 修復**：2026.6.9 引入的回退問題影響大量 Telegram 機器人用戶，追蹤對應 issue 修復時程。
3. **Exponential Backoff 替代方案**：#5159 關閉後，社群需要應用層解決方案，可追蹤相關討論串是否有官方或社群替代實作。
4. **RAFT CLI Wake Bridge 實測**：新功能剛推出，生產環境的穩定性待社群實測驗證，值得觀察後續 issue 回報。
5. **per-DM Model Override 使用案例**：此功能對多模型混用工作流影響重大，預期社群會分享各種配置心得與最佳實踐。
