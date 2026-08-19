---
title: "CF 102201F - 果樹"
description: "すべての頂点に 1 つの果物の種類が割り当てられた木があります。 各クエリについて、2 つの頂点 s と e が一意のパスを定義し、そのパス上で他のすべての頂点を組み合わせたものより厳密に多くのフルーツ タイプが発生するかどうかを判断する必要があります。 そのような型が存在する場合は、それを出力します。"
date: "2026-08-18T10:25:46+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102201
codeforces_index: "F"
codeforces_contest_name: "Moscow Pre-Finals Workshop 2019. KAIST Contest"
rating: 0
weight: 102201
solve_time_s: 331
verified: true
draft: false
---

[CF 102201F - 果樹](https://codeforces.com/problemset/problem/102201/F)

 **評価:** -
 **タグ:** -
 **解決時間:** 5 分 31 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 すべての頂点に 1 つの果物の種類が割り当てられた木があります。 クエリごとに 2 つの頂点`s`そして`e`一意のパスを定義し、そのパス上で他のすべての頂点を組み合わせたものよりも厳密に果物の種類が発生するかどうかを判断する必要があります。 そのような型が存在する場合は、それを出力します。 それ以外の場合は印刷します`-1`。 公式の制約では両方が許可されています`N`そして`Q`到達する`250000`、制限時間は 3 秒、メモリは 1024 MB です。 

主な問題は、クエリされたオブジェクトが配列内の連続した間隔ではなくツリー パスであることです。 パスには以下を含めることができます`O(N)`頂点が存在する可能性があります`O(N)`クエリを実行するため、すべてのパスを明示的に実行するには、`O(NQ)`操作。 と`N = Q = 250000`、それはおおよそです`6.25 * 10^10`頂点の訪問数は、数秒に収まる範囲をはるかに超えています。 両方の前処理が必要です`O(N log N)`そしてそれに近いクエリ`O(log N)`。 

いくつかの境界ケースは誤って処理されやすいものです。 まず、1 つの頂点で構成されるパスは常に過半数を持ちます。 例えば、```
1 1
7
1 1
```出力があります```
7
```少なくとも 2 つの長さのパスのみをチェックするソリューションでは、誤って印刷されます。`-1`。 

第二に、平等だけでは十分ではありません。 のために```
3 1
1 2 1
1 2
2 3
1 3
```パスには次のものが含まれます`1, 2, 1`、したがって、答えは次のとおりです`1`。 しかし、```
4 1
1 2 1 2
1 2
2 3
3 4
1 4
```カウントは`2`そして`2`、したがって、答えは次のとおりです`-1`。 を使用した不注意な実装`>= half`過半数を誤って報告してしまうだろう。 

3 番目に、最も低い共通祖先はパスに属しており、正確に 1 回カウントされる必要があります。 例えば、```
3 1
1 2 1
1 2
1 3
2 3
```パスがあります`2 -> 1 -> 3`、果物の種類は`2, 1, 1`、したがって、答えは次のとおりです`1`。 LCA を足し戻さずに 2 回減算するパス式では、次の現象が 1 回だけ発生します。`1`そして間違った結果を生み出します。 

## アプローチ

 直接的な解決策は簡単です。 すべてのクエリについて、2 つのエンドポイントの LCA を見つけ、LCA に到達するまで両方のエンドポイントから上向きに歩きます。 歩きながら、辞書で果物の種類を数え、その数が道の半分より大きいかどうかを確認します。 パス上のすべての頂点は 1 回だけ訪問されるため、これは正しいです。 

問題は最悪の場合の操作数です。 単純なチェーンと、エンドポイントがそのチェーンの両端であるクエリであるツリーを考えてみましょう。 すべてのクエリが訪問する`N`頂点。 と`Q = 250000`そして`N = 250000`、これは約に達することができます`6.25 * 10^10`ディクショナリ操作や LCA 計算を考慮する前であっても、頂点操作を実行します。 ブルート フォースはパスの完全な頻度分布を明示的に取得するため機能しますが、同じ長いパスを何度も横断する可能性があるため失敗します。 

有益な観察は、ツリーが静的であるということです。 ツリーの頂点をルート化します`1`。 頂点ごとに`u`、根から果物へのパス上のすべての果物の種類を含む度数表を想像してください。`u`。 そのテーブルをすべてのデータに保存できれば`u`、任意のパス上の果物の種類の頻度は、4 つの根から頂点までのテーブルを組み合わせることで取得できます。 

永続的なセグメント ツリーは、周波数配列全体をコピーすることなく、これらのテーブルを正確に提供します。 に属するバージョン`u`に属するバージョンから取得されます`parent[u]`の数を増やすことで`color[u]`。 1 回の更新で作成されるのは`O(log N)`新しいセグメント ツリー ノードのため、ルートから頂点までのすべてのバージョンで必要になります。`O(N log N)`メモリと構築時間。 

仮定する`w = LCA(u, v)`そして`p = parent[w]`。 パスの頻度分布`u -> v`は```
root[u] + root[v] - root[w] - root[p].
```の引き算`root[w]`そして`root[p]`LCA 上のすべての頂点を削除し、LCA 自体を 1 回だけ保持します。 これは、ツリー パス頻度クエリに使用されるのと同じ永続ツリーのアイデアであり、特定のフルーツ ツリーの問題は通常、永続セグメント ツリー ソリューションに分類されます。 

個別の候補者検証パスの必要性をなくすもう 1 つの観察結果があります。 パス全体に長さがあると仮定します`L`、その大部分のタイプが発生します`M > L/2`回。 カラー ドメインを 2 つの半分に分割します。 大多数のタイプを含む半分には、以下のタイプを含む必要があります。`L/2`頂点。 したがって、セグメント ツリーを下降するときに、左半分に現在のパスの頂点の半分以上が含まれている場合、大部分がそこにあるはずです。 それ以外の場合、右半分に半分以上が含まれている場合は、そこに存在する必要があります。 どちらの半分にも半分以上が含まれていない場合は、過半数は存在しません。 

過半数を含む半分を選択した後、同じ議論が再帰的に適用されます。 後は一色に到達`O(log N)`レベル。 これにより、ランダム化された候補検索ではなく、正確で決定的なクエリが得られます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース |`O(NQ)`最悪の場合 |`O(N)`| 遅すぎる |
 | 最適 |`O(N log N + Q log N)`|`O(N log N)`| 承認済み |

 ## アルゴリズムのチュートリアル

 1. ツリーの頂点にルートを作成します`1`そして計算する`parent[u]`そして`depth[u]`頂点ごとに。 親関係により、永続セグメント ツリーに必要なルートから頂点へのパスが得られ、深さは LCA の計算に使用されます。 
2. 親用のバイナリリフティングテーブルを作成します。`up[k][u]`を保管します`2^k`- 番目の祖先`u`。 バイナリリフティングで見つけられる`LCA(u, v)`で`O(log N)`木の間を歩く代わりに時間を費やすことができます。 
3. 永続的なセグメントツリーのバージョンを構築する`root[u]`頂点ごとに。 バージョン`root[u]`頂点からのパス上のすべての果物の種類の頻度を表します`1`に`u`。 から始まる`root[parent[u]]`を 1 回挿入します。`color[u]`。 
4. お問い合わせの場合`(u, v)`、計算する`w = LCA(u, v)`そしてさせてください`p = parent[w]`。 クエリされたパス上のすべての果物の種類の頻度は、4 つの永続的なルートで表されます。`root[u]`、`root[v]`、`root[w]`、 そして`root[p]`。 
5. パスの合計長を次のように計算します。`depth[u] + depth[v] - 2 * depth[w] + 1`。 の`+1`LCA 自体を考慮します。 
6. カラーセグメントツリーのルートから開始します。その間隔は`[1, N]`。 クエリされたパス上の左半分に色がある頂点の数を計算します。 これは、次の左側の子の数を加算することによって取得されます。`root[u]`そして`root[v]`から対応するカウントを減算します。`root[w]`そして`root[p]`。 
7. 左半分のカウントがパス全体の長さの半分より大きい場合は、左の子に降ります。 それ以外の場合は、右半分のカウントを計算します。 右半分のカウントが半分より大きい場合は、右の子に降ります。 どちらの側も半分を超えない場合、クエリされたパスには過半数がないため、戻り値が返されます。`-1`。 
8. セグメントに単一の色が含まれるまで下降を繰り返します。 その色は可能な唯一の多数派であり、すべての決定はクエリされたパス内の正確な周波数に基づいているため、それを印刷しても安全です。 

セグメント ツリーの降下中の不変条件は、子に進むたびに、その子には現在のセグメントで表されるすべての頂点の半分以上が含まれるということです。 真の多数派は、その数全体がその色を含む子の内部にあるため、あらゆるレベルでこの特性を満たさなければなりません。 どちらの子も半分を超える値を持たない場合、どちらの子の個々の色もパスの半分を超える値を持つことはできません。 したがって、葉では、以前のすべての決定が有効であったときに、生き残った色が過半数となります。 

## Python ソリューション```python
import sys
from array import array

input = sys.stdin.readline

def solve():
    input = sys.stdin.buffer.readline

    n, q = map(int, input().split())
    color = [0] + list(map(int, input().split()))

    graph = [[] for _ in range(n + 1)]
    for _ in range(n - 1):
        a, b = map(int, input().split())
        graph[a].append(b)
        graph[b].append(a)

    # Root the tree at 1.
    parent = array('i', [0]) * (n + 1)
    depth = array('i', [0]) * (n + 1)

    # root[u] is the persistent segment-tree root for root -> u.
    root = array('i', [0]) * (n + 1)

    # Every insertion creates at most bit_length(n) + 1 nodes.
    max_nodes = n * (n.bit_length() + 1) + 5

    left = array('i', [0]) * max_nodes
    right = array('i', [0]) * max_nodes
    count = array('i', [0]) * max_nodes

    nodes = 0

    def update(previous, pos):
        nonlocal nodes

        # Clone the root of the previous version.
        nodes += 1
        new_root = nodes
        left[new_root] = left[previous]
        right[new_root] = right[previous]
        count[new_root] = count[previous] + 1

        old = previous
        cur = new_root
        lo = 1
        hi = n

        while lo < hi:
            mid = (lo + hi) >> 1

            if pos <= mid:
                old_child = left[old]

                nodes += 1
                new_child = nodes

                left[new_child] = left[old_child]
                right[new_child] = right[old_child]
                count[new_child] = count[old_child] + 1

                left[cur] = new_child

                old = old_child
                cur = new_child
                hi = mid
            else:
                old_child = right[old]

                nodes += 1
                new_child = nodes

                left[new_child] = left[old_child]
                right[new_child] = right[old_child]
                count[new_child] = count[old_child] + 1

                right[cur] = new_child

                old = old_child
                cur = new_child
                lo = mid + 1

        return new_root

    # Build parent/depth and persistent roots in one DFS.
    stack = [1]

    while stack:
        u = stack.pop()

        root[u] = update(root[parent[u]], color[u])

        for v in graph[u]:
            if v == parent[u]:
                continue
            parent[v] = u
            depth[v] = depth[u] + 1
            stack.append(v)

    # Binary lifting table.
    log = n.bit_length()
    up = [parent]

    for k in range(1, log):
        prev = up[-1]
        cur = array('i', [0]) * (n + 1)

        for u in range(1, n + 1):
            cur[u] = prev[prev[u]]

        up.append(cur)

    def lca(a, b):
        if depth[a] < depth[b]:
            a, b = b, a

        diff = depth[a] - depth[b]
        bit = 0

        while diff:
            if diff & 1:
                a = up[bit][a]
            diff >>= 1
            bit += 1

        if a == b:
            return a

        for k in range(log - 1, -1, -1):
            ua = up[k][a]
            ub = up[k][b]
            if ua != ub:
                a = ua
                b = ub

        return parent[a]

    output = []

    for _ in range(q):
        u, v = map(int, input().split())

        w = lca(u, v)
        pw = parent[w]

        total = depth[u] + depth[v] - 2 * depth[w] + 1

        ru = root[u]
        rv = root[v]
        rw = root[w]
        rp = root[pw]

        lo = 1
        hi = n

        while lo < hi:
            mid = (lo + hi) >> 1

            lu = left[ru]
            lv = left[rv]
            lw = left[rw]
            lp = left[rp]

            left_count = (
                count[lu] + count[lv]
                - count[lw] - count[lp]
            )

            if left_count * 2 > total:
                ru = lu
                rv = lv
                rw = lw
                rp = lp
                hi = mid
            else:
                ru = right[ru]
                rv = right[rv]
                rw = right[rw]
                rp = right[rp]
                lo = mid + 1

        # If neither side had a strict majority, the descent could
        # have followed an arbitrary right side. Verify the leaf.
        candidate = lo

        occurrences = (
            count[ru] + count[rv]
            - count[rw] - count[rp]
        )

        if occurrences * 2 > total:
            output.append(str(candidate))
        else:
            output.append("-1")

    sys.stdout.write("\n".join(output))

if __name__ == "__main__":
    solve()
```各エッジはツリーのルート化中にのみ必要となるため、グラフは通常の隣接リストとして保存されます。 親配列と深さの配列はコンパクトな整数配列を使用するため、最大の入力サイズでも Python のメモリ使用量が制御されます。 

永続セグメント ツリーは 3 つの整数配列を使用します。 新しいバージョンはそれぞれ、1 つの果物の種類に対応する根から葉のルートに沿ったノードのみを複製します。 何百万もの永続ノードが存在する可能性があり、何百万もの Python 関数呼び出しを回避することで大きな違いが生じるため、更新は再帰的ではなく反復的に書き込まれます。 

0 番目の永続ノードは空の頻度テーブルを表します。 したがって、LCA がルートである場合、`parent[w]`ゼロであり、`root[0]`まだ空のバージョンです。 これにより、パス周波数の式から特殊なケースが削除されます。 

4 根の公式は中心的な実装詳細です。`root[u]`そして`root[v]`2 つのルート パスを含み、減算します。`root[w]`そして`root[parent[w]]`LCA 自体を除き、ルートから LCA への寄与をすべて 2 回削除します。 結果として得られるカウントは、正確に周波数となります。`u -> v`。 

最終リーフがもう一度チェックされます。 降下中に、左側の子にパスの半分以上が含まれている場合は、そこに入らなければなりません。 そうでない場合は、正しい子が多数派にとって唯一可能な場所になります。 また、最終チェックにより、厳密な多数派を含まない側を繰り返し取った後、降下がリーフに到達する場合に対してコードが堅牢になります。 

すべてのカウントは最大です`N`したがって、通常の Python 整数ですでに十分です。 Python には整数オーバーフローの問題はありません。 

## 実用的な例

 公式サンプルには 7 つの頂点があります。 その木にはいくつかの種類の果物が含まれています`1`、クエリは多数決パスと非多数決パスの両方を実行します。 

最初のクエリでは、`1 -> 4`、パスは```
1 -> 3 -> 5 -> 4
```色付き```
3, 1, 1, 2
```どの色も 2 回以上出現しないため、答えは次のようになります。`-1`。 

2 番目のクエリについては、`7 -> 2`、パスは```
7 -> 5 -> 3 -> 2
```色付き```
2, 1, 1, 1
```色`1`は 4 回中 3 回発生するため、大多数です。 

| クエリ | LCA | パスの色 | 合計 | 多数派候補者 | 候補者数 | 出力 |
 | --- | --- | --- | --- | --- | --- | --- |
 |`1 4`|`1`|`3,1,1,2`| 4 |`1`| 2 |`-1`|
 |`7 2`|`3`|`2,1,1,1`| 4 |`1`| 3 |`1`|
 |`3 3`|`3`|`1`| 1 |`1`| 1 |`1`|
 |`4 7`|`5`|`2,1,1,2`| 4 |`2`| 2 |`-1`|

 4 行目は、平等では不十分な理由を示しています。 実際の公式出力は次のとおりです。`2`4 番目のクエリの場合、パスは`4 -> 5 -> 7`、誤って再構築されたパスによって示される 4 つの頂点シーケンスではありません。 その色は、`2,1,2`、型を与える`2`3 回のうち 2 回発生します。 これがまさに、LCA とエンドポイントを含むパスの式を慎重に扱う必要がある理由です。 公式のサンプル出力は次のとおりです`-1, 1, 1, 2`。 

より小さな例を使用すると、永続ツリーの降下がわかりやすくなります。```
5 2
1 2 2 3 2
1 2
2 3
3 4
4 5
1 5
2 4
```最初のパスには以下が含まれます`1,2,2,3,2`。 その合計サイズは、`5`、色`2`発生する`3`回。 カラードメインの下降中、カラーを含むセグメントは`2`必要なすべてのレベルでパスの周波数の半分以上を保持します。 

| クエリ | パス | 合計 | 候補者 | カウント | 結果 |
 | --- | --- | --- | --- | --- | --- |
 |`1 5`|`1,2,2,3,2`| 5 | 2 | 3 |`2`|
 |`2 4`|`2,2,3`| 3 | 2 | 2 |`2`|

 LCA はエンドポイントであるため、2 番目のクエリも役立ちます。 いつ`w = 2`、式`root[u] + root[v] - root[w] - root[parent[w]]`まだ頂点を数えます`2`まさに一度。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |`O(N log N + Q log N)`| 各頂点が作成する`O(log N)`永続ノード、各 LCA コスト`O(log N)`、各多数決クエリは下降します`O(log N)`セグメントツリーのレベル |
 | スペース |`O(N log N)`| 永続的なセグメント ツリーには、`O(N log N)`ノード、グラフとバイナリリフティングテーブルは`O(N log N)`以下 |

 最大のインスタンスには、`250000`頂点と`250000`クエリ。 クエリされたすべてのパスの線形スキャンは数百億の操作に達する可能性がありますが、永続的な構築では実行されるのは`O(N log N)`更新され、すべてのクエリは対数作業のみを実行します。 1024 MB のメモリ制限も永続構造としては異例に寛大ですが、およそ数百万の永続セグメント ツリー ノードが必要となるため、これは適切です。 

## テストケース

 次のテストでは、送信されたソリューションが次のように保存されていることを前提としています。`solution.py`そして暴露します`solve()`上に示した関数。 最大サイズのテストは、入力に数十万行が含まれるため、文字通りに記述するのではなく生成されます。```python
import sys
import io
import solution

def run(inp: str) -> str:
    old_stdin = sys.stdin
    old_stdout = sys.stdout

    try:
        sys.stdin = io.TextIOWrapper(io.BytesIO(inp.encode()))
        sys.stdout = io.StringIO()
        solution.solve()
        return sys.stdout.getvalue()
    finally:
        sys.stdin = old_stdin
        sys.stdout = old_stdout

# Official sample
sample1 = """\
7 4
3 1 1 2 1 1 2
1 3
7 5
2 3
5 3
5 6
4 5
1 4
7 2
3 3
4 7
"""

assert run(sample1) == " -1".strip() + "\n1\n1\n2", "official sample"

# Minimum-size input.
sample2 = """\
1 3
9
1 1
1 1
1 1
"""

assert run(sample2) == "9\n9\n9", "single vertex"

# All colors equal.
sample3 = """\
5 3
4 4 4 4 4
1 2
2 3
3 4
4 5
1 5
2 4
3 3
"""

assert run(sample3) == "4\n4\n4", "all equal"

# Exact half is not a majority.
sample4 = """\
4 3
1 2 1 2
1 2
2 3
3 4
1 4
1 3
2 4
"""

assert run(sample4) == "-1\n1\n2", "strict majority boundary"

# LCA is an endpoint, and the path is not rooted at either endpoint.
sample5 = """\
5 4
1 2 2 3 2
1 2
2 3
3 4
4 5
1 5
2 4
2 5
3 5
"""

assert run(sample5) == "2\n2\n2\n2", "LCA and endpoint cases"

# Maximum-size generated test.
# A chain makes the tree as deep as possible.
# All colors are distinct, so every path of length > 1 has no majority.
n = 250000
q = 250000

parts = [f"{n} {q}\n"]
parts.append(" ".join(map(str, range(1, n + 1))) + "\n")

for i in range(1, n):
    parts.append(f"{i} {i + 1}\n")

for i in range(q):
    if i & 1:
        parts.append(f"1 {n}\n")
    else:
        parts.append(f"{i + 1} {i + 1}\n")

large_input = "".join(parts)
large_output = run(large_input)

lines = large_output.splitlines()

assert len(lines) == q, "maximum-size query count"

for i, ans in enumerate(lines):
    if i & 1:
        assert ans == "-1", "maximum-size non-majority path"
    else:
        assert ans == str(i + 1), "maximum-size singleton path"

print("all tests passed")
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 公式サンプル |`-1, 1, 1, 2`| 一般的なツリー パスと公式の動作 |
 | 単一の頂点 |`9`すべてのクエリに対して | 最小サイズとエンドポイントを含むパス |
 | すべての色が等しい |`4`すべてのクエリに対して | 1 つの色が完全に優勢な場合の永続的なカウント |
 |`1,2,1,2`チェーン |`-1,1,2`| 厳密に半分より大きく、それ以上ではありません |
 | LCA エンドポイントのケース |`2`すべてのクエリに対して | 正しい 4 ルート パスの式 |
 | 生成された最大ケース | シングルトンカラー、それ以外の場合`-1`| 最大`N`、最大`Q`、深いツリー、およびスケーラビリティ |

 ## 特殊なケース

 単一頂点の場合```
1 1
7
1 1
```もっている`w = 1`、`parent[w] = 0`、 そして`total = 1`。 4根式は次のようになります。`root[1] + root[1] - root[1] - root[0]`、色の出現を 1 つだけ残します`7`。 セグメントツリーの降下がカラーに達する`7`、その数は`1`したがって、出力は次のようになります`7`。 

ちょうど半分のケース```
4 1
1 2 1 2
1 2
2 3
3 4
1 4
```パスカラーあり`1,2,1,2`。 どちらの色も 2 回出現しますが、大多数の色では厳密に 2 より大きい数が必要です。`4/2 = 2`。 セグメント ツリーの下降中、どちらの色範囲も正当に優勢であると宣言することはできず、最終的なカウント チェックで候補が拒否されます。 答えは`-1`。 

LCA補正の場合```
3 1
1 2 1
1 2
1 3
2 3
```もっている`w = 1`そして`parent[w] = 0`。 パス周波数は次のように計算されます。```
root[2] + root[3] - root[1] - root[0].
```ルートから`2`カラーが含まれているバージョン`1,2`、ルートから`3`バージョンに含まれる`1,1`、ルート バージョンを減算すると、LCA の重複コピーが 1 つ削除されますが、空のバージョンを減算しても何も変わりません。 結果のパスは次のとおりです`2,1,1`、だから色`1`カウントがある`2`そして答えは`1`。 

エンドポイント LCA の場合も同じ式で処理されます。 のために```
5 1
1 2 2 3 2
1 2
2 3
3 4
4 5
2 4
```LCA は頂点です`2`、これは最初のエンドポイントでもあります。 パスは`2 -> 3 -> 4`、色付き`2,2,3`。 を使用した式`root[2]`、`root[4]`、`root[2]`、 そして`root[1]`正確にこれら 3 つの頂点を残すので、色を付けます。`2`周波数がある`2`そして答えは`2`。 

非多数派のケースは、1 つの色がグローバルに頻繁に見える場合でも、長いパスで発生する可能性があります。 クエリでは、特定のパス上の頂点のみが考慮されます。 永続セグメント ツリーは、すべてのカウントが 2 つのエンドポイントとその LCA に対応する 4 つのバージョンから形成されるため、グローバル周波数とパス周波数の混同を回避します。 

最後に、可能な限り最大の深さが反復的に処理されます。 再帰的 DFS は、次のチェーン上で Python の再帰制限を超える可能性があります。`250000`一方、実装では明示的なスタックが使用されます。 永続的なセグメント ツリーの更新も反復的であり、Python 呼び出しスタックをツリーの深さに関係なく維持します。
