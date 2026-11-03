# Hermes-Agent 每日情報 — 2026-11-03

> 資料來源涵蓋截至 2026-11-03 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **v0.22.6 於 2026-11-02 發布，為新的穩定版**：這是涵蓋 v0.22.5 以來約 776 個已合併 PR 的修補版本，範圍包含 CLI 與安裝程式、Microsoft Store app、Docker 與 Hermes Cloud；官方表示完整的精選發行說明將隨 v0.23.0 提供。（[Releases feed](https://github.com/NousResearch/hermes-agent/releases.atom)）

2. **v0.22.6 經歷多次 RC 才定案**：當天依序出現 rc.1、rc.2、rc.3，其中 rc.1、rc.2 被標記為 abandoned，最後才發布正式版。（[Releases feed](https://github.com/NousResearch/hermes-agent/releases.atom)）

3. **新增 issue 中有數則與 provider／本地模型相關**：包含 `/model <alias>` 搭配 `key_env` 時誤用預設 Anthropic 金鑰計費、provider client 忽略 `SSL_CERT_FILE`，以及本地模型載入（Vulkan／GPU 探測）問題，皆為使用者回報，尚待維護者確認。（[Issues](https://github.com/NousResearch/hermes-agent/issues)）

---

## 2. LLM 串接實戰回報

過去 24 小時未查到 Claude／GPT／Gemini／本地模型的獨立實測回報，僅有以下 issue 回報（使用者回報，未證實）：

### Claude／Anthropic
- [#148255](https://github.com/NousResearch/hermes-agent/issues/148255)：`/model <alias>` 搭配 `key_env` 時，會以預設的 Anthropic 金鑰計費，而非指定的金鑰。

### 本地模型
- [#148261](https://github.com/NousResearch/hermes-agent/issues/148261)：固定的載入模式會把 MoE experts 放進大型 Vulkan buffer，導致 KV cache 配置失敗。
- [#148249](https://github.com/NousResearch/hermes-agent/issues/148249)：缺少 DLL 時 GPU 探測靜默失敗，進而造成模型載入記憶體不足。

### 其他
- [#148241](https://github.com/NousResearch/hermes-agent/issues/148241)：provider client 忽略 `SSL_CERT_FILE`，在攔截式 proxy 後 TLS 失敗。

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 說明 |
|------|----------|------|
| **v0.22.6** | 2026-11-02 | 目前最新穩定版，約 776 個 PR 的修補版本（[release](https://github.com/NousResearch/hermes-agent/releases/tag/v0.22.6)） |
| v0.22.5 | 2026-10-23 | 前一穩定版 |

v0.22.6 的 RC 標籤（rc.1、rc.2、rc.3 及兩個 abandoned 標籤）僅有建置中繼資料，無發行說明。完整精選說明預告隨 v0.23.0 提供。來源：[Releases feed](https://github.com/NousResearch/hermes-agent/releases.atom)。

### 公司動態（背景）
據報導 Nous Research 於 2026-10-07 完成 9,000 萬美元募資、估值 15 億美元，資金用於企業版 Hermes for Businesses；GitHub 星數等數字為各報導自述，不同來源不一。（[The Next Web](https://thenextweb.com/news/nous-research-90m-1-5bn-hermes-agent-wsj)）此為既有消息，非過去 24 小時新增。

---

## 4. 社群討論與已知問題

2026-11-02／11-03 新增 issue（部分）：

| Issue | 說明 |
|------|------|
| [#148246](https://github.com/NousResearch/hermes-agent/issues/148246) | Android beta 直連找到 Nous 驗證資訊，但無法解析主機 |
| [#148270](https://github.com/NousResearch/hermes-agent/issues/148270)／[#148245](https://github.com/NousResearch/hermes-agent/issues/148245) | 桌面版：封存清單在後續分頁逾時時隱藏已成功的頁面；遠端 gateway 桌面版首次聊天忽略預設專案目錄 |
| [#148264](https://github.com/NousResearch/hermes-agent/issues/148264)／[#148262](https://github.com/NousResearch/hermes-agent/issues/148262) | Windows／安裝：stash-restore 匯入探測逾時；套件管理器移除下載並行設定，大型 wheel 發生 HTTP 逾時 |
| [#148257](https://github.com/NousResearch/hermes-agent/issues/148257)／[#148248](https://github.com/NousResearch/hermes-agent/issues/148248) | Gateway：獨立模式轉 gateway 後群組成員無法喚醒；多工 gateway 的 `/new`、`/undo` 讀到錯誤 profile 的確認設定 |
| [#148217](https://github.com/NousResearch/hermes-agent/issues/148217)／[#148211](https://github.com/NousResearch/hermes-agent/issues/148211)／[#148210](https://github.com/NousResearch/hermes-agent/issues/148210) | Kanban：Windows 上 scratch 工作區未完全移除；worker 匯入卡片 worktree 而非已安裝的 Hermes；停用 kanban toolset 後 worker 仍保有生命週期工具 |
| [#148258](https://github.com/NousResearch/hermes-agent/issues/148258)／[#148235](https://github.com/NousResearch/hermes-agent/issues/148235)／[#148236](https://github.com/NousResearch/hermes-agent/issues/148236) | 通訊平台：Matrix 唯讀訊息顯示錯誤；Buzz 附件下載未驗證而回 401；功能請求 Telegram 區段導覽按鈕 |

以上為 issue 回報，是否成立與是否修補尚待維護者確認。來源：[Issues](https://github.com/NousResearch/hermes-agent/issues)。
