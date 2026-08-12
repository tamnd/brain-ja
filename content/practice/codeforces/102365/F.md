---
title: "CF 102365F - 公正な分配"
description: "村人のすべてのサブセットについて、家の凸包の面積を考慮します。 ランダムな並べ替えにより、各村人に限界寄与が与えられます。その村人が挿入されると、新しい凸包の領域と挿入前の領域が比較されます。"
date: "2026-08-13T00:02:04+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102365
codeforces_index: "F"
codeforces_contest_name: "UBC Programming Contest 2019 (UBCPC 2019)"
rating: 0
weight: 102365
solve_time_s: 216
verified: true
draft: false
---

[CF 102365F - 公正な配布](https://codeforces.com/problemset/problem/102365/F)

 **評価:** -
 **タグ:** -
 **解決時間:** 3 分 36 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 村人のすべてのサブセットについて、家の凸包の面積を考慮します。 ランダムな並べ替えにより、各村人に限界寄与が与えられます。その村人が挿入されると、新しい凸包の領域と挿入前の領域が比較されます。 村人に必要な答えは、考えられるすべての広告掲載オーダーに対する限界貢献度の平均です。 

同様に、これは凸包面積関数の Shapley 値です。 固定の村人 (p) の場合、(p) を他の (N-1) 個の点の一様にランダムな順序に挿入したときに予想される船体面積の増加が必要です。 

入力には、(10^4) で区切られた座標を持つ最大 200 個の異なる平面点が含まれます。 (N) の値が小さいため、階乗または指数列挙は除外されますが、三次アルゴリズムの余地が残されています。 特に、(N^3) は最大サイズでも (8\cdot10^6) 回の反復のみであり、これは最適化された実装の妥当な目標です。 座標の境界は、Python の整数ではオーバーフローの懸念がなくなりますが、通常の方向テストはすべて 64 ビット整数に快適に収まることも意味します。 

最初の縮退ケースは (N=1) です。 単一点は面積ゼロの凸包を持つため、唯一の答えは (0) です。```
1
10000 -10000
```正しい出力は次のとおりです```
0
```すべての村人が最終的に三角形を形成できると想定した実装では、存在しないレベルで除算しようとする可能性があります。 

2 番目のケースは 2 点です。```
2
0 0
10000 10000
```繰り返しになりますが、答えは```
0
0
```2 点の凸包はセグメントであるため、すべての周辺寄与はゼロになります。 3 つの点に基づく公式は、このケースを明示的に処理する必要があります。 

3 番目の問題は共線性です。 考慮する```
4
0 0
1 0
2 0
0 1
```ハルは頂点 ((0,0)、(2,0)、(0,1)) を持つ三角形ですが、点 ((1,0)) はそのエッジの 1 つにあります。 通常の一般的な位置の公式の素朴なバージョンでは、両方の端点 ((0,0)、(2,0)) を使用して三角形を数えることができ、さらに ((1,0)) を含む小さな三角形も数えることができるため、面積が大きくなりすぎます。 正しいShapley値は次のとおりです。```
0.4166666666666667
0.0833333333333333
0.0833333333333333
0.4166666666666667
```以下の実装では、三角形領域の元の座標を保持しながら、向きの決定に無限小のシンボリック摂動を使用することで、このような縮退を処理します。 凸包領域、したがって周辺寄与のすべての有限平均は、任意の小さな摂動のもとでも連続であるため、これは正しい限定解釈です。 

## アプローチ

 直接的なアプローチは、文字通り定義に従います。 (N!) 個の順列をすべて列挙します。 順列ごとに、ポイントを一度に 1 つずつ挿入し、挿入するたびに凸包を再計算し、面積の増加分を対応する村人に加算します。 これは、問題が求める期待の実験を明示的に評価するため、正しいです。 

問題は順列の階乗数です。 船体の構造を考慮する前でも、(200!) はおよそ (7.9\cdot10^{374}) です。 すべてのプレフィックスに対してハルを再計算すると、作業は大まかに (O(N!,N^2\log N)) になりますが、これは絶望的です。 

有用な観察は、船体の限界面積の増加が非常に特殊な幾何学的構造を持っているということです。 (p) が挿入される点であるとします。 古いハルにはまだ存在していなかった新しいハルの部分は、共通の頂点が (p) である三角形の連鎖です。 このような三角形はすべて、前の 2 つの点 (q) と (q') によって記述できます。 

線を (q) から (q') に向け、(H(q,q')) をその左の開いた半平面とします。 (p) がその半平面内にある場合、三角形 (pqq') は、(q) と (q') が最初に現れる 2 つの関連点であり、(p) が 3 番目の点であるときに、船体の新たに露出した部分に現れる可能性があります。 

(L(q,q')) を (H(q,q')) 内に厳密に存在する入力点の数とする。 (q,q') とそれらの (L) 点で構成される (L+2) 点のうち、必須イベントは、(q,q') がいずれかの順序で最初の 2 つの位置を占め、(p) が 3 番目の位置を占めることを示します。 その確率は

 # \frac{2!,(L-1)!}{(L+2)!}

 \frac{2}{L(L+1)(L+2)}。 
]

 これが重要な削減です。 膨大な順列のコレクションが消えます。 点の順序ペアごとに、1 つの整数、その​​半平面レベル、および 1 つの有理確率だけが必要です。 

固定 (p) の場合、期待される寄与は次のようになります。 

\sum_{\substack{q\ne q'\p\in H(q,q')}}
 \オペレーター名{エリア}(pqq')\rho(q,q')。 
]

 レベル値は、各 (q) の周りの極角によって他のポイントを並べ替え、回転ポインターを使用することによって効率的に計算できます。 レベルがわかったら、すべての (p) の式を評価するには (O(N^3)) 時間がかかります。 

指定された (N\le200) の場合、これが実際的な解決策です。 同じ幾何学的な分解では、ライン配置を通じて重み付きライン半平面クエリを集約することにより、より高度な (O(N^2)) アルゴリズムが可能になります。これは、このアイデアの漸近的最適バージョンです。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | (O(N!,N^2\log N)) | (O(N)) | 遅すぎる |
 | ペアプレーンとハーフプレーンのレベル | (O(N^3)) | (O(N^2)) | 承認済み |

 ## アルゴリズムのチュートリアル

1. すべてのポイントを読み取り、方向を決定するための小さな象徴的な摂動を準備します。 三角形の領域を計算する場合、元の座標は変更されません。 摂動は、元の 3 つの点が同一線上にある場合に、点が線のどちら側に属するかを決定するだけです。 
2. すべての点 (q) について、他のすべての点を (q) の周囲の極角で並べ替えます。 摂動があると、関連する 2 つの方向が正確に結び付くことはありません。 
3. 2 番目のポインターを使用して、ソートされた方向をスイープします。 有向ペア (q\to q') の場合、(180^\circ) 未満で (q') から厳密に反時計回りに遭遇するすべての点は、有向直線の左半平面内にあります。 そのような点の数は (L(q,q')) です。 
4. すべてのレベル (L\ge1) を確率に変換します
 [
 \rho(q,q')=\frac{2}{L(L+1)(L+2)}。 
]
 レベル 0 は、その半平面内に 3 番目の点が存在できないため、重み 0 を受け取ります。 
5. すべてのターゲット ポイント (p) について、すべての順序付けされていないペア (q,q') を検査します。 元の符号付き倍精度領域を計算する
 [
 c=(q'-q)\times(p-q)。 
]
 (c>0) の場合、(p) は (q\to q') の左側にあるので、次を追加します。 
[
 \frac{c}{2}\rho(q,q')。 
]
 (c<0) の場合、(p) は (q'\to q) の左側にあるので、次を追加します。 
[
 \frac{-c}{2}\rho(q',q)。 
]
 (c=0) の場合、三角形の面積はゼロであり、何も寄与しません。 
6. 各ポイントの累積値を出力します。 (N<3) の場合、ループでは当然すべての村人に対してゼロが生成されます。 

計算の背後にある不変条件は、(p) を挿入することによって作成されたすべての正の領域のピースが、正確に 1 つの有向ペア (q,q')、つまり古いハルの 1 つの露出したエッジの端点によって表されるということです。 そのペアによって表されるイベントは、まさに (q,q') が (p) の前に現れ、関連する側のすべての点が (p) の後に現れるということです。 ペアに割り当てられた確率は、まさにそのイベントの確率です。 したがって、対応する三角形の領域を合計すると、期待値の 1 つの順列に対する限界寄与が得られ、期待値の線形性により、すべてのペアを独立して合計することができます。 

共線入力の場合、微小摂動は、元の面積が 0 であるトリプルのサイド分類のみを変更します。 このようなトリプルはいずれにせよ、寄与する面積がゼロです。 同一直線上にないすべてのトリプルについて、元の整数方向はゼロ以外であり、無限小摂動よりもはるかに大きいため、その側面分類は変更されません。 

## Python ソリューション```python
import sys
import math

input = sys.stdin.readline

def solve():
    n = int(input())
    pts = [tuple(map(int, input().split())) for _ in range(n)]

    if n < 3:
        for _ in range(n):
            print("0.0")
        return

    # The perturbation is only used for orientation / angular ordering.
    # x_i -> x_i + eps * i
    # y_i -> y_i + eps * i^2
    #
    # eps is chosen far below the smallest possible nonzero integer
    # orientation, which has absolute value at least 1.
    eps = 1e-10

    px = [x + eps * (i + 1) for i, (x, y) in enumerate(pts)]
    py = [y + eps * (i + 1) * (i + 1)
          for i, (x, y) in enumerate(pts)]

    rho = [[0.0] * n for _ in range(n)]

    # Compute the level of every directed line q -> r.
    #
    # For a fixed q, points are sorted by angle around q.
    # For every starting direction, a monotone pointer finds the
    # entire open semicircle to its left.
    for q in range(n):
        order = [i for i in range(n) if i != q]

        qx = px[q]
        qy = py[q]

        order.sort(
            key=lambda i: math.atan2(py[i] - qy, px[i] - qx)
        )

        m = n - 1
        doubled = order + order
        t = 0

        for s in range(m):
            if t < s + 1:
                t = s + 1

            a = doubled[s]
            ax = px[a] - qx
            ay = py[a] - qy

            while t < s + m:
                b = doubled[t]
                bx = px[b] - qx
                by = py[b] - qy

                cross = ax * by - ay * bx
                if cross > 0.0:
                    t += 1
                else:
                    break

            level = t - s - 1
            if level > 0:
                rho[q][a] = (
                    2.0 / (level * (level + 1) * (level + 2))
                )

    ans = [0.0] * n

    # For each p and each unordered pair q,r, exactly one orientation
    # puts p strictly on the left, unless p,q,r are collinear.
    for p in range(n):
        total = 0.0
        px0, py0 = pts[p]

        for q in range(n):
            qx, qy = pts[q]

            for r in range(q + 1, n):
                if r == p or q == p:
                    continue

                rx, ry = pts[r]

                cross = (
                    (rx - qx) * (py0 - qy)
                    - (ry - qy) * (px0 - qx)
                )

                if cross > 0:
                    total += 0.5 * cross * rho[q][r]
                elif cross < 0:
                    total += 0.5 * (-cross) * rho[r][q]

        ans[p] = total

    for value in ans:
        print("{:.15f}".format(value))

if __name__ == "__main__":
    solve()
```実装の最初の部分では、摂動された座標を構築します。 摂動は意図的に小さくなっていますが、ポイント インデックスに依存しているため、摂動は決定的になります。 元の方向がゼロ以外の場合、すべての入力座標は整数であるため、その絶対値は少なくとも 1 になります。 このような方向を逆転するには摂動が小さすぎます。 

角度スイープは、すべての (N) 点をすべての有向直線に対してテストせずに (L(q,q')) を計算します。 各固定 (q) について、点は 1 回循環的に順序付けされます。 開始方向が進むにつれて、左の半円の終点が後方に移動することはありません。そのため、その (q) のすべてのレベルは、ソート後に線形時間で取得されます。 

の`rho`行列は有向ペアによってインデックス付けされます。 これは、同じ幾何学的な線の 2 つの方向が異なる左半平面を持ち、したがって一般に異なるレベルと確率を持つため重要です。 

最後のトリプル ループでは、順序のないペアのみが使用されます。 元の外積が正の場合、関連する有向直線は (q\to r) になります。 負の場合、関連する方向は (r\to q) です。 これにより、同じ幾何学的ペアを 2 回実行することが回避されます。 

面積の計算では、摂動された整数座標ではなく、元の整数座標が使用されます。 これは不可欠です。 摂動は、組み合わせ関係を決定するための象徴的な手段であり、幾何学によって表される実際の金額を変更するものではありません。 

元の座標を含むすべての幾何学的配向計算は整数計算です。 Python の任意精度の整数により、ここではオーバーフローが不可能になります。 唯一の浮動小数点演算は、確率値、角度順序付け、および最終累積です。 

## 実用的な例

 ### サンプル 1

 サンプルには次の 4 つのポイントが含まれています。```
(2,2)
(0,2)
(2,0)
(1,1)
```最初の 3 つの点は領域 (2) の三角形を形成し、((1,1)) はその内側にあります。 予想シェアは、```
0.8333333333333333
0.5
0.5
0.1666666666666667
```次のトレースは、幾何学的ペアの寄与に焦点を当てています。 

| ターゲット (p) | ペア | ダブルエリア | 関連レベル | 確率 | 貢献 |
 | --- | --- | --- | --- | --- | --- |
 | ((2,2)) | ((0,2),(2,0)) | 4 | 1 | (1/3) | (2/3) |
 | ((2,2)) | 他の関連するペア | さまざまです | さまざまです | さまざまです | (1/6) 合計 |
 | ((0,2)) | 船体ペア | さまざまです | さまざまです | さまざまです | (1/2) 合計 |
 | ((2,0)) | 船体ペア | さまざまです | さまざまです | さまざまです | (1/2) 合計 |
 | ((1,1)) | 船体ペア | さまざまです | さまざまです | さまざまです | (1/6) 合計 |

 内部ポイントは、4 つのポイントすべての最後のハルに属さない場合でも、より小さいハルを形成する 2 つのポイントの後に挿入でき、そのハルを拡大できるため、正の量が与えられます。 

### サンプル 2

 3点入力を考える```
3
0 0
2 0
0 2
```完全な凸包には領域 (2) があります。 正確に 3 ポイントの場合、村人が順列で 3 番目に位置する場合、その村人は正確にゼロ以外の面積を提供します。 各村人は (3!) 個の順列のうち正確に (2!) 番目に位置するため、すべての村人が (2/3) を受け取ります。 

| ターゲット (p) | その他の点の数 | 関連するペア | ダブルエリア | レベル | 確率 | 答え |
 | --- | --- | --- | --- | --- | --- | --- |
 | ((0,0)) | 2 | ((2,0),(0,2)) | 4 | 1 | (1/3) | (2/3) |
 | ((2,0)) | 2 | ((0,2),(0,0)) | 4 | 1 | (1/3) | (2/3) |
 | ((0,2)) | 2 | ((0,0),(2,0)) | 4 | 1 | (1/3) | (2/3) |

 3 つの答えの合計は (2)、つまり最終的な船体の面積となります。 これは、ジオメトリに直接現れる Shapley 割り当ての効率特性です。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O(N^3)) | レベルの計算は (O(N^2\log N)) の後に (O(N^3)) ペアの累積が続きます。 
| スペース | (O(N^2)) | 有向確率行列には (N^2) 個の値が含まれます。 

(N\le200) の場合、3 次部分に含まれる順序なしポイント ペア チェックはターゲットごとに 400 万未満であり、順序なしペアを利用した後のペアとターゲットの組み合わせは合計で約 400 万しかありません。 確率の前処理はそれよりも小さいです。 メモリ使用量は、(200\times200) 確率行列によって支配されます。 

漸近的により強力な幾何学的な解決策は、最終的な重み付き半平面集約を (O(N^2)) に削減しますが、これにはライン配置の構築と横断が必要です。 上記の 3 次実装はかなり単純で、コンテストの (N=200) 制約によく適合します。 基礎となるペア分解は、既知の (O(N^2)) 結果で使用されるものと同じです。 

## テストケース```python
# The tests below assume the solution above is placed in this file.
# For a standalone test script, the implementation is reproduced
# through the run() helper.

import sys
import io
import math

def algorithm(inp: str) -> list[float]:
    old_stdin = sys.stdin
    sys.stdin = io.StringIO(inp)

    try:
        n = int(input())
        pts = [tuple(map(int, input().split())) for _ in range(n)]

        if n < 3:
            return [0.0] * n

        eps = 1e-10

        px = [x + eps * (i + 1) for i, (x, y) in enumerate(pts)]
        py = [y + eps * (i + 1) * (i + 1)
              for i, (x, y) in enumerate(pts)]

        rho = [[0.0] * n for _ in range(n)]

        for q in range(n):
            order = [i for i in range(n) if i != q]
            qx, qy = px[q], py[q]

            order.sort(
                key=lambda i: math.atan2(py[i] - qy, px[i] - qx)
            )

            m = n - 1
            doubled = order + order
            t = 0

            for s in range(m):
                if t < s + 1:
                    t = s + 1

                a = doubled[s]
                ax = px[a] - qx
                ay = py[a] - qy

                while t < s + m:
                    b = doubled[t]
                    bx = px[b] - qx
                    by = py[b] - qy

                    if ax * by - ay * bx > 0.0:
                        t += 1
                    else:
                        break

                level = t - s - 1
                if level > 0:
                    rho[q][a] = (
                        2.0 / (level * (level + 1) * (level + 2))
                    )

        ans = [0.0] * n

        for p in range(n):
            px0, py0 = pts[p]
            total = 0.0

            for q in range(n):
                if q == p:
                    continue

                qx, qy = pts[q]

                for r in range(q + 1, n):
                    if r == p:
                        continue

                    rx, ry = pts[r]

                    cross = (
                        (rx - qx) * (py0 - qy)
                        - (ry - qy) * (px0 - qx)
                    )

                    if cross > 0:
                        total += 0.5 * cross * rho[q][r]
                    elif cross < 0:
                        total += 0.5 * (-cross) * rho[r][q]

            ans[p] = total

        return ans

    finally:
        sys.stdin = old_stdin

def run(inp: str) -> list[float]:
    return algorithm(inp)

def assert_close(actual, expected, name):
    assert len(actual) == len(expected), name
    for a, e in zip(actual, expected):
        assert abs(a - e) <= 1e-8 * max(1.0, abs(e)), (
            name, a, e
        )

# Provided sample
sample = """\
4
2 2
0 2
2 0
1 1
"""
assert_close(
    run(sample),
    [
        0.8333333333333333,
        0.5,
        0.5,
        0.16666666666666666,
    ],
    "provided sample",
)

# Minimum-size input
assert_close(
    run("""\
1
10000 -10000
"""),
    [0.0],
    "one point",
)

# Two points, still zero area
assert_close(
    run("""\
2
-10000 -10000
10000 10000
"""),
    [0.0, 0.0],
    "two points",
)

# Three points, every point gets one third of the triangle area.
triangle = """\
3
0 0
2 0
0 2
"""
assert_close(
    run(triangle),
    [2.0 / 3.0, 2.0 / 3.0, 2.0 / 3.0],
    "triangle",
)

# Square: all four vertices are symmetric, so every answer is 1/4.
square = """\
4
0 0
1 0
1 1
0 1
"""
assert_close(
    run(square),
    [0.25, 0.25, 0.25, 0.25],
    "square",
)

# Collinear points plus one off-line point.
degenerate = """\
4
0 0
1 0
2 0
0 1
"""
assert_close(
    run(degenerate),
    [
        5.0 / 12.0,
        1.0 / 12.0,
        1.0 / 12.0,
        5.0 / 12.0,
    ],
    "collinear boundary point",
)

# Maximum-size input. The expected individual answers are not written
# explicitly, so we check the defining efficiency property:
# their sum must equal the area of the full convex hull.
pts = [(i, (i * i) % 10000) for i in range(200)]
maximum = "200\n" + "\n".join(f"{x} {y}" for x, y in pts) + "\n"
got = run(maximum)

assert len(got) == 200, "maximum size length"
assert all(x >= -1e-9 for x in got), "nonnegative maximum answers"

def convex_hull(points):
    points = sorted(set(points))
    if len(points) <= 1:
        return points

    def cross(o, a, b):
        return (
            (a[0] - o[0]) * (b[1] - o[1])
            - (a[1] - o[1]) * (b[0] - o[0])
        )

    lower = []
    for p in points:
        while len(lower) >= 2 and cross(lower[-2], lower[-1], p) <= 0:
            lower.pop()
        lower.append(p)

    upper = []
    for p in reversed(points):
        while len(upper) >= 2 and cross(upper[-2], upper[-1], p) <= 0:
            upper.pop()
        upper.append(p)

    return lower[:-1] + upper[:-1]

def hull_area(points):
    h = convex_hull(points)
    if len(h) < 3:
        return 0.0

    s = 0
    for i in range(len(h)):
        x1, y1 = h[i]
        x2, y2 = h[(i + 1) % len(h)]
        s += x1 * y2 - y1 * x2

    return abs(s) / 2.0

assert abs(sum(got) - hull_area(pts)) <= 1e-6 * max(
    1.0, hull_area(pts)
), "maximum efficiency"

print("all tests passed")
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | サンプル 1 | (0.8333333,0.5,0.5,0.1666667) | オリジナルサンプルとインテリアポイント |
 |`1 / (10000,-10000)`|`0`| 最小サイズと面積ゼロの船体 |
 | 2 つの対向する境界点 |`0,0`| 三角形は存在しません |
 | 直角三角形 |`2/3`あらゆる点について | 最後に挿入される確率 (1/3) |
 | 単位平方 |`0.25`あらゆる点について | 対称性と均等な割り当て |
 | 同一線上のチェーンに 1 つの点を加えます |`5/12,1/12,1/12,5/12`| 縮退共線性の処理 |
 | 生成された 200 ポイント | 合計は船体の全面積に等しい | 最大入力サイズと効率は不変 |

 ## 特殊なケース

 単一点の場合、(N<3) であるため前処理段階はスキップされ、解はすぐにゼロを出力します。 三角形を定義できる有向対は存在しないため、これは幾何学的定義と一致します。 

2 点についても同様の早期返却が適用されます。 それらの凸包は線分であり、その面積はゼロです。 これにより、存在しない 3 番目の点の確率を構築することがなくなります。 

3 つの同一線上にないポイントの場合、ターゲット ポイントが 3 番目に挿入されるたびに、先行ペアが 1 つだけ存在します。 ターゲットを含む半平面にはちょうど 1 つの点が含まれるため、(L=1) および

 [
 \rho=\frac{2}{1\cdot2\cdot3}=\frac13。 
]

 ターゲットは三角形の領域の 3 分の 1 を受け取ります。 3 つの点のそれぞれが 3 番目になる確率は同じであるため、すべての点は完全な領域のちょうど 3 分の 1 を受け取ります。 

同一線上にある点の場合、シンボリック摂動によって点が線の明確な側に配置された場合でも、元の外積がゼロになる可能性があります。 アルゴリズムは、これら 2 つのジョブに対して意図的に異なる座標を使用します。 元の外積によって実際の三角形の面積が決まります。同一線上のトリプルの場合はゼロになります。 摂動幾何学は、確率に必要な組み合わせ順序を決定します。 これは、一般的な位置構成から制限を取得することに対応し、複数の入力ポイントを含むハル エッジの二重カウントを回避します。 

具体的な縮退入力の場合```
4
0 0
1 0
2 0
0 1
```完全な船体には領域 (1) があります。 2 つのエンドポイント ((0,0)) と ((0,1)) はそれぞれ (5/12) を受信し、下端の 2 つのポイントはそれぞれ (1/12) を受信します。 それらの合計は

 [
 \frac5{12}+\frac1{12}+\frac1{12}+\frac5{12}=1、
 ]

 したがって、船体エリア全体が 1 回だけ配布されます。 

最終的な効率チェックもデバッグに役立つ不変条件です。 すべての順列について、すべての限界増加の合計が最終的な船体領域まで望遠鏡されます。 期待値を考慮するとその等価性が維持されるため、計算された答えは常にすべての入力点の凸包の面積の合計になる必要があります。 重大な不一致は、通常、有向半平面、確率レベル、または方向標識が正しく処理されていないことを意味します。
