---
title: "CF 906D - パワータワー"
description: "与えられた数値を法とする「電力塔」を計算するように求められます。 概念的には、それぞれが正の整数乗を持つ一連の岩石を想像してください。"
date: "2026-06-12T23:21:08+07:00"
tags: ["codeforces", "competitive-programming", "chinese-remainder-theorem", "math", "number-theory"]
categories: ["algorithms"]
codeforces_contest: 906
codeforces_index: "D"
codeforces_contest_name: "Codeforces Round 454 (Div. 1, based on Technocup 2018 Elimination Round 4)"
rating: 2700
weight: 906
solve_time_s: 887
verified: true
draft: false
---

[CF 906D - パワータワー](https://codeforces.com/problemset/problem/906/D)

 **評価:** 2700
 **タグ:** 中国の剰余定理、数学、整数論
 **解決時間:** 14 分 47 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 与えられた数値を法とする「パワータワー」を計算するように求められます。 概念的には、それぞれが正の整数乗を持つ一連の岩石を想像してください。 _l_ から _r_ までの番号が付けられた岩からタワーを構築するには、一番上の岩 _r_ から始めて _l_ まで作業を開始し、現在の岩のパワーをその上のタワーのパワーまで繰り返し上げます。 正式には、岩があれば$w_l, w_{l+1}, \dots, w_r$、累積電力は$$w_l^{w_{l+1}^{\dots^{w_r}}} \mod m$$入力により岩の数が得られます$n$、係数$m$、各岩のパワーを順番に、$q$岩石の部分範囲の累積パワーを求めるクエリ。 

制約が教えてくれる$n$そして$q$到達できる$10^5$岩石の強度と弾性率は最大で$10^9$。 べき乗を直接評価する単純な解決策では、天文学的に大きな数が生成されるため、実行不可能です。 Python の任意精度の演算でも、繰り返し計算するには遅すぎます。$10^5$クエリ。 これは、全累乗の計算を回避し、代わりにモジュラー演算のプロパティを使用するソリューションが必要であることを意味します。 

明らかではないエッジケースとしては、次のような場合があります。$m = 1$、結果は常に 0 であり、パワーが 1 であるシーケンスでは、タワーがすぐに崩壊する可能性があります。 別の注意が必要なケースは、連続するべき乗が非常に大きい場合に発生します。たとえば、$2^{2^{30}}$、直接計算すると 64 ビット整数でもオーバーフローします。 これらのケースを正しく処理するには、モジュラー リダクションとオイラーの定理を注意深く使用する必要があります。 

## アプローチ

 ブルートフォースアプローチはタワーを上から下まで反復し、ネストされたべき乗モジュロを計算します。$m$。 具体的には、クエリごとに以下を計算できます。```
result = w_r
for i in range(r-1, l-1, -1):
    result = w_i ** result % m
```これは数学的には正しいですが、実際には不可能です。 1 つのクエリでも 10^9^{10^9} もの大きな数値のべき乗が必要になる可能性があり、これには天文学的なコストがかかります。 まで$10^5$クエリの複雑さは部分配列の長さに応じて指数関数的に増加するため、耐えられません。 

最適化のための重要な洞察は、べき乗モジュラーにはオイラーの定理によって支配される再帰的特性があるということです。 もし$a$そして$m$互いに素であり、$a^{\phi(m)} \equiv 1 \mod m$、 どこ$\phi$はオイラーのトーティエント関数です。 これにより、指数モジュロを減らすことができます$\phi(m)$各ステップで、正確さを維持しながら中間検出力のサイズを大幅に削減します。 もし$a$そして$m$が互いに素ではないため、正しいモジュロの動作が失われることを避けるために、通常は指数を境界付けて加算することによって、リダクションを慎重に処理する必要があります。$\phi(m)$必要に応じて。 

これにより、問題がタワーを下降する再帰的計算に変換され、底に到達するまでトーティエント チェーンを使用して各ステップで指数が減少します。 このアプローチは、大国や大企業に対しても効率的です。$m$。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(q * (r-l+1) * べき乗) | お(1) | 遅すぎる |
 | 最適 | O(n log m + q log m) | O(log m) 再帰深さ | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 係数のトーティエント チェーンを事前計算します。 から始まる$m$、繰り返し計算します$\phi(m_i)$これにより、再帰の各レベルで指数の削減が可能になります。 
2. 再帰関数を定義する`tower(l, r, mod)`それは計算します$w_l^{w_{l+1}^{\dots^{w_r}}} \mod mod$。 もし`l == r`、 戻る$w_l \mod mod$。 それ以外の場合は計算します`next_exp = tower(l+1, r, phi(mod))`。 
3. べき乗剰余演算を指数で使用する`next_exp`。 指数を超えた場合`phi(mod)`または非共素状況により必要以上に大きい場合は、追加して調整します。`phi(mod)`オーバーフローを正しく処理するため。 
4. クエリごとに、`tower(l, r, m)`そして結果を出力します。 

機能する理由: 合計削減により、レベルごとに、潜在的に巨大な指数がはるかに小さい同等のモジュロに置き換えられることが保証されます。$\phi(m)$一方、オイラーの定理は結果のモジュロを保証します。$m$正しいままです。 これをタワーの下で再帰的に適用すると、正確性が不変に保たれます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline
sys.setrecursionlimit(200000)

def phi(n):
    result = n
    i = 2
    while i * i <= n:
        if n % i == 0:
            while n % i == 0:
                n //= i
            result -= result // i
        i += 1
    if n > 1:
        result -= result // n
    return result

def mod_pow(a, b, mod):
    result = 1
    a %= mod
    while b > 0:
        if b & 1:
            result = result * a % mod
        a = a * a % mod
        b >>= 1
    return result

def tower(l, r, mod):
    if mod == 1:
        return 0
    if l == r:
        return w[l] % mod
    next_exp = tower(l+1, r, phis[mod])
    if next_exp >= phis[mod]:
        next_exp = next_exp % phis[mod] + phis[mod]
    return mod_pow(w[l], next_exp, mod)

n, m = map(int, input().split())
w = [0] + list(map(int, input().split()))
phis = {}

def build_phi_chain(x):
    if x in phis:
        return phis[x]
    if x == 1:
        phis[x] = 1
    else:
        phis[x] = phi(x)
    return phis[x]

def prepare_phis(x):
    build_phi_chain(x)
    current = x
    while current != 1:
        current = phis[current]
        build_phi_chain(current)

prepare_phis(m)

q = int(input())
for _ in range(q):
    l, r = map(int, input().split())
    print(tower(l, r, m))
```このソリューションは、効率的な再帰的指数削減を可能にするために、係数のトーティエント チェーンを事前計算することから始まります。`mod_pow`高速べき乗剰余演算を実行します。 再帰的`tower`関数は送電塔のモジュロを計算します`mod`、合計を超える値を考慮して必要に応じて指数を調整します。 各クエリが呼び出すのは、`tower`直接。 

## 実用的な例

 ### サンプル入力 1```
6 1000000000
1 2 2 3 3 3
8
1 1
1 6
2 2
2 3
2 4
4 4
4 5
4 6
```| クエリ | l | r | 計算されたタワー | 出力 |
 | --- | --- | --- | --- | --- |
 | 1 | 1 | 1 | 1 | 1 |
 | 2 | 1 | 6 | 1^{2^{2^{3^{3^3}}}} mod 1e9 | 1 |
 | 3 | 2 | 2 | 2 | 2 |
 | 4 | 2 | 3 | 2^2 = 4 | 4 |
 | 5 | 2 | 4 | 2^{2^3} = 2^8 = 256 | 256 |
 | 6 | 4 | 4 | 3 | 3 |
 | 7 | 4 | 5 | 3^3 = 27 | 27 |
 | 8 | 4 | 6 | 3^{3^3} mod 1e9 = 597484987 | 597484987 |

 このトレースは、トーティエント チェーンと再帰によって、巨大な指数が管理可能な計算にどのように削減されるかを示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(q log m * log max w_i) | 各クエリはタワーを下降し、各レベルの高速モジュラべき乗を使用して、合計チェーンのモジュロを削減します。 
| スペース | O(n + log m) | 累乗の配列と合計チェーンの長さによって制限される再帰スタック |

 これは制限内に収まります。$log m \le 30$のために$m \le 10^9$、 そして$q \le 10^5$。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    output = io.StringIO()
    sys.stdout = output
    # Call the solution function
    exec(open('power_tower.py').read())
    return output.getvalue().strip()

# Provided sample
assert run("""6 1000000000
1 2 2 3 3 3
8
1 1
1 6
2 2
2 3
2 4
4 4
4 5
4 6""") == """1
1
2
4
256
3
27
597484987"""

# Custom cases
assert run("1
```
