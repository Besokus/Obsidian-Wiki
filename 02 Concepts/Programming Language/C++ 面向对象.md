---
type: concept
domain: programming-language
tags: [cpp, oop, virtual, polymorphism, inheritance]
created: 2026-05-14
updated: 2026-05-14
---

# C++ 面向对象

## 三大特性

### 封装（Encapsulation）

隐藏实现细节，提供公共接口。将数据（属性）和操作数据的方法（行为）捆绑成类，通过 `private`/`protected` 保护数据，只通过 `public` 接口受控操作。

### 继承（Inheritance）

允许派生类获取基类的属性和方法，建立 "is-a" 关系，提高代码可重用性。C++ 支持多继承。

### 多态（Polymorphism）

同一接口在不同对象上表现出不同的行为。

- **动态多态**：通过虚函数（`virtual`）和基类指针/引用，运行时决定调用哪个函数
- **静态多态**：通过函数重载和模板，编译时确定

---

## struct vs class

| | struct | class |
|---|---|---|
| 默认访问权限 | public | private |
| 默认继承方式 | public | private |

struct 通常用于组织数据，class 用于封装数据和操作。

---

## 虚函数与纯虚函数

### 虚函数

使用 `virtual` 声明，基类中**必须有实现**，派生类可重写也可不重写。

### 纯虚函数

声明后跟 `= 0`，基类中**不提供实现**，**强制**派生类实现。

含有纯虚函数的类称为**抽象基类**，不能被实例化。

---

## vtable 与 vptr

| 组件 | 归属 | 说明 |
|---|---|---|
| vtable（虚函数表） | 类 | 存储该类所有虚函数地址，类全局唯一 |
| vptr（虚表指针） | 每个对象 | 指向所属类的 vtable，占用 4/8 字节 |

动态绑定过程：
```text
Animal* a = new Dog();
a->speak();  // 通过 vptr 找到 Dog 类的 vtable，调用 Dog::speak()
```

---

## 析构函数为什么必须是虚函数

当通过基类指针删除派生类对象时，如果析构函数不是虚函数，只会调用基类析构函数，导致派生类资源泄漏。

**构造函数不能是虚函数**：构造时 vtable 还没完全建立，对象不完整。

---

## 多重继承与菱形继承

一个类可继承多个基类。菱形继承（Diamond Problem）会导致二义性。

**虚继承**（`virtual`）解决菱形继承问题，确保派生类只保留一份共享基类实例。

---

## 相关笔记

- [[C++ 内存管理]] — RAII、智能指针
- [[C++ 关键字与语法]] — static、const、引用
- [[C++ 现代特性]] — 移动语义、智能指针
