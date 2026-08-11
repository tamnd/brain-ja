---
title: "CF 102394D - 無人自動車"
description: "軸に整列した長方形のフィールドと、厳密にその内側に 2 つの互いに切り離された線分があります。 車は点であり、そのルートのすべての点が 2 つのセグメントから等距離にある必要があります。"
date: "2026-08-10T19:04:26+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102394
codeforces_index: "D"
codeforces_contest_name: "The 2019 China Collegiate Programming Contest Harbin Site"
rating: 0
weight: 102394
solve_time_s: 187
verified: true
draft: false
---

[CF 102394D - 無人運転車](https://codeforces.com/problemset/problem/102394/D)

 **評価:** -
 **タグ:** -
 **解決時間:** 3 分 7 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 軸に整列した長方形のフィールドと、厳密にその内側に 2 つの互いに切り離された線分があります。 車は点であり、そのルートのすべての点が 2 つのセグメントから等距離にある必要があります。 ルートは長方形の境界で開始および終了しますが、2 つの境界点は異なり、内部を通過する必要があります。 

したがって、キーオブジェクトはセットです

 [
 {P\mid \オペレーター名{距離}(P,A)=\オペレーター名{距離}(P,B)}。 
】

 これは 2 つのセグメントのボロノイ二等分線です。 両方のセグメントは長方形の内側にあり交差しないため、関連する二等分線は、セグメント (A) に近い領域をセグメント (B) に近い領域から分離します。 長方形の内側に、自動車がたどる必要がある固有の境界から境界へのルートが形成されます。 答えは、長方形内の二等分線の長さです。 

各入力ケースでは、左下隅 ((x_l,y_l)) と右上隅 ((x_r,y_r)) を通る長方形が得られ、その後に 2 つのセグメントの端点が続きます。 終点は整数の座標ですが、目的のルートは連続した曲線であるため、答えは一般に非合理的です。 

公式の制約では、(10^5) 個もの独立したケースが許可されますが、すべての座標は (-1000) と (1000) の間にあります。 必要な誤差は (10^{-9}) であり、入力の自然な単位スケールよりもはるかに小さいため、座標範囲が狭いとグリッド ソリューションは実行できません。 テスト ケースごとに一定量の計算ジオメトリが適切な目標となります。 

誤って対処しやすい特殊なケースがいくつかあります。 まず、セグメントの最近接点は必ずしも内部点であるとは限りません。 例えば、```
1
0 0 5 5
1 1 4 1
1 4 4 4
```答えは(5)です。 セグメントの水平範囲の外側では、最も近い点が端点となるため、すべてのセグメントを無限の直線として扱うと、間違った二等分線が得られます。 

第 2 に、最も近いフィーチャはセグメントの終点で正確に変更される可能性があります。 サンプルでは、```
1
0 0 7 6
2 4 4 4
3 2 5 2
```二等分線は、端点-端点の線、点-線の放物線部分、および線-線の部分で構成されます。 (x=2,3,4,5) での遷移を無視すると、間違った長さが得られます。 正解は (7.552593593868681136) です。 

第三に、2 本の補助線は平行または交差することができます。 たとえば、2 つの平行な水平セグメントは 1 つの角の二等分線を生成しますが、2 つの非平行なセグメントは、それらの支持線の交点を通る 2 つの角の二等分線を生成します。 単一の有限交点を想定する一般的な線交差ルーチンは、並列の場合に黙って失敗します。 

最後に、セグメントは垂直になる可能性があるため、その (x) 差で除算する数式は安全ではありません。 この実装では、垂直制約線を個別に処理します。 

## アプローチ

 文字通りの総当たりアプローチでは、グリッド上の長方形をサンプリングし、すべてのグリッド点で 2 つのセグメントの距離を計算し、等価曲線の再構築を試みます。 1 単位の間隔のグリッドでも、最大サイズの長方形にはおよそ (2000\cdot2000=4\cdot10^6) 個の点があります。 (10^5) 個のケース全体では、約 (4\cdot10^{11}) 点の評価になります。 より根本的には、このようなグリッドでは、必要な (10^{-9}) 幾何学的精度を保証することはできません。 

この強引なアイデアは、本当に知りたいこと、つまり、目的の曲線のすべての点で、各セグメントのどの部分が距離に関与しているのかを明らかにするので便利です。 1 つのセグメントについては、可能性は 3 つだけです。 最も近い点は、その最初の端点、その 2 番目の端点、またはセグメントの内部点になります。 

両方のセグメントに対して最も近い特徴が固定されると、等式曲線は非常に単純な古典的なオブジェクトになります。 2 つのポイント フィーチャは垂直二等分線を与えます。 2 つのライン フィーチャは角の二等分線を与えます。 点フィーチャとライン フィーチャは放物線を形成します。これは、曲線がまさに焦点までの距離が準線までの距離に等しい点の集合であるためです。 

この観察により、連続的な問題は最も近い特徴の 9 つの組み合わせのみに縮小されます。 各組み合わせには、1 つまたは 2 つの半平面で記述される単純な有効領域もあります。 対応する線または放物線をそれらの半平面および長方形と交差させます。 結果として得られる部分は完全なボロノイ二等分線を分割するため、それらの長さを単純に加算できます。 

受け入れられている幾何学的実装では、まさにこの定数サイズの分解が使用されます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | グリッド総当たり | (O((W/\バレプシロン)(H/\バレプシロン))) | (O(1)) または (O(WH)) | 遅すぎて正確ではありません |
 | 特徴分解 | (O(1)) ケースごと | (O(1)) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 有効な点がその内側にあるという規則に従って、長方形のすべての境界を有向線として表します。 同じ表現が、セグメントのどの端点または内部部分が最も近いフィーチャであるかを表す半平面にも使用されます。 
2. 各セグメント (XY) について、最も近い 3 つの特徴状態を考慮します。 状態 (0) は、最も近い点が (X) であることを意味します。 状態(1)は(Y)であることを意味します。 状態 (2) は、垂直投影がセグメントの内側にあることを意味します。 

状態 (0) の場合、有効条件は次のとおりです。 

[
 (P-X)\cdot(Y-X)\le 0.
 】

 状態 (1) の場合、次のようになります。 

[
 (P-Y)\cdot(X-Y)\le 0.
 】

状態 (2) では、両方のエンドポイントの条件が逆になり、投影がエンドポイントの間に存在する必要があります。 
3. 特徴状態のペア (3\times3=9) をすべて列挙します (セグメントごとに 1 つの状態)。 対応する半平面の境界を 4 つの長方形の境界に追加します。 これらの不等式によって受け入れられるすべての点は、各セグメント上で選択された最も近いフィーチャを正確に持ちます。 
4. 選択した両方のフィーチャが点 (P) と (Q) である場合、それらの距離は (PQ) の垂直二等分上で正確に等しくなります。 その線を作成し、現在のすべての半平面でクリップします。 
5. 選択した両方のフィーチャがセグメントの内部である場合、それらの距離は 2 本の支持線までの距離になります。 2 つの直線までの距離が等しいことは、角の二等分線です。 補助線が平行の場合、補助線の中間に 1 本の二等分線が存在します。 それらが交差する場合、2 つの角の二等分線が存在し、両方を特徴有効性の半平面に対してテストする必要があります。 
6. 一方のフィーチャが点で、もう一方が内部の線である場合、等値曲線は放物線になります。 準線を (y=0) に移動し、回転して水平になり、焦点を ((u,v)) に変換します。 放物線の頂点に変換し、必要に応じて垂直方向に反映すると、方程式は次のようになります。 

[
 x^2=py、\qquad p=2v。 
】

 有効性の境界は線です。 線 (y=kx+b) を放物線と交差すると、次のようになります。 

[
 x^2-pkx-pb=0、
 】

 これは単なる二次方程式です。 垂直境界は 1 つの (x) 座標を直接与えます。 
7. すべての候補線について、すべてのアクティブな半面境界と交差します。 すべての交差パラメータを候補線に沿って並べ替えます。 2 つの連続するパラメーターの間では、区間内で制約境界を越えないため、候補は完全に有効か完全に無効になります。 すべての間隔の中点をテストし、有効な間隔の長さを追加します。 
8. すべての候補放物線について、その (x) パラメーターで同じプロセスを実行します。 すべての根を並べ替えた後、対応する放物線点 ((x,x^2/p)) を評価して中点 (x) をテストします。 円弧の長さは、数値サンプリングではなく解析的に計算されます。 

\frac{x\sqrt{1+4x^2/p^2}}2+
 \frac p4\オペレーター名{asinh}\left(\frac{2x}{p}\right)。 
】
 9. 9 つの特徴の組み合わせすべてから有効な部分を合計します。 これらは、長さがゼロである機能遷移境界を除いて互いに素です。 それらの結合はまさに長方形内のボロノイ二等分線であるため、累積された長さが必要な最小ルートになります。 

### なぜ効果があるのか

 平面のすべての点について、セグメント上の最も近い点は、2 つの記述が一致する境界を除き、その第 1 端点、第 2 端点、または内部投影として一意に分類されます。 したがって、9 つの特徴ペアは等価軌跡全体をカバーします。 

1 つの固定特徴ペア内では、両方の距離関数が固定代数形式を持ちます。 したがって、等式軌跡は、上で処理した 3 つのオブジェクト、つまり直線、角の二等分線、放物線のいずれかになります。 半平面制約により、選択したフィーチャが実際にセグメント上の最も近い点であることが保証されます。 これらのオブジェクトをすべての制約境界と交差させると、有効性が一定である間隔にオブジェクトが分割されます。 

したがって、等価軌跡の正の長さの部分はすべて 1 回カウントされ、無効な部分はカウントされません。 2 つの互いに素な凸状セグメントの等価軌跡は、それらの 2 つのボロノイ領域間のセパレータであるため、長方形の内側の部分が必要な境界から境界へのルートになります。 したがって、その全長が有効な最小パス長になります。 

## Python ソリューション```python
import sys
import math

input = sys.stdin.readline

EPS = 1e-9
INF = 1e100

def add(a, b):
    return (a[0] + b[0], a[1] + b[1])

def sub(a, b):
    return (a[0] - b[0], a[1] - b[1])

def mul(a, k):
    return (a[0] * k, a[1] * k)

def rot90(a):
    return (-a[1], a[0])

def rot270(a):
    return (a[1], -a[0])

def flip(a):
    return (-a[0], -a[1])

def cross(a, b):
    return a[0] * b[1] - a[1] * b[0]

def length(a):
    return math.hypot(a[0], a[1])

def sgn(x):
    if x > EPS:
        return 1
    if x < -EPS:
        return -1
    return 0

def line_intersection(a, b, p, q):
    """
    Intersect infinite lines AB and PQ.

    Return:
        (2, None, None) if coincident,
        (0, None, None) if parallel,
        (1, point, t) otherwise, where point = A + t(B-A).
    """
    d1 = sub(b, a)
    d2 = sub(q, p)
    den = cross(d1, d2)
    num = cross(sub(p, a), d2)

    if sgn(den) == 0:
        if sgn(num) == 0:
            return 2, None, None
        return 0, None, None

    t = num / den
    return 1, add(a, mul(d1, t)), t

def solve_case(xl, yl, xr, yr, seg_a, seg_b):
    rect = [
        ((float(xl), float(yl)), (float(xr), float(yl))),
        ((float(xr), float(yl)), (float(xr), float(yr))),
        ((float(xr), float(yr)), (float(xl), float(yr))),
        ((float(xl), float(yr)), (float(xl), float(yl))),
    ]

    total = 0.0

    def check(point, edges):
        for a, b in edges:
            if sgn(cross(sub(point, a), sub(b, a))) > 0:
                return False
        return True

    def call_line(a, b, edges):
        """
        Add the valid length of the infinite line AB under all
        current half-plane constraints.
        """
        values = []

        for p, q in edges:
            typ, _, t = line_intersection(a, b, p, q)
            if typ == 2:
                return 0.0
            if typ == 1:
                values.append(t)

        if len(values) < 2:
            return 0.0

        values.sort()
        dlen = length(sub(b, a))
        ret = 0.0

        for i in range(1, len(values)):
            t1 = values[i - 1]
            t2 = values[i]
            mid = (t1 + t2) * 0.5
            p = add(a, mul(sub(b, a), mid))
            if check(p, edges):
                ret += (t2 - t1) * dlen

        return ret

    def integral_parabola(p, x):
        """
        Integral of sqrt(1 + 4*x^2/p^2) dx.
        """
        if p <= 0:
            return 0.0

        z = 2.0 * x / p
        root = math.sqrt(1.0 + z * z)
        return 0.5 * x * root + 0.25 * p * math.asinh(z)

    def solve_features(edges, tp0, f0, tp1, f1):
        nonlocal total

        if tp0 > tp1:
            tp0, tp1 = tp1, tp0
            f0, f1 = f1, f0

        A = f0[0]
        B = f0[1] if len(f0) > 1 else None
        C = f1[0]
        D = f1[1] if len(f1) > 1 else None

        if tp0 == 0 and tp1 == 0:
            mid = mul(add(A, C), 0.5)
            direction = rot90(sub(A, mid))
            total += call_line(add(mid, direction), mid, edges)
            return

        if tp0 == 1 and tp1 == 1:
            typ, o, _ = line_intersection(A, B, C, D)

            if typ == 0:
                origin = mul(add(A, C), 0.5)
                total += call_line(origin, add(origin, sub(D, C)), edges)
                return

            if typ == 2:
                return

            if length(sub(A, o)) < length(sub(B, o)):
                A, B = B, A

            if length(sub(C, o)) < length(sub(D, o)):
                C, D = D, C

            ang1 = math.atan2(A[1] - o[1], A[0] - o[0])
            ang2 = math.atan2(C[1] - o[1], C[0] - o[0])
            ang = (ang1 + ang2) * 0.5

            direction = (math.cos(ang), math.sin(ang))
            total += call_line(o, add(o, direction), edges)
            total += call_line(o, add(o, rot90(direction)), edges)
            return

        # Point-line case.
        # A is the point, CD is the supporting line.
        direction = sub(D, C)

        # Translate C to the origin.
        A = sub(A, C)
        transformed = [(sub(p, C), sub(q, C)) for p, q in edges]

        # Rotate CD onto the positive x-axis.
        w = math.atan2(direction[1], direction[0])
        cw = math.cos(-w)
        sw = math.sin(-w)

        def rotate_point(p):
            return (p[0] * cw - p[1] * sw,
                    p[0] * sw + p[1] * cw)

        A = rotate_point(A)
        transformed = [
            (rotate_point(p), rotate_point(q))
            for p, q in transformed
        ]

        if A[1] < 0:
            A = flip(A)
            transformed = [(flip(p), flip(q)) for p, q in transformed]

        p = 2.0 * A[1]

        if sgn(p) == 0:
            return

        vertex = (A[0], A[1] * 0.5)

        transformed = [
            (sub(a, vertex), sub(b, vertex))
            for a, b in transformed
        ]

        roots = []

        for a, b in transformed:
            dx = a[0] - b[0]

            if sgn(dx) == 0:
                roots.append(a[0])
                continue

            k = (a[1] - b[1]) / dx
            bb = a[1] - k * a[0]

            # x^2 - p*k*x - p*b = 0
            disc = p * p * k * k + 4.0 * p * bb

            if sgn(disc) < 0:
                continue

            if disc < 0:
                disc = 0.0

            root = math.sqrt(disc)
            roots.append((p * k - root) * 0.5)
            roots.append((p * k + root) * 0.5)

        if len(roots) < 2:
            return

        roots.sort()

        prev_value = None

        for i, x in enumerate(roots):
            value = integral_parabola(p, x)

            if i > 0:
                mid = (roots[i - 1] + x) * 0.5
                point = (mid, mid * mid / p)

                if check(point, transformed):
                    total += value - prev_value

            prev_value = value

    segments = [seg_a, seg_b]

    for state_a in range(3):
        for state_b in range(3):
            edges = list(rect)
            features = [None, None]
            types = [state_a, state_b]

            for idx, state in enumerate(types):
                a, b = segments[idx]
                d = sub(b, a)

                if state == 0:
                    # Closest point is a.
                    edges.append((a, add(a, rot90(d))))
                    features[idx] = [a]

                elif state == 1:
                    # Closest point is b.
                    edges.append((b, add(b, rot90(sub(a, b)))))
                    features[idx] = [b]

                else:
                    # Closest point is in the interior of AB.
                    edges.append((a, add(a, rot270(d))))
                    edges.append((b, add(b, rot270(sub(a, b)))))
                    features[idx] = [a, b]

            solve_features(
                edges,
                state_a,
                features[0],
                state_b,
                features[1]
            )

    return total

def solve(inp):
    rd = inp.readline
    t = int(rd())
    out = []

    for _ in range(t):
        xl, yl, xr, yr = map(int, rd().split())
        a = tuple(map(float, rd().split()))
        b = tuple(map(float, rd().split()))

        seg_a = ((a[0], a[1]), (a[2], a[3]))
        seg_b = ((b[0], b[1]), (b[2], b[3]))

        ans = solve_case(
            xl, yl, xr, yr,
            seg_a, seg_b
        )

        if abs(ans) < 5e-12:
            ans = 0.0

        out.append(f"{ans:.15f}")

    return "\n".join(out)

if __name__ == "__main__":
    sys.stdout.write(solve(sys.stdin))
```長方形は、フィーチャが考慮される前に 4 つの半平面の境界として挿入されます。 長方形は凸状であるため、これらの境界との交点をソートするだけで候補曲線をクリップできます。 

の`work`幾何学的な導出からの論理は 3 つの状態によって直接表現されます。 端点を通る垂直境界は、セグメントへの投影がその端点の前にあるかどうかを定義する内積条件から得られます。`call_line`無限直線を (A+t(B-A)) としてパラメータ化します。 制約が交差するたびに、(t) の 1 つの値が生成されます。 これらの値を並べ替えると、連続的なクリッピングの問題が一定数の間隔チェックに変わります。 その区間内では制約境界を越えないため、中間点をテストするだけで十分です。 

線と線の場合、支持線が交差する場合には特別な処理が必要です。 角の二等分線は 1 つではなく 2 つあります。 コードは、交点から離れる方向を指す光線を選択し、その方向を平均してから、2 番目の二等分線に垂直方向を使用します。 

点線の場合は数値的にデリケートな部分です。 変換により任意のセグメントの方向が削除され、その後の等式曲線は標準の放物線方程式 (x^2=py) になります。 すべての制約は垂直線または (y=kx+b) のいずれかになるため、すべての交差パラメーターは直接 (x) 値または二次方程式から得られます。 

パイソンの`float`必要な誤差は (10^{-9}) ですが、座標の大きさは (2000) のみであるため、ここでは十分です。 この実装では、外積、行列式、または判別式がゼロかどうかを判断するときに絶対イプシロンが使用されます。 これにより、小さな浮動小数点ノイズが幾何学的に平行な線を人為的な交差点に変えるのを防ぎます。 

## 実用的な例

 ### サンプル 1

 入力は```
1
0 0 7 6
2 4 4 4
3 2 5 2
```両方のセグメントは水平ですが、下のセグメントは右に 1 単位シフトされます。 等式曲線には 5 つの正の長さの部分があります。 9 つの機能の組み合わせを以下にまとめます。 

| 機能ペア | 等価曲線 | 有効な間隔 | 長さ |
 | --- | --- | --- | --- |
 | A-左端点、B-左端点 | 直線 (y=x/2+7/4) | (0\le x\le2) | (\sqrt5) |
 | A-内部、B-左端点 | 放物線 | (2\le x\le3) | (1.0402288194) |
 | A-インテリア、B-インテリア | (y=3) | (3\le x\le4) | (1) |
 | A-右端点、B-内部 | 放物線 | (4\le x\le5) | (1.0402288194) |
 | A-右端点、B-右端点 | 直線 (y=x/2+3/4) | (5\le x\le7) | (\sqrt5) |

 他の 4 つの特徴の組み合わせには、正の長さの有効な間隔がありません。 どちらの放物線部分についても、対応する座標を代入すると、弧長積分は次のようになります。 

1.040228819434551。 
】

 したがって、累積された長さは次のようになります。 

[
 2\sqrt5
 +1.040228819434551
 +1
 +1.040228819434551
 +2\sqrt5、
 】

 それは与える

 [
 7.552593593868681。 
】

 これは、公式のサンプル出力 (7.552593593868681136) と一致します。 

### 対称セグメント

 検討してください```
1
0 0 10 10
2 3 8 3
2 7 8 7
```2 つのセグメントは水平で、長さが等しく、互いの真上にあります。 それらの支持線の二等分線は (y=5) です。 

| 機能ペア | 有効な間隔 | 貢献 | 累積長さ |
 | --- | --- | --- | --- |
 | 左端点、左端点 | (0\le x\le2) | (2) | (2) |
 | インテリア・インテリア | (2\le x\le8) | (6) | (8) |
 | 右端点、右端点 | (8\le x\le10) | (2) | (10) |

 すべての混合点と線の組み合わせは、セグメントの終点で長さ 0 の遷移に折りたたまれます。 最終的な答えは(10)です。 

この例は、アルゴリズムがセグメントを単一の幾何学的プリミティブとして扱ってはいけない理由を示しています。 同じ最終二等分線は、1 つの直線のように見えますが、エンドポイント ケースと内部フィーチャ ケースから組み立てられます。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | テスト ケースごとの (O(1)) | 正確に 9 つの特徴ペアがあり、それぞれに一定数の制約、交差、ルート、および間隔チェックがあります。 
| スペース | (O(1)) | 最大でも一定数の点、境界、ルート、および一時的な間隔が保存されます。 

(10^5) のケース全体で、合計作業量は比較的小さな定数を持つケースの数において線形です。 元の問題には 6 秒の制限と 512 MB のメモリ制限があるため、四角形の座標領域への依存を避けることが重要です。 

## テストケース

 次のハーネスは、上記のソリューション コードが同じファイル内で利用可能であるか、インポートされていると想定しています。`solve`呼び出し可能です。```
import io
import math

def run(inp: str) -> str:
    return solve(io.BytesIO(inp.encode())).strip()

def assert_close(inp: str, expected: float, name: str):
    got = float(run(inp))
    assert math.isclose(got, expected, rel_tol=1e-10, abs_tol=1e-10), (
        f"{name}: got {got}, expected {expected}"
    )

# Provided sample
sample1 = """\
1
0 0 7 6
2 4 4 4
3 2 5 2
"""
assert_close(sample1, 7.552593593868681136, "sample 1")

# Minimum-size valid construction.
# The rectangle has width 2 and height 5.
# The two vertical segments occupy disjoint interior intervals.
case_minimum = """\
1
0 0 2 5
1 1 1 2
1 3 1 4
"""
assert_close(case_minimum, 2.0, "minimum-size case")

# Maximum-size rectangle and symmetric horizontal segments.
case_maximum = """\
1
-1000 -1000 1000 1000
-500 -500 500 -500
-500 500 500 500
"""
assert_close(case_maximum, 2000.0, "maximum-size case")

# Segment endpoints are as close as allowed to the rectangle boundary.
case_boundary = """\
1
0 0 5 5
1 1 4 1
1 4 4 4
"""
assert_close(case_boundary, 5.0, "boundary case")

# Both segments are vertical, exercising x1 == x2.
case_vertical = """\
1
0 0 10 10
3 2 3 8
7 2 7 8
"""
assert_close(case_vertical, 10.0, "vertical segments")
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`0 0 2 5`、(x=1) 上のセグメント |`2`| 2 つの素な整数座標セグメントを持つ実用的な最小の長方形 |
 |`-1000 -1000 1000 1000`、水平セグメント |`2000`| 最大の座標範囲と大きな幾何学的値 |
 |`0 0 5 5`、座標 (1) と (4) を使用したセグメント |`5`| 長方形の境界から許容される最も近い距離にある端点 |
 |`0 0 10 10`、垂直セグメント |`10`| 垂直セグメントとゼロ除算に対する保護 |

 ## 特殊なケース

 最初のエッジ ケースは、最近接点が端点であるセグメントです。 考慮する```
1
0 0 5 5
1 1 4 1
1 4 4 4
```(x<1) の場合、下のセグメントの最も近い点はその左端点であり、同じことが上のセグメントにも当てはまります。 そこでの等価軌跡は、端点-端点の垂直二等分線です。 アルゴリズムは、2 つの無限サポート ラインを誤って使用するのではなく、特徴状態 (0,0) を通じてこのケースに到達します。 最後のルートは、長さ (5) の完全な水平線 (y=2.5) です。 

2 番目のエッジ ケースは機能の移行です。 公式サンプルでは、​​(2<x<3) の場合、上部セグメントの最も近いフィーチャは内部であり、下部セグメントの最も近いフィーチャは左端点です。 (x=3) では、下側の最も近いフィーチャがセグメントの内部に変わります。 アルゴリズムは両方のフィーチャ状態を明示的に考慮し、遷移点はアクティブな半平面境界の 1 つとの交点として表示されます。 単一の点の長さはゼロであるため、二重カウントの問題は発生しません。 結果の合計は (7.552593593868681136) になります。 

3 番目のエッジ ケースは垂直セグメントです。 考慮する```
1
0 0 10 10
3 2 3 8
7 2 7 8
```2 本の補助線は (x=3) と (x=7) であるため、それらの二等分線は (x=5) になります。 ルートは ((5,0)) から ((5,10)) まで長方形を横切り、長さは (10) になります。 パラボラ コードでは、垂直制約線はゼロ (x) の差によって検出されるため、ゼロによる除算は発生しません。 

4 番目のエッジ ケースは平行な支持線です。 選択した 2 つの内部フィーチャが平行である場合、それらの間のちょうど 1 本の等距離の線が存在します。 このアルゴリズムは、サポート ラインの交点が存在しないことを検出し、中点ラインを直接構築します。 これにより、未定義の交点から角度を形成しようとすることが回避されます。 

5 番目のエッジ ケースは、交差するサポート ラインのペアです。 2 つの非平行なセグメントには、有限セグメント自体が互いに素であっても、交わる支持線がある場合があります。 これら 2 つの直線までの距離が等しいと、2 つの角の二等分線が得られます。 このアルゴリズムは両方を生成し、実際のセグメントに対応しない部分をフィーチャの半平面で破棄させます。 これが必要なのは、角の二等分線を 1 つだけ保持すると、ボロノイ二等分線の有効な部分が削除される可能性があるためです。 

最後のエッジ ケースは、制約境界での数値縮退です。 候補曲線は、特に端点フィーチャと内部フィーチャの間の移行部で、半平面境界と正確に一致する可能性があります。 同じ幾何学的部分が隣接するフィーチャ ケースで表されるため、実装では、一致するラインはそのフィーチャ ケースでは寄与ゼロとして扱われます。 これにより、同じ正の長さの曲線が 2 回カウントされるのを防ぎます。 

このバージョンは、式を優先するのではなく、意図的に幾何学的なものになっています。最近接特徴分解が理解されれば、3 つの曲線タイプとそのクリッピング ルールを同様のボロノイ問題に対して再導出できます。
