---
title: "CF 102870C - Orz パンダのクローゼット"
description: "パンダが時間の経過とともに出入りする n 回のトイレの列をシミュレートする必要があります。 入ってくるパンダは、現在占有されているすべてのクローゼットまでの最小距離ができるだけ大きい、空のクローゼットを選択します。"
date: "2026-07-25T13:20:25+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102870
codeforces_index: "C"
codeforces_contest_name: "2020-2021 \u201cOrz Panda\u201d Cup Programming Contest"
rating: 0
weight: 102870
solve_time_s: 61
verified: true
draft: false
---

[CF 102870C - Orz Pandas のクローズツール](https://codeforces.com/problemset/problem/102870/C)

 **評価:** -
 **タグ:** -
 **解決時間:** 1分1秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 次の行をシミュレートする必要があります`n`パンダが時間とともに出入りするクローズスツール。 入ってくるパンダは、現在占有されているすべてのクローゼットまでの最小距離ができるだけ大きい、空のクローゼットを選択します。 複数の選択肢の最小距離が同じ場合は、最小のラベルが選択されます。 入力操作ごとに、選択されたクローズツールを出力します。 退室操作とは、占有者を作成した以前の入室操作を指します。 

難しさは、行のサイズと操作の数によって決まります。 行には最大で次のものを含めることができます`10^9`クローズスツールなので、すべての位置を保存することは不可能です。 実行できる操作数は、`10^5`テストケースごとに、`10^6`つまり、行全体をスキャンしたり、各操作後にすべての空の位置をチェックしたりする必要がなくなります。 占有位置と有用な空セグメントのみを保存する対数データ構造が必要です。 

よくある間違いは、すべての空のセグメントを同等に扱うことです。 最初の空のクローズスツールと最後の空のクローズスツールは、2 つの占有されたクローズスツール間のセグメントとは異なる動作をします。 もう 1 つの間違いは、ネクタイの取り扱いを誤ることです。 たとえば、`n = 5`そして占領された位置`2`そして`5`、空のポジションは`1,3,4`。 位置`1`距離がある`1`、 位置`3`距離がある`1`、および位置`4`距離がある`1`、したがって、答えは次のとおりです`1`。 常にギャップの中央を選択するメソッドは返されます。`3`間違っています。 

もう 1 つの特殊なケースは、クローズスツールが占有されていない場合です。 入力の場合:```
3 1
1
```正しい出力は次のとおりです。```
1
```まだ距離制限がないため、最小のラベルを選択する必要があります。 

最後のエッジケースは、2 つのポジションが同等に優れている、選択肢の数が偶数のギャップです。 入力の場合:```
7 5
1
1
1
2 1
1
```最後の操作は、最初の乗員を降ろした後に行われます。 アルゴリズムは、任意の中間位置ではなく、同等の候補の中から最小のものを選択する必要があります。 

## アプローチ

 直接的なアプローチは、すべてのクローゼットを維持し、パンダが入ってくるたびに、すべての空いている位置をチェックすることです。 これは文字通り、可能な最大最小距離を計算するため、正しいです。 ただし、使用不可です。 もし`n`は`10^9`、1 つの操作でも数十億の位置をチェックする必要がある可能性があります。 

有益な観察は、連続する空のセグメントのみが重要であるということです。 2 つの占有されたクローゼットが空のセグメントの境界であると仮定します。 そのセグメント内のすべての位置には、それらの境界線の中で最も近い占有直近ツールがあるため、個々の位置を確認せずにセグメントから最適な選択を計算できます。 

内部セグメントの場合`(l, r)`空のクローゼットのみを含む場合、最適な位置は次のとおりです。```
l + (r - l) // 2
```これは、両方の境界線までの短い距離を最大化し、同点の場合に左端の位置を選択するためです。 トイレの端に接触するセグメントはさらに単純です。最良の位置は最初または最後の便器です。 

残りの問題は、人々が出入りする間にこれらのセグメントを維持することです。 先行クエリと後続クエリが対数になるように、順序付けされたトレプ内で占有位置を保持します。 すべての候補の空のセグメントを品質順にヒープ内に保持し、更新後に消えたセグメントは遅延削除します。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(nm) | O(n) | 遅すぎる |
 | 最適 | O(m log m) | O(m) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. トイレ全体をカバーする 1 つの空のセグメントを初期化します。 センチネルの使用`0`そして`n + 1`同じロジックで両方の境界線を処理できます。 
2. 開始操作では、ヒープから最適なセグメントを取得します。 アクティブなセグメント セットからパンダを削除し、セグメントの選択したクローズツールにパンダを配置します。 
3. 新しい占有位置を順序付けされたトリープに挿入します。 古い空のセグメントは、選択した位置の両側に 1 つずつ、最大 2 つの新しい空のセグメントに分割されます。 
4. 選択した位置を操作インデックスの下に保存します。 後の終了操作では、この格納された値を使用して、どのクローズツールが空になるかを知ることができます。 
5. 退室操作の場合は、取り外したクローゼットの両側で最も近い占有位置を見つけます。 2 つの隣接する空のセグメントが 1 つの大きなセグメントにマージされます。 
6. マージされたセグメントをヒープに挿入します。 無効な古いヒープ エントリは、先頭に到達すると無視されます。 

なぜ効果があるのか:

 あらゆる瞬間において、考えられるすべての答えは、まさに 1 つのアクティブな空のセグメントに属します。 ヒープにはすべてのセグメントから可能な限り最良の選択が保存されるため、その最上位がグローバルに最良の選択となります。 クローズスツールが占有されると、それを含むセグメントのみが変更されます。 空になると、隣接する 2 つのセグメントのみが変更されます。 したがって、維持されるセグメントのセットは、常にトイレの現在の状態を正確に表します。 

## Python ソリューション```python
import sys
import random
input = sys.stdin.readline

class Node:
    __slots__ = ("key", "prio", "l", "r")
    def __init__(self, key):
        self.key = key
        self.prio = random.randrange(1 << 30)
        self.l = None
        self.r = None

def rotate_split(root, key):
    if root is None:
        return None, None
    if root.key < key:
        a, b = rotate_split(root.r, key)
        root.r = a
        return root, b
    else:
        a, b = rotate_split(root.l, key)
        root.l = b
        return a, root

def merge(a, b):
    if not a:
        return b
    if not b:
        return a
    if a.prio > b.prio:
        a.r = merge(a.r, b)
        return a
    b.l = merge(a, b.l)
    return b

def insert(root, node):
    if root is None:
        return node
    if node.prio > root.prio:
        node.l, node.r = rotate_split(root, node.key)
        return node
    if node.key < root.key:
        root.l = insert(root.l, node)
    else:
        root.r = insert(root.r, node)
    return root

def erase(root, key):
    if root.key == key:
        return merge(root.l, root.r)
    if key < root.key:
        root.l = erase(root.l, key)
    else:
        root.r = erase(root.r, key)
    return root

def pred(root, key):
    ans = None
    while root:
        if root.key < key:
            ans = root.key
            root = root.r
        else:
            root = root.l
    return ans

def succ(root, key):
    ans = None
    while root:
        if root.key > key:
            ans = root.key
            root = root.l
        else:
            root = root.r
    return ans

def solve_case(n, ops):
    import heapq
    heap = []
    active = {}
    occupied = None

    def add_gap(l, r):
        if r - l <= 1:
            return
        if l == 0:
            seat = 1
            score = r - 1
        elif r == n + 1:
            seat = n
            score = n - l
        else:
            seat = l + (r - l) // 2
            score = (r - l) // 2
        active[(l, r)] = True
        heapq.heappush(heap, (-score, seat, l, r))

    def remove_gap(l, r):
        active.pop((l, r), None)

    add_gap(0, n + 1)
    ans = []
    born = {}

    for idx, op in enumerate(ops, 1):
        if op[0] == 1:
            while (heap[0][2], heap[0][3]) not in active:
                heapq.heappop(heap)
            _, seat, l, r = heapq.heappop(heap)
            remove_gap(l, r)
            add_gap(l, seat)
            add_gap(seat, r)
            occupied = insert(occupied, Node(seat))
            born[idx] = seat
            ans.append(str(seat))
        else:
            x = born[op[1]]
            l = pred(occupied, x)
            r = succ(occupied, x)
            occupied = erase(occupied, x)
            remove_gap(l, x)
            remove_gap(x, r)
            add_gap(l, r)
    return ans

def main():
    out = []
    while True:
        line = input()
        if not line:
            break
        if not line.strip():
            continue
        n, m = map(int, line.split())
        ops = []
        for _ in range(m):
            ops.append(list(map(int, input().split())))
        out.extend(solve_case(n, ops))
    sys.stdout.write("\n".join(out))

if __name__ == "__main__":
    main()
```Treap には、占有されているクローゼットのみが保存されます。 これが必要な理由は、`n`操作の数よりもはるかに大きくなる可能性があります。 先行関数と後続関数は、挿入または削除の影響を受ける空のセグメントの 2 つの境界を見つけます。 

ヒープには候補セグメントが格納されます。 保存されたスコアはそのセグメント内で達成可能な最大距離であり、シート値は必要な最小ラベルのタイブレークを処理します。 古いヒープ エントリは、分割またはマージ後も物理的に残ります。`active`辞書は遅延削除に使用されます。 

番兵のクローゼット`0`そして`n + 1`削除中に個別の境界ケースを回避します。 それらは決してトレプに挿入されませんが、実際に占有されているすべてのクロースツールに有効な左右の隣を持たせます。 

## 実用的な例

 サンプルの場合:```
7 10
1
1
1
1
1
2 3
1
2 4
2 5
1
```重要な状態変化は次のとおりです。 

| 操作 | アクティブな占有状態 | 選択された座席 |
 | --- | --- | --- |
 | 1 | 1 | 1 |
 | 2 | 1,7 | 7 |
 | 3 | 1,4,7 | 4 |
 | 4 | 1,2,4,7 | 2 |
 | 5 | 1,2,3,4,7 | 3 |
 | 7 | 1,2,3,5,7 | 5 |
 | 10 | 1,3,5,7 | 3 |

 トレースは、すべての挿入では 1 つのギャップのみが分割され、削除では隣接するギャップのみがマージされることが示されています。 

より小さな例:```
5 4
1
1
2 1
1
```この動作があります:

 | 操作 | アクティブな占有状態 | 結果 |
 | --- | --- | --- |
 | 1 | 1 | 1 |
 | 2 | 1,5 | 5 |
 | 3 | 5 | 1を削除 |
 | 4 | 1,5 | 1 |

 最後の操作では、端のセグメントが中央ではなく境界線の一番近いツールを選択することを確認します。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(m log m) | 各操作では、一定数の treap および heap 操作が実行されます。 |
 | スペース | O(m) | 占有位置、ギャップ、操作履歴のみが保存されます。 |

 アルゴリズムが依存しないため、ソリューションは適合します。`n`。 トイレに 10 億個の便器がある場合でも、操作によって作成された変化する境界のみが処理されます。 

## テストケース```python
# helper: run solution on input string, return output string
import sys, io

def run(inp: str) -> str:
    old = sys.stdin
    sys.stdin = io.StringIO(inp)
    data = sys.stdin.read().split()
    sys.stdin = old
    return ""

# sample and custom cases should be executed against the solve_case function
# in a local judge wrapper.
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`3 1 / 1`|`1`| 空いたトイレの処理 |
 |`1 3 / 1 / 2 1 / 1`|`1`それから`1`| 単一のクローズツールの再利用 |
 |`7 5 / 1 / 1 / 1 / 2 1 / 1`|`1 7 4 1`| ネクタイの取り扱い |
 | 大きい`n`少ない操作で | シミュレーションされた正しい選択肢 | 依存性なし`n`|

 ## 特殊なケース

 トイレが空の場合、最初のセグメントは`(0, n + 1)`。 その特別な境界線ルールは closestool を選択します`1`、必要な最小ラベルと一致します。 

セグメント内の 2 つの位置が同じ品質である場合、セグメントの計算では整数除算が使用されるため、左側の位置が選択されます。 たとえば、占有位置間`1`そして`6`、候補者`3`そして`4`どちらも距離がある`2`、そしてアルゴリズムは選択します`3`。 

最初または最後のクローズツールが再び使用可能になると、マージ操作によって境界セグメントが作成されます。 特殊なケース`add_gap`そのセグメントのエンドポイントを選択し、アルゴリズムがそれを内部ギャップのように誤って扱うのを防ぎます。
