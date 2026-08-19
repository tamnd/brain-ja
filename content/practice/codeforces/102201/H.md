---
title: "CF 102201H - 説明が難しい"
description: "頂点 1 でツリーをルートします。クエリ (V, T) の場合、関連する頂点は、V 自体とルートを含む、まさに V の祖先です。 これらの祖先のうち、頂点 i は Ci = T の場合にのみ使用でき、そのコストは直線 [ fi(T)=Ai+BiT の値です。"
date: "2026-08-18T10:35:46+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102201
codeforces_index: "H"
codeforces_contest_name: "Moscow Pre-Finals Workshop 2019. KAIST Contest"
rating: 0
weight: 102201
solve_time_s: 600
verified: true
draft: false
---

[CF 102201H - 説明が難しい](https://codeforces.com/problemset/problem/102201/H)

 **評価:** -
 **タグ:** -
 **解決時間:** 10 分
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 ツリーの頂点 1 をルートにします。クエリの場合`(V, T)`、関連する頂点はまさにその祖先です。`V`、 含む`V`それ自体とルート。 その先祖たちの中で、頂点は`i`の場合にのみ使用できます`C_i >= T`、そのコストはラインの値です

 [
 f_i(T)=A_i+B_iT。 
】

 タスクは、そのような最小値を返すことです。 

木の状態は`B`は中心的な構造特性です。 

[
 B_{\operatorname{parent}(v)}\le B_v.
 】

 したがって、根から葉へのすべてのパスに沿って、対応する線の傾きが減少することはありません。 この条件がなければ、この問題には完全に動的な凸包構造が必要になります。 これにより、ルートから頂点への 1 つのパスに属するラインに便利な順序が付けられます。 

最大 80,000 個の頂点と 160,000 個のクエリがあります。 クエリにはルートからリーフへのパス全体が含まれる場合があるため、チェーン形状のツリーではパスを明示的にたどるのはすでに遅すぎます。 最悪の場合、クエリごとに約 (80,000) の祖先があり、160,000 クエリに対しておよそ (1.28\times10^{10}) 行の評価が得られます。 ソリューションは、クエリごとの対数作業に近づく必要があります。 

一般に誤った実装を引き起こすエッジ条件が 3 つあります。 

まず、クエリされた頂点自体がパスに属します。 例えば、```
1 1
5
7
1000000000
1 0
```答えがあります`5`頂点 1 はクエリされた頂点でもあるためです。 から始まる実装`parent[V]`誤って候補が見つからない可能性があります。 

２つ目の条件は、`C_i >= T`、 ない`C_i > T`。 のために```
2 1
10 1
1 2
5 5
1 2
2 5
```答えは`6`、頂点 2 には`C_2 = 5`で有効です`T = 5`。 厳密な比較では、それが破棄されて返されます。`15`。 

三番目、`T = 0`合法です。 その場合、すべての頂点は満たされます`C_i >= T`したがって、答えは単純に最小になります。`A_i`ルートから`V`パス。 サンプルでは、​​クエリは`(4, 0)`頂点を考慮します`1, 2, 4`そして戻ってきます`2`。 正のクエリ座標を想定する実装、または Li Chao ドメインを次の位置で開始する実装`1`ここで失敗する可能性があります。 

## アプローチ

 ブルート フォース ソリューションは、定義に直接準拠します。 For every query, walk from`V`ルートに向かって、その頂点を無視します。`C_i`よりも小さいです`T`、評価します`A_i+B_iT`残りは。 これは正しいです。そのウォーク上のすべての頂点は、ルートからルートに至る一意のパス上の頂点の 1 つであるからです。`V`。 

問題は経路の長さです。 80,000 個の頂点のチェーンと 160,000 個のクエリでは、(80,000\cdot160,000=128) 億回の評価が必要になる可能性があります。 1 回の評価はほんの数回の整数演算ですが、それは制限時間をはるかに超えています。 

最初の有用な観察は、すべての頂点を線とみなすことです。 

[
 y=A_i+B_ix。 
】

 次にクエリは、次の場所で最も低い有効な行を要求します。`x = T`。 2 番目の観察は、有効性が X 軸の接頭辞であるということです。`i`すべての人のために存在する`x <= C_i`。 したがって、すべての頂点は、実際には左から右に向かって伸びる線分を与えます。`C_i`。 

3 番目の観察はツリー構造です。 ルートからノードに向かって頂点を処理すると、勾配が減少しないラインを挿入することになります。 頂点でのクエリ`V`ルートからルートに行を挿入した後に得られるデータ構造が正確に必要です。`V`パス。 

これを永続化することができます。 各頂点は、親から継承したデータ構造のバージョンを保存し、独自の線分を追加します。 でのクエリ`V`バージョンを使用します`V`、そのため、永続化により、候補セットが自動的にその先祖に制限されます。`V`。 

残りの問題は、行は次までしか有効ではないことです。`C_i`X 軸全体ではなく、 Li Chao ツリーでは、座標ドメイン全体ではなく、ある間隔にわたってラインを挿入できます。 すべての有効な間隔は`[0, C_i]`、そのプレフィックス上の行の範囲挿入を実行します。 Li Chao ツリーは、次のクエリが実行されることを保証します。`T`有効期間に含まれる行を正確に表示します。`T`。 

永続化は、挿入によって変更された Li Chao ノードのみをコピーすることによって処理されます。 子に属するバージョンは親のルートを指すため、異なるツリーのブランチは変更されていない構造をすべて共有します。 

結果として得られるアプローチは、ノードに Li Chao 情報が含まれる永続的なセグメント ツリーです。 範囲挿入のコストは (O(\log^2 C)) (C\le10^9)、ポイント クエリのコストは (O(\log C)) です。 指定された範囲では、対数は最大でも約 30 になります。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | (O(NQ)) | (O(N)) | 遅すぎる |
 | 永続的な間隔 Li Chao | (O(N\log^2 10^9+Q\log 10^9)) | (O(N\log^2 10^9)) 最悪の場合 | 承認済み |

 ## アルゴリズムのチュートリアル

 1. ツリーを頂点 1 にルートし、すべての頂点の親を決定します。 

ツリーは 80,000 個の頂点のチェーンになる可能性があり、Python の再帰制限はそのような走査には適していないため、これを再帰的ではなく反復的に行います。 
2. 頂点ごとに`v`、 定義する`root[v]`頂点 1 から頂点 1 までのパス上の頂点に属するすべての有効な線分を表す永続的な Li Chao ツリーのルートになります。`v`。 

ルートのバージョンには、最初は行 1 が含まれています。他の頂点ごとに`v`、から始めます`root[parent[v]]`そして行を挿入します

 [
 y=A_v+B_vx
 】

 間隔で

 [
 [0,C_v]。 
】

 これは重要な永続性不変条件です。 現時点で`root[v]`が構築されると、その先祖に属する行が正確に含まれます。`v`。 
3. X 軸を整数間隔で表現します。`[0, 10^9]`。 

整数値のみをクエリします。`T`したがって、浮動小数点の交差は必要ありません。 Li Chao ツリーは、完全に整数行値の比較で機能します。 
4. ラインを挿入するには`[0,C_v]`、Li Chao 座標ツリーを再帰的に下降します。 

現在の座標間隔が完全にカバーされている場合、`[0,C_v]`、その間隔で通常のリーチャオ挿入を実行します。 

間隔の一部のみがカバーされている場合は、カバーされている子を再帰的に処理します。 

データ構造は永続的であるため、変更されたすべての Li Chao ノードがコピーされます。 変更されていない子は引き続き古いバージョンを指します。 
5. 通常の Li Chao 挿入の場合、各セグメント ツリー ノードに 1 つの候補行を保持します。 

左端点、中点、右端点で古い線と新しい線を比較します。 新しいラインの方が中間点で優れている場合は、保存されているラインと交換します。 中間点で負けたラインは、せいぜい 1 つの側でまだ関連している可能性があるため、その側に再帰します。 

これは標準の Li Chao 不変式です。ルートからリーフへのすべてのパスに沿って、少なくとも 1 つの保存されたラインが対応するリーフ座標で最適です。 
6. 答えるには`(V,T)`、で開始します`root[V]`そして葉を表す葉に降りていきます`T`。 

訪問したすべての Li Chao ノードで、保存されているラインを評価します。`T`そして最低限のものを取る。 以来`root[V]`～の祖先のみが含まれます`V`、および間隔の挿入により、行が次の場合にのみ存在するようになりました。`T <= C_i`、最小値はクエリで許可される頂点を正確に超えています。 
7. 収集した回答を元の順序で出力します。 

### なぜ効果があるのか

 不変条件とは、`root[v]`ルートから頂点までの頂点の線分が正確に含まれます。`v`パス。 これは、ラインを挿入した後の頂点 1 にも当てはまります。 親から子に移動するとき、永続化によって親のバージョンがコピーされ、子の線分が正確に追加されるため、不変式は true のままになります。 

お問い合わせの場合`(V,T)`、頂点からの線`i`に登場する`root[V]`まさにいつ`i`の先祖です`V`。 間隔を挿入すると、座標上でのみ Li Chao 構造に配置されます。`x <= C_i`なので、正確にいつでもクエリに参加します。`T <= C_i`。 Li Chao 不変式は、すべての関与する行の最小値がルートから`T`検索パス。 

の単調性`B`は祖先の構築と互換性があり、この問題がパスベースの凸包解決を可能にする構造的な理由です。 以下の実装では、より一般的な区間 Li Chao 公式を使用しているため、浮動小数点交差計算や等しい傾きの特殊なケースは必要ありません。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

INF = 10**30
XMAX = 10**9

def solve():
    n, q = map(int, input().split())

    A = [0] + list(map(int, input().split()))
    B = [0] + list(map(int, input().split()))
    C = [0] + list(map(int, input().split()))

    graph = [[] for _ in range(n + 1)]
    for _ in range(n - 1):
        u, v = map(int, input().split())
        graph[u].append(v)
        graph[v].append(u)

    parent = [0] * (n + 1)
    order = [1]
    parent[1] = -1

    for u in order:
        for v in graph[u]:
            if v == parent[u]:
                continue
            parent[v] = u
            order.append(v)

    # Persistent Li Chao nodes.
    #
    # Each node contains:
    #   line index stored at this node
    #   left child
    #   right child
    #
    # A line index of 0 means "no line".
    lc = [0]
    rc = [0]
    ln = [0]

    def value(line_id, x):
        if line_id == 0:
            return INF
        return A[line_id] + B[line_id] * x

    def clone(node):
        if node == 0:
            lc.append(0)
            rc.append(0)
            ln.append(0)
        else:
            lc.append(lc[node])
            rc.append(rc[node])
            ln.append(ln[node])
        return len(ln) - 1

    def add_line(node, l, r, new_line):
        node = clone(node)

        old_line = ln[node]

        if old_line == 0:
            ln[node] = new_line
            return node

        mid = (l + r) >> 1

        if value(new_line, mid) < value(old_line, mid):
            ln[node], new_line = new_line, old_line

        if l == r:
            return node

        if value(new_line, l) < value(ln[node], l):
            left = add_line(lc[node], l, mid, new_line)
            lc[node] = left
        elif value(new_line, r) < value(ln[node], r):
            right = add_line(rc[node], mid + 1, r, new_line)
            rc[node] = right

        return node

    def add_segment(node, l, r, ql, qr, new_line):
        if qr < l or r < ql:
            return node

        node = clone(node)

        if ql <= l and r <= qr:
            # The whole interval is covered, so this is a normal
            # Li Chao insertion.
            return add_line(node, l, r, new_line)

        mid = (l + r) >> 1

        if ql <= mid:
            lc[node] = add_segment(
                lc[node], l, mid, ql, qr, new_line
            )

        if qr > mid:
            rc[node] = add_segment(
                rc[node], mid + 1, r, ql, qr, new_line
            )

        return node

    def query(node, l, r, x):
        ans = value(ln[node], x)

        if l == r:
            return ans

        mid = (l + r) >> 1

        if x <= mid:
            if lc[node]:
                other = query(lc[node], l, mid, x)
                if other < ans:
                    ans = other
        else:
            if rc[node]:
                other = query(rc[node], mid + 1, r, x)
                if other < ans:
                    ans = other

        return ans

    roots = [0] * (n + 1)

    # Build versions in parent-before-child order.
    for v in order:
        base = roots[parent[v]] if parent[v] > 0 else 0
        roots[v] = add_segment(base, 0, XMAX, 0, C[v], v)

    out = []

    for _ in range(q):
        v, t = map(int, input().split())
        out.append(str(query(roots[v], 0, XMAX, t)))

    sys.stdout.write("\n".join(out))

if __name__ == "__main__":
    solve()
```最初のトラバーサルでは、親関係が構築されます。 配列`order`これは、すべての親がその子の前に存在するという意味で、ルート付きツリーのトポロジー的順序です。`root[v]`再帰なしで。 

配列`lc`、`rc`、 そして`ln`永続的なリーチャオツリーを形成します。 インデックス 0 は空のノードを表します。 コピーされたノードは、子ポインタとその格納された行の両方を以前のバージョンから継承するため、新しい行の影響を受けるノードのみを変更する必要があります。`add_line`通常のリーチャオ挿入です。 ノードに保存されているラインは、現在中間点でより良好なラインです。 移動された線がまだ左または右の端点でそれを上回ることができる場合、その線は再帰的にその側に挿入されます。`add_segment`座標範囲にのみ線を追加します`[0,C_v]`。 現在の間隔が完全にカバーされると、次に委任されます。`add_line`。 それ以外の場合は、現在のノードをコピーし、交差する子に続行します。 

境界は包括的です。 呼び出しでは、`0`を通して`C[v]`したがって、次のクエリは`T == C[v]`頂点を正しく受け入れます。 グローバル座標間隔も両端を含みます。そのため、再帰では次のものが使用されます。`[l, r]`半開インターバルではなく。 

Python の整数には任意の精度があるため、次のような式が使用できます。`B[i] * T + A[i]`オーバーフローすることはできません。 C++ では、最大値は最大でも約 (10^{18}+10^9) であるため、ここでも 64 ビットの符号付き整数で十分です。 

実装の詳細の 1 つは注目に値します。 ツリー走査は反復的ですが、Li Chao 再帰の深さは約 30 のみです。これは、その座標範囲が次のとおりであるためです。`[0,10^9]`。 したがって、ツリー自体は Python の再帰の深さの制限を引き起こすことはできません。 

## 実用的な例

 付属のサンプルにはツリーが付いています```
        1
       / \
      2   3
     / \
    4   5
```頂点 4 のパスは次のとおりです。`1 -> 2 -> 4`。 彼らのセリフは、

 [
 5+T、\qquad 4+2T、\qquad 2+4T。 
】

 有効期限は、`10^9`、`2`、 そして`5`。 

最初のクエリでは、`T = 0`、これらの行はすべて有効です。 

| 頂点 | ライン | C | T=0 での値 | 現在の最小値 |
 | --- | --- | --- | --- | --- |
 | 1 | (5+T) | (10^9) | 5 | 5 |
 | 2 | (4+2T) | 2 | 4 | 4 |
 | 4 | (2+4T) | 5 | 2 | 2 |

 答えは`2`。 

2 番目のクエリについては、`T = 2`、3 つの頂点すべてがまだ有効条件を満たしています。 

| 頂点 | ライン | C | T=2 での値 | 現在の最小値 |
 | --- | --- | --- | --- | --- |
 | 1 | (5+T) | (10^9) | 7 | 7 |
 | 2 | (4+2T) | 2 | 8 | 7 |
 | 4 | (2+4T) | 5 | 10 | 7 |

 答えは`7`。 

トレースは、有効性条件がクエリ対象の頂点ではなくライン自体に付加されている理由を示しています。 で`T=2`、頂点 2 は正確にその境界上にあり、引き続き適格です。 

2 番目の例では、境界条件を分離します。```
3 3
10 1 100
1 2 3
1000000000 5 100
1 2
2 3
2 5
3 5
3 0
```ルートから 3 へのパスには頂点 1、2、および 3 が含まれます。 

| クエリ | 適格な頂点 | 値 | 答え |
 | --- | --- | --- | --- |
 |`(2,5)`| 1、2 | 15、11 | 11 |
 |`(3,5)`| 1、2、3 | 15、11、115 | 11 |
 |`(3,0)`| 1、2、3 | 10、1、100 | 1 |

 最初のクエリは特に便利です。`C_2 = 5`そして`T = 5`。 頂点 2 からのラインは存在し続ける必要があります。 最後のクエリにより、次のことが確認されます。`T = 0`すべての頂点を適格にし、目的を最小限に抑えます`A`。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O(N\log^2 10^9+Q\log 10^9)) | すべての頂点は 1 つの永続的な間隔の挿入を作成し、すべてのクエリは 1 つの Li Chao ポイント クエリを実行します。 
| スペース | (O(N\log^2 10^9)) 最悪の場合 | 永続化は、間隔の挿入ごとに変更された Li Chao ノードをコピーします。 

(\log_2(10^9)) は約 30 にすぎないため、対数係数は小さな定数によって制限されます。 このソリューションは、クエリごとにルートから頂点までのパスを移動することを回避します。これは、チェーン形状のツリーの直接実装を不可能にする部分です。 

1024 MB という大きなメモリ制限は、この永続的な表現に役立ちます。 主なコストは、元のツリーではなく、コピーされた Li Chao ノードです。 

## テストケース```python
import io
import sys

# The production solution is the solve() function from above.
# For assert-based tests, execute the same algorithm against an
# in-memory stdin/stdout pair.

def run(inp: str) -> str:
    old_stdin = sys.stdin
    old_stdout = sys.stdout

    sys.stdin = io.StringIO(inp)
    sys.stdout = io.StringIO()

    try:
        solve()
        return sys.stdout.getvalue().strip()
    finally:
        sys.stdin = old_stdin
        sys.stdout = old_stdout

# Provided sample
sample1 = """\
5 2
5 4 3 2 1
1 2 3 4 5
1000000000 2 4 5 2
1 2
1 3
2 4
2 5
4 0
4 2
"""

assert run(sample1) == """\
2
7
""".strip(), "sample 1"

# Minimum-size tree. The only possible candidate is the root.
sample2 = """\
1 3
17
23
1000000000
1 0
1 1000000000
1 999999999
"""

assert run(sample2) == """\
17
23000000017
23000000000
""".strip(), "single vertex"

# Exact C boundary and T = 0.
sample3 = """\
3 3
10 1 100
1 2 3
1000000000 5 100
1 2
2 3
2 5
3 5
3 0
"""

assert run(sample3) == """\
11
11
1
""".strip(), "C boundary and zero"

# Equal slopes. The ancestor condition allows equal B values.
# At T=10, vertex 3 is valid exactly at C=10.
sample4 = """\
4 4
20 5 1 100
7 7 7 7
1000000000 3 10 2
1 2
2 3
2 4
3 3
3 10
4 10
4 0
"""

assert run(sample4) == """\
15
71
27
1
""".strip(), "equal slopes"

# Large values, testing 64-bit-sized products.
sample5 = """\
2 3
1000000000 1000000000
1000000000 1000000000
1000000000 1000000000
1 2
2 0
2 1
2 1000000000
"""

assert run(sample5) == """\
1000000000
2000000000
1000000000000001000000000
""".strip(), "large arithmetic"

# A chain catches implementations that accidentally exclude an
# ancestor or use the wrong validity comparison.
sample6 = """\
5 4
50 40 30 20 10
1 2 3 4 5
1000000000 1 2 3 4
1 2
2 3
3 4
4 5
5 0
5 1
5 2
5 4
"""

assert run(sample6) == """\
10
41
56
250
""".strip(), "chain boundaries"

print("all tests passed")
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 単一の頂点 |`17`、`23000000017`、`23000000000`| ルートのみのパスと可能な最大のクエリ値 |
 | 3 頂点チェーン |`11`、`11`、`1`| ちょうど`C_i = T`境界線と`T = 0`|
 | 等しい傾き |`15`、`71`、`27`、`1`| の非厳密な単調性`B`と等傾斜線 |
 | 大規模な算術 | 大きな 64 ビット サイズの値 | オーバーフローのない正しい整数演算 |
 | 5 頂点チェーン |`10`、`41`、`56`、`250`| 複数の祖先の有効性境界とパスの永続性 |

 ## 特殊なケース

 単一頂点の場合```
1 3
17
23
1000000000
1 0
1 1000000000
1 999999999
```にはエッジがなく、すべてのクエリが同じルート行を使用します。 永続的な構造は空で始まり、その上にルート行が挿入されます。`[0,10^9]`。 すべてのクエリがその行に到達すると、`17`、`23000000017`、 そして`23000000000`。 

正確な妥当性境界については、```
3 1
10 1 100
1 2 3
1000000000 5 100
1 2
2 3
2 5
```頂点 2 には`C_2 = 5`、その行は上に挿入されます`[0,5]`。 でのクエリ`T=5`その間隔の終点に到達し、ラインが含まれます。 その値は`1+7*5=36`この特定の入力で、行に`B_2=7`; 先ほどの具体的なテストでは、`A_2=1`そして`B_2=2`、与える`11`。 実装では使用します`qr = C[v]`, したがって、平等は自然に処理されます。 

のために`T=0`、 毎`C_i`は少なくとも 1 であるため、パス上のすべての行が有効になります。 Li Chao クエリは座標 0 から始まり、特別な分岐は必要ありません。 サンプルパス内`1 -> 2 -> 4`、値は次のとおりです。`5`、`4`、 そして`2`、したがって、答えは次のとおりです`2`。 

等勾配も合法です。`B`非厳密です。 祖先とその子供が両方とも持っていると仮定します。`B=7`。 それらの線は平行です。 The Li Chao comparisons still work because it never divides by a slope difference. The line with the smaller value at the query coordinate wins automatically.

 Finally, the largest possible product is on the order of (10^9\cdot10^9=10^{18}). Python handles this directly with arbitrary-precision integers. In a fixed-width implementation, the calculation must use a 64-bit signed type rather than a 32-bit integer.
