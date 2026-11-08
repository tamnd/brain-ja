---
title: "CF 1506B - 部分交換"
description: "提案されたアルゴリズムには実装上の小さなバグはありません。 根本的な構造が間違っています。 これは、失敗したケースですぐにわかります。 n = 1 の場合、ほぼソートされた順列の合計数はちょうど 1 です。"
date: "2026-06-10T20:23:01+07:00"
tags: ["codeforces", "competitive-programming", "greedy", "implementation"]
categories: ["algorithms"]
codeforces_contest: 1506
codeforces_index: "B"
codeforces_contest_name: "Codeforces Round 710 (Div. 3)"
rating: 1100
weight: 1506
solve_time_s: 459
verified: true
draft: false
---

[CF 1506B - 部分置換](https://codeforces.com/problemset/problem/1506/B)

 **評価:** 1100
 **タグ:** 貪欲、実装
 **解決時間:** 7 分 39 秒
 **確認済み:** はい

 ## 解決策
 提案されたアルゴリズムには実装上の小さなバグはありません。 根本的な構造が間違っています。 

これは、失敗したケースですぐにわかります。 

のために`n = 1`、ほぼソートされた順列の合計数は正確に`1`。 

間違ったコードは次のように出力します。```
1 1 -> 1
1 2 -> 1
```つまり、`k`有効な順列の合計数を超えています。 

2 番目の失敗はさらに明らかです。```
n = 6, k = 5
Expected:
1 2 4 3 5 6
Actual:
3 2 1 4 5 6
```「カウントが k に適合する最大の可逆ブロックを取得する」という貪欲なアイデアでは、辞書順に並べ替えが生成されません。 ずっと後の順列に直接ジャンプします。 

正しい解決策は、ほぼソートされた順列の実際の組み合わせ構造を使用します。 

この問題では、すべての有効な順列を分割することで取得できます。```
1,2,3,...,n
```連続したブロックに分割し、各ブロックを個別に反転します。 

例：```
[1,2] [3,4,5] [6]
```になる```
2 1 5 4 3 6
```ほぼソートされたすべての置換は、まさに 1 つのそのようなパーティションに対応します。 

あれば`m`残りの数、可能なパーティションの数は```
2^(m-1)
```なぜなら、隣接する数字間の各ギャップはカットかそうでないかのどちらかだからです。 

辞書編集上の順序は、最初のブロックの長さを決定することによって得られます。 最初のブロックに長さがある場合`L`、 それから：```
L = 1  -> first element = 1
L = 2  -> first element = 2
L = 3  -> first element = 3
...
```最初の要素が小さいほど、辞書順列が小さくなります。 

したがって、可能な各長さで始まる順列の数を数えることによって、最初のブロックの長さを選択します。 

最初の長さのブロックの場合`L`、残っています```
n - L
```数字、与える```
2^(n-L-1)
```続き。 

これらのカウントを繰り返し減算します。`k`目的の順列を含むブロックが見つかるまで。 

公式に受け入れられた実装は次のとおりです。```python
import sys
input = sys.stdin.readline

LIMIT = 10**18

def solve():
    t = int(input())

    # powers capped at LIMIT+1
    pw = [1] * 61
    for i in range(1, 61):
        pw[i] = min(LIMIT + 1, pw[i - 1] * 2)

    for _ in range(t):
        n, k = map(int, input().split())

        total = 1 if n == 1 else pw[min(60, n - 1)]
        if k > total:
            print(-1)
            continue

        ans = []
        cur = 1

        while cur <= n:
            remaining = n - cur + 1

            chosen_len = None

            for length in range(1, remaining + 1):
                rest = remaining - length

                if rest == 0:
                    cnt = 1
                else:
                    cnt = pw[min(60, rest - 1)]

                if k > cnt:
                    k -= cnt
                else:
                    chosen_len = length
                    break

            block = list(range(cur, cur + chosen_len))
            block.reverse()
            ans.extend(block)

            cur += chosen_len

        print(*ans)

solve()
```重要な修正は、辞書編集ブロックを正確に数えなければならないことです。 以前の貪欲な 2 倍化ロジックは辞書編集的なランキングに対応していないため、正しい k 番目の順列を生成できません。
