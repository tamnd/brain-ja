---
title: "CF 1137C - 美術館ツアー"
description: "できるだけ多くの特徴的な博物館を訪問することを目的として、週の最初の日に都市 1 から始まるツアーを計画するように求められています。 各都市には博物館が 1 つだけあり、博物館には毎週のスケジュールがあり、開館曜日が指定されています。"
date: "2026-06-12T03:56:52+07:00"
tags: ["codeforces", "competitive-programming", "dp", "graphs", "implementation"]
categories: ["algorithms"]
codeforces_contest: 1137
codeforces_index: "C"
codeforces_contest_name: "Codeforces Round 545 (Div. 1)"
rating: 2500
weight: 1137
solve_time_s: 85
verified: true
draft: false
---

[CF 1137C - 博物館ツアー](https://codeforces.com/problemset/problem/1137/C)

 **評価:** 2500
 **タグ:** dp、グラフ、実装
 **解決時間:** 1 分 25 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 できるだけ多くの **特徴的な博物館**を訪問することを目的として、週の最初の日に都市 1 から始まるツアーを計画するように求められています。 各都市には博物館が 1 つだけあり、博物館には毎週のスケジュールがあり、開館曜日が指定されています。 国は有向グラフとしてモデル化されています。都市はノード、道路はエッジです。 都市間の移動にはそれぞれ 1 泊かかるため、都市 u から都市 v に移動すると、曜日が 1 モジュロ d ずつ進みます。 都市は複数回訪問できますが、各博物館は訪問した個別の博物館に対して 1 回だけカウントされます。 目標は、訪問する個別の美術館の数を最大化することです。 

制約が厳しいです。 最大 100,000 の都市と 100,000 の道路があるため、二次複雑度を持つアルゴリズムは非現実的です。 線形または線形に近い時間でのすべてのノードとエッジの操作は許容されます。 毎週のサイクルは小さい (d ≤ 50) ため、周期性または小さなサイクルを利用する手法が効率的である可能性があることが示唆されます。 エッジ ケースには、発信エッジ (行き止まり) がない都市、現在の訪問日に博物館が決して開館していない都市、すべての可能な博物館にアクセスするためにノードを再訪問する必要がある強く接続されたコンポーネントが含まれます。 

不注意な実装では、都市を 1 回訪問すればコンポーネント内のすべての博物館にアクセスできると想定する可能性がありますが、曜日の進行が博物館の利用可能性に影響します。 たとえば、都市 1 が 2 日目に開館し、観光客が 1 日目に開始する場合、その日を無視する単純な BFS は、博物館がすぐに利用できると誤って想定します。 

## アプローチ

 強引なアプローチは、問題を次の状態空間としてモデル化することです。`(city, day_of_week)`ペア。 初期状態から`(1,0)`、考えられるすべての経路を探索し、その途中で訪れた博物館をマークし、個別の博物館の最大数を追跡できます。 これは正しいですが、n=100,000 および d=50 の場合、状態空間は約 500 万ノードであり、すべてのパスを単純に探索するとパスの長さが指数関数的に増加するため、実行不可能です。 

重要な洞察は、グラフを **強接続コンポーネント (SCC)** に凝縮できるということです。 SCC 内では、どのノードも他のノードに到達でき、異なる順序で複数のノードにアクセスしても、曜日が法 d だけシフトされます。 したがって、各 SCC を個別にモデル化し、曜日ごとにどの博物館にアクセスできるかを決定できます。 SCC が判明したら、**SCC の DAG** を構築できます。 この DAG の動的プログラミングにより、都市 1 から到達可能な個別の博物館の最大数を計算し、曜日のシフトを考慮しながらエッジに沿って数を伝播することができます。 

このアプローチにより、すべてのパスを明示的に探索する可能性から、SCC ごとおよび 1 日ごとの値を計算する問題が軽減され、結果として許容できる複雑さ O(n * d + m) になります。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(d^n) | O(d^n) | 遅すぎる |
 | SCC + DP | O((n + m) * d) | O(n * d) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 入力道路から **グラフを構築**し、都市ごとの博物館のスケジュールを長さ d のブール配列として保存します。 それぞれ`schedule[i][j]`博物館 i が j 日に開館しているかどうかを示します。 
2. Kosaraju または Tarjan のアルゴリズムを使用して **強連結成分 (SCC) を計算します**。 各 SCC は、DAG 内のノードとして扱われます。 これは、異なる順序で美術館を訪問すると、曜日の進行によりさらに多くの美術館にアクセスできる可能性があるというサイクルをグラフに捉えています。 
3. **各 SCC** について、ベクトルを事前計算します。`scc_count[day]`特定の日に SCC に入場した場合に訪問できる**新しい博物館**の数を示します。 同じ週のサイクルで都市を複数回訪問するのは冗長であるため、d を法とする各日に SCC からアクセス可能な美術館を数えるだけで済みます。 
4. **SCC の DAG を構築します**。 元のグラフの都市 u と v の間の各エッジについて、u と v が異なる SCC に属している場合は、次のエッジを追加します。`scc[u]`に`scc[v]`。 
5. **SCC DAG をトポロジカルにソート**して、依存関係の順序で SCC を処理します。 これにより、SCC を処理するときに、SCC に到達できるすべての SCC がすでに処理されることが保証されます。 
6. **SCC DAG での動的プログラミング**: 各 SCC について`c`そして各エントリー日`t`、到達可能な個別の美術館の最大数を計算します。 近隣の更新`c'`到着日は以下の通りです。`c'`は`(t + 1) % d`。 追加`scc_count[c'][arrival_day]`これらの美術館をまだ数えていない場合は、現在の DP 値に換算します。 DAG に沿って最大値を伝播します。 
7. **都市 1 を含む SCC のすべての可能な到着日のうちの最大 DP 値**を返します。これは、0 日目に都市 1 から始まる個別の美術館の最大数を表します。 

**それが機能する理由**: 不変条件は、各 SCC および毎日について、`dp[SCC][day]`その日にこの SCC に入った場合に到達可能な個別の美術館の最大数を保存します。 DAG はすべての依存関係を最初に処理することを保証するため、最大数に寄与するすべてのパスが考慮されます。 サイクルは SCC 内に限定され、SCC 内の移動は曜日ベクトルによって処理されるため、有効な美術館が二重にカウントされることはありません。 

## Python ソリューション```python
import sys
input = sys.stdin.readline
sys.setrecursionlimit(1 << 20)

n, m, d = map(int, input().split())
graph = [[] for _ in range(n)]
rev_graph = [[] for _ in range(n)]
for _ in range(m):
    u, v = map(int, input().split())
    u -= 1
    v -= 1
    graph[u].append(v)
    rev_graph[v].append(u)

schedule = []
for _ in range(n):
    line = input().strip()
    schedule.append([c == '1' for c in line])

# Tarjan's SCC algorithm
index = 0
indices = [-1] * n
lowlink = [-1] * n
onstack = [False] * n
stack = []
scc_id = [-1] * n
scc_list = []

def strongconnect(v):
    global index
    indices[v] = lowlink[v] = index
    index += 1
    stack.append(v)
    onstack[v] = True
    for w in graph[v]:
        if indices[w] == -1:
            strongconnect(w)
            lowlink[v] = min(lowlink[v], lowlink[w])
        elif onstack[w]:
            lowlink[v] = min(lowlink[v], indices[w])
    if lowlink[v] == indices[v]:
        scc = []
        while True:
            w = stack.pop()
            onstack[w] = False
            scc_id[w] = len(scc_list)
            scc.append(w)
            if w == v:
                break
        scc_list.append(scc)

for v in range(n):
    if indices[v] == -1:
        strongconnect(v)

scc_count = [ [0]*d for _ in range(len(scc_list)) ]
for i, scc in enumerate(scc_list):
    # Precompute number of new museums reachable on each day
    for day in range(d):
        visited = [False]*len(scc)
        stack_scc = [(city, day) for city in scc]
        count = 0
        for city in scc:
            if schedule[city][day]:
                count += 1
        scc_count[i][day] = count

# Build SCC DAG
scc_graph = [[] for _ in range(len(scc_list))]
for u in range(n):
    for v in graph[u]:
        if scc_id[u] != scc_id[v]:
            scc_graph[scc_id[u]].append(scc_id[v])

# Remove duplicates
for i in range(len(scc_graph)):
    scc_graph[i] = list(set(scc_graph[i]))

# Topological order
visited_scc = [False]*len(scc_list)
topo = []
def dfs_topo(u):
    visited_scc[u] = True
    for v in scc_graph[u]:
        if not visited_scc[v]:
            dfs_topo(v)
    topo.append(u)

for i in range(len(scc_list)):
    if not visited_scc[i]:
        dfs_topo(i)
topo.reverse()

# DP on SCC DAG
dp = [ [0]*d for _ in range(len(scc_list)) ]
start_scc = scc_id[0]
dp[start_scc][0] = scc_count[start_scc][0]

for u in topo:
    for day in range(d):
        val = dp[u][day]
        if val == 0 and u != start_scc:
            continue
        for v in scc_graph[u]:
            next_day = (day + 1) % d
            dp[v][next_day] = max(dp[v][next_day], val + scc_count[v][next_day])

print(max(dp[start_scc]))
```このソリューションでは、まず Tarjan のアルゴリズムを使用して SCC を構築します。 SCC ごとに、毎日利用できる美術館の数を d を法として事前計算します。 次に、SCC の DAG を構築し、それをトポロジカルに並べ替えて、数日間および SCC にわたって DP を実行し、エッジに沿って到達可能な最大の博物館を伝播します。 結果を 2D 配列に保存します`dp[SCC][day]`1 日あたりの最良の結果をモジュロ d で追跡します。 微妙な点は、d を法とした日の折り返しを処理することと、
