##### git rebase

`git rebase` 用于修改 commit 历史，将多个临时 commit 合并为一或多个具有清晰描述的 commit 或将 feature 分支上的更改重新应用于 master 或 main 等主分支的最新状态上，以解决分支冲突和保持 commit 历史的线性。具体来说，先取出一系列 commit，复制它们，再在另一个地方重新放置

```bash
git rebase <base>
```

`<base>` 是一个分支名、commit ID 或任何用于标识 commit 的引用

###### 基本原理

若你有如下的分支结构：

```
A---B---C main
         \
          D---E feature
```

当你在 `feature` 分支上运行 `git rebase main` 命令时，Git 会做如下事情：

1. 找到两个分支 --- 在本例中是 `feature` 和 `main` 的最近共同祖先，这里是 commit `C`
2. 提取 `feature` 分支上相对于该共同祖先的更改，在本例中是 commit `D` 和 `E`
3. 将 `feature` 分支的基础更改为 `main` 分支的最新提交，在本例中是 commit `C`
4. 依次将提取的更改应用于新基础之上

此后分支结构变为：

```
A---B---C main
         \
          D'---E' feature
```

这里的 `D'` 和 `E'` 表示 `D` 和 `E` 的更改已重新应用于 `C` 之上的新 commit

在 `rebase` 过程中，若遇到不能自动合并的更改，Git 暂停 `rebase` 操作，让我们手动解决冲突。解决冲突后使用 `git add` 标记解决冲突的文件，再使用 `git rebase --continue` 命令继续 `rebase` 过程。若想放弃 `rebase` 操作并返回到操作前的状态，可使用 `git rebase --abort` 命令

##### git merge

`git merge` 用于在保留分支历史的情况下合并两个分支的 commit，并自动创建一个新 merge commit，该 commit 有两个父节点，分别指向被合并的两个分支的最新 commit，合并保留 commit 历史的分支结构即包含所有的分支和合并点

###### 区别

1. 历史结构：

   - `merge` 合并保留 commit 历史的分支结构即包含所有的分支和合并点
   - `rebase` 修改并简化 commit 历史，创建线性 commit 历史，好像所有更改是按顺序提交的

2. 冲突解决：

   - `merge` 可能需要解决合并冲突一次
   - `rebase` 可能需要解决合并冲突多次，每个被重新应用的 commit 都可能引起冲突

3. 应用场景：

   - `merge` 适用于公共分支或需要保留分支历史的场景
   - `rebase` 适合在准备将分支合并入主分支前整理个人私有分支的提交历史的场景，将多个临时 commit 合并为一或多个具有清晰描述的 commit 或将 feature 分支上的更改重新应用于 master 或 main 等主分支的最新状态上
