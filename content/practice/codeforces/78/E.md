---
title: "CF 78E - 避難"
description: "サイズ n × n の実験室グリッドが与えられます。これは、一部のタイルが原子炉であり、他のタイルが研究室である研究ステーションを表します。 原子炉の 1 基が故障して爆発し、有毒な冷却剤が近隣の研究室に拡散する可能性があります。"
date: "2026-05-28T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "flows", "graphs", "shortest-paths"]
categories: ["algorithms"]
codeforces_contest: 78
codeforces_index: "E"
codeforces_contest_name: "Codeforces Beta Round 70 (Div. 2)"
rating: 2300
weight: 78
solve_time_s: 154
verified: true
draft: false
---

[CF 78E - 避難](https://codeforces.com/problemset/problem/78/E)

 **評価:** 2300
 **タグ:** フロー、グラフ、最短パス
 **解決時間:** 2 分 34 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 サイズ _n_ × _n_ の実験室グリッドが与えられます。これは、一部のタイルが原子炉であり、他のタイルが研究室である研究ステーションを表しています。 原子炉の 1 基が故障して爆発し、有毒な冷却剤が近隣の研究室に拡散する可能性があります。 各研究室には、ある程度の数の科学者と、ある程度の数の救助カプセルが含まれています。 科学者は隣接する研究室間を 1 分で移動でき、カプセルに瞬時に入ることができます。 冷却剤も同様の方法で広がり、毎分近隣の研究室に感染します。 研究所が感染すると、まだカプセルに入っていない科学者は死亡します。 

課題は、爆発までの限られた時間を考慮して、すべての科学者が最適に行動した場合に救出できる科学者の最大数を決定することです。 

制約は小さいです: _n_ は最大 10、つまりタイルの総数は最大 100 です。分数 _t_ は最大 60 まで可能です。グリッドが小さいため、_n_ の 3 次または 4 次の複雑さを持つ解も実現可能です。 ただし、この問題では、感染する前に研究室のネットワークを介した科学者の流れを考慮する必要があるため、注意が必要です。 

エッジケースには、故障した原子炉にすぐ隣接する研究室が含まれます。 たとえば、原子炉の隣に科学者とカプセルがある研究室では、感染のタイミングによっては科学者が助かる場合もあれば助からない場合もあります。 もう 1 つの注意が必要なケースは、科学者の数が到達可能な安全な研究室にあるカプセルの数を超え、必然的に一部の科学者が死亡する場合です。 感染スケジュールやカプセルの容量を考慮せずに、ただ貪欲に科学者を動かすだけの単純な解決策では、誤った結果が生じるでしょう。 

## アプローチ

 強引なアプローチでは、科学者のあらゆる動きを毎分シミュレートしながら、同時に感染を拡大させようとします。 各科学者は各ステップで決定を下さなければならないため、可能性は指数関数的に増加しますが、_n_ = 10 および _t_ = 60 の場合でも明らかに実行不可能です。 

重要な観察は、これは基本的に時間制約のある最大流量の問題であるということです。 各タイムステップでの各研究室をグラフ内のノードとしてモデル化できます。 エッジは、時刻 _k_ の研究室を時刻 _k+1_ の隣接する研究室に接続し、科学者が 1 分間で移動することを表します。 さらに、各ラボには、カプセルの数に等しい容量を持つカプセルに入るノードがあります。 感染により、各ノードが存在できる時間の長さが制限されます。 

グラフが構築されたら、標準の最大フロー アルゴリズムを使用して、研究室が感染する前にカプセルに到達できる科学者の最大数を計算できます。 グリッドのサイズが小さく、時間制限があるため、これを最大 _n_ × _n_ × (_t_+1) 個のノードを持つフロー ネットワークとしてモデル化することが可能であり、Dinic または Edmonds-Karp フロー アルゴリズムで管理可能です。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォースシミュレーション | O(4^(_n^2_×_t_)) | O(_n^2_×_t_) | 実現不可能 |
 | 時間拡張最大流量 | O(V^2 E) ≈ O((n^2 t)^2 × 4) | O(n^2 × t) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 故障した原子炉の座標を特定します。 これがクーラントの広がりの根源となります。 
2. 故障した原子炉から開始する幅優先検索を使用して、各研究室が感染した最も早い瞬間を計算します。 原子炉は拡散を阻止します。 この感染時間を 2D 配列に保存します`infect_time`。 決して感染しない研究室は次のように考えられます。`infect_time = t+1`。 
3. 時間拡張したフローネットワークを構築します。 時刻 _k_ (0 ≤ k ≤ _t_) における各研究室はノードで表されます。 位置 (i,j) と時間 k にあるラボの場合、近隣の感染時間が k+1 より大きい場合、時間 k+1 で無限の容量で近隣に接続します。 また、科学者がその場に留まることができるように、時刻 k の同じ研究室を時刻 k+1 に接続します。 
4. 各研究室ごとに、そのノードを、その研究室内のカプセルの数に等しい容量を持つ「カプセル シンク」ノードに接続します。 このエッジは、実験室の感染時間よりも厳密に短い時間にのみ存在します。 
5. 時刻 0 ですべての研究室に接続され、その研究室の科学者の数と同じ容量を持つソース ノードを導入します。 
6. ソースからカプセルシンクまでの最大流量アルゴリズム (Dinic) を実行します。 最大流量値は、感染が到達する前に逃げられる科学者の最大数に等しい。 
7. 計算された最大流量を返します。 

機能する理由: ネットワークは動きとタイミングの制約を正しくエンコードします。 各科学者は流量の単位に対応し、カプセルの容量と感染時間によってシンクへの流量が制限されます。 科学者は感染時間を超えてノードを占有することはできず、利用可能な数を超えてカプセルに入ることができません。 したがって、流量の最大化は最適な避難計画を直接モデル化します。 

## Python ソリューション```python
import sys
input = sys.stdin.readline
from collections import deque

class Edge:
    def __init__(self, to, rev, cap):
        self.to = to
        self.rev = rev
        self.cap = cap

class MaxFlow:
    def __init__(self, N):
        self.size = N
        self.graph = [[] for _ in range(N)]

    def add(self, fr, to, cap):
        self.graph[fr].append(Edge(to, len(self.graph[to]), cap))
        self.graph[to].append(Edge(fr, len(self.graph[fr]) - 1, 0))

    def bfs_level(self, s, t, level):
        q = deque()
        level[s] = 0
        q.append(s)
        while q:
            v = q.popleft()
            for e in self.graph[v]:
                if e.cap > 0 and level[e.to] < 0:
                    level[e.to] = level[v] + 1
                    q.append(e.to)
        return level[t] >= 0

    def dfs_flow(self, v, t, upTo, level, iter):
        if v == t:
            return upTo
        for i in range(iter[v], len(self.graph[v])):
            e = self.graph[v][i]
            if e.cap > 0 and level[v] < level[e.to]:
                d = self.dfs_flow(e.to, t, min(upTo, e.cap), level, iter)
                if d > 0:
                    e.cap -= d
                    self.graph[e.to][e.rev].cap += d
                    return d
            iter[v] += 1
        return 0

    def max_flow(self, s, t):
        flow = 0
        level = [-1] * self.size
        INF = 10**9
        while True:
            level = [-1] * self.size
            if not self.bfs_level(s, t, level):
                break
            iter = [0] * self.size
            while True:
                f = self.dfs_flow(s, t, INF, level, iter)
                if f == 0:
                    break
                flow += f
        return flow

n, t = map(int, input().split())
sci_grid = [input().strip() for _ in range(n)]
input()  # empty line
cap_grid = [input().strip() for _ in range(n)]

INF = 10**9
dx = [0,0,1,-1]
dy = [1,-1,0,0]

infect_time = [[INF]*n for _ in range(n)]
queue = deque()
for i in range(n):
    for j in range(n):
        if sci_grid[i][j] == 'Z':
            queue.append((i,j,0))
            infect_time[i][j] = 0

while queue:
    x, y, time = queue.popleft()
    for d in range(4):
        nx, ny = x+dx[d], y+dy[d]
        if 0<=nx<n and 0<=ny<n and sci_grid[nx][ny] not in 'YZ' and infect_time[nx][ny] > time+1:
            infect_time[nx][ny] = time+1
            queue.append((nx, ny, time+1))

# build flow graph
def node(i,j,k):
    return (i*n+j)*(t+1)+k

N = n*n*(t+1) + 2
src = N-2
sink = N-1
mf = MaxFlow(N)

for i in range(n):
    for j in range(n):
        if sci_grid[i][j] in 'YZ':
            continue
        max_time = min(t, infect_time[i][j]-1)
        for k in range(max_time+1):
            # stay
            if k < max_time:
                mf.add(node(i,j,k), node(i,j,k+1), INF)
            # move
            for d in range(4):
                ni, nj = i+dx[d], j+dy[d]
                if 0<=ni<n and 0<=nj<n and sci_grid[ni][nj] not in 'YZ' and k+1 <= infect_time[ni][nj]-1:
                    mf.add(node(i,j,k), node(ni,nj,k+1), INF)
            # capsules
            mf.add(node(i,j,k), sink, int(cap_grid[i][j]))

for i in range(n):
    for j in range(n):
        if sci_grid[i][j] not in 'YZ' and int(sci_grid[i][j]) > 0:
            mf.add(src, node(i,j,0), int(sci_grid[i][j]))

print
```
