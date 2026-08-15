---
title: "CF 102361H - 蓬莱山輝夜"
description: "素数法 (p) と配列 (a1,ldots,an) があり、すべての配列値は非ゼロ法 (p) です。 2 つの非ゼロ残基 (a,b) については、(a^u) が (b) によって生成された循環サブグループに属するような最小の正の指数 (u) が必要です。 その値を (f(a,b)) と呼びます。"
date: "2026-08-14T02:46:07+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102361
codeforces_index: "H"
codeforces_contest_name: "2019 China Collegiate Programming Contest Qinhuangdao Onsite"
rating: 0
weight: 102361
solve_time_s: 98
verified: true
draft: false
---

[CF 102361H - 蓬莱山輝夜](https://codeforces.com/problemset/problem/102361/H)

 **評価:** -
 **タグ:** -
 **解決時間:** 1 分 38 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 素数法 (p) と配列 (a_1,\ldots,a_n) があり、すべての配列値は非ゼロ法 (p) です。 2 つの非ゼロ残基 (a,b) については、(a^u) が (b) によって生成された循環サブグループに属するような最小の正の指数 (u) が必要です。 その値を (f(a,b)) と呼びます。 

必要な答えは、配列位置の順序付きペアごとの (f(a_i,a_j)f(a_j,a_i)) の合計 (p) を換算したものです。 

最初の有用な単純化は、素数を法とする非ゼロ残基が次数 (p-1) の巡回群を形成するという事実から得られます。 したがって、入力値は乗算順序を通じて理解できます。 難しいのは、(p) が (10^{18}) まで大きくなる可能性があるため、因数分解 (p-1) ですら、(\sqrt p) までの試算ではなく、本物の整数因数分解アルゴリズムが必要になることです。 

(n) が (10^5) に達すると、すべての (n^2) ペアを直接調べることは、最大 (10^{10}) ペアの評価を意味します。 これは、競技プログラミングの制限時間でサポートできる範囲をはるかに超えています。 この解決策では、配列からの等しい構造情報を圧縮し、(p-1) の除数構造を利用する必要があります。 

誤って対処しやすい特殊なケースがいくつかあります。 たとえば (n=1) の場合、入力```
1 2
1
```(f(1,1)=1) なので、答えは (1) です。 誤って単位要素を次数 0 であるものとして扱う数式は、ここで失敗します。 

繰り返される値も重要です。 のために```
3 7
2 2 2
```(2) モジュロ (7) の次数は (3) です。 すべての順序ペアには寄与 (1) があるため、答えは (9\bmod 7=2) になります。 個別の値だけをカウントするのではなく、多重度をカウントする必要があります。 

境界値 (p-1) も有用なテストです。 のために```
2 7
1 6
```順序は(1)と(2)です。 4 つの順序ペアが (1,2,2,1) に寄与し、(6\bmod7=6) が得られます。 ゼロ以外のすべての剰余がジェネレーターであると想定する実装では、これが間違ってしまいます。 

最後に、定義では一般に (f(a,b)=0) が許可されていますが、実際の入力ではそのようなケースは決して発生しません。 (a\neq0) なので、(a) の正のべき乗は (1) であり、(1) は非ゼロ (b) によって生成されるすべての部分群に含まれます。 したがって、入力値のすべてのペアは正の値 (f) を持ちます。 

## アプローチ

 ブルートフォース手法では、すべての順序付きペア ((a_i,a_j)) が処理されます。 ペアの場合、(a_j) によって生成される要素に到達するまで繰り返し (a_i) を乗算できますが、これには 1 つのペアに対してすでに (O(p)) の作業が必要になる可能性があります。 グループ構造を認識し、各ペアの答えを gcds で計算した後でも、まだ (n^2) 個のペアが存在します。 (n=10^5) の場合、つまり (10^{10}) ペアの演算となり、遅すぎます。 

ペアの答えは、2 つの要素が循環群モジュロ (p) 内にどのように位置するかによって完全に決定されるため、総当たりが機能します。 重要な観察は、この位置が乗法順序で記述できることです。 

(q=p-1) とし、(p) を法とする原始根 (g) を選択します。 書く

 [
 a=g^A、\qquad b=g^B。 
]

 (b) によって生成されたサブグループは、次の数で割り切れる指数で構成されます。 

[
 d_b=\gcd(B,q)。 
]

 (a^u) がそのサブグループに属するような最小の正の (u) が必要です。 (a^u=g^{Au}) なので、これは意味します。 

[
 d_b\mid Au.
 ]

 最小の正の解は、

 [
 f(a,b)=\frac{\gcd(B,q)}{\gcd(A,B,q)}。 
]

 この式は、離散対数 (A) と (B) を知らなくても書き直すことができます。 (a) の乗算順序は次のようになります。 

[
 \演算子名{ord}(a)=\frac{q}{\gcd(A,q)},
 ]

 (b) についても同様です。 また、

 [
 \gcd(A,B,q)=\frac{q}{\オペレーター名{lcm}(\オペレーター名{ord}(a),\オペレーター名{ord}(b))}。 
]

 これらのアイデンティティを置き換えると、次のようになります。 

\frac{\オペレーター名{lcm}(r,s)}
 {\オペレータ名{gcd}(r,s)},
 ]

 ここで、(r=\operatorname{ord}(a)) および (s=\operatorname{ord}(b)) です。 

したがって、実際の値 (a_i) はペア計算から消えます。 必要なのは、すべての配列要素の乗算順序だけです。 

次の圧縮では、要素を順序に従ってグループ化します。 (c_d) を次数 (d) を持つ入力値の数とする。 すると、希望の合計は次のようになります。 

[
 \sum_{d\mid q}\sum_{e\mid q}
 c_d c_e
 \frac{\operatorname{lcm}(d,e)}{\gcd(d,e)}。 
]

 以来

 \frac{de}{\gcd(d,e)^2},
 ]

 (b_d=c_d d) を定義します。 答えは、

 [
 \sum_{d,e\mid q}\frac{b_d b_e}{\gcd(d,e)^2}。 
]

 残りの問題は、除数と和の変換です。 任意の正の整数 (x) について、

 [
 \frac1{x^2}=\sum_{k\mid x} h(k)、
 ]

 ここで、メビウス反転は次のようになります。 

[
 h(k)=\sum_{t\mid k}\frac{\mu(t)}{(k/t)^2}。 
]

 (k) は (p-1) の素因数で構成されているため、これを単純化すると次のようになります。 

\frac{1}{k^2}
 \prod_{r\mid k}(1-r^2)。 
]

 ここで (x=\gcd(d,e)) を代入します。 

\sum_{k\mid d,\ k\mid e}h(k)。 
]

 合計金額を交換した後、

 \sum_{k\mid q}
 h(k)
 \左(
 \sum_{\substack{d\mid q\k\mid d}} b_d
 \右)^2.
 ]

 これが中心的な削減です。 (q) のすべての約数 (k) について、(k) のすべての約数の倍数 (d) にわたる (b_d) の合計が必要なだけです。 

(q) のすべての約数は明示的に生成できます。 値 (b_d) から始めると、除数格子接尾辞変換はこれらの複数の合計を (O(\tau(q)\omega(q))) で計算します。ここで、(\tau(q)) は約数の数、(\omega(q)) は個別の素因数の数です。 整数の約数の数は (10^{18}) までで、このアプローチには十分小さいです。 

残りの数論的タスクは、(p-1) を因数分解することです。 (p-1) は (10^{18}) に近い可能性があるため、試行分割は十分ではありません。 以下の素数性テスト (2^{64}) には決定論的な Miller-Rabin を使用し、因数分解には Pollard-Rho を組み合わせます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | (O(n^2)) ペアの評価 | (O(1)) おまけ | 遅すぎる |
 | 最適 | (O(n\omega(q)\log p+\tau(q)\omega(q)+\text{因数分解})) | (O(\tau(q)+n)) | 承認済み |

 ## アルゴリズムのチュートリアル

1. (q=p-1) と因数 (q) をその個別の素因数とその指数に設定します。 (p<2^{60}) であるため、基底の固定セットを使用した決定論的な Miller-Rabin は素数性テストに十分であり、Pollard-Rho は残りの合成数を効率的に因数分解できます。 
2. すべての入力値 (a_i) について、その乗算次数モジュロ (p) を計算します。 から始める`order = q`。 (q) のすべての異なる素因数 (r) について、次のことを繰り返しテストします。 

[
 a_i^{\text{order}/r}\equiv1\pmod p.
 ]

 テストが成功したら分割する`order`(r) で再テストしてください。 乗算順序では (q) を除算する必要があり、除算が成功するたびに不要な素因数が削除されるため、最終的な値は正確に (\operatorname{ord}(a_i)) になります。 

1. 考えられるすべての順序を持​​つ入力値の数を数えます。 これを (c_d) として保存します。 (q) の約数のみが順序として現れることができます。 
2. (q) のすべての約数 (d) を生成します。 約数ごとに初期化します

 [
 b_d=c_d d\pmod p.
 ]

 (d) による乗算は、ペアの寄与を (de/\gcd(d,e)^2) として書き換えることから直接得られます。 

1. コンピューティング

 [
 S_k=\sum_{\substack{d\mid q\k\mid d}}b_d
 ]

 すべての約数 (k) について。 一度に 1 つの異なる素因数 (r) を処理します。 (dr\mid q) となるすべての約数 (d) について、(dr) に属する値を (d) に属する値に加算します。 除数を数値の降順に処理すると、(r) の指数に対するインプレース接尾辞の合計が更新されます。 

1. コンピューティング

 [
 h(k)=\frac{\prod_{r\mid k}(1-r^2)}{k^2}\pmod p.
 ]

 (p-1) のすべての約数は (p) と互いに素であるため、除算は (p) を法として有効です。 すべての約数の逆剰余を計算する代わりに、それぞれの異なる素数の逆二乗を事前に計算し、(h(k/r)) から (h(k)) を導出します。 

1. 蓄積する

 \sum_{k\mid q}h(k)S_k^2\pmod p.
 ]

 これはまさに元の double sum を変形したものです。 

### なぜ効果があるのか

 すべての入力値について、その乗算順序によって関連するサブグループ情報が完全に決まります。 積 (f(a,b)f(b,a)) は正確に (\operatorname{lcm}(r,s)/\gcd(r,s)=rs/\gcd(r,s)^2) であるため、値を順序でグループ化しても情報は失われません。 

メビウス導出関数 (h) は (1/x^2=\sum_{k\mid x}h(k)) を満たします。 この恒等式を (x=\gcd(d,e)) に適用すると、ペアごとの gcd 式が単一の公約数 (k) でインデックス付けされた合計に変換されます。 サフィックス変換はすべての値 (\sum_{k\mid d}b_d) を正確に計算するため、(h(k)S_k^2) にわたる最終的な合計には、すべての順序付きペアが元の寄与とともに正確に 1 回含まれます。 

## Python ソリューション```python
import sys
import math
import random

input = sys.stdin.readline

def is_prime(n):
    if n < 2:
        return False

    small = (2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37)
    for p in small:
        if n % p == 0:
            return n == p

    d = n - 1
    s = 0
    while d % 2 == 0:
        s += 1
        d //= 2

    # Deterministic for every n < 2^64.
    for a in (2, 325, 9375, 28178, 450775, 9780504, 1795265022):
        if a % n == 0:
            continue

        x = pow(a, d, n)
        if x == 1 or x == n - 1:
            continue

        for _ in range(s - 1):
            x = x * x % n
            if x == n - 1:
                break
        else:
            return False

    return True

def pollard_rho(n):
    if n % 2 == 0:
        return 2
    if n % 3 == 0:
        return 3

    while True:
        c = random.randrange(1, n)
        x = random.randrange(0, n)
        y = x
        d = 1

        while d == 1:
            x = (x * x + c) % n
            y = (y * y + c) % n
            y = (y * y + c) % n
            d = math.gcd(abs(x - y), n)

        if d != n:
            return d

def factor_rec(n, factors):
    if n == 1:
        return
    if is_prime(n):
        factors.append(n)
        return

    d = pollard_rho(n)
    factor_rec(d, factors)
    factor_rec(n // d, factors)

def factorize(n):
    factors = []
    factor_rec(n, factors)
    factors.sort()

    result = []
    for x in factors:
        if not result or result[-1][0] != x:
            result.append([x, 1])
        else:
            result[-1][1] += 1
    return result

def solve_data(data):
    it = iter(data.split())
    n = int(next(it))
    mod = int(next(it))
    a = [int(next(it)) for _ in range(n)]

    q = mod - 1
    factorization = factorize(q)
    primes = [r for r, _ in factorization]

    # Count elements by multiplicative order.
    freq = {}

    for x in a:
        order = q

        for r in primes:
            while order % r == 0 and pow(x, order // r, mod) == 1:
                order //= r

        freq[order] = freq.get(order, 0) + 1

    # Generate all divisors of q.
    divisors = [1]
    for r, e in factorization:
        old = divisors[:]
        mul = 1
        new = []
        for _ in range(e + 1):
            for d in old:
                new.append(d * mul)
            mul *= r
        divisors = new

    divisors.sort()
    index = {d: i for i, d in enumerate(divisors)}

    # b[d] = count[d] * d.
    values = [0] * len(divisors)
    for d, cnt in freq.items():
        values[index[d]] = (cnt * d) % mod

    # S[k] = sum_{d: k|d} b[d].
    #
    # Since d*r > d, descending order guarantees that values[d*r]
    # has already received all contributions for the current prime.
    descending = divisors[::-1]

    for r in primes:
        for d in descending:
            nd = d * r
            pos = index.get(nd)
            if pos is not None:
                values[index[d]] += values[pos]
                if values[index[d]] >= mod:
                    values[index[d]] -= mod

    # h[d] = sum_{t|d} mu(t) / (d/t)^2.
    #
    # If r is a prime divisor of d and d = r*m:
    #
    # h[d] / h[m] =
    #   (1-r^2)/r^2, if r does not divide m,
    #   1/r^2,       otherwise.
    inv_r2 = {}
    for r in primes:
        inv_r = pow(r, mod - 2, mod)
        inv_r2[r] = inv_r * inv_r % mod

    h = [0] * len(divisors)
    h[index[1]] = 1

    for d in divisors[1:]:
        for r in primes:
            if d % r == 0:
                m = d // r
                base = h[index[m]]
                inv2 = inv_r2[r]

                if m % r == 0:
                    h[index[d]] = base * inv2 % mod
                else:
                    factor = (1 - r * r) % mod
                    h[index[d]] = base * factor % mod
                break

    ans = 0
    for i in range(len(divisors)):
        ans = (ans + h[i] * values[i] % mod * values[i]) % mod

    return str(ans)

def solve():
    data = sys.stdin.buffer.read()
    sys.stdout.write(solve_data(data))

if __name__ == "__main__":
    solve()
```素数性テストでは、64 ビット整数範囲全体に対して標準の決定論的な Miller-Rabin 基底が使用されます。 (p-1) はほぼ (10^{18}) になる可能性があるため、これは重要であり、ここではミラー・ラビンを単なる確率論として扱う必要はありません。 

ポラード・ローは、すべての因数が素数になるまで再帰的に分割 (p-1) します。 分割が成功するたびに合成数が大幅に減少するため、再帰の深さは浅くなります。 

各入力値について、次数の計算は (1) ではなく (p-1) から始まります。 素数 (r) が現在の候補順序を除算し、(a^{\text{order}/r}=1) になる場合、その係数は削除できます。 テストを繰り返すと、素数べき乗が正しく処理されます。 たとえば、真の順序に (r^2) が含まれる場合、最初の除算は成功する可能性がありますが、2 番目の除算は失敗します。 

除数配列には、(1) と (p-1) を含む、(p-1) のすべての約数が含まれます。 除数からインデックスへの辞書では、除数が連続する整数であるという仮定が回避されます。 

多重和変換は除数の降順で実行されます。 素数 (r) を処理するとき、(d r) の値には、(r) の指数を増加することによって得られるすべての倍数がすでに含まれているため、それを (d) に加算すると、必要な接尾辞の合計が 1 回のパスで計算されます。 

(h(d)) の計算は (p) を法として行われます。 (p-1) のすべての約数は (p) よりも厳密に小さいため、必要なすべての逆モジュラーが存在します。 Python の整数は、(10^{18}) に近い数値を乗算するときに 64 ビット実装で発生するオーバーフローの問題も回避します。 

## 実用的な例

 付属のサンプルは、```
4 5
1 2 3 4
```ここでは (p-1=4=2^2) となります。 乗算の次数は (1,4,4,2) です。 

| 注文 (d) | 周波数 (c_d) | (b_d=c_d d) |
 | --- | --- | --- |
 | 1 | 1 | 1 |
 | 2 | 1 | 2 |
 | 4 | 2 | 8 |

 モジュロ (5) を実行すると、初期値 (b) は除数 (1,2,4) に対して (1,2,0) になります。 

素数 (2) の場合、接尾辞変換は次のようになります。 

| (k) | イニシャル (b_k) | (S_k=\sum_{k\mid d}b_d) |
 | --- | --- | --- |
 | 1 | 1 | 11 |
 | 2 | 2 | 10 |
 | 4 | 8 | 8 |

 モジュロ (5)、これらは (1,0,3) です。 

対応する (h) 値は次のとおりです。 

[
 h(1)=1,\qquad
 h(2)=\frac{1-2^2}{2^2}=-\frac34,\qquad
 h(4)=\frac{1-2^2}{4^2}=-\frac3{16}。 
]

 モジュロ (5)、これは次のようになります

 | (k) | (h(k)\bmod5) | (S_k\bmod5) | 貢献 |
 | --- | --- | --- | --- |
 | 1 | 1 | 1 | 1 |
 | 2 | 3 | 0 | 0 |
 | 4 | 2 | 3 | 18 mod 5 = 3 |

 合計は (4) となり、サンプル出力と一致します。 

トレースは主な圧縮を示しています。入力位置の順序付きペアは (16) ありますが、順序でグループ化した後は 3 つの約数 (1、2、4) のみを処理します。 

2 番目の例として、次のことを考えてみましょう。```
2 7
1 6
```ここでは (q=6=2\cdot3) となります。 (1) の順序は (1) ですが、(6=-1) の順序は (2) です。 

| 注文 (d) | 周波数 (c_d) | (b_d=c_d d) |
 | --- | --- | --- |
 | 1 | 1 | 1 |
 | 2 | 1 | 2 |
 | 3 | 0 | 0 |
 | 6 | 0 | 0 |

 複数の合計は次のとおりです

 | (k) | (S_k) |
 | --- | --- |
 | 1 | 3 |
 | 2 | 2 |
 | 3 | 0 |
 | 6 | 0 |

 4 つの元のペアの寄与は直接的には

 [
 \frac{1\cdot1}{1^2}=1、
 \クアッド
 \frac{1\cdot2}{1^2}=2、
 \クアッド
 \frac{2\cdot1}{1^2}=2、
 \クアッド
 \frac{2\cdot2}{2^2}=1。 
]

 それらの合計は (6) なので、答えは (6\bmod7=6) となります。 

この例では、identity 要素と非ジェネレータを同時に実行します。 また、式では次数が等しいかどうかを単純に比較するのではなく、2 つの次数の gcd が使用されていることも確認されます。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O(\text{ポラード-ロー} + n\omega(q)\log p+\tau(q)\omega(q)+\tau(q)\omega(q)\log p)) | 次数の計算では個別の素因数に対してべき乗剰余演算が使用されますが、除数変換では個別の素数ごとに 1 つのパスが使用されます。 
| スペース | (O(n+\tau(q))) | 入力値、次数、除数配列、因数分解データが保存されます。 

(n\le10^5) の場合、ペアごとの (O(n^2)) アプローチは不可能です。 最適化された方法は、べき乗剰余コストを除いて (n) にのみ線形に依存しますが、除数の仕事は (p-1) に依存します。 (10^{18}) までの整数の場合、約数の数は明示的な除数格子変換に十分小さいため、Pollard-Rho は (10^9) までの試行除算なしで (p-1) の因数分解を処理します。 

## テストケース```python
import io
import sys

# Paste the solve_data function and its helpers from the solution above
# before running these tests.

def run(inp: str) -> str:
    return solve_data(inp.encode()).strip()

# Provided sample
assert run("""\
4 5
1 2 3 4
""") == "4", "sample 1"

# Minimum size
assert run("""\
1 2
1
""") == "1", "minimum-size case"

# All values equal
assert run("""\
3 7
2 2 2
""") == "2", "all-equal values"

# Boundary value p-1 together with the identity
assert run("""\
2 7
1 6
""") == "6", "boundary orders"

# Mixed orders, catches confusion between gcd and lcm
assert run("""\
2 7
2 3
""") == "5", "different order structure"

# Maximum n with p=2. The only possible value is 1, so every
# ordered pair contributes 1. Since 100000^2 is even, the result is 0.
maximum_input = "100000 2\n" + " ".join(["1"] * 100000) + "\n"
assert run(maximum_input) == "0", "maximum-size case"

print("all tests passed")
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`1 2 / 1`|`1`| (1) のみを含む最小サイズと除数のセット |
 |`3 7 / 2 2 2`|`2`| 多重度と等しい次数 |
 |`2 7 / 1 6`|`6`| (p-1) を含む注文 (1) および (2) |
 |`2 7 / 2 3`|`5`| さまざまな重要な順序 |
 |`100000 2 / 1 ... 1`|`0`| 最大値 (n)、繰り返し値、および (p=2) |

 ## 特殊なケース

 最低限の入力に関しては```
1 2
1
```(q=1) であるため、因数分解は空であり、約数は (1) だけです。 (1) の次数は (q=1) に初期化され、次数 (1) の頻度は 1 で、サフィックスの合計は (1) になります。 (h(1)=1) なので、最終的な答えは (1) になります。 (p=2) の特別なケースは必要ありません。 

繰り返される値については、次の点を考慮してください。```
3 7
2 2 2
```(2) モジュロ (7) の次数は (3) であるため、周波数マップには (c_3=3) が含まれます。 すべてのペアには次数 (3,3) があり、次のようになります。 

[
 \frac{3\cdot3}{3^2}=1。 
]

 9 個の順序ペアがあるため、結果は (9\bmod7=2) になります。 周波数集約は、明示的に列挙することなく、9 つのペアすべてを処理します。 

境界値 (p-1) については、次のように考慮します。```
2 7
1 6
```順番は(1)と(2)です。 両方の値が (6) に等しいペアは (2\cdot2/2^2=1) に寄与し、混合された各ペアは (1\cdot2/1^2=2) に寄​​与します。 4 つの寄与を加算すると (6) が得られるため、出力は (6) になります。 このアルゴリズムは、任意の非ゼロ剰余が次数 (p-1) を持つことを決して想定しません。 

可能な最小の係数の場合、```
100000 2
1 1 1 ... 1
```すべての入力値は (1) であり、その順序は (1) です。 すべての順序ペアは (1) に寄与し、(10^{10}) を生成します。 (p=2) であるため、必要な結果は (10^{10}\bmod2=0) になります。 (p-1=1) の因数分解では素因数は生成されず、除数変換は自然に 1 つの除数 (1) になるため、ゼロ除算や空因数分解の問題は発生しません。
