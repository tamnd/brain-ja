---
title: "CF 417A - 排除"
description: "私たちはプログラミング コンテストを企画しており、その目標は少なくとも $n cdot m$ のファイナリストを選出することです。 参加者は 3 つの方法で資格を得ることができます。1 つは、$c$ の問題があり、$n$ の勝者を生み出すメインのエリミネーション ラウンドの 1 つに勝つことです。 2 つ目は、次のいずれかに勝つことです…"
date: "2026-05-29T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "dp", "implementation", "math"]
categories: ["algorithms"]
codeforces_contest: 417
codeforces_index: "A"
codeforces_contest_name: "RCC 2014 Warmup (Div. 2)"
rating: 1500
weight: 417
solve_time_s: 99
verified: true
draft: false
---

[CF 417A - 排除](https://codeforces.com/problemset/problem/417/A)

 **評価:** 1500
 **タグ:** dp、実装、数学
 **解決時間:** 1 分 39 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 私たちはプログラミング コンテストを開催しています。その目的は、少なくとも$n \cdot m$ファイナリスト。 参加者は 3 つの方法で資格を得ることができます。まず、メインのエリミネーション ラウンドの 1 つに勝つことで、$c$問題と成果$n$勝者。 2 つ目は、追加のエリミネーション ラウンドの 1 つに勝つことです。$d$問題を起こし、1 人の勝者を生み出します。 第三に、その中にいることによって$k$自動的に資格を得る事前に選択された勝者。 タスクは、必要なファイナリスト数を確保しながら、すべてのラウンドで使用される問題の総数が最小限になるようにラウンドをスケジュールすることです。 

入力により、問題の各ラウンドのサイズ、メインラウンドごとの勝者の数、必要なグループの数が得られます。$m$、および事前に選択された当選者の数$k$。 出力は、少なくとも到達するために必要な問題の最小総数です。$n \cdot m$ファイナリスト。 

すべての値が ≤ 100 であるため、メイン ラウンドと追加ラウンドのすべての合理的な組み合わせを総当たりでチェックすることが可能です。 この制約により、高度なデータ構造や高度なアルゴリズムは必要なくなります。 

難しいのは、事前に選択された勝者により、必要な追加ラウンドの数が減るということです。 無視した素朴なアプローチ$k$問題全体を超えてしまう可能性があります。 同様に、整数除算における丸めの問題は重要です。さらに 15 人の勝者が必要で、メイン ラウンドで 7 人が生成される場合、実際には 3 つのメイン ラウンドが必要になります (2 人で 14 人の勝者が生成され、もう 1 回で 7 人が生成されますが、わずかにオーバーシュートします)。 不用意に実装すると 2 ラウンドかかり、目標を外れる可能性があります。 

## アプローチ

 ブルートフォースアプローチでは、メインラウンドと追加ラウンドのすべての組み合わせが考慮されます。 考えられるメインラウンド数ごとに、獲得した勝者の数を計算します。 事前に選択された勝者を考慮した後もその数がまだ必要な合計を下回っている場合は、しきい値に到達するために最小限の追加ラウンド数が追加されます。 各組み合わせの問題の総数を計算し、最小値を選択します。 これは、必要なラウンド数が制限されているため機能します (メイン ラウンドごとに最大 100 人の勝者、必要な合計勝者は最大 100 人) が、すべての組み合わせを列挙するとネストされたループが含まれるため、慎重に計算することで回避できます。 

重要な洞察は、問題が 2 つの独立した計算に帰着するということです。つまり、必要なメイン ラウンドの数と、メイン ラウンドの勝者と事前に選択された勝者を考慮した後に必要な追加ラウンドの数です。 単調な関係があります。メイン ラウンドを追加すると常に勝者の合計が増加し、必要な追加ラウンドの数は減少するだけです。 この構造により、メイン ラウンドの数を反復し、必要な正確な追加ラウンドを数学的に計算し、追加ラウンドに対する不必要な総当たり列挙を回避することができます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | ラウンドのすべての組み合わせに対する O(T^2) | お(1) | 動作しますが、不要なネストされたループ |
 | 最適 | O(T) メインラウンドのみを反復する場合 | お(1) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 必要なファイナリストの合計数を次のように計算します。$\text{total\_needed} = n \cdot m$。 
2. 問題の最小合計を非常に大きな数に初期化します。 
3. メインラウンドの回数を繰り返す$x$0 から必要な勝者をカバーできる最大数まで: 技術的には$\text{ceil}(\text{total\_needed}/n)$, ただし、制約があるため、安全に 100 まで上げることができます。 
4. それぞれについて$x$、メインラウンドの勝者の合計を計算します。$\text{winners\_main} = x \cdot n$。 
5. 必要な残りの勝者を計算します。$\text{remaining} = \text{total\_needed} - k - \text{winners\_main}$。 これが ≤ 0 の場合、追加のラウンドは必要ありません。 
6. それ以外の場合は、必要な追加ラウンド数を計算します。$y = \text{ceil}(\text{remaining}/1) = \text{remaining}$ラウンドが追加されるたびに勝者が 1 人追加されるためです。 
7. 使用された問題の総数を計算します。$\text{total\_problems} = x \cdot c + y \cdot d$。 この値が小さい場合は最小値を更新します。 
8. 反復が終了したら、問題の最小総数を出力します。 

Why it works: At each step, we consider all reasonable main-round counts and compute the exact minimal number of additional rounds mathematically. Because winners only increase with rounds and the additional rounds are computed optimally, the algorithm guarantees the minimum total number of problems is found.

 ## Python ソリューション```python
import sys
input = sys.stdin.readline

c, d = map(int, input().split())
n, m = map(int, input().split())
k = int(input())

total_needed = n * m
min_problems = float('inf')

# iterate over possible numbers of main rounds
for main_rounds in range(0, 101):
    winners_from_main = main_rounds * n
    remaining_needed = total_needed - k - winners_from_main
    if remaining_needed <= 0:
        additional_rounds = 0
    else:
        additional_rounds = remaining_needed  # each additional round gives 1 winner
    total_problems = main_rounds * c + additional_rounds * d
    min_problems = min(min_problems, total_problems)

print(min_problems)
```制約により、考えられるメインラウンドを最大 100 ラウンドまで繰り返します。 不必要な追加ラウンドを避けるために、remaining_needed ≤ 0 の場合を慎重に処理します。 追加の丸めの計算式は正確であるため、丸め誤差が回避されます。 最小合計は反復ごとに更新されます。 

## 実用的な例

 **サンプル 1**

 入力:```
1 10
7 2
1
```トレース:

 | メインラウンド | メインからの勝者 | 残りの必要性 | 追加ラウンド | 合計問題数 | 最小の問題 |
 | --- | --- | --- | --- | --- | --- |
 | 0 | 0 | 13 | 13 | 0_1 + 13_10=130 | 130 |
 | 1 | 7 | 6 | 6 | 1 + 60=61 | 61 |
 | 2 | 14 | -1 | 0 | 2*1 + 0=2 | 2 |

 アルゴリズムは、合計 2 つの問題に対して、追加ラウンドなしで 2 つのメイン ラウンドを使用することが最適であることを正しく検出しました。 

**カスタム入力**```
2 3
3 2
4
```トレース:

 | メインラウンド | メインからの勝者 | 残りの必要性 | 追加ラウンド | 合計問題数 | 最小の問題 |
 | --- | --- | --- | --- | --- | --- |
 | 0 | 0 | 2 | 2 | 0 + 6=6 | 6 |
 | 1 | 3 | -1 | 0 | 2 + 0=2 | 2 |

 最適解は 1 つのメインラウンドを使用します。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(100) | main_rounds を 0 から 100 まで繰り返します。 
| スペース | お(1) | 少数の整変数のみが保存されます。 |

 複雑さは、最悪のシナリオであっても 1 秒の制限をはるかに下回ります。 このアルゴリズムはメモリ制限内に快適に適合します。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    import builtins
    old_input = builtins.input
    builtins.input = lambda: sys.stdin.readline()
    c, d = map(int, input().split())
    n, m = map(int, input().split())
    k = int(input())
    total_needed = n * m
    min_problems = float('inf')
    for main_rounds in range(0, 101):
        winners_from_main = main_rounds * n
        remaining_needed = total_needed - k - winners_from_main
        if remaining_needed <= 0:
            additional_rounds = 0
        else:
            additional_rounds = remaining_needed
        total_problems = main_rounds * c + additional_rounds * d
        min_problems = min(min_problems, total_problems)
    builtins.input = old_input
    return str(min_problems)

# provided sample
assert run("1 10\n7 2\n1\n") == "2", "sample 1"

# custom cases
assert run("2 3\n3 2\n4\n") == "2", "minimum additional round needed"
assert run("5 5\n10 10\n0\n") == "50", "large total, no pre-selected winners"
assert run("1 100\n1 1\n1\n") == "0", "pre-selected winner covers requirement"
assert run("10 1\n2 3\n0\n") == "6", "main round cheaper than additional rounds"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 1 10 / 7 2 / 1 | 2 | 提供されたサンプルの正確性 |
 | 2 3 / 3 2 / 4 | 2 | 複数の追加ラウンドではなくメインラウンドを正しく選択します。 
| 5 5 / 10 10 / 0 | 50 | より大きな合計が必要ですが、事前に選択された勝者は必要ありません |
 | 1 100 / 1 1 / 1 | 0 | |
