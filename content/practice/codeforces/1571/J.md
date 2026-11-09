---
title: "CF 1571J - 2 つの鉄道"
description: "問題は実装上の小さなバグではありません。 アルゴリズムのアプローチ全体が根本的に間違っています。 これをすぐに反証するサンプルを追跡してみましょう。提案されたアルゴリズムは、少なくとも 1 つの '1' を確認し、最初と最後の文字のみをチェックします。 - s[0] = ')' → 反転が必要 -…"
date: "2026-06-10T11:33:46+07:00"
tags: ["codeforces", "competitive-programming", "*special"]
categories: ["algorithms"]
codeforces_contest: 1571
codeforces_index: "J"
codeforces_contest_name: "Kotlin Heroes: Episode 8"
rating: 3000
weight: 1571
solve_time_s: 562
verified: false
draft: false
---

[CF 1571J - 2 つの鉄道](https://codeforces.com/problemset/problem/1571/J)

 **評価:** 3000
 **タグ:** *特別な
 **解決時間:** 9 分 22 秒
 **確認済み:** いいえ

 ## 解決策
 問題は実装上の小さなバグではありません。 アルゴリズムのアプローチ全体が根本的に間違っています。 

それを即座に反証するサンプルを追跡してみましょう。```
n = 6
s = ))(()(
a = 101
```提案されたアルゴリズムでは、少なくとも 1 つの`'1'`、その後、最初と最後の文字のみをチェックします。 

-`s[0] = ')'`→反転が必要
 -`s[5] = '('`→反転が必要

 戻ります`2`。 

しかし、正しい答えは、`4`。 

なぜ？ 制約は **すべての長さ 4 ウィンドウにあり、その対応するビットが`a`は 1** です。 

ここで:

 - 窓`[0..3]`通常の括弧シーケンスである必要があります。 
- 窓`[2..5]`通常の括弧シーケンスである必要があります。 

これらのウィンドウは重なっています。 彼らの内面の性格は重要です。 最初と最後の文字を修正するだけでは、どちらのウィンドウも有効であるとは限りません。 

実際、「最初と最後の文字だけが重要である」という社説の主張全体が誤りです。 

CF 1571E の実際のソリューションでは、4 ポジションのスライディング ウィンドウでダイナミック プログラミングを使用します。 各制約には連続する 4 文字のみが含まれるため、割り当てられた最後の 3 文字を状態として保持しながら、文字列を左から右に処理できます。 

すべてのポジションについて、最終的なキャラクターが次のキャラクターであるかどうかを決定します。`'('`または`')'`。 元のキャラクターを維持する場合のコストは 0、反転する場合は 1 です。 

連続する 4 文字を割り当てると、対応する制約ビットがチェックされます。 

- もし`a[i] = '1'`、4 文字ブロックは、2 つの有効な長さ 4 の通常の括弧シーケンスのいずれかである必要があります。 

-`(())`-`()()`- もし`a[i] = '0'`、制限は課されません。 

これにより、次の DP が得られます。$2^3 = 8$州。 

正しい解決策は次のとおりです。```python
import sys
input = sys.stdin.readline

INF = 10 ** 18

VALID = {
    0b1100,  # (())
    0b1010,  # ()()
}

t = int(input())

for _ in range(t):
    n = int(input())
    s = input().strip()
    a = input().strip()

    dp = {}

    # initialize first character
    for b in range(2):
        cost = (s[0] != ('(' if b else ')'))
        dp[(1, b)] = cost

    for i in range(1, n):
        ndp = {}

        for (length, mask), cur_cost in dp.items():
            for b in range(2):
                add = (s[i] != ('(' if b else ')'))

                if length < 3:
                    nmask = (mask << 1) | b
                    key = (length + 1, nmask)
                    old = ndp.get(key, INF)
                    if cur_cost + add < old:
                        ndp[key] = cur_cost + add
                else:
                    block = ((mask << 1) | b)

                    start = i - 3

                    if a[start] == '1' and block not in VALID:
                        continue

                    nmask = block & 0b111
                    key = (3, nmask)

                    old = ndp.get(key, INF)
                    if cur_cost + add < old:
                        ndp[key] = cur_cost + add

        dp = ndp

    ans = min(dp.values(), default=INF)
    print(-1 if ans == INF else ans)
```これは実行されます$O(n \cdot 8 \cdot 2)$テスト ケースごとに、合計入力サイズは線形であり、制約に簡単に適合します。
