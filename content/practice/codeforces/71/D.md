---
title: "CF 71D - ソリティア"
description: "トランプがいっぱい詰まった長方形のボードが与えられます。 ボードのサイズは最大でも $17 × 17 $ ですが、デッキには通常のカードが 52 枚とジョーカーが 2 枚しかないため、配置されるカードの総数が 52 枚を超えることはありません。 有効な 3×3 の正方形は、次の 2 つのプロパティのいずれかを満たさなければなりません。1."
date: "2026-05-28T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "brute-force", "implementation"]
categories: ["algorithms"]
codeforces_contest: 71
codeforces_index: "D"
codeforces_contest_name: "Codeforces Beta Round 65 (Div. 2)"
rating: 2200
weight: 71
solve_time_s: 138
verified: true
draft: false
---

[CF 71D - ソリティア](https://codeforces.com/problemset/problem/71/D)

 **評価:** 2200
 **タグ:** ブルート フォース、実装
 **解決時間:** 2 分 18 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 トランプがいっぱい詰まった長方形のボードが与えられます。 ボードのサイズは最大で$17 \times 17$, ただし、デッキには通常のカードが 52 枚とジョーカー 2 枚しか入っていないため、配置されたカードの総数が 52 枚を超えることはありません。 

有効な 3×3 正方形は、次の 2 つのプロパティのいずれかを満たさなければなりません。 

1. 9 枚のカードはすべて同じスーツです。 
2. 9 枚のカードすべてにペアごとに異なるランクがあります。 

このような正方形が 2 つ、重なり合うことなく存在する必要があります。 

ひねりはジョーカーから来ています。 入力には以下が含まれる場合があります`J1`そして`J2`、デックの 2 つの異なるジョーカーを表します。 状態を確認する前に、各ジョーカーを残りのデッキの未使用のカードと交換する場合があります。 交換用のカードは区別できるものでなければならず、すでにボード上に存在していてはなりません。 

このタスクは、解が存在するかどうかを判断するだけでなく、1 つの有効なジョーカー置換と 2 つの 3×3 正方形の座標を出力することでもあります。 

制約は十分に小さいため、ボードのポジションに対する強引な攻撃が現実的です。 可能な 3×3 の正方形の数は最大で次のとおりです。$$(17 - 2)^2 = 225$$したがって、正方形のすべてのペアをチェックするだけでも安価です。$$225^2 \approx 5 \times 10^4$$高価な部品はジョーカーの交換です。 考えられるすべてのカード割り当てを単純に検索すると、おおよそ次のような結果が得られます。$$54^2 \cdot 225^2 \approx 1.5 \times 10^8$$検証ロジックが追加されると、Python ではこれが不快になります。 

重要な観察は、各 3×3 正方形には 9 個のセルしか含まれていないということです。 正方形が有効になるかどうかは、ボードの残りの部分ではなく、それらの 9 枚のカードにのみ依存します。 これにより、すべての四角形を個別に前処理し、どのジョーカーの割り当てがそれを有効にするかを記録することができます。 

いくつかの特殊なケースは、誤って処理されやすいものです。 

ジョーカーは選択したマス目の外側にある場合があります。 その場合でも、デッキ内のすべてのカードが一意である必要があるため、その置き換えは依然として重要です。 不注意な実装では、どちらのジョーカーもスクエアに参加していないという理由だけで、両方のジョーカーに同じ置換カードが割り当てられる可能性があります。 

例えば：```
3 6
2S 3S 4S 5S 6S 7S
8S 9S TS JS QS KS
AS J1 J2 2H 3H 4H
```2 つのジョーカーは有効な正方形とは無関係ですが、それでも別個の未使用のカードを受け取る必要があります。 

もう 1 つの微妙なケースは、重複検出です。 2 つの 3×3 正方形は、1 つのセルでも共有している場合は無効です。 

たとえば、4×4 ボードでは次のようになります。```
(1,1) square covers rows 1..3 and cols 1..3
(2,2) square covers rows 2..4 and cols 2..4
```これらは 2×2 領域で重複するため、一緒に使用することはできません。 

よくあるバグは、左上隅が異なるかどうかのみをチェックすることです。 

ランク条件では、9 つ​​のランクすべてがペアごとに区別できることも必要です。 ランクが重複すると、たとえスートが異なっていたとしても、そのマスは直ちに無効になります。 

例：```
2S 3H 4D
5C 6S 7H
8D 2C 9S
```これはランクルールの下では無効です。`2`が2回出現します。 

## アプローチ

 最も直接的な解決策はブルートフォースです。 

3×3 の正方形のすべてのペアを列挙し、各ジョーカーの可能な代替をすべて列挙し、最終的なボードを構築して、両方の正方形がいずれかの条件を満たすかどうかをテストできます。 

ボードには最大 225 個の可能な正方形が含まれます。 各ジョーカーの候補カードは最大 54 枚です。 1 つの正方形のテストには常に 9 個のセルが含まれるため、一定の時間がかかります。 

これにより、次のような最悪の場合の複雑さが生じます。$$225^2 \cdot 54^2$$これは、検証のオーバーヘッドを考慮する前に、すでに 1 億以上の組み合わせになります。 Python はこれに苦戦する可能性があります。 

重要な構造上の観察は、正方形が独立しているということです。 正方形は、その 9 つのセル内のカードのみを考慮します。 したがって、最初にすべてのグローバル ジョーカー割り当てを試す代わりに、各正方形を前処理して次のように尋ねることができます。 

「この特定のマスを有効にするジョーカーの代替品はどれですか?」

 ジョーカーは最大 2 つであるため、置換の組み合わせの数はごくわずかです。 

- ジョーカーなし: まさに 1 つの状態
 - ジョーカー 1 枚: 最大 52 枚のカードが使用可能
 - ジョーカー 2 枚: 最大でも$52 \cdot 51$正方形ごとに、すべての正当なジョーカー割り当てを 1 回テストし、正方形を有効にするすべての割り当てを保存します。 

前処理を行うと、問題の解決が簡単になります。 

1. 重なり合わない正方形のペアを列挙します。 
2. 有効な割り当てセットに互換性があるかどうかを確認します。 
3. 互換性とは、両方のマスが最終的なジョーカーの置き換えに同意することを意味します。 

これにより、問題は、コストのかかる検証の繰り返しから検索の問題に変わります。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース |$O(S^2 \cdot C^2)$|$O(1)$| 遅すぎる |
 | 最適 |$O(S \cdot C^2 + S^2 \cdot A)$|$O(S \cdot A)$| 承認済み |

 ここ：

-$S \le 225$は正方形の数です
 -$C \le 54$はカード割り当ての数です
 -$A$は正方形あたりの有効な割り当ての数です

 実際には、$A$小さいです。 

## アルゴリズムのチュートリアル

 1. ボードを解析し、使用済みの通常カードをすべて収集します。 

ジョーカーは後で未使用のカードになる可能性があるため除外されます。 
2. 52 枚のカード デッキ全体から未使用のカードのリストを生成します。 

これらはジョーカーの唯一の合法的な代替品です。 
3. ボード上の 3×3 のマス目ごとに数えます。 

正方形は左上隅で識別されます。 
4. 各マス目について、考えられるすべてのジョーカーの割り当てをテストします。 

ジョーカーがいない場合、割り当ては 1 つだけです。 

ジョーカーが 1 枚ある場合は、未使用のカードをすべて試します。 

ジョーカーが 2 枚ある場合は、注文された未使用のカードのすべてのペアを試してください。 
5. 四角形内のジョーカー値を代入した後、四角形が有効かどうかをテストします。 

次のいずれかの場合、正方形は有効です。 

- すべてのスーツは同一です
 - すべてのランクが異なります
 6. 正方形を有効にするすべての割り当てを保存します。 

保存されたキーは、完全なグローバル ジョーカー割り当てです。 

-`()`ジョーカーがいない場合
 -`(card,)`ジョーカーが1人いるとしたら
 -`(card1, card2)`両方が存在する場合
 7. 正方形のペアをすべて列挙します。 

領域が重複するペアをスキップします。 
8. 重複しないペアの場合は、両方の正方形の有効な割り当てセットにジョーカー割り当てが存在するかどうかを確認します。 

ジョーカーの置き換えはグローバルであるため、両方の四角形が同じ最終割り当てに同意する必要があります。 
9. 互換性のあるペアが 1 つ見つかるとすぐに、必要な出力を印刷します。 
10. 機能するペアがない場合は、印刷します。`"No solution."`### なぜ効果があるのか

 各四角形は、法的なジョーカーの置き換えごとに評価されます。 前処理ステップでは、正方形が有効になる割り当てを正確に記録します。 

後で 2 つの正方形を組み合わせるときに、共有割り当てを要求することで、同じグローバル ジョーカー置換によって両方の正方形が同時に有効になることが保証されます。 

非オーバーラップは明示的にチェックされるため、最終的な構成は常にすべての制約を満たします。 

すべての正当な割り当てとすべての正方形のペアが検査されるため、有効な解決策が見逃されることはありません。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

RANKS = "23456789TJQKA"
SUITS = "CDHS"

ALL_CARDS = [r + s for r in RANKS for s in SUITS]

def overlap(r1, c1, r2, c2):
    return not (
        r1 + 2 < r2 or
        r2 + 2 < r1 or
        c1 + 2 < c2 or
        c2 + 2 < c1
    )

def valid_square(cells):
    suits = [x[1] for x in cells]
    if len(set(suits)) == 1:
        return True

    ranks = [x[0] for x in cells]
    return len(set(ranks)) == 9

def solve():
    n, m = map(int, input().split())
    board = [input().split() for _ in range(n)]

    jokers = []
    used = set()

    for i in range(n):
        for j in range(m):
            card = board[i][j]
            if card in ("J1", "J2"):
                jokers.append(card)
            else:
                used.add(card)

    unused = [c for c in ALL_CARDS if c not in used]

    joker_count = len(jokers)

    assignments = []

    if joker_count == 0:
        assignments.append(())
    elif joker_count == 1:
        for c in unused:
            assignments.append((c,))
    else:
        for c1 in unused:
            for c2 in unused:
                if c1 != c2:
                    assignments.append((c1, c2))

    square_info = []

    for r in range(n - 2):
        for c in range(m - 2):
            valid_assignments = set()

            for assn in assignments:
                cells = []

                ok = True

                for i in range(r, r + 3):
                    for j in range(c, c + 3):
                        card = board[i][j]

                        if card == "J1":
                            if joker_count == 1:
                                card = assn[0]
                            else:
                                card = assn[0]
                        elif card == "J2":
                            card = assn[1]

                        cells.append(card)

                if valid_square(cells):
                    valid_assignments.add(assn)

            square_info.append((r, c, valid_assignments))

    total = len(square_info)

    for i in range(total):
        r1, c1, s1 = square_info[i]

        if not s1:
            continue

        for j in range(i + 1, total):
            r2, c2, s2 = square_info[j]

            if not s2:
                continue

            if overlap(r1, c1, r2, c2):
                continue

            common = s1 & s2

            if common:
                assn = next(iter(common))

                print("Solution exists.")

                if joker_count == 0:
                    print("There are no jokers.")
                elif joker_count == 1:
                    print(f"Replace {jokers[0]} with {assn[0]}.")
                else:
                    print(f"Replace J1 with {assn[0]} and J2 with {assn[1]}.")

                print(f"Put the first square to ({r1 + 1}, {c1 + 1}).")
                print(f"Put the second square to ({r2 + 1}, {c2 + 1}).")
                return

    print("No solution.")

solve()
```実装は前処理戦略に直接に従います。`valid_square`2 つの許可されたパターンをチェックします。 スーツの同等性は、少し安いため、最初にテストされます。 ランクの一意性は単純にセットを使用します。 

の`assignments`リストは、すべての合法的なグローバル ジョーカーの代替品を表します。 両方のジョーカーが存在する場合、順序は重要です。`J1 -> A`、`J2 -> B`とは異なります`J1 -> B`、`J2 -> A`。 

各四角形には、有効な割り当てのセットが保存されます。 セットを使用すると、後で交差テストを効率的に行うことができます。 

オーバーラップテストは間違いやすいです。 2 つの 3×3 正方形は、一方がもう一方の上下左右に完全に位置する場合にのみ重なりません。 他の構成はすべて重複します。 

正方形構造内の置換ロジックは、意図的にジョーカー セルのみを置換します。 通常のカードはすべて変更されません。 

最後の検索では、単に空ではない代入交差を持つ正方形のペアが見つかります。 

## 実用的な例

 ### 例 1

 入力:```
4 6
2S 3S 4S 7S 8S AS
5H 6H 7H 5S TC AC
8H 9H TH 7C 8C 9C
2D 2C 3C 4C 5C 6C
```可能な正方形:

 | スクエア | 左上 | 有効 |
 | --- | --- | --- |
 | あ | (1,1) | いいえ |
 | B | (1,2) | いいえ |
 | C | (1,3) | いいえ |
 | D | (1,4) | いいえ |
 | E | (2,1) | いいえ |
 | ふ | (2,2) | いいえ |
 | G | (2,3) | いいえ |
 | H | (2,4) | いいえ |

 どちらのルールも満たす正方形はないため、どのペアも機能しません。 

出力：```
No solution.
```この例は、ランクの一意性には 9 つのランクすべてが正確に異なる必要があるため、視覚的に構造化された領域であっても失敗する可能性があることを示しています。 

### 例 2

 入力:```
3 6
2S 3S 4S 5H 6H 7H
5S 6S 7S 8H 9H TH
8S 9S TS J1 QH KH
```未使用のカードには多くの可能性が含まれています。 

左側の 3×3 の正方形には次のものが含まれます。 

| 2S | 3S | 4S |
 | --- | --- | --- |
 | 5S | 6S | 7S |
 | 8S | 9S | TS |

 すべてのスーツは、`S`, したがって、正方形はすぐに有効になります。 

右側の 3×3 の正方形にはランクが含まれます。 

| H | H | H |
 | --- | --- | --- |
 | H | H | H |
 | J1 | H | H |

 交換中`J1`と`AH`すべてのランクを区別します:$$5,6,7,8,9,T,A,Q,K$$アルゴリズムは、この割り当てを 2 番目の正方形に対して有効なものとして記録し、互換性のあるペアを見つけます。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |$O(S \cdot C^2 + S^2 \cdot A)$| すべての正方形と割り当てを前処理してから、正方形のペアをテストします。 
| スペース |$O(S \cdot A)$| 各正方形の有効な割り当てを保存します。 

最大 225 個の正方形と最大数千のジョーカー割り当てにより、この解は容易に制限内に収まります。 

## テストケース```python
# helper: run solution on input string, return output string
import sys
import io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)

    out = io.StringIO()
    sys.stdout = out

    RANKS = "23456789TJQKA"
    SUITS = "CDHS"

    ALL_CARDS = [r + s for r in RANKS for s in SUITS]

    def overlap(r1, c1, r2, c2):
        return not (
            r1 + 2 < r2 or
            r2 + 2 < r1 or
            c1 + 2 < c2 or
            c2 + 2 < c1
        )

    def valid_square(cells):
        suits = [x[1] for x in cells]
        if len(set(suits)) == 1:
            return True

        ranks = [x[0] for x in cells]
        return len(set(ranks)) == 9

    input = sys.stdin.readline

    n, m = map(int, input().split())
    board = [input().split() for _ in range(n)]

    jokers = []
    used = set()

    for i in range(n):
        for j in range(m):
            card = board[i][j]
            if card in ("J1", "J2"):
                jokers.append(card)
            else:
                used.add(card)

    unused = [c for c in ALL_CARDS if c not in used]

    joker_count = len(jokers)

    assignments = []

    if joker_count == 0:
        assignments.append(())
    elif joker_count == 1:
        for c in unused:
            assignments.append((c,))
    else:
        for c1 in unused:
            for c2 in unused:
                if c1 != c2:
                    assignments.append((c1, c2))

    square_info = []

    for r in range(n - 2):
        for c in range(m - 2):
            valid_assignments = set()

            for assn in assignments:
                cells = []

                for i in range(r, r + 3):
                    for j in range(c, c + 3):
                        card = board[i][j]

                        if card == "J1":
                            card = assn[0]
                        elif card == "J2":
                            card = assn[1]

                        cells.append(card)

                if valid_square(cells):
                    valid_assignments.add(assn)

            square_info.append((r, c, valid_assignments))

    total = len(square_info)

    for i in range(total):
        r1, c1, s1 = square_info[i]

        for j in range(i + 1, total):
            r2, c2, s2 = square_info[j]

            if overlap(r1, c1, r2, c2):
                continue

            if s1 & s2:
                print("Solution exists.")
                return out.getvalue()

    print("No solution.")
    return out.getvalue()

# provided sample
assert run(
"""4 6
2S 3S 4S 7S 8S AS
5H 6H 7H 5S TC AC
8H 9H TH 7C 8C 9C
2D 2C 3C 4C 5C 6C
"""
).strip() == "No solution.", "sample 1"

# minimum board
assert "No solution." in run(
"""3 3
2S 3S 4S
5S 6S 7S
8S 9S TS
"""
), "single square cannot form two non-overlapping squares"

# simple positive case
assert "Solution exists." in run(
"""3 6
2S 3S 4S 2H 3H 4H
5S 6S 7S 5H 6H 7H
8S 9S TS 8H 9H TH
"""
), "two disjoint suit squares"

# overlap trap
assert "No solution." in run(
"""4 4
2S 3S 4S 5S
6S 7S 8S 9S
TS JS QS KS
AH 2H 3H 4H
"""
), "overlapping valid squares are forbidden"

# joker replacement
assert "Solution exists." in run(
"""3 6
2S 3S 4S 5H 6H 7H
5S 6S 7S 8H 9H TH
8S 9S TS J1 QH KH
"""
), "joker creates distinct ranks"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 3×3 の単一の有効な正方形 | 解決策はありません | 重なり合わない 2 つの正方形が必要 |
 | 2 つの水平スーツ正方形 | 写真 2 つの水平スーツ正方形 解決策は存在します | 基本的なポジティブなケース |
 | 4×4の重複候補 | 解決策はありません | 正しい重なり検出 |
 | ジョーカーの修理 | ワンジョーカー 解決策は存在します | ジョーカー置換ロジック |

 ## 特殊なケース

 重複の問題をもう一度考えてみましょう。 

入力：```
4 4
2S 3S 4S 5S
6S 7S 8S 9S
TS JS QS KS
AH 2H 3H 4H
```の広場`(1,1)`すべてのスートがスペードであるため、これは有効です。 

の広場`(2,2)`こちらも有望に見えます:```
7S 8S 9S
JS QS KS
2H 3H 4H
```バグのある実装では、左上の座標が異なるために両方を受け入れる可能性があります。 

アルゴリズムはそれらを正しく拒否します。`2..3`とコラム`2..3`重なり合う。 

もう 1 つの注意が必要なケースには、ランクの重複が関係します。 

入力：```
3 3
2S 3H 4D
5C 6S 7H
8D 2C 9S
```スーツは均一ではありません。 

ランクは次のとおりです。```
2 3 4
5 6 7
8 2 9
```ランク`2`が 2 回表示されるため、四角形は無効になります。 

アルゴリズムが使用するのは、`len(set(ranks)) == 9`、これをすぐにキャッチします。 

最後に、アクティブな正方形の外側にあるジョーカーについて考えてみましょう。 

入力：```
3 6
2S 3S 4S 5S 6S 7S
8S 9S TS JS QS KS
AS J1 J2 2H 3H 4H
```左側の正方形はすでにスーツの条件を満たしています。 ジョーカーはそれとは無関係です。 

不注意な解決策では、検査されないため、両方のジョーカーに同じ未使用カードが割り当てられる可能性があります。 

このアルゴリズムは、順序付けられた個別のペアのみを使用して割り当てを生成し、デッキの一貫性をグローバルに保証します。
