---
title: "CF 2091E - 興味深い比率"
description: "$1 le a < b le n$ である整数 $(a, b)$ のペアを数えて、比率 $F(a, b) = frac{text{lcm}(a, b)}{gcd(a, b)}$ が素数になるようにするように求められます。"
date: "2026-06-08T05:47:01+07:00"
tags: ["codeforces", "competitive-programming", "brute-force", "math", "number-theory", "two-pointers"]
categories: ["algorithms"]
codeforces_contest: 2091
codeforces_index: "E"
codeforces_contest_name: "Codeforces Round 1013 (Div. 3)"
rating: 1300
weight: 2091
solve_time_s: 72
verified: true
draft: false
---

[CF 2091E - 興味深い比率](https://codeforces.com/problemset/problem/2091/E)

 **評価:** 1300
 **タグ:** ブルート フォース、数学、数論、2 つのポインタ
 **解決時間:** 1 分 12 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 整数のペアを数えるように求められます$(a, b)$どこ$1 \le a < b \le n$比率が$F(a, b) = \frac{\text{lcm}(a, b)}{\gcd(a, b)}$は素数です。 入力は複数のテスト ケースで構成され、それぞれが最大数を指定します。$n$、各テスト ケースについて、この条件を満たすペアの数を出力する必要があります。 

主な洞察は次のとおりです$F(a, b) = \frac{\text{lcm}(a, b)}{\gcd(a, b)} = \frac{a \cdot b / \gcd(a, b)}{\gcd(a, b)} = \frac{a \cdot b}{\gcd(a, b)^2}$。 それで$F(a, b)$は常に整数であり、素数である必要があります。 素数は1より大きいので、$F(a, b) \ge 2$。 

制約は重要です。$n$と同じ大きさにすることができます$10^7$、およびすべての合計$n$テストケース全体で次を超えない$10^7$。 すべてをチェックする素朴なアプローチ$O(n^2)$各テストケースのペアには最大で$10^{14}$最悪の場合の作戦は実行可能をはるかに超えています。 これは、線形時間に近い、またはわずかに超線形な解が必要であることを示しています。$n$。 

不注意なアプローチを破る可能性のあるエッジケースとしては、次のようなものがあります。$n$のように$n = 2$、ペアが 1 つだけ存在する場合$(1, 2)$、または次の場合$a$は 1 です。$F(1, p) = p$あらゆるプライムに対して$p$。 もう 1 つの注意が必要なケースは次のような場合です。$b$の倍数です$a$;$F(a, b)$単純化すると$\frac{b}{a}$したがって、基本整数の倍数を考慮することで、すべての興味深い比率を生成できます。 

## アプローチ

 ブルートフォース手法ではすべてのペアを列挙します$(a, b)$と$1 \le a < b \le n$、計算します$\gcd(a, b)$、計算します$F(a, b)$、素数かどうかを確認します。 これは原理的には正しいですが、$n \sim 10^7$このアプローチには次のことが必要です$O(n^2 \log n)$コンピューティングのための時間$\gcd$そして素数のチェックは一定時間ではありません。 これは明らかに遅すぎます。 

重要な観察は、次のように表現することです。$b$として$b = a \cdot k$、 どこ$k > 1$は整数です。 それから$\gcd(a, b) = a$そして$\text{lcm}(a, b) = a \cdot k$、 それで$F(a, b) = \frac{a \cdot k}{a} / a = k$。 したがって、$F(a, b)$まさにそのときがプライムです$b = a \cdot p$いくつかのプライムのために$p$。 問題は、各整数の倍数をすべて数えることに帰着します。$a$素数によって$p$製品が以下を超えないように$n$。 

までの素数を事前計算できます。$n$ふるいを使用してそれぞれ$a$すべての素数を反復処理する$p$と$a \cdot p \le n$。 これにより、複雑さが大まかに軽減されます。$O(n \log \log n)$ふるいプラス用$O(n \log n)$ペアを生成するため、制約があれば実現可能です。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n² log n) | お(1) | 遅すぎる |
 | 素数の倍数 | O(n log log n + n log n) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 最大値までのすべての素数を事前計算します。$n$エラトステネスのふるいを使用したすべてのテスト ケースにわたって。 これにより、素数をチェックして列挙する迅速な方法が得られます。 
2. 配列を初期化する`counts`どこ`counts[i]`指定された対象に対する興味深い比率の数を保存します$i$。 
3. 各整数について$a$1から$n$、素数を反復処理する$p$そのような$a \cdot p \le n$。 有効な各製品$b = a \cdot p$有効なペアを形成します$(a, b)$ので、増加します`counts[a * p]`。 
4. 複数のテスト ケースを効率的に処理するには、すべての整数のカウントを最大で累積します。`max_n`必要に応じてプレフィックスの合計を保存します。 
5. 与えられたテストケースごとに$n$、すべての有効なペアからの寄与を合計します。$n$そして結果を印刷します。 
なぜうまくいくのか: 因数分解によって$b$の倍数として$a$、私たちは保証します$\gcd(a, b) = a$、 それで$F(a, b) = b / a$。 これにより、整数の結果が保証され、素数性を直接チェックできるようになります。 ふるいにより、すべての素因数が考慮されることが保証され、倍数を反復することで、すべての有効なペアが正確に 1 回カウントされることが保証されます。 制約$1 \le a < b \le n$自然に維持されています。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

# sieve of Eratosthenes
def sieve(max_n):
    is_prime = [True] * (max_n + 1)
    is_prime[0] = is_prime[1] = False
    for i in range(2, int(max_n**0.5) + 1):
        if is_prime[i]:
            for j in range(i*i, max_n + 1, i):
                is_prime[j] = False
    primes = [i for i, val in enumerate(is_prime) if val]
    return primes, is_prime

def solve():
    t = int(input())
    ns = [int(input()) for _ in range(t)]
    max_n = max(ns)
    primes, _ = sieve(max_n)
    
    # precompute number of interesting ratios
    counts = [0] * (max_n + 1)
    for p in primes:
        for a in range(1, max_n // p + 1):
            counts[a * p] += 1
    
    # prefix sums
    for i in range(1, max_n + 1):
        counts[i] += counts[i - 1]
    
    for n in ns:
        print(counts[n])

if __name__ == "__main__":
    solve()
```ふるいはすべての素数を効率的に生成します。 各素数を反復し、次に次を超えないすべての倍数を反復します。`max_n`。 プレフィックス合計配列を使用すると、各テスト ケースに一定時間で答えることができます。 境界に細心の注意が払われます: ループ`a in range(1, max_n // p + 1)`許可された最大値を超えないようにする$b$。 

## 実用的な例

 サンプル入力`n = 5`:

 | | 素数 p | a * p <= 5 | この素数の後の count[a*p] |
 | --- | --- | --- | --- |
 | 1 | 2,3,5 | 2,3,5 | カウント[2]=1、カウント[3]=1、カウント[5]=1 |
 | 2 | 2 | 4 | カウント[4]=1 |
 | 3 | 2 | 6 > 5 | スキップ |
 | 4 | 2 | 8 > 5 | スキップ |
 | 5 | 2 | 10 > 5 | スキップ |

 プレフィックスの合計から合計が得られます`counts[5] = 4`。 

これにより、アルゴリズムがペアをカウントしていることが確認されます。`(1,2)`、`(1,3)`、`(1,5)`、`(2,4)`正しく。 

サンプル入力`n = 10`:

 | | p | a*p <= 10 | カウントされたペア |
 | --- | --- | --- | --- |
 | 1 | 2,3,5,7 | 2,3,5,7 | 4 |
 | 2 | 2,3,5 | 4,6,10 | あと 3 件 |
 | 3 | 2,3 | 6,9 | あと 2 件 |
 | 4 | 2 | 8 | あと 1 つ |
 | 5 | 2 | 10 | すでに 2*5 に数えられていますか? 別々にカウントします |
 | 6..10 | 10 を超える製品のみ | スキップしました | |

 合計数 = 11、予想される出力と一致します。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n log log n + n log n) | ふるい + 素数と倍数の反復 |
 | スペース | O(n) | カウントとふるい配列を保存する |

 これは、2 秒の制限時間内に快適に収まります。`n ≤ 10^7`メモリ制限は 256 MB です。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    out = io.StringIO()
    sys.stdout = out
    solve()
    return out.getvalue().strip()

# provided samples
assert run("4\n5\n10\n34\n10007\n") == "4\n11\n49\n24317"

# minimum-size input
assert run("1\n2\n") == "1"

# maximum-size input (just check it runs)
assert run("1\n10000000\n
```
