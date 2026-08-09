---
title: "CF 102440L - \u0420\u0430\u0437\u0434\u0435\u043b\u0435\u043d\u0438\u0435 \u043a\u0440\u043e\u043b\u0438\u043a\u043e\u0432"
description: "ウサギには (1) から (n) までの番号が付けられており、すべてのウサギには (0) または (1) の 2 つのラベルのいずれかが付けられます。 ラベルは任意に選択できません。 (b) が (a) を割るときは常に、ラベルは [ f(a)=f(b) text{OR} f(a/b) ] を満たす必要があります。"
date: "2026-08-08T14:13:45+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102440
codeforces_index: "L"
codeforces_contest_name: "2018-2019 9th BSUIR Open Programming Championship. Junior"
rating: 0
weight: 102440
solve_time_s: 499
verified: true
draft: false
---

[CF 102440L - \u0420\u0430\u0437\u0434\u0435\u043b\u0435\u043d\u0438\u0435 \u043a\u0440\u043e\u043b\u0438\u043a\u043e\u0432](https://codeforces.com/problemset/problem/102440/L)

 **評価:** -
 **タグ:** -
 **解決時間:** 8 分 19 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 ウサギには (1) から (n) までの番号が付けられており、すべてのウサギには (0) または (1) の 2 つのラベルのいずれかが付けられます。 ラベルは任意に選択できません。 (b) が (a) を割るときは常に、ラベルは次の条件を満たす必要があります。 

[
 f(a)=f(b)\ \text{OR}\ f(a/b)。 
】

 一部のウサギはすでに処方されたラベルを持っており、分割規則とすべての処方の両方を満たす完全なラベルをすべてカウントする必要があります。 答えはモジュロ (10^9+7) で求められます。 

入力構造の便利な部分は、(n\le 10^6) と (m\le18) の組み合わせです。 ウサギの数が非常に多いため、すべて (n) 個のウサギ、さらにはすべて (2^n) 個のラベル付けの状態を列挙するアルゴリズムは不可能です。 同時に、拘束されているウサギは 18 匹だけであり、これは指数部分が (n) ではなく (m) に依存することを強く示唆しています。 すべての数値が最大 (10^6) であるため、最大 18 個の所定の数値の因数分解も安価です。 

ルールを通常のローカル制約として扱うと間違った答えが得られる、いくつかの特殊なケースがあります。 まずはうさぎ(1)です。 例えば、```
1 1
1 0
```(f(1)=0) は完全に有効であり、他にウサギが存在しないため、答えは (1) になります。 不注意な解決法では、(1) が素因数を持つものとして扱われ、別のものを誤って強制する可能性があります。 

2 つ目は (f(1)=1) の可能性です。 例えば、```
3 1
1 1
```答えは(1)です。 一度 (f(1)=1) になると、すべての素数も色 (1) を持つ必要があり、その結果、すべてのウサギは色 (1) を持ちます。 これは、(f(1)=0) の主要な選択肢から生成された色だけを考慮すると、見落としがちな 1 つの完全な色です。 

3 番目のエッジ ケースは、ありえない肯定的な条件です。 例えば、```
5 2
4 1
2 0
```答えは(0)です。 条件 (f(2)=0) は、規定値 (f(4)=1) に反して (f(4)=f(2)=0) を強制します。 単に処方されたウサギの数を独立して数えるだけでは、この依存関係を見逃してしまいます。 

最後に役立つ例は次のとおりです。```
5 2
2 1
3 1
```これには答え(3)があります。 グループ (1) のウサギごとに 1 つの色があり、(f(1)=0) の場合、素数 (2) と (3) は両方とも (1) でなければなりませんが、素数 (5) は独立して (0) または (1) のいずれかになります。 したがって、2 番目のタイプのカラーリングは 2 つあり、合計 (3) になります。 

## アプローチ

 直接的な総当たりアプローチは、(0) または (1) を (n) 匹のウサギのすべてに割り当てることです。 (2^n) 個の代入があります。 各割り当てについて、(b) とその倍数 (a) を反復処理することで、すべての割り算のペアをチェックできます。 そのようなペアの数は

 [
 \sum_{b=1}^{n}\left\lfloor\frac nb\right\rfloor=\Theta(n\log n)。 
】

 (n=10^6) では、これは 1 つの色について約 (1.4\cdot10^7) 回の可分性チェックに相当し、色の数は (2^{10^6}) になります。 このアプローチは正しいですが、(n) に指数関数的に依存しているため、完全に使用できなくなります。 

重要な観察は、ルールを素約数にのみ適用することから得られます。 (p) が素数であり、(p\mid x) であるとします。 (f(p)=1) の場合、

 [
 f(x)=f(p)\text{ OR }f(x/p)=1、
 】

 したがって、(p) のすべての倍数にも色 (1) がなければなりません。 代わりに (f(p)=0) の場合、

 [
 f(x)=f(x/p)。 
】

 素因数を繰り返し削除すると、(f(1)) の特別な選択を除いて、すべての数値の色がその個別の素因数の色によって完全に決定されることがわかります。 

したがって、構造的なケースは 2 つだけになります。 

(f(1)=1) の場合、すべての素数 (p) も色 (1) を持たなければなりません。これは、ルールを (a=p,b=p) に適用してもこれが強制されるわけではありませんが、(a=p,b=1) に適用するとトートロジーになるため、別の引数が必要になります。 (p) は (a=p,b=p) として表すことができるため、それでも制限はありません。 決定的な条件は、(a=p^2,b=p) を考慮することで得られます。 

[
 f(p^2)=f(p)\text{ OR }f(p)=f(p)。 
】

 したがって、プライムカラーは最初は独立して見える可能性があります。 ただし、(f(1)=1) の場合、(b=x) との一般関係も何も強制しません。 正しい構造ステートメントは実際には少し異なります。(f(1)) は割り算ルールによって強制されません。 (f(1)=1) の場合、(f(p)=0) の素数 (p) による漸化式は (f(p)=f(1)=1) となるため、(p) は (0) にはなりません。 したがって、すべての素数は (1) であり、すべての数は (1) です。 したがって、(f(1)=1) は 1 つの色を正確に与えます。 

(f(1)=0) の場合、すべての数値 (x>1) は、その個別の素因数の色の OR を取得します。 

[
 f(x)=\bigvee_{p\mid x}f(p)。 
】

 今、未知のものは単なる素数の色です。 規定の (x=0) は、(x) のすべての素約数を強制的に (0) にします。 所定の(x=1)とは、(x)の少なくとも1つの素約数が(1)でなければならないことを意味します。 

これにより、問題は、最大 18 個の OR 節に従う素数へのブール代入を数えるという問題に変換されます。 指定された色が (1) のウサギに対して、包含/除外を使用して割り当てを数えることができます。 肯定的な制約の選択されたサブセットについて、包含-排他は、選択されたすべての OR 節を false にするように要求します。 OR 節を false にするということは、その素数をすべて (0) に設定することを意味します。 サブセットから必要な唯一の量は、強制的にゼロにされる個別の素数変数の数です。 

素数変数の数は (n=10^6) の場合に (78498) まで大きくなる可能性がありますが、正の制約​​の数は最大 18 です。 したがって、各正の制約の素因数を Python 整数ビットマスクとして表します。 制約のサブセットは、それらのマスクのビットごとの OR をとることによって処理できます。 指数部分は (2^{18}=262144) のみであり、小さいです。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | (\シータ(2^n n\log n)) | (O(n)) | 遅すぎる |
 | 最適 | (O(n\log\log n + 2^m m)) | (O(n+2^m)) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. (n) までのエラトステネスのふるいを構築します。 (n) までのすべての素数を数えるだけでなく、最大 18 個の所定の数を効率的に因数分解できるように、結果として得られる素数リストを保存します。 
2. 所定のウサギを、色(0)のウサギと色(1)のウサギに分けます。 指定されたすべての数値を個別の素約数に因数分解します。 
3. 所定のゼロ値の数のすべての素約数をセットに追加します。`forced_zero`。 (f(1)=0) の場合、すべての素因数が色 (0) を持つとき、数値は正確に色 (0) を持つため、これらの素数は強制されます。 
4. オールワンカラーリングが可能かどうかを確認します。 規定のウサギが色 (0) を持っていない場合にのみ可能です。 その場合は、回答に 1 つ追加してください。 これは別のケース (f(1)=1) を説明します。 
5. (f(1)=0) の場合、指定されたすべてのウサギを色 (1) で検査します。 その値が (1) の場合、その色を (1) にする素因数は存在しないため、この場合の寄与はゼロになります。 
6. 正の制約で発生し、まだゼロに強制されていないすべての素数にビット位置を与えます。 正の制約ごとに、これらの素数を正確に含むマスクを構築します。 ゼロ制約でのみ発生する素数はすでにゼロに固定されているため、ビットは必要ありません。 
7. (F) を、正の制約​​を考慮する前に自由のままである素数変数の総数とする。 ポジティブ制約のサブセット (S) については、それらのマスクの OR を計算します。 その設定ビットは、(S) のすべての制約が false になるためにはゼロでなければならない追加の素数です。 したがって、(S) のすべての制約が false となる代入の数は、

 [
 2^{F-\オペレーター名{ポップカウント}(\オペレーター名{OR}(S))}。 
】

 1. ポジティブ制約のすべてのサブセットに包含/除外を適用します。 偶数サイズのサブセットの場合はこの量を加算し、奇数サイズのサブセットの場合は減算します。 結果の値は、すべての正の制約を満たす (f(1)=0) 色の正確な数です。 
2. (f(1)=0) の寄与を可能なすべて 1 のカラーリングに追加し、モジュロ (10^9+7) を減算して、結果を出力します。 

包含-排他不変条件が機能する理由は、正の制約​​がその原色の OR であるためです。 このような制約は、すべての素変数が 0 の場合に正確に失敗します。 失敗した制約の選択されたコレクションについて、必要なゼロ変数は、正確にそれらの素因数セットの和集合です。 マスク OR は、共有素数を二重にカウントせずにその和集合を計算します。 次に、包含-除外では、肯定的な制約がいずれも失敗しない割り当てがカウントされます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

MOD = 1_000_000_007

def build_sieve(n):
    sieve = bytearray(b'\x01') * (n + 1)

    if n >= 0:
        sieve[0] = 0
    if n >= 1:
        sieve[1] = 0

    p = 2
    while p * p <= n:
        if sieve[p]:
            start = p * p
            count = (n - start) // p + 1
            sieve[start:n + 1:p] = b'\x00' * count
        p += 1

    primes = [i for i in range(2, n + 1) if sieve[i]]
    return sieve, primes

def factor_distinct(x, primes):
    factors = []

    for p in primes:
        if p * p > x:
            break

        if x % p == 0:
            factors.append(p)
            while x % p == 0:
                x //= p

        if x == 1:
            break

    if x > 1:
        factors.append(x)

    return factors

def solve():
    n, m = map(int, input().split())

    fixed = []
    for _ in range(m):
        x, y = map(int, input().split())
        fixed.append((x, y))

    sieve, primes = build_sieve(n)
    prime_count = len(primes)

    factorized = []
    forced_zero = set()

    for x, y in fixed:
        factors = factor_distinct(x, primes)
        factorized.append((x, y, factors))

        if y == 0:
            for p in factors:
                forced_zero.add(p)

    answer = 0

    # Case f(1) = 1.
    # Then every prime must also be 1, so the whole coloring is all ones.
    if all(y == 1 for _, y in fixed):
        answer = 1

    # Case f(1) = 0.
    positive = []

    impossible = False

    for x, y, factors in factorized:
        if y == 1:
            if x == 1:
                impossible = True
                break
            positive.append(factors)

    if not impossible:
        # Assign bit positions only to primes that can still be free.
        prime_id = {}
        next_bit = 0

        for factors in positive:
            for p in factors:
                if p not in forced_zero and p not in prime_id:
                    prime_id[p] = next_bit
                    next_bit += 1

        masks = []
        for factors in positive:
            mask = 0
            for p in factors:
                if p not in forced_zero:
                    mask |= 1 << prime_id[p]
            masks.append(mask)

        # A positive constraint with all its prime factors forced to zero
        # can never become 1.
        if any(mask == 0 for mask in masks):
            c0 = 0
        else:
            free_primes = prime_count - len(forced_zero)

            k = len(masks)
            total_subsets = 1 << k

            union = [0] * total_subsets
            c0 = 0

            for subset in range(1, total_subsets):
                bit = subset & -subset
                idx = bit.bit_length() - 1
                union[subset] = union[subset ^ bit] | masks[idx]

            for subset in range(total_subsets):
                used = union[subset].bit_count()
                ways = pow(2, free_primes - used, MOD)

                if subset.bit_count() & 1:
                    c0 -= ways
                else:
                    c0 += ways

            c0 %= MOD

        answer = (answer + c0) % MOD

    print(answer)

if __name__ == "__main__":
    solve()
```ふるいは 2 つの異なる目的で必要です。`prime_count`は、(f(1)=0) の場合にいくつの独立した素数変数が存在するかを示します。一方、素数リストを使用すると、最大 18 個の所定の数値を因数分解できます。 すべての入力値は最大 (10^6) であるため、その平方根までの素数による試行除算はごくわずかです。 

の`forced_zero`set represents information coming from prescribed zero-valued rabbits. If (x) is prescribed as zero, every prime factor of (x) has to be zero. Removing these primes from the masks is essential. They are already fixed, so counting them again as free variables would overcount assignments.

 特別な`all(y == 1)`check handles (f(1)=1). その場合、ゼロ色の素数を削除することによって得られる漸化式は矛盾します (f(1)=1)。したがって、すべての素数は 1 でなければならず、すべてのウサギは 1 になります。 まさにそのようなカラーリングが1つあります。 

(f(1)=0) の場合、すべての正の制約は、少なくとも 1 つの素因数が色 (1) でなければならないという文節になります。 包含/除外は、これらすべての句を満たす割り当てをカウントします。 の`union`配列は、次を使用して、すべてのサブセットのプライム マスクの和集合を格納します。 

[
 U(S)=U(S\setminus{i})\ \text{OR}\ M_i。 
】

 の指数`pow(2, free_primes - used, MOD)`は、選択された句が強制的に失敗した後もまだ空いている素数変数の数です。 

Python では整数オーバーフローの問題はありません。 べき乗剰余演算は次のように直接実行されます。`pow(base, exponent, MOD)`したがって、たとえ色の数学的数が膨大であっても、中間値を構築する必要はありません。 

## 実用的な例

 ### サンプル 1

 入力は```
5 2
4 1
2 0
```(5) までの素数は (2,3,5) です。 ウサギ (2) はゼロに固定されているため、素数 (2) は強制的にゼロになります。 ウサギ (4) には素因数セット ({2}) がありますが、1 として規定されています。 

| ステップ | 所定のゼロ係数 | ポジティブマスク | 有効な (f(1)=0) 割り当て | 答え |
 | --- | --- | --- | --- | --- |
 | 要因(2) | ({2}) | | | |
 | 要因(4) | ({2}) | (0) | (0) | (0) |
 | チェック (f(1)=1) | (2=0) が規定されているため無効 | | (0) | (0) |

 唯一の素因数がすでにゼロに強制されているため、正のマスクは空です。 したがって、正の条件は決して満たされません。 オール 1 の色付けも規定のゼロによって禁止されているため、最終的な答えは (0) になります。 

### サンプル 2

 入力は```
5 2
2 1
3 1
```(2,3,5) の 3 つの素数があり、強制的にゼロにされる素数はありません。 (f(1)=0) の場合、2 つの肯定的な制約は句 (2=1) と (3=1) です。 

| 句のサブセット | 強制ゼロ素数の和集合 | サイズ | サイン | 課題 |
 | --- | --- | --- | --- | --- |
 | (\varnothing) | (\varnothing) | 0 | (+) | (2^3=8) |
 | ({2}) | ({2}) | 1 | (-) | (2^2=4) |
 | ({3}) | ({3}) | 1 | (-) | (2^2=4) |
 | ({2,3}) | ({2,3}) | 2 | (+) | (2^1=2) |

 包含排除により、

 [
 8-4-4+2=2。 
】

 これらは (f(1)=0) による 2 つの色付けです。ここで、素数 (2) と (3) は両方とも 1 で、素数 (5) は任意です。 すべての所定の値は 1 であるため、オール 1 のカラーリングはさらに 1 つ寄与します。 

最終的な答えは (2+1=3) です。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O(n\log\log n + m\sqrt n + 2^m m)) | 範囲をふるいにかけ、最大 (m) 個の数値を因数分解して、正の制約​​のすべてのサブセットを処理します。 
| スペース | (O(n+2^m)) | ふるいと素数リストは (O(n)) を使用しますが、サブセット和集合配列は (O(2^m)) | を使用します。 

(n\le10^6) を使用すると、ふるいは簡単に管理でき、18 個の数値のみの因数分解は無視できます。 指数成分は (2^{18}=262144) のサブセットによって制限されており、Python にとっては十分小さい値です。 このアルゴリズムは、100 万匹のウサギに指数関数的に依存する状態空間を回避します。 

## テストケース```python
import sys
import io
from contextlib import redirect_stdout

MOD = 1_000_000_007

def solve():
    input = sys.stdin.readline

    n, m = map(int, input().split())

    fixed = []
    for _ in range(m):
        x, y = map(int, input().split())
        fixed.append((x, y))

    sieve = bytearray(b'\x01') * (n + 1)
    sieve[0] = 0
    if n >= 1:
        sieve[1] = 0

    p = 2
    while p * p <= n:
        if sieve[p]:
            start = p * p
            count = (n - start) // p + 1
            sieve[start:n + 1:p] = b'\x00' * count
        p += 1

    primes = [i for i in range(2, n + 1) if sieve[i]]

    def factor_distinct(x):
        res = []
        for p in primes:
            if p * p > x:
                break
            if x % p == 0:
                res.append(p)
                while x % p == 0:
                    x //= p
            if x == 1:
                break
        if x > 1:
            res.append(x)
        return res

    factorized = []
    forced_zero = set()

    for x, y in fixed:
        factors = factor_distinct(x)
        factorized.append((x, y, factors))
        if y == 0:
            forced_zero.update(factors)

    answer = 1 if all(y == 1 for _, y in fixed) else 0

    positive = []
    impossible = False

    for x, y, factors in factorized:
        if y == 1:
            if x == 1:
                impossible = True
                break
            positive.append(factors)

    if not impossible:
        prime_id = {}
        for factors in positive:
            for p in factors:
                if p not in forced_zero and p not in prime_id:
                    prime_id[p] = len(prime_id)

        masks = []
        for factors in positive:
            mask = 0
            for p in factors:
                if p not in forced_zero:
                    mask |= 1 << prime_id[p]
            masks.append(mask)

        if any(mask == 0 for mask in masks):
            c0 = 0
        else:
            free_primes = len(primes) - len(forced_zero)
            k = len(masks)
            total = 1 << k

            union = [0] * total
            c0 = 0

            for subset in range(1, total):
                bit = subset & -subset
                idx = bit.bit_length() - 1
                union[subset] = union[subset ^ bit] | masks[idx]

            for subset in range(total):
                ways = pow(
                    2,
                    free_primes - union[subset].bit_count(),
                    MOD
                )
                if subset.bit_count() & 1:
                    c0 -= ways
                else:
                    c0 += ways

            c0 %= MOD

        answer = (answer + c0) % MOD

    print(answer)

def run(inp: str) -> str:
    old_stdin = sys.stdin
    sys.stdin = io.StringIO(inp)

    out = io.StringIO()
    try:
        with redirect_stdout(out):
            solve()
    finally:
        sys.stdin = old_stdin

    return out.getvalue().strip()

# Provided samples
assert run("""5 2
4 1
2 0
""") == "0", "sample 1"

assert run("""5 2
2 1
3 1
""") == "3", "sample 2"

# Minimum size, f(1) = 0 gives the unique valid coloring.
assert run("""1 1
1 0
""") == "1", "minimum size with zero"

# Minimum size, f(1) = 1 gives the unique all-ones coloring.
assert run("""1 1
1 1
""") == "1", "minimum size with one"

# All prescribed values are zero. Prime 2 is forced to zero,
# while primes 3, 5, and 7 remain arbitrary.
assert run("""10 3
2 0
4 0
8 0
""") == "8", "all-equal zero constraints"

# A positive constraint on 1 is impossible when f(1) = 0,
# while the all-ones coloring remains valid.
assert run("""10 1
1 1
""") == "1", "positive constraint on one"

# Maximum n, boundary factorization at n itself.
# There are 78498 primes <= 1,000,000, and fixing n to zero
# forces exactly primes 2 and 5 to zero.
expected = pow(2, 78498 - 2, MOD)
assert run("""1000000 1
1000000 0
""") == str(expected), "maximum n boundary"

print("All tests passed.")
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`1 1 / 1 0`|`1`| 可能な最小の (n)、(1) の正しい処理 |
 |`1 1 / 1 1`|`1`| 個別の (f(1)=1) カラーリング |
 |`10 3 / 2 0, 4 0, 8 0`|`8`| 同じ強制プライム制約とオールゼロ制約の繰り返し。 
|`10 1 / 1 1`|`1`| (1) とすべて 1 の場合の肯定的制約 |
 |`1000000 1 / 1000000 0`| (2^{78496}\bmod 10^9+7) | 最大値 (n)、境界因数分解、および大きな素数 |

 ## 特殊なケース

 最初の特殊なケースは (x=1) です。 その素因数セットは空です。 これがゼロとして規定されている場合、単純に (f(1)=0) が固定され、これはプライム カラーのすべての独立した選択と互換性があります。 例えば、```
1 1
1 0
```には答えが1つあります。 アルゴリズムは素数を入れません`forced_zero`、(f(1)=0) の場合に入り、(2^0=1) のカラーリングをカウントします。 

(1) が 1 と規定されている場合、(f(1)) を 1 にできる素数が存在しないため、(f(1)=0) の場合は不可能です。 個別のすべて 1 のケースでは、正確に 1 が寄与します。```
10 1
1 1
```したがって、答えは(1)です。 

2 番目のケースは、素因数が正の制約と重なるゼロ制約です。 で```
5 2
4 1
2 0
```ゼロ制約は素数 (2) を強制的にゼロにします。 (4) の正の制約には素因数 (2) しかないため、そのマスクはゼロになります。 ゼロマスクは、対応する OR 節が永続的に false であり、(f(1)=0) の寄与が直ちにゼロになることを意味します。 ゼロ処方ではオール 1 の色付けも禁止されるため、最終結果は (0) になります。 

3 番目のケースは、多くの所定のゼロ値に同じ素数が含まれる場合です。 のために```
10 3
2 0
4 0
8 0
```3 つの制約はすべて、素数 (2) のみをゼロに強制します。 素数 (3,5,7) は独立したままであり、(2^3=8) の有効なカラーリングが得られます。 このアルゴリズムは強制素数をセットに保存するため、(2) の繰り返し発生は 1 回だけカウントされます。 

最後のケースは最大境界 (n=10^6) です。 のために```
1000000 1
1000000 0
```因数分解は (2^6\cdot5^6) であるため、素数 (2) と (5) のみが強制的にゼロになります。 (10^6) までの他のすべての素数は独立したままです。 (10^6) までの素数は (78498) 個あるので、答えは次のようになります。 

[
 2^{78498-2}\bmod 10^9+7。 
】

 アルゴリズムは、これらの主要な割り当てを決して列挙しません。 必要なのはその数だけです。だからこそ、このソリューションは許容される最大値 (n) で実用的なままになります。
