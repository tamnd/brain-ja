---
title: "CF 102201G - グッドセット"
description: "すべての (k) ビット整数のブール世界の内部で作業するため、(2^k) 個の可能な要素が存在します。 適切なセットとは、ビット単位の AND とビット単位の OR の両方で閉じられる、空ではない整数の集合です。"
date: "2026-08-18T01:44:10+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102201
codeforces_index: "G"
codeforces_contest_name: "Moscow Pre-Finals Workshop 2019. KAIST Contest"
rating: 0
weight: 102201
solve_time_s: 329
verified: true
draft: false
---

[CF 102201G - グッドセット](https://codeforces.com/problemset/problem/102201/G)

 **評価:** -
 **タグ:** -
 **解決時間:** 5 分 29 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 すべての (k) ビット整数のブール世界の内部で作業するため、(2^k) 個の可能な要素が存在します。 適切なセットとは、ビット単位の AND とビット単位の OR の両方で閉じられる、空ではない整数の集合です。 入力では、すべてがファミリーに属している必要があるいくつかの異なる整数が与えられます。タスクは、その要件を満たすすべての適切なファミリーをカウントすることです。 

重要な問題は、この宇宙には最大でも (128) 個の整数しか含まれないが、そのファミリーには (2^{128}) 個の異なる候補が存在する可能性があることです。 宇宙のサブセットを直接列挙することは不可能です。 小さい境界 (k\le 7) は、ビット位置の数が小さいことを示しています。 宇宙のサイズ (2^k) ではなく、ブール格子の副格子の構造を利用する必要があります。 

特別な注意が必要な境界ケースが 2 つあります。 まず、(n=0) は必要な整数がないことを意味します。 空でないすべての良いセットは有効であるため、空の要件は答えが (0) であることを意味しません。 たとえば、(k=1,n=0) の場合、有効なセットは ({0})、({1})、および ({0,1}) であるため、答えは (3) になります。 次に、シングルトンは常に AND と OR で閉じられます。 したがって、(k=2,n=1,a_1=0) の場合、シングルトン ({0}) をカウントする必要があります。 空の集合と完全な宇宙の両方が存在することを前提とした表現を構築する場合、シングルトン格子を忘れることは簡単な間違いです。 

「distinct」という言葉によって引き起こされる微妙なケースがもう 1 つあります。 (k=3,n=2) などの値 (1,1) を持つ入力は無効であるため、正当な入力では「すべての値が等しい」テストを行うことはできません。 関連する状況は (n=1) で、単一の必要な値は任意のビット パターンを持つことができます。 

## アプローチ

 明らかな強引な方法は、(2^k) 個の可能な整数のすべてのサブセットを列挙し、必要な値がすべて含まれているかどうかをテストし、要素のすべてのペアが AND と OR で閉じているかどうかをテストすることです。 (k=7) の場合でも、これは (2^{128}) 族を考慮することを意味しますが、これは手が届きません。 問題は、(128) 個の要素のスキャンを最適化することを求めているわけではありません。 そのためには、恣意的な家族を列挙することを完全に避ける必要があります。 

有益な観察は、適切なファミリはまさにブール格子の部分格子であるということです。 すべての有限部分格子には、そのすべてのメンバーの AND 演算によって得られる最小要素と、そのすべてのメンバーの OR 演算によって得られる最大要素があります。 常に (0) または常に (1) の座標はすぐに分離できます。 残りの座標は、実際に家族内で異なる座標です。 

次に、これらの変数座標について考えてみましょう。 適切なセットのすべてのメンバーが同じビットを与えている場合、2 つの座標は同等です。 同様に、それらは常に同時に発生します。 各同値クラスを 1 つの抽象座標で置き換えることができます。 この圧縮後、残りのすべての座標は格子の一部のメンバーによって真に区別できるため、結果として得られる部分格子はフルランクになります。 

ランク (r) のブール格子のフルランク部分格子は、(r) のラベル付き要素上で半順序を持つ全単射になります。 半順序が与えられた場合、その下向きに閉じられたサブセットをすべて取得します。 それらは交差と結合の下で閉じられており、順序が反対称であるため、すべての (r) 抽象座標を区別します。 逆に、フルランク副格子から、(y) を含むすべての格子要素が (x) も含む場合、(x\le y) を定義します。 結果として得られる関係は半順序であり、元の格子はまさにその理想系です。 

これにより、元の問題が非常に小さな列挙に変わります。 どのビット位置が可変であるかを選択し、それらの位置を等価クラスに分割してから、クラスの半順序を選択します。 必要な整数は、その半順序に 1 つの条件だけを課します。すべての必要な整数が理想に対応する必要があります。

固定パーティションの場合、各ブロックには、そのブロックがどの必須整数に含まれるかを記述する署名があります。 半順序関係 (x<y) は、(y) を含むすべての必須整数に (x) も含まれる場合にのみ許可されます。 その関係制約を明示的に構築するのではなく、部分順序自体を列挙します。 (k\le7) なので、ランク (1) から (7) までのラベル付き部分順序の数は (1,3,19,219,4231,130023,5941889) だけです。 

部分順序は再帰的に生成できます。 (r) ラベル付き頂点の半順序から開始し、新しい頂点を挿入します。 その先行要素はダウンセット (D) を形成し、後続要素はアップセット (U) を形成します。(D) のすべての要素は (U) のすべての要素の下になければなりません。 すべての有効なペア ((D,U)) は正確に 1 つの拡張子を与えるため、これによりすべてのラベル付き部分順序が正確に 1 回生成されます。 

最終的な実装では、すべての半順序のすべての理想が構築されるわけではありません。 頂点 (v) の場合、(down[v]) をその厳密な先行者とします。 サブセット (S) は、(v) を含んでいるものの、(down[v]) の一部のメンバーが欠落している場合、正確にはイデアルではありません。 すべての (2^r) サブセットを 1 つの Python 整数のビットとしてエンコードします。 これにより、少数のビット演算のみを使用して半順序の非理想のセットを計算し、それに対して 1 つの整数 AND を使用してすべてのパーティションをテストできます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | (O(2^{2^k}\cdot 2^{2k})) | (O(2^k)) | 遅すぎる |
 | 構造の列挙 | (O(P_7\cdot k\cdot Q_7)) | (O(P_7)) 生成された状態はストリーミングされます。 承認済み |

 ここで、(P_7=5,941,889) は 7 つの要素のラベル付き半順序の数、(Q_7) は入力に関連する個別の分割制約の数です。 (k\le7) の場合、これらの量はすべて、構造を列挙するには十分小さいです。 

## アルゴリズムのチュートリアル

 1. 必要な整数を読み取り、すべてのビット位置の署名を計算します。 座標 (b) の署名は入力値に対するビットセットであり、(a_i) に座標 (b) が含まれる場合にビット (i) が正確に設定されます。 署名が必要な値全体にわたって変化する座標は、有効なファミリ内で固定することはできませんが、一定の座標は固定されたままになるか、変数ブロックの一部になる可能性があります。 
2. すべての可変座標を含むすべての可変座標マスク (V) を列挙します。 外側の座標（V）は固定であり、その固定値は共通の入力パターンによってすでに決定されています。 
3. すべて (V) について、その座標のすべてのパーティションを空でないブロックに列挙します。 ブロックは、そのすべての座標が同じ入力署名を持つ場合にのみ有効です。 そうしないと、同じ同値クラス内の 2 つの座標が必要な整数によってすでに区別されていることになりますが、これは不可能です。 
4. 有効なパーティションごとに、必要な各整数をブロックのサブセットにマップします。 ブロック全体が必要な整数に存在する場合、サブセットにはブロック (j) が含まれます。 これらの必要なブロック サブセットのコレクションを 1 つの整数としてエンコードします。`req`ここで、要件の中にサブセット (S) が発生する場合、ビット (S) が設定されます。 グループは等しい`req`異なる座標パーティションが抽象部分順序に同じ条件を課す可能性があるためです。 
5. (0,1,\ldots,r-1) のすべての部分順序を再帰的に生成します。 新しい頂点 (r) を追加するときは、古い順序のダウンセット (D) を先行者として選択します。 可能な後続要素は、(D) のすべての要素の共通の上限を形成します。 共通上限セット内のアップセット (U) は有効な拡張を与えます。 
6. 生成されたすべての部分順序について、ビットセットを計算します。`bad`その設定ビットはまさに理想的ではないサブセットです。 すべての頂点 (v)、(v) を含むすべてのサブセットについて、すべてを含むわけではありません。`down[v]`悪いです。 すべての頂点にわたるこれらのセットの結合は、完全な非理想的なマスクです。 
7. パーティション制約`req`まさにそのとき満足する`req & bad == 0`。 この半順序ランクの答えに、満たされたすべての制約の多重度を加算します。 
8. ケース (n=0) は別個に処理されます。 構築を制約する入力署名がないため、ラベル付き半順序の事前計算数とスターリング数分割数を使用して、すべての可能な固定座標の選択とすべての可能な可変座標の分割をカウントします。 

すべての部分注文を処理した後、累積された数は、必要なすべての整数を含む個別の良好なセットの数となります。 

### なぜ効果があるのか

 すべての適切なセットには、変化する固有の座標セット、常に同一に動作するクラスへのそれらの座標の固有の分割、およびそれらのクラス上の固有のフルランク副格子があります。 後者は半順序によって一意に表現され、その理想はまさに良い集合の可能なメンバーです。 

入力制約は、そのブロック表現が選択された半順序の理想である場合に限り、必要な整数が表現された格子に属するため、正確に保存されます。 この構築では、考えられるすべての座標分解と考えられるすべての半順序が考慮されますが、結果として得られる適切なセットには、そのような正規分解が 1 つだけ含まれます。 したがって、すべての有効な良好なセットは 1 回カウントされ、無効なセットはカウントされません。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

# Number of labeled partial orders on 0..7 elements.
POSets = [1, 1, 3, 19, 219, 4231, 130023, 5941889]

def partitions(mask):
    """Yield every unordered partition of the set bits of mask."""
    if mask == 0:
        yield ()
        return

    bit = mask & -mask
    rest = mask ^ bit

    for p in partitions(rest):
        # Put bit into an existing block.
        for i in range(len(p)):
            q = list(p)
            q[i] |= bit
            yield tuple(q)

        # Start a new block. Blocks stay ordered by their minimum bit.
        yield (bit,) + p

def solve_case(k, a):
    n = len(a)

    if n == 0:
        # g[r] = number of sublattices of B_r containing both
        # the empty set and the full set.
        # g[r] = sum_j S(r,j) * POSets[j].
        stirling = [[0] * 8 for _ in range(8)]
        stirling[0][0] = 1
        for i in range(1, 8):
            for j in range(1, i + 1):
                stirling[i][j] = stirling[i - 1][j - 1] + j * stirling[i - 1][j]

        bounded = [0] * 8
        for r in range(8):
            bounded[r] = sum(
                stirling[r][j] * POSets[j]
                for j in range(r + 1)
            )

        ans = 0
        for r in range(k + 1):
            # Choose r variable coordinates. Every other coordinate
            # can independently be fixed to 0 or 1.
            ans += (
                (1 << (k - r))
                * __import__("math").comb(k, r)
                * bounded[r]
            )
        return ans

    # Signature of every original coordinate.
    # Bit i is set when coordinate b occurs in a[i].
    sig = [0] * k
    for i, x in enumerate(a):
        bit = 1 << i
        for b in range(k):
            if (x >> b) & 1:
                sig[b] |= bit

    # Coordinates with non-constant signatures must be variable.
    varying = 0
    for b in range(k):
        if sig[b] != 0 and sig[b] != (1 << n) - 1:
            varying |= 1 << b

    # queries[r] is a dictionary:
    #   required-ideal-mask -> number of coordinate partitions producing it
    queries = [dict() for _ in range(k + 1)]

    all_coords = (1 << k) - 1

    # Every variable mask must contain all genuinely varying coordinates.
    optional = all_coords ^ varying
    sub = optional

    while True:
        V = varying | sub

        for blocks in partitions(V):
            r = len(blocks)

            # Every block must consist of coordinates with identical
            # signatures among the required elements.
            block_sig = []
            valid = True

            for block in blocks:
                first = block & -block
                b0 = first.bit_length() - 1
                s = sig[b0]

                rest = block ^ first
                while rest:
                    bit = rest & -rest
                    b = bit.bit_length() - 1
                    if sig[b] != s:
                        valid = False
                        break
                    rest ^= bit

                if not valid:
                    break
                block_sig.append(s)

            if not valid:
                continue

            # Convert every required integer into its block mask.
            req = 0
            for i in range(n):
                mask = 0
                ibit = 1 << i

                for j, s in enumerate(block_sig):
                    if s & ibit:
                        mask |= 1 << j

                req |= 1 << mask

            queries[r][req] = queries[r].get(req, 0) + 1

        if sub == 0:
            break
        sub = (sub - 1) & optional

    # For r=0 there is exactly one partial order.
    # Its only subset is the empty set, which is always an ideal.
    answer = 0
    if queries[0]:
        # A legal r=0 representation is a singleton.
        answer += sum(queries[0].values())

    # Process all partial orders of every rank in one recursive generation.
    for target in range(1, k + 1):
        if not queries[target]:
            continue

        # We generate only up to this target. Since targets are processed
        # separately, the code remains simple and k <= 7 keeps this safe.
        qitems = list(queries[target].items())

        contain_all = [0] * (1 << target)
        subset_count = 1 << target
        full_subset_bits = (1 << subset_count) - 1

        for d in range(subset_count):
            x = 0
            s = d
            while s < subset_count:
                x |= 1 << s
                s += 1
            contain_all[d] = x

        # The loop above is intentionally replaced below by a direct
        # construction, which is faster for these tiny dimensions.
        for d in range(subset_count):
            x = 0
            for s in range(subset_count):
                if (s & d) == d:
                    x |= 1 << s
            contain_all[d] = x

        contains_vertex = [
            contain_all[1 << v]
            for v in range(target)
        ]

        local_answer = 0

        def process(down):
            nonlocal local_answer

            bad = 0
            for v in range(target):
                bad |= contains_vertex[v] & (
                    full_subset_bits ^ contain_all[down[v]]
                )

            for req, multiplicity in qitems:
                if (req & bad) == 0:
                    local_answer += multiplicity

        def generate(m, down):
            if m == target:
                process(down)
                return

            old_all = (1 << m) - 1

            up = [0] * m
            for v in range(m):
                mask = 0
                for w in range(m):
                    if (down[w] >> v) & 1:
                        mask |= 1 << w
                up[v] = mask

            size = 1 << m
            is_down = [False] * size
            is_up = [False] * size
            is_down[0] = True
            is_up[0] = True

            for s in range(1, size):
                bit = s & -s
                v = bit.bit_length() - 1
                rest = s ^ bit

                is_down[s] = (
                    is_down[rest]
                    and (down[v] & ~s) == 0
                )
                is_up[s] = (
                    is_up[rest]
                    and (up[v] & ~s) == 0
                )

            xbit = 1 << m

            for D in range(size):
                if not is_down[D]:
                    continue

                # U must consist only of elements strictly above every
                # member of D.
                C = old_all
                bits = D
                while bits:
                    bit = bits & -bits
                    v = bit.bit_length() - 1
                    C &= up[v]
                    bits ^= bit

                U = C
                while True:
                    if is_up[U]:
                        nd = list(down)
                        nd.append(D)

                        bits2 = U
                        while bits2:
                            bit = bits2 & -bits2
                            v = bit.bit_length() - 1
                            nd[v] |= xbit
                            bits2 ^= bit

                        generate(m + 1, tuple(nd))

                    if U == 0:
                        break
                    U = (U - 1) & C

        generate(0, ())
        answer += local_answer

    return answer

def main():
    k, n = map(int, input().split())

    if n:
        a = list(map(int, input().split()))
    else:
        a = []

    print(solve_case(k, a))

if __name__ == "__main__":
    main()
```の最初の部分`solve_case`ハンドル (n=0)。必要な署名情報はありません。 ランク (r) の有界部分格子の数は、(r) 座標を同値クラスに分割し、それらのクラスにフルランク部分順序を置くことによって取得されます。 スターリング数はパーティションをカウントします。 

(n>0) の場合、`sig[b]`どの必須値に座標 (b) が含まれるかを正確に記録します。 マスク`varying`必要な入力全体にわたって値が一定ではない座標を識別します。 このような座標はすべての有効な格子で可変でなければなりませんが、他のすべての座標は固定または可変にすることができます。 

の`partitions`ジェネレーターは、残っている最小の座標を使用して、ブロックを正規の順序に保ちます。 これにより、同じ順序付けされていないパーティションを何度もカウントすることがなくなります。 

辞書`queries[r]`元のビット位置と抽象ポセットの間のブリッジです。 単一の辞書キーは、理想的である必要があるすべての必要なサブセットを記述します。 その多重度は、まったく同じ抽象要件につながる異なる座標パーティションの数を記録します。 

再帰的`generate`関数は、新しい最大ラベル付き頂点を挿入することによって部分順序を構築します。`D`その前任者が含まれており、`U`その後継者たち。 先行セットはダウンセットでなければならず、後続セットはアップセットでなければならず、すべての先行セットはすべての後続セットの下にある必要があります。 これらの条件はまさに、結果として得られる関係を推移的なものにするものです。 

の`bad`bitset は最も便利な実装テクニックです。`contain_all[d]`を含むすべてのサブセット マスクが含まれます。`d`。 頂点 (v) の場合、式は次のようになります。`contains_vertex[v] & ~contain_all[down[v]]`(v) を含むが、少なくとも 1 つの先行要素が欠落しているすべてのサブセットを表します。 彼らの結合はまさに非理想の集合体である。 

Python の整数は任意の精度であるため、ここでのビットセットには (2^7=128) ビットを安全に含めることができます。 整数オーバーフローの問題はありません。 パーティションのサブセット マスクは最大 7 ビットを使用しますが、外側のマスクは`req`マスクは最大 (128) ビットを使用します。 

## 実用的な例

 ### サンプル 1

 最初のサンプルは (k=2,n=1,a_1=0) です。 必要な値は 1 つだけであるため、両方のビット位置は定数署名 (0) を持ちます。 固定することも、可変にすることもできます。 

| 変数座標 | パーティション | 抽象ブロックの数 | 0 | を含む有効な部分注文
 | --- | --- | --- | --- |
 | なし | 空のパーティション | 0 | 1 |
 | ビット0 | ({0}) | 1 | 1 |
 | ビット 1 | ({1}) | 1 | 1 |
 | 両方 | ({0,1}) | 1 | 1 |
 | 両方 | ({0}、{1}) | 2 | 3 |

 最初の 4 つの表現により、4 つの適切なセットが得られます。 最後のパーティションには 2 つのブロックがあり、2 つのラベル付き要素のすべての部分順序は理想として空のセットを持つため、3 つの部分順序はすべて機能します。 合計は (1+1+1+1+3=7) となり、サンプル出力と一致します。 

このトレースは、({0}) などのシングルトンをカウントする必要がある理由も示しています。 ゼロブロック表現は本物の適切なセットであり、無効な空のファミリではありません。 

### サンプル 2

 (k=4) と必要な値 (1、2、7) では、座標シグネチャが十分に異なるため、いくつかの座標が強制的に変数のままになります。 すべての候補変数マスクについて、分割ステップは、必要な署名が異なる座標を含むブロックを拒否します。 

存続するパーティションの場合、必要な各整数は抽象ブロックのサブセットになります。 次に、半次数ジェネレーターは、これら 3 つのサブセットすべてが理想的である次数のみをカウントします。 

| 必須の値 | 抽象的なブロックマスク |
 | --- | --- |
 | 1 | ビット 0 を含むブロックによって決定される |
 | 2 | ビット 1 を含むブロックによって決定される |
 | 7 | ビット 0、1、2 を含むブロックによって決定される |

 受け入れられたすべての部分順序は、抽象ブロックを元の座標に展開した後の 1 つの異なる適切なセットを表します。 すべての有効なパーティションと順序を合計すると、サンプル出力 (29) が得られます。 

ここで重要な不変条件は、ラティス全体を明示的に構築することによって必要な値が決してチェックされないということです。 そのメンバーシップは、その抽象ブロック サブセットが理想であるかどうかという 1 つの質問に集約されます。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O(P_7\cdot k + C_k\cdot P_7)) 最悪の構造列挙 | (P_7=5,941,889)、一方 (k\le7) および関連するパーティション制約の数は小さい |
 | スペース | (O(k2^k)) 再帰のほか | すべてのサブセット条件は最大 (128) ビットを使用し、部分的な注文は保存されるのではなくストリーミングされます。 

ブルート フォースとの決定的な違いは、このアルゴリズムでは、(128) 要素の宇宙の任意のサブセットではなく、最大 7 つの抽象座標で部分順序を列挙することです。 ラベル付き半順序数の最大値は約 590 万ですが、これは有限で管理可能であり、再帰的ジェネレーターがそれらすべてを同時に格納することはありません。 

## テストケース```python
# This test harness assumes the editorial solution has been placed above
# in a file named solution.py. For a standalone local test, copy the
# solve_case function and main implementation into the same file.

import sys
import io

# Reuse the solve_case function from the solution.
# The helper accepts exactly the input format used by the judge.
def run(inp: str) -> str:
    old_stdin = sys.stdin
    old_stdout = sys.stdout

    sys.stdin = io.StringIO(inp)
    sys.stdout = io.StringIO()

    try:
        k, n = map(int, input().split())
        a = list(map(int, input().split())) if n else []
        print(solve_case(k, a))
        return sys.stdout.getvalue().strip()
    finally:
        sys.stdin = old_stdin
        sys.stdout = old_stdout

# Provided samples
assert run("2 1\n0\n") == "7", "sample 1"
assert run("4 3\n1 2 7\n") == "29", "sample 2"

# Minimum k, one required value.
assert run("1 1\n0\n") == "2", "minimum size"

# Same boundary case, but requiring the other element.
assert run("1 1\n1\n") == "2", "upper boundary"

# Two extreme elements in B_2.
# The valid families are {0,3}, {0,1,3}, {0,2,3}, and the full B_2.
assert run("2 2\n0 3\n") == "4", "fixed minimum and maximum"

# Maximum-size input. Requiring every element forces the entire universe.
assert run(
    "7 128\n" +
    " ".join(map(str, range(128))) +
    "\n"
) == "1", "all elements required"

# No required elements.
assert run("7 0\n") == "12982681", "empty requirement"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`1 1 / 0`| 2 | 最小 (k)、シングルトンおよびフルセットのケース |
 |`1 1 / 1`| 2 | 対称の上限 |
 |`2 2 / 0 3`| 4 | 中間格子を含む必要な最小値と最大値 |
 |`7 128 / 0 ... 127`| 1 | 最大 (n)、一意の完全なユニバースを強制する |
 |`7 0`| 12982681 | 空の要件と個別の (n=0) カウント式 |

 ## 特殊なケース

 (k=1,n=1,a_1=0) の場合、入力には 2 つの可能な整数 (0) と (1) のみが含まれます。 (0) を含む適切なセットは ({0}) と ({0,1}) であるため、答えは (2) になります。 このアルゴリズムは、変数座標を持たない 1 つの表現と、単一の座標変数を含む 1 つの表現を取得します。 

(k=2,n=1,a_1=0) の場合、答えは (7) です。 ゼロブロック表現は ({0}) を与え、1 ブロック表現は ({0,1})、({0,2})、および ({0,3}) を与え、2 ブロック表現は 2 つの要素の 3 つの部分順序すべてに寄与します。 合計は(7)になります。 

必要な値 (0) と (3) を持つ (k=2,n=2) の場合、両方の座標が入力全体で変化するため、両方が変数部分に属している必要があります。 1 ブロックのパーティションでは ({0,3}) が得られます。 2 ブロックの分割により、2 つのラベル付き要素上の 3 つの部分順序に対応する 3 つのフルランクのサブ格子が得られます。 したがって、答えは(4)となります。 

(k=7,n=128) の場合、可能なすべての整数が必要です。 すべてのビット位置は変化するため、座標を固定することはできません。 さらに、入力には座標のすべてのペアを区別する値が含まれるため、すべての座標は独自の同値クラスを形成する必要があります。 必要なブロック サブセットは、7 つの抽象座標のすべて (128) サブセットです。 すべてのサブセットが理想となる唯一の部分順序はアンチチェーンであり、その理想的な格子はブール格子全体です。 したがって、答えはまさに (1) です。 

(n=0) の場合、署名情報はなく、すべての座標は (0) に固定されるか、(1) に固定されるか、変数同値クラスの 1 つに配置される可能性があります。 ランク (r) の場合、有界副格子の数は半次数のスターリング変換です。 これを可変座標の選択と固定座標の (2^{k-r}) 割り当てと組み合わせると、(k=7) に対して (12,982,681) 個の適切なセットが得られます。 このケースは、署名を導出するために必要な値がないため、署名ベースの要件チェックでは処理できません。
