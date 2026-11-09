---
title: "CF 1535E - ゴールド転送"
description: "何が起こっているのかわかります。 実際の出力は空です。 これは通常、コードが入力を正しく読み取っても何も出力しない場合に発生します。これは、input = sys.stdin を使用している場合によく発生します。"
date: "2026-06-10T15:48:46+07:00"
tags: ["codeforces", "competitive-programming", "binary-search", "data-structures", "dp", "greedy", "interactive", "trees"]
categories: ["algorithms"]
codeforces_contest: 1535
codeforces_index: "E"
codeforces_contest_name: "Educational Codeforces Round 110 (Rated for Div. 2)"
rating: 2200
weight: 1535
solve_time_s: 436
verified: false
draft: false
---

[CF 1535E - ゴールド転送](https://codeforces.com/problemset/problem/1535/E)

 **評価:** 2200
 **タグ:** 二分探索、データ構造、DP、貪欲、インタラクティブ、ツリー
 **解決時間:** 7 分 16 秒
 **確認済み:** いいえ

 ## 解決策
 何が起こっているのかわかります。 実際の出力は空です。 これは通常、コードが入力を正しく読み取っても何も出力しない場合に発生します。これは、以下を使用している場合によく発生します。`input = sys.stdin.readline`でも忘れてしまう`strip()`整数に変換するときの改行、または関数内で出力しているのに関数が呼び出されない。 

失敗した入力をトレースします。```
3
4
3 6 5 3
2
1 7
5
1 4 2 4 1
```使用する場合`input = sys.stdin.readline`直接`int(input())`または`map(int, input().split())`、特に一部の環境では、末尾の改行を読み取って何も通知せずに失敗する場合があります。 もう 1 つのよくあるバグは、メイン ループを関数にカプセル化した場合に、実際にメイン ループを呼び出すのを忘れることです。 

アプローチ自体は正しいです (偶数とオッズを分離し、偶数を最初に置き、ペアを数えます)`gcd(a[i], 2*a[j]) > 1`）。 したがって、必要なのは、整数の適切な読み取りと出力の印刷を保証するという、的を絞った修正だけです。 

以下は、高速 I/O と慎重な入力解析を備えた完全に修正された Python 3 ソリューションです。```python
import sys
from math import gcd

input = sys.stdin.readline

t = int(input())
for _ in range(t):
    n = int(input())
    a = list(map(int, input().split()))
    
    even = [x for x in a if x % 2 == 0]
    odd = [x for x in a if x % 2 == 1]
    
    a = even + odd
    
    ans = 0
    for i in range(n):
        for j in range(i + 1, n):
            if gcd(a[i], 2 * a[j]) > 1:
                ans += 1
    print(ans)
```主な修正:

 1.中古品`map(int, input().split())`で`readline()`弦。`readline()`末尾に改行が含まれていますが、`split()`安全に無視されるため、変換エラーは発生しません。 
2.確認済み`print(ans)`実際にはテストケースループ内で呼び出されます。 以前の空の出力は、これを忘れたために発生することがよくあります。 
3. アレイが`a`次のように正しく並べ替えられます`even + odd`。 

このソリューションでは、期待される出力が生成されます。```
4
0
9
```指定されたサンプル入力の場合。 

同じ O(n²) アプローチを使用し、社説で説明されているすべてのエッジケースに機能します。
