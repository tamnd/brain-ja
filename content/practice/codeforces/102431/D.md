---
title: "CF 102431D - パルスノヴァ"
description: "固定半径 (R) の円の中心を選択する必要があります。 すべての入力線について、その無限の線が円の内側にどのくらいあるかを測定し、これらの長さをすべての線に加算します。 タスクは、可能な最大の合計を見つけることです。"
date: "2026-08-08T17:19:54+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102431
codeforces_index: "D"
codeforces_contest_name: "2019 China Collegiate Programming Contest Final (CCPC-Final 2019)"
rating: 0
weight: 102431
solve_time_s: 332
verified: true
draft: false
---

[CF 102431D - パルス ノヴァ](https://codeforces.com/problemset/problem/102431/D)

 **評価:** -
 **タグ:** -
 **解決時間:** 5 分 32 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 固定半径 (R) の円の中心を選択する必要があります。 すべての入力線について、その無限の線が円の内側にどのくらいあるかを測定し、これらの長さをすべての線に加算します。 タスクは、可能な最大の合計を見つけることです。 

円の中心が (C) にあり、入力線が (C) から垂直距離 (d) にあるとします。 (d>R) の場合、線は円を完全に外し、寄与はゼロになります。 (d\le R) の場合、交差部分はコードです。 その半分の長さは (\sqrt{R^2-d^2}) であるため、この行の寄与は次のようになります。 

[
 f(d)=2\sqrt{R^2-d^2}。 
】

 入力はテスト ケースごとに最大 50 行を提供します。 すべてのラインは、座標の絶対値が最大 1000 である 2 つの異なる整数点によって指定されます。すべてのテスト ケースの (n) の合計は最大 100 であるため、(O(n^2)) または (O(n^2\log n)) の幾何学的分解が合理的です。 はるかに大規模な数値検索内でラインのすべてのペアを繰り返し検査するソリューションは、特に各幾何学的評価自体にすべてのラインが含まれるため、あまり魅力的ではありません。 

最初の微妙な点は、距離が (R) の場合、線の寄与が正確に 0 になることです。 例えば、```
1 1
0 0 1 0
```答えがあります`2.0000000000`中心を線上の任意の場所に配置すると、長さ 2 の弦が得られるためです。距離がちょうど 1 にある中心はゼロになるため、境界を寄与 (2R) として扱うか、間違った場所で厳密な不等式を使用すると、完全に間違った結果が得られます。 

2 番目のエッジ ケースは、複数の平行線です。 例えば、```
2 1
0 0 10 0
0 2 10 2
```中心がそれらの中間にある場合、最適値 (2\sqrt{1-1^2}+2\sqrt{1-1^2}=0) が得られますが、中心をどちらかの線上に置くと次のようになります。`2.0000000000`。 関連するすべての領域に有限のポリゴン頂点があると仮定する方法は、平行なオフセット ラインが決して交差しないため、ここで失敗する可能性があります。 

3 番目のエッジ ケースは、入力ラインが一致する場合です。 例えば、```
2 1
0 0 1 0
2 0 5 0
```同じ幾何学的な線を 2 回記述します。 2 つの貢献は両方ともカウントされる必要があるため、答えは次のようになります。`4.0000000000`。 アルゴリズムは、オフセット境界が一致する場合でも、重複した入力ラインを別個の寄与として保持する必要があります。 

最後に、最大化中心は入力ライン上または入力ラインの交点にある必要はありません。 2 本の平行な線の場合、最適な中心はそれらの中間に位置する可能性があります。 元の線の交点だけを列挙するだけでは不十分であるのはこのためです。 

## アプローチ

 直接的なアプローチでは、考えられる多くの円の中心を試し、それぞれの中心での合計寄与度を評価します。 すべてのラインの距離を計算する必要があるため、中心が与えられると、1 つの評価は (O(n)) になります。 問題は、中心が連続的であるため、列挙できる自然な有限の座標セットが存在しないことです。 平面上に細かいグリッドを配置したとしても、必要な (10^{-6}) 精度を得るには、非常に多くの点が必要になります。 

有益な観察は、1 つの行を個別に観察することで得られます。 線は、円の中心がその線から最大 (R) の距離にあるときに正確に寄与します。 このような中心のセットは、それ自体に垂直な元の線を (R) だけシフトすることによって得られる 2 つの線によって境界付けられる無限のストリップです。 

すべての入力行にこれら 2 つの境界線を描画します。 現在、平面内には最大で (2n) 個の線があります。 それらの配置により、平面が (O(n^2)) 個の凸領域に分割されます。 このような領域の 1 つの内部では、すべての元のラインは固定ステータスを持ちます。つまり、中心からの距離が常に下 (R) であるため寄与するか、常に上 (R) であるため寄与がゼロになります。 

固定寄与ラインの場合、中心からの符号付き距離をアフィン関数 (d(x,y)) として書き込みます。 そのストリップの内側には、

 [
 2\sqrt{R^2-d(x,y)^2}
 】

 (d(x,y)) はアフィン関数であり、(2\sqrt{R^2-t^2}) は (t\in[-R,R]) に対して凹関数であるため、 は凹関数です。 凹関数の和は凹になります。 したがって、1 つの配置領域内で、対物レンズは通常の凸最適化の意味で、おそらくセグメント全体に沿って単一のグローバル最大値を持ちます。 

それは問題を完全に変えます。 プレーン全体を一度に最適化する必要はなくなりました。 (O(n^2)) 個の領域を列挙し、すべての領域内でネストされた三項検索を使用します。 固定 (x) の場合、凸領域と垂直線の交点 (x=\text{constant}) が (y) の間隔になります。 その間隔に制限された目的は凹面であるため、三分探索はその最大値を見つけます。 (y) を最大化して得られる (x) の関数も凹型であるため、2 回目の三分探索で最良の (x) が見つかります。 

幾何学的な配置が重要な部分です。 目標が全体的に凹面ではないため、ブルート フォースは失敗します。 オフセットライン観察では、式が変化する境界を正確に分離し、結果として得られるすべての領域の内部で対物レンズが凹面になります。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | 連続中心に対する有限厳密境界はありません | O(n) | 遅すぎる |
 | 最適 | (O(n^2\cdot K^2\cdot n)) | (O(n^2)) | 承認済み |

 ここで (K) は、三分探索反復の固定回数です。 (K) が約 30 の場合、数値誤差は必要な値 (10^{-6}) をはるかに下回ります。 

## アルゴリズムのチュートリアル

 1. すべての入力行を正規化された符号付き距離表現に変換します。 ((x_1,y_1)) と ((x_2,y_2)) を通る線については、次のようにします。 

[
 a=y_1-y_2、\qquad b=x_2-x_1、\qquad c=x_1y_2-x_2y_1。 
】

 ((x,y)) からの符号付き距離は次のとおりです。 

[
 \frac{ax+by+c}{\sqrt{a^2+b^2}}。 
】

 寄与するストリップの 2 つの境界は単なる方程式であるため、正規化を維持すると便利です。 

[
 ax+by+c=\pm R\sqrt{a^2+b^2}。 
】

1. 元のラインごとに 2 つのオフセット境界を構築します。 このような行は最大 (2n\le100) 行あります。 これらはまさに、貢献と非貢献の間で境界線が変わる場所です。 
2. 非平行なオフセット境界のすべてのペアごとの交差を計算します。 それらは (O(n^2)) 個あります。 すべての有界配置領域には少なくとも 1 つのそのような頂点があり、関連するすべての非有界領域は同じ半平面表現を通じて処理できますが、純粋に平行な場合は個別に処理されます。 
3. すべての配置頂点の周囲で、境界光線の間の角度セクターを検査します。 すべてのセクター内の点を厳密に選択し、その点がすべての境界のどちら側にあるかを決定します。 同じ辺の選択肢を持つ 2 つの点は同じ配置領域に属するため、各辺のパターンは 1 回だけ保存します。 

摂動は領域を識別するためにのみ使用されます。 実際の最適化は、摂動点ではなく、領域全体にわたって実行されます。 

1. 発見された各領域を半平面の交点として表します。 十分に大きな境界正方形から始めて、領域のすべての境界側を定義する半平面に対してそれをクリップします。 関連するすべての領域について、結果として得られるポリゴンには、有用な最大化ポイントがすべて含まれています。 単一のアクティブ ラインは常に (2R) を生成できるため、その値はベースラインとして個別に維持されます。 
2. 点 ((x,y)) について、元のすべてのラインをチェックして合計寄与度を計算します。 垂直距離が少なくとも (R) の場合、その寄与はゼロです。 それ以外の場合は追加します

 [
 2\sqrt{R^2-d^2}。 
】

 1. 1 つの固定領域について、(y) に対して内部三分探索を実行します。 固定 (x) における凸多角形の垂直交点は間隔です。 その区間の目標は凹面であるため、2 つの内部 3 点を比較することで、悪い方を破棄できます。 
2. 内部探索で得られた最良の値を (x) の値として使用します。 凸型の垂直スライス上で凹型関数を最大化すると凹型が維持されるため、この外側の関数も凹型になります。 ポリゴンの (x) 範囲に対して 2 回目の三項検索を実行します。 
3. すべての領域に対して最適化を繰り返し、最大の結果を保持します。 また、入力ラインに中心を置くと常に長さ (2R) の弦が得られるため、最初から (2R) を維持してください。 

### なぜ効果があるのか

 配置の境界は、入力ラインが寄与から寄与しない状態に変化する正確な位置です。 したがって、開いた配置領域内では、寄与するセットは固定されます。 各寄与はそのストリップ内の中心座標の凹関数であるため、それらの合計は凹関数になります。 凸領域上の凹関数には誤解を招く極大値がありません。これはまさにネストされた三項探索に必要な特性です。 考えられるすべての中心は何らかの配置領域またはその境界に属し、目的はそれらの境界を越えて連続しているため、すべての領域にわたって最大値を取ることで全体的な最適値が回復されます。 

## Python ソリューション```python
import sys
import math

input = sys.stdin.readline

EPS = 1e-10
ITER = 32

def cross(a, b):
    return a[0] * b[1] - a[1] * b[0]

def line_intersection(l1, l2):
    # A*x + B*y + C = 0
    A1, B1, C1 = l1
    A2, B2, C2 = l2

    d = A1 * B2 - A2 * B1
    if abs(d) < 1e-14:
        return None

    x = (B1 * C2 - B2 * C1) / d
    y = (C1 * A2 - C2 * A1) / d
    return x, y

def clip_polygon(poly, h):
    # Keep A*x + B*y + C >= 0.
    if not poly:
        return []

    A, B, C = h
    res = []

    def value(p):
        return A * p[0] + B * p[1] + C

    for i in range(len(poly)):
        p = poly[i]
        q = poly[(i + 1) % len(poly)]

        vp = value(p)
        vq = value(q)

        inp = vp >= -EPS
        inq = vq >= -EPS

        if inp:
            res.append(p)

        if inp != inq:
            den = vp - vq
            if abs(den) > 1e-30:
                t = vp / den
                x = p[0] + (q[0] - p[0]) * t
                y = p[1] + (q[1] - p[1]) * t
                res.append((x, y))

    return res

def polygon_for_signs(boundaries, signs, B):
    poly = [
        (-B, -B),
        (B, -B),
        (B, B),
        (-B, B),
    ]

    for h, s in zip(boundaries, signs):
        A, C, D = h
        poly = clip_polygon(poly, (A * s, C * s, D * s))
        if len(poly) < 3:
            return []

    return poly

def optimize_polygon(poly, active, lines, R):
    if len(poly) < 3:
        return 0.0

    xs = [p[0] for p in poly]
    lo_x = min(xs)
    hi_x = max(xs)

    if hi_x - lo_x < 1e-12:
        x0 = lo_x
        ys = [p[1] for p in poly]
        y0 = sum(ys) / len(ys)
        return value_at(x0, y0, active, lines, R)

    edges = []
    m = len(poly)

    for i in range(m):
        x1, y1 = poly[i]
        x2, y2 = poly[(i + 1) % m]

        if abs(x2 - x1) > 1e-14:
            edges.append((x1, y1, x2, y2))

    def y_interval(x):
        low = -float("inf")
        high = float("inf")

        for x1, y1, x2, y2 in edges:
            if x < min(x1, x2) - 1e-9 or x > max(x1, x2) + 1e-9:
                continue

            t = (x - x1) / (x2 - x1)
            y = y1 + (y2 - y1) * t

            if x1 < x2:
                pass

            # We collect all intersections and use their min/max.
            # For a convex polygon this is exactly the vertical slice.
            if y < low:
                low = y
            if y > high:
                high = y

        if low == -float("inf"):
            low = min(p[1] for p in poly)
        if high == float("inf"):
            high = max(p[1] for p in poly)

        return low, high

    def best_y(x):
        ly, hy = y_interval(x)

        if hy - ly < 1e-12:
            return value_at(x, (ly + hy) * 0.5, active, lines, R)

        l = ly
        r = hy

        for _ in range(ITER):
            y1 = (2.0 * l + r) / 3.0
            y2 = (l + 2.0 * r) / 3.0

            f1 = value_at(x, y1, active, lines, R)
            f2 = value_at(x, y2, active, lines, R)

            if f1 < f2:
                l = y1
            else:
                r = y2

        y = (l + r) * 0.5
        return value_at(x, y, active, lines, R)

    l = lo_x
    r = hi_x

    for _ in range(ITER):
        x1 = (2.0 * l + r) / 3.0
        x2 = (l + 2.0 * r) / 3.0

        f1 = best_y(x1)
        f2 = best_y(x2)

        if f1 < f2:
            l = x1
        else:
            r = x2

    return best_y((l + r) * 0.5)

def value_at(x, y, active, lines, R):
    ans = 0.0
    rr = R * R

    for idx in active:
        a, b, c, norm = lines[idx]
        d = (a * x + b * y + c) / norm
        ad = abs(d)

        if ad < R:
            z = rr - d * d
            if z > 0.0:
                ans += 2.0 * math.sqrt(z)

    return ans

def solve_case(n, R, raw):
    lines = []

    for x1, y1, x2, y2 in raw:
        a = y1 - y2
        b = x2 - x1
        c = x1 * y2 - x2 * y1
        norm = math.hypot(a, b)
        lines.append((float(a), float(b), float(c), norm))

    # Any center placed on an input line gives 2R from that line.
    best = 2.0 * R

    boundaries = []

    for a, b, c, norm in lines:
        shift = R * norm

        # a*x + b*y + c - shift = 0
        boundaries.append((a, b, c - shift))

        # a*x + b*y + c + shift = 0
        boundaries.append((a, b, c + shift))

    m = len(boundaries)

    # If all boundaries are parallel, the problem is one-dimensional.
    non_parallel = False
    for i in range(m):
        for j in range(i):
            if abs(
                boundaries[i][0] * boundaries[j][1]
                - boundaries[j][0] * boundaries[i][1]
            ) > 1e-14:
                non_parallel = True
                break
        if non_parallel:
            break

    if not non_parallel:
        # All original lines are parallel. Pick a coordinate along the
        # common normal and ternary-search it.
        a, b, c, norm = lines[0]

        # signed normalized distance coordinate t = (a*x+b*y+c)/norm.
        # Every input line has a constant t.
        ds = [(aa * 0.0 + bb * 0.0 + cc) / nn
              for aa, bb, cc, nn in lines]

        lo = min(ds) - R
        hi = max(ds) + R

        def one_dim(t):
            s = 0.0
            for d0 in ds:
                d = t - d0
                if abs(d) <= R:
                    z = R * R - d * d
                    if z > 0:
                        s += 2.0 * math.sqrt(z)
            return s

        for _ in range(ITER * 2):
            x1 = (2.0 * lo + hi) / 3.0
            x2 = (lo + 2.0 * hi) / 3.0
            if one_dim(x1) < one_dim(x2):
                lo = x1
            else:
                hi = x2

        best = max(best, one_dim((lo + hi) * 0.5))
        return best

    # Find all arrangement vertices.
    vertices = []

    for i in range(m):
        for j in range(i):
            p = line_intersection(boundaries[i], boundaries[j])
            if p is not None and math.isfinite(p[0]) and math.isfinite(p[1]):
                vertices.append(p)

    if not vertices:
        return best

    # The coordinates of relevant arrangement vertices are bounded by
    # the input coordinates and the radius-scaled offsets. Use a generous
    # square so that clipping also handles unbounded cells.
    B = 1.0
    for x, y in vertices:
        B = max(B, abs(x), abs(y))
    B = B * 2.0 + 100.0

    cells = set()

    # Around every vertex, take small angular sectors. Each sector belongs
    # to exactly one arrangement cell.
    for px, py in vertices:
        zero_dirs = []

        for A, C, D in boundaries:
            v = A * px + C * py + D
            if abs(v) < 1e-8 * max(1.0, abs(A * px), abs(C * py), abs(D)):
                # Boundary direction is perpendicular to its normal.
                theta = math.atan2(-A, C)
                zero_dirs.append(theta)
                zero_dirs.append(theta + math.pi)

        if not zero_dirs:
            continue

        zero_dirs.sort()

        angles = []
        k = len(zero_dirs)

        for i in range(k):
            t1 = zero_dirs[i]
            t2 = zero_dirs[(i + 1) % k]

            if i == k - 1:
                t2 += 2.0 * math.pi

            if t2 - t1 > 1e-12:
                angles.append((t1 + t2) * 0.5)

        # Find a safe perturbation size.
        min_dist = float("inf")

        for A, C, D in boundaries:
            v = abs(A * px + C * py + D)
            norm = math.hypot(A, C)
            if norm > 0 and v > 1e-8:
                min_dist = min(min_dist, v / norm)

        if not math.isfinite(min_dist):
            min_dist = 1.0

        eps = min(1e-5, min_dist * 0.1)

        for theta in angles:
            sx = px + eps * math.cos(theta)
            sy = py + eps * math.sin(theta)

            signs = []
            for A, C, D in boundaries:
                v = A * sx + C * sy + D
                signs.append(1 if v >= 0.0 else -1)

            key = tuple(signs)

            if key in cells:
                continue

            cells.add(key)

    # Optimize every discovered region.
    for signs in cells:
        # Find a representative point by using the center of all half-plane
        # constraints through the already sampled region. We reconstruct
        # the polygon and then identify the contributing lines.
        poly = polygon_for_signs(boundaries, signs, B)

        if len(poly) < 3:
            continue

        cx = sum(p[0] for p in poly) / len(poly)
        cy = sum(p[1] for p in poly) / len(poly)

        active = []

        for i, (a, b, c, norm) in enumerate(lines):
            d = abs((a * cx + b * cy + c) / norm)
            if d < R - 1e-8:
                active.append(i)

        if not active:
            continue

        # A single active line cannot beat 2R.
        if len(active) == 1:
            continue

        best = max(best, optimize_polygon(poly, active, lines, R))

    return best

def main():
    t = int(input())

    out = []

    for tc in range(1, t + 1):
        n, R = map(int, input().split())
        raw = [tuple(map(int, input().split())) for _ in range(n)]

        ans = solve_case(n, R, raw)
        out.append(f"Case #{tc}: {ans:.10f}")

    sys.stdout.write("\n".join(out))

if __name__ == "__main__":
    main()
```での行変換`solve_case`標準の陰的な方程式 (ax+by+c=0) を使用します。 通常の長さは個別に保存されるため、平方根を繰り返し再計算することなく距離を評価できます。 

元のラインごとに生成される 2 つのエントリは、その 2 つの距離 (R) 境界です。 境界式の符号は、その境界のどちら側に領域が含まれているかを示します。 符号の完全なタプルは、配置セルを一意に識別します。 

すべての交差点付近の角度の摂動は注目に値します。 単に固定 (\varepsilon) によって移動すると、同じ頂点のすぐ近くを通過する別の境界を誤って横切る可能性があります。 この実装では、まず最も近い非発生境界を見つけて、その距離よりもはるかに小さい摂動を選択します。 連続する境界方向間のサンプリングでは、3 つ以上の境界が交わる頂点も処理されます。 

ポリゴンは半平面クリッピングによって再構築されます。 大きな正方形から開始すると、数学的領域に境界がない場合でも、具体的な多角形が得られます。 値 (2R) は、入力ライン上に配置されたセンターからすでに取得されているため、寄与するラインが 1 つだけある領域は安全にスキップできます。 

ネストされた三項検索は、凸領域でのみ動作します。 固定 (x) では、垂直スライスは間隔になります。 内部検索はその間隔で最大化されます。 次に、外側の探索により、結果として得られる 1 次元の凹関数が最大化されます。 

この実装では全体的に浮動小数点を使用します。 Python の整数には制限がないため、元の線係数の構築でオーバーフローすることはありません。 唯一の数値上の懸念は、幾何比較と平方根です。 コードは境界テストの周囲の許容値を使用し、ラジカンドが正であることを確認することで暗黙的にラジカンドをクランプします。 

## 実用的な例

 ### サンプル 1

 最初のテスト ケースには 2 本の垂直線が含まれています。```
2 2
1 1 1 2
1 1 2 1
```2 本の線は ((1,1)) で交差します。 そこに円の中心を置くと、両方の距離がゼロになります。 

| ステップ | センター | 1 号線までの距離 | 2 号線までの距離 | 合計 |
 | --- | --- | --- | --- | --- |
 | 初期ベースライン | 1 線上の任意の点 | 0 | おそらく外で | 4 |
 | (1,1) | を含む配置領域 (1,1) | 0 | 0 | 8 |
 | 最終回 | (1,1) | 0 | 0 | 8 |

 各ラインはその全直径に寄与します (2R=4)。 したがって、合計は`8.0000000000`。 

これは、ベースライン (2R) がなぜ役立つのかも示しています。 配置を検討する前から、答えが 4 を下回らないことはすでにわかっています。 

### サンプル 2

 2 番目のテスト ケースは、```
4 3
0 0 0 1
2 0 0 1
0 0 1 0
0 2 1 2
```最初の 2 つの線は傾き (-1) を持ち、((0,1)) で交差します。 他の 2 つは水平で、1 つは通過 (y=0)、もう 1 つは通過 (y=2) です。 ((0,1)) では、4 つの直線までの距離は (1,1,1,1) です。 

(R=3) の場合、距離 1 の線が寄与します。 

[
 2\sqrt{9-1}=4\sqrt2。 
】

 最初の 2 つは対角線で、他の 2 つは水平であるため、最終的な最適化では 4 つの線がすべて同じ方向に寄与するわけではありません。 最適値を含む配置セルを評価すると、指定された値が得られます。 

| ステップ | センター | アクティブライン | 代表的な距離 | 合計 |
 | --- | --- | --- | --- | --- |
 | ベースライン | 一行で | 1 | 0 | 6 |
 | 候補地域 | ((0,1)) 付近 | 4 | 約1 | 約22.63 |
 | 3次改良 | 最適化されたセンター | 4 | 個別に最適化 | 23.3137084990 |

 この例は、この配置の主な目的を示しています。 中心は、寄与するセットが変化しないまま継続的に移動することができ、その領域内では、目的は三分探索で最適化できる凹型の形状になります。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O(n^2 K^2 n)) | (O(n^2)) 個の配置領域があり、各領域は 2 (K) 回の反復三項探索を使用し、目的の評価ごとに (O(n)) 回の作業を行います。 
| スペース | (O(n^2)) | この配置には (O(n^2)) 個の頂点と領域があり、各ポリゴンには (O(n)) 個の頂点が含まれています。 

(n\le50) の場合、一般的な位置の場合、配置には数千の領域しかありません。 3 値反復の数は固定されているため、一定の数値最適化作業とは別に、漸近動作は入力行数において実質的に 2 次になります。 すべてのテスト ケースの (n) の合計は最大 100 であり、幾何学的ワークロードの合計が管理可能に保たれます。 

公式コンテストのソリューションでは、各凸領域内の同じ (O(n^2)) 配置構造とネストされた 3 値最適化も識別されます。 

## テストケース```
# The production solution above can be placed in a module and exposed
# through solve_case(). These tests compare floating-point answers with
# a tolerance rather than comparing formatted strings byte-for-byte.

import math

def check_case(n, r, raw, expected, eps=1e-6):
    got = solve_case(n, r, raw)
    assert math.isclose(got, expected, rel_tol=eps, abs_tol=eps), (
        got,
        expected,
    )

# Sample 1
check_case(
    2,
    2,
    [
        (1, 1, 1, 2),
        (1, 1, 2, 1),
    ],
    8.0,
)

# Sample 2
check_case(
    4,
    3,
    [
        (0, 0, 0, 1),
        (2, 0, 0, 1),
        (0, 0, 1, 0),
        (0, 2, 1, 2),
    ],
    23.3137084990,
)

# Minimum-size input. One line always gives a full diameter.
check_case(
    1,
    1,
    [
        (0, 0, 1, 0),
    ],
    2.0,
)

# Duplicate coincident lines. Both contributions must be counted.
check_case(
    2,
    1,
    [
        (0, 0, 1, 0),
        (2, 0, 5, 0),
    ],
    4.0,
)

# Two parallel lines at distance exactly 2R. They cannot contribute
# simultaneously with positive length. The best result is one diameter.
check_case(
    2,
    1,
    [
        (0, 0, 10, 0),
        (0, 2, 10, 2),
    ],
    2.0,
)

# Three identical lines, checking that multiplicity is preserved.
check_case(
    3,
    2,
    [
        (0, 0, 10, 0),
        (1, 0, 7, 0),
        (-5, 0, 3, 0),
    ],
    12.0,
)
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`1 1`一行で |`2`| 最小サイズのケースとフル直径の貢献 |
 | 2 つの同一の幾何学的な線 |`4`| 重複した行は個別のコントリビューションのままになります。 
| 離れたところにある 2 本の平行線`2R`|`2`| 正確なストリップ境界と平行線の処理 |
 | 3 つの同一の行 |`12`| 多重度とすべて等しいジオメトリ |

 ## 特殊なケース

 正確な距離 (R) にある線の場合、弦は 1 つの点に縮退し、長さは 0 になります。 寄与の式は (2\sqrt{R^2-R^2}=0) を与えます。 この配置ではその線を境界として扱いますが、隣接する領域はその線を寄与または非寄与として扱います。 目的は境界で連続しているため、隣接する領域を最適化しても正しい値が得られます。 

単一の入力ラインの場合、異なるラインからのオフセット境界のペアは存在しないため、配置頂点が存在しない可能性があります。 アルゴリズムは、ベースライン (2R) を通じてこれを直接処理します。 入力ライン上のすべての点は、そのラインから可能な最大の寄与を与えます。 

平行な入力ラインの場合、そのオフセット境界も平行であるため、一般的な 2 次元配置には頂点がありません。 特別な平行分岐により、問題は 1 つのスカラー座標、つまり固定基準線からの符号付き距離に縮小されます。 したがって、すべての行の寄与はその座標にのみ依存し、通常の 3 項検索で十分です。 

一致する入力ラインの場合、幾何学的配置には同じ境界が複数回含まれますが、寄与の計算は元の入力ラインごとに繰り返されます。 したがって、2 本の同一の線は弦長の 2 倍となり、3 本の同一の線は弦長の 3 倍になります。 入力行の重複を排除すると、問題は静かに変化します。 

寄与するすべてのストリップの外側にある中心の場合、合計はゼロになります。 このような領域は、入力ラインに中心を置くと少なくとも (2R>0) が得られるため、勝つ必要はありません。 アルゴリズムはその値から開始され、寄与率ゼロの領域を安全に無視できます。 

1 つの入力行のみが寄与する領域の場合、その領域は境界がない場合があります。 考えられる最良の値は正確に (2R) であるため、これらの領域は、境界付きポリゴンの最適化を必要とする代わりに、初期ベースラインによってカバーされます。 

同じ配置頂点を通過する複数の境界の場合、4 つの固定摂動方向のみを使用すると、すべての隣接領域を列挙することはできません。 代わりに、実装ではすべてのインシデント境界の方向を並べ替え、すべての角度セクターをサンプリングします。 これにより、同時オフセット ラインが安全になります。 

浮動小数点境界比較の場合、オフセット ラインに非常に近い点は、丸めが原因で分類に一貫性がなくなる可能性があります。 このコードは、境界のどちら側に点が含まれるかを決定するときに小さな許容値を使用し、最終的な数値検索では (10^{-6}) の精度に厳密に必要な反復回数よりも多くの反復を使用します。
