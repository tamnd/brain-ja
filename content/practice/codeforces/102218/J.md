---
title: "CF 102218J - 簡単な作業です"
description: "(0) から (n-1) までの毎日 (k) について、[icdot j equiv k pmod n.] を満たす順序ペア ((i,j)) がいくつあるかを決定する必要があります。"
date: "2026-08-17T23:24:10+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102218
codeforces_index: "J"
codeforces_contest_name: "2019, XI Annual Programming Contest by ESCOM-IPN"
rating: 0
weight: 102218
solve_time_s: 179
verified: false
draft: false
---

[CF 102218J - 簡単なタスク](https://codeforces.com/problemset/problem/102218/J)

 **評価:** -
 **タグ:** -
 **解決時間:** 2 分 59 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 (0) から (n-1) までの毎日 (k) について、いくつの順序ペア ((i,j)) が満たされるかを決定する必要があります。 

[
 i\cdot j \equiv k \pmod n。 
】

 このような各ペアは、1 日 (k) に 1 単位の容量を提供するため、必要な配列は、すべての (n^2) 個の順序ペアにわたる積 (i j \bmod n) の度数分布となります。 公式声明では、日付が (0) から (n-1) までインデックス付けされており、その範囲内の順序付きペアごとに 1 つの寄与があることが確認されています。 

直接シミュレーションでは、すべての (n^2) ペアが考慮されます。 (n) が (2.2\times10^6) と同じくらい大きい場合、これは最大 (4.84\times10^{12}) 個の剰余乗算を意味します。これは、2 秒の実装で実行できる範囲をはるかに超えています。 (O(n\sqrt n)) アプローチでも、この規模では大きすぎます。 このソリューションでは、ペアを列挙するのではなく、乗算法 (n) の算術構造を利用する必要があります。 

ゼロ剰余には、(i=0) が (j) ごとに寄与し、(ij) が (n) で割り切れる場合は常にゼロ以外の (i) も寄与するため、特別な注意が必要です。 (n=1) の場合、ペア ((0,0)) のみが存在するため、答えは単純です。`1`。 正の係数に複数の剰余があると仮定した解決策では、このケースを簡単に誤って処理する可能性があります。 

2 番目のよくある間違いは、合成数を法とする乗算を、ゼロ以外のすべての乗数が可逆であるかのように扱うことです。 たとえば、(n=4) の場合、正しい出力は次のようになります。```
8
2
4
2
```(0\cdot2)、(2\cdot1)、(2\cdot3)、および (2\cdot2) は剰余の直接の推論としては適切ではないため、値 (2) が 4 回出現します。 より体系的には、解の数は (\gcd(i,n)) に依存します。 逆モジュラのみに基づく不用意なアプローチでは、非共素乗算によって生じる追加の解を見逃すことになります。 

(n=5) のような素数係数の場合、ゼロ以外の乗数はすべて可逆です。 答えは、```
9
4
4
4
4
```ゼロ以外のすべての剰余は同じ周波数を持ちますが、ゼロの方が周波数が高くなります。 すべての残基のカウントが等しい必要があると想定する実装は、このような小さなケースでも失敗します。 

## アプローチ

 ブルートフォース ソリューションは定義に正確に従います。 (n) 個のカウンターの配列を作成し、すべての (i) とすべての (j) を反復処理し、((i j)\bmod n) を計算して、対応するカウンターをインクリメントします。 すべてのペアが 1 回処理されるため、結果は正確になります。 問題はペアの数です。 最大 (n=2{,}200{,}000) では、(2{,}200{,}000^2=4{,}840{,}000{,}000{,}000) 個のペアが存在するため、このアプローチは使用できなくなります。 

重要なのは、どの個々のペアが剰余を生成するかを尋ねることをやめ、代わりに、特定の (i) に対して固定剰余を生成する (j) 値の数を尋ねることです。 合同を考えてみる

 [
 ij\equiv k\pmod n。 
】

 (g=\gcd(i,n)) とします。 線形合同の標準的な性質は、この方程式が (g\mid k) のときに正確に解を持ち、解けるときは (n) を法とする正確に (g) の解を持つということです。 

これにより、1 つの乗数 (i) が何に寄与するかがすぐに分かります。 (g=\gcd(i,n)) の場合、(i) は (g) で割り切れるすべての解答位置 (k) に (g) を寄与し、他のすべての位置には 0 を寄与します。 

次の質問は、(n) との特定の gcd を持つ (i) の値がいくつあるかです。 (g\mid n) と仮定します。 執筆

 [
 i=gx,\qquad n=gm
 】

 与える

 [
 \gcd(i,n)=g
 】

 まさに (\gcd(x,m)=1) のときです。 (x) の範囲は (0,\ldots,m-1) であるため、そのような値は (\varphi(m)) 個あります。 (m=1) および (\varphi(1)=1) に対応する (\gcd(0,n)=n) ため、ケース (i=0) がここに含まれています。 

したがって、(n) のすべての約数 (g) について、正確に

 [
 \varphi\left(\frac ng\right)
 】

 (i) の値には gcd (g) があります。 これらの (i) のそれぞれは、(g) で割り切れるすべての (k) に (g) を与えます。 したがって、除数 (g) に関連する合計寄与率は次のようになります。 

[
 g\varphi\left(\frac ng\right)
 】

 (g) の倍数ごとに。 

したがって、最終的な式は次のようになります

 [
 \箱入り{
 c_k=
 \sum_{\substack{g\mid n\g\mid k}}
 g\varphi\left(\frac ng\right)
 }
 】

 または同様に、

 [
 c_k=
 \sum_{g\mid\gcd(k,n)}
 g\varphi\left(\frac ng\right)。 
】

 ここで必要なのは、(n) の約数を列挙することだけです。 各約数 (g) について、その重み (g\varphi(n/g)) を位置 (0,g,2g,\ldots) に加算します。 配列更新の合計数は次のとおりです。 

[
 \sum_{g\mid n}\frac nd=\sum_{g\mid n}\frac ng=\sigma(n),
 】

 無害なエンドポイント規約まで。 これは (n^2) よりも大幅に小さくなります。 最大入力の場合、(2{,}200{,}000=2^6\cdot5^5\cdot11) であるため、約数は 84 個のみで、約数の合計は (5{,}952{,}744) のみです。 

最初に (n) を因数分解し、そのすべての約数を生成し、素因数分解から直接 (\varphi(n/g)) を計算できます。 (n) までのふるいは必要ないため、実装がより単純になり、メモリ効率が高くなります。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | (O(n^2)) | (O(n)) | 遅すぎる |
 | 最適 | (O(\sqrt n+\sigma(n))) | (O(n+\tau(n))) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. (n) を素数累乗に因数分解します (n=\prod p^a)。 (n\le2.2\times10^6) であるため、(O(\sqrt n)) 個の候補除数のみをチェックする必要があるため、試行除算で十分です。 
2. (n) のすべての約数 (g) を生成します。 この生成中に、(\varphi(n/g)) も計算します。 (p^b) が (n/g) の素数の残りの累乗である場合、全体への寄与は、(b=0) の場合は (1)、それ以外の場合は (p^{b-1}(p-1)) になります。 
3. 各約数 (g) について、その重みを計算します。 

[
 w=g\varphi(n/g)。 
】

 (\gcd(i,n)=g) を満たす (i) の値は正確に (\varphi(n/g)) 個あり、そのような各 (i) は (g) で割り切れるすべての剰余に (g) の解を与えます。

1. (g) で割り切れるすべての配列位置に (w) を追加します。 影響を受ける位置は (0,g,2g,\ldots,n-g) です。 ゼロはすべての正の約数で割り切れるため、位置ゼロが意図的に含まれています。 
2. すべての約数を処理した後、結果の配列を出力します。 すべての順序ペアは最初の座標の gcd に従って考慮されているため、位置 (k) での累積値は、積が (k) モジュロ (n) に一致するペアの数と正確に一致します。 

### なぜ効果があるのか

 剰余 (k) を固定します。 (g=\gcd(i,n)) に従って、考えられるすべての最初の座標 (i) を分割します。 そのような (i) の 1 つとして、合同式 (ij\equiv k\pmod n) には、(g\mid k) の場合 (j) に対する (g) つの解があり、それ以外の場合には解がありません。 gcd (g) を持つ最初の座標は正確に (\varphi(n/g)) 個あります。 したがって、(g\mid k) の場合、このグループのすべての最初の座標は正確に (g\varphi(n/g)) を (c_k) に寄与します。 アルゴリズムは、(g) のすべての倍数に正確にその数量を加算するため、すべての有効なペアは 1 回寄与し、すべての無効なペアはゼロに寄与します。 すべての約数を合計すると、毎日の正確な容量が得られます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

def factorize(n):
    factors = []

    if n % 2 == 0:
        e = 0
        while n % 2 == 0:
            n //= 2
            e += 1
        factors.append((2, e))

    p = 3
    while p * p <= n:
        if n % p == 0:
            e = 0
            while n % p == 0:
                n //= p
                e += 1
            factors.append((p, e))
        p += 2

    if n > 1:
        factors.append((n, 1))

    return factors

def generate_terms(factors):
    terms = []

    def dfs(pos, divisor, phi_quotient):
        if pos == len(factors):
            terms.append((divisor, phi_quotient))
            return

        p, a = factors[pos]

        p_powers = [1]
        for _ in range(a):
            p_powers.append(p_powers[-1] * p)

        for e in range(a + 1):
            remaining = a - e

            if remaining == 0:
                phi_part = 1
            else:
                phi_part = (p - 1) * p_powers[remaining - 1]

            dfs(
                pos + 1,
                divisor * p_powers[e],
                phi_quotient * phi_part
            )

    dfs(0, 1, 1)
    return terms

def solve():
    n = int(input())

    factors = factorize(n)
    terms = generate_terms(factors)

    ans = [0] * n

    for divisor, phi_quotient in terms:
        weight = divisor * phi_quotient

        for k in range(0, n, divisor):
            ans[k] += weight

    sys.stdout.write('\n'.join(map(str, ans)))

if __name__ == "__main__":
    solve()
```の`factorize`関数は (n) の素数累乗を抽出します。 可能な最大の入力の平方根はわずか約 1484 であるため、試行分割は主な出力作業に比べてわずかです。 

再帰的`generate_terms`関数は、素因数分解を使用して約数を列挙します。 (n) に (p^a) が含まれる場合、除数 (g) 内の (p) の指数 (e) を選択すると、指数 (a-e) が (n/g) 内に残ります。 コードは対応する合計係数を即座に計算するため、生成されたすべてのペアは正確に`(g, phi(n/g))`。 

メインループは除数の寄与を直接実装します。 除数の場合`divisor`、値`weight`は (g\varphi(n/g)) です。 範囲は ではなくゼロから始まります`divisor`剰余 0 はすべての約数で割り切れ、すべての gcd クラスからの寄与を受け取るためです。 

Python の整数には任意の精度があるため、オーバーフローの問題はありません。 固定幅言語では、個々の容量が (2^{31}-1) よりもはるかに大きくなる可能性があるため、64 ビット整数が適切な型です。 

回答配列は Python のリスト表現を使用します。 (22) 百万の位置では、これは 256 MB のメモリ制限内に収まりますが、整数の加算を繰り返す場合、ボックス化された高レベルのマッピング構造よりもかなり高速になります。 

## 実用的な例

 (n=6) の場合、素因数分解は (2\cdot3) です。 除数の項は簡単に導出できます。 

[
 \begin{配列}{c|c|c}
 g & \varphi(6/g) & g\varphi(6/g)\
 \hline
 1 & \varphi(6)=2 & 2\
 2 & \varphi(3)=2 & 4\
 3 & \varphi(2)=1 & 3\
 6 & \varphi(1)=1 & 6
 \end{配列}
 】

 配列更新のトレースは次のとおりです。 

| 約数 (g) | 重量 | ポジションが更新されました | 更新後の配列 |
 | --- | --- | --- | --- |
 | 1 | 2 | 0、1、2、3、4、5 | 2、2、2、2、2、2 |
 | 2 | 4 | 0、2、4 | 6、2、6、2、6、2 |
 | 3 | 3 | 0、3 | 9、2、6、5、6、2 |
 | 6 | 6 | 0 | 15、2、6、5、6、2 |

 最終的な配列は、まさにサンプル出力です。 トレースは、ゼロがすべての約数から寄与を受け取るのに対し、ゼロ以外の各剰余はそれ自体の約数の重みのみを受け取る理由を示しています。 

(n=5) (素数) の場合、約数は (1) と (5) だけです。 

| 約数 (g) | 重量 | ポジションが更新されました | 更新後の配列 |
 | --- | --- | --- | --- |
 | 1 | (\varphi(5)=4) | 0、1、2、3、4 | 4、4、4、4、4 |
 | 5 | (5\varphi(1)=5) | 0 | 9、4、4、4、4 |

 これは、素数係数の特殊なケースを示しています。 すべての非ゼロ乗数は素数を法として可逆であるため、すべての非ゼロ残基は同じ 4 つの寄与を受け取ります。 ゼロは、乗算器から 5 つの追加の寄与を受け取ります (i=0)。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O(\sqrt n+\sigma(n))) | 因数分解コスト (O(\sqrt n))、除数更新ループは (\sum_{g\mid n}n/g=\sigma(n)) 回の反復を実行します。 
| スペース | (O(n+\tau(n))) | 答えの配列には (n) 個のエントリがあり、除数リストには (\tau(n)) 個のエントリがあります。 

ブルート フォースとの決定的な違いは、算術更新の数が (n^2) ではなく、(n) の除数構造に関連付けられていることです。 最大入力では、(n) には 84 個の約数と (\sigma(n)=5{,}952{,}744) しかないため、直接列挙に必要な (4.84\times10^{12}) 回の演算に比べて、更新フェーズは小さいままです。 メモリ消費量の大部分は (n) 要素の応答配列によって占められ、256 MB 以内に収まります。 

## テストケース```python
import sys
import io

def solution(data: str) -> str:
    n = int(data.strip())

    def factorize(x):
        factors = []

        if x % 2 == 0:
            e = 0
            while x % 2 == 0:
                x //= 2
                e += 1
            factors.append((2, e))

        p = 3
        while p * p <= x:
            if x % p == 0:
                e = 0
                while x % p == 0:
                    x //= p
                    e += 1
                factors.append((p, e))
            p += 2

        if x > 1:
            factors.append((x, 1))

        return factors

    factors = factorize(n)
    terms = []

    def dfs(pos, divisor, phi_quotient):
        if pos == len(factors):
            terms.append((divisor, phi_quotient))
            return

        p, a = factors[pos]

        powers = [1]
        for _ in range(a):
            powers.append(powers[-1] * p)

        for e in range(a + 1):
            remaining = a - e

            if remaining == 0:
                phi_part = 1
            else:
                phi_part = (p - 1) * powers[remaining - 1]

            dfs(
                pos + 1,
                divisor * powers[e],
                phi_quotient * phi_part
            )

    dfs(0, 1, 1)

    ans = [0] * n

    for divisor, phi_quotient in terms:
        weight = divisor * phi_quotient
        for k in range(0, n, divisor):
            ans[k] += weight

    return '\n'.join(map(str, ans))

# Provided sample
assert solution("6") == "15\n2\n6\n5\n6\n2", "sample 1"

# Minimum input
assert solution("1") == "1", "n = 1"

# Prime n, all nonzero residues have equal capacities
assert solution("5") == "9\n4\n4\n4\n4", "prime modulus"

# Composite n with repeated prime factors
assert solution("4") == "8\n2\n4\n2", "composite modulus"

# Maximum-size input.
# Checking the complete 2.2-million-line string directly would waste memory,
# so verify its size and boundary values.
maximum = solution("2200000")
maximum_lines = maximum.splitlines()

assert len(maximum_lines) == 2200000, "maximum n output length"
assert maximum_lines[0] == "84000000", "maximum n c[0]"
assert maximum_lines[-1] == "800000", "maximum n c[n-1]"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`1`|`1`| 最小サイズとゼロ剰余の特別な役割 |
 |`5`|`9, 4, 4, 4, 4`| プライムモジュラスと等しい非ゼロ容量 |
 |`4`|`8, 2, 4, 2`| 合成係数と非可逆乗数 |
 |`2200000`| 2,200,000 行、最初`84000000`、 最後`800000`| 最大入力サイズ、出力境界、およびパフォーマンス |

 ## 特殊なケース

 (n=1) の場合、可能なペアは ((0,0)) のみです。 因数分解には素因数がないため、除数ジェネレーターは (\varphi(1)=1) で (g=1) のみを生成します。 更新ループは位置 0 に (1) を加算し、正確な値を生成します。```
1
```から除数の列挙を開始するソリューション`2`唯一の貢献を黙って見逃してしまうでしょう。 

(n=4) の場合、除数の寄与により、合成係数に gcd 引数が必要な理由が明らかになります。 項は、(g=1) と重み (\varphi(4)=2)、(g=2) と重み (2\varphi(2)=2)、および (g=4) と重み (4\varphi(1)=4) です。 最初の項はすべての位置を更新し、2 番目の項は位置 0 と 2 を更新し、3 番目の項は 0 のみを更新します。 結果は```
8
2
4
2
```0 の位置は (2+2+4=8) を受け取り、2 番目の位置は (2+2=4) を受け取ります。 これは、すべての非ゼロ乗算器がちょうど 1 つの逆モジュラーを持つと仮定する実装を捕捉します。 

(n=5) の場合、約数は (1) と (5) のみです。 除数 (1) はすべての位置 (\varphi(5)=4) に寄与しますが、除数 (5) はゼロにのみ寄与します。 結果は```
9
4
4
4
4
```これは、解がゼロを通常の剰余として扱い、乗数 (i=0) がすべての可能な (j) についてゼロに寄与することを忘れるという、逆の間違いを捕らえます。 

最大値 (n=2{,}200{,}000) の場合、素因数分解は (2^6\cdot5^5\cdot11) となり、約数は 84 になります。 更新ループは (5{,}952{,}744) 個の加算のみを実行しますが、出力には (22) 万個の容量がすべて含まれています。 最初の値は (84{,}000{,}000) で、次から取得されます。 

[
 \sum_{g\mid n}g\varphi(n/g),
 】

 (\gcd(n-1,n)=1) であるため、残基 (n-1) に対応する最終値は (800{,}000=\varphi(n)) になります。 このケースでは、意図した漸近動作と、位置 0 と (n-1) の配列境界の両方が実行されます。
