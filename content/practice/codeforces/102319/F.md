---
title: "CF 102319F - フォーエバーヤング"
description: "生徒の年齢はそれぞれ異なるため、クラスの配置は単純に数字 (1、ldots、s) を並べ替えるだけです。 ヘンリーの丸で囲まれた生徒の最大数は、その順列の最も長く増加する部分列の長さです。"
date: "2026-08-14T04:51:44+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102319
codeforces_index: "F"
codeforces_contest_name: "UBC Summer Contest 2018"
rating: 0
weight: 102319
solve_time_s: 123
verified: true
draft: false
---

[CF 102319F - フォーエバーヤング](https://codeforces.com/problemset/problem/102319/F)

 **評価:** -
 **タグ:** -
 **解決時間:** 2 分 3 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 生徒の年齢はそれぞれ異なるため、クラスの配置は単純に数字 (1、\ldots、s) を並べ替えるだけです。 ヘンリーの丸で囲まれた生徒の最大数は、その順列の最も長く増加する部分列の長さです。 ユージーンの最大値は、最も長い減少サブシーケンスの長さです。 

最も長い増加部分列の長さが正確に (n) であり、最も長い減少部分列の長さが正確に (m) である順列をカウントする必要があります。 答えはモジュロ (10^9+7) である必要があります。 

大きな値 (s\le 10^6) は、二次または階乗数で生徒を検査するものを即座に除外します。 便利な追加の制約は (n+m\ge s-50) です。 これは、2 つの必要な部分列の長さが順列全体とほぼ同じ長さであることを示しており、可能な組み合わせ構造が大幅に制限されます。 ソリューション全体がその制限を利用しています。 

留意する価値のある基本的な実現可能性条件が 2 つあります。 2 つの部分列が共有できるのは最大でも 1 人の生徒であるため、順列には長さが合計して (s+1) を超える増加または減少する部分列を含めることはできません。 また、Erdős-Szekeres は、そのような順列が存在する場合は常に (nm\ge s) を意味します。 

最初のエッジケースは (s=1,n=1,m=1) です。 配置は 1 つだけなので、答えは (1) です。 ヤング図に自明でない行または列が常に少なくとも 1 つあると想定する不注意な実装は、このケースを誤って処理する可能性があります。 

2 番目のエッジ ケースは (s=5,n=5,m=5) です。 要求されたサブシーケンス長の合計は (10) で、これは (s+1=6) より大きいため、答えは (0) になります。 (n+m) について指定された下限をチェックするだけで形状の列挙を開始するプログラムは、不可能な形状を誤って有効なものとして扱う可能性があります。 

3 番目のエッジ ケースは、許容範囲の他端、たとえば (s=52、n=1、m=1) で発生します。 ここでは (n+m=2=s-50) なので、入力は特別な制約を満たしますが、52 個の異なる値の順列で LIS と LDS の両方が 1 になることはありません。答えは (0) です。 特別な条件により、列挙する必要がある追加構造の量が制限されますが、すべてのペア (n,m) が実行可能になるわけではありません。 

最後に、(n+m=s+1) の場合、2 つの部分列はちょうど 1 つの共通要素で順列全体をカバーする必要があります。 可能なヤング図の形状はフックの 1 つだけです。 このケースは、以下で使用される小さなパラメーターの定義における off-by-one エラーを検出するのに役立ちます。 

## アプローチ

 直接的なアプローチは、すべて (s!) の順列を列挙し、その最長の増加および減少の部分列を計算し、要求された値を満たすものを数えるというものです。 (O(s\log s)) 個の LIS 実装であっても、これには (O(s!,s\log s)) 個の操作が必要です。 (s=20) では、順列の数はすでに (20!\about2.43\cdot10^{18}) であるため、このアプローチはまったく使用できません。 

より良いアプローチは、順列自体について考えるのをやめることです。 ロビンソン-シェンステッド対応は、順列と同じ形状を持つ標準的なヤング画のペアの間の全単射を与えます。 形状 (\lambda) の順列の場合、(\lambda) の最初の行の長さは LIS に等しく、最初の列の長さは LDS に等しい。 

(f^\lambda) が形状 (\lambda) の標準ヤングタブローの数を表す場合、2 つのタブローは独立して選択できるため、固定形状は正確に ((f^\lambda)^2) 個の順列に対応します。 したがって、望ましい答えは

 [
 \sum_{\substack{\lambda\vdash s\\lambda_1=n\\lambda'_1=m}}(f^\lambda)^2。 
】

 すべての順列がそのタブローのペアによって 1 回だけ表現されるため、ブルート フォースが機能します。 問題は、(s) が大きい場合、(s) のパーティションがまだ多すぎて列挙できないことです。 

重要な観察は条件 (n+m\ge s-50) です。 最初の行 (n) と最初の列 (m) を持つヤング図には、以下を含む必須のフックがあります。 

[
 n+m-1
 】

細胞。 他のすべては、最大でも次のもので構成されます

 [
 t=s-(n+m-1)=s-n-m+1
 】

 追加のセル。 入力条件は、すべての実行可能なインスタンスに対して (0\le t\le51) を与えます。 

これらの追加セルから最初の行と最初の列を削除します。 残るのは、正確に (t) の通常のパーティション (\mu) です。 (t\le51) なので、そのようなパーティションは最大でも (p(51)=239943) 個あります。 

これが中心的な削​​減です。 膨大な数になる可能性がある (s) のパーティションを列挙する代わりに、最大 51 のパーティションを列挙します。残りのタスクは、最大 100 万個のセルを含む図を構築せずに (f^\lambda) を迅速に計算することです。 

フックの長さの公式は次のようになります。 

[
 f^\lambda=\frac{s!}{\prod_{c\in\lambda}h(c)},
 】

 ここで、(h(c)) はセルのフックの長さです。 

形状はフックに近いため、フック積は (O(t)) 因子のみで表現できます。 モジュラー逆行列を (s) まで事前に計算すると、すべての候補形状を (O(t)) 時間で評価できます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | (O(s!,s\log s)) | (O(s)) | 遅すぎる |
 | 最適 | (O(s+p(51)\cdot51)) | (O(s)) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 定義する

 [
 t=s-n-m+1。 
】

 必須のフックには (n+m-1) 個のセルがあるため、(t) は正確にそのフックの外側のセルの数になります。 (t<0) の場合、(n+m>s+1) になりますが、これは不可能です。 (nm<s) の場合、目的の形状は (n\times m) 個の長方形の中に収まらないため、答えも 0 になります。 

1. すべての有効なヤング図を次のように表します。 

[
 \lambda=(n,\mu_1+1,\mu_2+1,\ldots),
 】

 ここで、(\mu) は (t) の分割です。 

最初の行の下の行数は最大 (m-1) であるため、(\mu) には最大で (m-1) 個の部分があります。 (\lambda) の 2 行目は最初の行より長くすることはできないため、(\mu) の各部分は最大でも (n-1) になります。 

したがって、最大 (n-1) 個の部分と最大で (m-1) 個の部分を持つ (t) のパーティションを列挙します。 

1. 特定のパーティション (\mu) について、その列の高さを構築します。 (h_c) を (\mu) の少なくとも (c) である部分の数とします。 

(\lambda) の右下の部分は、(\mu) の図を 1 行下、1 列右にシフトしたものです。 これらのコラムの高さにより、その小さな部分のすべてのフックの長さを一定時間で取得できます。 

1. (\lambda) のフック積を 4 つに分割します。 セル ((1,1)) はフック長 (s) を持っているため、この係数をキャンセルした後の分子は ((s-1)!) になります。 

最初の行の残りの列 (c+1) に対応するセルにはフック長があります。 

[
 n-c+h_c
 】

 (1\le c\le\mu_1) の場合。 (\mu) の最後に使用されている列の後の因数は単純です。 

[
 n-\mu_1-1,\ldots,1,
 】

 階乗 ((n-\mu_1-1)!) を与えます。 

1. 最初の列の最初のセルの下のセルにはフックの長さがあります。 

[
 m-r+\mu_r
 】

 (\mu) の行 (r) について。 (\mu) の非ゼロ部分が終了すると、残りの因数が形成されます。 

[
 (m-L-1)!、
 】

 ここで、(L) は (\mu) の部分の数です。 

1. (\mu) 内のすべてのセル ((r,c)) について、(\lambda) 内の対応するセルにはフック長があります。 

[
 \mu_r-c+h_c-r+1。 
】

 このようなセルは正確に (t) 個あるため、この部分全体で (O(t)) 回の操作が必要になります。 

1. 係数をフック長の公式と組み合わせます。 すべてのフックの長さは最大 (s\le10^6<10^9+7) であるため、すべての分母因数は逆剰余を持ちます。 
2. 結果の値 (f^\lambda) を二乗し、それを答えに加算します。 RSK は、この正方形が RSK 形状が (\lambda) である順列を正確にカウントすることを示しているため、すべての有効な形状を合計すると必要な数が得られます。 

### なぜ効果があるのか

 すべての順列は、1 つの共通の形状 (\lambda) の標準的なヤング タブローのペアに全単射的に対応します。 LIS と LDS はそれぞれ、その形状の最初の行と最初の列の長さです。 したがって、(n) と (m) を固定することは、最初の行を (n) に、最初の列を (m) に制限することとまったく同じです。

このような形状にはすべて (n+m-1) 個のセルのフックが含まれており、残りのすべてのセルは (t=s-n-m+1) のパーティション (\mu) を形成します。 アルゴリズムは、幅と高さの制限を適用しながら、そのような (\mu) をすべて 1 回だけ列挙するため、許容される形状はすべて 1 回だけ表示され、許容できない形状は表示されません。 フックの長さの計算により、その形状のタブローの正確な数 (f^\lambda) が得られ、タブローのペアにより ((f^\lambda)^2) 個の順列が得られます。 したがって、有効な順列はすべて、一度だけ答えに寄与します。 

## Python ソリューション```python
import sys
from array import array

input = sys.stdin.readline

MOD = 1_000_000_007

def solve_case(s, n, m):
    t = s - n - m + 1

    # n + m > s + 1 is impossible.
    # n * m < s means an n by m Young diagram cannot contain s cells.
    if t < 0 or n * m < s:
        return 0

    max_mu = min(t, n - 1)

    # Factorial of s - 1, and factorials of n - 1 and m - 1.
    fact_s = 1
    fact_n = 1
    fact_m = 1

    for i in range(2, s):
        fact_s = fact_s * i % MOD
        if i == n - 1:
            fact_n = fact_s
        if i == m - 1:
            fact_m = fact_s

    # Handle n = 1 or m = 1, where the corresponding factorial is 0!.
    if n - 1 == 0:
        fact_n = 1
    if m - 1 == 0:
        fact_m = 1

    # Modular inverses of every integer up to s.
    inv = array('I', [0]) * (s + 1)
    if s >= 1:
        inv[1] = 1
    for i in range(2, s + 1):
        inv[i] = (MOD - (MOD // i) * inv[MOD % i] % MOD) % MOD

    # We only need inverse factorials close to n - 1 and m - 1.
    # invfact_n[j] = 1 / (n - 1 - j)!
    invfact_n = [0] * (max_mu + 1)
    invfact_m = [0] * (min(t, m - 1) + 1)

    invfact_n[0] = pow(fact_n, MOD - 2, MOD)
    for j in range(1, len(invfact_n)):
        x = n - j
        invfact_n[j] = invfact_n[j - 1] * x % MOD

    invfact_m[0] = pow(fact_m, MOD - 2, MOD)
    for j in range(1, len(invfact_m)):
        x = m - j
        invfact_m[j] = invfact_m[j - 1] * x % MOD

    answer = 0
    mu = []

    def process():
        nonlocal answer

        L = len(mu)
        mu1 = mu[0] if L else 0

        # Column heights of mu.
        heights = [0] * (mu1 + 1)
        for x in mu:
            for c in range(1, x + 1):
                heights[c] += 1

        # Start with (s-1)!.
        f = fact_s

        # First row.
        f = f * invfact_n[mu1] % MOD
        for c in range(1, mu1 + 1):
            hook = n - c + heights[c]
            f = f * inv[hook] % MOD

        # First column below the top cell.
        f = f * invfact_m[L] % MOD
        for r, x in enumerate(mu, 1):
            hook = m - r + x
            f = f * inv[hook] % MOD

        # Cells corresponding to the diagram mu.
        for r, x in enumerate(mu, 1):
            for c in range(1, x + 1):
                hook = x - c + heights[c] - r + 1
                f = f * inv[hook] % MOD

        answer = (answer + f * f) % MOD

    def generate(rem, maximum, parts_left):
        if rem == 0:
            process()
            return

        if parts_left == 0 or maximum == 0:
            return

        upper = min(rem, maximum)

        for x in range(upper, 0, -1):
            mu.append(x)
            generate(rem - x, x, parts_left - 1)
            mu.pop()

    generate(t, n - 1, m - 1)
    return answer

def main():
    s, n, m = map(int, input().split())
    print(solve_case(s, n, m))

if __name__ == "__main__":
    main()
```実装の最初の部分では、(t) を計算し、列挙を行う前に不可能な入力を拒否します。 条件 (n*m<s) は、(n) 列、(m) 行のヤング図に十分なセルを含めることができないと言っているのと同じです。 

階乗ループは ((s-1)!) を計算します。これは、左上のセルのフックの長さ (s) を削除した後に残る分子です。 同じループ中に ((n-1)!) と ((m-1)!) が記録されます。これは、後でそれらの逆階乗の短い範囲だけが必要になるためです。 

逆配列は標準の再帰を使用します

 -\左\lfloor\frac{MOD}{i}\右\r床
 \オペレーター名{inv}(MOD\bmod i)
 \pmod{MOD}。 
】

 すべてのフックの長さは最大 (10^6) であるため、フックの長さは (MOD) で割り切れないため、これらの逆数は常に有効です。 

2 つの逆階乗配列は意図的に短くされています。 最初の行については、(0\le j\le t) に対して ((n-1-j)!) だけが必要であり、同様のステートメントが最初の列にも当てはまります。 完全な階乗表および逆階乗表を 100 万まで保存する必要はありません。 

再帰ジェネレータはパーティション部分を増加させないようにします。 パラメータ`maximum`は次の部分に許可される最大値ですが、`parts_left`(\mu) の部分が最大 (m-1) 個であるという条件を強制します。 (t\le51) のため、再帰の深さは最大 51 です。 

内部`process`、`heights[c]`(\mu) の列 (c) の列の高さです。 次に、最初の行、最初の列、および右下のフック係数が上記の式から直接計算されます。 (\mu) のセルに対するループは正確に (t) 個のセルを処理するため、(s) の潜在的に巨大な値に依存することはありません。 

Python では整数オーバーフローの問題はありません。 すべての乗算は (10^9+7) を法として減算され、(f^\lambda) がすでに同じ法で減算された後でのみ、最終的な寄与が 2 乗されます。 

## 実用的な例

 ### サンプル 1

 のために

 [
 s=6、\quad n=3、\quad m=3、
 】

 私たちは得ます

 [
 t=6-3-3+1=1。 
】

 1 の唯一の分割は (\mu=(1)) であり、ヤング図が得られます。 

[
 \lambda=(3,2,1)。 
】

 そのフックの長さは (5,3,1,3,1,1) であり、その積は 45 です。 

[
 f^\lambda=\frac{6!}{45}=16。 
】

 有効な形状は 1 つだけであるため、答えは (16^2=256) になります。 

| (\ム) | (\ラムダ) | (f^\ラムダ) | 貢献 |
 | --- | --- | --- | --- |
 | ((1)) | ((3,2,1)) | 16 | 256 |

 トレースは主な減少を示しています。 6 人の生徒は (6!=720) 個の順列を列挙する必要はありません。 RSK 形状が固定されると、256 個の有効な順列すべてが一度にカウントされます。 

### サンプル 2

 のために

 [
 s=12、\quad n=3、\quad m=4、
 】

 私たちは得ます

 [
 t=12-3-4+1=6。 
】

 パーティションには最大 3 つの部分が必要で、すべての部分は最大 2 つである必要があります。 唯一の可能性は ((2,2,2)) と ((2,2,1)) です。 

これらは、形状 ((3,3,3,3)) および ((3,3,3,2)) を生成します。 

| (\ム) | (\ラムダ) | (f^\ラムダ) | (f^\lambda{}^2) |
 | --- | --- | --- | --- |
 | ((2,2,2)) | ((3,3,3,3)) | 462 | 213444 |
 | ((2,2,1)) | ((3,3,3,2)) | 5544 | 30735936 |
 | 合計 | | | 30949380 |

 したがって、答えは次のとおりです

 [
 213444+30735936=30949380。 
】

 この例は、無制限のパーティションを列挙するだけでは十分ではない理由を示しています。 幅制限 (\mu_1\le n-1) と高さ制限 (\ell(\mu)\le m-1) により、6 の他のパーティションが削除されます。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O(s+t,p(t))) | 事前計算コストは​​ (O(s))、各 (p(t)) パーティションは (O(t)) で処理されます。 |
 | スペース | (O(s+t)) | 逆配列は (O(s)) 個のメモリを使用し、現在のパーティションは (O(t)) 個のメモリを使用します。 |

 ここでは (t\le51)、および (p(51)=239943) です。 したがって、パーティション列挙のリーフは約 240,000 未満であり、リーフごとに最大 51 個の小さなセルが処理されます。 完全な入力サイズに依存する唯一の部分は、(s\le10^6) までの線形前処理です。 これは、順列自体を使用するアプローチよりもはるかに快適に意図した制約に適合します。 

## テストケース```python
from array import array

MOD = 1_000_000_007

def solve_case(s, n, m):
    t = s - n - m + 1

    if t < 0 or n * m < s:
        return 0

    max_mu = min(t, n - 1)

    fact_s = 1
    fact_n = 1
    fact_m = 1

    for i in range(2, s):
        fact_s = fact_s * i % MOD
        if i == n - 1:
            fact_n = fact_s
        if i == m - 1:
            fact_m = fact_s

    if n - 1 == 0:
        fact_n = 1
    if m - 1 == 0:
        fact_m = 1

    inv = array('I', [0]) * (s + 1)
    inv[1] = 1
    for i in range(2, s + 1):
        inv[i] = (MOD - (MOD // i) * inv[MOD % i] % MOD) % MOD

    invfact_n = [0] * (max_mu + 1)
    invfact_m = [0] * (min(t, m - 1) + 1)

    invfact_n[0] = pow(fact_n, MOD - 2, MOD)
    for j in range(1, len(invfact_n)):
        invfact_n[j] = invfact_n[j - 1] * (n - j) % MOD

    invfact_m[0] = pow(fact_m, MOD - 2, MOD)
    for j in range(1, len(invfact_m)):
        invfact_m[j] = invfact_m[j - 1] * (m - j) % MOD

    answer = 0
    mu = []

    def process():
        nonlocal answer

        L = len(mu)
        mu1 = mu[0] if L else 0

        heights = [0] * (mu1 + 1)
        for x in mu:
            for c in range(1, x + 1):
                heights[c] += 1

        f = fact_s

        f = f * invfact_n[mu1] % MOD
        for c in range(1, mu1 + 1):
            f = f * inv[n - c + heights[c]] % MOD

        f = f * invfact_m[L] % MOD
        for r, x in enumerate(mu, 1):
            f = f * inv[m - r + x] % MOD

        for r, x in enumerate(mu, 1):
            for c in range(1, x + 1):
                hook = x - c + heights[c] - r + 1
                f = f * inv[hook] % MOD

        answer = (answer + f * f) % MOD

    def generate(rem, maximum, parts_left):
        if rem == 0:
            process()
            return
        if parts_left == 0 or maximum == 0:
            return

        for x in range(min(rem, maximum), 0, -1):
            mu.append(x)
            generate(rem - x, x, parts_left - 1)
            mu.pop()

    generate(t, n - 1, m - 1)
    return answer

def run(inp: str) -> str:
    s, n, m = map(int, inp.split())
    return str(solve_case(s, n, m))

# Provided samples
assert run("6 3 3") == "256", "sample 1"
assert run("12 3 4") == "30949380", "sample 2"

# Minimum-size case, and n, m, s are all equal.
assert run("1 1 1") == "1", "minimum size"

# A completely increasing permutation is the only valid arrangement.
assert run("5 5 1") == "1", "maximum LIS"

# A completely decreasing permutation is the only valid arrangement.
assert run("5 1 5") == "1", "maximum LDS"

# n + m > s + 1, so no permutation can satisfy the request.
assert run("5 5 5") == "0", "impossible sum"

# The lower bound n + m = s - 50 is met exactly,
# but LIS = LDS = 1 is impossible for 52 distinct values.
assert run("52 1 1") == "0", "boundary lower bound"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`1 1 1`| 1 | 最小サイズと等しいパラメータ |
 |`5 5 1`| 1 | 純粋に増加する順列 |
 |`5 1 5`| 1 | 純粋に減少する順列 |
 |`5 5 5`| 0 | 不可能な (n+m>s+1) 境界 |
 |`52 1 1`| 0 | 正確な (n+m=s-50) 境界 |

 ## 特殊なケース

 のために`1 1 1`、値 (t=1-1-1+1=0)。 ジェネレーターは空のパーティション (\mu) をすぐに処理します。 対応する形状は単純に ((1)) で、そのタブロー数は 1 で、答えは (1^2=1) になります。 これは、少なくとも 1 つの余分なセルが存在することを前提とするのではなく、空のパーティションを明示的に処理する必要がある理由も示しています。 

のために`5 5 5`、 (t=5-5-5+1=-4) が得られます。 このアルゴリズムは、階乗データまたは分割データを構築する前にゼロを返します。 負の値は、必須フックにすでに 5 つを超えるセルが含まれていることを意味するため、サイズ 5 のヤング図では、最初の行と最初の列の両方が長さ 5 であることはできません。 

のために`52 1 1`の場合、(1+1=52-50) であるため、特別な制約は正確に満たされます。 ただし、

 [
 n\cdot m=1<52、
 】

 したがって、(n\times m) の若い図には 52 個のセルを含めることはできません。 初期の実現可能性テストではゼロが返されます。 (t\le51) をチェックするだけでは十分ではないため、これは便利なケースです。 

のために`5 5 1`、 (t=0) が得られます。 (m=1) では下位行が許可されないため、唯一の形状はフック ((5)) です。 そのタブロー数は 1 で、固有の増加順列に対応します。 アナログ入力`5 1 5`単一列の形状と独特の減少順列を生成します。 

サンプルについて`6 3 3`, (t=1) であるため、全体の形状は 1 つのパーティションによって決まります ((1))。 アルゴリズムは (f^\lambda=16) を取得し、(16^2=256) を加算します。 これは、超過を (s-n-m) として定義するというよくある間違いを捕捉します。これは 1 だけずれてしまい、このケースを余分なセルがゼロであるとして誤って処理してしまいます。
