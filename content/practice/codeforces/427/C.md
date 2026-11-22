---
title: "CF 427C - チェックポスト"
description: "各ノードが都市の交差点を表し、各有向エッジが一方通行の道路を表す有向グラフが与えられます。 各交差点には警察の検問所を建設する費用がかかります。"
date: "2026-05-29T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "dfs-and-similar", "graphs", "two-pointers"]
categories: ["algorithms"]
codeforces_contest: 427
codeforces_index: "C"
codeforces_contest_name: "Codeforces Round 244 (Div. 2)"
rating: 1700
weight: 427
solve_time_s: 94
verified: true
draft: false
---

[CF 427C - チェックポスト](https://codeforces.com/problemset/problem/427/C)

 **評価:** 1700
 **タグ:** dfs および同様のもの、グラフ、2 つのポインター
 **解決時間:** 1 分 34 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 各ノードが都市の交差点を表し、各有向エッジが一方通行の道路を表す有向グラフが与えられます。 各交差点には警察の検問所を建設する費用がかかります。 ジャンクション _i_ のチェックポストは、_i_ 自体を含め、_i_ から到達可能で _i_ に戻ることができるすべてのジャンクションを保護します。 言い換えれば、ノードのチェックポストはその強接続コンポーネント (SCC) を保護します。 目標は、総コストが最小になるようにチェックポストを配置してすべてのノードをカバーし、さまざまな最小コストの配置がいくつ存在するかを数えることです。 

入力サイズでは、最大 100,000 個のジャンクションと 300,000 個の道路が可能です。 サブセットの数が指数関数的に増加するため、ジャンクションのすべての組み合わせを試す総当りアプローチは明らかに実行不可能です。 ノードとエッジに関して線形または線形に近いアプローチが必要です。これは、グラフ走査と SCC 分解手法が適切であることを示唆しています。 

SCC に複数のノードが含まれる場合、微妙なエッジ ケースが発生します。 たとえば、ノード 2 と 3 がサイクルを形成している場合、両方とも相互に到達可能です。 この SCC 内に必要なチェックポストは 1 つだけです。 個々のノードを独立して考慮する不注意なアプローチでは、複数の冗長なチェックポストが配置され、コストが膨らむ可能性があります。 もう 1 つのエッジ ケースは、SCC 内の複数のノードの最小コストが同じである場合です。その場合、最も安価なノードを選択する方法の数が倍増します。 

## アプローチ

 単純なアプローチでは、すべてのノードを独立して考慮します。 すべてのジャンクションを反復処理し、そこにチェックポストを配置して、すべてのノードが到達可能性によってカバーされているかどうかをテストすることを想像できます。 このアプローチはすべての組み合わせをチェックするため理論的には正しいですが、実際にはノードのすべてのサブセットを列挙する必要があるため、複雑さが生じます。$O(2^n)$- 不可能です$n$100,000まで。 

重要な洞察は、保護関係が強く接続されたコンポーネントを正確に反映しているということです。 任意の SCC は、各ノードが他のすべてのノードに到達できるノードの最大セットです。 したがって、各 SCC に 1 つのチェックポストを配置するだけで、その SCC 内のすべてのノードをカバーでき、それ以上のチェックポストは必要ありません。 SCC あたりの最小コストは、最も安価なノードのコストにすぎません。 その SCC でチェックポストを選択する方法の数は、その最小コストを共有するノードの数です。 

グラフを SCC に分解すると、問題は各 SCC を反復して最小コストを選択し、多重度をカウントすることに減ります。 SCC 分解は、Kosaraju または Tarjan のアルゴリズムを使用して線形時間で実行できます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(2^n) | O(n + m) | 遅すぎる |
 | 最適 (SCC + 最小コスト) | O(n + m) | O(n + m) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 入力を解析してノード数を抽出します。`n`、チェックポストのコスト、エッジの数`m`、および有向エッジのリスト。 
2. グラフの隣接リストを作成します。 
3. Kosaraju のアルゴリズムを実行して SCC を見つけます。 これには 2 つの DFS パスが含まれます。最初のパスでは、終了時間に従ってノードをスタックに埋めます。 2 番目のパスでは、転置グラフ上のスタック順にノードを処理して SCC を識別します。 
4. SCC ごとに、チェックポストの最小コストを決定します。 その最小コストを共有する SCC 内のノードの数を数えます。 
5. すべての SCC にわたる最小コストを合計して、最小支出の合計を取得します。 
6. 各 SCC の最小コスト ノードの数をモジュロで乗算します。$10^9 + 7$、最小コストを達成する方法の総数を取得します。 

機能する理由: 各 SCC は、相互に保護できる最大のノードのセットを形成します。 SCC 内の最も安価なノードにチェックポストを配置することで、そのすべてのノードを最小限のコストでカバーします。 SCC は保護の観点から独立しているため、最小コストの合計は SCC 全体の合計となります。 乗算カウントにより、各 SCC での独立した選択が考慮されます。 

## Python ソリューション```python
import sys
sys.setrecursionlimit(10**6)
input = sys.stdin.readline

MOD = 10**9 + 7

def kosaraju_scc(n, adj):
    visited = [False] * n
    order = []

    def dfs1(u):
        visited[u] = True
        for v in adj[u]:
            if not visited[v]:
                dfs1(v)
        order.append(u)

    for i in range(n):
        if not visited[i]:
            dfs1(i)

    # Transpose graph
    adj_t = [[] for _ in range(n)]
    for u in range(n):
        for v in adj[u]:
            adj_t[v].append(u)

    visited = [False] * n
    sccs = []

    def dfs2(u, comp):
        visited[u] = True
        comp.append(u)
        for v in adj_t[u]:
            if not visited[v]:
                dfs2(v, comp)

    for u in reversed(order):
        if not visited[u]:
            comp = []
            dfs2(u, comp)
            sccs.append(comp)

    return sccs

def main():
    n = int(input())
    costs = list(map(int, input().split()))
    m = int(input())
    adj = [[] for _ in range(n)]
    for _ in range(m):
        u, v = map(int, input().split())
        adj[u-1].append(v-1)

    sccs = kosaraju_scc(n, adj)
    total_cost = 0
    ways = 1

    for comp in sccs:
        min_cost = min(costs[u] for u in comp)
        count = sum(1 for u in comp if costs[u] == min_cost)
        total_cost += min_cost
        ways = (ways * count) % MOD

    print(total_cost, ways)

if __name__ == "__main__":
    main()
```コードはまずグラフを構築し、Kosaraju のアルゴリズムを実行して SCC を取得します。 各 SCC について、最も安価なノードを見つけて重複をカウントします。 最小値を合計すると総コストが得られます。 カウントを乗算するとウェイの数が得られます。 使用する`sys.setrecursionlimit`大きなグラフ上の DFS がクラッシュしないようにします。 

## 実用的な例

 **サンプル入力 1**```
3
1 2 3
3
1 2
2 3
3 2
```| ステップ | SCC が見つかりました | SCC あたりの最小コスト | SCC ごとの方法 | 累積総コスト | 累積的な方法 |
 | --- | --- | --- | --- | --- | --- |
 | 1 | [0]、[1,2] | 1、2 | 1,1 | 3 | 1 |

 グラフは単一ノード SCC [0] とサイクル [1,2] に分解されます。 最も安価なノードのコストはそれぞれ 1 と 2 です。 各 SCC で最小値を選択する方法は 1 つだけです。 

**カスタム入力 2**```
4
1 1 2 2
4
1 2
2 1
3 4
4 3
```| ステップ | SCC が見つかりました | SCC あたりの最小コスト | SCC ごとの方法 | 累積総コスト | 累積的な方法 |
 | --- | --- | --- | --- | --- | --- |
 | 1 | [0,1]、[2,3] | 1,2 | 2,2 | 3 | 4 |

 それぞれがサイクルを形成する 2 つの SCC。 最初の SCC の最小コスト ノード: ノード 0、1 は両方ともコスト 1 で、2 つの方法が与えられます。 2 番目の SCC: ノード 2、3 は両方ともコスト 2 で、2 つのウェイが得られます。 2*2 = 4 を掛けます。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n + m) | Kosaraju のアルゴリズムは線形時間で実行されます。 SCC ごとの合計とカウントの最小値は O(n) です。 |
 | スペース | O(n + m) | 隣接リスト、転置グラフ、訪問先配列、および SCC ストレージはすべて、n + m で線形にスケールされます。 |

 制約 (n ≤ 10^5、m ≤ 3・10^5) と線形時間計算量を考慮すると、この解は 2 秒の制限内に簡単に収まります。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    from contextlib import redirect_stdout
    out = io.StringIO()
    with redirect_stdout(out):
        main()
    return out.getvalue().strip()

# Provided sample
assert run("3\n1 2 3\n3\n1 2\n2 3\n3 2\n") == "3 1", "sample 1"

# Minimum-size input
assert run("1\n5\n0\n") == "5 1", "single node no edges"

# Two SCCs with multiple minimal nodes
assert run("4\n1 1 2 2\n4\n1 2\n2 1\n3 4\n4 3\n") == "3 4", "two cycles with duplicate minimums"

# All nodes same cost
assert run("3\n2 2 2\n2\n1 2\n2 3\n") == "4 1", "same cost, linear chain"

# Edge case: single cycle covering all nodes
assert run("3\n3 1 2\n3\n1 2\n2 3\n3 1\n") == "1 1", "full cycle"

# No edges, all nodes isolated
assert run("3\n1 2 3\n0\n") == "6 1", "isolated nodes"
```|
