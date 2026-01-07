# ImageLite

一个展示 CMake 功能的简单图像处理库

## 特点

- 基础图像加载与保存（使用 stb_image）
- 核心操作：裁剪、调整尺寸、旋转
- 滤镜：灰度、亮度调节、模糊

## 构建

```bash
mkdir build
cd build
cmake ..
cmake --build .
```

## 说明

这个版本构建了内部库和外部依赖，下个版本将添加自定义参数命令来进行构建
