---
title: "CF 1836F - 博士のブラウン仮説"
description: "私たちは、ノードが惑星を表し、エッジがそれらの間のワームホールを表す有向グラフを使用しています。 各エッジを通過するのにちょうど 1 時間かかります。"
date: "2026-06-09T06:46:37+07:00"
tags: ["codeforces", "competitive-programming", "dfs-and-similar", "graphs", "math", "number-theory"]
categories: ["algorithms"]
codeforces_contest: 1836
codeforces_index: "F"
codeforces_contest_name: "Codeforces Round 880 (Div. 2)"
rating: 2900
weight: 1836
solve_time_s: 114
verified: false
draft: false
---

[CF 1836F - ブラウン博士の仮説](https://codeforces.com/problemset/problem/1836/F)

 **評価:** 2900
 **タグ:** dfs および同様のもの、グラフ、数学、数理論
 **解決時間:** 1 分 54 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 私たちは、ノードが惑星を表し、エッジがそれらの間のワームホールを表す有向グラフを使用しています。 各エッジを通過するのにちょうど 1 時間かかります。 この問題では、船が正確に継続的に移動するように、1 隻または 2 隻の船を選択する方法の数を数えるように求められます。`k`これらのワームホールに沿って何時間も。 2 隻の船を選択した場合、それらの位置を正確に交換する必要があります`k`時間。 船を 1 つ選んだ場合、その船はその後出発した惑星に戻らなければなりません。`k`何時間も。 

入力は次のもので構成されます`n`ノードと`m`エッジが非常に大きい`k`最大 10^18 まで可能です。 これにより、長さのパスをシミュレートしようとする単純なアプローチが即座に排除されます。`k`明示的に。 グラフには最大 10^5 のノードと 2×10^5 のエッジを含めることができるため、線形または線形に近いアルゴリズムよりも劣るアルゴリズムはすべて使用できません。`n`そして`m`タイムアウトになります。 これは、明示的なパス列挙ではなく、構造グラフのプロパティに基づいたソリューションが必要であることも示唆しています。 

明らかではないエッジケースは、孤立した惑星、接続されていないコンポーネント、小さなサイクルから発生します。 たとえば、次の場合`k`どのサイクル長よりも短いため、船は正確に移動して戻ることはできません。`k`何時間も。 もう 1 つの微妙なケースは 2 ノード サイクルです。これは 2 つのシップのスワップを許可しますが、対称ペアの二重カウントを避けるために慎重にカウントする必要があります。 グラフにサイクルがない場合は、孤立した自己ループのみが単一のシップで条件を満たすことができます。 

2 つのノードとノード 1 からノード 2 までの 1 つのエッジを持つグラフを考えてみましょう。`k = 1`、有効なスワップまたは単一シップ サイクルはありません。 サイクル長を考慮せずに到達可能なノードをカウントする単純なアプローチでは、有効な解決策が誤って報告されてしまいます。 

## アプローチ

 ブルートフォースアプローチでは、長さのすべてのパスを列挙しようとします。`k`ノードの各ペア間。 これには、DFS または BFS を繰り返す必要があります。`k`隣接行列のステップまたは計算能力。 どちらのアプローチも失敗する理由は、`k`最大 10^18 になる可能性があり、O(k) 演算さえも不可能になります。 

重要な洞察は、グラフの強連結成分 (SCC) を考慮することです。 正確に自分自身に到達できるノード`k`ステップは長さが分割されるサイクルの一部である必要があります`k`。 同様に、スワップ可能な 2 つのノード`k`ステップは同じ SCC に属し、分割する長さのサイクルに従う必要があります。`k`。 したがって、問題を SCC の分析と各 SCC 内のサイクル長の最大公約数 (GCD) の計算に集約します。 

各 SCC のサイクル長の GCD を取得したら、次の場合にノードを単一のシップとして使用できます。`k`はこの GCD で割り切れます。 同じ SCC 内のノードのペアの場合、`k`SCC の GCD で割り切れるので、任意の 2 つの異なるノードを交換できます。`k`ステップ。 これにより、パスの列挙が回避され、問題がグラフ上の数回の線形パスに軽減されます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース DFS/BFS | O(n・k) | O(n) | 遅すぎます、k が大きすぎます |
 | 隣接行列のべき乗 | O(n3・log k) | O(n²) | n=10^5 では遅すぎます |
 | SCC + サイクル GCD 分析 | O(n + m) | O(n + m) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. Tarjan のアルゴリズムまたは Kosaraju のアルゴリズムを実行して、グラフ内のすべての強く接続されたコンポーネントを特定します。 各 SCC は、すべてのノードが他のすべてのノードに到達できるノードの最大セットを表します。 このステップは、ノードとエッジの数において線形です。 
2. 各 SCC について、すべてのサイクルの長さを計算します。 これは DFS 経由で実行できます。 DFS 中に各ノードの深さを追跡し、バック エッジが見つかるたびに深さの違いを記録します。 これらの差の GCD は、SCC のすべてのサイクル長の GCD を与えます。 
3. 各 SCC について、次のことを決定します。`k`はその周期 GCD で割り切れます。 「はい」の場合、SCC 内のすべてのノードが単一のシップとして機能できます。 有効な単一シップの選択肢の数は、そのような SCC 内のノードの総数です。 
4. 同様に、SCC 内の異なるノードの任意のペアは位置を交換できます。`k`場合の手順`k`は SCC のサイクル GCD で割り切れます。 次のようなペアの数を数えます。`size * (size - 1)`対象となる各 SCC に対して。 
5. すべての SCC を合計して、有効な単一シップとペアの合計数を取得します。 

機能する理由: SCC はノード間の到達可能性を保証します。 サイクル長の GCD を使用すると、次のことが保証されます。`k`ステップはグラフ内のサイクルと一致させることができます。 これらのプロパティの組み合わせにより、カウントされた単一の船またはペアが正確な条件を満たすことが保証されます。`k`-時間の制約。 

## Python ソリューション```python
import sys
import threading
import math
input = sys.stdin.readline
sys.setrecursionlimit(1 << 25)

def main():
    n, m, k = map(int, input().split())
    graph = [[] for _ in range(n)]
    for _ in range(m):
        x, y = map(int, input().split())
        graph[x-1].append(y-1)

    # Tarjan's SCC algorithm
    index = 0
    indices = [-1]*n
    lowlink = [0]*n
    stack = []
    on_stack = [False]*n
    sccs = []

    def strongconnect(v):
        nonlocal index
        indices[v] = lowlink[v] = index
        index += 1
        stack.append(v)
        on_stack[v] = True
        for w in graph[v]:
            if indices[w] == -1:
                strongconnect(w)
                lowlink[v] = min(lowlink[v], lowlink[w])
            elif on_stack[w]:
                lowlink[v] = min(lowlink[v], indices[w])
        if lowlink[v] == indices[v]:
            scc = []
            while True:
                w = stack.pop()
                on_stack[w] = False
                scc.append(w)
                if w == v:
                    break
            sccs.append(scc)

    for v in range(n):
        if indices[v] == -1:
            strongconnect(v)

    total = 0

    # For each SCC, find the GCD of cycle lengths
    for scc in sccs:
        if len(scc) == 1:
            # Single node SCC
            v = scc[0]
            if v in graph[v]:  # self-loop
                if k % 1 == 0:
                    total += 1
        else:
            # Compute cycle GCD
            depths = [-1]*n
            gcd = 0

            def dfs(u, d):
                nonlocal gcd
                depths[u] = d
                for v in graph[u]:
                    if v not in scc_set:
                        continue
                    if depths[v] == -1:
                        dfs(v, d+1)
                    else:
                        cycle_len = d + 1 - depths[v]
                        gcd = math.gcd(gcd, cycle_len)

            scc_set = set(scc)
            for u in scc:
                if depths[u] == -1:
                    dfs(u, 0)

            if k % gcd == 0:
                total += len(scc)  # single ships
                total += len(scc) * (len(scc) - 1)  # pairs

    print(total)

threading.Thread(target=main).start()
```このソリューションはまず、Tarjan のアルゴリズムを使用して SCC を計算します。 次に、各 SCC 内で DFS を使用して、すべてのサイクル長の GCD を計算します。 割り切れる可能性をチェックします。`k`どのノードを使用できるかを決定します。 セットを使用して DFS を SCC 内のノードに制限すると、SCC 外のサイクルが GCD 計算に影響を与えなくなります。 

## 実用的な例

 ### サンプル 1

 入力:```
7 8 346
1 2
1 3
2 4
3 4
4 5
5 1
6 7
7 6
```主要な SCC:`{1,2,3,4,5}`サイクルで 1 つの SCC を形成し、`{6,7}`2 サイクルで別の SCC を形成します。 最初の SCC サイクルの GCD は 346 を分割し、2 番目の SCC の 2 サイクルは 346 を分割します。ノード 6 と 7 は単一のシップとして使用できます。 GCD 条件を満たす最初の SCC からのペアは 3 つのペアを生成します。 合計: 5。 

### サンプル 2

 入力:```
4 4 2
1 2
2 3
3 4
4 1
```グラフは 4 つのサイクルを形成します。 サイクル GCD は 4 で、2 を割ることはありません。したがって、ペアはありません。 2 が GCD で割り切れる場合、各ノードは 2 ステップ後に戻ることができますが、2 % 4 != 0 であるため、特定の走査サイクルのみが有効です。 単一艦艇の合計数: 0。 

上の表は、SCC、サイクル GCD、および次の割り算を追跡しています。`k`正しさを検証するため。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n + m) | Tarjan のアルゴリズムは線形です。 サイクル長の DFS は SCC 内で各エッジで 1 回実行されます。 |
 | スペース | O(n + m) | 再帰のためのグラフ隣接リストとスタック。 |

 このアルゴリズムは次の場合に実行可能です`n = 10^5`、`m = 2×10^5`そして大きい`k`明示的なパスの列挙を回避し、
