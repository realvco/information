# Hermes-Agent 每日情報 — 2026-06-01

> 資料截止時間：2026-06-01 UTC｜搜尋範圍：過去 24 小時
> 專案：[NousResearch/hermes-agent](https://github.com/nousresearch/hermes-agent)

---

## 1. 今日重點摘要

1. **v0.15.1 / v0.15.2 hotfix（5 月 29 日）剛落地**：主版本 v0.15.0「Velocity Release」發布後僅一天，NousResearch 即釋出同日 hotfix v0.15.1，再補 v0.15.2，修復多項因大規模重構引發的回歸問題，目前社群正在驗收這兩個 patch。
2. **v0.15.0 核心重構：run_agent.py 縮減 76%**：16,083 行的單體 `run_agent.py` 拆解為 14 個模組（共 3,821 行），啟動速度大幅提升；`session_search` 效能達 4,500 倍加速且現已免費使用。
3. **Kanban 升級為真正的多 agent 平台**：歷經 104 個 PR，Kanban 新增 orchestrator 自動任務拆解（auto-decomposition）、swarm topology、scheduled tasks、worktree-per-task 隔離、以及 per-task model override，讓多 agent 並行工作流程正式可用。
4. **v0.15.1 緊急修復：dashboard 無限 reload 迴圈**：v0.15.0 在 loopback 模式（Docker、hosted Hermes、全新安裝）下，dashboard 的 `/api/auth/me` 探針回傳 401 時會被誤判為 token 輪換，導致無限重載；v0.15.1 已修正此邏輯。
5. **v0.15.2 後續修復項目**：Kanban worker SIGTERM 處理、`/model` picker 統一化、`/yolo` session bypass 修復、完整 19,932 筆 skills.sh 目錄、`.md` 媒體傳遞還原、gateway probe-stepdown 安全性、Docker arm64 PR build cache 修正。
6. **computer-use 擴展至非 Anthropic provider**：v0.14.0 引入的新 `cua-driver` 後端讓 computer-use（agent 控制滑鼠鍵盤操作 GUI 應用）不再綁定 Anthropic SDK，任何支援 vision 的模型均可驅動桌面。
7. **HuggingFace skills index 成為預設**：社群在 `huggingface.co/skills` 發布的技能現可直接從 Skills Hub 安裝，無需額外設定，大幅降低社群技能流通門檻。
8. **專案規模**：v0.15.0 版本包含 1,302 commits、747 merged PRs、321 位社群貢獻者，MIT 授權；專案在 GitHub 累積約 172k stars。

---

## 2. LLM 串接實戰回報

### Claude — context window 因服務方而異
- **現象**：`claude-opus-4-6` 在 Anthropic direct 支援 1M context，但透過 GitHub Copilot 服務時上限降為 128K。官方文件建議在 CLI 啟動輸出中確認 detected context length。
- **注意**：使用 Kanban per-task model override 時，若部分 worker 用 Copilot route 的 Claude 而其他 worker 用 direct API，context limit 不一致可能影響長文任務的完成品質。
- **來源**：[Hermes Agent FAQ & Troubleshooting](https://hermes-agent.nousresearch.com/docs/reference/faq)

### 多 provider 串接建議（Claude vs DeepSeek）
- **社群討論**：remoteopenclaw.com 有一篇 2026 年比較文章，討論在 Hermes Agent 中選用 Claude 或 DeepSeek 的最佳實踐；Claude 在需要推理深度的驗證步驟（verifier）表現突出，DeepSeek 適合量大、低成本的 routine worker task。
- **Kanban swarm 建議組合**：用廉價模型處理一般 worker task，Claude 負責 verifier/synthesizer 步驟，可平衡費用與品質。
- **來源**：[Best Hermes Agent Model 2026: Claude vs DeepSeek](https://www.remoteopenclaw.com/blog/best-models-for-hermes-agent)

### Gemini — 免費層 rate limit 限制
- **現象**：Gemini 免費層在 Hermes Agent 中使用時，Gemini 2.0 Flash 上限為 15 RPM / 1M TPM，其他模型更低（10–30 RPM），多 agent 並行任務容易觸發 rate limit。
- **解法**：超過限制時等待後重試；若需要高並行度，建議升級至付費方案或搭配 OpenRouter 做 load balancing。
- **來源**：[Gemini API Free Tier 2026 Limits](https://pecollective.com/tools/gemini-free-tier-guide/)

### 費用參考（2026 年 6 月）
| 模型 | 輸入（/1M tokens） | 輸出（/1M tokens） |
|------|------------------|--------------------|
| Gemini 2.5 Pro（≤200K context） | $1.25 | $10 |
| Claude Sonnet 4.6 | $3 | $15 |

- **來源**：[Hermes Agent v0.15 Reference](https://blakecrosley.com/guides/hermes)

### computer-use 與非 Anthropic provider
- v0.14.0 起，`cua-driver` 後端讓任何 vision-capable 模型（如 Gemini、GPT-4o、Grok-Vision）均可驅動 computer-use，不再限於 Anthropic；但社群尚未有大量跨 provider 的 computer-use 穩定性比較資料，建議保守測試。
- **來源**：[Hermes Agent Releases v0.14.0](https://github.com/NousResearch/hermes-agent/releases/tag/v2026.5.16)

---

## 3. 社群反應與討論亮點

- **Windows 使用者的安裝痛點**：社群成員建立了 `hermes-for-win` 一鍵安裝工具，反映 Hermes 在 Windows 上的初始安裝門檻仍高，吸引不少 Windows 使用者討論與感謝。
- **Obsidian 長期記憶架構高熱度**：Reddit 上有一張說明如何以 Obsidian 作為 Hermes Agent 長期記憶骨幹的架構圖，獲得 794 個讚，顯示社群對個人知識管理（PKM）與 AI agent 整合有強烈興趣。
- **v0.15 「Velocity Release」媒體曝光**：Digg、TECHSY 等科技媒體均報導 v0.15 重構成果，重點強調 76% 程式碼縮減與 cold-start 效能提升，外部能見度明顯提高。
- **v0.15.1 hotfix 速度受到肯定**：社群留言普遍肯定 NousResearch 在 dashboard 無限 reload 問題上的快速應對（發布後不到 24 小時即修復），認為維護節奏積極。

---

## 4. 值得追蹤的後續議題

1. **v0.15.x 穩定性驗收**：v0.15.1 / v0.15.2 是否已完整修復 hotfix 清單中的所有項目？社群是否還在回報 v0.15.0 引入的新 bug？
2. **Kanban swarm 大規模使用體驗**：104 個 PR 重構後的 multi-agent 板塊尚屬新功能，實際大規模任務（如長時間 coding agent 集群）的穩定性與死鎖處理有待更多實戰回報。
3. **per-task model override 的 context limit 不一致問題**：在 Kanban swarm 中混用不同 provider 的同名模型時，context limit 差異可能造成任務失敗，期待官方提供明確的 provider 配置建議。
4. **computer-use 跨 provider 穩定性報告**：目前缺乏 Gemini / GPT-4o 等非 Anthropic provider 驅動 computer-use 的公開穩定性比較，等待社群貢獻測試資料。
5. **v0.16 路線圖**：v0.15 主打效能與多 agent，下一個主要版本的方向（安全性？更多 provider？技能生態）尚未公開。

---

*資料來源彙整*
- [NousResearch/hermes-agent GitHub Releases](https://github.com/NousResearch/hermes-agent/releases)
- [Hermes Agent v0.15.0 Release Notes](https://github.com/NousResearch/hermes-agent/releases/tag/v2026.5.28)
- [Hermes Agent v0.15.1 Release Notes](https://github.com/NousResearch/hermes-agent/releases/tag/v2026.5.29)
- [Hermes Agent v0.15.2 Release Notes](https://github.com/NousResearch/hermes-agent/releases/tag/v2026.5.29.2)
- [RELEASE_v0.15.0.md](https://github.com/NousResearch/hermes-agent/blob/main/RELEASE_v0.15.0.md)
- [RELEASE_v0.15.1.md](https://github.com/NousResearch/hermes-agent/blob/main/RELEASE_v0.15.1.md)
- [Hermes Agent v0.15 Reference (blakecrosley.com)](https://blakecrosley.com/guides/hermes)
- [TECHSY — Hermes Agent v0.15: What Actually Matters](https://techsy.io/en/blog/hermes-agent-v0-15)
- [Kanban 文件 (nousresearch.com)](https://hermes-agent.nousresearch.com/docs/user-guide/features/kanban)
- [Hermes Agent FAQ & Troubleshooting](https://hermes-agent.nousresearch.com/docs/reference/faq)
- [Best Hermes Agent Model 2026: Claude vs DeepSeek](https://www.remoteopenclaw.com/blog/best-models-for-hermes-agent)
- [Digg — Nous Research Releases Hermes Agent v0.15.0](https://digg.com/ai/907ztpex)
