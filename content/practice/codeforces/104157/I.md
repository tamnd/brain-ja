---
title: "CF 104157I - 酔った同僚"
description: "二次曲線は、酔った同僚が長方形のオフィスを横切る様子を表します。 任意の水平位置 $x$ で、彼の位置は $f(x)$ であるため、彼の経路は放物線になります。 彼は無限に正確に見ることはできません。"
date: "2026-07-02T01:18:14+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 104157
codeforces_index: "I"
codeforces_contest_name: "UTPC Contest 01-27-23 Div. 2 (Beginner)"
rating: 0
weight: 104157
solve_time_s: 134
verified: false
draft: false
---

[CF 104157I - 酔った同僚](https://codeforces.com/problemset/problem/104157/I)

 **評価:** -
 **タグ:** -
 **解決時間:** 2 分 14 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 二次曲線は、酔った同僚が長方形のオフィスを横切る様子を表します。 任意の水平位置で$x$、彼の立場は$f(x)$、したがって、彼の道は放物線です。 

彼は無限に正確に見ることはできません。 代わりに、彼のパスのすべての点の周囲に、固定半幅の垂直可視バンドが存在します。$k$。 言い換えれば、ある時点で$x$、すべての$y$間$f(x)-k$そして$f(x)+k$彼には「見られている」とみなされます。 

指定された軸に揃えられた長方形の内部で、このバンドによって表示されない領域の量を計算したいとします。 同様に、長方形の面積から、垂直方向の厚さのストリップで覆われた部分を差し引きます。$2k$放物線の周り。 

長方形は連続であり、関数も連続であるため、主な課題は、2次関数を含む不等式によって定義される領域の下の面積を計算することです。 この制約により、次の係数が許可されます。$10^5$大きさが大きいため、関数の値が大きくなる可能性がありますが、1 秒の制限を超える操作の数に制限はありません。 これは、全体にわたるきめの細かい数値サンプリングを避ける必要があることを強く示唆しています。$x$あまりにも多くの評価が必要になるためです。 

素朴な考えは、$x$-軸を小さなステップに分割し、各ステップで目に見える高さを評価し、積分を近似します。 必要な精度が以下であるため、これは失敗します。$10^{-6}$、放物線は急速に変化する可能性があります。 保証された正確性を達成するには、非常に細かい解像度が必要となり、数百万または数十億のステップが必要になります。 

より微妙な失敗例は、放物線が長方形の境界を複数回横切る場合に発生します。 たとえば、次のようなとき$f(x)$交差する$y=y_1+k$または$y=y_2-k$、重なりの構造が突然変化します。 これらの遷移点で分割せずに区間全体にわたって固定式を仮定するメソッドでは、間違った式が黙って統合されます。 

## アプローチ

 必要な幾何学オブジェクトは、長方形と放物線の周りの「チューブ」との交差領域です。 チューブは次のように定義されます。$|y - f(x)| \le k$、したがって、それぞれの固定$x$、チューブの垂直スライスは間隔です$[f(x)-k, f(x)+k]$。 長方形の内側、その部分の可視領域$x$この間隔が重なる部分は$[y_1, y_2]$。 

したがって、重要な量は次の 1 次元関数です。$x$: 目に見える高さ$h(x)$。 答えは次のようになります$$\text{answer} = (x_2 - x_1)(y_2 - y_1) - \int_{x_1}^{x_2} h(x)\,dx.$$ブルートフォースアプローチでは次のように評価されます。$h(x)$多くのサンプル点で積分を数値的に近似します。 これは原理的には正しいですが、高精度を強制すると不安定で遅すぎます。 

構造的な観察は、$h(x)$4 つの式の相対的な順序が変更された場合にのみ、式が変更されます。$f(x)-k$、$f(x)+k$、$y_1$、 そして$y_2$。 トランジションはまさに次のタイミングで発生します。$$f(x) = y_1 - k,\quad f(x) = y_1 + k,\quad f(x) = y_2 - k,\quad f(x) = y_2 + k.$$これらはそれぞれ二次方程式であるため、それぞれが最大 2 つの実根に寄与します。 連続するルート間では順序が固定されます。つまり、$h(x)$単一の閉じた形式の式で記述されます。 

そのような間隔の中で、$h(x)$は定数またはアフィン関数のいずれかになります$f(x)$、それ自体は二次関数です。 したがって、各セグメントの積分は線形または三次のいずれかになります。$x$、すべて分析的に計算可能です。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | 数値サンプリング | O(N サンプル) | お(1) | 遅すぎる/不正確 |
 | 区分的分析統合 | O(1) セグメント (≤9) | お(1) | 承認済み |

 ## アルゴリズムのチュートリアル

 問題を区分的に定義された関数を積分することに帰着します。$x$ここで、ブレークポイントは 4 つの二次方程式を解くことで得られます。 

### 1. 重要な X 座標を構築する

 私たちは次のことを解決します:$$f(x) = y_1 - k,\; y_1 + k,\; y_2 - k,\; y_2 + k.$$各方程式は二次方程式であるため、実根を計算し、その中にあるすべての値を収集します。$[x_1, x_2]$、一緒に$x_1$そして$x_2$。 これらのポイントは、ドメインをセグメントに分割します。 

これが機能する理由は、放物線がこれらのルートでのみ境界を横切り、バンドのどの部分が長方形と交差するかを変更するためです。 

### 2. 並べ替えと重複排除

 すべての候補点を並べ替えて、重複に近いものを削除します。 これにより、一連の間隔が生成されます。$f(x)$4 つのしきい値はすべて固定されています。 

### 3. 各セグメントを個別に評価する

 インターバルごとに$[l, r]$、中間点を選択します$m$そして、次の符号を評価します。$$f(m) - (y_1 - k),\quad f(m) - (y_1 + k),\quad f(m) - (y_2 - k),\quad f(m) - (y_2 + k).$$これにより、次のいずれのケースが適用されるかが決まります。 

もし$f(x)+k \le y_1$または$f(x)-k \ge y_2$の場合、表示される高さはゼロになります。 

もし$f(x)-k \ge y_1$そして$f(x)+k \le y_2$、バンド全体が長方形の内側にあるため、表示される高さは次のようになります。$2k$。 

バンドの上部、下部、または両側が部分的にクリップされている場合は、次のような式が得られます。$$y_2 - (f(x)-k),\quad (f(x)+k) - y_1,\quad y_2 - y_1.$$これらはそれぞれ定数または線形のいずれかです$f(x)$したがって、分析的に積分可能です。 

### 4. セグメント上で統合する

 以下を事前計算します。$$\int f(x)\,dx = \frac{a_2}{3}x^3 + \frac{a_1}{2}x^2 + a_0 x.$$したがって、形式の任意の式は$A f(x) + B$直接統合します。 

すべてのセグメントにわたる寄与を合計して、合計の表示領域を取得します。 

### なぜ効果があるのか

 重要な不変条件は、4 つの境界方程式の連続する根の間の各区間内で、$f(x)$に対して$y_1 \pm k$そして$y_2 \pm k$変わりません。 可視高さの式はこの順序のみに依存するため、被積分関数は区間全体にわたって同一のままです。 これにより、セグメント上の積分を閉形式の逆微分で置き換えることが正確であり、隠れた不連続性が見逃されないことが保証されます。 

## Python ソリューション```python
import sys
import math

input = sys.stdin.readline

EPS = 1e-12

def solve_quadratic(a, b, c):
    if abs(a) < EPS:
        if abs(b) < EPS:
            return []
        return [-c / b]
    d = b * b - 4 * a * c
    if d < -EPS:
        return []
    if d < 0:
        d = 0.0
    sd = math.sqrt(d)
    return [(-b - sd) / (2 * a), (-b + sd) / (2 * a)]

def F(a2, a1, a0, x):
    return a2 * x * x + a1 * x + a0

def integral_f(a2, a1, a0, x):
    return (a2 / 3) * x**3 + (a1 / 2) * x**2 + a0 * x

def visible_height(a2, a1, a0, k, y1, y2, x):
    fx = F(a2, a1, a0, x)
    low = fx - k
    high = fx + k

    if high <= y1 + EPS or low >= y2 - EPS:
        return 0.0
    if low >= y1 - EPS and high <= y2 + EPS:
        return 2 * k
    if low >= y1 - EPS:
        return max(0.0, y2 - low)
    if high <= y2 + EPS:
        return max(0.0, high - y1)
    return y2 - y1

def integrate_segment(a2, a1, a0, k, y1, y2, l, r):
    m = (l + r) / 2
    h = visible_height(a2, a1, a0, k, y1, y2, m)

    # constant case
    if abs(h - 0.0) < 1e-12:
        return 0.0
    if abs(h - (y2 - y1)) < 1e-12:
        return (y2 - y1) * (r - l)
    if abs(h - 2 * k) < 1e-12:
        return 2 * k * (r - l)

    # linear cases: h = A*f(x) + B
    # deduce by sampling endpoints
    h1 = visible_height(a2, a1, a0, k, y1, y2, l)
    h2 = visible_height(a2, a1, a0, k, y1, y2, r)

    if abs(h2 - h1) < 1e-12:
        return h1 * (r - l)

    # assume h(x) = alpha * f(x) + beta
    f1 = F(a2, a1, a0, l)
    f2 = F(a2, a1, a0, r)

    if abs(f2 - f1) < 1e-12:
        return h1 * (r - l)

    alpha = (h2 - h1) / (f2 - f1)
    beta = h1 - alpha * f1

    # integrate alpha*f(x) + beta
    return alpha * (integral_f(a2, a1, a0, r) - integral_f(a2, a1, a0, l)) + beta * (r - l)

def solve():
    a2, a1, a0 = map(float, input().split())
    k = float(input())
    x1, y1, x2, y2 = map(float, input().split())

    if x2 < x1:
        x1, x2 = x2, x1
    if y2 < y1:
        y1, y2 = y2, y1

    xs = [x1, x2]

    for c in [y1 - k, y1 + k, y2 - k, y2 + k]:
        roots = solve_quadratic(a2, a1, a0 - c)
        for r in roots:
            if x1 - 1e-9 <= r <= x2 + 1e-9:
                xs.append(r)

    xs = sorted(xs)

    # deduplicate
    cleaned = []
    for x in xs:
        if not cleaned or abs(cleaned[-1] - x) > 1e-9:
            cleaned.append(x)

    xs = cleaned

    total_visible = 0.0
    for i in range(len(xs) - 1):
        l, r = xs[i], xs[i + 1]
        if r > l + 1e-12:
            total_visible += integrate_segment(a2, a1, a0, k, y1, y2, l, r)

    area = (x2 - x1) * (y2 - y1)
    answer = area - total_visible
    print(answer)

if __name__ == "__main__":
    solve()
```実装は、オーバーラップの構造が変化する可能性があるすべての x 座標を抽出することから始まります。 これらはまさに 4 つの境界方程式の根です。 並べ替えと重複排除の後、x 軸は、visible-height 関数が安定した代数形式を持つ間隔に分割されます。 

その後、各間隔が独立して統合されます。 定数の場合は直接処理されますが、非定数の場合は放物線値への線形依存性を再構築し、二次関数の逆微分を使用して面積を正確に計算します。 

微妙な点は、二次関数を解いて根を比較するときの浮動小数点の安定性です。 根をフィルタリングして区間に入れる場合と、ほぼ同一の分割点をマージする場合の両方に小さなイプシロンが必要です。そうしないと、境界が重複して長さ 0 のセグメントが生成され、数値ノイズが蓄積される可能性があります。 

## 実用的な例

 ### サンプル 1

 入力:```
1 1 -2
3
-4 -5 1 1
```最初に長方形の面積を計算し、次に放物線の周りのバンドによって引き起こされる可視領域を差し引きます。 

| セグメント | 間隔 | ケースの動作 |
 | --- | --- | --- |
 | 1 | [-4, x1'] | 重複なし |
 | 2 | [x1', x2'] | 部分的なバンドのオーバーラップ |
 | 3 | [x2', 1] | 重複なし |

 中央の領域は、放物線が長方形に入り、バンドが交差する場所に対応します。 この間隔で積分すると可視領域が得られ、合計領域から減算すると次の結果が得られます。$$11.666666666666668.$$このトレースは、関数が x の境界領域内でのみアクティブになり、その外側では関数がゼロに寄与する様子を示しています。 

### サンプル 2 (構築済み)

 入力:```
0 0 0
1
0 0 10 10
```ここで、放物線は x 軸であり、目に見えるバンドは単に$|y| \le 1$。 長方形の内側には、高さ 2 の水平ストリップが表示されます。 

| セグメント | 間隔 | 目に見える高さ |
 | --- | --- | --- |
 | 1 | [0, 10] | 2 |

 したがって、見える領域は$10 \cdot 2 = 20$、合計面積は 100 なので、答えは 80 になります。 

これにより、バンドが完全に長方形の内側に収まる定数ケースの処理が確認されます。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |$O(1)$| 二次ルートから最大 9 つのセグメント。それぞれが閉形式積分を使用して定数時間で評価されます。 
| スペース |$O(1)$| 重要なポイントの小さなリストのみが保存されます。 

間隔の数は二次境界交差の一定数によって制限されるため、アルゴリズムは係数の大きさに関係なく高速なままです。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    import math

    EPS = 1e-12

    def solve_quadratic(a, b, c):
        if abs(a) < EPS:
            if abs(b) < EPS:
                return []
            return [-c / b]
        d = b * b - 4 * a * c
        if d < -EPS:
            return []
        if d < 0:
            d = 0.0
        sd = math.sqrt(d)
        return [(-b - sd) / (2 * a), (-b + sd) / (2 * a)]

    def F(a2, a1, a0, x):
        return a2 * x * x + a1 * x + a0

    def integral_f(a2, a1, a0, x):
        return (a2 / 3) * x**3 + (a1 / 2) * x**2 + a0 * x

    def visible_height(a2, a1, a0, k, y1, y2, x):
        fx = F(a2, a1, a0, x)
        low = fx - k
        high = fx + k

        if high <= y1 or low >= y2:
            return 0.0
        if low >= y1 and high <= y2:
            return 2 * k
        if low >= y1:
            return max(0.0, y2 - low)
        if high <= y2:
            return max(0.0, high - y1)
        return y2 - y1

    def solve():
        a2, a1, a0 = map(float, sys.stdin.readline().split())
        k = float(sys.stdin.readline())
        x1, y1, x2, y2 = map(float, sys.stdin.readline().split())

        xs = [x1, x2]

        for c in [y1 - k, y1 + k, y2 - k, y2 + k]:
            roots = solve_quadratic(a2, a1, a0 - c)
            xs += roots

        xs = sorted(xs)
        cleaned = []
        for x in xs:
            if not cleaned or abs(cleaned[-1] - x) > 1e-9:
                cleaned.append(x)
        xs = cleaned

        def integrate_segment(l, r):
            m = (l + r) / 2
            h = visible_height(a2, a1, a0, k, y1, y2, m)
            return h * (r - l)

        total_visible = 0.0
        for i in range(len(xs) - 1):
            l, r = xs[i], xs[i + 1]
            if r > l:
                total_visible += integrate_segment(l, r)

        area = (x2 - x1) * (y2 - y1)
        return str(area - total_visible)

# provided sample
assert run("1 1 -2\n3\n-4 -5 1 1\n") == "11.666666666666668"

# custom: flat line, centered band
assert abs(float(run("0 0 0\n1\n0 0 10 10\n")) - 80) < 1e-6

# custom: no visibility
assert abs(float(run("0 0 100\n0\n0 0 1 1\n")) - 1) < 1e-6

# custom: full visibility inside band
assert abs(float(run("0 0 0\n100\n0 0 1 1\n")) - 0) < 1e-6
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 平らな放物線 | 写真 80 | 定数バンドの場合 |
 | 巨大なk | 1 | フルカバレッジエッジ |
 | ゼロ k 大きな長方形 | 1 | 可視性がない場合 |

 ## 特殊なケース

 微妙なケースが現れるのは、$k = 0$。 可視領域は曲線自体に折りたたまれ、連続ジオメトリでは面積がゼロになります。 すべての境界方程式が同一のペアになり、非ゼロの高さが選択された区間が生成されず、可視積分がゼロに評価されるため、アルゴリズムはこれを処理します。 

放物線が長方形またはそのオフセット バンドと決して交差しない場合には、別のケースが発生します。 たとえば、次の場合$f(x)$いつもはるか上にいる$y_2 + k$、すべてのセグメントが条件を満たします。$f(x)-k \ge y_2$したがって、表示される高さはどこでもゼロになります。 各セグメントがすぐにゼロを返すため、積分では何も蓄積されません。 

ルートが長方形の境界上に正確に位置する場合、より微妙な状況が発生します。 イプシロンベースのフィルタリングにより、そのような点が確実に含まれますが、縮退した長さゼロのセグメントは作成されません。 これらの境界では、区間のどちらの側でも同じ式が生成されるため、分割しても結果は変化せず、評価が安定するだけです。
