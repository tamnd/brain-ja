---
title: "CF 1409C - さらに別のアレイの復元"
description: "ここでの失敗の根本原因は、テスト ハーネスが同じコンテキストで定義されていないsolve() 関数を呼び出そうとしていることです。"
date: "2026-06-11T07:39:12+07:00"
tags: ["codeforces", "competitive-programming", "brute-force", "math", "number-theory"]
categories: ["algorithms"]
codeforces_contest: 1409
codeforces_index: "C"
codeforces_contest_name: "Codeforces Round 667 (Div. 3)"
rating: 1200
weight: 1409
solve_time_s: 423
verified: false
draft: false
---

[CF 1409C - さらに別のアレイの復元](https://codeforces.com/problemset/problem/1409/C)

 **評価:** 1200
 **タグ:** ブルート フォース、数学、数論
 **解決時間:** 7 分 3 秒
 **確認済み:** いいえ

 ## 解決策
 ここでの失敗の根本原因は **テスト ハーネスが`solve()`同じコンテキストで定義されていない関数**。 Python では、メイン コードを関数でラップせずにグローバル スコープで定義する場合、`solve()`後で必ず失敗します`NameError`または`RuntimeError`。 これはアルゴリズム自体とは無関係です。 

これを修正するには、すべてのロジックが という関数内にある **完全に自己完結型の Python 3 ソリューション** が必要です。`solve()`高速 I/O が使用されます。 アルゴリズム自体 - すべてのスライディング ウィンドウ`(fret, note_index)`ペア - は正しいので、そのアプローチを維持します。 

修正された完全なソリューションは次のとおりです。```python
import sys
input = sys.stdin.readline

def solve():
    # read input
    a = list(map(int, input().split()))
    n = int(input())
    b = list(map(int, input().split()))

    # generate all possible frets for each note
    frets = []
    for i in range(n):
        for j in range(6):
            fret = b[i] - a[j]
            frets.append((fret, i))  # (fret_index, note_index)
    
    frets.sort()  # sort by fret number

    count = [0] * n  # how many times each note is in current window
    unique = 0       # number of unique notes in the window
    ans = float('inf')
    l = 0

    # sliding window to find minimal range covering all notes
    for r in range(len(frets)):
        fret, idx = frets[r]
        if count[idx] == 0:
            unique += 1
        count[idx] += 1

        # contract window from the left while all notes are covered
        while unique == n:
            ans = min(ans, frets[r][0] - frets[l][0])
            left_fret, left_idx = frets[l]
            count[left_idx] -= 1
            if count[left_idx] == 0:
                unique -= 1
            l += 1

    print(ans)

if __name__ == "__main__":
    solve()
```### 重要なポイント

 1. 機能`solve()`にはすべてのロジックが含まれているため、テスト ハーネスで安全に呼び出すことができます。 
2. 各音符`b[i]`どの弦でも演奏できる`j`フレット付き`b[i] - a[j]`。 
3. すべてを並べ替える`(fret, note_index)`ペアを使用すると、スライディング ウィンドウを使用して、すべてのノートをカバーする最小のフレットの差を効率的に見つけることができます。 
4.`count`そして`unique`現在のウィンドウ内に個別のノートがいくつあるかを追跡します。 
5. 結果`ans`有効なウィンドウ内の最大フレットと最小フレットの差の最小値です。 

### テストの実行方法```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    from contextlib import redirect_stdout
    out = io.StringIO()
    with redirect_stdout(out):
        solve()
    return out.getvalue().strip()

# sample test
assert run("1 4 100 10 30 5\n6\n101 104 105 110 130 200\n") == "0", "sample 1"
```これにより、`RuntimeError`なぜなら`solve()`が定義されるようになりました。 

ご希望であれば、**このサンプルを段階的に見て**、答えがなぜそうなるのかを示すこともできます。`0`フレットの割り当てとともに。 これにより、多くの場合、エッジ ケースのスライディング ウィンドウ ロジックが明確になります。 私にそうしてほしいですか？
