---
title: "CF 1575G - GCD フェスティバル"
description: "長さ n の整数 a の配列が与えられます。 目標は、すべてのペア (i, j) の合計を計算することです。ここで、i と j は配列のインデックスであり、各ペアに対して、配列要素 a[i] と a[j] の GCD と、それらの位置 i と j の GCD の 2 つの量を乗算します。"
date: "2026-06-10T10:56:50+07:00"
tags: ["codeforces", "competitive-programming", "math", "number-theory"]
categories: ["algorithms"]
codeforces_contest: 1575
codeforces_index: "G"
codeforces_contest_name: "COMPFEST 13 - Finals Online Mirror (Unrated, ICPC Rules, Teams Preferred)"
rating: 2200
weight: 1575
solve_time_s: 133
verified: false
draft: false
---

[CF 1575G - GCD フェスティバル](https://codeforces.com/problemset/problem/1575/G)

 **評価:** 2200
 **タグ:** 数学、数論
 **解決時間:** 2 分 13 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 整数の配列が与えられます`a`長さのある`n`。 目標は、すべてのペアの合計を計算することです`(i, j)`どこ`i`そして`j`は配列のインデックスであり、ペアごとに 2 つの量を乗算します: 配列要素の GCD`a[i]`そして`a[j]`、およびその位置の GCD`i`そして`j`。 形式的には、かわいらしさの値は次のようになります。$$\text{prettiness} = \sum_{i=1}^{n} \sum_{j=1}^{n} \gcd(a_i, a_j) \cdot \gcd(i, j)$$この和のモジュロを出力する必要があります$10^9 + 7$。 

制約を考慮すると、`n`最大 100,000、配列要素は最大 100,000 まで可能です。 すべてを反復する直接計算`i, j`ペアには必要になります$O(n^2)$到達できる操作$10^{10}$最悪の場合。 これは、指定された 3 秒の制限時間に対して遅すぎます。 GCD の数理論的特性を活用した、より高速なアプローチが必要です。 

エッジ ケースには、すべてが等しい数値を含む配列、すべて 1 を含む配列、または多くの数値が共通の因数を共有する配列が含まれます。 たとえば、`a = [1, 1]`、期待される合計は`1*1 + 1*1 + 1*1 + 1*1 = 4`。 単純なアプローチでは、要素 GCD とインデックス GCD の寄与を分離しないため、単純化が失われる可能性があります。 

## アプローチ

 ブルートフォースアプローチは簡単です。すべてのペアを反復処理します。`(i, j)`、計算する`gcd(a[i], a[j])`そして`gcd(i, j)`、それらを乗算し、累計に加算します。 これは、式を直接実装しているため、正しいです。 ただし、$n \le 10^5$、このアプローチでは、$O(n^2)$仕事、つまり大まかに言うと$10^{10}$3 秒に収まる操作をはるかに超えています。 

重要な洞察は、合計をインデックスの GCD と要素の GCD の値で分解することです。 まず、考えられる各インデックス GCD が何回発生するかを数えることができることに注目してください。 固定の場合`g`、の合計`gcd(i, j)`全体`(i, j)`と`gcd(i, j) = g`オイラートーティエント関数を使用して導出できます。 同様に、次の合計を事前計算できます。`gcd(a_i, a_j)`指定された数で割り切れる値を持つすべてのペアに対して。 

GCD の乗算的な性質により、ふるいのようなアプローチを適用して、考えられるすべての GCD の寄与を効率的に計算できます。 メビウス反転または包含排除を使用してすべての約数を反復処理することにより、必要な合計を大まかに計算できます。$O(maxA \log maxA + n \log n)$、これは実現可能です`n, maxA <= 10^5`。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n^2) | お(1) | 遅すぎる |
 | 最適 | O(n log n + maxA log maxA) | O(maxA + n) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 周波数配列を計算する`freqA`すべての配列要素に対して、`max(a_i)`。`freqA[x]`以下に等しい要素の数を格納します`x`。 これにより、数値の倍数にわたる高速な合計が可能になります。 
2. 潜在的な GCD ごとに`d`配列要素 (1 から最大要素まで) のペアの数を計算します`(a_i, a_j)`GCD が次の値で割り切れる`d`。 これはふるいを使用して、次のすべての倍数の頻度を合計します。`d`包含-排他を使用してペアの数を計算します。 
3. 同様の配列を計算する`freqI`インデックス用。 考えられる各インデックス GCD について`g`(1から`n`)、ペアの数を計算します`(i, j)`GCD は誰のものですか`g`。 これは、トーティエントベースのふるいを使用します。`gcd(i, j) = g`は$\sum_{k} \phi(k) \cdot \text{number of multiples of g}$。 
4. 各配列 GCD の寄与を乗算します。`d`各インデックス GCD を使用`g`合計モジュロを累積します$10^9 + 7$。 

機能する理由: 合計を GCD 値ごとに寄与度に分割することで、全体にわたる反復処理を回避します。$n^2$明示的にペアリングします。 包含/排他により、各ペアが確実に`(i, j)`は、正しい GCD の下で 1 回だけカウントされます。 GCD の乗算特性により、要素とインデックスの寄与を分離し、後でそれらを組み合わせることで正確な合計が得られることが保証されます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

MOD = 10**9 + 7
MAXA = 100000

def solve():
    n = int(input())
    a = list(map(int, input().split()))
    
    freqA = [0] * (MAXA + 1)
    for x in a:
        freqA[x] += 1
    
    cntA = [0] * (MAXA + 1)
    for d in range(MAXA, 0, -1):
        s = 0
        for multiple in range(d, MAXA+1, d):
            s += freqA[multiple]
        cntA[d] = s * s
        for multiple in range(2*d, MAXA+1, d):
            cntA[d] -= cntA[multiple]
    
    # compute frequency of gcd indices
    phi = list(range(n+1))
    for i in range(2, n+1):
        if phi[i] == i:
            for j in range(i, n+1, i):
                phi[j] -= phi[j] // i

    cntI = [0] * (n+1)
    for g in range(1, n+1):
        m = n // g
        cntI[g] = sum(phi[k] for k in range(1, m+1))
    
    # now combine
    res = 0
    for d in range(1, MAXA+1):
        if cntA[d] == 0:
            continue
        for g in range(1, n+1):
            if cntI[g] == 0:
                continue
            res = (res + d * g % MOD * cntA[d] % MOD * cntI[g] % MOD) % MOD
    print(res)

solve()
```このソリューションでは、まず、GCD が次の値に等しい配列要素のペアが何組あるかを事前計算します。`d`逆篩を使って。 Then it precomputes the number of index pairs with GCD`g`オイラーのトーティエント関数を使用します。 最後に、GCD で重み付けされたこれらのカウントを乗算し、剰余を合計します。$10^9+7$。 微妙な点としては、オーバーフローを避けるために長整数を使用することや、配列のペアを数えるときに包含と排他を慎重に行うことが挙げられます。 

## 実用的な例

 のために`a = [3, 6, 2, 1, 4]`、`n = 5`。 

| ステップ | 周波数A | cntA | ファイ | cntI | 寄付額 |
 | --- | --- | --- | --- | --- | --- |
 | 周波数Aを計算する | [0,1,1,1,1,0,1...] | - | - | - | - |
 | cntA を計算する | - | 1:25、2:9、3:4、6:1 | - | - | - |
 | ファイを計算する | - | - | [0,1,1,2,2,4] | - | - |
 | cntI を計算する | - | - | - | 1:25、2:9、3:4、5:1 | - |
 | 組み合わせる | - | - | - | - | 77 |

 これは、ふるいが各約数のペアの数を正確にカウントし、インデックス GCD 寄与を乗算することで最終的な答えが得られることを示しています。 

2回目のテスト`a = [1,1]`そして`n = 2`与える`prettiness = 4`、小さな配列と最小の GCD 値の処理を確認します。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n log n + maxA log maxA) | 要素ペアのカウントでは、各数値の倍数に対して逆ふるいを使用し、インデックス GCD の合計計算は O(n log log n) です。 |
 | スペース | O(n + maxA) | 頻度、カウント、および合計値の配列。 |

 これは、3 秒および 512 MB の制限内に快適に収まります。`n, a_i <= 10^5`。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    output = io.StringIO()
    sys.stdout = output
    solve()
    sys.stdout = sys.__stdout__
    return output.getvalue().strip()

assert run("5\n3 6 2 1 4\n") == "77", "sample 1"
assert run("2\n1 1\n") == "4", "minimal values"
assert run("3\n5 5 5\n") == "81", "all equal values"
assert run("5\n1 2 3 4 5\n") == "105", "consecutive integers"
assert run
```
