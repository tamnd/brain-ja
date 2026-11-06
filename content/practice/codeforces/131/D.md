---
title: "CF 131D - 地下鉄"
description: "私たちは、正確に n 個の通路で接続された n 個の駅からなる地下鉄システムを与えられており、各通路は 2 つの異なる駅を接続しています。 システムは、各ステーションが他のすべてのステーションに到達できる接続されたグラフを形成します。"
date: "2026-05-28T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "dfs-and-similar", "graphs"]
categories: ["algorithms"]
codeforces_contest: 131
codeforces_index: "D"
codeforces_contest_name: "Codeforces Beta Round 95 (Div. 2)"
rating: 1600
weight: 131
solve_time_s: 74
verified: true
draft: false
---

[CF 131D - 地下鉄](https://codeforces.com/problemset/problem/131/D)

 **評価:** 1600
 **タグ:** dfs および類似のグラフ
 **解決時間:** 1 分 14 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 私たちは、正確に _n_ 個の通路で接続された _n_ 個の駅からなる地下鉄システムを与えられており、各通路は 2 つの異なる駅を接続しています。 システムは、各ステーションが他のすべてのステーションに到達できる接続されたグラフを形成します。 グラフ理論によれば、_n_ 個のノードと _n_ 個のエッジを持つ接続されたグラフには、正確に 1 つのサイクルが含まれます。 このサイクルは問題の「環状道路」に相当します。 目標は、各駅から環状道路までの距離を計算することです。これは、自転車上の任意の駅に到達するために通過する通路の最小数です。 サイクルの一部であるステーションの距離はゼロです。 

入力は最初の行以降の _n_ 行で構成され、各行は 2 つのステーション間の双方向接続を記述します。 出力は _n_ 個の数値のシーケンスで、i 番目の数値は駅 _i_ から環状道路までの距離です。 

制約 _n ≤ 3000_ は、時間計算量がおよそ O(n²) までの解が 2 秒の制限内で快適に実行されることを意味します。 グラフ構造を利用せずに距離を繰り返し検索する単純なアプローチでも、不必要にノードにアクセスすると速度が遅すぎる可能性があります。 

注意すべきエッジ ケースには、ほぼすべてのステーションが環状道路上にある小さなサイクルや、分岐に沿って距離を正しく伝播する必要があるサイクルに接続されたリニア チェーンなどがあります。 たとえば、1 つのノードに長い尾が付いている三角形のような形状のグラフでは、尾のノードに正しい増分距離が割り当てられる必要があります。 

## アプローチ

 ブルートフォースアプローチでは、まず考えられるすべてのサイクルをチェックして環状道路を見つけようとします。 これには、サイクルを列挙してその一意性を検証することが含まれますが、これには組み合わせ的にコストがかかり、隣接性チェックにおよそ O(n³) かかります。 サイクルが判明すると、BFS は各ステーションからサイクルまでの距離を O(n²) で計算できます。 それは正しいですが、グラフのプロパティによりより直接的なアプローチが可能になるため、これは不必要な作業です。 

重要な観察は、グラフが接続されており、正確に 1 つのサイクルを持っていることです。 これは、サイクルに含まれていないステーションは、あるサイクル ノードをルートとするツリーの一部であることを意味します。 したがって、問題は 2 つのステップに短縮されます。まずサイクル内のノードを識別し、次に木の枝に沿ってサイクルまでの距離を伝播します。 

サイクル検出は、修正された深さ優先探索を使用して効率的に実行できます。 DFS 中にバックエッジが検出されると、サイクルの存在がマークされます。 DFS 中にパスを追跡すると、どのノードがサイクル内にあるかを正確に識別できます。 サイクルが特定されると、すべてのサイクル ノードから開始される幅優先検索によって、距離が外側に同時に伝播されます。 各ステーションは自転車への最短経路に沿って到着するため、正しい距離が割り当てられます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルート フォース (全サイクル + ノードごとの BFS) | O(n3) | O(n²) | 遅すぎる |
 | サイクルの DFS + 距離の BFS | O(n) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

1. 地下鉄を隣接リストとして表し、各駅が接続する駅のリストにマップされます。 この構造により、近傍に対して O(1) 回の反復が可能になります。 
2. 深さ優先検索を実行して、固有のサイクルを検出します。 DFS 中に各ノードの親を追跡します。 親ではない訪問ノードが見つかった場合、サイクルが見つかります。 現在のノードと訪問先ノード間の DFS パスに沿ったすべてのノードを記録して、サイクルを特定します。 
3. すべてのステーションに対して距離配列を「未訪問」を意味する -1 で初期化します。 サイクル内で識別されたすべてのステーションに対して距離 0 を設定します。 
4. キューを使用して、すべてのサイクル ノードから同時に開始する幅優先検索を実行します。 デキューされたノードごとに、その隣接ノードを反復処理します。 隣接ノードの距離が -1 の場合、現在のノードの距離に 1 を加えた値に設定し、キューに追加します。 
5. すべてのノードに距離が割り当てられるまで BFS を続けます。 最後に、距離配列には各駅から環状道路までの最小距離が含まれます。 

機能する理由: グラフにはサイクルが 1 つだけあるため、DFS は一意のサイクルの識別を保証します。 BFS は、サイクルからの距離を層内で外側に伝播します。すべてのエッジの重みが等しいため、最初にノードにアクセスするときは、サイクルへの最短パスを経由します。 

## Python ソリューション```python
import sys
from collections import deque

input = sys.stdin.readline
sys.setrecursionlimit(10000)

n = int(input())
adj = [[] for _ in range(n)]
for _ in range(n):
    u, v = map(int, input().split())
    adj[u - 1].append(v - 1)
    adj[v - 1].append(u - 1)

visited = [False] * n
parent = [-1] * n
cycle_nodes = []

def dfs(u, p):
    visited[u] = True
    for v in adj[u]:
        if v == p:
            continue
        if visited[v]:
            # cycle detected, reconstruct path
            cycle_nodes.append(v)
            node = u
            while node != v:
                cycle_nodes.append(node)
                node = parent[node]
            return True
        parent[v] = u
        if dfs(v, u):
            return True
    return False

dfs(0, -1)

dist = [-1] * n
q = deque()
for node in cycle_nodes:
    dist[node] = 0
    q.append(node)

while q:
    u = q.popleft()
    for v in adj[u]:
        if dist[v] == -1:
            dist[v] = dist[u] + 1
            q.append(v)

print(' '.join(map(str, dist)))
```隣接リストは地下鉄を効率的に表します。 DFS 機能は、バック エッジが発生したときに親ノードを追跡してサイクルを再構築します。 BFS は、最初のサイクル ノードが距離 0 にある状態で、すべての距離が正しく伝播することを保証します。 ノードの再訪を避け、0 ベースのインデックスに変換することで 1 ベースの入力を処理するように注意が必要です。 

## 実用的な例

 ### サンプル入力 1```
4
1 3
4 3
4 2
1 2
```| ノード | DFS 親 | ノードを循環する | BFS キュー | 距離配列 |
 | --- | --- | --- | --- | --- |
 | 1 | - | 1、2、3、4 | 1、2、3、4 | 0,0,0,0 |

 すべてのノードはサイクルの一部であるため、BFS はそれ以上拡張されません。 予想どおり、それぞれの距離は 0 です。 

### カスタム入力 2```
6
1 2
2 3
3 1
3 4
4 5
5 6
```| ノード | DFS 親 | ノードを循環する | BFS キュー | 距離配列 |
 | --- | --- | --- | --- | --- |
 | 1 | - | 1、2、3 | 1、2、3 | 0,0,0,-1,-1,-1 |
 | 4 | 3 | | 4 | 1 |
 | 5 | 4 | | 5 | 2 |
 | 6 | 5 | | 6 | 3 |

 これは、BFS がサイクルに接続されたツリーに沿って距離を伝播することを示しています。 ノード 4 は 1 通路離れており、ノード 5 は 2 通路、ノード 6 は 3 通路です。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n) | 各ノードは、DFS で 1 回、BFS で 1 回アクセスされます。 各エッジは最大 2 回横断されます。 |
 | スペース | O(n) | 隣接リスト、親配列、訪問先配列、および BFS キューはすべて線形にスケールされます。 |

 _n ≤ 3000_ の場合、これは時間とメモリの制限内で快適に実行されます。 

## テストケース```python
import sys, io
def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    from collections import deque
    input = sys.stdin.readline
    sys.setrecursionlimit(10000)

    n = int(input())
    adj = [[] for _ in range(n)]
    for _ in range(n):
        u, v = map(int, input().split())
        adj[u - 1].append(v - 1)
        adj[v - 1].append(u - 1)

    visited = [False] * n
    parent = [-1] * n
    cycle_nodes = []

    def dfs(u, p):
        visited[u] = True
        for v in adj[u]:
            if v == p:
                continue
            if visited[v]:
                cycle_nodes.append(v)
                node = u
                while node != v:
                    cycle_nodes.append(node)
                    node = parent[node]
                return True
            parent[v] = u
            if dfs(v, u):
                return True
        return False

    dfs(0, -1)

    dist = [-1] * n
    q = deque()
    for node in cycle_nodes:
        dist[node] = 0
        q.append(node)

    while q:
        u = q.popleft()
        for v in adj[u]:
            if dist[v] == -1:
                dist[v] = dist[u] + 1
                q.append(v)

    return ' '.join(map(str, dist))

# Provided sample
assert run("4\n1 3\n4 3\n4 2\n1 2\n") == "0 0 0 0", "sample 1"

# Custom tests
assert run("6\n1 2\n2 3\n3 1\n3 4\n4 5\n5 6\n") == "0 0 0 1 2 3", "triangle with tail"
assert run("3\n1 2\n2 3\n3 1\n") == "
```
