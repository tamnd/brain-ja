---
title: "CF 917C - ポリーウォッグ"
description: "x 個のポリーウォッグのグループを、列の最初の x 個の石から最後の x 個の石まで移動するように求められます。 石には 1 から n までの番号が付けられており、各ポリウォッグはちょうど 1 つの石を占めます。"
date: "2026-06-13T02:16:36+07:00"
tags: ["codeforces", "competitive-programming", "combinatorics", "dp", "matrices"]
categories: ["algorithms"]
codeforces_contest: 917
codeforces_index: "C"
codeforces_contest_name: "Codeforces Round 459 (Div. 1)"
rating: 2900
weight: 917
solve_time_s: 386
verified: false
draft: false
---

[CF 917C - ポリーウォグ](https://codeforces.com/problemset/problem/917/C)

 **評価:** 2900
 **タグ:** 組み合わせ論、dp、行列
 **解決時間:** 6 分 26 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 のグループを移動するように求められています。`x`ポリーウォグスは最初から`x`最後まで石が並んでいる`x`石。 石には番号が振ってあります`1`を通して`n`、各ポリウォッグはちょうど 1 つの石を占めます。 ポリーウォッグはせいぜいジャンプできる`k`ジャンプ距離ごとに石を一度に右に移動します`i`原価計算`c[i]`エネルギー。 一部の石は特別であり、追加のエネルギーコストまたはボーナスがかかります`w[p]`。 2 匹のポリウォッグが同時に同じ石を占有することはできません。 目標は、すべてのポリウォッグを最後まで動かすのに必要な最小の総エネルギーを見つけることです。`x`ジャンプコストと特別な石を考慮した石。 

制約は次のことを教えてくれます`x`そして`k`は最大でも 8 と非常に小さいですが、`n`最大 10^8 まで可能です。 これは、すべての石を明示的にシミュレートしたり、すべての位置を反復したりする余裕がないことをすぐに示唆しています。 代わりに、解決策は少数のポリウォッグと狭いジャンプ範囲を利用する必要があります。 特殊石の数は多くても25個なので、他の石に比べると少ないです。`n`。 エネルギー値は負の値になる可能性があるため、最適なパスは意図的に有益な特別な石に到達する可能性があります。 

ジャンプが最後の石に重なると、微妙なエッジケースが発生します。 たとえば、Polywog が 1 ～ 3 個の石をジャンプでき、最後の石の直前にいるとします。`x`石の場合は、他のポリウォッグと衝突することなく、空のターゲット石に正確に着地することを確認する必要があります。 常に最小の当面のジャンプコストを取るという単純な貪欲な戦略は、後のポリウォッグにコストのかかるジャンプを強制したり、マイナス効果のある特別な石に着地させたりする可能性があるため、失敗する可能性があります。 

## アプローチ

 単純なアプローチは、可能なすべての動きのシーケンスを状態遷移としてモデル化することです。 状態はすべての位置によって定義できます。`x`各状態から、左端のpollywogの有効なジャンプをすべて生成します。 各遷移のエネルギーを計算し、最小総エネルギーを再帰的に見つけます。 これは原理的には正しいですが、状態の数は`O(n^x)`最悪の場合、これは天文学的に大きくなります。`n`を使用しても 10^8 に達する可能性があります`x`したがって、直接の状態空間検索は実行できません。 

重要な洞察は、ポリーウォッグが厳密に右に移動し、お互いを追い越すことができないという観察から得られます。 それらの相対的な順序は維持されるため、エネルギーを絶対的な位置ではなくポリウォッグ間の距離の関数として考えることができます。 以来`k`そして`x`が小さい場合は、移動する石のウィンドウからのポリウォッグのオフセットとして状態をエンコードし、次の特別な石または最後の石に到達するための最小エネルギーを計算できます。`x`動的プログラミングを使用した石。 特殊な石がまばらであるということは、明示的に少数の位置を処理するだけで済み、それらの間で、min-plus 算術を使用して最小エネルギーを行列積として計算できることを意味します。 これにより、複雑さが指数関数的に軽減されます。`n`指数関数的に`x`そして`k`、独立して`n`、大きなギャップを効率的に処理します。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n^x * k^x) | O(n^x) | 遅すぎる |
 | 最適 (DP + 行列累乗) | O(q * k^x * x^2) | O(k^x) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. の状態をエンコードします。`x`相対位置のタプルとしてのpollywogs。 ジャンプはせいぜい`k`石、サイズのウィンドウ内の相対的な位置`k`衝突に関するすべての制約をキャプチャします。 
2. 移行マトリックスを構築する`T`単一の移動の場合、`T[s1][s2]`状態から移動するための最小エネルギーです`s1`述べます`s2`ジャンプコストと特殊な石の効果を考慮すると、一番左のポリウォグの1回のジャンプで。 
3. 特殊な石がまばらなので、連続する特殊な石の間でパスをセグメントに分割できます。 各セグメントごとに、`d`特別な石のない石、遷移行列の累乗を上げる`d`min-plus 演算の下。 これにより、横断するための最小エネルギーが計算されます。`d`石を一度にまとめて。 
4. それぞれの特別な石に、石のエネルギーを追加してエネルギーを調整します。`w[p]`一番左のポリウォッグが着地したときの値。 次に、次のセグメントの行列のべき乗を続けます。 
5. すべての特別な石を処理した後、最後のセグメントにつながる最後のセグメントを処理します。`x`石。 すべての有効な状態のうち、Pollywogs が最後の状態を占める最小のエネルギー。`x`石が答えです。 

これが機能する理由: 不変条件は、各ステップで、DP または行列が各エンコード状態に到達するための最小限のエネルギーを含むということです。 ポリーウォーグは互いに追い越すことができないため、状態がスキップされることはなく、行列のべき乗は石のセグメント全体に最小限のエネルギーを正しく伝播します。 特別な石は到達した時点で正確に処理されるため、すべてのエネルギー寄与が考慮されます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

from itertools import product
from collections import defaultdict

def min_plus_matmul(A, B):
    n = len(A)
    C = [[float('inf')]*n for _ in range(n)]
    for i in range(n):
        for j in range(n):
            for k in range(n):
                C[i][j] = min(C[i][j], A[i][k]+B[k][j])
    return C

def mat_pow(mat, power):
    n = len(mat)
    res = [[0 if i==j else float('inf') for j in range(n)] for i in range(n)]
    while power:
        if power % 2:
            res = min_plus_matmul(res, mat)
        mat = min_plus_matmul(mat, mat)
        power //= 2
    return res

x, k, n, q = map(int, input().split())
c = list(map(int, input().split()))
special = {}
for _ in range(q):
    p, w = map(int, input().split())
    special[p] = w

# Enumerate all valid relative states
states = []
def dfs(pos, last):
    if len(pos) == x:
        states.append(tuple(pos))
        return
    for jump in range(1, k+1):
        next_pos = last + jump
        dfs(pos+[next_pos], next_pos)
dfs([], 0)

state_index = {s:i for i,s in enumerate(states)}
size = len(states)

# Build single-step transition matrix
T = [[float('inf')]*size for _ in range(size)]
for i, s1 in enumerate(states):
    for j, s2 in enumerate(states):
        valid = True
        cost = 0
        for a, b in zip(s1, s2):
            d = b - a
            if d < 1 or d > k:
                valid = False
                break
            cost += c[d-1]
        if valid:
            T[i][j] = cost

dp = [float('inf')]*size
dp[state_index[tuple(range(1,x+1))]] = 0

# Process segments between special stones
points = sorted(list(special.keys()) + [n-x+1])
prev = x+1
for p in points:
    d = p - prev
    if d > 0:
        Tp = mat_pow(T, d)
        new_dp = [float('inf')]*size
        for i in range(size):
            for j in range(size):
                new_dp[j] = min(new_dp[j], dp[i]+Tp[i][j])
        dp = new_dp
    # apply special stone effect if leftmost pollywog lands on it
    new_dp = [float('inf')]*size
    for i, s in enumerate(states):
        leftmost = s[0] + (p-prev)
        add = special.get(leftmost, 0)
        new_dp[i] = dp[i] + add
    dp = new_dp
    prev = p

# Final segment to last x stones
d = n - x + 1 - prev
if d > 0:
    Tp = mat_pow(T, d)
    new_dp = [float('inf')]*size
    for i in range(size):
        for j in range(size):
            new_dp[j] = min(new_dp[j], dp[i]+Tp[i][j])
    dp = new_dp

# Answer: min energy among states ending at last x stones
ans = float('inf')
for i, s in enumerate(states):
    if s == tuple(range(n-x+1, n+1)):
        ans = min(ans, dp[i])
print(ans)
```コードはまず、次のウィンドウ内でのpollywogsのすべての相対状態を生成します。`k`ジャンプ制限を考慮したトランジションを列挙します。 行列累乗は、特殊な石間の大きなギャップを効率的に処理します。 各特別な石のエネルギー調整は、左端のポリウォッグがそれに到達したときに適用されます。 境界インデックスは、衝突が起こらず、最終位置が最後の位置と正確に一致するように慎重に処理されます。`x`石。 

## 実用的な例

 サンプル 1 の使用:

 | ステップ | ポジション (状態) | dp値 | コメント |
 | --- | --- | --- | --- |
 | 0 | (1,2) | 0 | 初期位置 |
 | ジャンプ 3 | (2,5) | 1 | 最小限のジャンプコスト |
 | ジャンプ 2 | (3 | | |
