## 构建

```bash
cmake -B build
cmake --build build
```

## 说明

* 之前的 stb 库是外部的，每次使用或者更新都需要人为手动导入
* 现在可通过 FetchContent 来进行配置，让它在使用时自动去下载并导入