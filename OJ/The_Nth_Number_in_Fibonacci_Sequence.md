# The Nth Number in Fibonacci Sequence

- [The Nth Number in Fibonacci Sequence](#the-nth-number-in-fibonacci-sequence)
  - [题目](#题目)
    - [描述](#描述)
    - [IO格式](#io格式)
    - [样例](#样例)
  - [解答](#解答)
    - [思路](#思路)
    - [代码](#代码)
    - [时空消耗](#时空消耗)

## 题目

### 描述

打印 `Fib(n) mod 9973`.

### IO格式

输入:

```
N
n1
...
nN
```

输出：

```
Fib(n1)
...
Fib(nN)
```

### 样例

输入:

```
5
1
2
8
10
15
```

输出:

```
1
1
21
55
610
```

## 解答

### 思路

这里援引 [斐波那契数列 - OI Wiki](https://oi-wiki.org/math/combinatorics/fibonacci/), 其中提到了几种用于快速计算斐波那契数列某项的方法; 其中, 这里采用了皮萨诺周期法.

利用以下的 Python 代码我们可以计算得到模 9973 的皮萨诺周期:

```Python
from functools import cache
mod: int = 9973
@cache
def fib(n: int) -> int:
    if n == 0 or n == 1:
        return n
    return (fib(n-1)+fib(n-2)) % mod
start: list[int, int] = [0, 1]
cur: list[int, int] = [0, 0]
for i in range(2, 20000):
    if i % 2:
        cur[1] = fib(i)
        if cur == start:
            print('period: ', i-1)
    else:
        cur[0] = fib(i)
```

### 代码

```C++
#include <cstdio>
#include <map>
using namespace std;

#define prf printf
#define scf scanf
using ull = unsigned long long;

constexpr ull intMax = 2147483647;
constexpr int mod = 9973;      // 模数
constexpr int period = 19948;  // Pisano周期

map<ull, int> mp{{1, 1}};

int fib(ull n) {
    if (n > period) return fib(n % period);
    if (n == 0) return 0;
    if (int p = mp[n]) return p;
    auto&& ret = (fib(n - 1) + fib(n - 2)) % mod;
    mp[n] = ret;
    return ret;
}

int main() {
    int n;
    scf("%d", &n);
    for (auto i = 0; i < n; i++) {
        ull temp;
        scf("%llu", &temp);
        prf("%d\n", fib(temp));
    }
    return 0;
}
```

### 时空消耗

```
1	Accepted	4 ms	2012 KB
```