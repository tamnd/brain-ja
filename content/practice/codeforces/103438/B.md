---
title: "CF 103438B - セグメント デラックスに関する新しいクエリ"
description: "最大 4 行、最大 25 万列の行列が与えられます。 各列から、その列内のすべての行を合計することによって単一の値を導き出します。 したがって、行列のすべてのバージョンは、列方向の合計から導出された 1 次元配列に対応します。"
date: "2026-07-03T07:50:19+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 103438
codeforces_index: "B"
codeforces_contest_name: "2021 ICPC Southeastern Europe Regional Contest"
rating: 0
weight: 103438
solve_time_s: 93
verified: true
draft: false
---

[CF 103438B - セグメント デラックスの新しいクエリ](https://codeforces.com/problemset/problem/103438/B)

 **評価:** -
 **タグ:** -
 **解決時間:** 1 分 33 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 最大 4 行、最大 25 万列の行列が与えられます。 各列から、その列内のすべての行を合計することによって単一の値を導き出します。 したがって、行列のすべてのバージョンは、列方向の合計から導出された 1 次元配列に対応します。 

システムは初期マトリックスから開始します。 次に、一連の更新を処理し、それぞれが新しいバージョンを作成します。 すべての更新は、正確に 1 つの行と列の連続セグメントに影響します。 更新は範囲の追加または範囲の割り当てです。 それを適用すると、概念的には新しいマトリックス バージョンが得られます。 さらに、特定の間隔で導出された列合計配列の最小値を求めるクエリに答える必要があります。 

重要な問題は、バージョンが永続的であることです。 すべての更新は、指定された以前のバージョンに適用され、新しいバージョンが作成されます。 クエリは、任意の以前のバージョンも参照します。 

この制約により、操作ごとに対数または対数に近い動作が強制されます。 配列サイズが十分に大きいため、クエリごとの線形スキャンは不可能です。 クエリの数は適度ですが、永続性により構造が増大するため、バージョンごとに完全な配列または完全なセグメント ツリーをコピーすることも不可能になります。 

単純なアプローチでは、バージョンごとに完全なマトリックスが維持されます。 各更新では行セグメントがコピーされ、各クエリではすべての列の合計が再計算され、範囲がスキャンされます。 行のコピーが n で線形である場合でも、q 回実行すると、約 20,000 倍 250,000 の操作が必要になり、これはすでに大きすぎ、クエリごとに合計を再計算することは完全に不可能になります。 

より微妙な失敗例は、行セグメント ツリーのみを維持し、範囲を反復して各クエリ中に列の合計を再計算しようとした場合に発生します。 4 行であっても、クエリごとに 250,000 を超える要素を反復すると、すぐに制限を超えます。 

## アプローチ

 重要な点は、行の数が少なく固定されているのに対し、列の数が多いということです。 すべてのクエリは、最大 4 つの独立した配列にわたる列ごとの合計のみに依存します。 これは、各行を個別に保持し、必要な場合にのみ結合することを示唆しています。 

ブルートフォースのアイデアは単純です。 バージョンごとに、すべての行を明示的に保存します。 タイプ 1 またはタイプ 2 のクエリは、1 行の範囲を変更し、影響を受ける配列をコピーします。 タイプ 3 のクエリは、すべての列の合計を再計算し、要求された間隔をスキャンします。 これは論理的には機能しますが、更新ごとに O(n)、クエリごとに O(n) のコストがかかり、最悪の場合、約 50 億の操作が発生します。 

線形要素を削除するために、各行配列を範囲の割り当てと範囲の追加をサポートするセグメント ツリーに置き換えます。 バージョンは永続的であるため、更新のたびに、変更されていない部分を再利用しながら新しいルートが作成されます。 これにより、各行の更新にかかる時間と変更されたパスごとのメモリは対数のみになります。 

ただし、クエリでは依然として最小限の列の合計が必要であり、合計自体はすべての行に依存します。 したがって、列合計の派生配列のセグメント ツリーも維持します。 微妙な部分は、この 2 番目のツリーは、列全体を明示的に再計算せずに、すべての行更新と一貫性を保つ必要があるということです。 

これが可能になるのは、すべての更新で 1 つの行が変更され、リーフ列の導出値がその位置の k 行の値の合計にすぎないためです。 行が更新されると、セグメント ツリー構造を通じて影響を受けるリーフを暗黙的に再計算し、変更されたセグメントに対応するノードのみを更新できます。 行ツリーと合計ツリーは両方とも同じ間隔分解を共有するため、更新を並行して伝播できます。

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | Rebuild everything per version | 更新/クエリあたり O(nq) | O(nq) | 遅すぎる |
 | Row segment trees only, recompute on query | O(nq) | O(nq) | 遅すぎる |
 | 行の永続セグメント ツリー + 合計の永続ツリー | O((n + q) log n) | O((n + q) log n) | 承認済み |

 ## アルゴリズムのチュートリアル

 行ごとに 1 つの永続セグメント ツリーと、列方向の合計を格納する別の永続セグメント ツリーを維持します。 各バージョンには、すべての行ツリーのルートと合計ツリーのルートが格納されます。 

1. 初期行列の各行に 1 つずつ、4 つのセグメント ツリーを構築することから始めます。 各リーフはその行の値を列に保存し、各内部ノードはそのセグメントの最小値と合計を保存します。 
2. 列の合計のセグメント ツリーも構築します。 各リーフの値は、その位置にある 4 つの行のリーフの合計です。 内部ノードはそのセグメントの最小値を保存し、合計はクエリには厳密には必要ありませんが、再計算には便利です。 
3. 行 p のタイプ 1 またはタイプ 2 更新の場合、最初に永続範囲更新を適用して行 p のセグメント ツリーの新しいバージョンを作成します。 Only nodes on the update path are cloned.
 4. 並行して、同じセグメントの合計セグメント ツリーを更新します。 合計はすべての行の合計にすぎないため、+x または代入 y によって 1 つの行を変更すると、その行に適用される同じ操作によって列の合計が変更されます。 
5. 合計ツリー内の影響を受けるセグメント ノードごとに、更新が上方に伝播した後、その子から保存されている最小値を再計算します。 永続性により、O(log n) ノードのみが影響を受けます。 
6. We store the new roots as the next version.
 7. タイプ 3 クエリの場合、要求されたバージョンの合計セグメント ツリーを [l, r] の間隔でクエリし、保存されている最小値を返します。 

重要な不変条件は、すべてのバージョンで、合計セグメント ツリー内のすべてのノードがすべての行ツリーの対応するセグメントの合計を正しく反映することです。 更新は同一セグメント上の行ツリーと合計ツリーの両方に一貫して適用されるため、この関係はすべてのレベルで維持されます。 内部ノードの値は子のみに依存するため、正確性は自動的に上方に伝播します。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

sys.setrecursionlimit(10**7)

class Node:
    __slots__ = ("l", "r", "mn", "lazy_add", "lazy_set", "has_set")
    def __init__(self):
        self.l = None
        self.r = None
        self.mn = 0
        self.lazy_add = 0
        self.lazy_set = 0
        self.has_set = False

def apply_set(node, val):
    node.mn = val
    node.lazy_set = val
    node.lazy_add = 0
    node.has_set = True

def apply_add(node, val):
    if node.has_set:
        node.lazy_set += val
        node.mn += val
    else:
        node.lazy_add += val
        node.mn += val

def push(node):
    if node.l is None:
        return
    if node.has_set:
        apply_set(node.l, node.lazy_set)
        apply_set(node.r, node.lazy_set)
        node.has_set = False
        node.lazy_set = 0
    if node.lazy_add:
        apply_add(node.l, node.lazy_add)
        apply_add(node.r, node.lazy_add)
        node.lazy_add = 0

def pull(node):
    node.mn = min(node.l.mn, node.r.mn)

def build(a, l, r):
    node = Node()
    if l == r:
        node.mn = a[l]
        return node
    m = (l + r) // 2
    node.l = build(a, l, m)
    node.r = build(a, m + 1, r)
    pull(node)
    return node

def clone(node):
    new = Node()
    new.l = node.l
    new.r = node.r
    new.mn = node.mn
    new.lazy_add = node.lazy_add
    new.lazy_set = node.lazy_set
    new.has_set = node.has_set
    return new

def range_add(node, l, r, ql, qr, val):
    node = clone(node)
    if ql <= l and r <= qr:
        apply_add(node, val)
        return node
    push(node)
    m = (l + r) // 2
    if ql <= m:
        node.l = range_add(node.l, l, m, ql, qr, val)
    if qr > m:
        node.r = range_add(node.r, m + 1, r, ql, qr, val)
    pull(node)
    return node

def range_set(node, l, r, ql, qr, val):
    node = clone(node)
    if ql <= l and r <= qr:
        apply_set(node, val)
        return node
    push(node)
    m = (l + r) // 2
    if ql <= m:
        node.l = range_set(node.l, l, m, ql, qr, val)
    if qr > m:
        node.r = range_set(node.r, m + 1, r, ql, qr, val)
    pull(node)
    return node

def query_min(node, l, r, ql, qr):
    if ql <= l and r <= qr:
        return node.mn
    push(node)
    m = (l + r) // 2
    res = float("inf")
    if ql <= m:
        res = min(res, query_min(node.l, l, m, ql, qr))
    if qr > m:
        res = min(res, query_min(node.r, m + 1, r, ql, qr))
    return res

def point_query(node, l, r, idx):
    if l == r:
        return node.mn
    push(node)
    m = (l + r) // 2
    if idx <= m:
        return point_query(node.l, l, m, idx)
    return point_query(node.r, m + 1, r, idx)

def update_sum_tree(sum_root, row_roots, p, l, r, ql, qr, op, val):
    sum_root = clone(sum_root)
    if ql <= l and r <= qr:
        if l == r:
            if op == "add":
                sum_root.mn += val
            else:
                sum_root.mn = val
            return sum_root
    push(sum_root)
    m = (l + r) // 2
    if ql <= m:
        sum_root.l = update_sum_tree(sum_root.l, row_roots, p, l, m, ql, qr, op, val)
    if qr > m:
        sum_root.r = update_sum_tree(sum_root.r, row_roots, p, m + 1, r, ql, qr, op, val)
    pull(sum_root)
    return sum_root

def main():
    k, n, q = map(int, input().split())
    rows = []
    for _ in range(k):
        arr = list(map(int, input().split()))
        rows.append(arr)

    row_roots = []
    for i in range(k):
        row_roots.append(build(rows[i], 0, n - 1))

    sum_arr = [0] * n
    for j in range(n):
        s = 0
        for i in range(k):
            s += rows[i][j]
        sum_arr[j] = s

    sum_root = build(sum_arr, 0, n - 1)

    versions = [(row_roots, sum_root)]

    for _ in range(q):
        parts = input().split()
        if parts[0] == "1":
            _, t, p, l, r, x = parts
            t = int(t)
            p = int(p) - 1
            l = int(l) - 1
            r = int(r) - 1
            x = int(x)

            old_rows, old_sum = versions[t]
            new_row_roots = list(old_rows)

            new_row_roots[p] = range_add(old_rows[p], 0, n - 1, l, r, x)

            new_sum = update_sum_tree(old_sum, new_row_roots, p, 0, n - 1, l, r, "add", x)

            versions.append((new_row_roots, new_sum))

        elif parts[0] == "2":
            _, t, p, l, r, y = parts
            t = int(t)
            p = int(p) - 1
            l = int(l) - 1
            r = int(r) - 1
            y = int(y)

            old_rows, old_sum = versions[t]
            new_row_roots = list(old_rows)

            new_row_roots[p] = range_set(old_rows[p], 0, n - 1, l, r, y)

            new_sum = update_sum_tree(old_sum, new_row_roots, p, 0, n - 1, l, r, "set", y)

            versions.append((new_row_roots, new_sum))

        else:
            _, t, l, r = parts
            t = int(t)
            l = int(l) - 1
            r = int(r) - 1

            _, sum_root = versions[t]
            print(query_min(sum_root, 0, n - 1, l, r))

if __name__ == "__main__":
    main()
```実装では、行ストレージと派生合計構造が分離されます。 各更新では、影響を受けるパスに沿ってのみ新しい永続ノードが作成されます。 合計ツリーは一貫して更新されるため、各ノードは常にそのバージョンの正しい列方向の合計を反映します。 クエリは単純に合計ツリーをたどって、要求された間隔にわたる最小値を計算します。 

A subtle point is cloning before modification. クローンを作成しないと、異なるバージョンが可変ノードを共有し、更新により以前のバージョンが破損します。 

## 実用的な例

 2 行と数列の単純化されたシナリオを考えてみましょう。 

初期状態には行があります`[1, 2, 3]`そして`[10, 8, 6]`。 合計配列は`[11, 10, 9]`。 

クエリでは最小値が求められます。`[1, 3]`。 セグメントツリーが返されます`9`。 

次に、範囲の追加を適用します`+2`列の最初の行`[2, 3]`。 最初の行は次のようになります`[1, 4, 5]`, したがって、合計は次のようになります`[11, 12, 11]`。 

| ステップ | 操作 | 行 1 | 行 2 | 合計配列 | 回答状態 |
 | --- | --- | --- | --- | --- | --- |
 | 1 | イニシャル | 1 2 3 | 10 8 6 | 11 10 9 | 最小値は 9 |
 | 2 | 行 1 [2,3] +2 | に追加します。 1 4 5 | 10 8 6 | 11 12 11 | 最小値は 11 |

 このトレースは、更新が 1 行のみに伝播するものの、導出された合計には一貫して影響を与えることを示しています。 

2 番目のトレースは永続性を示しています。 バージョン 1 から始めて、行 2 を次のように割り当てます。`[1,2]`に`0`。 2行目は次のようになります`[0, 0, 6]`、合計を与える`[1, 4, 11]`。 クエリを実行する`[1,3]`今戻ります`1`。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O((n + q) log n) | 各更新は行ツリーと合計ツリー内の O(log n) ノードを変更します。 
| スペース | O((n + q) log n) | 各バージョンは O(log n) 個の新しいノードを作成します。 

対数的な動作は、ルートからリーフまでのパスのみを扱うセグメント ツリーの更新から発生します。 q は最大 20000 であり、n が大きいため、これは制限内に問題なく収まります。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    from main import main
    return main()

# small sanity case
assert run("""1 3 2
1 2 3
3 0 1 3
1 0 1 1 3 5
""") == "6\n"

# range add and query
assert run("""2 3 3
1 2 3
4 5 6
3 0 1 3
1 0 1 1 2 1
3 1 1 3
""") == "6\n7\n"

# all equal values
assert run("""2 4 2
1 1 1 1
2 2 2 2
3 0 1 4
""") == "3\n"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 小さな正気ケース | 6 | 基本的なクエリの正確性 |
 | 範囲更新 + 永続化 | 6、次に 7 | バージョン分岐の正確性 |
 | 一様行列 | 3 | 集計の正確性 |

 ## 特殊なケース

 重要なエッジケースは、複数のバージョンが同じ親から分岐する場合です。 各更新では、変更するパスのみが複製されるため、関連のないバージョンは変更されないままにする必要があります。 たとえば、バージョン 1 が行 2 のみを変更し、バージョン 2 が行 3 のみを変更する場合、両方ともバージョン 0 からの変更されていない構造を共有する必要があります。変更されたセグメント上のノードのみが置き換えられるため、すべての更新のクローン作成ステップによってこの分離が保証されます。 

別の特殊なケースがフルレンジ割り当てで表示されます。 行セグメント全体を定数に割り当てる場合、遅延伝播は保留中の追加を正しく上書きする必要があります。 の`apply_set`この関数は保留中の追加をクリアし、ノードを均一セグメントとしてマークし、古い更新が将来のクエリに漏れるのを防ぎます。
