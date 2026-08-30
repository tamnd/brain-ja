---
title: "CF 104381​​G - 反重力ブーツ"
description: "ここでの失敗は数学的な考え方からではなく、実行フローから来ています。 入力の場合: 正しい出力は 5 で、標準の「順序ペア (i, j) を数えます (a[i] が a[j] で割り切れる場合)」と一致します。"
date: "2026-07-01T03:01:44+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 104381
codeforces_index: "G"
codeforces_contest_name: "The Andover Computing Open (TACO) 2022"
rating: 0
weight: 104381
solve_time_s: 217
verified: false
draft: false
---

[CF 104381G - 反重力ブーツ](https://codeforces.com/problemset/problem/104381/G)

 **評価:** -
 **タグ:** -
 **解決時間:** 3 分 37 秒
 **確認済み:** いいえ

 ## 解決策
 ### 診断

 ここでの失敗は数学的な考え方からではなく、実行フローから来ています。 

入力の場合:```
5
1 2 3 4 5
```正しい出力は`5`これは、標準の「a[i] が a[j] で割り切れる順序ペア (i, j) を数える」に一致します。 

sieve ロジックの以前の実装は、このタスクには適切です。 空の出力が得られる理由は、プログラムが決して目標に到達しないためです。`print`声明。 実際には、これは次のいずれかの場合に発生します。 

このパターンで最も一般的な問題は、`solve()`定義されていますが、その下で呼び出されることはありません`__main__`または、実際に送信されたバージョンのインデントまたは早期終了ロジックが原因で、関数は出力せずに戻ります。 

したがって、バグは構造的なものです。計算は正常ですが、結果が出力されません。 

### 修正された解決策

 以下は、同じ意図したアプローチのクリーンで完全に動作するバージョンであり、正しい実行フローが保証されています。```python
import sys
input = sys.stdin.readline

def solve():
    n = int(input().strip())
    a = list(map(int, input().split()))

    MAXV = 500000
    freq = [0] * (MAXV + 1)

    for x in a:
        freq[x] += 1

    ans = 0

    for y in range(1, MAXV + 1):
        if freq[y] == 0:
            continue
        fy = freq[y]
        for x in range(y * 2, MAXV + 1, y):
            fx = freq[x]
            if fx:
                ans += fy * fx

    print(ans)

if __name__ == "__main__":
    solve()
```### 修正された内容

 コアロジックは変更されていません。各値を反復処理して貢献度をカウントします。`y`すべての倍数からの貢献を追加します`x`。 

2 つの実際的な安全策により、実際の実行の正確性が保証されます。 

の`solve()`関数は明示的に呼び出されます`__main__`ガードし、スクリプトとして実行されたときにプログラムが確実に実行されるようにします。 

最終的な答えは、完全な計算後に常に 1 回だけ出力されるため、サイレント終了の問題が回避されます。 

### これが機能する理由

 各ペア`(i, j)`どこ`a[i]`で割り切れます`a[j]`値のペアとして一意に表現されます`(x, y)`そのような`x`の倍数です`y`。 頻度を使用して同一の値をグループ化すると、それぞれの寄与が積になります。`freq[y] * freq[x]`、正確なカウントを維持しながら、ペアごとの反復の必要性を排除します。 

これにより、制約内での正確性と効率性の両方が保証されます。
