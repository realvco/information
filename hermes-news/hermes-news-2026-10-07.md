# Hermes-Agent 每日情報 — 2026-10-07

> 資料來源涵蓋截至 2026-10-07 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **過去 24 小時無新版本，目前穩定版仍為 v0.21.5（v2026.9.24）**：Releases 頁面最新一筆仍是 9/24 的 patch rollup。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

2. **DeepSeek 供應商出現 P1 問題**：超過 50 則訊息的 gateway session 會遇到內容審查 400（"Content Exists Risk"）。（[#134261](https://github.com/NousResearch/hermes-agent/issues/134261)）

3. **Desktop 更新交接 P2 錯誤**：hand-off 將錯誤的 pid 匯出為 `HERMES_UPDATE_HANDOFF_PID`。（[#134268](https://github.com/NousResearch/hermes-agent/issues/134268)）

4. **跨平台相容性問題**：Windows 主機上的 Docker backend 沙箱路徑無法對應到主機檔案；Matrix extra 限定 Linux 導致 macOS 上未加密的 Matrix 無法使用。（[#134264](https://github.com/NousResearch/hermes-agent/issues/134264)、[#134265](https://github.com/NousResearch/hermes-agent/issues/134265)）

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）
- 今日未查到新的實戰回報。

### GPT（OpenAI）
- 今日未查到新的 GPT 專屬回報。

### Gemini（Google）
- 今日未查到新的 Gemini 專屬回報。

### 其他供應商／本地模型
- **DeepSeek**：>50 則訊息的 gateway session 觸發內容審查 400 錯誤，標為 P1。（[#134261](https://github.com/NousResearch/hermes-agent/issues/134261)）

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 類型 | 重點 |
|------|----------|------|------|
| **v0.21.5（v2026.9.24）** | 2026-09-24 | Patch rollup | **目前最新穩定版**；約 460 PR、Desktop plugin SDK、法／德／西語目錄 |
| v0.21.4（v2026.9.21） | 2026-09-21 | Patch rollup | 約 1,800 PR；host-wide gateway singleton lock、結構化 JSONL 輸出、skill 自動載入 |
| v0.21.3（v2026.9.14） | 2026-09-14 | Patch rollup | 遠端 dashboard session 不再因重新整理頻繁而過期、state.db writer 洩漏修正 |

來源：[Releases](https://github.com/NousResearch/hermes-agent/releases)。過去 24 小時無新版本。

---

## 4. 社群討論與已知問題

| Issue | 說明 | 優先度 |
|------|------|------|
| [#134261](https://github.com/NousResearch/hermes-agent/issues/134261) | DeepSeek 內容審查 400，影響 >50 則訊息的 gateway session | P1 |
| [#134268](https://github.com/NousResearch/hermes-agent/issues/134268) | Desktop hand-off 匯出錯誤的 pid | P2 |
| [#134265](https://github.com/NousResearch/hermes-agent/issues/134265) | Matrix extra 限定 Linux，macOS 未加密 Matrix 失效（標為 duplicate） | P2 |
| [#134264](https://github.com/NousResearch/hermes-agent/issues/134264) | Windows 主機 Docker backend 沙箱路徑無法對應 | P2 |
| [#134257](https://github.com/NousResearch/hermes-agent/issues/134257) | `/reasoning --global` 未帶等級時報錯而非開啟選單（Telegram） | P2 |
| [#134258](https://github.com/NousResearch/hermes-agent/issues/134258) | Discord `/model` 供應商選擇因選項值重複而逾時 | P3 |
| [#134275](https://github.com/NousResearch/hermes-agent/issues/134275) | 功能提案：state.db 健康檢查（快照探測、FTS 完整性檢查、定期快照） | P3 |
| [#134283](https://github.com/NousResearch/hermes-agent/issues/134283) | Desktop 新增專案 UI 強制要求資料夾，但 `project_create` 支援無路徑專案 | 未標示 |

來源：[Issues](https://github.com/NousResearch/hermes-agent/issues)。
