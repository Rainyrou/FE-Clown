1. 未跟踪 Untracked：文件存在于工作区中，但 Git 尚未开始跟踪，在之前的 commit 中未包含文件，也未被添加到暂存区
2. 已修改 Modified：文件已被 Git 跟踪，且自上次 commit 以来，工作区中的文件发生了更改，但未被添加到暂存区
3. 已暂存 Staged：文件已修改且被添加到暂存区，等待下次 commit，文件的更改已被标记，包含在下一次 `git commit` 操作中
4. 已提交 Committed：文件的更改已被 commit 到 Git 仓库的历史记录中，此时文件处于最新提交状态
5. 已删除 Deleted：文件已被删除，但删除操作仍未 commit
6. 已重命名 Renamed & 已移动 Moved：文件已被重命名或移动到另一位置，Git 能自动检测大多数重命名和移动操作，并在 `git status` 中相应显示


