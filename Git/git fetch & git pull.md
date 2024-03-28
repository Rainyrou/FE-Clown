`git fetch` 从远程仓库拉取当前分支的最新更改，更新远程跟踪分支，通常是 `origin/main`，但不合并到本地分支。它是一种查看远程仓库变化的安全做法，但并不影响本地开发环境，可在 `fetch` 后，手动检查更新再决定是否合并这些更改到本地分支上

```bash
git fetch origin
git diff origin/main
git merge origin/main
```

`git pull` 是 `git fetch` 后跟 `git merge FETCH_HEAD` 的简写，用于从远程仓库拉取最新更改并自动合并到本地分支

```bash
git pull origin main
```