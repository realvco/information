# Hermes-Agent 每日情報 — 2026-05-30

> 搜尋範圍：過去 24 小時（UTC 2026-05-29 ～ 2026-05-30）

---

## 1. 今日重點摘要

1. **v0.15.1 Patch Release（5 月 29 日）**：v0.15.0 發布翌日即推出熱修補，主因是 loopback 模式（Docker、hosted Hermes、新安裝）的 dashboard 出現無限重新載入迴圈，已緊急修復。
2. **v0.15.2 同日推出（5 月 29 日）**：修正 wheel 與 sdist 套件中缺少 `plugin.yaml` manifest 的打包問題，確保安裝完整性。
3. **v0.15.0「Velocity Release」昨日正式發布（5 月 28 日）**：1,302 commits、747 merged PRs、321 位社群貢獻者，560+ 個 issue 關閉（含 15 個 P0、65 個 P1、19 個安全標記）。
4. **核心程式碼大幅瘦身**：主執行檔 `run_agent.py` 從 16,083 行降至 3,821 行（-76%），拆分為 14 個內聚模組；每次對話的 function calls 減少 47%；`session_search` 速度提升 4,500 倍且改為免費。
5. **Kanban 進化為真正的多代理平台**：跨 104 個 PR 完成，支援 orchestrator 自動分解任務、swarm 拓撲、定時任務、每個任務獨立 worktree，以及每個任務可指定不同模型。
6. **Bitwarden Secrets Manager 整合**：以單一 bootstrap token 取代多個 provider API key，大幅簡化密鑰管理；Skill Bundles 讓一條 slash command 可載入整個工作流。
7. **新增 provider 與平台**：Krea 2 Medium + Large 圖像生成、FAL 移植為 plugin、深度 xAI 整合（SuperGrok OAuth，grok-4.3 升至 1M context window）、ntfy 成為第 23 個訊息平台。
8. **Promptware 防禦機制（Brainworm-class）**：加入對抗 prompt injection 的防禦層，針對 Brainworm 類型攻擊提供結構性保護。
9. **v0.15.1 其他修補項目**：Kanban worker SIGTERM 訊號處理、`/model` picker 統一、`/yolo` session bypass、完整 19,932 筆 skills.sh catalog 復原、`.md` 媒體交付修復、gateway probe-stepdown 安全性改善。

---

## 2. LLM 串接實戰回報

### 2-1. DeepSeek V4 Pro via OpenRouter 導致 gateway 崩潰迴圈

- **現象**：以 `deepseek/deepseek-v4-pro` 為預設模型並透過 OpenRouter 使用時，Hermes Agent gateway 進入崩潰迴圈，Telegram bot 完全無回應。
- **根因**：OpenRouter 上游 provider「Io Net」對 DeepSeek V4 Pro 施加激進的 rate limit，限制觸發時回傳 `HTTP 401 - User not found`（而非標準 429），導致 Hermes 的 fallback 機制無法正確識別並切換。
- **暫行解法**：在 OpenRouter settings/integrations 頁面加入個人 DeepSeek API key，使用個人限額取代 OpenRouter 共享池。
- **來源**：[Issue #16677 — DeepSeek V4 Pro via OpenRouter causes gateway crash loop](https://github.com/NousResearch/hermes-agent/issues/16677)

### 2-2. OpenRouter max_tokens 硬編碼觸發 HTTP 402

- **現象**：使用 OpenRouter 為 provider 時，Hermes 將 `max_tokens` 硬編碼為各模型上限（例如 Claude Sonnet/Haiku 4.5 為 64,000），OpenRouter 在呼叫前以「預留全額 max_tokens × 輸出費率」計算抵押，導致帳戶餘額不足時直接回傳 402 錯誤。
- **影響模型**：Claude Sonnet 4.6、Claude Haiku 4.5（凡透過 OpenRouter 使用 Anthropic 模型者皆受影響）。
- **Feature Request**：允許使用者在 per-profile 設定中覆寫 `max_tokens`，目前 issue 仍開放。
- **來源**：[Issue #22879 — Make max_tokens configurable per-profile](https://github.com/NousResearch/hermes-agent/issues/22879)

### 2-3. Token 膨脹問題（已修復）

- **現象**：部分工具/skills 啟用後，CLI 模式每次對話消耗約 6–8k input tokens，Telegram 模式高達 15–20k（正常的 2–3 倍）。
- **根因**：gateway 在 `hermes-agent` 目錄啟動，載入了開發用 `AGENTS.md` 及其他測試資料。
- **修復方式**：改為在使用者 home 目錄啟動，已在後續版本修復。
- **來源**：[Hermes Agent Troubleshooting Guide (2026)](https://www.getopenclaw.ai/blog/hermes-agent-troubleshooting)

### 2-4. Claude OAuth vs. API key：Hermes 官方建議

- Hermes 官方文件建議：選用 Anthropic OAuth 時，優先使用 Claude Code 本身的 credential store，而非將 token 複製到 `~/.hermes/.env`，確保 OAuth token 的自動 refresh 機制能正常運作。
- 支援模型：Claude Opus 4.6、Sonnet 4.6、Haiku 4.5（可直接透過 Anthropic API 或 OpenRouter）。
- **來源**：[AI Providers | Hermes Agent Documentation](https://hermes-agent.nousresearch.com/docs/integrations/providers)

### 2-5. Tirith 安全模組阻擋合法命令（已知行為）

- Hermes 的安全模組 Tirith 對 `curl | sh` 類型命令直接硬性封鎖（hard block），而非提供人工審批提示，部分進階使用者認為缺乏彈性。
- 目前無官方設定可調整此行為，需透過其他方式執行此類安裝指令。
- **來源**：[FAQ & Troubleshooting | Hermes Agent](https://hermes-agent.nousresearch.com/docs/reference/faq/)

---

## 3. 社群反應與討論亮點

- **Teknium（NousResearch）官方 X 貼文**：[@Teknium](https://x.com/Teknium/status/2060088572049559893) 發文宣布 v0.15.0，強調「747 PRs by 321 Contributors」並感謝社群，提及 Skill Bundles、MCP Catalog、Krea 2、Opus 4.8、Qwen 3.7 及深度 xAI 整合為主要亮點，獲大量轉發與回應。
- **v0.15.1 熱修補速度獲讚**：dashboard 無限重載問題在 v0.15.0 發布約 24 小時內修復，社群反映「hotfix turnaround 很快」，但也有聲音質疑「為何大版本沒有更完整的 loopback 環境測試」。
- **Kanban 多代理架構引發討論**：Towards AI 上一篇比較 Hermes Agent vs Claude Code vs OpenClaw 的長文（[18 real tasks test](https://pub.towardsai.net/i-tested-hermes-agent-vs-claude-code-vs-openclaw-on-18-real-tasks-the-10-week-old-one-cheats-by-0f2881a10213?gi=9123006f42ac)）引發廣泛討論，主要爭點在於 Kanban 的 orchestrator 自動分解任務是否「過度自治」。
- **DeepSeek + OpenRouter 崩潰問題社群聲量持續**：[Issue #16677](https://github.com/NousResearch/hermes-agent/issues/16677) 留言持續增加，多名使用者確認複現，目前 maintainer 尚未給出根本修復時程。
- **MindStudio 文章引發正反論戰**：《[AI Tools That Got Replaced in 2026](https://www.mindstudio.ai/blog/ai-tools-replaced-2026-claude-code-hermes-agent-killed-cursor-openclaw)》將 Hermes Agent 列為「殺手級工具」之一，引發 OpenClaw 社群不滿，認為文章具有誤導性。

---

## 4. 值得追蹤的後續議題

| 議題 | 說明 | 相關連結 |
|------|------|----------|
| DeepSeek V4 Pro via OpenRouter 崩潰根本修復 | 目前僅有 workaround（自備 DeepSeek API key），issue 開放中 | [Issue #16677](https://github.com/NousResearch/hermes-agent/issues/16677) |
| max_tokens per-profile 可設定化 | 影響所有透過 OpenRouter 使用高 max_tokens 模型的使用者 | [Issue #22879](https://github.com/NousResearch/hermes-agent/issues/22879) |
| v0.15.1 dashboard 修復是否完整 | 已有使用者回報 v0.15.1 在特定 Docker 設定下仍有問題，待確認 | [Releases · NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent/releases) |
| Kanban 多代理成熟度與文件 | 跨 104 PR 開發的功能，文件仍不完整，實際 swarm topology 設定方式待補 | [hermes-agent/RELEASE_v0.15.0.md](https://github.com/NousResearch/hermes-agent/blob/main/RELEASE_v0.15.0.md) |
| Tirith 安全模組彈性設定 | 社群持續反映 hard block 策略過於嚴苛，期待 per-project 白名單機制 | [FAQ & Troubleshooting](https://hermes-agent.nousresearch.com/docs/reference/faq/) |
| computer_use 非 Anthropic 模型支援成熟度 | v0.15.0 宣稱任何視覺模型皆可驅動桌面，實際相容性報告待社群驗證 | — |
