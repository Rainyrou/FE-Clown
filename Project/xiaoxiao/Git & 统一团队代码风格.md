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

然后就是三件套

```
git add .
git commit -m ""
git push origin main
```

###### 为什么我桌面的东西会和 git 联系起来？

如果在桌面 git clone 一个远程仓库，那么该目录及其子目录将被 git 跟踪。即在该目录中进行的任何更改（包括增删修改文件）都会被 Git 检测到

删除桌面上的 `.git` 目录（这是一个隐藏目录）。请确保备份重要数据，然后执行：

```bash
rm -rf ~/Desktop/.git
```

这会删除桌面的 git 仓库信息

##### 统一团队代码风格

1. 为项目瘦身，手动删除屎山和 dead code，反正我删除的无用代码挺多的

2. 文档

- 文件 & 函数命名、文件结构 & 组织方式、注释、git 提交规范

3. 配置 `.eslintrc.js` + `.eslintignore` + `.prettierrc.js` + `.prettierignore`

- 这又涉及到业务逻辑与技术难点的权衡了，出门右转(｡･∀･)ﾉﾞ嗨

4. Code Review

- 校校团队成员是协同开发的，一个人一个分支，在代码合并到主分支前由师兄们 Code Review，我也会给点评

5. 团队沟通：和新成员我有时会提一嘴

6. 定期审查并更新规范
