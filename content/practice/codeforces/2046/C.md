---
title: "CF 2046C - 冒険者"
description: "各都市は平面上の点です。 分割点 $(x0,y0)$ を選択し、平面を 4 つの領域に分割します。 $$begin{aligned} &x ge x0, y ge y0 &x < x0, y ge y0 &x ge x0, y < y0 &x < x0, y < y0 end{aligned}$$ によると、すべての都市はこれらの領域の 1 つに正確に属します。"
date: "2026-06-08T09:08:00+07:00"
tags: ["codeforces", "competitive-programming", "binary-search", "data-structures", "greedy", "sortings", "ternary-search", "two-pointers"]
categories: ["algorithms"]
codeforces_contest: 2046
codeforces_index: "C"
codeforces_contest_name: "Codeforces Round 990 (Div. 1)"
rating: 2100
weight: 2046
solve_time_s: 199
verified: false
draft: false
---

[CF 2046C - 冒険者](https://codeforces.com/problemset/problem/2046/C)

 **評価:** 2100
 **タグ:** 二分検索、データ構造、貪欲、ソート、三分検索、2 つのポインター
 **解決時間:** 3 分 19 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 各都市は平面上の点です。 分割点を選択します$(x_0,y_0)$これにより、平面が 4 つの領域に分割されます。$$\begin{aligned} &x \ge x_0,\ y \ge y_0 \\ &x < x_0,\ y \ge y_0 \\ &x \ge x_0,\ y < y_0 \\ &x < x_0,\ y < y_0 \end{aligned}$$上記の不平等によれば、すべての都市はこれらの地域のいずれかに正確に属します。 

選択した分割点について、4 つの領域の数を次のようにします。$c_1,c_2,c_3,c_4$。 最大化したい$$\min(c_1,c_2,c_3,c_4).$$達成可能な最大値と、それを達成する 1 つの分割点の両方を出力する必要があります。 

すべてのテスト ケースの合計ポイント数は最大で$10^5$。 すべての候補のペアを検査するソリューション$x_0$そして$y_0$値には必要なものがあります$O(n^2)$あるいは、テストケースごとの動作が非常に遅くなり、さらに悪くなります。 周りに何かが必要です$O(n \log^2 n)$。 

微妙な点は、不等式が非対称であることです。 ある都市$x=x_0$右半分に属し、$y=y_0$上半分に属します。 誤って分割を両側で厳密に扱うソリューションでは、不正確な象限カウントが生成されます。 

多くの都市が同じものを共有している場合、別の厄介なケースが発生します。$x$-座標。 While sweeping possible vertical splits, all cities with the same$x$一緒に動かなければなりません。 このようなグループ内で分割すると、不等式の意味が変わり、無効な状態が作成される可能性があります。 

## アプローチ

 ブルートフォースソリューションでは、あらゆる可能性を試します$x_0$、可能な限り$y_0$、4つの象限を数えて、最良の答えを保持します。 候補を入力に出現する座標に制限したとしても、これには依然として次のことが必要です。$O(n^2)$候補ペアと$O(n)$それぞれを評価する作業を行い、その結果、$O(n^3)$。 

重要な点は、最適な最小象限サイズを直接検索する必要がないということです。 代わりに、もっと簡単な質問をするとします。 

> すべての象限に少なくとも次のものを含めることは可能ですか?$k$都市？ 

これに効率的に答えることができれば、最終的な答えは二分探索で見つけることができます。$k$。 

固定の場合$k$、垂直境界を左から右にスイープすることを想像してください。 いつでも、都市は 2 つのグループに分けられます。$$L = \{x_i < x_0\}, \qquad R = \{x_i \ge x_0\}.$$選択した水平境界に対して$y_0$、必要です$$\begin{aligned} \text{left-below} &\ge k,\\ \text{left-above} &\ge k,\\ \text{right-below} &\ge k,\\ \text{right-above} &\ge k. \end{aligned}$$させて$P_L(y)$そして$P_R(y)$の都市の数になります$L$そして$R$だれの$y$-座標は厳密に次より小さいです$y$。 

すると条件はこうなります$$k \le P_L(y_0) \le |L|-k,$$そして$$k \le P_R(y_0) \le |R|-k.$$各辺の有効な値は、$y_0$プレフィックス数は単調であるため、連続した間隔を形成します。 必要なのは、次の間隔かどうかを判断することだけです。$L$の区間と交差します$R$。 

フェンウィック ツリーを使用すると、スイープ中にプレフィックス数を維持できます。$x$、また、プレフィックスが指定された値に達する最初の位置を見つけることもできます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース |$O(n^3)$|$O(1)$| 遅すぎる |
 | 二分探索 + スイープ + フェンウィック |$O(n \log^2 n)$|$O(n)$| 承認済み |

 ## アルゴリズムのチュートリアル

 ### 座標圧縮

 すべての操作は、次の相対的な順序のみに依存します。$y$-コーディネート。 

すべて個別に圧縮する$y$-値をインデックスに変換$1 \ldots m$。 

### 修正の実現可能性チェック$k$1. すべてのポイントを適切な構造に入れる$R$。 
2. 左の構造$L$空から始まります。 
3. 個別のスイープ$x$- 昇順で座標。 
4. 座標付きグループを処理する前に$x$、分割は以下に対応します$x_0=x$。 小さいすべての点$x$すでに入っています$L$、およびすべての点$x\ge x_0$に残っている$R$。 
5. しましょう$tot_L$そして$tot_R$両側の現在のサイズになります。 
6. どちらかの側が次の数より少ない場合$2k$少なくともそっち側は貢献できない$k$水平線の上と下の両方のポイント。 このスイープ位置をスキップします。 
7. のために$L$、圧縮された間隔を見つけます$y$-位置$$k \le P_L \le tot_L-k.$$プレフィックス数は単調であるため、この間隔は Fenwick 順序統計クエリで取得できます。 

1. 対応する間隔を計算します。$R$。 
2. 2 つの間隔が交差する場合、有効な$y_0$存在し、このチェックの答えは「可能」です。 
3. 現在全体を移動します$x$-からのグループ$R$の中へ$L$そして掃除を続けます。 

### 二分探索

 1. 答えはこれを超えることはできません$\lfloor n/4 \rfloor$。 
2.二分探索$k$範囲内で$[0,\lfloor n/4 \rfloor]$。 
3. 実現可能性チェックを実行します。 
4. 実現可能な最大値を維持する$k$。 
5. その値のチェックをもう一度実行し、対応する値を保存します。$(x_0,y_0)$。 

### なぜ効果があるのか

 固定垂直分割の場合、有効な水平分割はすべて次の 2 つの独立した制約を満たす必要があります。$$k \le P_L(y_0) \le |L|-k,$$そして$$k \le P_R(y_0) \le |R|-k.$$両方のプレフィックス関数が単調であるため、各制約は可能な連続間隔を定義します。$y_0$-ポジション。 水平方向の分割は、これらの間隔が交差するときに正確に存在します。 

スイープでは、意味のあるすべての垂直分割が列挙されます。これは、分類が変更されるのは、個別の分割を通過した場合のみであるためです。$x$-座標。 実行可能性が単調であるため、二分探索は有効です。すべての象限に少なくとも次のものを含めることができれば、$k$都市の場合は、より小さな値も達成可能です。 

## Python ソリューション```python
import sys
from bisect import bisect_left

input = sys.stdin.readline

class Fenwick:
    def __init__(self, n):
        self.n = n
        self.bit = [0] * (n + 1)

    def add(self, idx, val):
        n = self.n
        bit = self.bit
        while idx <= n:
            bit[idx] += val
            idx += idx & -idx

    def sum(self, idx):
        res = 0
        bit = self.bit
        while idx:
            res += bit[idx]
            idx -= idx & -idx
        return res

    def kth(self, k):
        idx = 0
        bitmask = 1 << (self.n.bit_length() - 1)
        while bitmask:
            nxt = idx + bitmask
            if nxt <= self.n and self.bit[nxt] < k:
                k -= self.bit[nxt]
                idx = nxt
            bitmask >>= 1
        return idx + 1

def solve():
    t = int(input())
    out = []

    for _ in range(t):
        n = int(input())
        pts = [tuple(map(int, input().split())) for _ in range(n)]

        ys = sorted(set(y for _, y in pts))
        m = len(ys)

        data = []
        for x, y in pts:
            data.append((x, bisect_left(ys, y) + 1))

        data.sort()

        groups = []
        i = 0
        while i < n:
            j = i
            while j < n and data[j][0] == data[i][0]:
                j += 1
            groups.append((data[i][0], data[i:j]))
            i = j

        def get_interval(ft, total, k):
            if total < 2 * k:
                return None

            left = ft.kth(k)

            limit = total - k
            if limit == total:
                right = m
            else:
                pos = ft.kth(limit + 1)
                right = pos - 1

            if left > right:
                return None
            return (left, right)

        def check(k):
            L = Fenwick(m)
            R = Fenwick(m)

            for _, y in data:
                R.add(y, 1)

            left_cnt = 0
            right_cnt = n

            for x0, grp in groups:
                A = get_interval(L, left_cnt, k)
                B = get_interval(R, right_cnt, k)

                if A is not None and B is not None:
                    l = max(A[0], B[0])
                    r = min(A[1], B[1])

                    if l <= r:
                        y0 = ys[l - 1]
                        return True, x0, y0

                for _, y in grp:
                    R.add(y, -1)
                    L.add(y, 1)

                sz = len(grp)
                left_cnt += sz
                right_cnt -= sz

            return False, 0, 0

        lo, hi = 0, n // 4
        best = 0

        while lo <= hi:
            mid = (lo + hi) // 2
            ok, _, _ = check(mid)

            if ok:
                best = mid
                lo = mid + 1
            else:
                hi = mid - 1

        _, x0, y0 = check(best)

        out.append(str(best))
        out.append(f"{x0} {y0}")

    sys.stdout.write("\n".join(out))

if __name__ == "__main__":
    solve()
```## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |$O(n \log^2 n)$| 二分探索$k$、各実現可能性チェックは、Fenwick 順序統計クエリを使用してスイープを実行します。 
| スペース |$O(n)$| 圧縮された座標、グループ化された点、およびフェンウィック ツリー |

 すべてのテスト ケースの合計ポイント数は最大で$10^5$、 それで$O(n \log^2 n)$制限内に簡単に収まります。 メモリ使用量はポイント数に比例します。
