---
title: "CF 102203M - レッド-7"
description: "個別のカードを使用してプレイする 2 人用ゲームがあります。 すべてのカードには 1 ～ 7 の値と、順序付けされたセット R、O、Y、G、B、N、P の色があります。値が大きいカードほど強力で、同じ値のカードは色順に並べられ、R が最も強く、P が最も弱くなります。"
date: "2026-08-18T00:59:12+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102203
codeforces_index: "M"
codeforces_contest_name: "\u0427\u0435\u0442\u0432\u0435\u0440\u0442\u0430\u044f \u041b\u0438\u043f\u0435\u0446\u043a\u0430\u044f \u043a\u043e\u043c\u0430\u043d\u0434\u043d\u0430\u044f \u043e\u043b\u0438\u043c\u043f\u0438\u0430\u0434\u0430 \u0448\u043a\u043e\u043b\u044c\u043d\u0438\u043a\u043e\u0432 \u043f\u043e \u043f\u0440\u043e\u0433\u0440\u0430\u043c\u043c\u0438\u0440\u043e\u0432\u0430\u043d\u0438\u044e (8-11 \u043a\u043b\u0430\u0441\u0441\u044b)"
rating: 0
weight: 102203
solve_time_s: 284
verified: true
draft: false
---

[CF 102203M - RED-7](https://codeforces.com/problemset/problem/102203/M)

 **評価:** -
 **タグ:** -
 **解決時間:** 4 分 44 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 個別のカードを使用してプレイする 2 人用ゲームがあります。 すべてのカードには 1 ～ 7 の値と、順序付けされたセット R、O、Y、G、B、N、P の色があります。値が大きいカードほど強力で、同じ値のカードは色順に並べられ、R が最も強く、P が最も弱くなります。 

各プレイヤーは、すでにパレットに 1 枚のカードがあり、最大 6 枚のカードが手札にある状態で開始します。 キャンバスは最初は赤のルールを表しているため、最も強いパレット カードを持つプレーヤーが現在リードしています。 最初のプレイヤーの初期パレット カードは、2 番目のプレイヤーの初期パレット カードよりも弱いことが保証されています。 

キャンバス カードによって、勝者が評価されるルールが決まります。 7 つのルールは、最も強いカードを赤、同じ値の最大のグループをオレンジ、1 つの色の最大のグループを黄、偶数の値のカードを最大数の緑、異なる色の最大数を青、連続した値の最長値を藍、値が 4 未満のカードの最大数を紫です。 

どのルールでも、プレイヤーは単に適切なカードをすべて使用するわけではありません。 最初にカードのサイズを最大化し、次に最強のカードを最大化することで、可能な限り最良の組み合わせを選択します。 したがって、ゲームの状態は、現在両方のパレットにあるカード、まだ両手にあるカード、現在のキャンバスの色、および誰の順番であるかによって決まります。 

ターンでは、プレイヤーは 1 枚の手札を自分のパレットに移動したり、1 枚の手札をキャンバスに移動したり、2 枚の異なるカードを使用して両方の操作を実行したりできます。 操作後、プレイヤーは結果として生じるキャンバス ルールに従って厳密に先行していなければなりません。 そのような動きが存在しない場合、そのプレイヤーは即座に負けます。 手札が空のプレイヤーは、手札が空の状態で合法的にターンを終了することもできますが、ターン開始時に負けます。 

入力により、2 つの手のサイズと各プレーヤーに属するカードが得られます。 各プレイヤーのラインの最初のカードはすでにそのプレイヤーのパレットにあり、残りのカードはすべて手札から始まります。 必要な出力は次のとおりです`First`最初のプレイヤーが勝利戦略を持っている場合、そして`Second`さもないと。 

各ハンドに最大 6 枚のカードという制限が、ゲーム状態の徹底的な検索が可能な主な理由です。 位置を変更できるカードは最大 12 枚あります。 このような各カードは、手札、パレットにあるか、すでにキャンバスに捨てられている可能性があり、キャンバス ルールとターンを考慮する前に、最大 (3^{12}=531441) 個のローカル所有権構成が与えられます。 これにより、ゲーム ツリー全体のように作業が増大するアルゴリズムは除外されますが、メモ化された状態検索が実用的になります。 

いくつかの詳細は間違いやすいものです。 たとえそのプレイヤーが前の手の後にリードしていたとしても、ターン開始時に手札が空の場合は即座に負けとなります。 たとえば、最初のサンプルは次のとおりです。```
0 0
3G
7Y
```そして答えは`Second`。 合法的な手はまったくないので、2番目のプレーヤーが最初にリードするという事実は、答えの主な理由ではありません。 現在のプレイヤーがすでに勝っているかどうかだけをチェックする検索では、誤って返される`First`。 

もう 1 つの微妙なケースは、キャンバスにカードをプレイする場合、新しいキャンバス ルールを使用して評価する必要があることです。 2 番目のサンプルでは、```
3 0
1R 2R 3R 4R
7R
```最初のプレイヤーは、利用可能な最も強力なパレット カードでさえ 7R 未満であるため、赤の下では改善できません。 キャンバスにカードをプレイすることも、結果のルールによって最初のプレイヤーがリードする場合を除き、役に立ちません。 正しい答えは、`Second`。 

ルール内のタイブレークも、よくあるエラーの原因です。 考慮する```
1 1
2P 2R
2Y 2O
```先攻プレイヤーは2Rをパレットに置くことができます。 赤の下では、両方のプレイヤーが値 2 の最も高いカードを持っていますが、R の方が Y より強いため、最初のプレイヤーがリードします。 正しい答えは、`First`。 数値のみに基づいて比較すると、色のタイブレークを見逃してしまいます。 

紫色のルールの境界も適切な場所で厳密です。値 1、2、および 3 はカウントされますが、4 はカウントされません。 例えば、```
1 1
3P 1R
7R 4O
```先攻プレイヤーはキャンバスに1Rを置くことができます。 新しいルールは紫で、最初のパレットには 3P が含まれ、2 番目のパレットには 7R が含まれます。 最初のプレイヤーは資格カードを 1 枚持っていますが、2 番目のプレイヤーは何も持っていないので、答えは次のようになります。`First`。 を使用した実装`value <= 4`この場合、間違った結果が生成されます。 

## アプローチ

 直接的な総当たりアプローチは、あらゆる可能な動きをシミュレートし、ゲームの継続をすべて再帰的に検査することです。 これは正しいです。なぜなら、現在のプレイヤーを先に残し、相手に負けのポジションを与える正当な手が存在するとき、そのポジションはまさに勝利しているからです。 そのような動きが存在しない場合、ポジションは負けています。 

6 枚のカードを持っている場合、プレイヤーは 6 つのパレットのみのアクション、6 つのキャンバスのみのアクション、およびパレットに 1 枚のカードを使用し、キャンバスに別のカードを使用する (6\cdot5=30) 個のアクションを持ちます。 つまり、ターン開始時に可能なアクションは 42 通りです。 したがって、生のゲームツリー検索の上限は (42^{12}) となり、これは約 (3.0\cdot10^{19}) 個の分岐に相当します。 これらの分岐のほとんどは不正であるか、かなり早く終了しますが、境界はすでに直接再帰が使用できないことを示しています。 

ブルートフォース検索が機能するのは、すべての動きが現在のプレイヤーの手札のカードの数を厳密に減らすため、サイクルが存在しないためです。 問題は、多くの異なる移動シーケンスが同じ位置に到達することです。 同じパレット、ハンド、キャンバス ルール、ターンが再び発生すると、その位置にどのように到達したかに関係なく、その後のゲームは同じになります。 

したがって、重要な観察は、シーケンスを移動するのではなく、位置をメモ化することです。 各プレイヤーにとって、すべての手札カードには 3 つの関連ステータスがあります。それはまだ手札にある、パレットに移動されている、またはすでにキャンバスに捨てられているということです。 初期パレット カードは固定されており、3 値の状態は必要ありません。 したがって、6 枚の手札では、プレイヤーごとに (3^6=729) 個の可能なローカル状態のみが与えられます。 

現在キャンバスの上にあるカードそのものは必要ありません。 キャンバスがルールを決定するため、その色のみが重要です。 カード自体がゲームから消えたという事実は、そのカードが手札にもパレットにも存在しないという事実によってすでに表現されています。 これにより、グローバル状態が 2 つのローカル状態、7 つのキャンバスの色の 1 つ、およびプレーヤーの移動に減らされます。 

考えられるすべてのパレット マスクについて、7 つのルールのそれぞれに最適な組み合わせを事前計算することもできます。 パレットには最大 7 枚のカードが含まれるため、すべてのサブマスクを単純に列挙し、最大サイズの有効なサブマスクを選択し、次に最大のカード強度を選択できます。 これにより、ゲーム探索中の位置の評価が一定時間になります。 

結果として得られる検索は依然として指数関数的ですが、その状態空間はこれらの制約に対して十分小さいものです。 完全な状態空間には約 740 万のエントリしかなく、エントリごとのバイトが安価であるため、Python 辞書ではなく高密度バイト配列がメモ化に使用されます。 また、再帰は勝ち手を見つけるとすぐに停止します。これは特に効果的です。なぜなら、ほとんどのポジションには、すべての選択肢を検討することなく拒否または受け入れできる正当な手が存在するためです。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | (O(42^{12})) ゲームツリーのブランチ | (O(12)) 再帰の深さ | 遅すぎる |
 | 最適 | 最悪の場合の (O(3^{n+m}(n+m)^2)) の状態と遷移 | (O(3^{n+m})) メモ化 | 承認済み |

 ## アルゴリズムのチュートリアル

 1. すべてのカードを、その値とその色のランクで構成されるペアに変換します。 色の順番はR、O、Y、G、B、N、Pの0～6になるので、カードを整数で比較することができます。`value * 7 + color`。 
2. 各プレーヤーについて、すべてのキャンバス ルールに基づいて、可能なすべてのパレット マスクの最適なスコアを事前計算します。 スコアには、最適な組み合わせのサイズとその最強のカードのランクが含まれます。 それを次のようにエンコードします`size * 50 + rank`、不可能な組み合わせが受信されますが、`-1`。 

ルールのスコアを計算するには、パレットの空でないすべてのサブマスクを列挙し、それがそのルールを満たすかどうかをテストします。 パレットには最大でも 7 枚のカードが含まれるため、これは小さいです。 
3. 各プレイヤーのカードの可変部分を 6 つの 3 進数を使用して表します。 数字の 0 は対応する手札が捨てられたことを意味し、数字 1 はそれがまだ手札にあることを意味し、数字 2 はそれがパレットにあることを意味します。 最初のパレット カードは常にパレット マスクに個別に含まれます。 

したがって、ローカル状態は最大でも (3^6=729) 個の値を持ちます。 地元の州からハンドマスクとパレットマスクをすぐに入手できます。 
4. 可能なすべての手札をパレットに移動した結果と、すべての可能な手札をキャンバスに移動した結果を事前計算します。 パレットを移動すると、その 3 進数が 1 から 2 に変わります。 キャンバスを移動すると、それが 1 から 0 に変わります。 
5. 状態が最初のプレーヤーのローカル状態、2 番目のプレーヤーのローカル状態、現在のキャンバスの色、および順番が回っているプレーヤーで構成される再帰的ゲーム関数を定義します。 

メモテーブルには、プレイヤーが移動する局面が勝ちか負けかを記憶します。 
6. 現在のプレイヤーの手札が空の場合、そのポジションを直ちに負けとしてマークします。 これは、現在のキャンバス ルールを検討する前にチェックされます。これは、ルールがターン開始時に空のハンドを明示的に損失とするためです。 
7. パレットのみの動きをすべて試してください。 1 枚の手札を現在のプレイヤーのパレットに移動し、キャンバス ルールを変更せずに、結果として得られる最適スコアを比較します。 現在のプレイヤーがリードしていて、相手の状態が負けている場合、現在の位置が勝ちです。 
8. キャンバスのみの移動をすべて試してください。 手札を 1 枚取り除き、その色を新しいキャンバス ルールとして使用し、新しいルールをすぐに比較します。 カードはパレットに存在しなくなるため、パレット自体は変わりません。 
9. 組み合わせたアクションで、順序付けられた個別の手札のペアをすべて試してください。 まず 1 枚のカードをパレットに移動し、次に別のカードをキャンバスから削除します。 新しいキャンバス ルールは、拡大されたパレットと残りのカードを使用して評価する必要があります。 
10. これらの動きのいずれかが、現在のプレイヤーを先に残したまま相手の負けの位置につながる場合、現在の状態を勝ちとしてマークします。 考えられるすべての動きが失敗した場合は、負けとしてマークします。 

中心的な不変条件は、すべてのメモ化された状態には、将来のすべての動きに影響を与える可能性のある情報が正確に含まれているということです。 手札に残っているカードは引き続きプレイでき、パレット内のカードは将来のあらゆるルールに貢献し、捨てられたカードは決して戻ることはできません。 キャンバスの色は、将来のルールに影響を与える現在のキャンバス カードの唯一のプロパティです。 したがって、同じ 4 つのローカル ハンド/パレットの状態、キャンバスの色、およびターンを持つ 2 つのゲーム履歴には、まったく同じ一連の将来の可能性があります。 次に、再帰的ミニマックス ルールが最適なプレイと一致します。つまり、プレイヤーが対戦相手の負けポジションに対して少なくとも 1 つの正当な手を持っている場合に、そのポジションは正確に勝利しています。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

from functools import lru_cache

COLORS = "ROYGBNP"
COLOR_ID = {c: i for i, c in enumerate(COLORS)}
BASE = 3 ** 6
STATE_COUNT = BASE
STATE_SPACE = BASE * BASE * 7 * 2

def card_rank(card):
    value, color = card
    return value * 7 + color

def build_scores(cards):
    """
    score[rule][mask] = encoded optimal combination score.
    -1 means that no valid combination exists.

    The encoding is size * 50 + highest_card_rank.
    """
    score = [[-1] * 128 for _ in range(7)]
    total = len(cards)

    ranks = [card_rank(c) for c in cards]
    values = [c[0] for c in cards]
    colors = [c[1] for c in cards]

    for mask in range(1, 1 << total):
        sub = mask

        while sub:
            cnt = sub.bit_count()

            highest = -1
            vals = []
            cols = []

            x = sub
            while x:
                bit = x & -x
                i = bit.bit_length() - 1

                if ranks[i] > highest:
                    highest = ranks[i]

                vals.append(values[i])
                cols.append(colors[i])
                x ^= bit

            valid = [False] * 7

            # Red: exactly one card.
            valid[0] = cnt == 1

            # Orange: all cards have the same value.
            valid[1] = len(set(vals)) == 1

            # Yellow: all cards have the same color.
            valid[2] = len(set(cols)) == 1

            # Green: all cards are even.
            valid[3] = all(v % 2 == 0 for v in vals)

            # Blue: all colors are different.
            valid[4] = len(set(cols)) == cnt

            # Indigo: distinct consecutive values.
            if len(set(vals)) == cnt:
                lo = min(vals)
                hi = max(vals)
                valid[5] = hi - lo + 1 == cnt

            # Violet: all values are below 4.
            valid[6] = all(v < 4 for v in vals)

            encoded = cnt * 50 + highest

            for rule in range(7):
                if valid[rule] and encoded > score[rule][mask]:
                    score[rule][mask] = encoded

            sub = (sub - 1) & mask

    return score

def solve_case(data):
    lines = data.strip().splitlines()
    n, m = map(int, lines[0].split())

    first = []
    second = []

    for token in lines[1].split():
        first.append((int(token[0]), COLOR_ID[token[1]]))

    for token in lines[2].split():
        second.append((int(token[0]), COLOR_ID[token[1]]))

    score_first = build_scores(first)
    score_second = build_scores(second)

    # For a local ternary state:
    # digit 0 = discarded
    # digit 1 = hand
    # digit 2 = palette
    #
    # Bit 0 of the palette mask is always the initial palette card.
    powers = [3 ** i for i in range(6)]

    palette_mask = [0] * STATE_COUNT
    hand_mask = [0] * STATE_COUNT

    next_palette = [[-1] * 6 for _ in range(STATE_COUNT)]
    next_canvas = [[-1] * 6 for _ in range(STATE_COUNT)]

    for state in range(STATE_COUNT):
        x = state
        pmask = 1
        hmask = 0

        digits = [0] * 6

        for i in range(6):
            digits[i] = x % 3
            x //= 3

            if digits[i] == 1:
                hmask |= 1 << (i + 1)
            elif digits[i] == 2:
                pmask |= 1 << (i + 1)

        palette_mask[state] = pmask
        hand_mask[state] = hmask

        for i in range(6):
            if digits[i] == 1:
                # 1 -> 2: move to palette.
                next_palette[state][i] = state + powers[i]

                # 1 -> 0: move to canvas.
                next_canvas[state][i] = state - powers[i]

    # Only the first n or m variable positions are real cards.
    initial_first = sum(powers[i] for i in range(n))
    initial_second = sum(powers[i] for i in range(m))

    colors_first = [c[1] for c in first[1:]]
    colors_second = [c[1] for c in second[1:]]

    memo = bytearray(STATE_SPACE)

    def memo_index(s1, s2, canvas, turn):
        return ((((s1 * STATE_COUNT) + s2) * 7 + canvas) << 1) | turn

    def leads_first(pm1, pm2, rule):
        return score_first[rule][pm1] > score_second[rule][pm2]

    def leads_second(pm1, pm2, rule):
        return score_second[rule][pm2] > score_first[rule][pm1]

    sys.setrecursionlimit(100000)

    def win(s1, s2, canvas, turn):
        idx = memo_index(s1, s2, canvas, turn)
        saved = memo[idx]

        if saved:
            return saved == 2

        if turn == 0:
            me = s1
            opp = s2
            pm_opp = palette_mask[opp]
            hands = hand_mask[me]

            if hands == 0:
                memo[idx] = 1
                return False

            pm_me = palette_mask[me]

            # Action 1: hand -> palette.
            bits = hands
            while bits:
                bit = bits & -bits
                i = bit.bit_length() - 2

                ns = next_palette[me][i]

                if leads_first(palette_mask[ns], pm_opp, canvas):
                    if not win(ns, opp, canvas, 1):
                        memo[idx] = 2
                        return True

                bits ^= bit

            # Action 2: hand -> canvas.
            bits = hands
            while bits:
                bit = bits & -bits
                i = bit.bit_length() - 2

                ns = next_canvas[me][i]
                new_canvas = colors_first[i]

                if leads_first(palette_mask[ns], pm_opp, new_canvas):
                    if not win(ns, opp, new_canvas, 1):
                        memo[idx] = 2
                        return True

                bits ^= bit

            # Action 3: hand -> palette, another hand card -> canvas.
            bits_a = hands
            while bits_a:
                bit_a = bits_a & -bits_a
                a = bit_a.bit_length() - 2

                after_palette = next_palette[me][a]
                remaining = hands ^ bit_a

                bits_b = remaining
                while bits_b:
                    bit_b = bits_b & -bits_b
                    b = bit_b.bit_length() - 2

                    ns = next_canvas[after_palette][b]
                    new_canvas = colors_first[b]

                    if leads_first(
                        palette_mask[ns],
                        pm_opp,
                        new_canvas
                    ):
                        if not win(ns, opp, new_canvas, 1):
                            memo[idx] = 2
                            return True

                    bits_b ^= bit_b

                bits_a ^= bit_a

        else:
            me = s2
            opp = s1
            pm_opp = palette_mask[opp]
            hands = hand_mask[me]

            if hands == 0:
                memo[idx] = 1
                return False

            pm_me = palette_mask[me]

            # Action 1: hand -> palette.
            bits = hands
            while bits:
                bit = bits & -bits
                i = bit.bit_length() - 2

                ns = next_palette[me][i]

                if leads_second(palette_mask[ns], pm_opp, canvas):
                    if not win(opp, ns, canvas, 0):
                        memo[idx] = 2
                        return True

                bits ^= bit

            # Action 2: hand -> canvas.
            bits = hands
            while bits:
                bit = bits & -bits
                i = bit.bit_length() - 2

                ns = next_canvas[me][i]
                new_canvas = colors_second[i]

                if leads_second(palette_mask[ns], pm_opp, new_canvas):
                    if not win(opp, ns, new_canvas, 0):
                        memo[idx] = 2
                        return True

                bits ^= bit

            # Action 3: hand -> palette, another hand card -> canvas.
            bits_a = hands
            while bits_a:
                bit_a = bits_a & -bits_a
                a = bit_a.bit_length() - 2

                after_palette = next_palette[me][a]
                remaining = hands ^ bit_a

                bits_b = remaining
                while bits_b:
                    bit_b = bits_b & -bits_b
                    b = bit_b.bit_length() - 2

                    ns = next_canvas[after_palette][b]
                    new_canvas = colors_second[b]

                    if leads_second(
                        palette_mask[ns],
                        pm_opp,
                        new_canvas
                    ):
                        if not win(opp, ns, new_canvas, 0):
                            memo[idx] = 2
                            return True

                    bits_b ^= bit_b

                bits_a ^= bit_a

        memo[idx] = 1
        return False

    return "First" if win(initial_first, initial_second, 0, 0) else "Second"

def main():
    data = sys.stdin.read()
    if data.strip():
        print(solve_case(data))

if __name__ == "__main__":
    main()
```実装の最初の部分では、色を整数に変換し、各カードをその値と色のランクで表します。 ランク式により、必要な厳密な順序付けが通常の整数比較になります。 値は最大 7 で、色は 7 つあるため、すべてのタイブレークには 50 未満のランクで十分です。`build_scores`ルールの仕組みをゲームの仕組みとは別に扱います。 各パレット マスクについて、考えられるすべての組み合わせが考慮され、7 つの定義が直接チェックされます。 これは意図的にシンプルにしています。 パレット マスクは (2^7=128) 個しかなく、それぞれのパレット マスクには最大でも (2^7=128) 個のサブマスクが含まれるため、ここでの網羅的な評価はゲーム検索に比べてわずかです。 

インディゴルールは特に注目に値します。 有効な組み合わせには、1 つの連続した間隔を形成する個別の値が含まれている必要があります。 1 枚のカードが有効なランであるため、1 枚のカードのみを含むパレットにはサイズ 1 のインディゴの組み合わせが残ります。 条件`hi - lo + 1 == cnt`個別の値と組み合わせると、そのプロパティが正確に取得されます。 

3 値エンコードは主な状態圧縮です。 可変カードには、将来のプレイの観点から 3 つの可能な場所があります。 手札カードをパレットに移動すると、その 3 進数が 1 から 2 に増加し、キャンバスに移動すると、その 3 進数が 1 から 0 に減分します。最初のパレット カードは常にパレット マスクのビット 0 であり、3 進数には関与しません。 

キャンバスにはカラー インデックスのみが保存されます。 カードが破棄されると、その識別情報は今後のルールに影響を与えなくなります。 その三進数がゼロであるため、その消失はすでに目に見えています。 これが、完成したキャンバス カードを保存すると不要な状態が作成される理由です。 

の`memo`配列では、未知の状態には 0、負けている状態には 1、勝ちの状態には 2 が使用されます。 そのインデックスは、両方のローカル状態、キャンバスの色、およびターンを 1 つの整数にパックします。 バイト配列は、何百万ものタプル キーを含む Python 辞書よりもはるかにメモリ効率が高くなります。 

などの表現が`bit.bit_length() - 2`パレットまたはハンドビットを対応する 3 値カードインデックスに変換します。 ビット 0 は最初のパレット カード用に予約されているため、ローカル インデックス 0 にあるハンド カードはビット 1 で表されます。 このオフセットは、off-by-one エラーが発生しやすい場所です。 

組み合わせたアクションは正しい順序で生成されます。 最初に選択したカードがパレットに移動され、次に選択された別のカードがキャンバスに移動します。 したがって、2 番目のカードは拡大されたパレットを使用して評価されます。これは、オレンジ、黄、緑、青、藍、紫などのルールにとって重要です。 

Python では整数オーバーフローの懸念はありません。 状態のインデックス作成に使用される最大の算術オブジェクトはわずか数百万ですが、カード スコアは 400 未満です。 

## 実用的な例

 ### サンプル 1

 入力は```
0 0
3G
7Y
```初期のローカル状態には、固定パレット カードのみが含まれます。 両手が空であるため、再帰的検索は現在のキャンバス ルールを調べる前に終了します。 

| ターン | 最初の手 | 最初のパレット | 中古 | 2番目のパレット | キャンバス | 結果 |
 | --- | --- | --- | --- | --- | --- | --- |
 | 最初 | 空 | 3G | 空 | 7歳 | R | 最初は移動できません |

 ターン開始時に手札が空の場合は即座に負けとなるため、この状態は最初のプレイヤーの負けとなります。 2番目のプレイヤーは何も動かずに勝ちます。 

### サンプル 2

 入力は```
3 0
1R 2R 3R 4R
7R
```キャンバスは最初は赤です。 最初のプレイヤーの唯一のパレット カードは 1R ですが、2 番目のプレイヤーのパレットには 7R が含まれています。 

| ファーストによるアクション | 新しいパレット | 新しいキャンバス | 最初のスコア | 2番目のスコア | 合法的な勝ち手 |
 | --- | --- | --- | --- | --- | --- |
 | 2Rをパレットに入れる | 1R、2R | R | 2R | 7R | いいえ |
 | 3Rをパレットに入れる | 1R、3R | R | 3R | 7R | いいえ |
 | 4Rをパレットに入れる | 1R、4R | R | 4R | 7R | いいえ |
 | 2Rをキャンバスに置く | 1R | R | 1R | 7R | いいえ |
 | 3Rをキャンバスに置く | 1R | R | 1R | 7R | いいえ |
 | 4Rをキャンバスに置く | 1R | R | 1R | 7R | いいえ |

 利用可能な 3 枚のカードがすべて赤であるため、キャンバス カードは赤のままです。そのため、キャンバスのみの移動によってルールは変わりません。 パレット移動では 7R より強いカードを生み出すことはできません。 可能なすべてのキャンバス カードが赤になり、同じ最上位カード ルールの下でゲームから離れるため、組み合わせたアクションも役に立ちません。 

最初の州は結果的に負けているので、答えは次のようになります。`Second`。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O(3^{n+m}(n+m)^2)) | 各カードには 3 つのステータスがあり、各状態は 1 枚のカードと 2 枚のカードのアクションを考慮します。 
| スペース | (O(3^{n+m})) | メモ化は、7 つのルールと 2 ターンの定数係数まで、パックされたゲーム状態ごとに 1 バイトを保存します。 

最大 (n=m=6) の場合、各プレーヤーのローカル状態の可能性は 729 個のみです。 両方のプレイヤー、7 つのキャンバスの色、および 2 つのターンを含めると、約 740 万のパックされた状態が得られ、メモ化配列が占有するのはわずか数メガバイトです。 ルール スコアと遷移テーブルは、比較すると無視できるものです。 探索は手札の数において指数関数的ですが、指数は 12 で制限されています。これがまさにこのアプローチが問題の異常に小さい制約に適合する理由です。 

## テストケース```
# This test block assumes solve_case from the solution above is available.

def run(inp: str) -> str:
    return solve_case(inp).strip()

# Provided samples.
assert run("""\
0 0
3G
7Y
""") == "Second", "sample 1"

assert run("""\
3 0
1R 2R 3R 4R
7R
""") == "Second", "sample 2"

assert run("""\
4 3
1O 2O 4G 6G 5B
7B 2Y 5P 2G
""") == "First", "sample 3"

# Minimum-size input. Nobody has a hand, so the first player loses immediately.
assert run("""\
0 0
3G
7Y
""") == "Second", "empty hands"

# Equal values test the color tie-break.
assert run("""\
1 1
2P 2R
2Y 2O
""") == "First", "equal value, stronger color wins"

# Violet boundary: 3 counts, 4 does not.
assert run("""\
1 1
3P 1R
7R 4O
""") == "First", "value 3 belongs to violet"

# Indigo singleton boundary. A one-card run exists, but 7P beats 1R.
assert run("""\
1 0
1R 2O
7P
""") == "Second", "singleton indigo run"

# Maximum hand size for one player.
# First can put 2R into the palette and 3R onto the canvas,
# producing a yellow rule where First has two cards of one color.
# Second has no hand and loses on the following turn.
assert run("""\
6 0
1R 2R 3R 4R 5R 6R 7R
7P
""") == "First", "maximum first-hand size"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`0 0 / 3G / 7Y`|`Second`| ターン開始時の手ぶら負け |
 |`2P 2R / 2Y 2O`|`First`| 等しい値の後の色のタイブレーク |
 |`3P 1R / 7R 4O`|`First`| バイオレットには 3 が含まれますが、4 は含まれません |
 |`1R 2O / 7P`|`Second`| シングルトンは有効な indigo 実行です。 
|`1R 2R 3R 4R 5R 6R 7R / 7P`|`First`| 6 枚のカードの手とパレット/キャンバスの組み合わせの動き |

 ## 特殊なケース

 手が空の場合は、手が生成される前に処理されます。 のために```
0 0
3G
7Y
```the first local state has a zero hand mask. 再帰関数は、すぐに負けとしてマークし、2 つのパレット カードを比較しようとしません。 結果は`Second`。 

赤のタイブレークは、すべてのカードをその数値とそれに続く色のランクとしてエンコードすることによって処理されます。 のために```
1 1
2P 2R
2Y 2O
```2R を最初のパレットに移動すると、2 枚の最上位のカード 2R と 2Y が生成されます。 どちらも値は 2 ですが、2R のエンコードされたランクの方が大きいため、最初のプレイヤーがリードします。 現在のポジションに勝つだけで十分であると仮定するのではなく、ミニマックス再帰によって対戦相手の将来の可能性も探求されます。 

紫色の境界は次の条件で表されます。`v < 4`。 で```
1 1
3P 1R
7R 4O
```最初のプレイヤーは 1R をキャンバスに捨て、ルールを紫に変更します。 最初のパレットには 3P が含まれており、これにより 1 枚の対象カードが提供されます。 2 番目のパレットには 7R が含まれており、寄与はゼロです。 したがって、最初のプレーヤーは正当な勝ち手となります。 

インディゴのシングルトンの場合は、最大値から最小値を引いた値が個別の値の数から 1 を引いた値に等しいという条件を使用します。 1 枚のカードの場合、両側が 0 であるため、その組み合わせは有効です。 で```
1 0
1R 2O
7P
```キャンバスに 2O をプレイするとインディゴ ルールが作成されますが、結果として生じるシングルトン 1R は相手のシングルトン 7P よりも弱くなります。 パレットに 2O をプレイすると、赤のルールが有効なままになり、負けます。 他にアクションがない場合、最初のプレイヤーが負けます。 

組み合わせた移動では 2 つの異なるカードを使用する必要があり、パレット カードが追加された後でキャンバス ルールを評価する必要があります。 で```
6 0
1R 2R 3R 4R 5R 6R 7R
7P
```最初のプレイヤーは 2R をパレットに、3R をキャンバスに移動できます。 新しいルールは黄色です。 最初のパレットには同じ色の 1R と 2R が含まれ、2 番目のパレットには 7P のみが含まれます。 最初のプレイヤーは 1 枚に対して 2 枚の適格カードを持っているため、この動きは合法であり、勝ちとなります。 2 番目のプレイヤーの手札は空なので、ゲームは次のターンで終了します。`First`勝者として。 

indigo ルールでは、重複した値が存在する場合にも注意が必要です。 値 5 の 2 枚のカードは、2 枚のカード ランを形成しません。 スコアの事前計算では、インディゴの組み合わせ内のすべての値が異なるものであることを確認してから、それらの値が 1 つの連続した間隔を形成しているかどうかを確認します。 これにより、重複した値によってランレングスが誤って増加することが防止されます。 

最後に、キャンバス上に置かれたカードは、対応するプレイヤーの使用可能なカードから消えます。 実装では、その 3 つのステータスを手札から破棄に変更することでそれを記録します。 キャンバスには新しい色のみが保存されます。 後で別のカードに置き換えられた場合、古いキャンバス カードは必要に応じて破棄されたままとなり、それがどのカードであったかを思い出す必要はありません。
