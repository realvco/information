# Hermes-Agent 每日情報 — 2026-09-28

> 資料來源涵蓋截至 2026-09-28 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **目前穩定版仍為 v0.21.5（v2026.9.24）**：過去 24 小時內查無新版本或新 tag，main 分支持續有一般性 feature／fix commit（如 desktop 分頁 reveal floor、TUI push-to-talk 打斷 in-flight TTS、雙擊 Esc 中斷當前回合、`/s` 作為 `/steer` 別名、plugins known_issues 改為非阻斷性告警等），但無 Opus 5.5 或版本號相關內容。（[Releases](https://github.com/NousResearch/hermes-agent/releases)、[Commits](https://github.com/NousResearch/hermes-agent/commits/main)）

2. **URL 帳密遮罩修復 PR（#123319）作者已回應審查、補上兩項修正，仍待新一輪 CI 與人工核准**：作者 kvnloo 於 9/27 針對 reviewer Enough1122 指出的三項問題，推送 4 個新 commit，修正了 (1) 遮罩 regex 改為比對最後一個 `@` 而非第一個，避免密碼含 `@` 時部分外洩；(2) 新增 `x_amz_signature`（底線寫法）到敏感參數集合，補齊 AWS 預簽章 URL 遮罩缺口。但作者明確表示**第三項問題（scheme-agnostic 參數比對誤遮罩非 URL 一般文字）刻意不在此次修正範圍內**，理由是這屬於獨立的「誤判政策」問題而非憑證外洩問題；PR 目前狀態為 **draft／open**，作者亦聲明本次未取得乾淨 CI 執行結果，仍待新一輪 CI 驗證。（[PR #123319](https://github.com/NousResearch/hermes-agent/pull/123319)）

3. **Opus 5.5「強制 thinking」修復 PR（#121016）狀態未變**：仍為 open，9/26 的 review 结論（核心修復正確、僅剩兩項非阻斷性 nit：帶點模型名稱測試覆蓋、貢獻者歸屬對應問題）過去 24 小時無新進展，尚未合併。

4. **Anthropic content blocks 導致 token 用量「雙重計費」，已有修復 PR 待審**：新開 issue [#125761](https://github.com/NousResearch/hermes-agent/issues/125761)（9/27，P2）指出 `agent/model_metadata.py` 的 `_wire_message_shadow` 未排除 `anthropic_content_blocks` 欄位，導致 thinking 與 signature 內容被重複計入 token 估算，實測案例顯示畫面顯示 79.8 萬 token、實際用量僅 56.5 萬（約高估 41%）。對應修復 [PR #125845](https://github.com/NousResearch/hermes-agent/pull/125845) 已由 liuhao1024 於同日開出，修改排除清單並補測試，通過 112 項現有／新增測試，尚待 review。

5. **原生 Google AI Studio 圖像生成後端提案（#121183）過去 24 小時無新進展**：9/26 審查指出的「參考圖片讀取失敗時靜默降級為文字生圖」與「缺乏對應測試」兩項阻斷性問題，作者尚未回應或修正，PR 仍為 open。

6. **Ollama／閒置模型記憶體釋放 PR（#120944）於 9/27 收到阻斷性審查意見**：reviewer Enough1122 指出 `@guard_idle_release` decorator 在磁碟已滿、權限不足、只讀掛載等 gate 目錄錯誤情境下，即使功能本身已停用，仍會導致每個回合失敗並報 "TURN DIED"，並點出「一個記憶體壓力功能不該反而害死整個回合」；另有非阻斷性意見（debounce 與釋放確認缺測試、釋放間隔可能過於頻繁、gate 綁定 Hermes root 而非 endpoint 可能跨安裝互相影響可見性）。PR 仍為 open，作者尚未回應。

7. **Azure Foundry／AWS Bedrock GPT-6 路由修復系列無新進展**：既有重複 PR 組合（[#120327](https://github.com/NousResearch/hermes-agent/pull/120327) 與 [#124105](https://github.com/NousResearch/hermes-agent/pull/124105)，maintainer 已於 9/26 確認重複）過去 24 小時無新評論；AWS Bedrock 端 [#122116](https://github.com/NousResearch/hermes-agent/pull/122116) 與 [#115931](https://github.com/NousResearch/hermes-agent/pull/115931) 的整併問題同樣無新動態，最近一次活動仍是 9/25。

8. **CometAPI＋Opus 5.5 thinking 驗證錯誤（#122672）過去 24 小時無新進展**：仍為 open、P2，無新評論，尚無對應 PR。

9. **新一批 `sweeper:risk-security-boundary` 安全性 issue 於 9/27 集中開立，聚焦憑證池與敏感文字遮罩缺口**：已核實至少 12 則同批新開項目，包含 `redact_sensitive_text` 遺漏 URL userinfo 與 `NAME=user:password` 格式憑證（[#125664](https://github.com/NousResearch/hermes-agent/issues/125664)，P2，與第 2 條 #123319 的遮罩修復屬同類問題但範疇不同）、skills 同步時遠端樹狀目錄名稱可跳出同步根目錄造成 `rmtree` 與任意寫入（[#125555](https://github.com/NousResearch/hermes-agent/issues/125555)，P2）、Desktop multiplex 模式跳過載入 `~/.hermes/.env` 導致 `fallback_providers` 靜默失效（[#125530](https://github.com/NousResearch/hermes-agent/issues/125530)，P2）、`hermes -p <profile> auth add openai-codex` 回報成功但實際靜默丟棄憑證（[#125501](https://github.com/NousResearch/hermes-agent/issues/125501)，P2）、`read_file` 與終端機 `cat` 會顯示 `~/.aws/credentials`、`.netrc`、`.pgpass` 等檔案中的密鑰內容（[#124999](https://github.com/NousResearch/hermes-agent/issues/124999)，P2）等，詳見第 4 節完整列表。**註：清單受頁面顯示筆數限制，實際總數可能更多，未逐則人工核對時間戳精確性。**

10. **社群媒體：過去 24 小時查無 Nous Research 官方帳號的新發文**。WebSearch 僅找到既有的、日期較早的 Hermes Desktop／Bot Mode 宣傳內容與若干第三方彙整／SEO 型網站（如 gradually.ai、releasebot.io）之「changelog」頁面，其內容與既有 release note 重疊、可信度與更新時效性存疑，本報告不採用其作為獨立來源，僅以 GitHub 官方頁面核實後的資訊為準。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）
- **新增：token 用量雙重計費問題已有修復 PR**：[#125761](https://github.com/NousResearch/hermes-agent/issues/125761)（9/27，P2）指出 `anthropic_content_blocks` 造成 thinking／signature 內容被重複計入 context 估算，導致 Desktop 計量器與 CLI 狀態列高估用量約 41%；對應修復 [PR #125845](https://github.com/NousResearch/hermes-agent/pull/125845) 已提出，通過 112 項測試，尚待 review。
- **Opus 5.5 強制 thinking 修復（#121016）無新進展**：仍為 open，9/26 的 review 結論未變，兩項非阻斷性 nit 尚待處理。
- **無新進展**：[#122672](https://github.com/NousResearch/hermes-agent/issues/122672) CometAPI＋Opus 5.5 thinking 參數驗證錯誤；[#120844](https://github.com/NousResearch/hermes-agent/issues/120844) `ANTHROPIC_BASE_URL` 探測問題；[#120723](https://github.com/NousResearch/hermes-agent/issues/120723) 簽章 thinking block 保留請求；[#118714](https://github.com/NousResearch/hermes-agent/issues/118714) OAuth 過期欄位損毀；[#118199](https://github.com/NousResearch/hermes-agent/pull/118199) Keychain refresh token 誤耗修復。

### GPT（OpenAI／Azure／Bedrock）
- **Azure Foundry 重複 PR、Bedrock 整併問題均無新進展**：[#120327](https://github.com/NousResearch/hermes-agent/pull/120327)／[#124105](https://github.com/NousResearch/hermes-agent/pull/124105)（maintainer 已確認重複）與 [#122116](https://github.com/NousResearch/hermes-agent/pull/122116)（與 #115931 衝突待整併）過去 24 小時皆無新評論。
- **新增（間接相關）**：`hermes -p <profile> auth add openai-codex` 回報成功但實際靜默丟棄憑證（[#125501](https://github.com/NousResearch/hermes-agent/issues/125501)，9/27，P2），屬多 profile 憑證管理層面的問題，非 API 相容性問題。

### Gemini（Google）
- **原生 Google AI Studio 圖像後端提案（#121183）無新進展**：靜默降級與測試缺口兩項阻斷性問題仍待作者修正。
- **新增：Gemini 原生請求逾時設定失效**：[#125412](https://github.com/NousResearch/hermes-agent/issues/125412)（9/27，P2，`provider/gemini`）指出當請求未明確覆寫逾時設定時，系統會忽略已配置的 client timeout。
- **新增：Windows 系統 proxy 未套用於 auxiliary 呼叫，影響區域限制供應商**：[#124773](https://github.com/NousResearch/hermes-agent/issues/124773)（9/27，P2）指出 `auxiliary.*` 呼叫在 Windows 上繞過系統 proxy 設定，導致 OpenRouter／Gemini 等區域限制供應商回傳 HTTP 403。

### 本地模型／Local Runtime
- [PR #120944](https://github.com/NousResearch/hermes-agent/pull/120944) 閒置模型記憶體釋放：**9/27 收到阻斷性審查意見**，指出錯誤處理不當會導致 `@guard_idle_release` 在磁碟／權限異常時反而害死整個回合，尚待作者回應。
- 過去 24 小時查無 Ollama／llama.cpp 專屬的新 issue 或 PR；llama.cpp context 溢位訊息辨識問題（[#117793](https://github.com/NousResearch/hermes-agent/issues/117793)）無新進展。

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 類型 | 重點 |
|------|----------|------|------|
| **v0.21.5（v2026.9.24）** | 2026-09-24 | Patch rollup | **目前最新穩定版**；過去 24 小時內查無更新版本 |
| v0.21.4（v2026.9.21） | 2026-09-21 | Patch rollup | host-wide gateway singleton lock、`--format stream-json`、`skills.auto_load`、12 個新社群外掛 |
| v0.21.3（v2026.9.14） | 2026-09-14 | Patch rollup | remote gateway 登入失效修復、refresh token 併發合併、refresh 移出 event loop |
| v0.21.2（v2026.9.11） | 2026-09-11 | Patch rollup | state.db 可靠性戰役、multiplexed profile 隔離強化、密碼隱藏式憑證保險箱 |
| v0.21.1（v2026.9.7） | 2026-09-07 | Patch rollup | 效能、MCP 授權、cron、delegation 修復 |

過去 24 小時 main 分支持續有大量一般性合併，較值得留意的包含：desktop 分頁 reveal floor 與隱藏目錄顯示、TUI push-to-talk 打斷 in-flight TTS、雙擊 Esc 中斷當前回合、CLI 新增 `/s` 作為 `/steer` 別名、reasoning effort 快捷鍵階梯調整、plugins `known_issues` 由「強制擋下安裝」改為「僅告警不擋裝」（[#124037](https://github.com/NousResearch/hermes-agent/pull/124037) 系列）、cron 允許 Python 腳本指定外部 interpreter 但收斂為僅接受 Python 執行檔等。皆屬一般性功能與維護項目，未見 Opus 5.5 或重大版本相關的新合併項目，亦無新 tag。（[Releases 總覽](https://github.com/NousResearch/hermes-agent/releases)、[main 分支 commits](https://github.com/NousResearch/hermes-agent/commits/main)）

---

## 4. 社群討論與已知問題

**本日最值得留意的是 URL 帳密遮罩修復 PR（#123319）作者已回應審查並補上兩項修正但明確擱置第三項、Anthropic token 雙重計費問題已有修復 PR 待審，以及 Ollama 閒置記憶體釋放 PR 收到新的阻斷性審查意見。**

| Issue／PR | 說明 | 優先度／狀態 |
|------|------|----------|
| [#123319](https://github.com/NousResearch/hermes-agent/pull/123319) | `hermes approvals suggest` 遮罩 URL 帳密，9/27 補修 2/3 項瑕疵，第 3 項（誤遮罩一般文字）刻意擱置 | Draft／Open |
| [#125761](https://github.com/NousResearch/hermes-agent/issues/125761) | `anthropic_content_blocks` 造成 token 用量雙重計費，高估約 41% | P2／Open |
| [#125845](https://github.com/NousResearch/hermes-agent/pull/125845) | 上述問題的修復 PR，已提出待審 | Open |
| [#121016](https://github.com/NousResearch/hermes-agent/pull/121016) | Opus 5.5「強制 thinking」修復，核心邏輯已獲確認、剩兩項 nit | Open |
| [#122672](https://github.com/NousResearch/hermes-agent/issues/122672) | CometAPI＋Opus 5.5 thinking 參數驗證錯誤 | P2／Open |
| [#121183](https://github.com/NousResearch/hermes-agent/pull/121183) | 原生 Google AI Studio 圖像生成後端，靜默降級與測試缺口待修正 | Open |
| [#125412](https://github.com/NousResearch/hermes-agent/issues/125412) | Gemini 原生請求逾時設定被忽略 | P2／Open |
| [#124773](https://github.com/NousResearch/hermes-agent/issues/124773) | Windows 上 `auxiliary.*` 呼叫繞過系統 proxy，Gemini／OpenRouter 回 403 | P2／Open |
| [#120944](https://github.com/NousResearch/hermes-agent/pull/120944) | 閒置模型記憶體釋放，9/27 審查指出錯誤處理會害死整個回合 | Open（待修正） |
| [#124105](https://github.com/NousResearch/hermes-agent/pull/124105) | Azure Foundry GPT-6 Responses API 路由（與 #120327 重複，maintainer 已確認） | Open（重複待收斂） |
| [#120327](https://github.com/NousResearch/hermes-agent/pull/120327) | Azure Foundry gpt-6-luna／sol 需 Responses API（原始 PR） | P3／Open |
| [#122116](https://github.com/NousResearch/hermes-agent/pull/122116) | AWS Bedrock GPT-6 Responses 路由修復，與 #115931 衝突待整併 | P2／Open |
| [#125664](https://github.com/NousResearch/hermes-agent/issues/125664) | `redact_sensitive_text` 遺漏 URL userinfo 與 `NAME=user:password` 憑證 | P2／sweeper:risk-security-boundary／Open |
| [#125555](https://github.com/NousResearch/hermes-agent/issues/125555) | skills 同步：遠端樹狀目錄名稱可跳出同步根目錄，導致 `rmtree` 與任意寫入 | P2／sweeper:risk-security-boundary／Open |
| [#125530](https://github.com/NousResearch/hermes-agent/issues/125530) | Desktop multiplex 模式跳過載入 `~/.hermes/.env`，靜默破壞 `fallback_providers` | P2／sweeper:risk-security-boundary／Open |
| [#125501](https://github.com/NousResearch/hermes-agent/issues/125501) | `auth add openai-codex` 回報成功但實際靜默丟棄憑證 | P2／sweeper:risk-security-boundary／Open |
| [#124999](https://github.com/NousResearch/hermes-agent/issues/124999) | `read_file` 與終端機 `cat` 顯示 `~/.aws/credentials`／`.netrc`／`.pgpass` 等密鑰內容 | P2／sweeper:risk-security-boundary／Open |
| （另有至少 7 則同批 9/27 開立的 `sweeper:risk-security-boundary` issue：[#125841](https://github.com/NousResearch/hermes-agent/issues/125841)、[#125762](https://github.com/NousResearch/hermes-agent/issues/125762)、[#125427](https://github.com/NousResearch/hermes-agent/issues/125427)、[#125189](https://github.com/NousResearch/hermes-agent/issues/125189)、[#125032](https://github.com/NousResearch/hermes-agent/issues/125032)、[#124790](https://github.com/NousResearch/hermes-agent/issues/124790)、[#124782](https://github.com/NousResearch/hermes-agent/issues/124782)） | 涵蓋 plugin-catalog pinned-source 驗證可跳出已釘選 commit 範圍、1Password 密鑰來源冷啟動延遲、`config set/get` 純數字密碼／PIN 以明文顯示、子行程繼承 `os.environ` 使憑證清除失效、Curator 的 LLM pass 在 multiplex gateway 上未正確隔離範圍、憑證池 ISO-8601 `reset_at` 誤判為本地時間、憑證池數字標籤與索引定址衝突等 | sweeper:risk-security-boundary |
| [#120851](https://github.com/NousResearch/hermes-agent/pull/120851) | Nano Banana image_gen 外掛遭 maintainer 政策駁回 | Closed（未合併） |
| [#120844](https://github.com/NousResearch/hermes-agent/issues/120844) | Anthropic 模型探測忽略 `ANTHROPIC_BASE_URL` | 未標記／Open |
| [#118714](https://github.com/NousResearch/hermes-agent/issues/118714) | OAuth 過期欄位損毀 | P2／Open |
| [#118199](https://github.com/NousResearch/hermes-agent/pull/118199) | Hermes 誤耗 Claude Code 專屬 Keychain refresh token | P2／sweeper:risk-security-boundary／Open |
| [#120723](https://github.com/NousResearch/hermes-agent/issues/120723) | 要求為可信 Anthropic 相容 proxy 保留已簽章 thinking block | 未標記／Open |
| [#109115](https://github.com/NousResearch/hermes-agent/issues/109115) | Vertex-backed OpenAI 相容 endpoint 拒絕 `notify` 參數 | P2／Open |
| [#117793](https://github.com/NousResearch/hermes-agent/issues/117793) | llama.cpp context 溢位錯誤格式未被辨識 | P2／Open |

社群媒體方面，過去 24 小時查無 Nous Research 官方帳號、r/LocalLLaMA、r/selfhosted 或其他部落格對 Hermes-Agent 的新討論串。WebSearch 找到的內容多為既有、日期較早的 Hermes Desktop／Bot Mode 宣傳貼文，以及若干第三方 SEO／彙整型網站（gradually.ai、releasebot.io 等）的「changelog」頁面，其內容與 GitHub 官方 release note 高度重疊、更新時效性與獨立性存疑，本報告不採用其作為獨立佐證來源。上述新增安全性 issue 清單受頁面顯示筆數限制，可能仍有未列出的項目，且未逐則人工核對精確時間戳。

---

*報告生成時間：2026-09-28 UTC*
