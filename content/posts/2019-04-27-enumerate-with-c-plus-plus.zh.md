+++
title = "用 C++ 来 enumerate()"
tags = ["c-plus-plus"]
categories = ["programming"]
draft = false
date = 2019-04-27
slug = "enumerate-with-c-plus-plus"
+++

不少编程语言都提供了在迭代容器的同时记录步数的方法，例如 Python 的 `enumerate()` ：

```python
for i, elem in enumerate(v):
    print(i, elem)
```

以及 Rust 里 `std::iter::Iterator` 特性下的 `enumerate()` ：

```rust
for (i, elem) in v.iter().enumerate() {
    println!("{}, {}", i, elem);
}
```

这里记录了如何在 C++17 或更新的标准里尽量简洁地实现类似功能的办法。

第一种方法是使用一个可变的 lambda ：

```c++
std::for_each(v.begin(), v.end(),
              [i = 0](auto elem) mutable {
                  std::cout << i << ", " << elem << std::endl;
                  ++i;
              });
```

这个方法使用于所有能够保证 lambda 有序执行的算法，但是我并不喜欢末尾很可能被混入其他逻辑的 `++i` 。

第二种方法是在 for 循环中使用结构化绑定：

```c++
for (auto [i, elem_it] = std::tuple{0, v.begin()}; elem_it != v.end();
     ++i, ++elem_it) {
    std::cout << i << ", " << *elem_it << std::endl;
}
```

为了不让编译器默认创建只允许同种内容的 `std::initializer_list` ，我们必须加上 `std::tuple` 。

第三种最朴实无华的办法是在循环的每一步计算指针距离：

```c++
for (auto elem_it = v.begin(); elem_it != v.end(); ++elem_it) {
    auto i = std::distance(v.begin(), elem_it);
    std::cout << i << ", " << *elem_it << std::endl;
}
```

由于这种方法需要我们在两个地方指定初始指针，我更喜欢之前提到的基于计数器的方法。

在 C++20 中，我们可以在基于范围的 for 循环中加入初始化语句：

```c++
for (auto i = 0; auto elem : v) {
    std::cout << i << ", " << elem << std::endl;
    i++;
}
```

新加入的 `<ranges>` 库则提供了一种更加吸引人的实现方法：

```c++
for (auto [i, elem] : v | std::view::transform(
         [i = 0](auto elem) mutable { return std::tuple{i++, elem}; })) {
    std::cout << i << ", " << elem << std::endl;
}
```

我最喜欢基于结构化绑定和 `<ranges>` 库的方法。当然如果要是有 `std::view::enumerate` 来一劳永逸地解决这个问题就最好不过了。
