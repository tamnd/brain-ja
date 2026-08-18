---
title: "CF 102253J - ナップザックの旅"
description: "(n) 種類の食品があります。 タイプ (i) は (i) 単位の体積を占め、(0) 個と (ai) 個の部分の間で選択できます。 また、機器タイプを 1 つだけ選択する必要があります。"
date: "2026-08-17T21:43:32+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102253
codeforces_index: "J"
codeforces_contest_name: "2017 Chinese Multi-University Training, BeihangU Contest"
rating: 0
weight: 102253
solve_time_s: 163
verified: true
draft: false
---

[CF 102253J - ナップザックの旅](https://codeforces.com/problemset/problem/102253/J)

 **評価:** -
 **タグ:** -
 **解決時間:** 2 分 43 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 (n) 種類の食品があります。 タイプ (i) は (i) 単位の体積を占め、(0) 個と (a_i) 個の部分の間で選択できます。 また、機器タイプを 1 つだけ選択する必要があります。 機器 (j) は (b_j) 単位の体積を占めており、同じ体積の 2 つの機器が異なるインデックスを持つ場合でも、異なる選択肢になります。 

ナップザックの容量は2nです。 選択した機器の容積が (b) の場合、食品は正確に (2n-b) を占める必要があります。 (f(s)) を、総体積 (s) を持つ有効な食品選択の数とします。 最終的な答えは単純に、

 [
 \sum_{j=1}^{m} f(2n-b_j)。 
】

 入力には約 100 個のテスト ケースを含めることができ、(n) は (5\cdot 10^4) に達します。 (2n) 個の可能な体積に対する二次アルゴリズムはすでに大きすぎるため、すべての (n) 種類の食品に対する従来の有界ナップザック遷移は (O(n^2)) になります。 最大 5 つのテスト (n\ge 1000) という制限により、おおよそ (O(n\sqrt n)) 個の解が意図されていることが分かります。 また、メモリ制限により、完全な 2 次元の動的プログラムよりも、少数の 1 次元配列が優先されます。 

特徴的な条件は (a_1<a_2<\cdots<a_n) です。 (a_i) は別個の非負の整数であるため、(a_i\ge i-1) となります。 この一見小さな観察が、生成関数を管理しやすくするための鍵となります。 

不注意な実装を黙って破ってしまう可能性のある境界例がいくつかあります。 考慮する```
1 1
0
1
```食料はまったくなく、ナップザックに詰められるのは装備だけなので、答えは次のとおりです。`Case #1: 1`。 すべての食品の種類が少なくとも 1 個は貢献できると想定した実装では、不可能な選択がカウントされることになります。 

繰り返し使用される機器のボリュームも、繰り返し選択し続ける必要があります。 のために```
2 3
0 1
4 4 4
```食品のボリュームは 0 でなければならないため、食品の選択は 1 つだけです。 3 つの異なる機器タイプがあるため、答えは次のとおりです。`Case #1: 3`。 (b_i) 値を重複排除すると、誤って 1 が返されます。 

制限のない分割が別の表現を与える場合でも、食品の上限は尊重されなければなりません。 のために```
2 1
0 1
2
```唯一可能な餌の量は (2) で、タイプ 2 を 1 つ摂取することによって得られます。タイプ 1 を 2 つ摂取することは (a_1=0) のため禁止されています。 答えは、`Case #1: 1`。 分子因数を復元せずに分母を通常の制限のない分割関数として扱うと、2 つの表現がカウントされます。 

同じ境界の問題が最大の装置容積でも発生します。 (b_j=2n) の場合、必要な食料量はゼロであり、食料の選択は 1 つだけ存在します。つまり、何も摂取しません。 係数 (f(0)) は、すべての多項式演算を通じて 1 に等しくなければなりません。 

## アプローチ

 直接的な解決策では、あらゆる種類の食品の可能な数をすべて列挙し、その総体積を計算し、残りの容量がいずれかの機器の部品と一致するかどうかを確認できます。 固定機器タイプの場合、これは検査します

 [
 \prod_{i=1}^{n}(a_i+1)
 】

 食べ物の組み合わせ。 (a_i) は個別であり、([0,2n]) 内にあるため、(a_i=n+i) を選択することで可能な最大の積が得られます。 

\frac{(2n+1)!}{(n+1)!}。 
】

 最大 (2n) 個の機器タイプを掛け合わせると、これは完全に不可能になります。 (a_i\ge i-1) から導かれる弱い下限 (\prod_i(a_i+1)\ge n!) でさえ、(n=5\cdot10^4) ではすでに天文学的に大きくなっています。 

通常の動的プログラミングのアプローチは、概念的にははるかに優れています。 あらゆる量を取得し、境界のあるナップザック遷移で各食品の種類を処理する方法の数を維持できます。 残念ながら、(n) 個のタイプと (2n) 個の可能なボリュームがあるため、単純な実装には依然として (O(n^2)) のコストがかかります。 

問題を圧縮する便利な方法は、その通常の生成関数を作成することです。 食品タイプ (i) の場合、考えられる寄与は次のとおりです。 

\frac{1-x^{(a_i+1)i}}{1-x^i}。 
】

 したがって、

 \prod_{i=1}^{n}
 \frac{1-x^{(a_i+1)i}}{1-x^i}。 
】

 (x^{2n}) までの係数のみが重要なので、すべての多項式計算は (x^{2n+1}) を法として実行できます。 これは公式社説で使用された主な削減です。 

次に分子について考えてみましょう。 なぜなら (a_i\ge i-1)、

 [
 (a_i+1)i\ge i^2。 
】

 (i^2>2n) の場合、因数は

 [
 1-x^{(a_i+1)i}
 】

 は単なる (1) モジュロ (x^{2n+1}) です。 したがって、(O(\sqrt n)) 個の分子因子のみが答えに影響を与えることができます。 これらの係数を (O(n\sqrt n)) 時間で明示的に乗算して多項式にすることができます。 

分母は、整数パーティションの生成関数に似ています。 

[
 P(x)=\prod_{i\ge1}\frac1{1-x^i}
 =\sum_{k\ge0}p(k)x^k。 
】

 オイラーの五角形数の漸化式では、(p(k)) から (k=2n) までを (O(n\sqrt n)) 時間で計算します。 標準的な再発は、

 [
 p(k)=
 \sum_{r\ge1}(-1)^{r+1}
 \左(
 p\left(k-\frac{r(3r-1)}2\right)
 +
 p\left(k-\frac{r(3r+1)}2\right)
 \右）。 
】

 すべての正の整数ではなく、因数 (1,\ldots,n) のみを含む分母が必要です。 次数 (2n) までは次のように書くことができます。 

P(x)\prod_{i=n+1}^{2n}(1-x^i)。 
】

 (i,j>n) の 2 乗の積 (x^i x^j) は次数が (2n) より大きいため、(x^{2n+1}) を法とします。 

1-\sum_{i=n+1}^{2n}x^i。 
】

 したがって、分母係数は単純なプレフィックス合計を使用して分割番号から取得されます。 これは、(O(n\sqrt n)) 解の背後にある 2 番目の重要な観察です。 

ブルートフォースアプローチと最適アプローチは次のように要約できます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | (O\left(m\prod_i(a_i+1)\right)) | (O(n)) | 遅すぎる |
 | バウンドナップザック DP | (O(n^2)) | (O(n)) | 遅すぎる |
 | 生成関数 + 五角形再帰 | (O(n\sqrt n+m)) | (O(n)) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. (N=2n) とします。 機器アイテムは正の体積を持ち、ナップザックの容量は正確に (N) であるため、次数 (0) から (N) までの (F(x)) の係数のみが必要です。 
2. オイラーの五角形漸化式を使用して、無制限の分割番号 (p(0),p(1),\ldots,p(N)) を計算します。 ここで、(p(k)) は、任意の正の整数部分サイズを使用して (k) のパーティションをカウントします。 
3. 無制限のパーティション シーケンスを食品の種類の分母 (1,\ldots,n) に変換します。 (q(k)) を次の係数とします。 

[
 \prod_{i=1}^{n}\frac1{1-x^i}。 
】

(k\le n) の場合、(q(k)=p(k))。 (k>n) の場合、追加の無制限のパーティションは、まさに (n) より大きい 1 つの部分を含むパーティションです。 (k\le2n) なので、そのような部分は最大 1 つだけ存在します。 同様に、上記の多項式恒等式を使用すると、

 [
 q(k)=p(k)-\sum_{r=0}^{k-n-1}p(r)。 
】

 実行中のプレフィックス合計は、そのようなすべての係数を線形時間で計算します。 

1. これらの分母係数を作業配列 (f) にコピーします。 最初は (f(k)=q(k)) で、これはあらゆる食品タイプを上限なしで出現させることに対応します。 
2. 分子因数を処理する

 [
 1-x^{(a_i+1)i}。 
】

 タイプ (i) の場合、次のように定義します。 

[
 t_i=(a_i+1)i。 
】

 (t_i>N) の場合、その係数は注目する係数に影響を与えません。 (a_i) が増加しているため、値 (t_i) も増加しているため、最初の (i) で停止できます。 

有用な (t_i) ごとに、現在の多項式に (1-x^{t_i}) を掛けます。 係数形式では、

 [
 f[k]\leftarrow f[k]-f[k-t_i]。 
】

 更新は、大きい (k) から (t_i) まで実行する必要があります。 降順では、すべての (f[k-t_i]) は、ゼロから 1 へのナップザック遷移とまったく同じように、この係数が適用される前の多項式から取得されます。 

1. 有用な分子因数をすべて適用した後、(f(s)) は、総体積が (s) となる合法的な食品選択の数となります。 体積 (b_j) のすべての機器アイテムに対して、食品の体積 (N-b_j) が必要です。 追加

 [
 f[N-b_j]
 】

 すべての機器インデックス (j) について、機器タイプが異なれば表現方法も異なるため、重複を保持します。 

### なぜ効果があるのか

 1 つの食品タイプの生成関数は、そのタイプの許容数をすべて 1 回だけ記録します。 これらの係数を乗算すると、独立した選択肢が結合されるため、(x^s) の係数は、総量 (s) のうちの食品の選択を正確にカウントします。 

各有限幾何級数を次のように置き換えます。 

[
 \frac{1-x^{(a_i+1)i}}{1-x^i}
 】

 は、問題を無制限の分母と、それぞれの上限を超える選択を削除する補正係数に分割します。 分母は分割生成関数によって正確に計算され、関連するすべての分子係数が 1 回だけ適用されます。 省略されたすべての分子因数は (2n) より大きい次数を持っているため、答えで使用される係数に影響を与えることはできません。 

最後に、機器 (j) を選択すると、正確に (2n-b_j) 個の量の食料が残されるため、すべての機器インデックスに対して対応する係数を 1 回合計すると、すべての有効な梱包が 1 回だけカウントされます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

MOD = 1_000_000_007

# Partition numbers p(k), initially p(0) = 1.
part = [1]

def ensure_partitions(N):
    """Extend part[] so that it contains p(0)..p(N)."""
    old = len(part)
    if old > N:
        return

    part.extend([0] * (N + 1 - old))

    pent = []
    r = 1
    while True:
        g1 = r * (3 * r - 1) // 2
        if g1 > N:
            break

        sign = 1 if (r & 1) else -1
        pent.append((g1, sign))

        g2 = r * (3 * r + 1) // 2
        if g2 <= N:
            pent.append((g2, sign))

        r += 1

    # The generalized pentagonal numbers are generated in increasing order.
    for k in range(old, N + 1):
        s = 0
        for g, sign in pent:
            if g > k:
                break
            if sign == 1:
                s += part[k - g]
            else:
                s -= part[k - g]
        part[k] = s % MOD

def solve_case(n, m, a, b):
    N = 2 * n
    ensure_partitions(N)

    # Start with P(x) = sum p(k)x^k.
    f = part[:N + 1]

    # Replace unrestricted partitions by partitions whose parts are <= n.
    # For k > n, subtract sum_{r=0}^{k-n-1} p(r).
    prefix = 0
    for k in range(n + 1, N + 1):
        prefix += part[k - n - 1]
        if prefix >= MOD:
            prefix -= MOD

        value = f[k] - prefix
        if value < 0:
            value += MOD
        f[k] = value

    # Apply the useful numerator factors:
    # product (1 - x^((a_i + 1)i)).
    #
    # Since a_i is increasing, t_i is increasing too.
    for i, ai in enumerate(a, 1):
        t = (ai + 1) * i
        if t > N:
            break

        for k in range(N, t - 1, -1):
            value = f[k] - f[k - t]
            if value < 0:
                value += MOD
            f[k] = value

    # Choose one equipment type. Equal b values are intentionally counted
    # repeatedly because the equipment types themselves are different.
    ans = 0
    for bj in b:
        ans += f[N - bj]
        if ans >= MOD:
            ans -= MOD

    return ans

def solve():
    case_no = 1
    out = []

    while True:
        line = input()
        if not line:
            break

        if not line.strip():
            continue

        n, m = map(int, line.split())

        a = list(map(int, input().split()))
        b = list(map(int, input().split()))

        ans = solve_case(n, m, a, b)
        out.append(f"Case #{case_no}: {ans}")
        case_no += 1

    sys.stdout.write("\n".join(out))

if __name__ == "__main__":
    solve()
```グローバルな`part`配列は、(2n) のより大きな値が必要な場合にのみ拡張されます。 これが重要なのは、入力に多くのテスト ケースが含まれており、テストごとに分割シーケンスを個別に再計算すると、計算の最もコストがかかる部分を繰り返すことになるためです。 新しく追加された (p(k)) の繰り返しは以前の値にのみ依存するため、既存の配列の拡張は有効です。 

分割漸化式では、一般化五角形数 (r(3r-1)/2) および (r(3r+1)/2) が使用されます。 符号は、奇数 (r) の場合は正、偶数 (r) の場合は負です。 内側のループは、五角形の数が現在の次数を超えるとすぐに停止します。 

分母の修正には特に注意が必要です。 (k=n+1) の場合、プレフィックスには (p(0)) のみが含まれるため、1 つの部分 (n+1) を持つパーティションが正確に削除されます。 (k=2n) の場合、プレフィックスには (p(0),\ldots,p(n-1)) が含まれ、(n) より上の固有の部分が (n+1,\ldots,2n) であるすべての可能なパーティションをカバーします。 

分子の乗算では降順のインデックスが使用されます。 ループが上向きに実行された場合、同じ係数内で以前に変更された係数が再度使用され、(1-x^t) を複数回効果的に適用することができます。 降順にすることでその汚染を防ぎます。 

条件`if t > N: break`(t_i=(a_i+1)i) は (i) とともに増加するため、安全です。 分子の仕事が (O(n\sqrt n)) だけである理由もこれです。 

Python には整数オーバーフローの問題はありません。 値はモジュロ (10^9+7) で削減され、分割反復内の一時的な合計でも、(O(\sqrt N)) 個の項のみが含まれるため、管理可能な整数サイズ内に収まります。 

## 実用的な例

 ### サンプル 1

 最初のサンプルケースは、```
1 1
1
1
```ここでは (n=1) なので、ナップザックの容量は (N=2) となります。 ボリューム (1) には食品タイプが 1 つあり、最大で 1 個と、ボリューム (1) の装備アイテムが 1 つあります。 

| ステップ | 状態 | 値 |
 | --- | --- | --- |
 | 容量 | (N=2n) | 2 |
 | 分配係数 | (p(0),p(1),p(2)) | (1,1,2) |
 | 分母 | パーツは 1 つまで許可されます | (1,1,1) |
 | 分子の指数 | ((a_1+1)\cdot1) | 2 |
 | 分子の後 | (f(0),f(1),f(2)) | (1,1,0) |
 | 装備ターゲット | (N-b_1) | 1 |
 | 貢献を追加しました | (f(1)) | 1 |

 分子係数 (1-x^2) は、タイプ 1 の食品 2 個の無効な選択を削除します。 ボリューム 1 での係数は 1 のままで、食品を 1 つ摂取することに対応します。 

### サンプル 2

 2 番目のサンプルケースは、```
2 2
1 2
3 4
```容量は(N=4)です。 タイプ 1 は 1 個、タイプ 2 は 2 個使用できます。 

| ステップ | 状態 | 値 |
 | --- | --- | --- |
 | 容量 | (N=2n) | 4 |
 | 分配係数 | (p(0)\ldots p(4)) | (1,1,2,3,5) |
 | 分母 | パーツは 2 つまで許可されます | (1,1,2,2,3) |
 | タイプ 1 の指数 | ((1+1)\cdot1) | 2 |
 | タイプ 1 アップデート | (1-x^2) を掛ける | (1,1,1,1,1) |
 | タイプ 2 の指数 | ((2+1)\cdot2) | 6 |
 | タイプ 2 アップデート | 指数が 4 を超える | 変更なし |
 | 装備1ターゲット | (4-3) | 1 |
 | 装備2ターゲット | (4-4) | 0 |
 | 合計 | (f(1)+f(0)) | 2 |

 最初の分子因数は、タイプ 1 の 2 つの部分を含む無制限の表現を削除します。2 番目の因数は、指数が 6 であるため、4 度までの次数に影響を与えることはできません。 どちらの機器の選択にも、互換性のある食品の選択が 1 つだけあり、合計 2 つの方法が提供されます。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | 大規模なテストあたり (O(n\sqrt n+m)) | パーティション反復コスト (O(n\sqrt n))、有用な分子係数コスト (O(n\sqrt n))、機器処理コスト (O(m)) |
 | スペース | (O(n)) | 長さ (2n+1) の 2 つの係数配列と (a) および (b) の配列 |

 関連する多項式の次数の最大値は (2n\le10^5) です。 分子因数は (O(\sqrt n)) のみです。その指数は少なくとも (i^2) であるためです。 最大 5 つのテスト ケース (n\ge10^3)​​ という制限により、コストのかかる作業の総量が制御される一方で、小規模なケースでは必要な操作が大幅に少なくなります。 この実装では、テスト ケース全体でパーティション シーケンスも再利用します。 

## テストケース```python
import sys
import io

MOD = 1_000_000_007

part = [1]

def ensure_partitions(N):
    old = len(part)
    if old > N:
        return

    part.extend([0] * (N + 1 - old))

    pent = []
    r = 1
    while True:
        g1 = r * (3 * r - 1) // 2
        if g1 > N:
            break

        sign = 1 if r & 1 else -1
        pent.append((g1, sign))

        g2 = r * (3 * r + 1) // 2
        if g2 <= N:
            pent.append((g2, sign))

        r += 1

    for k in range(old, N + 1):
        s = 0
        for g, sign in pent:
            if g > k:
                break
            s += sign * part[k - g]
        part[k] = s % MOD

def solve_case(n, m, a, b):
    N = 2 * n
    ensure_partitions(N)

    f = part[:N + 1]

    prefix = 0
    for k in range(n + 1, N + 1):
        prefix += part[k - n - 1]
        if prefix >= MOD:
            prefix -= MOD

        f[k] -= prefix
        if f[k] < 0:
            f[k] += MOD

    for i, ai in enumerate(a, 1):
        t = (ai + 1) * i
        if t > N:
            break

        for k in range(N, t - 1, -1):
            f[k] -= f[k - t]
            if f[k] < 0:
                f[k] += MOD

    ans = 0
    for bj in b:
        ans += f[N - bj]
        if ans >= MOD:
            ans -= MOD

    return ans

def solution():
    input = sys.stdin.readline
    case_no = 1
    out = []

    while True:
        line = input()
        if not line:
            break

        if not line.strip():
            continue

        n, m = map(int, line.split())
        a = list(map(int, input().split()))
        b = list(map(int, input().split()))

        out.append(f"Case #{case_no}: {solve_case(n, m, a, b)}")
        case_no += 1

    return "\n".join(out)

def run(inp: str) -> str:
    old_stdin = sys.stdin
    old_stdout = sys.stdout

    sys.stdin = io.StringIO(inp)
    sys.stdout = io.StringIO()

    try:
        # Reuse the same global partition cache as the actual solution.
        exec_result = solution()
        sys.stdout.write(exec_result)
        return sys.stdout.getvalue()
    finally:
        sys.stdin = old_stdin
        sys.stdout = old_stdout

# Provided samples.
sample = """\
1 1
1
1
2 2
1 2
3 4
3 3
1 2 3
2 3 3
"""

assert run(sample) == (
    "Case #1: 1\n"
    "Case #2: 2\n"
    "Case #3: 6"
), "provided samples"

# Minimum-size input, with no food available.
assert run("""\
1 1
0
1
""") == "Case #1: 1", "minimum-size case"

# All equipment volumes are equal, so every equipment type must be counted.
assert run("""\
2 3
0 1
4 4 4
""") == "Case #1: 3", "duplicate equipment types"

# Boundary case for a food upper bound.
# With a = [0, 1], volume 2 can only be formed by one type-2 food.
assert run("""\
2 1
0 1
2
""") == "Case #1: 1", "food upper-bound boundary"

# Maximum n. Choosing equipment of volume 2n leaves zero volume for food,
# so the answer is exactly one regardless of the many food types.
n = 50000
a = " ".join(str(i) for i in range(n))
large_input = f"{n} 1\n{a}\n{2 * n}\n"
assert run(large_input) == "Case #1: 1", "maximum-size boundary"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`1 1 / 0 / 1`|`Case #1: 1`| 最小限のサイズと食料の入手可能性はゼロ |
 |`2 3 / 0 1 / 4 4 4`|`Case #1: 3`| 同じ量の機器を別々の選択肢として残しておく必要があります。 
|`2 1 / 0 1 / 2`|`Case #1: 1`| 食品の上限と分子の補正 |
 | (n=50000,\ a_i=i-1,\ b_1=100000) |`Case #1: 1`| 最大サイズと (b=2n) 境界 |

 ## 特殊なケース

 食品の上限がゼロの場合、その母関数係数全体はちょうど (1) になります。 のために```
1 1
0
1
```分子補正の指数は ((0+1)\cdot1=1) であるため、食料生成関数は次のようになります。 

[
 \frac{1-x}{1-x}=1。 
】

 唯一の食料量はゼロであり、装備量 1 が残りのユニットを消費します。 アルゴリズムが生成するのは、`Case #1: 1`。 

複数の機器タイプの容積が同じ場合、それらを結合してはなりません。 のために```
2 3
0 1
4 4 4
```すべての機器アイテムは食料の量をゼロのままにします。 係数 (f(0)=1) と、最後のループでその係数が 3 回追加されます。 結果は`Case #1: 3`。 

上限補正例の場合```
2 1
0 1
2
```分母に制限がないため、パート 1 とパート 2 を使用して 2 を分割でき、(1+1) と (2) の 2 つの可能性が得られます。 (a_1=0) であるため、タイプ 1 の分子係数は (1-x) ですが、タイプ 2 の分子係数は (1-x^4) です。 (1-x) を乗算すると、(1+1) 表現が削除され、(f(2)=1) が残ります。 必要な餌の量は2なので、答えは次のようになります。`Case #1: 1`。 

装備がナップザック全体に満たされると、必要な食料の量はゼロになります。 (n=50000)、(a_i=i-1)、および (b_1=100000=2n) を使用した最大サイズのテストでは、すべての分子と分母の演算で (f(0)=1) が保持されます。 最終的な検索は (f(0)) なので、答えは 1 つだけです。 これにより、係数配列に次数 0 が含まれていることと、式が`N - bj`すべての機器の体積は最大 (N) であるため、負のインデックスは必要ありません。 

多項式構築における最も微妙な境界は、次数 (2n) で発生します。 (i,j>n) の 2 つの因子 (x^i) と (x^j) の積は (2n) より大きい次数を持つため、これらの交差項は安全に破棄できます。 これがまさに、分母補正が別の多項式乗算を必要とするのではなく、1 回の前置和の減算に減少する理由です。 

分子には逆の種類の境界があります。 指数がちょうど (2n) の因数でも次数 (2n) で係数が変化するため、条件は次のようにする必要があります。`t > N`、 ない`t >= N`。 降順アップデートには以下が含まれます`k=N`いつ`t=N`これは、その寄与を正しく削除するために必要です。
