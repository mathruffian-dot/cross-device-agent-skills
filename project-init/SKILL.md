---
name: project-init
description: 專案初始化技能（三層級自動偵測）。當使用者說「初始化專案」、「專案初始化」、「幫這個專案做初始化」、「開新專案」、「建立專案藍圖」、「幫我 init 專案」等要為當前資料夾建立專案基礎建設的請求時，請一定要使用此技能。本技能會依這台電腦的工具鏈自動建到最高可用層級：L1 本地（AGENTS.md + handoff.md）→ L2 GitHub（git init + 私有 repo）→ L3 Obsidian（專案詳細筆記）。
---

# 專案初始化技能（三層級自動偵測）

## 設計理念

一套技能、三個層級。**這台電腦裝了什麼工具，就自動建到哪個層級**——不用問使用者「你要第幾層級」。

三層資訊的定位與讀取頻率不同：

| 層級 | 平台 | 建立的東西 | 讀取時機 |
|------|------|-----------|---------|
| L1 本地 | 專案資料夾（建議放 GDrive） | `AGENTS.md`（專案藍圖）＋`handoff.md`（交接檔） | **每個 session 都讀** |
| L2 GitHub | 私有 repo | git 版本控制＋雲端備份 | 指定才讀 |
| L3 Obsidian | 第二大腦 vault | `專案工作流程.md`（詳細筆記） | 有需要才讀 |

> 為什麼藍圖叫 `AGENTS.md` 而不是 `CLAUDE.md`？因為 AGENTS.md 是跨 Agent 開放標準——Claude Code、Codex、Gemini CLI、OpenCode 都讀得懂。專案層的檔案刻意用開放格式，任何 Agent 接手都能無縫工作。

## 層級偵測（初始化看「這台電腦」有什麼）

依序檢查，決定本次能建到第幾層級：

1. **L1**：無條件可建
2. **L2**：跑 `gh auth status`，成功（已登入 GitHub CLI）→ 可建
3. **L3**：Obsidian MCP 工具（`mcp__obsidian__*`）可用 → 可建

檢查完先告訴使用者：「這台電腦可初始化至第 N 層級」，再開始執行。

## 初始化 SOP（依序執行）

### L1：本地藍圖（永遠執行）

1. **掃描資料夾現況**：列出既有檔案，若已有 `AGENTS.md` 或 `handoff.md` → 停下來問使用者是否要覆蓋
2. **詢問使用者**：專案名稱、一句話目標、關鍵時程（沒有就留白，不要硬編）
3. **建立 `AGENTS.md`**：用 `templates/AGENTS.template.md` 為底，填入實際內容；「資料夾結構」區塊由掃描結果自動生成
4. **建立 `handoff.md`**：用 `templates/handoff.template.md` 為底，「目前做到哪」填「專案初始化完成」，更新者填 Agent 名＋電腦名（PowerShell 用 `$env:COMPUTERNAME` 取得）
5. 若路徑含「雲端硬碟」或「My Drive」→ 提醒使用者確認 Google 雲端硬碟桌面版的同步圖示已打勾（檔案要真的躺在雲端，換電腦才拿得到）

### L2：GitHub（gh 已登入才做，否則跳過並註明）

6. **git 初始化**：
   ```bash
   git init
   git config user.email "<你的email>"
   git config user.name "<你的GitHub帳號>"
   git config windows.appendAtomically false   # GDrive 上跑 git 的必要設定，避免寫入錯誤
   ```
7. **建立 `.gitignore`**（GDrive 專用）：
   ```
   desktop.ini
   *.tmp
   ~$*
   .env
   *.key
   credentials.*
   ```
8. **初始 commit**：`git add .` → `git commit -m "初始化專案：<專案名稱>"`
9. **建立私有 repo**：問使用者偏好的英文 repo 名，然後
   ```bash
   gh repo create <你的GitHub帳號>/<repo-name> --private --source=. --push
   ```
10. **回填 `AGENTS.md`** 同步層級表的 GitHub 欄（repo 網址）

### L3：Obsidian（MCP 可用才做，否則跳過並註明）

11. 在 vault 根目錄建立與專案資料夾**同名**的資料夾
12. 建立 `<資料夾名>/專案工作流程.md`，內容包含：專案背景與詳細脈絡、決策紀錄（為什麼這樣做）、素材與相關筆記連結、🕳️ 踩坑筆記、🗓️ 最近更動紀錄表格（第一行寫今天的初始化）
13. **回填 `AGENTS.md`** 同步層級表的 Obsidian 欄（vault 內路徑）

### 回報

給使用者一個層級 checklist：

```
🏗️ 本專案初始化至第 N 層級
✅ L1 本地：AGENTS.md ＋ handoff.md
✅ L2 GitHub：<你的GitHub帳號>/<repo>（私有）
⚠️ L3 Obsidian：未建（這台電腦沒有 Obsidian MCP，之後可在有 Obsidian 的電腦說「補建第三層級」）
```

## 不該做的事

- ❌ 未經確認就覆蓋既有的 `AGENTS.md`／`handoff.md`
- ❌ 電腦沒 gh／Obsidian 時報錯中斷（正確行為：跳過該層級、在回報中註明原因）
- ❌ 把 `.env`、API key 之類敏感檔 commit 進 git
- ❌ 建 public repo（預設一律 private，使用者明說才轉公開）

## 注意事項

- 所有訊息與檔案內容使用**繁體中文**
- 之後的日常循環交給搭檔技能：開工（startup）讀、收工（shutdown）寫

