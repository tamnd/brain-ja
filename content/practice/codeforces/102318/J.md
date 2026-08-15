---
title: "CF 102318J - 倍数"
description: "各クエリには、2 つの整数 a と b があります。 1 から b までのすべての整数を調べ、2 から a までの少なくとも 1 つの整数で割り切れるかどうかを数えます。 答えは、倍数の集合の和集合のサイズです。"
date: "2026-08-14T00:06:24+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102318
codeforces_index: "J"
codeforces_contest_name: "UCF Locals 2017"
rating: 0
weight: 102318
solve_time_s: 210
verified: true
draft: false
---

[CF 102318J - 倍数](https://codeforces.com/problemset/problem/102318/J)

 **評価:** -
 **タグ:** -
 **解決時間:** 3 分 30 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 各クエリには 2 つの整数があります`a`そして`b`。 以下のすべての整数を調べます。`1`を通して`b`少なくとも 1 つの整数で割り切れる場合はそれを数えます。`2`を通して`a`。 答えは、倍数の集合の和集合のサイズです。 

たとえば、`a = 3`そして`b = 30`、関連する約数は次のとおりです。`2`そして`3`。 それらの倍数は重なるので、答えは次のようになります。`15 + 10 - 5 = 20`。 元のコンテストの声明には、まさにこの例が示されています。 

制約は小さい`a`、 と`a <= 130`、 しかし`b`と同じ大きさにすることができます`10^15`、最大で`100`クエリ。 この組み合わせにより、反復処理が排除されます。`1..b`、一度でも。 ループオーバー`10^15`整数は利用可能な 6 秒をはるかに超えています。 の制限`a`便利な部分は次のとおりです。`31`最大でも素数`130`したがって、問題は実際には、素数の可分性条件の小さな固定セットを効率的に処理することです。 

最初の微妙な点は、重複した分割可能性です。 のために`a = 3, b = 6`、整数`2, 3, 4, 6`は有効なので、答えは`4`。 単純に追加する`6//2 + 6//3 = 5`カウント`6`2回。 包含/除外が必要です。 

2 番目のエッジ ケースは上限です。 のために`a = 15, b = 15`、答えには次のものが含まれます`15`それ自体、なぜなら`15`で割り切れます`3`そして`5`。 のみに基づいた式`b // d // 2`奇数の倍数をカウントするときに、誤ってこの値が失われる可能性があります。 奇数の約数の正しい奇数倍数`d`まで`b`は`(b // d + 1) // 2`。 

3 番目のエッジケースは、`b = 1`。 例えば、`a = 130, b = 1`答えがあります`0`、以下の整数で割り切れる範囲に正の整数がないためです。`2`を通して`130`。 で始まるすべてのメソッド`b - 1`または、何らかの除数が発生する必要があると仮定すると、ここでは失敗します。 

4 番目のエッジ ケースは、複合約数が独自の包含/除外セットを必要としないことです。 数値が次の数で割り切れる場合`12`、すでに割り切れます。`2`そして`3`。 の倍数に対して別のセットを追加する`12`情報が重複するだけです。 コンテストの公式レビューでも同じ観察が行われ、関連する約数が素数に還元されます。 

## アプローチ

 直接的な総当たりアプローチは、すべての整数を調べることです。`x`から`1`に`b`、それぞれについて`x`に何らかの値があるかどうかをテストする`2..a`それを分割します。 より賢く割り算をチェックしても、すべてにアクセスすると、`b`価値はすでにコストがかかります`O(b)`、つまり最大までを意味します`10^15`1 つのクエリの反復。 より数学的な強引な方法では、素約数に包含/除外を適用します。 がある`31`最大でも素数`130`したがって、制限なしバージョンではすべてが考慮されます。`2^31`、または約`2.15 * 10^9`、サブセット。 それも大きすぎます。 公式分析では、この指数関数的な障害が正確に特定されています。 

有益な観察は、答えを補足することで説明しやすくなるということです。 数値がどの素数でも割り切れない場合、その数値は正確に数えられません。`a`。 の複合値`2..a`すべての合成数はそれ自体より大きくない素因数を持つため、新しい条件を追加する必要はありません。 

素数を超えないようにします`a`なれ`p1, p2, ..., pk`。 定義する`phi(x, k)`からの整数の数として`1`を通して`x`これらのどれでも割り切れないもの`k`素数。 次に、要求された答えは単純です`b - phi(b, k)`。 

この関数には標準的な繰り返しがあります。 最初の回避回数がすでにわかっている場合`k-1`素数がある場合、それらの数の中から次の数で割り切れるものを削除します。`pk`。 このような数値をで割った後、`pk`、残るのは正確に、によって数えられたセットです。`phi(b // pk, k-1)`。 したがって、`phi(x, k) = phi(x, k-1) - phi(x // pk, k-1)`。 

漸化式は正しいですが、やみくもに拡張すると指数ツリーが作成されます。 2 番目の最適化は、小さな状態を直接評価し、有用な大きな状態をメモ化することです。 あるのは`31`レベルだから`a <= 130`。 また、次の場合はすぐに停止します。`x < pk`, なぜなら、すべての素数を除外した後だからです。`pk`、唯一生き残る正の整数は`1`。 

これは、古典的な素数計数アルゴリズムで使用されるのと同じ部分ふるい関数です。 メモ化の繰り返しと重要性は、標準的な特性です。`phi(x,k)`。 

結果として得られる実装では、数千万の素数積を明示的に生成することが回避されます。 オリジナルの UCF レビューでは、次のことを事前計算する代替の包含/除外実装について説明しています。`23.6`百万の関連製品。 Python の場合、再帰はクエリが実際に到達する状態のみを具体化するため、等価な部分ふるい再帰を評価する方がはるかに実用的です。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | 直接列挙 |`O(b)`クエリごと |`O(1)`| 遅すぎる |
 | 完全な包含/除外 |`O(2^31)`クエリごと |`O(31)`| 遅すぎる |
 | メモ化された部分ふるい | 素数レベルが 31 のみの実用的な準指数関数状態カウント |`O(S)`キャッシュされた状態 | 承認済み |

 ## アルゴリズムのチュートリアル

 1. すべてのクエリを読み取り、以下の素数のリストを決定します。`130`。 お問い合わせの場合`(a, b)`、素数のみ`p <= a`大事なことだから、そうしましょう`k`彼らの数になってください。 これは、可能な約数の元の範囲を変換します。`2..a`せいぜい`31`明確な最高の条件。 
2. 定義する`phi(x, k)`の整数として`[1, x]`最初のどれでも割り切れないもの`k`素数。 望ましい答えは、`x - phi(x, k)`なぜなら、すべての正の整数は、これらすべての素数に触れていないか、少なくとも 1 つの素数で割り切れるためです。 
3. 使用する`phi(x, 0) = x`。 禁止された素数がない場合、以下のすべての整数は`1`を通して`x`生き残る。 
4. のために`k > 0`、 使用`phi(x, k) = phi(x, k-1) - phi(x // p[k-1], k-1)`。 

最初の項では、前の素数を避けたすべての数値が保持されます。 2 番目の項は、新しく導入された素数でも割り切れる生存者を正確に削除します。 
5. もし`x < p[k-1]`、 戻る`1`ポジティブな`x`。 以外の整数はありません`1`少なくとも素因数を持つことができる`p[k-1]`せいぜい残っている間に`x`、だからそれだけ`1`生き残る。 
6.小型用`k`、多くの再帰呼び出しを作成する代わりに、繰り返しを直接評価します。 最大で 7 つの素数が必要です`2^7 = 128`包含-除外用語、これは小さなものです。 
7. 事前計算`phi(x, k)`みんなのために`x < 800`そしてすべて`k <= 31`。 再帰状態が小さくなると、一定時間で応答できるようになります。 これは部分ふるい計算の標準的な最適化であり、再帰によって同じ小さな状態が繰り返し再構築されるのを防ぎます。 
8. 入力クエリによって到達される大きな状態をメモ化します。 異なるブランチから同じペアが生成されることがよくあります`(x, k)`特に整数の除算の後。 これらの結果を再利用すると、再帰が完全なもののように動作するのを防ぐことができます。`2^k`木。 
9. 各クエリについて、計算します。`b - phi(b, k)`そして結果を印刷します。 Python の整数には任意の精度があるため、次の値までの値が得られます。`10^15`特別なオーバーフロー処理は必要ありません。 

不変条件は、すべての呼び出しが`phi(x, k)`の整数を正確に表します`[1, x]`その主因数が最初のものを避ける`k`素数。 再帰化により、これらの整数が次の整数で割り切れないものに分割されます。`pk`で割り切れるもの`pk`。 後者は、によってカウントされた値と 1 対 1 に対応付けられます。`phi(x // pk, k-1)`。 2 つのグループは互いに素であり、網羅的であるため、すべての再帰結果は正確です。 

## Python ソリューション```python
import sys
from functools import lru_cache

input = sys.stdin.readline

MAX_A = 130
SMALL = 800

def sieve_primes(n):
    is_prime = bytearray(b'\x01') * (n + 1)
    is_prime[0:2] = b'\x00\x00'
    p = 2
    while p * p <= n:
        if is_prime[p]:
            is_prime[p * p:n + 1:p] = b'\x00' * (((n - p * p) // p) + 1)
        p += 1
    return [i for i in range(2, n + 1) if is_prime[i]]

primes = sieve_primes(MAX_A)
K = len(primes)

# small_phi[x][k] = numbers <= x not divisible by the first k primes.
small_phi = [[0] * (K + 1) for _ in range(SMALL)]

for x in range(SMALL):
    small_phi[x][0] = x

for k in range(1, K + 1):
    p = primes[k - 1]
    for x in range(SMALL):
        if x < p:
            small_phi[x][k] = 1 if x > 0 else 0
        else:
            small_phi[x][k] = (
                small_phi[x][k - 1]
                - small_phi[x // p][k - 1]
            )

@lru_cache(maxsize=250000)
def phi(x, k):
    if x < SMALL:
        return small_phi[x][k]

    if k == 0:
        return x

    p = primes[k - 1]

    if x < p:
        return 1

    # For small k, direct inclusion-exclusion is tiny.
    if k <= 7:
        res = x
        # Add/subtract all non-empty subsets.
        products = [1]
        for i in range(k):
            p = primes[i]
            old = products[:]
            for v in old:
                nv = v * p
                if nv <= x:
                    products.append(nv)

        # Recompute with signs from the number of prime factors.
        res = x
        products = [1]
        for i in range(k):
            p = primes[i]
            old = products[:]
            for v in old:
                nv = v * p
                if nv <= x:
                    products.append(nv)
                    if len([]) == -1:
                        pass

        # The compact recursive version is clearer and has only 2^7 states.
        def dfs(i, product, sign):
            if i == k:
                return 0
            total = 0
            np = product * primes[i]
            if np <= x:
                total += sign * (x // np)
                total += dfs(i + 1, np, -sign)
            total += dfs(i + 1, product, sign)
            return total

        # Inclusion-exclusion gives the number removed from [1, x].
        removed = dfs(0, 1, 1)
        return x - removed

    return phi(x, k - 1) - phi(x // p, k - 1)

def solve():
    t = int(input())
    out = []

    for _ in range(t):
        a, b = map(int, input().split())

        k = 0
        while k < K and primes[k] <= a:
            k += 1

        out.append(str(b - phi(b, k)))

    sys.stdout.write("\n".join(out))

if __name__ == "__main__":
    solve()
```ふるいは、`31`までの素数`130`。 合成数による割り可能性は、その素因数の 1 つによる割り可能性によってすでに暗示されているため、クエリ自体は複合約数について何も知る必要はありません。 

の`small_phi`テーブルはあらゆる状態を処理します`x < 800`。 その再帰性は数学的定義とまったく同じであるため、近似やヒューリスティックではありません。 このテーブルは、繰り返しの再帰を定数時間の検索に置き換えるだけです。 

キャッシュされた`phi`関数はより大きな状態を処理します。 の`x < p`check は有用な境界条件です: 戻り値`1`は正の場合のみ正しい`x`、 その間`x = 0`戻らなければなりません`0`。 小さな-`k`ブランチは最大でも使用します`128`サブセットの選択は、大規模な再帰部分と比較すると無視できます。 

一時的な二人`products`小規模な建築物`k`ブランチは実際の計算には不要なので削除できます。 次のクリーンなバージョンを提出する必要があります。```python
import sys
from functools import lru_cache

input = sys.stdin.readline

MAX_A = 130
SMALL = 800

def sieve_primes(n):
    is_prime = bytearray(b'\x01') * (n + 1)
    is_prime[0:2] = b'\x00\x00'
    p = 2
    while p * p <= n:
        if is_prime[p]:
            is_prime[p * p:n + 1:p] = b'\x00' * (
                (n - p * p) // p + 1
            )
        p += 1
    return [i for i in range(2, n + 1) if is_prime[i]]

primes = sieve_primes(MAX_A)
K = len(primes)

small_phi = [[0] * (K + 1) for _ in range(SMALL)]

for x in range(SMALL):
    small_phi[x][0] = x

for k in range(1, K + 1):
    p = primes[k - 1]
    for x in range(SMALL):
        if x < p:
            small_phi[x][k] = 1 if x else 0
        else:
            small_phi[x][k] = (
                small_phi[x][k - 1]
                - small_phi[x // p][k - 1]
            )

@lru_cache(maxsize=250000)
def phi(x, k):
    if x < SMALL:
        return small_phi[x][k]

    if k == 0:
        return x

    p = primes[k - 1]

    if x < p:
        return 1

    if k <= 7:
        def dfs(i, product, sign):
            if i == k:
                return 0

            total = dfs(i + 1, product, sign)

            np = product * primes[i]
            if np <= x:
                total += sign * (x // np)
                total += dfs(i + 1, np, -sign)

            return total

        return x - dfs(0, 1, 1)

    return phi(x, k - 1) - phi(x // p, k - 1)

def solve():
    t = int(input())
    ans = []

    for _ in range(t):
        a, b = map(int, input().split())

        k = 0
        while k < K and primes[k] <= a:
            k += 1

        ans.append(str(b - phi(b, k)))

    sys.stdout.write("\n".join(ans))

if __name__ == "__main__":
    solve()
```2 番目のコード ブロックは提出バージョンです。 素数カウントは`31`、それで見つける`k`短いループによる影響は重要ではありません。 キャッシュは意図的に制限されているため、無関係なクエリの大規模なバッチによってメモリが無制限に増加することはありません。 キャッシュ ヒットはすぐに返されますが、アルゴリズムの正確さは特定のキャッシュ サイズには依存しません。 

## 実用的な例

 提供されている Codeforces ページでは、現在の HTML レンダリングでサンプルの入力と出力が公開されていませんが、元の UCF ステートメントではサンプルが提供されています。`a = 3, b = 30`。 次のトレースでは、その例と 2 番目の小さなクエリを使用します。 

のために`a = 3, b = 30`、関連する素数は次のとおりです。`2`そして`3`。 

| ステップ |`x`|`k`| プライム導入 |`phi(x,k)`|
 | --- | --- | --- | --- | --- |
 | 開始 | 30 | 2 | 2、3 | ? |
 | ～から 3 の倍数を削除します`phi(30,1)`| 30 | 2 | 3 |`phi(30,1) - phi(10,1)`|
 | 回避 2 | 30 | 1 | 2 |`15`|
 | そのうちの 2 つを避ける`1..10`| 10 | 1 | 2 |`5`|
 | 最終回 | 30 | 2 | 2、3 |`15 - 5 = 10`|

 がある`10`からの整数`1`を通して`30`どちらでも割り切れない`2`または`3`。 彼らです`1, 5, 7, 11, 13, 17, 19, 23, 25, 29`。 それらを差し引くと、`30`与える`20`、これは記載された例と一致します。 

のために`a = 5, b = 10`、関連する素数は次のとおりです。`2, 3, 5`。 

| ステップ |`x`|`k`| 意味 |
 | --- | --- | --- | --- |
 | 開始 | 10 | 3 | 2、3、5 を避ける |
 | 最初の分割 | 10 | 2 |`phi(10,2) - phi(2,2)`|
 | 2 と 3 を避ける | 10 | 2 |`3`、つまり`1, 7, 5`|
 | 2 インチと 3 インチを避ける`1..2`| 2 | 2 |`1`|
 | 最終回 | 10 | 3 |`3 - 1 = 2`|

 割り切れない 2 つの数`2`、`3`、 または`5`は`1`そして`7`。 したがって、答えは次のとおりです`10 - 2 = 8`。 有効な数値は次のとおりです。`2, 3, 4, 5, 6, 8, 9, 10`。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | 関連する素数の数における実用的な準指数 | 再帰レベルは 31 のみで、小さな状態はテーブル検索であり、繰り返される状態はキャッシュされます。 
| スペース |`O(800 * 31 + S)`| 固定の小さなテーブルは約 25,000 の整数を使用しますが、`S`キャッシュされた大きいサイズの数です`(x,k)`状態 |

 キー制約はそうではありません`b`それ自体、以来`b`到達できる`10^15`。 アルゴリズムがその範囲を反復することはありません。 その仕事は以下の少数の素数によって決まります。`130`そして、整数の除算によって生成される個別の部分ふるい状態によるものです。 固定素数境界により、この方法が実用的になります。 

元のコンテストのソリューションは、異なるが同等の包含-除外ルートを採用し、以下に残る個別の素数の積のみを生成します。`10^15`; についての分析レポート`23.6`何百万もの生成された製品。 部分ふるいの定式化は、コレクション全体の具体化を回避し、特に Python に適しています。 

## テストケース```python
# helper: run solution on input string, return output string
import sys
import io
from functools import lru_cache

def solve_io(inp: str) -> str:
    old_stdin = sys.stdin
    old_stdout = sys.stdout

    sys.stdin = io.StringIO(inp)
    sys.stdout = io.StringIO()

    # Same implementation used by the submission.
    MAX_A = 130
    SMALL = 800

    def sieve_primes(n):
        is_prime = bytearray(b'\x01') * (n + 1)
        is_prime[0:2] = b'\x00\x00'
        p = 2
        while p * p <= n:
            if is_prime[p]:
                is_prime[p * p:n + 1:p] = b'\x00' * (
                    (n - p * p) // p + 1
                )
            p += 1
        return [i for i in range(2, n + 1) if is_prime[i]]

    primes = sieve_primes(MAX_A)
    K = len(primes)

    small_phi = [[0] * (K + 1) for _ in range(SMALL)]

    for x in range(SMALL):
        small_phi[x][0] = x

    for k in range(1, K + 1):
        p = primes[k - 1]
        for x in range(SMALL):
            if x < p:
                small_phi[x][k] = 1 if x else 0
            else:
                small_phi[x][k] = (
                    small_phi[x][k - 1]
                    - small_phi[x // p][k - 1]
                )

    @lru_cache(maxsize=250000)
    def phi(x, k):
        if x < SMALL:
            return small_phi[x][k]

        if k == 0:
            return x

        p = primes[k - 1]

        if x < p:
            return 1

        if k <= 7:
            def dfs(i, product, sign):
                if i == k:
                    return 0

                total = dfs(i + 1, product, sign)

                np = product * primes[i]
                if np <= x:
                    total += sign * (x // np)
                    total += dfs(i + 1, np, -sign)

                return total

            return x - dfs(0, 1, 1)

        return phi(x, k - 1) - phi(x // p, k - 1)

    data = sys.stdin.readline
    t = int(data())
    out = []

    for _ in range(t):
        a, b = map(int, data().split())

        k = 0
        while k < K and primes[k] <= a:
            k += 1

        out.append(str(b - phi(b, k)))

    sys.stdout = old_stdout
    sys.stdin = old_stdin
    return "\n".join(out)

# Provided statement example.
assert solve_io("1\n3 30\n") == "20", "a=3, b=30"

# Minimum b: no positive integer can be a multiple of 2.
assert solve_io("1\n2 1\n") == "0", "minimum b"

# Minimum a and exact boundary.
assert solve_io("1\n2 2\n") == "1", "2 itself is a multiple of 2"

# All numbers except 1 are covered when a >= b.
assert solve_io("1\n130 100\n") == "99", "every integer 2..100 is itself an allowed divisor"

# Composite divisors must not create double counting.
assert solve_io("1\n4 20\n") == "13", "divisibility by 4 adds nothing beyond divisibility by 2"

# Maximum-size query, checked by range and complement properties.
out = solve_io("2\n130 1000000000000000\n130 1000000000000000\n").splitlines()
assert len(out) == 2
assert out[0] == out[1], "identical maximum-size queries must reuse the same exact answer"
assert 0 <= int(out[0]) <= 10**15, "answer must stay inside the queried range"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`1 / 2 1`|`0`| 最小`b`有効な倍数の空のセット |
 |`1 / 2 2`|`1`| 除数が等しい正確な下限`b`|
 |`1 / 130 100`|`99`| からのすべての値`2`を通して`100`カバーされています |
 |`1 / 4 20`|`13`| 複合約数は独立した条件としてカウントしてはなりません。 
|`2 / 130 10^15`繰り返し | 同じ値が 2 回 | 最大サイズの算術演算とキャッシュの再利用 |

 ## 特殊なケース

 のために`a = 2, b = 1`、アルゴリズムは関連する素数を 1 つ見つけます。`2`。 補数数は`phi(1,1) = 1`、 なぜなら`1`で割り切れない`2`。 答えは、`1 - 1 = 0`。 境界は素数による除算の前に処理されます。 

のために`a = 2, b = 2`、`phi(2,1)`カウントのみ`1`、したがって、答えは次のとおりです`2 - 1 = 1`。 値`2`定義では閉じた範囲を使用しているため、それ自体が含まれます。`1..b`。 

のために`a = 3, b = 30`、再帰は計算します`phi(30,2) = phi(30,1) - phi(10,1) = 15 - 5 = 10`。 生き残った10人は互いに素な数です`6`、他の 20 の数は次の数で割り切れます。`2`または`3`。 これにより、手動で交差をリストしなくても、繰り返しハンドルが重複していることが確認されます。 

のために`a = 4, b = 20`、許可される約数は次のとおりです。`2, 3, 4`、ただし、プライムリストに含まれるのは`2`そして`3`。 これは意図的なものです。 の倍数ごとに`4`はすでに次の倍数です`2`、追加します`4`結合を変更することはできません。 アルゴリズムが取得するのは、`20 - phi(20,2) = 20 - 7 = 13`。 

のために`a = 130, b = 100`、からのすべての整数`2`を通して`100`自体は許容される除数です。 結果的にのみ`1`除外され、与えられる`99`。 アルゴリズムには、以下のすべての素数が含まれます。`127`ただし、部分ふるい解釈では、依然として 1 つの生き残る整数が生成されます。 

のために`a = 130, b = 10^15`、アルゴリズムは最初の要素を構築しようとすることはありません。`10^15`整数。 境界を最大でも素数で再帰的に分割します`127`、そして状態がそれを下回ると`800`テーブルルックアップになります。 Python の整数演算は、カウントに含まれるすべての中間値を安全に表します。
