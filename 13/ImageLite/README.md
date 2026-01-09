## 构建

```bash
cmake -B build
cmake --build build
```

## 说明

* 使用 CMake 自动化构建任务，自动化代码生成，管理不同的构建配置
* 顶层 CMakeLists 文件代码会在构建时生成一个头文件，其中包含支持的图像格式信息
* 同时会输出当前的构建类型，比如是 debug 还是 release
