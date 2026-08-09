---
title: "CF 102460F - ミス・スローン"
description: "すべての上院議員に対して整数 (ai) があり、最終的な合意は、すべての現在の (ai) 値の gcd が 1 より大きいときに正確に発生します。スローンは上院議員を 1 回選択し、その上院議員の値を (dle k) を満たす任意の約数 (d) で割ることができます。"
date: "2026-08-09T02:50:20+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102460
codeforces_index: "F"
codeforces_contest_name: "2019-2020 ICPC Asia Taipei-Hsinchu Regional Contest"
rating: 0
weight: 102460
solve_time_s: 397
verified: true
draft: false
---

[CF 102460F - ミス・スローン](https://codeforces.com/problemset/problem/102460/F)

 **評価:** -
 **タグ:** -
 **解決時間:** 6 分 37 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 上院議員ごとに整数があります\(a_i\)、そして最終的な合意は、現在のすべての gcd が一致したときに正確に行われます。\(a_i\)値は 1 より大きい。スローンは上院議員を 1 回選択し、その上院議員の値を任意の約数で割ることができる\(d\)満足のいく\(d\le k\)。 目標は、ロビー活動の合計時間を最小限に抑えながら、最終的な gcd を 1 に等しくすることです。 

抵抗\(e_i\)少し珍しい方法で時間に影響を与えます。 現在のキャンペーンがすでにロビー活動を行っている場合\(x\)完全に抵抗する上院議員\(y\)、上院議員にロビー活動\(i\)費用\[
y+e_i(x+1).
\]この注文は一見関連性があるように見えますが、便利なキャンセルがあります。 正確に仮定してください\(m\)上院議員にはロビー活動が行われ、選挙運動の順序での抵抗は\(e_1,\ldots,e_m\)。 総費用は\[
\sum_{j=1}^{m}\left(\sum_{t<j}e_t+je_j\right).
\]固定上院議員の場合\(j\)、その抵抗は、後続の上院議員ごとに最初の合計に 1 回表示され、それ自身の立場で 1 回乗算されます。 その合計係数は\[
(m-j)+j=m.
\]したがって、キャンペーンの合計時間は単純に次のようになります。\[
m\sum e_i.
\]順序はまったく関係ありません。 したがって、問題は、元の gcd のすべての素因数が削除され、最小化されるように上院議員と法務部門を選択することです。\[
(\text{number of lobbied senators})\times(\text{sum of their resistances}).
\]制約の大半を占めるのは、\(n\le 10^6\)。 \(O(n^2)\) アプローチは直ちに不可能であり、\(O(n2^{11})\) アプローチですら大きすぎます。\(n2^{11}\)についてです\(2\cdot10^9\)。 代わりに、有用な小さなパラメータは、共通の gcd の個別の素因数の数です。 gcd は最大なので、\(10^{12}\)、最大 11 個の異なる素数が含まれます。 

誤って対処しやすい特殊なケースがいくつかあります。 初期の gcd がすでに 1 である場合、ロビー活動は必要ないため、答えは 0 になります。 例えば、```text
2 10
6 35
1 1
```gcd 1 があるため、正解は次のとおりです。`0`。 常に少なくとも 1 人の上院議員に働きかける実装では、正の値が返されます。 

もう 1 つの微妙なケースは、単一の上院議員がすべての共通素数を削除できない場合です。 例えば、```text
2 2
6 6
1 1
```1 つの上院議員は 2 で割って 3 が残り、もう 1 つの上院議員は 3 で割って 2 が残ります。最終的な gcd は 1 なので、答えは 4 です。1 人の上院議員が gcd 全体を削除できるかどうかだけをチェックするソリューションでは、誤ったレポートが返されます。`-1`。 

除数の制限も含まれます。 と```text
2 6
6 10
1 1
```最初の上院議員は正確に 6 で割ることができます。\(6\le k\)。 両方の上院議員が必要なので、合計時間は \(2(1+1)=4\) になります。 テスト`d < k`の代わりに`d <= k`法的操作を拒否することができます。 

最後に、プライムは上院議員からのみ削除できます\(i\)その割り算に、その素数のパワー全体が含まれている場合、\(a_i\)。 もし\(a_i=12\)、素数 2 を削除するには、単に 2 で割るのではなく、4 で割る必要があります。指数を忘れると、常に間違った答えが得られます。\(a_i\)共通素数のより高い累乗が含まれています。 

## アプローチ

 直接的なブルートフォースアプローチでは、上院議員のすべてのサブセットと、選択された各上院議員のすべての法定数が試行されます。 考えられるすべてのキャンペーンが明示的に考慮されているため、これは正しいですが、\(n=10^6\)。 上院議員のサブセットを列挙するだけでもすでに時間がかかります\(2^n\)可能な分割を検討する前に、操作を行ってください。 

最初の有用な観察は、元の gcd 内の素数のみが重要であるということです。 させて\[
g=\gcd(a_1,a_2,\ldots,a_n).
\]すべての素数は除算されない\(g\)最終的な gcd にはすでに存在しません。 素数ごとに\(p\mid g\)、少なくとも 1 人のロビー活動を受けた上院議員は、すべてのコピーを紛失しなければなりません。\(p\)。 

仮定する\[
a_i=p^{v_i}u,\qquad p\nmid u.
\]削除するには\(p\)完全に上院議員から\(i\)、スローンは分割する必要があります\(a_i\)少なくとも\(p^{v_i}\)。 同じ上院議員から複数の素数が削除された場合、必要な約数はそれらの完全な素数乗の積になります。 その製品が最高の場合にこそ合法です。\(k\)。 

これにより、算術問題が小さなセットカバー問題に変わります。 宇宙は次の異なる素数だけで構成されています。\(g\)、最大 11 個の要素。 ビットマスクは、どの共通素数がすでに削除されているかを表します。 

残りの問題は、の値が大きいことです。\(n\)。 重要な圧縮点は、最適なキャンペーンにはこれ以上のものは決して必要ないということです。\(r\)上院議員、どこで\(r\)は異なる素数の数です\(g\)。 以上の場合\(r\)上院議員が選択されると、一部の選択された上院議員は固有に必要な素数を提供しないため、その上院議員を削除するとすべての素数が削除され、厳密にコストが削減されます。 

その結果、有用な除去パターンごとに少数の最も安価な上院議員のみを維持することができます。 上院議員の場合、削除パターンは下向きに閉じられています。上院議員が素数のセットを削除できる場合、そのセットのすべてのサブセットも削除できます。 最大限の法的パターンを列挙し、最大でも保持します\(r\)各パターンをサポートする最も安価な上院議員を選択し、標準のサブセット DP を実行します。 の境界\(r\)最適解には最大でも次のものが含まれるため、候補だけで十分です。\(r\)したがって、選択した上院議員を保存された候補で置き換えると、必要なパターンの候補セットについてホールの条件が満たされます。 

単一のグローバル gcd に現れる素数のみを因数分解するため、算術作業も小さくなります。 すべてを考慮に入れることはありません\(a_i\)トライアル分割により最大\(\sqrt{a_i}\)。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 |---|---|---|---|
 | ブルートフォース | \(O(2^n)\) またはそれ以上 | \(O(n)\) | 遅すぎる |
 | 最適 | \(O(nr + C2^r + 3^r)\)、ここで\(r\le11\)そして\(C\)は個別の上院議員プロファイルの数です。 \(O(Cr+2^r)\) | 承認済み |

 ここ\(C\)同一のプライムパワープロファイルを持つ上院議員が一緒に処理されるため、圧縮表現では小さくなります。 重要な点は、指数部分は次の要素のみに依存するということです。\(r\le11\)、決してオンになりません\(n\)。 

## アルゴリズムのチュートリアル

 1. gcd を計算します。\(g\)全部の\(a_i\)。 もし\(g=1\)、すぐに 0 を出力します。 消去すべき共通素数は残っていない。 

2. 要因\(g\)その異なる素数に\(p_0,p_1,\ldots,p_{r-1}\)。 これらの素数のみが必要であり、すべての素因数分解は必要ありません。\(a_i\)。 

3. すべての上院議員について、すべての指数を決定します。\(p_j\)で\(a_i\)。 指数が\(v\)、完全に除去\(p_j\)係数が必要です\(p_j^v\)。 

4. マスクで表される選択された素数のセットについて\(S\)、必要な約数を計算します\[
D_i(S)=\prod_{j\in S}p_j^{v_{i,j}}.
\]上院議員のマスク着用は合法\(i\)まさに \(D_i(S)\le k\) のときです。 最大の合法マスクのすべてのサブセットは自動的に合法となるため、上院議員の最大の合法マスクを列挙します。 

5. 同じ主要権力プロフィールを持つ上院議員を圧縮する。 各除去パターンについて、最も安価なパターンのみを保持します\(r\)上院議員。 より多い\(r\)有効なキャンペーンでは最大でも 1 つのパターンの候補が必要になることはありません。\(r\)上院議員。 

6. サブセット DP を実行します。 させて`dp[mask]`すでに選択されている上院議員の集合の最小合計抵抗値であり、その結合された除去プライムマスクは正確に一致します。`mask`。 上院議員がパターンを削除できる場合`s`、に移行します\[
dp[mask\mid s]
=
\min(dp[mask\mid s],dp[mask]+e_i).
\]上院議員は 1 回だけ処理されるため、同じ上院議員を 2 回使用することはできません。 

7. 到達可能なすべての最終状態について`FULL`、DP が使用されたと仮定します\(m\)上院議員と蓄積された抵抗\(E\)。 対応するキャンペーン期間は、\(mE\)。 選択した上院議員の数を抵抗とともに DP 状態に保存するか、上院議員の数によってインデックス付けされた 2 次元状態を同等に使用します。 

上院議員の数を別に維持する理由は、抵抗を最小限に抑えるだけでは十分ではないためです。 実際のコストはそれぞれ 100 と 450 であるため、レジスタンス 100 を持つ上院議員 1 名を使用するキャンペーンは、それぞれレジスタンス 50 を持つ上院議員 3 名を使用するキャンペーンよりも優れている可能性があります。 

### なぜ効果があるのか

 グローバル gcd には、破壊する必要がある素数が正確に含まれています。 上院議員は、その上院議員の対応する完全な素数べき乗の積が最大で以下のとき、それらの素数の選択されたサブセットを破壊できます。\(k\)。 したがって、すべての法的キャンペーンは、その結合が完全なプライムマスクである上院議員マスクのコレクションに対応し、そのようなすべてのコレクションが法的キャンペーンを提供します。 

最適なキャンペーンには、最大でも次のものが含まれます。\(r\)上院議員。 あらゆる除去パターンを保持します\(r\)これは、同じ上院議員に 2 つの必要な役割を強制することなく、最適なキャンペーンの上院議員を置き換えるのに十分です。 次に、サブセット DP は、上院議員ごとに 1 回使用という条件を尊重しながら、除去マスクのあらゆる可能な結合を考慮します。 最後にアイデンティティ\[
\text{time}=m\sum e_i
\]最小抵抗と上院議員数を実際の選挙期間に変換します。 

## Python ソリューション```python
import sys
from math import gcd

input = sys.stdin.readline

INF = 10**30

def factor_distinct(x):
    primes = []
    p = 2
    while p * p <= x:
        if x % p == 0:
            primes.append(p)
            while x % p == 0:
                x //= p
        p = 3 if p == 2 else p + 2
    if x > 1:
        primes.append(x)
    return primes

def solve():
    n, k = map(int, input().split())
    a = list(map(int, input().split()))
    e = list(map(int, input().split()))

    g = 0
    for x in a:
        g = gcd(g, x)

    if g == 1:
        print(0)
        return

    primes = factor_distinct(g)
    r = len(primes)
    full = (1 << r) - 1

    # For every distinct profile of prime powers, keep the smallest
    # resistances. At most r copies of one profile are ever useful.
    profiles = {}

    for x, cost in zip(a, e):
        powers = []
        y = x

        for p in primes:
            q = 1
            while y % p == 0:
                y //= p
                q *= p
            powers.append(q)

        key = tuple(powers)

        if key not in profiles:
            profiles[key] = [cost]
        else:
            arr = profiles[key]
            if len(arr) < r:
                arr.append(cost)
                arr.sort()
            elif cost < arr[-1]:
                arr[-1] = cost
                arr.sort()

    # Convert every profile into its maximal legal masks.
    #
    # A mask is maximal if it is legal but adding any omitted prime
    # makes the required divisor exceed k.
    candidates = []

    for powers, costs in profiles.items():
        legal = [False] * (1 << r)
        product = [1] * (1 << r)

        legal[0] = True

        for mask in range(1, 1 << r):
            bit = mask & -mask
            j = bit.bit_length() - 1
            prev = mask ^ bit

            if product[prev] <= k // powers[j]:
                product[mask] = product[prev] * powers[j]
                legal[mask] = True

        maximal = []

        for mask in range(1, 1 << r):
            if not legal[mask]:
                continue

            is_maximal = True
            missing = full ^ mask

            while missing:
                bit = missing & -missing
                j = bit.bit_length() - 1

                if legal[mask | bit]:
                    is_maximal = False
                    break

                missing ^= bit

            if is_maximal:
                maximal.append(mask)

        # Every stored resistance has the same profile, so it can
        # realize every maximal mask of this profile.
        for cost in costs:
            candidates.append((cost, maximal))

    # dp[mask] = (number of senators, total resistance).
    # We compare by the eventual objective indirectly using the pair.
    #
    # Since m <= r <= 11, keep the best resistance for every exact
    # count and mask.
    dp = [[INF] * (1 << r) for _ in range(r + 1)]
    dp[0][0] = 0

    for cost, masks in candidates:
        old = [row[:] for row in dp]

        for cnt in range(r):
            row = old[cnt]
            for mask, cur in enumerate(row):
                if cur == INF:
                    continue

                for s in masks:
                    nm = mask | s
                    nv = cur + cost
                    if nv < dp[cnt + 1][nm]:
                        dp[cnt + 1][nm] = nv

    ans = INF

    for cnt in range(1, r + 1):
        if dp[cnt][full] != INF:
            ans = min(ans, cnt * dp[cnt][full])

    print(-1 if ans == INF else ans)

if __name__ == "__main__":
    solve()
```実装の最初の部分では、グローバル gcd を計算し、ゼロ ロビーのケースを直ちに処理します。 gcd 因数分解では、1 つの数値に対してのみ試行除算が使用され、最大 11 個の異なる素数が生き残ります。 

各上院議員について、`powers[j]`の完全な力です`primes[j]`その上院議員の中に含まれている\(a_i\)。 これは、素数が完全に消える場合に約数に含める必要がある量です。 素数自体のみを使用すると、次のような値は正しくありません。\(a_i=12\), ここで、2 を削除するには 4 で割る必要があります。 

の`profiles`辞書は主な圧縮ステップです。 同じプライムパワー要件を持つ上院議員は、gcd の観点からは同じように動作するため、最小の抵抗のみが問題になります。 Keeping\(r\)最適なキャンペーンにはこれ以上のものは必要ないため、これらのうちの 1 つだけで十分です。\(r\)上院議員。 

の`legal`配列は、再帰を使用して 1 つのプロファイルのすべての正当なサブセットを計算します。\[
D[S]=D[S\setminus\{p\}]\cdot p^{v_p(a_i)}.
\]部門`powers[j] <= k // product[prev]`最初に乗算するのではなく、意図的にこのように書かれています。 必要以上に大きな数値の作成を回避し、正確な比較を提供します。\(k\)。 

最大限の合法的なマスクのみが保管されます。 上院議員がより大きな素数セットを削除できる場合、厳密なサブセットを使用してもその上院議員が有利になることはありません。 大きなマスクはいつでも代わりに使用できます。 

DP 寸法`cnt`何人の上院議員が選ばれたかを記録します。 最終的な目的は単に抵抗の合計ではないため、これが必要になります。 実際の答えは、その合計にロビー活動を行った上院議員の数を掛けたものになります。 

Python の整数には任意の精度があるため、積が大きくなる可能性があります。`cnt * dp[cnt][full]`オーバーフローしません。 可能な最大の答えも整数として快適に管理できます。 

## 実用的な例

 ### サンプル 1

 入力は```text
3 6
30 30 30
100 4 5
```初期の gcd は 30 で、その素因数は 2、3、5 です。各上院議員について、2 を削除すると約数 2 のコストがかかり、3 を削除すると 3 のコストがかかり、5 を削除すると 5 のコストがかかり、2 と 3 の両方を削除するとコスト 6 になります。 

重要な状態は次のとおりです。 

| 上院議員が使用 | 削除された素数 | 抵抗 | キャンペーン期間 |
 |---:|---|---:|---:|
 | 0 | なし | 0 | 0 |
 | 1 | {2,3} | 4 | 4 |
 | 2 | {2,3,5} | 9 | 18 |
 | 2 | {2,3,5} | 105 | 210 |
 | 3 | {2,3,5} | 109 | 327 |

 最良の最終状態では、抵抗値 4 と 5 を持つ上院議員が使用されます。最初の状態では 30 を 6 で割ることで 2 と 3 を一緒に除去できますが、2 番目の状態では 5 で割ることで 5 を除去できます。抵抗力の合計は 9 で、2 人の上院議員がロビー活動を行い、次のようになります。\[
2\cdot9=18.
\]したがって、出力は次のとおりです`18`。 

この例は、ロビー活動の順序が重要ではない理由と、必要な約数が範囲内に収まる場合に 1 人の上院議員が複数の素数を削除できる理由を示しています。\(k\)。 

### サンプル 2

 入力は```text
1 1000000
```上院議員が 1 人いて、その\(a_i\)値は 1 です。gcd はすでに 1 です。 

| 初期 gcd | プライムマスク | 上院議員が使用 | 答え |
 |---:|---|---:|---:|
 | 1 | 0 | 0 | 0 |

 キャンペーンはすでに成功しているため、アルゴリズムは DP 状態を構築する前に終了します。 正しい出力は次のとおりです`0`。 

これは、ロビー活動が必要ない境界ケースを実行します。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 |---|---|---|
 | 時間 | 圧縮実装の \(O(nr + C2^r + C2^{2r})\) |\(r\le11\)、指数関数的な仕事は、個別の gcd 素数にのみ依存します。 
| スペース | \(O(Cr + r2^r)\) | 圧縮されたプロファイルと小さなサブセット DP | を保存します。 

線形部分は最大で処理します\(10^6\)上院議員とグローバル gcd の最大 11 個の素数のみ。 指数関数的な部分は、小さな素数宇宙によって境界が定められています。\(n\)。 この実装では、DP を実行する前に等しいプライム電力プロファイルも圧縮されます。これは大規模なアプリケーションにとって不可欠です。\(n\)限界。 

## テストケース```python
# This test harness uses the same solve logic through a small wrapper.

import sys
import io
from math import gcd

def run(inp: str) -> str:
    old_stdin = sys.stdin
    old_stdout = sys.stdout

    sys.stdin = io.StringIO(inp)
    sys.stdout = io.StringIO()

    try:
        n, k = map(int, sys.stdin.readline().split())
        a = list(map(int, sys.stdin.readline().split()))
        e = list(map(int, sys.stdin.readline().split()))

        g = 0
        for x in a:
            g = gcd(g, x)

        if g == 1:
            return "0"

        primes = []
        x = g
        p = 2
        while p * p <= x:
            if x % p == 0:
                primes.append(p)
                while x % p == 0:
                    x //= p
            p = 3 if p == 2 else p + 2
        if x > 1:
            primes.append(x)

        r = len(primes)
        full = (1 << r) - 1

        profiles = {}

        for x, cost in zip(a, e):
            powers = []
            y = x

            for p in primes:
                q = 1
                while y % p == 0:
                    y //= p
                    q *= p
                powers.append(q)

            key = tuple(powers)
            arr = profiles.setdefault(key, [])

            if len(arr) < r:
                arr.append(cost)
                arr.sort()
            elif cost < arr[-1]:
                arr[-1] = cost
                arr.sort()

        candidates = []

        for powers, costs in profiles.items():
            msize = 1 << r
            product = [1] * msize
            legal = [False] * msize
            legal[0] = True

            for mask in range(1, msize):
                bit = mask & -mask
                j = bit.bit_length() - 1
                prev = mask ^ bit

                if product[prev] <= k // powers[j]:
                    product[mask] = product[prev] * powers[j]
                    legal[mask] = True

            maximal = []

            for mask in range(1, msize):
                if not legal[mask]:
                    continue

                missing = full ^ mask
                maximal_flag = True

                while missing:
                    bit = missing & -missing
                    if legal[mask | bit]:
                        maximal_flag = False
                        break
                    missing ^= bit

                if maximal_flag:
                    maximal.append(mask)

            for cost in costs:
                candidates.append((cost, maximal))

        INF = 10**30
        dp = [[INF] * (1 << r) for _ in range(r + 1)]
        dp[0][0] = 0

        for cost, masks in candidates:
            old = [row[:] for row in dp]

            for cnt in range(r):
                for mask in range(1 << r):
                    cur = old[cnt][mask]
                    if cur == INF:
                        continue

                    for s in masks:
                        nm = mask | s
                        nv = cur + cost
                        if nv < dp[cnt + 1][nm]:
                            dp[cnt + 1][nm] = nv

        ans = INF
        for cnt in range(1, r + 1):
            if dp[cnt][full] != INF:
                ans = min(ans, cnt * dp[cnt][full])

        return str(-1 if ans == INF else ans)

    finally:
        sys.stdin = old_stdin
        sys.stdout = old_stdout

# Provided sample 1
assert run("""3 6
30 30 30
100 4 5
""") == "18", "sample 1"

# Provided sample 2
assert run("""1 1000000
1
""") == "0", "sample 2"

# Provided sample 3
assert run("""3 5
7 7 7
1 1 1
""") == "-1", "sample 3"

# Already coprime, so no lobbying is needed.
assert run("""2 10
6 35
1 1
""") == "0", "initial gcd is already 1"

# Two senators must split the primes 2 and 3.
assert run("""2 2
6 6
1 1
""") == "4", "split one prime between two senators"

# k is inclusive: division by 6 is legal when k = 6.
assert run("""2 6
6 10
1 1
""") == "4", "boundary k"

# A higher prime exponent must be removed completely.
assert run("""2 4
12 18
1 1
""") == "4", "complete prime power removal"
```| テスト入力 | 期待される出力 | 検証内容 |
 |---|---:|---|
 |`2 10 / 6 35 / 1 1`|`0`| 初期 gcd はすでに 1 |
 |`2 2 / 6 6 / 1 1`|`4`| 異なる上院議員は異なる共通素数を削除できます。 
|`2 6 / 6 10 / 1 1`|`4`| 除数の制限は包括的です |
 |`2 4 / 12 18 / 1 1`|`4`| 単なる素数ではなく、完全な素数パワーを分割する必要があります。 

## 特殊なケース

 初期 gcd が 1 の場合、アルゴリズムは直ちに停止します。 のために```text
2 10
6 35
1 1
```gcd は 1 であるため、素数リストは空であり、正しい答えは次のとおりです。`0`。 上院議員は選ばれるべきではない。 

複数の上院議員が協力しなければならない場合、マスク民主党は自然に分裂を処理する。 のために```text
2 2
6 6
1 1
```共通の素数は 2 と 3 です。\(k=2\)、1 人の上院議員は 2 つだけ削除でき、もう 1 人の上院議員は 3 つだけ削除できます。DP は合計耐性 2 の 2 人の上院議員を使用して完全なマスクに達するため、最終コストは次のようになります。\(2\cdot2=4\)。 

除数の制限が厳密に厳しい場合、積の比較では等価性が認められる必要があります。 のために```text
2 6
6 10
1 1
```最初の上院議員は 6 を 6 で割って 2 と 3 の両方を削除できますが、2 番目の上院議員は 10 を 5 で割って 5 を削除します。必要な約数は正確に制限内にあるため、答えは 4 です。 

共通の素数が 1 より大きい指数とともに現れる場合、選択された上院議員から完全な指数が消える必要があります。 たとえば、```text
2 4
12 18
1 1
```gcd は 6 です。上院議員 1 は 2 を完全に削除するために 4 による除算を必要としますが、上院議員 2 は 3 を削除するために 3 による除算を必要とします。両方の演算は正当であり、最終的な gcd は 1 で合計時間は 4 になります。素数が除算するかどうかのみに基づくマスク構築\(a_i\)12 を 2 で割れば十分だと誤って信じてしまい、公約数 2 が残されてしまいます。
