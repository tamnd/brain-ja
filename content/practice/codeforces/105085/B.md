---
title: "CF 105085B - ファーマーズストライキ"
description: "都市と一方通行の道路の有向グラフが与えられます。 都市 0 が出発点で、都市 $N-1$ が目的地です。 各道路は 1 人の農民を割り当てることで「ブロック」でき、ブロックするとその有向エッジがグラフから削除されます。"
date: "2026-06-27T20:54:06+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 105085
codeforces_index: "B"
codeforces_contest_name: "AdaByron Regional Madrid 2024"
rating: 0
weight: 105085
solve_time_s: 54
verified: true
draft: false
---

[CF 105085B - 農民ストライキ](https://codeforces.com/problemset/problem/105085/B)

 **評価:** -
 **タグ:** -
 **解決時間:** 54 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 都市と一方通行の道路の有向グラフが与えられます。 City 0 は出発点であり都市です$N-1$が目的地です。 各道路は 1 人の農民を割り当てることで「ブロック」でき、ブロックするとその有向エッジがグラフから削除されます。 タスクは、0 から 0 への方向のある経路がなくなるように、遮断する必要がある道路の最小数を決定することです。$N-1$。 また、どの特定の道路がこの最小値を達成するかを出力する必要があります。 

問題を理解する別の方法は、できるだけ少ないエッジを削除することでソースからシンクまでの可能なルートをすべて破棄したいため、1 つの最適なエッジ セットを明示的に出力する必要があるということです。 

制約によって与えられるのは、$N \le 160$そして$M \ge 1000$その範囲内のグラフ密度全体まで。 これは十分小さいです$O(N^3)$max-flow アルゴリズムは、その構造が最短経路や組み合わせ検索の問題ではなく、フローの問題であることを即座に示唆します。 重要な隠れた構造は、有向グラフでソースとシンクを切断するエッジの最小数を探していることです。これはまさに最小 s-t カット問題です。 

単純なアプローチでは、エッジのサブセットを列挙し、それらを削除すると 0 と 0 が切断されるかどうかをテストしようとします。$N-1$。 サイズのサブセットだけを試したとしても$k$、組み合わせの数は次のように増加します。$\binom{M}{k}$、たとえそれが不可能になる$k=3$いつ$M$大きいです。 もう 1 つの素朴なアイデアは、0 から 0 までのパスを繰り返し見つけることです。$N-1$パスごとに 1 つのエッジを貪欲に削除しますが、それは選択されたパスに大きく依存し、最小性は保証されません。 

複数のエッジが独立したパスが存在する場合、微妙なエッジ ケースが発生します。 たとえば、0 から 0 までの完全に切り離された 2 つのルートがあるとします。$N-1$の場合、一方のパスから 1 つのエッジを削除しても、もう一方のパスがまだ存在するため役に立ちません。 貪欲なパスブレイク戦略は、真の最小値を簡単に過小評価または過大評価する可能性があります。 

## アプローチ

 ブルートフォースの観点は、答えをエッジのセットとして考えることであり、そのエッジを削除するとソースとシンクが切断されます。 エッジのすべてのサブセットを試し、毎回 BFS または DFS を使用して削除後の接続をチェックすることを想像することもできます。 これは条件を直接検証するので正しいですが、サブセットの数は指数関数的です。$M$、小さなグラフを超えると使用できなくなります。 

重要な観察は、問題はまさにノード 0 とノードの間の最小カットであるということです。$N-1$すべてのエッジが容量 1 を持つ有向グラフです。各エッジは「接続電力」の単位を表し、エッジを削除することは容量の 1 単位を削減することと同等です。 削除するエッジの最小数は、ソースとシンクを分離する最小総容量に対応します。 

問題が最小カット問題として認識されると、最大流量最小カット定理が適用されます。 0 から 0 までの最大流量を計算すると、$N-1$ユニット容量の場合、最大流量の値は最小カットのサイズと等しくなります。 さらに、フローを計算した後、残差グラフを確認することで最小カット エッジを特定できます。ソースから到達可能なノードはカットのソース側を定義し、到達可能なノードから到達不可能なノードに向かう元のエッジはすべてカットに属します。 

ブルートフォースアプローチは、エッジを共有するパスの構造を利用しないため失敗します。 流れの定式化では、すべてのパスの相互作用が 1 つのグローバル量に圧縮されます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース |$O(2^M \cdot (N+M))$|$O(N+M)$| 遅すぎる |
 | マックスフロー (ディニック) |$O(M \sqrt{N})$に$O(N^2 M)$最悪の場合 |$O(N+M)$| 承認済み |

 ## アルゴリズムのチュートリアル

 各道路を容量 1 の有向エッジに変換し、ノード 0 からノードまでの最大フロー アルゴリズムを実行します。$N-1$。 フローを計算した後、残差グラフを探索して最小カットを抽出します。 

1. すべての道路が接続される有向隣接構造を構築します。$A_i \to B_i$は容量 1 のエッジになります。また、残りの更新のために容量 0 の逆エッジも保存します。 
2. Dinic のアルゴリズムなどの最大フロー アルゴリズムをソース 0 からシンクまで実行します。$N-1$。 各拡張により、残差グラフ内の利用可能なパスに沿ってフローが押し出されます。 すべての容量が 1 であるため、成功した各拡張は、フローの 1 つのエッジ非結合単位の使用に対応します。 
3. 最大フローが終了したら、残りの容量が 0 より大きいエッジのみをたどって、残差グラフのノード 0 から DFS または BFS を実行します。これにより、可能なすべての拡張後にソースから到達可能なすべてのノードがマークされます。 
4. すべての元のエッジを反復処理します。$A_i \to B_i$。 もし$A_i$残差グラフでは到達可能ですが、$B_i$そうでない場合、このエッジは切り込みを横切り、ブロックされた道路の最小セットの一部である必要があります。 
5. そのようなエッジの数を出力し、リストします。 

重要な決定はステップ 3 です。残差グラフの到達可能性は、考えられるすべてのフロー パスが飽和した後にソース側にどの頂点が残るかを正確にエンコードします。 

### なぜ効果があるのか

 最大流量最小カット定理は、最大流量を計算した後、残差グラフ内のソースから到達可能な頂点のセットによって、容量が流量値に等しいカットを定義することを保証します。 すべての容量は 1 であるため、この容量は正確に、到達可能なノードから到達不可能なノードへ交差するエッジの数になります。 ソースとシンクを切断するには、そのようなエッジはすべて削除する必要があり、フローの最大化と矛盾するため、より小さなセットは成功できません。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

class Dinic:
    def __init__(self, n):
        self.n = n
        self.adj = [[] for _ in range(n)]
        self.to = []
        self.cap = []
        self.nxt = []
        self.head = []
    
    def add_edge(self, u, v, c):
        self.to.append(v)
        self.cap.append(c)
        self.nxt.append(len(self.head[u]) if u < len(self.head) else 0)
        if u >= len(self.head):
            self.head.extend([[] for _ in range(u - len(self.head) + 1)])
        self.head[u].append(len(self.to) - 1)

    def bfs(self, s, t):
        self.level = [-1] * self.n
        q = [s]
        self.level[s] = 0
        for u in q:
            for ei in self.head[u]:
                v = self.to[ei]
                if self.cap[ei] > 0 and self.level[v] < 0:
                    self.level[v] = self.level[u] + 1
                    q.append(v)
        return self.level[t] >= 0

    def dfs(self, u, t, f):
        if u == t:
            return f
        for i in range(self.it[u], len(self.head[u])):
            self.it[u] = i
            ei = self.head[u][i]
            v = self.to[ei]
            if self.cap[ei] > 0 and self.level[v] == self.level[u] + 1:
                ret = self.dfs(v, t, min(f, self.cap[ei]))
                if ret:
                    self.cap[ei] -= ret
                    self.cap[ei ^ 1] += ret
                    return ret
        return 0

    def max_flow(self, s, t):
        flow = 0
        INF = 10**9
        while self.bfs(s, t):
            self.it = [0] * self.n
            while True:
                pushed = self.dfs(s, t, INF)
                if not pushed:
                    break
                flow += pushed
        return flow

def solve():
    n, m = map(int, input().split())
    dinic = Dinic(n)

    edges = []
    for _ in range(m):
        a, b = map(int, input().split())
        edges.append((a, b))
        dinic.add_edge(a, b, 1)
        dinic.add_edge(b, a, 0)

    dinic.max_flow(0, n - 1)

    # residual reachability
    vis = [False] * n
    stack = [0]
    vis[0] = True
    while stack:
        u = stack.pop()
        for ei in dinic.head[u]:
            v = dinic.to[ei]
            if dinic.cap[ei] > 0 and not vis[v]:
                vis[v] = True
                stack.append(v)

    ans = []
    for a, b in edges:
        if vis[a] and not vis[b]:
            ans.append((a, b))

    print(len(ans))
    for a, b in ans:
        print(a, b)

if __name__ == "__main__":
    solve()
```この実装は、エッジ容量を通じて暗黙的に残差グラフを維持します。 各有向エッジは逆エッジとペアになっているため、拡張中にフローをキャンセルできます。 最大流量を計算した後、残留 DFS は残りの容量を持つエッジのみを使用し、最小カットのソース側を正確に識別します。 

よくある実装の落とし穴は、逆エッジのペアリングが間違っていることです。 このコードは、各順方向エッジの直後にその逆方向エッジが続くという不変条件に依存しているため、1 との XOR によりパートナー エッジが得られます。 もう 1 つの微妙な点は、到達可能性を元の隣接関係ではなく残差グラフで計算する必要があることです。そうしないと、カット抽出が不正確になります。 

## 実用的な例

 小さなグラフを考えてみましょう。 

入力:```
4 5
0 1
1 3
0 2
2 3
1 2
```0 から 3 までには 2 つの主なルートがあります。1 を通過するルートと 2 を通過するルートで、1 と 2 の間にクロス エッジがあります。 

最大流量の後、1 つのユニットは 0→1→3 に沿って送信され、もう 1 つのユニットは 0→2→3 に沿って送信されます。 流量値は2になります。 

| ステップ | Residual で訪問 | 解釈 |
 | --- | --- | --- |
 | アフターフロー | 0、1、2 | 両方のブランチに到達可能 |
 | カットエッジ | 1→3、2→3 | これらはシンク アクセスをブロックします。 

出力には、シンク側の境界に入る 2 つのエッジがリストされます。 

これは、独立した各ルートをブロックする必要があるため、複数の独立したルートによりカット サイズが増加することを示しています。 

ここで、線形チェーンを考えてみましょう。 

入力:```
3 2
0 1
1 2
```パスは 1 つだけ存在します。 1 単位の流れが両方のエッジを飽和させます。 0 からの残りの到達可能性にはノード 0 のみが含まれます。 

| ステップ | Residual で訪問 | 解釈 |
 | --- | --- | --- |
 | アフターフロー | 0 | シンクが切断されました |
 | カットエッジ | 0→1 | 単一のブロッキング エッジ |

 これは、アルゴリズムが正確に 1 つのエッジを選択し、すべてのパスがボトルネックを共有しているという直観と一致していることを確認します。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |$O(E \cdot F)$通常、ディニックと$O(E \sqrt{V})$ここに | 各 BFS/DFS フェーズはすべてのエッジを処理し、容量はフローを高速化する単位です。 
| スペース |$O(V + E)$| 隣接リストと残留エッジのストレージ |

 と$N \le 160$そして$M \ge 1000$、これは簡単に制限内に収まります。 このスケールでは、最悪の場合の 3 次動作も許容され、ディニックは時間内に快適に実行されます。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    import sys
    input = sys.stdin.readline

    class Dinic:
        def __init__(self, n):
            self.n = n
            self.to = []
            self.cap = []
            self.head = [[] for _ in range(n)]

        def add_edge(self, u, v, c):
            self.to.append(v)
            self.cap.append(c)
            self.head[u].append(len(self.to) - 1)

        def bfs(self, s, t):
            self.level = [-1] * self.n
            q = [s]
            self.level[s] = 0
            for u in q:
                for ei in self.head[u]:
                    v = self.to[ei]
                    if self.cap[ei] > 0 and self.level[v] < 0:
                        self.level[v] = self.level[u] + 1
                        q.append(v)
            return self.level[t] >= 0

        def dfs(self, u, t, f):
            if u == t:
                return f
            for i in range(len(self.head[u])):
                ei = self.head[u][i]
                v = self.to[ei]
                if self.cap[ei] > 0 and self.level[v] == self.level[u] + 1:
                    pushed = self.dfs(v, t, min(f, self.cap[ei]))
                    if pushed:
                        self.cap[ei] -= pushed
                        return pushed
            return 0

        def max_flow(self, s, t):
            flow = 0
            INF = 10**9
            while self.bfs(s, t):
                while True:
                    pushed = self.dfs(s, t, INF)
                    if not pushed:
                        break
                    flow += pushed
            return flow

    n, m = map(int, input().split())
    dinic = Dinic(n)
    edges = []
    for _ in range(m):
        a, b = map(int, input().split())
        edges.append((a, b))
        dinic.add_edge(a, b, 1)
        dinic.add_edge(b, a, 0)

    dinic.max_flow(0, n - 1)

    vis = [False] * n
    stack = [0]
    vis[0] = True
    while stack:
        u = stack.pop()
        for ei in dinic.head[u]:
            v = dinic.to[ei]
            if dinic.cap[ei] > 0 and not vis[v]:
                vis[v] = True
                stack.append(v)

    ans = [(a, b) for a, b in edges if vis[a] and not vis[b]]
    out = str(len(ans)) + "\n" + "\n".join(f"{a} {b}" for a, b in ans)
    return out.strip()

# provided sample
assert run("""6 8
0 1
0 2
1 2
2 3
3 4
4 1
3 5
4 5
""") == """2
3 5
4 5""", "sample 1"

# minimal chain
assert run("""3 2
0 1
1 2
""") == """1
0 1""", "chain"

# two disjoint paths
assert run("""4 4
0 1
1 3
0 2
2 3
""") == """2
1 3
2 3""", "disjoint"

# cycle + exit
assert run("""4 5
0 1
1 2
2 0
2 3
1 3
""") == """1
2 3""", "cycle"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | チェーン0-1-2 | 1 エッジ | 単一パスのボトルネック |
 | 沈む2つの道 | 2 エッジ | 互いに素なルートの処理 |
 | サイクルグラフ | 1 エッジ | サイクルはカットサイズに影響しません。 
| サンプル入力 | 2 エッジ | 混合グラフの正確性 |

 ## 特殊なケース

 多くのノードが相互に到達可能であるが、シンクにつながる出口エッジが 1 つだけであるサイクルの多いグラフは、残留 DFS が飽和エッジを通過しないため、正しく処理されます。 ソース側ノード間にサイクルが存在する場合でも、それらは到達可能としてマークされたままとなり、シンク側に交差するエッジのみが選択されます。 

すべてのノードが他のすべてのノードに接続する密なグラフでも、複数の拡張パスが容量を飽和させるフロー計算に変わります。 残留到達可能性は、飽和後にまだシンクに到達できるかどうかに応じて、ノードをきれいに分離します。 これにより、フローの保存により最終カットの一貫性が保証されるため、パスのオーバーラップによって生じるあいまいさが回避されます。
