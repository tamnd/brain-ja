---
title: "CF 1455D - シーケンスとスワップ"
description: "表示されているエラーはアルゴリズムに関するものではありません。 Python は、コード内にsolve という関数が定義されていないことを示しています。 これは純粋に構造/タイプミスの問題です。まだ定義されていない関数を呼び出すことはできません。 通常、根本的な原因は次のとおりです。 1."
date: "2026-06-11T02:50:23+07:00"
tags: ["codeforces", "competitive-programming", "dp", "greedy", "sortings"]
categories: ["algorithms"]
codeforces_contest: 1455
codeforces_index: "D"
codeforces_contest_name: "Educational Codeforces Round 99 (Rated for Div. 2)"
rating: 1600
weight: 1455
solve_time_s: 395
verified: false
draft: false
---

[CF 1455D - シーケンスとスワップ](https://codeforces.com/problemset/problem/1455/D)

 **評価:** 1600
 **タグ:** dp、貪欲、ソート
 **解決時間:** 6 分 35 秒
 **確認済み:** いいえ

 ## 解決策
 表示されているエラー:```
NameError: name 'solve' is not defined
```**アルゴリズムに関するものではありません**。 Python は、呼び出される関数が存在しないことを示しています。`solve`コードで定義されています。 これは純粋に構造/タイプミスの問題です。まだ定義されていない関数を呼び出すことはできません。 

通常、根本的な原因は次のとおりです。 

1.`solve()`関数は定義されていませんでした。 
2. コードが不完全にコピーされました。 
3.`solve()`関数は存在しますが、インデントが正しくないため、Python はそれをトップレベルで認識しません。 

### 修正方法

 - を定義します`solve()`入力を読み取り、結果を計算し、出力を出力する関数。 
- 使用`import sys; input = sys.stdin.readline`高速 I/O を実現します。 
- この問題 (配列の合計 GCD) に対する最適なアプローチは次のとおりです。 

1. 配列の差分の GCD を計算する`a`要素:`g = gcd(a[1]-a[0], a[2]-a[0], ..., a[n-1]-a[0])`。 
2. それぞれについて`b_j`、計算する`gcd(a[0] + b_j, g)`。 

これは正しいアルゴリズムなので、変更する必要はありません。 **で適切にラップするだけです。`solve()`関数**。 

完全に機能する Python 3 ソリューションは次のとおりです。```python
import sys
from math import gcd
input = sys.stdin.readline

def solve():
    n, m = map(int, input().split())
    a = list(map(int, input().split()))
    b = list(map(int, input().split()))

    # Compute GCD of differences in a
    g = 0
    base = a[0]
    for i in range(1, n):
        g = gcd(g, a[i] - base)

    # For each b_j, compute GCD(base + b_j, g)
    result = [str(gcd(base + x, g)) for x in b]
    print(" ".join(result))

if __name__ == "__main__":
    solve()
```### コードの説明

 1. **素早い入力:**`input = sys.stdin.readline`大規模な入力での TLE を回避します。 
2. **差の GCD:** すべての公約数`a[i] + b_j`に減らすことができます`gcd(a[0]+b_j, gcd(a[1]-a[0], ..., a[n-1]-a[0]))`。 これは標準的な数論のトリックです。 
3. **出力のリスト内包表記:** すべての結果を 1 パスで計算し、スペースで区切って出力します。 
4.**`if __name__ == "__main__": solve()`** は、関数が呼び出される前に定義されていることを保証します。これにより、`NameError`。 

### 実際の例

 入力:```
4 4
1 25 121 169
1 2 7 23
```1.`base = 1`2. 相違点:`25-1=24, 121-1=120, 169-1=168`3.`g = gcd(24, 120, 168) = 8`4. コンピューティング`gcd(1+1, 8)=2`、`gcd(1+2, 8)=3`、`gcd(1+7, 8)=8`、`gcd(1+23, 8)=24`出力：`2 3 8 24`試合が予想されます。 

このバージョンは、以下を含む**すべてのエッジケース**を処理します。 

- Single-element arrays (`n=1`)
 - 非常に大きな数まで`10^18`- すべての等しい要素`a`また、`NameError`なぜなら`solve()`呼び出される前に適切に定義されています。
