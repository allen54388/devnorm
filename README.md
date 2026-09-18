# devnorm

Claude Code plugin：三條開發規範，裝了之後 AI 在 commit、建分支、寫文件時自動遵守。不用 Claude Code 的人也可以直接讀本文當團隊規範。

規範來自一個 Laravel 專案半年、347 筆 commit 的實際運作經驗，協助設計。

## 安裝

三個 skill 都是 [Agent Skills](https://agentskills.io) 開放標準的 `SKILL.md`，Claude Code、Codex、Copilot、Antigravity 都能直接讀，差別只在放哪個目錄。

### Claude Code

```
/plugin marketplace add allen54388/devnorm
/plugin install devnorm@devnorm
```

裝一次全部專案生效；更新用 `/plugin update devnorm@devnorm`。

### Codex CLI

```bash
git clone https://github.com/allen54388/devnorm ~/devnorm
cp -r ~/devnorm/skills/* ~/.codex/skills/        # 個人層級，所有專案生效
# 或放專案：mkdir -p .codex/skills && cp -r ~/devnorm/skills/* .codex/skills/
```

Codex 依 description 自動載入；若要每次都生效，在專案 `AGENTS.md` 加一行「commit、docs、分支依 devnorm skill」。

### GitHub Copilot（CLI / VS Code / Visual Studio）

```bash
git clone https://github.com/allen54388/devnorm ~/devnorm
mkdir -p .agents/skills && cp -r ~/devnorm/skills/* .agents/skills/   # 專案層級
```

Copilot 也讀 `.github/skills/` 與 `.claude/skills/`；用 `.agents/skills/` 可與 Antigravity 共用同一份。

### Google Antigravity

```bash
git clone https://github.com/allen54388/devnorm ~/devnorm
mkdir -p .agents/skills && cp -r ~/devnorm/skills/* .agents/skills/   # 與 Copilot 同一目錄
```

要強制每次生效可再放一份到 `.agents/rules/`。

### 專案覆寫

不論哪個工具，複製 [`examples/conventions.json`](examples/conventions.json) 到專案的 `.claude/conventions.json`，改成自己的 scope 清單；skill 會讀這個檔。

### 更新

Claude Code 用 `/plugin update`；其他工具重新 `git pull ~/devnorm` 後再複製一次。

## 三個 skill

| skill | 何時自動觸發 | 內容 |
|---|---|---|
| [`commit`](skills/commit/SKILL.md) | 寫 commit 訊息、`git commit` | 固定 11 個 type、專案 scope 白名單、繁中主旨寫影響非動作 |
| [`docs`](skills/docs/SKILL.md) | 在 `docs/` 新增、搬移、命名文件 | 8 類目錄、日期前綴規則、新文件放哪決策表、索引同步 |
| [`branch`](skills/branch/SKILL.md) | 開分支、結束任務、開 PR/MR、合併、推送、部署 | GitHub Flow、主幹 main/master、完成的定義、gh / glab / 本機 --no-ff 三條結束路徑、合併後刪分支、部署打 tag、多 remote 補推 |

手動呼叫：`/devnorm:commit`、`/devnorm:docs`、`/devnorm:branch`。

---

## 規範摘要

### Commit

```
<type>(<scope>): <主旨：繁中，寫影響或原因，≤60 字，不加句號>

<body：可選，列點寫改了什麼與為什麼>

<footer：可選，Refs / BREAKING CHANGE / Co-Authored-By>
```

type 只能是：`feat` `fix` `docs` `refactor` `perf` `style` `test` `chore` `build` `ci` `revert`。
scope 從專案 `.claude/conventions.json` 讀；沒有就 kebab-case 單詞。禁中文 scope、禁 `a,b` 多 scope。
merge commit：`merge: feature/xxx 一句話摘要`。

| ✅ | ❌ |
|---|---|
| `fix(sync): 匯入指令補帶 reminder_count，避免上線後重寄整批提醒` | `修正錯誤` |
| `feat(auth): 自助註冊拒絕內部網域` | `Add login resend verification flow` |
| `build(composer): 升級 PHP 8.3 支援並更新依賴套件` | `ui: 調整 sidebar` |

### docs/

| 目錄 | 放什麼 | 日期前綴 |
|---|---|---|
| `manuals/` | 終端使用者手冊 | 否 |
| `guides/` | 開發／維運指南 | 否 |
| `specs/` | 開發規格書 | `YYYY-MM-DD-` |
| `plans/` | 實作計畫 | `YYYY-MM-DD-` |
| `requests/` | 需求方原始文件 | `YYYY-MM-DD-` |
| `decisions/` | 設計決策紀錄 | `YYYY-MM-DD-` |
| `reports/` | 一次性分析報告 | `YYYY-MM-DD-` |
| `archive/` | 封存 | 保留原名 |

`docs/README.md` 是唯一索引，動文件必同步。superpowers / oh-my-claudecode 使用者：規格與計畫一律寫到 `docs/specs/`、`docs/plans/`，不用套件預設路徑。

### 分支

- 主幹依 repo 既有為 `main` 或 `master`（不改名）；`feature/*`、`hotfix/*` 從主幹開
- **完成的定義**：分支合回主幹且本機與遠端分支都已刪除才算完成；同時最多一條未合併分支
- 結束分支三條路：GitHub 用 `gh`、GitLab 用 `glab`、沒有 CLI 就本機 `git merge --no-ff`；一律 merge commit，主旨 `merge: <分支> <摘要>`
- 每次部署在 `main` 打 annotated tag `vX.Y.Z`（SemVer，依 commit type 決定升哪一位），訊息 `YYYY-MM-DD 部署：摘要`；已推送的 tag 不移不刪
- `origin` 有多個 pushurl 時，PR 合併後本機 pull 再 push，任一失敗要單獨補推並用 `git ls-remote` 核對

## 授權

MIT
