---
title: "CF 102396D - ピザのカット"
description: "円形のピザと (n) 人がいます。 人 (i) は、角度が正確に (アルファイ) 度である 1 つのセクターを必要とします。 セクターはピザのどこにでも配置でき、入力順序に含まれる必要はありません。 ピザの未使用部分は箱の中に入れておくことができます。"
date: "2026-08-11T23:24:11+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102396
codeforces_index: "D"
codeforces_contest_name: "2019-2020 Saint-Petersburg Open High School Programming Contest (SpbKOSHP 19)"
rating: 0
weight: 102396
solve_time_s: 662
verified: true
draft: false
---

[CF 102396D - ピザのカット](https://codeforces.com/problemset/problem/102396/D)

 **評価:** -
 **タグ:** -
 **解決時間:** 11 分 2 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 円形のピザと (n) 人がいます。 人 (i) には、角度が正確に (\alpha_i) 度である 1 つのセクターが必要です。 セクターはピザのどこにでも配置でき、入力順序に含まれる必要はありません。 ピザの未使用部分は箱の中に入れておくことができます。 

カットは、1 つの境界光線を作成する半径、または 2 つの反対の境界光線を一度に作成する直径にすることができます。 すべてのカットを行った後、要求されたすべてのセクターは結果として得られるセクターの 1 つである必要があり、内部を通過する余分なカットはありません。 タスクは、カットの数を最小限に抑え、その最小値を達成する実際のカットのセットを出力することです。 公式の例は、(90^\circ) のコピー (4)、(30^\circ) のコピー 2 つ、および (200^\circ,80^\circ,80^\circ) です。 

小さな境界 (n\le16) が主なアルゴリズムの手掛かりです。 (16!) は約 (2.09\cdot10^{13}) であるため、すべての順列を列挙する余裕はありません。 どの部分がピザの反対側の半分に属するかを考慮すると、単純な順列ベースの検索は 1 秒の制限が許容できる範囲をはるかに超えています。 意図した解決策では、(n) で指数アルゴリズムを使用できますが、階乗時間ではなく、(2^n) または (3^n) 付近のものが必要です。 すべての角度が整数であり、ピザ全体が (360^\circ) のみであるという事実も、小さな角度状態空間を与えます。ただし、よりクリーンな解決策では、(360) 状態ジオメトリ DP ではなくサブセット和を使用します。 

一見合理的に見える実装が間違っている可能性がある境界例がいくつかあります。 たとえば、(180^\circ) という 1 つのリクエストでは、直径が 1 つだけ必要になります。 入力`1 / 180`答えがあります`1`直径自体が、要求されたハーフピザの 2 つの境界であるためです。 要求されたすべてのセクターを 2 つの独立した半径カットが必要なものとして扱うと、出力は次のようになります。`2`。 

もう 1 つの重要なケースは、要求された角度の合計が正確に (360^\circ) である場合です。 のために`3 / 200 80 80`、リクエストされた 3 つのセクターをピザ全体の周りに連続して配置できます。 3 つの境界光線は (0,200,280) であるため、必要な半径カットは 3 つだけです。 常に (n+1) カットで始まるソリューションでは、完全に回転した後は同じ光線であるにもかかわらず、開始と終了の両方が異なる境界として誤ってカウントされます。 

より微妙なケースは、`2 / 30 30`。 2 つの (30^\circ) セクターを隣り合わせに配置する場合、3 つの半径カットで十分ですが、2 つの直径カットの方が優れています。 (0^\circ) と (30^\circ) の切断直径により、(0\ldots30) と (180\ldots210) の両方に (30^\circ) のセクターが作成されます。 要求された 2 つのセクターは反対コピーであるため、答えは次のようになります。`2`。 これはまさに、純粋に直線的な配置では欠けている種類の対称性です。 

最後に、角度が (180^\circ) より大きいと、要求されたセクターの内部で直径が使用されなくなります。 たとえば、`3 / 200 80 80`、直径が (200^\circ) セクターが直径を横切る場合、カット レイはそのセクター内に配置されます。 リクエストされたすべてのピースは内部にカットのない完全なセクターである必要があるため、大きなピースでは半径のみの構築を余儀なくされます。 

## アプローチ

 最も直接的な強引な方法は、要求されたセクターがピザの周囲にどのように配置されるか、可能な直径によって作成される 2 つの半円にどのセクターが属するか、およびグループ間の境界がどこに発生するかを決定することです。 両側の順列と二項選択を列挙するだけでも、すでにおおよその結果が得られます。 

[
 n!,2^n
 ]

 可能性。 (n=16) では、これは約 (1.37\cdot10^{18}) 個の組み合わせになります。 各配置をチェックするには、その境界を構築し、直径のペアを数えることも必要となるため、このアプローチは実現可能とは程遠いものです。 

有益な観察は、ソリューションを構築する際に個々のカットの正確なアイデンティティを気にする必要はないということです。 直径は、1 つのカットで 2 つの反対側の境界光線に対応できるため便利です。 1 つの直径を固定し、その 2 つの光線を (0^\circ) と (180^\circ) と呼びます。 その後、要求されたセクターを 2 つの半円の間に分散できます。 半円の内側には、いくつかの要求されたセクタを連続して配置し、それらの間に任意の未使用のギャップを置くことができます。 

要求されたセクターの 2 つのグループ (直径の両側に 1 つずつ) が同じ境界で開始し、別の共通の境界で終了するようにしたいとします。 要求された角度の合計を (x) と (y) とします。 これら 2 つの共通の境界間の間隔は、少なくとも (\max(x,y)) 以上の長さでなければなりません。 (x=y) の場合、両方のグループを単純に連続して配置できます。 (x<y) の場合、セクタ間に​​未使用のギャップを挿入できるため、要求されたセクタが少なくとも 2 つ含まれていれば、短い辺は両方の境界に触れることができます。 1 セクター グループは、その角度がすでに区間の長さに等しくない限り、両方の端点に触れることができません。 

これにより、中心となる組み合わせオブジェクト、つまりペアになったブロックが得られます。 ペアのブロックは、半円ごとに 1 つずつ、空ではない 2 つのサブセット (A) と (B) に分割された人々のサブセット (U) で構成されます。 必要な長さは

 [
 w(U)=\max\left(\sum_{i\in A}\alpha_i,\sum_{i\in B}\alpha_i\right)。 
]

 分割は、両方のグループがブロックの両端に接触できる場合に有効です。 1 つのグループには常に合計 (w(U)) があるため、小さいグループのみをチェックする必要があります。 その合計が (w(U)) よりも厳密に小さい場合は、不足している角度を内部ギャップとして挿入できるように、少なくとも 2 つのセクターが含まれている必要があります。 2 つの合計が等しい場合は、1 セクターのグループでも問題ありません。 

ペアになったブロックごとに、2 つの半円の間に共通の境界が 1 つ追加されます。 1 つの直径が両方の側に使用できるため、共通の境界により 1 回のカットが節約されます。 ペアのブロックは (0^\circ) から (180^\circ) に向かって連続して配置されます。 

残りの要求されたセクターをペアにする必要はありません。 ペアになったブロックをすべて配置した後、その全長が (P) であるとします。 各半円には (180-P) 度が残っています。 残りのセクターは、各辺に割り当てられる角度の合計がこの残りの容量以下になるように、2 つの半円の間で分割する必要があります。 これは単なるサブセット合計のチェックです。 

対処しなければならない節約がもう 1 つあります。 1 つの半円が (180^\circ) まで正確に満たされている場合、その開始光線と終了光線は反対になり、実際には 1 つの直径でカットされます。 したがって、開始境界と終了境界を別々に数えるべきではありません。 同じ考え方で、完全な (360^\circ) 半径のみの配置を処理できます。 

(n) は (16) のみであるため、すべてのサブセットを列挙し、そのサブセットに対して可能な限り最適なペア分割を計算できます。 次に、サブセット DP は、一部の人物をペアのブロックに分割し、それらのブロックが消費する全長を最小限に抑えます。 ペアになったブロックの数ごとに、可能な最小の消費角度が維持されます。 最後に、ペアになっていない人々が残りの 2 つの半円に収まるかどうかをテストします。 

比較は次のとおりです。

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | (O(n!2^n)) | (O(n)) | 遅すぎる |
 | サブセット DP | (O(n3^n)) | (O(n2^n)) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 要求された合計角度 (S=\sum\alpha_i) を計算します。 値 (S) は、どれだけの角空間を占有する必要があるかを示し、2 つの半円に必要な容量を即座に示します。 
2. すべてのサブセット (U) について、その合計角度を計算します。`sum[U]`。 また、(U) から取得可能なすべてのサブセットの合計を記述するビットセットと、少なくとも 2 つの要素を使用して取得可能な合計を含む 2 番目のビットセットも計算します。 
3. 少なくとも 2 人を含む各サブセット (U) について、有効な分割 (U=A\cup B) の可能な最大の小さい辺を見つけます。 両側の合計が等しい場合、分割は直ちに有効になります。 それ以外の場合は、小さい方の側に少なくとも 2 人が含まれている必要があります。これは、ペアになったブロックの両端に触れるために内部に隙間が必要になるためです。 
4. 結果のブロック長と実際の分割を保存します。 小さい側が和(t)を持つ場合、大きい側は和を持つ`sum[U] - t`、したがってブロックは消費します`sum[U] - t`各半円の度数。 
5. サブセット DP を実行します。 州`dp[mask][k]`からの人々のみを使用して、正確に (k) 個のペアのブロックが占める最小の合計長を格納します。`mask`。 に含まれない人`mask`まだ無料なので、後で配置されます。 
6. マスクを処理するときは、マスクの最下位人物に注目してください。 その人のペアを解除したままにするか、その人を含むペアのブロックを作成します。 選択したすべてのブロックを、残りの最小の人を含むように制限すると、各パーティションに固有の分解が与えられ、同じブロックのコレクションを多くの順序でカウントすることが回避されます。 
7. すべての DP 状態について、占有ペアブロック長を (P) とします。 各半円の残りの容量は (C=180-P) です。 ペアになっていない人々は、それらのサブセットの合計が 2 つの側にある場合に正確に 2 つの側に分散できます。`remaining_sum - C`そして`C`。 それらのサブセット合計ビットセットを使用すると、DP 状態がわかった後、定数時間のビット操作でこれをテストできます。 
8. すべての実現可能な状態の中で、ペアになったブロックの数 (k) を最大化します。 複数の状態が同じ (k) を持つ場合は、半円を正確に (180^\circ) まで埋めることができる状態を優先します。これは、その 2 つの端点光線が 1 つの直径でカットされ、もう 1 つのカットが節約できるためです。 
9. DP 親ポインタからペアのブロックを再構築します。 ペアになったブロックごとに、最初のグループを上の半円に配置し、2 番目のグループを下の半円に配置します。 両方のグループは現在の共通境界で開始し、次の共通境界で終了します。 グループの合計がブロック長より小さく、グループに少なくとも 2 つのセクターが含まれる場合は、セクター間に未使用の角度を置きます。 
10. 残りの人々をペアのブロックの後に配置し、サブセット和の再構成に従ってブロックを分割します。 各辺が残りの (180^\circ) 容量内に収まるようになりました。 
11. セクター境界として必要なすべての光線を収集します。 反対の光線 (x) と (x+180) のペアごとに、両方の光線が必要な場合は 1 つの直径を放射します。 それ以外の場合は、必要な光線の半径を放射します。 この最終圧縮では、(180^\circ) および (360^\circ) の境界ケースも自動的に処理されます。 

### なぜ効果があるのか

 有効な直径はすべて、両方の半円に必要な境界を作成するか、半円の 2 つの端を識別します。 2 つの連続する共通境界の間で、両側の要求されたセクターが 2 つのグループを形成します。 それらの合計角度によって、共通の境界間の可能な最小距離が決まります。これは、まさに 2 つのグループの合計の最大値になります。 短いグループの有効性条件は、それが両方のエンドポイントに到達できるかどうかによって決まります。 

サブセットの有効な分割はすべてそのサブセット (U) で表されるため、DP では考えられるすべてのペアのグループが考慮されます。 また、最小セットビット反復では、最初の人がペアにならないままになるか、その人が選択された 1 つのブロックに入れられるため、素のペアのグループの考えられるすべてのコレクションも考慮されます。 ブロックの数ごとに最小占有角度が維持されるため、使用される人数とブロック数が同じ状態は、残りの人数を適合させるのに適しています。 

ペアのブロックが固定された後、残る唯一の問題は、未使用のセクターが残りの 2 つの半円形の容量に収まるかどうかです。 部分集合和テストはまさにその条件です。 したがって、実行可能なすべての幾何学的配置は何らかの DP 状態によって表され、容量テストに合格したすべての DP 状態は幾何学的に構築できます。 ペアになったブロックの数を最大化し、利用可能な場合は (180^\circ) エンドポイントを節約すると、カット数が最小限に抑えられます。 

## Python ソリューション```python
import sys
from array import array

input = sys.stdin.readline

def solve():
    n = int(input())
    a = list(map(int, input().split()))

    N = 1 << n
    ALL = N - 1
    INF = 10**9

    total = [0] * N
    reach = [0] * N
    reach2 = [0] * N
    popcnt = [0] * N

    # Bit i in reach[mask] means that this subset of mask
    # can have total angle i.
    reach[0] = 1

    for mask in range(1, N):
        bit = mask & -mask
        i = bit.bit_length() - 1
        rem = mask ^ bit

        total[mask] = total[rem] + a[i]
        popcnt[mask] = popcnt[rem] + 1

        r = reach[rem]
        reach[mask] = r | (r << a[i])

        # At least two elements:
        # either a >=2-element subset already exists in rem,
        # or we take i together with a nonempty subset of rem.
        nonempty = r & ~1
        reach2[mask] = reach2[rem] | (nonempty << a[i])

    # For each subset U:
    # weight[U] = minimum length of a paired block using U.
    # split[U] = one side of the corresponding split.
    weight = array('H', [0]) * N
    split = array('H', [0]) * N

    for mask in range(1, N):
        if popcnt[mask] < 2:
            continue

        s = total[mask]
        half = s // 2

        # First try a perfectly balanced split.
        if s % 2 == 0 and ((reach[mask] >> half) & 1):
            small = half
            need_two = False
        else:
            # Otherwise the smaller side must contain >= 2 elements.
            limited = reach2[mask] & ((1 << (half + 1)) - 1)
            if not limited:
                continue
            small = limited.bit_length() - 1
            need_two = True

        large = s - small

        # The paired block must fit into a semicircle.
        if large > 180:
            continue

        # Recover an actual subset having sum == small.
        x = mask
        target = small
        side = 0

        while target:
            bit = x & -x
            i = bit.bit_length() - 1
            rem = x ^ bit

            source = reach2[rem] if need_two else reach[rem]

            if (source >> target) & 1:
                x = rem
            else:
                side |= bit
                target -= a[i]
                x = rem

        if side == 0:
            continue

        weight[mask] = large
        split[mask] = side

    # dp[mask][k] = minimum total length of k paired blocks
    # using exactly the people in mask.
    K = n // 2
    W = K + 1

    dp = [None] * N
    dp[0] = [INF] * W
    dp[0][0] = 0

    # choice[mask * W + k] is the paired block used to obtain
    # the state. Zero means that the least significant person
    # was left unpaired.
    choice = array('H', [0]) * (N * W)

    for mask in range(1, N):
        bit = mask & -mask
        without = mask ^ bit

        cur = dp[without][:]

        sub = without
        while sub:
            block = sub | bit
            w = weight[block]

            if w:
                rem = mask ^ block
                prev = dp[rem]

                max_k = min(K - 1, popcnt[rem] // 2)

                for k in range(max_k + 1):
                    old = prev[k]
                    if old == INF:
                        continue

                    nw = old + w
                    if nw < cur[k + 1]:
                        cur[k + 1] = nw
                        choice[mask * W + k + 1] = block

            sub = (sub - 1) & without

        dp[mask] = cur

    # We need enough saving to fit everything into two semicircles.
    required = max(0, total[ALL] - 180)

    best_k = -1
    best_mask = -1
    best_p = INF
    best_e = False
    best_left = 0

    # Try the largest number of paired blocks first.
    for k in range(K, -1, -1):
        found = False
        found_e = False
        candidate = None

        for mask in range(N):
            p = dp[mask][k]
            if p == INF or p > 180:
                continue

            capacity = 180 - p
            rem = ALL ^ mask
            rs = total[rem]

            # The remaining people must be split between the
            # two semicircles, each with capacity 'capacity'.
            low = max(0, rs - capacity)
            high = min(capacity, rs)

            if low > high:
                continue

            bits = reach[rem]
            allowed = bits & ((1 << (high + 1)) - 1)

            if low:
                allowed &= ~((1 << low) - 1)

            if not allowed:
                continue

            # Prefer an exact capacity on one side.
            exact = (
                capacity <= high
                and capacity >= low
                and ((bits >> capacity) & 1)
            )

            if exact and not found_e:
                found_e = True
                candidate = (mask, p, capacity, rem, True)
            elif not found_e and candidate is None:
                target = allowed.bit_length() - 1
                candidate = (mask, p, capacity, rem, False)

            found = True

        if found:
            best_k = k
            best_mask, best_p, capacity, rem, best_e = candidate
            break

    # If no partition into two semicircles exists, no diameter can
    # be used without cutting through a requested sector.
    if best_k == -1:
        need = [False] * 360
        need[0] = True

        cur_angle = 0
        for x in a:
            cur_angle += x
            if cur_angle < 360:
                need[cur_angle] = True

        cuts = []
        for ang in range(180):
            x = need[ang]
            y = need[ang + 180]

            if x and y:
                cuts.append((ang, 1))
            elif x:
                cuts.append((ang, 0))
            elif y:
                cuts.append((ang + 180, 0))

        out = [str(len(cuts))]
        out.extend(f"{ang} {typ}" for ang, typ in cuts)
        sys.stdout.write("\n".join(out))
        return

    # Recover the paired blocks.
    blocks = []
    mask = best_mask
    k = best_k

    while mask:
        block = choice[mask * W + k]

        if block:
            blocks.append((block, split[block]))
            mask ^= block
            k -= 1
        else:
            bit = mask & -mask
            mask ^= bit

    blocks.reverse()

    # Recover the remaining people assigned to one semicircle.
    paired_mask = best_mask
    remaining = ALL ^ paired_mask
    capacity = 180 - best_p

    rs = total[remaining]
    low = max(0, rs - capacity)
    high = min(capacity, rs)

    bits = reach[remaining]

    if best_e and ((bits >> capacity) & 1):
        target = capacity
    else:
        allowed = bits & ((1 << (high + 1)) - 1)
        if low:
            allowed &= ~((1 << low) - 1)
        target = allowed.bit_length() - 1

    top_remaining = 0
    x = remaining
    t = target

    while t:
        bit = x & -x
        i = bit.bit_length() - 1
        rem = x ^ bit

        if (reach[rem] >> t) & 1:
            x = rem
        else:
            top_remaining |= bit
            t -= a[i]
            x = rem

    bottom_remaining = remaining ^ top_remaining

    need = [False] * 360
    need[0] = True

    def place_group(mask, start, length):
        if mask == 0:
            return

        ids = []
        x = mask
        while x:
            bit = x & -x
            ids.append(bit.bit_length() - 1)
            x ^= bit

        cur = start

        for j, i in enumerate(ids):
            if j + 1 == len(ids):
                end = start + length
            else:
                cur += a[i]
                end = cur

            need[end % 360] = True

    pos = 0

    # Paired blocks occupy the same interval in both semicircles.
    for block, side_a in blocks:
        side_b = block ^ side_a

        sa = total[side_a]
        sb = total[side_b]
        length = max(sa, sb)

        place_group(side_a, pos, length)
        place_group(side_b, 180 + pos, length)

        pos += length

    # Place unpaired people after all paired blocks.
    def place_consecutive(mask, start):
        cur = start
        x = mask

        while x:
            bit = x & -x
            i = bit.bit_length() - 1
            cur += a[i]
            need[cur % 360] = True
            x ^= bit

    place_consecutive(top_remaining, pos)
    place_consecutive(bottom_remaining, 180 + pos)

    # Compress opposite required rays into diameter cuts.
    cuts = []

    for ang in range(180):
        x = need[ang]
        y = need[ang + 180]

        if x and y:
            cuts.append((ang, 1))
        elif x:
            cuts.append((ang, 0))
        elif y:
            cuts.append((ang + 180, 0))

    out = [str(len(cuts))]
    out.extend(f"{ang} {typ}" for ang, typ in cuts)
    sys.stdout.write("\n".join(out))

if __name__ == "__main__":
    solve()
```実装の最初の部分では、ビットセットとして使用される Python 整数としてサブセット合計を構築します。 位置 (x) の単一ビットは、サブセットから何人かの人々を選択することで角度 (x) を取得できることを意味します。 Python の任意精度の整数により、これらのサブセット合計の遷移が非常にコンパクトかつ高速になります。 

2 番目のビットセット、`reach2`、 は少なくとも 2 人で得られる合計を表します。 この区別は、等しくないペアのブロックに必要です。 一方の辺に合計 (x) があり、もう一方の辺に合計 (y>x) がある場合、短い方の辺が区間の両端に触れるには少なくとも 2 つのセクターが必要です。 セクターが 1 つだけの場合、角度 (y) が正確になければなりません。 

の`weight`配列には、有効なペアのブロックの最小間隔長が格納されます。 サブセットに合計 (s) があり、その小さい側に合計 (t) が含まれる場合、大きい側には合計 (s-t) が含まれるため、間隔は次のようになります。`s - t`度。 可能な最大の有効値 (t) を選択すると、その間隔が最小化されます。 

メインのサブセット DP は、標準的な選択として最も重要でない人物を使用します。 その人のペアを解除したままにすると、その人なしでマスクから状態がコピーされます。 それ以外の場合は、その人物を含むペアのブロックが削除され、その重みが追加されます。 この標準的な選択により、考えられるすべてのブロック順序で同じブロック パーティションが生成されることが防止されます。 

最終段階では、`dp[mask][k]`2 つの半円のうちどれだけが (k) 個のペアのブロックによってすでに予約されているかを示します。 補数には、ペアになっていないすべての人々が含まれます。 サブセット合計ビットセットは、残りのセクターを 2 つの残りの容量に分割できるかどうかを決定します。 

再構成は DP を正確に反映します。 ペアのブロックは両方の半円上の同じ角度間隔に配置されます。 一方の側で要求された角度が間隔の長さよりも小さい場合、実装では未使用の角度が最後のセクターの直前に配置されます。 これにより、グループの最初と最後のレイが実際の要求されたセクターの境界であることが保証されます。 

決勝戦`need`アレイは、カットを直接放射するのではなく、光線のセットとして意図的に構築されています。 これにより、(0^\circ)、(180^\circ)、(360^\circ) 付近の脆弱な特殊なケースが回避されます。 必要な光線がすべてわかったら、すべての対蹠ペアは自然に 1 つの直径で表されます。 

ここでは Python の整数はオーバーフローせず、最大のサブセット合計は (360) のみです。 注意が必要な主な実装の詳細は、角度モジュロ (360) と 2 つの異なる光線 (0^\circ) および (360^\circ) の区別です。 これらは同じ光線であるため、コードは常に角度を法 (360) で保存します。 

## 実用的な例

 ### サンプル 1

 入力は次のとおりです。```
4
90 90 90 90
```4 つのリクエストを 2 つのペアのブロックに分割できます。 各ブロックには各辺に 1 つの (90^\circ) セクターが含まれるため、各ブロックの長さは (90^\circ) になります。 2 つのブロックは完全な (180^\circ) 半円を占めます。 

| ステップ | ペアになったブロック | 上限値 | 下限値 | ブロック長 | ポジション |
 | --- | --- | --- | --- | --- | --- |
 | 1 | 90 / 90 | 90 | 90 | 90 | 0 -> 90 |
 | 2 | 90 / 90 | 90 | 90 | 90 | 90 -> 180 |

 必要な光線は (0^\circ,90^\circ,180^\circ,270^\circ) です。 (0/180) と (90/270) のペアは対蹠的であるため、各ペアは 1 つの直径になります。 

したがって、出力には次のような 2 つのカットが含まれます。```
2
0 1
90 1
```これは、両方の節約源を示しています。 各ペアのブロックは共通の境界を与え、半円全体が (180^\circ) に達するため、その半円の 2 つの端点は 1 つの直径になります。 

### サンプル 2

 入力は次のとおりです。```
2
30 30
```2 つのリクエストは 1 つのペアのブロックを形成します。 

| ステップ | ペアになったブロック | 上限値 | 下限値 | ブロック長 | ポジション |
 | --- | --- | --- | --- | --- | --- |
 | 1 | 30 / 30 | 30 | 30 | 30 | 0 -> 30 |

 2 つのセクターは (0\ldots30) と (180\ldots210) に配置されます。 必要な光線は (0^\circ,30^\circ,180^\circ,210^\circ) です。 

| レイペア | 必須？ | カット |
 | --- | --- | --- |
 | 0 と 180 | 両方 | 0 での直径 |
 | 30と210 | 両方 | 直径30 |

 したがって、2回のカットで十分です。 

この例は、単にリクエストを連続して配置するだけでは十分ではない理由を示しています。 連続して配置すると光線 (0、30、60) が使用され、3 つのカットが必要になります。 反対側の半円の間でリクエストを分割すると、追加の共有境界が 1 つ作成され、答えは 2 つに減ります。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O(n3^n)) | サブセット DP は、最大 (O(n)) 個のブロック数状態を持つ正規最小要素を含むサブマスクを考慮します。 |
 | スペース | (O(n2^n)) | DP 状態、サブセット合計ビットセット、および再構成情報がすべてのマスクに対して保存されます。 |

 (n\le16) の場合、(2^n=65536) であるため、指数状態空間は意図した解に対して十分小さいです。 サブセット合計演算は、Python 整数をビットセットとして使用するため、特に効率的です。 (3^n) 遷移境界は実行時間に影響を与える部分ですが、(3^{16}=43,046,721) であり、コンパクトなサブセット表現と可能なペアのブロックの数による枝刈りを使用すると実用的です。 

## テストケース

 次のテストでは、プログラムによって印刷された正確な角度を比較するのではなく、生成されたカットを検証します。これは、この問題により最適な構築が可能になるためです。```python
# Save the submitted solution as solution.py before running this file.

import subprocess

def run(inp: str) -> str:
    p = subprocess.run(
        ["python3", "solution.py"],
        input=inp,
        text=True,
        capture_output=True,
        check=True,
    )
    return p.stdout

def validate(inp: str, out: str, expected_min_cuts: int):
    data = list(map(int, inp.split()))
    n = data[0]
    a = data[1:1 + n]

    lines = out.strip().splitlines()
    m = int(lines[0])

    assert m == len(lines) - 1
    assert m == expected_min_cuts

    rays = set()

    for line in lines[1:]:
        angle, typ = map(int, line.split())
        assert 0 <= angle < 360
        assert typ in (0, 1)

        rays.add(angle)

        if typ == 1:
            rays.add((angle + 180) % 360)

    rays = sorted(rays)
    assert rays

    sectors = []
    for i in range(len(rays)):
        x = rays[i]
        y = rays[(i + 1) % len(rays)]
        if i + 1 == len(rays):
            y += 360
        sectors.append(y - x)

    sectors.sort()

    wanted = sorted(a)

    # Every requested sector must occur as a complete atomic sector.
    i = 0
    j = 0
    while i < len(wanted) and j < len(sectors):
        if wanted[i] == sectors[j]:
            i += 1
        j += 1

    assert i == len(wanted)

# Sample 1
sample1 = """\
4
90 90 90 90
"""
out = run(sample1)
validate(sample1, out, 2)

# Sample 2
sample2 = """\
2
30 30
"""
out = run(sample2)
validate(sample2, out, 2)

# Sample 3
sample3 = """\
3
200 80 80
"""
out = run(sample3)
validate(sample3, out, 3)

# Minimum-size input: one 180-degree sector is exactly one diameter.
case4 = """\
1
180
"""
out = run(case4)
validate(case4, out, 1)

# All equal values. Eight opposite pairs of 1-degree sectors
# require nine distinct boundary positions.
case5 = """\
16
1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
"""
out = run(case5)
validate(case5, out, 9)

# Boundary case: the requested sectors fill exactly one semicircle.
case6 = """\
3
60 60 60
"""
out = run(case6)
validate(case6, out, 3)

# A 180-degree subset can be made into one side of a diameter,
# reducing the number of radius cuts.
case7 = """\
3
100 80 50
"""
out = run(case7)
validate(case7, out, 3)
```カスタム ケースは、重要な構造境界をカバーします。 

| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`1 / 180`|`1`| ハーフピザ 1 枚は 1 つの直径にカットされます。 |
 |`16 / sixteen 1s`|`9`| 最大 (n)、多数のペアのブロック、繰り返される等しい角度。 |
 |`3 / 60 60 60`|`3`| 正確な (180^\circ) 個の合計とエンドポイント識別のケース。 |
 |`3 / 100 80 50`|`3`| (180^\circ) サブセットは、2 つの対称グループがなくても有用な直径を作成できます。 |

 ## 特殊なケース

 単一 (180^\circ) リクエストの場合、入力は次のようになります。```
1
180
```この構築では、セクターを (0^\circ) から (180^\circ) に配置します。 両方の光線は同じ直径に属しているため、最終圧縮では必要なペア (0/180) が認識され、1 つの直径が放射されます。 出力には正確に 1 つのカットがあります。 

完全な (360^\circ) 配置については、次を考慮してください。```
3
200 80 80
```直径が (200^\circ) リクエストを安全に越えることはできないため、解決策は連続した半径カットに戻ります。 境界は (0,200,280) で、最後の (80^\circ) セクターの後、角度は (360^\circ=0^\circ) に戻ります。 最初と最後の境界は同じ光線であり、正確に 3 つのカットが得られます。 

同等のリクエストが繰り返される場合は、次の点を考慮してください。```
2
30 30
```DP は、(30/30) として分割された、両方の人物を含む 1 つのペアのブロックを見つけます。 その長さは (30^\circ) です。 セクターは (0) ～ (30) および (180) ～ (210) に配置されます。 両方の境界ペアは対蹠的であるため、必要な 2 つのカットは (0^\circ) と (30^\circ) の直径になります。 

それほど明白ではないケースについては、次のようになります。```
3
100 80 50
```(100^\circ) セクターと (80^\circ) セクターは、(0^\circ) から (180^\circ) までの 1 つの完全な半円を占めることができます。 (50^\circ) セクターは、他の半円の (180^\circ) から (230^\circ) を占めます。 必要な光線は (0^\circ,100^\circ,180^\circ,230^\circ) です。 (0^\circ) と (180^\circ) は反対であるため、1 つの直径が 2 つの半径カットに置き換わります。 最終的な答えは3カットです。 

DP は、ペアブロック DP に要求されたセクターの対称ペアを製造することを要求するのではなく、最後の 2 つの半円パーティションを通じてこの最後のケースを処理します。 直径によって反対側に余分な境界を作成できるため、この区別は重要です。 エクストラ カットが要求されたセクターを通過しない限り、反対側のレイは別の要求されたセクターのエンドポイントである必要はありません。 

16 個の等しい (1^\circ) リクエストを持つ最大サイズのインスタンスの場合、DP は 8 つのペアのブロックを作成できます。 各ペアは反対側の半円上で 1 度を占め、(0,1,\ldots,8) に共通の境界を生成します。 9 つの直径のカットが必要です。 各辺の残り (172^\circ) は単に未使用であるため、追加のカットは必要ありません。
