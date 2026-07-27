# 跨電腦專案管理三技能（cross-device-agent-skills）

> 三師爸「AI Agent 基本功」EP06 懶人包：讓你的專案在**任何電腦、任何 Agent** 之間無縫接續。
> 三個口令搞定一切：「**初始化專案**」「**開工**」「**收工**」。

## 這包裡有什麼

| 技能 | 口令 | 做什麼 |
|------|------|--------|
| `project-init` | 「初始化專案」 | 為專案建立藍圖（AGENTS.md）＋交接檔（handoff.md），有 GitHub 就順便建私有 repo，有 Obsidian 就建詳細筆記 |
| `startup` | 「開工」 | 讀藍圖＋交接檔，回報上次做到哪（含「上次在哪台電腦收工」）、git 狀態、建議下一步 |
| `shutdown` | 「收工」 | 更新藍圖進度、改寫交接檔、git commit + push、詳細紀錄寫進 Obsidian |

## 三個層級：工具裝到哪，技能就做到哪

這三個技能會**自動偵測**你的工具鏈，不用選版本：

| 層級 | 需要安裝 | 你會得到 |
|------|---------|---------|
| **L1 本地** | 什麼都不用（建議專案放 Google 雲端硬碟資料夾） | `AGENTS.md`＋`handoff.md`，跨電腦靠雲端硬碟同步 |
| **L2 +GitHub** | [GitHub CLI](https://cli.github.com/)（`gh auth login` 登入） | 版本控制＋雲端備份，貼網址就能分享專案 |
| **L3 +Obsidian** | Obsidian＋Obsidian MCP | 專案詳細筆記（第二大腦） |

三層資訊的讀取頻率不同——這是整套設計的核心：

- `AGENTS.md`＋`handoff.md`：**每個 session 都讀**（放交接必需的精簡資訊）
- GitHub：**指定才讀**（備份與歷史）
- Obsidian：**有需要才讀**（完整脈絡與細節）

## 安裝

```bash
git clone https://github.com/mathruffian-dot/cross-device-agent-skills.git
```

把 `project-init/`、`startup/`、`shutdown/` 三個資料夾複製到你的全域技能目錄：

- Claude Code：`~/.claude/skills/`

> **用 OpenCode 的話請改用 [cross-device-agent-skills-opencode](https://github.com/mathruffian-dot/cross-device-agent-skills-opencode)**——那一版的安裝路徑、權限設定與 shell 語法都已經對齊 OpenCode，SOP 內容相同。

然後跟你的 Agent 說一句：

> 「把剛裝的三個技能裡的 `<你的GitHub帳號>` 和 `<你的email>` 占位符，換成我的 GitHub 帳號和 email」

（只有 `project-init` 會用到，L1 使用者可跳過這步。）

## 典型的一天

```
早上（家裡電腦）
  你：「開工」
  Agent：📂 專案 xxx（第 2 層級）
         📘 上次做到哪：完成了報名表單（昨天 22:10，Claude Code @ 學校電腦）
         ⚠️ 上次在另一台電腦收工，GDrive 已同步完成
         ➡️ 建議下一步：1. 接 Firebase 寫入 …

  （工作中……）

晚上
  你：「收工」
  Agent：✅ L1：agents.md 進度已更新、handoff.md 已改寫
         ✅ L2：已 commit + push「新增報名表單 Firebase 寫入」
```

## 兩個核心檔案

- **`AGENTS.md`**（專案藍圖）：用 AGENTS.md 開放標準命名——Claude Code、Codex、Gemini CLI、OpenCode 都讀得懂，換 Agent 不用改檔案
- **`handoff.md`**（交接檔）：記錄「目前做到哪／下一步／注意事項／**最後更新者＋電腦名＋有沒有 push**」。不管是**換電腦**還是**換 Agent** 接手，都先讀這個檔

範本在 `project-init/templates/`，初始化技能會自動套用。

## 常見問題

**Q：專案資料夾一定要放 Google 雲端硬碟嗎？**
L1 的跨電腦同步就是靠雲端硬碟桌面版（要裝應用程式，不能只用網頁版）。不放 GDrive 也能用這三個技能，但跨電腦就得完全依賴 L2 的 git push／pull。

**Q：在 GDrive 資料夾跑 git 會出錯？**
初始化技能會自動設定 `git config windows.appendAtomically false`，這是 GDrive＋git 的已知坑。

**Q：兩台電腦可以同時開工同一個專案嗎？**
不建議——GDrive 會產生衝突副本。開工技能會顯示上次收工的電腦與時間，幫你避開這個情況。

**Q：我只有其中一台電腦裝 Obsidian，怎麼辦？**
沒關係，這正是自動偵測的用途：沒 Obsidian 的電腦收工時會在 handoff.md 註明「L3 未更新」，回到有 Obsidian 的電腦再補。

---

📺 完整教學：三師爸「AI Agent 基本功」EP06——如何跨電腦進行你的專案
