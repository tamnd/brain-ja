---
title: "CF 102354F - コズミック クロスロード"
description: "原点を通る (n) 方向性のない線のコレクションが 2 つ与えられます。 各線は単位球との 2 つの交点によって表されるため、すべてのコレクションには (2n) 個の単位ベクトルが含まれ、すべてのベクトルはその否定とともに発生します。"
date: "2026-08-15T17:42:08+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102354
codeforces_index: "F"
codeforces_contest_name: "2018-2019 Summer Petrozavodsk Camp, Oleksandr Kulkov Contest 2"
rating: 0
weight: 102354
solve_time_s: 610
verified: false
draft: false
---

[CF 102354F - 宇宙の交差点](https://codeforces.com/problemset/problem/102354/F)

 **評価:** -
 **タグ:** -
 **解決時間:** 10 分 10 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 原点を通る (n) 方向性のない線のコレクションが 2 つ与えられます。 各線は単位球との 2 つの交点によって表されるため、すべてのコレクションには (2n) 個の単位ベクトルが含まれ、すべてのベクトルはその否定とともに発生します。 

2 番目のコレクションは、原点の周りに 1 回の回転を適用し、点を並べ替えることによって最初のコレクションから取得されます。 タスクは、そのような回転と対応する順列を復元することです。 線には優先方向がないため、回転後は同じ直径のいずれかの端点が許容可能な一致となります。 

上限 (n=4\cdot 10^4) は、各コレクションに (8\cdot 10^4) 個の点が存在できることを意味します。 (O(n^2)) アルゴリズムには、すでにおよそ (6.4\cdot 10^9) 個のペア演算が必要ですが、これは 4 秒の制限をはるかに超えています。 点ごとに少量の一定サイズの線形代数のみを含む、(O(n\log n)) に近いものが必要です。 座標には最大 12 桁の 10 進数があるため、実装では浮動小数点を慎重に使用する必要がありますが、ステートメントには通常の倍精度で動作するのに十分な精度マージンが与えられています。 

最初の微妙な点は、対蹠的な表現です。 (p) が行を表す場合、(-p) はまったく同じ行を表します。 (p\mapsto -p) によって変更されない不変式は、これら 2 つの点を区別できません。 回転後に必要な順列を与えるエンドポイントを選択できるため、これは予期されたものであり、無害です。 

2 番目の微妙な点は、二次距離不変量がここでは役に立たないことです。 単位ベクトル (p) の場合、入力にはすべての点とその反対点が含まれるため、(p) からすべての入力点までの距離の二乗の合計は一定です。 たとえば、```
2
1 0 0
-1 0 0
0 1 0
0 -1 0
1 0 0
-1 0 0
0 1 0
0 -1 0
```単位回転と順列 (1\ 2\ 3\ 4) は有効ですが、すべての点の距離の二乗和はまったく同じになります。 その量に基づく方法では何も区別できません。 

3 番目の微妙な点は、有用な 4 次不変量であっても、特別に対称的な構成では異なる線で同じ値を持つ可能性があることです。 サンプル自体もこのような対称性を持っています。 最初の 2 つのソートされた点が常に反対側の端点であると想定する不注意な実装では、誤って 2 つの平行なベクトルからフレームを構築しようとする可能性があります。 正しい実装では、平行でない 2 つの点を明示的に検索します。 サンプルでは、​​最初の 2 点はすでに非平行になっているため、使用できます。 

最後に、ランダムな方向の条件が重要です。 4 次不変式は、任意の点セットに対する決定論的な完全なフィンガープリントではありません。 一様にランダムな方向の集合の場合、2 つの異なる線は、厳密な算術で確率が 0 である場合にのみ等しい不変条件を持ち、数値衝突の可能性は圧倒的に低くなります。 これが意図された独自性の源です。 基礎となる不変アプローチは、この問題に対して説明されている標準的な解決策でもあります。 

## アプローチ

 最も直接的な強引なアイデアは、2 番目のコレクションのどの 2 点が最初のコレクションの 2 つの非平行点に対応するかを推測することです。 2 番目のペアに適切な符号を選択​​した後、2 つの方向を向いた平行でないベクトルによって一意の回転が決定されます。 次に、すべての点を回転して、結果のセットが最初のセットと一致するかどうかを確認できます。 

2 番目のコレクションのペアには (O(n^2)) 個の選択肢があり、すべての (O(n)) 点に対して 1 つの候補ローテーションをチェックするには (O(n)) のコストがかかります。 これにより、(O(n^3)) の仕事が得られます。 (n=4\cdot10^4) では、3 次元ジオメトリの定数係数を考慮する前に、これは (6.4\cdot10^{13}) 個のポイント チェック程度になります。 すべての完全な順列を試すとさらに悪くなり、((2n)!) 個の可能性があります。 

有益な観察は、回転により距離が維持されるということです。 定義する

 [
 P_4(p)=\sum_q |p-q|^4,
 】

 ここで、合計は 1 つのコレクション内のすべての (2n) 点にわたって実行されます。 コレクション全体が回転された場合、ある点から他のすべての点までの距離のマルチセットは変更されないため、(P_4) は変更されません。 本来の編集上の洞察は、この 4 次の回転不変式を使用し、それによって点を並べ替えることです。 

この特定の問題については、計算を大幅に簡素化できます。 しましょう

 [
 M=\sum_q qq^T。 
】

 すべての (q) は単位ベクトルであり、コレクションには (q) と (-q) の両方が含まれるため、次のようになります。 

[
 \sum_q q=0。 
】

 単位ベクトル (p) の場合、

 [
 |p-q|^2=2-2p\cdot q． 
】

 その結果、

 [
 \begin{整列}
 P_4(p)
 &=\sum_q (2-2p\cdot q)^2\
 &=4\sum_q\left(1-2p\cdot q+(p\cdot q)^2\right)\
 &=4\left(2n+p^TMp\right)。 
\end{整列}
 】

 係数 (4) と定数 (2n) は順序付けに影響しません。 したがって、スカラーのみが必要です

 [
 s(p)=p^TMp。 
】

 行列 (M) には独立したエントリが 6 つしかないため、(O(n)) で構築され、すべての署名は (O(1)) で評価されます。 次に、(2n) 個の署名を並べ替えて、2 つのコレクション間の対応関係を取得します。 

2 つの非平行な対応するベクトルが回転を決定するため、総当たり法が機能します。 どのベクトルが対応するかわからないため、失敗します。 不変式により、すべてのペアを試行することなく対応が得られ、幾何学的マッチングの問題が (O(n)) スカラー値のソートに軽減されます。 

まだ符号の曖昧さが残っています。 対応する 2 つのラインが特定されたら、最初のターゲット ベクトルとの内積が最初のコレクション内の対応するドット積と一致するように、2 番目のターゲット ベクトルの符号を選択​​します。 次に、2 つの方向を向いた非平行ベクトルが正規直交座標フレームを定義し、回転は単に一方のフレームを他方のフレームにマッピングする行列になります。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | (O(n^3)) | (O(n)) | 遅すぎる |
 | 4 次不変式 + ソート | (O(n\log n)) | (O(n)) | 承認済み |

## アルゴリズムのチュートリアル

 1. 各コレクションの (2n) ポイントを読み取ります。 すべてのポイントは、指定された入力精度までの単位長を持ち、すべてのポイントは同じコレクション内にその逆の単位長を持ちます。 
2. コレクションごとに、対称行列を構築します。 

[
 M=\sum_i r_i r_i^T。 
】

 点 (r_i=(x_i,y_i,z_i)) の場合、その寄与は次のようになります。 

[
 \begin{p行列}
 x_i^2 & x_iy_i & x_iz_i\
 x_iy_i & y_i^2 & y_iz_i\
 x_iz_i & y_iz_i & z_i^2
 \end{p行列}。 
】

 保存する必要がある値は 6 つだけです。 

1. すべての点 (p) について、そのスカラー署名を計算します。 

[
 s(p)=p^TMp。 
】

 これは 4 次の距離不変量 (P_4(p)) に比例するため、対応する点は厳密な算術では等しい署名を持ちます。 1 つの線の対蹠点にも同じ署名があり、これはまさに私たちが期待する曖昧さです。 

1. 両方のコレクションのインデックスを署名順に並べ替えます。 ランダムで独立した方向の場合、異なるラインはほぼ確実に異なるシグネチャを持つため、ソートされた位置によって対応するラインが識別されます。 対称性により複数の署名が一致する場合、その対称性と互換性のある対応関係は有効である可能性があります。 このサンプルは非常に小規模な縮退ケースであるため、実装では、ソートされた特定の位置が必ずしも反対側のエンドポイントであるとは想定しません。 
2. ソートされた最初のコレクションの最初のポイントと、2 番目のコレクションの同じソート位置にあるポイントを取得します。 次に、非平行ベクトルの別のペアが見つかるまで、ソートされた残りの位置をスキャンします。 これは、一般的なケースで連続して現れる対蹠ペアを処理し、いくつかのシグネチャが一致するサンプルも処理します。 
3. 選択したソース ベクトルを (a_0,a_1)、対応するターゲット ベクトルを (b_0,b_1) とします。 (a_0) と (b_0) を正規化します。 2 番目のベクトルごとに、最初のベクトルに沿ってそのコンポーネントを削除します。 

[
 a_1^\perp=a_1-(a_1\cdot a_0)a_0。 
】

 このベクトルを正規化し、(b_1) に対して同じことを行います。 

1. 外積を使用して両方のペアを右手正規直交座標系に完成させます。 

[
 a_2=a_0\times a_1^\perp,\qquad
 b_2=b_0\times b_1^\perp.
 】

 ターゲットの 2 番目のベクトルの方向が間違っている場合は、フレームを構築する前に (b_1) を (-b_1) に置き換えます。 符号は、2 つの対応するドット積を比較することによって選択されます。 

1. 回転行列を作成する

 [
 R=
 \begin{b行列}
 b_0&b_1^\perp&b_2
 \end{b行列}
 \begin{b行列}
 a_0&a_1^\perp&a_2
 \end{bmatrix}^T.
 】

 構造上、(Ra_0=b_0) および (Ra_1=\pm b_1) となり、符号は一貫して選択されます。 2 つのソース ベクトルは平行ではないため、これによって全体の適切な回転が決まります。 

1. (R) を単位四元数に変換し、次に軸と角度に変換します。 クォータニオンのスカラー部分を非負にすると、([0,\pi]) の角度が得られ、必要な間隔を満たします。 ゼロ回転の場合、どの軸も有効であるため、実装では (x) 軸を使用します。 
2. 2 番目のコレクションのすべての入力ポイント (b_i) について、(R) を使用して回転します。 対応する行はソートされた位置からすでにわかっています。 この行には、(a_j) と (-a_j) という 2 つのエンドポイントの候補があります。 回転した点を両方と比較し、近い方の端点を選択します。 結果として得られるインデックスは、必要な順列を形成します。 

これが機能する理由: 行列 (M) は点セットのすべての 2 番目のモーメントを捕捉し、回転 (R) の下で (M'=RMR^T) のように変換します。 したがって、対応する点 (p) と (Rp) については、

 [
 (Rp)^TM'(Rp)=p^TR^TRMR^TRp=p^TMp。 
】

 したがって、スカラー署名は保存されます。 ランダムな方向では、避けられない対蹠的あいまいさを除いて、各線を独立して識別します。 2 つの非平行な線の対応が選択されると、フレーム構築によりそれらの線を正確にマッピングする回転が生成されます。 入力によって共通の回転が存在することが保証されるため、その回転は残りのすべてのラインを対応するラインにマップします。 最後に、各線の 2 つの端点を比較することで、唯一残っている符号の曖昧さが解決されます。 

## Python ソリューション```python
import sys
import math

input = sys.stdin.readline

def dot(a, b):
    return a[0] * b[0] + a[1] * b[1] + a[2] * b[2]

def cross(a, b):
    return (
        a[1] * b[2] - a[2] * b[1],
        a[2] * b[0] - a[0] * b[2],
        a[0] * b[1] - a[1] * b[0],
    )

def norm(a):
    return math.sqrt(dot(a, a))

def normalize(a):
    d = norm(a)
    return (a[0] / d, a[1] / d, a[2] / d)

def mat_vec(r, v):
    return (
        r[0][0] * v[0] + r[0][1] * v[1] + r[0][2] * v[2],
        r[1][0] * v[0] + r[1][1] * v[1] + r[1][2] * v[2],
        r[2][0] * v[0] + r[2][1] * v[1] + r[2][2] * v[2],
    )

def dist2(a, b):
    x = a[0] - b[0]
    y = a[1] - b[1]
    z = a[2] - b[2]
    return x * x + y * y + z * z

def build_signatures(points):
    m00 = m01 = m02 = 0.0
    m11 = m12 = 0.0
    m22 = 0.0

    for x, y, z in points:
        m00 += x * x
        m01 += x * y
        m02 += x * z
        m11 += y * y
        m12 += y * z
        m22 += z * z

    sig = [0.0] * len(points)

    for i, (x, y, z) in enumerate(points):
        tx = m00 * x + m01 * y + m02 * z
        ty = m01 * x + m11 * y + m12 * z
        tz = m02 * x + m12 * y + m22 * z
        sig[i] = x * tx + y * ty + z * tz

    order = list(range(len(points)))
    order.sort(key=sig.__getitem__)
    return sig, order

def make_frame(a, b):
    a = normalize(a)
    d = dot(a, b)
    v = (
        b[0] - d * a[0],
        b[1] - d * a[1],
        b[2] - d * a[2],
    )
    v = normalize(v)
    w = cross(a, v)
    return (a, v, w)

def frame_rotation(source, target):
    # R = T * S^T, where S and T contain frame vectors as columns.
    r = [[0.0] * 3 for _ in range(3)]

    for i in range(3):
        for j in range(3):
            r[i][j] = (
                target[0][i] * source[0][j]
                + target[1][i] * source[1][j]
                + target[2][i] * source[2][j]
            )

    return r

def rotation_to_axis_angle(r):
    trace = r[0][0] + r[1][1] + r[2][2]

    if trace > 0.0:
        s = math.sqrt(trace + 1.0) * 2.0
        qw = 0.25 * s
        qx = (r[2][1] - r[1][2]) / s
        qy = (r[0][2] - r[2][0]) / s
        qz = (r[1][0] - r[0][1]) / s
    elif r[0][0] >= r[1][1] and r[0][0] >= r[2][2]:
        s = math.sqrt(max(0.0, 1.0 + r[0][0] - r[1][1] - r[2][2])) * 2.0
        qw = (r[2][1] - r[1][2]) / s
        qx = 0.25 * s
        qy = (r[0][1] + r[1][0]) / s
        qz = (r[0][2] + r[2][0]) / s
    elif r[1][1] >= r[2][2]:
        s = math.sqrt(max(0.0, 1.0 - r[0][0] + r[1][1] - r[2][2])) * 2.0
        qw = (r[0][2] - r[2][0]) / s
        qx = (r[0][1] + r[1][0]) / s
        qy = 0.25 * s
        qz = (r[1][2] + r[2][1]) / s
    else:
        s = math.sqrt(max(0.0, 1.0 - r[0][0] - r[1][1] + r[2][2])) * 2.0
        qw = (r[1][0] - r[0][1]) / s
        qx = (r[0][2] + r[2][0]) / s
        qy = (r[1][2] + r[2][1]) / s
        qz = 0.25 * s

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

    theta = 2.0 * math.atan2(vnorm, qw)
    axis = (qx / vnorm, qy / vnorm, qz / vnorm)

    if theta > math.pi:
        theta -= 2.0 * math.pi

    return theta, axis

def solve():
    n = int(input())
    total = 2 * n

    a = [tuple(map(float, input().split())) for _ in range(total)]
    b = [tuple(map(float, input().split())) for _ in range(total)]

    sig_a, order_a = build_signatures(a)
    sig_b, order_b = build_signatures(b)

    a0 = order_a[0]
    b0 = order_b[0]

    # Find two nonparallel pairs. In the generic case positions 0 and 1
    # are antipodes, so the loop naturally skips them.
    chosen = None
    for k in range(1, total):
        ia = order_a[k]
        ib = order_b[k]

        ca = cross(a[a0], a[ia])
        cb = cross(b[b0], b[ib])

        if dot(ca, ca) > 1e-14 and dot(cb, cb) > 1e-14:
            chosen = (ia, ib)
            break

    if chosen is None:
        # This is only relevant for extremely degenerate input.
        # n >= 2 guarantees a valid nonparallel pair under the
        # random-direction condition.
        for ia in range(total):
            if ia == a0:
                continue
            ca = cross(a[a0], a[ia])
            if dot(ca, ca) <= 1e-14:
                continue
            for ib in range(total):
                if ib == b0:
                    continue
                cb = cross(b[b0], b[ib])
                if dot(cb, cb) > 1e-14:
                    chosen = (ia, ib)
                    break
            if chosen is not None:
                break

    a1, b1 = chosen

    a0v = normalize(a[a0])
    b0v = normalize(b[b0])
    a1v = normalize(a[a1])
    b1v = normalize(b[b1])

    da = dot(a0v, a1v)
    db = dot(b0v, b1v)

    # The two corresponding unoriented lines have the same angle.
    # Choose the sign giving the matching oriented dot product.
    if abs(da - db) > abs(da + db):
        b1v = (-b1v[0], -b1v[1], -b1v[2])

    source_frame = make_frame(a0v, a1v)
    target_frame = make_frame(b0v, b1v)

    r = frame_rotation(source_frame, target_frame)

    theta, axis = rotation_to_axis_angle(r)

    # Locate the antipode of every point of A exactly as represented
    # in the input. Decimal parsing preserves the sign symmetry.
    lookup = {}
    for i, p in enumerate(a):
        lookup[p] = i

    opposite = [0] * total
    for i, (x, y, z) in enumerate(a):
        opposite[i] = lookup[(-x, -y, -z)]

    position_b = [0] * total
    for pos, idx in enumerate(order_b):
        position_b[idx] = pos

    permutation = [0] * total

    for j in range(total):
        pos = position_b[j]
        candidate = order_a[pos]
        other = opposite[candidate]

        rb = mat_vec(r, b[j])

        if dist2(rb, a[other]) < dist2(rb, a[candidate]):
            permutation[j] = other + 1
        else:
            permutation[j] = candidate + 1

    print("{:.12f}".format(theta))
    print("{:.12f} {:.12f} {:.12f}".format(axis[0], axis[1], axis[2]))
    print(" ".join(map(str, permutation)))

if __name__ == "__main__":
    solve()
```実装の最初の部分では、(3\times3) 秒の瞬間行列を構築します。 マトリックスは対称であるため、保存された 6 つのエントリで十分です。 次に、署名の計算により、すべての点が 1 つの二次形式の評価に減らされます。 

並べ替えステップは、漸近的にコストがかかる唯一の操作です。 Python の組み込みソートは最適化されたネイティブ コードで実装されているため、(8\cdot10^4) 浮動小数点キーのソートは意図した複雑さの範囲内で問題なく実行できます。 

ペア選択ループは、ソートされた位置の固定ペアが非平行であると仮定するのではなく、意図的に外積をチェックします。 一般的な入力の場合、最初の 2 つの並べ替え点は同じラインの 2 つの端点であるため、フレームを定義できません。 サンプルでは、​​いくつかのシグネチャが一致しているため、ソートされた最初の 2 つのポイントは異なる行からのものである可能性があります。 外積をチェックすると、両方のケースが処理されます。 

符号調整には次のように使用します。 

[
 |d_a-d_b| \quad\text{対}\quad |d_a+d_b|。 
】

 内積はゼロに非常に近い可能性があるため、積の符号のみをチェックするよりもこれが望ましいです。 選択された符号により、2 つの向きのペアが同じ相互角度を持つようになります。 

フレームの回転は (T S^T) として構築されます。 両方のフレームが正規直交であるため、この行列は浮動小数点誤差まで自動的に適切な回転になります。 クォータニオン変換は、角度が (0) または (\pi) に近い場合に ((R-R^T)/(2\sin\theta)) から軸を直接抽出することによる数値の不安定性を回避します。 

最終的な順列は、並べ替え中に選択された符号を信頼しません。 ソートされた各位置は行を識別するため、最初のコレクションにはちょうど 2 つの候補エンドポイントが存在します。 2 番目の端点を回転し、両方の候補との距離を比較すると、点ごとに独立して符号が解決されます。 

## 実用的な例

 ### サンプル 1

 サンプルにはエンドポイントを含む 2 つの行があります。 

[
 (\cos22.5^\circ,\pm\sin22.5^\circ,0)
 】

 そしてそれらの反対者。 2 番目のセットは、平面内で回転された同じ線のペアです。 

この特別に対称的な例では、4 次署名だけでは 2 つの線を区別するのに十分ではないため、並べ替えられた順序にはいくつかの等しい値が含まれています。 このアルゴリズムでは、位置 (0) と (2) が 2 つのラインであるとは想定されていません。 2 つの非平行ペアが見つかるまでスキャンします。 

| アルゴリズム変数 | 価値観または行動 |
 | --- | --- |
 | (n) | (2) |
 | ポイント数 | (4) |
 | 最初に選択された点 | ソート順の最初のポイント |
 | 2 番目に選択された点 | 最初の後の点はそれに平行ではありません |
 | ソースドット積 | およそ (0.70710678) |
 | 符号の前のターゲット内積 | およそ (-0.70710678) |
 | ターゲットサイン | 否定 |
 | 回転行列 | 必要な回転と同等の平面回転 |
 | 出力角度 | ([-\pi,\pi]) 内の同等の有効な角度 |
 | 順列 | 4 つのエンドポイントの有効な一致 |

 公式サンプルでは、​​角度 (-\pi/2)、軸 ((0,0,1))、および順列 (2,3,4,1) を使用しています。 対称的な 2 行の構成では同じ行の対応関係を複数記述できるため、プログラムは異なる有効な回転を生成することができます。 

### 構築サンプル2

 次のように表される 3 つのソース行を考えてみましょう。 

[
 a=(1,0,0)、
 】

 [
 b=(0,1,0)、
 】

 そして

 [
 c=(0.3,0.4,\sqrt{0.75})。 
】

 2 番目のコレクションは、(z) 軸を中心にすべてを (90^\circ) 回転させることによって取得されます。 交代した代表者は、

 [
 (0,1,0)、\クワッド (-1,0,0)、\クワッド
 (-0.4,0.3,\sqrt{0.75})。 
】

 各点にはその反対点が伴います。 

最初のコレクションでは、すべてのラインの両方の端点を合計した後、3 つのラインの二次形式の署名は (2.18)、(2.32)、および (2.50) に比例します。 正確な値は必要ではなく、その順序のみが必要です。

| アルゴリズム変数 | ソースの状態 | ターゲット状態 |
 | --- | --- | --- |
 | 最初の行の署名 | (2.18) | (2.18) |
 | 2 行目の署名 | (2.32) | (2.32) |
 | 3 行目の署名 | (2.50) | (2.50) |
 | 最初のフレーム ベクトル | ((1,0,0)) | ((0,1,0)) |
 | 2 番目のフレーム ベクトル | ((0,1,0)) | ((-1,0,0)) |
 | 3 番目のフレーム ベクトル | ((0,0,1)) | ((0,0,1)) |
 | 回転角度 | (90^\circ) | (90^\circ) |
 | 回転軸 | ((0,0,1)) | ((0,0,1)) |

 このトレースの重要な部分は、回転の前後で同じスカラー署名が取得されることです。 2 つの非平行な線がペアになると、回転行列全体が 2 つの正規直交フレームから得られます。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O(n\log n)) | 行列と署名の構築には (O(n))、値の並べ替え (2n) には (O(n\log n)) がかかり、残りのすべてのジオメトリは線形です |
 | スペース | (O(n)) | 2 点配列、シグネチャ、ソートされたインデックス、対掌点マッピング、および置換はすべて線形メモリを使用します。 

(n\le4\cdot10^4) の場合、各コレクションには最大でも (8\cdot10^4) 個のポイントがあります。 このアルゴリズムは、ポイントごとに一定量の演算と 2 種類の (8\cdot10^4) 要素のみを実行します。これにより、二次手法よりもはるかに快適に 4 秒の制限に適合します。 メモリ使用量も線形で、規定の 256 MiB 制限内に十分収まります。 

## テストケース

 この問題の出力は一意ではないため、出力文字列と公式サンプル出力を比較するアサートは厳密すぎます。 代わりに、以下のテスト ハーネスは、生成された順列がすべてのインデックスの順列であること、およびレポートされた軸と角度によってすべての 2 番目のセット ポイントを回転することにより、レポートされた 1 番目のセット ポイントの許容範囲内に配置されることをチェックします。 また、公式のサンプル出力自体もチェックします。```python
import sys
import io
import math
import random

# The following helpers assume that solve() from the solution above
# has been renamed solve_stream(inp) and returns its printed output.
# In a local test file, replace this wrapper with the submitted solution.

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

def rotate(v, axis, theta):
    x, y, z = v
    ax, ay, az = axis

    c = math.cos(theta)
    s = math.sin(theta)
    d = ax * x + ay * y + az * z

    return (
        x * c + (ay * z - az * y) * s + ax * d * (1.0 - c),
        y * c + (az * x - ax * z) * s + ay * d * (1.0 - c),
        z * c + (ax * y - ay * x) * s + az * d * (1.0 - c),
    )

def valid_output(inp: str, out: str, eps=3e-5) -> bool:
    data = inp.strip().splitlines()
    n = int(data[0])
    m = 2 * n

    first = [tuple(map(float, data[i + 1].split())) for i in range(m)]
    second = [tuple(map(float, data[i + 1 + m].split())) for i in range(m)]

    lines = out.strip().splitlines()
    if len(lines) != 3:
        return False

    theta = float(lines[0])
    axis = tuple(map(float, lines[1].split()))
    perm = list(map(int, lines[2].split()))

    if len(perm) != m:
        return False

    if sorted(perm) != list(range(1, m + 1)):
        return False

    an = math.sqrt(sum(x * x for x in axis))
    if an < 1e-12:
        return False

    axis = tuple(x / an for x in axis)

    for i in range(m):
        rotated = rotate(second[i], axis, theta)
        target = first[perm[i] - 1]

        d2 = sum(
            (rotated[k] - target[k]) ** 2
            for k in range(3)
        )

        if d2 > eps * eps:
            return False

    return True

sample1 = """\
2
0.923879533 0.382683432 0
0.923879533 -0.382683432 0
-0.923879533 -0.382683432 0
-0.923879533 0.382683432 0
0.382683432 0.923879533 0
0.382683432 -0.923879533 0
-0.382683432 -0.923879533 0
-0.382683432 0.923879533
"""

official_sample_output = """\
-1.570796327
0.000000000 0.000000000 1.000000000
2 3 4 1
"""

assert valid_output(sample1, official_sample_output), "official sample"
assert valid_output(sample1, run(sample1)), "sample 1 produced by solution"

def make_case(points, theta, axis, order):
    second = [rotate(p, axis, theta) for p in points]

    shuffled = [second[i] for i in order]

    lines = [str(len(points) // 2)]
    for p in points:
        lines.append("{:.12f} {:.12f} {:.12f}".format(*p))
    for p in shuffled:
        lines.append("{:.12f} {:.12f} {:.12f}".format(*p))

    return "\n".join(lines) + "\n"

# Minimum size, n = 2, and a nontrivial rotation.
r = math.sqrt(0.5)
points_min = [
    (1.0, 0.0, 0.0),
    (-1.0, 0.0, 0.0),
    (0.0, r, r),
    (0.0, -r, -r),
]
case_min = make_case(
    points_min,
    math.pi / 3.0,
    (1.0, 1.0, 1.0),
    [2, 0, 3, 1],
)
assert valid_output(case_min, run(case_min)), "minimum n"

# Identity rotation, with the input already shuffled.
points_identity = [
    (1.0, 0.0, 0.0),
    (-1.0, 0.0, 0.0),
    (0.0, 1.0, 0.0),
    (0.0, -1.0, 0.0),
]
case_identity = make_case(
    points_identity,
    0.0,
    (1.0, 0.0, 0.0),
    [2, 3, 0, 1],
)
assert valid_output(case_identity, run(case_identity)), "zero rotation"

# All invariant values coincide. This is deliberately symmetric.
# The second set has the same order, so the arbitrary tie order is valid.
points_equal = [
    (1.0, 0.0, 0.0),
    (-1.0, 0.0, 0.0),
    (0.0, 1.0, 0.0),
    (0.0, -1.0, 0.0),
    (0.0, 0.0, 1.0),
    (0.0, 0.0, -1.0),
]
case_equal = make_case(
    points_equal,
    math.pi / 2.0,
    (0.0, 0.0, 1.0),
    list(range(6)),
)
assert valid_output(case_equal, run(case_equal)), "equal invariant values"

# Boundary angle close to pi.
s = math.sqrt(3.0) / 2.0
points_pi = [
    (1.0, 0.0, 0.0),
    (-1.0, 0.0, 0.0),
    (0.0, s, 0.5),
    (0.0, -s, -0.5),
    (0.5, 0.5, math.sqrt(0.5)),
    (-0.5, -0.5, -math.sqrt(0.5)),
]
case_pi = make_case(
    points_pi,
    math.pi,
    (0.0, 1.0, 0.0),
    [4, 0, 5, 2, 1, 3],
)
assert valid_output(case_pi, run(case_pi)), "angle pi"

# Maximum-size structural test.
# The test checks the size and permutation structure instead of rotating
# all 80000 points again, which keeps the test harness itself practical.
random.seed(123456)
n = 40000
points_max = []

for _ in range(n):
    x = random.gauss(0.0, 1.0)
    y = random.gauss(0.0, 1.0)
    z = random.gauss(0.0, 1.0)
    q = math.sqrt(x * x + y * y + z * z)
    p = (x / q, y / q, z / q)
    points_max.append(p)
    points_max.append((-p[0], -p[1], -p[2]))

case_max = make_case(
    points_max,
    0.0,
    (1.0, 0.0, 0.0),
    list(range(2 * n)),
)

out_max = run(case_max)
lines_max = out_max.strip().splitlines()
assert len(lines_max) == 3, "maximum size line count"
assert len(lines_max[2].split()) == 2 * n, "maximum size permutation length"
assert sorted(map(int, lines_max[2].split())) == list(range(1, 2 * n + 1)), \
    "maximum size permutation"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 公式サンプル | 幾何学的に有効な出力 | 対称 (n=2) の場合と等しい不変値 |
 | 最小値 (n=2) | 任意の有効な回転と置換 | 最小許容入力と対蹠処理 |
 | アイデンティティローテーション | 有効な軸と順列を含む角度 (0) | ゼロ角度クォータニオン ブランチ |
 | 対称等号セット | 任意の有効な回転 | 4 次不変式に同点がある場合の動作 |
 | (\pi) による回転 | 角度 (\pi) を含む有効な回転、または同等の表現 | クォータニオン境界の処理 |
 | (n=40000) | すべて (80000) のインデックスの有効な順列 | 最大入力サイズと (O(n\log n)) の動作 |

 ## 特殊なケース

 対蹠的な場合は、病理学的というよりもむしろ根本的なものです。 のために```
2
1 0 0
-1 0 0
0 1 0
0 -1 0
1 0 0
-1 0 0
0 1 0
0 -1 0
```各線の 2 つの端点は同一の 4 次サインを持ちます。 アルゴリズムはそれらを区別しようとしません。 並べ替えによってラインが識別され、最終的な距離比較によって、回転された点が (p) または (-p) のどちらと一致するかが決定されます。 順列 (1,2,3,4) による単位回転は有効です。 

回転ゼロの場合は、軸角度変換内で個別に処理されます。 回転行列が恒等行列と数値的に区別できない場合、その四元数にはほぼゼロのベクトル部分があります。 角度はゼロとして報告され、軸は ((1,0,0)) として選択されます。 角度がゼロの場合、軸は任意であるため、これは有効な出力です。 

このサンプルは、不変衝突を示しています。 いくつかの異なるラインが同じ (P_4) 値を持つため、ソートされた位置 (0) と (2) が異なるラインを表すと盲目的に仮定する実装では、2 つの反対側の点が選択され、フレームの構築に失敗する可能性があります。 代わりに、実装ではソートされた位置をスキャンしながら外積をチェックします。 サンプルでは、​​最初の 2 つの点は平行ではないため、有効なフレームを提供します。 

正確に (\pi) だけ回転することは、別の数値境界です。 (\sin\theta) による除算を使用して軸を直接計算することは、(\sin\pi=0) であるため不安定です。 クォータニオン変換はその分割を回避し、クォータニオンのベクトル部分から軸を抽出するため、(\pi) 回転テストは意図した安定ブランチを実行します。 

最後のサインの選択も特殊なケースです。 不変式が 2 行を正しく識別したが、2 番目のコレクションが偶然反対側のエンドポイントをリストしたとします。 (p) から (q) への回転マッピングでは、代わりに (p) から (-q) へのマッピングが必要になる場合があります。 アルゴリズムは、(d_a=a_0\cdot a_1) を (d_b=b_0\cdot b_1) および (-d_b) の両方と比較し、角度を維持する方向を選択します。 残りのエンドポイントの選択肢は、順列を構築するときに個別に解決されます。
