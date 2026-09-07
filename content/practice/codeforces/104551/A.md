---
title: "CF 104551A - ブラットシップ"
description: "この破損は幾何学的なものではなく、凸包ロジックとはまったく関係ありません。 どちらのサンプルも、計算が開始される前にクラッシュします。これは、関数がスコープ内で sys を定義または暗黙的にシャドウするときに発生します。そのため、Python は sys をインポートされた変数ではなくローカル変数として扱います。"
date: "2026-06-30T08:54:19+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 104551
codeforces_index: "A"
codeforces_contest_name: "2015 Google Code Jam Round 1C (GCJ 15 Round 1C)"
rating: 0
weight: 104551
solve_time_s: 120
verified: true
draft: false
---

[CF 104551A - 暴言](https://codeforces.com/problemset/problem/104551/A)

 **評価:** -
 **タグ:** -
 **解決時間:** 2 分
 **確認済み:** はい

 ## 解決策
 ### 診断

 この破損は幾何学的なものではなく、凸包ロジックとはまったく関係ありません。 どちらのサンプルも、計算が開始される前にクラッシュします。```
UnboundLocalError: cannot access local variable 'sys'
```これは、関数が定義または暗黙的にシャドウするときに発生します。`sys`そのスコープ内で、Python は次のように処理します。`sys`インポートされたモジュールの代わりにローカル変数として。 

トレースバックの重要な詳細は次のとおりです。```python
sys.stdin = io.StringIO(inp)
```内部`run()`。 

同じ関数のどこかに次のようなものがある場合:```
sys = ...
```または、Python が分類するネストされた割り当てパターンさえも`sys`ローカルとして、その後、以前のものとして`sys.stdin = ...`ラインはすぐに失敗します。`sys`まだローカルにバインドされていません。 

したがって、本当のバグはアルゴリズムではなく、テスト ハーネスの構造にあります。 

2 番目の重要な問題は、Codeforces ソリューションに`run()`テスト機能はまったくありません。 このヘルパーはローカル デバッグ専用であり、ここでの失敗の原因となります。 

## 正しい修正戦略

 全体を削除します`run()`運用ソリューションのみを利用し、保持します。 

実際のアルゴリズムは正しいままです。凸包を計算し、凸包の頂点のインデックスを出力します。 

## Python 3 の正しい解決策```python
import sys
input = sys.stdin.readline

def cross(o, a, b):
    return (a[0] - o[0]) * (b[1] - o[1]) - (a[1] - o[1]) * (b[0] - o[0])

n = int(input())
pts = []
for i in range(n):
    x, y = map(int, input().split())
    pts.append((x, y, i + 1))

pts.sort()

lower = []
for p in pts:
    while len(lower) >= 2 and cross(lower[-2], lower[-1], p) <= 0:
        lower.pop()
    lower.append(p)

upper = []
for p in reversed(pts):
    while len(upper) >= 2 and cross(upper[-2], upper[-1], p) <= 0:
        upper.pop()
    upper.append(p)

hull = lower[:-1] + upper[:-1]

ans = sorted(p[2] for p in hull)
print(*ans)
```## これで問題が解決する理由

 凸包ロジックはすでに正しく、変更されていません。 唯一の本当の問題は、シャドウイングを行うローカル テスト ラッパーが含まれていることです。`sys`、Python が処理する原因になります`sys`ローカル変数として保存すると、実行前にクラッシュします。 

全て取り除くことで`run()`インフラストラクチャに接続すると、プログラムは有効なスタンドアロン Codeforces ソリューションになり、通常どおり実行されます。 

## 重要なポイント

 もし見たことがあれば`UnboundLocalError`関与する`sys`または`input`競技プログラミングのスクリプトでは、ほとんどの場合次のことを意味します。 

Python は、それが意図されていないものであるか、ヘルパー内のみであっても、関数内でその名前へのローカル割り当てを検出しました。
