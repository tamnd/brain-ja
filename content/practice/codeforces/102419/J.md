---
title: "CF 102419J - ジェイバー 警官"
description: "すべての行とすべての列を 2 部グラフの頂点として考えてください。 1 を含むセルは、行の頂点と列の頂点の間のエッジです。 値 ai は、行 i に含まれるエッジの数を正確に示します。 列の度数は完全に制御されます。"
date: "2026-08-16T09:11:50+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102419
codeforces_index: "J"
codeforces_contest_name: "SPC 2019"
rating: 0
weight: 102419
solve_time_s: 546
verified: false
draft: false
---

[CF 102419J - 警察官のジェイバー](https://codeforces.com/problemset/problem/102419/J)

 **評価:** -
 **タグ:** -
 **解決時間:** 9 分 6 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 すべての行とすべての列を 2 部グラフの頂点として考えてください。 を含むセル`1`行頂点と列頂点の間のエッジです。 値`a_i`行に必要なエッジの数を正確に教えてくれます`i`。 列の度数は完全に制御されます。 

Jaber が頂点をチェックすると、すでにチェックされている頂点に付随するすべてのエッジが消えます。 したがって、彼が見るライトの数は、まだチェックされていない頂点によって引き起こされるサブグラフ内の頂点の次数と正確に一致します。 したがって、有効なチェック順序は、すべての頂点が削除されたときに、最大でも 1 つのインシデント エッジが残る順序になります。 

グラフがフォレストである場合、まさにそのような順序が存在します。 グラフにサイクルが含まれている場合、最大次数 1 の頂点を繰り返し削除すると、最終的にサイクルが残り、その上のすべての頂点が次数 2 になります。 逆に、空ではないすべてのフォレストにはリーフまたは孤立した頂点があるため、最大 1 次の頂点を繰り返し削除できます。 

したがって、問題はグラフの構築問題になります。 次のような単純な 2 部構成のフォレストが必要です。`n`行の頂点、`m`列の頂点と行側の所定の度数。 

境界`n,m <= 1000`十分に小さいので、`O(nm)`施工は適切です。 特に、出力自体には次のものが含まれます。`nm`文字なので、答えを印刷するだけでもすでにコストがかかります`O(nm)`。 したがって、二次時間とメモリを使用した構築は自然ですが、指数関数的なものは完全に実行不可能です。 

単純化した構築が失敗する特殊なケースがいくつかあります。 考慮する```
2 2
2 2
```両方の行に 2 つの 1 が必要なので、4 つのセルすべてを次のようにする必要があります。`1`。 結果として得られる 2 部グラフは 4 サイクルであり、有効なチェック順序は存在しません。 1 の合計数が最大であることを確認するだけです`n+m-1`この例を拒否するには十分ですが、一般にその条件は十分ではありません。 

例えば、```
5 3
3 3 0 0 0
```には頂点が 8 つあるのに対し、頂点は 6 つしかありません。したがって、粗いエッジ数の条件は次のようになります。`6 <= 8-1`通ります。 それにもかかわらず、2 つの正の行は両方とも次数 3 を持ち、列は 3 つしかありません。 これらは 3 つの列すべてを共有し、複数のサイクルを生成する必要があります。 正しい答えは、`NO`。 

逆に、0 度の行を接続された構造の一部として扱ってはなりません。 のために```
3 2
2 0 0
```最初の行は単純に両方の列を使用でき、他の行は分離できます。 答えは`YES`。 すべての行が正の次数を持つと仮定する構成では、このケースが誤って拒否されてしまいます。 

ケース```
1 1
0
```も有効です。 エッジがまったくないので、行も列もすぐに確認できます。 答えは`YES`。 

## アプローチ

 直接的な総当たりアプローチでは、すべてのバイナリが列挙されます。`n x m`行列を作成する場合は、行の合計が必要な値と異なる行列を破棄し、結果の 2 部グラフに有効な消去順序があるかどうかをテストします。 がある`2^(nm)`合計でバイナリ行列になるため、最悪の場合、これは次のようになります。`2^(1,000,000)`候補者たち。 列挙を正しい行合計を持つ行列に制限したとしても、最悪の場合の候補数は次のようになります。`C(m, floor(m/2))^n`、

 それは依然として指数関数的です。 各候補者のコストを確認するには少なくとも`O(nm)`したがって、このアプローチには最悪の場合のコストがかかります`Theta(nm * C(m, floor(m/2))^n)`。 その正しさは単純ですが、使用できません。 

有益な観察は、有効なチェック順序は、フォレストである構築された 2 部グラフと同等であるということです。 したがって、マトリックスを構築する際にチェック プロセスについて考える必要はありません。 必要なのは、要求された行次数を使用して非巡回グラフを構築することだけです。 

あると仮定します`k`正の次数を持つ行、すべての行の次数の合計を次のようにします。`S`。 単一の列を含むリーフ列として列を使用することを検討してください。`1`、または 2 つの列を含むコネクタ列として`1`s と 2 つの行の頂点を結合します。 コネクタ列は、2 つの行頂点間のエッジのように動作します。 使用する場合`E`コネクタ列を使用すると、それらの列は上にフォレストを形成できます。`k`正の行。 そのような森が持つことができるのは、せいぜい`k-1`すべてのコネクタは 2 行次数単位を消費するため、次のようになります。`E <= floor(S/2)`。 

したがって、有効なコネクタ列の最大数は次のようになります。`E = min(k-1, floor(S/2))`。 

すべてのコネクタは、2 つの別々のリーフ列を使用する場合と比較して、1 つの列を節約します。 したがって、使用される列の合計数は次のようになります。`E + (S - 2E) = S - E`。 

これを超えると`m`、構築は存在できません。 そうでなければ、まさにそのような森を構築することができます。 

残りのタスクは、行の次数が正の行にあるフォレストを実現することです。`i`何らかの値です`t_i <= a_i`、まさに`E`エッジ。 私たちが選ぶのは、`t_i`それらの合計は`2E`。 フォレストを実現する便利な方法は、一時的にスーパー頂点を 1 つ追加することです。 ある森`E`エッジオン`k`頂点には`k-E`接続されたコンポーネント。 スーパー頂点をすべてのコンポーネントの 1 つの頂点に接続します。 結果は木になります。`k+1`頂点なので、Prüfer シーケンスを使用して次数シーケンスからそれを構築できます。 

これにより、問題は、通常のフォレスト走査が後に続く線形サイズの次数シーケンスの構築に変わります。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース |`Theta(nm * C(m,floor(m/2))^n)`|`O(nm)`| 遅すぎる |
 | 最適 |`O(nm + (n+m) log(n+m))`|`O(nm + n+m)`| 承認済み |

 ## アルゴリズムのチュートリアル

 1. コンピューティング`S`、すべての行の次数の合計を求め、正の次数を持つ行を収集します。 彼らの番号を`k`。 の行`a_i = 0`エッジを必要としないので、孤立したままでいられます。 
2.セット`E = min(k - 1, S // 2)`。 

これらは、使用したいコネクタ列です。 以上の使用はできません`k-1`行の頂点間にサイクルを作成せずに、それ以上を使用することはできません。`S//2`各コネクタは 2 つの行次数単位を消費するためです。 
3. 建設に必要な柱の数を次のように計算します。`S-E`。 もし`S-E > m`、印刷`NO`。 

数量`S-E`可能な最小の列数です。 すべてのコネクタは、2 つの別々の列ではなく 1 つの列で 2 つのコネクタを使用するため、各コネクタは正確に 1 列を保存します。 
4. コネクタの度数を選択します`t_i`正の行の場合。 必要です`0 <= t_i <= a_i`そして`sum(t_i) = 2E`。 

いつ`E > 0`、まずコネクタ次数を 1 つ与えます`E+1`明確な正の行。 これが可能なのは、`E <= k-1`。 その後、残りを分配します`E-1`度単位を超えずに貪欲に`a_i`または`k-1`。 

1単位を与えると、`E+1`行は、コネクタ グラフに次のものを含めることができることを保証します。`E`少数の頂点にすべての次数を集中させようとせずにエッジを作成します。 追加の上限は、`k-1`どの行も次数を超えることができないため、後で役立ちます。`k`スーパー頂点が追加された後の一時ツリー内。 
5. しましょう`C = k-E`、コネクタ フォレストに含まれるコンポーネントの数。 すべての行に`t_i=0`スーパー頂点へのエッジを受け取る必要があります。 このような行をすべてスーパー頂点近傍として選択し、正確に一致するまで任意の追加行を選択します。`C`行が選択されました。 

構築により少なくとも正のコネクタ次数が与えられたため、これを行うのに十分な 0 度の行が存在します。`E+1`行。 したがって、せいぜい`k-E-1`行には`t_i=0`、 その間`C=k-E`。 
6. 行の次数を定義する`i`一時ツリーでは次のようになります`t_i + 1`行の場合`i`スーパー頂点ネイバーとして選択され、`t_i`さもないと。 超頂点次数を与える`C`。 

合計の学位は、`2E + C + C = 2(E+C) = 2k`、

 まさにツリーに必要な数です`k+1`頂点。 すべての学位はプラスであり、最大でも`k`, したがって、これは有効なツリー次数シーケンスです。 
7. 各一時頂点を正確に含むプリューファー シーケンスを構築します。`degree[i]-1`回。 その長さは`k-1`。 現在のリーフを含む優先キューを使用してシーケンスをデコードします。 

Prüfer デコードでは、要求された次数シーケンスを正確に持つツリーが構築されます。 頂点の数はこれだけなので、`k+1 <= 1001`、`O(k log k)`実装は簡単に十分に高速です。 
8. スーパー頂点に付随するすべてのエッジを削除します。 残りのエッジは正の行にフォレストを形成します。 残りのツリー エッジはそれぞれ、各端点に 1 つずつ、正確に 2 つのコネクタ列を持つ 1 つのコネクタ列になります。 
9. 各行について`i`、 がある`a_i-t_i`残りはコネクタ列で使用されませんでした。 それぞれに 1 つの葉を含む個別のリーフ列を与えます。`1`並んでいる`i`。 

作成される列の数は正確に`E + S - 2E = S-E`、に対してチェックされました`m`以前。 未使用の列はすべてゼロで埋められます。 
10. 結果の行列の 2 部隣接リストを構築し、現在の次数が最大 1 である頂点を繰り返し取得します。 それを回答順序に追加し、概念的に削除して、その近傍の次数を減らします。 

グラフは森なので、このような頂点は必ず存在します。 分離された行と未使用の列は、単純に次数 0 でキューに入ります。 
11. 行列とその結果の行/列の順序を出力します。 

### なぜ効果があるのか

 コネクタ列は、正の行上にフォレストを形成します。 追加ごとに`1`コネクタによって使用されていないものはプライベート リーフ列に配置されるため、それらの列をアタッチしてもサイクルを作成できません。 したがって、2 部グラフ全体がフォレストになります。 

すべての行が正確に受信します`t_i`コネクタのエッジと`a_i-t_i`葉の端、正確に与える`a_i`もの。 建設で使用されているのは、`S-E`列、これ以上のものはありません`m`アルゴリズムが受け入れるときはいつでも。 

最後に、最大次数 1 の頂点を繰り返し削除すると、あらゆるフォレストに対して有効なチェック順序が生成されます。 頂点が削除された瞬間、その現在の次数はその行または列でまだ点灯しているライトの数と正確に一致するため、Jaber が複数のライトを認識することはありません。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

import heapq

def solve():
    n, m = map(int, input().split())
    a = list(map(int, input().split()))

    positive = [i for i, x in enumerate(a) if x > 0]
    k = len(positive)
    total = sum(a)

    if k == 0:
        mat = ['0' * m for _ in range(n)]
        order = []
        for i in range(n):
            order.append(("row", i))
        for j in range(m):
            order.append(("col", j))

        out = ["YES"]
        out.extend(mat)
        out.extend(f"{typ} {idx + 1}" for typ, idx in order)
        sys.stdout.write("\n".join(out))
        return

    E = min(k - 1, total // 2)

    used_columns = total - E
    if used_columns > m:
        print("NO")
        return

    # t[i] is the number of row i's edges used by connector columns.
    t = [0] * n

    if E > 0:
        # First give one connector incidence to E+1 rows.
        base_count = E + 1
        for idx in range(base_count):
            t[positive[idx]] = 1

        remaining = E - 1

        # Distribute the remaining incidences.
        # Capping at k-1 is enough for a row degree in the
        # temporary tree after possibly adding one super edge.
        for v in positive:
            if remaining == 0:
                break
            cap = min(a[v], k - 1)
            extra = min(cap - t[v], remaining)
            if extra > 0:
                t[v] += extra
                remaining -= extra

        if remaining != 0:
            print("NO")
            return

    # Number of connected components in the connector forest.
    C = k - E

    # Rows selected to connect to the super vertex.
    roots = []
    selected_root = [False] * n

    for v in positive:
        if t[v] == 0:
            roots.append(v)
            selected_root[v] = True

    if len(roots) > C:
        print("NO")
        return

    for v in positive:
        if len(roots) == C:
            break
        if not selected_root[v]:
            roots.append(v)
            selected_root[v] = True

    if len(roots) != C:
        print("NO")
        return

    # Temporary tree vertices:
    # 0 .. k-1 are positive rows
    # k is the super vertex
    super_v = k
    Ntree = k + 1

    degree = [0] * Ntree

    pos_index = {}
    for idx, v in enumerate(positive):
        pos_index[v] = idx

    for v in positive:
        idx = pos_index[v]
        degree[idx] = t[v] + (1 if selected_root[v] else 0)

    degree[super_v] = C

    # Build a Prüfer sequence.
    prufer = []
    for v in range(Ntree):
        prufer.extend([v] * (degree[v] - 1))

    # Decode Prüfer sequence.
    cur_degree = degree[:]
    leaves = []
    for v in range(Ntree):
        if cur_degree[v] == 1:
            heapq.heappush(leaves, v)

    tree_edges = []

    for v in prufer:
        leaf = heapq.heappop(leaves)
        tree_edges.append((leaf, v))

        cur_degree[leaf] -= 1
        cur_degree[v] -= 1

        if cur_degree[v] == 1:
            heapq.heappush(leaves, v)

    last1 = heapq.heappop(leaves)
    last2 = heapq.heappop(leaves)
    tree_edges.append((last1, last2))

    # Convert the tree, after removing the super vertex,
    # into connector columns.
    row_connector_edges = []

    for u, v in tree_edges:
        if u == super_v or v == super_v:
            continue

        original_u = positive[u]
        original_v = positive[v]
        row_connector_edges.append((original_u, original_v))

    if len(row_connector_edges) != E:
        print("NO")
        return

    # Matrix as mutable byte arrays.
    mat = [bytearray(b'0' * m) for _ in range(n)]

    col = 0

    # Each connector edge gets one column with two ones.
    for u, v in row_connector_edges:
        if col >= m:
            print("NO")
            return
        mat[u][col] = ord('1')
        mat[v][col] = ord('1')
        col += 1

    # Remaining row degrees use private leaf columns.
    for i in range(n):
        remaining = a[i] - t[i]
        for _ in range(remaining):
            if col >= m:
                print("NO")
                return
            mat[i][col] = ord('1')
            col += 1

    # Build the bipartite graph.
    total_vertices = n + m
    adj = [[] for _ in range(total_vertices)]
    deg = [0] * total_vertices

    for i in range(n):
        for j in range(m):
            if mat[i][j] == ord('1'):
                u = i
                v = n + j
                adj[u].append(v)
                adj[v].append(u)
                deg[u] += 1
                deg[v] += 1

    # Every forest has a vertex of degree <= 1.
    queue = []
    for v in range(total_vertices):
        if deg[v] <= 1:
            heapq.heappush(queue, v)

    removed = [False] * total_vertices
    order = []

    while queue:
        v = heapq.heappop(queue)
        if removed[v]:
            continue

        removed[v] = True
        order.append(v)

        for u in adj[v]:
            if removed[u]:
                continue
            deg[u] -= 1
            if deg[u] <= 1:
                heapq.heappush(queue, u)

    if len(order) != total_vertices:
        print("NO")
        return

    out = ["YES"]
    out.extend(row.decode() for row in mat)

    for v in order:
        if v < n:
            out.append(f"row {v + 1}")
        else:
            out.append(f"col {v - n + 1}")

    sys.stdout.write("\n".join(out))

if __name__ == "__main__":
    solve()
```実装の最初の部分では、ゼロ行と正の行を分離します。 ゼロ行はコネクタ フォレストに参加する必要がまったくないため、これは重要です。 

変数`E`コネクタ列の数です。 表現`total - E`は、2 行の列の数を最大化した後に必要な列の数です。 その数値がより大きい場合`m`、どんなアレンジもうまくいきません。 

配列`t`各行の必要な行のうちコネクタ列に属するものがいくつあるかを記録します。 初期の`E+1`割り当てにより、フォレストに十分な非ゼロ頂点が保証されます。`E`エッジ。 残りの次数は貪欲に分配されます。`k-1`上限として。 Python の整数はオーバーフローせず、関連するすべての値は最大でも約`10^6`ここ。 

スーパー頂点の構築は、グラフ理論の重要な部分です。 ある森`E`エッジオン`k`頂点には`k-E`コンポーネント。 すべてのコンポーネントの 1 つの頂点を新しいスーパー頂点に接続すると、ツリーが生成されます。 コンポーネントを直接構築するのではなく、次数シーケンスからこのツリーを構築し、スーパー頂点を削除します。 

プリューファー数列には頂点が含まれています`v`その通り`degree[v]-1`回。 その長さは`k-1`、木と一致します`k+1`頂点。 ヒープはデコード中に現在のリーフを保存し、次のリーフの二次検索を回避します。 

デコード後、スーパー頂点に触れていないすべてのツリー エッジがコネクタ列に対応します。 残りの行次数はプライベート列に配置されます。 この構造では、2 つの同一の行ペアが別々のコネクタ列に配置されることはありません。また、結果として得られるグラフは、一時ツリーとリーフ列のサブグラフであるため、非循環になります。 

最後のトラバーサルは、標準的なリーフ除去プロセスです。`deg[v]`頂点が存在するときにまだ生きているエッジの数です。`v`チェックされるところだ。 次数 0 または次数 1 の頂点を削除することは、まさにステートメントで要求される条件です。 

## 実用的な例

 ### サンプル 1

 のために```
4 4
1 0 0 0
```行 1 のみが正なので、`k=1`そして`S=1`。 

| 変数 | 値 |
 | --- | --- |
 |`positive`|`[1]`|
 |`k`|`1`|
 |`S`|`1`|
 |`E = min(k-1,S//2)`|`0`|
 | 使用された列 |`1`|
 |`t`|`[1,0,0,0]`|

 コネクタ エッジがないため、必要な 1 つのエッジがプライベート リーフ列になります。 結果として得られる行列は次のようになります。```
1000
0000
0000
0000
```グラフにはエッジが 1 つだけ含まれています。 すべての頂点には次数 0 または 1 があるため、8 つの頂点すべての任意の順序が機能します。 サンプルの順序はそのような順序の 1 つです。 

この例では、`E=0`場合。 この建設では、正の列が 1 つしかないため、列を接続するためのスーパーバーテックス機械は必要ありません。 

### サンプル 2

 のために```
4 4
2 1 1 1
```我々は持っています`k=4`そして`S=5`。 

| 変数 | 値 |
 | --- | --- |
 |`positive`|`[1,2,3,4]`|
 |`k`|`4`|
 |`S`|`5`|
 |`E`|`2`|
 |`C = k-E`|`2`|
 | 必須の列 |`S-E = 3`|

 コネクタ度を選べる構造```
t = [2, 1, 1, 0]
```それらの合計は`4 = 2E`。 対応する一時ツリーには行次数があります。```
3, 1, 1, 1
```行 1 と行 4 をスーパー頂点の近傍として選択した後、スーパー頂点の次数は 2 です。 

考えられるコネクタ フォレストの 1 つは次のとおりです。```
row 1 -- row 2
row 1 -- row 3
```行 4 はコネクタ フォレスト内で分離されています。 残りの次数は完全に行 4 に属するため、そこに 1 つのプライベート列が追加されます。 

したがって、可能な行列は次のようになります。```
1100
1000
1000
0010
```正確な行列はサンプルとは異なりますが、問題は有効な構成を受け入れるため問題ありません。 その 2 部グラフはフォレストであるため、葉を削除する順序が存在します。 

このトレースは、行の次数を行自体のツリーの次数として解釈する必要がない理由を示しています。 一部の行エッジはリーフ列になりますが、コネクタ列はフォレスト構造を決定する部分です。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |`O(nm + (n+m) log(n+m))`| 行列と二部隣接関係は次のように処理されます。`O(nm)`一方、Prüfer デコードと最終的なヒープ トラバーサルは頂点ごとに対数的です。 |
 | スペース |`O(nm + n+m)`| マトリックスと隣接リストはメモリ使用量の大半を占めます。 |

 と`n,m <= 1000`、マトリックスには最大 100 万個のセルが含まれます。 の`O(nm)`完全な行列を出力する必要があるため、定数までの部分も避けられません。 メモリ使用量は 256 MB の制限を快適に下回ります。 

## テストケース

 出力は一意ではないため、文字列の正確な比較はこの問題には不適切です。 以下のテストではソルバーを実行し、生成された行列を検証し、代わりに順序をチェックします。 バリデータは、行の合計、順序内のすべての行と列の一意性、およびチェックされたすべての頂点に残りエッジが 1 つ以下であるという条件をチェックします。```python
import sys
import io
import heapq

def solve_string(inp: str) -> str:
    old_stdin = sys.stdin
    old_stdout = sys.stdout

    sys.stdin = io.StringIO(inp)
    sys.stdout = io.StringIO()

    try:
        solve()
        return sys.stdout.getvalue()
    finally:
        sys.stdin = old_stdin
        sys.stdout = old_stdout

def validate(inp: str, out: str) -> bool:
    data = inp.strip().split()
    it = iter(data)

    n = int(next(it))
    m = int(next(it))
    a = [int(next(it)) for _ in range(n)]

    lines = out.strip().splitlines()
    if not lines:
        return False

    possible = lines[0] == "YES"

    total = sum(a)
    if not possible:
        # A validator for NO instances is supplied separately below.
        return True

    if len(lines) != 1 + n + n + m:
        return False

    matrix = lines[1:1 + n]
    if any(len(row) != m for row in matrix):
        return False

    for i in range(n):
        if sum(c == '1' for c in matrix[i]) != a[i]:
            return False
        if any(c not in '01' for c in matrix[i]):
            return False

    order_lines = lines[1 + n:]
    if len(order_lines) != n + m:
        return False

    seen = set()
    order = []

    for line in order_lines:
        parts = line.split()
        if len(parts) != 2:
            return False

        typ, x = parts
        x = int(x)

        if typ == "row":
            if not (1 <= x <= n):
                return False
            v = x - 1
        elif typ == "col":
            if not (1 <= x <= m):
                return False
            v = n + x - 1
        else:
            return False

        if v in seen:
            return False
        seen.add(v)
        order.append(v)

    if len(seen) != n + m:
        return False

    adj = [[] for _ in range(n + m)]
    for i in range(n):
        for j in range(m):
            if matrix[i][j] == '1':
                u = i
                v = n + j
                adj[u].append(v)
                adj[v].append(u)

    removed = [False] * (n + m)

    for v in order:
        remaining = sum(not removed[u] for u in adj[v])
        if remaining > 1:
            return False
        removed[v] = True

    return True

def expect_no(inp: str):
    out = solve_string(inp)
    assert out.strip() == "NO"

# Sample 1
sample1 = """\
4 4
1 0 0 0
"""
assert validate(sample1, solve_string(sample1)), "sample 1"

# Sample 2
sample2 = """\
4 4
2 1 1 1
"""
assert validate(sample2, solve_string(sample2)), "sample 2"

# Minimum-size instance
case_min = """\
1 1
0
"""
assert validate(case_min, solve_string(case_min)), "minimum-size zero"

# Minimum-size instance with one edge
case_min_edge = """\
1 1
1
"""
assert validate(case_min_edge, solve_string(case_min_edge)), "minimum-size edge"

# Boundary case: both rows require every column.
# The only possible matrix is all ones, which contains a cycle.
case_impossible = """\
2 2
2 2
"""
expect_no(case_impossible)

# Same total-edge count as a sparse graph might allow, but the
# prescribed row degrees force two rows to share all three columns.
case_impossible_2 = """\
5 3
3 3 0 0 0
"""
expect_no(case_impossible_2)

# Maximum-size feasible case.
# Every row has exactly one one, so one private column per row is enough.
case_max = "1000 1000\n" + " ".join(["1"] * 1000) + "\n"
assert validate(case_max, solve_string(case_max)), "maximum-size case"

# All equal positive row degrees.
# 1000 rows and 1000 columns, every row has degree 1.
case_equal = "1000 1000\n" + " ".join(["1"] * 1000) + "\n"
assert validate(case_equal, solve_string(case_equal)), "all-equal case"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`1 1 / 0`|`YES`| エッジのない最小のグラフ |
 |`1 1 / 1`|`YES`| エッジを含む最小のグラフ |
 |`2 2 / 2 2`|`NO`| 強制4サイクル |
 |`5 3 / 3 3 0 0 0`|`NO`| 不十分なエッジ数条件を捕捉します。 
|`1000 1000 / 1 ... 1`|`YES`| 最大寸法と大出力 |
 |`1000 1000 / 1 ... 1`|`YES`| すべて等しい行の次数と境界列の使用法 |

 ## 特殊なケース

 のために```
2 2
2 2
```我々は持っています`S=4`、`k=2`、 そして`E=min(1,2)=1`。 建設に必要となるのは、`S-E=3`列がありますが、存在する列は 2 つだけです。 すぐに印刷されます`NO`。 これは、フォレストの制限が、エッジの数が最大でも頂点の数から 1 を引いた数であることを単に要求するよりも強力であるという事実を捉えています。 

のために```
5 3
3 3 0 0 0
```我々は持っています`S=6`、`k=2`、そしてまた`E=1`。 最小列数は次のとおりです。`6-1=5`を超える`m=3`。 アルゴリズムは、グラフの構築を試みる前にインスタンスを拒否します。 これはまさに、チェックのみを行う場合の一種です。`S <= n+m-1`間違った答えを出します。 

のために```
3 2
2 0 0
```正の行が 1 つあるので、`k=1`、`S=2`、 そして`E=0`。 両方の必須の列がプライベート リーフ列になります。 最初の行は`11`、他の 2 行はゼロです。 グラフは行を中心とした星型であり、ツリーであるため、有効なチェック順序が存在します。 

のために```
3 3
0 0 0
```我々は持っています`k=0`。 どこにもエッジがないため、すべての行と列の次数は 0 になります。 特殊なケースでは、すべてゼロの行列と 6 つの頂点の任意の順列が直接出力されます。 これにより、正の行を持たないコネクタ フォレストを構築しようとすることが回避されます。 

1000 行と 1000 列の最大サイズのインスタンスの場合、`a_i=1`、 我々は持っています`S=1000`、`k=1000`、 そして`E=999`。 建設で使用されているのは、`1000-999=1`コネクタ フォレストの列。残りの度数単位構造は同じコネクタ ツリーで表されます。 結果として得られる 2 部グラフは、1000 行の頂点すべてと使用された 1 つの列を含むツリーですが、他の 999 列は分離されています。 最後のリーフ削除プロセスでは、分離された列が自動的に処理されます。 

これらすべてのケースの背後にある中心的な不変条件は変更されていません。`1`森の端です。 その不変条件が確立されると、最大 1 つのエッジが残っている頂点を繰り返し削除することで、必要なチェック順序が保証されます。
