# A + B Problem

- [A + B Problem](#a--b-problem)
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

输入两个正整数 `a` 和 `b`, 求 `a + b` 的值.

_提示:_ 注意数据范围.

### IO格式

输入:

```
a b
```

输出：

```
a + b
```

### 样例

输入:

```
1 1
```

输出:

```
2
```

## 解答

### 思路

这道题本身是大整数加法, 只需要用 `int` 或 `char` 链表十进制表示整数即可.

设置十进制链表, 以 `head` 为高位, `end` 为低位; 输入时从高到低构造, 计算时从低到高进位.

这里我采用的是十进制存储, 理论上为了内存消耗最小化, 可以采用 `unsigned int` 的 2147483647 进制 (`unsigned int` 范围的一半, 避免加法溢出) 链表, 可自行完成.
### 代码

```C++
#include <iostream>
#include <string>
using namespace std;

constexpr int chr2int = '0';

struct Big {
    struct Int {
        int val;
        Int* prev;
        Int* next;

        Int() : val(0), prev(nullptr), next(nullptr){};
        ~Int(){};
    };

    Int* head;
    Int* end;

    Big() : head(new Int), end(head){};
    Big(string&);
    ~Big(){};
    Big operator+(Big&);
    friend ostream& operator<<(ostream&, Big&);
};

Big Big::operator+(Big& ot) {
    Big& th = *this;
    Big ret;
    auto* cur1 = th.end;
    auto* cur2 = ot.end;
    auto* cur3 = ret.end;
    int reg = 0;
    while (cur1 && cur2) {
        auto _i = cur1->val + cur2->val + reg;
        reg = 0;
        if (_i >= 10) {
            _i -= 10;
            reg = 1;
        }
        cur3->val = _i;
        cur1 = cur1->prev;
        cur2 = cur2->prev;
        cur3->prev = new Big::Int;
        cur3->prev->next = cur3;
        cur3 = cur3->prev;
    }
    if ((!cur1) && (!cur2)) {
        auto* temp = cur3->next ? cur3->next : cur3;
        if (reg) {
            if (temp->prev)
                temp->prev->val = reg;
            else {
                temp->prev = new Big::Int;
                temp->prev->next = temp;
            }
            ret.head = temp->prev;
        } else
            ret.head = temp;
    } else if (!cur1) {
        auto* regTemp = cur2;
        while (regTemp != ot.head) {
            regTemp->val += reg;
            reg = 0;
            if (regTemp->val >= 10) {
                regTemp->val -= 10;
                reg = 1;
            } else
                break;
            regTemp = regTemp->prev;
        }
        if (regTemp == ot.head) {
            if (reg) {
                ot.head->val += reg;
                if (ot.head->val >= 10) {
                    ot.head->val -= 10;
                    ot.head->prev = new Big::Int;
                    ot.head->prev->next = ot.head;
                    ot.head->prev->val = 1;
                    ot.head = ot.head->prev;
                }
            }
        }
        auto* temp = cur3->next ? cur3->next : cur3;
        temp->prev = cur2;
        cur2->next = temp;
        ret.head = ot.head;
    } else if (!cur2) {
        auto* regTemp = cur1;
        while (regTemp != th.head) {
            regTemp->val += reg;
            reg = 0;
            if (regTemp->val >= 10) {
                regTemp->val -= 10;
                reg = 1;
            } else
                break;
            regTemp = regTemp->prev;
        }
        if (regTemp == th.head) {
            if (reg) {
                th.head->val += reg;
                if (th.head->val >= 10) {
                    th.head->val -= 10;
                    th.head->prev = new Big::Int;
                    th.head->prev->next = th.head;
                    th.head->prev->val = 1;
                    th.head = th.head->prev;
                }
            }
        }
        auto* temp = cur3->next ? cur3->next : cur3;
        temp->prev = cur1;
        cur1->next = temp;
        ret.head = th.head;
    }
    return ret;
}

Big::Big(string& s) : head(new Int), end(head) {
    auto& big = *this;
    auto* cur = big.head;
    for (auto c : s) {
        if (isdigit(c)) {
            cur->val = c - chr2int;
            cur->next = new Big::Int;
            cur->next->prev = cur;
            cur = cur->next;
            big.end = cur;
        } else
            goto L_END;
    }
L_END:
    if (big.end->prev) big.end = big.end->prev;
}

ostream& operator<<(ostream& os, Big& big) {
    auto* cur = big.head;
    while (cur != big.end) {
        os << (char)(cur->val + chr2int);
        cur = cur->next;
    }
    os << (char)(cur->val + chr2int);
    return os;
}

int main() {
    string sa, sb;
    cin >> sa >> sb;
    Big a(sa), b(sb);
    auto c = a + b;
    cout << c;
    return 0;
}
```

### 时空消耗

```
1	Accepted	0 ms	1436 KB
2	Accepted	0 ms	1436 KB
3	Accepted	0 ms	1440 KB
4	Accepted	0 ms	1436 KB
5	Accepted	0 ms	1440 KB
6	Accepted	0 ms	1444 KB
7	Accepted	0 ms	1440 KB
8	Accepted	0 ms	1440 KB
9	Accepted	0 ms	1436 KB
10	Accepted	0 ms	1444 KB
```