---
title: "CF 102419H - 度内"
description: "無向グラフがあり、すべてのエッジは最終的に 2 つの端点のうちの 1 つを正確に指す必要があります。 値が (ai) として指定されている頂点の場合、正確に (ai) 個の入射エッジがその頂点を指している必要があります。 (ai=-1) の頂点には、最終的な入次数に制限がありません。"
date: "2026-08-16T09:02:42+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102419
codeforces_index: "H"
codeforces_contest_name: "SPC 2019"
rating: 0
weight: 102419
solve_time_s: 287
verified: false
draft: false
---

[CF 102419H - 度単位](https://codeforces.com/problemset/problem/102419/H)

 **評価:** -
 **タグ:** -
 **解決時間:** 4 分 47 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 無向グラフがあり、すべてのエッジは最終的に 2 つの端点のうちの 1 つを正確に指す必要があります。 値が (a_i) として指定されている頂点の場合、正確に (a_i) 個の入射エッジがその頂点を指している必要があります。 (a_i=-1) の頂点には、最終的な入次数に制限がありません。 

課題は、そのような方向性を見つけるか、存在しないことを証明することです。 出力には次のいずれかが含まれます`NO`、 または`YES`次に、元のすべてのエッジの方向が続きます。 元のエッジ ((u,v)) の場合、印刷`u v`は、エッジが (u) から (v) に向かうことを意味するため、(v) は 1 単位の入次数を受け取ります。 

元の制約には (n,m\le 2000) があり、平行エッジや自己ループはありません。 これは、多項式グラフ アルゴリズムとしては十分小さいですが、方向を列挙するアルゴリズムは除外されます。 (2^m) 個の可能な方向があり、(m=2000) になると、1 つの方向を一定時間で調べることさえすでに絶望的になります。 (O(n+m)) 個の頂点とエッジを持つフロー ネットワークはメモリ制限内に収まり、標準の積分最大フロー アルゴリズムが適しています。 

誤って処理されやすいケースがいくつかあります。 

考慮する```
2 1
0 0
1 2
```両方の頂点は次数ゼロを要求しますが、唯一のエッジはどこかを指す必要があります。 正しい答えは、`NO`。 すべての要求された値が対応する程度以下であることを単にチェックするだけでは、このケースは誤って受け入れられてしまいます。 

ここで考えてみましょう```
2 1
2 -1
1 2
```頂点 1 は次数 1 を持っていますが、次数 2 を要求しています。 正しい答えは、`NO`。 フロー構築では、要求された量を上限として扱うのではなく、正確な要求量を尊重する必要があります。 

エッジが 2 つの拘束された頂点を結合する場合にも、あまり明らかではない問題があります。 例えば、```
2 1
0 1
1 2
```エッジは強制的に頂点 2 を指すようになります。制約された頂点を満たすためにいくつかのエッジを選択するだけのアプローチでは、制約された 2 つの頂点間のすべてのエッジがいずれかの頂点に割り当てられるようにする必要があります。 このようなエッジを未割り当てのままにしておくと、どちらの端点も拘束されていないため、後でそれを拘束されていない頂点に向けることによって修復することはできません。 

最後に、2 つのエンドポイントが制約されていないエッジは、制約解決部分にまったく参加する必要がありません。 すべての制約された入次数が満たされた後、そのエッジは任意の方向に向けることができます。 

## アプローチ

 直接的な総当たりアプローチは、すべてのエッジに対して両方向を試行することです。 深さ優先検索では、エッジごとに 1 つのバイナリ決定を行うことができ、完全な方向に到達した後、すべての角度をカウントして制約をチェックします。 これは正しいです。考えられるすべての方向が検索ツリーの 1 つのブランチにのみ表示されるからです。 問題はそのサイズです。(2^m) 個の葉があり、向きのチェックには (O(m+n)) かかり、(O(2^m(m+n))) の作業が必要になります。 (m=2000) では、これは完全に不可能です。 

有益な観察は、指向性エッジは、1 つの単位の入次数を 1 つの端点に割り当てているとみなすことができるということです。 エッジの方向を直接決定する代わりに、どのエンドポイントがそのユニットを受信するかを示すフロー決定を作成できます。 

関連するすべての元のエッジに対して、フロー ノードを作成します。 エッジ ノードから 1 つのユニットをどちらかのエンドポイントに送信できます。 制約された頂点には、そこに到達する必要がある正確な量 (a_i) があります。 2 つの制約された頂点を結合するエッジはそのユニットをどこかに送信する必要がありますが、制約されていないエンドポイントを持つエッジは制約された頂点に寄与せずにフロー ネットワークから離れる可能性があります。 

正確な頂点要件と必須の拘束エッジと拘束エッジは、フローの下限と上限によって自然に表されます。 これにより、実行可能循環問題が生じます。 

エッジごとに 3 つのケースを区別します。 両方のエンドポイントが制約されている場合、そのエッジ ノードは正確に 1 つのユニットを受信する必要があります。 ちょうど 1 つのエンドポイントが制約されている場合、そのエッジ ノードはその制約されたエンドポイントに 0 または 1 つのユニットを送信できます。 どちらのエンドポイントも制約されていない場合は、制約を解決するときにそれを無視し、後で任意に方向を設定します。 

制約されたすべての頂点 (v) について、(v) からシンクまでのエッジは、(a_v) に等しい下限と上限の両方を持ちます。 したがって、正確に (a_v) 個の選択されたエッジ ユニットが (v) に到達する必要があります。 

標準の下限変換では、下限が削除され、結果として生じる不均衡がすべてのノードで記録されます。 その後、スーパー ソースとスーパー シンクが追加され、通常の最大流量によって必要な循環が存在するかどうかが決まります。 

重要な関係は、頂点 (v) に到達するフローの単位が、(v) に向かう 1 つの元のエッジに正確に対応するということです。 フローは積分であるため、選択されたすべてのエッジが 1 つのエンドポイントに割り当てられ、部分的に分割されることはありません。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | (O(2^m(m+n))) | (O(m+n)) | 遅すぎる |
 | 下限循環 + ディニック | (O(V^2E)) 最悪の場合 | (O(V+E)) | 承認済み |

 ここで、構築されたネットワークは (V=O(n+m)) および (E=O(n+m)) になります。 (n,m\le2000) の場合、ネットワークには数千の頂点とエッジしかなく、このまばらな、ほとんどがユニット容量のネットワーク上での Dinic の実際の動作は、制限を十分に満たします。 

## アルゴリズムのチュートリアル

 1. グラフを読み取り、すべての頂点を (a_i\ne-1) で拘束済みとしてマークします。 最終的な答えは元のエッジの順序で出力する必要があるため、すべてのエッジの元のエンドポイントを保存します。 
2. ソース (S)、シンク (T)、少なくとも 1 つの制約された頂点に接触する元のエッジごとに 1 つのノード、および制約された頂点ごとに 1 つのノードを含むフロー ネットワークを構築します。 

2 つの制約されていない端点を持つエッジは、必要な入次数に影響を与えることができないため、省略されます。 後は安全に向きを変えることができます。 
3. 関連するすべての元のエッジ (e=(u,v)) に対して、エッジ ノード (E_e) を作成します。 

(u) と (v) の両方が制約されている場合は、次を追加します。

[
 S\右矢印 E_e
 】

 下限と上限は両方とも (1) に等しい。 エッジは 1 つの入力エッジを (u) または (v) のいずれかに提供する必要があります。 

少なくとも 1 つのエンドポイントが制約されていない場合は、下限 (0) と上限 (1) を使用します。 このようなエッジは、制約されたエンドポイントに寄与することができますが、そうする必要はありません。 

(E_e) から、元のエッジのすべての制約されたエンドポイントに容量 (1) エッジを追加します。 (E_e\rightarrow v) を介して 1 つのユニットを送信することは、元のエッジを (v) の方向に向けることを意味します。 
4. 制約された頂点 (v) ごとにエッジを追加します。 

[
 v\右矢印 T
 】

 下限と上限は両方とも (a_v) です。 

(v) から出る量は正確に (a_v) になるように強制されるため、流れ保存によって正確に (a_v) のユニットが (v) に入るように強制されます。 これはまさに必要な入次数です。 
5. 容量 (m) のエッジ (T\rightarrow S) を追加します。 これにより、ネットワークが循環的に閉じられます。 保存により、拘束された頂点に割り当てられたユニットの総数と等しくなるため、その正確な量は重要ではありません。 
6. 下限 (L) と上限 (R) を持つすべての有界エッジ ((u,v)) を、容量の通常のエッジ (R-L) に変換します。 バランスの取れた配列を維持します。 (u) の残高から (L) を引き、(v) の残高に (L) を加えます。 

天びんには、下限によってすでに強制されている流れの影響が記録されます。 残りの通常の流れは、これらの不均衡を補う必要があります。 
7. スーパーソース (SS) とスーパーシンク (TT) を追加します。 ノードの残高がプラスの場合は、その残高と等しい容量の (SS\rightarrow v) を追加します。 マイナス残高がある場合は、絶対残高と等しい容量の (v\rightarrow TT) を追加します。 

実行可能な循環は、(SS) から (TT) までの最大流量がこれらすべてのバランス エッジを飽和するときに正確に存在します。 そうでない場合は、印刷してください`NO`。 
8. 循環が可能であれば、元の関連する各エッジのエッジ ノードから制約されたエンドポイントまでのフローを検査します。 1 つのユニットが (u) に移動した場合、元のエッジを (u) に向けます。 1 つのユニットが (v) に移動した場合、そのユニットを (v) に向けます。 

制約付き-制約付きエッジには、そのソースからエッジへのフローが強制的に 1 つになるため、そのようなユニットが常に 1 つだけあります。 制約のないエンドポイントが 1 つあるエッジの場合、フローがゼロであるということは、エッジをその制約のないエンドポイントに向けることを意味します。 2 つの拘束されていない端点を持つエッジの場合は、どちらかの方向を選択します。 
9. 印刷`YES`そして、結果として生じるすべての元のエッジの方向。 

構築全体を通じて不変なのは、拘束された頂点に入るすべてのユニットが、その頂点が頭である 1 つだけの元のエッジを表すということです。 制約された頂点の正確な下限と上限により、そのようなユニットの要求された数が正確に強制されます。 エッジ ノードを通過する必須のフローにより、制約された 2 つの頂点を結合するすべてのエッジがヘッドを受け取ることが保証されます。 したがって、実現可能な循環は有効な方向に直接マッピングされます。 逆に、有効な方向はすべてのエッジ ノードを介してそのエッジの先頭である頂点に 1 つのユニットを送信することによって実行可能な循環を誘導するため、フロー テストは真に有効なインスタンスを拒否できません。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

sys.setrecursionlimit(1_000_000)

class Dinic:
    def __init__(self, n):
        self.n = n
        self.g = [[] for _ in range(n)]

    def add_edge(self, u, v, cap):
        idx = len(self.g[u])
        self.g[u].append([v, cap, len(self.g[v])])
        self.g[v].append([u, 0, idx])
        return idx

    def bfs(self, s, t):
        level = [-1] * self.n
        level[s] = 0
        q = [s]
        head = 0

        while head < len(q):
            u = q[head]
            head += 1

            for v, cap, rev in self.g[u]:
                if cap > 0 and level[v] == -1:
                    level[v] = level[u] + 1
                    q.append(v)

        self.level = level
        return level[t] != -1

    def dfs(self, u, t, pushed):
        if u == t:
            return pushed

        g_u = self.g[u]
        while self.it[u] < len(g_u):
            i = self.it[u]
            v, cap, rev = g_u[i]

            if cap > 0 and self.level[v] == self.level[u] + 1:
                got = self.dfs(v, t, min(pushed, cap))
                if got:
                    g_u[i][1] -= got
                    self.g[v][rev][1] += got
                    return got

            self.it[u] += 1

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

def solve(data):
    it = iter(map(int, data.split()))
    n = next(it)
    m = next(it)

    a = [next(it) for _ in range(n)]
    edges = [(next(it), next(it)) for _ in range(m)]

    constrained = [x != -1 for x in a]

    # Node layout:
    # 0 ... n-1                 constrained vertex slots
    # edge_base ... edge nodes
    # S, T, SS, TT
    #
    # We only need vertex nodes for constrained vertices.
    vertex_id = [-1] * n
    vertex_nodes = []

    for v in range(n):
        if constrained[v]:
            vertex_id[v] = len(vertex_nodes)
            vertex_nodes.append(v)

    k = len(vertex_nodes)
    edge_base = k
    relevant = []

    for i, (u, v) in enumerate(edges):
        u -= 1
        v -= 1

        if constrained[u] or constrained[v]:
            relevant.append(i)

    r = len(relevant)

    S = k + r
    T = S + 1
    SS = T + 1
    TT = SS + 1
    N = TT + 1

    dinic = Dinic(N)
    balance = [0] * N

    # Store references to edge-node -> constrained-vertex arcs.
    # Each entry is (edge_index, original_endpoint, network_u, arc_index).
    choice_arcs = []

    def add_bounded(u, v, low, high):
        if low > high:
            return False

        cap = high - low
        dinic.add_edge(u, v, cap)

        balance[u] -= low
        balance[v] += low
        return True

    # Source -> edge node.
    for pos, ei in enumerate(relevant):
        u, v = edges[ei]
        u -= 1
        v -= 1

        enode = edge_base + pos

        if constrained[u] and constrained[v]:
            low = high = 1
        else:
            low, high = 0, 1

        if not add_bounded(S, enode, low, high):
            return "NO\n"

        if constrained[u]:
            idx = dinic.add_edge(enode, vertex_id[u], 1)
            choice_arcs.append((ei, u, enode, idx))

        if constrained[v]:
            idx = dinic.add_edge(enode, vertex_id[v], 1)
            choice_arcs.append((ei, v, enode, idx))

    # Every constrained vertex must receive exactly a[v] units.
    for v in vertex_nodes:
        need = a[v]
        if need < 0:
            continue

        # A vertex cannot receive more than its graph degree.
        # The lower-bound construction would reject this anyway,
        # but this check avoids creating an obviously impossible edge.
        degree = 0
        for u, w in edges:
            u -= 1
            w -= 1
            if u == v or w == v:
                degree += 1

        if need > degree:
            return "NO\n"

        if not add_bounded(vertex_id[v], T, need, need):
            return "NO\n"

    # Close the network into a circulation.
    add_bounded(T, S, 0, m)

    # Satisfy all lower-bound imbalances.
    required = 0

    for v in range(N - 2):
        if balance[v] > 0:
            dinic.add_edge(SS, v, balance[v])
            required += balance[v]
        elif balance[v] < 0:
            dinic.add_edge(v, TT, -balance[v])

    got = dinic.max_flow(SS, TT)

    if got != required:
        return "NO\n"

    # Start with arbitrary directions.
    answer = []
    for u, v in edges:
        answer.append([u, v])

    # A relevant edge with flow into a constrained endpoint is directed
    # toward that endpoint.
    selected = {}

    for ei, endpoint, enode, idx in choice_arcs:
        # The residual capacity of the forward edge is 0 exactly when
        # one unit of flow is using it.
        if dinic.g[enode][idx][1] == 0:
            selected[ei] = endpoint

    for ei in relevant:
        u, v = edges[ei]
        u -= 1
        v -= 1

        if ei in selected:
            head = selected[ei]

            if head == u:
                answer[ei] = [v + 1, u + 1]
            else:
                answer[ei] = [u + 1, v + 1]
        else:
            # No constrained endpoint receives this edge.
            # This is possible only when at least one endpoint is free.
            if not constrained[u]:
                answer[ei] = [v + 1, u + 1]
            else:
                answer[ei] = [u + 1, v + 1]

    out = ["YES"]
    for u, v in answer:
        out.append(f"{u} {v}")

    return "\n".join(out) + "\n"

def main():
    data = sys.stdin.buffer.read()
    sys.stdout.write(solve(data.decode()))

if __name__ == "__main__":
    main()
```の`Dinic`クラスはすべての残りのエッジを次のように格納します`[to, capacity, reverse_index]`。 リバース インデックスを使用すると、隣接リストを検索せずに、オーグメンテーションがリバース残余容量を即座に更新できるようになります。 

の`add_bounded`関数は下限変換の中核です。 元の境界 (L\le f\le R) の場合、残余容量 (R-L) が作成され、必須 (L) 単位が記録されます。`balance`。 プラスのバランスは、ノードに追加の発信フローによって補償する必要がある必須の着信フローがあることを意味します。そのため、スーパーソースがノードに接続されます。 

実際の頂点ノードが必要なのは、拘束された頂点のみです。 2 つの自由な頂点間のエッジは要件に影響を及ぼさないため、フロー ネットワークからエッジを削除すると、実現可能性を変えることなく構築が小さくなります。 

の`choice_arcs`配列は、どの残存エッジが各元のエッジの考えられる各ヘッドに対応するかを正確に記憶します。 最大フローが成功した後、飽和した選択アークは、1 つのユニットがそのエンドポイントに送信されたことを意味します。 関連するすべてのエッジ ノードは、両方のエンドポイントが制約されている場合は 1 つのユニットを正確に受け取り、自由なエンドポイントが存在する場合は最大でも 1 つのユニットを受け取るため、選択されたヘッドについてあいまいさはありません。 

次数チェックは、循環自体が不可能な下限を検出するため、技術的には冗長ですが、フローを実行する前に最も明らかな不可能なケースを処理します。 Python の整数には任意の精度があるため、整数のオーバーフローの問題はありません。 

出力の再構成は意図的に任意の方向から開始されます。 制約ネットワークに参加しているエッジのみが上書きされます。 選択されていない関連エッジには空きエンドポイントが必要であるため、そのエンドポイントに向けても正確な次数要件に違反することはできません。 

## 実用的な例

 サンプル 1 の場合、制約された頂点は (1,2,3,5) で、要求された次数は (1,2,1,0) です。 Vertex 4 は無料です。 

選択されたヘッドの有効なシーケンスを以下に示します。 残りの需要は、制約された各頂点が依然として必要とする入力エッジの数です。 

| エッジ | 選択されたヘッド | エッジ後の残りの需要 |
 | --- | --- | --- |
 | (1-2) | 2 | (a=(1,1,1,0)) |
 | (1-3) | 1 | (a=(0,1,1,0)) |
 | (2-3) | 2 | (a=(0,0,1,0)) |
 | (3-4) | 3 | (a=(0,0,0,0)) |
 | (4-5) | 4 | (a=(0,0,0,0)) |

 最後のエッジは、その需要がすでにゼロであるため、制約された頂点 5 には割り当てられません。 代わりに、自由頂点 4 に向けられます。 結果として得られる方向は、サンプルの方向とまったく同じです。```
1 2
3 1
3 2
4 3
5 4
```トレースは中心不変式を示しています。 制約されたエンドポイントがヘッドとして選択されるたびに、その残りの要求は 1 つずつ減り、すべての正確な要求が満たされた場合にのみ、循環は方向を受け入れます。 

サンプル 2 の唯一の違いは、頂点 5 が 1 つの入力エッジを必要とすることです。 最初の 4 つのエッジは以前とまったく同じように処理でき、頂点 5 に需要の 1 単位が残ります。 

| エッジ | 選択されたヘッド | エッジ後の残りの需要 |
 | --- | --- | --- |
 | (1-2) | 2 | (a=(1,1,1,1)) |
 | (1-3) | 1 | (a=(0,1,1,1)) |
 | (2-3) | 2 | (a=(0,0,1,1)) |
 | (3-4) | 3 | (a=(0,0,0,1)) |
 | (4-5) | 5 | (a=(0,0,0,0)) |

 したがって、最後のエッジは 4 から 5 を指します。結果の方向は次のようになります。```
1 2
3 1
3 2
4 3
4 5
```ここで、トレースは 1 つの自由端点と 1 つの制約された端点を持つエッジを実行します。 サンプル 1 では、そのエッジは制約された端点を避けることができましたが、サンプル 2 では、頂点 5 での正確な要求により、強制的にそこを指すようになります。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O(V^2E)) ディニックにとって最悪のケース | 構築された循環ネットワークは (V=O(n+m)) および (E=O(n+m)) | を持ちます。 
| スペース | (O(V+E)) | 残差グラフ、残高、元のエッジ、および再構成データはすべて線形です。 

(n,m\le2000) の場合、構築されたネットワークには、残りのエッジが追加される前に (O(4000)) 個の頂点と (O(4000)) ～ (O(6000)) 個の論理エッジのみが含まれます。 グラフはまばらで、ほぼすべてのエッジ選択容量は 1 つです。 これは 256 MB のメモリ制限内に十分収まっており、最適化された Dinic 実装では 1 秒の制限に対して実用的です。 

## テストケース

 この問題では有効な方向が許容されるため、テストでは完全な出力テキストを 1 つの固定された答えと安全に比較できません。 下のハーネスは、`YES`または`NO`結果と、`YES`は、印刷されたすべての有向エッジが元のエッジに対応し、すべての制約された頂点が要求された次数を正確に受け取ることを検証します。```python
# Save the editorial solution as solution.py before running these tests.

from solution import solve

def run(inp: str) -> str:
    out = solve(inp)
    tokens = out.split()

    data = list(map(int, inp.split()))
    p = 0

    n = data[p]
    m = data[p + 1]
    p += 2

    a = data[p:p + n]
    p += n

    edges = []
    for _ in range(m):
        u = data[p]
        v = data[p + 1]
        p += 2
        edges.append((u, v))

    if not tokens:
        raise AssertionError("empty output")

    if tokens[0] == "NO":
        return "NO"

    assert tokens[0] == "YES", f"bad first token: {tokens[0]}"
    assert len(tokens) == 1 + 2 * m, "wrong number of output vertices"

    original = {tuple(sorted(e)) for e in edges}
    used = set()
    indeg = [0] * (n + 1)

    q = 1
    for _ in range(m):
        u = int(tokens[q])
        v = int(tokens[q + 1])
        q += 2

        assert 1 <= u <= n
        assert 1 <= v <= n
        assert u != v
        assert tuple(sorted((u, v))) in original
        assert tuple(sorted((u, v))) not in used, "an original edge was repeated"

        used.add(tuple(sorted((u, v))))
        indeg[v] += 1

    assert len(used) == m

    for v in range(1, n + 1):
        if a[v - 1] != -1:
            assert indeg[v] == a[v - 1], (
                f"vertex {v}: expected {a[v - 1]}, got {indeg[v]}"
            )

    return "YES"

# Sample 1
assert run("""\
5 5
1 2 1 -1 0
1 2
1 3
2 3
3 4
4 5
""") == "YES", "sample 1"

# Sample 2
assert run("""\
5 5
1 2 1 -1 1
1 2
1 3
2 3
3 4
4 5
""") == "YES", "sample 2"

# Minimum-size valid graph.
assert run("""\
2 1
0 1
1 2
""") == "YES", "minimum valid case"

# Boundary case: requested in-degree exceeds the actual degree.
assert run("""\
2 1
2 -1
1 2
""") == "NO", "degree upper boundary"

# Both endpoints are constrained and both demand zero.
# The single edge has nowhere valid to point.
assert run("""\
2 1
0 0
1 2
""") == "NO", "mandatory constrained-constrained edge"

# Maximum-size graph with all vertices unconstrained.
# A 2000-cycle has 2000 edges and needs no constrained flow at all.
n = 2000
cycle_edges = "\n".join(
    f"{i} {i + 1}" for i in range(1, n)
) + f"\n{n} 1\n"

max_case = f"{n} {n}\n" + ("-1 " * (n - 1)) + "-1\n" + cycle_edges

assert run(max_case) == "YES", "maximum-size all-free case"

# All-equal exact demands on a cycle.
all_equal_case = f"{n} {n}\n" + ("1 " * (n - 1)) + "1\n" + cycle_edges

assert run(all_equal_case) == "YES", "maximum-size all-equal case"

print("all tests passed")
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`2 1`、要求`0 1`、 角`1 2`|`YES`| 最小有効インスタンスと正確な 1 ユニットの需要 |
 |`2 1`、要求`2 -1`、 角`1 2`|`NO`| 要求された度数が利用可能な度数よりも大きい |
 |`2 1`、要求`0 0`、 角`1 2`|`NO`| 拘束された 2 つの頂点間のエッジを未割り当てのままにすることはできません。 
| あらゆる要求に対応する 2000 サイクル`-1`|`YES`| グラフの最大サイズと無関係な自由辺の処理 |
 | あらゆる要求に対応する 2000 サイクル`1`|`YES`| すべての頂点が制約され、正確な要求が等しい最大サイズのグラフ |

 ## 特殊なケース

 最初のエッジ ケースは、要求された次数が頂点次数よりも大きいです。 のために```
2 1
2 -1
1 2
```頂点 1 には次数 1 がありますが、2 つの入力エッジが必要です。 この構造では、頂点 1 から (T) までのエッジに 2 の下限と上限が追加されますが、唯一の入射エッジは最大 1 ユニットに寄与できます。 循環は下限を満たすことができないため、最大流量は失敗し、アルゴリズムは次のように出力します。`NO`。 

2 番目のエッジ ケースは、端点が両方とも制約されているエッジです。 のために```
2 1
0 0
1 2
```(1-2) のエッジ ノードは、両方のエンドポイントが制約されているため、(S) から必須の 1 ユニットを受け取ります。 そのユニットを頂点 1 または頂点 2 にのみ送信できますが、両方の頂点の正確な発信先 (T) 要件はゼロです。 結果として生じる不均衡は修復できないため、フローは実行不可能であることを報告します。 これは、制約ネットワーク内ですべてのエッジをオプションとして扱うというよくある間違いを捉えています。 

3 番目のエッジ ケースは、制約された頂点と制約されていない頂点の間のエッジです。 考慮する```
2 1
0 -1
1 2
```エッジ ノードには、制約された頂点 1 に対してオプションの容量 1 があります。頂点 1 にはゼロが必要なため、フローはその選択を通じて何も送信しません。 再構築中、アルゴリズムは、エッジが制約されたエンドポイントとして選択されていないことを確認し、代わりに頂点 2 の方向を指します。 出力は効果的に`1 2`、必要に応じて頂点 1 に 0 度を与えます。 

4 番目のエッジ ケースは、すべての頂点が制約されていないグラフです。 すべて (a_i=-1) の 2000 サイクルでは、フロー ネットワークには制約された頂点要件がなく、サイクル エッジは循環に参加する必要がありません。 アルゴリズムは単にそれらすべてに対して任意の方向を選択し、出力します。`YES`。 これが、自由エッジと自由エッジを流れモデルから安全に省略できる理由です。 

最後のケースは、すべてが制約された状況です。 三角形の場合```
3 3
1 1 1
1 2
2 3
3 1
```すべてのエッジは 2 つの制約された頂点を結合するため、すべてのエッジ ノードは正確に 1 つのユニットを保持することを強制されます。 3 つの頂点にはそれぞれ 1 つのユニットが必要なので、循環によって 3 つのユニットを 3 つの頂点に送信でき、たとえば有向サイクル (1\rightarrow2)、(2\rightarrow3)、(3\rightarrow1) が生成されます。 各頂点は正確に 1 つのエッジを受け取ります。 このケースは、制約されていない頂点がない場合に、下限構成が元の指定度方向の問題も処理することを示しています。
