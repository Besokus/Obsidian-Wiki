---
tags: [cpp]
source: "[[后端岗位面试题]]"
---

# C++中的extern关键字的作用

在 C++ 中，`extern` 关键字主要用于**声明全局变量或函数**，告知编译器这些变量或函数的定义位于其他文件中，从而实现跨文件共享。其核心作用包括：

```c++
// 文件A.cpp
int x = 10; // 定义全局变量x（分配内存）
// 文件B.cpp
extern int x; // 声明x，链接到A.cpp中的定义
void func() {
    x = 20; // 使用A.cpp中定义的x
}
```

#### **常见面试问题**

- Q1：`extern int x;` 和 `int x;` 的区别？

  - `extern int x;` 是声明，不分配内存，需依赖其他文件的定义。
  - `int x;` 是定义，分配内存（若多次定义会导致链接错误）。

- Q2：如何在 C++ 中调用 C 函数？

  ```c++
  // 方式1：直接声明
  extern "C" void c_function(int);
  // 方式2：包含C头文件
  extern "C" {
      #include <cstdio> // 例如调用printf
  }
  ```

- Q3：`extern` 与 `static` 的关系？

  - `extern` 使变量 / 函数具有**外部链接属性**（跨文件可见）。
  - `static` 使变量 / 函数具有**内部链接属性**（仅当前文件可见）。
