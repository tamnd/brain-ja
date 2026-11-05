---
title: "CF 1286A - ガーランド"
description: "それぞれ 1 から n までの番号が付けられた n 個の電球の列が与えられます。 一部の電球が欠落しており、入力配列のゼロで示されます。"
date: "2026-06-11T19:09:52+07:00"
tags: ["codeforces", "competitive-programming", "dp", "greedy", "sortings"]
categories: ["algorithms"]
codeforces_contest: 1286
codeforces_index: "A"
codeforces_contest_name: "Codeforces Round 612 (Div. 1)"
rating: 1800
weight: 1286
solve_time_s: 162
verified: true
draft: false
---

[CF 1286A - ガーランド](https://codeforces.com/problemset/problem/1286/A)

 **評価:** 1800
 **タグ:** dp、貪欲、ソート
 **解決時間:** 2 分 42 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 の行が与えられています`n`電球、それぞれに番号が付けられています`1`に`n`。 一部の電球が欠落しており、入力配列のゼロで示されます。 私たちの目標は、まだ存在していない番号を使用して不足している電球を埋め、異なるパリティを持つ隣接するペアの数を最小限にする順序で電球を配置することです。 この問題の複雑さは、1 つの偶数と 1 つの奇数を持つ隣接するペアがいくつあるかを数えます。 たとえば、シーケンスが次の場合、`1 4 2 3 5`、奇数と偶数の間には 2 つの遷移があります。`(1,4)`そして`(2,3)`。 

入力サイズが小さい場合:`n`最大 100 まで上がる可能性があります。これは、`O(n^3)`あるいは`O(n^4)`アプローチですが、`O(n^2)`動的プログラミング ソリューションはよりクリーンで堅牢です。 パリティを考慮せずに、欠落している最小の数値を貪欲に配置するだけの不注意なアプローチでは、不要な遷移が簡単に作成される可能性があります。 たとえば、次の場合`n=4`そしてそのシーケンスは`0 0 1 0`、パリティを考慮せずに左から右にゼロを埋めると、次の結果が得られる可能性があります`[2,3,1,4]`、3 つのパリティ遷移が生成されますが、`[3,2,1,4]`1つだけが得られます。 

エッジケースは、欠落している位置がすべて連続しているか、端にある場合に発生します。 このような場合、余分な複雑さを避けるために、最後に配置された電球のパリティを追跡する必要があります。 

## アプローチ

 素朴な総当り的な解決策では、欠落している数値の可能なすべての順列を試し、それぞれの結果として生じる複雑さをカウントし、最小値を返します。 これが機能するのは、`k`番号が欠落しているため、原則として必要です`k!`手配。 ただし、最悪の場合、`k=n=100`、天文学的に大きい（`100!`）そして明らかに実行不可能です。 欠落している数値を奇数グループと偶数グループに分割するあらゆる方法を試しても、組み合わせはすぐに爆発的に増加します。 

重要な観察は、複雑さは隣接する数値の正確な値ではなく、パリティのみに依存するということです。 これにより、この問題を **奇数と偶数の位置と残りの数に関する動的計画問題**に縮小することができます。 具体的には、配列内の各位置について、前の数値が奇数または偶数の場合の最小複雑さと、残っている未使用の奇数および偶数のバルブの数を追跡できます。 DP 状態は`(pos, odd_remaining, even_remaining, prev_parity)`そして遷移は奇数または偶数を現在のゼロ位置に配置し、それが異なる場合は複雑さを更新します。`prev_parity`。 

この構造が機能するのは、すべての決定が前の番号のパリティと、各タイプの電球が何個残っているかのみに依存するためです。 正確な値を知る必要はありません。 中間結果を保存し、再計算を避けるためにメモ化を使用します。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(k!) | O(n) | 遅すぎる |
 | DP オーバーパリティ | O(n^3) | O(n^3) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 配列に欠落している数値を特定し、奇数と偶数に分類します。 合計数が次の場合`1`に`n`、すでに存在する数値を減算して、不足している数値を計算します。 
2. DP テーブルを定義する`dp[pos][odd_left][even_left][prev_parity]`、 どこ`pos`は現在のインデックス、`odd_left`そして`even_left`未使用の奇数および偶数の電球がいくつ残っているかを数え、`prev_parity`前に配置された電球のパリティです。 電球が配置されていない開始位置を除き、すべての値を無限大に初期化します。`prev_parity`can be undefined.
 3. 位置を繰り返します。 電球がすでに配置されている (ゼロ以外) 場合は、そのパリティと一致する遷移のみを考慮して DP を更新し、そのパリティが異なる場合は複雑さを増分します。`prev_parity`。 電球が足りない場合 (ゼロ)、残っている場合は奇数または偶数の電球を置いてみてください。 両方の選択肢について最小限の複雑さを採用して DP テーブルを更新します。 
4. 最終的に、答えは次の値のうちの最小値になります。`dp[n][0][0][0]`そして`dp[n][0][0][1]`、最後の電球が偶数か奇数であるように配置されたすべての電球を表します。 
5. この最小値をガーランドの最小複雑さとして返します。 

機能する理由: DP は、各ステップでゼロのパリティの有効な選択肢をすべて考慮し、残りの奇数/偶数の電球の正確な数を追跡するため、正確性を保証します。 複雑さはパリティ遷移のみに依存するため、DP 状態は最適な決定に必要な情報を完全に取得します。 メモ化により、重複する部分問題が 1 回だけ計算されることが保証されます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

n = int(input())
p = list(map(int, input().split()))

used = [False] * (n + 1)
for x in p:
    if x != 0:
        used[x] = True

odd_left = sum(1 for i in range(1, n+1) if not used[i] and i % 2 == 1)
even_left = sum(1 for i in range(1, n+1) if not used[i] and i % 2 == 0)

INF = 10**9
dp = [[[INF, INF] for _ in range(even_left+1)] for _ in range(odd_left+1)]
dp[odd_left][even_left][0] = 0
dp[odd_left][even_left][1] = 0

for idx, val in enumerate(p):
    new_dp = [[[INF, INF] for _ in range(even_left+1)] for _ in range(odd_left+1)]
    for o in range(odd_left+1):
        for e in range(even_left+1):
            for prev_parity in [0,1]:
                if dp[o][e][prev_parity] >= INF:
                    continue
                if val != 0:
                    cur_parity = val % 2
                    add = 0 if idx == 0 or cur_parity == prev_parity else 1
                    new_dp[o][e][cur_parity] = min(new_dp[o][e][cur_parity], dp[o][e][prev_parity] + add)
                else:
                    if o > 0:
                        cur_parity = 1
                        add = 0 if idx == 0 or cur_parity == prev_parity else 1
                        new_dp[o-1][e][cur_parity] = min(new_dp[o-1][e][cur_parity], dp[o][e][prev_parity] + add)
                    if e > 0:
                        cur_parity = 0
                        add = 0 if idx == 0 or cur_parity == prev_parity else 1
                        new_dp[o][e-1][cur_parity] = min(new_dp[o][e-1][cur_parity], dp[o][e][prev_parity] + add)
    dp = new_dp

res = min(dp[0][0][0], dp[0][0][1])
print(res)
```説明: まず、未使用の奇数と偶数を数えます。 次に、DP オーバーを使用します`(odd_left, even_left, prev_parity)`効率を高めるための完全なインデックスの代わりに。 各位置について、前の状態を次の状態に伝播し、パリティが変化した場合には複雑さを増分します。 負のインデックスを避けるために境界を注意深くチェックします。 すべての位置を処理した後、最小の複雑さが求められます。 

## 実用的な例

 ### 例 1

 入力:`5\n0 5 0 2 3\n`| いずみ | 値 | o | e | 前のパリティ | new_dp[奇数][偶数][cur_parity] |
 | --- | --- | --- | --- | --- | --- |
 | 0 | 0 | 2 | 1 | - | 奇数:1、偶数:2 を試してください。 
| 1 | 5 | - | - | - | パリティ=1のみを考慮し、同じパリティの場合は0を追加し、それ以外の場合は1を追加します。 
| 2 | 0 | 1 | 1 | - | 奇数/偶数の配置を試してください |
 | 3 | 2 | - | - | - | parity=0 のみを考慮します |
 | 4 | 3 | - | - | - | parity=1 のみを考慮します |

 結果：`2`トランジション。 

### 例 2

 入力:`7\n0 7 3 5 0 0 0\n`残りの奇数/偶数電球を追跡しながらすべてのゼロを処理すると、最適な配置での単一の遷移が得られます。 

これらのトレースは、DP が内部ゼロと終端ゼロの両方を正しく処理していることを確認します。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n^3) | 3 つのネストされたループ: odd_left (≤n/2)、even_left (≤n/2)、prev_parity (2) |
 | スペース | O(n^3) | サイズ (odd_left+1) * (even_left+1) * 2 の DP テーブル |

 与えられた`n≤100`、最悪の場合の操作は 50^2_2_100=~500,000 程度であり、1 秒の制限時間内では許容されます。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    exec(open('solution.py').read())  # assuming code above is saved as solution.py
    return sys.stdout.getvalue().strip()

# provided samples
assert run("5\n0 5 0
```
