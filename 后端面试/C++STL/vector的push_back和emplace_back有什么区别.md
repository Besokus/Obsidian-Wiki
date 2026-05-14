---
tags: [stl]
source: "[[后端岗位面试题]]"
---

# vector的push_back和emplace_back有什么区别？

std::vector的push_back和emplace_back都用于在向量的末尾添加元素，但它们添加元素的方式不同：

- `push_back`：创建一个元素的副本或移动该元素，然后将其添加到向量的末尾。
- `emplace_back`：在向量的末尾就地构造元素，避免了额外的复制或移动。

性能：emplace_back通常比push_back更高效，因为它省去了临时对象的创建和销毁的开销，特别是在构造和析构成本较高时。
