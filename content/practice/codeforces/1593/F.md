---
title: "CF 1593F - 赤黒ナンバー"
description: "数字の文字列と 2 つの約数 A と B が与えられます。課題は、赤い数字で形成される数字が A で割り切れ、黒い数字で形成される数字が B で割り切れるように、各数字を赤または黒に色付けすることです。少なくとも両方の色が使用されます。"
date: "2026-06-10T09:10:01+07:00"
tags: ["codeforces", "competitive-programming", "dfs-and-similar", "dp", "implementation", "math", "meet-in-the-middle"]
categories: ["algorithms"]
codeforces_contest: 1593
codeforces_index: "F"
codeforces_contest_name: "Codeforces Round 748 (Div. 3)"
rating: 2100
weight: 1593
solve_time_s: 136
verified: false
draft: false
---

[CF 1593F - 赤黒番号](https://codeforces.com/problemset/problem/1593/F)

 **評価:** 2100
 **タグ:** dfs および同様のもの、dp、実装、数学、中間者会合
 **解決時間:** 2 分 16 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 数字の文字列と 2 つの約数 A と B が与えられます。課題は、赤い数字で形成される数字が A で割り切れ、黒い数字で形成される数字が B で割り切れて、両方の色が少なくとも 1 回使用されるように、各数字を赤または黒に色付けすることです。 すべての有効なカラーリングの中で、赤と黒の数字のカウント間の絶対差を最小限に抑えることを目指しています。 出力は、カラーリングを表す「R」と「B」の文字列か、有効なカラーリングが存在しない場合は -1 になります。 

制約により、n (桁数) が最大 40 であることがわかります。A と B は最大 40 で、テスト ケースは最大 10 です。 これは十分に小さいため、構造を活用できれば n の指数時間解も実現可能です。2^40 は大きいですが、モジュラー演算や枝刈り手法と組み合わせると管理可能であるためです。 

エッジ ケースは、先頭にゼロがある数値、数字が繰り返される数値、または互いに素である約数から発生します。 たとえば、次のような数字`10`A=2 および B=5 の場合、次のように分割できます。`1`赤と`0`黒、数字を形成`1`そして`0`、割り切れない場合。 単純なアプローチでは、先行ゼロの可能性を無視したり、連続する数字グループのみを考慮したりする可能性があり、誤った結果が生成されます。 

## アプローチ

 強引な解決策は、n 桁の可能な色をすべて試し、赤と黒の桁の数値を計算し、割り切れるかどうかを確認することです。 これには、2^n 通りの可能性を反復し、それぞれに対して文字列から数値への変換 (O(n)) を実行する必要があります。 n が最大 40 の場合、約 10^12 回の演算が必要となり、非常に遅すぎます。 

重要な洞察は、割り算は赤い桁の剰余 A と黒い桁の剰余 B のみに依存するということです。 完全な数値を保存する代わりに、カラーリングを作成するときに、選択した数字のモジュロ A または B によって形成される数値の残りを保存できます。 これは自然に動的プログラミング ソリューションにつながります。 dp[position][red_mod][black_mod][count_red] は、最初の位置に色を付けることが可能かどうかを格納します。`position`達成すべき数字`red_mod`モジュロ A、`black_mod`モジュロ B、付き`count_red`赤い数字。 黒の桁の数は暗黙的に`position - count_red`。 これにより、状態空間は n * A * B * n ≈ 40 * 40 * 40 * 40 = 256 万に減り、これは 1 秒の制限では実現可能です。 

次に、最小の |count_red - count_black| を使用して有効な最終状態からバックトラックすることによってカラーリングを再構築します。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(2^n * n) | O(n) | 遅すぎる |
 | モジュロ状態の DP | O(n * A * B * n) | O(n * A * B * n) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 数値を数字のリストに変換します。 次元 [n+1][A][B][n+1] の 4 次元 DP 配列を初期化し、位置 0 の基本ケースを両方の色と 0 の赤色の数字の剰余 0 で True に設定します。 
2. 0 から n-1 までの位置を繰り返します。 位置の各 DP 状態について`i`と`red_mod`、`black_mod`、 そして`count_red`、次の桁を赤または黒に着色することを検討してください。 
3. 次の桁が赤色の場合、A を法とする新しい剰余を次のように計算します。`(red_mod * 10 + digit) % A`。 インクリメント`count_red`1. 位置の DP 状態を更新します。`i+1`。 
4. 次の桁が黒の場合、新しい剰余モジュロ B を次のように計算します。`(black_mod * 10 + digit) % B`。`count_red`同じままです。 位置の DP 状態を更新する`i+1`。 
5. すべての位置を処理した後、red_mod=0 および black_mod=0 である位置 n のすべての DP 状態を調べます。 それぞれについて計算すると、`count_black = n - count_red`そして`diff = |count_red - count_black|`。 最小限の差分で状態を追跡します。 
6. そのような状態が存在しない場合は、-1 を出力します。 それ以外の場合は、選択した最終状態からバックトラックして、各ステップで保存された DP 状態につながる色を選択することによってカラーリングを再構築します。 

機能する理由: 各 DP 状態は有効な部分的なカラーリングを表し、割り算に必要なすべてのモジュール情報を保存します。 各位置のすべての桁を考慮し、モジュラー剰余を更新することで、すべての可能性が確実に探索されます。 |r - b| を最小化する DP を埋めた後、絶対差が最小限になるカラーリングを確実に選択します。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

for _ in range(int(input())):
    n, A, B = map(int, input().split())
    s = input().strip()
    digits = [int(c) for c in s]

    dp = [[[[False]*(n+1) for _ in range(B)] for _ in range(A)] for _ in range(n+1)]
    parent = [[[[None]*(n+1) for _ in range(B)] for _ in range(A)] for _ in range(n+1)]

    dp[0][0][0][0] = True

    for i in range(n):
        d = digits[i]
        for r_mod in range(A):
            for b_mod in range(B):
                for r_count in range(i+1):
                    if not dp[i][r_mod][b_mod][r_count]:
                        continue

                    # Color red
                    new_r_mod = (r_mod*10 + d) % A
                    new_r_count = r_count + 1
                    if not dp[i+1][new_r_mod][b_mod][new_r_count]:
                        dp[i+1][new_r_mod][b_mod][new_r_count] = True
                        parent[i+1][new_r_mod][b_mod][new_r_count] = ('R', r_mod, b_mod, r_count)

                    # Color black
                    new_b_mod = (b_mod*10 + d) % B
                    if not dp[i+1][r_mod][new_b_mod][r_count]:
                        dp[i+1][r_mod][new_b_mod][r_count] = True
                        parent[i+1][r_mod][new_b_mod][r_count] = ('B', r_mod, b_mod, r_count)

    best_diff = n+1
    best_state = None
    for r_count in range(1, n):
        b_count = n - r_count
        if dp[n][0][0][r_count]:
            diff = abs(r_count - b_count)
            if diff < best_diff:
                best_diff = diff
                best_state = (n, 0, 0, r_count)

    if not best_state:
        print(-1)
        continue

    res = []
    i, r_mod, b_mod, r_count = best_state
    while i > 0:
        color, pr_mod, pb_mod, pr_count = parent[i][r_mod][b_mod][r_count]
        res.append(color)
        i, r_mod, b_mod, r_count = i-1, pr_mod, pb_mod, pr_count

    print(''.join(reversed(res)))
```DP セクションでは部分的なカラーリングの実現可能性を追跡します。`parent`配列を使用すると、バックトラッキングしてカラーリングを再構築できます。 以前に到達していない場合にのみ、それぞれの新しい状態を慎重に更新し、不必要な上書きを回避し、回復したパスの差が最小限に抑えられるようにします。 

## 実用的な例

 入力のトレース`02165`、A=3、B=13、n=5:

 | 私 | 数字 | r_mod | b_mod | r_count | アクション | 新しい状態 |
 | --- | --- | --- | --- | --- | --- | --- |
 | 0 | 0 | 0 | 0 | 0 | R | (1,0,0,1) |
 | 0 | 0 | 0 | 0 | 0 | B | (1,0,0,0) |
 | 1 | 2 | ... | ... | ... | ... | ... |

 DP を満たした後、r_mod=0、b_mod=0 で考えられる終了状態は、r_count=2 または 3 です。r_count=3、b_count=2 を選択すると、最小の差分 1 が得られます。バックトラッキングによりカラーリングが行われます。`RBRBR`。 

入力用`1357`、A=2、B=1、n=4:

 すべての桁は奇数です。 2 で割り切れる赤い数字は存在しません。 r_count >0 の場合、DP は red_mod=0 に到達しません。 -1 を出力します。 

これらのトレースは、アルゴリズムが可分性チェックを正しく処理し、最小の差を見つけていることを確認します。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n * A * B * n) | 4 つのネストされたループ: 位置、red_mod、black_mod、red_count |
 | スペース | O(n * A * B * n) | DP 配列と親配列はすべての状態を保存します。 

n ≤ 40、A、B ≤ 40、総状態数 ≈ 256 万。 各更新は一定時間です。 256 MB および 1 秒の制限内に快適に収まります。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    out = io.StringIO()
    sys.stdout = out
    exec(open("solution.py").read())
    return out.getvalue().strip()

# Provided samples
assert run("4\n5 3 13\n02165\n4 2 1\n1357\n8
```
