1. 若 commit 尚未被其他人拉取或基于其工作，此种情况大胆修改信息，因为不会影响到其他人的工作

- 若需修改的是最后一次 commit：`git commit --amend` 直接修改 commit 信息，接着 `git push --force` 将更改强制推送到远程仓库

```bash
git commit --amend -m "新的提交信息"
git push --force origin <branch-name>
```

- 若需修改的不是最后一次 commit：`git rebase -i` 进行交互式变基，选择需修改的 commit 前的一个 commit ID

```bash
git rebase -i 'commitID^'
```

用 Vim 将 `pick` 修改为 `reword`，保存并关闭，再根据提示修改 commit 信息，接着 `git push --force` 将更改强制推送到远程仓库

2. 若 commit 已被其他人拉取或基于其工作，那么使用上述方式进行强制推送可能导致团队成员的 commit 丢失或产生冲突

- 创建一个新的 commit 来修正错误：若只是 commit 信息不当，直接在新的 commit 里修正之前的提交信息。若为代码错误，修复错误后正常提交，并在提交信息中进行说明

```bash
git add .
git commit -m "修正之前提交的错误，原提交信息：‘错误的提交信息’"
git push origin <branch-name>
```

- 通知团队有关更改，并提供任何必要的指导，帮助他们重新基于最新 commit


