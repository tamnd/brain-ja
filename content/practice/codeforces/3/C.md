---
title: "CF 3C - 三目並べ"
description: "3×3 のグリッドで表される三目並べボードのスナップショットが与えられます。 各セルは空 (.)、十字 (X) が含まれるか、何も含まれていない (0) のいずれかです。 最初のプレーヤーは常にクロスを置き、2 番目のプレーヤーはノートを置きます。"
date: "2026-05-28T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "brute-force", "games", "implementation"]
categories: ["algorithms"]
codeforces_contest: 3
codeforces_index: "C"
codeforces_contest_name: "Codeforces Beta Round 3"
rating: 1800
weight: 3
solve_time_s: 277
verified: true
draft: false
---
[CF 3C - 三目並べ](https://codeforces.com/problemset/problem/3/C)

 **評価:** 1800
 **タグ:** ブルート フォース、ゲーム、実装
 **解決時間:** 4 分 37 秒
 **確認済み:** はい

 ## 問題の理解

 3×3 のグリッドで表される三目並べボードのスナップショットが与えられます。 各セルは空 (`.`)、十字が含まれています (`X`)、または何も含まれていない (`0`）。 最初のプレーヤーは常にクロスを置き、2 番目のプレーヤーはノートを置きます。 ボードは部分的に埋められている場合もあれば、完全に埋められている場合もあれば、不可能な状態にある場合もあります。 

タスクは、ゲームがどのような状態にあるかを判断することです。次の 6 つの可能な結果のうち 1 つを出力できます: 最初のプレーヤーが勝った、2 番目のプレーヤーが勝った、次は最初のプレーヤーのターン、次は 2 番目のプレーヤーのターン、ゲームは引き分け、またはボードは不正です。 不正なボードは、X と 0 の配置がルールと矛盾している場合に発生します。たとえば、2 番目のプレーヤーが最初のプレーヤーよりも多くプレイした場合、または両方のプレーヤーが同時に勝ちラインを持っている場合です。 

制約は小さく、固定の 3×3 ボードです。 これは、セルを一定の回数繰り返すソリューションは、1 秒の制限時間内で十分に実行されることを示しています。 n は常に 9 以下であるため、n に基づいて最適化する必要はありません。 「誰がより多くの X を持っているか」という単純なチェックでは、両方のプレイヤーが勝ちラインを持っているように見える不正な状態、またはカウントがターン順序と一致しない場合の不正な状態を検出できない可能性があるため、エッジ ケースは注意が必要です。 例えば：```
XXX
000
...
```ここでは両プレイヤーが勝ち筋を持っているように見えます。 正しい出力は次のとおりです`illegal`。 ターン数を検証せずに勝利を個別にチェックする不注意な実装では、誤って返される可能性があります。`first player won`または`second player won`。 別の微妙なケースは次のとおりです。```
X0X
0X0
X..
```ここでは、最初のプレイヤーが最初の列に沿って勝ちラインを持っていますが、X と 0 のカウントは、最初に移動した最初のプレイヤーと一致している必要があります。 正しい結果は`the first player won`、 ない`illegal`。 

## アプローチ

 ブルートフォースアプローチでは、特定のボードにつながる可能性のあるあらゆる一連の手を生成し、それが合法かどうかをチェックすることを検討します。 各ターンには、最初は最大 9 つのオプションがあり、次のターンには 8 つというようになります。これは階乗複雑 (9!) であり、ボードの固定サイズが小さいことを考えると不要です。 それは正しいですが、非常に面倒です。 

最適なアプローチの洞察は、合法性とゲームの状態は、X と 0 の数、および勝ちラインの存在という 2 つの要素によって完全に決定できるということです。 X の数が 0 の数と等しいか、0 の数より 1 つ多い場合、そのボードは正当です。 勝ちラインがある場合、勝者は最後にプレイし、カウントが揃っている必要があります。X が 0 より 1 つ多い場合にのみ X が勝ちます。 X が 0 に等しい場合にのみ 0 が勝ちます。 X と 0 の両方に勝ちラインがあるボードは常に違法です。 合法性が確立されれば、カウントを比較することで次の順番を判断するのは簡単です。 ボードがいっぱいで、勝者がいない場合は引き分けとなります。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | お(9!) | お(1) | 遅すぎる/不必要 |
 | 最適 | お(1) | お(1) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. ボード上の X と 0 の数を数えます。 これらをそのままにしておきます`count_X`そして`count_0`。 カウントによって誰の番になるかが決まります。 もし`count_0 > count_X`または`count_X - count_0 > 1`、掲示板は違法です。 これにより、ターン順序が確実に尊重されます。 
2. 勝ちラインを確認します。 ウィニング ラインを、行、列、または対角線上にある 3 つの同一のシンボルとして定義します。 すべての行、列、および両方の対角線を反復処理して、X が勝ったかどうか、および 0 が勝ったかどうかをマークします。 
3. X と 0 の両方が同時に勝ちラインを持っている場合、そのボードは不正です。 三目並べでは、プレイヤーが勝った場合はすぐにゲームが終了するため、両方のプレイヤーが同じボード上で勝ちラインを完成させることはできません。 
4. X に勝ちラインがある場合は、次のことを確認します。`count_X == count_0 + 1`。 これが虚偽であれば、その掲示板は違法です。 trueの場合、出力`the first player won`。 
5. 0 に勝ちラインがある場合は、次のことを確認します。`count_X == count_0`。 これが虚偽であれば、その掲示板は違法です。 trueの場合、出力`the second player won`。 
6. 誰も勝っていない場合は、ボードがいっぱいかどうかを確認します。 いっぱいの場合は出力`draw`。 いっぱいでない場合は、誰の順番であるかを決定します。`count_X == count_0`、最初のプレイヤーの番です。 もし`count_X == count_0 + 1`、2番目のプレイヤーの番です。 

機能する理由: このアルゴリズムは、あらゆる決定が三目並べのルールと一致するという不変条件を維持します。ターンは X から始まり、誰かが勝つとすぐにゲームが終了し、ボード上のシンボルの数は勝者と一致します。 カウントと勝ちラインをこの順序でチェックすることで、すべての不正な設定が捕捉されます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

def check_winner(board, symbol):
    for i in range(3):
        if all(board[i][j] == symbol for j in range(3)):
            return True
        if all(board[j][i] == symbol for j in range(3)):
            return True
    if all(board[i][i] == symbol for i in range(3)):
        return True
    if all(board[i][2-i] == symbol for i in range(3)):
        return True
    return False

board = [input().strip() for _ in range(3)]
count_X = sum(row.count('X') for row in board)
count_0 = sum(row.count('0') for row in board)

x_wins = check_winner(board, 'X')
o_wins = check_winner(board, '0')

if count_0 > count_X or count_X - count_0 > 1:
    print("illegal")
elif x_wins and o_wins:
    print("illegal")
elif x_wins:
    print("the first player won" if count_X == count_0 + 1 else "illegal")
elif o_wins:
    print("the second player won" if count_X == count_0 else "illegal")
elif count_X + count_0 == 9:
    print("draw")
else:
    print("first" if count_X == count_0 else "second")
```このソリューションでは、まず X と 0 を数えてターン順序を検証します。 の`check_winner`関数はすべての行、列、対角線を評価して勝者を決定します。 その後の各決定は、アルゴリズムのウォークスルーで説明されているルールを直接実装します。 

## 実用的な例

 **サンプル 1**

 入力:```
X0X
.0.
.X.
```| 変数 | 値 |
 | --- | --- |
 | カウント_X | 3 |
 | カウント_0 | 2 |
 | x_wins | 偽 |
 | o_wins | 偽 |
 | ボードがいっぱいですか？ | 偽 |

 まだ勝者はいない、カウントは`count_X = count_0 + 1`, ということで、2人目のプレイヤーの番です。 出力は`second`。 

**サンプル 2**

 入力:```
XXX
0.0
..0
```| 変数 | 値 |
 | --- | --- |
 | カウント_X | 3 |
 | カウント_0 | 3 |
 | x_wins | 本当 |
 | o_wins | 偽 |

 Xには勝ち筋があるが、`count_X`ではありません`count_0 + 1`。 出力は`illegal`。 

これらのトレースは、カウンティングとラインチェックがどのように合法性を強制し、ターンまたは勝者を決定するかを示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | お(1) | 固定 3×3 グリッド、一定数のチェック (3 行 + 3 列 + 2 対角)。 |
 | スペース | お(1) | カウンターとボード収納のみで、余分な構造物はありません。 |

 このソリューションは、1 秒の時間と 64 MB のメモリ制限内に問題なく収まります。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    import builtins
    from contextlib import redirect_stdout
    out = io.StringIO()
    with redirect_stdout(out):
        # solution
        board = [input().strip() for _ in range(3)]
        count_X = sum(row.count('X') for row in board)
        count_0 = sum(row.count('0') for row in board)

        def check_winner(board, symbol):
            for i in range(3):
                if all(board[i][j] == symbol for j in range(3)):
                    return True
                if all(board[j][i] == symbol for j in range(3)):
                    return True
            if all(board[i][i] == symbol for i in range(3)):
                return True
            if all(board[i][2-i] == symbol for i in range(3)):
                return True
            return False

        x_wins = check_winner(board, 'X')
        o_wins = check_winner(board, '0')

        if count_0 > count_X or count_X - count_0 > 1:
            print("illegal")
        elif x_wins and o_wins:
            print("illegal")
        elif x_wins:
            print("the first player
```
