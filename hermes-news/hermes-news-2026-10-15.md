# Hermes-Agent 每日情報 — 2026-10-15

> 資料來源涵蓋截至 2026-10-15 UTC 過去 24 小時內可查詢到的最新公開資訊。

---

## 1. 今日重點摘要

1. **v0.22.1 發布，成為目前最新穩定版**：GitHub Releases 標示日期為 2026-10-14，屬修補版，內容以 Windows Store 修正與外掛更新為主，並新增 Nemotron Super 3.5 預設參數、模型選擇器置頂顯示精選清單。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

2. **v0.22.0「The Threshold Release」已列於 Releases（標示 2026-10-13）**：昨日情報寫「v0.22.0 尚未發布」，本次查證 Releases 頁面已有此版本，特此更正。內容涵蓋 v0.21.0 以來約 24,200 個 commit、約 6,080 個合併 PR，含首次啟動引導、Connectors、一鍵 llama.cpp 本地模型設定、穩定版更新通道、簽章桌面套件等。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）

3. **今日新增 issue 以桌面版啟動／更新、gateway 與設定相關問題為主**，多數標為 P2（見第 4 節）。

---

## 2. LLM 串接實戰回報

- Claude／GPT／Gemini／本地模型：過去 24 小時未查到獨立的使用者實測回報。
- 依 v0.22.0 官方說明，模型支援包含 Claude 5.5 系列、GPT-6 變體與 Gemini 4 Argon；先前推理內容預設會重送，遇暫時性 503／529／逾時錯誤會以有限次數重試。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）
- 本地模型：v0.22.0 提供一鍵 llama.cpp 設定（依 CPU／CUDA／Metal／Vulkan／ROCm 挑選版本並依 GPU 與記憶體配置模型），目錄含 Qwen3.8 27B、Qwen3.6 35B-A3B、DeepSeek V4 Flash。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）
- v0.22.1 新增 Nemotron Super 3.5 預設參數。（[Releases](https://github.com/NousResearch/hermes-agent/releases)）
- 供應商相關新 issue：建議支援 OpenRouter Batch API 作為離線 agent swarm 的傳輸方式（[#138727](https://github.com/NousResearch/hermes-agent/issues/138727)，P3、待決策）。

---

## 3. 版本與 release 動態

| 版本 | 發布日期 | 說明 |
|------|----------|------|
| **v0.22.1** | 2026-10-14 | **目前最新穩定版**；Windows Store 修正、外掛目錄新增（韓文與巴西葡萄牙文語言包、TeamoRouter 等）、Kumiho Memory 移至記憶目錄、Nemotron Super 3.5 預設參數、安全預設與私有 worker 隔離、ACP 工具政策與 MCP 處理；40 位以上貢獻者 |
| v0.22.0 | 2026-10-13 | 「The Threshold Release」；引導式首次啟動（免 API key 的 `nous/welcome` 試用模型）、Connectors（Linear、Notion 等）、穩定版更新通道、簽章桌面套件（Windows MSIX、macOS 公證）、`hermes pm`、約 600 個審核外掛、瀏覽器代理可經 1Password／Bitwarden／加密保管庫登入而不讓模型看到密碼、Bot Screen |
| v0.21.7 | 2026-10-11 | 原始碼安裝改追蹤已標記 release；簽章推理在 session 恢復後延續 |
| v0.21.6 | 2026-10-08 | 新穩定版流程首發；dashboard 驗證、git filter、email gateway 寄件者白名單等安全修補 |

來源：[Releases](https://github.com/NousResearch/hermes-agent/releases)。

---

## 4. 社群討論與已知問題

以下為 2026-10-15 新增的部分 issue，優先度標籤取自 GitHub issue 頁面。

| Issue | 說明 | 優先度 |
|------|------|------|
| [#138754](https://github.com/NousResearch/hermes-agent/issues/138754) | macOS 桌面版在 Gateway profile 路徑僅大小寫不同時，啟動卡住 | P2 |
| [#138750](https://github.com/NousResearch/hermes-agent/issues/138750) | `hermes serve` 部分新工作入口略過准入檢查（retirement fence） | P2 |
| [#138741](https://github.com/NousResearch/hermes-agent/issues/138741) | `execute_code` 沙箱未帶入容器的 TZ，改用系統本地時間 | P2 |
| [#138716](https://github.com/NousResearch/hermes-agent/issues/138716) | 更新失敗，需重新安裝 | P2 |
| [#138709](https://github.com/NousResearch/hermes-agent/issues/138709) | Signal：訊息頁面永遠顯示「需要重新啟動」 | P2 |
| [#138695](https://github.com/NousResearch/hermes-agent/issues/138695) | `delegation.inherit_mcp_toolsets: false` 在 `delegate_task` 路徑上無作用 | P2 |
| [#138690](https://github.com/NousResearch/hermes-agent/issues/138690) | Windows 桌面版更新交接拒絕自己保管者的宣告（exit 2） | P2 |
| [#138689](https://github.com/NousResearch/hermes-agent/issues/138689) | 桌面版每次啟動都清除內建 tools-Python 的 site-packages | P2 |
| [#138748](https://github.com/NousResearch/hermes-agent/issues/138748) | CLI 執行 `hermes` 出現 `invalid_params` 錯誤（需重現） | P2 |
| [#138738](https://github.com/NousResearch/hermes-agent/issues/138738) | 功能建議：外掛 hook 與 middleware 可選擇 fail-closed | P3 |
| [#138740](https://github.com/NousResearch/hermes-agent/issues/138740) | 功能建議：`session.control` 新增 heartbeat／loop／goal 設定器 | P3 |

來源：[Issues](https://github.com/NousResearch/hermes-agent/issues)。
