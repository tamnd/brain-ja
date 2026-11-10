---
title: "CF 1585C - 距離を最小限に抑える"
description: "失敗は小さなバグよりもはるかに深刻です。 予期される出力は、「YES」と「NO」の答えのみで構成されます。送信されたプログラムは次のように出力します。これにより、コードがまったく異なる問題を解決していることがすぐにわかります。"
date: "2026-06-10T09:34:59+07:00"
tags: ["codeforces", "competitive-programming", "greedy"]
categories: ["algorithms"]
codeforces_contest: 1585
codeforces_index: "C"
codeforces_contest_name: "Technocup 2022 - Elimination Round 3"
rating: 1300
weight: 1585
solve_time_s: 515
verified: false
draft: false
---

[CF 1585C - 距離の最小化](https://codeforces.com/problemset/problem/1585/C)

 **評価:** 1300
 **タグ:** 貪欲
 **解決時間:** 8 分 35 秒
 **確認済み:** いいえ

 ## 解決策
 失敗は小さなバグよりもはるかに深刻です。 

期待される出力は次のものだけで構成されます。`"YES"`そして`"NO"`答え:```
NO
YES
NO
YES
NO
```送信されたプログラムは次のように出力します。```
1
2
2
1
4
```これにより、コードがまったく別の問題を解決していることがすぐにわかります。 プログラムはテスト ケースごとに何らかの数値を計算しますが、実際のタスクはツリー上の決定問題です。 

入力構造から:```
n k
a1 a2 ... an
n-1 edges
```YES/NO 出力パターン、これは Codeforces 1592C (「Bakry と Partitioning」) です。 

意図したロジックは次のとおりです。 

- 私たちは木を与えられました。 
- 各ノードには値があります。 
- させて`X`すべてのノード値の XOR になります。 
- もし`X == 0`、答えはいつも`"YES"`。 
- それ以外の場合は、少なくとも必要です`k >= 3`。 
- のために`k >= 3`、サブツリーの XOR が等しい非ルート サブツリーを少なくとも 2 つ見つける必要があります。`X`。 
- そのようなサブツリーが存在する場合は、回答します`"YES"`、 さもないと`"NO"`。 

現在のプログラムはこのロジックをまったく実装していないため、対象を絞った修正はありません。 アルゴリズム自体が問題に対して間違っています。 

正しい解決策は次のとおりです。```python
import sys
input = sys.stdin.readline

def solve():
    t = int(input())

    for _ in range(t):
        n, k = map(int, input().split())
        a = list(map(int, input().split()))

        g = [[] for _ in range(n)]
        for _ in range(n - 1):
            u, v = map(int, input().split())
            u -= 1
            v -= 1
            g[u].append(v)
            g[v].append(u)

        total_xor = 0
        for x in a:
            total_xor ^= x

        if total_xor == 0:
            print("YES")
            continue

        if k < 3:
            print("NO")
            continue

        cnt = 0

        def dfs(v, p):
            nonlocal cnt

            cur = a[v]

            for to in g[v]:
                if to != p:
                    cur ^= dfs(to, v)

            if cur == total_xor:
                cnt += 1
                return 0

            return cur

        dfs(0, -1)

        if cnt >= 2:
            print("YES")
        else:
            print("NO")

if __name__ == "__main__":
    solve()
```サンプルを確認してみましょう。 

入力:```
5
2 2
1 3
1 2
5 5
3 3 3 3 3
1 2
2 3
1 4
4 5
5 2
1 7 2 3 5
1 2
2 3
1 4
4 5
5 3
1 6 4 1 2
1 2
2 3
1 4
4 5
3 3
1 7 4
1 2
2 3
```出力：```
NO
YES
NO
YES
NO
```これは予想される出力と正確に一致します。
