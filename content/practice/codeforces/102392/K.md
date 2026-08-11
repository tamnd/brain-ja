---
title: "CF 102392K - 座礁ロボット"
description: "寸法が m × n × p の 3 次元長方形グリッドがあります。 セルは、固体の残骸、空きスペース、ロボットの開始セル R、またはテレポーター T のいずれかです。ロボットは空のセルを占有し、最初は隣接する固体の残骸に取り付けられます。"
date: "2026-08-10T19:43:32+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102392
codeforces_index: "K"
codeforces_contest_name: "2019-2020 ICPC Southeastern European Regional Programming Contest (SEERC 2019)"
rating: 0
weight: 102392
solve_time_s: 372
verified: true
draft: false
---

[CF 102392K - 座礁ロボット](https://codeforces.com/problemset/problem/102392/K)

 **評価:** -
 **タグ:** -
 **解決時間:** 6 分 12 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 寸法を含む 3 次元の長方形グリッドがあります。`m × n × p`。 セルは固い残骸、空きスペース、ロボットの開始セルのいずれかです`R`、またはテレポーター`T`。 ロボットは空のセルを占有し、最初は近くの固い残骸に取り付けられています。 目標は達成することです`T`残骸に付着した移動を最小限の移動で終了します。 

珍しいのは、すべての動きの前に重力を独立して選択できることです。 考えられる重力方向は 6 つあり、各座標軸に対して 1 つは正の方向、もう 1 つは負の方向です。 選択した方向では、太陽光は反対側から差し込みます。 位置は、太陽との間に固体のブロックがない場合にのみ使用できます。 移動には、表面に沿った通常の水平移動、高い表面からの飛び降りとそれに続く落下、または重力の方向を変えた後の純粋な落下が含まれます。 すべての移動には正確に 1 のコストがかかります。 

入力には各次元に沿って最大 500 個のセルが含まれるため、ボリュームは次の範囲に達します。`500^3 = 125,000,000`細胞。 これにより、グリッド セルごとに 1 つの頂点を持つグラフの保存または検索が直ちに除外されます。 すべてのセルにわたる線形時間の走査でさえ、すでに入力自体のスケールになっていますが、ボリューム内で二次的なものは完全に不可能です。 有用なアルゴリズムは、入力全体を読み取る必要があります。`O(mnp)`時間はかかりますが、その後ははるかに小さい表現で作業する必要があります。 

重要なエッジ ケースは、空のセルと有効な静止位置のセルの区別から生じます。 たとえば、次の 1 次元配置を考えてみましょう。```
3 1 1
R*T
```ロボットは中央の固体セルによってサポートされていますが、ロボットが移動できる 2 番目の横方向の次元はありません。 固体セルをただ歩いて到達することはできません。`T`、したがって、答えは次のとおりです`-1`。 隣接する空のセルを越える単純な最短パスでは、テレポーターが到達可能であると誤って扱われてしまいます。 

もう一つの微妙なケースは、テレポーターを通って落ちるというものです。 考慮する：```
2 4 1
R*
T-
--
*-
```ロボットは重力を増大させる方向を選択できる`y`。 その列には固体ブロックが含まれています`y = 3`、だから、から`y = 0`に落ちます`y = 2`。 テレポーターは、`y = 1`その落下中に通過し、アクティブになりません。 落下によって横切られたすべてのセルをチェックする不注意な実装では、誤って成功が報告されてしまいます。 正しい答えは、`-1`。 

3 番目のエッジケースは太陽光です。 で```
3 3 1
-R-
-*-
-T-
```関連する方向のロボットとテレポーターの間に残骸があり、ロボットは有効な一連の照明された動きを手配できません。 答えは`-1`。 すべての空の隣接セルを移動可能として扱うと、移動全体が両方のエンドポイントが点灯している間に行われなければならないという事実が見落とされます。 

最後に、グリッドの境界が重要です。 最初または最後の座標面上の固体ブロックは、片側からのみ見える場合があります。 グリッドの外側の位置は決して有効なロボット位置ではないため、座標にある表面は`0`座標で静止位置を生成できません`-1`、反対側の境界でも同様に。 

## アプローチ

 ブルートフォースアプローチは、すべての空のグリッドセルをロボットの可能な位置とみなして、そこから 6 つの重力方向すべてを試すことです。 各方向について、最初の固体ブロックが見つかるまでグリッドをスキャンし、ロボットが移動できるか落下できるかを判断してから、BFS を実行できます。 すべての物理的な動きを直接シミュレートできるため、これは正しいです。 

問題は状態空間と反復スキャンです。 あり得る`125,000,000`セルがあり、それらの BFS にはすでに約 1 億 2,500 万の状態が必要です。 すべての状態が 6 方向を検査し、各方向が最大 500 個のセルをスキャンすると、最悪のケースはおよそ`6 · 125,000,000 · 500 = 375,000,000,000`基本的なセルチェック。 訪問した配列全体を保存するだけでも、不必要に大きくなります。 

問題を変える観察は、太陽光の 1 つの固定方向では、各行の最初に目に見える固体ブロックのみが重要であるということです。 重力を正方向に考える`z`。 あらゆる人にとって`(x,y)`、 させて`zMin[x,y]`最小になる`z`固いブロックが入っている。 同じ線に沿ってさらに遠いすべての実ブロックは、この方向では永久に隠されます。 ロボットは、そのような目に見えるブロックの直前でのみ動作を完了できるため、最大でも`m · n`この方向には関連する位置が存在します。 

同じ構造が、次を使用して反対方向にも機能します。`zMax`、他の 2 つの軸についても同様です。 6 つの方向すべてにわたって、最大でも`2(np + mp + mn)`表面の状態。 すべての次元が最大 500 である場合、これは 1 億 2,500 万のグリッド セルではなく、最大 150 万の状態になります。 

深度バッファは、BFS に必要な完全な幾何学的情報です。 軸に沿った正の重力の場合、座標上のロボット`q`最初の固体ブロックがその座標にある場合にのみ移動できます`s`と`q + 1 <= s`。 もし`q + 1 < s`、ロボットはぶら下がっていて、1 回の操作でロボットを落とします。`s - 1`。 もし`q + 1 = s`、それは表面上に静止しており、別の目に見える表面に横方向に移動できます。 負の方向は対称です。`q - 1 >= s`そして着陸する`s + 1`。 

ブルートフォース手法は、文字通り物理規則をシミュレートしようとするため機能しますが、ほとんどのセルがアンカー状態になることができないため失敗します。 各行の最初に表示されるブロックのみが重要であるという観察により、BFS を実行する前に 3 次元ボリュームのほぼ全体を破棄できます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース |`O(mnp · max(m,n,p))`直接シミュレーションでは |`O(mnp)`| 遅すぎる |
 | 最適 |`O(mnp + mnp)`入力/深度構築用と`O(mn + mp + np)`BFS用 |`O(mn + mp + np)`| 承認済み |

 入力自体には以下が含まれます`Θ(mnp)`したがって、線形入力処理項を漸近的に回避することはできません。 

## アルゴリズムのチュートリアル

 1. グリッドを一度に 1 行ずつ読み取り、ロボットとテレポーターを見つけます。 元の 3 次元文字グリッドを保持する必要はありません。 すべての行について、どの位置にソリッド ブロックが含まれているかをコンパクトなビットセットとして記録します。これは、後の深度バッファの構築では、セルがソリッドかどうかだけを知る必要があるためです。 
2. 構築する`xmin`そして`xmax`あらゆる人にとって`(y,z)`ライン。`xmin[y,z]`最初の固体です`x`コーディネートと`xmax[y,z]`最後です。 行の最初と最後の固定位置はバイト文字列検索で直接見つけることができるため、全体にわたる Python ループは必要ありません。`m`文字。 
3. 固定ごとに`(z,x)`線、計算`ymin`そして`ymax`。 各レイヤーの行を上から下に処理します。`ymin`、下から上へ`ymax`。 未解決の列のビットセットを使用すると、各列を各方向に 1 回だけ割り当てることができます。 
4. 固定ごとに`(x,y)`線、計算`zmin`そして`zmax`。 低層から高層までレイヤを処理します。`zmin`そして高いものから低いものまで`zmax`。 繰り返しますが、未解決のビットセットは、すべてのビットセットが`(x,y)`位置は各方向に対して最大 1 回割り当てられます。 
5. BFS 状態を、前の移動に使用された重力の方向とともに固定位置として扱います。 状態は完全な 3 次元座標を保存する必要はありません。 固定方向と横座標の場合、深度バッファは可視ソリッド ブロックのすぐ隣に固定された座標を一意に決定します。 
6. ロボットの開始座標から直接、6 つの可能な重力方向をすべて考慮して BFS を初期化します。 これは、通常の状態のグラフにロボットを挿入するのとは少し異なります。 最初のロボットは固定されていますが、新しく選択された重力方向の可視表面に隣接していない可能性があります。 単にぶら下がっている場合、最初の動きは対応するフォールです。 BFS に挿入されたすべての状態は、すでに有効な移動のエンドポイントになっています。 
7. アンカー状態を処理するときは、6 つの重力方向すべてを試してください。 選択した方向について、対応するライン上の最初の実線ブロックを見つけます。 そのようなブロックが存在しない場合、ロボットは空間に落下するため、その方向では動きません。 現在の座標が点灯していない場合、その方向も移動を生成しません。 
8. 現在の位置がすでに可視ブロックに隣接している場合、ロボットは停止しています。 重力に垂直な 2 つの軸上の 4 つの隣接する位置の 1 つに移動できます。 正の重力方向の場合、宛先サーフェスは少なくとも現在のサーフェスと同じくらい重力軸に沿った距離にある必要があります。 負の方向の場合、その軸に沿ってそれ以上離れてはいけません。 エンドポイントは、宛先の表示ブロックの直前のセルになります。 
9. ロボットが照明されていても、目に見えるブロックに隣接していない場合、ロボットはぶら下がっています。 この重力下で可能な唯一の動作は、目に見える表面に直接落下することです。 終点は、ブロックの重力方向の直前のセルです。 
10. 目的地が生成されるたびに、その座標がテレポーターと等しいかどうかを確認します。 チェックは移動の最終エンドポイントでのみ実行され、落下中に交差する中間セルでは決して実行されません。 新しい場合は、その方向固有の状態を BFS に挿入します。 
11. BFS は、すべての遷移が正確に 1 つの物理的な動きを表すため、減少しない動きの数で状態を探索します。 初めてエンドポイントがテレポーターと等しい場合、その距離が可能な最小の答えとなります。 

機能する理由: すべての重力方向について、対応する深度バッファーは、すべての線に沿って太陽から見える最初の残骸ブロックを正確に識別します。 最初のブロックの背後にあるブロックは、その指示に基づく合法的な動きに影響を与えることはできません。 照明されたロボットからのすべての合法的な動きは、この最初に見えるブロックへの落下、または静止面から別の目に見える面への横移動のいずれかです。 これらはまさに BFS によって生成された遷移です。 逆に、生成されたすべてのトランジションは、問題からの照明、サポート、および移動の条件を満たします。 したがって、BFS グラフには、アンカーされたエンドポイント間のすべての正当な移動が正確に含まれます。 各エッジのコストは 1 であるため、BFS は最小移動数を返します。 

## Python ソリューション```python
import sys
from array import array

input = sys.stdin.readline

def solve():
    m, n, p = map(int, input().split())

    # For the six directions:
    # +x, -x use planes of size n*p
    # +y, -y use planes of size m*p
    # +z, -z use planes of size m*n
    sx = n * p
    sy = m * p
    sz = m * n

    xmin = array('h', [-1]) * sx
    xmax = array('h', [-1]) * sx
    ymin = array('h', [-1]) * sy
    ymax = array('h', [-1]) * sy
    zmin = array('h', [-1]) * sz
    zmax = array('h', [-1]) * sz

    rx = ry = rz = -1
    tx = ty = tz = -1

    # Translate every '*' to byte 1 and everything else to byte 0.
    trans = bytearray(256)
    trans[ord('*')] = 1
    trans = bytes(trans)

    # Bit i of a row-bitset is stored at bit 8*i.
    # This wastes 7 bits per cell, but makes extraction very simple.
    row_lane_mask = ((1 << (8 * m)) - 1) // 255

    layers = []

    for z in range(p):
        layer = []

        for y in range(n):
            row = input().strip()

            pos = row.find(b'R')
            if pos != -1:
                rx, ry, rz = pos, y, z

            pos = row.find(b'T')
            if pos != -1:
                tx, ty, tz = pos, y, z

            first = row.find(b'*')
            if first != -1:
                last = row.rfind(b'*')
                idx = z * n + y
                xmin[idx] = first
                xmax[idx] = last

            bits = int.from_bytes(row.translate(trans), 'little')
            layer.append(bits)

        layers.append(layer)

        # yMin for this z-layer.
        unseen = row_lane_mask
        base = z * m

        for y, bits in enumerate(layer):
            new = bits & unseen

            while new:
                low = new & -new
                x = (low.bit_length() - 1) >> 3
                ymin[base + x] = y
                unseen ^= low
                new ^= low

        # yMax for this z-layer.
        unseen = row_lane_mask

        for y in range(n - 1, -1, -1):
            new = layer[y] & unseen

            while new:
                low = new & -new
                x = (low.bit_length() - 1) >> 3
                ymax[base + x] = y
                unseen ^= low
                new ^= low

    # zMin and zMax use one lane per (x,y).
    cells = m * n
    global_lane_mask = ((1 << (8 * cells)) - 1) // 255

    # zMin.
    unseen = global_lane_mask

    for z in range(p):
        layer = layers[z]
        for y, bits in enumerate(layer):
            shifted = bits << (8 * y * m)
            new = shifted & unseen

            while new:
                low = new & -new
                cell = (low.bit_length() - 1) >> 3
                zmin[cell] = z
                unseen ^= low
                new ^= low

    # zMax.
    unseen = global_lane_mask

    for z in range(p - 1, -1, -1):
        layer = layers[z]
        for y, bits in enumerate(layer):
            shifted = bits << (8 * y * m)
            new = shifted & unseen

            while new:
                low = new & -new
                cell = (low.bit_length() - 1) >> 3
                zmax[cell] = z
                unseen ^= low
                new ^= low

    # The original 3D grid is no longer needed.
    del layers

    mins = (xmin, ymin, zmin)
    maxs = (xmax, ymax, zmax)
    dims = (m, n, p)
    planes = (sx, sy, sz)

    # Use one fixed stride for the six direction-specific state spaces.
    # Unused entries are harmless and keep state encoding simple.
    stride = max(planes)
    visited = bytearray(6 * stride)

    # Compact BFS queue. Every state id fits in an unsigned 32-bit integer.
    queue = array('I')

    def add_state(d, idx, x, y, z):
        if x == tx and y == ty and z == tz:
            return True

        sid = d * stride + idx
        if not visited[sid]:
            visited[sid] = 1
            queue.append(sid)

        return False

    def expand(x, y, z):
        """
        Generate all one-move destinations from (x,y,z).
        Returns True if the teleporter is reached.
        """
        coords = (x, y, z)

        for d in range(6):
            axis = d >> 1
            sign = 1 if (d & 1) == 0 else -1

            q = coords[axis]

            if axis == 0:
                tidx = y * p + z
            elif axis == 1:
                tidx = x * p + z
            else:
                tidx = y * m + x

            if sign == 1:
                surface = mins[axis][tidx]
                if surface < 0 or q + 1 > surface:
                    continue
            else:
                surface = maxs[axis][tidx]
                if surface < 0 or q - 1 < surface:
                    continue

            # The robot is hanging, so the only possible move is a fall.
            if q + sign != surface:
                q2 = surface - sign

                if q2 < 0 or q2 >= dims[axis]:
                    continue

                if axis == 0:
                    nx, ny, nz = q2, y, z
                elif axis == 1:
                    nx, ny, nz = x, q2, z
                else:
                    nx, ny, nz = x, y, q2

                if add_state(d, tidx, nx, ny, nz):
                    return True

                continue

            # The robot is resting on the visible surface.
            for other in range(3):
                if other == axis:
                    continue

                for delta in (-1, 1):
                    nx, ny, nz = x, y, z

                    if other == 0:
                        nx += delta
                        if nx < 0 or nx >= m:
                            continue
                    elif other == 1:
                        ny += delta
                        if ny < 0 or ny >= n:
                            continue
                    else:
                        nz += delta
                        if nz < 0 or nz >= p:
                            continue

                    if axis == 0:
                        nidx = ny * p + nz
                    elif axis == 1:
                        nidx = nx * p + nz
                    else:
                        nidx = ny * m + nx

                    if sign == 1:
                        ns = mins[axis][nidx]
                        if ns < 0 or ns < q + 1:
                            continue
                        nq = ns - 1
                    else:
                        ns = maxs[axis][nidx]
                        if ns < 0 or ns > q - 1:
                            continue
                        nq = ns + 1

                    if nq < 0 or nq >= dims[axis]:
                        continue

                    if axis == 0:
                        fx, fy, fz = nq, ny, nz
                    elif axis == 1:
                        fx, fy, fz = nx, nq, nz
                    else:
                        fx, fy, fz = nx, ny, nq

                    if add_state(d, nidx, fx, fy, fz):
                        return True

        return False

    # The robot is an anchored starting position, but it has no fixed
    # gravity direction. Generate its first move directly.
    if expand(rx, ry, rz):
        print(1)
        return

    # All states currently in the queue are endpoints of one move.
    distance = 1
    head = 0

    while head < len(queue):
        end = len(queue)

        while head < end:
            sid = queue[head]
            head += 1

            d = sid // stride
            idx = sid - d * stride

            axis = d >> 1
            sign = 1 if (d & 1) == 0 else -1

            if axis == 0:
                y = idx // p
                z = idx - y * p
                surface = xmin[idx] if sign == 1 else xmax[idx]
                x = surface - 1 if sign == 1 else surface + 1
            elif axis == 1:
                x = idx // p
                z = idx - x * p
                surface = ymin[idx] if sign == 1 else ymax[idx]
                y = surface - 1 if sign == 1 else surface + 1
            else:
                y = idx // m
                x = idx - y * m
                surface = zmin[idx] if sign == 1 else zmax[idx]
                z = surface - 1 if sign == 1 else surface + 1

            if expand(x, y, z):
                print(distance + 1)
                return

        distance += 1

    print(-1)

if __name__ == "__main__":
    solve()
```6 つの深度バッファは次の場所に保存されます。`array('h')`通常の Python リストではなく。 すべての座標は次の範囲にあります`0`そして`499`したがって、符号付き 16 ビット整数で十分ですが、値は`-1`は実線ブロックを含まない行を表します。 これにより、必要なメモリに比例したメモリが維持されます。`O(mn + mp + np)`表面的な情報。 

入力表現では、各行にコンパクトなビットセットが使用されます。 1 行には最大 500 個のセルがあり、各文字の情報をそのバイトの下位ビットに入れることで、Python は最適化された整数およびバイト演算内で入力前処理の大部分を実行できるようになります。 このビットセットは、Python ですべての列をスキャンせずに最初と最後の実線の行の位置を見つける場合に特に便利です。 

の`ymin`そして`ymax`構築では未解決列マスクが使用されます。 列が最初の固体セルに遭遇すると、その列はマスクから削除されます。 したがって、すべての入力行が検査されますが、それぞれの`(x,z)`この行では、各方向に 1 つずつ、合計 2 つの割り当てのみが成功します。 

同じ考え方が次の場合にも使用されます`zmin`そして`zmax`ただし、行のビットセットがグローバルにシフトされることを除きます。`(x,y)`コーディネートスペース。 各セル位置は、検索時に最大 1 回割り当てられます。`zmin`そして見つけたときに一度`zmax`。 

BFS は方向固有の状態を使用します。 状態は一般的なグリッド座標ではありません。 これは、最後の移動に使用された方向に関連付けられたアンカーされたエンドポイントです。 指定された方向と横断座標について、対応する深度バッファーが実際の 3 次元座標を決定します。`O(mn + mp + np)`状態が必要です。 

ロボットは最初に移動する前に新しい重力方向を選択できるため、ロボットの初期位置は個別に処理されます。 その方向でロボットがぶら下がったままになった場合、最初の動作は落下です。 その移動後、すべての BFS 状態は通常のアンカーされた表面状態になります。 

Python には整数オーバーフローの問題はなく、入力次元が最大 500 であるためのみ、座標バッファーは符号付き 16 ビット整数を使用します。BFS キューは、最大約 150 万の方向固有の状態が存在するため、符号なし 32 ビット整数を使用します。 

## 実用的な例

 ### サンプル 1

 公式の最初のサンプルは、```
2 5 1
R-
*-
*-
*T
**
```ロボットは次から始まります`(0,0,0)`そしてテレポーターはここにあります`(1,3,0)`。 重力を増加方向に考える`y`。 ロボットの列の最初の固体ブロックは次の位置にあります。`y = 1`、つまりロボットは で休んでいます。`y = 0`。 隣の列には`x = 1`、最初の固体ブロックは次のとおりです。`y = 4`にアンカー位置を与えます。`y = 3`。 

| BFS 状態 | 重力 | 現在の位置 | 目的地 | 距離 |
 | --- | --- | --- | --- | --- |
 | イニシャル |`+y`|`(0,0,0)`|`(1,3,0)`| 1 |

 行き先はまさにテレポーターだから答えは`1`。 これは、深度バッファ条件によってエンコードされたジャンプオフ動作を示しています。 ロボットは中間の空のセルを 1 つずつ移動する必要はありません。 

### サンプル 2

 公式の第 2 サンプルは、```
3 2 1
R-T
***
```ロボットは次から始まります`(0,0,0)`そしてテレポーターは`(2,0,0)`。 重力の下で増加に向かって`y`、すべての列には最初のソリッド ブロックがあります。`y = 1`、したがって、ロボットは上面に沿って水平に移動できます。 

| BFS 状態 | 現在の位置 | 移動 | 目的地 | 距離 |
 | --- | --- | --- | --- | --- |
 | イニシャル |`(0,0,0)`|`+x`表面移動 |`(1,0,0)`| 1 |
 |`(1,0,0)`|`(1,0,0)`|`+x`表面移動 |`(2,0,0)`| 2 |

 2 番目のエンドポイントはテレポーターなので、答えは次のようになります。`2`。 このトレースは、BFS が表面状態に関連付けられた方向を保持する必要がある理由も示しています。 同じ座標が、いくつかの重力方向の下で有効な固定エンドポイントになる可能性があり、それらの可能性が将来の異なる動きにつながる可能性があります。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |`O(mnp + mn + mp + np)`| 深度情報の読み取りと構築は、入力サイズにおいて線形です。 BFS は方向固有の表面状態のみを参照します。 
| スペース |`O(mnp)`入力の前処理中に、`O(mn + mp + np)`前処理後 | コンパクトな行ビットセットは、深度バッファーを構築するために一時的に保持されます。 BFS 自体は 6 つの深さバッファー、訪問された状態、およびそのキューのみを使用します。 

避けられない入力サイズが次のとおりであるため、理論的なアルゴリズムは意図したソリューションと一致します。`O(mnp)`、実際の検索スペースは`O(mn + mp + np)`。 のために`m,n,p <= 500`、検索グラフには最大約 150 万の方向固有の状態があります。 また、Python 実装では、元の 3 次元文字グリッドの保存を回避し、永続的な状態表現にコンパクトな数値配列を使用します。 

## テストケース

 公式声明の PDF 形式により、最初のサンプルが平坦に見える場合があります。 以下で使用される有効なサンプル レイアウトは、実際のレイアウトに対応するものです。`m × n × p`寸法。```python
import sys
import io
from array import array

# Paste the solve() implementation above here.

def run(inp: str) -> str:
    old_stdin = sys.stdin
    old_stdout = sys.stdout
    try:
        sys.stdin = io.StringIO(inp)
        sys.stdout = io.StringIO()
        solve()
        return sys.stdout.getvalue().strip()
    finally:
        sys.stdin = old_stdin
        sys.stdout = old_stdout

# Official samples
assert run("""\
2 5 1
R-
*-
*-
*T
**
""") == "1", "sample 1"

assert run("""\
3 2 1
R-T
***
""") == "2", "sample 2"

assert run("""\
3 3 1
-R-
-*-
-T-
""") == "-1", "sample 3"

assert run("""\
5 4 2
-R---
-****
-****
-****
-----
-----
*T---
----*
""") == "5", "sample 4"

# Minimum possible number of cells that can contain both R and T
# while still giving R a neighboring solid block.
assert run("""\
2 1 1
RT
""") == "-1", "R and T cannot share a supporting configuration"

# Simple one-move boundary case.
assert run("""\
2 2 1
RT
**
""") == "1", "teleporter is reached by one surface move"

# A fall passes through T but does not end there.
assert run("""\
2 4 1
R*
T-
--
*-
""") == "-1", "passing through T during a fall must not count"

# Maximum individual dimension, while keeping the volume practical
# for a regression test. R is supported by the adjacent star.
row = ["-"] * 500
row[0] = "R"
row[1] = "*"
row[499] = "T"
max_dimension_case = "500 1 1\n" + "".join(row) + "\n"
assert run(max_dimension_case) == "-1", "maximum dimension and boundary handling"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 公式サンプル1 |`1`| 単一のサーフェス移動には長いドロップが含まれる場合があります。 
| 公式サンプル2 |`2`| 通常の水平移動とBFS距離 |
 | 公式サンプル3 |`-1`| 照明により、一見近くにあるターゲットが到達不能になる可能性があります。 
| 公式サンプル4 |`5`| 重力変化と三次元運動の組み合わせ |
 |`2 1 1`と`RT`|`-1`| 縮退した寸法と横方向の動きの欠如 |
 |`2 2 1`と`RT / **`|`1`| 境界面と即時テレポーターの到達距離 |
 |`2 4 1`フォールスルーケース |`-1`| 落下中にテレポーターを通過することはカウントされません。 
|`500 1 1`スパースケース |`-1`| 最大座標次元と境界処理 |

 ## 特殊なケース

 フォールスルーの場合は、最終的な静止座標を計算した後にのみ目的地をチェックすることによって処理されます。 で```
2 4 1
R*
T-
--
*-
```ポジティブな-`y`深度バッファ`x = 0`最初の固体ブロックは次の場所にあります`y = 3`。 から始まる`(0,0,0)`、ロボットは照らされていますがぶら下がっているため、生成されたエンドポイントは次のようになります。`(0,2,0)`。 テレポーターは、`(0,1,0)`は決して目的地とはみなされません。 BFS は次から続きます。`(0,2,0)`そして最終的には報告する`-1`。 これは、落下中にテレポーターを通過するだけでは不十分であるというルールを直接強制します。 

縮退した 1 次元の場合```
3 1 1
R*T
```ロボットは固体の中央セルにもたれかかっています。 唯一有効な重力方向では、他の次元は両方ともサイズ 1 であるため、横方向に移動することはできません。 したがって、BFS は次の場所にエンドポイントを生成しません。`T`、そして答えは`-1`。 通常の 6 近傍グリッドの移動に基づく解決策では、実線の中央のセルを横切ることができないという事実が誤って無視されてしまいます。 

イルミネーションケース用```
3 3 1
-R-
-*-
-T-
```深度バッファは、実線の中央セルを関連するラインの最初の障害物として正確に識別します。 ロボットの座標が最初に表示されるブロックを超えると、方向は拒否されるため、BFS が照明された影を通る移動を誤って作成することはありません。 検索は 6 方向すべてを網羅し、結果を返します。`-1`。 

境界の場合```
2 2 1
RT
**
```ロボットは次から始まります`(0,0,0)`。 重力が増大するにつれて`y`、目に見えるサポートは次のとおりです。`y = 1`、つまりロボットは で休んでいます。`y = 0`。 進んでいく`x`に`(1,0,0)`到達する`T`一度の動きで。 深度バッファのエンドポイントはグリッド内に残るため、結果は正しくなります。`1`。 

最も一般的なオフバイワンエラーは、ソリッドブロック座標とロボット座標を混同することです。 正方向の最初の固体ブロックが`s`、ロボットは次の場所で休んでいます。`s - 1`ではありません`s`。 負の重力の場合、対応するエンドポイントは次のとおりです。`s + 1`。 実装は、BFS 状態の作成時とそのデコード時に一貫してこれらの式に従います。
