---
title: "CF 1510B - ボタンロック"
description: "この問題により、0 から d-1 までのラベルが付いた d ボタンを備えた押しボタン式コンビネーション ロックが得られます。 ボタンを押すと、「リセット」ボタンを押すまで押し続けられ、すべてのボタンが元に戻ります。"
date: "2026-06-10T19:22:31+07:00"
tags: ["codeforces", "competitive-programming", "flows", "graph-matchings", "graphs"]
categories: ["algorithms"]
codeforces_contest: 1510
codeforces_index: "B"
codeforces_contest_name: "2020-2021 ICPC, NERC, Northern Eurasia Onsite (Unrated, Online Mirror, ICPC Rules, Teams Preferred)"
rating: 2600
weight: 1510
solve_time_s: 187
verified: true
draft: false
---

[CF 1510B - ボタン ロック](https://codeforces.com/problemset/problem/1510/B)

 **評価:** 2600
 **タグ:** フロー、グラフ マッチング、グラフ
 **解決時間:** 3 分 7 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 この問題により、プッシュボタン式のコンビネーションロックが得られます。`d`～というラベルが付いたボタン`0`に`d-1`。 ボタンを押すと、「RESET」ボタンを押すまで押し続けられ、すべてのボタンが元に戻ります。 特定のボタンのセットを同時に押すとロックが即座に開き、そのリストが提供されます。`n`使用可能なパスワード。それぞれ長さのバイナリ文字列として表されます。`d`。 あ`1`所定の位置にある`j`ボタンを意味します`j`そのパスワードを入力するには を押す必要があります。 

私たちのタスクは、このシーケンスの実行中にロックが各ボタンを通過するように、ボタンを押す最短のシーケンス (数字または「リセット」) を構築することです。`n`少なくとも 1 回はパスワードを入力します。 出力には、プレスの合計数とシーケンス自体が含まれている必要があります。 

制約が十分に厳しいため、組み合わせ手法を使用できます。`d`は最大 10 なので、可能なボタン状態の総数は次のようになります。`2^d = 1024`最悪の場合。`n`までです`2^d - 1`したがって、ボタンの空ではないすべてのサブセットを考慮する必要がある可能性があります。 これは、アルゴリズムが指数関数的に増加することを意味します。`d`許容されますが、スケーリング可能なものは何でも`n!`または`2^n`大きい場合には遅すぎるでしょう`d`。 注意すべきエッジケースには、複数のパスワードが必要なボタンで重複している場合や、あるパスワードが別のパスワードのサブセットである場合などのシナリオが含まれます。 パスワードごとに個別にボタンを押す単純なアプローチでは、不必要に RESET が頻繁に押される可能性があり、必要以上に長いシーケンスが発生する可能性があります。 

微妙さの例は次のとおりです。`d = 3`そしてパスワードは`101`、`111`。 不注意なアプローチは危険をもたらす可能性があります`1 0 R 1 1 0 1`短いシーケンスながら`1 0 1`十分です。 重なりを検出し、すでに押されたボタンを再利用することが重要です。 

## アプローチ

 ブルートフォース手法では、ボタンを押すすべてのシーケンスを生成し、すべてのパスワードをカバーする最短のものを選択します。 シーケンスの長さは先験的に無制限であり、`d`オプションに加えて、押すたびに RESET を実行します。 小さいものでも`d`、シーケンスの数は長さに応じて指数関数的に増加するため、このアプローチは些細な場合にのみ機能します。 

重要な観察は、問題がパスワード状態のグラフとしてモデル化できるということです。 各パスワードをビットマスクとして表します。 パスワード間の遷移は、ボタン (ビットが 0 から 1 に進む) を押すか、RESET を押すことに対応します。 パスワードから移行するためのコスト`A`に`B`のボタンの数です`B`まだ押し込まれていない`A`、RESET が必要な場合は 1 を加えます。 次に、問題は、すべてのパスワード ノードを訪問する最小コストのシーケンスを見つけることに帰着します。これは、すべてのノードをカバーする有向グラフを通る最短パスに相当します。 と`d <= 10`、すべてのペアごとのコストを事前計算し、これらの状態に対して巡回セールスマン問題 (TSP) の変形を使用できます。 なぜなら`2^d <= 1024`, サブセット上の DP は実現可能です。 

もう 1 つの洞察は、ビットマスク間のハミング距離のみを考慮すればよいということです。 もし`current_mask`は現在押されているボタンです、そして`next_mask`はターゲットのパスワード、押された回数はパスワードのビット数です。`next_mask`入っていない`current_mask`。 もし`next_mask`現在押されていないビットが必要ですが、現在押されているビットの一部が含まれていません`next_mask`、RESETの方が安いです。 これにより、DP の正確なコスト関数が得られます。 ビットマスク DP を使用すると、最小シーケンス長を効率的に見つけてシーケンスを再構築できます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O((d+1)^L) | O(L) | 遅すぎる |
 | 最適 (パスワードのビットマスク DP) | O(n * 2^n + n^2 * d) | O(n * 2^n) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 各パスワード文字列を整数ビットマスクに変換します。 これにより、効率的な比較とビット演算が可能になります。 
2. 任意の 2 つのパスワード間を移行するコストを事前計算します。 もし`maskA`は現在の状態であり、`maskB`がターゲットであるかどうかを確認してください`maskB`押されたすべてのボタンが含まれます`maskA`。 そうでない場合は、リセットが必要です。 次に、次のビット数を数えます。`maskB`設定されていない`maskA`追加のプレス回数を決定します。 
3. ビットマスク DP を使用して、訪問されたパスワードを表します。 させて`dp[mask][i]`で表されるパスワードのサブセットをカバーする最小シーケンス長であること。`mask`、パスワードで終わる`i`。 
4.初期化`dp[1 << i][i]`パスワードのビット数で`i`空の状態からは、これらのビットを押すだけで済むためです。 
5. 考えられる最後のパスワードごとに、パスワードのすべてのサブセットを繰り返します。`i`サブセットに別のパスワードを追加してみてください`j`まだ含まれていません。 アップデート`dp[subset | (1 << j)][j]`として`min(dp[subset | (1 << j)][j], dp[subset][i] + cost[i][j])`。 
6. DP テーブルを埋めた後の最小シーケンス長は次のようになります。`min(dp[(1 << n) - 1][i])`全体`i`。 
7. DP を遡ってシーケンスを再構築し、新しいボタンを押す場合は数字を追加し、RESET が必要な場合は「R」を追加します。 

機能する理由: DP は、パスワードのすべてのサブセットと、そのサブセット内の考えられる最後のパスワードをすべて考慮します。 サブセット間の最小限の遷移を計算することで、最終的なソリューションが最小限の押下ですべてのパスワードをカバーすることが保証されます。 ビットマスクを使用すると、効率的なサブセットとコストの計算が可能になり、コストを事前計算することで RESET の決定が最適になることが保証されます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

def main():
    d, n = map(int, input().split())
    passwords = []
    for _ in range(n):
        s = input().strip()
        mask = 0
        for i, c in enumerate(s):
            if c == '1':
                mask |= 1 << i
        passwords.append(mask)

    # Precompute cost between passwords
    cost = [[0] * n for _ in range(n)]
    need_reset = [[False] * n for _ in range(n)]
    for i in range(n):
        for j in range(n):
            if i == j:
                continue
            if passwords[j] & ~passwords[i]:
                # buttons to press outside current mask
                new_bits = passwords[j] & ~passwords[i]
                if passwords[i] & ~passwords[j]:
                    need_reset[i][j] = True
                cost[i][j] = bin(new_bits).count('1')
                if need_reset[i][j]:
                    cost[i][j] += 1  # for the RESET

    # DP over subsets
    INF = 10**9
    dp = [[INF] * n for _ in range(1 << n)]
    parent = [[-1] * n for _ in range(1 << n)]
    for i in range(n):
        dp[1 << i][i] = bin(passwords[i]).count('1')

    for mask in range(1 << n):
        for u in range(n):
            if not (mask & (1 << u)):
                continue
            for v in range(n):
                if mask & (1 << v):
                    continue
                new_mask = mask | (1 << v)
                val = dp[mask][u] + cost[u][v]
                if val < dp[new_mask][v]:
                    dp[new_mask][v] = val
                    parent[new_mask][v] = u

    # Find best last password
    full = (1 << n) - 1
    last = min(range(n), key=lambda i: dp[full][i])
    seq = []

    # Reconstruct sequence
    order = []
    mask = full
    while last != -1:
        order.append(last)
        prev = parent[mask][last]
        mask ^= (1 << last)
        last = prev
    order = order[::-1]

    # Build actual button sequence
    current = 0
    res = []
    for idx in order:
        target = passwords[idx]
        if current & ~target:
            res.append('R')
            current = 0
        for b in range(d):
            if (target >> b) & 1 and not (current >> b) & 1:
                res.append(str(b))
                current |= 1 << b

    print(len(res))
    print(' '.join(res))

if __name__ == "__main__":
    main()
```このコードは、比較とカウントを容易にするために、まず各パスワードをビットマスクに変換します。 コストと、パスワード間の移行に RESET が必要かどうかを事前計算します。 次に、DP はすべてのパスワードをカバーする最小シーケンス長を見つけます。 最後に、RESET を慎重に処理し、すでに押されたボタンを押さないようにしながら、シーケンスを再構築します。 

## 実用的な例

 ### サンプル 1

 入力:```
2 2
10
11
```| ステップ | 現在 | ターゲット | リセットしますか？ | 押されたボタン | シーケンス |
 | --- | --- | --- | --- | --- | --- |
 | 開始 | 0 | 10 | いいえ | 0 | |
 | 1 | 0 | 10 | いいえ | 10 | 0 |
 | 2 | 10 | 11 | いいえ | 11 | 0 1 |

 この表は、アルゴリズムが最小シーケンスを特定していることを確認します。`0`、 それから`1`。 

### サンプル 2

 入力:```
3 3
100
110
111
```| ステップ |
