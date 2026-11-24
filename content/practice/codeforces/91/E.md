---
title: "CF 91E - イグルー超高層ビル"
description: "各セイウチは、時間の経過とともに高さが直線的に変化する超高層ビルを建設します。 セイウチ i は高さ a[i] から始まり、1 分ごとに b[i] 階分ずつ増加します。したがって、時刻 t での高さは次のようになります。 $$hi(t) = ai + bi cdot t$$ すべてのクエリ [l, r, t] について、その間隔内のインデックスを見つける必要があります。"
date: "2026-05-28T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "data-structures", "geometry"]
categories: ["algorithms"]
codeforces_contest: 91
codeforces_index: "E"
codeforces_contest_name: "Codeforces Beta Round 75 (Div. 1 Only)"
rating: 2500
weight: 91
solve_time_s: 164
verified: true
draft: false
---

[CF 91E - イグルー超高層ビル](https://codeforces.com/problemset/problem/91/E)

 **評価:** 2500
 **タグ:** データ構造、ジオメトリ
 **解決時間:** 2 分 44 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 各セイウチは、時間の経過とともに高さが直線的に変化する超高層ビルを建設します。 セイウチ`i`身長から始まる`a[i]`そして利益`b[i]`フロアは毎分なので、時間に応じて`t`その高さは次のとおりです。$$h_i(t) = a_i + b_i \cdot t$$あらゆるクエリに対して`[l, r, t]`、その区間内でその時点で超高層ビルが最も高いインデックスを見つけなければなりません`t`。 

単純な解釈は次のとおりです。すべての行の中で$$y = a_i + b_i t$$のインデックスに限定されます`[l, r]`で最大値のものを返します。`t`。 

制約により、二次関数は即座に除外されます。 と`n, q ≤ 10^5`、たとえ`O(n)`クエリごとのスキャンにはおおよその時間がかかります`10^{10}`最悪の場合の運用。 に近いものが必要です`O(log^2 n)`または`O(\sqrt n)`クエリごとに。 

この問題の最も危険な部分は、時間の経過とともに最大値が変化することです。 小さいのが得意なセイウチ`t`別のセイウチの方が傾斜が大きいため、後で状況が悪化する可能性があります。 

次の例を考えてみましょう。```
2 2
100 1
1 100
1 2 0
1 2 2
```時間に`0`、セイウチ`1`高さで勝つ`100`。 

時間に`2`、セイウチ`2`高さで勝つ`201`。 

現在の最大値のみを保存する構造や、固定順序でセイウチを比較する構造は、すぐに失敗します。 

もう一つの微妙なケースはネクタイです。```
2 1
5 3
5 3
1 2 10
```どちらのセイウチも永遠に同じ身長です。 どの答えも有効です。 等価線を誤っ​​て削除する不注意な凸包の実装により、誤ってすべての候補が削除されたり、間違った候補が保持されたりする可能性があります。 

もう 1 つのトラップは、同じ傾きを持つラインを処理することです。```
3 1
1 5
10 5
7 5
1 3 100
```すべての傾きは等しいため、最大の切片を持つセイウチが常に他のセイウチを圧倒します。 船体の建造中に等しい傾斜が慎重に処理されない場合、平行線は決して交差しないため、交差点での二分探索は無効になります。 

幾何学の視点が重要な観察です。 各セイウチは 1 つの行に対応し、各クエリはインデックスの部分配列上の 1 つの X 座標における最大の行を求めます。 

## アプローチ

 ブルートフォースソリューションは内部のすべてのセイウチを評価します`[l, r]`。 

1 つのクエリについて、次のように計算します。$$a_i + b_i \cdot t$$みんなのために`i`間隔内で最大値のインデックスを返します。 

これは問題の定義を直接実装しているため、これは正しいです。 問題はコストです。 クエリによって検査される場合があります`O(n)`したがって、全体の複雑さは次のようになります。`O(nq)`。 両方の値が等しい場合`10^5`、これは届きます`10^{10}`限界をはるかに超えた評価。 

重要な観察は、すべてのセイウチが平面内に線を定義していることです。 クエリは座標の上部エンベロープを要求します`t`。 

すべてのラインをグローバルに持っていれば、凸包トリック構造を構築できます。 難点は間隔制限`[l, r]`。 

これは、2 つのアイデアを組み合わせることを示唆しています。 

まず、インデックスに対してセグメント ツリーを使用します。 すべてのノードには、そのセグメントに属するすべてのラインが格納されます。 

次に、各ノード内で、クエリを最大化するための凸包を構築します。 

次にクエリ間隔`[l, r]`に分解される`O(log n)`セグメントツリーノード。 各ノードについて、そのハルを一度にクエリします`t`、それらの答えの中で最良のものは全体的な最大値です。 

なぜここでこれがうまくいくのでしょうか？ すべてのセイウチは唯一のものだから`O(log n)`セグメントツリーノード。 格納行数の合計は次のようになります。`O(n log n)`、管理可能です。 

ノード内でラインを勾配ごとに分類し、上部ハルを構築します。 クエリでは単一の X 座標のみが要求されるため、交点で二分検索を実行して最適な線を見つけることができます。`O(log n)`時間。 

最終的な複雑さは次のようになります。`O(log^2 n)`クエリごとに、十分な速度で`10^5`操作。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(nq) | お(1) | 遅すぎる |
 | 最適 | O(n log n + q log² n) | O(n log n) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. すべてのセイウチを線で表します。 

セイウチ用`i`、 定義する：$$f_i(t) = a_i + b_i t$$インデックスも保存します`i`クエリでは身長だけでなくセイウチの番号も返さなければならないためです。 
2. セイウチ インデックス上にセグメント ツリーを構築します。 

各ノードはインデックスのある間隔に対応します。 ノードには、その区間にセイウチが属するすべてのラインが保存されます。 
3. すべてのノードについて、傾きによってラインを並べ替えます。 

2 本の直線の傾きが同じ場合、切片が大きい方のみが重要になります。これは、すべての直線が他方を圧倒するためです。`t`。 
4. ノードの上部凸包を構築します。 

傾き順に線を挿入しながら、後ろの無駄な線を削除します。 

すでに行があると仮定します`A`そして`B`、挿入したい`C`。 の交差点の場合、`A`そして`B`の交差点の右側に発生します`B`そして`C`、 それから`B`決して最適ではないため、削除することができます。 
5. ハルをラインの順序付きリストとして保存します。 

傾きがソートされているので、増加に最適なライン`t`船体に沿って単調に移動します。 
6. クエリを処理する`[l, r, t]`。 

間隔を次のように分解します。`O(log n)`セグメントツリーノード。 
7. 各ノード ハルを個別にクエリします。 

固定の場合`t`、ハルを二分探索して、最大値を持つラインを見つけます。`t`。 
8. すべての候補の回答を比較します。 

返されたすべての行を一度に評価します`t`最高のセイウチ指数を維持します。 

### なぜ効果があるのか

 すべてのセイウチ ラインは、その間隔にインデックスが含まれるすべてのセグメント ツリー ノードに表示されます。 問い合わせるとき`[l, r]`、セグメント ツリー分解は、その区間内のセイウチだけを正確にカバーし、それ以外は何もカバーしません。 

1 つのノード内で、凸包は、ある時間値に対して最大になる可能性のあるラインを正確に格納します。 削除された行は、可能な限り他の行によって支配されます`t`, したがって、これを削除しても答えを変更することはできません。 

二分探索が機能するのは、最適な直線が船体上部に沿って次のように単調に変化するためです。`t`が増えます。 連続する船体のライン間の交差は順序付けられており、これはまさに船体の建造中に維持される特性です。 

クエリされたすべてのノードを結合すると、すべてのセイウチが考慮されることが保証されます。`[l, r]`、最良の候補の中から最大値を取ると正しい答えが得られます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

INF = 10**30

class Line:
    __slots__ = ("m", "b", "idx")

    def __init__(self, m, b, idx):
        self.m = m
        self.b = b
        self.idx = idx

    def value(self, x):
        return self.m * x + self.b

def bad(l1, l2, l3):
    return (l3.b - l1.b) * (l1.m - l2.m) <= (l2.b - l1.b) * (l1.m - l3.m)

class Hull:
    __slots__ = ("lines",)

    def __init__(self, arr):
        arr.sort(key=lambda x: (x.m, x.b))

        filtered = []
        for line in arr:
            if filtered and filtered[-1].m == line.m:
                if filtered[-1].b < line.b:
                    filtered[-1] = line
            else:
                filtered.append(line)

        hull = []
        for line in filtered:
            while len(hull) >= 2 and bad(hull[-2], hull[-1], line):
                hull.pop()
            hull.append(line)

        self.lines = hull

    def query(self, x):
        lines = self.lines

        lo, hi = 0, len(lines) - 1

        while lo < hi:
            mid = (lo + hi) // 2

            if lines[mid].value(x) <= lines[mid + 1].value(x):
                lo = mid + 1
            else:
                hi = mid

        best = lines[lo]
        return best.value(x), best.idx

class SegmentTree:
    def __init__(self, a, b):
        self.n = len(a)
        self.tree = [[] for _ in range(4 * self.n)]

        self.build(1, 0, self.n - 1, a, b)

    def build(self, node, l, r, a, b):
        if l == r:
            self.tree[node] = Hull([Line(b[l], a[l], l + 1)])
            return

        mid = (l + r) // 2

        self.build(node * 2, l, mid, a, b)
        self.build(node * 2 + 1, mid + 1, r, a, b)

        merged = []

        merged.extend(self.tree[node * 2].lines)
        merged.extend(self.tree[node * 2 + 1].lines)

        self.tree[node] = Hull(merged)

    def query(self, node, l, r, ql, qr, t):
        if ql <= l and r <= qr:
            return self.tree[node].query(t)

        mid = (l + r) // 2

        best_val = -INF
        best_idx = -1

        if ql <= mid:
            val, idx = self.query(node * 2, l, mid, ql, qr, t)
            if val > best_val:
                best_val = val
                best_idx = idx

        if qr > mid:
            val, idx = self.query(node * 2 + 1, mid + 1, r, ql, qr, t)
            if val > best_val:
                best_val = val
                best_idx = idx

        return best_val, best_idx

def solve():
    n, q = map(int, input().split())

    a = [0] * n
    b = [0] * n

    for i in range(n):
        a[i], b[i] = map(int, input().split())

    seg = SegmentTree(a, b)

    ans = []

    for _ in range(q):
        l, r, t = map(int, input().split())

        _, idx = seg.query(1, 0, n - 1, l - 1, r - 1, t)

        ans.append(str(idx))

    sys.stdout.write("\n".join(ans))

solve()
```の`Line`クラスには、傾き、切片、および元のセイウチ インデックスが格納されます。 式は次のように書かれます`m * x + b`、 どこ`m = b[i]`そして`b = a[i]`。 

の`bad`関数は、交差乗算を使用して凸包除去条件を実装します。 整数演算を使用すると、浮動小数点の精度の問題が回避されます。 

船体の構造は、まず勾配ごとに分類されます。 2 つの直線の傾きが同じ場合、小さい方が勝つことはできないため、大きい方の切片のみが生き残ります。 

内部の二分探索`Hull.query`上部ハルに沿った値が固定で単峰シーケンスを形成するという事実に依存します。`x`。 どちらの側に最適なものが含まれているかを判断するには、隣接する行を比較するだけで十分です。 

セグメント ツリーにはノードごとにハルが格納されます。 構築では、両方の子の船体ラインをマージし、親の船体を再構築します。 

実装の微妙な詳細の 1 つはインデックス付けです。 ツリーは内部で動作します`0`-ベースのインデックスですが、答えはオリジナルを使用する必要があります`1`-ベースのセイウチ番号なので、ラインストア`l + 1`。 

もう 1 つの重要な点は、クエリの比較では次のようなものだけが使用されることです。`>`の代わりに`>=`。 この問題では、有効な答えが同点になることが認められているため、どちらを選択しても機能します。 

## 実用的な例

 ### サンプル 1

 入力:```
5 4
4 1
3 5
6 2
3 5
6 5
1 5 2
1 3 5
1 1 0
1 5 0
```セイウチのラインは次のとおりです。 

| セイウチ | 式 |
 | --- | --- |
 | 1 | 4 + t |
 | 2 | 3+5t |
 | 3 | 6+2t |
 | 4 | 3+5t |
 | 5 | 6+5t |

 クエリ`[1, 5, 2]`。 

| セイウチ | t=2 での高さ |
 | --- | --- |
 | 1 | 6 |
 | 2 | 13 |
 | 3 | 10 |
 | 4 | 13 |
 | 5 | 16 |

 答えはセイウチです`5`。 

クエリ`[1, 3, 5]`。 

| セイウチ | t=5 での高さ |
 | --- | --- |
 | 1 | 9 |
 | 2 | 28 |
 | 3 | 16 |

 答えはセイウチです`2`。 

この例は、開始時の高さが低くても、最終的には大きな斜面がどのように優勢になるかを示しています。 

### カスタム例```
3 2
100 1
1 100
50 2
1 3 0
1 3 10
```時間に`0`:

 | セイウチ | 高さ |
 | --- | --- |
 | 1 | 100 |
 | 2 | 1 |
 | 3 | 50 |

 答え：`1`時間に`10`:

 | セイウチ | 高さ |
 | --- | --- |
 | 1 | 110 |
 | 2 | 1001 |
 | 3 | 70 |

 答え：`2`このトレースは、静的な順序付けが不可能な理由を示しています。 勾配が重要であるため、最大値は時間の経過とともに変化します。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n log n + q log² n) | 各クエリは O(log n) 個のノードにアクセスし、O(log n) 個のハル検索を実行します。 
| スペース | O(n log n) | すべての行は O(log n) セグメント ツリー ノードに表示されます。 

と`10^5`セイウチとクエリでは、およそ数百万の操作が実行されますが、これは Python の制限内に問題なく収まります。 

## テストケース```python
# helper: run solution on input string, return output string
import sys, io

INF = 10**30

class Line:
    __slots__ = ("m", "b", "idx")

    def __init__(self, m, b, idx):
        self.m = m
        self.b = b
        self.idx = idx

    def value(self, x):
        return self.m * x + self.b

def bad(l1, l2, l3):
    return (l3.b - l1.b) * (l1.m - l2.m) <= (l2.b - l1.b) * (l1.m - l3.m)

class Hull:
    def __init__(self, arr):
        arr.sort(key=lambda x: (x.m, x.b))

        filtered = []
        for line in arr:
            if filtered and filtered[-1].m == line.m:
                if filtered[-1].b < line.b:
                    filtered[-1] = line
            else:
                filtered.append(line)

        hull = []
        for line in filtered:
            while len(hull) >= 2 and bad(hull[-2], hull[-1], line):
                hull.pop()
            hull.append(line)

        self.lines = hull

    def query(self, x):
        lines = self.lines

        lo, hi = 0, len(lines) - 1

        while lo < hi:
            mid = (lo + hi) // 2

            if lines[mid].value(x) <= lines[mid + 1].value(x):
                lo = mid + 1
            else:
                hi = mid

        best = lines[lo]
        return best.value(x), best.idx

class SegmentTree:
    def __init__(self, a, b):
        self.n = len(a)
        self.tree = [[] for _ in range(4 * self.n)]

        self.build(1, 0, self.n - 1, a, b)

    def build(self, node, l, r, a, b):
        if l == r:
            self.tree[node] = Hull([Line(b[l], a[l], l + 1)])
            return

        mid = (l + r) // 2

        self.build(node * 2, l, mid, a, b)
        self.build(node * 2 + 1, mid + 1, r, a, b)

        merged = []
        merged.extend(self.tree[node * 2].lines)
        merged.extend(self.tree[node * 2 + 1].lines)

        self.tree[node] = Hull(merged)

    def query(self, node, l, r, ql, qr, t):
        if ql <= l and r <= qr:
            return self.tree[node].query(t)

        mid = (l + r) // 2

        best_val = -INF
        best_idx = -1

        if ql <= mid:
            val, idx = self.query(node * 2, l, mid, ql, qr, t)
            if val > best_val:
                best_val = val
                best_idx = idx

        if qr > mid:
            val, idx = self.query(node * 2 + 1, mid + 1, r, ql, qr, t)
            if val > best_val:
                best_val = val
                best_idx = idx

        return best_val, best_idx

def solve():
    input = sys.stdin.readline

    n, q = map(int, input().split())

    a = []
    b = []

    for _ in range(n):
        x, y = map(int, input().split())
        a.append(x)
        b.append(y)

    seg = SegmentTree(a, b)

    out = []

    for _ in range(q):
        l, r, t = map(int, input().split())

        _, idx = seg.query(1, 0, n - 1, l - 1, r - 1, t)

        out.append(str(idx))

    return "\n".join(out)

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    return solve()

# provided sample
assert run(
"""5 4
4 1
3 5
6 2
3 5
6 5
1 5 2
1 3 5
1 1 0
1 5 0
"""
) == "5\n2\n1\n5"

# minimum size
assert run(
"""1 1
7 3
1 1 10
"""
) == "1"

# equal slopes
assert run(
"""3 1
1 5
10 5
7 5
1 3 100
"""
) == "2"

# changing maximum over time
assert run(
"""2 2
100 1
1 100
1 2 0
1 2 10
"""
) == "1\n2"

# single-element range inside larger array
assert run(
"""4 2
1 1
2 2
3 3
4 4
2 2 100
4 4 0
"""
) == "2\n4"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 単一のセイウチ | 写真 単一のセイウチ 常にそれ自体 | 最小制約 |
 | 等しい傾き | インターセプトが最大の勝利 | 平行線の処理 |
 | 最大値の変更 | 時間の経過とともに異なる答え | 正しい船体遷移 |
 | 単一要素間隔 | 返される正確なインデックス | セグメントツリーの境界 |

 ## 特殊なケース

 再度同一の傾きを考えてみましょう。```
3 1
1 5
10 5
7 5
1 3 100
```すべての線は平行です。 船体建造中、切片のあるラインのみ`10`生き残る理由:$$10 + 5t > 7 + 5t$$そして$$10 + 5t > 1 + 5t$$あらゆる人にとって`t`。 

クエリは正しくセイウチを返します`2`。 

ここでネクタイを考えてみましょう。```
2 1
5 3
5 3
1 2 10
```両方の行は同一です。 船体はどちらを保持してもよい。 時間に`10`、両方とも次のように評価されます`35`したがって、ステートメントによれば、どちらのインデックスも有効です。 

最後に、ドミナンス スイッチについて考えてみましょう。```
2 2
100 1
1 100
1 2 0
1 2 2
```で`t = 0`:

 | セイウチ | 高さ |
 | --- | --- |
 | 1 | 100 |
 | 2 | 1 |

 で`t = 2`:

 | セイウチ | 高さ |
 | --- | --- |
 | 1 | 102 |
 | 2 | 201 |

 凸包は両方の線を格納します。これは、それぞれが一定の間隔で最適であるためです。`t`。 セイウチからの船体の遷移に対する二分探索`1`セイウチに`2`ちょうど交差点の直後です。
