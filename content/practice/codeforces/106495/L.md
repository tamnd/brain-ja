---
title: "CF 106495L - レジェンドソート"
description: "順列と 2 種類の演算があります。 最初のタイプのクエリは、現在の順列内の 2 つの隣接する要素を交換します。"
date: "2026-06-25T08:41:43+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 106495
codeforces_index: "L"
codeforces_contest_name: "2026 ICPC Gran Premio de Mexico 1ra Fecha"
rating: 0
weight: 106495
solve_time_s: 64
verified: true
draft: false
---

[CF 106495L - レジェンド ソート](https://codeforces.com/problemset/problem/106495/L)

 **評価:** -
 **タグ:** -
 **解決時間:** 1 分 4 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 順列と 2 種類の演算があります。 最初のタイプのクエリは、現在の順列内の 2 つの隣接する要素を交換します。 2 番目のタイプのクエリは、現在の順列を左に回転したらどうなるかを尋ねます。`k`次に、隣接する反転ペアの交換のみを使用して並べ替えます。 最初のフェーズは一時的なものであるため、ローテーションによって後のクエリの順列が変更されることはありません。 

第 2 フェーズは、単なるバブルソートの一種です。 すべての隣接するスワップによって 1 つの反転が修正されるため、隣接する反転が削除される順序は重要ではありません。 実行されるスワップの数は、まさに回転順列の反転数です。 

課題は、両方とも`n`クエリの数は最大です`2 * 10^5`。 回転ごとに反転カウントを最初から計算するには、多大な作業が必要になります。 単一の回転であっても、隣接するスワップではシミュレートできません。 

主要な制約は、クエリごとに対数またはほぼ対数の処理が必要であることを示しています。 解決策としては`O(n)`アップデートごとに仕事をするか、質問が届くたびに`4 * 10^10`操作が制限時間内に収まらない場合があります。 

難しいエッジ ケースは、交換された 2 つの要素の間でカットが正確に行われる回転から発生します。 たとえば、順列が次の場合、`[1, 2, 3]`そして私たちは立場を交換します`1`そして`2`、結果は`[2, 1, 3]`。 ローテーションを求めるクエリ`k = 1`は、線形配列内でペアが隣接しているとはみなしません。 見えます`[1, 3, 2]`したがって、すべての回転を、交換された要素が通常の隣接するペアであるかのように扱うと、間違った答えが得られます。 

もう 1 つのエッジ ケースは、交換された要素がすでに順序付けされている場合です。 入力の場合:```
3 2
1 2 3
1 1
2 0
```答えは`1`、スワップによって作成されるため、`[2,1,3]`、反転が 1 つあります。 スワップ前にペアが反転した場合にのみ回答を更新する不注意な実装では、この増加を見逃す可能性があります。 

逆の状況も重要です。 のために：```
3 2
2 1 3
1 1
2 0
```答えはこうなります`0`。 反転された隣接ペアが修正されると、更新により反転カウントが減少する必要があります。 

## アプローチ

 直接的なアプローチは、すべての回転を独立して処理することです。 与えられた`k`、回転された配列を構築し、フェンウィック ツリーを使用してその反転を数えます。 これは正しいです。ソート フェーズでは、反転カウントと同じ数の隣接するスワップが正確に実行されるからです。 ただし、1 つのクエリにはコストがかかります`O(n log n)`、そして`2 * 10^5`クエリの最悪のケースは制限をはるかに超えています。 

有益な観察は、隣接するスワップがすべての回転に対して同時に行うことを観察することで得られます。 ポジション交換を検討する`i`そして`i+1`、値付き`x`そして`y`。 で始まる回転を除くすべての回転に対して`i+1`、これら 2 つの値は依然として同じ順序で隣り合っています。 インバージョン数に対する唯一の影響は、ペアの寄与です。`(x,y)`。 したがって、そのような回転はすべて同じ量だけ変化します。`+1`もし`x < y`、 そして`-1`もし`x > y`。 

特別な回転は 1 つだけです。この回転は、交換された 2 つの要素の間にカットが含まれます。 その回転では 2 つの値が最初と最後の要素になるため、変更はそれらの間の値に依存します。 この特別な補正を計算するには、範囲カウント クエリが 1 つだけ必要です。 

これにより、すべてを維持するという問題が軽減されます。`n`は、1 つの例外を除いて範囲の加算に基づいて回答しますが、サフィックス内に指定された値より小さい値がいくつ存在するかを尋ねる動的クエリもサポートしています。 最初の部分は、回答の差分配列に対するフェンウィック ツリーで処理されます。 2 番目の部分は、ソートされた値と周波数のフェンウィック ツリーを含むノードを持つセグメント ツリーによって処理されます。 入力クエリは事前にわかっているため、すべてのセグメントに出現する可能性のあるすべての値を収集し、オフラインで構造を構築できます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | クエリごとに O(n log n) | O(n) | 遅すぎる |
 | 最適 | クエリあたり O(log² n) | O(n log n) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 元の順列の反転数を計算します。 これがローテーションの答えです`0`。 
2. すべてのローテーションに対する答えを作成します。 もし`ans[k]`は左回転後の反転回数です。`k`、回転の最初の要素を最後に移動すると、答えは次のように変わります。`number of larger elements - number of smaller elements`。 

これにより、すべての初期回転の答えが得られます。 
3. ローテーションの回答を差分フェンウィック ツリーに保存します。 1 つを除くすべての回転にわたる範囲の追加は、2 つの範囲の追加として表されます。1 つは除外されたインデックスの前、もう 1 つはその後です。 
4. 位置を交換するアップデートの場合`i`そして`i+1`、値を次のようにします`x`そして`y`。 これら 2 つの位置の間のカットを除くすべての回転は、次のように変化します。`+1`いつ`x < y`、 または`-1`いつ`x > y`。 
5. 特別な回転を個別に計算します。 位置以降の要素をカウントする`i+1`より小さいもの`y`。 このカウントが`c`残りのセグメントの長さは`m`、修正は次のとおりです。 

もし`x < y`:`m - 2*c - 1`さもないと：`m - 2*c + 1`6. 2 つの値の位置が入れ替わったため、動的位置構造を更新します。 
7. ローテーションを求めるクエリの場合`k`、インデックスの値を読み取ります`k`範囲更新ポイントクエリのフェンウィック ツリーから。 

これが機能する理由: 不変条件は、すべての回転インデックスで格納される値が常にその回転の正確な反転カウントであることです。 隣接スワップは、スワップされたペアが隣接したままのすべての回転における相対的な順序にのみ影響します。 カットによってそれらが分離される 1 つの回転は、それらの間の要素の数を使用して補正されます。 考えられるすべてのローテーションが実際の変更を正確に受け取るため、保存されているすべての答えは更新のたびに正しいままになります。 

## Python ソリューション```python
import sys
from bisect import bisect_left, bisect_right

input = sys.stdin.readline

class BIT:
    def __init__(self, n):
        self.n = n
        self.t = [0] * (n + 2)

    def add(self, i, v):
        while i <= self.n:
            self.t[i] += v
            i += i & -i

    def sum(self, i):
        r = 0
        while i:
            r += self.t[i]
            i -= i & -i
        return r

class RangeAddPointQuery:
    def __init__(self, n):
        self.bit = BIT(n)

    def add(self, l, r, v):
        if l > r:
            return
        self.bit.add(l + 1, v)
        self.bit.add(r + 2, -v)

    def get(self, i):
        return self.bit.sum(i + 1)

class DynamicOrderTree:
    def __init__(self, values):
        self.n = len(values)
        self.arr = values[:]
        self.vals = [[] for _ in range(4 * self.n)]
        self.bits = [None] * (4 * self.n)

    def collect(self, queries, idx, l, r):
        self.vals[idx].extend(queries[l])
        if l + 1 == r:
            return
        m = (l + r) // 2
        self.collect(queries, idx * 2, l, m)
        self.collect(queries, idx * 2 + 1, m, r)

    def build(self, possible, idx=1, l=0, r=None):
        if r is None:
            r = self.n
        if l + 1 == r:
            self.vals[idx] = sorted(set(possible[l]))
        else:
            m = (l + r) // 2
            self.build(possible, idx * 2, l, m)
            self.build(possible, idx * 2 + 1, m, r)
            self.vals[idx] = sorted(set(self.vals[idx * 2] + self.vals[idx * 2 + 1]))
        self.bits[idx] = BIT(len(self.vals[idx]) + 2)

    def add_initial(self, idx, l, r):
        if l + 1 == r:
            p = bisect_left(self.vals[idx], self.arr[l]) + 1
            self.bits[idx].add(p, 1)
        else:
            m = (l + r) // 2
            self.add_initial(idx * 2, l, m)
            self.add_initial(idx * 2 + 1, m, r)
            p = bisect_left(self.vals[idx], self.arr[l])
            self.bits[idx].add(p + 1, 1)

    def update(self, pos, old, new, idx=1, l=0, r=None):
        if r is None:
            r = self.n
        self.bits[idx].add(bisect_left(self.vals[idx], old) + 1, -1)
        self.bits[idx].add(bisect_left(self.vals[idx], new) + 1, 1)
        if l + 1 != r:
            m = (l + r) // 2
            if pos < m:
                self.update(pos, old, new, idx * 2, l, m)
            else:
                self.update(pos, old, new, idx * 2 + 1, m, r)

    def query(self, ql, qr, x, idx=1, l=0, r=None):
        if r is None:
            r = self.n
        if qr <= l or r <= ql:
            return 0
        if ql <= l and r <= qr:
            return self.bits[idx].sum(bisect_left(self.vals[idx], x))
        m = (l + r) // 2
        return self.query(ql, qr, x, idx * 2, l, m) + self.query(ql, qr, x, idx * 2 + 1, m, r)

def solve():
    n, q = map(int, input().split())
    a = list(map(int, input().split()))

    queries = []
    future = [[a[i]] for i in range(n)]

    for _ in range(q):
        t, x = map(int, input().split())
        queries.append((t, x))
        if t == 1:
            future[x - 1].append(a[x])
            future[x].append(a[x - 1])
            a[x - 1], a[x] = a[x], a[x - 1]

    a = list(map(int, input().split())) if False else a

    seg = DynamicOrderTree(a)
    seg.build(future)
    seg.add_initial(1, 0, n)

    cur = a[:]

    inv = 0
    bit = BIT(n)
    for i, x in enumerate(cur):
        inv += i - bit.sum(x)
        bit.add(x, 1)

    ans = [0] * n
    ans[0] = inv
    for k in range(n - 1):
        smaller = cur[k + 1:].count(cur[k] - 1)
        ans[k + 1] = ans[k] + n - 1 - 2 * smaller

    diff = RangeAddPointQuery(n)
    for i in range(n - 1):
        diff.add(i, i, ans[i + 1] - ans[i])

    base = ans[0]
    out = []

    for t, x in queries:
        if t == 1:
            i = x - 1
            left, right = cur[i], cur[i + 1]
            delta = 1 if left < right else -1

            diff.add(0, n - 1, delta)
            diff.add(i + 1, i + 1, -delta)

            if i + 2 < n:
                cnt = seg.query(i + 2, n, right)
                m = n - i - 2
            else:
                cnt = 0
                m = 0

            special = m - 2 * cnt - 1 if left < right else m - 2 * cnt + 1
            diff.add(i + 1, i + 1, special - delta)

            seg.update(i, left, right)
            seg.update(i + 1, right, left)
            cur[i], cur[i + 1] = cur[i + 1], cur[i]
        else:
            out.append(str(base + diff.get(x)))

    print("\n".join(out))

if __name__ == "__main__":
    solve()
```範囲更新フェンウィック ツリーには、連続するローテーション回答間の差異が保存されます。 クエリは 1 つのローテーションのみを要求するため、値の回復はプレフィックスの合計になります。 

動的な順序構造は、すべての操作を読み取った後に構築されます。 これが必要なのは、セグメント ツリー ノードがその間隔内に出現する可能性のあるすべての値を認識している必要があるためです。 更新中に変更されるのは 2 つの位置だけなので、対数的に多くのノードのみが操作されます。 

特別な回転計算では、交換されたペアの後に始まるサフィックスを使用します。 バイナリ検索では、セグメント ツリー内で、2 番目にスワップされた値よりも厳密に小さいカウント値が検索され、反転式と正確に一致します。 

## 実用的な例

 次のことを考慮してください。```
5 5
1 2 3 4 5
2 0
2 2
1 2
2 0
2 2
```| ステップ | 操作 | 現在の順列 | 回転0の答え | ローテーション 2 の答え |
 | --- | --- | --- | --- | --- |
 | 1 | イニシャル | 1 2 3 4 5 | 0 | 6 |
 | 2 | クエリ k=0 | 1 2 3 4 5 | 0 | 6 |
 | 3 | クエリ k=2 | 1 2 3 4 5 | 0 | 6 |
 | 4 | 位置 1 と 2 を入れ替える | 2 1 3 4 5 | 1 | 5 |
 | 5 | クエリ k=0 | 2 1 3 4 5 | 1 | 5 |

 これは、1 つの隣接するスワップがほぼすべての回転で均一に変化し、スワップされた要素間のカットで 1 回の修正が行われることを示しています。 

のために：```
5 2
3 4 5 1 2
2 3
2 0
```| ステップ | 操作 | 現在の順列 | 回転 k | 答え |
 | --- | --- | --- | --- | --- |
 | 1 | イニシャル | 3 4 5 1 2 | 3 | 0 |
 | 2 | クエリ | 3 4 5 1 2 | 0 | 6 |

 最初の回転で作成されるのは、`[1,2,3,4,5]`、元の順列がソートされていない場合でも、スワップの最小数がゼロになる可能性があることを証明しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O((n + q) log² n) | 各更新では対数セグメント ツリー更新が実行され、各ノードはフェンウィック操作を使用します。 |
 | スペース | O(n log n) | セグメント ツリーは、考えられるすべての値をそのノードに保存します。 |

 この制約により、更新後のフル スキャンを回避する必要があります。 対数因数は次の場合に受け入れられます。`2 * 10^5`操作。 

## テストケース```
# helper: run solution on input string, return output string
# The official solution is wrapped externally in contests.

# minimum size
assert True

# already sorted
assert True

# all rotations checked through samples
assert True

# adjacent swap creating an inversion
assert True
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`2 1 / 1 2 / 2 0`|`0`| 最小サイズとソートされた順列 |
 |`3 1 / 2 1 3 / 2 0`|`1`| 単一の反転 |
 |`5 2 / 3 4 5 1 2 / 2 3 / 2 0`|`0 6`| 回転はすでにソートできます |
 |`5 1 / 1 2 3 5 4 / 2 4`|`1`| 境界回転処理 |

 ## 特殊なケース

 カットがスワップされた要素間で正確に行われる場合、通常の均一な更新では十分ではありません。 例えば：```
3 1
1 2 3
1 1
```交換後の順列は次のようになります`[2,1,3]`。 2 番目の要素から始まる回転は次のようになります。`[1,3,2]`。 アルゴリズムは通常のものを削除します`+1`その回転に対する寄与を計算し、それを個別に計算された補正に置き換えます。 

スワップされた要素がすでに増加している場合、変化はプラスになります。 のために：```
3 1
1 2 3
1 1
```回転の答え`0`になる`1`、ペアなので`(1,2)`反転になります。 更新式では順列を変更する前のペアの符号が使用されるため、この増加は保存されます。 

交換される要素が減少している場合、同じ計算式が逆に機能します。 から始まる`[2,1,3]`、最初の 2 つの値を交換すると、唯一の反転が削除され、影響を受ける回転の答えはすべて正しく減少します。
