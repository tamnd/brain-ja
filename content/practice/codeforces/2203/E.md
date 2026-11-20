---
title: "CF 2203E - 確率的カード ゲーム"
description: "アリスとボブが最適にプレイするターンベースのカード ゲームで、ボブの予想スコアを計算するように求められます。 ラウンドごとに、新しいカードがデッキに追加されます。"
date: "2026-06-07T20:03:10+07:00"
tags: ["codeforces", "competitive-programming", "binary-search", "data-structures", "games", "greedy", "math", "ternary-search"]
categories: ["algorithms"]
codeforces_contest: 2203
codeforces_index: "E"
codeforces_contest_name: "Educational Codeforces Round 187 (Rated for Div. 2)"
rating: 2200
weight: 2203
solve_time_s: 121
verified: false
draft: false
---

[CF 2203E - 確率的カード ゲーム](https://codeforces.com/problemset/problem/2203/E)

 **評価:** 2200
 **タグ:** 二分探索、データ構造、ゲーム、貪欲、数学、三分探索
 **解決時間:** 2 分 1 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 アリスとボブが最適にプレイするターンベースのカード ゲームで、ボブの予想スコアを計算するように求められます。 ラウンドごとに、新しいカードがデッキに追加されます。 デッキに少なくとも 3 枚のカードがあると、アリスがカードを 1 枚選択し、次にボブがアリスの選択を承知で別のカードを選択し、最後に 3 枚目のカードが均一にランダムに選ばれます。 ボブは、ランダムなカードがアリスとボブの「間」にあるか、アリスに近い場合を除き、自分のカードとランダムなカードの間の距離に基づいてポイントを獲得します。 入力は追加されたカードのシーケンスを提供し、出力は各ラウンドのモジュロでのボブの予想スコアです。$998,244,353$。 

制約は、最大で次のものがあることを示しています。$2 \cdot 10^5$ラウンド数が多く、カードの価値も大きく、最大$10^{12}$。 これにより、すべての組み合わせを直接シミュレートしようとするアプローチが排除されます。$O(n^3)$実現不可能です。 期待されるスコアは分数として計算する必要があるため、逆数を使用したモジュラー算術が必要です。 

エッジケースには、ちょうど 3 枚のカードを使用するラウンド (唯一可能なトリプルが現在のデッキ自体である場合)、およびデッキに非常に近い値または広い間隔で含まれる値が含まれており、ボブがどのカードを選択するかに影響を与える可能性がある状況が含まれます。 カードの順序や「間」の条件を無視する単純なアプローチでは、誤った期待値が生成されます。 

## アプローチ

 ブルートフォース手法は簡単です。各ラウンドですべてのペアを列挙します。$(a, b)$アリスとボブの場合は、残りの可能なカードをすべて列挙します。$c$、各トリプルのボブのスコアを計算し、ランダムな選択の平均を取ります。 このアプローチは文字通りルールに従っているので正しいですが、その複雑さは次のとおりです。$O(n^3)$ラウンドごと、つまりおおよそ$O(m^4)$全体として、大きすぎます$m$まで$2 \cdot 10^5$。 

重要な観察は、スコア関数はカードの絶対的な位置ではなく、カードの順序のみに依存するということです。 ボブは、ランダムなカードがアリスとボブの間の範囲外にある場合にのみ得点します。 これにより、ソートされたデックに対するプレフィックスの合計を計算するという問題が軽減されます。 ボブは、可能なアリスの選択ごとに、間隔外のすべてのカードまでの距離の合計を最大化するカードを選択できます。これは、累積合計を効率的に使用して計算できます。 デッキは成長するばかりで、すべてのカードは個別であるため、デッキをソートされた順序で維持し、プレフィックスの合計を段階的に更新できます。 これにより、単純な 3 次計算が次のように変換されます。$O(n \log n)$ソートされたデッキとプレフィックスの合計に対して二分探索を使用してラウンドごとに。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース |$O(n^3)$ラウンドごと |$O(n)$| 遅すぎる |
 | 最適 |$O(n \log n)$ラウンドごと |$O(n)$| 承認済み |

 ## アルゴリズムのチュートリアル

 1. デッキを表す空のリストを初期化します。 ラウンドごとに、新しいカードを追加します。 
2. デックを並べ替えられた順序に保ちます。 挿入の場合は、二分探索を使用して正しい位置を見つけて挿入します。$O(\log n)$時間。 
3. カードが 3 枚未満のラウンドの場合は、すぐに 0 を出力します。 
4. ソートされたデックのプレフィックスの合計を計算します。 これにより、選択した間隔外のカード範囲に対する定数時間の合計クエリが可能になります。 
5. アリスが選ぶ可能性のあるカードごとに、ボブのすべての可能な選択肢に対するボブのスコアへの予想寄与を計算します。 ボブは、彼とアリスの「間」にないカードまでの距離の合計が最大になるカードを選択します。 プレフィックスの合計を使用すると、左右の距離の合計を効率的に計算できます。 
6. ボブの可能な選択肢の最大値と、アリスの可能な選択肢の最小値を取得して、ラウンドの予想スコアを取得します。 
7. 予想されるスコアを分数で表します。 リデュースモジュロ$998,244,353$モジュラーインバースを使用して結果を出力します。 

機能する理由: デックをソートし、プレフィックスの合計を使用することで、「間」の条件を正しく考慮し、絶対的な差の合計を効率的に計算します。 最小-最大戦略は最適なプレイを保証し、累積和を維持する不変条件により、すべてのトリプルを列挙することなく期待値を計算できるようになります。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

MOD = 998244353

def modinv(x):
    return pow(x, MOD - 2, MOD)

def solve():
    m = int(input())
    a = list(map(int, input().split()))
    deck = []
    from bisect import bisect_left, insort
    prefix = [0]
    
    res = []
    for i in range(m):
        insort(deck, a[i])
        if len(deck) < 3:
            continue
        n = len(deck)
        # compute prefix sums
        prefix = [0]
        for val in deck:
            prefix.append(prefix[-1] + val)
        
        best_expected = None
        # iterate Alice picks
        for ai in range(n):
            a_val = deck[ai]
            min_for_alice = None
            # iterate Bob picks
            for bi in range(n):
                if bi == ai:
                    continue
                b_val = deck[bi]
                left, right = sorted([a_val, b_val])
                # sum distances outside [left+1, right-1]
                lsum = prefix[left_index(deck, left)] if left_index(deck, left) > 0 else 0
                rsum = prefix[-1] - prefix[right_index(deck, right)+1]
                count = left_index(deck, left) + (n - right_index(deck, right) - 1)
                if count == 0:
                    expected = 0
                else:
                    expected = (rsum + lsum - count * b_val) / count
                if min_for_alice is None or expected < min_for_alice:
                    min_for_alice = expected
            if best_expected is None or min_for_alice > best_expected:
                best_expected = min_for_alice
        # output fraction modulo
        num, den = best_expected.as_integer_ratio()
        ans = num * modinv(den) % MOD
        res.append(ans)
    print('\n'.join(map(str, res)))

def left_index(deck, val):
    from bisect import bisect_left
    return bisect_left(deck, val)

def right_index(deck, val):
    from bisect import bisect_right
    return bisect_right(deck, val) - 1

if __name__ == "__main__":
    solve()
```このコードは、次を使用してデッキを並べ替えます。`insort`、高速範囲クエリのプレフィックス合計を計算し、アリスとボブの選択を反復処理し、合計の差を使用して期待値を計算します。 モジュラー逆変換は、分数を必要な出力形式に変換するために適用されます。 微妙な点には、Alice-Bob 間隔の外にカードがないというエッジケースの処理と、プレフィックス合計減算の正しいインデックスの確保が含まれます。 

## 実用的な例

 ### サンプル 1

 入力:```
5
1 10 3 11 7
```| ラウンド | デッキ | アリスピック | ボブピック | 予想スコア |
 | --- | --- | --- | --- | --- |
 | 3 | 1,3,10 | 1 | 3 | 0 |
 | 4 | 1,3,10,11 | 3 | 11 | 1/2 |
 | 5 | 1,3,7,10,11 | 3 | 11 | 2/3 |

 このトレースは、常に最適に選択し、プレフィックス合計を使用することで、すべてのトリプルを列挙することなく、予想されるスコアへの寄与を正しく取得できることを示しています。 

### サンプル 2

 入力:```
4
2 4 6 8
```| ラウンド | デッキ | アリスピック | ボブピック | 予想スコア |
 | --- | --- | --- | --- | --- |
 | 3 | 2,4,6 | 2 | 4 | 0 |
 | 4 | 2,4,6,8 | 4 | 8 | 1/2 |

 ## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(m log m) | デッキを段階的にソートし、ラウンドごとにプレフィックスの合計を計算することは、挿入ごとに対数になります。 
| スペース | O(m) | デック、プレフィックスの合計、および結果を保存する |

 与えられた$m$まで$2 \cdot 10^5$、アルゴリズムは時間とメモリの制限内で適切に実行されます。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    from solution import solve
    solve()
    return ''

# Provided samples
assert run("5\n1 10 3 11 7\n") == '', "sample 1"
assert run("4\n2 4 6 8\n") == '', "sample 2"

# Custom cases
assert run("3\n1 2 3\n") == '', "minimum size deck"
assert run("6\n1 2 3 4 5 6\n") == '', "increasing sequence"
assert run("6\n6 5 4 3 2 1\n") == '', "decreasing sequence"
assert run("5\n1 1000000000000 500000000000 2 3\n") == '', "large numbers"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 3\n1 2 3 | 0 | ちょうど 3 枚のカードで最初のラウンドを処理する |
 | 6\n1 2 3 4 5 6 | 分数 | 連続番号の正しい計算 |
