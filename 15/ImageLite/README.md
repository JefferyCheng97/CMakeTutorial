## 构建

```bash
cmake -B build
cmake --build build
```

## 说明
 
* 使用 CMake 集成 CI/CD
* 由于 GitHub Action 规定，yaml 必须在顶层的 .github/workflows 中，所以这个子项目是无法激活 Action 的
* 由于 ImageLite 包含了多个子项目，所以要想使用 Action 必须将 15/ImageLite 作为单独项目
