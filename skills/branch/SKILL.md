---
name: branch
description: Use when creating a git branch, naming a branch, opening or merging a pull/merge request, pushing to remotes, tagging a release/deploy, finishing a task that touched git, or before reporting a task as complete. Defines GitHub Flow with a single long-lived trunk (main or master), feature/ and hotfix/ prefixes, merge-commit integration via gh, glab, or local --no-ff, mandatory branch cleanup, deploy tags, and multi-remote push verification.
---

# 分支流程（GitHub Flow）

## 完成的定義（最重要，先讀）

一個任務**只有在分支已合回主幹、遠端與本機分支都已刪除**時才算完成。commit 推上去不算，開了 PR 沒合併也不算。回報「完成」之前必須跑完「結束分支」那一節的每一步。

開新分支之前先檢查沒有殘留：

```bash
git branch --no-merged $(git rev-parse --abbrev-ref origin/HEAD | sed 's#origin/##')
```

有輸出就代表有未合併分支，先把它結束（合併或明確廢棄），再開新的。任何時候最多只有一條進行中的分支。

## 主幹

- 唯一長期分支，名稱依 repo 既有：`main` 或 `master`，**不改名**。偵測：

```bash
TRUNK=$(git rev-parse --abbrev-ref origin/HEAD 2>/dev/null | sed 's#origin/##'); TRUNK=${TRUNK:-main}
```

- 沒有 `develop`、沒有 `release/*`。主幹隨時可部署。

## 分支命名

- 功能 `feature/<kebab-case-主題>`，修復 `hotfix/<kebab-case-主題>`，從主幹開
- 全小寫英文，主題與 commit scope 對齊：`feature/order-export`、`hotfix/login-redirect-loop`
- 一條分支一件事，活不過幾天

## 開始

```bash
git switch $TRUNK && git pull --ff-only
git switch -c feature/<主題>
```

## 結束分支（三條路，依託管平台選一條，全部都要做到刪分支）

先判斷平台：`git remote get-url origin`。

### A. GitHub（有 `gh`）

```bash
git push -u origin feature/<主題>
gh pr create --fill
gh pr merge --merge --delete-branch --subject "merge: feature/<主題> <一句話摘要>"
git switch $TRUNK && git pull --ff-only
git branch -d feature/<主題> 2>/dev/null; git fetch --prune
```

### B. GitLab（有 `glab`）

```bash
git push -u origin feature/<主題>
glab mr create --fill --yes --remove-source-branch
glab mr merge --yes --remove-source-branch
git switch $TRUNK && git pull --ff-only
git branch -d feature/<主題> 2>/dev/null; git fetch --prune
```

### C. 沒有平台 CLI（或 CLI 認證失敗）：本機合併

```bash
git switch $TRUNK && git pull --ff-only
git merge --no-ff feature/<主題> -m "merge: feature/<主題> <一句話摘要>"
git push origin $TRUNK
git branch -d feature/<主題>
git push origin --delete feature/<主題> 2>/dev/null   # 若分支曾推上遠端
```

路徑 C 不需要任何工具就能完成，所以「工具不能用」永遠不是分支掛著的理由。

共同規則：一律 merge commit（不 squash、不 rebase），主旨 `merge: <分支名> <一句話摘要>`；合併後本機與遠端分支都刪；建議 repo 開啟 merge 後自動刪來源分支。

## Tag 規則

每次部署到正式環境，在主幹上被部署的那個 commit 打 tag。

- 格式 `vMAJOR.MINOR.PATCH`（SemVer），一律 annotated tag（`-a`）
- 版號依上一個 tag 以來的 commit type：有 `!` 或 `BREAKING CHANGE` → major；有 `feat` → minor；否則 patch
- 訊息第一行 `YYYY-MM-DD 部署：<一句話摘要>`，第二段列主要變更
- 只在主幹 commit 上打；hotfix 合併後同樣打 patch tag
- 預發布可用 `v1.4.0-rc.1`

```bash
git log $(git describe --tags --abbrev=0)..HEAD --format=%s
git tag -a v1.4.0 -m "2026-09-17 部署：訂單匯出與登入轉址修正" \
  -m "- feat(order): ...
- fix(sync): ..."
git push origin v1.4.0
```

禁止：移動或刪除已推送的 tag、在非主幹 commit 打正式版 tag、部署了不打 tag。

## 多 remote 推送

`origin` 可能設有多個 `pushurl`。**任一失敗就不算完成**：

```bash
git remote -v
git push <失敗的URL> $TRUNK
git ls-remote <URL> refs/heads/$TRUNK   # 逐一核對 hash 一致
```

## 禁止

- 直接 commit 到主幹（小修一行也開 `hotfix/`）
- `git push --force` 到主幹
- rebase 已推送的分支
- 同時有兩條以上未合併的分支
- 回報完成時分支還在
