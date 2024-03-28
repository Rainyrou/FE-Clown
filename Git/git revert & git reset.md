`git revert` 用于创建一个新的 commit，该 commit 是对需要撤销的提交之逆操作，它不会删除任何历史记录，而是通过 add 新的 commit 来撤销某个 commit 的效果，它只影响指定的 commit，并不会改变原有的 commit 历史。适用于公共仓库的场景，当需要在公共分支上撤销某些更改，而又不想丢失项目历史中的任何信息时，使用它非常安全

```bash
git revert <commit-hash>
```

`git reset` 用于移动分支头指针回到指定的 commit，有选择地更改暂存区和工作区中的文件。根据参数，`reset` 可以撤销暂存区中的更改 `--mixed`（默认行为），仅移动分支头 `--soft`，或撤销暂存区和工作区中的更改 `--hard` 丢弃指定 commit 后的所有更改。适用于私有仓库或尚未推送的更改之场景，而在公共分支上使用 `git reset --hard` 需非常谨慎，因为它会丢弃指定 commit 后的所有更改并可能对团队造成影响

```bash
git reset --hard <commit-hash>
```

