---
title: "CF 1641C - 匿名性は重要です"
description: "n 人の列があります。 すべての人は病気か健康かのどちらかです。 情報が到着すると、2 種類のステートメントを受け取ります。 x = 0 のステートメントは、区間 [l, r] 内の誰も病気ではないことを示します。 その範囲内のすべての人は間違いなく健康になります。"
date: "2026-06-10T04:21:22+07:00"
tags: ["codeforces", "competitive-programming", "binary-search", "brute-force", "data-structures", "dsu", "greedy", "sortings"]
categories: ["algorithms"]
codeforces_contest: 1641
codeforces_index: "C"
codeforces_contest_name: "Codeforces Round 773 (Div. 1)"
rating: 2200
weight: 1641
solve_time_s: 122
verified: true
draft: false
---

[CF 1641C - 匿名性は重要です](https://codeforces.com/problemset/problem/1641/C)

 **評価:** 2200
 **タグ:** バイナリ検索、ブルート フォース、データ構造、dsu、貪欲、ソート
 **解決時間:** 2 分 2 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 私たちは次のラインを持っています`n`人々。 すべての人は病気か健康かのどちらかです。 

情報が到着すると、2 種類のステートメントを受け取ります。 

との声明`x = 0`その間誰もいないと言う`[l, r]`病気です。 その範囲内のすべての人は間違いなく健康になります。 

との声明`x = 1`内部のどこかに少なくとも一人の病人がいると言っています`[l, r]`。 これは誰が病気であるかを特定するものではなく、間隔内に少なくとも 1 人の病気の人が含まれることを保証するだけです。 

これらのステートメントには、単一のポジションについて尋ねるクエリが混在しています。`j`。 私たちは次のように答えなければなりません。`NO`もし人`j`間違いなく健康です。`YES`もし人`j`間違いなく病気です。`N/A`両方の可能性がこれまでに確認されたすべての情報と依然として一致している場合。 

制約こそが本当の課題なのです。 両方`n`そして`q`と同じくらい大きい`2 · 10^5`。 更新中に一定の間隔をスキャンしたり、クエリ中に保存されているすべての制約をスキャンしたりするソリューションは、すぐに速度が非常に遅くなります。 二次アプローチは次のように実行されます。`4 · 10^10`操作。 

厄介なケースは、ポジティブな情報とネガティブな情報の相互作用から生じます。 

次のことがわかっているとします。```
0 1 2 0
0 1 3 1
```最初のステートメントはポジションを決定します`1`そして`2`健康。 2 番目のステートメントは、病気の人がいると述べています。`[1,3]`。 ポジションだけなので`3`可能性は残っていますが、位置の答えは`3`は`YES`。 

間隔を保存するだけの単純なソリューション`[1,3]`後の消去を組み込むと、この控除が見逃されます。 

もう 1 つの微妙なケースは、ネストされた正の間隔です。```
[2,10] contains a sick person
[4,6] contains a sick person
```2 番目のステートメントは厳密にはより強力です。 わかったら`[4,6]`病人が含まれている場合、間隔が長くても新しいことは何もありません。 両方の間隔を維持すると、冗長な作業が発生するだけです。 

この観察が、受け入れられた解決策の鍵となります。 

## アプローチ

 ブルートフォースのアイデアは単純です。 

あらゆる人のステータスを維持します。 を含むすべてのステートメントに対して、`x = 0`、間隔全体を正常とマークします。 を含むすべてのステートメントに対して、`x = 1`、間隔を保存します。 ポジションについて聞かれると`j`、保存されているすべての間隔を検査し、蓄積された情報が強制的であるかどうかを確認します。`j`病気になること。 

これは制約を直接モデル化するため正しいですが、絶望的に遅くなります。 と`2 · 10^5`演算と長さの間隔`2 · 10^5`、最悪の場合は数百億回の操作に達します。 

重要な観察は、健康な人が永続的に知られる唯一の立場であるということです。 健康であることが証明されると、その人は永久に検討対象から外される可能性があります。 

ステータスがまだ不明な人々のセットを維持することを想像してください。 

ポジティブな発言の場合`[l, r]`、私たちが本当に気にしているのは、その期間内の現在不明のポジションの中で、少なくとも1つは最終的に病気になるに違いないということだけです。 

ここで、一方が他方を含む 2 つの正の区間を考えてみましょう。 

もし`[4,6]`すでに病気の人を保証しているのなら、`[2,10]`新しい情報は追加しません。 小さい間隔を満たす割り当ては、自動的に大きい間隔も満たします。 

したがって、封じ込め下では最小限の陽性間隔を維持するだけで済みます。 

クエリでポジションについて尋ねられた場合`j`、 させて：`L`= 以前の未知の位置`j`

`R`= 次の未知の位置`j`それから`(L, R)`正確に 1 人の未知の人物が含まれています。`j`。 

完全に内部に格納された正の間隔が存在する場合`(L, R)`そして含まれています`j`、その区間には残りの候補が 1 つだけあります。 必要な病人は次のとおりです。`j`。 

これにより、問題は次の維持に変わります。 

まだ知られていないダイナミックなポジションのセット。 

左のエンドポイント順に並べられた非冗長な正の間隔のコレクション。 

どちらも対数時間で処理できます。 

承認されたソリューションは、DSU のような「ネクスト アライブ」構造を使用して、健全な位置を効率的に削除し、最小の正の間隔の順序付きマップを使用します。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(nq) | O(n + q) | 遅すぎる |
 | 最適 | O((n + q) log n) | O(n + q) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. ステータスがまだ不明な一連の役職を維持します。 

最初はすべてのポジションがこのセットに属します。 
2. ステートメントについて`[l, r]`と`x = 0`、その区間内のすべての位置を未知のセットから削除します。 

その人たちは今、間違いなく健康です。 
3. DSU の「ネクスト ポインター」構造を使用して、既に削除された位置をスキップします。 

各ポジションは最大 1 回削除されるため、すべての削除は全体としてほぼ直線的になります。 
4. 声明について`[l, r]`と`x = 1`、区間をポジティブ制約の順序付きマップに挿入します。 
5. 既存の保存された間隔が完全に含まれている場合`[l, r]`の場合、新しい間隔は冗長なので無視できます。 

間隔が狭いほど厳密には強力です。 
6. それ以外の場合は挿入します`[l, r]`そして、それを含む以前に保存された間隔を削除します。 

このステップの後、保存された間隔には別の間隔が含まれなくなります。 
7. 位置に関するお問い合わせについて`j`、まず次のことを確認してください。`j`まだ未知のセットに属しています。 

そうでない場合は、答えてください`NO`。 
8. しましょう`L`以前の未知の位置であり、`R`次の未知の位置。 

未知の位置は順番に保存されるため、これらの近傍は対数時間で見つけることができます。 
9. 左端が次の値より大きい、最初に保存された間隔を検索します。`L`。 
10. このような間隔が開始される場合、またはそれより前に`j`そしてその前に終わる`R`、 答え`YES`。 

その間隔は完全にブロック内にあり、その唯一の未知の位置は`j`したがって、その必要な病人は次のとおりである必要があります。`j`。 
11. それ以外の場合は回答してください`N/A`。 

### なぜ効果があるのか

 未知のセットには常に、健全であることが証明されていないポジションが正確に含まれます。 

間隔構造には、最小限のポジティブ制約のみが保存されます。 小さい間隔を含む大きい間隔を削除しても、有効な割り当てのセットが変更されることはありません。小さい間隔を満たすと、自動的に大きい間隔も満たされるためです。 

問い合わせられた位置について`j`、近所の人たち`L`そして`R`唯一未知の位置を持つ最大セグメントを区切る`j`。 そのセグメント内の他のすべての位置は、すでに正常であることがわかっています。 

正の区間が完全に内側にある場合`(L, R)`そして含まれています`j`の場合、その間隔には、病気の可能性のある候補が 1 人だけ残っています。 したがって、`j`病気に違いない。 

そのような間隔が存在しない場合は、常に有効な代入を構築できます。`j`は健康なので、病気を強いるには情報が不十分です。 

これら 2 つの事実は、必要な答えと正確に一致します。 

## Python ソリューション```python
import sys
from bisect import bisect_left, bisect_right
import random

input = sys.stdin.readline

class Fenwick:
    def __init__(self, n):
        self.n = n
        self.bit = [0] * (n + 1)

    def add(self, idx, val):
        n = self.n
        while idx <= n:
            self.bit[idx] += val
            idx += idx & -idx

    def sum(self, idx):
        res = 0
        while idx:
            res += self.bit[idx]
            idx -= idx & -idx
        return res

    def kth(self, k):
        idx = 0
        bitmask = 1 << (self.n.bit_length())
        while bitmask:
            nxt = idx + bitmask
            if nxt <= self.n and self.bit[nxt] < k:
                k -= self.bit[nxt]
                idx = nxt
            bitmask >>= 1
        return idx + 1

class DSU:
    def __init__(self, n):
        self.p = list(range(n + 3))

    def find(self, x):
        while self.p[x] != x:
            self.p[x] = self.p[self.p[x]]
            x = self.p[x]
        return x

    def erase(self, x):
        self.p[x] = self.find(x + 1)

class Node:
    __slots__ = ("l", "r", "prio", "left", "right")

    def __init__(self, l, r):
        self.l = l
        self.r = r
        self.prio = random.randint(1, 1 << 30)
        self.left = None
        self.right = None

def split(root, key):
    if not root:
        return (None, None)
    if root.l < key:
        a, b = split(root.right, key)
        root.right = a
        return (root, b)
    a, b = split(root.left, key)
    root.left = b
    return (a, root)

def merge(a, b):
    if not a or not b:
        return a or b
    if a.prio > b.prio:
        a.right = merge(a.right, b)
        return a
    b.left = merge(a, b.left)
    return b

def insert(root, node):
    if not root:
        return node
    if node.prio > root.prio:
        node.left, node.right = split(root, node.l)
        return node
    if node.l < root.l:
        root.left = insert(root.left, node)
    else:
        root.right = insert(root.right, node)
    return root

def lower_bound(root, key):
    ans = None
    while root:
        if root.l >= key:
            ans = root
            root = root.left
        else:
            root = root.right
    return ans

def predecessor(root, key):
    ans = None
    while root:
        if root.l < key:
            ans = root
            root = root.right
        else:
            root = root.left
    return ans

def erase_key(root, key):
    if not root:
        return None
    if root.l == key:
        return merge(root.left, root.right)
    if key < root.l:
        root.left = erase_key(root.left, key)
    else:
        root.right = erase_key(root.right, key)
    return root

n, q = map(int, input().split())

fw = Fenwick(n + 2)
for i in range(n + 2):
    fw.add(i + 1, 1)

alive = [True] * (n + 2)

dsu = DSU(n + 2)
root = None

out = []

for _ in range(q):
    data = list(map(int, input().split()))

    if data[0] == 0:
        l, r, x = data[1], data[2], data[3]

        if x == 0:
            p = dsu.find(l)
            while p <= r:
                if alive[p]:
                    alive[p] = False
                    fw.add(p + 1, -1)
                dsu.erase(p)
                p = dsu.find(p)

        else:
            cur = lower_bound(root, l)

            if cur and cur.r <= r:
                continue

            root = insert(root, Node(l, r))

            while True:
                prv = predecessor(root, l)
                if prv is None or prv.r < r:
                    break
                root = erase_key(root, prv.l)

    else:
        j = data[1]

        if not alive[j]:
            out.append("NO")
            continue

        rank = fw.sum(j + 1)

        left_pos = fw.kth(rank - 1) - 1
        right_pos = fw.kth(rank + 1) - 1

        cur = lower_bound(root, left_pos + 1)

        if cur and cur.l <= j and cur.r < right_pos:
            out.append("YES")
        else:
            out.append("N/A")

sys.stdout.write("\n".join(out))
```DSU は、間隔の削除のみを担当します。 ポジションが一度健全になると元に戻ることはないため、すべてのインデックスは最大 1 回削除されます。 

フェンウィック ツリーは、未知の位置の順序付けされたセットを維持します。 これにより、注文統計を通じて、クエリされたポジションの先行者と後続者を見つけることができます。 

treap は、左のエンドポイントによって順序付けられた最小の正の間隔を保存します。 挿入により、より大きな冗長な間隔が削除され、保存された間隔には別の間隔が含まれないという不変条件が維持されます。 

クエリ ロジックは証明に直接続きます。 位置が唯一不明なギャップ内にインターバルが閉じ込められている場合、`j`、 それから`j`その間隔までに必要な病人でなければなりません。 

## 実用的な例

 ### サンプル 1

 入力:```
6 9
0 4 5 0
1 5
1 6
0 4 6 1
1 6
0 2 5 1
0 2 2 0
1 3
1 2
```| 操作 | 不明な位置 | 保存された正の間隔 | 答え |
 | --- | --- | --- | --- |
 | 0 4 5 0 | {1,2,3,6} | {} | |
 | 1 5 | {1,2,3,6} | {} | いいえ |
 | 1 6 | {1,2,3,6} | {} | 該当なし |
 | 0 4 6 1 | {1,2,3,6} | {[4,6]} | |
 | 1 6 | {1,2,3,6} | {[4,6]} | はい |
 | 0 2 5 1 | {1,2,3,6} | {[2,5]} | |
 | 0 2 2 0 | {1,3,6} | {[2,5]} | |
 | 1 3 | {1,3,6} | {[2,5]} | はい |
 | 1 2 | {1,3,6} | {[2,5]} | いいえ |

 重要な瞬間はポジション獲得後`2`健康になります。 間隔`[2,5]`まだ病人とその地位が必要だ`3`その領域内に残っている唯一の候補です。 

### カスタム例```
5 4
0 1 5 1
1 3
0 1 4 0
1 5
```| 操作 | 不明な位置 | 保存された正の間隔 | 答え |
 | --- | --- | --- | --- |
 | 0 1 5 1 | {1,2,3,4,5} | {[1,5]} | |
 | 1 3 | {1,2,3,4,5} | {[1,5]} | 該当なし |
 | 0 1 4 0 | {5} | {[1,5]} | |
 | 1 5 | {5} | {[1,5]} | はい |

 最初は、どの位置でも正の間隔を満たすことができます。 ポジション後`1..4`健康になる、位置だけ`5`残っています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O((n + q) log n) | 各削除は 1 回行われ、すべてのツリー操作は対数的です。 
| スペース | O(n + q) | DSU、フェンウィック ツリー、および保存された間隔 |

 と`n, q ≤ 2 · 10^5`、対数更新とクエリは簡単に制限内に収まります。 

## テストケース```python
# helper: run solution on input string, return output string
import sys, io

def run(inp: str) -> str:
    # call solve() here in a real test harness
    pass

# sample 1
assert run(
"""6 9
0 4 5 0
1 5
1 6
0 4 6 1
1 6
0 2 5 1
0 2 2 0
1 3
1 2
"""
) == """NO
N/A
YES
YES
NO"""

# minimum size
assert run(
"""1 2
1 1
1 1
"""
) == """N/A
N/A"""

# single forced sick person
assert run(
"""3 3
0 1 2 0
0 1 3 1
1 3
"""
) == "YES"

# everybody healthy
assert run(
"""4 3
0 1 4 0
1 1
1 4
"""
) == """NO
NO"""

# boundary positions
assert run(
"""5 4
0 2 4 0
0 1 5 1
1 1
1 5
"""
) == """N/A
N/A"""
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 最小サイズのケース | 該当なし | 情報はありません |
 | 強制生存者 | はい | 正の区間は 1 つの候補に集約されます。 
| すべて健康 | いいえ | 範囲削除ロジック |
 | 境界位置 | 該当なし | 前世代/後世代のニアエンド対応 |

 ## 特殊なケース

 次のことを考慮してください。```
3 3
0 1 2 0
0 1 3 1
1 3
```最初のステートメントの後の位置`1`そして`2`are removed from the unknown set. 間隔`[1,3]`まだ病人が必要です。 残る唯一の候補は、`3`したがって、アルゴリズムは、間隔が完全にギャップの内側にあることを検出します。そのギャップの唯一の未知の位置は次のとおりです。`3`そして答えます`YES`。 

ここで、ネストされた間隔を考えてみましょう。```
5 3
0 1 5 1
0 2 4 1
1 3
```間隔`[2,4]`よりも強いです`[1,5]`。 アルゴリズムは最小限の間隔のみを維持します。 より大きな間隔を削除しても、有効な割り当ては変更されません。これがまさに、包含プルーニングが正しい理由です。 

最後に、次のことを考慮してください。```
4 2
0 1 4 0
1 2
```位置`2`健全な間隔が到来すると、すぐに削除されます。 クエリはポジティブ制約を調べず、直接答えを返します。`NO`、これが唯一可能な結果です。
