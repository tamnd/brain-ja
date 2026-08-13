---
title: "CF 102419I - 別のクエリの問題"
description: "長さ (n) の整数配列 (A) を維持し、最初はゼロで埋められます。 タイプ 2 の演算は、1 つの連続した間隔に等差数列を追加します。 演算 ((l,r,a,b)) の場合、位置 (i) は [ a+b(i-l) を受け取ります。"
date: "2026-08-12T20:25:48+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102419
codeforces_index: "I"
codeforces_contest_name: "SPC 2019"
rating: 0
weight: 102419
solve_time_s: 474
verified: true
draft: false
---

[CF 102419I - 別のクエリの問題](https://codeforces.com/problemset/problem/102419/I)

 **評価:** -
 **タグ:** -
 **解決時間:** 7 分 54 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 長さ (n) の整数配列 (A) を維持し、最初はゼロで埋められます。 タイプ 2 の演算は、1 つの連続した間隔に等差数列を追加します。 演算 ((l,r,a,b)) の場合、位置 (i) は次の値を受け取ります。 

[
 a+b(i-l)。 
]

 タイプ 1 の演算は、(A_l,A_{l+1},\ldots,A_r) のすべての値が同一であるかどうかを尋ねます。 必要な出力は、間隔が一定の場合は (1)、それ以外の場合は (0) です。 

境界 (n,q\le 2\cdot10^5) により、クエリごとに間隔全体をスキャンすることが除外されます。 最悪の場合、長さ (2\cdot10^5) の間隔で (2\cdot10^5) 回の演算が行われる可能性があり、これにより、およそ (4\cdot10^{10}) 回の配列アクセスが行われます。 各操作には (O(\log n)) 程度、または少なくとも線形時間より大幅に短い時間が必要です。 

厄介な点は、等差数列の更新が間隔のすべての要素を個別に変更しているように見えることです。 実際の値を保存し、間隔全体を変更することは、まさにブルート フォース ソリューションの速度を遅らせる原因となります。 

便利なエッジ ケースは、1 要素のクエリです。 例えば、```
1 1
1 1 1
```出力があります```
1
```なぜなら、単一の要素は常にそれ自体と等しいからです。 間隔内の差異を検索するソリューションでは、これを誤って非定数範囲として扱う可能性があります。 

別のエッジケースは、進行が (b=0) の場合に発生します。 例えば、```
3 2
2 1 3 5 0
1 1 3
```生成する```
1
```更新によりすべての位置に (5) が追加され、([5,5,5]) が与えられるためです。 不注意な解決策では、タイプ 2 のすべての演算が等差数列として記述されるため、必ず異なる値が作成されると想定する可能性があります。 

間隔の境界も重要です。 考慮する```
4 3
2 2 4 1 1
1 2 4
1 1 4
```更新後の配列は ([0,1,2,3]) となるため、出力は次のようになります。```
0
0
```最初のクエリは位置 2 ～ 4 のみをチェックします。2 番目のクエリには、変更されていない位置 1 が含まれます。 正しい境界を忘れたり、等価性をチェックするときに (l+1) の代わりに (l) を使用したりする差分配列の実装では、間違った差分が暗黙的に含まれる可能性があります。 

ネガティブな更新も間違いの原因です。 例えば、```
3 3
2 1 3 5 -2
1 1 3
2 2 2 -1 0
1 1 3
```与える```
0
0
```最初の更新では ([5,3,1]) が作成され、2 番目の更新では位置 2 が (2) に変更され、([5,2,1]) が与えられます。 データ構造は、値が増加するか減少するかだけでなく、符号付きの値も処理できる必要があります。 

## アプローチ

 直接的な解決策は、(A) 自体を保存することです。 タイプ 2 の演算の場合、(l) から (r) までを繰り返し、数列の対応する項を追加します。 タイプ 1 クエリの場合、間隔をスキャンし、すべての値を最初の値と比較します。 これは、問題で説明されている操作を明示的に実行し、一定間隔の定義を直接チェックするため、正しいです。 

問題は最悪のケースだ。 1 回の更新で (O(n)) 個の要素にアクセスでき、1 回のクエリで (O(n)) 個の要素を検査できます。 (2\cdot10^5) の演算では、制限時間をはるかに超える、約 (4\cdot10^{10}) 個の基本配列演算が必要になる可能性があります。 

重要な観察は、値自体を見るのをやめて、代わりにそれらの隣接する差分に注目することです。 定義する

 [
 D_i=A_i-A_{i-1}、
 ]

 (A_0=0) となります。 間隔 (A_l,\ldots,A_r) は次の場合に正確に一定になります。 

[
 D_{l+1}=D_{l+2}=\cdots=D_r=0。 
]

 したがって、潜在的に長い等価クエリは、差の範囲にゼロ以外のものが含まれているかどうかを尋ねるクエリになります。 

ここで、等差数列更新が (D) に対して何を行うかを考えてみましょう。 しましょう

 [
 x=a+b(r-l)
 ]

 間隔に追加される最後の値になります。 (l) の位置では、差は (a) だけ変化します。 (l+1) と (r) の間では、隣接するすべての差分が (b) だけ変化します。 (r+1) では、差は (-x) だけ変化します。 したがって、アップデート全体は次のようになります

 [
 D_l\マスレル{+}=a、
 ]

 [
 D_{l+1},\ldots,D_r\mathrel{+}=b,
 ]

 [
 D_{r+1}\マスレル{-}=x。 
]

 長い等差数列は、1 つの範囲の加算と 2 つの点の変更に短縮されました。 

(D) の範囲内のすべての値がゼロであるかどうかを判断する必要があります。 これを行う便利な方法は、差の二乗和を維持することです。 すべての (D_i^2) は非負であるため、

 [
 \sum D_i^2=0
 ]

 すべての (D_i) がゼロの場合に正確に成立します。 

遅延セグメント ツリーは両方を維持できます。 

[
 S=\sum D_i
 ]

 そして

 [
 Q=\sum D_i^2
 ]

 セグメントごとに。 長さ (k) のセグメント内のすべての値に (x) を追加すると、次のようになります。 

[
 S' = S+kx
 ]

 そして

 [
 Q' = Q+2xS+kx^2。 
]

 これにより、訪問されたセグメントごとに一定時間の遅延伝播が行われます。 各等差数列更新には (O(\log n)) 個のセグメント ツリー操作が必要で、各等価クエリには 1 つの (O(\log n)) 個の範囲クエリが必要です。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | (O(nq)) | (O(n)) | 遅すぎる |
 | 差分配列 + 遅延セグメント ツリー | (O(q\log n)) | (O(n)) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 現在の配列を差分配列 (D) で表します。ここで (D_i=A_i-A_{i-1})。 すべての (A_i) がゼロであるため、最初はすべての (D_i) がゼロになります。 
2. (D) 上に遅延セグメント ツリーを構築します。 各ノードは、その差の合計、それらの二乗の合計、およびセグメント全体への保留中の追加を表す遅延値を格納します。 合計は平方和を更新するときに必要になります。 
3. タイプ 2 更新 ((l,r,a,b)) の場合、最後に追加された値を計算します。 

[
 x=a+b(r-l)。 
]

 (a) を (D_l) に加算し、(l+1\le i\le r) のすべての (D_i) に (b) を加算し、(r<n) の場合は (D_{r+1}) から (x) を減算します。 

これは、等差数列を (A_l,\ldots,A_r) に追加した結果を正確に表しています。 特殊なケース (r=n) では、維持される配列内に (D_{n+1}) が存在しないため、最終的な修正は単純に省略されます。 

1. タイプ 1 クエリ ((l,r)) の場合、(D_{l+1},\ldots,D_r) に対してセグメント ツリーをクエリします。 (l=r) の場合、範囲には差分が含まれていないため、答えは直ちに (1) になります。 
2. それ以外の場合は、返された平方和を検査します。 それがゼロの場合、間隔内のすべての差はゼロであるため、(A) の対応するすべての値は等しくなります。 正の場合、少なくとも 1 つの隣接ペアが異なるため、間隔は一定ではありません。 

### なぜ効果があるのか

不変条件は、セグメント ツリーが常に正確な現在の差分配列 (D) を表すことです。 等差数列の更新は、左側の境界でのみ、その内部全体にわたって均一に、そして右側の境界の直後の位置でのみ変更されるため (D)、対応する 3 つのセグメント ツリーの更新はその不変条件を保持します。 クエリされた任意の間隔について、隣接するすべての差 (D_{l+1},\ldots,D_r) が 0 である場合に限り、(A_l,\ldots,A_r) はすべて等しくなります。 セグメント ツリーはそれらの二乗の合計を格納し、二乗は負ではないため、定数の場合、その合計は正確にゼロになります。 したがって、すべてのクエリの答えは正しいです。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

class SegmentTree:
    def __init__(self, n):
        self.n = n
        size = 4 * n + 5
        self.s = [0] * size
        self.sq = [0] * size
        self.lazy = [0] * size

    def _apply(self, node, left, right, x):
        length = right - left + 1
        old_sum = self.s[node]

        self.sq[node] += 2 * x * old_sum + length * x * x
        self.s[node] = old_sum + length * x
        self.lazy[node] += x

    def _push(self, node, left, right):
        x = self.lazy[node]
        if x == 0 or left == right:
            return

        mid = (left + right) >> 1
        self._apply(node << 1, left, mid, x)
        self._apply(node << 1 | 1, mid + 1, right, x)
        self.lazy[node] = 0

    def add(self, ql, qr, x):
        if ql > qr:
            return
        self._add(1, 1, self.n, ql, qr, x)

    def _add(self, node, left, right, ql, qr, x):
        if ql <= left and right <= qr:
            self._apply(node, left, right, x)
            return

        self._push(node, left, right)
        mid = (left + right) >> 1

        if ql <= mid:
            self._add(node << 1, left, mid, ql, qr, x)
        if qr > mid:
            self._add(node << 1 | 1, mid + 1, right, ql, qr, x)

        lc = node << 1
        rc = lc | 1
        self.s[node] = self.s[lc] + self.s[rc]
        self.sq[node] = self.sq[lc] + self.sq[rc]

    def query_sq(self, ql, qr):
        if ql > qr:
            return 0
        return self._query_sq(1, 1, self.n, ql, qr)

    def _query_sq(self, node, left, right, ql, qr):
        if ql <= left and right <= qr:
            return self.sq[node]

        self._push(node, left, right)
        mid = (left + right) >> 1
        result = 0

        if ql <= mid:
            result += self._query_sq(node << 1, left, mid, ql, qr)
        if qr > mid:
            result += self._query_sq(node << 1 | 1, mid + 1, right, ql, qr)

        return result

def solve():
    n, q = map(int, input().split())
    seg = SegmentTree(n)
    out = []

    for _ in range(q):
        query = list(map(int, input().split()))
        typ = query[0]

        if typ == 1:
            l, r = query[1], query[2]

            if l == r:
                out.append("1")
                continue

            # A[l..r] is constant iff
            # D[l+1], ..., D[r] are all zero.
            value = seg.query_sq(l + 1, r)
            out.append("1" if value == 0 else "0")

        else:
            l, r, a, b = query[1:]

            # D[l] += a
            seg.add(l, l, a)

            # D[l+1..r] += b
            if l + 1 <= r:
                seg.add(l + 1, r, b)

            # The value added at position r is the final term.
            last = a + b * (r - l)

            # D[r+1] -= last
            if r < n:
                seg.add(r + 1, r + 1, -last)

    sys.stdout.write("\n".join(out))

if __name__ == "__main__":
    solve()
```元の配列はすべてゼロで構成されているため、セグメント ツリーはゼロで初期化され、初期の差分はすべてゼロになります。 別途ビルド手順を行う必要はありません。 

の`_apply`関数は中心的な遅延伝播操作です。 ノードが値 (D_1,\ldots,D_k) を現在の合計 (S) と二乗和 (Q) で表すとします。 すべての値に (x) を加算した後の新しい二乗和は次のようになります。 

[
 \sum(D_i+x)^2
 =\sum D_i^2+2x\sum D_i+kx^2。 
]

 数式では (S) の古い値が必要であるため、コードは古い合計を変更する前に保存します。 

アップデートの場合は、`seg.add(l, l, a)`差分配列の左境界を処理します。 範囲のアップデート`seg.add(l + 1, r, b)`すべての内部差異の共通増分を処理します。 ついに、`seg.add(r + 1, r + 1, -last)`これは、位置 (r) の後で進行が停止するという事実を説明します。 

の`r < n`状態は必須です。 元の配列は (n) で終わるため、維持される差分 (D_{n+1}) はありません。 このチェックを忘れると、無効なセグメント ツリー インデックスが作成されます。 

タイプ 1 クエリの場合、関連する差異は (l) ではなく (l+1) から始まります。 (D_l=A_l-A_{l-1}) は、(A_l) がクエリされた間隔の前の要素とどのように異なるかを示します。これは、クエリされた間隔自体が定数であるかどうかとは関係がありません。 

Python の整数には任意の精度があるため、潜在的に大きな二乗値がオーバーフローすることはありません。 多くの操作を行うと、最大の配列値が元の (10^8) 更新パラメーターよりも大幅に大きくなる可能性があり、固定幅の 32 ビット表現を使用する言語では安全でなくなります。 

## 実用的な例

 ### サンプル 1

 入力は```
5 3
2 1 3 4 1
1 1 3
1 4 5
```アップデート後は、(A=[4,9,14,0,0])となります。 その差分配列は

 [
 D=[4,5,5,-14,0]。 
]

 トレースは次のとおりです。 

| 操作 | (A) 概念的 | 該当する(D)範囲 | 平方和 | 答え |
 | --- | --- | --- | --- | --- |
 | イニシャル | ([0,0,0,0,0]) | すべてゼロ | 0 | |
 |`2 1 3 4 1`| ([4,9,14,0,0]) | (D_2,D_3=5,5) | 50 | |
 |`1 1 3`| 変更なし | (D_2,D_3=5,5) | 50 | 0 |
 |`1 4 5`| 変更なし | (D_5=0) | 0 | 1 |

 最初のクエリでは、位置 1 ～ 3 内にゼロ以外の違いがあるため、これらの値がすべて等しいことはありません。 2 番目のクエリには 2 つのゼロ値要素が含まれているため、関連する唯一の差異はゼロです。 

### 構築サンプル 2

 検討してください```
4 5
1 1 4
2 1 4 2 0
1 2 3
2 2 3 -1 2
1 1 4
```トレースは次のとおりです。 

| 操作 | (A) 概念的 | 該当する(D)範囲 | 平方和 | 答え |
 | --- | --- | --- | --- | --- |
 | イニシャル | ([0,0,0,0]) | すべてゼロ | 0 | |
 |`1 1 4`| ([0,0,0,0]) | (D_2,D_3,D_4=0,0,0) | 0 | 1 |
 |`2 1 4 2 0`| ([2,2,2,2]) | (D_2,D_3,D_4=0,0,0) | 0 | |
 |`1 2 3`| 変更なし | (D_3=0) | 0 | 1 |
 |`2 2 3 -1 2`| ([2,1,3,2]) | (D_2,D_3,D_4=-1,2,-1) | 6 | |
 |`1 1 4`| 変更なし | (D_2,D_3,D_4=-1,2,-1) | 6 | 0 |

 最初の更新には (b=0) が含まれているため、値は変更されますが、すべての内部差分はゼロのままになります。 2 番目の更新ではゼロ以外の差分が導入され、二乗和によって即座にそれらの差分が検出されます。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O(q\log n)) | 各更新では最大 3 つの範囲の追加が実行され、各クエリでは 1 つのセグメント ツリー クエリが実行されます。 |
 | スペース | (O(n)) | セグメント ツリーには、ノードごとに一定量の情報が格納されます。 |

 (n,q\le2\cdot10^5) を使用すると、ソリューションは、間隔内のすべての配列位置を操作するのではなく、クエリごとに対数的に多くのツリー操作のみを実行します。 セグメント ツリーは (O(n)) 個のメモリを使用しますが、512 MB の制限内で快適に使用できます。 

## テストケース```python
import sys
import io

class SegmentTree:
    def __init__(self, n):
        size = 4 * n + 5
        self.n = n
        self.s = [0] * size
        self.sq = [0] * size
        self.lazy = [0] * size

    def apply(self, node, left, right, x):
        length = right - left + 1
        old_sum = self.s[node]
        self.sq[node] += 2 * x * old_sum + length * x * x
        self.s[node] = old_sum + length * x
        self.lazy[node] += x

    def push(self, node, left, right):
        x = self.lazy[node]
        if x == 0 or left == right:
            return
        mid = (left + right) // 2
        self.apply(node * 2, left, mid, x)
        self.apply(node * 2 + 1, mid + 1, right, x)
        self.lazy[node] = 0

    def add(self, ql, qr, x):
        if ql > qr:
            return
        self._add(1, 1, self.n, ql, qr, x)

    def _add(self, node, left, right, ql, qr, x):
        if ql <= left and right <= qr:
            self.apply(node, left, right, x)
            return

        self.push(node, left, right)
        mid = (left + right) // 2

        if ql <= mid:
            self._add(node * 2, left, mid, ql, qr, x)
        if qr > mid:
            self._add(node * 2 + 1, mid + 1, right, ql, qr, x)

        self.s[node] = self.s[node * 2] + self.s[node * 2 + 1]
        self.sq[node] = self.sq[node * 2] + self.sq[node * 2 + 1]

    def query_sq(self, ql, qr):
        if ql > qr:
            return 0
        return self._query_sq(1, 1, self.n, ql, qr)

    def _query_sq(self, node, left, right, ql, qr):
        if ql <= left and right <= qr:
            return self.sq[node]

        self.push(node, left, right)
        mid = (left + right) // 2
        ans = 0

        if ql <= mid:
            ans += self._query_sq(node * 2, left, mid, ql, qr)
        if qr > mid:
            ans += self._query_sq(node * 2 + 1, mid + 1, right, ql, qr)

        return ans

def solve(data):
    inp = io.StringIO(data)
    n, q = map(int, inp.readline().split())
    seg = SegmentTree(n)
    ans = []

    for _ in range(q):
        v = list(map(int, inp.readline().split()))

        if v[0] == 1:
            l, r = v[1], v[2]
            if l == r:
                ans.append("1")
            else:
                ans.append("1" if seg.query_sq(l + 1, r) == 0 else "0")
        else:
            _, l, r, a, b = v

            seg.add(l, l, a)

            if l + 1 <= r:
                seg.add(l + 1, r, b)

            last = a + b * (r - l)

            if r < n:
                seg.add(r + 1, r + 1, -last)

    return "\n".join(ans)

# Provided sample.
assert solve(
    """5 3
2 1 3 4 1
1 1 3
1 4 5
"""
) == "0\n1", "sample 1"

# Minimum-size input. A one-element interval is always constant.
assert solve(
    """1 4
1 1 1
2 1 1 100000000 100000000
1 1 1
1 1 1
"""
) == "1\n1\n1", "minimum-size case"

# All values remain equal after a constant update.
assert solve(
    """5 4
2 1 5 7 0
1 1 5
2 2 4 -7 0
1 2 4
"""
) == "1\n1", "all-equal values"

# Boundary-sensitive case. The update starts at 2 and ends at 4.
assert solve(
    """4 4
2 2 4 1 1
1 2 4
1 1 4
1 3 4
"""
) == "0\n0\n0", "boundary conditions"

# Cancellation and negative values.
assert solve(
    """4 6
2 1 4 5 -2
1 1 4
2 2 3 -3 0
1 2 3
2 2 3 1 0
1 1 4
"""
) == "0\n1\n0", "negative updates and cancellation"

# Large input size. The update makes the whole array equal,
# then the full-range query must still be answered efficiently.
n = 200000
large_input = f"{n} 2\n2 1 {n} 12345678 0\n1 1 {n}\n"
assert solve(large_input) == "1", "maximum-size case"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`1 4 ...`|`1 1 1`| 単一要素の間隔と (n=1) |
 | 定数加算 |`1 1`| (b=0) と等価性の維持 |
 | 2 から 4 までの更新 |`0 0 0`| 左右の境界と未加工の要素 |
 | ネガティブなアップデート |`0 1 0`| 符号付き値とキャンセル |
 | (n=200000) |`1`| 最大サイズの入力と対数処理 |

 ## 特殊なケース

 単一要素間隔の場合、次のようになります。```
1 2
1 1 1
```クエリには比較する隣接ペアがありません。 実装は、(1) を返すことでこれを明示的に処理します。 差分配列の用語では、必要な範囲 (D_{l+1},\ldots,D_r) が空です。 

継続的に進歩するには、次のことを考慮してください。```
3 2
2 1 3 5 0
1 1 3
```更新により ([5,5,5]) が追加されるため、(A=[5,5,5]) になります。 差分配列では (D_1=5)、(D_2=D_3=0) になります。 クエリは (D_2,D_3) のみをチェックし、ゼロに等しい二乗和を取得し、(1) を返します。 

最後の位置に触れる更新については、次のことを考慮してください。```
3 2
2 2 3 4 2
1 2 3
```新しい配列は ([0,4,6]) です。 更新により (D_2) は (4) によって変更され、(D_3) は (2) によって変更されます。 維持される構造には (D_4) が存在しないため、右境界の修正はスキップされます。 クエリは、二乗が正である (D_3=2) を調べ、(0) を返します。 

位置 1 から始まる範囲については、次のことを考慮してください。```
3 2
2 1 3 7 3
1 1 3
```配列は ([7,10,13]) となり、差分 (D_2=3,D_3=3) になります。 クエリは、差分配列の位置 (2) から (3) を正しく検査します。 (A_0) はクエリされた間隔外にあるため、(D_1=A_1-A_0=7) は検査されません。 

負の値の場合は、次のように考慮します。```
3 2
2 1 3 5 -2
1 1 3
```配列は ([5,3,1]) になるため、関連する違いは (D_2=-2) と (D_3=-2) になります。 それらの二乗の合計は (8) となり、正となり、答えは (0) になります。 二乗和法は差が正であるか負であるかには依存しません。これはまさに任意の署名付き更新に必要なものです。
