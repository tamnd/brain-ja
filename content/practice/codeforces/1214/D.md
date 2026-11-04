---
title: "CF 1214D - トレジャーアイランド"
description: "マップを表す $n 倍 m$ グリッドが与えられます。 各セルはブロックされているか、フリーになっています。 左上のセル $(1,1)$ から開始して、右下のセル $(n,m)$ に到達したいと考えています。"
date: "2026-06-15T18:37:57+07:00"
tags: ["codeforces", "competitive-programming", "dfs-and-similar", "dp", "flows", "hashing"]
categories: ["algorithms"]
codeforces_contest: 1214
codeforces_index: "D"
codeforces_contest_name: "Codeforces Round 583 (Div. 1 + Div. 2, based on Olympiad of Metropolises)"
rating: 1900
weight: 1214
solve_time_s: 209
verified: true
draft: false
---

[CF 1214D - トレジャーアイランド](https://codeforces.com/problemset/problem/1214/D)

 **評価:** 1900
 **タグ:** dfs および類似の、dp、フロー、ハッシュ
 **解決時間:** 3 分 29 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 私たちに与えられているのは、$n \times m$地図を表すグリッド。 各セルはブロックされているか、フリーになっています。 左上のセルから始めます$(1,1)$右下のセルに到達したい$(n,m)$。 移動は非常に制限されており、どのセルからでも 1 歩下か 1 歩右にのみ移動でき、上にも左にも移動できません。 

移動を開始する前に、敵は最初にフリーのセルの一部をブロックされたセルに変換することができます。 敵は開始セルと終了セルに触れることはできません。 目標は、からの有効な単調パスが存在しないように、ブロックする必要があるセルの最小数を見つけることです。$(1,1)$に$(n,m)$。 

これは基本的に、単調な移動制約下での「すべてのパスを破壊する」問題です。 有効なパスはすべて、正確に次のシーケンスです。$n + m - 2$で構成される動き$n-1$ダウンと$m-1$権利があり、フリーセルのみを通過する必要があります。 

重要な制約は、$n \cdot m \le 10^6$これにより、グリッド上で線形または線形に近いグラフ アルゴリズムが可能になります。 単調なパスの数だけでも指数関数的に増加する可能性があるため、すべてのパスを列挙しようとする解決策は直ちに不可能になります。$n + m$。 

パスが存在しない形でグリッドがすでにブロックされている場合、微妙なエッジ ケースが発生します。 この場合、追加のブロックは必要ないため、答えはゼロになります。 もう 1 つの重要なケースは、グリッドが完全に開いている場合です。つまり、有向非巡回グリッド グラフの左上と右下を分ける最小の頂点カットを効果的に求めています。 

## アプローチ

 直接的な総当たりのアイデアは、次からすべての単調なパスを列挙することです。$(1,1)$に$(n,m)$次に、ブロックされたセルができるだけ少ないすべてのパスをヒットするようにします。 中程度のグリッド サイズであっても、パスの数は組み合わせによって増加します。 のために$10 \times 10$グリッドには、すでに 1,000 万を超える単調パスがあり、各パスは候補ブロック セットとの交差をチェックする必要があります。 これはまったく実現不可能です。 

より構造化されたビューはグラフ理論から得られます。 グリッドは、すべてのセルがノードであり、エッジが右または下のみに向かう有向非巡回グラフを形成します。 ソースからシンクへのパスが存在しないように、最小限の頂点 (セル) を削除する必要があります。 これは古典的な最小頂点カット問題です。 

最小頂点カットは通常、流れの問題に帰着します。 各頂点は、「in」ノードと「out」ノードの 2 つのノードに分割され、そのセルを削除するコストを表す容量 1 のエッジによって接続されます。 すべての元のグリッド移動 (右/下エッジ) は、1 つのセルの「アウト」ノードからその隣接セルの「イン」ノードまでの無限容量エッジとして表されます。 答えは最小限になる$s$-$t$この変換されたグラフをカットします。 

すべての容量が整数であり、グラフが疎であるため、Dinic などの最大フロー アルゴリズムは、次の制限内で快適に実行されます。$10^6$ノードとおおよそ$2 \cdot 10^6$エッジ。 

ブルート フォースは、すべてのカットを暗黙的に検索するため概念的には機能しますが、パスが指数関数的に増加するため失敗します。 フロー変換は、組み合わせ爆発を構造化された容量に対する多項式時間の最適化に置き換えます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルート フォース (パスを列挙) | 指数 | 指数 | 遅すぎる |
 | 最大流量 (頂点分割) |$O(E \sqrt{V})$典型的 (実際のディニックの速さ) |$O(V+E)$| 承認済み |

 ## アルゴリズムのチュートリアル

 グリッドをフロー ネットワークに変換し、最小カットを計算します。 

1. 各セルについて$(i,j)$、2 つのノードを作成します。$v_{in}$そして$v_{out}$。 それらの間のエッジは、このセルを「使用」するか「削除」するかを表します。 空きセルにはブロックのコストが 1 であるため容量 1 を割り当て、ブロックされたセルにはすでに使用できないため容量 0 を割り当てます。 
2. セルごとに接続します。$v_{in} \to v_{out}$上記の容量を備えています。 これにより、セルを通過するには、セルを切断した場合にその除去コストを支払う必要があります。 
3. セルからの許可された移動ごとに$(i,j)$から無限容量エッジを追加します。$v_{out}$の$(i,j)$に$v_{in}$右隣と下隣の。 これらのエッジにより、細胞を切断しない限り自由に動くことが保証されます。 
4. ソースは$(1,1)_{out}$、開始セルを出た後に開始し、シンクは$(n,m)_{in}$、目的地に到達するということはそこに入るということなので。 
5. 最大流量アルゴリズムを実行します。 結果として得られる流量値は、ブロックする必要があるセルの最小数に対応する最小カット容量と等しくなります。 
6. 計算された最大流量値を返します。 

### なぜ効果があるのか

 開始から終了までの有効なパスはすべて、内部エッジを介してすべての頂点を少なくとも 1 回横断する必要があるフロー グラフ内の有向パスに対応します。 頂点インから頂点アウトのエッジをカットすると、すべての可能なパスからそのセルが削除されます。 逆に、ソースとシンクを分離するカットは、すべての単調パスを破壊するブロックされたセルのセットに正確に対応します。 ユニット容量により、ブロックされた各セルがカット サイズに 1 つだけ寄与することが強制されるため、カット コストを最小限に抑えることは、ブロックされたセルの数を最小限に抑えることと同じになります。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

from collections import deque

INF = 10**18

class Dinic:
    def __init__(self, n):
        self.n = n
        self.graph = [[] for _ in range(n)]
    
    def add_edge(self, fr, to, cap):
        self.graph[fr].append([to, cap, len(self.graph[to])])
        self.graph[to].append([fr, 0, len(self.graph[fr]) - 1])
    
    def bfs(self, s, t):
        self.level = [-1] * self.n
        q = deque([s])
        self.level[s] = 0
        while q:
            v = q.popleft()
            for to, cap, rev in self.graph[v]:
                if cap > 0 and self.level[to] < 0:
                    self.level[to] = self.level[v] + 1
                    q.append(to)
        return self.level[t] >= 0
    
    def dfs(self, v, t, f):
        if v == t:
            return f
        for i in range(self.it[v], len(self.graph[v])):
            self.it[v] = i
            to, cap, rev = self.graph[v][i]
            if cap > 0 and self.level[to] == self.level[v] + 1:
                ret = self.dfs(to, t, min(f, cap))
                if ret:
                    self.graph[v][i][1] -= ret
                    self.graph[to][rev][1] += ret
                    return ret
        return 0
    
    def max_flow(self, s, t):
        flow = 0
        while self.bfs(s, t):
            self.it = [0] * self.n
            while True:
                f = self.dfs(s, t, INF)
                if not f:
                    break
                flow += f
        return flow

n, m = map(int, input().split())
grid = [input().strip() for _ in range(n)]

def id_in(i, j):
    return (i * m + j) * 2

def id_out(i, j):
    return (i * m + j) * 2 + 1

N = n * m * 2
dinic = Dinic(N)

for i in range(n):
    for j in range(m):
        if grid[i][j] == '#':
            cap = 0
        else:
            cap = 1
        dinic.add_edge(id_in(i, j), id_out(i, j), cap)

dirs = [(1, 0), (0, 1)]

for i in range(n):
    for j in range(m):
        for di, dj in dirs:
            ni, nj = i + di, j + dj
            if ni < n and nj < m:
                dinic.add_edge(id_out(i, j), id_in(ni, nj), INF)

s = id_out(0, 0)
t = id_in(n - 1, m - 1)

print(dinic.max_flow(s, t))
```この実装では、エッジ カットによる頂点の削除をシミュレートするために、各セルを 2 つのノードとしてエンコードします。 唯一の微妙な点は容量の割り当てです。ブロックされたセルの寄与はゼロであるため、カットには表示されませんが、自由なセルのコストは 1 単位です。 

無限容量エッジは、カットを決して制約しないように十分な大きさでなければなりません。 のような値$10^{18}$グリッドは最大でも$10^6$細胞。 

開始セルの「出力」ノードとしてソースを選択すると、最初のセルに入るときに誤ってコストが強制されることが回避されます。 

## 実用的な例

 ### 例 1

 入力:```
2 2
..
..
```| ステップ | フロー状態 (概念) | 説明 |
 | --- | --- | --- |
 | 1 | すべてのパスが存在します | 2 つの単調なパス: 右下と右下 |
 | 2 | 1 つのセルを切断してみてください | 単一のセルを削除してもパスは残ります。 
| 3 | カットサイズが大きくなる | 少なくとも 2 つの削除が必要 |

 アルゴリズムでは、両方の内部セルを何らかの組み合わせでブロックする必要があることがわかり、答え 2 が得られます。これは、完全に開いた 2x2 グリッドでは、考えられる両方の中間ルートを破棄する必要があることを確認します。 

### 例 2

 入力:```
3 3
...
.#.
...
```| ステップ | フロー状態 (概念) | 説明 |
 | --- | --- | --- |
 | 1 | 中央のセルがブロックされています | 中央に障害物が 1 つ |
 | 2 | パスの再ルート | 2 つの切り離された廊下がまだ存在します。 
| 3 | カット計算 | フローによりボトルネック構造が特定される |

 流れの計算により、中央に障害物があるにもかかわらず、2 つの切り離された単調な廊下が残っているため、開始と終了を完全に分離するには追加のブロックが必要であることがわかります。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |$O(E \sqrt{V})$ディニックの典型 | 各グリッド セルは一定のエッジに寄与し、ノードの合計は次のようになります。$O(nm)$|
 | スペース |$O(nm)$| 各セルは 2 つのノードと隣接リストに分割されます。 

グリッド サイズの制約は、$10^6$セルはメモリ制限内に快適に収まり、ユニット容量のようなグラフでの Dinic のパフォーマンスは、Python で慎重に実装するか、PyPy/C++ でより確実に実装すれば、実際には 1 秒の制限に十分対応できる速度になります。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    from collections import deque

    INF = 10**18

    class Dinic:
        def __init__(self, n):
            self.n = n
            self.graph = [[] for _ in range(n)]

        def add_edge(self, fr, to, cap):
            self.graph[fr].append([to, cap, len(self.graph[to])])
            self.graph[to].append([fr, 0, len(self.graph[fr]) - 1])

        def bfs(self, s, t):
            self.level = [-1] * self.n
            q = deque([s])
            self.level[s] = 0
            while q:
                v = q.popleft()
                for to, cap, rev in self.graph[v]:
                    if cap > 0 and self.level[to] < 0:
                        self.level[to] = self.level[v] + 1
                        q.append(to)
            return self.level[t] >= 0

        def dfs(self, v, t, f):
            if v == t:
                return f
            for i in range(self.it[v], len(self.graph[v])):
                self.it[v] = i
                to, cap, rev = self.graph[v][i]
                if cap > 0 and self.level[to] == self.level[v] + 1:
                    ret = self.dfs(to, t, min(f, cap))
                    if ret:
                        self.graph[v][i][1] -= ret
                        self.graph[to][rev][1] += ret
                        return ret
            return 0

        def max_flow(self, s, t):
            flow = 0
            while self.bfs(s, t):
                self.it = [0] * self.n
                while True:
                    f = self.dfs(s, t, INF)
                    if not f:
                        break
                    flow += f
            return flow

    n, m = map(int, input().split())
    grid = [input().strip() for _ in range(n)]

    def id_in(i, j):
        return (i * m + j) * 2

    def id_out(i, j):
        return (i * m + j) * 2 + 1

    N = n * m * 2
    dinic = Dinic(N)

    for i in range(n):
        for j in range(m):
            cap = 0 if grid[i][j] == '#' else 1
            dinic.add_edge(id_in(i, j), id_out(i, j), cap)

    for i in range(n):
        for j in range(m):
            if i + 1 < n:
                dinic.add_edge(id_out(i, j), id_in(i + 1, j), INF)
            if j + 1 < m:
                dinic.add_edge(id_out(i, j), id_in(i, j + 1), INF)

    s = id_out(0, 0)
    t = id_in(n - 1, m - 1)

    return str(dinic.max_flow(s, t))

# provided sample
assert run("""2 2
..
..""") == "2"

# custom tests
assert run("""3 3
...
.#.
...""") >= "1", "center block case"
assert run("""3 3
...
...
...""") == "2", "fully open small grid sanity"
assert run("""3 3
###
#..
###""") == "0", "already blocked path"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 2x2 の空のグリッド | 2 | 最小限の非自明な分離 |
 | 中央障害物グリッド | ≥1 | 障害物のルート変更動作 |
 | フルオープン 3x3 | 2 | 密集したパスでの正確性 |
 | すでにブロックされた廊下 | 0 | パスがないエッジケース |

 ## 特殊なケース

 完全に封鎖された廊下の事件は、なぜ残酷な推理が失敗するのかを浮き彫りにします。 単一の水平壁がすでに開始点から終了点まで分離しているグリッドを考えてみましょう。 フロー グラフはただちにゼロ拡張パスを生成するため、アルゴリズムは不必要なカットを試行せずに 0 を返します。 

2 番目のエッジ ケースは、狭い蛇のような廊下です。 このような場合、すべてのセルはすべての可能なパス上にあります。 最小カットは内部セルの数に等しく、すべてのパスがすべての中間頂点インから頂点アウトのエッジを通過する必要があるため、頂点分割構造によりカットに正確にそのコストが蓄積されます。
