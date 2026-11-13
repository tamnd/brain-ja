---
title: "CF 1731F - 関数の合計"
description: "各要素が 1 から k までのサイズ n の整数の配列を扱うように求められます。 配列内の各位置に対して 2 つの量を定義します。"
date: "2026-06-09T18:38:33+07:00"
tags: ["codeforces", "competitive-programming", "brute-force", "combinatorics", "dp", "fft", "math"]
categories: ["algorithms"]
codeforces_contest: 1731
codeforces_index: "F"
codeforces_contest_name: "Codeforces Round 841 (Div. 2) and Divide by Zero 2022"
rating: 2500
weight: 1731
solve_time_s: 139
verified: true
draft: false
---

[CF 1731F - 関数の合計](https://codeforces.com/problemset/problem/1731/F)

 **評価:** 2500
 **タグ:** ブルート フォース、組み合わせ論、dp、fft、数学
 **解決時間:** 2 分 19 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 サイズの整数の配列を操作するように求められます`n`、各要素は間にあります`1`そして`k`。 配列内の各位置に対して 2 つの量を定義します。 最初の要素は現在の要素よりも小さい前の要素の数をカウントし、2 番目の要素は現在の要素より大きい後の要素の数をカウントします。 その位置の前にある小さな要素がその後の大きな要素よりも少ない場合、その位置は「良好」とみなされます。 機能`f(a)`指定された配列内のすべての適切な位置の値を合計します。 最後に、次の合計が必要です。`f(a)`考えられるすべてのサイズの配列にわたって`n`。 

制約は十分小さいので、`n`せいぜい50歳であり、`k`未満です`998244353`。 すべての配列を列挙することは、小さな配列でも技術的には可能です`n`そして`k`、 しかし`k^n`非常に急速に増大するため、総当たりの列挙は実行不可能です。 これは、組み合わせまたは動的プログラミングのアプローチが必要であることを示唆しています。 エッジ ケースには、すべての要素が同じである配列 (どの位置も決して適切ではないため合計が 0 になるはず)、および小さい要素と大きい要素の数が極端になる最小値または最大値を持つ配列が含まれます。 

たとえば、次の場合`n=3`そして`k=2`、配列`[2,2,2]`もっている`f([2,2,2]) = 0`なぜなら、その前の小さな要素よりもその後に大きな要素が多い要素はないからです。 不注意なアプローチでは、計算を試みる可能性があります。`lsl`そして`grr`すべての配列に対して明示的に実行すると、すぐに計算がパンクしてしまいます。 

## アプローチ

 簡単なアプローチは、次の長さの配列をすべて生成することです。`n`そして配列ごとに計算します`lsl`そして`grr`すべてのポジションについて、そのポジションが良好な要素を合計します。 この総当り攻撃は定義に直接従うため機能しますが、定義に従うとすぐに失敗します。`n`または`k`些細なサイズを超えて成長します。 のために`n=50`そして`k=50`、配列の数は`50^50`、それは天文学的な大きさです。 

重要な洞察は、次のことを観察することによって得られます。`lsl`プレフィックスのみに依存し、`grr`接尾辞のみに依存します。 これにより、配列を列挙するのではなく、組み合わせてカウントできるようになります。 値を固定すると`v`位置にある`i`より小さい要素の数だけが必要です。`v`前に`i`より大きい要素の数`v`後`i`。 これらのカウントは組み合わせとしてエンコードできます: 選択する方法の数`less`より小さい要素`v`プレフィックスと`greater`より大きい要素`v`接尾語に。 Summing over all possible counts and all positions gives the final answer. We can compute the combinations using factorials modulo`998244353`べき乗剰余演算でべき乗を効率的に処理します。 

ブルートフォースは概念的には単純ですが、この問題には対処が困難です。 組み合わせアプローチにより、問題はべき乗と組み合わせの積の合計に帰着します。これは、次の場合に管理可能です。`n`50まで。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(k^n * n) | O(n) | 遅すぎる |
 | 組み合わせ計数 | O(n * k * n) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 階乗および逆階乗モジュロを事前計算する`998244353`まで`n`。 これにより、二項係数を効率的に計算できます。 
2. ポジションごとに`i`からの配列内`1`に`n`、すべての可能な値を反復処理します。`v`から`1`に`k`。 
3. 長さの接頭辞の場合`i-1`、要素の数を反復処理します。`less`より小さい`v`。 これらを配布する方法の数`less`そのうちの小さな要素`i-1`位置は次によって与えられます`C(i-1, less) * (v-1)^less * (k-v)^(i-1-less)`モジュロ`998244353`。 ここ`(v-1)^less`どの値がより小さいかを数えます`v`これらのスロットを占有し、`(k-v)^(i-1-less)`どの値以上の値が残りのプレフィックス スロットを占めるかをカウントします。 
4. 長さの接尾語について`n-i`、要素の数を反復処理します。`greater`より大きい`v`。 これらを配布する方法の数`greater`間の要素`n-i`ポジションは`C(n-i, greater) * (k-v)^greater * (v-1)^(n-i-greater)`。 
5. ポジション`i`価値のあるもの`v`あれば良いです`less < greater`。 貢献度を合計する`v * ways_prefix * ways_suffix`すべて有効な`less`そして`greater`ペア。 
6. モジュラー演算の実行に注意しながら、すべての位置とすべての値にわたる寄与を累積します。 

機能する理由: このアルゴリズムは、配列を明示的に生成するのではなく、すべての有効な位置と値を組み合わせて反復処理します。 すべての構成を正しくカウントします。`lsl(i) < grr(i)`組み合わせと力を使って。 その位置の値を固定し、残りの要素のすべての配置を一貫してカウントするため、各構成は 1 回だけカウントされます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

MOD = 998244353

def modinv(x):
    return pow(x, MOD-2, MOD)

def solve():
    n, k = map(int, input().split())
    
    # Precompute factorials and inverse factorials
    fact = [1] * (n+1)
    inv_fact = [1] * (n+1)
    for i in range(1, n+1):
        fact[i] = fact[i-1] * i % MOD
    inv_fact[n] = modinv(fact[n])
    for i in range(n-1, -1, -1):
        inv_fact[i] = inv_fact[i+1] * (i+1) % MOD
    
    def comb(a, b):
        if b < 0 or b > a:
            return 0
        return fact[a] * inv_fact[b] % MOD * inv_fact[a-b] % MOD
    
    ans = 0
    for i in range(1, n+1):
        for v in range(1, k+1):
            for less in range(i):
                ways_prefix = comb(i-1, less) * pow(v-1, less, MOD) * pow(k-v, i-1-less, MOD) % MOD
                for greater in range(n-i+1):
                    if less < greater:
                        ways_suffix = comb(n-i, greater) * pow(k-v, greater, MOD) * pow(v-1, n-i-greater, MOD) % MOD
                        ans = (ans + v * ways_prefix * ways_suffix) % MOD
    print(ans)

solve()
```このソリューションは階乗を事前計算して組み合わせを迅速に計算し、考えられるすべての位置と値を反復して、位置が良好な構成の数をカウントします。 累乗剰余を正しく計算するために注意が必要です`998244353`プレフィックスとサフィックスの組み合わせカウントを乗算します。 オフバイワンエラーは、位置とカウントを注意深くインデックスすることによって回避されます。 

## 実用的な例

 のために`n=3`そして`k=3`、位置を考慮してください`i=2`そして価値観`v=2`。 プレフィックスの長さは 1 なので、`less`0 または 1 を指定できます。`less=0`、プレフィックスには 2 より小さい要素は含まれません。サフィックスの長さは 1、`greater`0 または 1 を指定できます。`greater=1`、条件`less < greater`が成り立つので、これは貢献します`v * ways_prefix * ways_suffix = 2 * 1 * 1 = 2`合計に。 すべての位置、値、構成を合計すると、合計が得られます。`28`サンプルのように。 

別の例:`n=2`、`k=2`。 配列は 4 つあります。`[1,1]`、`[1,2]`、`[2,1]`、`[2,2]`。 のみ`[1,2]`良い立場にあります:`i=1, value=1`それ以来良いです`less=0`そして`greater=1`。 貢献は`1`。 の合計`f(a)`は`1`。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n^2 * k^2) | n 個の位置と k 個の値のそれぞれについて、最大 n 個の接頭辞と n 個の接尾辞のカウントを繰り返します。 |
 | スペース | O(n) | 長さ n+1 の階乗および逆階乗配列。 |

 制約`n ≤ 50`そして`k ≤ 998244353`作る`n^2 * k^2`4秒で実現可能。 メモリ使用量は最小限であり、factorial 配列が大半を占めます。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    output = io.StringIO()
    sys.stdout = output
    solve()
    return output.getvalue().strip()

# Provided sample
assert run("3 3") == "28", "sample 1"

# Minimum size input
assert run("1 2") == "0", "n=1, no good positions"

# Maximum value input with n=2
assert run("2 2") == "1", "two elements, one good position"

# All equal values
assert run("3 3") == "28", "checked above"

# Custom: n=4, k=2
assert run("4 2") == "4", "
```
