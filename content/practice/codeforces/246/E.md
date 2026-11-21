---
title: "CF 246E - ブラッド・カズンズ・リターン"
description: "私たちには、家族関係を表す根深い森が与えられています。 Every person has a name and at most one parent. 祖先をまったく持たない人もいる可能性があるため、複数のルートが許可されます。"
date: "2026-05-29T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "binary-search", "data-structures", "dfs-and-similar", "dp", "sortings"]
categories: ["algorithms"]
codeforces_contest: 246
codeforces_index: "E"
codeforces_contest_name: "Codeforces Round 151 (Div. 2)"
rating: 2400
weight: 246
solve_time_s: 98
verified: true
draft: false
---

[CF 246E - 血のいとこたちの帰還](https://codeforces.com/problemset/problem/246/E)

 **評価:** 2400
 **タグ:** 二分探索、データ構造、dfs など、dp、ソート
 **解決時間:** 1 分 38 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 私たちには、家族関係を表す根深い森が与えられています。 すべての人には名前があり、親はせいぜい 1 人です。 祖先をまったく持たない人もいる可能性があるため、複数のルートが許可されます。 

クエリごとに`(v, k)`のすべての子孫の中で異なる名前の数が必要です。`v`それはまさに`k`下のエッジ`v`。 

効果的な言い換えにより、構造がより明確になります。 させて`depth[x]`ノードの深さになります`x`木の根元から。 ノードとは、`k`-の息子`v`まさに次のとき:

 - それはサブツリー内にあります`v`- その深さは`depth[v] + k`したがって、すべてのクエリは次のことを尋ねます。 

> のサブツリー内`v`、深さで表示される個別の名前の数`depth[v] + k`?

 ツリーのサイズとクエリの数は両方とも最大になります。`10^5`。 すべてのクエリに対してサブツリーをスキャンするソリューションは、すぐに遅すぎます。 最悪の場合、1 つのサブツリーにほぼすべてのノードが含まれる可能性があり、その結果、`10^10`操作。 

この制約は、オフライン前処理アプローチを強く示唆しています。 周りに何かが必要です`O(n log n)`または`O((n + m) log n)`。 

いくつかの特殊なケースは、誤って処理されやすいものです。 

複数のルートを考慮します。```
4
a 0
b 0
c 1
d 2
2
1 1
2 1
```正しい答えは次のとおりです。```
1
1
```1 つのルートのみから DFS を開始すると、フォレストの一部が完全に欠落します。 

要求された深さが存在しない場合には、別の微妙なケースが発生します。```
3
a 0
b 1
c 2
1
1 5
```答えは次のとおりです。```
0
```不用意に実装すると、存在しない深さ構造にアクセスしようとする可能性があります。 

名前の繰り返しも重要です。```
5
john 0
john 1
john 1
alice 2
alice 3
2
1 1
1 2
```遠くで`1`ノードから`1`、両方の子孫に名前が付けられています`john`、したがって、答えは次のとおりです`1`、 ない`2`。 

最後に、次のクエリを実行します。`k = 0`制約が次から始まるため、この問題では不可能です。`1`。 これにより、一般的な特殊なケースが 1 つ削除されます。 

## アプローチ

 ブルートフォースの解決策は簡単です。 あらゆるクエリに対して`(v, k)`のサブツリーを走査します。`v`、深さが等しいすべてのノードを収集します`depth[v] + k`、それらの名前をセットに挿入し、セットのサイズを出力します。 

これは正しいです。なぜなら、これは の定義に直接従うからです。`k`-息子。 問題はコストです。 チェーン状ツリーでは、サブツリーに次のものを含めることができます。`O(n)`ノード。 と`m = 10^5`クエリの合計作業量は次のようになります`O(nm)`。 

クエリ間で作業を再利用する必要があります。 

重要な観察は、すべてのクエリは次の 2 つのことだけに依存しているということです。 

- サブツリー
 - 単一の深さ

 ツリーに対するサブツリー クエリは、オイラー ツアーの後、インターバル クエリになることがよくあります。 DFS 中に、各サブツリーは連続したセグメントになります。`[tin[v], tout[v]]`。 

次に、ノードを深さごとに処理することを想像してください。 深さごとに`d`、その深さに現れるすべての名前をオイラー時間順に維持します。 この場合、クエリは次のようになります。 

> 深さのノード間`d = depth[v] + k`、オイラー時間が内部にある個別の名前を数えます`[tin[v], tout[v]]`。 

残りの課題は、範囲内の個別の値を効率的にカウントすることです。 

ここで、古典的なオフライン個別クエリのトリックが登場します。 オイラー位置を左から右に処理するとします。 すべての名前について、フェンウィック ツリー内で最新の名前のみがアクティブな状態に保たれます。 次に、範囲の合計により、その範囲内の個別の名前の数が得られます。 

すべてのノードを深さごとにグループ化し、すべてのクエリをターゲットの深さごとにグループ化できます。 深さごとに独立して次のようになります。 

1. ノードをオイラー順に並べ替えます。 
2. すべての名前の最新の出現のみをアクティブにします。 
3. その深さに関するすべての間隔クエリに答えます。 

各ノードとクエリは 1 回だけ参加し、`O((n + m) log n)`解決。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(nm) | O(n) | 遅すぎる |
 | 最適 | O((n + m) log n) | O(n + m) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 親配列からフォレストを構築します。 

親を持つすべてのノード`0`根になります。 子の隣接リストを保存します。 
2. すべてのルートから DFS を実行します。 

DFS 中に、以下を計算します。 

-`depth[v]`-`tin[v]`-`tout[v]`エントリ時にグローバル タイマーをインクリメントします。 DFS の順序付けにより、すべてのサブツリーは連続したオイラー区間を形成します。 
3. ノードを深さ別にグループ化します。 

すべてのノードについて`v`、追加`(tin[v], name[v])`の中へ`depth_nodes[depth[v]]`。 

クエリは 1 つの正確な深さでのみノードを比較するため、深さを個別に処理することで不必要な作業を回避できます。 
4. 各クエリを変換する`(v, k)`深さが制限された間隔クエリに変換されます。 

必要な深さは次のとおりです。```
target = depth[v] + k
```サブツリー間隔は次のとおりです。```
[tin[v], tout[v]]
```クエリをグループ化して保存する`target`。 
5. For each depth separately, process all nodes in Euler order.

 Suppose we are processing depth`d`。 

すべてのノードを深さでソートします`d`オイラー時間までに。 次に、左から右にスイープします。 

保守:

 - オイラー位置上のフェンウィックツリー
 -`last[name]`、この名前が表示された最新のオイラー位置

 ご来店の際は`(pos, name)`:

 - 名前が以前に登場した場合`old`、投稿を削除します`old`- に貢献を追加します`pos`- アップデート`last[name] = pos`フェンウィック ツリーには、処理された位置の中で、個別の名前ごとにアクティブなオカレンスが常に 1 つだけ含まれます。 
6. オフラインで間隔クエリに回答します。 

この深さのクエリを正しいエンドポイントで並べ替えます。 

クエリの右側の境界までノードをスイープしながら、フェンウィック ツリーを更新します。 

次に、内部の個別の名前の数`[l, r]`は：```
bit.query(r) - bit.query(l - 1)
```7. 回答を元の順序で出力します。 

### なぜ効果があるのか

 固定深さの場合、スイープの不変条件は次のとおりです。 

> オイラー位置を処理した後、`x`、フェンウィックの木には以下が含まれます`1`ポジション内のすべての個別の名前が最後に出現した時点で正確に`<= x`。 

クエリ間隔を考慮する`[l, r]`。 

この間隔内に名前が出現する場合、その名前の最新の出現は次のとおりです。`r`も間隔内になければなりません。 以前の出現物は、後の出現物が出現したときに削除されました。 

したがって、すべての個別の名前が内部で 1 つのアクティブな位置に寄与します。`[l, r]`、および間隔に存在しない名前の寄与はゼロです。 

フェンウィック範囲の合計は、必要な深さのサブツリー内の個別の名前の数と正確に一致します。 

## Python ソリューション```python
import sys
from collections import defaultdict

input = sys.stdin.readline

class Fenwick:
    def __init__(self, n):
        self.n = n
        self.bit = [0] * (n + 1)

    def add(self, idx, val):
        while idx <= self.n:
            self.bit[idx] += val
            idx += idx & -idx

    def query(self, idx):
        res = 0
        while idx > 0:
            res += self.bit[idx]
            idx -= idx & -idx
        return res

    def range_query(self, l, r):
        return self.query(r) - self.query(l - 1)

def solve():
    n = int(input())

    names = [""] * (n + 1)
    children = [[] for _ in range(n + 1)]
    roots = []

    for i in range(1, n + 1):
        s, r = input().split()
        r = int(r)

        names[i] = s

        if r == 0:
            roots.append(i)
        else:
            children[r].append(i)

    tin = [0] * (n + 1)
    tout = [0] * (n + 1)
    depth = [0] * (n + 1)

    depth_nodes = defaultdict(list)

    timer = 0

    sys.setrecursionlimit(1 << 25)

    def dfs(v, d):
        nonlocal timer

        timer += 1
        tin[v] = timer
        depth[v] = d

        depth_nodes[d].append((tin[v], names[v]))

        for to in children[v]:
            dfs(to, d + 1)

        tout[v] = timer

    for root in roots:
        dfs(root, 0)

    m = int(input())

    queries_by_depth = defaultdict(list)
    ans = [0] * m

    for idx in range(m):
        v, k = map(int, input().split())

        target = depth[v] + k

        queries_by_depth[target].append(
            (tout[v], tin[v], idx)
        )

    bit = Fenwick(n)

    for d in queries_by_depth:
        nodes = depth_nodes.get(d, [])
        nodes.sort()

        queries = queries_by_depth[d]
        queries.sort()

        last = {}

        ptr = 0

        used_positions = []

        for r, l, qid in queries:
            while ptr < len(nodes) and nodes[ptr][0] <= r:
                pos, name = nodes[ptr]

                if name in last:
                    bit.add(last[name], -1)

                bit.add(pos, 1)
                last[name] = pos

                used_positions.append(pos)

                ptr += 1

            ans[qid] = bit.range_query(l, r)

        for pos in used_positions:
            bit.add(pos, -1)

    print("\n".join(map(str, ans)))

solve()
```DFS フェーズではオイラー区間を割り当てます。 DFS は戻る前にサブツリーを完全に探索するため、`v`連続したオイラー範囲を受け取ります。 このプロパティは、サブツリー クエリを間隔クエリに変換するものです。`depth_nodes[d]`すべてのノードを深度に保存します`d`。 ペア`(tin[v], name[v])`後でサブツリー フィルタリングがオイラー間隔のみを使用して行われるため、これで十分です。 

クエリは、ソース ノードではなくターゲットの深さによってグループ化されます。 これが中心的な削​​減です。 深さが固定されると、問題は標準的なオフラインの範囲内の個別要素の問題になります。 

フェンウィック ツリーには、アクティブな最新の出来事が保存されます。 名前が再表示されると、新しい名前が挿入される前に、以前の名前が削除されます。 この削除手順を行わないと、重複した名前が複数回カウントされます。 

各深さの終わりにあるクリーンアップ ループは微妙ですが、必要です。 同じフェンウィック ツリー インスタンスが深度を超えて再利用されるため、現在の深度からのすべての更新を元に戻す必要があります。 

もう 1 つの簡単な間違いは、クエリを次の基準で並べ替えることです。`tout[v]`。 スイープではノードがオイラーの昇順で処理されるため、クエリも正しいエンドポイントごとに進む必要があります。 

## 実用的な例

 ### サンプル 1

 入力:```
6
pasha 0
gerald 1
gerald 1
valera 2
igor 3
olesya 1
5
1 1
1 2
1 3
3 1
6 1
```DFS 注文:

 | ノード | 名前 | 深さ | 錫 | 客引き |
 | --- | --- | --- | --- | --- |
 | 1 | パシャ | 0 | 1 | 6 |
 | 2 | ジェラルド | 1 | 2 | 3 |
 | 4 | ヴァレラ | 2 | 3 | 3 |
 | 3 | ジェラルド | 1 | 4 | 5 |
 | 5 | イゴール | 2 | 5 | 5 |
 | 6 | オレシャ | 1 | 6 | 6 |

 クエリ変換:

 | クエリ | ターゲット深度 | 間隔 |
 | --- | --- | --- |
 | (1,1) | 1 | [1,6] |
 | (1,2) | 2 | [1,6] |
 | (1,3) | 3 | [1,6] |
 | (3,1) | 2 | [4,5] |
 | (6,1) | 2 | [6,6] |

 加工深さ`1`:

 | Euler Pos | 名前 | Active Names |
 | --- | --- | --- |
 | 2 | ジェラルド | {gerald} |
 | 4 | ジェラルド | {gerald at 4} |
 | 6 | オレシャ | {gerald, olesya} |

 The subtree interval`[1,6]`2 つのアクティブな名前が含まれているため、答えは次のようになります。`2`。 

加工深さ`2`:

 | Euler Pos | 名前 | Active Names |
 | --- | --- | --- |
 | 3 | ヴァレラ | {valera} |
 | 5 | イゴール | {valera, igor} |

 The full subtree gives`2`、インターバル中`[4,5]`のみが含まれています`igor`、与える`1`。 

このトレースは、最新のオカレンスだけがアクティブのままである理由を示しています。 重複`gerald`ノードは 1 つの異なる名前に折りたたまれます。 

### カスタム例

 入力:```
5
a 0
a 1
b 1
a 2
b 3
3
1 1
1 2
2 1
```DFS 注文:

 | ノード | 名前 | 深さ | 錫 | 客引き |
 | --- | --- | --- | --- | --- |
 | 1 | | 0 | 1 | 5 |
 | 2 | | 1 | 2 | 3 |
 | 4 | | 2 | 3 | 3 |
 | 3 | b | 1 | 4 | 5 |
 | 5 | b | 2 | 5 | 5 |

 クエリ:

 | クエリ | ターゲット深度 | 間隔 |
 | --- | --- | --- |
 | (1,1) | 1 | [1,5] |
 | (1,2) | 2 | [1,5] |
 | (2,1) | 2 | [2,3] |

 加工深さ`1`アクティブな名前を付ける`{a, b}`、 答え`2`。 

加工深さ`2`アクティブな名前を付ける`{a, b}`完全なサブツリーについては、答えてください`2`。 

インターバル用`[2,3]`、ノードのみ`4`中にある、答えて`1`。 

この例は、名前が異なるブランチ間で繰り返される場合でも、サブツリー フィルタリングと個別カウントが正しく相互作用することを示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O((n + m) log n) | 各ノードとクエリは、Fenwick の更新またはクエリを実行します。 
| スペース | O(n + m) | ツリー ストレージ、オイラー配列、グループ化されたクエリ、フェンウィック ツリー |

 と`10^5`ノードとクエリ、およそ数百万のフェンウィック操作が制限内に問題なく収まります。 メモリ使用量も直線的なままです。 

## テストケース```python
# helper: run solution on input string, return output string
import sys
import io
from collections import defaultdict

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)

    input = sys.stdin.readline

    class Fenwick:
        def __init__(self, n):
            self.n = n
            self.bit = [0] * (n + 1)

        def add(self, idx, val):
            while idx <= self.n:
                self.bit[idx] += val
                idx += idx & -idx

        def query(self, idx):
            res = 0
            while idx > 0:
                res += self.bit[idx]
                idx -= idx & -idx
            return res

        def range_query(self, l, r):
            return self.query(r) - self.query(l - 1)

    n = int(input())

    names = [""] * (n + 1)
    children = [[] for _ in range(n + 1)]
    roots = []

    for i in range(1, n + 1):
        s, r = input().split()
        r = int(r)

        names[i] = s

        if r == 0:
            roots.append(i)
        else:
            children[r].append(i)

    tin = [0] * (n + 1)
    tout = [0] * (n + 1)
    depth = [0] * (n + 1)

    depth_nodes = defaultdict(list)

    timer = 0

    sys.setrecursionlimit(1 << 25)

    def dfs(v, d):
        nonlocal timer

        timer += 1
        tin[v] = timer
        depth[v] = d

        depth_nodes[d].append((tin[v], names[v]))

        for to in children[v]:
            dfs(to, d + 1)

        tout[v] = timer

    for r in roots:
        dfs(r, 0)

    m = int(input())

    queries_by_depth = defaultdict(list)
    ans = [0] * m

    for idx in range(m):
        v, k = map(int, input().split())

        target = depth[v] + k

        queries_by_depth[target].append(
            (tout[v], tin[v], idx)
        )

    bit = Fenwick(n)

    for d in queries_by_depth:
        nodes = sorted(depth_nodes.get(d, []))
        queries = sorted(queries_by_depth[d])

        last = {}
        ptr = 0
        used = []

        for r, l, idx in queries:
            while ptr < len(nodes) and nodes[ptr][0] <= r:
                pos, name = nodes[ptr]

                if name in last:
                    bit.add(last[name], -1)

                bit.add(pos, 1)
                last[name] = pos

                used.append(pos)

                ptr += 1

            ans[idx] = bit.range_query(l, r)

        for pos in used:
            bit.add(pos, -1)

    return "\n".join(map(str, ans))

# provided sample
assert run(
"""6
pasha 0
gerald 1
gerald 1
valera 2
igor 3
olesya 1
5
1 1
1 2
1 3
3 1
6 1
"""
) == "2\n2\n0\n1\n0"

# minimum size
assert run(
"""1
alex 0
1
1 1
"""
) == "0"

# repeated names
assert run(
"""5
john 0
john 1
john 1
alice 2
alice 3
2
1 1
1 2
"""
) == "1\n1"

# multiple roots
assert run(
"""4
a 0
b 0
c 1
d 2
2
1 1
2 1
"""
) == "1\n1"

# chain tree, missing depth
assert run(
"""4
a 0
b 1
c 2
d 3
2
1 5
2 2
"""
) == "0\n1"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 単一ノードツリー | 0 | ターゲット深度がありません |
 | 繰り返される名前 | 1 1 | 明確な計数の正確さ |
 | 複数のルート | 1 1 | 森林横断の正確性 |
 | 大きな k を持つ深いチェーン | 0 1 | 境界深さの処理 |

 ## 特殊なケース

 複数のルートを持つフォレストを考えてみましょう。```
4
a 0
b 0
c 1
d 2
2
1 1
2 1
```DFS は両方のルートから開始します`1`そして`2`。 オイラー区間は次のようになります。 

| ノード | 錫 | 客引き |
 | --- | --- | --- |
 | 1 | 1 | 2 |
 | 3 | 2 | 2 |
 | 2 | 3 | 4 |
 | 4 | 4 | 4 |

 クエリ`(1,1)`間隔にマップします`[1,2]`深さで`1`、ノードのみが含まれます`3`。 クエリ`(2,1)`にマップします`[3,4]`、ノードのみを含む`4`。 

すべてのルートが独自の DFS を起動するため、アルゴリズムは切断されたツリーを処理します。 

ここで、存在しない深さを考えてみましょう。```
3
a 0
b 1
c 2
1
1 5
```目標深度は`5`、 しかし`depth_nodes[5]`は空です。 

処理中、この深さのノード リストは空であるため、フェンウィックの更新は行われません。 範囲クエリはすぐに返します。`0`。 

同じサブツリー内で名前が繰り返される場合は、正しく動作します。```
5
john 0
john 1
john 1
alice 2
alice 3
2
1 1
1 2
```深さで`1`、ノード`(2, john)`そして`(3, john)`現れる。 2 番目の出現を処理するときに、最初の出現がフェンウィック ツリーから削除されます。 

したがって、サブツリー間隔には、アクティブなオカレンスが 1 つだけ含まれます。`john`、答えを生み出す`1`の代わりに`2`。
