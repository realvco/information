# OpenClaw 每日情報｜2026-07-02

> 資料蒐集區間：UTC 2026-07-01 00:00 ～ 2026-07-02 00:00

---

## 1. 今日重點摘要

1. **v2026.6.11 於 7/1 正式釋出**：這是近期最重要的版本，聚焦「可靠性整修」，修補跨多個聊天平台的訊息傳遞與重連問題，同時強化頻道控制與 Operator 工作流程。
2. **Slack relay mode 上線**：Slack 現支援 relay 模式，搭配原生 Mattermost `/oc_queue` 指令，讓頻道操作更易自動化調校。
3. **每個 DM 可獨立覆寫模型（per-DM model override）**：使用者可對特定私訊頻道指定不同的 LLM，而不影響全域設定。
4. **`openclaw agent --message-file` 與 RAFT CLI wake bridge**：新增檔案驅動的訊息輸入路徑與遠端喚醒橋接，豐富 Operator 自動化場景。
5. **多平台傳送可靠性大修**：Telegram、WhatsApp、Matrix、Google Chat、iMessage、Feishu、Mattermost、WebChat、Control UI、Terminal UI 均納入修復範圍，串流多訊息回覆重複出現的問題亦同步解決。
6. **新增 GLM-5.2 與 Kimi K2.7 Code**：兩個模型加入 OpenCode Go 目錄，使用者可從 OpenClaw 直接選用。
7. **安全強化**：受信任套件路徑現在會拒絕仿冒相鄰路徑（例如 `/artifactory/openclaw` 不再同時接受 `/artifactory/openclaw-malicious`）。
8. **Telegram 富格式回覆格式修正**：段落、項目符號、狀態行不再被壓縮成連續單一段落，無需重新設定即自動生效。
9. **Claude Sonnet 4.6 在 PinchBench 領先群雄**：在 OpenClaw 內部 PinchBench 評測中以 86.9% 成功率排名第一，MCPAgentBench 得分 71.6。

---

## 2. LLM 串接實戰回報

### 2-1. 各家 LLM 在 OpenClaw 的定位（2026 年社群共識）

| LLM | 適用場景 | 已知限制 |
|---|---|---|
| **Claude Sonnet 4.6** | 通用 Agent 工作、Tool use、MCP 任務 | 高峰期 API 速率偶有誤報（見 Issue #32828） |
| **Claude Haiku** | 大量分類、路由、簡單轉換 | 推理深度不足複雜任務 |
| **Claude Opus** | 最高難度推理任務 | 成本較高，需謹慎使用 |
| **Gemini 3.1 Pro** | 多模態、長情境程式碼審查 | 免費層有每日 Token 上限 |
| **GPT-5.5 (Codex)** | 自主 Agent、Terminal-Bench 領域 | 超過 128K context 表現下滑 |
| **DeepSeek / Ollama / LM Studio** | 本地部署、降低成本 | 需自行管理算力與更新 |

OpenClaw 自 2026 年 4 月起提供 **Provider Manifest**，可在執行中的工作流程不停機切換 LLM。

**來源：**
- [Best LLM for OpenClaw: Gemini 3.1 Pro vs GPT-5.5 vs Claude Opus 4.7 (2026)](https://dev.to/matthewrevell/best-llm-for-openclaw-gemini-31-pro-vs-gpt-55-vs-claude-opus-47-2026-3na4)
- [Model providers · OpenClaw](https://docs.openclaw.ai/concepts/model-providers)
- [OpenClaw April 2026: 6 Model Providers You Can Now Swap at Runtime Without Rebuilding](https://www.mindstudio.ai/blog/openclaw-april-2026-model-agnostic-provider-manifest)

### 2-2. 已知踩坑：API Rate Limit 誤報

- **Issue #32828**：使用者反映在 API 實際正常的情況下，OpenClaw 仍顯示「API rate limit reached」，影響多個模型。
- **Issue #24865**：設定多組 Auth Profile（如 `anthropic:default` OAuth + `anthropic:key2` API Token）時，沒有統一的使用量／速率限制檢視介面，難以診斷是哪個 Profile 觸限。

**來源：**
- [Issue #32828：False 'API rate limit reached'](https://github.com/openclaw/openclaw/issues/32828)
- [Issue #24865：Show rate limit / usage state for ALL configured auth profiles](https://github.com/openclaw/openclaw/issues/24865)

### 2-3. Claude Sonnet 4.6 工具呼叫評測結果

Claude Sonnet 4.6 在 OpenClaw 環境下的 PinchBench 工具呼叫成功率為 **86.9%**，MCPAgentBench 得分 **71.6**，目前為 OpenClaw 社群測試下最高分的模型組合。

**來源：**
- [Best Model for OpenClaw: Claude vs GPT vs Gemini (2026)](https://cowork.ink/blog/best-model-for-openclaw/)

---

## 3. 社群反應與討論亮點

- **「Update 2026.4.1 徹底壞掉 exec」**（Issue #59006）：升級後 exec 指令全面失效，影響 git push、npm build 等自動化流程。起因是安全更新停用 sandbox，部分操作系統因此被鎖定。目前多名使用者表示已透過調整信任清單解決。
- **「最新版讓工作效率大幅下降」**（Issue #54539）：有使用者認為 2026.6.x 的部分行為改動導致生產力下滑，社群正在討論是否需要回退機制或更細緻的設定選項。
- **五個 Zero-day 安全警報（5~6 月）**：多家資安媒體報導 OpenClaw 出現五個零時差漏洞，官方已於近期版本中持續修補。`joylarkin/openclaw-security-news` 倉庫持續追蹤相關公告。
- **GitHub Discussions 生態摘要**：`openclaw-community/openclaw-hub` 的 Discussions 版面近期圍繞 MCP 擴充方式、自訂 Provider 路由、長情境壓縮風險等主題展開討論。

**來源：**
- [Issue #59006：Update 2026.4.1 broke exec completely](https://github.com/openclaw/openclaw/issues/59006)
- [Issue #54539：The latest version is so unproductive](https://github.com/openclaw/openclaw/issues/54539)
- [openclaw-community/openclaw-hub Discussions](https://github.com/openclaw-community/openclaw-hub/discussions)
- [openclaw-security-news](https://github.com/joylarkin/openclaw-security-news)

---

## 4. 值得追蹤的後續議題

| 議題 | 追蹤理由 |
|---|---|
| **Issue #32828 Rate Limit 誤報修復進度** | 影響多家 LLM provider，尚未合入 main |
| **Issue #24865 多 Auth Profile 統一檢視** | 維護者已標記 enhancement，尚未排期 |
| **安全沙箱策略調整後續** | 2026.4.1 的 sandbox 破壞性變更引發廣泛討論，需關注後續文件更新 |
| **Provider Manifest 在 RAFT 環境的相容性** | RAFT CLI wake bridge 為新功能，實戰踩坑報告尚少 |
| **GLM-5.2 / Kimi K2.7 Code 社群實測** | 剛加入 OpenCode Go 目錄，暫無公開 benchmark 對比 |
| **長情境壓縮（compaction）風險** | 維護者標記為最明顯的 Operator 風險，建議關注官方最佳實踐更新 |

---

*報告產生時間：2026-07-02 UTC｜資料來源：GitHub、releasebot.io、dev.to、MindStudio、cowork.ink 等*
