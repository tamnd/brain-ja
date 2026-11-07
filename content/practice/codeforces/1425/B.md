---
title: "CF 1425B - 私たちの学部の青と赤!"
description: "エラーはフロアを計算するロジックにはありません。 表示されている RuntimeError は NameError:solve が定義されていません。 これはスコープの問題です。テスト ハーネスは、実際に定義される前にsolve()を呼び出します。"
date: "2026-06-11T05:57:52+07:00"
tags: ["codeforces", "competitive-programming", "divide-and-conquer", "dp"]
categories: ["algorithms"]
codeforces_contest: 1425
codeforces_index: "B"
codeforces_contest_name: "2020 ICPC, COMPFEST 12, Indonesia Multi-Provincial Contest (Unrated, Online Mirror, ICPC Rules, Teams Preferred)"
rating: 2600
weight: 1425
solve_time_s: 511
verified: false
draft: false
---

[CF 1425B - 私たちの学部の青と赤!](https://codeforces.com/problemset/problem/1425/B)

 **評価:** 2600
 **タグ:** 分割統治、DP
 **解決時間:** 8 分 31 秒
 **確認済み:** いいえ

 ## 解決策
 エラーはフロアを計算するロジックにはありません。 の`RuntimeError`表示されているのは **NameError:`solve`定義されていません`**. This is a **scoping issue**: your testing harness calls `解決する（）`before it is actually defined. Python cannot find a function named`呼び出した時点でグローバル スコープ内でsolve` を実行します。 これは算術演算やエッジケースとは関係ありません。 

これを修正するには、**メインのソリューションをラップする必要があります。`solve()`関数**を作成し、それがテスト ハーネスまたは`__main__`ブロックがそれを呼び出します。 以下は、高速 I/O を使用した完全に機能する Python 3 ソリューションです。```python
import sys
input = sys.stdin.readline

def solve():
    t = int(input())
    for _ in range(t):
        n, x = map(int, input().split())
        if n <= 2:
            # First floor contains apartments 1 and 2
            print(1)
        else:
            # Apartments on floors after the first
            # Subtract first floor (2 apartments), compute zero-based floor index
            # Then add 2 to account for first floor being floor 1
            floor = (n - 3) // x + 2
            print(floor)

if __name__ == "__main__":
    solve()
```### これで問題が解決する理由

 1. 機能`solve()`は **明示的に定義**されました。 
2.`__main__`通話をブロックする`solve()`安全なので、スクリプトを直接実行しても機能します。 
3. ロジック`(n - 3) // x + 2`フロアを正しく計算します。 

- 1 階の 2 つのアパートメントを差し引きます。`n - 3`。 
- 整数除算`x`最初の階からさらに何階が追加されるかを示します。 
- 追加`2`ゼロから始まるインデックスを階数に変換します (1 階の場合は 1、さらに追加)。 

### トレースの例

 入力用`7 3`:

 -`n = 7`、`x = 3`-`n > 2`したがって、フロアを計算します。`(7 - 3) // 3 + 2 = 4 // 3 + 2 = 1 + 2 = 3`- 出力:`3`試合が予想されます。 

入力用`1 5`:

 -`n = 1`→`n <= 2`、1階→出力`1`試合が予想されます。 

この構造により、`run()`テストハーネスが機能するため`solve()`がスコープ内で定義されるようになりました。 次のことを安全に行うことができます。```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    from contextlib import redirect_stdout
    out = io.StringIO()
    with redirect_stdout(out):
        solve()
    return out.getvalue().strip()

assert run("4\n7 3\n1 5\n22 5\n987 13\n") == "3\n1\n5\n77"
```の`NameError`もう発生しなくなります。 アパートの 1 階と正確な倍数を含む、すべてのエッジケース`x`、正しく処理されます。
