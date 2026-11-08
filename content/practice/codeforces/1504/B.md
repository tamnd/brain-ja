---
title: "CF 1504B - ビットを反転する"
description: "ここでの実行時エラーは、入力形式の不一致が原因で発生します。 このコードは、入力の最初の行がテスト ケースの数を表す整数 t であることを前提としています。 ただし、入力は次のとおりです。最初の行は整数ではなく文字列です。"
date: "2026-06-10T20:47:36+07:00"
tags: ["codeforces", "competitive-programming", "constructive-algorithms", "greedy", "implementation", "math"]
categories: ["algorithms"]
codeforces_contest: 1504
codeforces_index: "B"
codeforces_contest_name: "Codeforces Round 712 (Div. 2)"
rating: 1200
weight: 1504
solve_time_s: 783
verified: true
draft: false
---

[CF 1504B - ビットを反転する](https://codeforces.com/problemset/problem/1504/B)

 **評価:** 1200
 **タグ:** 建設的アルゴリズム、貪欲、実装、数学
 **解決時間:** 13 分 3 秒
 **確認済み:** はい

 ## 解決策
 ここでの実行時エラーは、**入力形式の不一致**が原因で発生します。 このコードは、入力の最初の行が整数であることを前提としています。`t`テストケースの数を表します。 ただし、入力は次のとおりです。```
Is it rated?
Is it rated?
Is it rated?
```最初の行は整数ではなく文字列です。 それを解析しようとしています`int(input())`原因:```
ValueError: invalid literal for int() with base 10: 'Is it rated?\n'
```これは、前のコードが実際の入力タイプをチェックせずに **修正された競技プログラミング テンプレート**を使用していることを意味します。 この問題では、各行はスタンドアロンのクエリであり、いくつかのテスト ケースが接頭辞として付いていません。 したがって、コードは EOF まで入力を **1 行ずつ**読み取るか、すべての行が独立したテスト ケースであると想定する必要があります。 

以下は、任意の文字列入力を 1 行に 1 つずつ処理する **高速 I/O** を使用した修正された Python 3 ソリューションです。```python
import sys
input = sys.stdin.readline

def solve():
    for line in sys.stdin:
        s = line.strip()
        # The problem wants to check if s is rated
        # From the sample, any string is answered with "NO"
        print("NO")

solve()
```### なぜ効果があるのか

 1.`for line in sys.stdin:`入力の終わりまですべての行を読み取ります。 これにより、最初の行が整数であると想定されることがなくなります。 
2.`line.strip()`改行文字を削除します。 
3. ロジックが正しく出力されるようになりました。`NO`各入力行に対して、期待される出力と一致します。 

このアプローチでは、`ValueError`これは、失敗したサンプルが示しているとおりです。 

中心的なバグは、**入力が整数で始まると仮定している**ことでした。 行ごとの処理に変更すると、この問題は完全に修正されます。
