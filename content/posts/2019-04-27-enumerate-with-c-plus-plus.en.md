+++
title = "enumerate() with C++"
tags = ["c-plus-plus"]
categories = ["programming"]
draft = false
date = 2019-04-27
slug = "enumerate-with-c-plus-plus"
+++

Quite a few programming languages provide ways to iterating through a container while keeping count of the number of steps taken, such as `enumerate` in Python:

```python
for i, elem in enumerate(v):
    print(i, elem)
```

and `enumerate` under `std::iter::Iterator` trait in Rust:

```rust
for (i, elem) in v.iter().enumerate() {
    println!("{}, {}", i, elem);
}
```

This is just a quick note about how to do similar things in C++17 and later without declaring extra variables out of the for loop's scope.

The first way is to use a mutable lambda:

```c++
std::for_each(v.begin(), v.end(),
              [i = 0](auto elem) mutable {
                  std::cout << i << ", " << elem << std::endl;
                  ++i;
              });
```

This could be used with all the algorithms that guarantees in-order application of the lambda, but I don't like the dangling `i++` that could get mixed up with other logic.

The second way utilizes structured binding in for loops:

```c++
for (auto [i, elem_it] = std::tuple{0, v.begin()}; elem_it != v.end();
     ++i, ++elem_it) {
    std::cout << i << ", " << *elem_it << std::endl;
}
```

We have to throw in `std::tuple` as otherwise compiler would try to create a `std::initializer_list`, which does not allow heterogeneous contents.

The third least fancy method is to just calculate the distance every time:

```c++
for (auto elem_it = v.begin(); elem_it != v.end(); ++elem_it) {
    auto i = std::distance(v.begin(), elem_it);
    std::cout << i << ", " << *elem_it << std::endl;
}
```

Since we have to copy paste the starting point twice, I like other counter based approaches better.

In C++20, we have the ability to add an init-statement in ranged-based for loops, so we can write something like

```c++
for (auto i = 0; auto elem : v) {
    std::cout << i << ", " << elem << std::endl;
    i++;
}
```

Meh, not that impressive. The new `<ranges>` library provides a more appealing way to achieve this:

```c++
for (auto [i, elem] : v | std::view::transform(
         [i = 0](auto elem) mutable { return std::tuple{i++, elem}; })) {
    std::cout << i << ", " << elem << std::endl;
}
```

I like the structured binding method and the `<ranges>` based method the most. It would be even better though if we can get a `std::view::enumerate` to solve this problem once and for all.
