---
title: "CF 52C - 円形 RMQ"
description: "円状に配列された配列があります。 すべての操作は 2 つのインデックス間のセグメントに対して行われますが、セグメントは配列の末尾で折り返される場合があります。"
date: "2026-05-28T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "data-structures"]
categories: ["algorithms"]
codeforces_contest: 52
codeforces_index: "C"
codeforces_contest_name: "Codeforces Testing Round 1"
rating: 2200
weight: 52
solve_time_s: 138
verified: true
draft: false
---
[CF 52C - 循環 RMQ](https://codeforces.com/problemset/problem/52/C)

 **評価:** 2200
 **タグ:** データ構造
 **解決時間:** 2 分 18 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 円状に配列された配列があります。 すべての操作は 2 つのインデックス間のセグメントに対して行われますが、セグメントは配列の末尾で折り返される場合があります。 

操作は2つあります。 

最初の操作では、範囲内のすべての要素に値を追加します。 

2 番目の操作では、範囲内の最小値を求めます。 

左端が右端以下の場合、セグメントは通常の連続間隔です。 左端の方が大きい場合、セグメントは回り込みます。 たとえば、サイズ 5 の配列では、範囲は`[3,1]`インデックスを意味します`3,4,0,1`。 

入力により初期配列が与えられ、その後に最大 200,000 の演算が続きます。 すべてのクエリ操作で、要求された円形セグメントの最小値を出力する必要があります。 

この制約により、操作ごとの線形時間処理が即座に除外されます。 と`n = 200000`そして`m = 200000`、ブルートフォースソリューションは大まかに触れる可能性があります`4 * 10^10`最悪の場合、2 秒に収まる量をはるかに超えます。 平`O(sqrt n)`この規模の Python では操作ごとに不快な結果が生じる可能性があります。 更新とクエリの両方に対数時間に近い時間が必要です。 

操作も混ざってます。 更新では範囲が変更され、クエリでは範囲の最小値が求められます。 この組み合わせは、まさに遅延伝播が必要となる設定です。 ポイント更新のみ、または静的クエリのみをサポートする構造では十分ではありません。 

循環的な性質により、誤って処理されやすいいくつかの特殊なケースが発生します。 

次の入力を考えてみましょう。```
5
1 2 3 4 5
1
3 1
```クエリは空の範囲を意味しません。 インデックスという意味です`3,4,0,1`、したがって、答えは次のとおりです`1`。 を前提とした不注意な実装`l <= r`クラッシュするか、間違った結果が返される可能性があります。 

ネガティブなアップデートも重要です:```
3
5 5 5
2
0 2 -10
0 2
```更新後の配列は次のようになります。`[-5,-5,-5]`、したがって、答えは次のとおりです`-5`。 符号なし整数を使用する実装や最小値の誤った初期化はここで失敗します。 

ラップによって範囲が配列全体をカバーする場合、別の微妙なケースが発生します。```
4
10 20 30 40
2
2 1 -5
0 3
```アップデート`[2,1]`ラップされたセグメントは次のとおりであるため、すべてのインデックスに影響します。`2,3,0,1`。 最終的な配列は次のようになります`[5,15,25,35]`したがって、最小値は次のようになります。`5`。 円形範囲を誤って分割すると、ギャップが生じる可能性があります。 

## アプローチ

 最も直接的な解決策は、すべての操作を文字通りシミュレートすることです。 

更新の場合は、セグメント内のすべてのインデックスを反復処理し、値を追加します。 クエリの場合、セグメント内のすべてのインデックスを反復処理し、最小値を計算します。 

これが機能するのは、操作が簡単で、インデックスを手動でラップすることで循環間隔を処理できるためです。 問題はパフォーマンスです。 最悪の場合、すべての操作がすべてに影響を与える可能性があります。`n`要素。 両方とも`n`そして`m`に等しい`200000`、おおよそ次のようになります。```
200000 * 200000 = 4 * 10^10
```操作。 それは数百倍遅すぎます。 

重要な点は、どちらの操作も範囲ベースであるということです。 

すべての要素をすぐに更新する必要はありません。 セグメント全体が受信した場合`+v`、その情報を遅延して保存し、必要な場合にのみそれをさらに深くプッシュすることができます。 同時に、すべてのノードがそのセグメントの最小値を格納している場合、範囲最小クエリが自然になります。 

これは、遅延伝播を伴うセグメント ツリーに直接つながります。 

追加の複雑さは円形の間隔だけです。 ラップされたセグメント`[l,r]`と`l > r`は 2 つの通常の間隔に分割できます。```
[l, n-1]
[0, r]
```すべての円形範囲を最大 2 つの通常の範囲に縮小すると、残りは標準のセグメント ツリー処理になります。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(nm) | お(1) | 遅すぎる |
 | 最適 | O(m log n) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. アレイ上にセグメント ツリーを構築します。 

各ノードはセグメント内に最小値を格納します。 ルートには配列全体の最小値が格納されます。 
2. ツリーの横に遅延配列を追加します。`lazy[node]`まだ子に伝播する必要がある保留中の増分を保存します。 これにより、すべての要素にすぐにアクセスするのではなく、更新を遅らせることができます。 
3. 範囲更新の場合、セグメント ツリーを再帰的に処理します。 

現在のノードが完全に更新範囲内にある場合は、増分を直接加算します。`tree[node]`そしてそれを記録してください`lazy[node]`。 

そのセグメント内のすべての要素が同じ量だけ変化するため、これ以上下降しません。 
4. ノードが部分的にのみカバーされている場合は、続行する前にその保留中の遅延値を下にプッシュします。 

これにより、再帰がさらに深くなる前に、子の値が正しく保たれます。 
5. 子を更新した後、現在のノードを次のように再計算します。```
tree[node] = min(left child, right child)
```これにより、すべてのノードがそのセグメントの最小値を格納するという不変条件が維持されます。 
6. 範囲最小クエリの場合、関連するノードを再帰的に走査します。 

現在のセグメントが完全にクエリ範囲内にある場合は、保存されている最小値を直ちに返します。 
7. セグメントが部分的にのみ重複する場合は、最初に遅延値をプッシュし、次を使用して両方の子の結果を結合します。`min`。 
8. 円形の間隔を分割して処理します。 

もし`l <= r`、通常通り処理します。 

もし`l > r`、 プロセス：```
[l, n-1]
[0, r]
```クエリの場合は、2 つの部分の最小値を取得します。 

更新の場合は、両方の部分に増分を適用します。 

### なぜ効果があるのか

 セグメント ツリーは常に 1 つの不変条件を維持します。```
tree[node] = minimum value inside that segment,
including all applied and pending updates.
```遅延伝播では、遅延したすべての増分が、将来のクエリやより深い再帰でその情報が必要になる前に記録されるため、正確さが維持されます。 ノードが更新によって完全にカバーされると、保存される最小値が次のように増加します。`v`そのセグメント内のすべての要素が次のように均一に変化するため、これは有効です。`v`。 

ラップされた間隔を 2 つの通常の間隔に分割することも正しいです。これは、円形セグメントがこれら 2 つの互いに素な範囲を正確に結合したものであるためです。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

INF = 10**18

class SegmentTree:
    def __init__(self, arr):
        self.n = len(arr)
        self.tree = [0] * (4 * self.n)
        self.lazy = [0] * (4 * self.n)
        self.build(1, 0, self.n - 1, arr)

    def build(self, node, l, r, arr):
        if l == r:
            self.tree[node] = arr[l]
            return

        mid = (l + r) // 2

        self.build(node * 2, l, mid, arr)
        self.build(node * 2 + 1, mid + 1, r, arr)

        self.tree[node] = min(
            self.tree[node * 2],
            self.tree[node * 2 + 1]
        )

    def push(self, node):
        if self.lazy[node] != 0:
            val = self.lazy[node]

            left = node * 2
            right = node * 2 + 1

            self.tree[left] += val
            self.tree[right] += val

            self.lazy[left] += val
            self.lazy[right] += val

            self.lazy[node] = 0

    def update(self, node, l, r, ql, qr, val):
        if ql <= l and r <= qr:
            self.tree[node] += val
            self.lazy[node] += val
            return

        self.push(node)

        mid = (l + r) // 2

        if ql <= mid:
            self.update(node * 2, l, mid, ql, qr, val)

        if qr > mid:
            self.update(node * 2 + 1, mid + 1, r, ql, qr, val)

        self.tree[node] = min(
            self.tree[node * 2],
            self.tree[node * 2 + 1]
        )

    def query(self, node, l, r, ql, qr):
        if ql <= l and r <= qr:
            return self.tree[node]

        self.push(node)

        mid = (l + r) // 2
        ans = INF

        if ql <= mid:
            ans = min(ans, self.query(node * 2, l, mid, ql, qr))

        if qr > mid:
            ans = min(ans, self.query(node * 2 + 1, mid + 1, r, ql, qr))

        return ans

def solve():
    n = int(input())
    arr = list(map(int, input().split()))

    seg = SegmentTree(arr)

    m = int(input())

    out = []

    for _ in range(m):
        parts = list(map(int, input().split()))

        if len(parts) == 2:
            l, r = parts

            if l <= r:
                ans = seg.query(1, 0, n - 1, l, r)
            else:
                ans = min(
                    seg.query(1, 0, n - 1, l, n - 1),
                    seg.query(1, 0, n - 1, 0, r)
                )

            out.append(str(ans))

        else:
            l, r, v = parts

            if l <= r:
                seg.update(1, 0, n - 1, l, r, v)
            else:
                seg.update(1, 0, n - 1, l, n - 1, v)
                seg.update(1, 0, n - 1, 0, r, v)

    sys.stdout.write("\n".join(out))

solve()
```セグメント ツリーには、セグメントの最小値が格納されます。 構築フェーズでは、子の最小値が上向きに再帰的に結合されます。 

の`lazy`配列は重要な最適化です。 更新がセグメントを完全にカバーする場合、ノードを直接変更し、伝播を延期します。 これにより、すべての葉に降りるのが回避されます。 

の`push`関数は、より深い再帰の前に保留中の更新を子に転送します。 このステップを忘れることは、遅延伝播で最も一般的なバグです。 そうしないと、クエリで古い子の値が読み取られてしまいます。 

最小限のクエリには中立的な要素が必要であるため、クエリ関数は応答を非常に大きな値で初期化します。 使用する`0`ここでは、すべての値が正の場合は壊れます。 

循環間隔は、セグメント ツリー ロジックの外部で処理されます。 これにより、ツリー実装がクリーンな状態に保たれ、通常の間隔のみに焦点が当てられます。 

再帰境界は両端を含みます。 この選択は、どこでも、特に以下の場合に一貫性を保つ必要があります。```
mid = (l + r) // 2
```子セグメントの場合:```
[l, mid]
[mid + 1, r]
```インクルーシブ規約とハーフオープン規約を混合すると、off-by-one エラーの一般的な原因になります。 

## 実用的な例

 ### 例 1

 入力:```
4
1 2 3 4
4
3 0
3 0 -1
0 1
2 1
```初期配列:```
[1, 2, 3, 4]
```| 操作 | 影響を受ける範囲 | 操作後の配列 | クエリ結果 |
 | --- | --- | --- | --- |
 |`3 0`| インデックス`3,0`|`[1,2,3,4]`|`1`|
 |`3 0 -1`| インデックス`3,0`|`[0,2,3,3]`| - |
 |`0 1`| インデックス`0,1`|`[0,2,3,3]`|`0`|
 |`2 1`| インデックス`2,3,0,1`|`[0,2,3,3]`|`0`|

 最初のクエリは、ラップされたクエリを示しています。 この更新では、配列の両端もラップされ、正しく変更されます。 最後のクエリは、ラッピングを通じて配列全体をカバーします。 

### 例 2

 入力:```
5
10 20 30 40 50
5
1 3
4 1 -15
0 4
2 2 -100
0 4
```初期配列:```
[10,20,30,40,50]
```| 操作 | 影響を受ける範囲 | 操作後の配列 | クエリ結果 |
 | --- | --- | --- | --- |
 |`1 3`| インデックス`1,2,3`|`[10,20,30,40,50]`|`20`|
 |`4 1 -15`| インデックス`4,0,1`|`[-5,5,30,40,35]`| - |
 |`0 4`| 完全な配列 |`[-5,5,30,40,35]`|`-5`|
 |`2 2 -100`| 索引`2`|`[-5,5,-70,40,35]`| - |
 |`0 4`| 完全な配列 |`[-5,5,-70,40,35]`|`-70`|

 このトレースには、ラップされた更新とポイント更新の両方が表示されます。 セグメント ツリーは、同じ再帰機構を使用して両方を処理します。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(m log n) | 各更新とクエリは、最大で対数的に多くのセグメント ツリー ノードに触れます。 
| スペース | O(n) | セグメント ツリーと遅延配列には線形ストレージが必要です。 

と`n`そして`m`どちらも最大`200000`、対数演算は十分に高速です。 遅延伝播を備えたセグメント ツリーは、時間制限とメモリ制限内に快適に収まります。 

## テストケース```python
# helper: run solution on input string, return output string
import sys
import io

def run(inp: str) -> str:
    input_data = io.StringIO(inp)
    output_data = io.StringIO()

    input = input_data.readline

    INF = 10**18

    class SegmentTree:
        def __init__(self, arr):
            self.n = len(arr)
            self.tree = [0] * (4 * self.n)
            self.lazy = [0] * (4 * self.n)
            self.build(1, 0, self.n - 1, arr)

        def build(self, node, l, r, arr):
            if l == r:
                self.tree[node] = arr[l]
                return

            mid = (l + r) // 2

            self.build(node * 2, l, mid, arr)
            self.build(node * 2 + 1, mid + 1, r, arr)

            self.tree[node] = min(
                self.tree[node * 2],
                self.tree[node * 2 + 1]
            )

        def push(self, node):
            if self.lazy[node]:
                val = self.lazy[node]

                self.tree[node * 2] += val
                self.tree[node * 2 + 1] += val

                self.lazy[node * 2] += val
                self.lazy[node * 2 + 1] += val

                self.lazy[node] = 0

        def update(self, node, l, r, ql, qr, val):
            if ql <= l and r <= qr:
                self.tree[node] += val
                self.lazy[node] += val
                return

            self.push(node)

            mid = (l + r) // 2

            if ql <= mid:
                self.update(node * 2, l, mid, ql, qr, val)

            if qr > mid:
                self.update(node * 2 + 1, mid + 1, r, ql, qr, val)

            self.tree[node] = min(
                self.tree[node * 2],
                self.tree[node * 2 + 1]
            )

        def query(self, node, l, r, ql, qr):
            if ql <= l and r <= qr:
                return self.tree[node]

            self.push(node)

            mid = (l + r) // 2
            ans = INF

            if ql <= mid:
                ans = min(ans, self.query(node * 2, l, mid, ql, qr))

            if qr > mid:
                ans = min(ans, self.query(node * 2 + 1, mid + 1, r, ql, qr))

            return ans

    n = int(input())
    arr = list(map(int, input().split()))

    seg = SegmentTree(arr)

    m = int(input())

    out = []

    for _ in range(m):
        parts = list(map(int, input().split()))

        if len(parts) == 2:
            l, r = parts

            if l <= r:
                ans = seg.query(1, 0, n - 1, l, r)
            else:
                ans = min(
                    seg.query(1, 0, n - 1, l, n - 1),
                    seg.query(1, 0, n - 1, 0, r)
                )

            out.append(str(ans))

        else:
            l, r, v = parts

            if l <= r:
                seg.update(1, 0, n - 1, l, r, v)
            else:
                seg.update(1, 0, n - 1, l, n - 1, v)
                seg.update(1, 0, n - 1, 0, r, v)

    return "\n".join(out)

# provided samples
assert run(
"""4
1 2 3 4
4
3 0
3 0 -1
0 1
2 1
"""
) == "1\n0\n0", "sample 1"

# minimum size
assert run(
"""1
5
3
0 0
0 0 -3
0 0
"""
) == "5\n2", "single element"

# all equal values
assert run(
"""5
7 7 7 7 7
4
0 4
1 3 5
0 4
4 1
"""
) == "7\n7\n7", "all equal values"

# wrap-around update covering all elements
assert run(
"""4
10 20 30 40
2
2 1 -5
0 3
"""
) == "5", "full circular coverage"

# negative updates and queries
assert run(
"""3
5 5 5
3
0 2 -10
0 2
1 1
"""
) == "-5\n-5", "negative values"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 単一要素配列 |`5`、 それから`2`| 可能な限り最小のツリーを正しく処理する |
 | すべて等しい値 | いつも`7`| 更新が誤って無関係なセグメントに影響を与えることはありません。 
| ラップされたフルアレイの更新 |`5`| 循環分割ロジック |
 | ネガティブなアップデート |`-5`| 負の数の適切な最小値の処理 |

 ## 特殊なケース

 ラップされたクエリを空として扱ってはなりません。 

入力：```
5
1 2 3 4 5
1
3 1
```アルゴリズムが分割される`[3,1]`の中へ：```
[3,4]
[0,1]
```最初のクエリは次の結果を返します。`4`、2番目が戻ります`1`、最終的な答えは次のとおりです。```
min(4,1) = 1
```これは循環解釈と正確に一致します。 

ネガティブな更新は、遅延値を通じて正しく伝播される必要があります。 

入力：```
3
5 5 5
2
0 2 -10
0 2
```更新はルート セグメントを完全にカバーするため、ルートの最小値はすぐに次のようになります。`-5`、 そして`lazy[root]`店舗`-10`。 

後のクエリは、すべてのリーフを訪問する必要なく、ルートからすでに正しい最小値を読み取ります。 出力は次のとおりです。```
-5
```ラップされた更新は配列全体をカバーする場合があります。 

入力：```
4
10 20 30 40
2
2 1 -5
0 3
```アップデートは次のように分割されます。```
[2,3]
[0,1]
```これらを組み合わせると、すべてのインデックスが 1 回だけカバーされます。 

配列は次のようになります。```
[5,15,25,35]
```最終的な最小クエリは次のように返されます。`5`。 

このケースでは、ラップされた範囲が配列全体にまたがる場合でも、ラップされた範囲を分割すると正確さが保たれることが確認されています。
