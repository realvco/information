# Hermes-Agent 每日情報 — 2026-11-27

> 資料來源涵蓋截至 2026-11-27 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **過去 24 小時無新的穩定版，目前穩定版仍為 v0.23.4**（2026-11-24 發布，標示為 Latest）。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

2. **新增一個 canary 建置**：`canary.20261126T064809Z-v0.23.4`（2026-11-26 約 06:48 UTC，預先發行版），feed 中僅有 commit 中繼資料，未附變更說明。（[releases feed](https://github.com/NousResearch/hermes-agent/releases.atom)）

3. **新回報以 gateway、session 與安裝更新為主**：例如 `/v1/runs` 將已送出最終回應的 max-iterations 交接誤報為 `run.failed`、approval gate 有三種指令型態會把 `ask` 判定變成 `allow`（標為 security）。（見第 4 節，皆為使用者回報，尚待維護者確認）

---

## 2. LLM 串接實戰回報

過去 24 小時新增 issue 中，未查到針對 Claude／Gemini／本地模型的串接實戰回報，不予編造。與 GPT 相關者僅有一則：[#160622](https://github.com/NousResearch/hermes-agent/issues/160622)（修正多工 Codex OAuth 下 profile 範圍的 `HERMES_CODEX_BASE_URL` 解析，provider/openai）。

---

## 3. 版本與 release 動態

| 版本 | 發布時間（UTC） | 說明 |
|------|----------|------|
| canary.20261126T064809Z-v0.23.4 | 2026-11-26 約 06:48 | 預先發行版；feed 無變更說明 |
| canary.20261125T064800Z-v0.23.4 | 2026-11-25 約 08:03 | 桌面版安裝檔（macOS Apple Silicon、Windows x64／ARM64）冒煙測試通過；大量外掛目錄新增項目 |
| **v0.23.4** | 2026-11-24 | 目前最新穩定版（Latest） |

來源：[releases feed](https://github.com/NousResearch/hermes-agent/releases.atom)、[Releases](https://github.com/NousResearch/hermes-agent/releases)。

---

## 4. 社群討論與已知問題

新增 issue（皆為使用者回報，是否成立尚待維護者確認）：

| Issue | 說明 |
|------|------|
| [#160634](https://github.com/NousResearch/hermes-agent/issues/160634) | `/v1/runs` 將 max-iterations 交接（已送出最終回應）誤報為 `run.failed`（P2 bug） |
| [#160636](https://github.com/NousResearch/hermes-agent/issues/160636) | Bot Mode 已自動回覆後，狀態仍顯示「awaiting reply」（P2 bug） |
| [#160608](https://github.com/NousResearch/hermes-agent/issues/160608) | Session 因持久化的 `unknown` admission 無限期被 fence，開機時無校正（P2 bug） |
| [#160605](https://github.com/NousResearch/hermes-agent/issues/160605) | Approval gate：三種指令型態（如 `cp` 後接重新導向）會把 `ask` 變成 `allow`（security） |
| [#160616](https://github.com/NousResearch/hermes-agent/issues/160616) | `GET /api/sessions` 每 100 筆約帶 124 KB 逐列簿記資料（約佔 45%）（perf） |
| [#160597](https://github.com/NousResearch/hermes-agent/issues/160597) | disk-cleanup 於 session 結束時刪除 workbenches 下的 `test_*`／`tmp_*` 檔案（P2 bug） |
| [#160587](https://github.com/NousResearch/hermes-agent/issues/160587) | Hermes Termux 套件仍然損壞（P2 bug） |
| [#160628](https://github.com/NousResearch/hermes-agent/issues/160628) | macOS 無法更新／安裝（P2 bug） |
| [#160629](https://github.com/NousResearch/hermes-agent/issues/160629) | Buzz gateway 託管社群回傳 401，設定文件未說明 agent 驗證需求（P2 bug） |

功能提案：[#160603](https://github.com/NousResearch/hermes-agent/issues/160603)（`hermes update` 可選擇性更新外掛）、[#160627](https://github.com/NousResearch/hermes-agent/issues/160627)（desktop plugin SDK 新增 profile 範圍的 config 讀寫動詞）。

來源：[Issues](https://github.com/NousResearch/hermes-agent/issues?q=is%3Aissue+sort%3Acreated-desc)。
