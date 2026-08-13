---
title: "CF 102452F - 落下物"
description: "各オブジェクトは、立方体、球、正四面体の 3 つの凸面固体のいずれかです。 そのサイズ、方向、水平リリース位置が示されています。 オブジェクトは一度に 1 つずつ解放され、それぞれが垂直方向にのみ落下します。"
date: "2026-08-12T08:28:57+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102452
codeforces_index: "F"
codeforces_contest_name: "2019-2020 ICPC Asia Hong Kong Regional Contest"
rating: 0
weight: 102452
solve_time_s: 179
verified: true
draft: false
---

[CF 102452F - 落下物](https://codeforces.com/problemset/problem/102452/F)

 **評価:** -
 **タグ:** -
 **解決時間:** 2 分 59 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 各オブジェクトは、立方体、球、正四面体の 3 つの凸面固体のいずれかです。 そのサイズ、方向、水平リリース位置が示されています。 オブジェクトは一度に 1 つずつ解放され、それぞれが垂直方向にのみ落下します。 地面やすでに着地している物体に接触すると、永久に停止します。 

この回転は、適切な z-X-Z オイラー回転です。 適用後は、結果として得られる固定ジオメトリのみが必要になります。 物体 (i) についての問題は、それが解放された場所ではなく、垂直落下後の最高点の最終的な (z) 座標です。 

重要な問題は、オブジェクトが任意の点で別のオブジェクトに接触する可能性があることです。 たとえば、立方体は、最初に面に沿って、エッジまたは頂点で別の立方体に接触する可能性があります。 球は、明らかではない点で多面体の面またはエッジに接触する可能性があります。 各ソリッドを単一の境界ボックスとして扱うと、着地高さを決定する幾何学的情報が正確に失われます。 

この制約により、二次シミュレーションが可能になります。 すべてのテスト ケースで合計で最大 (1000) 個のオブジェクトが存在するため、すべての新しいオブジェクトについてすべての以前のオブジェクトを考慮すると、約 (5\cdot10^5) 個のオブジェクト ペアしか得られません。 時間制限は 5.5 秒、メモリ制限は 512 MB です。 したがって、(O(n^2)) シミュレーションが必要になりますが、各ペアは、数値的な落下シミュレーションではなく、慎重に選択された定数時間幾何学演算で処理する必要があります。 公式の問題ページでは、同じ (n\le1000) 制限と 5.5 秒の制限が示されています。 

不注意な実装を露呈する微妙なケースがいくつかあります。 半径 (1) で地上に放たれた球は中心の高さが (1) でなければならないため、その最高点は (2) になります。 半径を最高点として解釈する実装では (1) が出力されますが、これは誤りです。```
1
1
1 0 0 0 0 0 1
```答えは(2)です。 

2 番目の罠は接線性です。 中心が水平方向 (2) 単位離れている 2 つの単位球は、一点で正確に接触します。 2 番目の球は、水平方向の投影が境界でしか接触していないにもかかわらず、依然としてそこで停止しています。```
1
2
1 0 0 0 0 0 1
1 0 0 0 2 0 1
```最高点はいずれも（２）。 正の領域のオーバーラップを必要とする厳密な交差テストでは、2 番目の球体が誤って地面に落ちてしまいます。 

3 番目の罠は回転です。 四面体は任意の回転では対称ではないため、その最大および最小の垂直オフセットはオイラー角に依存します。 回転していない高さだけを使用すると、間違った着地位置が得られます。```
1
1
2 191 98 10 25 25 2
```正しい最高点は約 (1.9504473433) であり、回転していない四面体の高さではありません。 

## アプローチ

 魅力的な総当たりシミュレーションは、オブジェクトを少しずつ下げ、前のオブジェクトと交差するかどうかをテストし、交差が検出されたときに停止することです。 これは概念的には簡単ですが、必要な (10^{-4}) 精度を効率的に提供することはできません。 垂直ステップが (10^{-5}) の場合、高さが (10^4) 程度のオブジェクトには、およそ (10^9) 回の反復が必要です。 このような落下が 1 つだけでもすでにコストが高すぎ、(1,000 回) の落下が発生する可能性があります。 

より適切な数値的アプローチは、中心高さの二分探索です。 以前のすべてのオブジェクトについて、2 つのソリッドが提案された高さで交差するかどうかをテストし、約 50 ～ 60 回の二分探索反復を実行できました。 最悪の場合、これはおよそ (60\cdot n(n-1)/2)、つまり (n=1000) に対して約 3,000 万回のペア テストを意味し、すべてのペア テスト自体にかなりの 3 次元交差計算が必要になります。 この定数は、立方体と四面体では特に不快なものになります。 

有益な観察は、2 つの凸固体の最初の接触は常に少数の特徴の組み合わせで表現できるということです。 多面体は、頂点、辺、三角形の面で構成されます。 1 つの多面体が別の多面体に垂直に落ちるとき、最初の接触は、静止面に接触する移動頂点、静止頂点に接触する移動面、または水平投影が交わる 2 つのエッジによって表されます。 球の場合、同様のケースは、点-球、線-球、面-球、および球-球です。 このフィーチャ分解は、この問題の詳細な解決策で使用される標準的な幾何学的構成でもあります。 

それは問題を完全に変えます。 落下する高さを探索する必要はありません。 考えられるすべての接触特徴について、その接触が発生する中心の高さを直接計算し、最大値を取得します。 オブジェクトは (+\infty) から開始して下に移動するため、最大値は最初の接触です。 

三角形の面に当たる点の場合、水平座標は固定されているため、点は平面方程式から得られる固有の高さで面に到達します。 2 つのエッジの場合、それらの水平投影によって接触点が決定され、それらの垂直座標の差によって必要な中心の高さが決まります。 線と球の場合、高さ関数は二次関数の平方根に一次項を加えたもので、凹型であるため最大値が 1 つあります。 数値的な最適化を実行する代わりに、その導関数を明示的に解くことができます。 

したがって、3 次元幾何学は基本公式の固定された集合に還元されます。 公式の詳細なジオメトリの記述では、同じ 10 種類のプリミティブ衝突が説明されており、立方体と四面体の衝突がそれらに帰着できる理由が説明されています。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | 垂直ステップシミュレーション | 必要な精度には制限がない | (O(n)) | 遅すぎる |
 | 身長の二分探索 | (O(n^2\log \varepsilon^{-1})) | (O(n)) | 幾何学的作業が多すぎる |
 | フィーチャベースの衝突 | (O(n^2)) | (O(n)) | 承認済み |

 ## アルゴリズムのチュートリアル

1. すべての立方体または四面体を、回転した頂点、エッジ、および三角形の面で表します。 立方体には、各正方形の面を 2 つの三角形に分割した後、8 つの頂点、12 の辺、および 12 の三角形の面があります。 四面体には 4 つの頂点、6 つの辺、4 つの面があります。 球は中心と半径で表されます。 四面体の座標は、辺の長さを 1 として選択し、入力サイズによってスケーリングできます。 以下で使用される座標は、幾何学的中心を中心としています。 
2. 3 つの入力角度から z-X-z 回転行列を構築し、すべての多面体の頂点を回転します。 回転行列は、オブジェクトを指定された (x,y) 位置に移動する前に適用されます。 結果として得られる (x,y) 座標は落下中に固定されたままになりますが、すべての頂点は同じ未知の垂直移動を受けます。 ここで使用される標準の z-X-z 行列は、詳細なソリューションで説明されているものです。 
3. オブジェクト (i) を処理する前に、地面を使用してその中心の高さを初期化します。 中心に対して最も低い回転頂点または点の座標が (z_{\min}) である場合、接地は中心の高さ (-z_{\min}) で発生します。 球の場合、これは単にその半径です。 
4. 以前のすべてのオブジェクトを障害物の可能性があると考えてください。 2 つの中心間の水平距離がそれらの境界半径の合計よりも大きい場合、それらの水平投影は交わることができないため、ペアはすぐにスキップされます。 これは正確な拒絶テストであり、近似ではありません。 
5. 多面体と多面体のペアの場合、静止面に対する移動頂点、静止頂点に対する移動面、および静止エッジに対する移動エッジをテストします。 頂点と面の接触は、平面方程式と三角形の点のテストから得られます。 面と頂点の接触は、役割を逆にした同じ計算です。 エッジとエッジの接触は、2 つの水平線分の交点から検出されます。 
6. 球と多面体のペアについては、球と頂点、球とエッジ、および球と面の接触をテストします。 エッジケースの場合、エッジに沿った垂直接触高さを最大化します。 面の場合、最初の球面と面の接触は法線方向に沿って発生するため、面の法線を使用します。 
7. 2 つの球の場合、静止球の半径を移動球の半径だけ拡大します。 移動する球は、この拡大された球の上に点が落ちるように動作します。 水平距離を(d)とすると、中心の高さは
 [
 z_{\text{static}}+\sqrt{(r_1+r_2)^2-d^2}。 
】
 8. すべての有効なフィーチャ ペアについて、現在の着陸高さを最大の候補で更新します。 より高い候補は、移動オブジェクトが下向きの動き中にそのフィーチャに早く接触することを意味します。 
9. 着地中心の高さがわかったら、オブジェクトのすべての頂点をその高さだけ移動し、最終的な最高点を保存します。 次のオブジェクトは、このオブジェクトを完全に静的な障害物として認識します。 
10. オブジェクトを時系列順に処理し、各オブジェクトの最も高い最終頂点を印刷します。 以前のオブジェクトが再び移動することはありません。そのため、後のシミュレーションですでに計算された答えを変更することはできません。 

### なぜ効果があるのか

 どの段階でも、現在のオブジェクトは垂直方向の移動のみによって移動します。 前の凸状オブジェクトとの最初の接触を考えてみましょう。 接触点は各ソリッドの境界上にあります。 多面体の場合、境界点は頂点、エッジ、または面に属します。 接触が 2 つの面の内部間で行われる場合、最初の接触時にそれらの支持面が一致し、頂点面の計算で同じ高さが取得されます。 接触にエッジが含まれる場合、2 つの水平エッジ投影が交差し、エッジ間計算によってそれが捕捉されます。 最初の接触に関与する球の表面点は垂直方向または面の法線によって一意に決定されるため、球の接触は対応する点、エッジ、または面のケースに帰着します。

したがって、考えられるすべての最初の接触は、テストされた特徴ペアの 1 つによって表されます。 各フィーチャ ルーチンは、接触が発生する正確な中心高さを計算します。 それらを最大値にすると、可能な限り高い接触高さが得られます。これは、まさに (+\infty) から落下するときに遭遇する最初の衝突となります。 不変条件は、オブジェクト (i) を処理した後、その保存された中心の高さが、すでにフリーズした彫刻に最初に接触する真の高さであり、その保存されたすべてのジオメトリが最終位置にあることです。 

## Python ソリューション```python
import sys
import math

input = sys.stdin.readline

EPS = 1e-9
NEG = -1e100

def cross2(ax, ay, bx, by):
    return ax * by - ay * bx

def inside_triangle(p, a, b, c):
    abx, aby = b[0] - a[0], b[1] - a[1]
    bcx, bcy = c[0] - b[0], c[1] - b[1]
    cax, cay = a[0] - c[0], a[1] - c[1]

    apx, apy = p[0] - a[0], p[1] - a[1]
    bpx, bpy = p[0] - b[0], p[1] - b[1]
    cpx, cpy = p[0] - c[0], p[1] - c[1]

    x1 = cross2(abx, aby, apx, apy)
    x2 = cross2(bcx, bcy, bpx, bpy)
    x3 = cross2(cax, cay, cpx, cpy)

    scale = max(
        1.0,
        math.hypot(abx, aby) * math.hypot(apx, apy),
        math.hypot(bcx, bcy) * math.hypot(bpx, bpy),
        math.hypot(cax, cay) * math.hypot(cpx, cpy),
    )
    tol = EPS * scale

    return (x1 >= -tol and x2 >= -tol and x3 >= -tol) or (
        x1 <= tol and x2 <= tol and x3 <= tol
    )

def plane_of(tri):
    a, b, c = tri
    ux, uy, uz = b[0] - a[0], b[1] - a[1], b[2] - a[2]
    vx, vy, vz = c[0] - a[0], c[1] - a[1], c[2] - a[2]

    nx = uy * vz - uz * vy
    ny = uz * vx - ux * vz
    nz = ux * vy - uy * vx
    d = nx * a[0] + ny * a[1] + nz * a[2]
    return nx, ny, nz, d

def point_plane(moving_p, static_tri):
    a, b, c = static_tri
    if not inside_triangle(moving_p, a, b, c):
        return NEG

    nx, ny, nz, d = plane_of(static_tri)
    if abs(nz) < EPS:
        return NEG

    z = (d - nx * moving_p[0] - ny * moving_p[1]) / nz
    return z - moving_p[2]

def plane_point(moving_tri, static_p):
    if not inside_triangle(static_p, moving_tri[0], moving_tri[1], moving_tri[2]):
        return NEG

    nx, ny, nz, d = plane_of(moving_tri)
    if abs(nz) < EPS:
        return NEG

    z = (d - nx * static_p[0] - ny * static_p[1]) / nz
    return static_p[2] - z

def line_line(moving_l, static_l):
    a, b = moving_l
    c, d = static_l

    rx, ry = b[0] - a[0], b[1] - a[1]
    sx, sy = d[0] - c[0], d[1] - c[1]

    den = cross2(rx, ry, sx, sy)
    qx, qy = c[0] - a[0], c[1] - a[1]

    if abs(den) > EPS:
        t = cross2(qx, qy, sx, sy) / den
        u = cross2(qx, qy, rx, ry) / den

        if t < -EPS or t > 1.0 + EPS or u < -EPS or u > 1.0 + EPS:
            return NEG

        zm = a[2] + t * (b[2] - a[2])
        zs = c[2] + u * (d[2] - c[2])
        return zs - zm

    if abs(cross2(qx, qy, rx, ry)) > EPS:
        return NEG

    rr = rx * rx + ry * ry
    if rr < EPS:
        return NEG

    tc = ((c[0] - a[0]) * rx + (c[1] - a[1]) * ry) / rr
    td = ((d[0] - a[0]) * rx + (d[1] - a[1]) * ry) / rr

    lo = max(0.0, min(tc, td))
    hi = min(1.0, max(tc, td))

    if lo > hi + EPS:
        return NEG

    ans = NEG

    for t in (lo, hi):
        x = a[0] + rx * t
        y = a[1] + ry * t

        ss = sx * sx + sy * sy
        if ss < EPS:
            continue

        u = ((x - c[0]) * sx + (y - c[1]) * sy) / ss
        if u < -EPS or u > 1.0 + EPS:
            continue

        zm = a[2] + t * (b[2] - a[2])
        zs = c[2] + u * (d[2] - c[2])
        ans = max(ans, zs - zm)

    return ans

def point_sphere(moving_p, sphere_center, radius):
    dx = moving_p[0] - sphere_center[0]
    dy = moving_p[1] - sphere_center[1]
    q = radius * radius - dx * dx - dy * dy

    if q < -EPS:
        return NEG

    return sphere_center[2] + math.sqrt(max(0.0, q)) - moving_p[2]

def sphere_point(sphere_center_xy, radius, static_p):
    dx = sphere_center_xy[0] - static_p[0]
    dy = sphere_center_xy[1] - static_p[1]
    q = radius * radius - dx * dx - dy * dy

    if q < -EPS:
        return NEG

    return static_p[2] + math.sqrt(max(0.0, q))

def line_sphere(moving_l, sphere_center, radius, moving_line=True):
    a, b = moving_l

    dx = b[0] - a[0]
    dy = b[1] - a[1]
    dz = b[2] - a[2]

    ax = a[0] - sphere_center[0]
    ay = a[1] - sphere_center[1]

    B = dx * dx + dy * dy

    if B < EPS:
        q = radius * radius - ax * ax - ay * ay
        if q < -EPS:
            return NEG
        top = math.sqrt(max(0.0, q))

        if moving_line:
            return sphere_center[2] + top - a[2]
        return a[2] + top

    t0 = -(ax * dx + ay * dy) / B
    min_d2 = ax * ax + ay * ay - B * t0 * t0

    if min_d2 > radius * radius + EPS:
        return NEG

    D = max(0.0, radius * radius - min_d2)
    width = math.sqrt(D / B)

    left = max(0.0, t0 - width)
    right = min(1.0, t0 + width)

    if left > right + EPS:
        return NEG

    def value(t):
        px = ax + dx * t
        py = ay + dy * t
        q = radius * radius - px * px - py * py
        if q < -EPS:
            return NEG

        root = math.sqrt(max(0.0, q))

        if moving_line:
            return sphere_center[2] + root - (a[2] + dz * t)
        return a[2] + dz * t + root

    ans = max(value(left), value(right))

    if D > EPS:
        if moving_line:
            qlinear = -dz
        else:
            qlinear = dz

        if abs(qlinear) > EPS:
            t = t0 + qlinear * math.sqrt(
                D / (B * (B + qlinear * qlinear))
            )
            if left - EPS <= t <= right + EPS:
                ans = max(ans, value(t))

    return ans

def plane_sphere(moving_tri, sphere_center, radius):
    nx, ny, nz, d = plane_of(moving_tri)
    norm = math.sqrt(nx * nx + ny * ny + nz * nz)

    if norm < EPS:
        return NEG

    ans = NEG

    for sign in (-1.0, 1.0):
        qx = sphere_center[0] + sign * radius * nx / norm
        qy = sphere_center[1] + sign * radius * ny / norm
        qz = sphere_center[2] + sign * radius * nz / norm

        p = (qx, qy, qz)

        if not inside_triangle(p, moving_tri[0], moving_tri[1], moving_tri[2]):
            continue

        nx2, ny2, nz2, d2 = plane_of(moving_tri)
        if abs(nz2) < EPS:
            continue

        plane_z = (d2 - nx2 * qx - ny2 * qy) / nz2
        ans = max(ans, qz - plane_z)

    return ans

def sphere_plane(center_xy, radius, static_tri):
    nx, ny, nz, d = plane_of(static_tri)
    norm = math.sqrt(nx * nx + ny * ny + nz * nz)

    if norm < EPS:
        return NEG

    ans = NEG

    for sign in (-1.0, 1.0):
        qx = center_xy[0] + sign * radius * nx / norm
        qy = center_xy[1] + sign * radius * ny / norm

        if not inside_triangle(
            (qx, qy, 0.0),
            static_tri[0],
            static_tri[1],
            static_tri[2],
        ):
            continue

        if abs(nz) < EPS:
            continue

        plane_z = (d - nx * qx - ny * qy) / nz
        center_z = plane_z - sign * radius * nz / norm
        ans = max(ans, center_z)

    return ans

class Cloud:
    __slots__ = (
        "typ", "x", "y", "r", "rel", "pts", "edges", "faces",
        "bound", "vmax", "minz", "top", "z"
    )

    def __init__(self, typ, alpha, beta, gamma, x, y, r):
        self.typ = typ
        self.x = float(x)
        self.y = float(y)
        self.r = float(r)
        self.z = 0.0

        if typ == 1:
            self.rel = []
            self.pts = []
            self.edges = []
            self.faces = []
            self.bound = float(r)
            self.vmax = float(r)
            self.minz = -float(r)
            self.top = 0.0
            return

        if typ == 0:
            h = 0.5
            base = [
                (-h, -h, -h),
                ( h, -h, -h),
                ( h,  h, -h),
                (-h,  h, -h),
                (-h, -h,  h),
                ( h, -h,  h),
                ( h,  h,  h),
                (-h,  h,  h),
            ]
            self.edges = [
                (0, 1), (1, 2), (2, 3), (3, 0),
                (4, 5), (5, 6), (6, 7), (7, 4),
                (0, 4), (1, 5), (2, 6), (3, 7),
            ]
            self.faces = [
                (0, 1, 2), (0, 2, 3),
                (4, 6, 5), (4, 7, 6),
                (0, 4, 5), (0, 5, 1),
                (1, 5, 6), (1, 6, 2),
                (2, 6, 7), (2, 7, 3),
                (3, 7, 4), (3, 4, 0),
            ]
        else:
            s3 = math.sqrt(3.0)
            s6 = math.sqrt(6.0)
            base = [
                (-0.5 / s3,  0.5, -0.5 / s6),
                (-0.5 / s3, -0.5, -0.5 / s6),
                ( 1.0 / s3,  0.0, -0.5 / s6),
                ( 0.0,       0.0,  1.5 / s6),
            ]
            self.edges = [
                (0, 1), (0, 2), (0, 3),
                (1, 2), (1, 3), (2, 3),
            ]
            self.faces = [
                (0, 1, 2),
                (0, 1, 3),
                (0, 2, 3),
                (1, 2, 3),
            ]

        a = math.radians(alpha)
        b = math.radians(beta)
        g = math.radians(gamma)

        ca, sa = math.cos(a), math.sin(a)
        cb, sb = math.cos(b), math.sin(b)
        cg, sg = math.cos(g), math.sin(g)

        # Active z-X-z rotation.
        m00 = ca * cg - cb * sa * sg
        m01 = -ca * sg - cb * cg * sa
        m02 = sa * sb

        m10 = cg * sa + ca * cb * sg
        m11 = ca * cb * cg - sa * sg
        m12 = -ca * sb

        m20 = sb * sg
        m21 = cg * sb
        m22 = cb

        scale = float(r)
        rel = []

        for px, py, pz in base:
            px *= scale
            py *= scale
            pz *= scale

            rx = m00 * px + m01 * py + m02 * pz
            ry = m10 * px + m11 * py + m12 * pz
            rz = m20 * px + m21 * py + m22 * pz

            rel.append((self.x + rx, self.y + ry, rz))

        self.rel = rel
        self.pts = list(rel)

        self.bound = 0.0
        self.vmax = 0.0
        self.minz = float("inf")

        for p in rel:
            dx = p[0] - self.x
            dy = p[1] - self.y
            self.bound = max(self.bound, math.hypot(dx, dy))
            self.vmax = max(self.vmax, abs(p[2]))
            self.minz = min(self.minz, p[2])

        self.top = self.z + max(p[2] for p in self.pts)

    def set_height(self, z):
        self.z = z
        if self.typ == 1:
            self.top = z + self.r
            return

        self.pts = [
            (p[0], p[1], p[2] + z)
            for p in self.rel
        ]
        self.top = z + max(p[2] for p in self.rel)

def collision(moving, static):
    if moving.typ == 1 and static.typ == 1:
        dx = moving.x - static.x
        dy = moving.y - static.y
        rr = moving.r + static.r
        q = rr * rr - dx * dx - dy * dy
        if q < -EPS:
            return NEG
        return static.z + math.sqrt(max(0.0, q))

    ans = NEG

    if moving.typ == 1:
        sc = (static.x, static.y, static.z)

        for p in static.pts:
            ans = max(
                ans,
                sphere_point((moving.x, moving.y), moving.r, p)
            )

        for i, j in static.edges:
            ans = max(
                ans,
                line_sphere(
                    (static.pts[i], static.pts[j]),
                    (moving.x, moving.y, 0.0),
                    moving.r,
                    moving_line=False,
                )
            )

        for f in static.faces:
            tri = (static.pts[f[0]], static.pts[f[1]], static.pts[f[2]])
            ans = max(ans, sphere_plane((moving.x, moving.y), moving.r, tri))

        return ans

    if static.typ == 1:
        sc = (static.x, static.y, static.z)

        for p in moving.rel:
            ans = max(ans, point_sphere(p, sc, static.r))

        for i, j in moving.edges:
            ans = max(
                ans,
                line_sphere(
                    (moving.rel[i], moving.rel[j]),
                    sc,
                    static.r,
                    moving_line=True,
                )
            )

        for f in moving.faces:
            tri = (
                moving.rel[f[0]],
                moving.rel[f[1]],
                moving.rel[f[2]],
            )
            ans = max(ans, plane_sphere(tri, sc, static.r))

        return ans

    # Polyhedron versus polyhedron.
    for p in moving.rel:
        for f in static.faces:
            tri = (
                static.pts[f[0]],
                static.pts[f[1]],
                static.pts[f[2]],
            )
            ans = max(ans, point_plane(p, tri))

    for f in moving.faces:
        tri = (
            moving.rel[f[0]],
            moving.rel[f[1]],
            moving.rel[f[2]],
        )
        for p in static.pts:
            ans = max(ans, plane_point(tri, p))

    for i, j in moving.edges:
        ml = (moving.rel[i], moving.rel[j])
        for k, l in static.edges:
            sl = (static.pts[k], static.pts[l])
            ans = max(ans, line_line(ml, sl))

    return ans

def solve():
    T = int(input())
    out = []

    for _ in range(T):
        n = int(input())

        clouds = []

        for _ in range(n):
            typ, alpha, beta, gamma, x, y, r = map(int, input().split())

            cur = Cloud(typ, alpha, beta, gamma, x, y, r)

            # Ground contact.
            if typ == 1:
                ground_z = r
            else:
                ground_z = -cur.minz

            best = ground_z

            # Higher static objects are more likely to determine the answer.
            previous = sorted(
                clouds,
                key=lambda c: c.top,
                reverse=True,
            )

            for old in previous:
                # No point of old can force the center above this bound.
                if old.top + cur.vmax <= best + 1e-10:
                    break

                dx = cur.x - old.x
                dy = cur.y - old.y

                if dx * dx + dy * dy > (
                    cur.bound + old.bound
                ) ** 2 + 1e-8:
                    continue

                h = collision(cur, old)
                if h > best:
                    best = h

            cur.set_height(best)
            clouds.append(cur)
            out.append(f"{cur.top:.15f}")

    sys.stdout.write("\n".join(out))

if __name__ == "__main__":
    solve()
```の`Cloud`コンストラクターはまず標準ジオメトリを作成し、次に z-X-z 回転を適用します。 衝突検出中の未知の量はまさに一般的な垂直方向の移動であるため、頂点を中心に対して相対的に保つと便利です。 

多面体の場合、`rel`回転された頂点を要求された (x,y) 変換で保存しますが、中心の高さは 0 です。 物体が着地すると、`set_height`最終的な中心の高さをすべての頂点に追加します。 の`top`フィールドは必要な回答を直接返します。 

3 つの基本的な平面ルーチンは次のとおりです。`point_plane`、`plane_point`、 そして`line_line`。 点と面の接触は、点の水平投影が三角形の面に属する場合にのみ有効です。 その水平位置における平面の (z) 座標は、方程式から直接得られます。 エッジ ルーチンは、通常の交差と同一線上のオーバーラップの両方を処理します。境界での接触は有効な衝突であるため、これは重要です。 

球ルーチンでは、同じ垂直接触のアイデアが使用されます。 球に接触する点は、上半球を落下点として使用し、下半球を落下球として使用します。 線球ルーチンには特に注意が必要です。 (t) でラインをパラメータ化した後、水平距離の 2 乗は (t) で二次関数となるため、垂直接触高さは次の形式になります。 
[
 \sqrt{D-B(t-t_0)^2}+qt+c。 
】
 この関数は、有効区間において凹型です。 その静止点は次のように直接書くことができます。 
[
 t=t_0+q\sqrt{\frac{D}{B(B+q^2)}}。 
】
 その点を間隔のエンドポイントとともにチェックすると、三項検索を行わずに正確な最大値が得られます。 

面球ルーチンは面法線を使用します。 最初の球面と平面の接触では、動径ベクトルは面法線に平行であるため、球の中心を法線に沿って半径 1 つだけ移動して得られる 2 点のみを考慮する必要があります。 これは、詳細なソリューションで説明したのと同じ幾何学的縮小です。 

処理ループには、2 つの正確な枝刈りルールも含まれています。 水平境界半径テストでは、投影が一致しないオブジェクトは拒否されます。 垂直テストは古いオブジェクトのスキャンを一度停止します`old.top + cur.vmax`現在のベストアンサーを超えることはありません。 前のオブジェクトは上部の高さが減少するように処理されるため、後のすべてのオブジェクトはそれより高くなく、拒否される可能性もあります。 

Python は倍精度浮動小数点を使用します。`float`タイプ。 ジオメトリは浮動小数点演算で評価され、入力座標は二乗距離が快適に表現できる程度に十分小さいため、整数のオーバーフローは発生しません。 

## 実用的な例

 ### サンプル 1

 最初のテスト ケースには、立方体とそれに続く球が含まれています。```
2
0 45 90 270 0 0 2
1 11 45 14 0 0 1
```立方体にとって障害となるのは地面だけです。 

| オブジェクト | 形状 | 地面中心高さ | 最高の衝突 | 最終的な中心高さ | 最高点 |
 | --- | --- | --- | --- | --- | --- |
 | 1 | 立方体、側面 2 | 1.000000 | なし | 1.000000 | 2.000000 |
 | 2 | 球、半径 1 | 1.000000 | 3.000000 | 3.000000 | 4.000000 |

 最初の立方体には垂直オフセット (-1) と (1) があるため、その中心は (z=1) で止まります。 その最高点は(2)です。 球の水平位置は立方体の中心と同じです。 立方体の最高点は (2) で、球の半径は (1) であるため、立方体に触れる前に球の中心が (3) に到達する必要があります。 したがって、その最高点は (4) になります。 

これは、オブジェクトの答えは、幾何学的特徴が交差する最小の高さではなく、最大の有効な接触高さであるという不変条件を示しています。 

### サンプル 2

 2 番目のテスト ケースでは、回転した立方体を原点に配置し、非常に大きな球を ((8,9)) に配置します。```
2
0 45 90 0 0 0 2
1 112 345 67 8 9 99
```トレースは次のとおりです。 

| オブジェクト | 形状 | 地面中心高さ | 前のオブジェクトの候補 | 最終的な中心高さ | 最高点 |
 | --- | --- | --- | --- | --- | --- |
 | 1 | 立方体、側面 2 | 2.000000 | なし | 2.000000 | 2.000000 |
 | 2 | 球、半径 99 | 99.000000 | 約100.384662 | 約100.384662 | 約199.384662 |

 最初の立方体の回転垂直範囲は (2) であるため、その最高点は (2) になります。 2 番目のオブジェクトは、水平投影が立方体に達するほど十分に大きいです。 したがって、球の中心は地面ではなく立方体の上で止まります。 その半径を追加すると、報告される最高点はおよそ (199.3846615614) になります。 

この例では、単純な半径のみの垂直近似が不十分である理由も示しています。 前のオブジェクトまでの正確な水平距離により、平方根によって球の接触高さが決まります。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O(n^2)) | すべてのオブジェクトは以前のオブジェクトを考慮し、各ソリッドには一定数の頂点、エッジ、および面のみが含まれます。 
| スペース | (O(n)) | 着陸したすべてのオブジェクトの最終的なジオメトリを保存します。 

名目上の最悪のケースでもすべてのペアが検査されますが、各ペアには一定数の幾何学的特徴チェックのみが含まれます。 水平方向および垂直方向の枝刈りルールにより、特にオブジェクトが分離されている場合、または現在のオブジェクトがすでに高いサポートを見つけている場合、実際の特徴チェックの数が大幅に削減されます。 合計が (n\le1000) であるため、2次外部シミュレーションは 5.5 秒の制限に適しています。 元のコンテスト ページでは、これらの制約が確認されています。 

## テストケース

 次のテストでは、`solve()`上記のソリューションの関数。 浮動小数点出力は、フォーマットされた文字列を比較するのではなく、数値的にチェックされます。```python
# Save the solution above as solution.py before running these tests.

import io
import sys
import math
import solution

def run(inp: str) -> str:
    old_stdin = sys.stdin
    old_stdout = sys.stdout

    sys.stdin = io.StringIO(inp)
    sys.stdout = io.StringIO()

    try:
        solution.solve()
        return sys.stdout.getvalue()
    finally:
        sys.stdin = old_stdin
        sys.stdout = old_stdout

def values(inp: str):
    return [float(x) for x in run(inp).split()]

def assert_close(actual, expected, eps=1e-6):
    assert len(actual) == len(expected)
    for a, b in zip(actual, expected):
        assert abs(a - b) <= eps * max(1.0, abs(b)), (a, b)

# Provided sample.
sample = """\
3
2
0 45 90 270 0 0 2
1 11 45 14 0 0 1
2
0 45 90 0 0 0 2
1 112 345 67 8 9 99
1
2 191 98 10 25 25 2
"""

assert_close(
    values(sample),
    [
        2.000000000000001,
        4.000000000000001,
        2.000000000000001,
        199.384661561446364,
        1.950447343314250,
    ],
    1e-5,
)

# Minimum-size object, a unit sphere on the ground.
assert_close(
    values("""\
1
1
1 0 0 0 0 0 1
"""),
    [2.0],
)

# All equal values, three identical unit spheres stacked vertically.
assert_close(
    values("""\
1
3
1 0 0 0 0 0 1
1 0 0 0 0 0 1
1 0 0 0 0 0 1
"""),
    [2.0, 4.0, 6.0],
)

# Boundary tangency. The second sphere touches the first at exactly
# one point because their centers are two radii apart.
assert_close(
    values("""\
1
2
1 0 0 0 0 0 1
1 0 0 0 2 0 1
"""),
    [2.0, 2.0],
)

# Single tetrahedron with side length 2 and no rotation.
# Its height is 2*sqrt(2/3).
expected_tetra_top = 2.0 * math.sqrt(2.0 / 3.0)

assert_close(
    values("""\
1
1
2 0 0 0 0 0 2
"""),
    [expected_tetra_top],
)

# Maximum n. The spheres are far apart, so every one lands directly
# on the ground. This also exercises the horizontal-distance pruning.
n = 1000
parts = ["1", str(n)]
for i in range(n):
    parts.append(f"1 0 0 0 {3 * i} 0 1")

inp = "\n".join(parts) + "\n"
got = values(inp)

assert len(got) == n
assert all(abs(x - 2.0) < 1e-6 for x in got)
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 3 つのケースのサンプルを提供 |`2, 4, 2, 199.3846615614, 1.9504473433`| 回転、球接触、四面体幾何学、複数の場合 |
 | 1 つの単位球 |`2`| 最小サイズの物体と接地 |
 | 1 つの位置にある 3 つの同一の球 |`2, 4, 6`| 繰り返しのスタッキングと時系列処理 |
 | 距離 2 にある 2 つの単位球 |`2, 2`| 正確な接線と非厳密な交差 |
 | 辺 2 の回転されていない四面体 | (2\sqrt{2/3}) | 四面体の座標と垂直範囲 |
 | 1000 個の分離された単位球 | 1000部`2`| 最大入力サイズと水平方向の枝刈り |

 ## 特殊なケース

 ユニット球体ケース```
1
1
1 0 0 0 0 0 1
```から始まる`best = 1`球の最下点はその中心から半径 1 つ下にあるためです。 前のオブジェクトがないため、最終的な中心の高さは (1) のままで、最高点は (1+1=2) になります。 これは、保存された答えが中心の高さではなく最高点であることを直接チェックします。 

接線球の場合```
1
2
1 0 0 0 0 0 1
1 0 0 0 2 0 1
```最初に球の中心を (z=1) に配置します。 2 番目の球の場合、水平方向の中心距離は正確に (2) で、半径の合計に等しくなります。 球-球の公式には平方根項がゼロであるため、その中心の高さも (1) になります。 その最高点は(2)です。 の使用`<=`公差チェックを通じて、単一点の接線を接触としてカウントできるようになります。 

重ね重ねの場合は、```
1
3
1 0 0 0 0 0 1
1 0 0 0 0 0 1
1 0 0 0 0 0 1
```最初の球の中心高さは (1) です。 2 番目では、高さ (1) に静止した球が表示されるため、その中心は (3) に達します。 3 番目の球は 2 番目の球を関連する最も高い障害物とみなし、中心の高さ (5) に達します。 したがって、それらの最高点は (2,4,6) になります。 垂直枝刈りルールは、順序不変条件も示しています。これは、上位のオブジェクトがすでに答えを決定すると、十分に下位のオブジェクトではそれを改善できないためです。 

回転していない四面体の場合、```
1
1
2 0 0 0 0 0 2
```一番下の頂点は
 [
 -\frac{2}{2\sqrt6}=-\frac1{\sqrt6}、
 】
 したがって、中心は (1/\sqrt6) で止まります。 最も高い頂点は次のとおりです
 [
 \frac{3}{\sqrt6}、
 】
 そして最終的な最高点は
 [
 \frac1{\sqrt6}+\frac3{\sqrt6}
 =\frac4{\sqrt6}
 =2\sqrt{\frac23}
 \約1.6329931619。 
】
 結果は実際の四面体の頂点から得られるため、軸に揃えられた境界ボックスに関する仮定は含まれません。 

最後に、最大サイズのテストでは、中心が 3 単位離れている (1000) 球を使用します。 それらの半径は 1 であるため、隣接する水平投影は接触すらしません。 境界半径テストでは、高価な幾何学的計算の前に、以前のすべてのオブジェクトが拒否されます。 したがって、各球は中心の高さ (1) に着地し、最高点 (2) になります。 これにより、二次記憶構造と、定数係数を管理可能に保つ空間枝刈りの両方が検証されます。
