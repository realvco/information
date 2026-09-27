# Hermes-Agent 每日情報 — 2026-09-27

> 資料來源涵蓋截至 2026-09-27 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **目前穩定版仍為 v0.21.5（v2026.9.24）**：過去 24 小時內查無新版本或新 tag，main 分支持續有一般性 bug fix／feature commit（如 skills 目錄新增 `brag`／`brag-slim`、`transform_llm_output` 串流路由修復、TUI session kernel 回收等），但無 Opus 5.5 或版本號相關內容。（[Releases](https://github.com/NousResearch/hermes-agent/releases)、[Commits](https://github.com/NousResearch/hermes-agent/commits/main)）

2. **Opus 5.5「強制 thinking」修復 PR 仍卡在審查，未見退步**：[PR #121016](https://github.com/NousResearch/hermes-agent/pull/121016) 於 2026-09-26 收到 reviewer Enough1122 的詳細 code review，確認修復本身正確（新測試在 base 上會失敗、在 head 上通過），但點出兩項非阻斷性問題：(1) 帶點寫法 `claude-opus-5.5` 缺少直接驗證測試；(2) 貢獻者歸屬對應到組織帳號而非實際作者。仍為 open，尚待處理與 maintainer 核准。

3. **CometAPI＋Opus 5.5 thinking 驗證錯誤（#122672）過去 24 小時無新進展**：仍為 open、P2，無新評論，尚無對應 PR。

4. **原生 Google AI Studio 圖像生成後端提案收到阻斷性 review**：[PR #121183](https://github.com/NousResearch/hermes-agent/pull/121183) 於 9/26 由 Enough1122 提出兩項問題：(1) 當參考圖片無法讀取時會「靜默降級」為文字生圖，使用者要求編輯圖片卻無聲得到不相關的生成結果，且無任何錯誤提示；(2) 測試套件未覆蓋此失敗路徑。屬於會擋下合併的意見，過去 24 小時內尚無作者回應或修正。

5. **Azure Foundry GPT-6 Responses API 修復出現重複 PR，已由 maintainer 確認**：既有 [PR #120327](https://github.com/NousResearch/hermes-agent/pull/120327)（P3，處理 GPT-6 Luna／Sol 在 function tools ＋ reasoning 併用時遭 Azure Foundry 以 400 拒絕的問題）於 9/26 被使用者 MuhammadUsamaMX 提及與新開的 [PR #124105](https://github.com/NousResearch/hermes-agent/pull/124105)（同日開立，同樣改走 `/v1/responses`）重複；maintainer alt-glitch 已於同日留言確認兩者「實作相同路由邏輯，新增的 resolver 測試不影響 runtime 機制」，兩者無需都合併。AWS Bedrock 端的同類路由修復 [PR #122116](https://github.com/NousResearch/hermes-agent/pull/122116)（與 #115931 衝突待整併）過去 24 小時無新進展。

6. **Ollama 閒置 VRAM 自動釋放 PR（#120944）狀態未變**：仍為 open／ready for review，過去 24 小時無 reviewer 回應。

7. **新一批 `sweeper:risk-security-boundary` 安全性 issue 於 9/26～9/27 開立**：已核實至少 12 則，包含憑證池 plugin `refresh_credential` 結果可覆寫 row 身分與分類欄位（[#124623](https://github.com/NousResearch/hermes-agent/issues/124623)，9/27 開立，reporter 標註目前為理論風險）、bare `custom` provider 在 `/model` 選單切換與 session 重啟後配對錯誤金鑰（[#124140](https://github.com/NousResearch/hermes-agent/issues/124140)，9/26，P2）、Linux 上 Dashboard 長時間持有 profile auth-store 鎖導致 Desktop 助理啟動逾時（[#124533](https://github.com/NousResearch/hermes-agent/issues/124533)，9/26，P2，needs-repro）、bare custom provider 與具名 provider 共用 base_url 時互相誤用對方金鑰（[#124527](https://github.com/NousResearch/hermes-agent/issues/124527)）、multiplex 下不同 profile 的 API-server 對話因開場訊息相同而共用同一 Docker sandbox（[#123989](https://github.com/NousResearch/hermes-agent/issues/123989)）等，詳見第 4 節完整列表。**註：清單受頁面顯示筆數限制，實際總數可能更多，未逐則人工核對時間戳精確性。**

8. **URL userinfo 遮罩修復 PR 遭安全審查擋下**：[PR #123319](https://github.com/NousResearch/hermes-agent/pull/123319)（`hermes approvals suggest` 遮罩 URL 中 Basic Auth 帳密）雖已獲一名 reviewer 核准，但 Enough1122 於審查中指出三項阻斷性瑕疵：(1) 遮罩 regex 綁定第一個 `@` 而非最後一個，含 `@` 字元的密碼仍部分外洩；(2) AWS `X-Amz-Signature` 等查詢參數因 canonicalization 把連字號轉為底線而無法命中遮罩規則；(3) scheme-agnostic 的參數比對會誤遮罩非 URL 的一般 shell 文字，造成可讀性退化。目前仍為 open，待修正。

9. **昨日追蹤的其餘項目多數無新進展**：Anthropic 模型探測忽略 `ANTHROPIC_BASE_URL`（[#120844](https://github.com/NousResearch/hermes-agent/issues/120844)）、Claude OAuth 過期欄位損毀（[#118714](https://github.com/NousResearch/hermes-agent/issues/118714)）、Claude Code 專屬 Keychain refresh token 誤耗修復（[#118199](https://github.com/NousResearch/hermes-agent/pull/118199)，最近一次活動仍是 9/25 的交叉引用）、可信 proxy 保留簽章 thinking block 功能請求（[#120723](https://github.com/NousResearch/hermes-agent/issues/120723)）、Vertex `notify` 參數 400 問題（[#109115](https://github.com/NousResearch/hermes-agent/issues/109115)）、llama.cpp context 溢位訊息辨識問題（[#117793](https://github.com/NousResearch/hermes-agent/issues/117793)）、Nano Banana 外掛政策駁回維持原狀（[#120851](https://github.com/NousResearch/hermes-agent/pull/120851)）皆仍為 open／closed 未變，查無新評論。

10. **社群媒體：未查到過去 24 小時內新的官方或社群討論**。WebSearch 未找到 Nous Research 官方帳號在過去 24 小時內對 Opus 5.5 上線 Hermes Agent 的新發文（僅查到較早期、已過時的 Opus 5／Fable 5 相關宣傳貼文），昨日提及的 teknium1「Opus 5.5 已上線 Nous Portal」貼文仍未見官方或原始頁面二次證實。另注意到第三方彙整帳號 thanhtantran/agents-radar 也在追蹤 Hermes Agent 生態系動態，但交叉核對其聲稱「已合併」的 [#123319](https://github.com/NousResearch/hermes-agent/pull/123319)、[#123321](https://github.com/NousResearch/hermes-agent/pull/123321) 後，兩者於 GitHub 官方頁面上實際狀態均為 **open（未合併）**，該第三方來源之合併狀態描述不可靠，本報告不採用其結論，僅以 GitHub 官方頁面核實後的資訊為準。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）
- **Opus 5.5 強制 thinking 修復持續審查中**：[#121016](https://github.com/NousResearch/hermes-agent/pull/121016) 9/26 收到 reviewer 詳細 review，核心修復邏輯獲確認正確，僅剩兩項非阻斷性 nit（帶點模型名稱測試覆蓋、貢獻者歸屬），尚待處理。
- **無新進展**：[#122672](https://github.com/NousResearch/hermes-agent/issues/122672) CometAPI＋Opus 5.5 thinking 參數驗證錯誤；[#120844](https://github.com/NousResearch/hermes-agent/issues/120844) `ANTHROPIC_BASE_URL` 探測問題；[#120723](https://github.com/NousResearch/hermes-agent/issues/120723) 簽章 thinking block 保留請求；[#118714](https://github.com/NousResearch/hermes-agent/issues/118714) OAuth 過期欄位損毀；[#118199](https://github.com/NousResearch/hermes-agent/pull/118199) Keychain refresh token 誤耗修復。

### GPT（OpenAI／Azure／Bedrock）
- **Azure Foundry 修復出現重複 PR，maintainer 已確認去重**：[#124105](https://github.com/NousResearch/hermes-agent/pull/124105)（9/26 新開）與既有 [#120327](https://github.com/NousResearch/hermes-agent/pull/120327) 實作相同的 GPT-6 Luna／Sol Responses API 路由邏輯，alt-glitch 已留言標記為重複，兩者無需都合併。
- **無新進展**：[#122116](https://github.com/NousResearch/hermes-agent/pull/122116) AWS Bedrock GPT-6 路由修復，與 [#115931](https://github.com/NousResearch/hermes-agent/pull/115931) 的衝突仍待 maintainer 整併，過去 24 小時無新評論。

### Gemini（Google）
- **原生 Google AI Studio 圖像後端提案收到阻斷性意見**：[#121183](https://github.com/NousResearch/hermes-agent/pull/121183) 9/26 review 指出參考圖片讀取失敗時會靜默降級為文字生圖、且無測試覆蓋，屬合併前需修正的問題。
- Nano Banana 外掛提案（[#120851](https://github.com/NousResearch/hermes-agent/pull/120851)）政策駁回狀態未變。

### 本地模型／Local Runtime
- [PR #120944](https://github.com/NousResearch/hermes-agent/pull/120944) Ollama 閒置 VRAM 自動釋放：狀態未變，仍為 ready for review，無 reviewer 回應。
- llama.cpp context 溢位訊息辨識問題（[#117793](https://github.com/NousResearch/hermes-agent/issues/117793)）無新進展。

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 類型 | 重點 |
|------|----------|------|------|
| **v0.21.5（v2026.9.24）** | 2026-09-24 10:09 UTC | Patch rollup | **目前最新穩定版**；過去 24 小時內查無更新版本 |
| v0.21.4（v2026.9.21） | 2026-09-21 18:10 | Patch rollup | host-wide gateway singleton lock、`--format stream-json`、`skills.auto_load`、12 個新社群外掛 |
| v0.21.3（v2026.9.14） | 2026-09-14 | Patch rollup | remote gateway 登入失效修復、refresh token 併發合併、refresh 移出 event loop |
| v0.21.2（v2026.9.11） | 2026-09-11 | Patch rollup | state.db 可靠性戰役、multiplexed profile 隔離強化、密碼隱藏式憑證保險箱 |
| v0.21.1（v2026.9.7） | 2026-09-07 | Patch rollup | 效能、MCP 授權、cron、delegation 修復 |

過去 24 小時 main 分支持續有零星合併（skills 目錄新增 `brag`／`brag-slim`、API server 串流路由的 `transform_llm_output` 修復、TUI session kernel 回收、desktop 附件貼上排序修復等），皆屬一般性維護，未見 Opus 5.5 或重大功能相關的新合併項目，亦無新 tag。官方仍表示 v0.21.0 以來累積的完整功能列表與貢獻者名單將於 v0.22.0 一併補齊。（[Releases 總覽](https://github.com/NousResearch/hermes-agent/releases)、[main 分支 commits](https://github.com/NousResearch/hermes-agent/commits/main)）

---

## 4. 社群討論與已知問題

**本日最值得留意的是新一批集中在憑證／multiplex profile 隔離的 `sweeper:risk-security-boundary` 安全性 issue，以及 Azure Foundry GPT-6 修復重複 PR 已由 maintainer 確認去重。**

| Issue／PR | 說明 | 優先度／狀態 |
|------|------|----------|
| [#121016](https://github.com/NousResearch/hermes-agent/pull/121016) | Opus 5.5「強制 thinking」修復，9/26 review 通過核心邏輯、剩兩項 nit | Open |
| [#122672](https://github.com/NousResearch/hermes-agent/issues/122672) | CometAPI＋Opus 5.5 thinking 參數驗證錯誤 | P2／Open |
| [#121183](https://github.com/NousResearch/hermes-agent/pull/121183) | 原生 Google AI Studio 圖像生成後端，9/26 review 指出靜默降級與測試缺口 | Open（待修正） |
| [#124105](https://github.com/NousResearch/hermes-agent/pull/124105) | Azure Foundry GPT-6 Responses API 路由（與 #120327 重複，maintainer 已確認） | Open（重複待收斂） |
| [#120327](https://github.com/NousResearch/hermes-agent/pull/120327) | Azure Foundry gpt-6-luna／sol 需 Responses API（原始 PR） | P3／Open |
| [#122116](https://github.com/NousResearch/hermes-agent/pull/122116) | AWS Bedrock GPT-6 Responses 路由修復，與 #115931 衝突待整併 | P2／Open |
| [#120944](https://github.com/NousResearch/hermes-agent/pull/120944) | 閒置 Ollama 模型記憶體壓力下自動釋放 VRAM | Open（ready for review） |
| [#123319](https://github.com/NousResearch/hermes-agent/pull/123319) | `hermes approvals suggest` 遮罩 URL 帳密，審查揪出 3 項遮罩繞過瑕疵 | Open（待修正） |
| [#124623](https://github.com/NousResearch/hermes-agent/issues/124623) | 憑證池 plugin `refresh_credential` 結果可覆寫 row 身分／分類欄位 | P3／sweeper:risk-security-boundary／Open |
| [#124533](https://github.com/NousResearch/hermes-agent/issues/124533) | Linux：Dashboard 長時間持有 auth-store 鎖，Desktop 助理啟動逾時 | P2／needs-repro／Open |
| [#124140](https://github.com/NousResearch/hermes-agent/issues/124140) | bare `custom` provider：`/model` 切換與 session 重啟後配對錯誤金鑰 | P2／sweeper:risk-security-boundary／Open |
| [#124527](https://github.com/NousResearch/hermes-agent/issues/124527) | bare `custom` provider 與具名 provider 共用 base_url 互相誤用金鑰 | sweeper:risk-security-boundary／Open |
| [#123989](https://github.com/NousResearch/hermes-agent/issues/123989) | Multiplex：不同 profile 的 API-server 對話因開場訊息相同而共用 Docker sandbox | sweeper:risk-security-boundary／Open |
| （另有至少 6 則同批 9/26～9/27 開立的 `sweeper:risk-security-boundary` issue：[#124593](https://github.com/NousResearch/hermes-agent/issues/124593)、[#123980](https://github.com/NousResearch/hermes-agent/issues/123980)、[#123915](https://github.com/NousResearch/hermes-agent/issues/123915)、[#123906](https://github.com/NousResearch/hermes-agent/issues/123906)、[#123760](https://github.com/NousResearch/hermes-agent/issues/123760)、[#123748](https://github.com/NousResearch/hermes-agent/issues/123748)、[#123747](https://github.com/NousResearch/hermes-agent/issues/123747)） | 涵蓋 fallback／delegation 後仍以 base_url 誤配對 pool、多 profile 共用 xAI／MiniMax 登入 token 外洩到預設 profile、對稱連結 auth.json 導致併發寫入互相清空憑證、跨 profile 複製 refresh token 偵測請求、macOS 桌面版簽章與 Keychain 加密秘密保存問題、`_resolve_anthropic_pool_token` 文件宣稱唯讀卻實際寫入 auth.json 等 | sweeper:risk-security-boundary |
| [#120851](https://github.com/NousResearch/hermes-agent/pull/120851) | Nano Banana image_gen 外掛遭 maintainer 政策駁回 | Closed（未合併） |
| [#120844](https://github.com/NousResearch/hermes-agent/issues/120844) | Anthropic 模型探測忽略 `ANTHROPIC_BASE_URL` | 未標記／Open |
| [#118714](https://github.com/NousResearch/hermes-agent/issues/118714) | OAuth 過期欄位損毀 | P2／Open |
| [#118199](https://github.com/NousResearch/hermes-agent/pull/118199) | Hermes 誤耗 Claude Code 專屬 Keychain refresh token | P2／sweeper:risk-security-boundary／Open |
| [#120723](https://github.com/NousResearch/hermes-agent/issues/120723) | 要求為可信 Anthropic 相容 proxy 保留已簽章 thinking block | 未標記／Open |
| [#109115](https://github.com/NousResearch/hermes-agent/issues/109115) | Vertex-backed OpenAI 相容 endpoint 拒絕 `notify` 參數 | P2／Open |
| [#117793](https://github.com/NousResearch/hermes-agent/issues/117793) | llama.cpp context 溢位錯誤格式未被辨識 | P2／Open |

社群媒體方面，過去 24 小時查無 Nous Research 官方帳號、r/LocalLLaMA、r/selfhosted 或其他部落格對 Hermes-Agent 的新討論串；昨日提及的「Opus 5.5 已上線 Nous Portal」X 貼文（疑似 teknium1）仍未經官方或原始頁面二次證實。另外，第三方彙整帳號 thanhtantran/agents-radar 的每日報告經交叉核對後，其「PR 已合併」的部分描述與 GitHub 官方頁面實際狀態（仍為 open）不符，本報告未採用該來源之合併狀態結論。上述新增安全性 issue 清單受頁面顯示筆數限制，可能仍有未列出的項目，且未逐則人工核對精確時間戳。

---

*報告生成時間：2026-09-27 UTC*
