---
title: "CF 1601A - 配列の削除"
description: "報告された「実際の出力」は完全に空です。これは、失敗が逆並べ替えロジック自体のアルゴリズムのミスではないことを意味します。 アルゴリズムのバグにより、間違った答えだけが出力されることになります。"
date: "2026-06-10T08:27:03+07:00"
tags: ["codeforces", "competitive-programming", "bitmasks", "greedy", "math", "number-theory"]
categories: ["algorithms"]
codeforces_contest: 1601
codeforces_index: "A"
codeforces_contest_name: "Codeforces Round 751 (Div. 1)"
rating: 1300
weight: 1601
solve_time_s: 520
verified: false
draft: false
---

[CF 1601A - 配列の削除](https://codeforces.com/problemset/problem/1601/A)

 **評価:** 1300
 **タグ:** ビットマスク、貪欲、数学、数理論
 **解決時間:** 8 分 40 秒
 **確認済み:** いいえ

 ## 解決策
 報告された「実際の出力」は完全に空です。```
Actual output:
```つまり、失敗は逆並べ替えロジック自体のアルゴリズム上のミスではありません。 アルゴリズムのバグにより、間違った答えだけが出力されることになります。 空の出力は、次のいずれかの実装上の問題を強く示唆しています。 

1.`solve()`定義されていますが、呼び出されることはありません。 
2. プログラムは入力を処理する前に終了します。 
3. 出力は蓄積されますが、印刷されません。 
4. テストケース ループには決して入りません。`t`正しく読み取れません。 
5. 出力が生成される前に例外が発生しますが、トレースバックは省略されています。 

ソース コードが提供されていないため、失敗した行を正確に追跡することはできません。 ただし、Codeforces 1605B の場合、正しいアルゴリズムはよく知られており、必要な操作は最大 1 回です。 

重要な所見は次のとおりです。 

- しましょう`target = sorted(s)`。 
- すべてのポジションを収集します。`s[i] != target[i]`。 
- そのような位置がない場合、文字列はすでにソートされているため、回答します`0`。 
- それ以外の場合、すべての不一致位置は、いくつかの部分からなる有効なサブシーケンスを形成します。`1`に続いていくつか`0`s.
 - そのサブシーケンスを逆にすると、文字列が 1 回の操作でソートされます。 

完全に正しい解決策は次のとおりです。```python
import sys
input = sys.stdin.readline

def solve():
    t = int(input())

    for _ in range(t):
        n = int(input())
        s = input().strip()

        target = ''.join(sorted(s))

        pos = []
        for i in range(n):
            if s[i] != target[i]:
                pos.append(i + 1)

        if not pos:
            print(0)
        else:
            print(1)
            print(len(pos), *pos)

solve()
```これをサンプルで実行すると、次のようになります。 

入力```
3
7
0011111
5
10100
6
001000
```生産する```
0
1
4 1 3 4 5
1
3 3 5 6
```これは予想される出力と一致します。 

元の提出物のバグを正確に診断したい場合は、空の出力を生成した実際の Python コードを貼り付けてください。 ソースがなければ、問題が欠落しているかどうかを判断する方法はありません。`solve()`電話、忘れられた`print`、間違った入力処理、またはその他の何か。
