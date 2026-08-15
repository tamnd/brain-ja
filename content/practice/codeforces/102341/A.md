---
title: "CF 102341A - アラカザム"
description: "スプーン数の配列があり、位置 i には最初に a[i] が含まれます。 シャッフル l r 操作は、現在区間 [l, r] 内にあるすべての値を取得し、それらの値を同じ位置間でランダムに並べ替えます。"
date: "2026-08-13T03:02:01+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102341
codeforces_index: "A"
codeforces_contest_name: "Radewoosh+mnbvmar Contest (supported by AIM Tech)"
rating: 0
weight: 102341
solve_time_s: 643
verified: true
draft: false
---

[CF 102341A - アラカザム](https://codeforces.com/problemset/problem/102341/A)

 **評価:** -
 **タグ:** -
 **解決時間:** 10 分 43 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 スプーン数の配列があります。`i`最初に含まれています`a[i]`。 あ`shuffle l r`操作は、現在間隔内にあるすべての値を取得します`[l, r]`そして、それらの値を同じ位置間でランダムに並べ替えます。 あ`get i`操作は位置の期待値を要求します`i`これまでのすべてのシャッフルの後。 

最大の難点は、実際の順列がランダムであるため、具体的な配列を 1 つ維持できないことです。 すべてのポジションで期待値を維持する必要があります。 シャッフルでは、個々のポジションの期待値は保存されませんが、シャッフルされた間隔全体の合計は保存されます。 

と`n, q <= 250000`、すべての操作に対して間隔全体をスキャンするアプローチでは、大まかに`nq`、それは約です`6.25 * 10^10`最悪の場合の要素操作。 これは、競技プログラミングの制限である 2 秒がサポートできる範囲をはるかに超えています。 すべての操作で、対数的に多くのデータ構造ノードのみを操作する必要があります。 

直接実装が静かに失敗する可能性がある境界ケースがいくつかあります。 シャッフルされた間隔にポジションが 1 つだけ含まれている場合、たとえば```
1 2
7
shuffle 1 1
get 1
```答えは`7.000000000000`。 すべてのシャッフルを値の変更として扱う不用意な実装では、数学的には間隔の平均が依然として正確であるにもかかわらず、不必要な丸めまたは平均化操作が導入される可能性があります。`7`。 

より明らかなケースは、配列全体のシャッフルです。```
3 2
1 2 6
shuffle 1 3
get 2
```答えは`3.000000000000`なぜなら、すべてのポジションが 3 つの値のそれぞれを受け取る可能性が等しいからです。 元の位置の値のみを見る`2`誤って生成してしまう`2`。 

もう 1 つのよくある境界エラーは、エンドポイントが包括的であることを忘れていることです。```
3 2
1 2 9
shuffle 1 2
get 2
```期待値は`1.500000000000`。 位置`3`はそのままですが、ポジションは`1`そして`2`どちらも平均値になります`1`そして`2`。 誤って使用してしまう実装`[l, r)`ポジションを離れるだろう`2`変更せずに戻る`2`。 

重複するシャッフルが繰り返される場合は、元の配列のみを保存するだけでは不十分な場合もあります。```
3 4
1 2 3
shuffle 1 2
shuffle 2 3
get 1
get 3
```答えは次のとおりです。`1.500000000000`そして`2.250000000000`。 2 番目のシャッフルでは、元の値ではなく、最初のシャッフルで生成された期待値を使用する必要があります。`2`そして`3`。 

## アプローチ

 ブルートフォースアプローチは簡単です。 すべてのポジションの現在の期待値を維持します。 のために`shuffle l r`、からの期待値の合計を計算します。`l`を通して`r`、で割る`r-l+1`、その平均を区間のすべての位置に書き込みます。 のために`get i`、現在の値を位置に出力するだけです`i`。 

これは正しいです。なぜなら、次の値が一様にランダムに並べ替えられた後だからです。`[l,r]`、その間隔内のすべての位置は、すべての値を受け取る確率が同じになります。 現在の期待値が`E_l, E_{l+1}, ..., E_r`、期待値の線形性は、次のようにすべての位置で新しい期待値を与えます。 

[
 \frac{E_l+E_{l+1}+\cdots+E_r}{r-l+1}。 
】

 問題はコストです。 シャッフルには以下を含めることができます`250000`ポジション、そして最悪の場合はすべて`250000`操作は配列全体をシャッフルすることができます。 すべてのポジションを更新するには約時間がかかります

 [
 250000 \time 250000 = 62,500,000,000
 】

 合計を計算する前であっても、位置を更新します。 

このボトルネックを取り除く観察は、シャッフルの後、間隔全体が 1 つの共通の期待値を持つということです。 シャッフルが発生すると、その間隔内の個々の期待値を覚えておく必要はありません。 シャッフルの前に間隔の合計が必要なだけで、その後、間隔全体を 1 つの定数値を持つものとして遅延的にマークできます。 

これはまさに、範囲の割り当てと範囲合計クエリを組み合わせたものです。 遅延セグメント ツリーは両方の操作を表現できます。`O(log n)`。 各ツリー ノードは、その間隔の合計を保存し、必要に応じて、間隔内のすべての位置が現在同じ期待値を持つことを示す遅延割り当てを保存します。 

ブルートフォース手法が機能するのは、数学で必要とされるのと同じ範囲の割り当てを明示的に実行するためですが、すべての個別の割り当てが具体化されます。 セグメント ツリーは、その割り当てをツリー ノード内に圧縮して保持します。 間隔全体は、その位置をすべて書き換える代わりに、1 つの数値で表すことができます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース |`O(nq)`|`O(n)`| 遅すぎる |
 | 最適 |`O((n+q) log n)`|`O(n)`| 承認済み |

 ## アルゴリズムのチュートリアル

 1. 初期配列にセグメント ツリーを構築します。 各ノードは、その間隔内の期待値の合計を格納します。 最初は、これらは単なる元のスプーン数です。 
2. すべてのセグメント ツリー ノードに遅延割り当て値を追加します。 ノードに遅延値がある場合`x`、それは、そのノードによって表されるすべての位置が現在期待値を持っていることを意味します`x`。 私たちが使用するのは`None`これは、保留中の制服割り当てがないことを意味します。 
3. のために`shuffle l r`、最初に間隔の合計をクエリします。`[l,r]`。 この合計を`S`、間隔の長さを次のようにします`len = r-l+1`。 ランダムな置換後の期待値は次のとおりです。`S / len`。 
4. この平均を間隔全体に割り当てます`[l,r]`遅延伝播を使用します。 完全に覆われたツリー ノードの場合、`k`位置の合計は次のようになります`average * k`、その遅延割り当ては次のようになります`average`。 
5. のために`get i`、セグメント ツリーを通って、位置を表すリーフまで下降します。`i`。 ノードに保留中の遅延割り当てがある場合は、下降する前にそれを子に伝播します。 リーフには、要求された正確な期待値が含まれます。 
6. すべての回答を十分な 10 進数で出力します。 小数点以下 12 桁を出力するだけで、必要な情報を十分に満たすことができます。`1e-9`絶対誤差または相対誤差。 

### なぜ効果があるのか

 不変条件は、すべてのセグメント ツリー ノードがその区間内のすべての位置の期待値の正確な合計を格納するのに対し、遅延割り当てではその区間内のすべての位置が同じ期待値を持つことを記録することです。 

シャッフルして考えてみましょう`[l,r]`。 シャッフルの前に、位置に期待値を入れます`j`なれ`E_j`。 すべての順列は、区間内のすべての元の位置に、任意のターゲット位置に出現する同じ確率を与えます。 したがって、すべてのターゲット位置での新しい期待値は次のようになります。 

[
 \frac{\sum_{j=l}^{r} E_j}{r-l+1}。 
】

 セグメント ツリーはこの合計を正確に計算し、この平均を区間全体に正確に割り当てます。 したがって、シャッフルするたびに不変条件は true のままになります。 ポイント クエリは、そのパスに影響を与える割り当てに従い、リーフによって表される期待値を返します。`get`答えは正しいです。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

def solve():
    n, q = map(int, input().split())
    a = list(map(float, input().split()))

    size = 4 * n + 5
    tree = [0.0] * size
    lazy = [None] * size

    def build(node, left, right):
        if left == right:
            tree[node] = a[left]
            return

        mid = (left + right) >> 1
        lc = node << 1
        rc = lc | 1

        build(lc, left, mid)
        build(rc, mid + 1, right)
        tree[node] = tree[lc] + tree[rc]

    def apply(node, left, right, value):
        tree[node] = value * (right - left + 1)
        lazy[node] = value

    def push(node, left, right):
        value = lazy[node]
        if value is None or left == right:
            return

        mid = (left + right) >> 1
        lc = node << 1
        rc = lc | 1

        apply(lc, left, mid, value)
        apply(rc, mid + 1, right, value)
        lazy[node] = None

    def range_sum(node, left, right, ql, qr):
        if ql <= left and right <= qr:
            return tree[node]

        push(node, left, right)

        mid = (left + right) >> 1
        result = 0.0

        if ql <= mid:
            result += range_sum(node << 1, left, mid, ql, qr)

        if qr > mid:
            result += range_sum(node << 1 | 1, mid + 1, right, ql, qr)

        return result

    def range_assign(node, left, right, ql, qr, value):
        if ql <= left and right <= qr:
            apply(node, left, right, value)
            return

        push(node, left, right)

        mid = (left + right) >> 1

        if ql <= mid:
            range_assign(node << 1, left, mid, ql, qr, value)

        if qr > mid:
            range_assign(node << 1 | 1, mid + 1, right, ql, qr, value)

        tree[node] = tree[node << 1] + tree[node << 1 | 1]

    def point_query(node, left, right, pos):
        if left == right:
            return tree[node]

        push(node, left, right)

        mid = (left + right) >> 1

        if pos <= mid:
            return point_query(node << 1, left, mid, pos)

        return point_query(node << 1 | 1, mid + 1, right, pos)

    build(1, 0, n - 1)

    output = []

    for _ in range(q):
        parts = input().split()

        if parts[0] == "shuffle":
            l = int(parts[1]) - 1
            r = int(parts[2]) - 1

            total = range_sum(1, 0, n - 1, l, r)
            average = total / (r - l + 1)

            range_assign(1, 0, n - 1, l, r, average)

        else:
            pos = int(parts[1]) - 1
            answer = point_query(1, 0, n - 1, pos)
            output.append(f"{answer:.12f}")

    sys.stdout.write("\n".join(output))

if __name__ == "__main__":
    solve()
```の`tree`配列には間隔の合計が含まれます。 葉の場合、合計はその位置での期待値にすぎません。 内部ノードの場合、合計は 2 つの子の合計になります。 

の`lazy`配列は保留中の範囲割り当てを表します。 もし`lazy[node]`は`x`、そのノードの間隔内のすべての位置に期待値があります`x`。 したがって、その合計は`x * interval_length`。 単一の割り当て値はサブツリー全体を表すことができ、これがソリューションを高速化する圧縮です。 

の`range_sum`関数は、シャッフルの前に合計を取得するためにのみ使用されます。 概念的には値を変更しませんが、再帰的走査で正しい子の合計が見つかるように、遅延割り当てを下方にプッシュする可能性があります。 

の`range_assign`関数はシャッフルの実際の効果を実行します。 完全にカバーされたノードでは、子にはまったくアクセスしません。 ノードの合計を変更し、割り当てを遅延的に記録します。 部分的なオーバーラップでは、親の割り当てを最初にプッシュする必要があります。子は、いずれかがさらに変更される前に親の現在の値を受け取る必要があるためです。 

の`point_query`関数はルートからリーフへの 1 つのパスのみをたどります。 遅延割り当てをそのパスにプッシュするため、リーフは常に要求された位置の最新の期待値を表します。 

すべての入力インデックスは、クエリの読み取り時に 1 から始まるインデックスから 0 から始まるインデックスに 1 回だけ変換されます。 間隔の長さは引き続き次のように計算されます。`r - l + 1`両方のエンドポイントが含まれているためです。 

Python の整数では合計のオーバーフローの問題は発生しませんが、シャッフルするたびに除算が必要となるため、ツリーには浮動小数点の期待値が格納されます。 平均は平均化される値の最小値と最大値を離れることがないため、値は初期値の範囲によって制限されます。 小数点以下 12 桁を印刷すると、必要な公差に対して十分な精度が得られます。 

## 実用的な例

 提供されたサンプルは次で始まります`[1, 2, 3]`。 

| 操作 | 間隔 | シャッフル前の合計 | 割り当てられた平均値 | 演算後の期待値 |
 | --- | --- | --- | --- | --- |
 |`get 1`|`1`|`1`|`1`|`[1, 2, 3]`|
 |`get 3`|`3`|`3`|`3`|`[1, 2, 3]`|
 |`shuffle 1 2`|`[1,2]`|`3`|`1.5`|`[1.5, 1.5, 3]`|
 |`shuffle 2 3`|`[2,3]`|`4.5`|`2.25`|`[1.5, 2.25, 2.25]`|
 |`get 1`|`1`|`1.5`|`1.5`|`[1.5, 2.25, 2.25]`|
 |`get 3`|`3`|`2.25`|`2.25`|`[1.5, 2.25, 2.25]`|

 最初のシャッフルはポジションの期待を置き換えます`1`そして`2`による`(1+2)/2 = 1.5`。 2 番目のシャッフルでは使用する必要があります`1.5`ポジションでの期待として`2`、したがって、その平均は`(1.5+3)/2 = 2.25`。 これは、更新が元の配列ではなく現在の期待値に対して行われなければならない理由を示しています。 

2 番目の例を考えてみましょう。```
4 6
10 20 30 40
shuffle 1 4
get 1
get 4
shuffle 2 3
get 2
get 3
```| 操作 | 間隔 | シャッフル前の合計 | 割り当てられた平均値 | 演算後の期待値 |
 | --- | --- | --- | --- | --- |
 |`shuffle 1 4`|`[1,4]`|`100`|`25`|`[25,25,25,25]`|
 |`get 1`|`1`|`25`|`25`|`[25,25,25,25]`|
 |`get 4`|`4`|`25`|`25`|`[25,25,25,25]`|
 |`shuffle 2 3`|`[2,3]`|`50`|`25`|`[25,25,25,25]`|
 |`get 2`|`2`|`25`|`25`|`[25,25,25,25]`|
 |`get 3`|`3`|`25`|`25`|`[25,25,25,25]`|

 最初のシャッフルでは、配列全体に同じ期待値が割り当てられます。 間隔にはすでに均一な期待値があるため、2 番目のシャッフルには目に見える効果はありません。 これは、遅延割り当てがツリーの大部分を繰り返しカバーする場合を例にしています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |`O((n+q) log n)`| 木を建てるのにかかる費用`O(n)`、シャッフルごとに 1 つの範囲合計と 1 つの範囲割り当てが実行されます。`O(log n)`。 毎`get`のポイントクエリです`O(log n)`。 |
 | スペース |`O(n)`| セグメント ツリーは各ツリー ノードの一定量の情報を格納するため、そのサイズは線形です。`n`。 |

 と`n`そして`q`せいぜい両方`250000`の場合、アルゴリズムはクエリごとに対数的に多くのセグメント ツリー操作のみを実行します。 ツリーの高さは約 18 なので、訪問したノードの総数は管理可能なままです。`O(nq)`ブルートフォースシミュレーション。 

## テストケース```python
import sys
import io

def solve():
    input = sys.stdin.readline

    n, q = map(int, input().split())
    a = list(map(float, input().split()))

    size = 4 * n + 5
    tree = [0.0] * size
    lazy = [None] * size

    def build(node, left, right):
        if left == right:
            tree[node] = a[left]
            return

        mid = (left + right) >> 1
        lc = node << 1
        rc = lc | 1
        build(lc, left, mid)
        build(rc, mid + 1, right)
        tree[node] = tree[lc] + tree[rc]

    def apply(node, left, right, value):
        tree[node] = value * (right - left + 1)
        lazy[node] = value

    def push(node, left, right):
        value = lazy[node]
        if value is None or left == right:
            return

        mid = (left + right) >> 1
        lc = node << 1
        rc = lc | 1

        apply(lc, left, mid, value)
        apply(rc, mid + 1, right, value)
        lazy[node] = None

    def range_sum(node, left, right, ql, qr):
        if ql <= left and right <= qr:
            return tree[node]

        push(node, left, right)
        mid = (left + right) >> 1
        result = 0.0

        if ql <= mid:
            result += range_sum(node << 1, left, mid, ql, qr)

        if qr > mid:
            result += range_sum(node << 1 | 1, mid + 1, right, ql, qr)

        return result

    def range_assign(node, left, right, ql, qr, value):
        if ql <= left and right <= qr:
            apply(node, left, right, value)
            return

        push(node, left, right)
        mid = (left + right) >> 1

        if ql <= mid:
            range_assign(node << 1, left, mid, ql, qr, value)

        if qr > mid:
            range_assign(node << 1 | 1, mid + 1, right, ql, qr, value)

        tree[node] = tree[node << 1] + tree[node << 1 | 1]

    def point_query(node, left, right, pos):
        if left == right:
            return tree[node]

        push(node, left, right)
        mid = (left + right) >> 1

        if pos <= mid:
            return point_query(node << 1, left, mid, pos)

        return point_query(node << 1 | 1, mid + 1, right, pos)

    build(1, 0, n - 1)

    output = []

    for _ in range(q):
        parts = input().split()

        if parts[0] == "shuffle":
            l = int(parts[1]) - 1
            r = int(parts[2]) - 1
            total = range_sum(1, 0, n - 1, l, r)
            average = total / (r - l + 1)
            range_assign(1, 0, n - 1, l, r, average)
        else:
            pos = int(parts[1]) - 1
            output.append(f"{point_query(1, 0, n - 1, pos):.12f}")

    sys.stdout.write("\n".join(output))

def run(inp: str) -> str:
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

def close_enough(actual: str, expected: str) -> bool:
    a = [float(x) for x in actual.split()]
    b = [float(x) for x in expected.split()]

    if len(a) != len(b):
        return False

    return all(abs(x - y) <= 1e-9 * max(1.0, abs(y)) for x, y in zip(a, b))

sample1 = """\
3 6
1 2 3
get 1
get 3
shuffle 1 2
shuffle 2 3
get 1
get 3
"""

assert close_enough(
    run(sample1),
    """\
1.000000000000
3.000000000000
1.500000000000
2.250000000000
"""
), "provided sample"

minimum = """\
1 3
7
get 1
shuffle 1 1
get 1
"""

assert close_enough(
    run(minimum),
    """\
7.000000000000
7.000000000000
"""
), "minimum size"

all_equal = """\
5 7
8 8 8 8 8
shuffle 1 5
shuffle 2 4
get 1
get 2
get 5
"""

assert close_enough(
    run(all_equal),
    """\
8.000000000000
8.000000000000
8.000000000000
"""
), "all equal values"

boundaries = """\
4 7
1 2 9 10
shuffle 1 2
get 1
get 2
shuffle 3 4
get 3
get 4
get 2
"""

assert close_enough(
    run(boundaries),
    """\
1.500000000000
1.500000000000
9.500000000000
9.500000000000
1.500000000000
"""
), "boundary intervals"

maximum_n = 250000
maximum_case = (
    f"{maximum_n} 3\n"
    + " ".join(["1000000"] * maximum_n)
    + "\n"
    + "shuffle 1 250000\n"
    + "get 125000\n"
    + "get 250000\n"
)

assert close_enough(
    run(maximum_case),
    """\
1000000.000000000000
1000000.000000000000
"""
), "maximum n"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`3 6`提供された操作を使用して |`1`、`3`、`1.5`、`2.25`| 重複するシャッフル間での正しい伝播 |
 |`1 3`価値のあるもの`7`|`7`、`7`| 単一位置の間隔と可能な最小の配列 |
 | 配列全体と部分配列をシャッフルした 5 つの等しい値 | 3 つの答えは以下に等しい`8`| 均一な期待と繰り返される怠惰な割り当て |
 | シャッフルされた 4 つの値`[1,2]`そして`[3,4]`|`1.5`、`1.5`、`9.5`、`9.5`、`1.5`| 包含境界と素区間 |
 |`n = 250000`、すべての値`1000000`| 2 つの答えは以下に等しい`1000000`| 最大配列サイズと広い範囲の割り当て |

 ## 特殊なケース

 単一位置シャッフルの場合、セグメント ツリーは区間の合計を計算し、次の区間の長さで除算します。`1`。 と```
1 3
7
get 1
shuffle 1 1
get 1
```合計は`7`、平均は`7 / 1 = 7`、範囲の割り当てでは値は変更されません。 2 つのクエリは両方とも返されます`7.000000000000`。 

完全配列シャッフルの場合、すべての元の要素がすべての位置に到達する可能性が等しいため、すべての位置が同じ期待値を受け取ります。 のために```
3 2
1 2 6
shuffle 1 3
get 2
```ルートノードにはすでに完全な合計が保存されています`9`。 シャッフルは計算します`9 / 3 = 3`そして割り当てます`3`ゆっくり根元まで。 次に、ポイント クエリはその値をパスにプッシュして返します。`3.000000000000`。 

包括的な境界については、次の点を考慮してください。```
3 2
1 2 9
shuffle 1 2
get 2
```要求された間隔には合計があります`3`そして長さ`2`、したがって、その新しい期待値は次のようになります。`1.5`。 割り当ては正確にポジションをカバーします`1`そして`2`。 位置`3`残っている`9`、 そして`get 2`返品`1.500000000000`。 

オーバーラップするシャッフルの場合は、提供されたシーケンスを考慮してください。```
3 4
1 2 3
shuffle 1 2
shuffle 2 3
get 1
get 3
```最初の操作後の位置`1`そして`2`どちらも期待を持っています`1.5`。 2 番目の操作は、現在の位置の合計をクエリします。`2`そして`3`、つまり`1.5 + 3 = 4.5`、 ない`2 + 3 = 5`。 その新しい平均は`2.25`。 最終的な期待値は、`[1.5, 2.25, 2.25]`、したがって、答えは次のとおりです`1.500000000000`そして`2.250000000000`。 

すでに均一な間隔を繰り返し割り当てる場合は、次のことを考慮してください。```
4 3
5 5 5 5
shuffle 1 4
shuffle 2 3
get 2
```最初のシャッフルで割り当てます`5`あらゆるポジションに。 2 番目のシャッフルでは、次の間隔の平均が計算されます。`(5+5)/2 = 5`なので状態は変わりません。 遅延セグメント ツリーは、最初の配列全体の割り当てを個々の要素に拡張することなく、両方の操作を処理します。 

許容される最大の配列の場合、個々の変更を具体化する必要がなく、すべてのノードに同じ期待値を割り当てることができます。 と`250000`を含むすべての位置`1000000`、フルレンジシャッフルでも平均`1000000`。 後のポイント クエリは 1 つのツリー パスのみをたどるため、配列のサイズによってクエリが線形スキャンになることはありません。
