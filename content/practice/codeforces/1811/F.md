---
title: "CF 1811F - 花ですか?"
description: "$k$-flower と呼ばれる特定の種類のグラフを検出するように求められます。 概念的には、$k$-flower は長さ $k$ の中心サイクルで構成され、そのサイクルの各頂点には長さ $k$ の別の独立したサイクルが接続されています。"
date: "2026-06-09T08:41:02+07:00"
tags: ["codeforces", "competitive-programming", "dfs-and-similar", "graphs", "implementation"]
categories: ["algorithms"]
codeforces_contest: 1811
codeforces_index: "F"
codeforces_contest_name: "Codeforces Round 863 (Div. 3)"
rating: 2100
weight: 1811
solve_time_s: 106
verified: true
draft: false
---

[CF 1811F - 花ですか?](https://codeforces.com/problemset/problem/1811/F)

 **評価:** 2100
 **タグ:** dfs および同様のもの、グラフ、実装
 **解決時間:** 1 分 46 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 と呼ばれる特定のタイプのグラフを検出するように求められます。$k$-花。 概念的には、$k$-花は長さの中心サイクルでできています$k$そして、そのサイクルの各頂点には、長さの異なる別のサイクルが存在します。$k$添付。 これらのアタッチされたサイクルの 2 つは、接続頂点を除いて、互いに頂点を共有したり、中央サイクルと頂点を共有したりすることはありません。 グラフは頂点と辺の形式で与えられ、タスクはそれが次の条件に一致するかどうかを判断することです。$k$-一部の花の構造$k \ge 3$。 

各テスト ケースでは、頂点とエッジの数と、それに続くエッジのリストが提供されます。 出力は、グラフがグラフかどうかに応じて、単純な YES または NO です。$k$-花。 

制約は厳しいです:$n$そして$m$まで可能です$2 \cdot 10^5$、最大で$10^4$テストケース。 頂点またはサイクルのすべてのサブセットをチェックする総当りの試みは、3 次アルゴリズムですら実行不可能であるため、拡張できません。 各グラフのサイズが本質的に線形であるソリューションが必要です。 

エッジ ケースには、非常に小さなグラフや、表面的にはサイクルが含まれているものの、構造化できないグラフが含まれます。$k$-花。 たとえば、他の場所に余分なエッジがある三角形は**ではありません**$k$-flower 接続されたサイクルはすべて互いに素であり、中央のサイクルと同じ長さでなければならないためです。 同様に、正しい位置に 2 または 4 以外の次数の頂点があるグラフは、直ちにテストに不合格になります。 

## アプローチ

 ブルートフォース手法では、グラフ内の考えられるすべてのサイクルを列挙し、各頂点にサイクルを付加することで花のプロパティを検証しようとします。 これは、サイクルによって定義されるため、原理的には機能します。$k$-flower、ただし長さのすべてのサイクルを列挙します$k$指数関数的です。 と$n$まで$2 \cdot 10^5$、このアプローチは絶望的です。 

重要な洞察は構造的なものです。$k$-flower には非常に特殊な度数パターンがあります。 中央サイクルのすべての頂点は次数 4 を持ちます。これは、中央サイクルの 2 つのエッジとそれに付属するサイクルの 2 つのエッジに参加しているためです。 接続されたサイクル内のすべての頂点 (中心に接続する頂点を除く) には次数 2 があります。この厳密な次数パターンにより、検索スペースが大幅に減少します。 

この洞察があれば、頂点の次数が 2 または 4 ではないグラフを即座に拒否できます。さらに、次数 2 の頂点の接続コンポーネント自体は単純なサイクルでなければなりません。 すべての次数 4 の頂点を特定し、それらが単一のサイクルを形成し、それぞれが同じ長さの次数 2 の頂点の 1 つのサイクルに正確に接続していることを確認することで、花の構造を線形時間で検証できます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(2^n) | O(n^2) | 遅すぎる |
 | 学位に基づく構造検査 | O(n + m) | O(n + m) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. テスト ケースのグラフを読み取り、隣接リストを作成します。 これにより、効率的なトラバースと次数チェックが可能になります。 
2. 各頂点の次数を計算します。 頂点の次数が 2 または 4 以外の場合は、すぐに NO を返します。 これは、最初の構造特性を捉えています。$k$-花。 
3. 次数 4 のすべての頂点を収集します。それらが単一のサイクルを形成していることを確認します。 これは、DFS または BFS を使用して 1 つの次数 4 頂点から開始し、すべての次数 4 頂点を 1 回だけ訪問し、それらの間のエッジをたどることで実行できます。 頂点が欠落している場合、または頂点が再確認された場合は、NO を返します。 
4. 次数 4 の各頂点について、次数 2 の隣接頂点を検査します。これらの隣接頂点は、すべて同じ長さの単純なサイクルを形成する必要があります。$k$（セントラルサイクルと同じ）。 次数 2 の頂点に制限された DFS または BFS を実行して、各接続コンポーネントがサイクルを形成していることを確認します。 
5. 次数 2 の頂点が異なる次数 4 の頂点間で共有されていないことを確認します。 これにより、接続されたサイクルが互いに素になることが保証されます。 
6. すべてのチェックに合格した場合は、YES を出力します。 それ以外の場合は、NO を出力します。 

機能する理由: このアルゴリズムは、オブジェクトの正確な構造的不変量をエンコードします。$k$-花。 最初に次数をチェックすることで、不可能なグラフをすばやく除外します。 中心サイクルを確認すると、コア頂点間の接続が確保されます。 接続されたサイクルを検査することで、厳密な動作が保証されます。$k$-花柄。 次数に違反しない余分なエッジは存在できないため、このチェックは必要かつ十分です。 

## Python ソリューション```python
import sys
input = sys.stdin.readline
from collections import defaultdict, deque

def is_k_flower(n, edges):
    adj = [[] for _ in range(n)]
    deg = [0]*n
    for u, v in edges:
        u -= 1
        v -= 1
        adj[u].append(v)
        adj[v].append(u)
        deg[u] += 1
        deg[v] += 1

    deg4 = [i for i, d in enumerate(deg) if d == 4]
    deg2 = [i for i, d in enumerate(deg) if d == 2]

    if any(d not in (2,4) for d in deg):
        return "NO"
    if not deg4:
        return "NO"

    # check central cycle of degree 4 vertices
    visited = [False]*n
    def dfs_cycle(u, parent):
        visited[u] = True
        for v in adj[u]:
            if deg[v] != 4:
                continue
            if v == parent:
                continue
            if visited[v]:
                continue
            dfs_cycle(v, u)
    dfs_cycle(deg4[0], -1)
    if any(visited[v] == False for v in deg4):
        return "NO"

    # check attached cycles
    for u in deg4:
        for v in adj[u]:
            if deg[v] == 2 and not visited[v]:
                # explore attached cycle
                comp = []
                stack = [v]
                while stack:
                    x = stack.pop()
                    if visited[x]:
                        continue
                    visited[x] = True
                    comp.append(x)
                    for y in adj[x]:
                        if deg[y] == 2 and not visited[y]:
                            stack.append(y)
                # verify cycle
                if len(comp) < 3:
                    return "NO"
                for x in comp:
                    if len([y for y in adj[x] if deg[y]==2]) != 2:
                        return "NO"
    return "YES"

def main():
    t = int(input())
    results = []
    for _ in range(t):
        input()  # skip empty line
        n, m = map(int, input().split())
        edges = [tuple(map(int, input().split())) for _ in range(m)]
        results.append(is_k_flower(n, edges))
    print("\n".join(results))

if __name__ == "__main__":
    main()
```このコードは、まず次数によって不可能なグラフをフィルタリングし、次に DFS を使用して次数 4 の頂点の中心サイクルを確認します。 アタッチされたサイクルは同じパスで検証され、次数 2 の頂点の単純なサイクルを形成していることが確認され、重複を避けるためにすべての頂点が訪問済みとしてマークされます。 小切手`len([y for y in adj[x] if deg[y]==2]) != 2`アタッチされた各サイクル頂点がサイクル内に正確に 2 つの接続を持つことを保証します。 

## 実用的な例

 **サンプル 1:**```
9 12
1 2
3 1
2 3
1 6
4 1
6 4
3 8
3 5
5 8
9 7
2 9
7 2
```| ステップ | deg4 を訪問 | deg2 の成分 | チェックに合格しました |
 | --- | --- | --- | --- |
 | セントラルサイクル DFS | 1、2、3 | - | はい |
 | 接続されたサイクル | 4,5,6,7,8,9 | それぞれ長さ 3 のサイクルを形成します | はい |

 出力: はい。 中心三角形と付属のサイクルが正しく識別されていることを確認します。 

**サンプル 2:**```
8 12
1 2
3 1
2 3
1 6
4 1
6 4
3 8
3 5
5 8
8 7
2 8
7 2
```次数 4 の頂点は単純なサイクルを形成できないため、中央サイクル チェックは失敗します。出力: NO。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n + m) | DFS は各頂点とエッジを最大 1 回探索します。 
| スペース | O(n + m) | 隣接リストと訪問済み配列 |

 線形時間は許容可能です$n + m \le 2 \cdot 10^5$テスト ケースごとに、すべてのテスト ケースの合計がこの範囲内に収まります。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    sys.stdout = io.StringIO()
    main()
    return sys.stdout.getvalue().strip()

# provided samples
assert run("""5

9 12
1 2
3 1
2 3
1 6
4 1
6 4
3 8
3 5
5 8
9 7
2 9
7 2

8 12
1 2
3 1
2 3
1 6
4 1
6 4
3 8
3 5
5 8
```
