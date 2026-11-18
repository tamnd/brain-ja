---
title: "CF 2051E - ベストプライス"
description: "アルゴリズム自体は正しいです。 症状は、プログラムが間違った出力ではなく、まったく出力を生成しないことです。 つまり、合格/不合格を判定するロジックにバグはないということです。 それはプログラム構造にあります。"
date: "2026-06-08T08:43:13+07:00"
tags: ["codeforces", "competitive-programming", "binary-search", "brute-force", "data-structures", "greedy", "sortings"]
categories: ["algorithms"]
codeforces_contest: 2051
codeforces_index: "E"
codeforces_contest_name: "Codeforces Round 995 (Div. 3)"
rating: 1600
weight: 2051
solve_time_s: 175
verified: false
draft: false
---

[CF 2051E - ベストプライス](https://codeforces.com/problemset/problem/2051/E)

 **評価:** 1600
 **タグ:** バイナリ検索、ブルート フォース、データ構造、貪欲、ソート
 **解決時間:** 2 分 55 秒
 **確認済み:** いいえ

 ## 解決策
 アルゴリズム自体は正しいです。 症状は、プログラムが**まったく出力を生成しない**ということであり、間違った出力ではありません。 

つまり、合格/不合格を判定するロジックにバグはないということです。 それはプログラム構造にあります。 

非常に一般的な原因は、ソリューションが次のような関数内に記述されていることです。```python
def solve():
    ...
```しかし、関数は決して呼び出されません。 この場合、プログラムは何も読み取らず、何も書き込まず、裁判官には空の出力が表示されます。```
Expected:
0100
0000
1111
10

Actual:
```意図したアルゴリズムを通じてサンプルをトレースします。 

### テストケース 1```
n=4, m=4, k=3
known={1,3,4}
```不明な質問:```
{2}
```未知の質問は 1 つ、つまり 2 です。 

省略された質問が 2 であれば、リストは合格です。```
a = [1,2,3,4]
```結果：```
0100
```### テストケース 2```
n=5, k=3
known={1,3,4}
```不明な質問:```
{2,5}
```少なくとも 2 つの不明な質問があります。 1 つの質問を削除しても、両方の質問を削除することはできません。 

結果：```
0000
```### テストケース 3```
k=n
```すべての質問は既知です。 

結果：```
1111
```### テストケース 4```
n=2
known={2}
```不明な質問:```
{1}
```問題 1 を省略したリストのみが合格です。 

結果：```
10
```したがって、アルゴリズムは問題ありません。 修正方法は、完全な実行可能ソリューションを提供して呼び出しを行うだけです。`solve()`。```python
import sys
input = sys.stdin.readline

def solve():
    t = int(input())
    answers = []

    for _ in range(t):
        n, m, k = map(int, input().split())
        a = list(map(int, input().split()))
        q = list(map(int, input().split()))

        known = set(q)
        missing = n - k

        if missing == 0:
            answers.append("1" * m)
            continue

        if missing >= 2:
            answers.append("0" * m)
            continue

        # Exactly one unknown question.
        unknown = -1
        for x in range(1, n + 1):
            if x not in known:
                unknown = x
                break

        res = []
        for x in a:
            if x == unknown:
                res.append('1')
            else:
                res.append('0')

        answers.append(''.join(res))

    sys.stdout.write("\n".join(answers))

if __name__ == "__main__":
    solve()
```これにより、意図した内容が維持されます$O(n+m)$テストケースごとのアプローチを採用し、すべてのエッジケースを正しく処理します。
