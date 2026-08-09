---
title: "CF 102471I - ムーン"
description: "単位球上に点 a 1 ,…,a n を固定しました。 新しい点 a 0 が球から均一に選択されます。 すべての n+1 点が 1 つの閉じた半球内に収まるかどうかを尋ねます。"
date: "2026-08-09T04:49:13+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102471
codeforces_index: "I"
codeforces_contest_name: "2019 ICPC Asia-East Continent Final"
rating: 0
weight: 102471
solve_time_s: 347
verified: true
draft: false
---

[CF 102471I - 月](https://codeforces.com/problemset/problem/102471/I)

 **評価:** -
 **タグ:** -
 **解決時間:** 5 分 47 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 単位球上に点 a 1 ,…,a n を固定しました。 新しい点 a 0 が球から均一に選択されます。 すべての n+1 点が 1 つの閉じた半球内に収まるかどうかを尋ねます。 a 0 はランダムであり、f は 0 または 1 であるため、f の期待値は、ランダムな点が許容される確率と正確に一致します。 

有用な幾何学的再定式化は、 0 をしばらく忘れて、固定ベクトルによって生成された円錐を観察することです。 

C={ i ∑ λ i a i :λ i ≥0}。 

点 x は、-x がこの円錐の内部にある場合に正確に失敗します。 その結果、 0 の悪い位置は、固定点の球面凸包の対蹠イメージを形成します。 対蹠マッピングでは領域が保存されるため、

 E[f]=1− 4π area(sconv(a 1 ,…,a n )) .

 したがって、確率の問題は幾何学的問題になります。つまり、固定点の球面凸包の面積を計算します。 

入力は整数の 3 倍 (x、y、z) を与えますが、実際の点は正規化されたベクトルです。 すべての凸包述語を正確に実行できるため、幾何学的方向テスト用の整数トリプルを保持する必要があります。 正規化は、最終的な球面領域を評価する場合にのみ必要になります。 

n≤10 5 の場合、O(n 2 ) アルゴリズムは最悪の場合、およそ 5×10 9 回のペアワイズスケール演算を必要とし、これは 2 秒の制限をはるかに超えています。 O(nlogn) の予想時間幾何学的アルゴリズムが必要です。 標準ルートは、ランダム化された増分 3 次元の凸包であり、その予想される複雑さは固定次元で O(nlogn) です。 

明確な治療に値する変性がいくつかあります。 n=0、1、または 2 の場合、固定点は常に半球内に収まるため、答えは 1 になります。たとえば、次のようになります。```
2
1 0 0
-1 0 0
```答えは 1 です。2 つの対蹠点を正の面積の球面多角形を定義するものとして扱う不注意な解決策は、答えから面積を誤って減算してしまいます。 

すべての固定点が原点を通る 1 つの平面内にある場合、それらの球状の凸包の 2 次元面積はゼロになります。 例えば、```
3
1 0 0
-1 0 0
0 1 0
```答えは 1 です。3 つの点は大円上で大きな円弧を形成しますが、大円弧の表面積はゼロです。 

提供されているサンプルは、逆の場合に便利です。```
3
1 0 0
0 1 0
0 0 1
```球状の凸包は球の 1 八分円であり、その面積は π/2 です。 したがって、悪い確率は (π/2)/(4π)=1/8 となり、7/8 となります。 球面の面積ではなく、通常の平面三角形の面積を計算するソリューションでは、間違った答えが得られます。 

縮退した凸状のハル面も可能です。 複数の入力点が同じ平面上に存在する場合があります。 このような点は、同じ球面多角形を三角形に再分割する以外に球面領域を変更しないため、凸包の実装では、同一平面上の有効な三角形分割を使用できます。 

## アプローチ

 直接的なアプローチでは、固定点を含むすべての可能な半球を特徴付けてから、 0 のどの位置を追加できるかを決定しようとします。 各候補分離平面は複数の入力点によって定義されるため、これはすぐに 2 次になります。 トリプルを列挙すると、すでに Θ(n 3 ) の可能性が得られますが、ペアを列挙すると、n=10 5 で約 5⋅10 9 ペアの Θ(n 2 ) が得られます。 

重要な観察は、球状の凸包の境界のみが重要であるということです。 内部固定点は、円錐によってカバーされる一連の方向を変更できません。 球面凸包の境界は、入力ベクトルの通常の 3 次元凸包の関連する面の放射状投影に正確です。 

したがって、ブルート フォースは、サポートするプレーンを明示的に検出しようとするため機能しますが、考えられるプレーンが多すぎるために失敗します。 凸包は、すべてのサポート平面を線形サイズの面の集合にパッケージ化します。 これらの面がわかれば、答えは球面三角形の面積の合計になります。 

全次元の点セットの場合は、3 次元の凸包を構築します。 すべての船体の面を外側に向けます。 頂点 u、v、w を持つ各面は、これら 3 つの点を大圏の円弧に沿って原点に結ぶことによって球面三角形を定義します。 フェース平面が原点を通過しない場合、その放射状投影はその球面三角形の面積に正確に寄与します。 原点を含む面は 2 次元の球面領域を持たず、何も寄与しません。 

単位ベクトル u、v、w の三角形の場合、その面積を表す数値的に安定した公式は次のとおりです。 

A=2atan2(∣det(u,v,w)∣,1+u⋅v+v⋅w+w⋅u)。 

使用する`atan2`角度を回復するよりも望ましい`acos`、 なぜなら`acos`引数が 1 または −1 に非常に近い場合、精度が失われます。 

立体的な船体は段階的に構築されます。 四面体から始めて、挿入順序をランダム化し、新しいポイントごとに、現在表示されている面の接続されたセットを見つけます。 これらの面がキャップを形成します。 それらの境界は地平線であり、新しい点を地平線のすべての端に接続すると、新しい船体が生成されます。 競合オーナーは、まだ挿入されていないポイントごとに保存されるため、アルゴリズムは船体全体をスキャンしなくても、目に見える面を見つけることができます。 ランダム化された増分凸包構築では、固定次元で O(nlogn) の複雑さが予想されます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | サポートする構成の候補を列挙する | O(n 2 ) 以上 | O(n) | 遅すぎる |
 | ランダム化されたインクリメンタル 3D ハル | 期待される O(nlogn) | O(n) が予想されます | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 整数方向ベクトルを読み取り、元の整数座標を保持します。 正規化された浮動小数点座標は、球面領域の評価のためにのみ後で計算されます。 凸包方向テストが決定要因であり、丸めなしで評価できるため、正確な整数座標は貴重です。 
2. n≤2 を直ちに処理します。 常に最大 2 つの固定点が半球内に収まるので、確率は 1 です。 
3. 固定ベクトルのランクを計算します。 それらが最大 2 次元の空間にわたる場合、正規化後のすべての点は大円上に位置します。 球状の凸包の表面積はゼロなので、答えは 1 です。 
4. n=3 で 3 つのベクトルが線形独立である場合、それらは 1 つの球面三角形を直接定義します。 その面積を次のように計算します。`atan2`式を実行し、1−A/(4π) を返します。 構築する 3 次元多面体はまだありません。 
5. アフィン的に独立した 4 つの入力点を選択し、その四面体の 4 つの面を外側に向けます。 方向述語

 orient(a,b,c,d)=(b−a)⋅((c−a)×(d−a))

 整数で評価されます。 正の場合、d は abc の法線側にあるため、面の向きを反転する必要があります。 

1. 残りのポイントをすべてランダムにシャッフルします。 挿入されていないすべてのポイントについて、現在表示されている 1 つの面を競合オーナーとして保持します。 挿入中にその面が消えると、そのポイントは、表示される新しく作成された面の 1 つに再割り当てされます。 船体面上に正確に位置する点は、既存の境界面を細分化するだけであるため、さらに挿入する必要はありません。 
2. 残りのポイントを 1 つずつ挿入します。 ポイントの競合オーナーから開始して、隣接する面にわたってグラフの走査を実行します。 新しい点が方向付けされた平面の厳密に外側にある場合、面は正確に表示されます。 トラバースでは、接続されているすべての表示面が収集されます。 
3. 表示されている面を削除します。 可視面と非可視面を隔てるすべてのエッジは地平線に属します。 これらの地平線のエッジは、まさに新しい点を接続する必要がある境界です。 
4. 水平線のエッジごとに、そのエッジと挿入された点を含む新しい三角形を作成します。 元の四面体の既知の内部点が厳密に新しいハルの内側に位置するように方向を合わせます。 エッジ マップを介して新しい面を接続します。 
5. 以前の所有者が削除されたポイントの競合所有者を再割り当てします。 削除された可視領域は新しい水平扇形に置き換えられているため、これらの点については新しい面をテストするだけで十分です。 
6. すべての挿入後、残っているすべての船体面を横断します。 3 つの頂点を正規化し、球面三角形の面積を計算します。 これらの領域を合計すると、`spherical_area`。 
7. 球形の包は、まさに 0 の不適切な対蹠位置のセットです。 その面積を 4π で割った値が f=0 である確率です。 したがって出力

 1− 4π 球面領域 。 

### なぜ効果があるのか

 ランダムな点 x の場合、すべての固定点について h⋅a i ≥0 かつ h⋅x≥0 となるベクトル h が存在する場合、すべての固定点と x は 1 つの閉じた半球に正確に収まります。 分離超平面定理によれば、そのような h が存在しない点は、境界までの測度ゼロを持つ固定点の対蹠球面凸包です。 3 次元の凸包には、球面の凸包を定義する支持面が正確に含まれています。 それぞれの非原点の船面を放射状に投影すると 1 つの球面三角形が得られ、これらの投影は内部で重なり合うことなく球形の船体を分割します。 したがって、これらの三角形の領域を合計すると、不良位置の正確な測定値が得られます。 最後の補数は、必要な期待値です。 

## Python ソリューション

 以下の実装では、船体の向きとランダム化された増分船体の構築に正確な整数の決定要因を使用します。 最後の球状領域は、組み合わせ包が決定された後にのみ浮動小数点を使用します。```python
import sys
input = sys.stdin.readline

import math
import random

def cross(a, b):
    return (
        a[1] * b[2] - a[2] * b[1],
        a[2] * b[0] - a[0] * b[2],
        a[0] * b[1] - a[1] * b[0],
    )

def sub(a, b):
    return (
        a[0] - b[0],
        a[1] - b[1],
        a[2] - b[2],
    )

def dot(a, b):
    return a[0] * b[0] + a[1] * b[1] + a[2] * b[2]

def orient(a, b, c, d):
    ab = sub(b, a)
    ac = sub(c, a)
    ad = sub(d, a)
    return dot(cross(ab, ac), ad)

def face_area(p, q, r, nf):
    px, py, pz = p
    qx, qy, qz = q
    rx, ry, rz = r

    np = math.sqrt(px * px + py * py + pz * pz)
    nq = math.sqrt(qx * qx + qy * qy + qz * qz)
    nr = math.sqrt(rx * rx + ry * ry + rz * rz)

    ux, uy, uz = px / np, py / np, pz / np
    vx, vy, vz = qx / nq, qy / nq, qz / nq
    wx, wy, wz = rx / nr, ry / nr, rz / nr

    det = (
        ux * (vy * wz - vz * wy)
        - uy * (vx * wz - vz * wx)
        + uz * (vx * wy - vy * wx)
    )

    uv = ux * vx + uy * vy + uz * vz
    vw = vx * wx + vy * wy + vz * wz
    wu = wx * ux + wy * uy + wz * uz

    den = 1.0 + uv + vw + wu

    return 2.0 * math.atan2(abs(det), den)

def spherical_triangle_area(p, q, r):
    return face_area(p, q, r, None)

def solve_points(points):
    n = len(points)

    if n <= 2:
        return 1.0

    # Find three linearly independent vectors if possible.
    a = points[0]

    i1 = -1
    for i in range(1, n):
        if cross(a, points[i]) != (0, 0, 0):
            i1 = i
            break

    if i1 == -1:
        return 1.0

    b = points[i1]

    i2 = -1
    ab = cross(a, b)
    for i in range(i1 + 1, n):
        if dot(ab, points[i]) != 0:
            i2 = i
            break

    if i2 == -1:
        return 1.0

    c = points[i2]

    # Three fixed points already determine the spherical hull.
    if n == 3:
        area = spherical_triangle_area(a, b, c)
        return 1.0 - area / (4.0 * math.pi)

    # Find a fourth point outside the plane of a,b,c.
    i3 = -1
    for i in range(n):
        if i not in (0, i1, i2) and orient(a, b, c, points[i]) != 0:
            i3 = i
            break

    if i3 == -1:
        return 1.0

    # The centroid of the initial tetrahedron is strictly inside
    # the initial hull and remains inside every later hull.
    center = (
        a[0] + b[0] + c[0] + points[i3][0],
        a[1] + b[1] + c[1] + points[i3][1],
        a[2] + b[2] + c[2] + points[i3][2],
    )

    faces = []
    alive = []
    neigh = []
    buckets = []

    # edge -> (face_id, local_edge_index)
    edge_map = {}

    def edge_key(u, v):
        if u < v:
            return (u, v)
        return (v, u)

    def add_face(u, v, w):
        fid = len(faces)
        faces.append([u, v, w])
        alive.append(True)
        neigh.append([-1, -1, -1])
        buckets.append([])

        for e in range(3):
            x = faces[fid][e]
            y = faces[fid][(e + 1) % 3]
            key = edge_key(x, y)

            old = edge_map.get(key)
            if old is None:
                edge_map[key] = (fid, e)
            else:
                of, oe = old
                neigh[fid][e] = of
                neigh[of][oe] = fid

        return fid

    # Create the four tetrahedron faces.
    ids = [0, i1, i2, i3]

    tetra_faces = [
        (ids[0], ids[1], ids[2], ids[3]),
        (ids[0], ids[3], ids[1], ids[2]),
        (ids[0], ids[2], ids[3], ids[1]),
        (ids[1], ids[3], ids[2], ids[0]),
    ]

    for u, v, w, opposite in tetra_faces:
        if orient(points[u], points[v], points[w], points[opposite]) > 0:
            v, w = w, v
        add_face(u, v, w)

    owner = [-1] * n
    used = [False] * n
    for x in ids:
        used[x] = True

    # Every remaining point on the sphere is outside the tetrahedron,
    # except for coplanar degeneracies which can safely remain on the hull.
    for i in range(n):
        if used[i]:
            continue
        p = points[i]
        found = -1
        for fid in range(len(faces)):
            if orient(
                points[faces[fid][0]],
                points[faces[fid][1]],
                points[faces[fid][2]],
                p,
            ) > 0:
                found = fid
                break
        if found != -1:
            owner[i] = found
            buckets[found].append(i)

    order = [i for i in range(n) if not used[i]]
    random.shuffle(order)

    for pidx in order:
        start = owner[pidx]

        # Degenerate coplanar points need not be inserted.
        if start == -1 or not alive[start]:
            continue

        p = points[pidx]

        visible = set()
        stack = [start]

        while stack:
            fid = stack.pop()
            if fid in visible or not alive[fid]:
                continue

            u, v, w = faces[fid]
            if orient(points[u], points[v], points[w], p) <= 0:
                continue

            visible.add(fid)

            for nb in neigh[fid]:
                if nb != -1 and nb not in visible and alive[nb]:
                    stack.append(nb)

        if not visible:
            continue

        candidates = []
        for fid in visible:
            for q in buckets[fid]:
                if owner[q] == fid and q != pidx:
                    owner[q] = -1
                    candidates.append(q)
            buckets[fid].clear()

        horizon = []

        for fid in visible:
            u, v, w = faces[fid]
            vs = (u, v, w)

            for e in range(3):
                nb = neigh[fid][e]
                if nb not in visible:
                    x = vs[e]
                    y = vs[(e + 1) % 3]

                    nb_edge = -1
                    if nb != -1:
                        nu, nv, nw = faces[nb]
                        nvs = (nu, nv, nw)
                        for ee in range(3):
                            if edge_key(nvs[ee], nvs[(ee + 1) % 3]) == edge_key(x, y):
                                nb_edge = ee
                                break

                    horizon.append((x, y, nb, nb_edge))

        # Remove visible faces from the edge map.
        for fid in visible:
            alive[fid] = False
            u, v, w = faces[fid]
            vs = (u, v, w)

            for e in range(3):
                x = vs[e]
                y = vs[(e + 1) % 3]
                key = edge_key(x, y)
                old = edge_map.get(key)
                if old is not None and old[0] == fid:
                    del edge_map[key]

        new_faces = []

        for x, y, nb, nb_edge in horizon:
            u, v, w = x, y, pidx

            # The initial tetrahedron centroid must remain inside the hull.
            if orient(points[u], points[v], points[w], center) > 0:
                v, u = u, v

            fid = add_face(u, v, w)
            new_faces.append(fid)

            if nb != -1:
                # add_face already linked the two faces through the edge.
                pass

        # Reassign points whose only known visible face disappeared.
        for q in candidates:
            qp = points[q]
            for fid in new_faces:
                u, v, w = faces[fid]
                if orient(points[u], points[v], points[w], qp) > 0:
                    owner[q] = fid
                    buckets[fid].append(q)
                    break

    area = 0.0

    for fid in range(len(faces)):
        if not alive[fid]:
            continue

        u, v, w = faces[fid]
        area += spherical_triangle_area(
            points[u],
            points[v],
            points[w],
        )

    area = min(max(area, 0.0), 4.0 * math.pi)
    return 1.0 - area / (4.0 * math.pi)

def main():
    n = int(input())
    points = [tuple(map(int, input().split())) for _ in range(n)]

    ans = solve_points(points)
    print("{:.12f}".format(ans))

if __name__ == "__main__":
    main()
```の最初の部分`solve_points`ハル コードが開始される前に、すべての低次元のケースを処理します。 これが必要なのは、3 次元の凸包には最初の四面体が必要ですが、3 つの線形に独立した固定ベクトルがすでに完全に有効な球面三角形を定義しているためです。 

の`orient`function は中心的な正確な述語です。 これはスカラー 3 倍積であるため、整数入力を使用すると、点が船体平面のどちら側に位置するかについてのすべての決定が正確になります。 Python の整数には任意の精度があるため、`int64`中間行列式が元の座標よりはるかに大きくなる可能性がある場合でも、オーバーフローします。 

ハルは、各面を隣接する 3 つの面とともに保存します。`edge_map`新しい面が共有エッジを越えて既存の面を一定の予想時間内に見つけられるようにします。 の`buckets`配列は競合構造です。 ポイントには、既知の可視面が 1 つだけ必要です。 その面が消えると、ポイントは新しい地平線のファンに対してテストされ、外側に残っている場合は新しい所有者を受け取ります。 

最初の四面体は、その反対側の頂点を使用して方向付けされます。 後の面は次を使用して方向付けされます。`center`、これは厳密に初期四面体の内側にあります。 以降のすべてのハルには最初の四面体が含まれるため、その点は厳密にハルの内側に残ります。 その結果、新しい面に対してその方向をチェックすることで、信頼できる外側の方向が得られます。 

最後の面積計算では、正規化された浮動小数点ベクトルを意図的に使用します。 ハルの決定はすでに正確に完了しているため、浮動小数点は最終的に出力する必要がある連続量に限定されます。 の`atan2`この式は、次の式で 3 つの球面角度を個別に計算するよりも、非常に小さい球面三角形と非常に大きい球面三角形をより確実に処理します。`acos`。 

## 実用的な例

 ### サンプル 1

 固定点は正の座標軸です。 

| 固定小数点 | 座標 | 幾何学的結果 |
 | --- | --- | --- |
 | 1 | (1,0,0) | 最初の頂点 |
 | 2 | (0,1,0) | 2 番目の頂点 |
 | 3 | (0,0,1) | 3 番目の頂点 |
 | 球面領域 | π/2 | 球の 8 分の 1 |
 | 悪い確率 | 1/8 | 反対の八分儀の 0 |
 | 期待される f | 7/8 | 0.875000000000 |

 3 つの正規化されたベクトルの場合、すべてのペアごとの内積は 0 で、行列式は 1 になります。三角公式は次のようになります。 

2atan2(1,1)= 2 π 。 

球の面積 4π で割ると、1/8 という悪い確率が得られるため、望ましい期待値は 7/8 になります。 

### サンプル 2

 正四面体の 4 つの頂点を考えます。```
4
1 1 1
1 -1 -1
-1 1 -1
-1 -1 1
```原点は凸包の内側にあります。 

| ステップ | 船体の状態 | 球面領域 |
 | --- | --- | --- |
 | 初期四面体 | 4 つの点はすべてハルの頂点です。 4π |
 | 最終トラバース | 4 つの球面が球を分割します。 4π |
 | 悪い確率 | 4π/(4π) | 1 |
 | 期待される f | 補足 | 0 |

 原点からのすべての方向は、4 つの四面体の頂点によって生成される円錐に属します。 したがって、考えられるすべての 0 は不良であり、5 つの点すべてを含む半球はなく、答えは 0 であることを意味します。 

このトレースは、原点が通常の凸包の内側にある場合でも、船体面の球面領域の合計が機能する理由も示しています。 すべての面の放射状の投影は、その内部で球全体を 1 回だけカバーします。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | 期待される O(nlogn) | 固定次元でのランダムな増分凸包と線形サイズの面トラバーサル |
 | スペース | 期待される O(n) | 3 次元ハルには O(n) 個の面とエッジがあります。 

重要な境界は固定された次元です。 3 次元の凸包には O(n) 面しかなく、ランダム化された増分構築では O(nlogn) の作業が予想されます。 入力には 10 5 個の点が含まれているため、これにより、ペアごとまたは三重列挙の二次的な 5×10 9 スケールの作業が回避されます。 Python の任意精度の整数により、正確な方向の述語が安全になりますが、浮動小数点の作業は最終的な面積計算に限定されます。 

## テストケース```python
# helper: run solution on input string, return output string
import sys
import io
import math

# Paste the solve_points function and its helpers from the solution above.

def run(inp: str) -> str:
    data = inp.strip().splitlines()
    n = int(data[0])
    points = [tuple(map(int, line.split())) for line in data[1:]]
    return f"{solve_points(points):.12f}"

# Provided sample
assert abs(float(run("""\
3
1 0 0
0 1 0
0 0 1
""")) - 0.875) < 1e-10, "sample 1"

# Minimum-size input
assert abs(float(run("""\
0
""")) - 1.0) < 1e-10, "n = 0"

# Two antipodal points
assert abs(float(run("""\
2
1 0 0
-1 0 0
""")) - 1.0) < 1e-10, "two antipodal points"

# Three points on one great circle
assert abs(float(run("""\
3
1 0 0
-1 0 0
0 1 0
""")) - 1.0) < 1e-10, "coplanar through origin"

# Four regular-tetrahedron directions, origin strictly inside
assert abs(float(run("""\
4
1 1 1
1 -1 -1
-1 1 -1
-1 -1 1
""")) - 0.0) < 1e-10, "origin inside hull"

# Maximum-size input. All directions lie in z = 0, so the
# spherical convex hull has zero two-dimensional area.
pts = ["100000"]
for i in range(1, 100001):
    pts.append(f"{i} 1 0")

assert abs(float(run("\n".join(pts))) - 1.0) < 1e-10, "maximum n"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`0`|`1.000000000000`| 最小入力サイズ |
 | 2 つの対蹠ベクトル |`1.000000000000`| 境界と対蹠の場合 |
 | 3 つの同一平面上のベクトル |`1.000000000000`| ゼロ面積球形ハル |
 | 正四面体 |`0.000000000000`| 原点は厳密に船体の内側 |
 | 100000 の同一平面上の方向 |`1.000000000000`| 最大入力サイズとリニアハル処理 |

 正規化された固定点が別個であるという問題の約束は、文字通りの繰り返し点が合法ではないことを意味します。 したがって、「すべての値が等しい」テストは有効な入力とはなりません。 異なる整数のトリプルは同じ正規化された方向を持つ可能性がありますが、それらも区別条件によって禁止されています。 

## 特殊なケース

 n=0 の場合、 0 に対する固定の制限はありません。 すべてのランダムな点はそれ自体で半球に配置できるため、アルゴリズムはすぐに 1 を返します。 

2 つの対蹠的固定点の場合、```
2
1 0 0
-1 0 0
```点は多くの半球の境界上にあります。 3 番目の点は、境界に対蹠ペアが含まれる適切な半球を選択することによって常に対応できます。 球形の包には 2 次元の領域がないため、答えは 1 のままです。 

3 つの同一平面上にある点の場合```
3
1 0 0
-1 0 0
0 1 0
```行列式はゼロです。 3 つの点はすべて同じ大円上にあるため、それらの球状の凸包は 1 次元になります。 その表面積はゼロであり、アルゴリズムは四面体の構築を試行せずに 1 を返します。 

サンプルについては、```
3
1 0 0
0 1 0
0 0 1
```行列式は 1、三角公式の分母は 1、球面積は π/2 です。 アルゴリズムは戻ります

 1− 4π π/2 ＝ 8 7 。 

これは、確率的変換の重要な健全性チェックです。 

正四面体の場合、```
4
1 1 1
1 -1 -1
-1 1 -1
-1 -1 1
```原点は厳密に通常の凸包の内側にあります。 原点からのすべての光線は包と交差するため、球状の凸包は球全体になります。 4 つの球面領域を加算すると 4π となり、失敗の確率は 1、期待値は 0 になります。 

最後に、複数の点が同じ支持面上にある場合、正確な方向テストでは、一部の点が可視ではなく同一平面上にあると分類される場合があります。 このような点は、新しい 2 次元の球状領域を作成しません。 既存の船体の面を細分割するだけなので、既存の面上に正確に存在する点を無視しても、合計された球面領域は変わりません。
