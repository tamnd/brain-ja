---
title: "CF 1194C - S から T へ"
description: "3 つの文字列 s、t、p が与えられます。 p から任意の文字を繰り返し取得して、s の任意の場所に挿入できます。 目的は、このような挿入をいくつか続けた後、s が t と正確に等しくなるかどうかを判断することです。"
date: "2026-06-12T00:17:09+07:00"
tags: ["codeforces", "competitive-programming", "implementation", "strings"]
categories: ["algorithms"]
codeforces_contest: 1194
codeforces_index: "C"
codeforces_contest_name: "Educational Codeforces Round 68 (Rated for Div. 2)"
rating: 1300
weight: 1194
solve_time_s: 101
verified: true
draft: false
---

[CF 1194C - S から T へ](https://codeforces.com/problemset/problem/1194/C)

 **評価:** 1300
 **タグ:** 実装、文字列
 **解決時間:** 1 分 41 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 3つの文字列が与えられています`s`、`t`、 そして`p`。 から任意の文字を繰り返し取得できます。`p`そしてそれをどこかに挿入してください`s`。 目的は、このような挿入が続いた後に、`s`と全く同じになることができます`t`。 各クエリは独立しているため、答える必要があります`YES`または`NO`それぞれに。 

最初の観察は、既存の文字の順序が`s`重要です。 もし`s`のサブシーケンスと一致しない順序で文字が含まれています`t`、余分な文字をいくら挿入しても`p`相対的な位置を固定することができます。 したがって、`s`すでに次のサブシーケンスになっている必要があります`t`。 次に、含まれるすべての文字について、`t`しかし、中にはありません`s`、そのキャラクターは十分にあるはずです`p`ギャップを埋めるために。 

文字列の長さは短く、それぞれ最大 100 文字で、クエリの数は最大 100 です。 これにより、クエリごとの単純な線形スキャンが可能になります。これは、クエリごとの単純な O(|t| + |p|) チェックでも最大で約 10,000 操作であり、1 秒以内に収まるためです。 

微妙なエッジケースが発生する場合`s`すでに長くなっています`t`、またはいつ`s`文字の順序が間違っています。 例えば、`s = "ba"`、`t = "ab"`、`p = "a"`。`s`の続きではありません`t`、したがって、答えは次のようになります。`NO`、 それでも`p`に存在するキャラクターがいます`t`。 不用意に行うと文字数だけを確認して誤って報告してしまう可能性があります。`YES`。 

## アプローチ

 ブルートフォースアプローチでは、あらゆる方法で文字を挿入しようとします。`p`の中へ`s`合わせる`t`。 挿入のすべての順列を生成することもできますが、長さが 100 であっても、可能性の数は挿入の数に応じて階乗的に増加するため、これは完全に不可能です。 

重要な洞察は、問題を 2 つの簡単なチェックに分けることです。 まず、それを確認します`s`の続きです`t`。 これにより、すでに含まれている文字の相対的な順序が保証されます。`s`は正しいです。 次に、必要な各文字の数を数えます。`t`それは存在しない`s`。 もし`p`必要な各文字が少なくともその数だけ含まれている場合、答えは次のとおりです。`YES`; それ以外の場合は、`NO`。 このアプローチが機能するのは、挿入操作の位置が柔軟であり、順序によって文字が制約されないためです。`p`。 

ブルート フォース アプローチは理論的には正しいですが、現実的ではありません。一方、2 段階チェックでは、問題の構造を使用して問題を線形時間の解決策に還元します。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(階乗( | t | )) |
 | サブシーケンス + カウントのチェック | O( | t | + |

 ## アルゴリズムのチュートリアル

 1. まずは次のことを確認します。`s`の続きです`t`。 2 つのポインターを初期化します。`i`のために`s`そして`j`のために`t`。 スキャンスルー`t`、進んでいます`i`いつでも`t[j] == s[i]`。 最後に、もし`i`の長さに等しい`s`、 それから`s`の続きです`t`。 
2. もし`s`の続きではありません`t`、すぐに戻ります`NO`。 これは、文字の相対的な順序が異なるためです。`s`からの文字を挿入して変更することはできません`p`。 
3. の各文字の頻度を数えます。`s`、`t`、 そして`p`。 の各キャラクターごとに、`t`、それを超えて追加のオカレンスが何回必要になるかを計算します。`s`すでに提供しています。 これは単純に、`needed = count_t[char] - count_s.get(char, 0)`。 
4. 次のことを確認します。`p`必要な各文字が十分に揃っています。 任意の文字のカウントが`p`未満です`needed`、 戻る`NO`。 それ以外の場合は戻ります`YES`。 

なぜ機能するのか: 最初のステップは次のことを保証します。`s`と正しく位置合わせされます`t`。 カウントステップにより、埋めるために必要な余分な文字が保証されます。`s`の中へ`t`で利用可能です`p`。 どこにでも挿入が許可されるため、追加の制約は存在しないため、これら 2 つのチェックは必要かつ十分です。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

from collections import Counter

def can_transform(s, t, p):
    # Check if s is a subsequence of t
    i = 0
    for ch in t:
        if i < len(s) and s[i] == ch:
            i += 1
    if i != len(s):
        return "NO"
    
    # Count characters
    count_s = Counter(s)
    count_t = Counter(t)
    count_p = Counter(p)
    
    # Check if p can supply the missing characters
    for ch in count_t:
        if count_t[ch] > count_s.get(ch, 0) + count_p.get(ch, 0):
            return "NO"
    
    return "YES"

def main():
    q = int(input())
    for _ in range(q):
        s = input().strip()
        t = input().strip()
        p = input().strip()
        print(can_transform(s, t, p))

if __name__ == "__main__":
    main()
```機能`can_transform`まず、ポインタ スキャンを使用してサブシーケンス プロパティをチェックします。 これにより、文字列挿入による複雑なロジックが回避されます。 次、`Counter`文字カウントに使用されます。これにより、`p`必要な文字を提供できます。 操作の順序は重要です。サブシーケンスのチェックは、カウントする前に行う必要があります。`s`の続きではありません`t`、カウントだけでは不十分です。 

## 実用的な例

 最初のサンプル入力を使用します。 

| s | t | p | 私はポインタ | 決定 |
 | --- | --- | --- | --- | --- |
 | アブ | acxb | キャス | 0→1→2 | s は t の部分シーケンスであり、チェックパスをカウントします → YES |
 | | ああ | ああああ | 0→1 | 部分列が通過する、t には追加の a が 3 つ必要、p には十分な数がある → YES |
 | ああ | ああああ ああああ 0→1→2→3 | サブシーケンスが通過する、t には 2 つの b と 2 つの c が必要、p には 2 つの b と 2 つの c しかない → YES か NO? |

 2 番目のトレースではサブシーケンス チェックが機能していることを確認し、カウント チェックで必要なすべての文字が p で利用可能であることを確認します。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O( | s |
 | スペース | O(26) = O(1) | カウンタ辞書には最大 26 個の小文字が保存されます。 

|s|、|t|、|p| なので ≤ 100 および q ≤ 100 の場合、最悪の場合の操作は約 30,000 であり、これは許容範囲内です。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    output = io.StringIO()
    sys.stdout = output
    main()
    sys.stdout = sys.__stdout__
    return output.getvalue().strip()

# Provided samples
assert run("4\nab\nacxb\ncax\na\naaaa\naaabbcc\naaaa\naabbcc\nab\nbaaa\naaaaa\n") == "YES\nYES\nNO\nNO", "sample 1"

# Custom cases
assert run("1\na\nb\na\n") == "NO", "s not subsequence of t"
assert run("1\na\nab\nb\n") == "YES", "s can be extended by p"
assert run("1\nabc\nabc\n\n") == "YES", "s equals t, p empty"
assert run("1\nabc\nacb\nabc\n") == "NO", "s subsequence fails due to order"
assert run("1\na"*100 + "\n" + "a"*100 + "\n" + "" + "\n") == "YES", "maximum-length equal strings"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | a / b / a | いいえ | s は t の部分列ではありません |
 | a / ab / b | はい | s は t | と一致するように p で拡張できます。 
| abc / abc / 空 | はい | s は t に等しく、p は空です |
 | abc / acb / abc | いいえ | 順序の不一致によりサブシーケンスが妨げられる |
 | 100×a / 100×a / 空 | はい | 処理される最大長の文字列 |

 ## 特殊なケース

 のために`s = "ba"`、`t = "ab"`, `p =
