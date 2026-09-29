# Hermes-Agent 每日情報 — 2026-09-29

> 資料來源涵蓋截至 2026-09-29 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **目前穩定版仍為 v0.21.5（v2026.9.24）**：過去 24 小時查無新版本或新 tag，main 分支持續有大量一般性 fix commit（gateway browse 能力範圍收斂、bare `/browse` 指令區塊截斷修復、desktop 本地 profile 更名不影響其他連線的 owner 提示、Tailscale SSH 瀏覽器檢查、gateway 重啟輪詢與 socket 重連等）。（[Releases](https://github.com/NousResearch/hermes-agent/releases)、[Commits](https://github.com/NousResearch/hermes-agent/commits/main)）

2. **新開高優先度（P1）Bug：context compression 會就地竄改「進行中」的 tool-call 參數，導致終端機／檔案／程式碼執行內容毀損**：[#127260](https://github.com/NousResearch/hermes-agent/issues/127260)（9/29）指出 Windows git-bash 環境下，壓縮器內部的標記字元（`⟪`/`⟫`）會外洩進使用者的工具呼叫 payload，並出現字串被插入雜訊 token（如 `MAX_SPAN = 40riers`）、UTF-8 BOM 不穩定等三種毀損型態，會靜默寫壞檔案或造成指令莫名失敗，且會斷續發生、重試有時可過。標記為 P1、無 workaround，尚無對應 PR。（[#127260](https://github.com/NousResearch/hermes-agent/issues/127260)）

3. **新開高優先度（P1）Bug：本地模型串流陷入重複迴圈時，在無上限端點（如 LM Studio）永遠不會停止**：[#127234](https://github.com/NousResearch/hermes-agent/issues/127234)（9/29）指出偵測邏輯只在串流「結束後」才檢查內容，但無上限端點本來就不會送出 `max_tokens`，stale-stream 偵測需要「沉默」才觸發（迴圈持續在送 chunk 不算沉默），cron 逾時只認閒置而非持續輸出，且既有檢查完全忽略 reasoning channel，導致實測曾累積 43.6 萬字元才被人工中止。作者已表示願意提交修復（在串流過程中即時比對既有的 `is_runaway_repetition`／16K 字元門檻，含 reasoning channel），尚無 PR。（[#127234](https://github.com/NousResearch/hermes-agent/issues/127234)）

4. **Codex（OpenAI）OAuth 憑證池：單一模型的 entitlement 拒絕會誤傷整組憑證，導致 auxiliary 呼叫全部回報「查無憑證」**：[#126768](https://github.com/NousResearch/hermes-agent/issues/126768，9/28，P2)指出憑證池選取邏輯在未指定模型時以 `model_cooldown_until(entry, None)` 判斷，只要有任一模型（如 gpt-5.4-mini）被拒絕並產生長達 365 天的冷卻，就會連帶擋下同一憑證上其他本可正常使用的模型，造成 auxiliary 壓縮任務全數靜默失敗。已標記重複（duplicate），建議修法為改用 `select(model=...)` 只鎖定該模型的冷卻。目前 workaround 為 `hermes auth reset openai-codex` 後重啟。（[#126768](https://github.com/NousResearch/hermes-agent/issues/126768)）

5. **安全性：`OPENAI_BASE_URL`／`OPENAI_API_KEY` 可能因只比對主機名稱、未比對完整 origin，外洩給同主機名下不同 scheme／port 的 OpenRouter 鏡像**：[#126218](https://github.com/NousResearch/hermes-agent/issues/126218，9/28，P2，security)。回報者指出程式碼中已有 `utils.base_url_origin` 可直接沿用比對完整 origin，並表示願送修復 PR，目前尚未有對應 PR。（[#126218](https://github.com/NousResearch/hermes-agent/issues/126218)）

6. **新一批 `sweeper:risk-security-boundary` 安全性 issue 於 9/28 集中開立，本批含兩則值得留意的高風險項目**：`hermes proxy` 指令啟動的轉發端點完全無驗證、無 CORS／Origin 檢查，且會自動把操作者的真實 API 金鑰代入任何 bearer token（[#126757](https://github.com/NousResearch/hermes-agent/issues/126757)，P3，已有對應 PR [#126805](https://github.com/NousResearch/hermes-agent/pull/126805) 待審），以及 A2A push-callback 的 SSRF 防護只比對主機名字串、未做 DNS 解析，可被 169.254.x.x／RFC1918／localhost 等內網位址與 HTTP 轉址繞過（[#126755](https://github.com/NousResearch/hermes-agent/issues/126755)，P3，已有對應 PR [#126814](https://github.com/NousResearch/hermes-agent/pull/126814) 待審，且 README 現有文字聲稱「已有 SSRF 防護」與實作不符）。同批另有 A2A 信任清單預設失效開放（[#126756](https://github.com/NousResearch/hermes-agent/issues/126756)）、`auth.json` 曾以全域可讀權限建立（[#126950](https://github.com/NousResearch/hermes-agent/issues/126950)）等至少 10 則，詳見第 4 節。（[#126757](https://github.com/NousResearch/hermes-agent/issues/126757)、[#126755](https://github.com/NousResearch/hermes-agent/issues/126755)）

7. **前幾日追蹤項目多數無新進展**：URL 帳密遮罩 PR [#123319](https://github.com/NousResearch/hermes-agent/pull/123319)（仍為 draft，最後動態停在 9/27）、Opus 5.5 強制 thinking 修復 [#121016](https://github.com/NousResearch/hermes-agent/pull/121016)（仍 open，無新進展）、Anthropic token 雙重計費修復 [#125845](https://github.com/NousResearch/hermes-agent/pull/125845)（仍待 review，無新評論）、Ollama 閒置記憶體釋放 [#120944](https://github.com/NousResearch/hermes-agent/pull/120944)（仍待作者回應阻斷性意見）、CometAPI＋Opus 5.5 驗證錯誤 [#122672](https://github.com/NousResearch/hermes-agent/issues/122672)（無新進展）、Azure Foundry／AWS Bedrock GPT-6 路由整併系列（[#120327](https://github.com/NousResearch/hermes-agent/pull/120327)、[#124105](https://github.com/NousResearch/hermes-agent/pull/124105)、[#122116](https://github.com/NousResearch/hermes-agent/pull/122116)、[#115931](https://github.com/NousResearch/hermes-agent/issues/115931)，仍待 maintainer 整併決策）過去 24 小時皆無新評論或新 commit。

8. **社群媒體：過去 24 小時查無 Nous Research 官方帳號的新發文**。WebSearch 僅找到既有的官方介紹頁（hermes-agent.nousresearch.com）與第三方彙整／SEO 型網站（gradually.ai、releasebot.io、petronellatech.com 等）之「changelog」或「指南」頁面，內容與既有 release note 重疊或屬一般性產品介紹，非過去 24 小時內的新動態，本報告不採用其作為獨立來源。

---

## 2. LLM 串接實戰回報

### Claude（Anthropic）
- **無新增 issue／PR**：過去 24 小時查無新的 Anthropic 專屬回報。
- **既有追蹤項目無新進展**：[#125845](https://github.com/NousResearch/hermes-agent/pull/125845)（token 用量雙重計費修復，待 review）、[#121016](https://github.com/NousResearch/hermes-agent/pull/121016)（Opus 5.5 強制 thinking 修復，兩項 nit 未解）、[#122672](https://github.com/NousResearch/hermes-agent/issues/122672)（CometAPI＋Opus 5.5 thinking 驗證錯誤）、[#125095](https://github.com/NousResearch/hermes-agent/issues/125095)（claude-subscription-directsdk 被強制走 non-streaming 路徑）、[#124874](https://github.com/NousResearch/hermes-agent/issues/124874)（HTTP-200 拒絕／無效回應後 fallback 不留原因記錄）。

### GPT（OpenAI／Azure／Bedrock）
- **新增：Codex OAuth 憑證池「一顆老鼠屎壞一鍋粥」問題**：[#126768](https://github.com/NousResearch/hermes-agent/issues/126768)（9/28，P2），單一模型 entitlement 冷卻誤擋同憑證下其他模型，導致 auxiliary 呼叫全部失敗，已標記重複，待修。
- **新增（安全性）：`OPENAI_BASE_URL`／`OPENAI_API_KEY` 可能外洩給同主機名的 OpenRouter 鏡像**：[#126218](https://github.com/NousResearch/hermes-agent/issues/126218)（9/28，P2），因只比對主機名而非完整 origin。
- **Azure Foundry／AWS Bedrock GPT-6 路由整併系列無新進展**：[#120327](https://github.com/NousResearch/hermes-agent/pull/120327)／[#124105](https://github.com/NousResearch/hermes-agent/pull/124105)（重複待收斂）、[#122116](https://github.com/NousResearch/hermes-agent/pull/122116)（已通過 67/69 Bedrock 測試，仍待 maintainer 就 [#115931](https://github.com/NousResearch/hermes-agent/issues/115931) 的競爭方案做整併決策）過去 24 小時皆無新評論。

### Gemini（Google）
- **無新增 issue／PR**：過去 24 小時查無新的 Gemini 專屬回報。
- **既有追蹤項目無新進展**：[#125412](https://github.com/NousResearch/hermes-agent/issues/125412)（原生請求逾時設定被忽略）、[#124773](https://github.com/NousResearch/hermes-agent/issues/124773)（Windows 上 `auxiliary.*` 呼叫繞過系統 proxy）、[#121183](https://github.com/NousResearch/hermes-agent/pull/121183)（原生 Google AI Studio 圖像生成後端，靜默降級與測試缺口未修）。

### 本地模型／Local Runtime
- **新增高優先度（P1）Bug：無上限端點（如 LM Studio）遇串流重複迴圈永不停止**：[#127234](https://github.com/NousResearch/hermes-agent/issues/127234)（9/29），實測案例曾累積 43.6 萬字元才被人工中止，作者已表態願送修復。
- [PR #120944](https://github.com/NousResearch/hermes-agent/pull/120944) 閒置模型記憶體釋放：仍待作者回應 9/27 的阻斷性審查意見，無新進展。
- llama.cpp context 溢位訊息辨識問題（[#117793](https://github.com/NousResearch/hermes-agent/issues/117793)）無新進展。

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 類型 | 重點 |
|------|----------|------|------|
| **v0.21.5（v2026.9.24）** | 2026-09-24 | Patch rollup | **目前最新穩定版**；過去 24 小時內查無更新版本 |
| v0.21.4（v2026.9.21） | 2026-09-21 | Patch rollup | host-wide gateway singleton lock、`--format stream-json`、`skills.auto_load`、12 個新社群外掛 |
| v0.21.3（v2026.9.14） | 2026-09-14 | Patch rollup | remote gateway 登入失效修復、refresh token 併發合併、refresh 移出 event loop |
| v0.21.2（v2026.9.11） | 2026-09-11 | Patch rollup | state.db 可靠性戰役、multiplexed profile 隔離強化、密碼隱藏式憑證保險箱 |
| v0.21.1（v2026.9.7） | 2026-09-07 | Patch rollup | 效能、MCP 授權、cron、delegation 修復 |

過去 24 小時 main 分支持續有大量一般性合併，較值得留意的包含：gateway browse 能力豁免範圍收斂為僅限 registry 指令、修復 bare `/browse` 指令區塊被截斷的問題、desktop 本地 profile 更名不再影響其他連線的 owner 提示顯示、新增 Tailscale SSH 瀏覽器檢查、gateway 重啟輪詢與 client socket 重連修復、web client sidecar redial 預算調整、狀態管理面的「混合世代」tool-call uid 折疊修復等。皆屬一般性維護項目，未見 Opus 5.5 或重大版本相關的新合併項目，亦無新 tag。（[Releases 總覽](https://github.com/NousResearch/hermes-agent/releases)、[main 分支 commits](https://github.com/NousResearch/hermes-agent/commits/main)）

---

## 4. 社群討論與已知問題

**本日最值得留意的是兩則新開的高優先度（P1）Bug——context compression 竄改進行中的 tool-call 參數、本地模型串流重複迴圈永不停止——以及 Codex OAuth 憑證池的連坐冷卻問題與 `hermes proxy`／A2A SSRF 兩則安全性項目已有對應修復 PR 待審。**

| Issue／PR | 說明 | 優先度／狀態 |
|------|------|----------|
| [#127260](https://github.com/NousResearch/hermes-agent/issues/127260) | context compression 竄改進行中的 tool-call 參數，毀損終端機／檔案／程式碼內容 | P1／Open |
| [#127234](https://github.com/NousResearch/hermes-agent/issues/127234) | 無上限本地端點串流重複迴圈永不停止 | P1／Open（作者將送 PR） |
| [#126768](https://github.com/NousResearch/hermes-agent/issues/126768) | Codex OAuth 憑證池：單一模型冷卻誤擋整組憑證的 auxiliary 呼叫 | P2／重複／Open |
| [#126218](https://github.com/NousResearch/hermes-agent/issues/126218) | `OPENAI_BASE_URL`／`OPENAI_API_KEY` 只比對主機名，可能外洩給 OpenRouter 鏡像 | P2／security／Open |
| [#126757](https://github.com/NousResearch/hermes-agent/issues/126757) | `hermes proxy` 無驗證、無 CORS 檢查，自動代入真實 API 金鑰 | P3／sweeper:risk-security-boundary／Open（PR [#126805](https://github.com/NousResearch/hermes-agent/pull/126805) 待審） |
| [#126755](https://github.com/NousResearch/hermes-agent/issues/126755) | A2A push-callback SSRF 防護僅比對主機名字串，可被內網位址與轉址繞過 | P3／sweeper:risk-security-boundary／Open（PR [#126814](https://github.com/NousResearch/hermes-agent/pull/126814) 待審） |
| [#126756](https://github.com/NousResearch/hermes-agent/issues/126756) | A2A 信任對等清單未設定時預設失效開放 | sweeper:risk-security-boundary／Open |
| [#126950](https://github.com/NousResearch/hermes-agent/issues/126950) | `auth.json`（refresh token）曾以全域可讀權限建立，事後才收斂 | sweeper:risk-security-boundary／Open |
| [#126942](https://github.com/NousResearch/hermes-agent/issues/126942) | 官方 sandbox image 以 `curl \| tar` 安裝遠端執行檔，無完整性驗證 | sweeper:risk-security-boundary／Open |
| [#126939](https://github.com/NousResearch/hermes-agent/issues/126939) | WSL 外部連結路徑交給 `cmd.exe`，僅有 scheme allowlist | sweeper:risk-security-boundary／Open |
| [#126902](https://github.com/NousResearch/hermes-agent/issues/126902) | `key_cmd` token helper 會取得所綁定 profile 的 adapter 密鑰 | sweeper:risk-security-boundary／Open |
| [#126831](https://github.com/NousResearch/hermes-agent/issues/126831) | Bitwarden 密鑰來源將 429 誤判為 INTERNAL，profile 會在無密鑰狀態下啟動 | sweeper:risk-security-boundary／Open |
| [#126824](https://github.com/NousResearch/hermes-agent/issues/126824) | WhatsApp 採用的橋接沿用原本 DM／群組政策與白名單 | sweeper:risk-security-boundary／Open |
| [#126431](https://github.com/NousResearch/hermes-agent/issues/126431) | Multiplexed gateway：路由 profile 的 sandbox 未命中時退回啟動 profile 的環境 | sweeper:risk-security-boundary／Open |
| [#123319](https://github.com/NousResearch/hermes-agent/pull/123319) | `hermes approvals suggest` 遮罩 URL 帳密，第 3 項瑕疵仍刻意擱置 | Draft／Open（無新進展） |
| [#125761](https://github.com/NousResearch/hermes-agent/issues/125761) | `anthropic_content_blocks` 造成 token 用量雙重計費 | P2／Open |
| [#125845](https://github.com/NousResearch/hermes-agent/pull/125845) | 上述問題修復 PR，112 項測試通過，待 review | Open（無新進展） |
| [#121016](https://github.com/NousResearch/hermes-agent/pull/121016) | Opus 5.5「強制 thinking」修復，核心邏輯已確認、剩兩項 nit | Open（無新進展） |
| [#122672](https://github.com/NousResearch/hermes-agent/issues/122672) | CometAPI＋Opus 5.5 thinking 參數驗證錯誤 | P2／Open |
| [#125095](https://github.com/NousResearch/hermes-agent/issues/125095) | `claude-subscription-directsdk` 被強制走 non-streaming 路徑 | Open |
| [#124874](https://github.com/NousResearch/hermes-agent/issues/124874) | HTTP-200 拒絕／無效回應後 fallback 不留原因記錄 | Open |
| [#121183](https://github.com/NousResearch/hermes-agent/pull/121183) | 原生 Google AI Studio 圖像生成後端，靜默降級與測試缺口待修正 | Open（無新進展） |
| [#125412](https://github.com/NousResearch/hermes-agent/issues/125412) | Gemini 原生請求逾時設定被忽略 | P2／Open |
| [#124773](https://github.com/NousResearch/hermes-agent/issues/124773) | Windows 上 `auxiliary.*` 呼叫繞過系統 proxy，Gemini／OpenRouter 回 403 | P2／Open |
| [#120944](https://github.com/NousResearch/hermes-agent/pull/120944) | 閒置模型記憶體釋放，審查指出錯誤處理會害死整個回合 | Open（待作者回應） |
| [#124105](https://github.com/NousResearch/hermes-agent/pull/124105) | Azure Foundry GPT-6 Responses API 路由（與 #120327 重複） | Open（重複待收斂） |
| [#120327](https://github.com/NousResearch/hermes-agent/pull/120327) | Azure Foundry gpt-6-luna／sol 需 Responses API（原始 PR） | P3／Open |
| [#122116](https://github.com/NousResearch/hermes-agent/pull/122116) | AWS Bedrock GPT-6 Responses 路由修復，已過 67/69 測試，與 #115931 待整併 | P2／Open |
| [#117793](https://github.com/NousResearch/hermes-agent/issues/117793) | llama.cpp context 溢位錯誤格式未被辨識 | P2／Open |

（另有若干同批 9/28 開立、內容較次要或標題為「稽核」型式的 sweeper 安全性 issue 未逐一列出，如 [#127100](https://github.com/NousResearch/hermes-agent/issues/127100)、[#126935](https://github.com/NousResearch/hermes-agent/issues/126935)（已撤回）；清單受頁面顯示筆數限制，實際總數可能更多，未逐則人工核對時間戳精確性。）

社群媒體方面，過去 24 小時查無 Nous Research 官方帳號、r/LocalLLaMA、r/selfhosted 或其他部落格對 Hermes-Agent 的新討論串。WebSearch 找到的內容多為官方產品介紹頁與既有第三方 SEO／彙整型網站（gradually.ai、releasebot.io、petronellatech.com 等）的一般性頁面，非過去 24 小時內的新動態，本報告不採用其作為獨立佐證來源。

---

*報告生成時間：2026-09-29 UTC*
