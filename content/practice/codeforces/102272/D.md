---
title: "CF 102272D - C\u00e1nh \u0110\u1ed3ng ホア"
description: "一連の (N) 個の花畑があります。 プロット (i) には最初に (Ai) の花が含まれており、操作は順番に処理されます。 更新操作では間隔 ([l,r]) が選択されます。 その間隔内の位置 (i) に、正確に (i-l+1) 個の花が追加されます。"
date: "2026-08-17T11:10:49+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102272
codeforces_index: "D"
codeforces_contest_name: "HCW 19 Individual Day 1"
rating: 0
weight: 102272
solve_time_s: 218
verified: false
draft: false
---

[CF 102272D - C\u00e1nh \u0110\u1ed3ng ホア](https://codeforces.com/problemset/problem/102272/D)

 **評価:** -
 **タグ:** -
 **解決時間:** 3 分 38 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 一連の (N) 個の花畑があります。 プロット (i) には最初に (A_i) 個の花が含まれており、操作は順番に処理されます。 

更新操作では間隔 ([l,r]) が選択されます。 その間隔内の位置 (i) に、正確に (i-l+1) 個の花が追加されます。 したがって、加算された値は等差数列を形成します。 

[
 1、2、3、r-l+1。 
】

 クエリ操作では ([u,v]) を選択し、その間隔における現在の花の合計を尋ねます。 

難しいのは、アップデートが継続的に追加されるわけではないということです。 たとえば、([3,6]) を更新すると (1,2,3,4) が追加されるため、追加される量は位置に線形に依存します。 

[
 i-l+1=i+(1-l)。 
】

 その直線的なフォルムが解決の鍵となります。 

1 つのテスト ケースには最大 (10^5) 個のプロットと (10^5) 個の演算があり、最大 4 つのテスト ケースがあります。 (O(N)) 操作は、最悪の場合、すでに (10^{10}) 個の作業につながり、2 秒の制限が許容するものをはるかに超えています。 各更新とクエリには (O(\log N)) 程度の処理が必要で、テスト ケースごとにおよそ (10^5\log N) 回の操作が行われます。 

最初の境界ケースは 1 要素の更新です。 例えば、```
1
1
5
2
1 1 1
2 1 1
```アップデートで花が1つ追加されるので、答えは```
6
```進行をゼロから始まるものとして扱う不注意な実装では、(5) が生成されます。 

2 番目の境界ケースは、ちょうど (N) で終了する更新です。 例えば、```
1
5
0 0 0 0 0
2
1 2 5
2 1 5
```更新により (0,1,2,3,4) が位置 (1,2,3,4,5) にそれぞれ追加されるため、答えは次のようになります。```
10
```3 番目のケースは、左端が (1) ではない更新です。 のために```
1
5
0 0 0 0 0
2
1 3 5
2 1 5
```加算は (0,0,1,2,3) となり、```
6
```式では実際の左端点 (l) を使用する必要があります。 これを (i) に置き換えたり、すべての進行が位置 (1) から始まると仮定したりすると、間違った結果が得られます。 

4 番目のケースは、更新が重複している場合です。 のために```
1
4
0 0 0 0
3
1 1 3
1 2 4
2 1 4
```最初の更新では ([1,2,3,0]) が得られ、2 番目の更新では ([1,3,5,3]) が得られ、最終的な答えは (12) になります。 更新は追加であるため、以前の操作の効果を上書きすることはできません。 

## アプローチ

 直接的なアプローチでは、実際の配列を保存します。 更新 ([l,r]) の場合は、(l) から (r) までループし、すべての位置に (i-l+1) を追加します。 クエリ ([u,v]) の場合、間隔をループしてその合計を計算します。 すべての操作は、記述されている位置に正確に適用されるため、これは正しいです。 

問題は仕事量です。 更新はすべて (N) の位置にアクセスでき、クエリはすべて (N) の位置を検査することもできます。 (Q=10^5) 演算と (N=10^5) を使用すると、一連の全範囲演算には約

 [
 NQ=10^{10}
 】

 配列アクセス。 これは数桁大きすぎます。 

すべての花の数を明示的に実体化するため、ブルート フォースが機能します。 更新に破棄される構造があるため、失敗します。 

より高速な解決策を明らかにする観察は、更新ごとに位置の一次関数が追加されるということです。 ([l,r]) では、

 [
 i-l+1 = 1\cdot i +(1-l)。 
】

 したがって、更新を (r-l+1) 個の個別の追加として考える代わりに、同じ線形関数 (ai+b) をセグメント全体に追加すると考えることができます。 

遅延伝播を備えたセグメント ツリーは自然に適合します。 ([L,R]) を表すすべてのツリー ノードについて、そのセグメント内の合計の花数を保存します。 その遅延タグには 2 つの数値 (a、b) が格納されます。これは、このノード内のすべての位置 (i) がまだ必要であることを意味します。 

[
 ai+b
 】

 それに追加されました。 

ノードが ([L,R]) をカバーしているとします。 このような遅延アップデートの合計貢献度は、

 a\sum_{i=L}^{R}i+b(R-L+1)。 
】

 インデックス合計は閉じた形式になります

 \frac{(L+R)(R-L+1)}2.
 】

 したがって、セグメントに到達すると、セグメント全体を (O(1)) で更新できます。 遅延伝播は、実際に検査する必要があるまで、線形関数をその子にプッシュすることを延期します。 

元の操作では、単純に次のように使用します。 

[
 a=1、\qquad b=1-l。 
】

 同じセグメント ツリーは (O(\log N)) で範囲の合計を返すことができます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | (O(NQ)) | (O(N)) | 遅すぎる |
 | 最適 | (O((N+Q)\log N)) | (O(N)) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 初期配列にセグメント ツリーを構築します。 各ノードは、その間隔内の花の合計を格納します。 最初は遅延更新が存在しないため、遅延情報はゼロから始まります。 
2. 更新 ([l,r]) を一次関数として表現します。 

[
 f(i)=i-l+1=i+(1-l)。 
】

 したがって、その傾きは (a=1)、切片は (b=1-l) となります。 

1. ツリー ノード ([L,R]) が更新によって完全にカバーされると、保存されている合計を次のように増やします。 

[
 a\frac{(L+R)(R-L+1)}2+b(R-L+1)。 
】

 同時にノードのlazyタグに(a)と(b)を追加します。 区間全体が同じ線形関数を受け取っているため、その子にはアクセスしません。 

1. 更新がノードと部分的に交差する場合、まずノードの保留中の線形関数をその子にプッシュします。 次に、2 つの子を再帰的に更新し、それらの合計から現在のノードの合計を再計算します。 

プッシュ操作では、通常の更新とまったく同じ式が使用されます。 子カバー ([L,R]) は保留中の関数 (ai+b) を受け取るため、その合計は対応する等差級数の合計だけ増加します。 

1. 範囲合計クエリ ([u,v]) の場合、互いに素なノードの場合はゼロを返し、完全にカバーされたノードの場合は保存された合計を返します。 部分交差の場合は、子をクエリする前に保留中の遅延関数をプッシュし、その結果を追加します。 
2. すべての (Q) 操作を元の順序で処理します。 タイプ (1) の場合、線形更新を適用します。 タイプ (2) の場合、必要な間隔をクエリし、結果を出力します。 

### なぜ効果があるのか

不変条件は、すべてのセグメント ツリー ノードに格納された合計が、ノードにすでに到達したすべての更新を含む、そのノードの間隔にわたる現在の配列の真の合計に等しいということです。 その遅延ペア ((a,b)) は、そのノードの間隔内のすべての位置に適用する必要があり、ノードに格納されている合計にすでに含まれている線形関数を正確に表します。 

完全な更新がノードに到達すると、閉じた形式の算術合計により、その間隔内のすべての位置に更新の寄与が正確に加算されます。 遅延タグがプッシュされると、同じ関数が両方の子に適用され、その間隔が親間隔を分割します。 したがって、不変式は更新のたびに保存されます。 

クエリは、すでに正しい完全なノードを取得するか、正しい子の合計を再帰的に結合します。 クエリされたすべての位置は、関連する互いに素なツリー ノードに正確に属しているため、返される値は、要求された花の合計とまったく同じになります。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

sys.setrecursionlimit(1_000_000)

class SegmentTree:
    def __init__(self, arr):
        self.n = len(arr)
        size = 4 * self.n + 5
        self.tree = [0] * size
        self.lazy_a = [0] * size
        self.lazy_b = [0] * size
        self.arr = arr
        self._build(1, 1, self.n)

    def _build(self, node, left, right):
        if left == right:
            self.tree[node] = self.arr[left - 1]
            return

        mid = (left + right) // 2
        self._build(node * 2, left, mid)
        self._build(node * 2 + 1, mid + 1, right)
        self.tree[node] = self.tree[node * 2] + self.tree[node * 2 + 1]

    @staticmethod
    def _index_sum(left, right):
        length = right - left + 1
        return (left + right) * length // 2

    def _apply(self, node, left, right, a, b):
        length = right - left + 1
        index_sum = self._index_sum(left, right)

        self.tree[node] += a * index_sum + b * length
        self.lazy_a[node] += a
        self.lazy_b[node] += b

    def _push(self, node, left, right):
        a = self.lazy_a[node]
        b = self.lazy_b[node]

        if a == 0 and b == 0:
            return

        if left != right:
            mid = (left + right) // 2
            self._apply(node * 2, left, mid, a, b)
            self._apply(node * 2 + 1, mid + 1, right, a, b)

        self.lazy_a[node] = 0
        self.lazy_b[node] = 0

    def update(self, ql, qr):
        self._update(1, 1, self.n, ql, qr)

    def _update(self, node, left, right, ql, qr):
        if qr < left or right < ql:
            return

        if ql <= left and right <= qr:
            # Add i - ql + 1 = i + (1 - ql).
            self._apply(node, left, right, 1, 1 - ql)
            return

        self._push(node, left, right)

        mid = (left + right) // 2
        self._update(node * 2, left, mid, ql, qr)
        self._update(node * 2 + 1, mid + 1, right, ql, qr)

        self.tree[node] = self.tree[node * 2] + self.tree[node * 2 + 1]

    def query(self, ql, qr):
        return self._query(1, 1, self.n, ql, qr)

    def _query(self, node, left, right, ql, qr):
        if qr < left or right < ql:
            return 0

        if ql <= left and right <= qr:
            return self.tree[node]

        self._push(node, left, right)

        mid = (left + right) // 2
        return (
            self._query(node * 2, left, mid, ql, qr)
            + self._query(node * 2 + 1, mid + 1, right, ql, qr)
        )

def solve():
    t = int(input())
    output = []

    for _ in range(t):
        n = int(input())
        arr = list(map(int, input().split()))

        q = int(input())
        seg = SegmentTree(arr)

        for _ in range(q):
            typ, x, y = map(int, input().split())

            if typ == 1:
                seg.update(x, y)
            else:
                output.append(str(seg.query(x, y)))

    sys.stdout.write("\n".join(output))

if __name__ == "__main__":
    solve()
```ツリー内の 3 つの主要な配列には、異なる役割があります。`tree[node]`ノードの間隔における現在のフラワー合計を保存します。`lazy_a[node]`そして`lazy_b[node]`関数 (ai+b) の据え置き係数を格納します。 

の`_apply`メソッドは中心的な計算です。 ([L,R]) をカバーするノードの場合、(R-L+1) 個の位置があり、それらのインデックスの合計は ((L+R)(R-L+1)/2) になります。 したがって、(ai+b) を追加すると、ノードの合計が次のように変化します。`a * index_sum + b * length`。 

タイプ (1) の操作の場合、更新は常に傾き (1) と切片 (1-1) です。 右端点 (r) は、どのポジションがカバーされるかを決定するだけです。 関数自体には現れません。 

遅延係数は置換されるのではなく追加されます。 ノードが最初に (2i+3) を受信し、その後 (i-4) を受信した場合、結合された保留中の操作は (3i-1) になります。 これが理由です`_apply`実行します`+=`両方の遅延配列で。 

クエリは、下降する前に保留中の更新をプッシュします。 この手順を行わないと、親がすでに遅延更新を合計に含んでいる場合でも、子には古い値が含まれる可能性があります。 

Python の整数は 64 ビットを超えると自動的に大きくなるため、オーバーフローの問題は発生しません。 最大の答えは十分に大きいため、固定幅の 32 ビット演算は安全ではありません。 

実装内のすべての位置は 1 から始まり、数式に一致します。 これにより、式 (i-l+1) が直接使用できるようになり、すべての更新内での余分な変換が回避されます。 

再帰の深さは (O(\log N)) のみですが、とにかく再帰の制限が引き上げられます。 セグメント ツリーには、メモリ制限内に十分収まる (O(N)) 個のノードが含まれています。 

## 実用的な例

 ### サンプル 1、最初のテスト ケース

 初期配列は ([2,1,3,5,2]) です。 次の表は、各更新後の配列と、クエリが発生するたびの応答を記録します。 

| 操作 | アップデートが追加されました | 演算後の配列 | クエリの回答 |
 | --- | --- | --- | --- |
 |`1 1 3`| ([1,2,3,0,0]) | ([3,3,6,5,2]) | |
 |`2 3 5`| なし | ([3,3,6,5,2]) | (6+5+2=13) |
 |`1 4 5`| ([0,0,0,1,2]) | ([3,3,6,6,4]) | |
 |`1 2 5`| ([0,1,2,3,4]) | ([3,4,8,9,8]) | |
 |`1 1 1`| ([1,0,0,0,0]) | ([4,4,8,9,8]) | |
 |`2 1 4`| なし | ([4,4,8,9,8]) | (4+4+8+9=25) |

 たとえば、アップデート`[2,5]`は(i-1)と表されます。 位置 (2) から (5) をカバーするセグメントでは、その寄与は次のようになります。 

[
 (2-1)+(3-1)+(4-1)+(5-1)=1+2+3+4=10。 
】

 ツリーはこれら 4 つの位置を個別に訪問する必要はありません。 

### サンプル 1、2 番目のテスト ケース

 2 番目の初期配列は ([10,5,2,0,8,6,2]) です。 

| 操作 | アップデートが追加されました | 演算後の配列 | クエリの回答 |
 | --- | --- | --- | --- |
 |`1 2 5`| ([0,1,2,3,4,0,0]) | ([10,6,4,3,12,6,2]) | |
 |`1 1 6`| ([1,2,3,4,5,6,0]) | ([11,8,7,7,17​​,12,2]) | |
 |`2 4 7`| なし | ([11,8,7,7,17​​,12,2]) | (7+17+12+2=38) |
 |`1 1 3`| ([1,2,3,0,0,0,0]) | ([12,10,10,7,17​​,12,2]) | |
 |`1 5 5`| ([0,0,0,0,1,0,0]) | ([12,10,10,7,18,12,2]) | |
 |`1 1 5`| ([1,2,3,4,5,0,0]) | ([13,12,13,11,23,12,2]) | |
 |`2 1 7`| なし | ([13,12,13,11,23,12,2]) | (86) |

 2 番目のトレースは、重複した更新を実行します。 ツリーは、遅延線形関数を加算によって結合します。これはまさに配列演算に必要なものです。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O(N+Q\log N)) | ツリーの構築には (O(N)) かかりますが、すべての更新とクエリは (O(\log N)) のツリー レベルを訪問します。 |
 | スペース | (O(N)) | 3 つのセグメント ツリー配列には、それぞれ (O(N)) 個のエントリが含まれています。 |

 (N,Q\le 10^5) の場合、支配項は約 (10^5\log_2(10^5)) となり、テスト ケースごとに約 (17) 万のツリー レベルになります。 各訪問ノードで複数の算術演算を行ったとしても、これは直接的な解法で必要な (10^{10}) 演算をはるかに下回ります。 メモリ使用量は直線的であり、256 MB 内に快適に収まります。 

## テストケース```python
import sys
import io

sys.setrecursionlimit(1_000_000)

class SegmentTree:
    def __init__(self, arr):
        self.n = len(arr)
        size = 4 * self.n + 5
        self.tree = [0] * size
        self.lazy_a = [0] * size
        self.lazy_b = [0] * size
        self.arr = arr
        self._build(1, 1, self.n)

    def _build(self, node, left, right):
        if left == right:
            self.tree[node] = self.arr[left - 1]
            return

        mid = (left + right) // 2
        self._build(node * 2, left, mid)
        self._build(node * 2 + 1, mid + 1, right)
        self.tree[node] = self.tree[node * 2] + self.tree[node * 2 + 1]

    @staticmethod
    def _index_sum(left, right):
        length = right - left + 1
        return (left + right) * length // 2

    def _apply(self, node, left, right, a, b):
        length = right - left + 1
        index_sum = self._index_sum(left, right)
        self.tree[node] += a * index_sum + b * length
        self.lazy_a[node] += a
        self.lazy_b[node] += b

    def _push(self, node, left, right):
        a = self.lazy_a[node]
        b = self.lazy_b[node]

        if a == 0 and b == 0:
            return

        if left != right:
            mid = (left + right) // 2
            self._apply(node * 2, left, mid, a, b)
            self._apply(node * 2 + 1, mid + 1, right, a, b)

        self.lazy_a[node] = 0
        self.lazy_b[node] = 0

    def update(self, ql, qr):
        self._update(1, 1, self.n, ql, qr)

    def _update(self, node, left, right, ql, qr):
        if qr < left or right < ql:
            return

        if ql <= left and right <= qr:
            self._apply(node, left, right, 1, 1 - ql)
            return

        self._push(node, left, right)

        mid = (left + right) // 2
        self._update(node * 2, left, mid, ql, qr)
        self._update(node * 2 + 1, mid + 1, right, ql, qr)

        self.tree[node] = self.tree[node * 2] + self.tree[node * 2 + 1]

    def query(self, ql, qr):
        return self._query(1, 1, self.n, ql, qr)

    def _query(self, node, left, right, ql, qr):
        if qr < left or right < ql:
            return 0

        if ql <= left and right <= qr:
            return self.tree[node]

        self._push(node, left, right)

        mid = (left + right) // 2
        return (
            self._query(node * 2, left, mid, ql, qr)
            + self._query(node * 2 + 1, mid + 1, right, ql, qr)
        )

def solve():
    input = sys.stdin.readline
    t = int(input())
    ans = []

    for _ in range(t):
        n = int(input())
        arr = list(map(int, input().split()))
        q = int(input())

        seg = SegmentTree(arr)

        for _ in range(q):
            typ, x, y = map(int, input().split())
            if typ == 1:
                seg.update(x, y)
            else:
                ans.append(str(seg.query(x, y)))

    return "\n".join(ans)

def run(inp: str) -> str:
    old_stdin = sys.stdin
    old_stdout = sys.stdout

    try:
        sys.stdin = io.StringIO(inp)
        sys.stdout = io.StringIO()

        result = solve()
        sys.stdout.write(result)

        return sys.stdout.getvalue()
    finally:
        sys.stdin = old_stdin
        sys.stdout = old_stdout

sample = """\
2
5
2 1 3 5 2
6
1 1 3
2 3 5
1 4 5
1 2 5
1 1 1
2 1 4
7
10 5 2 0 8 6 2
7
1 2 5
1 1 6
2 4 7
1 1 3
1 5 5
1 1 5
2 1 7
"""

assert run(sample) == "13\n25\n38\n86", "provided sample"

assert run("""\
1
1
5
2
1 1 1
2 1 1
""") == "6", "minimum size"

assert run("""\
1
5
0 0 0 0 0
3
1 2 5
2 1 5
2 5 5
""") == "10\n4", "right boundary"

assert run("""\
1
5
0 0 0 0 0
4
1 3 5
2 1 5
2 3 5
2 4 4
""") == "6\n6\n2", "left boundary"

assert run("""\
1
4
0 0 0 0
3
1 1 3
1 2 4
2 1 4
""") == "12", "overlapping updates"

# Maximum-size test. Every initial value is equal and the update covers N.
n = 100000
maximum_test = (
    "1\n"
    + str(n) + "\n"
    + ("1 " * n).strip() + "\n"
    + "3\n"
    + f"1 1 {n}\n"
    + f"2 1 {n}\n"
    + f"2 {n} {n}\n"
)

expected_total = n + n * (n + 1) // 2
expected_last = 2

assert run(maximum_test) == f"{expected_total}\n{expected_last}", \
    "maximum size and all equal values"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`N=1`、1 つの更新と 1 つのクエリ |`6`| 最小サイズと数列の最初の項 |
 | ゼロ配列、更新`[2,5]`|`10`、`4`| 正しいエンドポイントと正確な進行値 |
 | ゼロ配列、更新`[3,5]`|`6`、`6`、`2`| ゼロ以外の左端点および部分範囲クエリ |
 | 2 つの重複する更新 |`12`| アップデートの追加構成 |
 | (N=100000)、すべての値は等しい |`5000150000`、`2`| 最大サイズ、大きな金額、全範囲の更新 |

 ## 特殊なケース

 1要素の場合```
1
1
5
2
1 1 1
2 1 1
```(1-l=0) であるため、更新には (a=1) と (b=0) があります。 セグメント ツリーにはルートのみが含まれるため、完全カバーの場合はすぐに (1) を追加します。 その合計は (5) から (6) に変化し、クエリは (6) を返します。 押し付ける子もいないので葉の状態も自然に処理されます。 

最終位置で終了する更新の場合、```
1
5
0 0 0 0 0
2
1 2 5
2 1 5
```遅延関数は (i-1) です。 ルートは部分的にのみカバーされているため、更新はカバーされたノードが見つかるまで下降します。 ポジション (2) ～ (5) に対する寄与度は次のとおりです。 

[
 1+2+3+4=10。 
】

 最後のクエリは (10) を返します。 右端点は区間境界によってのみ処理されるため、(r=N) には特別なケースはありません。 

1 つ以外の左端点の場合、```
1
5
0 0 0 0 0
4
1 3 5
2 1 5
2 3 5
2 4 4
```アップデート機能は

 [
 i-3+1=i-2。 
】

 したがって、位置 (3,4,5) は (1,2,3) を受け取り、合計は (6) になります。 クエリ`[3,5]`(6) を返しますが、`[4,4]`(2) を返します。 切片 (1-l) は、式を実際の開始位置に依存させるものです。 

重複する更新については、```
1
4
0 0 0 0
3
1 1 3
1 2 4
2 1 4
```最初の更新は (i) です`[1,3]`、生産`[1,2,3,0]`。 2 番目は (i-1) です。`[2,4]`、追加の生成`[0,1,2,3]`。 最終的な配列は`[1,3,5,3]`、その合計は (12) です。 ツリー内では、重複する遅延タグが係数ごとに追加されるため、データ構造は両方の操作の結合効果を正確に表します。 

最大サイズのテストでは、算術的安全性もチェックします。 (N=100000) の場合、すべて 1 つの配列の後に更新が行われます。`[1,N]`合計が生成されます

 5000150000。 
】

 これは符号付き 32 ビットの範囲を超えていますが、Python の整数はそれを正確に表します。 したがって、セグメント ツリーは特別なオーバーフロー処理を行わずに正しい値を返します。
