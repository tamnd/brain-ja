---
title: "CF 1418F - 同等の製品"
description: "$x1$ の値を修正します。 同じ積の 2 つの異なる表現を見つける必要があります。$$x1 y1 = x2 y2,$$ with $x2 x1$, $y2 < y1$、および $[l,r]$ 内にある積です。 出力は、$1$ から $n$ までの $x1$ ごとに個別に必要です。"
date: "2026-06-11T06:52:23+07:00"
tags: ["codeforces", "competitive-programming", "data-structures", "math", "number-theory", "two-pointers"]
categories: ["algorithms"]
codeforces_contest: 1418
codeforces_index: "F"
codeforces_contest_name: "Educational Codeforces Round 95 (Rated for Div. 2)"
rating: 3000
weight: 1418
solve_time_s: 122
verified: true
draft: false
---

[CF 1418F - 同等の製品](https://codeforces.com/problemset/problem/1418/F)

 **評価:** 3000
 **タグ:** データ構造、数学、数論、2 つのポインター
 **解決時間:** 2 分 2 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 の値を修正します$x_1$。 同じ製品の 2 つの異なる表現を見つける必要があります。$$x_1 y_1 = x_2 y_2,$$と$x_2 > x_1$、$y_2 < y_1$、そしてその中に横たわっている製品$[l,r]$。 

出力はそれぞれに個別に必要です$x_1$から$1$に$n$。 いくつかの値については、$x_1$有効な 4 倍数が存在しない場合は、次のように出力します。$-1$。 

制約が問題を難しくしているのです。 両方$n$そして$m$到達できる$2 \cdot 10^5$, したがって、二次関数は直ちに不可能になります。 すべてのペアを反復しても$(x_2,y_2)$あらゆる人にとって$x_1$数百億回の操作が必要になります。 

製品範囲の制約も誤解を招く可能性があります。 単純なアプローチでは、すべての製品を反復しようとする可能性があります。$[l,r]$、 しかし$r$と同じ大きさにすることができます$nm$までです$4 \cdot 10^{10}$。 

有効範囲が次の場合に、微妙なエッジ ケースが 1 つ発生します。$y_1$は空です。 

例えば：```
n = 3
m = 5
l = 20
r = 20
```のために$x_1 = 3$、$$y_1 = \frac{20}{3}$$は整数ではなく、値もありません$y_1$製品範囲と$1 \le y_1 \le m$。 間隔が空ではないことを前提とした実装では、無効な応答が生成されます。 

もう一つの簡単な間違いは、それを忘れることです$y_2$厳密に以下より小さくなければなりません$y_1$。 

考慮する：```
x1 = 4
y1 = 6
x2 = 8
y2 = 3
```これは有効です。$24 = 24$そして$3 < 6$。 

しかし、もし$a=b$後述する減速比構築では、$y_2=y_1$、これは禁止されています。 常に強制しなければなりません$a>b$。 

3 番目の罠は、同等の積を構築することですが、$x_2>n$。 

例えば：```
n = 5
x1 = 4
```比率を使用する$a/b = 3/2$与える$$x_2 = 4 \cdot \frac{3}{2} = 6,$$制限に違反するものは$x_2$。 比率は次の制限を満たす必要があります。$x_2$同じように。 

## アプローチ

 修正するとします$x_1$。 

有効な値は、$y_1$まさに満足のいくものです$$l \le x_1 y_1 \le r,$$と共に$1 \le y_1 \le m$。 これにより間隔が生まれます$$[y_{\text{low}}, y_{\text{high}}].$$ブルートフォースソリューションでは、すべてのことを試します。$y_1$その間隔で、同じ積の別の因数分解を検索します。 可能な限りのチェックをしても$x_2$値はおおよそにつながります$$O(nm)$$さらに悪いことに、限界をはるかに超えています。 

重要な観察は、等しい積は実際には比率に関する記述であるということです。 

仮定する$$x_1 y_1 = x_2 y_2.$$書く$$\frac{x_2}{x_1}=\frac{a}{b}$$最低の言葉で。 

以来$x_2>x_1$、 我々は持っています$a>b$。 

端数が減りますので、$b$分けなければなりません$x_1$。 また、$$\frac{y_1}{y_2}=\frac{a}{b},$$それで$a$分けなければなりません$y_1$。 

したがって、以下を見つけることができれば解決策が存在します。$$b \mid x_1,$$そしていくつか$$a>b$$次のような:$$x_1 \cdot \frac{a}{b} \le n,$$少なくとも 1 つの数字が含まれています$[y_{\text{low}},y_{\text{high}}]$で割り切れます$a$。 

ここで、問題は除数クエリの問題になります。 

間隔ごとに$[y_{\text{low}},y_{\text{high}}]$のどの値を知る必要がありますか$a$その間隔内に少なくとも 1 つの倍数が存在します。 次に、約数ごとに$b$の$x_1$、最小のアクティブが必要です$a$満足のいく$$b < a \le \frac{nb}{x_1}.$$間隔は次のように変化します$x_1$、そしてそれらの端点は単調に移動します。 これにより、2 ポインターのスイープが可能になります。 現在アクティブな間隔内で倍数を持つすべての約数を維持します。 セグメント ツリーを使用すると、最初のアクティブなセグメントを見つけることができます。$a$範囲内で。 

これにより、問題全体が大まかに要約されます$O((n+m)\log^2 n)$。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース |$O(nm)$それより悪い |$O(1)$| 遅すぎる |
 | 最適 |$O((n+m)\log^2 n)$|$O(n+m)$| 承認済み |

 ## アルゴリズムのチュートリアル

 1. 以下のすべての数値のすべての約数を事前計算します。$1$に$\max(n,m)$ふるいスタイルのループを使用します。 
2. あらゆる人にとって$x$、その除数リストを事前計算します。 
3. それぞれについて$x$、計算する$$L_x=\max\!\left(1,\left\lceil\frac{l}{x}\right\rceil\right),$$そして$$R_x=\min\!\left(m,\left\lfloor\frac{r}{x}\right\rfloor\right).$$これらはまさに次の有効な値です。$y_1$。 

1. プロセス$x$から$n$まで$1$。 
2. 現在の間隔を維持する$[curL,curR]$、最初は空です。 
3. 目標間隔が前回から変更されたら、2 つのポインタを移動します。 値が指定されるたびに、$y$が区間に入り、次の約数をすべて加算します$y$。 値が指定されるたびに、$y$間隔を残し、次の約数をすべて削除します。$y$。 
4. すべての約数について$a$、カウンタを次の倍数に等しく保ちます。$a$現在インターバル内に存在します。 
5. 価値観$a$カウンタが正の場合はアクティブです。 アクティブな値をセグメント ツリーに保存します。 
6. 現在のところ$x$、すべての約数を反復処理します$b$の$x$。 
7. の最大許容値$a$は$$\text{limit}=\left\lfloor \frac{nb}{x}\right\rfloor.$$1. セグメント ツリーにクエリを実行して、最初のアクティブな値を取得します。$$[b+1,\text{limit}].$$1. そのような場合$a$存在する、構築する$$y_1=\left\lceil\frac{L_x}{a}\right\rceil a.$$なぜなら$a$がアクティブな場合、この倍数は間隔内にあることが保証されます。 

1.セット$$x_2=x_1\frac{a}{b}, \qquad y_2=y_1\frac{b}{a}.$$1. 4 倍音を出力し、これの検索を停止します。$x_1$。 
2. 約数がない場合$b$有効な結果を生成します$a$、出力$-1$。 

### なぜ効果があるのか

 維持される間隔には、次の有効な値が正確に含まれます。$y_1$現在のために$x_1$。 

除数$a$有効な場合にのみアクティブになります$y_1$で割り切れます$a$。 セグメント ツリー クエリはアクティブなセグメントを見つけます。$a$満足のいく$a>b$そして$x_1a/b \le n$。 

以来$b\mid x_1$そして$a\mid y_1$、$$x_2=x_1\frac{a}{b}$$そして$$y_2=y_1\frac{b}{a}$$は整数です。 

不平等$a>b$保証する$$x_2>x_1$$そして$$y_2<y_1.$$平等$$x_1y_1=x_2y_2$$構築から直接続きます。 出力されたすべての 4 倍数は有効であり、すべての有効な 4 倍数は何らかの約数に対応します。$b$と有効約数$a$したがって、検索で解決策を見逃すことはありません。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

def first_in_range(seg, size, l, r):
    if l > r:
        return -1

    def dfs(node, nl, nr):
        if nr < l or nl > r or seg[node] == 0:
            return -1
        if nl == nr:
            return nl
        mid = (nl + nr) // 2
        left = dfs(node * 2, nl, mid)
        if left != -1:
            return left
        return dfs(node * 2 + 1, mid + 1, nr)

    return dfs(1, 1, size)

def solve():
    n, m = map(int, input().split())
    l, r = map(int, input().split())

    mx = max(n, m)

    divisors = [[] for _ in range(mx + 1)]
    for d in range(1, mx + 1):
        for v in range(d, mx + 1, d):
            divisors[v].append(d)

    size = 1
    while size < m:
        size <<= 1

    seg = [0] * (2 * size)
    cnt = [0] * (m + 1)

    def activate(a):
        p = a + size - 1
        seg[p] = 1
        p //= 2
        while p:
            seg[p] = seg[p * 2] | seg[p * 2 + 1]
            p //= 2

    def deactivate(a):
        p = a + size - 1
        seg[p] = 0
        p //= 2
        while p:
            seg[p] = seg[p * 2] | seg[p * 2 + 1]
            p //= 2

    def add_y(y):
        for d in divisors[y]:
            cnt[d] += 1
            if cnt[d] == 1:
                activate(d)

    def remove_y(y):
        for d in divisors[y]:
            cnt[d] -= 1
            if cnt[d] == 0:
                deactivate(d)

    ans = [None] * (n + 1)

    curL = 1
    curR = 0

    for x in range(n, 0, -1):
        L = (l + x - 1) // x
        R = r // x

        if L < 1:
            L = 1
        if R > m:
            R = m

        if L > R:
            ans[x] = None
            continue

        while curR < R:
            curR += 1
            add_y(curR)

        while curR > R:
            remove_y(curR)
            curR -= 1

        while curL < L:
            remove_y(curL)
            curL += 1

        while curL > L:
            curL -= 1
            add_y(curL)

        found = None

        for b in divisors[x]:
            limit = (n * b) // x
            if limit <= b:
                continue

            a = first_in_range(seg, size, b + 1, min(limit, m))
            if a == -1:
                continue

            y1 = ((L + a - 1) // a) * a
            x2 = x * a // b
            y2 = y1 * b // a

            found = (x, y1, x2, y2)
            break

        ans[x] = found

    out = []
    for x in range(1, n + 1):
        if ans[x] is None:
            out.append("-1")
        else:
            out.append(" ".join(map(str, ans[x])))

    sys.stdout.write("\n".join(out))

if __name__ == "__main__":
    solve()
```除数ふるいはソリューションの基礎です。 これにより、ウィンドウ エンドポイントのすべての更新で、開始値または終了値の約数のみを操作できるようになります。 

2 つのポインターは有効な間隔を正確に維持します。$y_1$現在の値$x$。 加工しておりますので、$x$から$n$まで$1$、両方の間隔のエンドポイントは前方にのみ移動するため、 のすべての値は$y$挿入および取り外しは最大 1 回です。 

セグメント ツリーには、除数がアクティブかどうかのみが保存されます。 クエリは範囲内の最初のアクティブな値を検索します。 それはまさに比率の定式化で必要な操作です。 

再構築のステップは間違いやすいです。 選ばれたもの$y_1$の倍数でなければなりません$a$それは現在の間隔内にあります。 最初の複数を取得すると、これが保証されます。 

## 実用的な例

 ### サンプル 1

 入力:```
8 20
91 100
```のために$x_1=6$:$$L=\left\lceil\frac{91}{6}\right\rceil=16, \qquad R=\left\lfloor\frac{100}{6}\right\rfloor=16.$$| × | 間隔 | b | | を選択しました y1 | x2 | y2 |
 | --- | --- | --- | --- | --- | --- | --- |
 | 6 | [16,16] | 3 | 4 | 16 | 8 | 12 |

 以下を取得します。$$6 \cdot 16 = 8 \cdot 12 = 96.$$これは比率の構築を示しています。 区間には値が 1 つだけ含まれていますが、有効な因数分解はまだ存在します。 

### サンプル 2

 入力:```
4 5
1 10
```のために$x_1=1$:$$L=1,\quad R=5.$$| × | 間隔 | b | | を選択しました y1 | x2 | y2 |
 | --- | --- | --- | --- | --- | --- | --- |
 | 1 | [1,5] | 1 | 2 | 2 | 2 | 1 |

 のために$x_1=2$:

 | × | 間隔 | b | | を選択しました y1 | x2 | y2 |
 | --- | --- | --- | --- | --- | --- | --- |
 | 2 | [1,5] | 2 | 3 | 3 | 3 | 2 |

 トレースは、アルゴリズムが次の約数をどのように検索するかを示します。$x$最初のアクティブなものを選択します$a$の限界を満たす$x_2$。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |$O((n+m)\log^2 n)$| 除数の更新とセグメント ツリーのクエリ |
 | スペース |$O(n+m)$| 除数リスト、カウンタ、セグメント ツリー |

 約数の合計は次のようにカウントされます$2 \cdot 10^5$についてです$N \log N$、管理が簡単です。 セグメント ツリー操作では、別の対数係数が追加されます。 ソリューションは制限内に問題なく収まります。 

## テストケース```python
# helper skeleton

import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    out = io.StringIO()

    # real testing would call solve() and capture stdout
    return out.getvalue()

# Sample outputs are not unique for this problem,
# so exact-string assertions are inappropriate.
# Instead, a checker should verify validity.

# Minimum size
inp = """\
1 1
1 1
"""

# No solution possible because x2 > x1 cannot exist.

# Boundary interval with single product
inp2 = """\
2 2
4 4
"""

# Small dense range
inp3 = """\
4 5
1 10
"""

# Large boundary style test
inp4 = f"""\
200000 200000
1 40000000000
"""

# Interval producing many empty y-ranges
inp5 = """\
5 5
24 25
"""
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |$n=m=1$| 全て$-1$| 可能な最小のインスタンス |
 | 単一製品間隔 | 有効または$-1$あたり$x$| タイトなインターバルの処理 |
 | サンプル 2 | 有効なクアドルプル | 基本的な正しさ |
 | 最大制限 | すぐに終わる | パフォーマンス |
 | 狭い高音域 | さまざまな答え | 空の間隔の処理 |

 ## 特殊なケース

 次のことを考慮してください。```
3 5
20 20
```のために$x_1=3$、$$L=\left\lceil\frac{20}{3}\right\rceil=7, \qquad R=\left\lfloor\frac{20}{3}\right\rfloor=6.$$以来$L>R$、間隔は空です。 アルゴリズムはすぐに出力します$-1$。 

ここで次のことを考えてみましょう。```
4 6
24 24
```のために$x_1=4$、唯一の有効な値は$y_1=6$。 アルゴリズムは、間隔内に存在するすべての約数をアクティブにします。$[6,6]$。 選ぶかも知れません$b=2$そして$a=3$、降伏$$x_2=6,$$を超えるもの$n=4$。 バウンド$$a \le \frac{nb}{x_1}$$建設前にこの候補を拒否します。 

ついに：```
4 5
1 10
```アルゴリズムが選択された場合$a=b$、 それから$$y_2=y_1.$$クエリ範囲は次から始まります$b+1$, したがって、このようなことは起こり得ません。 厳格な不平等は、後のチェックによってではなく、構造的に強制されます。
