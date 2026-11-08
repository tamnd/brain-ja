---
title: "CF 1517G - 星空キャンプ"
description: "各テントは整数座標に配置され、正の重みを持ちます。 テントのサブセットは撤去させていただく場合がございます。 目標は、残ったテントの総重量を最大化することです。 この制限は重要なテント、つまり座標が両方とも偶数であるテントにのみ適用されます。"
date: "2026-06-10T18:20:42+07:00"
tags: ["codeforces", "competitive-programming", "constructive-algorithms", "flows", "graphs"]
categories: ["algorithms"]
codeforces_contest: 1517
codeforces_index: "G"
codeforces_contest_name: "Contest 2050 and Codeforces Round 718 (Div. 1 + Div. 2)"
rating: 3300
weight: 1517
solve_time_s: 130
verified: true
draft: false
---

[CF 1517G - 星空の夜のキャンプ](https://codeforces.com/problemset/problem/1517/G)

 **評価:** 3300
 **タグ:** 建設的なアルゴリズム、フロー、グラフ
 **解決時間:** 2 分 10 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 各テントは整数座標に配置され、正の重みを持ちます。 テントのサブセットは撤去させていただく場合がございます。 目標は、残ったテントの総重量を最大化することです。 

この制限は重要なテント、つまり座標が両方とも偶数であるテントにのみ適用されます。 

\((x,y)\) にある重要なテントについて考えてみましょう。 周囲には最大でも座標が違うテントばかり\(1\)各方向で禁止された構成に参加できます。 すべての座標は整数であるため、その中で可能な点はすべて\(3\times3\)近所は$$
(x,y),\ (x\pm1,y),\ (x,y\pm1),\ (x\pm1,y\pm1).
$$4 つのテントが水平辺を持つ平行四辺形を形成するという条件は、最初に見えるよりもはるかに制限的です。 あらゆる可能性を検討した結果、すべての禁止された構成は、4 つのパリティ クラスを占有するちょうど 4 つのテントで構成されています。$$
(\text{even},\text{even}),
(\text{odd},\text{even}),
(\text{odd},\text{odd}),
(\text{even}, tent is a weighted point on the integer grid. We may\text{odd}).
$$さらに、4 つのテントはすべてチェビシェフの距離内になければなりません\(1\)1 つはテントのサブセットを削除し、もう 1 つは残りのテントの総重量を求めます。 重要なテントは常に \((\text{even},\text{even})\) の頂点をできるだけ大きくすることです。 

この制限は重要なテント、つまり座標が両方とも偶数であるテントにのみ適用されます。 残っているすべての重要なテントには、そのような単位の平行四辺形が必要です。 citeturn0search0turn0search3 その周りの特定のローカル構成を避ける必要があります。 禁断の

 制約\(n \le 1000\)が重要な観察です。 構成上の二次スキャンは、その重要なテントと近くの 3 つのテントで構成され、すべてが 1 つのエリア内に配置されています。\(3 \ all pairs of tents is perfectly feasible, since \(10^6\)ペアは、重要なテントを中心とする 3 倍の近傍をチェックし、4 つのテントが小さな平行四辺形を形成するようにします。 禁止された 4 倍体の 3 次または 4 次の列挙は、その水平辺がすでに\(x\)-軸。 

入力サイズは \(n \le 1000\ 不快です。問題の評価とタグは、実際のタスクが構成を検出していないことを強く示唆しています) ですが、座標自体は巨大です。 これは、ジオメトリが制限をグラフ最適化問題に変換していることを直ちに示唆しています。 

微妙なエッジケースは、座標値が問題ではないということです。 既存のテント間の相対的な位置のみが重要です。 テントは同時に多くの禁止された構成に参加する可能性があるため。 

例：```text
5
 \(n\) is small, an \(O(n^2)\) construction is perfectly acceptable0 0 100
1 0 1
1 1 1
0 1 1
2 0 1
, while anything resembling enumeration of subsets is impossible.

The first subtle point is```\((0,0)\) のテントは、禁止パターンがローカルなものに属します。 テントは、各座標で最大 1 つの距離にあるテントとのみ対話できます。 2つ目の微妙な点は、禁止パターンです。 関係する 4 つのテントのうち 1 つを撤去すると、違反が修正されます。 常に保持重量を最大化するという貪欲な戦略（通常は、検出された各パターンの最も軽いテントを削除する総重量を最小化すると考える方が簡単です）では、概念的に同じテントを誤って複数回削除し、テントが削除される可能性があります。 

よくある間違いは、四角形を直接検索することです。 この条件では、平行四辺形も許可されます。 たとえば、最適ではない答えが生成されます。 

もう 1 つの簡単な間違いは、長方形のみが重要であると仮定することです。```text
(0,0)  (1,0)
(1,1)  (2,1)
```。 

例：```text
4
0 0 10
1 0 10
0 1 10
1 1 10
These four points form a valid forbidden shape even though it is not axis-aligned.

Another easy mistake is to```これは長方形ですが、このステートメントでは水平辺を持つ一般的な平行四辺形も許可されています。 幸いなことに、重要なテントはすべて個別に扱ってください。 2つの禁止パターンがテントを共有する可能性があります。 すべての座標が統合され、すべての頂点が慎重に選択された 1 つのテントにとどまる必要がある場合に削除すると、距離内の多くの禁止パターンを一度に破壊できます。\(1\)、有効な禁止形状はすべて に対応しているため、問題はローカルではなくグローバルです。 

考慮する：```text
(0,0) weight 100
(1,0) same parity structure, so the graph reduction remains correct weight 1
(0,1) weight 1
(1,1) weight 1
```最適なソリューション。 

3 番目の落とし穴は、負の座標の処理です。 パリティは慎重に計算する必要があります。 

例：```text
4
 removes a weight-1 tent, not the important tent of weight 100.

## Approaches

A0 0 5
-1 0 5
-1 -1 5
0 -1 5
```4 つの総当たりビューは、構造を理解するのに役立ちます。 禁止されているすべての 4 倍数を列挙することができ、パリティ クラスは引き続き表示されます。 言語依存の負のモジュロ動作を誤って使用すると、頂点が分類され、間違ったグループを含まないテントの最大重みサブセットが求められる可能性があります。 

## アプローチ

 ブルートフォースの観点は単純です。 どのテントも保管されるか撤去されます。 私たちにはそれができました。 

ジオメトリはローカルであるため、禁止されているすべての 4 拍子を見つけることは難しくありません。 問題は、禁止されているすべてのクワドルプルを特定し、その後の収容を避ける最大重量のテントのサブセットを選択しようとすることのようです。 加重ヒッティングセット問題が得られます。つまり、完全に禁止された 4 回転になるようにテントを削除します。 

これは加重ヒッティングセットの問題になります。 それでも、すべての禁止された 4 つ組は少なくとも 1 つの頂点を失います。 一般に、その問題は禁止された 4 倍数が数百個だけであり、すべてのサブセットをチェックすることは不可能です。 と\(n=100 NP-hard, so a direct formulation does not help.

The key observation is that these0\)、検索スペースには以下が含まれます\(2^{1000}\)可能性。 

画期的な進歩は、4 倍体が任意ではないというジオメトリを調べることから生まれます。 

すべての格子点をその座標のパリティによって分類します。 

| クラス | 密接に。 

すべてのテントをその座標の同等性によって分類します。$$
A=(0,0),\quad
B=(1,0),\quad x parity | y parity |
|---|---|---|
| A | odd | odd |
| B | even | odd |
| C | even | even
C=(1,1),\quad
D=(0,1).
$$すべての禁止構成には、各クラスのテントが 1 つだけ含まれます。 |
 | D | 奇数 | 偶数 |

 肝心のテントはまさにクラスC。 

さあ、禁止されているものを調べてください さらに良いことに、これら 4 つのテントは常に鎖を形成しています$$
 parallelogram. Because all vertices lie within one unit of the important tent, the four vertices mustC \rightarrow B \rightarrow A \rightarrow D
$$ここで、連続するテントは 4 つのパリティ クラスを 1 回だけ占有します。 しかも連鎖的に出現する

 \ マンハッタンの距離\(1\)。 citeturn0search3turn0search0

 今再解釈してください[
 A \rightarrow B \rightarrow C \rightarrow D$$
where consecutive vertices are adjacent the task.
Keeping a tent gives us its weight. Removing a tent loses its weight. by Manhattan distance \(1\). Equivalently, every forbidden pattern corresponds to a path passing through one vertex of Let
$$\text{合計}=\合計 w_i。$$
Instead of maximizing kept weight, minimize the total weight of removed tents.
Every forbidden configuration requires that at least one of its four tents be removed. This is exactly the type of condition that of destroying forbidden quadruples, we must destroy all paths from class A to class D in this layered graph. minimum cut models naturally represent.
We build a directed graph whose infinite-capacity paths correspond to forbidden Each tent has a deletion cost equal to its weight. Destroying all such paths is exactly a minimum vertex configurations. Cutting a tent means paying its weight. Then the minimum \(s\)-\(t\) cut chooses the cheapest cut problem.
Minimum vertex cuts with vertex costs are standard. Split set of tents whose removal destroys every forbidden configuration.
The answer becomes
$$\text{すべてのテントをインノードとアウトノードに合計します。 テントの重量と同じ容量で接続してください。 このエッジをカットするということは、そのエッジを削除することを意味します} - \text{最小カット}。$$
This transforms a geometric optimization problem into a standard weighted vertex cut problem. citeturn0search3turn0search0
| Approach | Time Complexity | Space Complexity | Verdict |
|---|---|---|---|
| Brute Force subset selection | \(O(2^n)\) | \(O(n)\) | Too slow |
| Minimum Cut reduction | \(O(n^2 + \text{MaxFlow})\) | \(O(n^2)\) | Accepted |
## Algorithm Walkthrough
1. Split all tent.
All other graph edges receive infinite capacity. Then the minimum \( tents into four parity classes.
   Define
   $$type=(x\bmod2)+2(ys\)-\(t\)Cut は、削除によって破壊されるテントの最小総重量を選択します\bmod2)。$$
   After normalization, the four values every forbidden path. The answer is
$$\text{総重量} - \text{最小カット量}。$$
### Approach correspond to the four parity groups.
2. Create two vertices for every tent, \(i_{ Comparison
| Approach | Time Complexity | Space Complexity | Verdict |
|---|---|---|---|
| Brute Force over forbidden quadruples | Exin}\) and \(i_{out}\).
   Add an edge
   $$i_{in}\rightarrow i_{out}$$
  ponential after enumeration | Exponential | Too slow |
| Minimum vertex cut / maximum flow | \(O(n^ with capacity \(w_i\).
   Cutting this edge means removing tent \(i\), which2 \sqrt V)\) costs exactly its weight.
3. Connect the source to every class \(C=(1,1)\) tent with infinite capacity.
   These to \(O(n^3)\) depending on implementation | \(O(n^2)\) | Accepted |
## Algorithm Walkthrough
1. Divide all tents into the four tents form the left side of the parity chain.
4. Connect every class \(D=(0,1)\) tent to the sink with infinite capacity.
   These tents form the right side of the parity chain.
5. For every pair of tents whose Chebyshev distance is at most \(1 parity classes \(A,B,C,D\).
2. Create two vertices for every tent, \(v_{in}\) and \(v_{out}\).
3. Add an edge \(v_{in} \to v_{out}\)\), add infinite-capacity edges between consecutive parity classes:
   $$C \右矢印 B、$$
   $$B \ テントの重量と同じ容量。 

この端を切断することは、テントを取り除くことを意味します。 

4.右矢印A、$$
   $$A \rightarrow D.$$
   More precisely, if tent \(u\) belongs to the earlier class Create a source \(S\) and sink \(T\).
5. Connect \(S\) to every class \(A\) tent with and tent \(v\) belongs to the next class, add
   $$u_{out}\rightarrow v_{in}$$
   with infinite capacity.
6. infinite capacity.
6. Connect every class \(D\) tent to \(T\) with infinite capacity.
7. For every pair of tents whose Run a maximum flow algorithm.
   By the max-flow min-cut theorem, the resulting minimum parity classes are consecutive in the order
   $$A \rightarrow B \rightarrow C \rightarrow D、$$
   add cut gives the minimum total weight of tents that must be removed.
7. Let the minimum cut value be \( an infinite-capacity edge from the first tent's out-node to the second tent's in-node if their Manhattan distance is exactly \(1\).
   Thesecut\).
   The optimal answer is
   $$\sum w_i - カット。$$
### Why it edges encode all possible forbidden-pattern transitions.
8. Run a maximum flow algorithm such as Dinic.
9. Let the works
Every forbidden configuration contains exactly one tent from each parity class and forms a complete chain
$$C \rightarrow B \rightarrow A \rightarrow D.$$
Because all maximum flow value be \(F\). By the max-flow min-cut theorem, \(F\) is the minimum connecting edges have infinite capacity, keeping all four tents would create an infinite-capacity path from the source to the total weight that must be removed.
10. Output
$$\text{すべての重みの合計} - F.```python
import sys
from collections import deque

input = sys.stdin.readline

INF = 10 ** 18

class Dinic:
    def __init__(self, n):
        self.n = n
        self path survives, then a forbidden configuration survives. If every \(A \to D\) path is destroyed, then every forbidden configuration is destroyed.

Thus the minimum vertex cut is exactly the minimum total weight of tents that must be.g = [[] for _ in range(n)]

    def add_edge(self, u, v, c):
        self.g[u].append([v, c, len(self.g[v])])
        self.g[v].append([u, 0, len(self.g[u]) - 1])

    def bfs(self, s, t):
        self.level = [-1] * self.n
        q = deque([s removed. Subtracting this value from the total weight yields the maximum weight that can remain.

## Python Solution

```パイソン```python

The vertex-splitting edge is the heart of the construction. Every tent receives exactly one finite-capacity edge, and its capacity equals the cost of deleting that tent. All structural edges use an effectively infinite capacity, making them impossible to cut in an optimal finite solution.

The parity classification must handle negative coordinates correctly. Using the normalized modulo expression avoids language-specific issues.

The pairwise scan checks Chebyshev distance rather + 1:
            continue

        xj, yj, _ = pts[j]

        if abs(xi - xj) + abs(yi - yj) == 1:
            dinic.add_edge(2 * i + 1, 2 * j, INF)

min_removed = dinic.max_flow(S, T)
print(total - min_removed)
```
```text
5
0 0 4
0 1 5
1 0 3
1 1 1
- and break the reduction.

All capacities fit safely in 64-bit integers. The total weight is at most \(10^{12}\), so1 1 2
```
```text
5
0 0 4
0 0) | 4 | A |
| 2 | (0,1) | 5 | D |
| 1 5
1 0 3
1 1 1
-1 1 2
```
```text
12
```
```text
4
0 0 10
1  cut | Answer |
|---|---|---|
| 15 | 10 1
1 1 1
0 1 100
```
```python and the layered structure.stdin = io.StringIO(inp)

    # is extremely friendly to Dinic. This paste solve() and supporting classes here

    out = io.StringIO()
    old = sys.stdout
    sys.stdout = out

    solve()

    sys.stdout = old
    return out.getvalue()

# sample
assert run(
 comfortably fits within the contest limits.

## Test Cases

```パイソン```

| Test input | Expected output |0 7
"""
) == "7\n"

# one complete forbidden pattern
assert run(
"""4
0 0 100
0 1 1
1 0  What it validates |
|---|---|---|
| Single tent | 7 | Minimum size instance |
| One forbidden quadruple | 111 | Minimum1
1 1 1
"""
) == "102\n"

# no forbidden pattern exists
assert run(
"""3
0 0 5
10 10 6
20 20 7
"""
) == "18\n"

-cut chooses cheapest vertex |
| Negative coordinates | 15 | Correct parity handling |
| Widely separated tents | 60 | No unnecessary removals |

## Edge Cases

Consider the negative-coordinate example:

# choose cheapest tent to remove
assert run(
"""4
0 0 1
0 1 100
1 0 100
1 1 100
"""
) == "300\n"
```
```

The four |
|---|---|---|
| Single tent | 7 | Minimum size |
| One forbidden pattern | 102 | Basic cut construction |
| Isolated tents | 18 | No path means no removal |
| Expensive neighbors | 300 | Minimum tents still occupy the four parity classes. The graph contains one source-to-sink chain. Any-weight vertex cut valid solution must remove one tent. The minimum cut value is \(5\), producing answer, not greedy geometry |

## Edge Cases

Consider:

```文章```

There is exactly one forbidden configuration. The graph contains one \(A \to B \to C \to D\) path. The minimum cut \(20-5=15\). Incorrect parity computation would miss removes any weight-1 tent. The answer is \(103 - 1 = 102\). A greedy strategy that always deletes the important tent would the chain entirely and return \(20\).

Consider a tent participating in multiple forbidden structures:

```文章```text
0 100
1 0 1
1 1 1
0 1 1
2 0 1
```
```

No pair of tents is adjacent,) appears in more than one potential interaction. The cut model does not process configurations independently. It globally chooses the cheapest set of removed tents, avoiding the double-counting mistakes that so the flow graph contains no \(A \to D\) path. The maximum flow is zero, the minimum cut is zero, and all tents remain. The answer is 18.

Finally, greedy local fixes can make.

Consider a configuration where the important tent is very heavy:

```文章```text
4
0 0 1000
1 0 1
1 1 1
0 1 0 0 5
0 1 2
-1
```
```

Parity classification must focus on removing important tents. The minimum cut instead removes one of the weight-\(1\) tents. The answer becomes \(1002\), which is optimal because only be computed correctly for negative values. Using `x & 1` and `y & 1` works for both positive and negative integers in Python. The graph still forms one forbidden path, and the algorithm removes one weight-2 tent, producing the correct answer 9.

$$
### sink. Any finite cut must break this path.
The only finite-capacity edges in the graph are the vertex-spl Why it works
Every forbidden configuration contains exactly oneitting edges with capacities equal to tent weights. Cutting one of those edges corresponds exactly to deleting that tent from each parity class and induces a path from class \(A\) to class \(D\) through consecutive parity layers tent.
Thus every valid solution corresponds to an \(s\)-\(t\). Conversely, every such layered path corresponds to a forbidden configuration. citeturn0search2 cut of the same cost, and every finite cut corresponds to removingturn0search3
An tents whose total weight equals the cut value. The minimum cut finds the cheapest set of removals that destroys every forbidden configuration. By infinite-capacity edge can never belong to an optimal cut, because cutting a finite-weight tent is complementing against the total weight, we obtain the maximum total weight that can remain. citeturn0search3turn0search0
## Python Solution
import sys
from collections import deque
input = sys.stdin.readline
INF = 10 **])
        self.level[s] = 0
        while q:
            u = q.popleft()
            for v, cap, rev 18
class Dinic:
    def __init__(self, n):
        self.n = n
        self.g = in self.g[u]:
                if cap > 0 and self.level[v] == -1:
                    self.level[v] = self.level[u] + 1
                    q.append [[] for _ in range(n)]
    def add_edge(self, u, v, c):
        self.g[u].append([v, c,(v)
        return self.level[t] != -1
    def dfs(self, u, t, f):
        if u == t:
            return f
        for len(self.g[v])])
        self.g[v].append([u, 0, len(self.g[u]) - 1])
    def bfs(self, i in range(self.it[u], len(self.g[u])):
            self.it[u] = i
            v, cap s, t):
        level = [-1] * self.n
        q = deque([s])
        level[s] = 0
        while q:
            u = q.popleft()
, rev = self.g[u][i]
            if cap == 0 or self.level[v] != self.level[u] + 1:
                continue
            for v, cap, rev in self.g[u]:
                if cap > 0 and level[v] == -1:
                    level[v] = level[u]            pushed = self.dfs(v, t, min(f, cap))
            if pushed:
 + 1
                    q.append(v)
        self.level = level
        return level[t] !=                self.g[u][i][1] -= pushed
                self.g[v][rev][1] += pushed
                return pushed
        return 0
    def maxflow(self, s, t):
        flow =  -1
    def dfs(self, u, t, f):
        if u == t:
            return f
        g_u = self.g[u]
        while0
        while self.bfs(s, t):
            self.it = [0] * self.n
            while True:
                pushed = self.dfs(s, self.it[u] < len(g_u):
            idx = self.it[u]
            v, cap, rev = t, INF)
                if not pushed:
                    break
                flow += pushed
        return flow
def parity_type(x, y):
    px g_u[idx]
            if cap > 0 and self.level[v] == self.level[u] + 1:
                pushed = self.dfs(v, t, min(f, = (x % 2 + 2) % 2
    py = (y % 2 + 2) % 2
    return px +  cap))
                if pushed:
                    g_u[idx][1] -= pushed
                    self.g[v][rev][1] += pushed
                    return pushed
            self.it[u] +=2 * py
def chebyshev(a, b):
    return max(abs(a[0] - b[0]), abs(a[1] - b[1]))
 1
        return 0
    def max_flow(self, s, t):
        flow = 0
        while self.bfs(s, t):
            self.it = [0] *def solve():
    n = int(input())
    x = [0] * n
    y = [0] * n
    w = [0] * n
 self.n
            while True:
                pushed = self.dfs(s, t, INF)
                if not pushed:
                    break
                flow += pushed
        return flow
    typ = [0] * n
    total = 0def parity_type(x, y):
   
    s = 0
    t = 2 * n + 1
    x &= 1
    y &=  dinic = Dinic(t + 1)
    for i in range(n):
        xi, yi, wi = map(int, input().split())
        x[i] = xi
       1
    if x == 1 and y == 1:
        return 0  # A
    if x == 0 and y == 1:
        return 1  y[i] = yi
        w[i] = wi
        total += wi
        typ[i] = parity_type # B
    if x == 0 and y == 0:
        return 2  # C
    return 3     (xi, yi)
        vin = i + 1
        vout = i + 1 + n
        dinic.add_edge(vin, vout, wi)
        if typ[i] == 3:
            dinic.add_edge(s, vin, INF)
        if typ[i] == 1:
 # D
n = int(input())
pts = []
total = 0
for _ in range(n):
    x, y, w = map(int, input().split())
    pts.append((x, y, w))
    total += w
S =             dinic.add_edge(vout, t, INF)
    for i in range(n):
        for j in range(n):
            if chebyshev((x[i], y[i]), (x[j], y[j])) > 1:
                continue
            if typ[i] == 3 and typ[j] == 2:
2 * n
T = 2 * n + 1
dinic = Dinic(2 * n + 2)
for i, (_, _, w) in enumerate(pts):
    dinic.add_edge(2 * i, 2 * i + 1, w)
                dinic.add_edge(i + 1 + n, j + 1, INF)
            if typ[i] == 2 and typ[j] == 0:
                dinic.addtypes = [parity_type(x, y) for x, y, _ in pts]
for i in range(n):
    if types[i] == 0:
        dinic.add_edge(i + 1 + n, j + 1, INF)
            if typ[i] == 0 and typ[j] == 1:
                dinic.add_edge(i + _edge(S, 2 * i, INF)
    if types[i] == 3:
        dinic.add_edge(2 * i + 1, T, INF)
1 + n, j + 1,for i in INF)
    cut = dinic.maxflow(s, t)
    print(total - cut)
if __name__ == "__main__":
    solve range(n):
    xi, yi, _ = pts[i]
    for j in range(n):
        if types[j] != types[i]()
The node-splitting edge stores the deletion cost of a tent. Every other edge has infinite capacity, which forces any minimum cut to pay for removing tents rather than breaking than Manhattan distance. The forbidden configurations arise from tents lying within one unit horizontally graph structure.
The parity classification is the heart of the reduction. Consecutive parity classes differ and vertically, which is exactly the Chebyshev condition used in the accepted construction. in exactly one coordinate parity, so neighboring vertices in a forbidden pattern always appear between citeturn0search0turn0search3
 consecutive layers.
The implementation checks Manhattan distance equal to one when creating layer transitionsPython integers have arbitrary precision, but capacities can reach. This is the correct adjacency relation for the path interpretation of roughly \(10^{12}\), so the implementation uses \(10^{18}\) as the infinity constant.
## Worked Examples forbidden configurations. Using Chebyshev distance here would introduce invalid transitions
### Sample 1
Input:
Parity classes:
| Tent | Coordinate | Weight | Class |
|---|---|---|---|
| 1 | (0, \(10^{18}\) is a sufficient infinity value.
## Worked Examples
### Sample 1
Input:
Parity classes:
| Tent3 | (1,0) | 3 | B |
| 4 | (1,1) | 1 | C |
| 5 | (-1,1) | 2 | C |
Relevant | Coordinates | Class | Weight |
|---|---|---|---|
| 1 | (0,0) | C | 4 |
| 2 | (0,1) | B | 5 |
| 3 | (1, chain:
| C | B | A | D |
|---|---|---|---|
| (1,1) | (1,0) | (0,0) | (0,1) |
The cheapest tent on0) | D | 3 |
| 4 | (1,1) | A | 1 |
| 5 | (-1,1) | A | 2 |
Relevant path:
| Layer | Vertex this chain has weight \(1\), so the minimum cut removes tent 4.
| Total Weight | Minimum Cut | Answer |
|---|---|---|
| 15 | 3 | 12 |
Output:
This demonstrates that removing a non-important tent may be cheaper than removing the important |
|---|---|
| A | (1,1) |
| B | (0,1) |
| C | (0,0) |
| D | (1,0) |
The minimum cut removes the weight-1 tent \((1,1)\).
| Total weight | Min tent itself.
### Constructed Example
The unique forbidden chain is:
| C | B | | 12 |
This demonstrates that removing a single cheap tent can destroy the entire forbidden pattern.
### Sample 2
All weights are \(1\).
The A | D |
|---|---|---|---|
| 1 | 1 | 10 | 100 |
The minimum cut removes construction produces eight independent forbidden paths, either the \(C\) or \(B\) vertex.
| exactly matching the eight patterns shown in the statement illustration Removed Tent Cost | Feasible |
|---|---|
| 1 | Yes |
| 10 | Yes |
| 100 | Yes |
. citeturn0search0
| Total tents | TotalOptimal cut value is \(1\).
| Total Weight | Minimum Cut | Answer |
|---|---|---|
| 112 | 1 | 111 |
The trace shows that weight |
|---|---|
| 32 | 32 |
The minimum cut value is \(8\).
| Total weight | Min cut | Answer |
|---|---|---|
| the flow formulation naturally chooses the cheapest vertex in the forbidden structure.
## Complexity Analysis
| Measure | Complexity 32 | 8 | 24 |
This demonstrates that the graph | Explanation |
|---|---|---|
| Time | \(O(n^2 + V^ formulation correctly handles many overlapping forbidden patterns simultaneously.
## Complexity Analysis
| Measure | Complexity |2E)\) worst case Explanation |
|---|---|---|
| Time | \(O(n^ for Dinic, much smaller in practice | \(n^2\) pair generation plus max flow |
| Space | \(O(n^2)\2 + \text{Dinic})\) | Graph construction) | Graph may contain \(O(n^2)\) edges |
With \(n \le 1000\), the graph contains only checks all pairs of tents |
| Space | \(O(n^2)\) | a few thousand vertices and at most about Worst-case number of edges is quadratic |
With \(n \le 1000 one million candidate pair checks. This\), the graph contains at most a few million comfortably fits within the limits and is the intended solution. citeturn0search0 adjacency entries in the absolute worst case,turn0search3
## Test Cases
# helper: run solution on input string, return"""5
0 0 4
0 1 5
1 0  output string
import io
import sys
def run(inp: str) -> str:
    #3
1 1 1
-1 1 2
"""
) == "12\n"
# single tent
assert run(
"""1
0 0  paste solution7
"""
) == "7\n"
# complete forbidden pattern
assert run(
"""4
0 0 10
1 0 1
 into a solve() function when testing1 1 1
0 1 100
"""
) == "111\n"
# negative coordinates
assert run
    pass
# sample 1
assert run(
"""(
"""4
0 0 5
-1 0 5
-1 -1 5
0 -1 5
"""
) == "155
0 0 4
0 1 5
\n"
# no forbidden structure
assert run(
"""3
0 0 10
1 0 3
1 1 1
-1 1 2
"""
) == "12\n"
# single tent
assert run(
"""1
0 10 10 20
20 20 30
"""
) == "60\n"
| Test input | Expected output | What it validates```テキスト
 4
 0 0 5
 -1 0 5
 -1 -1 5
 0 -1 5
 4
 0 0 100
 0 1 1
 1 0 1
 1 1 1
 5
 0 は体重を減らしますが、100 では最適とは程遠いです。 
ここで次のことを考えてみましょう。 
大切なテント \((0,0)\3
 0 0 5
 10 10 6
 20 20 7
 4
 負の座標を考慮します。 
単純な戦略では可能性があります1 0 2
 -1 1 2
