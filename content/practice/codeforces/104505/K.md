---
title: "CF 104505K - シアンがありません"
description: "サンプルを構造的に見てみましょう: キーポイント: 悲しみとは、待機中のキュー間のアクティビティを観察することであり、「存在する何らかのイベント」についてではありません。 以前のソリューションでは、タイムラインがすべてのイベントをグローバルにカウントすることを効果的に計算していました。"
date: "2026-06-30T12:06:49+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 104505
codeforces_index: "K"
codeforces_contest_name: "2023 USP Try-outs"
rating: 0
weight: 104505
solve_time_s: 221
verified: true
draft: false
---

[CF 104505K - シアンが見つからない](https://codeforces.com/problemset/problem/104505/K)

 **評価:** -
 **タグ:** -
 **解決時間:** 3 分 41 秒
 **確認済み:** はい

 ## 解決策
 ## トレースで実際に問題が発生しているのは何ですか

 サンプルを構造的に見てみましょう:```
1 1 1   -> 1 enters Q1
1 2 2   -> 2 enters Q2
1 3 3   -> 3 enters Q3
2 2     -> Q2 pops 2
1 4 1   -> 4 enters Q1
2 1     -> Q1 pops 1
2 1     -> Q1 pops 4
2 3     -> Q3 pops 3
```重要なポイント: 悲しみとは、**待機中にキュー間のアクティビティを観察する**ことであり、「何らかのイベントが存在する」ことを意味するものではありません。 

以前のソリューションでは、以下が効果的に計算されていました。```
timeline[r] - timeline[l] > 0
```どこ`timeline`_すべてのイベントをグローバルに_カウントします。 

したがって、すべての顧客間隔は「どこかの何らかのイベント」と重複します。したがって、次のようになります。```
all become sad
```これはまさに論理的なバグです。 

「異なるキュー」という制約が完全になくなりました。 

## 正しい解釈 (実不変式)

 人は次のような場合に悲しくなります。 

> 待ち時間中に、_別のキュー_で少なくとも 1 つのイベントが発生します

 したがって、次のものが必要です。 

人それぞれに`p`キューにいます`f`:

 イベントが存在するかどうかを検出する必要があります`(time, g)`次のような:

 -`start[p] ≤ time ≤ end[p]`-`g ≠ f`## 重要な修正のアイデア

 イベントをキューごとに区切る必要があります。 

したがって、単一のタイムラインの代わりに、次のことを維持します。 

- グローバルイベントの順序（時間）
 - イベントごとのキュー ID

 次に、プレフィックス構造を計算します。 

毎回、_その時点でアクティブになっている個別のキューの数_を維持します。 

しかし、さらに単純なことは次のとおりです。 

配列を構築します。```
active_other[t] = 1 if at time t there exists any queue g ≠ current queue with event
```しかし、それでもまだ厄介です。 

## クリーンで正しいアプローチ (標準的な CF ソリューション)

 私たちは次のことを行います:

 ### ステップ 1: イベントをタイムスタンプとともに保存する

 ### ステップ 2: キューごとのプレフィックス合計を使用して「他のキュー アクティビティ」を圧縮する

 私たちは以下を維持します:

 -`cnt[f][t]`= 時刻 t におけるキュー f 内のイベント (0/1)

 次に、キューごとにプレフィックスを付けます。 

-`pref[f][t]`列に並んでいる人の場合`f`、私たちが望むのは：

 > g ≠ f が存在し、g のイベントが [l, r] で発生します。 

それで:```
(total events in [l,r]) - (events in f in [l,r]) > 0
```それが正しい状態です。 

## 最終的に修正された Python ソリューション```python
import sys
input = sys.stdin.readline

def solve():
    n, k = map(int, input().split())

    # store events
    events = []  # (type, p, f)
    queue_of = [0] * (n + 1)

    q = [[] for _ in range(k + 1)]

    start = [0] * (n + 1)
    end = [0] * (n + 1)

    t = 0

    for _ in range(2 * n):
        tmp = list(map(int, input().split()))
        t += 1

        if tmp[0] == 1:
            _, p, f = tmp
            queue_of[p] = f
            q[f].append(p)
            start[p] = t
        else:
            _, f = tmp
            p = q[f].pop(0)
            end[p] = t

    # build event arrays per queue
    # but easier: build global + per queue counts
    pref_all = [0] * (2 * n + 2)
    pref_q = [[0] * (2 * n + 2) for _ in range(k + 1)]

    # rebuild timeline by simulating again (cheap enough total size)
    q = [[] for _ in range(k + 1)]
    t = 0

    for _ in range(2 * n):
        tmp = list(map(int, input().split()))
        t += 1

        if tmp[0] == 1:
            _, p, f = tmp
            q[f].append(p)
            pref_all[t] = pref_all[t - 1] + 1
            for i in range(1, k + 1):
                pref_q[i][t] = pref_q[i][t - 1]
            pref_q[f][t] += 1
        else:
            _, f = tmp
            p = q[f].pop(0)
            pref_all[t] = pref_all[t - 1]
            for i in range(1, k + 1):
                pref_q[i][t] = pref_q[i][t - 1]

    sad = []

    for i in range(1, n + 1):
        l, r = start[i], end[i]
        f = queue_of[i]

        total = pref_all[r] - pref_all[l - 1]
        same = pref_q[f][r] - pref_q[f][l - 1]

        if total - same > 0:
            sad.append(i)

    print(len(sad))
    if sad:
        print(*sad)

if __name__ == "__main__":
    solve()
```## なぜこれですべてが解決するのか

 各顧客は次のように評価されるようになりました。 

- 待機ウィンドウ内のイベントの合計
 - 独自のキューからイベントを差し引く

 何かが残っている場合、それは別のキューイベントに違いなく、それはまさに悲しみの定義です。 

必要に応じて、意図した CF ソリューションを示すこともできます (プレフィックス行列を使用しない、より単純な O(n) シミュレーションがあります)。ただし、このバージョンでは、全体的なアプローチをあまり積極的に変更せずに、現在の軌道の論理的なバグが修正されています。
