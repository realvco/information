# Hermes-Agent 每日情報 — 2026-06-15

> 資料蒐集範圍：過去 24 小時（2026-06-14 ～ 2026-06-15 UTC）
> 專案：[NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent)

---

## 1. 今日重點摘要

1. **v0.16.0「The Surface Release」（6/5）後續追蹤**：正式版發布後進入穩定期，社群集中在 Desktop App 的使用問題回報與 provider 設定除錯，GitHub 上 6/14 已開立數個新 issue（#46304 ～ #46337 範圍）。
2. **Hermes Desktop App 成為最大討論焦點**：此版 Electron App 橫跨 macOS/Linux/Windows，100 PRs + 159 commits 在一週內完成，功能包含：Cmd+K 命令面板、拖曳上傳檔案、剪貼簿圖片貼上、狀態列 Model Picker、多 profile 並行 session、完整繁體/簡體中文翻譯、支援透過 OAuth 或帳號密碼連接遠端 Hermes Gateway。
3. **DeepSeek 設定 Bug 確認（Desktop App）**：已知問題：Hermes Desktop 啟動時反覆要求使用者設定 AI Provider，且不允許跳過設定流程，尤其在使用 DeepSeek 作為 provider 時最常觸發。CLI 版本不受此問題影響。（參見 [fathah/hermes-desktop issue #121](https://github.com/fathah/hermes-desktop/issues/121)）
4. **新增模型**：v0.16 model picker 新增：`deepseek-v4-flash`、`gemini-3.5-flash`（支援 Gemini OAuth 及 API Key）、`MiniMax-M3`、`Qwen3.7-Plus`。Feature Request #14902 持續追蹤 `deepseek-v4-pro` 支援。
5. **CVE-2026-48710 已修補**：v0.16 修補了 Starlette 框架漏洞 CVE-2026-48710，建議所有用戶升級。
6. **v0.15.2 Kanban 多 Agent 平台成熟**：上一版的 Kanban 已具備：Orchestrator 自動任務分解、Swarm 拓撲、排程任務、每任務獨立 worktree、per-task model override。`run_agent.py` 從 16,083 行重構縮減至 3,821 行（-76%）。
7. **GitHub Stars 突破 18 萬**：自 2026 年 2 月 25 日上線後，不到四個月 GitHub Stars 超過 180,000，被 Dealroom 列為 2026 年成長最快的開源 Agent 框架。
8. **6/14 GitHub 新 Issue 群**：多位使用者在 #46304 ～ #46337 之間開立 issue，開立者包括 LIJaByXa、lo-phare、NeverLookBackAgain、Drake-Armor、zltnxyz、chrisatl320 等，具體內容待 maintainer 回應。
9. **管理後台（Admin Panel）正式亮相**：v0.16 同步推出瀏覽器版 Admin Panel，可視覺化管理 Hermes 實例、skill 庫、session 記錄與 provider 設定，降低進階設定門檻。

---

## 2. LLM 串接實戰回報

### DeepSeek V4 Flash — Desktop App Provider 識別失敗

- **問題**：Hermes Desktop v0.16 每次啟動都強制顯示 AI Provider 設定流程，即使用戶已完成設定，CLI 層設定正常，Desktop 層卻無法讀取現有設定。在以 DeepSeek 為 provider 時最為常見。
- **狀態**：已確認為已知 bug，追蹤中。
- **暫解**：每次啟動手動重填 API Key，或改用 CLI 版操作。
- **來源**：[Repeatedly forces provider setup with DeepSeek · Issue #121](https://github.com/fathah/hermes-desktop/issues/121)

### Claude Sonnet 4.6 / GPT-4.1 — 工具呼叫最穩定

- **回報**：在 production 使用中，Claude Sonnet 4.6 與 GPT-4.1 的工具呼叫格式最可靠，生成 malformed function call 的機率最低。DeepSeek V4 作為預算方案表現良好，但工具呼叫偶有格式偏差，需搭配 retry 策略。
- **推薦組合（2026 最佳）**：整體品質選 **Claude Sonnet 4.6**；預算部署選 **DeepSeek V4**；本地隱私部署選 **Llama 4 Maverick（via Ollama）**。
- **來源**：[Best Hermes Agent Model 2026 (Remote OpenClaw)](https://www.remoteopenclaw.com/blog/best-models-for-hermes-agent) | [Hermes Agent AI Guide (Petronella)](https://petronellatech.com/blog/hermes-agent-ai-guide/)

### 小 Context 視窗模型 — 系統 Prompt 超出限制

- **問題**：Hermes Agent 的系統 prompt + 活躍 skill + memory 檔案 + 對話歷史，整體 token 需求偏高。選擇 context window 較小的模型時，極易在開始對話前就超出限制，導致錯誤、重試、token 浪費。
- **建議**：初次設定時確認模型 context window ≥ 32K（複雜 skill 組合建議 ≥ 64K）。
- **來源**：[Hermes Agent AI Guide (Petronella)](https://petronellatech.com/blog/hermes-agent-ai-guide/)

### Gemini 3.5 Flash — 新增支援

- **更新**：v0.16 新增原生 `gemini-3.5-flash` 支援（同時支援 Gemini OAuth 與 API Key），可直接在 model picker 選取，無需手動設定 Custom endpoint。
- **來源**：[Hermes Agent v0.16 Reference (blakecrosley.com)](https://blakecrosley.com/guides/hermes)

---

## 3. 社群反應與討論亮點

- **Desktop App 熱議**：Medium 上 Ewan Mak 撰文「Hermes Agent Desktop App: Everything You Need to Know」獲廣泛傳播，聚焦 Hermes 從 CLI 工具走向 GUI 的策略轉變。主流評論認為此舉大幅降低非技術用戶的進入門檻。
- **Nous Research Discord 活躍**：官方 Discord 持續是 Hermes 社群的核心，用戶分享 skill 配置、provider 選擇、Desktop App 踩坑等經驗，官方人員也在其中回應 bug。
- **本地 LLM 路線受關注**：「Hermes Agent Desktop + 本地 LLM（Ollama）完全免費」的教學吸引大量關注，強調在不依賴付費 API 的前提下完整運行 Hermes，Llama 4 Maverick 是目前社群最推薦的本地首選。
- **Security Issue #40889**：有使用者針對 hermes-agent GitHub repository 發起 Security Posture Review（issue #40889），目前仍在討論中。
- **G2 軟體獎項**：Nous Research 出現在 G2 2026 Best Software Awards 列表，顯示 Hermes-Agent 在企業評測平台的能見度提升。

---

## 4. 值得追蹤的後續議題

1. **Desktop App DeepSeek Provider Bug 修復**：issue #121（fathah/hermes-desktop）待官方 patch，預計近期版本處理。
2. **deepseek-v4-pro 支援（Feature #14902）**：社群有需求，待 NousResearch 評估時程。
3. **6/14 新 issue 群（#46304 ～ #46337）**：多個使用者回報集中出現，需觀察是否為某版本共同 regression。
4. **Security Review #40889**：repository 安全姿態評估進展，涉及供應鏈與 dependency 掃描結果。
5. **Kanban 多 Agent 平台穩定性**：v0.15.2 大幅重構後的 Kanban 在 production 中的穩定性回報，特別是 Swarm 拓撲 + per-task model override 的組合場景。
6. **Admin Panel 安全與權限管理**：全新瀏覽器版 Admin Panel 的細粒度權限控制是否符合企業需求，待社群實測反饋。

---

*來源整理*
- [Hermes Agent v0.16.0 Release Notes (GitHub)](https://github.com/NousResearch/hermes-agent/releases/tag/v2026.6.5)
- [Hermes Agent v0.16 Reference (blakecrosley.com)](https://blakecrosley.com/guides/hermes)
- [Hermes Agent Desktop App — Medium](https://medium.com/@tentenco/hermes-agent-desktop-app-everything-you-need-to-know-about-nous-researchs-self-improving-ai-agent-3cb59bd31e5f)
- [Best Hermes Agent Model 2026 (Remote OpenClaw)](https://www.remoteopenclaw.com/blog/best-models-for-hermes-agent)
- [Hermes Agent AI Guide (Petronella)](https://petronellatech.com/blog/hermes-agent-ai-guide/)
- [Repeatedly forces provider setup with DeepSeek · Issue #121](https://github.com/fathah/hermes-desktop/issues/121)
- [deepseek-v4-pro support request · Issue #14902](https://github.com/NousResearch/hermes-agent/issues/14902)
- [NousResearch/hermes-agent Issues](https://github.com/NousResearch/hermes-agent/issues)
- [Hermes Agent Free With Local LLMs](https://www.kunalganglani.com/blog/hermes-agent-desktop-free-local-llm)
