# Hermes-Agent 每日情報 — 2026-11-19

> 資料來源涵蓋截至 2026-11-19 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **目前最新穩定版仍為 v0.23.2**：2026-11-16 發布，過去 24 小時無新的穩定版；完整整理版說明仍延到 v0.24.0。（[Latest release](https://github.com/NousResearch/hermes-agent/releases/latest)）

2. **新的 canary 版 v0.23.2+canary.20261118T064620Z 加入 GPT-6.2 Astra 與 Sol 模型支援**：2026-11-18 07:55 UTC 發布，另有 FAL 目錄的 GPT Image 2.6 選項、多項 plugin 目錄新增、儲存層重構與 Codex 模型選擇的修正；macOS arm64 DMG 與 Windows x64／arm64 MSIX 的原生安裝與對話 smoke test 皆通過。（[Releases feed](https://github.com/NousResearch/hermes-agent/releases.atom)）

---

## 2. LLM 串接實戰回報

- **GPT**：上述 canary 版新增 GPT-6.2 Astra／Sol 支援並修正 Codex 模型選擇，屬 release 說明，尚未查到獨立的使用者實測回報。（[Releases feed](https://github.com/NousResearch/hermes-agent/releases.atom)）
- **Claude／Gemini／本地模型**：過去 24 小時未查到獨立實測回報，不予編造。
- 相關 issue（使用者回報，尚待維護者確認）：[#157598](https://github.com/NousResearch/hermes-agent/issues/157598) 串流解析器丟失具型別的 thinking content block；[#157606](https://github.com/NousResearch/hermes-agent/issues/157606) provider 憑證從即時環境變數解析，而非 profile 範圍。

---

## 3. 版本與 release 動態

| 版本 | 發布時間（UTC） | 說明 |
|------|----------|------|
| v0.23.2+canary.20261118T064620Z | 2026-11-18 | canary 版；GPT-6.2 Astra／Sol、GPT Image 2.6 選項、plugin 目錄新增、Codex 模型選擇修正 |
| **v0.23.2** | 2026-11-16 | 目前最新穩定版，彙整約 1,989 個 PR；修正 Desktop 更新後無法送出對話、啟動／恢復時輸入遺失、重連後重複回覆等問題 |

來源：[releases feed](https://github.com/NousResearch/hermes-agent/releases.atom)、[v0.23.2](https://github.com/NousResearch/hermes-agent/releases/latest)。

---

## 4. 社群討論與已知問題

新增 issue（皆為使用者回報或提案，是否成立尚待維護者確認）：

| Issue | 說明 |
|------|------|
| [#157633](https://github.com/NousResearch/hermes-agent/issues/157633) | 本機終端指令輸出無上限，可能使桌面 session 當機 |
| [#157638](https://github.com/NousResearch/hermes-agent/issues/157638) | Windows 安裝環境權限錯誤 |
| [#157625](https://github.com/NousResearch/hermes-agent/issues/157625) | Desktop 經 HTTPS 區網 IP 連遠端 gateway 時，登入票證被拒 |
| [#157615](https://github.com/NousResearch/hermes-agent/issues/157615) | 機密遮蔽漏掉以標籤為前綴的欄位傾印 |
| [#157632](https://github.com/NousResearch/hermes-agent/issues/157632) | 單次 CLI 把內嵌圖片資料當文字印出 |
| [#157605](https://github.com/NousResearch/hermes-agent/issues/157605) | 在 kitty 下的 Zellij 中內嵌圖片顯示異常 |
| [#157627](https://github.com/NousResearch/hermes-agent/issues/157627) | 傳統 CLI 中斷後重繪位置偏移 |
| [#157603](https://github.com/NousResearch/hermes-agent/issues/157603) | Desktop 的 Plugins 搜尋無法以 ID 找到已安裝 plugin |
| [#157635](https://github.com/NousResearch/hermes-agent/issues/157635) | 提案：ACP 編輯核准政策的預設設定 |
| [#157626](https://github.com/NousResearch/hermes-agent/issues/157626) | 提案：Kanban 支援排程的初始狀態 |

來源：[Issues](https://github.com/NousResearch/hermes-agent/issues?q=is%3Aissue+sort%3Acreated-desc)。
