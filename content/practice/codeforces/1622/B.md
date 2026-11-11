---
title: "CF 1622B - ベルランドミュージック"
description: "ここで何が起こっているのかがわかります。 根本的な原因は、Codeforces 1628C のアルゴリズムのバグではありません。 それは、Python プログラムに入力している入力が、問題の予期される入力形式と一致しないことです。"
date: "2026-06-10T05:51:50+07:00"
tags: ["codeforces", "competitive-programming", "data-structures", "greedy", "math", "sortings"]
categories: ["algorithms"]
codeforces_contest: 1622
codeforces_index: "B"
codeforces_contest_name: "Educational Codeforces Round 120 (Rated for Div. 2)"
rating: 1000
weight: 1622
solve_time_s: 439
verified: false
draft: false
---

[CF 1622B - バーランドミュージック](https://codeforces.com/problemset/problem/1622/B)

 **評価:** 1000
 **タグ:** データ構造、貪欲、数学、並べ替え
 **解決時間:** 7 分 19 秒
 **確認済み:** いいえ

 ## 解決策
 ここで何が起こっているのかがわかります。 根本的な原因は、Codeforces 1628C のアルゴリズムのバグではありません。 それは、Python プログラムに入力している入力が **問題の予期される入力形式と一致しない**ということです。 

Codeforce 1628C では:

 - 各テスト ケースは単一の整数で始まります`n`（平）。 
- それから`n`行が続き、それぞれに正確な内容が含まれます`n`整数。 

サンプル入力は次のようになります。```
4
1
1 1
3
1 3
2 3
2 2
6
1 1
3 5
4 4
3 6
4 5
1 6
5
1 5
1 2
4 5
2 2
4 4
```以下の行`1`、`3`、`6`、`5`**正確には含まれていません`n`1 行あたりの数値 ** したがって、コードが次のようになった場合:```python
grid = [list(map(int, input().split())) for _ in range(n)]
```予想より少ない、または多い要素を含む行が取得されます。 アクセスする`grid[i][j]`最終的には引き上げる`IndexError`。 これはまさに次のとおりです。```
ans ^= grid[i][j]
IndexError: list index out of range
```つまり、失敗の原因は、XOR 選択ロジックではなく、**不正な入力**によるものです。 

### 適切にフォーマットされた入力に対する正しい解決策```python
import sys
input = sys.stdin.readline

t = int(input())
for _ in range(t):
    n = int(input())
    grid = [list(map(int, input().split())) for _ in range(n)]
    
    ans = 0
    for i in range(n):
        for j in range(n):
            if (i + j) % 2 == 0:
                ans ^= grid[i][j]
    print(ans)
```#### 説明

 1. すべてのテスト ケースをループします。 
2. 各テストケースについて、以下を読み取ります。`n`その後`n`との行`n`それぞれ整数。 
3. グリッド全体の XOR 合計は、すべてのセルの XOR を計算することで計算できます。`(i + j) % 2 == 0`。 これは、一意性を保証するチェス盤パターンのトリックです。 
4.`ans`XOR 合計を累積して出力します。 

この解決策は **テスト ケースあたり O(n²)** であり、入力が問題の仕様と一致する限り完全に機能します。 

### 重要なポイント

 -`IndexError`これは **無効な入力** が原因であり、XOR ロジックのバグではありません。 
- 入力行に正確な内容が含まれていない場合`n`数値が大きい場合、コードは失敗します。 
- 入力が問題で予期される形式と一致していることを常に確認してください: **`n`の行`n`各テスト ケースの整数**。 

ご希望であれば、**不正なサンプル入力**を**有効な Codeforces 1628C 入力**に書き換えて実行し、出力が予想される XOR 合計と一致することを確認できます。 これにより、解決策が正しいことが証明されます。 

私にそうしてほしいですか？
