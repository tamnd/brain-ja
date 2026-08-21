---
title: "CF 102268I - 興味深いグラフ"
description: "最大 (10^5) 個の頂点と (10^5) 個のエッジを持つ単純な無向グラフが与えられます。 (1) から (n) までの利用可能な色 (k) のすべての可能な数について、それら (k) のラベル付き色を使用した適切な頂点カラーリングの数 (998244353) を法とする必要があります。"
date: "2026-08-19T04:33:06+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102268
codeforces_index: "I"
codeforces_contest_name: "300iq Contest 1"
rating: 0
weight: 102268
solve_time_s: 860
verified: false
draft: false
---

[CF 102268I - 興味深いグラフ](https://codeforces.com/problemset/problem/102268/I)

 **評価:** -
 **タグ:** -
 **解決時間:** 14 分 20 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 最大 (10^5) 個の頂点と (10^5) 個のエッジを持つ単純な無向グラフが与えられます。 (1) から (n) までの利用可能な色 (k) のすべての可能な数について、それら (k) のラベル付き色を使用した適切な頂点カラーリングの数 (998244353) を法とする必要があります。 

グラフ上の異常な状態により、問題は扱いやすくなります。 任意の 7 つの頂点を取ります。 そのうちの 2 つは、7 つの頂点間のすべてのパス上にある 3 番目の頂点を 7 つの頂点の外側に持つ必要があります。 この条件により、ブロックとも呼ばれるすべての二重接続コンポーネントに最大 6 つの頂点が含まれるようになります。 

その理由を確認するには、二重接続コンポーネントに少なくとも 7 つの頂点が含まれていると仮定します。 その頂点のうち任意の 7 つを (A) として選択します。 任意の 2 つの異なる頂点 (A の a,b\) および任意の (A の c\not) について、頂点 (c) は (a) と (b) を分離できません。 (c) がコンポーネントの外側にある場合、コンポーネント内のパスとは無関係です。 (c) がコンポーネントの別の頂点である場合、二重接続性により (c) を回避する (a)-(b) パスが得られます。 これは必要なプロパティと矛盾します。 

境界 (n,m\le 10^5) は、任意の頂点サブセットの探索、カラーリングの列挙、頂点ごとの 2 次演算の実行を除外します。 (O(n^2)) であっても、すでに上限で約 (10^{10}) 個の基本演算を意味します。 有用な分解は、入力サイズにおいて基本的に線形である必要があり、ブロックごとに少量の一定量の作業のみが必要です。 

誤って処理されやすい境界ケースがいくつかあります。 2 つの頂点と 1 つのエッジを持つグラフのカラー カウントは (0,2) です。これは、1 つの色では端点を分離できないのに対し、2 つの色では 2 つの割り当てが与えられるためです。 パス内に 3 つの頂点があるグラフは、色多項式が (k(k-1)^2) であるため、(0,2,12) になります。 切断されたグラフはコンポーネントごとに処理する必要があります。 たとえば、4 つの頂点上の 2 つの互いに素なエッジは多項式 (k^2(k-1)^2) を持ち、(0,4,36,144) となります。 最後に、6 つの頂点上の完全なグラフは 1 つの許可されたブロックであり、その答えは (0,0,0,0,0,720) です。 すべてのブロックが木の端であると仮定したり、間違った点で (k) で除算したりする不用意な実装は、これらのケースを誤ります。 

## アプローチ

 直接的なアプローチは、頂点への色の割り当てをすべて列挙することです。 固定 (k) の場合、(k^n) 個の割り当てがあり、1 つの割り当てをすべてのエッジに対してチェックするのにコスト (O(m)) がかかります。 これを (k) ごとに行うと (O(m\sum_{k=1}^n k^n)) が得られ、これはすでに (m n^n) のオーダーになります。 (n=10^5) の場合、これは単に遅すぎるだけでなく、まったく実行不可能です。 

有益な観察は、グラフが関節頂点で分割できることです。 接続されたグラフが二重接続されたコンポーネントに分解されると、異なるブロックは単一の共有アーティキュレーション頂点を介してのみ相互作用します。 共通のアーティキュレーション頂点の色が固定されると、1 つのブロックのカラーリングを次のブロックのカラーリングと独立して組み合わせることができます。 

接続されたグラフにブロック (B_1,\ldots,B_t) があるとします。 (P_B(k)) がブロック (B) の色多項式を表す場合、

 [
 P_G(k)=\frac{\prod_{i=1}^{t}P_{B_i}(k)}{k^{t-1}}。 
]

 すべてのブロックには少なくとも 1 つのエッジが含まれるため、(P_B(k)) は (k) で割り切れます。 定義する

 [
 Q_B(k)=\frac{P_B(k)}{k}。 
]

 次に、接続されたコンポーネントが貢献します

 [
 k\prod_B Q_B(k)。 
]

 (C) 連結成分を含むグラフの場合、完全な答えは次のようになります。 

[
 P_G(k)=k^C\prod_B Q_B(k)。 
]

 残りの困難は、これらすべての要素を (k) ごとに評価することです。 各ブロックには最大 6 つの頂点があるため、その頂点のすべてのパーティションを独立したセットに列挙できます。 6 つの要素のセット パーティションは (203) のみです。 (c_t) が正確に (t) 個の独立セットに分割される有効なパーティションの数である場合、

 [
 P_B(k)=\sum_{t=1}^{|B|}c_t(k)_t,
 ]

 どこで

 [
 (k)_t=k(k-1)\cdots(k-t+1)。 
]

 (k)で割った後、

 [
 Q_B(k)=\sum_{t=1}^{|B|}c_t(k-1)_{t-1}。 
]

したがって、すべてのブロックは最大 6 つの小さな整数のタプルで表されます。 

最大 6 つの頂点で接続されたグラフには、異なる色多項式はほとんどありません。 サイズ (1) ～ (6) の既知の数は (1,1,2,5,14,50) であるため、サイズ (2) ～ (6) にわたって接続された異なる色多項式は (72) 個だけです。 したがって、同じ係数タプルを持つブロックをグループ化し、各タイプを 1 回だけ処理できます。 元のコンテストのディスカッションでは、関連する局所多項式の数が (100) 未満であることが観察され、この小国家分類アプローチが正確に説明されています。 

したがって、ブロック内の総当り計算は小規模なものですが、大きなグラフはブロック分解によって処理されます。 これは、任意のグラフ色付け問題から一定サイズの問題の集合への重要な移行です。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | (O(mn^n)) | (O(n+m)) | 遅すぎる |
 | ブロック分解と局所多項式分類 | (O(n+m+Un))、(U<100) ローカル型 | (O(n+m)) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 二重接続コンポーネントに対して Tarjan の DFS を実行します。 すべての頂点と通過したエッジのスタックの検出時間とローリンク値を維持します。 DFS の子 (v) が (\operatorname{low}[v]\ge\operatorname{tin}[u]) を満たすたびに、エッジ (uv) が削除されるまでエッジをポップします。 それらの飛び出たエッジが 1 つのブロックを形成します。 指定されたグラフ プロパティにより、結果として得られるすべてのブロックに最大 6 つの頂点が含まれることが保証されます。 
2. DFS の実行中に接続されているコンポーネントをカウントします。 孤立した頂点にはエッジ ブロックはありませんが、それでも色多項式に係数 (k) が寄与します。 まさにこれが、最終的なグローバル因数が (k^C) である理由です。 
3. すべてのブロックについて、その頂点を収集し、それらをローカル インデックス (0,\ldots,s-1) (s\le6) に変換します。 ブロックのエッジをビットマスクとしてエンコードします。 考えられるローカル エッジは最大 (\binom 62=15) 個であるため、ブロック全体は 15 ビット整数に収まります。 
4. ローカル頂点のすべてのセット パーティションを列挙します。 パーティションは、どの頂点が同じ色を受け取るかを決定する 1 つの方法を表します。 パーティションは、同じパーツ内に両方の端点を持つグラフ エッジがない場合にのみ使用できます。 (t) 個の部分を持つ有効なパーティションの数を数えます。 これらのカウントは、ブロックの色多項式の下降階乗展開の係数 (c_t) です。 
5. 係数タプルをブロックのタイプとして保存し、各タイプを持つブロックの数を数えます。 同じタプルを持つブロックはまったく同じ (Q_B(k)) を持つため、それらを個別に評価する理由はありません。 
6. 個別のブロック タイプごとに、次の値を評価します。 

[
 Q(k)=\sum_t c_t(k-1)_{t-1}
 ]

 すべての場合 (k=1,\ldots,n)。 (Q) の次数は最大 5 であるため、その値は有限差分を使用して生成でき、各点で 5 回の乗算を伴う新たな多項式評価を回避できます。 

1. タイプの寄与を答えに乗算します。 型が (r) 回出現する場合、その寄与は (Q(k)^r) になります。 ある場合には直接乗算します。 複数の場合にはべき乗剰余を使用します。 
2. すべてのブロック タイプが処理された後、すべての答えに (k^C) を掛けます。 結果の値は、グラフ全体の適切な (k) カラーリングの数です。 

機能する理由: Tarjan の分解は、グラフを関節頂点でのみ交差するブロックに分割します。 このような関節頂点の色が固定されると、入射ブロックの色は独立します。 ブロックのカラーリングには (P_B(k)) の可能性がありますが、共有アーティキュレーション カラーはすべてのインシデント ブロックで 1 回カウントされるため、追加の各ブロックは (k) による除算に寄与します。 これにより、(k^C\prod_B(P_B(k)/k)) が得られます。 ローカル落下階乗展開では、最初に頂点を空ではないカラー クラスに分割し、次にそれらのクラスに個別のラベル付きカラーを割り当てることによって、すべての適切なカラーリングを 1 回だけカウントします。 すべてのブロックには最大 6 つの頂点があるため、その独立したパーティションの網羅的な列挙は正確かつ一定のサイズになります。

## Python ソリューション```python
import sys
input = sys.stdin.readline

MOD = 998244353

def generate_partitions(n):
    """Return (number_of_parts, internal_pair_mask) for every set partition."""
    if n == 0:
        return [(0, 0)]

    pair_id = [[-1] * n for _ in range(n)]
    bit = 0
    for i in range(n):
        for j in range(i + 1, n):
            pair_id[i][j] = pair_id[j][i] = bit
            bit += 1

    res = []

    # Restricted-growth strings describe set partitions uniquely.
    a = [0] * n

    def dfs(pos, mx):
        if pos == n:
            mask = 0
            for i in range(n):
                for j in range(i + 1, n):
                    if a[i] == a[j]:
                        mask |= 1 << pair_id[i][j]
            res.append((mx + 1, mask))
            return

        for x in range(mx + 2):
            a[pos] = x
            dfs(pos + 1, max(mx, x))

    a[0] = 0
    dfs(1, 0)
    return res

PARTITIONS = {s: generate_partitions(s) for s in range(2, 7)}

def block_signature(vertices, edge_ids, edges):
    """Return the falling-factorial coefficient tuple of one block."""
    s = len(vertices)

    where = {v: i for i, v in enumerate(vertices)}

    edge_mask = 0
    for eid in edge_ids:
        u, v = edges[eid]
        a = where[u]
        b = where[v]
        if a > b:
            a, b = b, a

        # Pair (a,b) among the s vertices.
        bit = 0
        for i in range(a):
            bit += s - 1 - i
        bit += b - a - 1
        edge_mask |= 1 << bit

    cnt = [0] * s

    for parts, inside in PARTITIONS[s]:
        if edge_mask & inside == 0:
            cnt[parts - 1] += 1

    return tuple(cnt)

def solve():
    n, m = map(int, input().split())

    edges = []
    graph = [[] for _ in range(n)]

    for eid in range(m):
        u, v = map(int, input().split())
        u -= 1
        v -= 1
        edges.append((u, v))
        graph[u].append((v, eid))
        graph[v].append((u, eid))

    sys.setrecursionlimit(max(1_000_000, 2 * n + 100))

    tin = [0] * n
    low = [0] * n
    timer = 0

    edge_stack = []
    type_count = {}
    components = 0

    def process_component(edge_ids):
        verts = set()
        for eid in edge_ids:
            u, v = edges[eid]
            verts.add(u)
            verts.add(v)

        vertices = list(verts)
        sig = block_signature(vertices, edge_ids, edges)
        type_count[sig] = type_count.get(sig, 0) + 1

    def dfs(u, parent_edge):
        nonlocal timer

        timer += 1
        tin[u] = low[u] = timer

        for v, eid in graph[u]:
            if eid == parent_edge:
                continue

            if tin[v] == 0:
                edge_stack.append(eid)

                dfs(v, eid)

                low[u] = min(low[u], low[v])

                if low[v] >= tin[u]:
                    comp_edges = []

                    while True:
                        x = edge_stack.pop()
                        comp_edges.append(x)
                        if x == eid:
                            break

                    process_component(comp_edges)

            elif tin[v] < tin[u]:
                edge_stack.append(eid)
                low[u] = min(low[u], tin[v])

    for root in range(n):
        if tin[root] == 0:
            components += 1
            dfs(root, -1)

    # ans[k] is the contribution accumulated from all Q_B(k).
    ans = [1] * (n + 1)

    for sig, multiplicity in type_count.items():
        # Q(k) = sum_{t=1}^s c_t * (k-1)_(t-1)
        #
        # Q is degree at most 5. Build its first six values and
        # turn them into forward differences.
        s = len(sig)

        vals = []
        for k in range(1, s + 2):
            x = k - 1
            falling = 1
            value = 0

            for j in range(s):
                if j > 0:
                    falling *= x - (j - 1)
                value += sig[j] * falling

            vals.append(value % MOD)

        # Forward differences.
        diff = vals[:]

        for level in range(s):
            for i in range(s - level):
                diff[i] = (diff[i + 1] - diff[i]) % MOD

        # The current value at k=1 is diff[0].
        cur = diff[:]
        q = diff[0]

        # Apply k=1 first.
        if multiplicity == 1:
            ans[1] = ans[1] * q % MOD
        else:
            ans[1] = ans[1] * pow(q, multiplicity, MOD) % MOD

        # Advance from k to k+1 using finite differences.
        for k in range(2, n + 1):
            for level in range(s - 1):
                cur[level] = (cur[level] + cur[level + 1]) % MOD

            q = cur[0]

            if multiplicity == 1:
                ans[k] = ans[k] * q % MOD
            else:
                ans[k] = ans[k] * pow(q, multiplicity, MOD) % MOD

    # Each connected component contributes one free root color.
    for k in range(1, n + 1):
        ans[k] = ans[k] * pow(k, components, MOD) % MOD

    print(*ans[1:])

if __name__ == "__main__":
    solve()
```隣接リストには、隣接する頂点だけではなくエッジ ID が格納されます。 これが必要なのは、2 つの DFS エンドポイントが同じ親頂点を持つことができるのは、平行なエッジを持つグラフ内のみであり、ここでは禁止されていますが、エッジ ID を使用すると、親エッジのテストが正確になり、特殊なケースが回避されます。 

Tarjan スタックには、各エッジが 1 回だけ含まれます。 ツリーのエッジはその子が最初に発見されたときにプッシュされますが、バック エッジは既に発見された祖先を指している場合にのみプッシュされます。 (\operatorname{low}[v]\ge\operatorname{tin}[u]) の場合、(uv) で終わるスタック セグメントはまさに 1 つの二重接続コンポーネントです。 

ローカル ブロック エンコーディングでは、最大 15 個のエッジ ビットが使用されます。 ビット位置の少し特殊な計算は、ローカル頂点の順序付けされていないペアのインデックス付けスキームにすぎません。 ブロックには最大でも 6 つの頂点があるため、グローバル頂点 ID をローカル ID に変換するために使用される辞書は小さいままです。 

パーティション ジェネレーターは、増加が制限された文字列を使用します。 たとえば、4 つの頂点を 3 つのグループに分割することは、(0,1,2,0) などのシーケンスで表すことができます。 すべてのセット パーティションにはそのような表現が 1 つだけ存在するため、パーティションの重複や欠落はありません。 6 つの頂点では (203) 個の可能性しかありません。 

署名には、可能な各部分数の独立したパーティションの数が含まれています。 落下階乗同一性

 [
 P_B(k)=\sum_t c_t(k)_t
 ]

 まさにこの署名で十分な理由です。 ブロックの頂点の実際のラベルは、この計算後に消えます。 

有限差分評価は注目に値します。 次数 (d) 多項式は、前方差分テーブルを維持することによって、連続する整数引数で評価できます。 引数を 1 つ進めると、最初の差異が 2 番目の差異によって変更され、2 番目の差異が 3 番目の差異によって変更されます。 次数は最大 5 であるため、それぞれの新しい値には数回の追加のみが必要です。 

最後にPythonの`pow(a,b,MOD)`巨大な整数 (a^b) を構築せずにべき乗剰余演算を実行します。 すべての乗算は剰余 (998244353) で計算されるため、整数増加の問題は発生しません。 

## 実用的な例

 ### サンプル 1

 グラフには 5 つの頂点があり、頂点 (1、3、5) に三角形が 1 つと、孤立した 2 つの頂点があります。 三角形は 1 つのブロックであり、孤立した各頂点は独自の接続コンポーネントです。 

三角形は色多項式を持っています

 [
 P_B(k)=k(k-1)(k-2)、
 ]

 したがって、その減少係数は

 [
 Q_B(k)=(k-1)(k-2)。 
]

 3 つの連結成分があり、グローバル係数 (k^3) が与えられます。 

| (k) | (Q_B(k)) | (k^3) | 答え |
 | --- | --- | --- | --- |
 | 1 | 0 | 1 | 0 |
 | 2 | 0 | 8 | 0 |
 | 3 | 2 | 27 | 54 |
 | 4 | 6 | 64 | 384 |
 | 5 | 12 | 125 | 1500 |

 結果の出力は次のとおりです。`0 0 54 384 1500`。 このトレースは、孤立した頂点が人工ブロックではなく接続コンポーネント要素によって処理される理由を示しています。 

### 1 つの頂点を共有する 2 つの三角形

 検討してください```
5 6
1 2
2 3
3 1
3 4
4 5
5 3
```頂点 (3) を共有する 2 つの三角形ブロックがあります。 グラフは接続されているため、グローバル因子 (k) は 1 つあります。 各三角形は ((k-1)(k-2)) に寄与します。 

| (k) | 三角係数 | 2 つの要素の積 | グローバル (k) | 答え |
 | --- | --- | --- | --- | --- |
 | 1 | 0 | 0 | 1 | 0 |
 | 2 | 0 | 0 | 2 | 0 |
 | 3 | 2 | 4 | 3 | 12 |
 | 4 | 6 | 36 | 4 | 144 |
 | 5 | 12 | 144 | 5 | 720 |

 この例では、アーティキュレーションと頂点の乗算ルールを実行します。 共有頂点には、独立して選択された 2 色ではなく 1 色が含まれます。これがまさに、ブロック多項式の積を (k) で除算する必要がある理由です。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O(n+m+Un)) | Tarjan は線形であり、各ブロックには最大 6 つの頂点があり、関連するローカル多項式タイプは (100) 未満です。 
| スペース | (O(n+m)) | グラフ ストレージ、DFS アレイ、エッジ スタック、ブロック情報はすべて線形です。 

重要な構造上の事実は、7 つの頂点の条件により、すべてのブロックが 6 つの頂点によって境界付けられるということです。 したがって、ローカル列挙のサイズは一定ですが、最大 6 つの頂点上にある個別に接続された色多項式の数はごくわずかです。 (n,m\le10^5) を使用すると、結果の計算は意図した複雑さの範囲内に収まり、(k^n) への依存を快適に回避できます。 

## テストケース

 次のハーネスは次のことを前提としています。`solve()`上記のソリューションの関数は、同じ Python プロセスで使用できます。```python
import sys
import io
from contextlib import redirect_stdout

def run(inp: str) -> str:
    old_stdin = sys.stdin
    sys.stdin = io.StringIO(inp)
    out = io.StringIO()
    try:
        with redirect_stdout(out):
            solve()
    finally:
        sys.stdin = old_stdin
    return out.getvalue().strip()

# Sample 1: triangle plus two isolated vertices.
assert run(
    """5 3
3 5
5 1
1 3
"""
) == "0 0 54 384 1500", "sample 1"

# Custom 1: minimum valid n with one edge.
assert run(
    """2 1
1 2
"""
) == "0 2", "single edge"

# Custom 2: a path on three vertices.
assert run(
    """3 2
1 2
2 3
"""
) == "0 2 12", "path"

# Custom 3: disconnected graph with two independent edges.
assert run(
    """4 2
1 2
3 4
"""
) == "0 4 36 144", "disconnected components"

# Custom 4: maximum-size block, K6.
assert run(
    """6 15
1 2
1 3
1 4
1 5
1 6
2 3
2 4
2 5
2 6
3 4
3 5
3 6
4 5
4 6
5 6
"""
) == "0 0 0 0 0 720", "K6 boundary"

# Large-size structural test.
# A path is useful for stress-testing the implementation of the block
# decomposition, although it does not satisfy the original seven-vertex
# promise once it becomes long.
n = 100000
edges = "\n".join(f"{i} {i+1}" for i in range(1, n))
large_input = f"{n} {n-1}\n{edges}\n"
large_output = run(large_input).split()

assert len(large_output) == n, "large output length"
assert large_output[0] == "0", "one-color boundary"
assert large_output[1] == str(2), "two-color path boundary"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`2 1 / 1 2`|`0 2`| 最小グラフと片辺ブロック |
 |`3 2 / 1 2, 2 3`|`0 2 12`| ツリー ブロックと反復関節頂点 |
 |`4 2 / 1 2, 3 4`|`0 4 36 144`| 複数の接続コンポーネント |
 | (K_6) |`0 0 0 0 0 720`| 最大許容ブロック サイズと下降階乗列挙 |
 | (10^5) 個の頂点を持つパス | (10^5) 値 | 大規模入力の Tarjan トラバーサルと出力境界 |

 大規模ストレス テストでは、頂点数が最大のグラフで実装を意図的にチェックします。 長いパスには外側の区切り文字がない 7 つの連続した頂点が含まれているため、これは元の Promise の有効なインスタンスとしては表示されません。 その目的は、構造上の保証とは関係なく、スタック、トラバーサル、およびパフォーマンスのバグを検出することです。 

## 特殊なケース

 単一のエッジを含むグラフの場合、唯一のブロックは (K_2) です。 その有効な独立セット パーティションは、2 つのシングルトン セットへのパーティションです。 

[
 P_{K_2}(k)=(k)_2=k(k-1)。 
]

 削減されたブロック係数は (k-1) であり、1 つの連結成分が追加の係数 (k) を提供します。 入力用```
2 1
1 2
```アルゴリズムは (0,2) を取得します。 

3 つの頂点上のパスの場合、Tarjan は 2 つ (K_2) のブロックを生成します。 それぞれが (k-1) に寄与し、連結成分が (k) に寄与します。 製品は

 [
 k(k-1)^2。 
]

 (k=1,2,3) では、(0,2,12) が得られます。 このケースでは、アーティキュレーション頂点が誤って 2 回カウントされるエラーが検出されます。 

接続されていないグラフの場合、接続されているすべてのコンポーネントは、1 つのルート頂点の色を独自に自由に選択できます。 2 つの互いに素なエッジがある場合、2 つのブロックと 2 つの接続コンポーネントがあるため、式は次のようになります。 

[
 k^2(k-1)^2。 
]

 (k=2) では、(4) が得られます。これは、2 つのエッジの方向に対する 2 つの独立した 2 値の選択肢に対応します。 

(K_6) の場合、6 つの頂点すべてを含むブロックが 1 つあります。 すべての適切な色付けには 6 つの異なる色が必要であるため、その色多項式は次のようになります。 

[
 (k)_6.
 ]

 ブロック署名には、対応するパーティションが完全なグラフと互換性がある場合にのみ、(1) から (6) までのパーツの数ごとに有効なパーティションが 1 つだけあります。 実際、6 シングルトン パーティションのみが残り、(P(k)=(k)_6) となります。 したがって、(k<6) のすべての値はゼロとなり、(k=6) での値は (6!=720) になります。 これにより、パーティションの列挙とローカルの降下階乗評価の両方で off-by-one エラーが捕捉されます。 

最も危険な実装上の間違いは、サイズ 6 のブロックを 7 つのローカル カラーを持つことができるかのように扱ったり、(Q_B(k)=P_B(k)/k) が ((k)_t) ではなく ((k-1)_{t-1}) を使用することを忘れたりすることです。 (K_6) テストでは、両方のエラーが即座に明らかになります。
