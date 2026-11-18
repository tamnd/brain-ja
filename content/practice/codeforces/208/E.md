---
title: "CF 208E - ブラッド・カズンズ"
description: "私たちには、各人が最大でも 1 人の親を持つ、根を張った森が与えられています。 親ポインターを上にたどっていくと、最終的にはルートに到達するか、構造から外れてしまいます。 これはツリーのコレクションを定義します。 「k 番目の祖先」とは、親関係を k 回適用することを意味します。"
date: "2026-06-03T17:24:34+07:00"
tags: ["codeforces", "competitive-programming", "binary-search", "data-structures", "dfs-and-similar", "trees"]
categories: ["algorithms"]
codeforces_contest: 208
codeforces_index: "E"
codeforces_contest_name: "Codeforces Round 130 (Div. 2)"
rating: 2100
weight: 208
solve_time_s: 110
verified: false
draft: false
---

[CF 208E - 血のいとこ](https://codeforces.com/problemset/problem/208/E)

 **評価:** 2100
 **タグ:** 二分探索、データ構造、DFS および同様のもの、ツリー
 **解決時間:** 1 分 50 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 私たちには、各人が最大でも 1 人の親を持つ、根を張った森が与えられています。 親ポインターを上にたどっていくと、最終的にはルートに到達するか、構造から外れてしまいます。 これはツリーのコレクションを定義します。 

「k 番目の祖先」とは、親関係を k 回適用することを意味します。 したがって、チェーンが存在する限り、1 番目の祖先は親、2 番目の祖先は親の親、というようになります。 

For each query consisting of a person v and a distance p, we are asked to count how many other people share the same p-th ancestor as v. In other words, we look at the node u obtained by climbing p steps from v, and we count how many nodes in the entire forest also have u as their p-th ancestor. The answer excludes v itself.

 この制約により、クエリごとのトラバーサルから遠ざけられます。 最大 100,000 のノードと 100,000 のクエリでは、クエリごとに p ステップ上方に進むソリューションは、最悪の場合、二次関数になります。 すべての祖先を明示的に事前計算するソリューションであっても、大量のメモリが必要になります。 

The key structure is that ancestor relationships are fixed by depth in a tree, so nodes that share the same ancestor at distance p form a contiguous grouping in a DFS ordering. これは、「サブツリー内に特定の深さのノードがいくつ存在するか」を答えることができるようにサブツリー構造を前処理することを示唆しています。 

微妙なエッジ ケースは、p 番目の祖先が存在しないノードから発生します。 If v is too shallow, the answer must be zero. もう 1 つのケースは、入力が単一のツリーではなくフォレストであるため、複数のルートが存在する場合です。 DFS ベースのソリューションでは、複数のルートを正しく処理する必要があります。 

## アプローチ

 A direct approach computes the p-th ancestor of each query node by walking up pointers, then scans the entire node set to count matches. This is correct but extremely slow. 最悪の場合、各クエリは O(n) を必要とし、O(nm) 操作が発生する可能性がありますが、これは 10^10 スケールでは実行不可能です。 

より良い視点は、問題を逆転させることから生まれます。 「私の p 番目の祖先を共有しているのは誰ですか」と尋ねる代わりに、ノード u を固定して、「u を p 番目の祖先として持つノードはいくつありますか?」と尋ねることができます。 This depends on the structure of the subtree rooted at u and the depth distribution inside it.

 各ルートから DFS を実行すると、エントリ時間とサブツリー範囲を記録できます。 We also compute depth of every node. 次に、任意のノード u について、そのサブツリー内のすべてのノードがオイラー ツアー順序で連続したセグメントに表示されます。 このうち、深さ Depth[u] + p のノードは、まさに p 番目の祖先が u であるノードです。 

したがって、次の形式の高速クエリが必要です。u のサブツリーで、正確に d の深さを持つノードの数。 This becomes a classic offline grouping problem. ノードを深さごとにグループ化して保存し、深さごとに DFS エントリ時間のソートされたリストを維持します。 Then each query reduces to two binary searches on that list restricted to the subtree interval.

 | アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | Brute force ancestor walking | O(nm) | O(n) | 遅すぎる |
 | DFS + depth buckets + binary search | O((n + m) log n) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

1. 親配列を使用してフォレストの隣接リストを作成します。 親が 0 である各ノードはルートとして扱われます。 これにより、すべてのツリーを独立して走査できるようになります。 
2. 各ルートから DFS を実行し、各ノードに深さを割り当て、その開始時間 tin と終了時間 toout を記録します。 DFS 順序により、任意のノードのサブツリーがオイラー ツアーで連続した間隔を形成することが保証されます。 
3. 各深度値について、その深度に現れるノードのエントリ時間のリストを維持します。 これにより、DFS の順序が維持されるようにノードがレベルごとにグループ化されます。 
4. クエリ (v, p) の場合、事前に計算されたバイナリ リフティング テーブルを使用して、v より上の u = 祖先 p ステップを計算します。 u が存在しない場合は、存在しない祖先を共有するノードは存在しないため、すぐに 0 を返します。 
5. u が見つかったら、深さが Depth[u] + p に等しい subtree(u) 内のノードを数えます。 そのターゲットの深さに対応するリストを取得し、二分探索を使用して [tin[u], tout[u]] 内にエントリ時間が何回あるかを数えます。 
6. v 自体が範囲に含まれている場合は、このカウントから 1 を引いた値を返します。これは、問題では v は含まれず「他の人」が求められているためです。 

これが機能する理由は、ツリー上の DFS の 2 つの構造特性に基づいています。 まず、サブツリー内のすべてのノードは、オイラー ツアー順序の連続セグメントに正確に対応します。 第 2 に、深さは祖先と子孫の差に沿って一定であるため、p 番目の祖先が u であるノードは、u からちょうど p レベル下、そのサブツリー内に存在する必要があります。 これら 2 つの制約は有効なノードを一意に特徴付けるため、深さバケットとサブツリー間隔の交差によってそれらをカウントしても、有効なノードを過剰にカウントしたり見逃したりすることはできません。 

## Python ソリューション```python
import sys
input = sys.stdin.readline
sys.setrecursionlimit(200000)

n = int(input())
parent = list(map(int, input().split()))

g = [[] for _ in range(n)]
roots = []

for i, p in enumerate(parent):
    if p == 0:
        roots.append(i)
    else:
        g[p - 1].append(i)

LOG = 17
up = [[-1] * n for _ in range(LOG)]
depth = [0] * n
tin = [0] * n
tout = [0] * n
timer = 0

depth_nodes = {}

def dfs(v, p):
    global timer
    tin[v] = timer
    timer += 1

    up[0][v] = p
    for i in range(1, LOG):
        if up[i - 1][v] != -1:
            up[i][v] = up[i - 1][up[i - 1][v]]

    d = depth[v]
    if d not in depth_nodes:
        depth_nodes[d] = []
    depth_nodes[d].append(tin[v])

    for to in g[v]:
        depth[to] = d + 1
        dfs(to, v)

    tout[v] = timer - 1

for r in roots:
    dfs(r, -1)

def lift(v, k):
    for i in range(LOG):
        if v == -1:
            return -1
        if k & (1 << i):
            v = up[i][v]
    return v

m = int(input())
out = []

for _ in range(m):
    v, p = map(int, input().split())
    v -= 1
    u = lift(v, p)

    if u == -1:
        out.append("0")
        continue

    target_depth = depth[u] + p
    arr = d
```
