---
title: "CF 19D - ポイント"
description: "2D 平面上に点の動的なセットを維持します。 3 つの操作がサポートされています。"
date: "2026-05-28T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "data-structures"]
categories: ["algorithms"]
codeforces_contest: 19
codeforces_index: "D"
codeforces_contest_name: "Codeforces Beta Round 19"
rating: 2800
weight: 19
solve_time_s: 229
verified: true
draft: false
---
[CF 19D - ポイント](https://codeforces.com/problemset/problem/19/D)

 **評価:** 2800
 **タグ:** データ構造
 **解決時間:** 3 分 49 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 2D 平面上に点の動的なセットを維持します。 3 つの操作がサポートされています。`add x y`点を挿入します。`remove x y`既存のポイントを削除します。`find x y`より厳密に大きい座標を持つ点を要求します`(x, y)`両方の次元で、非常に具体的なタイブレーク ルールが適用されます。 

すべての点で満足のいくもの:

 -`px > x`-`py > y`できるだけ小さいものが欲しいです`px`。 複数の点が同じものを共有する場合`px`、最小のものを選択します`py`。 

したがって、クエリは世界的に最小の有効な点を求めていません。 最初に x 座標を最小化し、次に y 座標を最小化します。 

操作数が到達しました`2 * 10^5`、座標は次のようになります。`10^9`。 座標値によって直接インデックス付けされた配列を構築できないため、大きな座標は直ちに座標圧縮を示唆します。 

操作数はより深刻な制約です。 すべてのポイントに対する単純なスキャン`find`最悪の場合、クエリには二次時間がかかります。 と`2 * 10^5`オペレーション、`O(n)`クエリは簡単に生成できます`4 * 10^10`2秒以内に比較することは完全に不可能です。 

操作ごとに対数に近い時間が必要です。 

難しいのは、順序付けルールが非対称であることです。 単に上と右側に点が必要なわけではありません。 特に左端の有効な x 座標が必要であり、その x 座標を固定した後でのみ y を最小化します。 

その構造が解決の鍵となります。 

次の例を考えてみましょう。```
add 2 100
add 3 1
find 1 50
```正しい答えは、`(2, 100)`。 

有効な点間で y をグローバルに最小化する不用意なアプローチでは、次の結果が返されます。`(3, 1)`これは、x の方が優先度が高いため、間違っています。 

もう 1 つの微妙なケースは、厳密な不平等です。```
add 5 5
find 5 4
```答えは`-1`。 

それでも`y > 4`、x 座標は厳密には以下より大きくありません。`5`。 

重複する X 座標も重要です。```
add 7 10
add 7 3
find 5 4
```答えは`(7, 10)`。 

ポイント`(7, 3)`失敗する理由`3`より大きくない`4`。 各 x 座標の最小 y を単に保存することはできません。 クエリよりも大きい y が存在するかどうかを知る必要があります。 

動的削除により、別の一般的な障害モードが作成されます。```
add 4 8
remove 4 8
find 1 1
```答えは`-1`。 

キャッシュされた構造は、ポイントが消失したときに寄与を正しく削除する必要があります。 

## アプローチ

 ブルートフォースの解決策は簡単です。 セット内のすべての現在のポイントを維持します。 それぞれについて`find x y`、すべてのポイントを反復処理し、問題の順序に従って最適な候補を保持します。 

これは、クエリ定義が明示的であるため機能します。 満足するすべての点を単純にテストします。```
px > x and py > y
```辞書編集上最小のものを維持します`(px, py)`。 

ハッシュ セットを使用すると挿入と削除は簡単ですが、クエリのコストが高くなります。 最悪の場合、`2 * 10^5`アクティブポイントと`2 * 10^5`クエリ、大まかに生成`4 * 10^10`小切手。 

ボトルネックは、最小の有効な x 座標を効率的に検索することです。 

重要な観察は、すべての x 座標について、その x に現在存在する最大の y のみを考慮するということです。 

ある x 座標がクエリに答えることができるかどうかをチェックしているとします。`(qx, qy)`。 

その x における最大 y が以下の場合`qy`、その場合、x は機能しません。 

最大値 y が より大きい場合`qy`の場合、そこに少なくとも 1 つの有効な点が存在します。 

これにより、問題は次のようになります。 

- より大きい最小の x 座標を見つけます。`qx`- 最大 y が次より大きい`qy`x が見つかったら、その x で次を超える最小の y だけが必要になります。`qy`。 

これはまさに、セグメント ツリーが適切に処理できる種類の条件です。 

入力に現れるすべての x 座標を圧縮します。 圧縮されたすべての x インデックスについて、以下を維持します。 

- その x に現在存在する y 値のソートされたセット
 - その x における最大の y 値

 セグメント ツリーにはこれらの最大値が格納されます。 

クエリは次のようになります。 

- セグメントツリーの左端の位置を検索します。`qx`- 保存されている最大値 y が超えている`qy`その x 座標を見つけた後、その y セット内で二分探索を行い、次の値より大きい最小の y を探します。`qy`。 

各演算は対数になります。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n²) 最悪の場合 | O(n) | 遅すぎる |
 | 最適 | O(n log n) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 最初にすべての操作を読み取り、表示されるすべての X 座標を収集します。 

x 値は次のように大きくなる可能性があるため、座標圧縮が必要です。`10^9`、しかし、多くてもあります。`2 * 10^5`独特の座標。 
2. Sort and deduplicate the x-coordinates.

 元の各 x 座標は、次の間の圧縮されたインデックスを受け取ります。`0`そして`m - 1`。 
3. 圧縮された x インデックスごとに、その x で現在アクティブな y 値のソートされたコンテナを維持します。 

この構造に対して 2 つの操作が必要です。 

- yの挿入/削除
 - クエリ値より厳密に大きい最小の y を見つけます
 4. 各ノードがその区間内に現在存在する最大 y を格納するセグメント ツリーを構築します。 

x 座標にアクティブな点がない場合、その値は次のようになります。`-1`。 
5.`add x y`操作:

 - 挿入`y`x のソートされた構造に変換する
 - その x における新しい最大値 y でセグメント ツリーのリーフを更新します。 

セグメント ツリーは常に、各 x 座標で利用可能な最大の y を反映します。 
6. のために`remove x y`操作:

 - 消去`y`x の構造から
 - その x における最大 y を再計算します。 
- セグメントツリーを更新します

 その x 座標にポイントが残っていない場合、保存されている最大値は次のようになります。`-1`。 
7. のために`find x y`操作:

 - より厳密に大きい最初の圧縮された x インデックスを見つけます。`x`- 格納されている最大値 y が超える左端のインデックスをセグメント ツリーにクエリします。`y`セグメントの最大値が以下の場合、検索は機能します。`y`の場合、そのセグメント内の x 座標はクエリに答えることができません。 
8. そのような x 座標が存在しない場合は、出力`-1`。 
9. それ以外の場合は、その x 座標の y コンテナ内で、クエリ値より厳密に大きい最小の y をバイナリ検索します。 

これにより、必要なタイブレーク動作が得られます。 

- 最小の有効な x が最初になります
 - その x 内の最小の有効な y

 ### なぜ効果があるのか

 セグメント ツリーの不変条件は次のとおりです。```
tree[node] = maximum y among all active points in that interval of x-coordinates
```クエリ中に、セグメントの最大値が次の場合、`qy`の場合、そのセグメント内のすべての点が条件を満たしません。`py > qy`。 そのセグメント全体をスキップしても常に安全です。 

検索は常に最初に左の子に進みます。 そのため、最初に見つかった有効なリーフは、クエリを満たす最小の x 座標に対応します。 

x 座標が固定されたら、次の値より大きい最小の y を選択します。`qy`その X 座標から、必要な順序付けルールと正確に一致します。 

挿入と削除は常に対応する最大 y を更新するため、更新のたびに不変条件は true のままになります。 

## Python ソリューション```python
import sys
from bisect import bisect_right, insort
from collections import defaultdict

input = sys.stdin.readline

class SegmentTree:
    def __init__(self, n):
        self.n = n
        self.tree = [-1] * (4 * n)

    def update(self, node, l, r, idx, val):
        if l == r:
            self.tree[node] = val
            return

        mid = (l + r) // 2

        if idx <= mid:
            self.update(node * 2, l, mid, idx, val)
        else:
            self.update(node * 2 + 1, mid + 1, r, idx, val)

        self.tree[node] = max(
            self.tree[node * 2],
            self.tree[node * 2 + 1]
        )

    def query(self, node, l, r, ql, y):
        if r < ql or self.tree[node] <= y:
            return -1

        if l == r:
            return l

        mid = (l + r) // 2

        left = self.query(node * 2, l, mid, ql, y)
        if left != -1:
            return left

        return self.query(node * 2 + 1, mid + 1, r, ql, y)

def solve():
    n = int(input())

    ops = []
    xs = []

    for _ in range(n):
        t, x, y = input().split()
        x = int(x)
        y = int(y)

        ops.append((t, x, y))
        xs.append(x)

    comp = sorted(set(xs))
    xid = {x: i for i, x in enumerate(comp)}

    m = len(comp)

    ys = defaultdict(list)

    seg = SegmentTree(m)

    out = []

    for t, x, y in ops:
        idx = xid[x]

        if t == "add":
            insort(ys[idx], y)
            seg.update(1, 0, m - 1, idx, ys[idx][-1])

        elif t == "remove":
            pos = bisect_right(ys[idx], y) - 1
            ys[idx].pop(pos)

            new_max = ys[idx][-1] if ys[idx] else -1
            seg.update(1, 0, m - 1, idx, new_max)

        else:
            start = bisect_right(comp, x)

            if start >= m:
                out.append("-1")
                continue

            res = seg.query(1, 0, m - 1, start, y)

            if res == -1:
                out.append("-1")
                continue

            pos = bisect_right(ys[res], y)
            ans_y = ys[res][pos]

            out.append(f"{comp[res]} {ans_y}")

    sys.stdout.write("\n".join(out))

solve()
```x 値がまばらで非常に大きいため、解決策は座標の圧縮から始まります。 入力内の任意の場所に現れるすべての x 座標は、コンパクトなインデックスを受け取ります。 

圧縮されたすべての x インデックスについて、`ys[idx]`すべてのアクティブな y 値をソートされた順序で保存します。 クエリではしきい値よりも厳密に大きい最小の y が必要であるため、並べ替え順序が重要になります。 パイソンの`bisect_right`その位置を直接与えます。 

セグメント ツリーには、x 座標ごとに 1 つの数値、つまりアクティブな y の最大値のみが保存されます。 その単一の値は、その x のある点がクエリを満たすことができるかどうかを判断するのに十分です。 

再帰クエリ関数はソリューションの中心です。 最大 y が小さすぎるセグメントはスキップされ、常に最初に左側の子が探索されます。 これにより、最初の有効なリーフが最小の有効な x 座標に対応することが保証されます。 

削除は間違いやすいです。 y 値を削除した後、その x 座標での最大 y を再計算する必要があります。 容器が空になった場合は保管いたします`-1`したがって、今後のクエリではその位置が正しく無視されます。 

クエリではあらゆる場所で厳密な不等式が使用されます。 それが理由です`bisect_right`x 検索と y 検索の両方に使用されます。 使用する`bisect_left`誤って等しい座標を許可してしまいます。 

## 実用的な例

 ### 例 1

 入力:```
7
add 1 1
add 3 4
find 0 0
remove 1 1
find 0 0
add 1 1
find 0 0
```### トレース

 | ステップ | 操作 | アクティブポイント | セグメントマキシマ | 答え |
 | --- | --- | --- | --- | --- |
 | 1 | 1 1 を追加 | {(1,1)} | x=1→1、x=3→-1 | |
 | 2 | 3 4 を追加 | {(1,1),(3,4)} | x=1→1、x=3→4 | |
 | 3 | 0 0 | を見つける 変更なし | 変更なし | 1 1 |
 | 4 | 1 1 を削除 | {(3,4)} | x=1→-1、x=3→4 | |
 | 5 | 0 0 | を見つける 変更なし | 変更なし | 3 4 |
 | 6 | 1 1 を追加 | {(1,1),(3,4)} | x=1→1、x=3→4 | |
 | 7 | 0 0 | を見つける 変更なし | 変更なし | 1 1 |

 トレースは、クエリが最初に最小の有効な x 座標を検索する理由を示しています。 それでも`(3,4)`常に条件を満たしており、`(1,1)`x 座標が小さいため、存在する場合は常に優先されます。 

### 例 2

 入力:```
8
add 5 2
add 5 10
add 7 3
find 4 5
find 5 1
remove 5 10
find 4 5
find 6 2
```### トレース

 | ステップ | 操作 | アクティブポイント | x=5 での最大値 | x=7 での最大値 | 答え |
 | --- | --- | --- | --- | --- | --- |
 | 1 | 5 2 を追加 | {(5,2)} | 2 | -1 | |
 | 2 | 5 10を追加 | {(5,2),(5,10)} | 10 | -1 | |
 | 3 | 7 3 を追加 | {(5,2),(5,10),(7,3)} | 10 | 3 | |
 | 4 | 4 5 | を見つける 変更なし | 10 | 3 | 5 10 |
 | 5 | 5 1 を見つける | 変更なし | 10 | 3 | 7 3 |
 | 6 | 5 10 を削除 | {(5,2),(7,3)} | 2 | 3 | |
 | 7 | 4 5 | を見つける 変更なし | 2 | 3 | -1 |
 | 8 | 6 2を見つける | 変更なし | 2 | 3 | 7 3 |

 この例は、各 x 座標の最大 y を保存するだけで十分である理由を示しています。 削除前は、y の最大値が 10 であるため、x=5 はそのまま残ります。削除後`(5,10)`、最大値は 2 となり、満足できなくなります。`y > 5`。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n log n) | 各更新とクエリでは、対数セグメント ツリーとバイナリ検索操作が実行されます。 
| スペース | O(n) | 座標圧縮、セグメント ツリー、および保存されたポイントはすべて線形にスケールされます。 

せいぜい`2 * 10^5`操作、`O(n log n)`制限内に簡単に収まります。 セグメントツリーは以下のことを実行します。`log₂(2 * 10^5) ≈ 18`操作ごとの再帰レベル。これは Python で非常に管理しやすいです。 

## テストケース```python
# helper: run solution on input string, return output string
import sys
import io
from bisect import bisect_right, insort
from collections import defaultdict

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    out = io.StringIO()

    input = sys.stdin.readline

    class SegmentTree:
        def __init__(self, n):
            self.n = n
            self.tree = [-1] * (4 * n)

        def update(self, node, l, r, idx, val):
            if l == r:
                self.tree[node] = val
                return

            mid = (l + r) // 2

            if idx <= mid:
                self.update(node * 2, l, mid, idx, val)
            else:
                self.update(node * 2 + 1, mid + 1, r, idx, val)

            self.tree[node] = max(
                self.tree[node * 2],
                self.tree[node * 2 + 1]
            )

        def query(self, node, l, r, ql, y):
            if r < ql or self.tree[node] <= y:
                return -1

            if l == r:
                return l

            mid = (l + r) // 2

            left = self.query(node * 2, l, mid, ql, y)
            if left != -1:
                return left

            return self.query(node * 2 + 1, mid + 1, r, ql, y)

    n = int(input())

    ops = []
    xs = []

    for _ in range(n):
        t, x, y = input().split()
        x = int(x)
        y = int(y)

        ops.append((t, x, y))
        xs.append(x)

    comp = sorted(set(xs))
    xid = {x: i for i, x in enumerate(comp)}

    ys = defaultdict(list)

    seg = SegmentTree(len(comp))

    ans = []

    for t, x, y in ops:
        idx = xid[x]

        if t == "add":
            insort(ys[idx], y)
            seg.update(1, 0, len(comp) - 1, idx, ys[idx][-1])

        elif t == "remove":
            pos = bisect_right(ys[idx], y) - 1
            ys[idx].pop(pos)

            val = ys[idx][-1] if ys[idx] else -1
            seg.update(1, 0, len(comp) - 1, idx, val)

        else:
            start = bisect_right(comp, x)

            if start >= len(comp):
                ans.append("-1")
                continue

            res = seg.query(1, 0, len(comp) - 1, start, y)

            if res == -1:
                ans.append("-1")
                continue

            pos = bisect_right(ys[res], y)
            ans.append(f"{comp[res]} {ys[res][pos]}")

    return "\n".join(ans)

# provided sample
assert run(
"""7
add 1 1
add 3 4
find 0 0
remove 1 1
find 0 0
add 1 1
find 0 0
"""
) == "1 1\n3 4\n1 1"

# minimum case
assert run(
"""1
find 0 0
"""
) == "-1"

# strict inequality check
assert run(
"""3
add 5 5
find 5 4
find 4 5
"""
) == "-1\n-1"

# multiple points with same x
assert run(
"""5
add 7 3
add 7 10
find 5 4
find 5 9
find 5 10
"""
) == "7 10\n7 10\n-1"

# deletion updates maximum correctly
assert run(
"""6
add 2 1
add 2 100
remove 2 100
find 1 50
find 1 0
find 0 0
"""
) == "-1\n2 1\n2 1"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | シングル`find`空集合で |`-1`| 空の構造体の処理 |
 | 等しい座標を持つクエリ |`-1`| 厳密な不等式の正しさ |
 | 同じ x に複数の y 値 | 適切な y の選択 | 1 つの x 内の正しいタイブレーク |
 | 最大の y を削除します | 更新された最大値 | セグメント ツリーのメンテナンスの正確性 |

 ## 特殊なケース

 厳密な不等式をもう一度考えてみましょう。```
3
add 5 5
find 5 4
find 4 5
```のために`find 5 4`の場合、検索は厳密に x=5 の後に開始されます。 これより大きな x は存在しないので、答えは次のようになります。`-1`。 

のために`find 4 5`、x=5 は有効ですが、y=5 は厳密には 5 より大きいわけではありません。セグメント ツリーには最大 y=5 が格納されますが、これはクエリしきい値を超えないため、クエリはセグメントを正しく拒否します。 

次に、重複する x 値を考えてみましょう。```
4
add 7 3
add 7 10
find 5 4
find 5 9
```セグメント ツリーには、x=7 に対して最大 y=10 が格納されます。 

のために`find 5 4`の場合、10 > 4 であるため、クエリは x=7 が実行可能であることを発見します。ソートされた y リスト内`[3, 10]`、二分探索では、4 より大きい最小の y として 10 が返されます。 

のために`find 5 9`、同じロジックで再び 10 が返されます。 

削除の正確性も微妙な点です。```
5
add 2 1
add 2 100
remove 2 100
find 1 50
find 1 0
```最初、セグメント ツリーは x=2 で最大 y=100 を格納します。 

削除後`(2,100)`、最大値は 1 になります。更新はツリーを介して上方に伝播します。 

のために`find 1 50`の場合、セグメント ツリーは、最大 y が 50 を超えないため、x=2 を拒否します。そのため、答えは次のようになります。`-1`。 

のために`find 1 0`、ポイント`(2,1)`それでもクエリを満たしており、正しく返されます。
