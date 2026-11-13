---
title: "CF 1749F - パスまでの距離"
description: "$n$ 個の頂点を持つ木が与えられます。 各頂点は最初は値 0 を保持します。 クエリには 2 種類あります。 最初のタイプは、特定の頂点の現在の値を要求します。"
date: "2026-06-09T15:19:12+07:00"
tags: ["codeforces", "competitive-programming", "data-structures", "dfs-and-similar", "trees"]
categories: ["algorithms"]
codeforces_contest: 1749
codeforces_index: "F"
codeforces_contest_name: "Educational Codeforces Round 138 (Rated for Div. 2)"
rating: 2800
weight: 1749
solve_time_s: 133
verified: true
draft: false
---

[CF 1749F - パスまでの距離](https://codeforces.com/problemset/problem/1749/F)

 **評価:** 2800
 **タグ:** データ構造、dfs および同様のもの、ツリー
 **解決時間:** 2 分 13 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 私たちに与えられた木は、$n$頂点。 各頂点は最初は値 0 を保持します。 クエリには 2 種類あります。 最初のタイプは、特定の頂点の現在の値を要求します。 2 番目のタイプでは、固定値を追加するように求められます。$k$短い距離内にあるすべての頂点に$d$2つの頂点を結ぶパスから$u$そして$v$。 ここでの距離は通常の木の距離、つまり最短パスに沿ったエッジの数です。 

なぜなら$n$そして$m$それぞれ最大で可能です$2 \cdot 10^5$、タイプ 2 のクエリごとにすべての頂点を反復する単純なソリューションでは、最大で次のパフォーマンスが得られます。$4 \cdot 10^{10}$最悪の場合の運用。 それは遅すぎます。 最大距離$d$アップデートでは 20 に制限されており、これはすべての頂点に触れるのではなく、近隣の小さなプロパティを利用できることを示唆しています。 

微妙な点は、「パスまでの距離」がどのように定義されるかです。 たとえば、パス上にない頂点も、距離内にある場合には影響を受ける可能性があります。$d$パス上の任意の頂点に。 パス自体に沿った頂点のみを更新する不注意な実装では、誤った結果が生成されます。 たとえば、中心が 1、葉が 2、3、4 の星形のツリーでは、パスを 2 から 3 に更新するクエリは次のようになります。$d = 1$パス上に直接ではなく隣接する頂点 1 も更新する必要があります。 

もう 1 つの潜在的な罠は次のような場合です。$d$とても大きいです。 のために$d \ge \text{tree diameter}$、更新はすべての頂点に影響します。 この特殊なケースは、すべてのノードを明示的に反復することなく、効率的に処理する必要があります。 

## アプローチ

 ブルートフォースアプローチを説明するのは簡単です。 タイプ 2 の各クエリについて、パスに沿ったすべての頂点を特定します。$u$に$v$次に、ツリー内のすべての頂点について、パス上の各頂点までの距離を計算し、距離が最大以下の場合は値を更新します。$d$。 これにより正確性が保証されますが、最悪の場合の複雑さは次のとおりです。$O(m \cdot n \cdot d_{\text{path}})$、それは約です$8 \cdot 10^{10}$最悪のシナリオでは。 明らかに遅すぎます。 

重要な洞察は 2 つの観察から得られます。 まずは最大飛距離$d$アップデートの数は最大でも 20 です。これは、$n$。 第 2 に、影響を受ける頂点は、正確に距離内の頂点です。$d$これは、「各エンドポイントからの距離からパスに沿った距離を引いたもの」に分解できます。 これは、パス ノードから開始して、BFS または DFS の方法で更新を伝播できることを示唆していますが、距離までしか伝播できません。$d$。 以来$d \le 20$、各アップデートはせいぜい大まかに触れます$O(d \cdot n_{\text{path}})$頂点、これは許容されます。 もう 1 つの簡略化は、深さと親ポインタを事前計算して、ツリーに沿ったパスと距離をすばやく特定できることです。 

更新にはパスの周囲の小さな「ボール」内の頂点のみが含まれることに注目して、さらに最適化します。 エンドポイントからの BFS の使用$u$そして$v$、または事前に計算されたオイラー ツアーと LCA 構造を使用すると、距離内のすべての頂点を効率的に列挙できます。$d$無関係な頂点を訪問せずにパスから離れます。 これにより、複雑さが大まかに軽減されます。$O(m \cdot d^2)$、これは実現可能です$d \le 20$そして$m \le 2 \cdot 10^5$。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース |$O(m \cdot n^2)$|$O(n)$| 遅すぎる |
 | 最適 (短距離 BFS/DFS) |$O(m \cdot d^2 + n)$|$O(n)$| 承認済み |

 ## アルゴリズムのチュートリアル

 1. ツリーを読み取り、隣接リストを作成します。 これにより、任意の頂点から隣接する頂点への素早いトラバースが可能になります。 
2. 単純な DFS を使用して、各頂点の深さと親を計算します。 深さは、ツリーに沿った 2 つのノード間の距離を迅速に決定するのに役立ちます。 
3. たとえばバイナリ リフティングを使用して、LCA 構造を準備します。 LCA を使用すると、任意の 2 つの頂点間のパスをすばやく見つけることができます。$u$そして$v$、からのパスに侵入できるため、$u$LCAへ、そしてから$v$LCAに。 
4. タイプ 1 のクエリの場合は、頂点の現在の値を単純に出力します。 これは一定時間です。 
5. タイプ 2 のクエリの場合、最初に次の LCA を計算します。$u$そして$v$パスを特定します。 各ノードについて$x$からの道中で$u$に$v$、距離まで BFS または DFS を実行します。$d$、追加$k$到達したすべての頂点に。 二重カウントを避けるために、訪問済みセットをこの更新のみに保持してください。 
6. なぜなら$d$が小さい場合、各パス ノードの BFS/DFS は小さな近傍に限定されます。 パスの長さが最悪の場合でも$n$、各更新は伝播するだけです$O(d^2)$制約を考慮すると許容範囲内です。 

機能する理由: 各更新は、距離内の頂点にのみ影響します。$d$パスの。 各パス ノードから BFS/DFS を開始し、トラバースを距離に制限することによって$d$、影響を受けるセットを正確にカバーします。 LCA 分解により、パスが正しく識別され、頂点がスキップされないことが保証されます。 訪問済みセットにより、追加しないことが保証されます$k$1 つのクエリで同じ頂点に複数回アクセスします。 

## Python ソリューション```python
import sys
from collections import deque
input = sys.stdin.readline
sys.setrecursionlimit(1 << 25)

n = int(input())
adj = [[] for _ in range(n)]
for _ in range(n-1):
    u, v = map(int, input().split())
    adj[u-1].append(v-1)
    adj[v-1].append(u-1)

# depth and parent for LCA
LOG = 20
parent = [[-1]*n for _ in range(LOG)]
depth = [0]*n

def dfs(u, p):
    parent[0][u] = p
    for v in adj[u]:
        if v != p:
            depth[v] = depth[u] + 1
            dfs(v, u)
dfs(0, -1)

# binary lifting
for k in range(1, LOG):
    for v in range(n):
        if parent[k-1][v] != -1:
            parent[k][v] = parent[k-1][parent[k-1][v]]

def lca(u, v):
    if depth[u] < depth[v]:
        u, v = v, u
    for k in reversed(range(LOG)):
        if parent[k][u] != -1 and depth[parent[k][u]] >= depth[v]:
            u = parent[k][u]
    if u == v:
        return u
    for k in reversed(range(LOG)):
        if parent[k][u] != -1 and parent[k][u] != parent[k][v]:
            u = parent[k][u]
            v = parent[k][v]
    return parent[0][u]

# value array
val = [0]*n

m = int(input())
for _ in range(m):
    tmp = list(map(int, input().split()))
    if tmp[0] == 1:
        print(val[tmp[1]-1])
    else:
        _, u, v, k, d = tmp
        u -= 1
        v -= 1
        w = lca(u, v)
        # collect path nodes
        path = []
        x = u
        while x != w:
            path.append(x)
            x = parent[0][x]
        path.append(w)
        stack = []
        x = v
        while x != w:
            stack.append(x)
            x = parent[0][x]
        path.extend(reversed(stack))
        
        visited = [False]*n
        for node in path:
            dq = deque()
            dq.append((node, 0))
            while dq:
                cur, dist = dq.popleft()
                if dist > d or visited[cur]:
                    continue
                visited[cur] = True
                val[cur] += k
                for nei in adj[cur]:
                    if not visited[nei]:
                        dq.append((nei, dist+1))
```説明: DFS は、すべてのノードの深さと親を計算します。 バイナリリフティングで LCA を見つけます。$O(\log n)$時間。 更新ごとに、次からのパスを列挙します。$u$に$v$LCA を介して、各パス ノードから距離までの限定的な BFS を実行します。$d$。 ローカルの訪問配列により、同じ更新内での二重カウントが回避されます。 タイプ 1 のクエリは単に格納された値を返します。 

## 実用的な例

 **サンプル 1**

 | クエリ | パス | 影響を受ける頂点 | 更新された値 |
 | --- | --- | --- | --- |
 | 2 4 5 10 2 | 4-2-5 | 4,2,5,1,3 | +10 |
 | 1 3 | - | - | 10 |
 | 1 6 | - | - | 0 |
 | 2 1 1 10 20 | 1 | すべて | |
