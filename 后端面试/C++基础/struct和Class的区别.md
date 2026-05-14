---
tags: [cpp]
source: "[[后端岗位面试题]]"
---

# struct和Class的区别

- 通常，`struct`用于表示一组相关的数据，而`class`用于表示一个封装了数据和操作的对象，在实际使用中，可以根据具体的需求选择使用`struct`或`class`。如果只是用来组织一些数据，而不涉及复杂的封装和继承关系，`struct`可能更直观；如果需要进行封装、继承等面向对象编程的特性，可以选择使用`class`。
- struct结构体中的成员**默认是公有**的（public）。类中的成员**默认是私有**的（private）。
- **struct** 继承时默认使用公有继承。**class** 继承时默认使用私有继承。

```c++
// 使用 struct 定义
struct MyStruct {
    int x;  // 默认是 public
    void print() {
        cout << "Struct method" << endl;
    }
};

// 使用 class 定义
class MyClass {
public:  // 如果省略，默认是 private
    int y;
    void display() {
        cout << "Class method" << endl;
    }
};
```
