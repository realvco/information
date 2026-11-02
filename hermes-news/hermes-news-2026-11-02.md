# Hermes-Agent 每日情報 — 2026-11-02

> 資料來源涵蓋截至 2026-11-02 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **過去 24 小時無重大更新，目前穩定版仍為 v0.22.5**：v0.22.5 於 2026-10-23 發布，之後僅有每日 canary 建置（最新為 11-01 的 v0.22.5+canary.20261101T064348Z，僅有時間戳說明，非正式版）。（[Releases feed](https://github.com/NousResearch/hermes-agent/releases.atom)）

2. **新增 issue 以行動版 app、Kanban、桌面版與 gateway 為主**：例如行動版（Android／iOS beta）登入與連線問題、Kanban 儀表板狀態分類錯誤、桌面 Bot relay 回覆滯留等，皆為使用者回報，尚待維護者確認。（[Issues](https://github.com/NousResearch/hermes-agent/issues)）

---

## 2. LLM 串接實戰回報

過去 24 小時未查到 Claude／GPT／Gemini／本地模型的獨立實測回報。與 provider 相關的新 issue 僅一則：[#147732](https://github.com/NousResearch/hermes-agent/issues/147732) 指出 kimi-coding 的即時模型探索遇到 gzip 回應時失敗，選單只顯示過期的靜態清單（使用者回報，未證實）。

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 說明 |
|------|----------|------|
| **v0.22.5** | 2026-10-23 | 目前最新穩定版（[release](https://github.com/NousResearch/hermes-agent/releases/tag/v0.22.5)） |

另有每日 canary 建置（最新為 [v0.22.5+canary.20261101T064348Z](https://github.com/NousResearch/hermes-agent/releases/tag/v0.22.5%2Bcanary.20261101T064348Z)），僅有建置時間戳，非正式版。來源：[Releases feed](https://github.com/NousResearch/hermes-agent/releases.atom)。

---

## 4. 社群討論與已知問題

2026-11-01／11-02 新增 issue（部分）：

| Issue | 說明 |
|------|------|
| [#147738](https://github.com/NousResearch/hermes-agent/issues/147738)／[#147726](https://github.com/NousResearch/hermes-agent/issues/147726)／[#147723](https://github.com/NousResearch/hermes-agent/issues/147723) | 行動版：Android beta 原生登入重複開啟 /auth/callback；iOS beta SSH／Mosh 模式驗證後失敗；聊天氣泡文字被裁切 |
| [#147756](https://github.com/NousResearch/hermes-agent/issues/147756) | 桌面 Bot relay：已完成的回覆在沒有新 outbox push 時滯留，重啟也無法恢復 |
| [#147729](https://github.com/NousResearch/hermes-agent/issues/147729)／[#147773](https://github.com/NousResearch/hermes-agent/issues/147773)／[#147759](https://github.com/NousResearch/hermes-agent/issues/147759) | Kanban：儀表板將非流程內狀態（如舊的 cancelled）歸入 todo 欄；TUI 通知訂閱寫入的資料無人消費；功能請求加入 per-model 並行的 claim-time 否決機制 |
| [#147747](https://github.com/NousResearch/hermes-agent/issues/147747)／[#147739](https://github.com/NousResearch/hermes-agent/issues/147739) | Cron 工作階段未收到所屬 profile 的 .env；容器重建後共用瀏覽器 profile 被永久拒絕 |
| [#147771](https://github.com/NousResearch/hermes-agent/issues/147771)／[#147766](https://github.com/NousResearch/hermes-agent/issues/147766) | 啟用 1Password 桌面整合時解鎖失敗；巢狀 repo 的 .git 損毀後 checkpoint 永久停止 |
| [#147765](https://github.com/NousResearch/hermes-agent/issues/147765)／[#147725](https://github.com/NousResearch/hermes-agent/issues/147725) | 功能請求：Windows 安裝時提供（不自動套用）Defender 排除選項；`hermes security` 對固定版本的 pyasn1／setuptools 回報 HIGH／MODERATE 發現 |
| [#147736](https://github.com/NousResearch/hermes-agent/issues/147736) | 社群分享：CPU-only 部署的實測紀錄（19 個 provider、58 個 MCP server、596 個 skills） |

以上為 issue 回報，是否成立與是否修補尚待維護者確認。來源：[Issues](https://github.com/NousResearch/hermes-agent/issues)。
