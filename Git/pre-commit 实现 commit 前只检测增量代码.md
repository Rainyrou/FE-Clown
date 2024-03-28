1. Git 钩子位于 `.git/hooks/` 目录下，`pre-commit` 钩子在执行 `git commit` 时执行，但在提交消息编辑器打开前

2. 将以下脚本保存为 `.git/hooks/pre-commit`，确保脚本具有执行权限

```bash
#!/bin/sh

FILES=$(git diff --cached --name-only --diff-filter=d)

for FILE in $FILES; do
  if [[ "$FILE" =~ \.js$ ]]; then
    eslint "$FILE"
  fi

  if [ $? -ne 0 ]; then
    echo "ESLint检查失败"
    exit 1
  fi
done

exit 0
```

`git diff --cached --name-only --diff-filter=d` 获取所有将被 commit 的文件，但不包括被删除的文件，接着循环遍历它们，使用条件判断文件类型来运行相应的检查工具

