## 构建

```bash
cmake -B build
cmake --build build
```

## 说明

* 使用函数和宏进行重构，减少冗余代码
* 会发现 core 和 filters 文件夹下的 cmake配置文件存在重复的部分
* function 和 macro 存在很多相似的地方，在大型项目中不推荐使用宏，因为宏的变量会泄露到外部，这可能会导致错误或者意外行为，而函数的变量只是局部的
* 使用 macro 时，变量使用的语法和 function 也不太一样, ${}
