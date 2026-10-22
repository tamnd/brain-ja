---
title: "CF 105962E - エステシュ vs イヴェンス"
description: "私たちには 1 つの石の山が与えられ、Yvens から始めて交互にターンする 2 人のプレーヤーが与えられます。 各ターンで、現在のプレイヤーは山から 1 つまたは 2 つの石を取り除かなければなりません。"
date: "2026-06-22T16:16:34+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 105962
codeforces_index: "E"
codeforces_contest_name: "UNICAMP Freshman Contest 2025"
rating: 0
weight: 105962
solve_time_s: 53
verified: true
draft: false
---

[CF 105962E - エステシュ vs イヴンス](https://codeforces.com/problemset/problem/105962/E)

 **評価:** -
 **タグ:** -
 **解決時間:** 53 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 私たちには 1 つの石の山が与えられ、Yvens から始めて交互にターンする 2 人のプレーヤーが与えられます。 各ターンで、現在のプレイヤーは山から 1 つまたは 2 つの石を取り除かなければなりません。 最後の石を取ったプレイヤーが負けとなるため、目標は、山を空にする最後の動きをするプレイヤーにならないようにすることです。 

入力するのは初期の石の数だけです。 出力は、最適なプレイで勝利をもたらすことができるプレーヤーの名前です。 

この制約により、石の数を 1e9 まで増やすことができ、ゲームの状態に関するシミュレーションが即座に除外されます。 1 から n までのすべての位置を探索しようとしたり、全範囲にわたって動的プログラミングを使用したりするアプローチは、時間とメモリの両方で遅すぎます。 ソリューションでは、ゲームを入力ごとの一定時間の決定に折りたたむ必要があります。 

このゲームを誤って読みがちな落とし穴は、最後の石を取ったプレイヤーに負け条件が適用され、最後の石を取った方が勝ちという通常のルールではないことを忘れていることです。 この 1 つの反転により、すべての基本ケースが反転します。 For example, when n = 1, Yvens is forced to take the last stone immediately and loses, so Esteche wins. n = 2 の場合、イヴェンスは石を 2 つ取ることができ、またすぐに負けます。 When n = 3, Yvens takes one or two stones and leaves a losing position for Esteche in both cases, so Yvens wins. 基本ケースの仮定が間違っていると、後で定期的な推論が壊れる傾向があります。 

もう 1 つの微妙なケースは、両方の手が利用可能であるものの、すぐに損失につながる値が小さい場合です。 たとえば、n = 2 は特に欺瞞的です。なぜなら、両方の正当な手がゲームを即座に終了させるためです。つまり、2 つの選択肢があるにもかかわらず、最初のプレイヤーには勝ち手がないことを意味します。 

## アプローチ

 ルールから直接推論しようとすると、それぞれの石の数を、プレイヤーが動くことによって勝ったか負けたかのゲーム状態として見ることができます。 プレイヤーは状態 n から n − 1 または n − 2 に進むことができ、これらの遷移の少なくとも 1 つが対戦相手の負け状態につながる場合、現在の状態は勝ちです。 A brute-force approach would compute this classification for all values from 1 to n using dynamic programming.

 ゲームには最適な下部構造があるため、これはきれいに機能します。つまり、ポジションの結果はより小さなポジションにのみ依存します。 ただし、ブルート フォース アプローチでは O(n) 状態と状態ごとの一定の遷移が必要なため、直線的な時間とメモリが必要になります。 With n up to 1e9, iterating over all states is impossible.

 The key observation is that the recurrence stabilizes into a repeating pattern very quickly. 小さな値を計算すると、次のことがわかります。 

n = 1 は負けです

 n = 2 は負けています

 n = 3 が勝ちます

 n = 4 が勝ちます

 n = 5 は負けています

 n = 6 は負けています

 n = 7 が勝ちです

 n = 8 が勝ちです

 The pattern repeats every 4 states: losing, losing, winning, winning. これは、ゲームが減算ゲームの変形に相当し、移動によって {1, 2} から要素が削除され、負け条件が通常の Nim 終了とは逆になるために発生します。 この構造により、合同なモジュロ 4 の位置が同じように動作するように強制されます。 

この周期性が認識されると、問題は n mod 4 を計算し、剰余を分類することになります。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース DP | O(n) | O(n) | 遅すぎる |
 | モジュラーパターン | お(1) | お(1) | 承認済み |

 ## アルゴリズムのチュートリアル

 ゲームを小さな剰余での動作に還元し、それから周期性を使用してそれをすべての n に拡張します。

1. r = n mod 4 を計算します。これにより、状態空間全体が 4 つの同値クラスの 1 つに圧縮されます。これは、遷移構造が 4 つの値ごとに繰り返されるためです。 
2. If r is 1 or 2, the position is losing for the player to move. This corresponds to situations where any move immediately hands the opponent a winning configuration.
 3. If r is 0 or 3, the position is winning for the player to move. In these cases, there exists at least one move that forces the opponent into a losing residue.
 4. Since Yvens always starts, output "Yvens" if the starting position is winning, otherwise output "Esteche".

 ### なぜ効果があるのか

 不変条件は、位置 n の結果が n mod 4 にのみ依存するということです。これは、勝利状態の漸化関係、W(n) = W(n−1) または W(n−2) ではなく、最初のいくつかの基本ケースの後で周期的なシーケンスを生成することをチェックすることによって正当化されます。 Once the cycle stabilizes, every step preserves the same structure shifted by four. Therefore, all positions in the same residue class share identical win or loss status, making the modular classification exact for all n.

 ## Python ソリューション```python
import sys
input = sys.stdin.readline

n = int(input().strip())

r = n % 4

# losing positions are 1 and 2 modulo 4
if r == 1 or r == 2:
    print("Esteche")
else:
    print("Yvens")
```The code reads the initial pile size and reduces it to its remainder modulo 4. That remainder directly determines whether the current player has a forced win. 重要な実装の詳細は、分類がすべての n に対して完全に対称であるため、ループや事前計算が必要ないことです。 

残基と結果のマッピングは、基本パターンを観察した後にハードコーディングされます。 決定は一定時間内に行われます。 

## 実用的な例

 ### Sample 1: n = 1

 r = 1 mod 4 = 1 を計算します。 

| n | r | Position type | 結果 |
 | --- | --- | --- | --- |
 | 1 | 1 | losing | Esteche |

 n = 1 から、Yvens は最後の石を取らなければなりませんが、すぐに負けるため、Esteche が勝ちます。 

### Sample 2: n = 7

 r = 7 mod 4 = 3 を計算します。 

| n | r | Position type | 結果 |
 | --- | --- | --- | --- |
 | 7 | 3 | winning | Yvens |

 n = 7 から、Yvens は 6 または 5 に移動できます。6 と 5 はどちらもポジションを失っているため、どのような動きでも Esteche は負け状態になり、Yvens の勝利が確定します。 

これら 2 つのケースは、2 つの連続する値のグループ内で勝ちブロックと負けブロックが交互に現れることを示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | お(1) | 単一のモジュロ演算と比較のみが実行されます。 
| スペース | お(1) | 補助的なデータ構造は使用されません。 

1e9 までの n に関係なく一定の作業を実行するため、この解は自明に制限内に収まります。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    import sys as _sys
    from math import *
    
    n = int(_sys.stdin.readline().strip())
    r = n % 4
    return "Esteche" if r in (1, 2) else "Yvens"

# provided samples (as inferred from statement)
assert run("1\n") == "Esteche"
assert run("3\n") == "Yvens"

# custom cases
assert run("2\n") == "Esteche"
assert run("4\n") == "Yvens"
assert run("5\n") == "Esteche"
assert run("8\n") == "Yvens"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 1 | Esteche | smallest losing state |
 | 2 | Esteche | 両方の動きはすぐに負けます。 
| 4 | イブンス | 最初の完全なサイクル境界 |
 | 5 | Esteche | 周期性のラップアラウンド |

 ## 特殊なケース

 n = 1 の場合、r = 1 になります。アルゴリズムはこれを負けとして分類するため、Esteche が出力されます。 これは、イヴェンスが最後の石を取り、すぐに負けるという強制的な動きと一致します。 

n = 2、r = 2 の場合。どちらの可能な手もゲームは即座に終了するため、開始プレイヤーは最後の石を取ることを避ける方法がありません。 モジュラー ルールは依然としてこれを損失として正しく分類します。 

n = 4、r = 0 の場合、アルゴリズムは勝ちの位置を予測します。 実際、Yvens は相手にとって負けの状態である n = 3 に移行することができ、Esteche の反応に関係なく強制勝利を確実にします。 

n = 5、r = 1 の場合、アルゴリズムはそれを負けとしてマークします。 5 からのどの手も 4 または 3 につながり、どちらも次のプレイヤーの勝ちとなるため、最適なプレイの下ではイヴェンスは敗北を避けることができません。
