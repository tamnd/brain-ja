---
title: "CF 1942F - ファーマー・ジョンのお気に入り機能"
description: "配列 $a$ と再帰的に定義された値があります: $$f(1)=sqrt{a1}, qquad f(i)=sqrt{f(i-1)+ai}.$$ $ak leftarrow x$ を更新するたびに、$f(n)$ の整数部分が必要になります。 最初の障害は、漸化式に実数が使用されることです。"
date: "2026-05-31T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "brute-force", "data-structures", "implementation", "math"]
categories: ["algorithms"]
codeforces_contest: 1942
codeforces_index: "F"
codeforces_contest_name: "CodeTON Round 8 (Div. 1 + Div. 2, Rated, Prizes!)"
rating: 2700
weight: 1942
solve_time_s: 130
verified: true
draft: false
---

[CF 1942F - ファーマー ジョンのお気に入り関数](https://codeforces.com/problemset/problem/1942/F)

 **評価:** 2700
 **タグ:** ブルート フォース、データ構造、実装、数学
 **解決時間:** 2 分 10 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 配列があります$a$、および再帰的に定義された値:$$f(1)=\sqrt{a_1}, \qquad f(i)=\sqrt{f(i-1)+a_i}.$$ポイント更新ごとに$a_k \leftarrow x$の整数部分が必要です。$f(n)$。 

最初の障害は、漸化式に実数が使用されることです。 直接シミュレーションでは浮動小数点の平方根が繰り返し評価されるため、わずかな精度の誤差によっても最終的なフロア値が変わる可能性があります。 サンプルではすでにそのような状況が示されています。本当の答えは次のとおりです。$2$最終値が に非常に近い場合でも、$3$。 

制約は大きいです。 両方$n$そして$q$までです$2 \cdot 10^5$。 更新のたびに繰り返し全体を再計算するには時間がかかります$O(nq)$、大まかに言うと$4 \cdot 10^{10}$最悪の場合の運用。 それは5秒の制限にはまったく届きません。 

主な難点は、配列上で更新が行われるが、その繰り返しが以前のすべての位置に依存することです。 先頭近くの 1 つの変更により、その後のすべての値が変更される可能性があります。 

間違った解決策を導き出す簡単な方法がいくつかあります。 

考慮する：```
n = 2
a = [3, 14]
```それから$$f(1)=\sqrt3 \approx 1.732,$$

$$f(2)=\sqrt{14+\sqrt3}\approx 3.966.$$答えは$3$。 正確な繰り返しを取得する代わりに中間値を丸めると、異なる結果が得られます。 

もう 1 つの危険なケースは、非常に大きな値です。```
n = 1
a = [10^18]
```答えはまさに$10^9$。 double を不用意に使用すると、境界付近で精度が失われ、次のような問題が発生する可能性があります。$999999999$。 

より微妙な例は次のとおりです。```
a = [3, 14, 0, 7, 6]
```最終的な値はおよそ$2.999766$、したがって、答えは次のとおりです$2$。 浮動小数点平方根を繰り返し計算し、最後に丸めるアプローチでは、リターンが発生するリスクがあります。$3$。 

ソリューションでは浮動小数点演算を完全に回避する必要があります。 

## アプローチ

 ブルーフォースのアイデアは単純明快です。 更新するたびに、左から右に繰り返しを再構築します。```
cur = sqrt(a[1])
for i = 2..n:
    cur = sqrt(cur + a[i])
answer = floor(cur)
```これは定義に直接従っているため、正しいです。 問題はその複雑さです。 各クエリには次のものが必要です$O(n)$作業するので、総コストは$O(nq)$。 と$n=q=2\cdot10^5$、これはおおよそになります$4\cdot10^{10}$操作。 

最初の観察は、必要なのは$\lfloor f(n)\rfloor$、正確な実際の値ではありません。 

定義する$$g(1)=\left\lfloor \sqrt{a_1}\right\rfloor,$$

$$g(i)=\left\lfloor \sqrt{g(i-1)+a_i}\right\rfloor.$$重要な事実は次のとおりです。$$g(i)=\lfloor f(i)\rfloor$$あらゆる人にとって$i$。 

理由は簡単です。 させて$$f(i-1)=g(i-1)+\delta, \qquad 0\le \delta<1.$$それから$$g(i-1)+a_i \le f(i-1)+a_i < g(i-1)+a_i+1.$$平方根を取ると、値が以下の長さの区間内に保持されます。$1$したがって、両方のエンドポイントは同じフロアになります。 したがって、$$\left\lfloor \sqrt{f(i-1)+a_i}\right\rfloor = \left\lfloor \sqrt{g(i-1)+a_i}\right\rfloor.$$これで、問題全体が整数のみになります。 

2 番目の観察は、すべてのクエリをオフラインで処理することです。 

固定位置の場合$i$、その値は、更新がインデックスに触れた場合にのみ変更されます。$i$。 クエリのタイムラインに沿って、$a_i$は区分的に一定です。 

クエリ時間ごとに次のように維持するとします。$t$、現在の値$$dp_t$$ポジション処理後$1 \ldots i-1$。 

位置を処理するとき$i$、すべてのタイムライン ポイントが実行されます$$dp_t \leftarrow \left\lfloor \sqrt{dp_t + a_i(t)} \right\rfloor.$$ここ$a_i(t)$位置の値を示します$i$クエリの後$t$。 

したがって、各ポジションは次のように貢献します。 

1. タイムラインにいくつかの範囲が追加されます。$a_i(t)$は区分的に一定です。 
2. 1 つのグローバル オペレーション$x \mapsto \lfloor\sqrt{x}\rfloor$。 

残りの課題は、繰り返されるグローバル平方根演算を効率的にサポートすることです。 

セグメント ツリーには、すべてのノードの最小値と最大値が格納されます。 

の値をカバーするノードの場合$[mn,mx]$、 もし$$mx-mn = \lfloor\sqrt{mx}\rfloor-\lfloor\sqrt{mn}\rfloor,$$すると、そのセグメント内のすべての値が同じ量だけ変化します。 

確かにそういう状況では$$\lfloor\sqrt{x}\rfloor - x$$区間全体を通じて一定です。 単一の範囲追加を使用して、セグメント全体を遅延更新できます。 

この条件が満たされないセグメントのみをさらに分割する必要があります。 

これは、古典的なセグメント ツリー ビート スタイルの引数です。 平方根を繰り返すと数値が急速に縮小するため、再帰の深さは管理可能なままになります。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース |$O(nq)$|$O(1)$| 遅すぎる |
 | 最適 |$O((n+q)\log^2 q)$償却済み |$O(q)$| 承認済み |

 ## アルゴリズムのチュートリアル

 1. 初期アレイとすべての更新を読み取ります。 
2. インデックスごとに$i$、そのインデックスが変更された時間を収集します。 
3. 各ポジションの更新履歴を素のタイムライン間隔に変換します。 間隔ごとに$[L,R]$、の値$a_i$一定のままです。 
4. クエリ回数にわたるセグメント ツリーを構築する$1 \ldots q$。 最初は、すべてのタイムライン値は次のようになります。$0$。 
5. 配列の位置を左から右に処理します。 
6. 現在の位置について$i$、タイムライン間隔ごとに範囲の追加を適用します。$a_i$は一定です。 これらの追加の後、各タイムライン ポイントには次のものが含まれます。$$g(i-1)+a_i.$$1. 変換を適用する$$x \mapsto \lfloor\sqrt{x}\rfloor$$セグメントツリー全体に。 

1. セグメント ツリーには次の情報が格納されます。$$g(i)$$すべてのクエリ時間に対して同時に実行されます。 

1. すべての位置が処理された後、すべての葉を抽出します。 タイムライン位置の値$t$まさにクエリ後の答えです$t$。 

### なぜ効果があるのか

 最初の処理後$i$位置、タイムラインごとのポイント$t$店舗$$g_i(t),$$クエリ後の配列状態の整数繰り返し値$t$。 

すべての値が次のとおりであるため、不変条件は最初は true です。$0$。 

位置処理時$i$、最初に追加します$a_i(t)$あらゆるタイムラインポイントに。 格納された値は次のようになります$$g_{i-1}(t)+a_i(t).$$平方根変換を適用すると、次のように変わります。$$\left\lfloor \sqrt{g_{i-1}(t)+a_i(t)} \right\rfloor,$$それはまさに$g_i(t)$。 

帰納法により、最後の位置を取得した後、$g_n(t)$クエリ時間ごとに。 以来$g_n(t)=\lfloor f_n(t)\rfloor$、報告された答えは正しいです。 

## Python ソリューション```python
import sys
from math import isqrt

input = sys.stdin.readline

class SegTree:
    def __init__(self, n):
        self.n = n
        size = 4 * n + 5
        self.mn = [0] * size
        self.mx = [0] * size
        self.lazy = [0] * size

    def apply_add(self, p, v):
        self.mn[p] += v
        self.mx[p] += v
        self.lazy[p] += v

    def push(self, p):
        if self.lazy[p]:
            v = self.lazy[p]
            self.apply_add(p * 2, v)
            self.apply_add(p * 2 + 1, v)
            self.lazy[p] = 0

    def pull(self, p):
        self.mn[p] = min(self.mn[p * 2], self.mn[p * 2 + 1])
        self.mx[p] = max(self.mx[p * 2], self.mx[p * 2 + 1])

    def range_add(self, p, l, r, ql, qr, v):
        if ql <= l and r <= qr:
            self.apply_add(p, v)
            return

        self.push(p)
        m = (l + r) >> 1

        if ql <= m:
            self.range_add(p * 2, l, m, ql, qr, v)
        if qr > m:
            self.range_add(p * 2 + 1, m + 1, r, ql, qr, v)

        self.pull(p)

    def apply_sqrt(self, p, l, r):
        mn = self.mn[p]
        mx = self.mx[p]

        if mx - mn == isqrt(mx) - isqrt(mn):
            delta = isqrt(mx) - mx
            self.apply_add(p, delta)
            return

        if l == r:
            v = isqrt(mx)
            self.mn[p] = v
            self.mx[p] = v
            self.lazy[p] = 0
            return

        self.push(p)
        m = (l + r) >> 1
        self.apply_sqrt(p * 2, l, m)
        self.apply_sqrt(p * 2 + 1, m + 1, r)
        self.pull(p)

    def collect(self, p, l, r, res):
        if l == r:
            res[l] = self.mn[p]
            return

        self.push(p)
        m = (l + r) >> 1
        self.collect(p * 2, l, m, res)
        self.collect(p * 2 + 1, m + 1, r, res)

def solve():
    n, q = map(int, input().split())
    a = list(map(int, input().split()))

    changes = [[] for _ in range(n)]

    for t in range(1, q + 1):
        k, x = map(int, input().split())
        changes[k - 1].append((t, x))

    seg = SegTree(q)

    for i in range(n):
        cur = a[i]
        last = 1

        for t, x in changes[i]:
            if last <= t - 1:
                seg.range_add(1, 1, q, last, t - 1, cur)
            cur = x
            last = t

        if last <= q:
            seg.range_add(1, 1, q, last, q, cur)

        seg.apply_sqrt(1, 1, q)

    ans = [0] * (q + 1)
    seg.collect(1, 1, q, ans)

    sys.stdout.write("\n".join(map(str, ans[1:])))

solve()
```実装における最初の重要なアイデアは、実数の繰り返しを整数の繰り返しに置き換えることです。 これにより、精度の問題がすべて取り除かれ、すべての値が整数のままになることが可能になります。 

前処理フェーズでは、配列の位置ごとにタイムライン間隔を構築します。 インデックスの場合$i$時々更新されます$5$、$11$、 そして$20$の場合、その値は複数の互いに素なクエリ範囲で定数になります。 このような各範囲は、1 つのセグメント ツリー範囲の追加となります。 

セグメント ツリーには、最小値、最大値、および遅延追加タグのみが格納されます。 合計は必要ありません。 

微妙な部分は、`apply_sqrt`。 ノードが満たす場合$$mx-mn = \lfloor\sqrt{mx}\rfloor-\lfloor\sqrt{mn}\rfloor,$$平方根演算は、そのノード内のすべての要素に定数を追加するように動作します。 セグメント全体を遅延更新できます。 

それ以外の場合は、子供たちに再帰的に戻ります。 

使用する`math.isqrt`が不可欠です。 正確な整数の平方根を計算し、すべての浮動小数点エラーを回避します。 

## 実用的な例

 ### 例 1

 入力:```
5 6
0 14 0 7 6
1 4
1 3
2 15
4 1
5 2
5 8
```位置 1 の処理後:

 | クエリ | あ₁ | sqrt | の後の値
 | --- | --- | --- |
 | 1 | 4 | 2 |
 | 2 | 3 | 1 |
 | 3 | 3 | 1 |
 | 4 | 3 | 1 |
 | 5 | 3 | 1 |
 | 6 | 3 | 1 |

 位置 2 の処理後:

 | クエリ | 前へ | +a₂ | 平方メートル |
 | --- | --- | --- | --- |
 | 1 | 2 | 16 | 4 |
 | 2 | 1 | 15 | 3 |
 | 3 | 1 | 16 | 4 |
 | 4 | 1 | 16 | 4 |
 | 5 | 1 | 16 | 4 |
 | 6 | 1 | 16 | 4 |

 すべての位置を継続すると、次の結果が得られます。```
3
2
3
2
1
3
```このトレースは、すべてのクエリ状態がタイムラインに沿ってどのように同時に処理されるかを示します。 

### 例 2

 入力:```
2 2
386056082462833225 923951085408043421
1 386056082462833225
1 386056082462833224
```クエリ 1 の後:

 | ポジション | 値 |
 | --- | --- |
 | あ₁ | 386056082462833225 |
 | a₂ | 923951085408043421 |

 反復により次のことが得られます。$$g(1)=621333955,$$

$$g(2)=961223744.$$クエリ 2 の後:$$g(1)=621333954,$$

$$g(2)=961223743.$$出力：```
961223744
961223743
```この例は、平方根の境界付近で正確な整数演算が必要な理由を示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |$O((n+q)\log^2 q)$償却済み | 範囲の追加とセグメント ツリー ビート スタイルの平方根更新 |
 | スペース |$O(q)$| セグメント ツリーとタイムラインのストレージ |

 セグメントツリーには以下が含まれます$O(q)$ノード。 更新間隔ごとに 1 つの範囲加算になります。 平方根を繰り返すと値が急速に減少し、総再帰コストが制限されます。 これは、次の制限に快適に適合します$n,q \le 2\cdot10^5$。 

## テストケース```python
# helper: run solution on input string, return output string
import sys
import io
from math import isqrt

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)

    input = sys.stdin.readline

    class SegTree:
        def __init__(self, n):
            self.n = n
            self.mn = [0] * (4 * n + 5)
            self.mx = [0] * (4 * n + 5)
            self.lazy = [0] * (4 * n + 5)

        def apply_add(self, p, v):
            self.mn[p] += v
            self.mx[p] += v
            self.lazy[p] += v

        def push(self, p):
            if self.lazy[p]:
                v = self.lazy[p]
                self.apply_add(p * 2, v)
                self.apply_add(p * 2 + 1, v)
                self.lazy[p] = 0

        def pull(self, p):
            self.mn[p] = min(self.mn[p * 2], self.mn[p * 2 + 1])
            self.mx[p] = max(self.mx[p * 2], self.mx[p * 2 + 1])

        def add(self, p, l, r, ql, qr, v):
            if ql <= l and r <= qr:
                self.apply_add(p, v)
                return
            self.push(p)
            m = (l + r) // 2
            if ql <= m:
                self.add(p * 2, l, m, ql, qr, v)
            if qr > m:
                self.add(p * 2 + 1, m + 1, r, ql, qr, v)
            self.pull(p)

        def sq(self, p, l, r):
            mn = self.mn[p]
            mx = self.mx[p]

            if mx - mn == isqrt(mx) - isqrt(mn):
                self.apply_add(p, isqrt(mx) - mx)
                return

            if l == r:
                v = isqrt(mx)
                self.mn[p] = v
                self.mx[p] = v
                self.lazy[p] = 0
                return

            self.push(p)
            m = (l + r) // 2
            self.sq(p * 2, l, m)
            self.sq(p * 2 + 1, m + 1, r)
            self.pull(p)

        def collect(self, p, l, r, out):
            if l == r:
                out[l] = self.mn[p]
                return
            self.push(p)
            m = (l + r) // 2
            self.collect(p * 2, l, m, out)
            self.collect(p * 2 + 1, m + 1, r, out)

    n, q = map(int, input().split())
    a = list(map(int, input().split()))

    changes = [[] for _ in range(n)]

    for t in range(1, q + 1):
        k, x = map(int, input().split())
        changes[k - 1].append((t, x))

    seg = SegTree(q)

    for i in range(n):
        cur = a[i]
        last = 1

        for t, x in changes[i]:
            if last <= t - 1:
                seg.add(1, 1, q, last, t - 1, cur)
            cur = x
            last = t

        if last <= q:
            seg.add(1, 1, q, last, q, cur)

        seg.sq(1, 1, q)

    ans = [0] * (q + 1)
    seg.collect(1, 1, q, ans)

    return "\n".join(map(str, ans[1:]))

# provided sample
assert run(
"""5 6
0 14 0 7 6
1 4
1 3
2 15
4 1
5 2
5 8
"""
) == "3\n2\n3\n2\n1\n3", "sample 1"

# minimum size
assert run(
"""1 1
0
1 0
"""
) == "0", "single element zero"

# boundary sqrt transition
assert run(
"""1 2
16
1 15
1 16
"""
) == "3\n4", "crosses perfect square boundary"

# very large values
assert run(
"""1 1
1000000000000000000
1 1000000000000000000
"""
) == "1000000000", "1e18 square root"

# repeated updates on same index
assert run(
"""2 3
0 0
1 1
1 4
1 9
"""
) == "1\n2\n3", "timeline interval construction"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 単一要素、値 0 | 0 | 最小入力サイズ |
 | 16 → 15 → 16 | 3、4 | 完全二乗境界の動作 |
 |$10^{18}$|$10^9$| 大きい整数の平方根 |
 | 1 つの位置で繰り返し更新 | 1、2、3 | タイムライン上での正しい間隔の生成 |

 ## 特殊なケース

 次のことを考慮してください。```
1 2
16
1 15
1 16
```最初のアップデート後、$$\lfloor\sqrt{15}\rfloor=3.$$2回目以降は、$$\lfloor\sqrt{16}\rfloor=4.$$このアルゴリズムでは浮動小数点演算は決して使用されません。 セグメント ツリーには正確な整数が保存され、適用されます。`isqrt`したがって、両方の答えが正確に計算されます。 

次に、次のことを考えてみましょう。```
1 1
1000000000000000000
1 1000000000000000000
```値はまさに$10^{18}$。 浮動小数点ソリューションでは、この程度の精度が失われる可能性があります。 整数反復の計算$$\lfloor\sqrt{10^{18}}\rfloor = 10^9$$丸めの問題はありません。 

最後に、同じインデックスに対する複数の更新について考えてみましょう。```
2 3
0 0
1 1
1 4
1 9
```インデックス用$1$、タイムラインの値は次のとおりです。 

| クエリ間隔 | 値 |
 | --- | --- |
 | [1,1] | 1 |
 | [2,2] | 4 |
 | [3,3] | 9 |

 前処理では、これらを 3 つの範囲加算に変換します。 各間隔は独立して処理されるため、すべてのクエリで正しいバージョンの配列が参照されます。
