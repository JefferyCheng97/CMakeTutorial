## 构建

```bash
cmake -B build
cmake --build build
```

## 说明

1. 添加构建前命令和构建后命令
2. 构建前命令是将 README.md 文件复制到 bin 目录下，并生成 temp 文件夹
3. 构建后命令是清理 temp 文件夹