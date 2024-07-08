# 1. 介绍

集合的交并补，可以使用bit位进行压缩表示，提高性能，减少存储空间。 Java 中的 `HashSet`，C++ STL 中的 `unordered_set`

用二进制从低到高表示一个数是否存在在集合中：*f*(*S*)=*∑2*i*(*i*∈*S)

# 2. 基础知识

 ## 2.1 集合运算

- 集合与集合的运算，(**如何通过一些运算得到结果**)

「对称差」指仅在其中一个集合的元素

| 术语         | 集合               | 位运算                         | 举例                    | 举例                                  |
| ------------ | ------------------ | ------------------------------ | ----------------------- | ------------------------------------- |
| 对称差       | *A* Δ *B*          | *a*⊕*b*                        | {0,2,3}Δ{0,1,2} = {1,3} | 1101⊕ 0111= 1010                      |
| 求差         | *A*∖*B*            | *a*&∼*b*                       | {0,2,3}∖ {1,2} = {0,3}  | 1101 & 1001 = 1001                    |
| 求差（子集） | *A*∖*B*（*B*⊆*A*） | *a*⊕*b*                        | {0,2,3}Δ{0,2} = {3}     | 1101⊕0101 = 1000                      |
| 判断包含于   | *A*⊆*B*            | *a*&*b*=*a*<br/>𝑏*a* ∣ *b*=*b* |                         | 0101 & 1101=0101<br/>0101 ∣ 1101=1101 |

- 集合与元素的运算，(**如何通过一些运算得到结果**)

| 术语                     | 集合                    | 位运算                       | 举例 | 举例       |
| ------------------------ | ----------------------- | ---------------------------- | ---- | ---------- |
| 求全集                   | *U*={0,1,2,⋯*n*−1}      | (1 << *n*)−1                 |      | (1 << 4)−1 |
| 求补集                   | ∁*U**S*=*U*∖*S*         | ∼*s* 或者<br/>((1 << 𝑛)−1)⊕𝑠 |      |            |
| 元素属于集合             | *i*∈*S*                 | (𝑠 >> 𝑖) & 1=1               |      |            |
| 元素不属于集合           | *i*∈/*S*                | (*s* >> *i*) & 1=0           |      |            |
| 添加元素                 | 𝑆∪{𝑖}*S*∪{*i*}          | 𝑠 ∣ (1 << 𝑖)                 |      |            |
| 删除元素                 | *S*∖{*i*}               | *s*&∼(1 << *i*)              |      |            |
| 删除元素（一定在集合中） | *S*∖{*i*}（𝑖∈𝑆*i*∈*S*） | *s*⊕(1 << *i*)               |      |            |
| **删除最小元素**         |                         | *s*&(*s*−1)                  |      |            |

最小元素的位置是1，右边都是0，所以s-1，最小元素位置变0，右边都是1，其余不会变

- lowbit，找到最低的那一位

     s = 101100
    ~s = 010011
(~s)+1 = 010100 // 根据补码的定义，这就是 **-s  =>  s 的最低 1 左侧取反，右侧不变**
s & -s = 000100 // lowbit

## 2.2 遍历集合

设元素范围从 0 到 n*−1，挨个判断每个元素是否在集合 𝑠* 中

```cpp
for (int i = 0; i < n; i++) {
    if ((s >> i) & 1) { // i 在 s 中
        // 处理 i 的逻辑
    }
}
```

## 2.3 枚举集合

- 设元素范围从 0 到 𝑛−1，从空集 ∅ 枚举到全集𝑈：

```cpp
for (int s = 0; s < (1 << n); s++)
```

- **枚举**所有**非空子集**

```cpp
for (int sub = 0;  sub; sub = (sub-1) & s)
```

核心思想，刨除所有集合中的0，相当于 111 ->110 -> 101 -> 100

在不断-1的过程中，最小的1右边变1，自己变0

- 枚举所有子集

```cpp
int sub = s;
do {
    // 处理 sub 的逻辑
    sub = (sub - 1) & s;
} while (sub != s);

```

0-1 = -1， 与s与就是s

-  Gosper's Hack

是一种生成 𝑛 元集合所有 𝑘 元子集的算法，求下一个1的数目相同的数

```cpp
// lb : lowbit
//x < (1 << k) - 1
while( x < ((1 << n) - 1))

// 左半 x + lb

// 右半 （x ^ (x+lb)）// lb >> 2
    
// x = left | right
```



## 2.4 异或



# 3. 语言特性

- 获得数字的二进制，以及二进制中1的个数

| 术语                 | Python        | Java                | C++                   | Go                |
| -------------------- | ------------- | ------------------- | --------------------- | ----------------- |
| 集合大小（元素个数） | s.bit_count() | Integer.bitcount(s) | __builtin_popcount(s) | bits.OnesCount(s) |

```python
import math

b = 5
b.bit_count()

bin(b).count('1') '0b100'
```

- 二进制长度

| 术语                                   | Python           | Java                                 | C++                   | Go            |
| -------------------------------------- | ---------------- | ------------------------------------ | --------------------- | ------------- |
| 二进制长度（减一得到集合中的最大元素） | `s.bit_length()` | `32-Integer.numberOfLeadingZeros(s)` | `32-__builtin_clz(s)` | `bits.Len(s)` |

- 集合集合中的最小元素**后面有多少0**

| 术语             | Python                  | Java                               | C++                | Go                    |
| ---------------- | ----------------------- | ---------------------------------- | ------------------ | --------------------- |
| 集合中的最小元素 | `(s&-s).bit_length()-1` | `Integer.numberOfTrailingZeros(s)` | `__builtin_ctz(s)` | bits.TrailingZeros(s) |

请特别注意 
s=0 的情况。对于 C++ 来说，__builtin_clz(0) 和 __builtin_ctz(0) 是未定义行为。其它语言请查阅 API 文档。

此外，对于 C++ 的 long long，需要使用 __builtin_popcountll 等，即函数名后缀添加 ll（两个小写字母 L）。

`__builtin_clz`是GCC和[Clang](https://so.csdn.net/so/search?q=Clang&spm=1001.2101.3001.7020)编译器提供的一个内置函数，用于计算一个整数的二进制表示中，从最高位开始连续的0的个数

[__builtin_ctz**](https://www.baidu.com/s?wd=__builtin_ctz&rsv_idx=2&tn=baiduhome_pg&usm=1&ie=utf-8&rsv_pq=a144d7a50003a0af&oq=__builtin_ctz&rsv_t=75dbPDk87jiI6kOYaGNQuUlwtSoMQNh0Z7Ue%2B76ai3cJ816bUxF3grwQEle2e%2BbOwMpl&sa=re_dqa_zy&icon=1)() 是[GCC**](https://www.baidu.com/s?wd=GCC&rsv_idx=2&tn=baiduhome_pg&usm=1&ie=utf-8&rsv_pq=a144d7a50003a0af&oq=__builtin_ctz&rsv_t=75dbPDk87jiI6kOYaGNQuUlwtSoMQNh0Z7Ue%2B76ai3cJ816bUxF3grwQEle2e%2BbOwMpl&sa=re_dqa_zy&icon=1)和[Clang**](https://www.baidu.com/s?wd=Clang&rsv_idx=2&tn=baiduhome_pg&usm=1&ie=utf-8&rsv_pq=a144d7a50003a0af&oq=__builtin_ctz&rsv_t=75dbPDk87jiI6kOYaGNQuUlwtSoMQNh0Z7Ue%2B76ai3cJ816bUxF3grwQEle2e%2BbOwMpl&sa=re_dqa_zy&icon=1)编译器提供的一系列内置函数，用于计算一个整数的二进制表示中从最低位（即二进制表示的最后一位）开始连续的0的个数

