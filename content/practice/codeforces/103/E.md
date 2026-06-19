---
title: "CF 103E - セット購入"
description: "それぞれに関連するコストを持つ整数のセットがいくつか与えられます。 空のコレクションを含む、これらのセットの任意のコレクションを選択できます。 選択されたセットの数を $k$ とし、選択されたすべてのセットの和集合に $u$ 個の異なる整数が含まれるとします。"
date: "2026-05-28T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "flows", "graph-matchings"]
categories: ["algorithms"]
codeforces_contest: 103
codeforces_index: "E"
codeforces_contest_name: "Codeforces Beta Round 80 (Div. 1 Only)"
rating: 2900
weight: 103
solve_time_s: 190
verified: false
draft: false
---

[CF 103E - セットの購入](https://codeforces.com/problemset/problem/103/E)

 **評価:** 2900
 **タグ:** フロー、グラフマッチング
 **解決時間:** 3 分 10 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 それぞれに関連するコストを持つ整数のセットがいくつか与えられます。 空のコレクションを含む、これらのセットの任意のコレクションを選択できます。 選択したセットの数を次のようにします$k$、選択したすべてのセットの和集合に次のものが含まれるようにします。$u$異なる整数。 

目標は、条件下での総コストを最小限に抑えることです。$u = k$。 

このステートメントの珍しい部分は、セットのファミリーに関する保証です。 あらゆるコレクションに対して、$k$セット、それらの共用体には常に少なくとも次のものが含まれます$k$明確な数字。 これはまさにホールの状態です。 これは、選択されたすべてのセットに、そのセット内から個別の代表要素を割り当てることができることを意味します。 

入力サイズは、三次グラフ アルゴリズムを許可するには十分小さいですが、サブセットの列挙には大きすぎます。 以来$n \le 300$、全体的に総当たり力$2^n$コレクションは絶望的です。 平$2^{40}$すでに大きすぎるので、ここで次のようにするかもしれません$2^{300}$サブセット。 

コストがマイナスになる可能性があり、最適化の性質が変わります。 「負のセットをすべて取得する」などの単純な貪欲な戦略は失敗する可能性があります。セットを 1 つ追加すると、結合サイズが強制的に増大し、等価条件が崩れる可能性があるからです。 

微妙なエッジケースは、空のコレクションです。 すべてのコストが正の場合、選択されたセットの数と和集合サイズの両方がゼロに等しいため、何も購入しないことは有効です。 

例えば：```
2
1 1
1 2
5 7
```正しい答えは次のとおりです。```
0
```少なくとも 1 つのセットを選択することを要求する不用意な解決策は、誤った出力を行う可能性があります。`5`。 

複数のセットが重なり合う場合には、別の注意が必要なケースが発生します。```
3
2 1 2
2 1 2
1 2
-5 -4 100
```正しい答えは次のとおりです。```
-9
```和集合のサイズが 2 であり、選択されたセットが 2 つあるため、最初の 2 つのセットは両方とも選択できます。 3 番目のセットは追加しないでください。その場合、セットは 3 つありますが、異なる数値は 2 つだけになるためです。 

よくある間違いは、条件が選択されたセットがペアごとに素でなければならないことを意味すると考えることです。 それは誤りです。 この条件は、結合の合計サイズのみを制限します。 

もう 1 つの危険なケースは、平等がすでに自動的に成り立つ場合です。```
2
1 1
1 2
-3 -4
```正しい答えは次のとおりです。```
-7
```各セットは個別の数値に寄与するため、両方を取得することが最適です。 

平等がいつ起こるかを理解することが、問題の鍵となります。 

## アプローチ

 ブルートフォースアプローチは簡単です。 セットのすべてのサブセットを列挙し、和集合のサイズを計算し、サブセット間の最小総コストを維持します。$$|\text{chosen sets}| = |\text{union}|$$ステートメントの条件は次のことを保証します。$$|\text{union}| \ge |\text{chosen sets}|$$すべてのサブセットに対して。 したがって、実現可能なサブセットは、まさに等価性が成立するサブセットです。 

この強引な方法は、考えられるすべてのコレクションを明示的にチェックするため、正しいです。 問題はその複雑さです。 がある$2^n$サブセット、および$n$すべてのサブセットを保存することすら不可能です。 

重要な観察はホールの定理から得られます。 

ステートメント保証では、セットのすべてのコレクションには少なくともセットと同じ数の個別の要素があると述べています。 ホールの定理は、すべてのコレクションが、選択されたセットと個別の要素の間の完全な一致を認めることを示します。 

ここで、等号が成り立つ場合を考えてみましょう。$$|\text{union}| = |\text{chosen sets}|$$選択したセットとそのセットに含まれる要素の間の 2 部グラフを観察するとします。 Hall は、選択されたすべてのセットをカバーするマッチングを保証します。 ユニオンにはセットとまったく同じ数の要素が含まれるため、マッチングでは実際にはユニオン内のすべての要素を使用する必要があります。 

これは、選択されたすべての要素が 1 回だけ一致することを意味します。 

これは、選択されたサブグラフがバランスの取れたコンポーネント構造を形成していることを直ちに意味します。 マトロイド言語では、実行可能なコレクションはまさに横断マトロイドのタイトな集合です。 

次に、最適化は最小重みクロージャ問題に変換され、最小カット計算に帰着します。 

重要な構造的事実は次のとおりです。 

セットのサブセットは、マッチングにおける交互のパスから導出された有向依存グラフ内の強く接続されたコンポーネントの和集合である場合に限り、等価性を満たします。 

まず、セットから個別の要素への完全な一致を 1 つ構築します。 次に、エッジの方向を設定します。 

セットからその中の一致しないすべての要素まで。 

一致した要素から一致したセットに戻ります。 

これにより有向グラフが作成されます。 タイトなサブセットは、到達可能性の下で閉じられた頂点サブセットに正確に対応します。 

最小コストの閉じたサブセットを見つけることは、最小カットへの古典的な削減です。 

この条件はサブセットと共用体のみに依存するため、ブルート フォースは機能しますが、サブセットが指数関数的に多いため失敗します。 ホール構造を使用すると、実行可能なサブセットを有向グラフ内の閉集合として再解釈し、指数関数的な探索を多項式時間の流れの計算に変えることができます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース |$O(2^n \cdot n^2)$|$O(n^2)$| 遅すぎる |
 | 最適 |$O(n^3)$|$O(n^2)$| 承認済み |

 ## アルゴリズムのチュートリアル

 1. セットと数値の間の 2 部グラフを作成します。 

左側に含まれるのは、$n$セット。 右側には数字が入っています$1 \ldots n$。 セットからのエッジがある$i$番号を付ける$x$もし$x$セットに属します。 
2. すべてのセットをカバーする完全なマッチングを計算します。 

ホールの条件は、そのような一致が存在することを保証します。 クーンのアルゴリズムを使用できるのは、次の理由からです。$n \le 300$。 
3. 有向グラフを構築します。 

セットごとに$i$：

もし$i$数字が含まれています$x$そして$x$と一致しません$i$、有向エッジを追加します。$$i \to \text{owner}(x)$$どこ`owner(x)`に一致するセットです$x$。 

このエッジは、交互パスの依存関係を表します。 セットを含めると$i$の所有者を含める必要があります。$x$密着性を保つため。 
4. 強連結成分を計算します。 

1 つの SCC 内では、すべての頂点が他のすべての頂点に依存します。 SCC 全体を取得するか、まったく取得しないかのどちらかです。 
5. SCC グラフを DAG に圧縮します。 

各 SCC は、そのセットのコストの合計に等しい重みを持つ 1 つのノードになります。 
6. 最小重みの閉じたサブセットを見つけます。 

閉じたサブセットとは次のことを意味します。 

ノードを選択した場合は、すべての発信近隣ノードも選択する必要があります。 

これはミニマムカットを使うことで解決します。 
7. フローネットワークを構築します。 

合計重みが負のすべての SCC について、次のように接続します。$$S \to \text{SCC}$$と等しい容量$-w$。 

総重量が正のすべての SCC について、次のように接続します。$$\text{SCC} \to T$$と等しい容量$w$。 

すべての DAG エッジについて:$$u \to v$$無限の容量を追加します。 
8. max-flow/min-cut を実行します。 

最小の閉じたサブセットの重みは次のようになります。$$\text{maxflow} - \sum_{\text{negative } w} |w|$$9. ゼロと比較します。 

空のコレクションは常に有効であるため、答えが 0 を超えることはできません。 

### なぜ効果があるのか

 マッチングにより、ホール条件が構造依存グラフに変換されます。 密なサブセットとは、まさに、到達可能なすべての依存関係も含まれるサブセットのことです。 これはまさに有向グラフにおける閉集合の定義です。 

SCC 内のすべての頂点が他のすべての頂点を強制するため、強く接続されたコンポーネントは分割できないグループを表します。 

最小重量クロージャーのためのミニカットリダクションが標準です。 無限容量エッジは、クロージャ制約に違反することを禁止します。 カットにより、ソースから到達可能な SCC が選択され、その容量は、負の SCC を除外するか正の SCC を含めることによるペナルティと正確に等しくなります。 

すべての実行可能なコレクションは 1 つの閉じたサブセットに対応し、その逆も同様であるため、閉包の重みを最小限に抑えることで最適な答えが得られます。 

## Python ソリューション```python
import sys
from collections import deque

input = sys.stdin.readline

INF = 10**18

class Dinic:
    def __init__(self, n):
        self.n = n
        self.g = [[] for _ in range(n)]

    def add_edge(self, u, v, c):
        self.g[u].append([v, c, len(self.g[v])])
        self.g[v].append([u, 0, len(self.g[u]) - 1])

    def bfs(self, s, t):
        self.level = [-1] * self.n
        q = deque([s])
        self.level[s] = 0

        while q:
            u = q.popleft()
            for v, c, rev in self.g[u]:
                if c > 0 and self.level[v] == -1:
                    self.level[v] = self.level[u] + 1
                    q.append(v)

        return self.level[t] != -1

    def dfs(self, u, t, f):
        if u == t:
            return f

        for i in range(self.ptr[u], len(self.g[u])):
            self.ptr[u] = i

            v, c, rev = self.g[u][i]

            if c > 0 and self.level[v] == self.level[u] + 1:
                pushed = self.dfs(v, t, min(f, c))

                if pushed:
                    self.g[u][i][1] -= pushed
                    self.g[v][rev][1] += pushed
                    return pushed

        return 0

    def maxflow(self, s, t):
        flow = 0

        while self.bfs(s, t):
            self.ptr = [0] * self.n

            while True:
                pushed = self.dfs(s, t, INF)
                if not pushed:
                    break
                flow += pushed

        return flow

def solve():
    n = int(input())

    sets = []
    for _ in range(n):
        arr = list(map(int, input().split()))
        sets.append(arr[1:])

    cost = list(map(int, input().split()))

    match_num = [-1] * (n + 1)

    def kuhn(u, vis):
        if vis[u]:
            return False

        vis[u] = True

        for x in sets[u]:
            if match_num[x] == -1 or kuhn(match_num[x], vis):
                match_num[x] = u
                return True

        return False

    for i in range(n):
        vis = [False] * n
        kuhn(i, vis)

    owner = [-1] * (n + 1)

    for x in range(1, n + 1):
        if match_num[x] != -1:
            owner[x] = match_num[x]

    g = [[] for _ in range(n)]

    for i in range(n):
        matched_x = -1

        for x in sets[i]:
            if owner[x] == i:
                matched_x = x

        for x in sets[i]:
            if x != matched_x:
                g[i].append(owner[x])

    # Tarjan SCC
    sys.setrecursionlimit(10**6)

    tin = [-1] * n
    low = [0] * n
    stack = []
    in_stack = [False] * n

    comp = [-1] * n
    timer = 0
    comp_cnt = 0

    def dfs(u):
        nonlocal timer, comp_cnt

        tin[u] = low[u] = timer
        timer += 1

        stack.append(u)
        in_stack[u] = True

        for v in g[u]:
            if tin[v] == -1:
                dfs(v)
                low[u] = min(low[u], low[v])
            elif in_stack[v]:
                low[u] = min(low[u], tin[v])

        if low[u] == tin[u]:
            while True:
                x = stack.pop()
                in_stack[x] = False
                comp[x] = comp_cnt

                if x == u:
                    break

            comp_cnt += 1

    for i in range(n):
        if tin[i] == -1:
            dfs(i)

    comp_weight = [0] * comp_cnt

    for i in range(n):
        comp_weight[comp[i]] += cost[i]

    dag = set()

    for u in range(n):
        for v in g[u]:
            cu = comp[u]
            cv = comp[v]

            if cu != cv:
                dag.add((cu, cv))

    S = comp_cnt
    T = comp_cnt + 1

    dinic = Dinic(comp_cnt + 2)

    neg_sum = 0

    for i in range(comp_cnt):
        w = comp_weight[i]

        if w < 0:
            dinic.add_edge(S, i, -w)
            neg_sum += -w
        else:
            dinic.add_edge(i, T, w)

    for u, v in dag:
        dinic.add_edge(u, v, INF)

    flow = dinic.maxflow(S, T)

    ans = flow - neg_sum
    ans = min(ans, 0)

    print(ans)

solve()
```最初の部分では、数値からセットへのマッチングを計算します。 Hall の条件は、すべてのセットが個別の代表を取得できることを保証するため、Kuhn のアルゴリズムは常に成功します。 

有向グラフの構築はデリケートな部分です。 一致しないエッジはすべて依存関係エッジを作成します。 設定されている場合`i`別のセットが所有する番号を使用することもできます。`i`その所有者がいなかったら、締め付けに違反するでしょう。 

依存関係が循環を形成する可能性があるため、SCC 圧縮が必要です。 サイクルでは、すべてのセットが他のすべてのセットを強制するため、これらを一緒に選択する必要があります。 

min-cut 構造はクロージャ ルールをエンコードします。 無限エッジにより、依存関係に違反するカットが防止されます。 負の SCC 重みは収益性が高く、ソースから接続されています。 正の SCC 重みはペナルティであり、シンクに接続されます。 

最終的な式:```
flow - neg_sum
```最小カット値から最小クロージャー重量への標準換算値です。 

実装の微妙な詳細では、十分な大きさのものが使用されています。`INF`。 考えられる絶対的な答えを超えていればよいのです。 コストには限界があるので、$10^6$最大でも 300 セットありますが、$10^{18}$完全に安全です。 

## 実用的な例

 ### サンプル 1

 入力:```
3
1 1
2 2 3
1 3
10 20 -3
```考えられる一致の 1 つは次のとおりです。 

| セット | 一致した番号 |
 | --- | --- |
 | 0 | 1 |
 | 1 | 2 |
 | 2 | 3 |

 依存関係グラフ:

 | セット | 追加番号 | エッジ |
 | --- | --- | --- |
 | 0 | なし | なし |
 | 1 | 3 | 1→2 |
 | 2 | なし | なし |

 SCC:

 | SCC | セット | 重量 |
 | --- | --- | --- |
 | あ | {0} | 10 |
 | B | {1} | 20 |
 | C | {2} | -3 |

 唯一のマイナスの SCC は、`{2}`また、発信への依存関係がないため、単独で選択します。 

結果：```
-3
```この例は、単一のセットがすでに等価条件を満たすことができることを示しています。 セット`{3}`1 つの要素を持ち、1 つのセットに寄与します。 

### カスタム例

 入力:```
3
2 1 2
2 1 2
1 2
-5 -4 100
```次のような一致があるとします。 

| セット | 一致した番号 |
 | --- | --- |
 | 0 | 1 |
 | 1 | 2 |

 依存関係:

 | セット | 追加番号 | エッジ |
 | --- | --- | --- |
 | 0 | 2 | 0→1 |
 | 1 | 1 | 1 → 0 |
 | 2 | 2 | 2→1 |

 SCC:

 | SCC | セット | 重量 |
 | --- | --- | --- |
 | あ | {0,1} | -9 |
 | B | {2} | 100 |

 セット 0 と 1 はサイクルを形成するため、これらを一緒に取得する必要があります。 

最適な答え:```
-9
```これは、SCC 圧縮が必要な理由を示しています。 最初の 2 つのセットのうち 1 つだけを取るとクロージャに違反します。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |$O(n^3)$| マッチング、SCC 計算、最大流量はすべて 3 次複雑さの範囲内に収まります。 
| スペース |$O(n^2)$| グラフとフロー ネットワークは最大 2 次エッジを格納します。 

と$n \le 300$, 三次アルゴリズムは完全に実用的です。 密なグラフでも問題なく制限内に収まります。 

## テストケース```python
# helper: run solution on input string, return output string
import sys
import io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)

    from collections import deque

    input = sys.stdin.readline
    INF = 10**18

    class Dinic:
        def __init__(self, n):
            self.n = n
            self.g = [[] for _ in range(n)]

        def add_edge(self, u, v, c):
            self.g[u].append([v, c, len(self.g[v])])
            self.g[v].append([u, 0, len(self.g[u]) - 1])

        def bfs(self, s, t):
            self.level = [-1] * self.n
            q = deque([s])
            self.level[s] = 0

            while q:
                u = q.popleft()

                for v, c, rev in self.g[u]:
                    if c > 0 and self.level[v] == -1:
                        self.level[v] = self.level[u] + 1
                        q.append(v)

            return self.level[t] != -1

        def dfs(self, u, t, f):
            if u == t:
                return f

            for i in range(self.ptr[u], len(self.g[u])):
                self.ptr[u] = i

                v, c, rev = self.g[u][i]

                if c > 0 and self.level[v] == self.level[u] + 1:
                    pushed = self.dfs(v, t, min(f, c))

                    if pushed:
                        self.g[u][i][1] -= pushed
                        self.g[v][rev][1] += pushed
                        return pushed

            return 0

        def maxflow(self, s, t):
            flow = 0

            while self.bfs(s, t):
                self.ptr = [0] * self.n

                while True:
                    pushed = self.dfs(s, t, INF)

                    if not pushed:
                        break

                    flow += pushed

            return flow

    n = int(input())

    sets = []

    for _ in range(n):
        arr = list(map(int, input().split()))
        sets.append(arr[1:])

    cost = list(map(int, input().split()))

    match_num = [-1] * (n + 1)

    def kuhn(u, vis):
        if vis[u]:
            return False

        vis[u] = True

        for x in sets[u]:
            if match_num[x] == -1 or kuhn(match_num[x], vis):
                match_num[x] = u
                return True

        return False

    for i in range(n):
        vis = [False] * n
        kuhn(i, vis)

    owner = [-1] * (n + 1)

    for x in range(1, n + 1):
        if match_num[x] != -1:
            owner[x] = match_num[x]

    g = [[] for _ in range(n)]

    for i in range(n):
        matched_x = -1

        for x in sets[i]:
            if owner[x] == i:
                matched_x = x

        for x in sets[i]:
            if x != matched_x:
                g[i].append(owner[x])

    sys.setrecursionlimit(10**6)

    tin = [-1] * n
    low = [0] * n
    stack = []
    in_stack = [False] * n

    comp = [-1] * n
    timer = 0
    comp_cnt = 0

    def dfs(u):
        nonlocal timer, comp_cnt

        tin[u] = low[u] = timer
        timer += 1

        stack.append(u)
        in_stack[u] = True

        for v in g[u]:
            if tin[v] == -1:
                dfs(v)
                low[u] = min(low[u], low[v])
            elif in_stack[v]:
                low[u] = min(low[u], tin[v])

        if low[u] == tin[u]:
            while True:
                x = stack.pop()
                in_stack[x] = False
                comp[x] = comp_cnt

                if x == u:
                    break

            comp_cnt += 1

    for i in range(n):
        if tin[i] == -1:
            dfs(i)

    comp_weight = [0] * comp_cnt

    for i in range(n):
        comp_weight[comp[i]] += cost[i]

    dag = set()

    for u in range(n):
        for v in g[u]:
            cu = comp[u]
            cv = comp[v]

            if cu != cv:
                dag.add((cu, cv))

    S = comp_cnt
    T = comp_cnt + 1

    dinic = Dinic(comp_cnt + 2)

    neg_sum = 0

    for i in range(comp_cnt):
        w = comp_weight[i]

        if w < 0:
            dinic.add_edge(S, i, -w)
            neg_sum += -w
        else:
            dinic.add_edge(i, T, w)

    for u, v in dag:
        dinic.add_edge(u, v, INF)

    ans = dinic.maxflow(S, T) - neg_sum
    ans = min(ans, 0)

    return str(ans) + "\n"

# provided sample
assert run(
"""3
1 1
2 2 3
1 3
10 20 -3
"""
) == "-3\n"

# empty collection optimal
assert run(
"""2
1 1
1 2
5 7
"""
) == "0\n"

# SCC cycle
assert run(
"""3
2 1 2
2 1 2
1 2
-5 -4 100
"""
) == "-9\n"

# all negative independent sets
assert run(
"""3
1 1
1 2
1 3
-1 -2 -3
"""
) == "-6\n"

# minimum size
assert run(
"""1
1 1
5
"""
) == "0\n"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 単一のポジティブセット | 0 | 空のコレクションの処理 |
 | 2 つの重複する負のセット | -9 | SCC 圧縮の正確性 |
 | 独立した負のシングルトン セット | -6 | 複数の独立した実行可能なコンポーネント |
 | 最小サイズ入力 | 0 | との境界条件$n=1$|

 ## 特殊なケース

 利用可能なすべてのセットが正のコストを持つ場合を考えてみましょう。```
2
1 1
1 2
5 7
```このアルゴリズムは、正の重みを持つ 2 つの分離された SCC を構築します。 フロー ネットワークでは、両方ともシンクにのみ接続します。 最小カットでは両方の SCC が除外され、合計コストが 0 になります。これは、空のコレクションを選択することに正確に対応します。 

次に、循環依存関係について考えてみましょう。```
2
2 1 2
2 1 2
-3 -4
```セット 0 が番号 1 を所有し、セット 1 が番号 2 を所有しているとします。すると、各セットには他の所有者の番号と一致しないエッジがあり、エッジが作成されます。```
0 → 1
1 → 0
```両方のセットが合計重量で 1 つの SCC に折りたたまれます。`-7`。 min-cut は両方を選択するか、どちらも選択しません。 SCC の重みが負であるため、アルゴリズムは両方を選択し、正しい答えを生成します。`-7`。 

最後に、誤解を招く重複について考えてみましょう。```
3
2 1 2
1 1
1 2
-100 1 1
```1 つのセットと 2 つの数値は等価性を満たさないため、最初のセットのみの選択は有効です。 依存関係グラフはこれを捉えています。 大きな負のセットは両方のシングルトン所有者に依存するため、クロージャにより 3 つのセットすべてが強制的に含められます。 彼らの総コストは`-98`、結合サイズは 2 になりますが、セット数は 3 になりますが、これは不可能です。 クロージャー制約により、この無効な選択が自動的に防止されます。
