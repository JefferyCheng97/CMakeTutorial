## 构建

```bash
cmake -B build
cmake --build build --target build_info
```

## 说明

1. 在顶层 cmake 文件中添加了版本号和项目描述
2. 创建了一个自定义命令，会在构建后的 build 文件夹下生成 build_info.txt 文件，文件中的内容是系统的一些构建信息
3. add_custom_target 中如果加入了 ALL，使得不用再写 --target build_info