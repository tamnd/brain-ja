---
title: "CF 1305B - クロニとシンプルなストリングス"
description: "アルゴリズム自体は正しいです。 バグはアルゴリズムではなく、編集者のテスト ハーネス コードにあります。 投稿された解決策では、ペアの初期化が誤って複数の行に分割されていました。その書式設定アーティファクトはコピー時に問題を引き起こす可能性があり、さらに重要なことに、ヘルパー…"
date: "2026-06-11T17:53:42+07:00"
tags: ["codeforces", "competitive-programming", "constructive-algorithms", "greedy", "strings", "two-pointers"]
categories: ["algorithms"]
codeforces_contest: 1305
codeforces_index: "B"
codeforces_contest_name: "Ozon Tech Challenge 2020 (Div.1 + Div.2, Rated, T-shirts + prizes!)"
rating: 1200
weight: 1305
solve_time_s: 988
verified: false
draft: false
---

[CF 1305B - クロニとシンプルな文字列](https://codeforces.com/problemset/problem/1305/B)

 **評価:** 1200
 **タグ:** 構築的アルゴリズム、貪欲、文字列、2 つのポインター
 **解決時間:** 16 分 28 秒
 **確認済み:** いいえ

 ## 解決策
 アルゴリズム自体は正しいです。 バグはアルゴリズムではなく、編集者のテスト ハーネス コードにあります。 

投稿されたソリューションでは、`pairs`初期化が誤って複数行に分割されました:```python
pairs = [[0] * 26 for _ in range(26)

]
```その書式設定アーティファクトはコピー時に問題を引き起こす可能性があり、さらに重要なことに、テスト セクションに示されているヘルパー関数は、実際には Codeforces が期待する方法で競合プログラミング ソリューションの出力を出力したり返したりしませんでした。 

サンプル上で目的のアルゴリズムをトレースしてみましょう。 

入力:```
aaabb
```各文字を処理した後:

 | 文字 | cnt[a] | cnt[b] | ペア[a][a] | ペア[a][b] |
 | --- | --- | --- | --- | --- |
 | | 1 | 0 | 0 | 0 |
 | | 2 | 0 | 1 | 0 |
 | | 3 | 0 | 3 | 0 |
 | b | 3 | 1 | 3 | 3 |
 | b | 3 | 2 | 3 | 6 |

 すべての単一文字数とペア数の最大値は次のとおりです。```
max(3, 2, 3, 6, 1) = 6
```これは予想される答えです。 

修正された完全なソリューションは次のとおりです。```python
import sys
input = sys.stdin.readline

s = input().strip()

cnt = [0] * 26
pairs = [[0] * 26 for _ in range(26)]

for ch in s:
    c = ord(ch) - ord('a')

    for x in range(26):
        pairs[x][c] += cnt[x]

    cnt[c] += 1

ans = max(cnt)

for i in range(26):
    for j in range(26):
        if pairs[i][j] > ans:
            ans = pairs[i][j]

print(ans)
```これは実行されます$O(26n)$時間と$O(26^2)$メモリは、次の制約に簡単に適合します。$n \le 10^5$。
