---
title: "CF 121E - ラッキーアレイ"
description: "値が常に最大 10000 である配列があります。2 つの操作をオンラインで処理する必要があります。 最初の操作では、セグメント [l, r] 内のすべての要素に値 d を追加します。"
date: "2026-05-28T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "data-structures"]
categories: ["algorithms"]
codeforces_contest: 121
codeforces_index: "E"
codeforces_contest_name: "Codeforces Beta Round 91 (Div. 1 Only)"
rating: 2400
weight: 121
solve_time_s: 141
verified: true
draft: false
---

[CF 121E - ラッキー アレイ](https://codeforces.com/problemset/problem/121/E)

 **評価:** 2400
 **タグ:** データ構造
 **解決時間:** 2 分 21 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 値が常に最大以下である配列があります。`10000`。 2 つの操作をオンラインで処理する必要があります。 

最初の操作は値を追加します`d`セグメント内のすべての要素に`[l, r]`。 

2 番目の演算では、中に数字がいくつあるかを尋ねます。`[l, r]`幸運な数字は数字のみで構成されます`4`そして`7`。 

難しいのは、ラッキーナンバーを認識できないことです。 以下のものはほとんどありません`10000`。 本当の課題は、以下をサポートすることです`10^5`範囲の追加と`10^5`範囲クエリは十分に高速です。 

直接シミュレーションを行うと、すぐに問題が発生します。 すべての操作で配列のほぼ全体が更新されるとします。 影響を受ける値を 1 つずつ再計算するとコストがかかります`O(n)`アップデートごとに。 と`10^5`操作と`10^5`要素、これはおおよそ次のようになります`10^10`限界をはるかに超えたアップデート。 

すべての値が常に最大値にとどまる限界`10000`重要な構造特性です。 あるのは`2 + 4 + 8 + 16 = 30`この範囲内の幸運な数字。 コストをかけて正確な値を追跡する代わりに、各値が幸運になるまでの距離を追跡できます。 

いくつかのエッジケースでは、単純な最適化が失敗します。 

次の入力を考えてみましょう。```
3 2
3 4 5
add 1 3 1
count 1 3
```追加後の配列は次のようになります`[4,5,6]`、したがって、答えは次のとおりです`1`。 以前の幸運な位置を更新するだけの実装では、それが見逃されます。`3`幸運になりました。 

もう一つの微妙なケース:```
3 3
4 7 44
add 1 3 1
add 1 3 1
count 1 3
```配列は次のように進化します`[5,8,45]`それから`[6,9,46]`。 正しい答えは、`0`。 遷移を再計算せずに、ポジションが現在ラッキーであるかどうかだけを保存する遅延構造では、誤って古いラッキー数が保持されてしまいます。 

3 番目の重要な状況は、セグメントが多くの遅延追加を受け取るときに発生します。```
5 3
1 2 3 4 5
add 1 5 100
add 1 5 200
count 1 5
```最終的な配列は`[301,302,303,304,305]`、そしてのみ`304`幸運ですか？ いいえ、そうではありません。ラッキーナンバーには数字しか含まれていません。`4`そして`7`。 正しい答えは、`0`。 これにより、「すべての数字が 4 または 7 である」ではなく「4 または 7 が含まれる」と誤って解釈する実装が捕捉されます。 

## アプローチ

 ブルートフォースによる解決策は簡単です。 あらゆる人にとって`add`操作を実行し、間隔全体を繰り返して各要素を更新します。 あらゆる人にとって`count` operation, iterate again and test each number individually.

Checking whether a number is lucky takes at most four digit inspections because values never exceed `10000`。 したがって、コストがかかる部分は、間隔を繰り返しスキャンすることです。 

最悪の場合、すべての操作が配列全体に影響します。 それは与える`10^5 × 10^5 = 10^10`要素の更新またはチェック。 C++ でも 4 秒の制限ではこれは絶望的です。 

重要な観察は、幸運な数字のセットが小さいということです。 ラッキーナンバーは最大で 30 個しかありません`10000`。 

価値のために`x`, 重要なのは正確な数字そのものではなく、次の幸運な数字までの距離です。 次のラッキーナンバーが またはそれ以上だと仮定します`x`は`L`。 定義する：```
delta = L - x
```もし`delta == 0`, この数字は現在幸運です。 

ここで追加を検討してください`d`セグメント全体に。 値は次のようになります`x + d`したがって、新しい距離は次のようになります。```
L - (x + d) = delta - d
```範囲の加算は単に減算するだけです`d`すべてのデルタから。 

これにより、問題はセグメント ツリーが非常に自然に処理できるものに変換されます。 

すべてのセグメント ツリー ノードについて、以下を維持します。```
mn = minimum delta in this segment
cnt = how many positions have delta == 0
```遅延加算を適用すると、すべてのデルタが均一にシフトするため、`mn`も一律にシフトします。 

重要なトリックは次のとおりです。 

差し引いた後なら`d`、ノードにはまだあります`mn > 0`、その後、セグメント内のどの要素も幸運になりませんでした。 それ以上下がらずにすぐに停止できます。 

一部のデルタが正でなくなった場合にのみ、さらに深く押し込みます。 すべての要素が幸運の閾値を超えることができるのは合計約 30 回だけであるため、高価な降下の総数は管理可能なままです。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース |`O(nm)`|`O(n)`| 遅すぎる |
 | 最適 |`O((n + m) log n log C)`償却済み |`O(n)`| 承認済み |

 ここ`C`は最大値の境界です。`10000`。 

## アルゴリズムのチュートリアル

 1. 以下のすべてのラッキーナンバーを事前計算します。`10000`。 

数字上の DFS または BFS`4`そして`7`生成される数値は 30 個だけなので、この手順は簡単です。 
2. 配列の初期値ごとに`a[i]`、次のラッキーナンバーを計算します`L >= a[i]`.

Store:

```
delta[i] = L - a[i]
```もし`delta[i] == 0`, then `a[i]`すでに幸運です。 
3. セグメントツリーを構築します。 

各ノードには以下が保存されます。```
mn  = minimum delta in the segment
cnt = number of zeros in the segment
lazy = pending subtraction applied to all deltas
```葉っぱ`delta == 0`もっている`cnt = 1`、 さもないと`cnt = 0`。 
4.`count l r`クエリ、合計を返します`cnt`要求された間隔にわたる値。 

位置は、そのデルタがゼロの場合に正確に寄与します。 
5.`add l r d`演算、減算`d`間隔内のすべてのデルタから。 

概念的には、あらゆる値が移動しました`d`次のラッキーナンバーに近づきます。 
6. 更新の対象となるセグメント ツリー ノードを更新する場合は、次の値を減らします。```
mn -= d
lazy -= d
```その後だったら`mn > 0`の場合、すべてのデルタは依然として正であるため、どの要素も幸運になりません。 ノードは有効なままです。 
7. もし`mn <= 0`、少なくとも 1 つの要素が幸運の境界を越えました。 

影響を受けるリーフを再計算するには、再帰的に下降する必要があります。 
8. 葉っぱのとき`delta <= 0`、現在の値が追跡されたラッキーナンバーに達しているか、それを超えています。 

実際の配列値を更新し、次のラッキーナンバーを再計算し、リーフの状態を再構築します。 
9. 再帰的な更新の後、子を上向きにマージします。```
mn = min(left.mn, right.mn)
cnt = left.cnt + right.cnt
```### なぜ効果があるのか

 各リーフは常に、現在の値からその値以上の次のラッキーナンバーまでの距離を保存します。 

範囲を追加すると、これらの距離が均一に減少します。 すべての距離が正のままであれば、どの値も幸運とはならず、再計算は必要ありません。 

距離が正でなくなると、その位置は幸運のしきい値を超えています。 次の幸運な数字を再計算すると、不変式が復元されます。 

すべての値が一度に交差するラッキーナンバーは最大 1 つであり、その下にラッキーナンバーは 30 個しかないためです。`10000`、各要素は実行全体にわたって少数回のみ再構築されます。 セグメント ツリーは、すべての間隔で正しいラッキー カウントを常に維持します。 

## Python ソリューション```python
import sys
from bisect import bisect_left

input = sys.stdin.readline

def generate_lucky():
    res = []

    def dfs(x):
        if x > 10000:
            return
        if x:
            res.append(x)
        dfs(x * 10 + 4)
        dfs(x * 10 + 7)

    dfs(0)
    res.sort()
    return res

LUCKY = generate_lucky()

class SegTree:
    def __init__(self, arr):
        self.n = len(arr)
        self.arr = arr[:]

        size = 4 * self.n + 5
        self.mn = [0] * size
        self.cnt = [0] * size
        self.lazy = [0] * size

        self.build(1, 0, self.n - 1)

    def next_delta(self, x):
        idx = bisect_left(LUCKY, x)
        return LUCKY[idx] - x

    def apply(self, node, val):
        self.mn[node] += val
        self.lazy[node] += val

    def push(self, node):
        if self.lazy[node]:
            v = self.lazy[node]
            self.apply(node * 2, v)
            self.apply(node * 2 + 1, v)
            self.lazy[node] = 0

    def pull(self, node):
        self.mn[node] = min(self.mn[node * 2], self.mn[node * 2 + 1])
        self.cnt[node] = self.cnt[node * 2] + self.cnt[node * 2 + 1]

    def build(self, node, l, r):
        if l == r:
            d = self.next_delta(self.arr[l])
            self.mn[node] = d
            self.cnt[node] = 1 if d == 0 else 0
            return

        mid = (l + r) // 2

        self.build(node * 2, l, mid)
        self.build(node * 2 + 1, mid + 1, r)

        self.pull(node)

    def rebuild_leaf(self, node, idx):
        d = self.next_delta(self.arr[idx])
        self.mn[node] = d
        self.cnt[node] = 1 if d == 0 else 0

    def range_add(self, node, l, r, ql, qr, val):
        if ql <= l and r <= qr:
            self.apply(node, -val)

            if self.mn[node] > 0:
                return

        if l == r:
            self.arr[l] += val
            self.rebuild_leaf(node, l)
            return

        self.push(node)

        mid = (l + r) // 2

        if ql <= mid:
            self.range_add(node * 2, l, mid, ql, qr, val)

        if qr > mid:
            self.range_add(node * 2 + 1, mid + 1, r, ql, qr, val)

        self.pull(node)

    def query(self, node, l, r, ql, qr):
        if ql <= l and r <= qr:
            return self.cnt[node]

        self.push(node)

        mid = (l + r) // 2
        ans = 0

        if ql <= mid:
            ans += self.query(node * 2, l, mid, ql, qr)

        if qr > mid:
            ans += self.query(node * 2 + 1, mid + 1, r, ql, qr)

        return ans

def solve():
    n, m = map(int, input().split())
    arr = list(map(int, input().split()))

    seg = SegTree(arr)

    out = []

    for _ in range(m):
        parts = input().split()

        if parts[0] == "add":
            l = int(parts[1]) - 1
            r = int(parts[2]) - 1
            d = int(parts[3])

            seg.range_add(1, 0, n - 1, l, r, d)

        else:
            l = int(parts[1]) - 1
            r = int(parts[2]) - 1

            out.append(str(seg.query(1, 0, n - 1, l, r)))

    sys.stdout.write("\n".join(out))

solve()
```最初の部分では、以下のすべてのラッキーナンバーが生成されます。`10000`。 カウントは小さいため、すべての整数を個別にチェックするよりも再帰的生成の方が簡単でクリーンです。 

セグメント ツリーは、葉から葉までを除いて実際の値を格納しません。`self.arr`。 内部ノードは、幸運な数字までの最小距離と、現在の幸運な位置の数のみを追跡します。 

最も微妙な点は、更新時の符号規則です。 

値が増加した場合`d`、次のラッキーナンバーまでの距離は によって減少します。`d`。 そのため、アップデートが適用されます`-d`に`mn`そして`lazy`。 

もう 1 つの簡単な間違いは、再帰の停止が早すぎることです。 次の場合にのみ停止する場合があります`mn > 0`。 もし`mn == 0`、いくつかの要素はまさに幸運であり、ノードはその情報を正確に保存する必要があります。 

Leave では、実際に更新された値を使用して再構築します。 値が追跡された幸運の数を超えると、「次の幸運」が変化するため、単純にデルタを永久に減算し続けることはできません。 

この実装では、後で再帰するノードに対しても保留中の遅延値が保存されます。 これにより、子はさらなる操作を行う前に、常に正しい蓄積された更新を受け取ることが保証されます。 

## 実用的な例

 ### サンプル 1

 入力:```
3 6
2 3 4
count 1 3
count 1 2
add 1 3 2
count 1 3
add 2 3 3
count 1 3
```初期デルタ:

 | ポジション | 値 | 次のラッキー | デルタ |
 | --- | --- | --- | --- |
 | 1 | 2 | 4 | 2 |
 | 2 | 3 | 4 | 1 |
 | 3 | 4 | 4 | 0 |

 最初のクエリ:

 | クエリ | 幸運な位置 | 答え |
 | --- | --- | --- |
 | カウント 1 3 | {3} | 1 |

 2 番目のクエリ:

 | クエリ | 幸運な位置 | 答え |
 | --- | --- | --- |
 | カウント 1 2 | {} | 0 |

 後`add 1 3 2`:

 | ポジション | 古い値 | 新しい価値 | ニューデルタ |
 | --- | --- | --- | --- |
 | 1 | 2 | 4 | 0 |
 | 2 | 3 | 5 | 2 |
 | 3 | 4 | 6 | 1 |

 3 番目のクエリ:

 | クエリ | 幸運な位置 | 答え |
 | --- | --- | --- |
 | カウント 1 3 | {1} | 1 |

 後`add 2 3 3`:

 | ポジション | 古い値 | 新しい価値 | ニューデルタ |
 | --- | --- | --- | --- |
 | 1 | 4 | 4 | 0 |
 | 2 | 5 | 8 | 36 |
 | 3 | 6 | 9 | 35 |

 最終的なクエリ:

 | クエリ | 幸運な位置 | 答え |
 | --- | --- | --- |
 | カウント 1 3 | {1} | 1 |

 このトレースは、幸運の閾値を超えた後に葉を再構築する必要がある理由を示しています。 ポジション 2 はターゲットのラッキーナンバーから移動しました`7`ターゲティングへ`44`。 

### サンプル 2

 入力:```
4 3
4 44 5 6
count 1 4
add 3 4 1
count 1 4
```初期状態:

 | ポジション | 値 | ラッキー |
 | --- | --- | --- |
 | 1 | 4 | はい |
 | 2 | 44 | はい |
 | 3 | 5 | いいえ |
 | 4 | 6 | いいえ |

 最初のクエリ:

 | クエリ | 答え |
 | --- | --- |
 | カウント 1 4 | 2 |

 アップデート後:

 | ポジション | 古い値 | 新しい価値 | ラッキー |
 | --- | --- | --- | --- |
 | 3 | 5 | 6 | いいえ |
 | 4 | 6 | 7 | はい |

 2 番目のクエリ:

 | クエリ | 答え |
 | --- | --- |
 | カウント 1 4 | 3 |

 この例は、ラッキーではない値は、そのデルタがゼロに達したときにまさにラッキーになる可能性があることを示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |`O((n + m) log n log C)`償却済み | 各再構築はツリーを下降し、すべての要素は少数の幸運なしきい値のみを超えます。 
| スペース |`O(n)`| セグメント ツリー配列と格納された値 |

 以下のラッキーナンバーは 30 個のみです`10000`したがって、各位置で高価な再構築ロジックをトリガーできるのは制限された回数だけです。 償却後の複雑さは容易に制限内に収まります。`10^5`操作。 

## テストケース```python
# helper: run solution on input string, return output string
import sys
import io
from bisect import bisect_left

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)

    input = sys.stdin.readline

    def generate_lucky():
        res = []

        def dfs(x):
            if x > 10000:
                return
            if x:
                res.append(x)
            dfs(x * 10 + 4)
            dfs(x * 10 + 7)

        dfs(0)
        res.sort()
        return res

    LUCKY = generate_lucky()

    class SegTree:
        def __init__(self, arr):
            self.n = len(arr)
            self.arr = arr[:]

            size = 4 * self.n + 5
            self.mn = [0] * size
            self.cnt = [0] * size
            self.lazy = [0] * size

            self.build(1, 0, self.n - 1)

        def next_delta(self, x):
            idx = bisect_left(LUCKY, x)
            return LUCKY[idx] - x

        def apply(self, node, val):
            self.mn[node] += val
            self.lazy[node] += val

        def push(self, node):
            if self.lazy[node]:
                v = self.lazy[node]
                self.apply(node * 2, v)
                self.apply(node * 2 + 1, v)
                self.lazy[node] = 0

        def pull(self, node):
            self.mn[node] = min(self.mn[node * 2], self.mn[node * 2 + 1])
            self.cnt[node] = self.cnt[node * 2] + self.cnt[node * 2 + 1]

        def build(self, node, l, r):
            if l == r:
                d = self.next_delta(self.arr[l])
                self.mn[node] = d
                self.cnt[node] = 1 if d == 0 else 0
                return

            mid = (l + r) // 2

            self.build(node * 2, l, mid)
            self.build(node * 2 + 1, mid + 1, r)

            self.pull(node)

        def rebuild_leaf(self, node, idx):
            d = self.next_delta(self.arr[idx])
            self.mn[node] = d
            self.cnt[node] = 1 if d == 0 else 0

        def range_add(self, node, l, r, ql, qr, val):
            if ql <= l and r <= qr:
                self.apply(node, -val)

                if self.mn[node] > 0:
                    return

            if l == r:
                self.arr[l] += val
                self.rebuild_leaf(node, l)
                return

            self.push(node)

            mid = (l + r) // 2

            if ql <= mid:
                self.range_add(node * 2, l, mid, ql, qr, val)

            if qr > mid:
                self.range_add(node * 2 + 1, mid + 1, r, ql, qr, val)

            self.pull(node)

        def query(self, node, l, r, ql, qr):
            if ql <= l and r <= qr:
                return self.cnt[node]

            self.push(node)

            mid = (l + r) // 2
            ans = 0

            if ql <= mid:
                ans += self.query(node * 2, l, mid, ql, qr)

            if qr > mid:
                ans += self.query(node * 2 + 1, mid + 1, r, ql, qr)

            return ans

    n, m = map(int, input().split())
    arr = list(map(int, input().split()))

    seg = SegTree(arr)

    out = []

    for _ in range(m):
        parts = input().split()

        if parts[0] == "add":
            l = int(parts[1]) - 1
            r = int(parts[2]) - 1
            d = int(parts[3])

            seg.range_add(1, 0, n - 1, l, r, d)

        else:
            l = int(parts[1]) - 1
            r = int(parts[2]) - 1

            out.append(str(seg.query(1, 0, n - 1, l, r)))

    return "\n".join(out)

# provided sample
assert run(
"""3 6
2 3 4
count 1 3
count 1 2
add 1 3 2
count 1 3
add 2 3 3
count 1 3
"""
) == "1\n0\n1\n1"

# minimum size
assert run(
"""1 2
4
count 1 1
count 1 1
"""
) == "1\n1"

# becoming lucky after update
assert run(
"""2 2
3 6
add 1 2 1
count 1 2
"""
) == "2"

# all equal values
assert run(
"""5 3
7 7 7 7 7
count 1 5
add 2 4 1
count 1 5
"""
) == "5\n2"

# boundary interval update
assert run(
"""5 4
1 2 3 4 5
add 1 5 3
count 1 5
add 5 5 2
count 4 5
"""
) == "2\n1"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 単一の要素はすでに幸運です |`1 1`| 最小サイズの取り扱い |
 | 幸運になる価値観 |`2`| 幸運の閾値を超える |
 | すべての値は最初は幸運です |`5 2`| 更新後の正しい減少率 |
 | フルレンジとサフィックスの更新 |`2 1`| 境界の正確性とインデックス作成 |

 ## 特殊なケース

 更新直後に値がラッキーになる場合を考えてみましょう。 

入力：```
1 2
3
add 1 1 1
count 1 1
```まず最初に次のラッキーナンバーは`4`、 それで`delta = 1`。 追加後`1`、セグメントの更新により減算されます。`1`デルタから、生産`0`。 以来`mn <= 0`、アルゴリズムはリーフに降りてそれを再構築します。 値は次のようになります`4`、それは幸運なので、`cnt = 1`。 クエリは正しく返されます`1`。 

次に、幸運な数字を残すことを検討してください。 

入力：```
1 2
4
add 1 1 1
count 1 1
```最初は`delta = 0`。 追加後`1`、デルタは`-1`、再構築を強制します。 新しい値は`5`、次のラッキーナンバーは`7`、したがって、再構築されたデルタは次のようになります。`2`そして`cnt = 0`。 答えは正解です`0`。 

最後に、ほとんどのノードを再構築せずに遅延更新を繰り返すことを検討してください。 

入力：```
5 3
1 1 1 1 1
add 1 5 1
add 1 5 1
count 1 5
```各値は次から移動します。`1`に`3`。 次のラッキーナンバーは残ります`4`、デルタはから進化します`3`に`2`に`1`。 セグメントの最小値が非正になることはないため、アルゴリズムが不必要に低下することはありません。 最終的な答えは、`0`、更新コストはすべてのリーフに影響を与えるのではなく、対数に近いままになります。
