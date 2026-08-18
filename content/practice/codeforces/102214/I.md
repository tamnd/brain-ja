---
title: "CF 102214I - 画像"
description: "ピクセルが 2 桁の 16 進値として書き込まれるバイトである大きなグレースケール イメージ (I) と、小さなテンプレート イメージ (T) があります。 テンプレートは大きな画像から切り取られた可能性がありますが、非可逆圧縮のため、そのピクセルは正確に一致する必要はありません。"
date: "2026-08-18T11:35:28+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102214
codeforces_index: "I"
codeforces_contest_name: "\u041e\u0442\u043a\u0440\u044b\u0442\u043e\u0435 \u043b\u0438\u0447\u043d\u043e\u0435 \u043f\u0435\u0440\u0432\u0435\u043d\u0441\u0442\u0432\u043e \u0418\u041a\u0418\u0422 \u0421\u0424\u0423 \u043f\u043e \u043f\u0440\u043e\u0433\u0440\u0430\u043c\u043c\u0438\u0440\u043e\u0432\u0430\u043d\u0438\u044e 2015"
rating: 0
weight: 102214
solve_time_s: 217
verified: true
draft: false
---

[CF 102214I - 画像](https://codeforces.com/problemset/problem/102214/I)

 **評価:** -
 **タグ:** -
 **解決時間:** 3 分 37 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 ピクセルが 2 桁の 16 進値として書き込まれるバイトである大きなグレースケール イメージ (I) と、小さなテンプレート イメージ (T) があります。 テンプレートは大きな画像から切り取られた可能性がありますが、非可逆圧縮のため、そのピクセルは正確に一致する必要はありません。 

テンプレートが適合するすべての可能な左上の位置 ((x,y)) について、すべてのテンプレート ピクセル (T(i,j)) と対応する画像ピクセル (I(x+i,y+j)) を比較します。 スコアは差の二乗の合計です。 

[
 SSD(x,y)=\sum_{i=0}^{M-1}\sum_{j=0}^{N-1}
 (I(x+j,y+i)-T(j,i))^2。 
】

 必要な出力は、最小スコアを持つ任意のポジションです。 座標はゼロベースなので、(0\le x\le W-N) および (0\le y\le H-M) となります。 入力ピクセルは 16 進数ですが、解析後は 0 ～ 255 の通常の整数になります。 

大きな画像には最大 (1024\cdot768=786432) ピクセルを含めることができますが、テンプレート自体もほぼ同じ大きさにすることができます。 あらゆる可能な位置での直接比較により、結果的に数百億のピクセル操作を実行できます。 画像次元の 2 次または 4 次アルゴリズムは、4 秒の制限の下では現実的ではありません。 すべてのテンプレート相関を同時に計算する必要がありますが、これはまさに畳み込みと FFT が役立つ種類の操作です。 

直接実装では誤って処理される可能性のある境界ケースがいくつかあります。 テンプレートの寸法が画像とまったく同じである場合、有効な位置は 1 つだけです。 例えば、```
1 1
7
1 1
7
```可能な唯一の答えがあります`0 0`。 誤って使用した検索`< W-N`の代わりに`<= W-N`ポジションが見つからないでしょう。 

1 ピクセルのテンプレートは、もう 1 つの有用な境界ケースです。 のために```
3 1
10 20 30
1 1
1E
```テンプレートには 16 進数が含まれています`1E`、これは 10 進数の 30 なので、答えは次のようになります。`2 0`。 入力を 16 進数ではなく 10 進数として扱うと、問題は静かに変化します。 

値が等しい画像には、最適な位置が多数存在する可能性があります。 のために```
3 2
07 07 07
07 07 07
2 1
07 07
```すべての法的立場には SSD ゼロがあるため、`0 0`、`1 0`、 そして`0 1`はすべて正しいです。 プログラムは、最適値が一意であると想定してはなりません。 

最後に、右下の位置は合法であり、検査する必要があります。 例えば、```
3 3
00 00 00
00 00 00
00 00 2A
1 1
2A
```に固有の最適値があります`2 2`。 で停止する不正なループ`W-N-1`または`H-M-1`それを逃します。 

## アプローチ

 ブルート フォース手法は定義に直接に従います。 正当な左上位置ごとに、すべての (NM) テンプレート ピクセルを訪問し、対応する画像ピクセルとの差を計算し、二乗して、現在のスコアに追加します。 すべての SSD は定義どおりに正確に評価されるため、これは正しいです。 

ポジションの数は、

 [
 (W-N+1)(H-M+1)、
 】

 したがって、合計作業量は

 [
 O((W-N+1)(H-M+1)NM)。 
】

 (W=1024)、(H=768)、(N=512)、および (M=384) には、(513\cdot385=197505) 個の位置があり、各比較は (512\cdot384=196608) ピクセルをスキャンします。 これは、約 (3.88\times10^{10}) ピクセルの比較に相当します。 ブルートフォース手法は数学的には単純ですが、制限時間の範囲をはるかに超えています。 

正方形を拡張すると、有益な観察が得られます。 

[
 (I-T)^2=I^2-2IT+T^2。 
】

 固定位置の場合、これは次のようになります。 

\sum I(x+j,y+i)^2
 -2\sum I(x+j,y+i)T(j,i)
 +\sum T(j,i)^2。 
】

 テンプレートは決して変更されないため、最後の項は位置に依存しません。 最初の項は、大きな画像の長方形ウィンドウ上の二乗和であるため、このようなすべての値は、(I^2) の 2 次元プレフィックス合計を構築した後、一定時間で取得できます。 

唯一難しい部分は、

 [
 C(x,y)=
 \sum I(x+j,y+i)T(j,i)。 
】

 これは 2 次元の相互相関です。 両方の次元でテンプレートを反転すると、相関は通常の 2 次元の畳み込みになります。 畳み込み定理によれば、この畳み込みは、両方の配列を 2 次元 FFT で変換し、対応する周波数係数を乗算し、その結果を逆変換することによって計算できます。 これにより、高価な部分が、すべての位置ですべてのテンプレート ピクセルをスキャンすることから、おおよそ (O(PQ(\log P+\log Q))) に変更されます。ここで、(P) と (Q) は適切な 2 の累乗です。 

便利な実装の最適化もあります。 単純な FFT ソリューションでは、画像に対して 1 つの順変換を実行し、逆テンプレートに対してもう 1 つを実行し、その後に 1 つの逆変換を実行します。 両方の入力が実数であるため、(I+iT') として 1 つの複素配列にパックできます。 1 つのフーリエ変換から、共役対称性を使用して実数部と虚数部の変換を復元できます。 これにより、順方向 2 次元 FFT が 1 つと逆 2 次元 FFT が 1 つだけ残ります。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | (O((W-N+1)(H-M+1)NM)) | (O(WH)) | 遅すぎる |
 | 最適 | (O(PQ(\log P+\log Q))) | (O(PQ)) | 承認済み |

 ## アルゴリズムのチュートリアル

1. イメージとテンプレートを読み取り、2 桁の 16 進ピクセルをすべて 0 ～ 255 の整数に変換します。大きなイメージを (I) として保存し、テンプレートを (T) として保存します。 出力では次元が幅優先形式で維持されますが、配列では行優先インデックスが使用されます。 
2. (I^2) の 2 次元プレフィックス合計を作成します。 任意の長方形の画像ウィンドウの場合、2 乗画像ピクセルの合計は 4 回のプレフィックスサム アクセスで取得できます。 これは、すべてのテンプレート ピクセルを繰り返し訪問することなく、位置依存 (\sum I^2) 項を処理します。 
3. (S_T=\sum T^2) を 1 回計算します。 これは考えられるすべての配置で同じであるため、再計算する必要はありません。 
4. (P\ge H+M-1) と (Q\ge W+N-1) を満たす 2 の累乗 (P) と (Q) を選択します。 これらの次元は、循環畳み込みではなく完全な線形畳み込みを保持するのに十分な大きさです。 FFT では循環畳み込みが自然に計算されるため、パディングは不可欠です。 
5. (P\times Q) 複素配列を作成します。 大きな画像 (I) を実部に置きます。 両次元で反転したテンプレートを虚数部に置きます。 (T') が反転テンプレートの場合、格納される値は概念的には (I+iT') です。 
6. 2 次元 FFT を適用します。 2 次元変換は、すべての行にわたる 1 次元 FFT として実装され、その後、すべての列に 1 次元 FFT が続きます。 この操作の後、共役対称恒等式を使用して、(I) と (T') の周波数領域変換を復元します。 
7. 復元された変換を点ごとに乗算します。 畳み込み定理により、この積の逆変換は畳み込み (I*T') になります。 (T') は両方の次元で反転されたテンプレートであるため、((y+M-1,x+N-1)) の係数は、SSD 式で必要な相関 (C(x,y)) と正確に一致します。 
8. 逆 2 次元 FFT を適用して、空間領域内のすべての相関値を取得します。 浮動小数点 FFT では小さな数値誤差が生じるため、各相関関係は正確な整数の SSD 式で使用される前に最も近い整数に丸められます。 
9. すべての有効なテンプレート位置を反復処理します。 ((x,y))ごとに、プレフィックス和から(I^2)の窓和を求め、畳み込み結果から相関を求め、

 [
 SSD(x,y)=windowSquareSum-2C(x,y)+S_T。 
】

 最小値の位置を保持します。 

1. 最適な位置を次のように印刷します。`x y`。 ステートメントでは任意の最適な位置が許可されるため、複数の位置に同じ SSD がある場合は、位置を上から下、左から右にスキャンするだけで十分です。 

### なぜ効果があるのか

 プレフィックスの合計は、拡張された SSD 式の最初の項の正確な値を示し、(S_T) は正確に定数の 3 番目の項になります。 テンプレートを反転すると、残りの相互相関が畳み込みに変換され、その係数は FFT 計算によって回復されます。 その結果、すべての正当な位置について、アルゴリズムは、整数相関を丸めることによって除去される無視できる浮動小数点 FFT 誤差まで、その SSD を定義する 3 つの項を正確に再構築します。 すべての正当な位置が検査され、最小の再構築された SSD が選択されるため、返される位置は最適になります。 

## Python ソリューション```python
import sys
import math

input = sys.stdin.readline

def next_pow2(x):
    p = 1
    while p < x:
        p <<= 1
    return p

def make_rev(n):
    rev = [0] * n
    half = n >> 1
    j = 0
    for i in range(1, n):
        bit = half
        while j & bit:
            j ^= bit
            bit >>= 1
        j ^= bit
        rev[i] = j
    return rev

def make_roots(n):
    forward = {}
    inverse = {}

    length = 2
    while length <= n:
        half = length >> 1
        angle = 2.0 * math.pi / length

        wf = []
        wi = []
        for j in range(half):
            a = angle * j
            c = math.cos(a)
            s = math.sin(a)
            wf.append(complex(c, -s))
            wi.append(complex(c, s))

        forward[length] = wf
        inverse[length] = wi
        length <<= 1

    return forward, inverse

def fft1d(a, invert, rev, roots_forward, roots_inverse):
    n = len(a)

    for i in range(n):
        j = rev[i]
        if i < j:
            a[i], a[j] = a[j], a[i]

    length = 2
    roots = roots_inverse if invert else roots_forward

    while length <= n:
        half = length >> 1
        w = roots[length]

        for base in range(0, n, length):
            for j in range(half):
                u = a[base + j]
                v = a[base + j + half] * w[j]
                a[base + j] = u + v
                a[base + j + half] = u - v

        length <<= 1

    if invert:
        inv_n = 1.0 / n
        for i in range(n):
            a[i] *= inv_n

def fft2(mat, invert, rev_p, rev_q, roots_p_f, roots_p_i,
         roots_q_f, roots_q_i):
    p = len(mat)
    q = len(mat[0])

    for r in range(p):
        fft1d(mat[r], invert, rev_q, roots_q_f, roots_q_i)

    col = [0j] * p
    for c in range(q):
        for r in range(p):
            col[r] = mat[r][c]

        fft1d(col, invert, rev_p, roots_p_f, roots_p_i)

        for r in range(p):
            mat[r][c] = col[r]

def build_prefix_square(img):
    h = len(img)
    w = len(img[0])

    pref = [[0] * (w + 1) for _ in range(h + 1)]

    for r in range(h):
        row_sum = 0
        prev = pref[r]
        cur = pref[r + 1]

        for c in range(w):
            v = img[r][c]
            row_sum += v * v
            cur[c + 1] = prev[c + 1] + row_sum

    return pref

def rect_sum(pref, y1, x1, y2, x2):
    return (
        pref[y2][x2]
        - pref[y1][x2]
        - pref[y2][x1]
        + pref[y1][x1]
    )

def solve():
    first = input().split()
    while not first:
        first = input().split()

    W, H = map(int, first)

    image = []
    for _ in range(H):
        row = input().split()
        while not row:
            row = input().split()
        image.append([int(x, 16) for x in row])

    N, M = map(int, input().split())

    template = []
    for _ in range(M):
        row = input().split()
        while not row:
            row = input().split()
        template.append([int(x, 16) for x in row])

    pref = build_prefix_square(image)

    template_square = 0
    for row in template:
        for v in row:
            template_square += v * v

    P = next_pow2(H + M - 1)
    Q = next_pow2(W + N - 1)

    mat = [[0j] * Q for _ in range(P)]

    for r in range(H):
        dst = mat[r]
        src = image[r]
        for c in range(W):
            dst[c] = complex(src[c], 0.0)

    for r in range(M):
        src = template[r]
        dst = mat[M - 1 - r]
        for c in range(N):
            dst[N - 1 - c] += complex(0.0, src[c])

    rev_p = make_rev(P)
    rev_q = make_rev(Q)

    roots_p_f, roots_p_i = make_roots(P)
    roots_q_f, roots_q_i = make_roots(Q)

    fft2(
        mat,
        False,
        rev_p,
        rev_q,
        roots_p_f,
        roots_p_i,
        roots_q_f,
        roots_q_i,
    )

    # Recover FFT(image) * FFT(reversed_template) from
    # one packed transform FFT(image + i * reversed_template).
    #
    # For Z = A + iB:
    # A_k = (Z_k + conj(Z_-k)) / 2
    # B_k = (Z_k - conj(Z_-k)) / (2i)
    #
    # Process conjugate-frequency pairs together so that the
    # original spectrum is never overwritten before it is needed.

    for r in range(P):
        rr = (-r) % P

        for c in range(Q):
            cc = (-c) % Q

            idx = r * Q + c
            ridx = rr * Q + cc

            if idx > ridx:
                continue

            z = mat[r][c]
            zn = mat[rr][cc].conjugate()

            a = (z + zn) * 0.5
            b = (z - zn) * (-0.5j)

            product = a * b

            mat[r][c] = product

            if idx != ridx:
                mat[rr][cc] = product.conjugate()

    fft2(
        mat,
        True,
        rev_p,
        rev_q,
        roots_p_f,
        roots_p_i,
        roots_q_f,
        roots_q_i,
    )

    best_x = 0
    best_y = 0
    best = None

    for y in range(H - M + 1):
        for x in range(W - N + 1):
            window_square = rect_sum(
                pref,
                y,
                x,
                y + M,
                x + N,
            )

            corr = int(round(mat[y + M - 1][x + N - 1].real))

            ssd = window_square - 2 * corr + template_square

            if best is None or ssd < best:
                best = ssd
                best_x = x
                best_y = y

    return f"{best_x} {best_y}"

if __name__ == "__main__":
    print(solve())
```入力フェーズは、すべての 16 進トークンを次のように変換します。`int(token, 16)`。 これは数字や文字を手動で処理するよりも適しており、大文字または小文字の 16 進数も受け入れます。 

プレフィックス構築により、追加の行と列が 1 つ保存されます。 半分開いた境界を持つ長方形`[y1, y2) x [x1, x2)`その後 4 回のアクセスで回復されます。 ハーフオープン座標を使用すると、最初の行と列での特殊なケースが回避されます。 

FFT の次元は、単なる元の画像サイズではなく、完全な畳み込みサイズに基づいています。 パディングが小さすぎる場合、FFT は巡回畳み込みを計算し、配列の一方の側の値がもう一方の側に回り込んでしまいます。 

テンプレートは反転した座標に配置されます。これは、畳み込みではカーネルが元の方向で使用されるのに対し、相関ではカーネルを反転する必要があるためです。 行の係数`y + M - 1`そしてコラム`x + N - 1`したがって、に配置されたテンプレートに対応します。`(x, y)`。 

パックされた FFT セクションは、実装の中で最もデリケートな部分です。 もし`Z`の変換です`A+iB`、次に の変換`A`～から回復できる`Z[k]`との共役`Z[-k]`。 同じペアは次の変換を与えます。`B`。 両方の周波数位置を一緒に処理すると、共役パートナーが読み取られる前に 1 つの変換された値が上書きされるのを防ぎます。 

Python の整数はオーバーフローしないため、最大値が (2^{32}) より大きい (255^2\cdot786432) 付近であっても、最終的な SSD 式は安全です。 FFT 自体は浮動小数点の複素数を使用しますが、必要な相関は整数です。 最終の実数係数を丸めると、指定された値の範囲でその整数が正確に回復されます。 

## 実用的な例

 ### サンプル 1

 画像は```
00 FF 12
AA BB 34
```そしてテンプレートは```
FF 11
```可能な水平位置は 2 つあり、垂直位置は 1 つだけです。 

| × | y | ウィンドウ | 相関関係 | ウィンドウ (\sum I^2) | SSD | これまでで最高 |
 | --- | --- | --- | --- | --- | --- | --- |
 | 0 | 0 |`00 FF`| (0\cdot255+255\cdot17) | (0^2+255^2) | 121669 |`(0,0)`|
 | 1 | 0 |`FF 12`| (255\cdot255+18\cdot17) | (255^2+18^2) | 1 |`(1,0)`|

 で`(1,0)`の場合、最初のピクセルは完全に一致し、2 番目のピクセルは 1 だけ異なるため、SSD は (1) になります。 アルゴリズムは、畳み込みから同じ相関関係を取得し、`1 0`、サンプルと一致します。 

### サンプル 2

 画像は (4\times5)、テンプレートは (3\times3) であるため、有効な位置は 6 つあります。 

| × | y | SSD | これまでで最高 |
 | --- | --- | --- | --- |
 | 0 | 0 | 82038 |`(0,0)`|
 | 1 | 0 | 72104 |`(1,0)`|
 | 0 | 1 | 85314 |`(1,0)`|
 | 1 | 1 | 88380 |`(1,0)`|
 | 0 | 2 | 83249 |`(1,0)`|
 | 1 | 2 | 105273 |`(1,0)`|

 最小スコアは次のとおりです`(1,0)`。 この例は、相関関係の最大化のみが SSD の最小化の有効な置き換えにならない理由を示しています。 (\sum I^2) 項はウィンドウ間で変化するため、ウィンドウ エネルギーと相関の両方を含める必要があります。 

## 複雑さの分析

 しましょう

 [
 P=2^{\lceil\log_2(H+M-1)\rceil}
 】

 そして

 [
 Q=2^{\lceil\log_2(W+N-1)\rceil}。 
】

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O(PQ(\log P+\log Q)+WH+NM)) | 2 次元 FFT とプレフィックスサムおよび入力処理 |
 | スペース | (O(PQ+WH)) | パディングされた複素 FFT 配列と画像プレフィックスの合計 |

 最大次元では、パディングされた次元は両方とも最大 2048 であるため、(P Q\le 2048^2)。 意図したソリューションは、1024 MB という寛大なメモリ制限に快適に収まり、FFT により、数十億の直接ピクセル乗算から周波数領域変換への相関計算が削減されます。 公開されている問題では 4 秒の制限があるため、実装には再帰的 FFT ではなく反復 FFT が必要であり、2 つの実数入力を 1 つの複素変換にパックすることで大きなメリットが得られます。 

## テストケース

 以下のテスト ハーネスは次のことを前提としています。`solve()`ソリューションの機能が利用可能です。 最大サイズのケースは、数十万の入力ピクセルを埋め込むのではなく、プログラムによって生成されます。```python
# helper: run solution on input string, return output string
import sys
import io

# Assume solve() is imported from the submitted solution.

def run(inp: str) -> str:
    old_stdin = sys.stdin
    try:
        sys.stdin = io.StringIO(inp)
        return solve().strip()
    finally:
        sys.stdin = old_stdin

# Sample 1
sample1 = """\
3 2
00 FF 12
AA BB 34
2 1
FF 11
"""
assert run(sample1) == "1 0", "sample 1"

# Sample 2
sample2 = """\
4 5
89 4E 72 C6
C7 E9 EA 8F
6E B1 FD E4
7C 22 6C D0
93 FB DB E5
3 3
79 C0 51
B9 98 37
BB 64 7F
"""
assert run(sample2) == "1 0", "sample 2"

# Minimum-size input.
minimum = """\
1 1
00
1 1
00
"""
assert run(minimum) == "0 0", "minimum size"

# All positions have the same SSD.
all_equal = """\
3 2
07 07 07
07 07 07
2 1
07 07
"""
assert run(all_equal) == "0 0", "all equal values"

# The unique optimum is the bottom-right position.
bottom_right = """\
3 3
00 00 00
00 00 00
00 00 2A
1 1
2A
"""
assert run(bottom_right) == "2 2", "bottom-right boundary"

# Maximum-size dimensions, all zeros.
# Every position is optimal, and the scan should return 0 0.
W, H = 1024, 768
N, M = 1024, 768

image_rows = "\n".join(
    " ".join(["00"] * W)
    for _ in range(H)
)
template_rows = "\n".join(
    " ".join(["00"] * N)
    for _ in range(M)
)

maximum = (
    f"{W} {H}\n"
    f"{image_rows}\n"
    f"{N} {M}\n"
    f"{template_rows}\n"
)

assert run(maximum) == "0 0", "maximum-size input"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`1 1 / 00 / 1 1 / 00`|`0 0`| 最小寸法と単一の法的位置 |
 | あ`3 x 2`で満たされたイメージ`07`、`2 x 1`が詰まったテンプレート`07`|`0 0`| 複数の最適な位置とゼロ SSD |
 | あ`3 x 3`右下のピクセルのみが以下と等しい画像`2A`、`1 x 1`テンプレート`2A`|`2 2`| 右と下の境界を含む |
 |`1024 x 768`オールゼロのイメージと同じサイズのオールゼロのテンプレート |`0 0`| 最大サイズ、メモリ使用量、および大きなパディング |
 | サンプル 1 |`1 0`| 16 進解析と独自の最適化 |
 | サンプル 2 |`1 0`| 一般的な 2 次元マッチング |

 ## 特殊なケース

 テンプレートが画像と同じサイズの場合でも FFT は機能しますが、候補は 1 つだけです。 のために```
1 1
7
1 1
7
```パディングされた畳み込みには、次の時点で必要な相関が含まれています。`(0,0)`、プレフィックス和は (7^2) を与え、テンプレート二乗和は (7^2) を与え、SSD はゼロになります。 スキャンは 1 回だけ反復され、印刷されます。`0 0`。 

1 ピクセルのテンプレートの場合、相関はテンプレート ピクセルと各画像ピクセルの積になります。 のために```
3 1
10 20 30
1 1
1E
```テンプレート値は 16 進数です`1E`、または 30。3 つの SSD 値は (400)、(100)、および (0) であるため、アルゴリズムは次のように出力します。`2 0`。 同じ畳み込み公式で処理できるため、特別な 1 次元の場合は必要ありません。 

イメージとテンプレートがすべて等しい場合、すべての法的ポジションが同じスコアを持つことができます。 と```
3 2
07 07 07
07 07 07
2 1
07 07
```相関とウィンドウ二乗項はすべての位置で同一であり、どこでも SSD がゼロになります。 スキャンが開始されるので、`(0,0)`厳密に小さいスコアが見つかった場合にのみ答えを置き換え、出力します。`0 0`、これは有効な最適値です。 

右下の境界については、```
3 3
00 00 00
00 00 00
00 00 2A
1 1
2A
```法的な座標は`0..2`両方の次元で。 独自のゼロ SSD の位置は次のとおりです。`(2,2)`。 アルゴリズムで使用される畳み込み係数は次の行にあります`2 + 1 - 1 = 2`そしてコラム`2 + 1 - 1 = 2`, したがって、境界位置は 1 回だけ含まれます。 

16 進数入力の場合、次のような値`0A`、`FF`、 そして`e7`すべて受け入れなければなりません。 パイソンの`int(token, 16)`はそれらをすべて処理するため、アルゴリズムは数字と文字を個別に解析するロジックを必要としません。 

最大サイズについては、画像とテンプレートの両方を (1024\times768) にすることができます。 畳み込みには最大 (2047\times1535) の次元が必要ですが、FFT では (2048\times2048) に四捨五入されます。 この実装では、元の画像領域のみを処理しようとするのではなく、意図的にパディングされた変換をこれらの次元に割り当てます。これは、ゼロ パディングが不十分だと、必要な線形畳み込みが巡回畳み込みに変わり、境界付近の相関値が破損するためです。
