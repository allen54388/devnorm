---
name: commit
description: Use when writing a git commit message, running git commit, amending, or reviewing commit history. Enforces Conventional Commits with a fixed type list, a per-project scope whitelist read from .claude/conventions.json, and Traditional Chinese subject lines that state impact rather than action.
---

# Commit 訊息規範

## 格式

```
<type>(<scope>): <主旨>

<body：可選>

<footer：可選>
```

- 主旨與 body 之間、body 與 footer 之間各空一行
- merge commit 例外：`merge: <分支名> <一句話摘要>`

## type（固定 11 個，不可自創）

| type | 用途 |
|---|---|
| `feat` | 新功能或行為變更（使用者看得到） |
| `fix` | 修 bug |
| `docs` | 只動文件（含 docs/、README、CLAUDE.md、註解） |
| `refactor` | 不改行為的程式重構 |
| `perf` | 效能改善 |
| `style` | 排版、空白、命名，不改邏輯 |
| `test` | 測試新增或修正 |
| `chore` | 雜務：腳本、設定、.gitignore、環境變數預設 |
| `build` | 建置系統、依賴升級（composer、npm、PHP 版本） |
| `ci` | CI 設定 |
| `revert` | 還原先前 commit，主旨寫被還原的 hash |

`ui`、`ux`、`debug`、`merge`（除 merge commit 本身）都不是 type。UI 變更依性質歸 `feat` 或 `fix`，scope 才寫 `ui`。

## scope（專案定義）

1. 先讀 `${CLAUDE_PROJECT_DIR}/.claude/conventions.json`，若有 `scopes` 陣列，scope 必須是其中之一。找不到合適的就用最接近的既有 scope，不要新增；真的需要新 scope 時先把它加進 conventions.json，同一筆 commit 一起提交。
2. 沒有這個檔案時：小寫 kebab-case、單一詞、對應模組或功能區塊。
3. 永遠禁止：中文 scope、逗號分隔多個 scope（`order,kb`）、同義詞漂移（`deploy`/`deployment`、`kb`/`knowledge`/`knowledge-base` 擇一）。
4. 跨多個模組時 scope 取主要受影響者；真的無法歸類才省略 scope。

## 主旨

- 語言依 conventions.json 的 `language`，預設繁體中文；技術名詞、指令、識別字保留原文
- 寫**影響或原因**，不寫動作：`fix(sync): 匯入指令補帶 reminder_count，避免上線後重寄整批提醒` 優於 `fix(sync): 修正 sync`
- 60 字以內，不加句號，不用「修正錯誤」「更新」「調整」這類沒有資訊量的詞
- 一筆 commit 一件事；主旨要列「三項」通常代表該拆

## body（可選，但有以下情況必寫）

- 改了兩個以上的地方
- 有「為什麼這樣做」需要說明
- 修的是先前某筆 commit 遺漏的部分：引用該 hash

寫法：列點（`-` 或 `1.`），每點一句，寫改了什麼與為什麼；不貼程式碼。

## footer（可選）

- `Refs #123` / `Closes #123`
- `BREAKING CHANGE: <說明>`，並在 type 後加 `!`：`feat(auth)!: ...`
- `Co-Authored-By:`

## 對照範例（取自真實專案歷史）

| ✅ 合規 | ❌ 不合規 | 問題 |
|---|---|---|
| `fix(sync): 匯入指令補帶 reminder_count，避免上線後重寄整批到期提醒` | `修正錯誤` | 無 type、無 scope、無資訊 |
| `feat(auth): 自助註冊拒絕公司內部網域信箱` | `Add login resend verification flow` | 語言不符、無 type |
| `build(composer): 升級 PHP 8.3 支援並更新依賴套件` | `換行或空白變更` | 無 type（應為 `style`） |
| `chore(schedule): 每月 1、15 日清除 storage/logs/schedule-*.log` | `ui: 調整 sidebar` | `ui` 不是 type |
| `docs(deploy): 同步 worker 存取規則並移除文件中的明文密碼` | `fix(order,kb): ...` | 多 scope |

## 執行

1. 讀 conventions.json 確認 scope 與語言
2. `git diff --staged` 看實際變更，主旨依變更寫，不依使用者口述
3. 多行訊息用 heredoc 傳給 `git commit -F -` 或 `-m` 多次，不要把 body 塞在同一個 `-m` 裡
4. 不改寫已推送的歷史 commit；規範只約束新 commit
