---
title: "CF 1883D - 恋に落ちて"
description: "追加と削除を 1 つずつ処理しながら、数直線上に複数のセグメントの動的なセットを維持することが求められます。 各操作の後、マルチセットに重複しない少なくとも 2 つのセグメントが含まれているかどうかを判断する必要があります。"
date: "2026-06-08T22:29:43+07:00"
tags: ["codeforces", "competitive-programming", "data-structures", "greedy"]
categories: ["algorithms"]
codeforces_contest: 1883
codeforces_index: "D"
codeforces_contest_name: "Codeforces Round 905 (Div. 3)"
rating: 1500
weight: 1883
solve_time_s: 135
verified: true
draft: false
---

[CF 1883D - 恋に落ちた](https://codeforces.com/problemset/problem/1883/D)

 **評価:** 1500
 **タグ:** データ構造、貪欲
 **解決時間:** 2 分 15 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 追加と削除を 1 つずつ処理しながら、数直線上に複数のセグメントの動的なセットを維持することが求められます。 各操作の後、マルチセットに重複しない少なくとも 2 つのセグメントが含まれているかどうかを判断する必要があります。 数直線上の点が両方のセグメントに属さない場合、セグメントのペアは交差しません。 

入力は最大で構成されます$10^5$操作。各操作は、エンドポイントによって定義されたセグメントを追加または削除します。$l$そして$r$、 どこ$1 \le l \le r \le 10^9$。 各操作の後、交差しないセグメントのペアが存在する場合は「YES」を出力し、存在しない場合は「NO」を出力する必要があります。 

なぜなら$q$と同じ大きさにすることができます$10^5$、各操作の後にセグメントのすべてのペアを検査するアルゴリズムでは、$O(q^2)$最悪の場合の時間、つまり$10^{10}$操作が遅すぎます。 これにより、すべてのセグメントを繰り返し再スキャンするのではなく、クエリに効率的に答えるために必要な情報のみを保持する必要があります。 

明らかではないエッジケースには、複数の同一セグメントが存在する状況が含まれます。 たとえば、マルチセットにセグメントのコピーが 2 つ含まれている場合、$(2, 4)$そして1つのセグメント$(5, 6)$、答えは依然として「YES」でなければなりません。$(2, 4)$そして$(5, 6)$たとえ交差しないとしても、$(2, 4)$それ自体は 2 回表示されます。 もう 1 つのエッジ ケースは、マルチセットに同一または重複するセグメントのみが含まれる場合です。 左の最大端点と右の最小端点を考慮しないアルゴリズムでは、交差しないセグメントが存在すると誤って結論付ける可能性があります。 

## アプローチ

 ブルートフォース手法は簡単です。追加または削除するたびに、セグメントのすべてのペアをチェックして、それらが交差するかどうかを確認します。 これは正しいですが、次のことが必要です$O(n^2)$最悪の場合は操作ごとに。 まで$10^5$セグメント、これにより最大で$10^{10}$実行不可能な操作。 

重要な洞察は、セグメントのすべてのペアを調べる必要はないということです。 セグメントのペアが交差していないかどうかを判断するには、最小の左端点を持つセグメントと最大の右端点を持つセグメントを知るだけで十分です。 最大の左端点を持つセグメントが最小の右端点を持つセグメントよりも大きい場合、これら 2 つのセグメントは交差しません。 

これを効率的に実装するために、すべてのセグメントにわたる現在の最小左エンドポイント、最大左エンドポイント、最小右エンドポイント、最大右エンドポイントと、重複セグメントを処理するためのそれらのカウントを維持します。 このアプローチでは、追加または削除のたびに、次の 4 つの値が更新されます。$O(\log n)$バランスのとれたデータ構造を使用するか、`SortedDict`/`SortedList`パイソンさんから`sortedcontainers`。 次に、比較することで、一定時間内に交差しないセグメントをチェックできます。`max_left`そして`min_right`。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(q * n) | O(n) | 遅すぎる |
 | 極端さを維持する | O(q log n) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 4 つのデータ構造を初期化します。1 つは左側のエンドポイント用のマルチセットまたはカウンター、もう 1 つは右側のエンドポイント用です。 追跡`min_left`、`max_left`、`min_right`、`max_right`重複の数を動的にカウントします。 
2. 各操作について:

 - 追加の場合は、セグメントの左端と右端をそれぞれのマルチセットに挿入します。 
- 削除の場合は、マルチセットからセグメントのエンドポイントを 1 つ削除します。 
3. 各操作の後、電流を計算します。`min_left`、`max_left`、`min_right`、 そして`max_right`。 
4. もし`max_left`>`min_right`、交差しないセグメントのペアが少なくとも 1 つ存在するため、「YES」を出力します。 それ以外の場合は「NO」を出力します。 
5. すべての操作に対して繰り返します。 

機能する理由: 極端なエンドポイントを追跡することで、重複しない可能性が最も高いセグメントを考慮することが保証されます。 不変条件とは、`min_right`は左から始まるセグメントの右端の点です。`max_left`は、右に伸びるセグメントの左端の点です。 もし`max_left`を超える`min_right`、セグメント間にギャップがあり、交差しないことが保証されます。 

## Python ソリューション```python
import sys
from collections import Counter
import bisect

input = sys.stdin.readline

class Multiset:
    def __init__(self):
        self.counter = Counter()
        self.sorted = []

    def add(self, x):
        if self.counter[x] == 0:
            bisect.insort(self.sorted, x)
        self.counter[x] += 1

    def remove(self, x):
        self.counter[x] -= 1
        if self.counter[x] == 0:
            idx = bisect.bisect_left(self.sorted, x)
            self.sorted.pop(idx)

    def min(self):
        return self.sorted[0]

    def max(self):
        return self.sorted[-1]

q = int(input())
lefts = Multiset()
rights = Multiset()
segments = Counter()

for _ in range(q):
    op, l, r = input().split()
    l = int(l)
    r = int(r)
    if op == '+':
        segments[(l,r)] += 1
        lefts.add(l)
        rights.add(r)
    else:
        segments[(l,r)] -= 1
        lefts.remove(l)
        rights.remove(r)
    if len(segments) < 2:
        print("NO")
        continue
    if lefts.max() > rights.min():
        print("YES")
    else:
        print("NO")
```の`Multiset`クラスは`Counter`重複の場合は、最小値と最大値を高速に取得するためのソートされたリストを使用します。 追加や削除を行うたびに、データの一貫性が保たれます。 これにより、すべてのセグメントのスキャンが回避され、`max_left`そして`min_right`比較は一定時間内にクエリに応答します。 

## 実用的な例

 サンプル入力の場合:```
+ 1 2
+ 3 4
+ 2 3
```| 操作 | セグメント | 最小右 | 最大左 | 出力 |
 | --- | --- | --- | --- | --- |
 | + 1 2 | {(1,2)} | 2 | 1 | いいえ |
 | + 3 4 | {(1,2),(3,4)} | 2 | 3 | はい |
 | + 2 3 | {(1,2),(3,4),(2,3)} | 2 | 3 | はい |

 このトレースは不変条件を確認します。`max_left`>`min_right`ギャップを示します。 

別の例:```
+ 2 2
+ 3 4
- 2 2
```| 操作 | セグメント | 最小右 | 最大左 | 出力 |
 | --- | --- | --- | --- | --- |
 | + 2 2 | {(2,2)} | 2 | 2 | いいえ |
 | + 3 4 | {(2,2),(3,4)} | 2 | 3 | はい |
 | - 2 2 | {(3,4)} | 4 | 3 | いいえ |

 セグメントの削除と極端な更新の正しい処理を示します。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(q log n) | それぞれの挿入/削除`Multiset`は O(log n) です。`bisect`。 最大 q = 10^5 では、10^5 * log 10^5 ≈ 2*10^6 の演算が行われます。 |
 | スペース | O(n) | Counter に格納される各セグメントと 2 つのマルチセット、最大で q 個のセグメントが存在します。 |

 これは、2 秒、256MB の制限内に問題なく収まります。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    out = io.StringIO()
    sys.stdout = out
    exec(open('solution.py').read())
    return out.getvalue().strip()

# provided sample
assert run("""12
+ 1 2
+ 3 4
+ 2 3
+ 2 2
+ 3 4
- 3 4
- 3 4
- 1 2
+ 3 4
- 2 2
- 2 3
- 3 4
""") == """NO
YES
YES
YES
YES
YES
NO
NO
YES
NO
NO
NO"""

# minimal input
assert run("""1
+ 1 1
""") == "NO", "single segment cannot have non-intersecting pair"

# identical segments
assert run("""3
+ 1 2
+ 1 2
+ 1 2
""") == "NO", "all identical, no non-intersecting pair"

# non-intersecting multiple
assert run("""4
+ 1 2
+ 3 4
+ 5 6
- 1 2
""") == """NO
YES
YES
YES""", "after removal, non-intersecting still exists"

# edge large numbers
assert run("""2
+ 1 1000000000
+ 500000000 1000000000
""") == """NO
NO""", "overlapping
```
