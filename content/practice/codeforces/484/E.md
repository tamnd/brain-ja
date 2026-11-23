---
title: "CF 484E - サインオンフェンス"
description: "一連の高さで表されるフェンスがあります。 長方形の標識は、いくつかの連続したパネルの上に設置する必要があります。 標識は固定幅 w を持っているため、連続する w 個のフェンス パネルを正確にカバーする必要があります。"
date: "2026-06-07T17:25:15+07:00"
tags: ["codeforces", "competitive-programming", "binary-search", "constructive-algorithms", "data-structures"]
categories: ["algorithms"]
codeforces_contest: 484
codeforces_index: "E"
codeforces_contest_name: "Codeforces Round 276 (Div. 1)"
rating: 2500
weight: 484
solve_time_s: 199
verified: true
draft: false
---

[CF 484E - サインオンフェンス](https://codeforces.com/problemset/problem/484/E)

 **評価:** 2500
 **タグ:** 二分探索、構築的アルゴリズム、データ構造
 **解決時間:** 3 分 19 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 一連の高さで表されるフェンスがあります。 長方形の標識は、いくつかの連続したパネルの上に設置する必要があります。 標識の幅は固定されています`w`したがって、正確にカバーする必要があります`w`連続したフェンスパネル。 その高さは、覆われているパネルの中で最も短いパネルを超えることはできません。そうでない場合、標識の一部がフェンスの上に突き出ることになります。 

お問い合わせの場合`(l, r, w)`、正確に任意の連続ブロックを選択できます。`w`パネルが完全に間隔内に含まれている`[l, r]`。 ブロックに標識を設置すると`[x, x+w-1]`、可能な最大の標識の高さは次のとおりです。$$\min(h_x,h_{x+1},\ldots,h_{x+w-1}).$$内部のすべての有効な配置の中で`[l,r]`、そのような最大の値が必要です。 

同様に、各クエリは次のことを尋ねます。$$\max_{x \in [l,r-w+1]} \min(h_x,\ldots,h_{x+w-1}).$$フェンスには以下のものがあります$10^5$パネルがあり、最大$10^5$クエリ。 クエリ間隔を直接スキャンするアルゴリズムは絶望的です。 たとえ$O((r-l+1)\log n)$クエリごとのソリューションには約$10^{10}$最悪の場合の運用。 

この制約は、すべてのクエリを一緒に処理する必要があることを示唆しています。 周囲の複雑さ$O((n+m)\log^2 n)$または$O((n+m)\log n)$が適切です。 

微妙な点は、答えは区間全体の最小の高さではないということです。 幅をどこにするかは自由です。`w`標識が置かれている。 

考慮する：```
heights = [5,1,5]
query = (1,3,1)
```答えは`5`、幅があるため`1`どちらかのエンドポイントを選択できます。 最小限のものだけを取り除いたソリューション`[1,3]`間違って戻ってきます`1`。 

もう 1 つのよくある間違いは、標識の幅が固定されていることを忘れることです。```
heights = [4,4,1,4]
query = (1,4,3)
```有効なウィンドウは次のとおりです。`[4,4,1]`そして`[4,1,4]`、両方とも最小値を持ちます`1`。 答えは`1`、最小値でより短いウィンドウが存在する場合でも、`4`。 

3 番目のエッジ ケースは、間隔全体を使用する必要がある場合に発生します。```
heights = [7,3,5]
query = (1,3,3)
```配置は 1 つだけ存在するため、答えは最小間隔です。`3`。 

最適なソリューションは、これらすべての状況を正しく処理する必要があります。 

## アプローチ

 最も直接的な解決策は、標識のあらゆる可能な配置を評価します。 お問い合わせの場合`(l,r,w)`、すべての開始位置を次から調べます。`l`に`r-w+1`。 それぞれの幅については、`w`ウィンドウの最小高さを計算し、最大値を取得します。 

これは、すべての法的標識の配置を明示的にチェックするため、正しいです。 

問題は走行時間です。 クエリ間隔には次のものが含まれる場合があります$O(n)$候補ウィンドウ。 範囲最小構造が各ウィンドウの最小値を与える場合でも、$O(1)$、単一のクエリには依然としてコストがかかります$O(n)$。 と$10^5$クエリの場合、最悪のケースは$10^{10}$操作。 

重要な観察は、答えを直接考えるのをやめ、代わりに決定に関する質問をすることです。 

高さのしきい値の場合$H$、幅は存在しますか-`w`内側のブロック`[l,r]`すべてのパネルに少なくとも高さがあるもの$H$?

 すべての位置に少なくとも高さをマークすると、$H$アクティブである場合、質問は次のようになります。 

内部に連続したアクティブセグメントはありますか`[l,r]`少なくとも長さの`w`?

 これにより、元の最適化問題が単調な決定問題に変換されます。 身長の場合$H$機能する場合は、それより小さな高さでも機能します。 この単調性は二分探索を示唆しています。 

ただし、クエリごとにバイナリ検索を個別に行うと、やはりコストが高すぎます。 標準的な手法は並列二分探索です。 

固定閾値の場合$H$、少なくとも高さのあるすべてのポジションをアクティブにします$H$。 次に、次のことに答えるデータ構造が必要です。 

内側の間隔`[l,r]`、連続するアクティブ セグメントの最大長はどれくらいですか?

 これは、以下を格納するセグメント ツリーで維持できます。 

- 最長のアクティブなプレフィックス
 - 最長のアクティブなサフィックス
 - 最長のアクティブセグメント
 - インターバルの長さ

 高さは降順でアクティブになるため、更新はポイントの割り当てになります。 

並列二分探索では、現在同じ高さのランクをテストしているすべてのクエリをグループ化します。 高さの 1 回の掃引中に、パネルをアクティブにして、すべてのクエリを評価します。 各クエリに必要なのは、$O(\log n)$セグメント ツリーは二分探索ラウンドごとに動作します。 

結果として生じる複雑さは、$O((n+m)\log^2 n)$、快適にフィットします。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース |$O(mn)$|$O(1)$| 遅すぎる |
 | 最適 |$O((n+m)\log^2 n)$|$O(n+m)$| 承認済み |

 ## アルゴリズムのチュートリアル

 まず、回答候補を圧縮します。 すべてのクエリに対する答えは、フェンスの高さのいずれかに等しくなければなりません。標識の高さを高くしても、パネルの高さを渡すときにのみ実現可能性が変わるためです。 

個別の高さを降順に並べ替えます。 

### 1. クエリごとにバイナリ検索範囲を初期化します。 

クエリごとにインデックスを維持する`low`そして`high`ソートされた個別の高さに変換されます。 

最初はあらゆる高さが可能であるため、次のようになります。`low = 0`

`high = number_of_distinct_heights - 1`### 2. 1 ラウンドの並列二分探索を繰り返し実行します。 

未解決のクエリごとに計算します$$mid = \frac{low+high}{2}.$$クエリを現在の中間点でグループ化します。 

同じグループ内のすべてのクエリは、特定の高さのしきい値が実現可能かどうかを尋ねます。 

### 3. 最大から最小までのスイープの高さ

 フェンスの位置を高さの降順に並べ替えます。 

中間点グループをしきい値の降順で処理します。 

現在のしきい値が減少するたびに、高さがそのしきい値以上であるすべてのフェンス位置をアクティブにします。 

アクティブ セットは、その高さの標識をサポートできるパネルを正確に表します。 

### 4. セグメント ツリーでアクティブなセグメントを維持する

 各リーフは 1 つのパネルに対応します。 

非アクティブなパネルには以下が保存されます。 

- プレフィックス = 0
 - サフィックス = 0
 - 最高 = 0

 アクティブなパネルには以下が保存されます。 

- プレフィックス = 1
 - サフィックス = 1
 - 最高 = 1

 2 人の子を結合する場合:$$prefix = \begin{cases} left.length + right.prefix & \text{if left is completely active}\\ left.prefix & \text{otherwise} \end{cases}$$サフィックスは対称的に計算されます。 

最適なセグメントとは、次の最大値です。 

- 左.最高
 - そう、最高
 - left.suffix + right.prefix

 ### 5. 実現可能性に関する質問に答える

 問い合わせ用`(l,r,w)`セグメントツリー情報を取得します`[l,r]`。 

フィールド`best`その間隔内の最長連続アクティブ実行に相当します。 

しきい値は正確に次の場合に実現可能です$$best \ge w.$$可能であれば、二分探索をより高いレベルに移動します。 

それ以外の場合は、より低い高さに向かって移動します。 

### 6. すべての検索が完了するまで続行します

 およそ後`log(number_of_distinct_heights)`丸めると、すべてのクエリは 1 つの高さの値に収束します。 

### 7. 対応する高さを出力します

 最終的な二分探索位置によって、実行可能な最大のしきい値が特定され、それがまさに答えになります。 

### なぜ効果があるのか

 しきい値を修正する$H$。 幅-`w`少なくとも身長の兆候$H$中に存在する`[l,r]`もし、そしてその場合に限り`w`その間隔内の連続するパネルの高さはすべて少なくとも$H$。 

少なくとも高さのあるパネルを正確にアクティブにした後、$H$、そのような兆候は、間隔に少なくとも一定の長さのアクティブな連続が含まれる場合にのみ存在します。`w`。 

セグメント ツリーは、クエリされた間隔ごとに最長のアクティブな実行を計算するため、実現可能性テストは正確です。 

実現可能性は単調です。 しきい値の場合$H$より多くのパネルをアクティブにしても既存の有効な実行を破壊できないため、しきい値を小さくすることも可能です。 したがって、二分探索は実行可能な最大のしきい値を見つけます。 並列バイナリ検索は、同じアクティベーション スイープを再利用しながら、このような多くの検索を同時に評価します。 したがって、報告されるすべての値は、正確に達成可能な最大標識高さになります。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

class SegTree:
    def __init__(self, n):
        self.n = n
        self.pref = [0] * (4 * n)
        self.suff = [0] * (4 * n)
        self.best = [0] * (4 * n)
        self.length = [0] * (4 * n)
        self._build(1, 0, n - 1)

    def _build(self, v, tl, tr):
        self.length[v] = tr - tl + 1
        if tl == tr:
            return
        tm = (tl + tr) // 2
        self._build(v * 2, tl, tm)
        self._build(v * 2 + 1, tm + 1, tr)

    def _pull(self, v):
        lc = v * 2
        rc = lc + 1

        self.pref[v] = self.pref[lc]
        if self.pref[lc] == self.length[lc]:
            self.pref[v] += self.pref[rc]

        self.suff[v] = self.suff[rc]
        if self.suff[rc] == self.length[rc]:
            self.suff[v] += self.suff[lc]

        self.best[v] = max(
            self.best[lc],
            self.best[rc],
            self.suff[lc] + self.pref[rc]
        )

    def update(self, v, tl, tr, pos):
        if tl == tr:
            self.pref[v] = 1
            self.suff[v] = 1
            self.best[v] = 1
            return

        tm = (tl + tr) // 2
        if pos <= tm:
            self.update(v * 2, tl, tm, pos)
        else:
            self.update(v * 2 + 1, tm + 1, tr, pos)

        self._pull(v)

    def query(self, v, tl, tr, l, r):
        if l == tl and r == tr:
            return (
                self.pref[v],
                self.suff[v],
                self.best[v],
                self.length[v]
            )

        tm = (tl + tr) // 2

        if r <= tm:
            return self.query(v * 2, tl, tm, l, r)

        if l > tm:
            return self.query(v * 2 + 1, tm + 1, tr, l, r)

        left = self.query(v * 2, tl, tm, l, tm)
        right = self.query(v * 2 + 1, tm + 1, tr, tm + 1, r)

        lp, ls, lb, ll = left
        rp, rs, rb, rl = right

        pref = lp
        if lp == ll:
            pref += rp

        suff = rs
        if rs == rl:
            suff += ls

        best = max(lb, rb, ls + rp)

        return (pref, suff, best, ll + rl)

def solve():
    n = int(input())
    h = list(map(int, input().split()))

    m = int(input())

    queries = []
    for _ in range(m):
        l, r, w = map(int, input().split())
        queries.append((l - 1, r - 1, w))

    vals = sorted(set(h), reverse=True)
    k = len(vals)

    low = [0] * m
    high = [k - 1] * m

    positions = sorted(
        [(h[i], i) for i in range(n)],
        reverse=True
    )

    while True:
        buckets = [[] for _ in range(k)]
        active = False

        for i in range(m):
            if low[i] < high[i]:
                active = True
                mid = (low[i] + high[i]) // 2
                buckets[mid].append(i)

        if not active:
            break

        seg = SegTree(n)

        ptr = 0

        for mid in range(k):
            threshold = vals[mid]

            while ptr < n and positions[ptr][0] >= threshold:
                seg.update(1, 0, n - 1, positions[ptr][1])
                ptr += 1

            for qi in buckets[mid]:
                l, r, w = queries[qi]
                best = seg.query(1, 0, n - 1, l, r)[2]

                if best >= w:
                    high[qi] = mid
                else:
                    low[qi] = mid + 1

    ans = [str(vals[low[i]]) for i in range(m)]
    sys.stdout.write("\n".join(ans))

if __name__ == "__main__":
    solve()
```セグメント ツリーには、連続するアクティブな実行をマージするために必要な情報が正確に保存されます。 の`best`value は、ノードの間隔内の最長のアクティブなセグメントです。 プレフィックスとサフィックスの長さにより、中間点を横切るランを再構築できます。 

二分探索は、最大範囲全体ではなく、個別の高さに対して実行されます。$10^9$。 これにより、検索深度が約 1 に減ります。`log2(n)`。 

よくある実装上の間違いは、二分探索の方向が逆になってしまうことです。 高さの配列`vals`降順にソートされます。 インデックスが小さいほど、高さが高くなります。 しきい値が実現可能な場合、より高い高みを目指して探索を続けます。これは、上限を次の値に移動することを意味します。`mid`。 

もう 1 つの微妙な点は、並列二分探索ラウンドごとにセグメント ツリーを再構築することです。 各ラウンドはしきい値を超える新たなスイープを表すため、アクティブ セットを最初から再構築する必要があります。 

## 実用的な例

 ### 例 1

 入力:```
5
1 2 2 3 3
1
2 5 3
```独特の高さは、`[3,2,1]`。 

| 中高さ | アクティブなポジション | クエリ間隔 | 最長アクティブ実行 | 実現可能 |
 | --- | --- | --- | --- | --- |
 | 2 | 2、3、4、5 | [2,5] | 4 | はい |
 | 3 | 4,5 | [2,5] | 2 | いいえ |

 二分探索ではまず高さを検証します`2`動作します。 次に身長をテストします`3`、少なくとも連続した高さのパネルが 2 つしかないため失敗します。`3`。 答えは`2`。 

これは単調性の性質を示しています。 一度`3`失敗すると、より大きなしきい値もすべて失敗する必要があります。 

### 例 2

 入力:```
4
5 1 5 5
1
1 4 2
```独特の高さは、`[5,1]`。 

| 中高さ | アクティブなポジション | クエリ間隔 | 最長アクティブ実行 | 実現可能 |
 | --- | --- | --- | --- | --- |
 | 5 | 1,3,4 | [1,4] | 2 | はい |

 アクティブな実行`[3,4]`長さがある`2`なので、幅 2 の標識を高さで配置できます。`5`。 

この例は、単にアクティブなパネルを数えるのではなく、最長連続実行が必要な理由を示しています。 アクティブなパネルは 3 つありますが、隣接しているのは 2 つだけです。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |$O((n+m)\log^2 n)$| について$\log n$並列二分探索ラウンド、それぞれの実行$O(n+m)$セグメントツリーの操作 |
 | スペース |$O(n+m)$| セグメント ツリー、クエリ、二分探索状態 |

 と$n,m \le 10^5$、$\log n$結果の操作数は 4 秒の制限内に十分収まり、メモリ制限内に余裕で収まります。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)

    input = sys.stdin.readline

    class SegTree:
        def __init__(self, n):
            self.n = n
            self.pref = [0] * (4 * n)
            self.suff = [0] * (4 * n)
            self.best = [0] * (4 * n)
            self.length = [0] * (4 * n)
            self.build(1, 0, n - 1)

        def build(self, v, l, r):
            self.length[v] = r - l + 1
            if l == r:
                return
            m = (l + r) // 2
            self.build(v * 2, l, m)
            self.build(v * 2 + 1, m + 1, r)

        def pull(self, v):
            lc = v * 2
            rc = lc + 1

            self.pref[v] = self.pref[lc]
            if self.pref[lc] == self.length[lc]:
                self.pref[v] += self.pref[rc]

            self.suff[v] = self.suff[rc]
            if self.suff[rc] == self.length[rc]:
                self.suff[v] += self.suff[lc]

            self.best[v] = max(
                self.best[lc],
                self.best[rc],
                self.suff[lc] + self.pref[rc]
            )

        def update(self, v, l, r, pos):
            if l == r:
                self.pref[v] = self.suff[v] = self.best[v] = 1
                return
            m = (l + r) // 2
            if pos <= m:
                self.update(v * 2, l, m, pos)
            else:
                self.update(v * 2 + 1, m + 1, r, pos)
            self.pull(v)

        def query(self, v, l, r, ql, qr):
            if ql == l and qr == r:
                return (self.pref[v], self.suff[v], self.best[v], self.length[v])

            m = (l + r) // 2

            if qr <= m:
                return self.query(v * 2, l, m, ql, qr)
            if ql > m:
                return self.query(v * 2 + 1, m + 1, r, ql, qr)

            A = self.query(v * 2, l, m, ql, m)
            B = self.query(v * 2 + 1, m + 1, r, m + 1, qr)

            ap, asf, ab, al = A
            bp, bsf, bb, bl = B

            pref = ap + bp if ap == al else ap
            suff = bsf + asf if bsf == bl else bsf
            best = max(ab, bb, asf + bp)

            return (pref, suff, best, al + bl)

    out = []

    n = int(input())
    h = list(map(int, input().split()))
    m = int(input())

    qs = [tuple(map(int, input().split())) for _ in range(m)]

    vals = sorted(set(h), reverse=True)
    k = len(vals)

    lo = [0] * m
    hi = [k - 1] * m

    pos = sorted([(h[i], i) for i in range(n)], reverse=True)

    while True:
        buckets = [[] for _ in range(k)]
        alive = False

        for i in range(m):
            if lo[i] < hi[i]:
                alive = True
                md = (lo[i] + hi[i]) // 2
                buckets[md].append(i)

        if not alive:
            break

        seg = SegTree(n)
        ptr = 0

        for md in range(k):
            thr = vals[md]

            while ptr < n and pos[ptr][0] >= thr:
                seg.update(1, 0, n - 1, pos[ptr][1])
                ptr += 1

            for qi in buckets[md]:
                l, r, w = qs[qi]
                best = seg.query(1, 0, n - 1, l - 1, r - 1)[2]

                if best >= w:
                    hi[qi] = md
                else:
                    lo[qi] = md + 1

    return "\n".join(str(vals[lo[i]]) for i in range(m))

# provided sample
assert run(
"""5
1 2 2 3 3
3
2 5 3
2 5 2
1 5 5
"""
) == "2\n3\n1"

# minimum size
assert run(
"""1
7
1
1 1 1
"""
) == "7"

# all equal
assert run(
"""4
5 5 5 5
2
1 4 2
2 3 1
"""
) == "5\n5"

# width equals interval length
assert run(
"""3
7 3 5
1
1 3 3
"""
) == "3"

# separated tall panels
assert run(
"""4
5 1 5 5
1
1 4 2
"""
) == "5"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 単一パネル | 7 | 最小の訴訟事例 |
 | すべての高さが等しい | 5、5 | 均一な高さの動作 |
 | 幅は間隔の長さに等しい | 3 | 可能な配置は 1 つだけ |
 |`5 1 5 5`幅2 | 5 | アクティブなパネルをカウントするのではなく、連続実行ロジック |

 ## 特殊なケース

 次のことを考慮してください。```
3
5 1 5
1
1 3 1
```答えは`5`。 閾値中`5`、位置 1 と 3 がアクティブになります。 屋内での最長のアクティブ ラン`[1,3]`は`1`、すでに幅を満たしています`1`。 二分探索が正しく返される`5`。 最小間隔を使用した解は誤って返されます。`1`。 

考慮する：```
4
4 4 1 4
1
1 4 3
```閾値用`4`、アクティブなランには長さがあります`2`そして`1`。 最長ランはただ`2`、必要な幅より小さい`3`、そのため、しきい値は失敗します。 閾値用`1`、すべてのポジションがアクティブになり、最長ランが`4`、したがって、答えは次のとおりです`1`。 このアルゴリズムは、固定幅の要件を正しく適用します。 

考慮する：```
3
7 3 5
1
1 3 3
```閾値で`5`、アクティブなポジションは次のとおりです。`{1,3}`そして最長ランは`1`, したがって、実現可能性は失敗します。 閾値で`3`、すべてのポジションがアクティブになり、最長ランは`3`、必要な幅に正確に一致します。 答えは`3`、これは区間全体の最小の高さです。
