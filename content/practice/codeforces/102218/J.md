---
title: "CF 102218J - 簡単な作業です"
description: "0 から n - 1 までの k ごとに、i⋅j≡k(modn) を満たす順序ペア (i, j) がいくつあるかを決定する必要があります。 このような各ペアは、k 日目に正確に 1 単位を提供するため、必要な出力は単に各剰余を n を法として生成するペアの数になります。"
date: "2026-08-20T03:33:49+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102218
codeforces_index: "J"
codeforces_contest_name: "2019, XI Annual Programming Contest by ESCOM-IPN"
rating: 0
weight: 102218
solve_time_s: 440
verified: false
draft: false
---

[CF 102218J - 簡単なタスク](https://codeforces.com/problemset/problem/102218/J)

 **評価:** -
 **タグ:** -
 **解決時間:** 7 分 20 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 私たちは毎日を決める必要があります`k`から`0`に`n - 1`、注文されたペアの数`(i, j)`満足させる

 i⋅j≡k(modn)。 

このような各ペアは、毎日 1 単位だけを提供します`k`したがって、必要な出力は単に各剰余をモジュロで生成するペアの数です。`n`。 

直接解釈すると、`n × n`九九のモジュロ`n`。 この観察は問題を理解するのに役立ちますが、制約は`n <= 2.2 × 10^6`そのテーブルを構築できなくなります。 まであります

 (2.2×10 6 ) 2 =4.84×10 12

 元の問題の制限時間はわずか 2.5 秒です。 直線に近い動作をするソリューションが必要です。`n`。 

最もデリケートなケースは、次のような事実から生じます。`0`も剰余であり、合成数を法とする乗算は素数を法とする乗算とは異なる動作をします。 のために`n = 1`、ペアしかありません`(0,0)`、したがって、答えは次のとおりです`1`。 正の残基のみをループする不注意な実装では、何も生成されません。 

のために`n = 2`、ペアは`(0,0)`、`(0,1)`、`(1,0)`、`(1,1)`。 3 つは残留物を生成します`0`そして一方は残留物を生成します`1`、与える```
31
```ゼロ以外のすべての剰余が同じ数の表現を持つと誤って仮定した式は、ここでは失敗します。 

のために`n = 6`、答えは次から始まります。`15`残留物で`0`、 ない`6`。 ゼロの値は、積が次で割り切れるすべてのペアをカウントします。`6`、および複合係数は、そのようなペアを多数作成します。 これはまさに、問題を素数法算術のように扱うと間違った結果が生じる状況です。 

## アプローチ

 ブルート フォース ソリューションは、定義に直接準拠します。 の配列を作成します`n`カウンタ、すべてを繰り返します`i`そしてすべての`j`、計算します`(i * j) % n`、対応するカウンタをインクリメントします。 これは正しいです。なぜなら、すべての順序付きペアが 1 回だけ考慮され、問題で指定された剰余に正確に寄与するからです。 

問題は操作回数です。 最大のケースとしては、`n² = 4.84 × 10^12`ペア。 ペアごとの定数が非常に小さい場合でも、制限時間を大幅に超えてしまいます。 

有益な観察は、両方の修正をやめることです。`i`そして`j`。 修理`i`そしていつなのか尋ねてください
 で≡a(mod)
 解決策があります。 
しましょう
 g=gcd(i,n)。 
線形合同式の標準的な性質は次のように述べています。`ij ≡ k (mod n)`まさにそのときの解決策がある`g`分割する`k`。 この条件が成り立つとき、まさに`g`の異なる値`j`モジュロ`n`合同を満たすこと。 

それで、`i`貢献する`gcd(i,n)`残差へのペア`k`まさにいつ`gcd(i,n)`分割する`k`。 

今度はすべてをグループ化します`i`同じ gcd を持つ`n`。 もし

 gcd(i,n)=d、

 書く`i = d x`。 それから

 gcd(x,n/d)=1。 

まさにあります

 φ(n/d)

 そのような値`i`、 どこ`φ`はオイラーのトーティエント関数です。 それぞれが貢献する`d`のためのソリューション`j`、それですべて`i`gcd が等しい場合`d`貢献する

 dφ(n/d)

 あらゆる残留物に`k`で割り切れる`d`。 

その結果、

 c k = d∣n d∣k ∑ dφ(n/d)= d∣gcd(k,n) ∑ dφ(n/d)

 この公式は問題を完全に変えます。 約数を考慮する必要があるだけです`n`。 あらゆる約数について`d`、計算します

 w d ＝dφ(n/d)

 そして追加します`w_d`の倍数ごとに`d`残留物の中で`1,2,\ldots,n-1`。 残基`0`はすべての約数で割り切れるため、すべての値を受け取ります。`w_d`別に。 

総更新数は

 d∣n ∑ d n =n d∣n ∑ d 1 、

 それは`O(n log log n)`指定された範囲では線形に非常に近くなります。 また、最大の完全な合計ふるいを構築することも避けます。`n`、なぜなら、`φ(n/d)`約数について`n`が必要です。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース |`O(n²)`|`O(n)`| 遅すぎる |
 | 最適 |`O(n log log n)`|`O(n)`| 承認済み |

 ## アルゴリズムのチュートリアル

 1. 要因`n`その主力に。 因数分解が必要なのは、すべての約数を列挙できるからです。`n`のオイラー係数を計算します。`n / d`サイズを構築せずに-`n`トーティエント配列。 
2. すべての約数を生成します。`n`素因数分解から。 あるのは`τ(n)`それらのうち、それはそれに比べて小さいです`n`のために`n <= 2.2 × 10^6`。 
3. すべての約数について`d`、計算します

 w=dφ(n/d)。 

これは全員の貢献度の合計です`i`満足のいく`gcd(i,n) = d`で割り切れるすべての剰余に`d`。 

1.追加`w`すべての正の倍数に`d`下に`n`。 ループ訪問`d, 2d, 3d, ...`、これらの剰余はすべて次の式で割り切れます。`d`、式の条件と完全に一致します。 
2.追加`w`に`answer[0]`同じように。 ゼロはすべての正の整数で割り切れますが、通常の倍数ループは次から始まります。`d`ゼロには訪れません。 
3. 結果の配列を出力します。 値は次のように大きくできます。`n²`したがって、Python の整数は自然に十分な精度を提供します。 

### なぜ効果があるのか

 固定の場合`i`、合同
 in≡k(mod n)
 持っています`gcd(i,n)`のためのソリューション`j`いつ`gcd(i,n)`分割する`k`、それ以外の解決策はありません。 の値をグループ化する`i`による`d = gcd(i,n)`、 がある`φ(n/d)`グループ内の価値観を尊重し、それぞれが貢献する`d`ソリューション。 したがって、そのグループは貢献します`d φ(n/d)`で割り切れる剰余まで正確に`d`。 このアルゴリズムは、すべての約数に対してこれらの加算を正確に実行します。`d`、特別な残留物を含む`0`したがって、すべてのペアが 1 回だけカウントされます。 

## Python ソリューション```python
Pythonimport sysinput = sys.stdin.readline

def factorize(n):    factors = []    x = n    p = 2
    while p * p <= x:        if x % p == 0:            e = 0            while x % p == 0:                x //= p                e += 1            factors.append((p, e))        p += 1 if p == 2 else 2
    if x > 1:        factors.append((x, 1))
    return factors

def get_divisors(factors):    divisors = [1]
    for p, e in factors:        old = divisors[:]        power = 1
        for _ in range(e):            power *= p            for d in old:                divisors.append(d * power)
    return divisors

def phi_from_factorization(x, factors):    result = x
    for p, _ in factors:        if x % p == 0:            result -= result // p
    return result

def compute(n):    factors = factorize(n)    divisors = get_divisors(factors)
    ans = [0] * n
    for d in divisors:        w = d * phi_from_factorization(n // d, factors)
        ans[0] += w
        for k in range(d, n, d):            ans[k] += w
    return ans

def solve():    n = int(input())    ans = compute(n)
    out = sys.stdout.buffer
    # Avoid constructing one enormous output string at once.    chunk = []    for x in ans:        chunk.append(str(x))        if len(chunk) == 100000:            out.write(("\n".join(chunk) + "\n").encode())            chunk.clear()
    if chunk:        out.write(("\n".join(chunk) + "\n").encode())

if __name__ == "__main__":    solve()
```因数分解は次から始まります`2`そして、奇妙な候補者のみをテストします。 以来`n`せいぜい`2.2 × 10^6`、トライアル分割まで`sqrt(n)`安価です。 

除数ジェネレータは次のように始まります。`{1}`。 各プライムパワーごとに`p^e`、既存のすべての約数は次のように結合されます。`p`、`p²`、...、`p^e`のすべての約数を正確に生成します。`n`一度。 

特定の約数について`d`、`n // d`トーティエント内に現れる係数です。 の素因数なので、`n // d`は主な要素の中に含まれている必要があります`n`、`phi_from_factorization`を使用して合計を計算できます

 φ(x)=x p∣x ∏ (1－ p 1 )。 

内側のループは次から始まります`d`、ゼロではありません。ゼロは明示的に次のように処理されるためです。`ans[0] += w`。 ゼロから開始することも有効ですが、少し異なるループ構造が必要になります。 

答えの配列には通常の Python 整数が含まれます。 オーバーフロー処理は必要ありません。ペアの総数は次のとおりであるため、これは重要です。`n²`、それは周りにある可能性があります`4.84 × 10^12`。 

出力は 100,000 行のチャンクで書き込まれます。 これにより、すべての回答を同時に含む潜在的に大きな文字列が構築されるのではなく、一時的な出力文字列が制限された状態に保たれます。 

## 実用的な例

 ### 例 1:`n = 6`の約数`6`は`1, 2, 3, 6`。 彼らの貢献は次のとおりです。 

1φ(6)=2、
 2φ(3)=4、
 3φ(2)=3、
 6φ(1)=6。 

このアルゴリズムは、各寄与をゼロとその除数のすべての正の倍数に加算します。 

| 除数`d`|`φ(6/d)`| 貢献`dφ(6/d)`| 陽性残基が更新されました |
 | --- | --- | --- | --- |
 | 1 | 2 | 2 | 1、2、3、4、5 |
 | 2 | 2 | 4 | 2、4 |
 | 3 | 1 | 3 | 3 |
 | 6 | 1 | 6 | なし |

 剰余ゼロが受け取る`2 + 4 + 3 + 6 = 15`。 

結果の配列は次のとおりです```
1526562
```たとえば、残留物`4`で割り切れます`1`そして`2`、したがって、それは受信します`2 + 4 = 6`。 で割り切れない`3`または`6`。 

### 例 2:`n = 5`以来`5`は素数であり、約数は次のとおりです`1`そして`5`。 

| 除数`d`|`φ(5/d)`| 貢献`dφ(5/d)`| 陽性残基が更新されました |
 | --- | --- | --- | --- |
 | 1 | 4 | 4 | 1、2、3、4 |
 | 5 | 1 | 5 | なし |

 残基ゼロは両方の寄与を受け取り、次のようになります。`9`。 ゼロ以外のすべての剰余は次でのみ割り切れます。`1`したがって、ゼロ以外のすべての答えは次のようになります。`4`。 

出力は```
94444
```これは、素数係数が特に単純な形状をしているのに対し、複合係数は完全な約数の和を必要とする理由を示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |`O(n log log n)`| あらゆる約数について`d`の`n`、おおよそ更新します`n/d`ポジション。 |
 | スペース |`O(n)`| 回答配列には以下が含まれます`n`整数値。 |

 約数の合計は次の条件を満たします

 d∣n ∑ d n =n n σ(n) =O(nloglogn),

 そのため、配列の更新数はほぼ線形に保たれます。 因数分解と除数の生成にかかる時間は、最大入力の更新に比べて無視できます。 の`O(n)`回答配列は、この制約の 256 MB のメモリ制限内に問題なく収まります。 

## テストケース```python
Pythonimport sysimport io
# The functions below are the same computational functions used by the solution.
def factorize(n):    factors = []    x = n    p = 2
    while p * p <= x:        if x % p == 0:            e = 0            while x % p == 0:                x //= p                e += 1            factors.append((p, e))        p += 1 if p == 2 else 2
    if x > 1:        factors.append((x, 1))
    return factors

def get_divisors(factors):    divisors = [1]
    for p, e in factors:        old = divisors[:]        power = 1
        for _ in range(e):            power *= p            for d in old:                divisors.append(d * power)
    return divisors

def phi_from_factorization(x, factors):    result = x
    for p, _ in factors:        if x % p == 0:            result -= result // p
    return result

def compute(n):    factors = factorize(n)    divisors = get_divisors(factors)    ans = [0] * n
    for d in divisors:        w = d * phi_from_factorization(n // d, factors)
        ans[0] += w
        for k in range(d, n, d):            ans[k] += w
    return ans

def run(inp: str) -> str:    n = int(inp.strip())    ans = compute(n)    return "\n".join(map(str, ans)) + "\n"

# Provided sampleassert run("6") == "15\n2\n6\n5\n6\n2\n", "sample 1"
# Minimum sizeassert run("1") == "1\n", "n = 1"
# Small composite numberassert run("4") == "8\n4\n4\n4\n", "n = 4"
# Prime modulus, all nonzero residues have equal valuesassert run("5") == "9\n4\n4\n4\n4\n", "n = 5"
# Another composite case, useful for catching divisor/multiple errorsassert run("8") == "20\n4\n8\n4\n12\n4\n8\n4\n", "n = 8"

# Maximum-size structural test.# We do not materialize a second expected 2.2-million-line string.n = 2_200_000ans = compute(n)
assert len(ans) == n, "maximum n output length"assert sum(ans) == n * n, "every ordered pair must be counted exactly once"assert ans[0] == sum(    d * phi_from_factorization(n // d, factorize(n))    for d in get_divisors(factorize(n))), "zero residue"
```最大サイズのテストでは、予想される何百万もの出力行を埋め込むのではなく、構造特性を意図的にチェックします。 アイデンティティ`sum(ans) = n²`特に便利です。`n²`順序ペアは正確に 1 つの残基に寄与する必要があります。 

| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`1`|`1`| 剰余ゼロの最小サイズと処理 |
 |`4`|`8, 4, 4, 4`| 合成係数と反復除数の寄与 |
 |`5`|`9, 4, 4, 4, 4`| プライムモジュラスと等しい非ゼロ残基 |
 |`8`|`20, 4, 8, 4, 12, 4, 8, 4`| いくつかの素数べき約数と複数の境界 |
 |`2_200_000`| 構造チェック | 最大入力サイズ、合計ペア数、およびパフォーマンス |

 ## 特殊なケース

 のために`n = 1`、唯一のペアは`(0,0)`。 除数セットには、`1`、その貢献度は

 1⋅φ(1)=1。 

正の倍数ループは更新を実行しませんが、`ans[0]`受け取ります`1`。 出力は正確に`1`。 

のために`n = 2`、約数は`1`そして`2`。 彼らの貢献は、`1·φ(2)=1`そして`2·φ(1)=2`。 ゼロが受け取ります`3`、残留しながら`1`除数からの寄与のみを受け取ります`1`、与える`1`。 出力は`3,1`、積が偶数である 3 つのペアが正しく計算されます。 

のために`n = 5`、除数`1`貢献する`φ(5)=4`すべての剰余に、約数は`5`貢献する`5`ゼロになるだけ。 したがって、答えは次のとおりです`9,4,4,4,4`。 これにより、剰余ゼロの特別な動作が忘れられるという簡単な間違いが見つかります。 

のために`n = 6`、除数`3`貢献する`3`残留物に`0`そして`3`、一方、除数`2`貢献する`4`に`0`、`2`、 そして`4`。 残基`4`結果的に受け取る`2 + 4 = 6`、残留しながら`5`のみを受け取ります`2`。 これは、アルゴリズムが単に剰余が素因数を共有するかどうかをテストするのではなく、除数による割り可能性をテストすることを裏付けます。 

最大値については`n = 2,200,000`、アルゴリズムは決して構築しません`n × n`九九。 約数のみを処理します`n`とその倍数であるため、作業量はほぼ線形のままです。`n`。 出力値は依然として最大でも順序付けされたペアの合計数です。`n²`、Python 整数はオーバーフローすることなくその範囲を処理します。
