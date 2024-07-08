[TOC]

### std::size_t

`std::size_t` 可以存放下理论上可能存在的对象的最大大小，该对象可以是任何类型，包括数组

`std::size_t` 是 [`sizeof`](https://c-cpp.com/cpp/language/sizeof.html) 运算符还有 [`sizeof...`](https://c-cpp.com/cpp/language/sizeof....html) 运算符和 [`alignof`](https://c-cpp.com/cpp/language/alignof.html) 运算符 (C++11 起)所返回的**无符号整数类型**

使用其它类型来进行数组索引操作的程序可能会在某些情况下出错，例如在 64 位系统中使用 unsigned int 进行索引时，如果索引号超过 [UINT_MAX](https://c-cpp.com/cpp/types/climits.html) 或者依赖于 32 位取模运算的话，程序就会出错。

