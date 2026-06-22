# OpenClaw 每日情報 — 2026-06-22

## 今日重點摘要

1. **v2026.6.8 穩定版存在多項嚴重回退**：OpenClaw 於 6 月 16 日發布的 v2026.6.8 穩定版被社群追蹤站 ClawStat.us 標記為「建議等待下一版」，已確認跨越 Telegram、Discord、Memory、Cron、Plugin 及 Provider 多個子系統的 high/critical 等級回退。

2. **Beta v2026.6.10-beta.1 正在修補上述問題**：最新 beta 版本修復了 pending subagent 完成通知遺失、chat history transcript 變空、media index 對齊偏移、dormant follow-up drain 未重啟，以及 compaction model alias 解析不一致等問題。安全邊界也全面收緊（transcript、sandbox、MCP、browser、channel 均改為 fail-closed）。

3. **社群新 Issue #95586（6 月 21 日）**：GitHub 用戶 jwong-art 昨日提交新 issue，ClawSweeper 已識別出可能的實作方向；細節尚待官方回應，可持續追蹤。

4. **Telegram 與 iMessage 頻道改善**：v2026.6.8 帶入 Telegram 結構化文字渲染（表格、清單、可展開引用區塊），iMessage 則強化 always-on inbound recovery、durable echo markers 及 outbound transport 在重啟後的持久性。

5. **April 2026 以來支援 6 家 Provider 執行期切換**：透過 provider manifest 可在不重建 workflow 的情況下於執行期動態切換底層 LLM，涵蓋 Anthropic、OpenAI Codex、Gemini、Ollama 等主流供應商。

6. **GPT-5.4-pro 正式支援**：2026.4.14 版本新增對 GPT-5.4-pro 的前向相容支援，並提供 Codex 計費與用量能見度。

7. **GitHub 活躍度高**：儲存庫目前擁有 379k+ Stars、3,300+ 個 open issues，持續有 PR 合併進 compaction、session delivery、channel media、gateway cron、Matrix 整合及 skills 基礎設施等面向。

8. **付費模型訂閱使用政策收緊**：Anthropic 持續調整封鎖規則，防止以 Max/Pro 訂閱 token 在 OpenClaw 等第三方軟體中使用；官方明確表示訂閱 token 不得用於第三方應用，建議改用 API Key。

---

## LLM 串接實戰回報

### Claude（Anthropic）

- **推薦配置**：大多數 Agent 工作流建議使用 Claude Sonnet（工具呼叫穩定、長 context 一致、MCP 原生支援），高頻低成本任務使用 Haiku，最難推理場景使用 Opus。
- **踩坑：Auth 失效**：HTTP 429（Rate Limit）佔 OpenClaw 所有錯誤回報的約 60%，Context Window Exceeded 佔 25%，Auth/Token Expired 佔 15%。多組 auth profile（OAuth + API Token 並存）時，目前無法在單一介面查看所有 profile 的用量與 rate limit 狀態（Issue [#24865](https://github.com/openclaw/openclaw/issues/24865) 尚未解決）。
- **費用異常**：大型工具輸出（如 gateway config.schema 回傳 396KB+ JSON）會被永久寫入 session 檔案，造成每次請求都拖帶完整 blob，35 則訊息後 session 可成長至 2.9MB，API 費用呈指數爆炸；無監控的全日自主 agent 操作費用可達 $1,000–$5,000。
- **無內建費用上限**：OpenClaw 預設無 token budget、無每日消費上限、無 retry 熔斷器（Issue [#58826](https://github.com/openclaw/openclaw/issues/58826) 正在討論加入）。

來源：
- [OpenClaw Token Usage & Cost Control Guide](https://www.getopenclaw.ai/help/token-usage-cost-management)
- [Issue #24865 — Rate limit view across auth profiles](https://github.com/openclaw/openclaw/issues/24865)
- [Issue #58826 — Built-in token budget request limit](https://github.com/openclaw/openclaw/issues/58826)
- [How to Run OpenClaw 24/7 Without Breaking the Bank](https://perelweb.be/blog/openclaw-token-management-smart-model-manager/)

### GPT-5.5（OpenAI）

- Agent 自主執行能力領先，Terminal-Bench 2.0 評測名列前茅，但上下文限制 128K tokens/call，長任務需分段規劃。
- 搭配 Codex 供應商使用可取得原生驗證；2026.4.14 起 Codex 計費在 OpenClaw 介面可見。

來源：
- [Best LLM for OpenClaw: Gemini 3.1 Pro vs GPT-5.5 vs Claude Opus 4.7 (2026)](https://dev.to/matthewrevell/best-llm-for-openclaw-gemini-31-pro-vs-gpt-55-vs-claude-opus-47-2026-3na4)
- [OpenClaw April 2026: 6 Model Providers You Can Now Swap at Runtime](https://www.mindstudio.ai/blog/openclaw-april-2026-model-agnostic-provider-manifest)

### Gemini 3.1 Pro（Google）

- 多模態與大 context 工作負載表現突出，最適合 OpenClaw 部署中的大 context 程式碼審查場景。
- 免費 dev tier 可供初期測試，成本效益佳。
- 透過 `openclaw onboard` 設定精靈輸入 Google AI Studio Key 即可啟用。

來源：
- [Best Model for OpenClaw: Claude vs GPT vs Gemini (2026)](https://cowork.ink/blog/best-model-for-openclaw/)
- [Setup OpenClaw with Claude & Gemini](https://vertu.com/ai-tools/the-ultimate-guide-setting-up-openclaw-with-claude-code-and-gemini-3-pro)

---

## 社群反應與討論亮點

- **「等待下一版」成主流建議**：[ClawStat.us](https://clawstat.us/) 對 v2026.6.8 標示紅色警示，社群普遍建議生產環境暫緩升級，待 beta 修復完成後再跟進。
- **費用失控討論持續延燒**：多位用戶分享 session 膨脹導致費用暴增的實際案例，呼籲官方加入 built-in token budget，對應 issue 在社群獲得大量 upvote。
- **Anthropic 訂閱封鎖爭議**：部分用戶對 Anthropic 限制 Max/Pro token 在第三方工具使用感到不滿，討論如何合規轉換為純 API Key 流程。
- **Agents-radar 生態系摘要**（[#544](https://github.com/duanyytop/agents-radar/issues/544)）：社群維護的 agent 追蹤儲存庫定期整理 OpenClaw 動態，可作為補充資訊來源。

---

## 值得追蹤的後續議題

1. **v2026.6.10-beta.1 → 正式穩定版時間表**：何時解除 v2026.6.8 的紅色警示，可訂閱 [ClawStat.us](https://clawstat.us/) 追蹤。
2. **Issue #58826 進度**：內建 token budget 與 request limit 控制何時落地，對長時間自主 agent 部署至關重要。
3. **Issue #24865 進度**：統一 rate limit 能見度介面，對多 provider 部署用戶是剛需。
4. **Issue #95586 後續**：6 月 21 日新提交的 issue，ClawSweeper 已有初步方向，值得持續關注官方回應。
5. **Anthropic 訂閱政策動向**：是否有官方途徑讓 OpenClaw 與 Claude Max/Pro 合規互動，或是否會維持封鎖。
6. **Matrix 頻道整合完整度**：目前 PR 仍在合併中，尚未確認穩定狀態。

---

*資料來源：GitHub openclaw/openclaw、releasebot.io、clawstat.us、dev.to、getopenclaw.ai、skywork.ai 等，搜尋時間 2026-06-22 UTC*
