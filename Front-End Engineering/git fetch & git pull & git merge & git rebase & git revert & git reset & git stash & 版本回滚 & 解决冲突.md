`git fetch` 用于从远程仓库拉取当前分支的最新更改，更新远程跟踪分支但不合并至本地分支

```bash
git fetch origin
git diff origin/main
git merge origin/main
```

`git pull` 用于从远程仓库拉取当前分支的最新更改并自动合并到本地分支

`git merge` 用于在保留分支 commit 历史的情况下合并两个分支的 commit 并创建新的 merge commit，其有两个父节点分别指向被合并的两个分支之最新 commit

`git rebase` 用于修改 commit 历史以解决分支冲突并保持 commit 历史的线性，将当前分支的 commit 置于目标分支的最新 commit 之上，若不能自动合并 commit，Git 暂停 `rebase` 操作，我们手动解决冲突而后通过 `git add` 标记解决冲突文件，`git rebase --continue` 继续执行 `rebase`，可通过 `git rebase --abort` 放弃 `rebase` 操作并返回先前状态

`git revert` 用于创建一个新的 commit，其为对需撤销的 commit 之逆操作，不删除任何 commit 历史，而通过创建 revert commit 来撤销先前某些 commit 的影响

`git reset` 用于移动当前分支指针回某个指定的 commit，`--mixed` -> 撤销暂存区的内容，`--soft` -> 仅移动分支头，`--hard` -> 撤销暂存区和工作区的内容，丢弃指定 commit 后的所有更改

git push 后发现 commit 写错：

```bash
git commit --amend -m "新的提交信息"
git push -f
```

`git stash`：暂存当前更改，将暂存区和工作区的更改保存于未完成更改的堆栈中，重置工作区为最后一次 commit 的状态

版本回滚：

1. `git log`：查看 commit 历史，找出引入问题的 commit
2. `git bisect` 二分查找问题 commit
3. `git revert` & `git reset`

解决冲突：

合并分支时，若不同分支对同一文件某部分做不同更改，Git 无法决定谁对谁错，其标记文件为冲突状态，停止合并

1. 通过 `git status` 查看冲突文件
2. 打开冲突文件，查找冲突标记，手动解决冲突

```
<<<<<<< HEAD
// 当前分支的版本
=======
// 要合并的分支的版本
>>>>>>> feature
```

3. `git add` + `git commit`
4. `git log` 查看 commit 历史
