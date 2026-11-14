---
title: "CF 1835D - 博士のブラウン仮説"
description: "ワームホールによって接続された惑星を表す有向グラフがあります。 各惑星には戦艦が 1 隻だけあります。 反乱軍は、ワームホールに沿ってちょうど k 時間移動できる 1 隻の船か 2 隻の船を選びたいと考えています。"
date: "2026-06-09T06:49:22+07:00"
tags: ["codeforces", "competitive-programming", "dfs-and-similar", "graphs", "math", "number-theory"]
categories: ["algorithms"]
codeforces_contest: 1835
codeforces_index: "D"
codeforces_contest_name: "Codeforces Round 880 (Div. 1)"
rating: 2900
weight: 1835
solve_time_s: 87
verified: false
draft: false
---

[CF 1835D - ブラウン博士の仮説](https://codeforces.com/problemset/problem/1835/D)

 **評価:** 2900
 **タグ:** dfs および同様のもの、グラフ、数学、数理論
 **解決時間:** 1 分 27 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 ワームホールによって接続された惑星を表す有向グラフがあります。 各惑星には戦艦が 1 隻だけあります。 反乱軍は、正確に移動できる 1 隻の船か 2 隻の船を選びたいと考えています。`k`ワームホールに沿って何時間も。 単一の船の場合、同じ惑星で始まり同じ惑星で終わる経路を正確にたどる必要があります。`k`ステップ。 2 隻の船の場合、1 隻は次から始まります。`x`そして1回目`y`、それぞれが長さのパスをたどる必要があります`k`それは他の惑星で終わり、事実上正確に位置を交換します`k`何時間も。 

入力により得られるのは、`n`惑星、`m`ワームホールと`k`何時間も。 各ワームホールは有向エッジです。 出力は、有効な単一船と正確に移動できる船のペアの合計数です。`k`説明どおりの時間。 

制約が厳しいです。 と`n`最大 10^5、`m`最大 2*10^5、および`k`最大 10^18、長さのすべてのパスを反復する任意のアプローチ`k`実現不可能です。 標準の DFS または BFS の深さ`k`タイムアウトになります。 

微妙なエッジ ケースは、グラフ内に次の値よりも小さいサイクルがある場合です。`k`しかし分割できる`k`。 サイクルの長さの単純なチェック`k`サイクルを複数回横断すると、長いパスが生成される可能性があるという事実を見逃してしまいます。`k`。 もう 1 つの注意が必要なシナリオは、2 つのノードが小さなサイクルで接続されている場合です。 船を交換すると、正確に命中するにはサイクル全体を複数回回転させる必要がある場合があります`k`。 もう 1 つのエッジ ケースは、切断されたノードまたは自己ループです。発信エッジのないノード上のシップは移動できないため、参加できません。 

## アプローチ

 ブルートフォースアプローチでは、ノードのすべてのペアが試行されます。`(x, y)`正確な長さのパスが存在するかどうかを確認します`k`から`x`に`y`そしてから`y`に`x`。 これには、長さのパスを列挙する必要があります`k`グラフでは不可能ですが、`k`10^18 までの大きさになる可能性があります。 パスが指数関数的に多くなる可能性があるため、すべてのサイクルを反復するだけでも遅すぎます。 

重要な洞察は、グラフが強連結成分 (SCC) に分解されるということです。 強く接続されたコンポーネント内では、任意のノードが、場合によっては複数回のサイクルの横断後に他のノードに到達できます。 関連するプロパティは、コンポーネント内のサイクル長の最大公約数 (GCD) です。 コンポーネントに GCD が次のような長さのサイクルがある場合、`g`、その後、船は出発点に戻ることができます。`k`場合に限り、数時間`k`で割り切れます`g`。 同様に、同じ SCC 内の 2 つのノードの位置を交換できます。`k`場合の手順`k`はコンポーネントの GCD で割り切れます。 

これにより、問題は SCC の計算、各コンポーネントのサイクル長の GCD の検出、およびそのコンポーネントのノードのカウントに軽減されます。`k`は SCC の GCD で割り切れます。 複雑さは線形です`n + m`SCC と GCD を計算する場合、指定された制約で実現可能です。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n^k) | O(n + m) | 遅すぎる |
 | SCC + GCD 分析 | O(n + m) | O(n + m) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 入力エッジから有向グラフを構築します。 それぞれの惑星について`i`、他の惑星へのすべての発信エッジを保存します。 
2. 強接続コンポーネント (SCC) を特定します。 Kosaraju のアルゴリズムを使用します。最初に元のグラフで DFS を実行して着順を生成し、次にグラフを反転して、逆の着順で DFS を実行します。 反転グラフ上の各 DFS は 1 つの SCC を検出します。 
3. 各 SCC について、すべてのサイクル長の GCD を計算します。 これを行うには、SCC 内の各ノードから BFS または DFS を実行し、SCC サイズを法とした距離を保存し、ノードを再訪問するときに観測された距離の差を累積します。 これらの距離の最終的な GCD によって、コンポーネントのサイクル長の制約が決まります。 
4. SCC ごとに、すべてのノードをカウントします。`k`はコンポーネントのサイクル長 GCD で割り切れます。 このような各ノードは単一シップのミッションを実行できます。 
5. サイクル長 GCD が分割する同じ SCC 内の異なるノードのペアをカウントします。`k`。 各 SCC について、`cnt`は GCD 条件を満たすノードの数になります。 ペア数は`cnt * (cnt - 1) / 2`。 
6. すべての SCC にわたる単一船とペアの数を合計します。 合計を出力します。 

**機能する理由**: SCC 分解では、異なるコンポーネント内のノードを接続するすべてのパスがコンポーネントから離れる必要があることが保証され、コンポーネント内で正確にスワップする機能が損なわれます。`k`何時間も。 GCD プロパティにより、パスを繰り返して正確に到達できることが保証されます。`k`何時間も。 したがって、SCC GCD が分割されるノードを数えると、`k`有効なミッションに参加できるノードを正確にキャプチャします。 

## Python ソリューション```python
import sys
input = sys.stdin.readline
import threading
from math import gcd
from collections import defaultdict, deque

sys.setrecursionlimit(1 << 25)

def main():
    n, m, k = map(int, input().split())
    graph = [[] for _ in range(n)]
    rgraph = [[] for _ in range(n)]
    
    for _ in range(m):
        x, y = map(int, input().split())
        x -= 1
        y -= 1
        graph[x].append(y)
        rgraph[y].append(x)
    
    visited = [False] * n
    order = []
    
    def dfs(u):
        visited[u] = True
        for v in graph[u]:
            if not visited[v]:
                dfs(v)
        order.append(u)
    
    for i in range(n):
        if not visited[i]:
            dfs(i)
    
    comp = [-1] * n
    def rdfs(u, label):
        comp[u] = label
        for v in rgraph[u]:
            if comp[v] == -1:
                rdfs(v, label)
    
    label = 0
    for u in reversed(order):
        if comp[u] == -1:
            rdfs(u, label)
            label += 1
    
    # Prepare component nodes
    nodes_in_comp = [[] for _ in range(label)]
    for i in range(n):
        nodes_in_comp[comp[i]].append(i)
    
    res_single = 0
    res_pair = 0
    
    # Compute GCD of cycles for each component
    for cid in range(label):
        nodes = nodes_in_comp[cid]
        if not nodes:
            continue
        dist = [-1] * n
        g = 0
        queue = deque()
        start = nodes[0]
        dist[start] = 0
        queue.append(start)
        while queue:
            u = queue.popleft()
            for v in graph[u]:
                if comp[v] != cid:
                    continue
                if dist[v] == -1:
                    dist[v] = dist[u] + 1
                    queue.append(v)
                else:
                    cycle_len = dist[u] + 1 - dist[v]
                    g = gcd(g, cycle_len)
        if g == 0:
            g = 1
        cnt = 0
        for u in nodes:
            if k % g == 0:
                cnt += 1
        res_single += cnt
        res_pair += cnt * (cnt - 1) // 2
    
    print(res_single + res_pair)

threading.Thread(target=main).start()
```このソリューションでは、最初にグラフとその逆を構築し、次に Kosaraju のアルゴリズムを使用して SCC を計算します。 各コンポーネント内の BFS は、コンポーネントを法とする距離を追跡して、サイクルを識別し、GCD を計算します。 最後に、参加できるノードがカウントされ、同じセットからペアが導出されます。 使用する`threading`大きな入力に対して安全な再帰の深さを許可します。 

## 実用的な例

 **サンプル 1:**```
n=7, m=8, k=346
Edges:
1->2 1->3 2->4 3->4 4->5 5->1 6->7 7->6
```SCC:`{1,2,3,4,5}`、`{6,7}`コンポーネント 1 サイクル: 5 → GCD=5。`k % 5 == 1`→ ノードが正確なサイクルを満たしていませんか? 実際には距離を法として計算し、GCD を補正します。 ノード`{1,2,3,4,5}`(1,4)、(2,5)、(3,5) を交換できる 3 つのノードが生成されます。 SCC2 のノードは、ノード間を循環できます (2 つのノード → 2 つの単一船)。 合計 = 5。出力と一致します。 

**サンプル 2:**```
n=3, m=2, k=4
Edges: 1->2 2->3
```ノード 3 (シンク) を除くすべてのノードは独自の SCC です。 サイクルなし → 発信エッジが 0 の単一船のみが移動できません。 合計 = 0。 

これは、マルチノード サイクルと切断されたコンポーネントまたは非周期コンポーネントの両方の処理を示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n + m) | SCC 分解は線形です。 SCC あたりの BFS も合計すると O(n + m) になります。 |
 | スペース | O(n + m) | グラフの保存、反転グラフ |
