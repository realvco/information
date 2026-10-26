# Hermes-Agent 每日情報 — 2026-10-26

> 資料來源涵蓋截至 2026-10-26 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **過去 24 小時無新的正式版發布，目前穩定版仍為 v0.22.5**：v0.22.5 於 2026-10-22 發布，之後只有每日 canary 建置（最新為 10-25 的 v0.22.5+canary.20261025T065651Z，非正式版）。（[Releases feed](https://github.com/NousResearch/hermes-agent/releases.atom)）

2. **新增 issue 以 gateway、更新／安裝與 macOS launchd 為主**：例如 `gateway stop --yes` 在 v0.22.5 會因 `ManagedWorker.interrupt()` 拋出 TypeError（[#144753](https://github.com/NousResearch/hermes-agent/issues/144753)）、launchd 主機上更新後重啟義務無法完成（[#144754](https://github.com/NousResearch/hermes-agent/issues/144754)）。以上為使用者回報，尚待維護者確認。

---

## 2. LLM 串接實戰回報

過去 24 小時未查到 Claude／GPT／Gemini／本地模型的獨立實測回報。

**Provider 相關 issue**
- `anthropic_messages` wire 在重新 seed 後若 anthropic extra 被移除，worker 啟動時會失效（[#144749](https://github.com/NousResearch/hermes-agent/issues/144749)，標籤 comp/agent、P2）。
- API 回報任務已取消，但其擁有的背景行程在 stop 或 SSE 斷線後仍存活（v0.22.5，[#144761](https://github.com/NousResearch/hermes-agent/issues/144761)）。

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 說明 |
|------|----------|------|
| **v0.22.5** | 2026-10-22 | 目前最新穩定版；約 81 項 PR 彙整，涵蓋 CLI、安裝程式、Microsoft Store 版、Docker 與 Hermes Cloud；`hermes mcp serve` 與 Singularity 後端移為外掛 |
| v0.22.4 | 2026-10-21 | 桌面版移除建議膠囊、WhatsApp 登出後可從 dashboard 安全重新配對、核准模式標籤跟隨 profile |
| v0.22.3 | 2026-10-21 | 約 620 項 PR 彙整；MCP 進度通知、桌面版可調整排隊中的提示、`/loop` 倒數、`post_compaction` 外掛 hook |

另有每日 canary 建置，無 release notes，非正式版。來源：[Releases](https://github.com/NousResearch/hermes-agent/releases)、[Releases feed](https://github.com/NousResearch/hermes-agent/releases.atom)。

---

## 4. 社群討論與已知問題

2026-10-26 新增 issue（部分，優先度取自 GitHub 標籤）：

| Issue | 說明 | 優先度 |
|------|------|------|
| [#144772](https://github.com/NousResearch/hermes-agent/issues/144772) | 兩個 Webapp 分頁同時寫入 prepared-submission journal 可能遺失或復原已撤銷的項目 | P2 |
| [#144766](https://github.com/NousResearch/hermes-agent/issues/144766) | 瀏覽器 READY 訊號可能早於 dashboard／WebSocket 冷啟動準備完成 | P2 |
| [#144761](https://github.com/NousResearch/hermes-agent/issues/144761) | API 回報已取消，但背景行程在 stop／SSE 斷線後仍存活 | P2 |
| [#144754](https://github.com/NousResearch/hermes-agent/issues/144754) | launchd 主機上更新後重啟義務無法完成 | P2 |
| [#144753](https://github.com/NousResearch/hermes-agent/issues/144753) | `gateway stop --yes` 因 `ManagedWorker.interrupt()` 拋出 TypeError 而當掉 | P2 |
| [#144749](https://github.com/NousResearch/hermes-agent/issues/144749) | anthropic_messages wire 於 re-seed 後 worker 啟動失敗 | P2 |
| [#144736](https://github.com/NousResearch/hermes-agent/issues/144736) | 桌面版：網址後接全形中日韓標點時，標點被吞進連結 href | P2 |
| [#144734](https://github.com/NousResearch/hermes-agent/issues/144734) | Linux：更新後桌面視窗約 94% 透明 | P2 |
| [#144735](https://github.com/NousResearch/hermes-agent/issues/144735) | Node 更新後 launchd gateway 仍使用舊的 Node | P3（security） |

以上為 issue 回報，是否成立與是否修補尚待維護者確認。來源：[Issues](https://github.com/NousResearch/hermes-agent/issues)。
