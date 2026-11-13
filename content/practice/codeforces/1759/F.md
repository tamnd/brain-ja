---
title: "CF 1759F - 可能なすべての桁"
description: "n 個のクエストがあります。 クエスト i は完了するたびに a[i] コインを与えます。 d 日間は、1 日に最大 1 つのクエストを実行できます。 クエストを実行した後、同じクエストを再度実行するまで k 日間待つ必要があります。"
date: "2026-06-09T14:34:49+07:00"
tags: ["codeforces", "competitive-programming", "binary-search", "data-structures", "greedy", "math", "number-theory"]
categories: ["algorithms"]
codeforces_contest: 1759
codeforces_index: "F"
codeforces_contest_name: "Codeforces Round  834 (Div. 3)"
rating: 1800
weight: 1759
solve_time_s: 362
verified: false
draft: false
---

[CF 1759F - 可能なすべての桁](https://codeforces.com/problemset/problem/1759/F)

 **評価:** 1800
 **タグ:** 二分探索、データ構造、貪欲、数学、数論
 **解決時間:** 6 分 2 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 私たちは持っています`n`クエスト。 クエスト`i`与える`a[i]`完了するたびにコインを獲得します。 

その間`d`1 日に実行できるクエストは 1 つだけです。 クエストを完了したら、待つ必要があります`k`同じクエストを再び行う数日前に。 

最大のものが必要です`k`少なくとも稼ぐことが可能である`c`中のコイン`d`日々。 

たとえさえあれば`k = 0`到達できない`c`、答えは`Impossible`。 

任意に大きい場合`k`価値観が機能する、その答えは`Infinity`。 

すべての合計`n`そしてすべて`d`せいぜい`2·10^5`、だから、`O(n log n + d log d)`テストケースごとのスタイルのソリューションは受け入れられます。 多くの候補値を徹底的にシミュレーション`k`そうではありません。 

## 重要な観察

 報酬を降順に並べ替えます。 

固定クールダウンの場合`k`、最適な戦略は貪欲です。 

任意のブロック中に、`k+1`連続してクエストを2回出現させることはできません。 したがって、可能な限り最良のブロックは最大のブロックで構成されます。`k+1`クエストの値。 

させて```
cycle = k + 1
```あるとします。```
full = d // cycle
rem  = d % cycle
```すべての完全なサイクルは最大の合計に寄与します。`cycle`報酬。 

残り`rem`日数は最大の合計に寄与します`rem`報酬。 

もし`cycle > n`、これ以上明確なクエストがないため、サイクル内で利用可能なすべてのクエストを一度使用するだけです。 

これにより、固定のコインで最大獲得可能なコインを計算する効率的な方法が得られます。`k`。 

## 二分探索が機能する理由

 として`k`増加すると制限が厳しくなります。 

達成可能な最大コインは、`k`。 

したがって：```
can(k) = achievable_coins(k) >= c
```単調な述語です。 

有効な最大値を二分探索できます`k`。 

## 無限大の検出

 もし`k`は任意の規模ですが、各クエストは全期間中最大 1 回まで有効に使用できます。 

その場合の最高の収入は、```
sum of largest min(n, d) rewards
```この値がすでに少なくとも`c`、その後、十分に大きいすべての`k`動作します。 

答え：```
Infinity
```## 検出不可能

 いつ`k = 0`、毎日最高のクエストを繰り返す可能性があります。 

可能な最大収入は次のようになります```
d * max(a)
```これがまだ下にある場合`c`、目標を達成することは不可能です。 

答え：```
Impossible
```## アルゴリズム

 1. 報酬を降順に並べ替えます。 
2. プレフィックスの合計を作成します。 
3. 無限大にチェックを入れます。 
4.「不可能」にチェックを入れます。 
5. 有効な最大値を二分探索する`k`。 
6. その値を出力します。 

## 正確さのスケッチ

 固定の場合`k`、どのクエストも、長さのブロックごとに最大 1 回出現できます。`k+1`。 

したがって、最適なスケジュールは、そのような各ブロック内で利用可能な最大の報酬を繰り返し取得します。 報酬は独立しており、決して減少しないため、最大の報酬を選択すると、各サイクルが独立して最大化されます。 

次のように計算された結果の値`can(k)`そのクールダウンの真の最適値に等しい。 

増えてからは`k`実行可能なスケジュールのみを削除し、`can(k)`モノトーンです。 したがって、二分探索は実現可能な最大値を見つけます。`k`。 

無限チェックは、クエストが決して繰り返されない制限を評価します。 Impossible チェックでは、反復が制限されない反対の極端な結果が評価されます。 これらは 2 つの特別な出力をカバーします。 

したがって、アルゴリズムは常に正しい答えを返します。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

def solve():
    t = int(input())

    for _ in range(t):
        n, c, d = map(int, input().split())
        a = list(map(int, input().split()))

        a.sort(reverse=True)

        pref = [0]
        for x in a:
            pref.append(pref[-1] + x)

        # Infinity
        if pref[min(n, d)] >= c:
            print("Infinity")
            continue

        # Impossible
        if a[0] * d < c:
            print("Impossible")
            continue

        def can(k):
            cycle = k + 1

            take = min(cycle, n)
            cycle_sum = pref[take]

            full = d // cycle
            rem = d % cycle

            total = full * cycle_sum
            total += pref[min(rem, n)]

            return total >= c

        lo, hi = 0, d
        ans = 0

        while lo <= hi:
            mid = (lo + hi) // 2

            if can(mid):
                ans = mid
                lo = mid + 1
            else:
                hi = mid - 1

        print(ans)

solve()
```## 複雑さの分析

 | 測定 | 複雑さ |
 | --- | --- |
 | 並べ替え |`O(n log n)`|
 | 各実現可能性チェック |`O(1)`|
 | 二分探索 |`O(log d)`|
 | テスト ケースごとの合計 |`O(n log n + log d)`|
 | スペース |`O(n)`|

 の合計以来、`n`そして`d`すべてのテスト ケースで、最大でも両方が`2·10^5`、これは簡単に制限内に収まります。
