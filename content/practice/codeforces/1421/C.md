---
title: "CF 1421C - 回文計算機"
description: "あなたが示した失敗は、実際にはアルゴリズムの失敗ではありません。 トレースバック: は、テスト ハーネスが実行を試みましたが、送信されたファイルにsolve という名前の関数が含まれていなかったことを意味します。 これは入力が処理される前に発生するため、abac でアルゴリズムを追跡する方法はありません。"
date: "2026-06-11T06:34:21+07:00"
tags: ["codeforces", "competitive-programming", "constructive-algorithms", "strings"]
categories: ["algorithms"]
codeforces_contest: 1421
codeforces_index: "C"
codeforces_contest_name: "Codeforces Round 676 (Div. 2)"
rating: 1400
weight: 1421
solve_time_s: 501
verified: true
draft: false
---

[CF 1421C - 回文](https://codeforces.com/problemset/problem/1421/C)

 **評価:** 1400
 **タグ:** 構築的アルゴリズム、文字列
 **解決時間:** 8 分 21 秒
 **確認済み:** はい

 ## 解決策
 あなたが示した失敗は、実際にはアルゴリズムの失敗ではありません。 

トレースバック:```
NameError: name 'solve' is not defined
```テスト ハーネスが実行しようとしたことを意味します。```
solve()
```しかし、送信されたファイルには という名前の関数が含まれていませんでした。`solve`。 

これは入力が処理される前に発生するため、アルゴリズムを追跡する方法はありません。`abac`。 プログラムは回文を構築するロジックに到達することはありません。 

Codeforces 1421C の場合、標準的な構築的なソリューションでは、すべての文字列に対して 3 つの操作が使用されます。```
L 2
R 2
R (2*n-1)
```どこ`n`文字列の元の長さです。 

その正しさは、公式社説で使用されているよく知られた構造に由来しています。 これらの操作の後、結果の文字列は元の内容に関係なく回文になります。 

完全に受け入れられた Python 3 ソリューションは次のとおりです。```python
import sys
input = sys.stdin.readline

def solve():
    s = input().strip()
    n = len(s)

    print(3)
    print("L 2")
    print("R 2")
    print("R", 2 * n - 1)

if __name__ == "__main__":
    solve()
```サンプル入力の場合:```
abac
```プログラムは次のように出力します。```
3
L 2
R 2
R 7
```それは有効な答えです。 この問題は、特定の操作ではなく、最大 30 個の操作の有効なシーケンスを受け入れます。 

失敗した送信のバグは単純に次のとおりです。`solve()`が見つからないか名前が変更されていたため、構築的アルゴリズムを実行する前にプログラムがクラッシュしました。 上記の修正バージョンでは、必要な内容が復元されます。`solve()`機能を備え、標準で認められた構造を使用します。
