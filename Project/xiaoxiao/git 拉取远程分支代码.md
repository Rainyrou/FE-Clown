查看本地分支：

```git
git branch
```

**查看所有远程分支**：

```git
git branch -r
```

切换本地分支：

```git
git checkout -b gdx/gdx_feature
```

**查看工作目录、暂存区与当前分支最新提交之间的差异：**

```git
git status
```

拉取远程分支代码:

```git
git pull origin gdx/gdx_feature
```

###### 为什么我桌面的东西会和 git 联系起来？

如果在桌面 git clone 一个远程仓库，那么该目录及其子目录将被 git 跟踪。即在该目录中进行的任何更改（包括增删修改文件）都会被 Git 检测到

删除桌面上的 `.git` 目录（这是一个隐藏目录）。请确保备份重要数据，然后执行：

```bash
rm -rf ~/Desktop/.git
```

这会删除桌面的 git 仓库信息
