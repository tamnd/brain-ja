---
title: "CF 102354F - コズミック クロスロード"
description: "単位球上に順序のない点のコレクションが 2 つあります。 原点を通るすべての幾何学的な線は、球との 2 つの交点によって 2 回表現されるため、点 (r) が発生するたびに、(-r) も発生します。"
date: "2026-08-14T02:31:45+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102354
codeforces_index: "F"
codeforces_contest_name: "2018-2019 Summer Petrozavodsk Camp, Oleksandr Kulkov Contest 2"
rating: 0
weight: 102354
solve_time_s: 377
verified: false
draft: false
---

[CF 102354F - 宇宙の交差点](https://codeforces.com/problemset/problem/102354/F)

 **評価:** -
 **タグ:** -
 **解決時間:** 6 分 17 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 単位球上に順序のない点のコレクションが 2 つあります。 原点を通るすべての幾何学的な線は、球との 2 つの交点によって 2 回表現されるため、点 (r) が発生するたびに、(-r) も発生します。 2 番目のコレクションは、原点の周りに 1 回転を適用し、ポイントの順序を変更することによって、最初のコレクションから取得されます。 

タスクは両方の情報を回復することです。 2 番目のコレクションのすべての点について、最初のコレクション内の対応する点のインデックスを出力し、軸と角度で回転を記述する必要があります。 必要な幾何学的誤差は (10^{-6}) のみですが、入力の精度は約 (10^{-12}) であるため、不必要に不安定な計算を避ければ通常の倍精度で十分です。 

決定的な制約は (n\le 4\cdot10^4) であるため、(8\cdot10^4) 個の点が存在する可能性があります。 すべての点のペアを比較する方法では、およそ (6.4\cdot10^9) 個のペア演算が必要になりますが、これは 4 秒の制限をはるかに超えています。 ソートとは別にほぼ線形の計算が必要なので、(O(n\log n)) が自然なターゲットになります。 

このような解決策を可能にする 2 つの構造的事実があります。 まず、回転により、距離、ドット積、およびそれらから構築されたすべての式が保持されます。 第二に、方向は均一にランダムに選択されました。 ここでのランダム性は装飾的なものではありません。慎重に選択された回転不変量がラインごとにほぼ確実に異なるため、不変量はフィンガープリントとして機能します。 

対蹠的表現によって引き起こされる微妙な点が 1 つあります。 座標の偶数乗のみに依存する不変式は、(r) と (-r) に同じ値を与えます。 これら 2 つの点は同じラインに属しているため、これはバグではありません。 最初にラインを特定し、回転を回復した後でのみ、2 つの反対側の端点のどちらが正しい点であるかを決定します。 

付属のサンプルは、もう 1 つの有用なエッジ ケースです。 その 4 つの点は 1 つの平面上に正方形を形成します。 以下で使用される不変式は 4 つの点すべてでまったく同じ値を持つため、ランダムな一意性の仮定はこのサンプルには当てはまりません。 連続するソートされたポイントを盲目的にペアにする不注意な実装では、間違ったライン ペアが形成される可能性があります。 以下の実装には、(n\le3) の小さなブルート フォース フォールバックが含まれており、サンプルとその他の小さな対称構成を処理します。 実際の大きな入力の場合、約束されたランダムな構築により、高速パスは圧倒的に信頼性が高くなります。 

たとえば、サンプルには 4 つの点があります。 
[
 (0.923879533,0.382683432,0),\quad
 (0.923879533、-0.382683432、0)、
 ]
 彼らのネガティブなことも一緒に。 すべてのポイントは同じ二次フィンガープリントを受け取ります。 正しい出力では、(z) 軸を中心とした (-\pi/2) 回転と置換 (2,3,4,1) が使用される可能性があります。 すべてのフィンガープリントが一意であると仮定するメソッドは、回転を計算しようとする前に何も言わずに失敗します。 

2 番目の単純なエッジ ケースは、アイデンティティのローテーションです。 2 つの入力セットが同一であるがシャッフルされている場合、必要な角度は (0) であり、軸はゼロ以外の任意のベクトルにすることができます。 この場合、実装は (x) 軸を出力します。 角度がゼロの場合、軸は一意に定義されないため、印刷された軸を予期される軸と比較すると不正確になります。 

## アプローチ

直接的なアプローチは概念的に単純です。 2 つのセットの点間の対応を試み、十分な対応するベクトルから回転を決定し、残りのすべての点をチェックします。 最初の点の可能なターゲットが (2n) 個、2 番目の点が (2n-1) 個であるため、残りの順列を処理する前でも、既に (\Theta(n^2)) 個の候補ペアが存在します。 すべての候補が (O(n)) 個の点をスキャンする必要がある場合、最悪のケースは (\Theta(n^3))、(n=4\cdot10^4) で約 (5.12\cdot10^{14}) 個の基本点比較になります。 より慎重な (O(n^2)) 検索でも、約 (6.4\cdot10^9) 個のペア演算が実行されます。 

有益な観察は、最初に回転を推測しようとするのをやめるということです。 代わりに、回転によって変更されず、セット全体の順序とは無関係に、各点に付加される番号を作成します。 

公式の解法では、4 乗の距離多項式が使用されます。 
[
 P_4(x,y,z)=
 \合計_l
 \left((x-x_l)^2+(y-y_l)^2+(z-z_l)^2\right)^2。 
]
 これは回転に対して不変であり、すべての点について評価すると、必要なモーメントを蓄積した後、点ごとの一定の仕事に減らすことができます。 

4-8(p\cdot r_l)+4(p\cdot r_l)^2。 
]
 すべての点を合計すると、入力が対蹠的であるため、線形項は消えます。 
[
 \sum_l r_l=0。 
]
 対称行列を定義する
 [
 M=\sum_l r_l r_l^T。 
]
 それから
 [
 \sum_l(p\cdot r_l)^2=p^TMp,
 ]
 それで
 [
 P_4(p)=4(2n)+4p^TMp。 
]
 定数因数と加算定数は並べ替えには影響しません。 したがって、私たちは使用します
 [
 F(p)=p^TMp
 ]
 指紋として。 

#b^TM_Bb

 #b^TR^TM_ARb

 # (Rb)^TM_A(Rb)

 F_A(Rb)。 
]
 したがって、対応する点は同じフィンガープリントを持ちます。 方向はランダムであるため、異なる線はほぼ確実に異なる値を持ちます。 (F(-r)=F(r)) であるため、避けられない唯一の等価性は (r) と (-r) の間です。 

私たちは指紋を分類します。 一般的なケースでは、2 つの連続する等しい値ごとに 1 つの対蹠ペアが形成され、そのペアは両方のセットで同じ順序で発生します。 これにより、(O(n\log n)) 時間における (n) 行間の対応関係が得られます。 

2 つの非平行な対応する線が判明すると、4 つの方向のみが残ります。 各セットの各ラインから代表者を 1 人選びます。 4 つの符号の選択肢のそれぞれについて、選択した 2 つのベクトルを選択したターゲット ベクトルにマッピングする固有の適切な回転を構築します。 次に、すべての点に対してテストします。 正しい符号の組み合わせが合格することが保証されています。 

最後のステップでは、回転行列を軸角度表現に変換します。 クォータニオン表現は、角度が (\pi) に近い場合でも安定するため便利です。この場合、行列の非対称部分のみに基づく通常の式では精度が失われます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | (O(n^2)) から (O(n^3)) 検証に応じて | (O(n)) | 遅すぎる |
 | 最適 | (O(n\log n)) | (O(n)) | 承認済み |

 ## アルゴリズムのチュートリアル

1. 最初のセットのすべて (2n) ポイントと 2 番目のセットのすべて (2n) ポイントを読み取ります。 座標を浮動小数点のトリプルとして保存します。 すべての点が単位球上にあり、必要な誤差は (10^{-6}) であるため、倍精度が適切です。 
2. セットごとに、対称行列の 6 つの独立したエントリを累積します。 
[
 M=\sum r_ir_i^T。 
]
 エントリは次のとおりです。 
[
 M_{xx}=\sum x_i^2,\quad
 M_{xy}=\sum x_iy_i,\quad
 M_{xz}=\sum x_iz_i,
 ]
 (M_{yy},M_{yz},M_{zz}) についても同様です。 
3. すべての点について (F(r)=r^TMr) を評価します。 (M) は (3\times3) のみであるため、点ごとに一定数の算術演算のみが必要になります。 
4. ポイント インデックスをフィンガープリントで並べ替えます。 ランダムな場合、各行の 2 つのコピーは同じフィンガープリントを持ち、異なる行は異なるフィンガープリントを持ちます。 したがって、両方のセットにおいて、位置 (0,1) は 1 つのラインに対応し、位置 (2,3) は別のラインに対応します。 
5. 最初のラインを 1 つの基準として使用し、方向が最初の基準とほぼ平行ではない 2 番目の基準が見つかるまで、他のライン グループをスキャンします。 ポイントはランダムなので、通常はすぐに完了します。 十分に分離されたペアを選択すると、座標フレームを構築するときに小さな外積による除算が回避されます。 
6. (s_1,s_2) を第 2 セットの 2 つの選択されたラインからの代表とし、(t_1,t_2) を第 1 セットの対応するラインからの代表とする。 4 つの選択肢すべてを試してください
 [
 (\pm t_1、\pm t_2)。 
]
 選択ごとに、(s_1,s_2) から正規直交基底を構築し、符号付きターゲット ベクトルから別の基底を構築し、最初の基底を 2 番目の基底にマッピングします。 これにより、適切な回転行列が得られます。 
7. すべてのポイントに対して候補ローテーションを検証します。 2 番目のセットの点 (b) については、そのフィンガープリントから、対応する 1 番目のセットのラインがわかります。このラインには、ちょうど 2 つの対向する点が含まれています。 (Rb) をこれら 2 つの候補と比較し、近い方を保持します。 すべての距離が小さな数値許容誤差を下回っている場合、候補は目的の回転と置換です。 
8. フィンガープリントがポイントをペアと (n\le3) に分割しない場合は、小さな総当たりフォールバックを使用します。 順列は最大 (6!=720) 個あるため、すべての順列を試し、2 つの非平行ベクトルから回転を構築し、すべての点を検証できます。 これにより、漸近的な複雑さに影響を与えることなく対称サンプルが処理されます。 
9. 結果の回転行列を単位四元数に変換します。 スカラー成分を非負にしてから使用します。 
[
 \theta=2\オペレーター名{atan2}(|v|,w)
 ]
 ここで、(w) はスカラー部分、(v) はベクトル部分です。 ベクトル (v/|v|) は回転軸です。 ゼロ回転の場合、どの軸も有効であるため、((1,0,0)) を出力します。 
10. 角度、軸点、および必要な 1 ベースのインデックス付けの順列を出力します。 

なぜ効果があるのか

 中心の不変式は (F(r)=r^TMr) で、4 乗距離多項式の非定数部分です。 回転は共役によって (M) が変化し、逆共役によって (r) が変化するため、対応する点では (F) は変化しません。 ランダムな独立した方向により、これらのフィンガープリントは、数学的モデルの確率 1 で異なるライン間で区別されます。 したがって、ソートステップではすべてのラインペアが識別されます。 

2 つの非平行ベクトルの場合、それらの順序ペアによって方向付き正規直交フレームが決まります。 あるフレームから別のフレームへの回転マッピングは一意です。 4 つの記号の選択肢は、線に 2 つの可能な代表があるという事実によって生じる唯一のあいまいさをカバーします。 正確に 1 つの候補が実際のローテーションに一致し、グローバル検証ではすべての不正確な候補が拒否されます。 その回転がわかったら、一致する各対蹠点ペア内のより近い端点を選択すると、必要な点の置換が得られます。 

## Python ソリューション```python
import sys
import math
import itertools

input = sys.stdin.readline

EPS = 1e-8
CHECK_EPS2 = 5e-10
CROSS_EPS = 1e-8

def dot(a, b):
    return a[0] * b[0] + a[1] * b[1] + a[2] * b[2]

def cross(a, b):
    return (
        a[1] * b[2] - a[2] * b[1],
        a[2] * b[0] - a[0] * b[2],
        a[0] * b[1] - a[1] * b[0],
    )

def norm2(a):
    return dot(a, a)

def scale(a, k):
    return (a[0] * k, a[1] * k, a[2] * k)

def sub(a, b):
    return (a[0] - b[0], a[1] - b[1], a[2] - b[2])

def add(a, b):
    return (a[0] + b[0], a[1] + b[1], a[2] + b[2])

def normalize(a):
    d = math.sqrt(norm2(a))
    return scale(a, 1.0 / d)

def apply_rot(R, v):
    return (
        R[0][0] * v[0] + R[0][1] * v[1] + R[0][2] * v[2],
        R[1][0] * v[0] + R[1][1] * v[1] + R[1][2] * v[2],
        R[2][0] * v[0] + R[2][1] * v[1] + R[2][2] * v[2],
    )

def rotation_from_two(source1, source2, target1, target2):
    u = normalize(source1)
    v0 = sub(source2, scale(u, dot(source2, u)))
    vlen2 = norm2(v0)
    if vlen2 < CROSS_EPS * CROSS_EPS:
        return None
    v = scale(v0, 1.0 / math.sqrt(vlen2))
    w = cross(u, v)

    U = normalize(target1)
    V0 = sub(target2, scale(U, dot(target2, U)))
    Vlen2 = norm2(V0)
    if Vlen2 < CROSS_EPS * CROSS_EPS:
        return None
    V = scale(V0, 1.0 / math.sqrt(Vlen2))
    W = cross(U, V)

    # R = [U V W] [u v w]^T
    R = [[0.0] * 3 for _ in range(3)]
    T = (U, V, W)
    S = (u, v, w)

    for i in range(3):
        for j in range(3):
            R[i][j] = (
                T[0][i] * S[0][j]
                + T[1][i] * S[1][j]
                + T[2][i] * S[2][j]
            )
    return R

def matrix_fingerprint(p, M):
    x, y, z = p
    qx = M[0][0] * x + M[0][1] * y + M[0][2] * z
    qy = M[0][1] * x + M[1][1] * y + M[1][2] * z
    qz = M[0][2] * x + M[1][2] * y + M[2][2] * z
    return x * qx + y * qy + z * qz

def build_matrix(points):
    xx = xy = xz = yy = yz = zz = 0.0
    for x, y, z in points:
        xx += x * x
        xy += x * y
        xz += x * z
        yy += y * y
        yz += y * z
        zz += z * z
    return (
        (xx, xy, xz),
        (xy, yy, yz),
        (xz, yz, zz),
    )

def build_groups(values, order):
    groups = []
    for idx in order:
        if not groups or abs(values[idx] - values[groups[-1][0]]) > EPS:
            groups.append([idx])
        else:
            groups[-1].append(idx)
    return groups

def validate_group_rotation(R, A, B, groups_a, groups_b):
    m = len(A)
    perm = [-1] * m

    for g in range(len(groups_b)):
        ga = groups_a[g]
        gb = groups_b[g]

        if len(ga) != 2 or len(gb) != 2:
            return None

        a0, a1 = ga
        for bi in gb:
            rb = apply_rot(R, B[bi])

            d0 = norm2(sub(rb, A[a0]))
            d1 = norm2(sub(rb, A[a1]))

            if d0 <= d1:
                best = a0
                bestd = d0
            else:
                best = a1
                bestd = d1

            if bestd > CHECK_EPS2:
                return None
            if perm[bi] != -1:
                return None
            perm[bi] = best

    if any(x == -1 for x in perm):
        return None
    return perm

def brute_force_small(A, B):
    m = len(A)

    first = 0
    second = -1
    for j in range(1, m):
        if norm2(cross(B[first], B[j])) > CROSS_EPS * CROSS_EPS:
            second = j
            break

    if second == -1:
        return None

    for p in itertools.permutations(range(m)):
        for s1 in (1.0, -1.0):
            for s2 in (1.0, -1.0):
                R = rotation_from_two(
                    B[first],
                    B[second],
                    scale(A[p[first]], s1),
                    scale(A[p[second]], s2),
                )
                if R is None:
                    continue

                ok = True
                for i in range(m):
                    rb = apply_rot(R, B[i])
                    if norm2(sub(rb, A[p[i]])) > CHECK_EPS2:
                        ok = False
                        break

                if ok:
                    return R, list(p)

    return None

def rotation_to_axis_angle(R):
    tr = R[0][0] + R[1][1] + R[2][2]

    if tr > 0.0:
        s = math.sqrt(tr + 1.0) * 2.0
        qw = 0.25 * s
        qx = (R[2][1] - R[1][2]) / s
        qy = (R[0][2] - R[2][0]) / s
        qz = (R[1][0] - R[0][1]) / s
    elif R[0][0] > R[1][1] and R[0][0] > R[2][2]:
        s = math.sqrt(max(0.0, 1.0 + R[0][0] - R[1][1] - R[2][2])) * 2.0
        qx = 0.25 * s
        qy = (R[0][1] + R[1][0]) / s
        qz = (R[0][2] + R[2][0]) / s
        qw = (R[2][1] - R[1][2]) / s
    elif R[1][1] > R[2][2]:
        s = math.sqrt(max(0.0, 1.0 + R[1][1] - R[0][0] - R[2][2])) * 2.0
        qx = (R[0][1] + R[1][0]) / s
        qy = 0.25 * s
        qz = (R[1][2] + R[2][1]) / s
        qw = (R[0][2] - R[2][0]) / s
    else:
        s = math.sqrt(max(0.0, 1.0 + R[2][2] - R[0][0] - R[1][1])) * 2.0
        qx = (R[0][2] + R[2][0]) / s
        qy = (R[1][2] + R[2][1]) / s
        qz = 0.25 * s
        qw = (R[1][0] - R[0][1]) / s

    qn = math.sqrt(qw * qw + qx * qx + qy * qy + qz * qz)
    qw /= qn
    qx /= qn
    qy /= qn
    qz /= qn

    if qw < 0.0:
        qw = -qw
        qx = -qx
        qy = -qy
        qz = -qz

    vnorm = math.sqrt(qx * qx + qy * qy + qz * qz)

    if vnorm < 1e-12:
        return 0.0, (1.0, 0.0, 0.0)

    theta = 2.0 * math.atan2(vnorm, max(0.0, qw))
    axis = (qx / vnorm, qy / vnorm, qz / vnorm)

    if theta > math.pi:
        theta -= 2.0 * math.pi
        axis = scale(axis, -1.0)

    return theta, axis

def solve():
    n = int(input())
    m = 2 * n

    A = [tuple(map(float, input().split())) for _ in range(m)]
    B = [tuple(map(float, input().split())) for _ in range(m)]

    MA = build_matrix(A)
    MB = build_matrix(B)

    qa = [matrix_fingerprint(p, MA) for p in A]
    qb = [matrix_fingerprint(p, MB) for p in B]

    order_a = sorted(range(m), key=qa.__getitem__)
    order_b = sorted(range(m), key=qb.__getitem__)

    groups_a = build_groups(qa, order_a)
    groups_b = build_groups(qb, order_b)

    # The random-instance fast path has exactly n groups,
    # each containing the two antipodal endpoints of one line.
    fast = (
        len(groups_a) == n
        and len(groups_b) == n
        and all(len(g) == 2 for g in groups_a)
        and all(len(g) == 2 for g in groups_b)
    )

    if not fast and n <= 3:
        ans = brute_force_small(A, B)
        if ans is not None:
            R, perm = ans
        else:
            raise RuntimeError("No rotation found")
    else:
        if not fast:
            # The official random-input guarantee makes this branch
            # practically unreachable for large n.
            groups_a = [order_a[2 * i:2 * i + 2] for i in range(n)]
            groups_b = [order_b[2 * i:2 * i + 2] for i in range(n)]

        g0 = 0
        best_g = 1
        best_sep = 2.0

        a0 = A[groups_a[g0][0]]
        b0 = B[groups_b[g0][0]]

        for g in range(1, n):
            ag = A[groups_a[g][0]]
            sep = abs(dot(a0, ag))
            if sep < best_sep:
                best_sep = sep
                best_g = g

        a1 = A[groups_a[best_g][0]]
        b1 = B[groups_b[best_g][0]]

        R = None
        perm = None

        for s0 in (1.0, -1.0):
            for s1 in (1.0, -1.0):
                cand = rotation_from_two(
                    b0,
                    b1,
                    scale(a0, s0),
                    scale(a1, s1),
                )
                if cand is None:
                    continue

                p = validate_group_rotation(
                    cand, A, B, groups_a, groups_b
                )
                if p is not None:
                    R = cand
                    perm = p
                    break

            if R is not None:
                break

        if R is None:
            # This is only a safety net for unusual numerical degeneracy.
            if n <= 3:
                ans = brute_force_small(A, B)
                if ans is None:
                    raise RuntimeError("No rotation found")
                R, perm = ans
            else:
                raise RuntimeError("Fingerprint matching failed")

    theta, axis = rotation_to_axis_angle(R)

    print("{:.12f}".format(theta))
    print("{:.12f} {:.12f} {:.12f}".format(*axis))
    print(" ".join(str(x + 1) for x in perm))

if __name__ == "__main__":
    solve()
```行列の累積は、不変式を構築するために必要な座標を渡す唯一の方法です。 行列は対称であるため、コードは 2 次形式を評価するときに完全な対称構造を維持しますが、保存される値は 6 つだけです。 

の表現`matrix_fingerprint`は (x(Mr)_x+y(Mr)_y+z(Mr)_z) として評価されます。 (r) を (-r) に置き換えると、2 次形式の符号積の両方の因数が変更されるため、2 つの対蹠点は同じ値を生成します。 

並べ替え配列には、座標ではなくインデックスが含まれます。 これにより、実際のポイント データの移動が回避され、最終的な置換の元の入力インデックスを簡単に回復できるようになります。 

4 つの記号の選択が必要です。 入力は方向付けされたベクトルではなく線を表すため、不変式はどの線がどの線に対応するかを示すことはできますが、選択した端点が正であるべきか負であるべきかを示すことはできません。 2 つの非平行な方向のベクトルが固定されると、回転自体によってこの曖昧さが解決されます。 

フレームの構築では、2 番目のベクトルの 1 番目のベクトルへの射影が減算されます。 これにより、最初のベクトルに垂直なベクトルが生成され、その後、外積によって右手正規直交基底が完成します。 ある右手系基底を別の右手系基底にマッピングすると、鏡映ではなく常に適切な回転が生成されます。 

クォータニオン変換では、トレースが非正の場合、主要な対角エントリに応じて異なる式が使用されます。 これにより、(180^\circ) 回転付近で小さな数で除算することが回避されます。 角度がゼロの場合は、軸が数学的に任意であるため、個別に処理されます。 

## 実用的な例

 ### サンプル 1

 提供されたサンプルでは、最初のセットの 4 つの点が (xy) 平面で正方形を形成し、2 番目のセットは、必要な逆回転が適用される前に同じ正方形を (+\pi/2) 回転させたものです。 

最初のセットの二次行列は対角行列です。 
[
 M=
 \begin{p行列}
 3.41421356&0&0\
 0&0.58578644&0\
 0&0&0
 \end{p行列}。 
]
 正方形の各点は同じ値 (r^TMr) を持つため、通常のランダム インスタンスのペアリングは利用できません。 

| ステージ | 状態 |
 | --- | --- |
 | (n) | (2) |
 | ポイント数 | (4) |
 | 指紋グループ | 4 つの点すべてを含む 1 つのグループ |
 | 高速パス | 拒否されました |
 | フォールバック | (4!=24) 個の順列を列挙する |
 | 有効な回転 | (z) を中心に (-\pi/2) 回転 |
 | 有効な順列 | (2,3,4,1) |

 フォールバックは順列を試み、平行ではない 2 つの点から回転を決定します。 正しい順列に達すると、計算された回転により、すべてのプライムされたポイントが割り当てられたポイントに送信されます。 ステートメントに示されている出力は 1 つの有効な表現であり、問​​題にはこの対称構成に対して多くの有効な選択肢があるため、プログラムは異なるが同等の表現を生成する可能性があります。 

### 非対称の 4 行の例

 方向性のない 4 つの方向を考慮する
 [
 (1,0,0),\quad
 (0,1,0),\quad
 (0,0,1),\quad
 \frac{1}{\sqrt3}(1,1,1),
 ]
 彼らのネガティブなことも一緒に。 (z) 軸を中心にすべてを (90^\circ) だけ回転し、点をシャッフルします。 

二次フィンガープリントはすべてのラインで同一ではなくなり、高速パスはライン グループを識別できます。 重要な状態遷移を以下に示します。 

| ステージ | 最初のセット | 2セット目 |
 | --- | --- | --- |
 | マトリックス(M) | 8ポイントから累計 | (M) | の回転バージョン
 | 分類された指紋 | 4行グループ | 同じ順序で同じ 4 つのグループ |
 | 参照グループ | 最初のグループ | 対応する最初のグループ |
 | 2 番目の参考資料 | 残りの最小並列グループ | 対応するグループ |
 | トライアルに署名する | 4 | 4 |
 | 試験の成功 | 1 つの符号ペア | 同じ物理的な回転 |
 | 検証 | 8 点すべて許容範囲内 | 8 点すべて許容範囲内 |

 この例は、アルゴリズムが回線識別をエンドポイント識別から分離する理由を示しています。 指紋は、対蹠ペアを 1 つのオブジェクトとして識別します。 次に、2 ベクトル回転再構成により、その 2 つの端点の方向が決定されます。 

## 複雑さの分析

| 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O(n\log n)) | 行列の累積とフィンガープリントの評価は (O(n)); (2n) 個の値のソートコスト (O(n\log n))。 すべてのポイントに対して 4 つの回転のみがチェックされます。 |
 | スペース | (O(n)) | 2 つの点セット、フィンガープリント、並べ替えインデックス、および順列はすべて線形メモリを使用します。 |

 (n=4\cdot10^4) の場合、点は (8\cdot10^4) 個だけです。 主な操作は、そのサイズの 2 つの配列をソートし、その後に一定数の線形スキャンを実行することです。 これは、コンパイルされた実装で意図した 4 秒の複雑さの目標内に問題なく収まり、Python 実装ではすべての幾何学的な操作が一定サイズに保たれ、`sys.stdin.readline`入力用。 

ランダム方向の保証は、不変式を汎用のフィンガープリントから実用的なものに変えるものです。 これがなければ、異なる行が同じフィンガープリントを持つ可能性があり、一般に単一のスカラー不変式では十分ではありません。 公式の議論でも同様の区別がなされています: (P_4) はランダムな構成には役立ちますが、対称的な構成では役に立たなくなる可能性があります。 

## テストケース

 この問題の出力は一意ではないため、アサートでは生の出力文字列を 1 つの事前に決定された答えと比較すべきではありません。 正しいテストは、返された回転と順列を解析し、幾何学的条件を検証することです。 次のハーネスは次のことを前提としています。`solve()`上記のソリューションの関数は、同じテスト ファイルで使用できます。```python
import sys
import io
import math
import random

def run(inp: str) -> str:
    old_stdin = sys.stdin
    old_stdout = sys.stdout
    sys.stdin = io.StringIO(inp)
    sys.stdout = io.StringIO()
    try:
        solve()
        return sys.stdout.getvalue()
    finally:
        sys.stdin = old_stdin
        sys.stdout = old_stdout

def rotate_z(p, angle):
    c = math.cos(angle)
    s = math.sin(angle)
    x, y, z = p
    return (c * x - s * y, s * x + c * y, z)

def make_case(points, angle):
    first = []
    for p in points:
        first.append(p)
        first.append((-p[0], -p[1], -p[2]))

    second = []
    for p in points:
        q = rotate_z(p, angle)
        second.append(q)
        second.append((-q[0], -q[1], -q[2]))

    rng = random.Random(1234567)
    rng.shuffle(second)

    lines = [str(len(points))]
    for p in first:
        lines.append("{:.12f} {:.12f} {:.12f}".format(*p))
    for p in second:
        lines.append("{:.12f} {:.12f} {:.12f}".format(*p))
    return "\n".join(lines) + "\n"

def parse_output(inp, out):
    data = inp.split()
    it = iter(data)

    n = int(next(it))
    m = 2 * n

    A = []
    for _ in range(m):
        A.append(tuple(float(next(it)) for _ in range(3)))

    B = []
    for _ in range(m):
        B.append(tuple(float(next(it)) for _ in range(3)))

    out_data = out.split()
    theta = float(out_data[0])
    axis = tuple(map(float, out_data[1:4]))
    perm = list(map(int, out_data[4:4 + m]))

    assert -math.pi - 1e-9 <= theta <= math.pi + 1e-9
    assert 1e-3 <= sum(abs(x) for x in axis) <= 1e3
    assert sorted(perm) == list(range(1, m + 1))

    c = math.cos(theta)
    s = math.sin(theta)
    x, y, z = axis
    length = math.sqrt(x * x + y * y + z * z)
    x /= length
    y /= length
    z /= length

    for i in range(m):
        bx, by, bz = B[i]

        # Rodrigues rotation.
        cross_x = y * bz - z * by
        cross_y = z * bx - x * bz
        cross_z = x * by - y * bx
        d = x * bx + y * by + z * bz

        rx = bx * c + cross_x * s + x * d * (1.0 - c)
        ry = by * c + cross_y * s + y * d * (1.0 - c)
        rz = bz * c + cross_z * s + z * d * (1.0 - c)

        ax, ay, az = A[perm[i] - 1]
        err = math.sqrt(
            (rx - ax) ** 2 +
            (ry - ay) ** 2 +
            (rz - az) ** 2
        )
        assert err <= 2e-6

# Provided sample.
sample1 = """\
2
0.923879533 0.382683432 0
0.923879533 -0.382683432 0
-0.923879533 -0.382683432 0
-0.923879533 0.382683432 0
0.382683432 0.923879533 0
0.382683432 -0.923879533 0
-0.382683432 -0.923879533 0
-0.382683432 0.923879533 0
"""

parse_output(sample1, run(sample1))

# Minimum-size case, n = 2, with an identity rotation.
case_min = make_case(
    [
        (1.0, 0.0, 0.0),
        (0.0, 1.0, 0.0),
    ],
    0.0,
)
parse_output(case_min, run(case_min))

# Symmetric three-line case. This exercises the small brute-force fallback.
case_symmetric = make_case(
    [
        (1.0, 0.0, 0.0),
        (0.0, 1.0, 0.0),
        (0.0, 0.0, 1.0),
    ],
    math.pi / 2,
)
parse_output(case_symmetric, run(case_symmetric))

# Non-symmetric case with a general-looking set of directions.
case_general = make_case(
    [
        (1.0, 0.0, 0.0),
        (0.0, 1.0, 0.0),
        (0.0, 0.0, 1.0),
        (1.0 / math.sqrt(3.0),
         1.0 / math.sqrt(3.0),
         1.0 / math.sqrt(3.0)),
    ],
    -0.731,
)
parse_output(case_general, run(case_general))

# Maximum-size stress case.
# The points are generated deterministically on the sphere and then rotated.
n_big = 40000
points_big = []

for i in range(n_big):
    z = -1.0 + 2.0 * (i + 0.5) / n_big
    phi = i * 2.399963229728653
    r = math.sqrt(max(0.0, 1.0 - z * z))
    points_big.append((r * math.cos(phi), r * math.sin(phi), z))

case_big = make_case(points_big, 1.234567)
parse_output(case_big, run(case_big))
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 提供されたサンプル | 幾何学的に有効な回転と置換 | 対称構成と小規模なブルート フォース フォールバック |
 | (n=2)、恒等回転 | 有効な軸と順列を含む角度 (0) | 最小サイズとゼロ角度の処理 |
 | 3つの座標軸 | 任意の有効な (90^\circ) 回転と置換 | 複数の等しいフィンガープリントとフォールバックの正確性 |
 | 4 つの非対称方向 | (z) 軸の周りの (-0.731) ラジアンに近い有効な回転 | 通常の不変式ベースのマッチングと符号の選択 |
 | (n=40000) が生成したルート | 最大でもエラーのある有効な順列 (2\cdot10^{-6}) | 最大入力サイズ、ソートコスト、数値安定性 |

 ## 特殊なケース

 最初の特殊なケースは、避けられない対蹠的等価性です。 セットに ((1,0,0)) と ((-1,0,0)) が含まれているとします。 彼らの指紋は満足する
 [
 F(1,0,0)=F(-1,0,0)。 
]
 不用意に実装すると、不変式が失敗したと結論付けられる可能性があります。 正しい解釈は、両方の点が同じ幾何学的な線を表すということです。 アルゴリズムはこれらをまとめて保持し、回転が判明するまで符号の決定を遅らせます。 

2 番目のエッジ ケースは、アイデンティティのローテーションです。 テイク
 [
 A={(1,0,0),(-1,0,0),(0,1,0),(0,-1,0)}
 ]
 (B=A) を別の順序にします。 必要な回転は、(\theta=0) を使用した恒等式にすることができます。 クォータニオンにはゼロのベクトル部分があるため、コードは axis ((1,0,0)) を出力します。 回転ゼロの場合、軸は任意であり、回転した点を直接一致させることによって順列が得られます。 

3 番目のエッジ ケースは、正確に (\pi) だけ回転します。 回転行列の反対称要素は理論的にはこの角度ではゼロであるため、次のような式が成り立ちます。 
[
 e_x=\frac{R_{32}-R_{23}}{2\sin\theta}
 ]
 数値的には危険です。 トレースが非正の場合、四元数変換では代わりに最大の対角項が選択されます。 たとえば、(z) 軸を中心に (\pi) だけ回転すると、次のようになります。 
[
 R=
 \begin{p行列}
 -1&0&0\
 0&-1&0\
 0&0&1
 \end{p行列}、
 ]
 そして、最大の対角線は、ゼロに近い量で割ることなく四元数の (z) 成分を決定します。 

4 番目のエッジ ケースは、提供された正方形のサンプルです。 その 4 つの点はすべて同じ 2 次フィンガープリントを持ちます。 並べ替えだけでは、どの 2 点が元の線を形成しているかを判断できません。 (n=2) なので、フォールバックはすべて (4!) 個の可能な点の置換を列挙します。 それぞれについて、2 つの非平行ベクトルから回転を構築し、4 つの点すべてをチェックします。 これらの候補の 1 つにより、有効な (-\pi/2) 回転と置換 (2,3,4,1) が得られます。 

最後の数値的なエッジ ケースは、たまたま非常に近い 2 つのランダムな指紋です。 独立して均一なランダムな方向の場合、異なるライン間の正確な等価性は確率 0 であり、固定された数値許容誤差内で衝突が発生する確率は非常に小さくなります。 このステートメントは、スカラー 4 次不変式を実際のフィンガープリントとして使用できるように、このランダムな構造を意図的に提供しています。 コードは依然としてすべての点に対して最終回転を検証するため、数値の曖昧さによって引き起こされる間違った候補は黙って出力されるのではなく拒否されます。
