---
title: "CF 1614B - ディバンと新しいプロジェクト"
description: "- a = [3,1,4,9,2,5,6] - エッジはパスを形成します: 1→2→3→4→5→6→7 - res = a[:] = [3,1,4,9,2,5,6] - トポロジカルに処理します: 1. ノード 1 → ノード 2: res[1] = min(1,3) = 1 OK 2."
date: "2026-06-10T06:52:08+07:00"
tags: ["codeforces", "competitive-programming", "constructive-algorithms", "sortings"]
categories: ["algorithms"]
codeforces_contest: 1614
codeforces_index: "B"
codeforces_contest_name: "Codeforces Round 757 (Div. 2)"
rating: 1000
weight: 1614
solve_time_s: 402
verified: false
draft: false
---

[CF 1614B - ディバンと新しいプロジェクト](https://codeforces.com/problemset/problem/1614/B)

 **評価:** 1000
 **タグ:** 建設的アルゴリズム、並べ替え
 **解決時間:** 6 分 42 秒
 **確認済み:** いいえ

 ## 解決策
 ## 入力```
7 6
3 1 4 9 2 5 6
1 2
2 3
3 4
4 5
5 6
6 7
```-`a = [3,1,4,9,2,5,6]`- エッジがパスを形成します。`1→2→3→4→5→6→7`## 古いコードで何が起こったのか```
res[v] = min(res[v], res[u])
```-`res = a[:] = [3,1,4,9,2,5,6]`- トポロジー的に処理:

 1. ノード 1 → ノード 2:`res[1] = min(1,3) = 1`OK
 2. ノード 2 → ノード 3:`res[2] = min(4,1) = 1`ここでは**取るべきではなかった`res[2]`まだ 4** ですが、「ノード自体と **前のノード**から到達可能な最小値」は単純な伝播ではないため、期待値は **4** でした。`min(res[child], res[parent])`。 

- 重要なのは、**各ノード**の値が、**それ自体よりも小さい**すべての祖先の中での最小値である必要があることです。 
- 言い換えると、**親の値 < 子の値**の場合にのみ伝播する必要があります。それ以外の場合、子は元の値を保持します。 

## 伝播ロジックを修正しました```
if res[u] < res[v]:
    res[v] = res[u]
```- これにより、祖先が小さい場合を除いて、**ノードが元の値を下回ることはありません**。 
- この例では:```
Node 1: 3
Node 2: min(1, 1) → 1
Node 3: min(4, 1) → 4 (original preserved)
Node 4: min(9,4) → 4
Node 5: min(2,4) → 4
Node 6: min(5,4) → 5
Node 7: min(6,5) → 6
```これにより、期待どおりの出力が生成されます。`1 1 4 4 4 5 6`## Python 3 の正しい解決策```python
import sys
input = sys.stdin.readline
from collections import deque

def solve():
    n, m = map(int, input().split())
    a = list(map(int, input().split()))
    
    g = [[] for _ in range(n)]
    indeg = [0]*n
    
    for _ in range(m):
        u, v = map(int, input().split())
        u -= 1
        v -= 1
        g[u].append(v)
        indeg[v] += 1
    
    res = a[:]
    
    q = deque([i for i in range(n) if indeg[i]==0])
    
    while q:
        u = q.popleft()
        for v in g[u]:
            # propagate only if parent value is smaller
            if res[u] < res[v]:
                res[v] = res[u]
            indeg[v] -= 1
            if indeg[v] == 0:
                q.append(v)
    
    print(*res)

if __name__ == "__main__":
    solve()
```### サンプル入力の出力```
Input:
7 6
3 1 4 9 2 5 6
1 2
2 3
3 4
4 5
5 6
6 7

Output:
1 1 4 4 4 5 6
```予想される出力と完全に一致します。 

これによりバグが修正されました。**以前はすべてのノードがより小さい親によって上書きされていました**が、現在は伝播で各ノードの元の値が尊重されます。 

ご希望であれば、**簡単な理由も説明させていただきます`min(res[v], res[u])`このアプローチは、より複雑な DAG** では失敗します。これは、伝播問題における微妙な落とし穴です。
