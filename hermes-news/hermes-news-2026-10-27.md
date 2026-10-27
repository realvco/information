# Hermes-Agent 每日情報 — 2026-10-27

> 資料來源涵蓋截至 2026-10-27 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **過去 24 小時無新的正式版發布，目前穩定版仍為 v0.22.5**：v0.22.5 於 2026-10-23 發布（約 81 項 PR 彙整，完整 release notes 延到 v0.23.0），之後只有每日 canary 建置（最新為 10-26 的 v0.22.5+canary.20261026T071310Z，非正式版）。（[Releases feed](https://github.com/NousResearch/hermes-agent/releases.atom)）

2. **新增 issue 以桌面版、cron／gateway 與 session 為主**：例如 webhook 觸發的 cron job 同時大量執行時可能使 gateway 死鎖（[#145315](https://github.com/NousResearch/hermes-agent/issues/145315)）、桌面版「Update」按鈕被殘留的 `.git/hermes-update.lock` 靜默擋住（[#145337](https://github.com/NousResearch/hermes-agent/issues/145337)）。以上為使用者回報，尚待維護者確認。

---

## 2. LLM 串接實戰回報

過去 24 小時未查到 Claude／GPT／Gemini／本地模型的獨立實測回報。

**Provider 相關 issue**
- Codex auxiliary adapter 會靜默丟棄 `response_format`（標籤 provider/openai、P2，[#145327](https://github.com/NousResearch/hermes-agent/issues/145327)）。

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 說明 |
|------|----------|------|
| **v0.22.5** | 2026-10-23 | 目前最新穩定版；約 81 項 PR 彙整，完整策展 notes 延至 v0.23.0（[release](https://github.com/NousResearch/hermes-agent/releases/tag/v0.22.5)） |

另有每日 canary 建置（最新 [v0.22.5+canary.20261026T071310Z](https://github.com/NousResearch/hermes-agent/releases/tag/v0.22.5%2Bcanary.20261026T071310Z)），僅有建置時間戳，無 release notes，非正式版。來源：[Releases feed](https://github.com/NousResearch/hermes-agent/releases.atom)。

---

## 4. 社群討論與已知問題

2026-10-27 新增 issue（部分，優先度取自 GitHub 標籤）：

| Issue | 說明 | 優先度 |
|------|------|------|
| [#145315](https://github.com/NousResearch/hermes-agent/issues/145315) | webhook cron_job 同時大量觸發時 gateway 死鎖 | P2 |
| [#145314](https://github.com/NousResearch/hermes-agent/issues/145314) | quiet cron script 每次觸發都留下永久的空 session | P2 |
| [#145337](https://github.com/NousResearch/hermes-agent/issues/145337) | 桌面版 Update 按鈕被殘留的 update lock 靜默擋住 | P2 |
| [#145332](https://github.com/NousResearch/hermes-agent/issues/145332) | Windows 桌面版無法開啟 bot 的舊 Bot Chat | P2 |
| [#145308](https://github.com/NousResearch/hermes-agent/issues/145308) | message_agent 啟動的回合中 clarify 不顯示卻占住 session lease | P2 |
| [#145299](https://github.com/NousResearch/hermes-agent/issues/145299) | 桌面版新對話不保留所選的 reasoning effort | P2 |
| [#145329](https://github.com/NousResearch/hermes-agent/issues/145329) | node-inspect-debugger 會任選一個既有的 TUI 行程 | P2 |
| [#145347](https://github.com/NousResearch/hermes-agent/issues/145347) | `hermes completion` 與 `--help` 未列出外掛註冊的 CLI 指令 | 未標示 |
| [#145339](https://github.com/NousResearch/hermes-agent/issues/145339) | 功能請求：桌面版檔案預覽支援跳到連結行號（#L42） | P3 |

以上為 issue 回報，是否成立與是否修補尚待維護者確認。來源：[Issues](https://github.com/NousResearch/hermes-agent/issues)。
