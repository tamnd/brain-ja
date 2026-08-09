---
title: "CF 102503F - ウラムスパイラル"
description: "グリッドには、1 を中心に正の整数が正の螺旋状に配置されています。座標は 1 を中心として、最初の座標は上に増加し、2 番目の座標は右に増加します。 したがって、2 は (0,1) に、3 は (1,1) に、4 は (1,0) に、というようになります。"
date: "2026-08-09T05:40:11+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102503
codeforces_index: "F"
codeforces_contest_name: "National Olympiad in Informatics - Philippines (NOI.PH) Online Eliminations 2020"
rating: 0
weight: 102503
solve_time_s: 515
verified: true
draft: false
---

[CF 102503F - ウラムスパイラル](https://codeforces.com/problemset/problem/102503/F)

 **評価:** -
 **タグ:** -
 **解決時間:** 8 分 35 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 グリッドには、正の整数が正方形の螺旋状に配置されています。`1`。 座標の中心は次のとおりです`1`、最初の座標は上に増加し、2 番目の座標は右に増加します。 したがって`2`にあります`(0,1)`、`3`で`(1,1)`、`4`で`(1,0)`、 等々。 

各テスト ケースには 2 つの ulam 番号が与えられます。`i`そして`j`。 無限スパイラル内で両方の数値を見つけ、それらの 2 つのセルを含む軸に沿った最小の長方形を取得し、その長方形内のすべての ulam の値を合計します。 必要な答えはこの和の剰余です`10^9 + 7`。 公式声明では、最大で以下の可能性があることが確認されています。`20,000`テスト ケースがあり、それぞれの数値は次のように大きくなる可能性があります。`10^18`。 

の境界`10^18`いずれかの入力値までスパイラルを構築することを即座に除外します。 周囲の数字`10^18`大雑把に嘘をつく`5 * 10^8`セルは中心から離れているため、1 つの大きな長方形でも約`10^18`細胞。 セルを個別に訪問するソリューションは、リモートでは実現できません。 と`20,000`テスト ケースでは、目的のソリューションにはケースごとに基本的に一定の作業または対数作業が必要です。 

誤って処理されやすい境界ケースがいくつかあります。 2 つの入力が等しい場合、長方形は 1 つのセルになります。 たとえば、入力`1 1`答えがあります`1`、 ない`0`または周囲のリングのサイズ。 2 つの異なる座標を想定した不注意な実装では、これが間違ってしまう可能性があります。 

もう 1 つのよくある間違いは、スパイラル コーナーの 4 つの辺を合計するときに、そのコーナーを 2 回数えることです。 例えば、`13`そして`25`両方とも半径のリングの同じ垂直側にあります`2`。 彼らの長方形には以下が含まれます`25, 10, 11, 12, 13`、その合計は`71`。 すべてのリングの辺が完全に包括的なものとして扱われる場合、コーナーの値を 2 回追加できます。 

座標系の方向も重要です。 例えば、`7`そして`9`同じ行の座標にあります`(-1,-1)`そして`(-1,1)`。 長方形には正確に次のものが含まれます`7,8,9`、したがって、の答えは`7 9`は`24`。 最初の座標の意味を逆にすると、スパイラルのどちら側が考慮されるかが変わり、誤った座標が自動的に生成されます。 

最後に、大きな値を浮動小数点平方根で変換しないでください。 次のような入力の場合`10^18`、浮動小数点近似は、完全な正方形に近い間違ったスパイラル リングに到達する可能性があります。 Python の整数と`math.isqrt`このクラスのエラー全体を回避してください。 

## アプローチ

 直接的なアプローチは簡単です。 まず場所を特定します`i`そして`j`スパイラルの中で。 次に、2 つの位置の間で最小および最大の行と列を決定します。 最後に、その四角形内のすべてのセルを列挙し、その ulam 番号を評価して、それを答えに追加します。 

要求された四角形は有限であり、すべてのセルが 1 回だけ訪問されるため、この強引な方法は正しいです。 問題はその大きさです。 値が近い場合`10^18`、2 つの点は大まかに分けることができます。`10^9`各座標方向のセル。おおよその内容を含む長方形が得られます。`10^18`細胞。 したがって、最悪の場合の操作数は次のとおりです。`10^18`1 つのテスト ケースのみのセル評価。 

有益な観察は、スパイラルが高度に構造化されていることです。 すべてのセルは正確に 1 つの正方形のリングに属します。リング インデックスは次のとおりです。 

[
 k=\max(|a|,|b|)。 
]

指輪`k`前の四角形に 1 を加えた値から 1 までの値が含まれます。`(2k+1)^2`。 さらに重要なことは、その 4 つの辺のそれぞれが等差数列であり、その値が次の 2 次多項式であることです。`k`その辺に沿った位置の一次関数を加えます。 

たとえば、リングの下側`k`、 どこ`a=-k`、持っています

 [
 値 = 4k^2+3k+1+b。 
】

 他の 3 つの辺も同様に単純な式になります。 これにより問題は完全に変わります。 すべてのセルを訪問する代わりに、要求された四角形とリングの各辺の交点を記号的に合計します。 固定辺の場合、長方形に属する座標は次の形式の式によって制限されます。`constant`、`k`、 または`-k`。 これらの式が交差する連続する点の間の端点は、次の固定アフィン関数になります。`k`。 1 つのリングの寄与は、次数で最大 3 次の多項式になります。`k`これは、整数のべき乗の標準的な公式で合計できます。 

ブルートフォース手法は明示的にセルにアクセスするため機能します。 数千兆個も存在する可能性があるため、これは失敗します。 すべてのリングの側面が二次基底の周りのアフィン シーケンスであるという観察により、これらすべての訪問を一定数の多項式の合計に置き換えることができます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース |`O(A)`ケースごとに、`A`は長方形の領域 |`O(1)`| 遅すぎる |
 | 最適 |`O(1)`ケースごと |`O(1)`| 承認済み |

 ## アルゴリズムのチュートリアル

 1. 各入力 ulam 番号をそのスパイラル座標に変換します。 

数字の場合`n`、 させて`k`を満たす最小の非負の整数であること

 [
 n\le(2k+1)^2。 
】

 これがその指輪です。 リング上の最大値は`(2k+1)^2`にあります`(-k,k)`。 そこからリングを一周すると、後で使用する座標式が得られます。 

もし

 [
 d=(2k+1)^2-n、
 】

 次に、リングの 4 つの部分が次から得られます。`d`:

 [
 \begin{整列}
 d<2k &: (a,b)=(-k,k-d),\
 2k\le d<4k &: (a,b)=(-k+d-2k,-k),\
 4k\le d<6k &: (a,b)=(k,-k+d-4k),\
 6k\le d &: (a,b)=(k-(d-6k),k)。 
\end{整列}
 】

 センター`n=1`自然に処理されます`k=0`。 
2. 2 つの位置の座標方向の境界四角形を取得します。 

2 つの位置がある場合、`(a1,b1)`そして`(a2,b2)`、定義します

 [
 L_a=\min(a_1,a_2),\quad R_a=\max(a_1,a_2),
 】

 そして

 [
 L_b=\min(b_1,b_2),\quad R_b=\max(b_1,b_2)。 
】

 必要な長方形は正確に

 [
 L_a\le a\le R_a,\qquad L_b\le b\le R_b。 
】
 3. 各リング側の値を多項式として表現します。 

二重カウントを避けるために、すべての角を正確に片側に割り当てます。 結果として得られるサイド範囲と値の式は次のとおりです。 

[
 \begin{配列}{c|c|c}
 辺と座標範囲と値\
 \hline
 下 & a=-k,\ -k\le b\le k & 4k^2+3k+1+b\
 左 & b=-k,\ -k+1\le a\le k & 4k^2+k+1-a\
 トップ & a=k,\ -k+1\le b\le k & 4k^2-k+1-b\
 右 & b=k,\ -k+1\le a\le k-1 & 4k^2-3k+1+a
 \end{配列}
 】

 非対称エンドポイント ルールは意図的に行われます。 下側は両方の下隅を所有し、左側は左上隅を所有し、上側は右上隅を所有し、右側はコーナーを所有しません。 
4. 各辺について、どのリングが要求された長方形と交差できるかを決定します。 

たとえば、下側には次のようになります。`a=-k`。 長方形に必要なのは

 [
 L_a\le-k\le R_a,
 】

 それで

 [
 -R_a\le k\le-L_a.
 】

 同様の不等式により、有効な次の式が得られます。`k`他の 3 つの側面の範囲。 
5. 固定有効リングの場合、辺の座標間隔と長方形の座標間隔を交差させます。 

すべてのエンドポイントは次のアフィン関数です。`k`。 たとえば、下側では、

 [
 -k\le b\le k
 】

 と交差する必要があります

 [
 L_b\le b\le R_b.
 】

 したがって、実際のエンドポイントは次のようになります。 

[
 l(k)=\max(L_b,-k),
 \qquad
 r(k)=\min(R_b,k)。 
】

 他の側でも同じ考えが当てはまります。次のような間隔があります。`[-k+1,k]`または`[-k+1,k-1]`。 
6.範囲を分割する`k`2 つのアフィン エンドポイント式が交差するたびに。 

各エンドポイントは、4 つのアフィン関数 (長方形の固定下限、辺の下限、長方形の固定上限、または辺の上限) のいずれかです。 2 つのアフィン関数の順序を変更できるのは 1 回だけです。 したがって、すべての整数交差ポイントを収集し、`k`範囲を設定し、結果として得られる各間隔を個別に処理します。 

そのような間隔の 1 つ内では、どのアフィン式が下限エンドポイントでどれが上限エンドポイントであるかが正確にわかります。 彼らの姿は、

 [
 l(k)=pk+q,\qquad r(k)=sk+t。 
】
 7. 片方の辺を合計する`k`多項式としての間隔。 

辺の値の式が次のようになったとします。 

[
 Ak^2+Bk+C+Dx。 
】

 選択されたセルの数は、

 [
 r-l+1、
 】

 これは線形です`k`。 それらの座標の合計は、

 [
 \frac{r(r+1)-l(l-1)}2,
 】

 これは二次関数です`k`。 

したがって、辺の合計寄与は、次の最大 3 次の多項式になります。`k`。 の合計を評価します。`1`、`k`、`k^2`、 そして`k^3`閉じた式を使用して区間全体にわたって計算します。 
8. 4 つの辺の寄与をモジュロで加算します。`10^9+7`。 

辺は 4 つしかなく、各辺からは一定数のオブジェクトしか生成されません。`k`間隔。 したがって、テスト ケース全体には一定の時間がかかります。 

### なぜ効果があるのか

 不変条件は、スパイラルのすべてのセルが、正確に 1 つのリングの 4 つの所有側範囲の正確に 1 つに属することです。 座標変換により、各入力 ulam がその固有のリングと側面に配置されますが、外接する四角形には、座標が 2 つの極値の間にあるセルが正確に含まれます。 

所有されている各辺について、長方形との交差は、その下位および上位のアフィン エンドポイント関数によって正確に表されます。 交差ごとに分割すると、これらの関数は処理される間隔全体にわたって固定の選択肢になります。 次に、多項式計算により、これらのリング上の選択されたすべてのセルが 1 回だけ合計されます。 4 つのサイドの所有権範囲はすべてのリングを重複なく分割するため、それらの寄与は要求された長方形の合計と正確に等しくなります。 

## Python ソリューション```python
import sys
from math import isqrt

input = sys.stdin.readline

MOD = 10**9 + 7
INV2 = pow(2, MOD - 2, MOD)
INV6 = pow(6, MOD - 2, MOD)

def coord(n):
    # Smallest k such that n <= (2k + 1)^2.
    k = (isqrt(n - 1) + 1) // 2

    m = (2 * k + 1) ** 2
    d = m - n

    if d < 2 * k:
        # Bottom: a = -k
        return -k, k - d

    if d < 4 * k:
        # Left: b = -k
        d -= 2 * k
        return -k + d, -k

    if d < 6 * k:
        # Top: a = k
        d -= 4 * k
        return k, -k + d

    # Right: b = k
    d -= 6 * k
    return k - d, k

def powers_sum(l, r):
    if l > r:
        return (0, 0, 0, 0)

    n = r - l + 1

    def pref1(x):
        return x * (x + 1) * INV2 % MOD

    def pref2(x):
        return x * (x + 1) * (2 * x + 1) % MOD * INV6 % MOD

    def pref3(x):
        y = x * (x + 1) % MOD * INV2 % MOD
        return y * y % MOD

    return (
        n % MOD,
        (pref1(r) - pref1(l - 1)) % MOD,
        (pref2(r) - pref2(l - 1)) % MOD,
        (pref3(r) - pref3(l - 1)) % MOD,
    )

def add_side(ans, kl, kr, fixed_l, fixed_r,
             lp, lq, rp, rq, A, B, C, D):
    """
    Sum one spiral side.

    The side coordinate interval is
        [lp*k + lq, rp*k + rq]
    and the rectangle coordinate interval is
        [fixed_l, fixed_r].

    Value on the side is
        A*k^2 + B*k + C + D*x.
    """
    kl = max(kl, 0)
    if kl > kr:
        return ans

    # Four affine expressions determine the two endpoints:
    # fixed_l, geometric_l, fixed_r, geometric_r.
    expr = [
        (0, fixed_l),
        (lp, lq),
        (0, fixed_r),
        (rp, rq),
    ]

    cuts = {kl, kr + 1}

    # Within each interval between crossings, the ordering of
    # all endpoint expressions is fixed.
    for i in range(4):
        p1, q1 = expr[i]
        for j in range(i + 1, 4):
            p2, q2 = expr[j]
            den = p1 - p2
            num = q2 - q1

            if den != 0 and num % den == 0:
                x = num // den
                if kl <= x <= kr:
                    cuts.add(x)
                    if x + 1 <= kr:
                        cuts.add(x + 1)

    cuts = sorted(cuts)

    for idx in range(len(cuts) - 1):
        l = cuts[idx]
        r = cuts[idx + 1] - 1

        if l > r:
            continue

        mid = (l + r) // 2

        gl = lp * mid + lq
        gr = rp * mid + rq

        # Choose which affine expression realizes max(fixed_l, geometric_l).
        if fixed_l >= gl:
            Lp, Lq = 0, fixed_l
        else:
            Lp, Lq = lp, lq

        # Choose which affine expression realizes min(fixed_r, geometric_r).
        if fixed_r <= gr:
            Rp, Rq = 0, fixed_r
        else:
            Rp, Rq = rp, rq

        # If the interval is empty at the midpoint, it is empty
        # throughout this segment because all orderings are fixed.
        if Lp * mid + Lq > Rp * mid + Rq:
            continue

        # count = r(k) - l(k) + 1
        count0 = Rq - Lq + 1
        count1 = Rp - Lp

        # Base value polynomial is C + B*k + A*k^2.
        base = [C, B, A]

        # Multiply base by count.
        poly = [0, 0, 0, 0]
        count = [count0, count1]

        for i in range(3):
            for j in range(2):
                poly[i + j] += base[i] * count[j]

        # Sum of coordinates:
        # (r(r+1) - l(l-1)) / 2.
        # For x = p*k + q:
        # x(x+1) = p^2*k^2 + p*(2q+1)*k + q(q+1).
        r2 = Rp * Rp
        r1 = Rp * (2 * Rq + 1)
        r0 = Rq * (Rq + 1)

        l2 = Lp * Lp
        l1 = Lp * (2 * Lq - 1)
        l0 = Lq * (Lq - 1)

        poly[2] += D * (r2 - l2) * INV2
        poly[1] += D * (r1 - l1) * INV2
        poly[0] += D * (r0 - l0) * INV2

        s0, s1, s2, s3 = powers_sum(l, r)

        ans += poly[0] * s0
        ans += poly[1] * s1
        ans += poly[2] * s2
        ans += poly[3] * s3
        ans %= MOD

    return ans

def solve_case(i, j):
    a1, b1 = coord(i)
    a2, b2 = coord(j)

    la = min(a1, a2)
    ra = max(a1, a2)
    lb = min(b1, b2)
    rb = max(b1, b2)

    ans = 0

    # Bottom:
    # a = -k, b in [-k, k]
    # value = 4k^2 + 3k + 1 + b
    ans = add_side(
        ans,
        -ra, -la,
        lb, rb,
        -1, 0, 1, 0,
        4, 3, 1, 1
    )

    # Left:
    # b = -k, a in [-k+1, k]
    # value = 4k^2 + k + 1 - a
    ans = add_side(
        ans,
        -rb, -lb,
        la, ra,
        -1, 1, 1, 0,
        4, 1, 1, -1
    )

    # Top:
    # a = k, b in [-k+1, k]
    # value = 4k^2 - k + 1 - b
    ans = add_side(
        ans,
        la, ra,
        lb, rb,
        -1, 1, 1, 0,
        4, -1, 1, -1
    )

    # Right:
    # b = k, a in [-k+1, k-1]
    # value = 4k^2 - 3k + 1 + a
    ans = add_side(
        ans,
        lb, rb,
        la, ra,
        -1, 1, 1, -1,
        4, -3, 1, 1
    )

    return ans % MOD

def main():
    t = int(input())
    out = []

    for _ in range(t):
        i, j = map(int, input().split())
        out.append(str(solve_case(i, j)))

    sys.stdout.write("\n".join(out))

if __name__ == "__main__":
    main()
```の`coord`関数は、まず、要求された番号を含むリングを検索します。 表現`(isqrt(n - 1) + 1) // 2`浮動小数点演算を行わずに正しいリングが得られます。 変数`d`数値が右下隅の最大値からどれだけ離れているかを測定し、その数値の辺と座標を直接識別します。 

の`powers_sum`関数は、3 次多項式に必要な 4 つの量を提供します。`1`、`k`、`k^2`、 そして`k^3`。 すべての除算はモジュロで実行されます`10^9+7`モジュラーインバースを使用します。 

中心となるルーチンは、`add_side`。 その 4 つのアフィン端点式は、螺旋の 1 つの辺と長方形の間の考えられるすべての交差を記述するのに十分です。 すべての整数交差がパーティションに挿入されるため、結果として得られる各間隔内では、同じエンドポイント式がアクティブなままになります。 中点は、アクティブな式を識別するためにのみ使用され、答えを近似するために使用されるわけではありません。 

の多項式`add_side`特に注目に値します。 1 つのセルの値は 2 次です。`k`、一方、辺の選択された部分のセルの数は線形です。`k`。 彼らの製品は立方体です。 座標和は、別の二次項に寄与します。 したがって、区間全体は、によって返される 4 つのべき乗和に還元できます。`powers_sum`。 

The side ranges deliberately use different endpoints. 下側には両方の下隅が含まれ、左側は左下隅の 1 つ後の位置から始まり、上面は左上隅から 1 つ後の位置から始まり、右側は残りの両方の隅を除きます。 This makes the four ranges disjoint and prevents double counting.

 Python integers have arbitrary precision, so the intermediate products are safe even though the original values are as large as`10^18`そして長方形の合計ははるかに大きくなります。 

## 実用的な例

 ### サンプル 1、`2 12`座標は

 [
 2=(0,1),\qquad 12=(1,2)。 
】

 したがって、外接する長方形は次のようになります。 

[
 0\le a\le1,\qquad1\le b\le2。 
】

 その中の細胞は、`2, 11, 3, 12`。 

| ステップ |`k`| 選択された座標 | 付加価値 | 累計額 |
 | --- | --- | --- | --- | --- |
 | 底面 |`1`|`(0,1)`|`2`|`2`|
 | 右/上の交差点 |`2`|`(0,2)`|`11`|`13`|
 | 上面 |`1`|`(1,1)`|`3`|`16`|
 | 右側 |`2`|`(1,2)`|`12`|`28`|

 答えは`28`、サンプルと一致します。 トレースは、長方形がいくつかの異なるリングと交差する可能性があることを示していますが、アルゴリズムはそれらの間のすべてのリングを反復することはありません。 適用可能な各範囲は多項式として合計されます。 

### サンプル 1、`9 7`座標は

 [
 9=(-1,1)、\qquad7=(-1,-1)。 
】

 両方の値が同じ行にあるため、外接する四角形は次のようになります。 

[
 a=-1,\qquad -1\le b\le1。 
】

 | ステップ |`k`| 選択された座標 | 付加価値 | 累計額 |
 | --- | --- | --- | --- | --- |
 | 底面 |`1`|`(-1,-1)`|`7`|`7`|
 | 底面 |`1`|`(-1,0)`|`8`|`15`|
 | 底面 |`1`|`(-1,1)`|`9`|`24`|

 答えは`24`。 このケースでは、内容全体が螺旋の 1 つの側面にある狭い長方形を実行します。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |`O(1)`テストケースごと | 4 つの辺、それぞれが一定数のアフィン次数区間のみに分割される |
 | スペース |`O(1)`| 一定数の座標、多項式係数、区間境界のみが保存されます。 

せいぜい`20,000`テストケースと入力は最大`10^18`の場合、ソリューションはケースごとに少量の一定量の演算のみを実行します。 スパイラルを構築したり、長方形を反復したり、2 つの入力値間のすべてのリングを反復したりすることはないため、`3`2番目と`512 MB`限界。 

## テストケース```python
# helper: run solution on input string, return output string
import sys
import io

# The production solution above can be placed in this function/module.
# For a standalone test file, assume solve_case is already defined.

def run(inp: str) -> str:
    old_stdin = sys.stdin
    old_stdout = sys.stdout

    sys.stdin = io.StringIO(inp)
    output = io.StringIO()
    sys.stdout = output

    try:
        t = int(input())
        ans = []
        for _ in range(t):
            i, j = map(int, input().split())
            ans.append(str(solve_case(i, j)))
        return "\n".join(ans)
    finally:
        sys.stdin = old_stdin
        sys.stdout = old_stdout

# Provided sample
assert run("""\
3
2 12
9 7
7 9
""") == """\
28
24
24
""", "sample 1"

# Minimum input, same cell
assert run("""\
1
1 1
""") == """\
1
""", "single center cell"

# Adjacent cells
assert run("""\
1
1 2
""") == """\
3
""", "adjacent cells"

# Same row, exercises side traversal
assert run("""\
1
7 9
""") == """\
24
""", "same-row boundary case"

# Same column, includes two corners of one ring
assert run("""\
1
13 25
""") == """\
71
""", "same-column ring case"

# Maximum input value, equal endpoints
assert run("""\
1
1000000000000000000 1000000000000000000
""") == """\
49
""", "maximum value and modular reduction"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`1 / 1 1`|`1`| 最小入力と縮退した 1 セルの長方形 |
 |`1 / 1 2`|`3`| 隣接するセルと最初の螺旋ステップ |
 |`1 / 7 9`|`24`| 同じ行の長方形と下側の走査 |
 |`1 / 13 25`|`71`| 同じ列のトラバーサルとリングコーナーの所有権 |
 |`1 / 10^18 10^18`|`49`| 最大入力、正確なリング計算、およびモジュロ演算 |

 ## 特殊なケース

 入力が等しい場合は、幾何学的な複雑さが発生する前に処理されます。 のために`1 1`、両方の座標は`(0,0)`, したがって、長方形は 1 つのセルです。 ボトム側の計算にはリングが含まれます`k=0`、その式は次のようになります`1`、他の 3 つの所有側範囲は空です。 出力は正確に`1`。 

同列の場合`7 9`座標があります`(-1,-1)`そして`(-1,1)`。 関連する唯一のリングは`k=1`、下辺は間隔に寄与します`b=-1..1`。 その式は次のようになります`7,8,9`、生産`24`。 他の側ではこれらのセルを追加しないため、重複はありません。 

コーナーケース`13 25`座標があります`(2,2)`そして`(-2,2)`。 長方形は単一の列です`b=2`、からの行`-2`を通して`2`。 値は次のとおりです。`25,10,11,12,13`、合計すると`71`。 下側が所有しています`25`、右側が所有します`10,11,12`、上側が所有します`13`。 エンドポイントの規則はまさに、コーナーが 2 回カウントされるのを防ぐものです。 

最大値の場合は、`10^18`両方の入力に対して。 2 つの座標は同一であるため、必要なセルは 1 つだけです。 このアルゴリズムは、整数平方根算術を使用して数値を特定し、その値を法で返します。`10^9+7`。 なぜなら

 [
 10^{18}=(10^9)^2\equiv(-7)^2\equiv49\pmod{10^9+7}、
 】

 期待される出力は`49`。 これは、ソリューションのどこにも浮動小数点計算が必要ない理由も示しています。
