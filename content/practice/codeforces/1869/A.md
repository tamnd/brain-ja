---
title: "CF 1869A - ゼロにする"
description: "Codeforces 1869A の場合、ソリューション全体は建設的な観察です。 n が偶数の場合、以下を実行します。 最初の操作の後、すべての要素は X になります。ここで、X は配列全体の XOR です。"
date: "2026-06-09T00:55:22+07:00"
tags: ["codeforces", "competitive-programming", "constructive-algorithms"]
categories: ["algorithms"]
codeforces_contest: 1869
codeforces_index: "A"
codeforces_contest_name: "Codeforces Round 896 (Div. 2)"
rating: 900
weight: 1869
solve_time_s: 158
verified: false
draft: false
---

[CF 1869A - ゼロにする](https://codeforces.com/problemset/problem/1869/A)

 **評価:** 900
 **タグ:** 建設的アルゴリズム
 **解決時間:** 2 分 38 秒
 **確認済み:** いいえ

 ## 解決策
 Codeforces 1869A の場合、ソリューション全体は建設的な観察です。 

もし`n`が偶数の場合、次を実行します。```
(1, n)
(1, n)
```最初の操作の後、すべての要素は次のようになります。`X`、 どこ`X`配列全体の XOR です。 

セグメントの長さが偶数であるため、配列全体の XOR は次のようになります。```
X xor X xor ... xor X = 0
```同じ操作を再度適用すると、すべての要素が次のようになります。`0`。 

もし`n`奇数です。最初のものを使用してください`n - 1`長さが偶数の要素:```
(1, n - 1)
(1, n - 1)
(n - 1, n)
(n - 1, n)
```最初の 2 つの操作でポジションを作成します`1..n-1`ゼロに等しい。 その後、最後の 2 つの操作でポジションを作成します`n-1`そして`n`ゼロにも等しい。 

これは最大 4 つの操作を使用しますが、制限の 8 よりはるかに下回ります。```python
import sys
input = sys.stdin.readline

def solve():
    t = int(input())

    for _ in range(t):
        n = int(input())
        a = list(map(int, input().split()))  # values are irrelevant

        if n % 2 == 0:
            print(2)
            print(1, n)
            print(1, n)
        else:
            print(4)
            print(1, n - 1)
            print(1, n - 1)
            print(n - 1, n)
            print(n - 1, n)

if __name__ == "__main__":
    solve()
```なぜ効果があるのか:

 偶数長のセグメントの場合、セグメントを XOR 値で置き換えた後`x`、すべての位置に含まれます`x`。 そのセグメント全体の XOR は次のようになります。`x`偶数回繰り返される、つまり`0`。 同じセグメントに操作を再適用すると、すべての位置が次のように置き換えられます。`0`。 

いつ`n`奇数です、セグメント`[1, n-1]`は偶数の長さなので、2 つの操作でゼロになります。 この後、ポジションを`n-1`はすでにゼロです。 操作を 2 回適用する`[n-1, n]`最後の 2 つの位置をゼロにし、配列全体をゼロのままにします。
