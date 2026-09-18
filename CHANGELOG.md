# Changelog

## 0.4.1 - 2026-09-18

- 去識別化：範例、文件、scope 範本中所有來源專案的公司、產品、客戶、系統名稱改為中性代號


## 0.4.0 - 2026-09-17

- `branch` skill：新增「完成的定義」（分支合回主幹並刪除才算完成、最多一條未合併分支）、主幹自動偵測 main/master、結束分支三條路徑（gh / glab / 本機 --no-ff），修正 GitLab 專案分支只開不合的問題
## 0.3.0 - 2026-09-17

- README 安裝說明擴充到 Codex CLI、GitHub Copilot、Google Antigravity（同一份 SKILL.md，只差放置目錄）

## 0.2.2 - 2026-09-17

- 真正修正 `branch` skill 內 tag 範例的續行符號（0.2.1 的修正未生效）

## 0.2.1 - 2026-09-17

- 修正 `branch` skill 內 tag 範例指令的換行

## 0.2.0 - 2026-09-17

- `branch` skill 由 Trunk-Based 改為 GitHub Flow：變更一律走 PR，merge commit 合併，部署打 tag：SemVer、annotated、版號由 commit type 推導、訊息格式與禁止事項
## 0.1.0 - 2026-09-17

- 初版：`commit`、`docs`、`branch` 三個 skill
- `examples/conventions.json` 專案覆寫範本（取自一個 Laravel 專案半年、347 筆 commit 的實際用法）
