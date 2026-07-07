# Hermes-Agent 每日情報｜2026-07-07

> 資料蒐集範圍：過去 24 小時內公開資訊（輔以近期背景脈絡）
> 撰寫語言：繁體中文

---

## 1. 今日重點摘要

過去 24 小時內 Hermes-Agent 無全新版本發布。最近一次重大版本為 **v0.18.0（2026.7.1）「The Judgment Release」**（發布於 2026-07-01），距今約 6 天，仍是本週最值得關注的更新。

1. **「The Judgment Release」清空所有 P0/P1 積壓**：v2026.7.1 包含約 1,720 次 commits、998 個合併 PR，以及 370+ 位社群貢獻者；完整關閉約 1,950 條 issues 與 PR，清零全部高優先級未解問題（0 open P0、0 open P1）。

2. **Coding Projects 正式登陸桌面應用**：桌面 App 新增側邊欄程式碼庫清單、coding rail、review pane 與 git worktree 管理，強化本機開發整合體驗。

3. **`/journey` 指令與記憶圖表上線**：可展示 agent 隨時間累積的記憶與技能，桌面端新增可播放的放射狀時間軸記憶圖，讓長期使用的個人化脈絡更易查閱。

4. **Mixture-of-Agents（MoA）成為一等公民**：使用者現在可以定義命名的模型集合（named ensembles），讓多模型協作在工作流程中標準化；agent 亦學會對照證據自我核實工作成果。

5. **背景 fan-out 委派（Background fan-out delegation）**：多個 subagent 可在背景並行執行，搭配 `/goal` 完成合約（completion contracts）、`/learn` 與 `/journey` 自我改善循環，讓長任務分解更有效率。

6. **Claude Agent SDK 訂閱路徑缺口（自 2026-06-15 起）**：Anthropic 引入獨立的月費 Agent SDK 點數機制後，目前尚無法將 Hermes 路由至該點數——即便是 Pro/Max 訂閱者也需另外持有 API key；相關 Feature Request 已開立（Issue #25267）。

7. **Claude Sonnet 4.6 仍為品質最佳選擇**：多份 2026 年基準測試報告（含 Hermes 實際工作負載）均指出 Claude Sonnet 4.6 在多步驟推理、複雜指令遵循與工具呼叫穩定性上表現最佳。

8. **DeepSeek V4 性價比躍升**：2026 年 3 月發布的 DeepSeek V4 在 SWE-bench Verified 從 69% 跳至 81%，並新增 1M token 情境視窗，成為預算受限部署的首選；唯工具呼叫（tool calling）格式不穩定，需偶爾重試。

9. **社群 153 人對 Judgment Release 正面反應**：GitHub 發布頁面顯示 153 個表情反應，社群熱度顯著，各類優化指南與 awesome-hermes-agent 策展清單持續更新。

---

## 2. LLM 串接實戰回報

### 2-1 Claude Agent SDK 訂閱路徑無法對應 Hermes 點數

- **來源**：[GitHub Issue #25267 – Feature: Claude Agent SDK model provider with subscription OAuth](https://github.com/NousResearch/hermes-agent/issues/25267)
- **問題**：Anthropic 自 2026-06-15 起為第三方應用程式透過 Claude 訂閱認證新增獨立月費 Agent SDK 點數，然而 Hermes-Agent 目前沒有機制將請求路由至該點數池。即使是 Pro/Max 訂閱者，只要透過 Hermes 使用 Claude，仍須另行維護付費 API key。
- **提議方案**：建立新的 model-provider 插件，以 Claude Agent SDK + 訂閱 OAuth 包裝（對應現有的 Codex OAuth 路徑），讓訂閱者可免 API key 額外費用使用 Claude。
- **現況**：Feature Request 已開立，尚無合併 ETA。

### 2-2 DeepSeek V4 工具呼叫格式不穩定

- **來源**：[remoteopenclaw.com – Best DeepSeek Models for Hermes Agent](https://www.remoteopenclaw.com/blog/best-deepseek-models-for-hermes)、[foxinthebox.io – Best Models for Hermes Agents May 2026](https://foxinthebox.io/blog/best-models-for-hermes-agents/)
- **問題**：DeepSeek V4 相較 Claude Sonnet 或 GPT-4.1，更頻繁產生格式錯誤的工具呼叫（malformed tool calls），在 Web 搜尋、檔案操作、程式碼生成等直接任務尚可接受，但在需要多輪工具鏈的複雜 agent 工作流中需加入重試機制。
- **建議**：在 Hermes 配置中為 DeepSeek V4 啟用工具呼叫重試；或改用 Claude Sonnet 4.6 作為工具密集任務的主力模型。

### 2-3 各 LLM 提供者實測效能比較（Hermes 工作負載）

- **來源**：[foxinthebox.io – Best Models for Hermes Agents (May 2026)](https://foxinthebox.io/blog/best-models-for-hermes-agents/)、[bswen.com – Hermes Agent Model Configuration Guide](https://docs.bswen.com/blog/2026-04-21-hermes-agent-model-configuration/)

| 模型 | 優勢 | 劣勢 |
|------|------|------|
| Claude Sonnet 4.6 | 多步驟推理最佳、工具呼叫最穩定、MCP 原生整合 | 成本相對較高 |
| GPT-4.1 | 工具呼叫格式穩定、廣泛社群支援 | 長情境任務有截斷風險 |
| DeepSeek V4 | SWE-bench 81%、1M token 視窗、成本極低 | 工具呼叫偶有格式錯誤，需重試 |
| Gemini 3.1 Flash Lite | 速度最快、成本最低 | 複雜推理任務穩定性略遜 |
| Llama 4 Maverick（Ollama） | 本機部署、完全隱私 | 需自建推理環境，設定門檻較高 |

### 2-4 OpenRouter 整合：200+ 模型一鍵切換

- **來源**：[OpenRouter – Hermes Agent Integration](https://openrouter.ai/docs/cookbook/coding-agents/hermes-integration)
- **實戰心得**：Hermes-Agent 支援 OpenRouter 作為統一 API 閘道，可在不重寫配置的情況下快速測試各家模型；適合需要跨模型評估或成本優化的使用者。

---

## 3. 社群反應與討論亮點

- **「The Judgment Release」創下社群里程碑**：GitHub 發布頁面顯示 153 個表情反應，是近期 Hermes-Agent 版本中互動最高的一次；社群留言強調「零 P0/P1 積壓」是長期使用者最期待的目標。
  - 來源：[GitHub Releases – NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent/releases/tag/v2026.7.1)
- **awesome-hermes-agent 策展清單持續擴充**：由社群維護的技能、工具與整合資源清單（[0xNyk/awesome-hermes-agent](https://github.com/0xNyk/awesome-hermes-agent)）隨版本更新同步擴充，顯示生態系活躍。
- **hermes-optimization-guide 上線**：[OnlyTerp/hermes-optimization-guide](https://github.com/OnlyTerp/hermes-optimization-guide) 提供涵蓋設定、遷移、LightRAG、Telegram 整合、技能建立的全面指南，社群自發補充官方文件不足之處。
- **MindStudio 文章引發討論**：「AI Tools That Got Replaced in 2026」（MindStudio）分析 Hermes-Agent 與 Claude Code 如何取代 Cursor、OpenClaw 等工具，引發社群對各工具定位的廣泛討論。
  - 來源：[MindStudio Blog](https://www.mindstudio.ai/blog/ai-tools-replaced-2026-claude-code-hermes-agent-killed-cursor-openclaw)

---

## 4. 值得追蹤的後續議題

| 議題 | 說明 | 參考連結 |
|------|------|----------|
| Claude Agent SDK 訂閱路徑支援 | Issue #25267 提案讓 Pro/Max 訂閱者免 API key 使用 Claude，進展值得追蹤 | [GitHub Issue #25267](https://github.com/NousResearch/hermes-agent/issues/25267) |
| DeepSeek V4 工具呼叫修復 | 社群期待 DeepSeek 或 Hermes 端改善工具呼叫格式穩定性 | [foxinthebox.io](https://foxinthebox.io/blog/best-models-for-hermes-agents/) |
| Mixture-of-Agents 生產實戰案例 | MoA 剛成為一等公民，期待社群分享 named ensemble 在實際任務中的效能 | [Hermes Changelog](https://hermes-ai.net/changelog/) |
| `/goal` 完成合約（completion contracts）深度評測 | 新功能讓 agent 在多輪對話中鎖定目標，長任務場景的表現待社群驗證 | [GitHub Releases v2026.7.1](https://github.com/NousResearch/hermes-agent/releases/tag/v2026.7.1) |
| 下一個 patch 版本（v2026.7.x）發布節奏 | Judgment Release 後的修復版何時發布尚未公告，觀察 GitHub releases 動態 | [newreleases.io – hermes-agent](https://newreleases.io/project/github/NousResearch/hermes-agent/release/v2026.6.5) |

---

*報告生成日期：2026-07-07（UTC）。資訊蒐集工具：Web Search。若引用來源有內容更新，以原始來源為準。*
