---
title: "CF 198E - 魅力的なストーリー"
description: "すべてのグリッパーは空間内の固定点に配置されます。 クワーティの船も直りました。 2 つの条件が同時に成立する場合、グリッパーは別のグリッパーを船内に引き込むことができます。"
date: "2026-06-03T09:52:49+07:00"
tags: ["codeforces", "competitive-programming", "binary-search", "data-structures", "sortings"]
categories: ["algorithms"]
codeforces_contest: 198
codeforces_index: "E"
codeforces_contest_name: "Codeforces Round 125 (Div. 1)"
rating: 2400
weight: 198
solve_time_s: 86
verified: true
draft: false
---

[CF 198E - 魅力的なストーリー](https://codeforces.com/problemset/problem/198/E)

 **評価:** 2400
 **タグ:** 二分探索、データ構造、並べ替え
 **解決時間:** 1 分 26 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 すべてのグリッパーは空間内の固定点に配置されます。 クワーティの船も直りました。 

2 つの条件が同時に成立する場合、グリッパーは別のグリッパーを船内に引き込むことができます。 ターゲット グリッパーの質量はアクティブ グリッパーの出力を超えてはならず、ターゲット グリッパーは船からアクティブ グリッパーの動作半径内になければなりません。 

2 番目の条件は重要な観察です。 船は決して動かず、散らばったすべてのグリッパーは回収されるまで元の座標に留まります。 収集されたグリッパーがアクティブになると、引き続き船から動作します。 つまり、重要な唯一の距離は、船から各グリッパーまでの距離です。 

すべてのグリッパーについて事前計算できます$$d_i = (x_i-x)^2 + (y_i-y)^2.$$半径のあるグリッパー$r$満足のいくグリッパーに正確に到達できます$$d_i \le r^2.$$グリッパーを収集した後、新しいペアを取得します$(p_i,r_i)$、より多くのグリッパーを収集できる可能性があります。 到達可能なセット全体のサイズが必要です。 

制約$n \le 250000$二次関数はすべて除外します。 平$O(n\sqrt n)$would be uncomfortable at this scale. 周りに何かが必要です$O(n \log n)$。 

微妙な点は、1 つのグリッパーを収集すると、以前は重すぎた別のグリッパーのロックが解除される可能性があり、新しく収集されたグリッパーによりさらに多くのグリッパーのロックが解除される可能性があることです。 これは到達可能性の問題であり、ワンパス フィルタリングの問題ではありません。 

考慮する：```
Ship: power=1 radius=10

A: mass=1 power=100 radius=10
B: mass=100 power=1 radius=10
```最初は A に到達可能です。 A を収集すると、B に到達できるようになります。 正解は 2 です。最初のグリッパーからの到達可能性のみをチェックするソリューションでは、1 が出力されます。 

もう 1 つの簡単な間違いは、グリッパー間の形状が重要であると考えることです。```
Ship at (0,0)

A at distance 5
B at distance 100
```A の半径が 100 で十分な力がある場合、A と B が互いに遠く離れていても、A は B を引き寄せることができます。 アクティブ グリッパーは船に取り付けられているため、すべての半径はグリッパーの元の位置からではなく、船から測定されます。 

最後の落とし穴は整数オーバーフローです。 座標と半径の範囲$10^9$、二乗距離は次のようになります。$10^{18}$。 すべての距離の計算には 64 ビット整数を使用する必要があります。 

## アプローチ

 ブルートフォースの見方は簡単です。 収集したグリッパーのセットを保守します。 新しいグリッパーが利用可能になるたびに、未収集のグリッパーをすべてスキャンし、現在アクティブなグリッパーの質量と距離の両方の要件を満たすすべてのグリッパーを収集します。 グリッパーが入手できなくなるまで繰り返します。 

これは、到達可能性プロセスを明示的にシミュレートするため、正しいです。 問題はコストです。 最悪の場合は回収させていただく場合がございます$n$グリッパーを使用し、各収集後にすべてをスキャンします$n$またオブジェクト。 それは、$O(n^2)$、 だいたい$6 \cdot 10^{10}$のための操作$n=250000$。 

クエリの構造は、任意の到達可能性よりもはるかに具体的です。 

パワーのあるグリッパーに$P$と半径$R$、満足する未回収のグリッパーをすべて必要とします。$$m_i \le P$$そして$$d_i \le R^2.$$これは属性に対する 2 次元の優越性クエリです。$(m_i,d_i)$。 

重要な観察は、すべてのグリッパーが最大 1 回しか収集されないことです。 同じ候補セットを繰り返し列挙する必要はありません。 必要なのは、長方形内のまだ収集されていないグリッパーを繰り返し見つけることができるデータ構造だけです。$$m_i \le P,\quad d_i \le R^2.$$存在する場合は、それを収集し、完全に削除して続行します。 

すべてのグリッパーを質量順に並べ替えます。 この場合、クエリは次のようになります。 

「距離インデックスがプレフィックスにあり、まだ生きているすべてのグリッパーの中で、最小質量を見つけます。」

 フェンウィック ツリーは、距離を越えてプレフィックス情報を維持できます。 各フェンウィック ノードには、質量別に注文されたグリッパーが格納されます。 遅延削除では、すでに収集されているグリッパーが削除されます。 

到達可能な距離のプレフィックス内の最小質量が最大でも現在のパワーであるときはいつでも、別の収集可能なグリッパーが見つかりました。 

これにより、$O(n \log n)$解決。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース |$O(n^2)$|$O(n)$| 遅すぎる |
 | 最適 |$O(n \log n)$|$O(n \log n)$| 承認済み |

 ## アルゴリズムのチュートリアル

 ### 距離の短縮

 すべてのグリッパーについて、船からの二乗距離を計算します。$$d_i=(x_i-x)^2+(y_i-y)^2.$$すべての半径を置き換えます$r_i$による$r_i^2$。 

これで、ジオメトリが完全に消えます。 各グリッパーは次のもののみで表されます。$$(d_i,m_i,p_i,r_i^2).$$### 座標圧縮

 距離のしきい値として表示される可能性のあるすべての値を収集します。$$d_i,\quad r_i^2,\quad r^2.$$並べ替えて圧縮します。 

フェンウィック ツリーは、これらの圧縮された距離座標に基づいて構築されます。 

### フェンウィックツリーの準備

 すべてのグリッパーを質量順に並べ替えます。 

各グリッパーについて、その距離に対応する位置でフェンウィック構造に挿入します。 

各フェンウィック ノードは、グリッパーを質量順に格納します。 

後でノードの先頭に削除された要素が含まれる可能性があるため、それらを遅延的に削除します。 

### 到達可能性 BFS

 収集されたすべてのグリッパーが新しい状態を生成すると考える$(p,r^2)$。 

最初のグリッパーから始めます。 

アクティブな状態のキューを維持します。 

1. 1 つのステートをポップします$(P,R^2)$。 
2. に対応する圧縮インデックスを見つけます。$R^2$。 
3. そのインデックスまでの Fenwick プレフィックスをクエリします。 
4. クエリは、到達可能な距離範囲内の最小質量を持つ生きているグリッパーを返します。 
5. このようなグリッパーが存在し、その質量は最大でも$P$:

 1. 収集済みとマークします。 
2. 構造からゆっくりと取り外します。 
3. 独自のプッシュ$(p_i,r_i^2)$列に並びます。 
4. 答えを増やします。 
5. 再度クエリを実行します。 

キューが空になるとプロセスは停止します。 

### なぜ効果があるのか

 あらゆるアクティブ状態に対して$(P,R^2)$、グリッパーは長方形の中にある場合に収集可能です。$$m_i \le P,\quad d_i \le R^2.$$Fenwick クエリは、距離条件を満たすすべてのグリッパーの中で最小の質量を持つ生きているグリッパーを常に返します。 

その最小質量がすでに超えている場合$P$の場合、到達可能な距離範囲内にある残りのグリッパーもすべて超過します。$P$, したがって、この状態には収集可能なグリッパーは存在しません。 

最小質量が以下の場合$P$、そのグリッパーは収集可能であり、到達可能なセットに属している必要があります。 グリッパーを収集しても、それまで到達可能だったグリッパーが到達不能になることはないため、すぐに収集するのは安全です。 

すべてのグリッパーは一度挿入され、一度削除されるため、アルゴリズムは到達可能なすべてのグリッパーの閉鎖を正確に検出します。 

## Python ソリューション```python
import sys
from collections import deque
from bisect import bisect_left, bisect_right

input = sys.stdin.readline

def solve():
    x, y, p0, r0, n = map(int, input().split())

    items = []
    coords = [r0 * r0]

    for idx in range(n):
        xi, yi, m, p, r = map(int, input().split())

        dx = xi - x
        dy = yi - y

        d = dx * dx + dy * dy
        rr = r * r

        items.append([d, m, p, rr, idx])

        coords.append(d)
        coords.append(rr)

    coords = sorted(set(coords))
    K = len(coords)

    def comp(v):
        return bisect_left(coords, v) + 1

    items_sorted = sorted(items, key=lambda z: (z[1], z[0]))

    bit = [[] for _ in range(K + 2)]

    for pos, item in enumerate(items_sorted):
        d, m, p, rr, original_id = item
        idx = comp(d)

        j = idx
        while j <= K:
            bit[j].append(pos)
            j += j & -j

    ptr = [0] * (K + 2)
    removed = [False] * n

    INF = (10**30, -1)

    def clean(node):
        arr = bit[node]
        p = ptr[node]

        while p < len(arr):
            pos = arr[p]
            oid = items_sorted[pos][4]
            if not removed[oid]:
                break
            p += 1

        ptr[node] = p

    def query(idx):
        best_mass = INF[0]
        best_pos = -1

        while idx > 0:
            clean(idx)

            p = ptr[idx]
            arr = bit[idx]

            if p < len(arr):
                pos = arr[p]
                mass = items_sorted[pos][1]

                if mass < best_mass:
                    best_mass = mass
                    best_pos = pos

            idx -= idx & -idx

        return best_pos

    ans = 0
    q = deque()
    q.append((p0, r0 * r0))

    while q:
        power, radius_sq = q.popleft()

        limit = bisect_right(coords, radius_sq)

        pos = query(limit)

        while pos != -1 and items_sorted[pos][1] <= power:
            d, m, p, rr, oid = items_sorted[pos]

            removed[oid] = True
            ans += 1

            q.append((p, rr))

            pos = query(limit)

    print(ans)

if __name__ == "__main__":
    solve()
```前処理フェーズでは、あらゆる幾何学的条件を二乗距離との比較に変換します。 これが、座標がデータ構造から完全に消える理由です。 

フェンウィック ツリーは、圧縮された距離値に基づいて構築されます。 グローバル リストは挿入時にすでに一括ソートされているため、各ノードには質量によってソートされたグリッパーが含まれています。 

の`ptr`配列は遅延削除を実装します。 グリッパーが収集される場合、当社はそれを削除済みとしてマークするだけです。 次回ノードにアクセスすると、そのポインタは削除されたエントリを超えて進みます。 これにより、多くのフェンウィック ノードからの高価な削除が回避されます。 

クエリ操作は、要求されたプレフィックス内のすべての距離の中で最小の質量を持つ生きているグリッパーを返します。 その最小質量がすでに大きすぎる場合、他のすべての候補も同様に大きすぎます。 

すべての距離と半径の平方は Python 整数を使用して計算され、最大の値を安全に処理します。$10^{18}$。 

## 実用的な例

 ### サンプル 1

 入力:```
0 0 5 10 5
5 4 7 11 5
-7 1 4 7 8
0 2 13 5 6
2 -3 9 3 4
13 5 1 9 9
```| ステップ | 有効電力 | アクティブ半径² | 新しく収集された |
 | --- | --- | --- | --- |
 | 1 | 5 | 100 | グリッパー 2 |
 | 2 | 7 | 64 | グリッパー 1 |
 | 3 | 11 | 25 | グリッパー 4 |
 | 4 | 3 | 16 | なし |

 答え = 3。 

トレースは連鎖効果を示します。 最初のグリッパーはグリッパー 1 を直接収集することはできませんが、グリッパー 2 を収集すると利用可能なパワーが増加し、ロックが解除されます。 

### カスタム例```
0 0 1 10 2
1 0 1 100 10
2 0 100 1 10
```| ステップ | 有効電力 | アクティブ半径² | 新しく収集された |
 | --- | --- | --- | --- |
 | 1 | 1 | 100 | 最初のグリッパー |
 | 2 | 100 | 100 | 2番目のグリッパー |
 | 3 | 1 | 100 | なし |

 答え = 2。 

これは、単一パスでは不十分な理由を示しています。 2 番目のグリッパーは、最初のグリッパーを取得した後にのみ到達可能になります。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |$O(n \log n)$| 各グリッパーは 1 回挿入および削除され、各操作は Fenwick ノードに接触します。 
| スペース |$O(n \log n)$| グリッパーは Fenwick ノード リスト内に保存されます。 

と$n=250000$、$O(n \log n)$4秒以内に余裕で収まります。 Fenwick 構造には標準のデータが格納されるため、メモリ使用量は 512 MB 以内に収まります。$n \log n$参考資料の配布。 

## テストケース```python
import sys, io
from collections import deque
from bisect import bisect_left, bisect_right

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)

    input = sys.stdin.readline

    x, y, p0, r0, n = map(int, input().split())

    items = []
    coords = [r0 * r0]

    for idx in range(n):
        xi, yi, m, p, r = map(int, input().split())

        d = (xi - x) ** 2 + (yi - y) ** 2
        rr = r * r

        items.append([d, m, p, rr, idx])

        coords.append(d)
        coords.append(rr)

    coords = sorted(set(coords))
    K = len(coords)

    def comp(v):
        return bisect_left(coords, v) + 1

    items_sorted = sorted(items, key=lambda z: (z[1], z[0]))

    bit = [[] for _ in range(K + 2)]

    for pos, item in enumerate(items_sorted):
        idx = comp(item[0])

        j = idx
        while j <= K:
            bit[j].append(pos)
            j += j & -j

    ptr = [0] * (K + 2)
    removed = [False] * n

    def clean(node):
        while ptr[node] < len(bit[node]):
            pos = bit[node][ptr[node]]
            if not removed[items_sorted[pos][4]]:
                break
            ptr[node] += 1

    def query(idx):
        best = None

        while idx > 0:
            clean(idx)

            if ptr[idx] < len(bit[idx]):
                pos = bit[idx][ptr[idx]]

                if best is None or items_sorted[pos][1] < items_sorted[best][1]:
                    best = pos

            idx -= idx & -idx

        return best

    ans = 0
    q = deque([(p0, r0 * r0)])

    while q:
        power, rr = q.popleft()

        limit = bisect_right(coords, rr)

        pos = query(limit)

        while pos is not None and items_sorted[pos][1] <= power:
            removed[items_sorted[pos][4]] = True
            ans += 1

            q.append((items_sorted[pos][2], items_sorted[pos][3]))

            pos = query(limit)

    return str(ans)

# sample
assert run(
"""0 0 5 10 5
5 4 7 11 5
-7 1 4 7 8
0 2 13 5 6
2 -3 9 3 4
13 5 1 9 9
"""
) == "3"

# minimum size
assert run(
"""0 0 1 1 1
1 0 1 1 1
"""
) == "1"

# unreachable because of mass
assert run(
"""0 0 1 10 1
1 0 2 100 100
"""
) == "0"

# chain unlocking
assert run(
"""0 0 1 10 2
1 0 1 100 10
2 0 100 1 10
"""
) == "2"

# boundary distance exactly equal to radius
assert run(
"""0 0 10 5 1
3 4 10 1 1
"""
) == "1"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | サンプル 1 | 3 | 公式シナリオ |
 | 単一の到達可能なグリッパー | 1 | 最小サイズ |
 | ヘビーグリッパー | 0 | 質量拘束 |
 | チェーンのロックを解除する | 2 | 到達可能性の伝播 |
 | 半径と正確に等しい距離 | 1 | 包含境界 |

 ## 特殊なケース

 次のことを考慮してください。```
0 0 1 10 2
1 0 1 100 10
2 0 100 1 10
```このアルゴリズムはまず、到達可能な最小質量のグリッパーを見つけて収集し、そのパワーと半径のペアをキューにプッシュします。 新しいステートが処理されると、2 番目のグリッパーが収集可能になります。 正解は２です。 

次のことを考慮してください。```
0 0 10 5 1
3 4 10 1 1
```二乗距離は$3^2+4^2=25$、正確に等しい$5^2$。 クエリは条件付きの距離プレフィックスを使用します$d_i \le r^2$, したがって、グリッパーは含まれており、答えは 1 です。 

次のことを考慮してください。```
0 0 1 1000000000 1
1000000000 0 1 1 1
```距離の二乗は以下に等しい$10^{18}$。 このアルゴリズムは、そのような値をすべて 64 ビット スケールの整数として保存し、二乗量を直接比較します。 浮動小数点演算は使用されないため、答えは正しいままです。
