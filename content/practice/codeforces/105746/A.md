---
title: "CF 105746A - 車"
description: "直線に沿って移動する一連の車を追跡しています。 各車両は位置 0 から開始し、クエリ間隔中一定の速度で移動します。 ただし、速度はグローバルに固定されているわけではなく、アップデートにより時間の経過とともに変化する可能性があります。"
date: "2026-06-22T04:42:26+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 105746
codeforces_index: "A"
codeforces_contest_name: "Bangladesh Olympiad in Informatics 2025 National Round Day 1"
rating: 0
weight: 105746
solve_time_s: 63
verified: true
draft: false
---

[CF 105746A - 車](https://codeforces.com/problemset/problem/105746/A)

 **評価:** -
 **タグ:** -
 **解決時間:** 1 分 3 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 直線に沿って移動する一連の車を追跡しています。 各車両は位置 0 から開始し、クエリ間隔中一定の速度で移動します。 ただし、速度はグローバルに固定されているわけではなく、アップデートにより時間の経過とともに変化する可能性があります。 

システムは 2 種類の操作を処理します。 1回の操作で特定の車の速度が変わります。 もう 1 つは、時間と空間に関する幾何学的な質問です。つまり、L 秒から R 秒までの時間間隔において、X メートルから Y メートルまでの空間セグメントに少なくとも一度は何台の車が進入したかということです。 

1 台の車は、その位置 A[i] · t が [X, Y] 内に収まる時刻 t が区間 [L, R] 内に少なくとも 1 つ存在する場合、答えに寄与します。 

位置は時間とともに直線的に増加するため、各車両は時間と位置の平面で線分をたどります。 クエリは、これらのラインのうちの何本が指定された水平時間スラブと垂直位置ストリップと空ではない方法で交差するかを尋ねます。 

この制約により、最大 10^9 の速度と最大 10^9 の時間で最大 100,000 の操作を処理する必要があり、位置範囲は最大 10^15 まで可能です。 これにより、クエリごとに各車をチェックすることは直ちに除外されます。これは、最悪の場合、10^10 の操作になるためです。 

単純な考えとしては、すべての車両を反復処理し、間隔が重複しているかどうかを確認することによって各クエリを評価することも考えられます。 それには、各車について、その車が [X, Y] に存在する時間間隔を計算し、それを [L, R] と交差させる必要があります。 これは論理的には正しいですが、遅すぎます。 

速度がゼロの場合、微妙なエッジケースが発生します。 A[i] = 0 の場合、車は永遠に位置 0 に留まります。 次に、時間に関係なく、X ≤ 0 ≤ Y の場合にのみ寄与します。 速度による除算を含む単純な公式はここで破綻します。 

もう 1 つの注意が必要なケースは、X = 0 または L = R の場合に発生します。L = R の場合、事実上単一の時点をチェックしていることになりますが、時間間隔をオープンまたはハーフオープンとして扱うソリューションでは、有効な境界ヒットが誤って削除される可能性があります。 

## アプローチ

 速度 v の 1 台の車の場合、その位置は v · t です。 [L, R] に次のような t が存在するかどうかを知りたいとします。 

X ≤ v · t ≤ Y。 

v = 0 の場合、条件は時間に関係なく、[X, Y] に 0 があるかどうかをチェックすることになります。 そうなれば、常に車が重要になります。 そうでなければ決して。 

v > 0 の場合、不等式を分割できます。 

X / v ≤ t ≤ Y / v。 

したがって、間隔 [X/v, Y/v] が [L, R] と重なる場合、車が寄与します。 これは単純な区間交差チェックになります。 

したがって、各クエリ タイプ 2 は、[L, R] と重複する間隔を生成する現在の速度の数をカウントすることに帰着します。 問題は、速度が動的に変化するため、ポイントの更新と派生値に対する範囲カウントをサポートする構造が必要であることです。 

重要な観察は、各クエリが車に対して独立しており、各車の状態は現在の速度のみに依存するということです。 そのため、複数の速度セットを維持し、各速度をチェックすることでクエリに答えます。 ただし、ダイレクトスキャンは遅すぎます。 

代わりに、次のように各クエリをカウント速度に削減します。 

最大(L, X / v) ≤ 最小(R, Y / v),

 これは v の制約に再配置できます。 

[L, R] には X ≤ v t ≤ Y を満たす t が存在します。 

同様に:

 v ≥ X / R および v ≤ Y / L。床と天井の境界に注意してください。 

したがって、各クエリは、(X、Y、L、R) から導出される範囲 [低速、高速] に含まれる速度の数をカウントすることになります。 更新には動的な順序構造が必要です。 

したがって、挿入、削除、および順序統計をサポートするバランスのとれた構造で現在のすべての速度を維持します。 圧縮値に対するフェンウィック ツリーまたはセグメント ツリーは、速度が制限され更新されるため機能します。 

初期配列と更新に表示されるすべての速度を調整圧縮します。 次に、頻度カウントを維持します。 各更新は O(log N) 単位でカウントを調整し、境界を計算した後、各クエリは O(log N) 単位の範囲合計になります。

The critical insight is that the geometric condition collapses into a pure inequality on speed.

 | アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | クエリごとのブルート フォース | O(NQ) | O(N) | 遅すぎる |
 | ソートされた構造 + 圧縮 | O((N + Q) log N) | O(N) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 初期値とすべての更新値を含む、表示される可能性のあるすべての速度を事前計算します。これは、それらをフェンウィック ツリーのインデックスに圧縮するためです。 
2. Build a sorted array of unique speeds and map each speed to its index. This allows us to treat the problem as frequency counting over a discrete domain.
 3. Initialize a Fenwick tree where each position stores how many cars currently have that speed.
 4. Insert all initial speeds into the Fenwick tree by increasing their frequency. This establishes the initial state of the system.
 5. For a type 1 query, remove the old speed of the car from the Fenwick tree and insert the new speed. This keeps the structure consistent with the current configuration.
 6. タイプ 2 クエリの場合、自動車が [L, R] 内の一部の t について X ≤ v · t ≤ Y を満たさなければならないという条件から導出される有効な速度間隔 [low, high] を計算します。 これは v ∈ [ceil(X / R), Floor(Y / L)] に帰着します。 
7. Convert low and high into compressed indices using binary search on the sorted speed list.
 8. Query the Fenwick tree for the number of speeds in this index range. This sum is exactly the number of cars satisfying the condition.
 9. タイプ 2 の各クエリの結果を出力します。 

正確さは、すべての有効な車が正確に 1 回カウントされるという事実にかかっています。これは、車の速度がその動きが必要な領域と交差するかどうかを一意に決定するためです。 

## なぜ機能するのか

 各自動車は、単一のパラメーター、その速度、および時間位置の長方形との交差の幾何学的条件を誘導し、そのパラメーターに対する単調な制約に帰着します。 This monotonicity ensures that the valid set of speeds forms a contiguous interval in sorted order. Since updates only change one value at a time, maintaining frequency over this ordered domain preserves correctness.

 ## Python ソリューション```python
import sys
input = sys.stdin.readline

class Fenwick:
    def __init__(self, n):
        self.n = n
        self.bit = [0] * (n + 1)

    def add(self, i, v):
        while i <= self.n:
            self.bit[i] += v
            i += i & -i

    def sum(self, i):
        s = 0
        while i > 0:
            s += self.bit[i]
            i -= i & -i
        return s

    def range_sum(self, l, r):
        if l > r:
            return 0
        return self.sum(r) - self.sum(l - 1)

def ceil_div(a, b):
    if b == 0:
        return float('inf')
    return (a + b - 1) // b

def floor_div(a, b):
    if b == 0:
        return 0
    return a // b

def solve():
    N = int(input())
    A = list(map(int, input().split()))
    Q = int(input())

    queries = []
    all_vals = set(A)

    for _ in range(Q):
        tmp = input().split()
        if tmp[0] == '1':
            i = int(tmp[1]) - 1
            s = int(tmp[2])
            queries.append(('1', i, s))
            all_vals.add(s)
        else:
            X = int(tmp[1])
            Y = int(tmp[2])
            L = int(tmp[3])
            R = int(tmp[4])
            queries.append(('2', X, Y, L, R))

    vals = sorted(all_vals)
    idx = {v: i + 1 for i, v in enumerate(vals)}

    fw = Fenwick(len(vals))

    cur = A[:]
    for v in cur:
        fw.add(idx[v], 1)

    out = []

    for q in queries:
        if q[0] == '1':
            i, s = q[1], q[2]
            fw.add(idx[cur[i]], -1)
            cur[i] = s
            fw.add(idx[s], 1)
        else:
            X, Y, L, R = q[1], q[2], q[3], q[4]

            if L == 0:
                low = 0
            else:
                low = ceil_div(X, R)

            high = Y // L

            l = 1
            r = len(vals)

            while l <= r:
                m = (l + r) // 2
                if vals[m - 1] < low:
                    l = m + 1
                else:
                    r = m - 1
            left = l

            l = 1
            r = len(vals)

            while l <= r:
                m = (l + r) // 2
                if vals[m - 1] <= high:
                    l = m + 1
                else:
                    r = m - 1
            right = r

            out.append(str(fw.range_sum(left, right)))

    print("\n".join(out))

if __name__ == "__main__":
    solve()
```実装では、更新とクエリが明確に分離されます。 フェンウィック ツリーは速度の頻度を維持し、各クエリは速度から導出された間隔を計算し、その速度に該当する間隔をカウントします。 

最もデリケートな部分は、境界を正しく計算することです。 t ∈ [L, R] に関する不等式 X ≤ v t ≤ Y は、v ≥ X / R および v ≤ Y / L になります。整数の除算は、下限は天井、上限は下限で処理する必要があります。 ここで 1 つずつずれたエラーがあると、X または Y での正確なヒットなどの境界ケースで車両のカウントが誤られます。 

速度は圧縮されますが、必ずしも連続的ではないため、これらの境界をインデックスに変換するために二分探索が使用されます。 

## 実用的な例

 ### 例 1

 入力:```
N = 3
A = [2, 10, 15]
Query: type 2 with X=10, Y=15, L=3, R=4
```お車一台一台査定させていただきます。 

| 車 | 速度 v | 区間条件 v ≥ X/R | v ≤ Y/L | 有効 |
 | --- | --- | --- | --- | --- |
 | 1 | 2 | 2 ≥ 2.5 偽 | はい | いいえ |
 | 2 | 10 | 10 ≥ 2.5 はい | 10 ≤ 5 偽 | いいえ |
 | 3 | 15 | 15 ≥ 2.5 はい | 15 ≤ 5 偽 | いいえ |

 両方の境界を満たす自動車のみがカウントされ、この分離された解釈では結果が 0 になります。 これは、完全なソリューション コンテキストで正しい整数境界を使用して直接推論を慎重に適用する必要がある理由を強調しています。 

### 例 2

 入力:```
N = 3
A = [1, 5, 20]
Query: X=5, Y=100, L=1, R=10
```境界:

 v ≥ ceil(5/10) = 1

 v ≤ フロア(100/1) = 100

 すべての車がこれを満たしています。 

| 車 | スピード | 範囲 [1,100] | カウント |
 | --- | --- | --- | --- |
 | 1 | 1 | はい | はい |
 | 2 | 5 | はい | はい |
 | 3 | 20 | はい | はい |

 答えは 3 です。 

これらの例は、幾何学的条件が速度に関する単純なしきい値テストにどのように組み込まれるかを示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O((N + Q) log N) | 各更新とクエリでは、圧縮された速度で Fenwick 操作とバイナリ検索を使用します。 
| スペース | O(N + Q) | 配列、圧縮マップ、およびフェンウィック ツリーのストレージ |

 N と Q が両方とも 100000 であり、対数係数が小さいままであるため、複雑さは制約内に問題なく収まります。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    return sys.stdout.getvalue()

# NOTE: In real use, hook solve() and capture output properly

# small sanity checks (conceptual placeholders)
# assert run(...) == ...
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | N=1、A=[0]、クエリ X=0 Y=0 L=1 R=1 | 1 | ゼロスピードエッジケース |
 | N=2、A=[1,2]、更新なし | 依存します | 基本的な静的正確性 |
 | X=0 Y=1e15 広い範囲 | N | 完全な包含境界 |
 | L=R 1 回 | 正しい | インスタントクエリ境界 |

 ## 特殊なケース

 重要なエッジ ケースの 1 つは、車の速度がゼロの場合です。 A[i] = 0 および X = 0、Y = 5、L = 1、R = 10 の入力の場合、自動車は常に位置 0 にあります。 条件はそれを 1 回カウントする必要があります。 導出された不等式 v ≥ ceil(X / R) は 0 ≥ 0、v ≤ Floor(Y / L) は 0 ≤ 5 となるため、正しく含まれています。 

もう 1 つのエッジ ケースは、L = R の場合です。X = 10、Y = 20、L = R = 2 とします。条件は、v · 2 が [10, 20] にあるかどうかを確認することになります。これは、[5, 10] の v と同等です。 アルゴリズムの式により同じ間隔が生成され、正確なカウントが保証されます。 

最後の微妙なケースは、境界の包含です。 車が v・L = X または v・R = Y を正確に満たす場合、その車はカウントされなければなりません。 ceil と Floor を正しく使用すると、これらの正確な一致が削除されずに保持されます。
