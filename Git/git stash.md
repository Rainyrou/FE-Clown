`git stash` & `git stash push -m "描述信息"`：暂存当前更改，将工作区和暂存区的更改保存到一个未完成更改的堆栈中，且重置工作区到最后一次提交的状态
`git stash list`：查看暂存的更改列表
`git stash pop`：应用最近暂存的更改，并从暂存的堆栈中移除它
`git stash apply`：应用最近暂存的更改，并在暂存的堆栈中保留它
`git stash clear`：移除所有暂存的更改
`git stash -u` & `git stash --include-untracked`：暂存未跟踪的文件
`git stash --all`：暂存忽略的文件







