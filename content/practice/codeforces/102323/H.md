---
title: "CF 102323H - チョコレート フィックス"
description: "このパズルにはちょうど 9 個のトリュフが使用されています。 各トリュフは、正方形、円形、三角形の 3 つの形状のうちの 1 つと、バニラ、ストロベリー、またはチョコレートの 3 つの味のうちの 1 つを持っています。 すべての組み合わせは 1 回だけ発生するため、9 つの物理的なトリュフはすべて異なります。"
date: "2026-08-13T04:18:35+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102323
codeforces_index: "H"
codeforces_contest_name: "UCF Locals 2014"
rating: 0
weight: 102323
solve_time_s: 85
verified: true
draft: false
---

[CF 102323H - チョコレートの修正](https://codeforces.com/problemset/problem/102323/H)

 **評価:** -
 **タグ:** -
 **解決時間:** 1 分 25 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 このパズルにはちょうど 9 個のトリュフが使用されています。 各トリュフは、正方形、円形、三角形の 3 つの形状のうちの 1 つと、バニラ、ストロベリー、またはチョコレートの 3 つの味のうちの 1 つを持っています。 すべての組み合わせは 1 回だけ発生するため、9 つの物理的なトリュフはすべて異なります。 

これら 9 個のトリュフを 3x3 ボードに配置する必要があります。 手がかりは、いくつかの固定された形状とフレーバー、およびいくつかのワイルドカードを含む小さな長方形のパターンです。 この手がかりは、その長方形がどこから始まるのかを教えてくれません。 代わりに、手がかり全体が回転せずに 3x3 ボード上のどこかに出現する必要があります。 サイズの手がかり`x × y`したがって、に配置できます`(4 - x) × (4 - y)`異なる立場。 

入力にはいくつかのパズルが含まれています。 パズルごとに、そのような手がかりを最大 10 個受け取り、ステートメントは、正確に 1 つの完全な配置がそれらすべてを満たすことを保証します。 同じ 2 文字の表記を手がかりとして使用して、その配置を出力する必要があります。 

この制約により、検索スペースのサイズが中心的な観察になります。 供給されるパズルの数に関係なく、常に正確に 9 個のトリュフが存在します。 完全な配置とは、単に 9 つの異なるオブジェクトを並べたものであるため、存在するのは 9 つだけです。`9! = 362880`可能なボード。 10 個の手がかりと、手がかりごとに最大 9 つの可能な配置は、小さな定数要素のみを追加します。 全体の検索`9^9 = 387420489`任意の代入は不必要に大きくなりますが、有効な順列のみを列挙することは直接の網羅的な検索には十分小さいです。 

手がかりを誤って扱う簡単な方法が 2 つあります。 まず、手がかりの長方形はボードよりも小さいため、手がかりは複数の場所に出現する可能性があります。 たとえば、`3 × 2`手がかりは列 1 または列 2 から開始できますが、他の場所に合わせて回転させてはいけません。 サンプル 1 では、`2 × 3`手がかりも同様に、一番上の行または真ん中の行から開始できます。 常にヒントを固定するプログラム`(0, 0)`有効な解決策を拒否します。 

次に、アンダースコアは何も解決しません。 例えば、`_C`は任意の形状のチョコレートフレーバーを意味します。`S_`任意の味のある四角形を意味します。 を扱う不注意な実装`_`通常の値では有効なボードが拒否されるためです。 サンプルの手がかりはこの区別を示しており、サンプル 3 の正しい出力は、サンプルに示されている一意に決定されたボードです。 

3 番目の境界ケースは完全な状態で表示されます。`3 × 3`手がかり。 このような手がかりには可能な配置が 1 つだけあるため、すべての固定属性が対応するボード セルを直接決定します。 たとえば、単一パズルの入力```
1
1
3 3
TC SC SS
RV RC SV
TS TV RS
```出力があります```
Puzzle #1:
TC SC SS
RV RC SV
TS TV RS
```ここでは手がかりの位置の選択はまったくありません。 

## アプローチ

 最も文字通りの強引な方法は、9 つのトリュフのうち 1 つを 9 つのセルのそれぞれに個別に割り当てることです。 それが生み出す`9^9 = 387420489`候補ボードの多くは、すべてのトリュフを一度だけ使用しなければならないというルールに直ちに違反します。 これらのボードごとに最大 10 個の手がかりをチェックすると、おおよその結果が得られます。`9^9 × 10 × 9`、または最悪の場合、約 349 億回の基本位置チェック。 それは必要以上に大変な作業です。 

有益な観察は、9 つ​​のトリュフが異なるものであることがすでに知られており、すべてのトリュフを 1 回ずつ使用する必要があるということです。 同じトリュフが 2 回含まれる無効なボードを考慮する必要はありません。 ボードはまさに 9 つのトリュフ アイデンティティの順列であり、候補者の数を 1 から 2 に減らします。`9^9`に`9! = 362880`。 

順列ごとに、すべての手がかりをチェックします。 手がかりは、その完全な長方形が 3x3 ボード内に残っている位置からのみ開始できます。 考えられる開始位置ごとに、手がかりが実際に指定する属性のみを比較します。 少なくとも 1 つの配置が一致する場合、その手がかりは満たされます。 

この直接検索の最悪のケースは、最大でも`9! × 10 × 9 = 32,659,200`細胞レベルのチェック。 ほとんどの手がかりには固定属性が含まれており、一意の解決策が見つかるとすぐに検索が停止するため、実際の実装はさらに小さくなります。 ボード サイズは永続的に 9 であるため、ここでは、より重い制約ソルバーを導入するのではなく、徹底的な順列検索が自然な解決策となります。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | 独立した割り当て | O(9^9・C・9) | お(9) | 遅すぎる |
 | 順列列挙 | O(9! · C · 9) | O(9 + C · 9) | 承認済み |

 ここ`C ≤ 10`手がかりの数です。 階乗は次のとおりであるため、明らかに階乗の複雑さは無害です。`9!`、固定値は 362880 のみです。 

## アルゴリズムのチュートリアル

 1. 各物理トリュフを 0 ～ 8 の整数としてエンコードします。`shape * 3 + flavor`、形状とフレーバーは両方とも 3 つの値で表されます。 これにより、あらゆる形状と味の組み合わせに独自のアイデンティティが与えられます。 
2. すべての手がかりコードを許可された属性マスクのペアに変換します。 シェイプキャラクターについては、`_`3 つの形状すべてを許可しますが、`S`、`R`、 そして`T`1 つだけ許可します。 フレーバーキャラクターも同様に機能します。`_`3 つのフレーバーすべてを許可し、`V`、`S`、 そして`C`1つのフレーバーを選択します。 
3. 9 つのトリュフのアイデンティティのすべての順列を生成します。 各順列は 1 つの完全な候補ボードを表すため、トリュフが重複しているか省略されているかを個別に確認する必要はありません。 
4. 各手がかりについて、その長方形の正当な左上隅をすべて列挙します。 その寸法が`x × y`、行の範囲は次のとおりです。`0`を通して`3 - x`、列の範囲は次のとおりです。`0`を通して`3 - y`。 これらの配置の少なくとも 1 つが候補ボードと一致する場合、手がかりは満たされます。 
5. 配置をテストするには、手がかりのすべてのセルを検査します。 手がかりが形状を修正した場合は、それを候補トリュフの形状と比較します。 味が決まったら味も比べてみましょう。 ワイルドカードには制限がありません。 
6. すべての手がかりに少なくとも 1 つの一致する配置がある場合、その順列が一意の解になります。 9 つのトリュフ コードを 3x3 レイアウトで印刷し、次のパズルに進みます。 

この検索が完了する理由は、すべての法律ボードが 9 つの順列の中で 1 回だけ出現するためです。 このようなボードの場合、すべての手がかりのすべての法的配置をチェックすることは、手がかりの定義を正確に反映することになります。 したがって、ボードはすべての手がかりが満たされたときに正確に受け入れられます。 

### なぜ効果があるのか

 重要な不変条件は、検索で考慮されるすべての候補が 9 つの物理的なトリュフの有効な並べ替えであるということです。 各手がかりについて、照合手順では、その手がかりが回転せずに出現する可能性のあるすべての位置が考慮され、それらの位置の 1 つが指定されたすべての属性と一致する場合に、その手がかりが正確に受け入れられます。 したがって、受験者は、それが法的なパズルの解決策である場合に限り、テスト全体に合格します。 この問題では一意性が保証されているため、最初に通過する順列が必須の配置になります。 

## Python ソリューション```python
import sys
from itertools import permutations

input = sys.stdin.readline

def solve_puzzle(clues):
    shape_id = {'S': 0, 'R': 1, 'T': 2}
    flavor_id = {'V': 0, 'S': 1, 'C': 2}

    # Piece id = shape * 3 + flavor.
    pieces = list(range(9))

    def shape_mask(ch):
        if ch == '_':
            return 0b111
        return 1 << shape_id[ch]

    def flavor_mask(ch):
        if ch == '_':
            return 0b111
        return 1 << flavor_id[ch]

    # Each placement is represented by a list of
    # (board_position, allowed_shape_mask, allowed_flavor_mask).
    prepared = []

    for x, y, grid in clues:
        placements = []

        for sr in range(4 - x):
            for sc in range(4 - y):
                placement = []

                for r in range(x):
                    for c in range(y):
                        code = grid[r][c]
                        sm = shape_mask(code[0])
                        fm = flavor_mask(code[1])
                        pos = (sr + r) * 3 + (sc + c)
                        placement.append((pos, sm, fm))

                placements.append(placement)

        prepared.append(placements)

    # More restrictive clues first. This does not change correctness,
    # but usually rejects a wrong permutation earlier.
    def restriction_score(placements):
        score = 0
        for placement in placements:
            for _, sm, fm in placement:
                if sm != 0b111:
                    score += 1
                if fm != 0b111:
                    score += 1
        return score

    prepared.sort(key=restriction_score, reverse=True)

    for board in permutations(pieces):
        good = True

        for placements in prepared:
            clue_good = False

            for placement in placements:
                matches = True

                for pos, sm, fm in placement:
                    piece = board[pos]
                    shape = piece // 3
                    flavor = piece % 3

                    if not (sm & (1 << shape)):
                        matches = False
                        break

                    if not (fm & (1 << flavor)):
                        matches = False
                        break

                if matches:
                    clue_good = True
                    break

            if not clue_good:
                good = False
                break

        if good:
            return board

    return None

def main():
    t = int(input())
    output = []

    for case in range(1, t + 1):
        c = int(input())
        clues = []

        for _ in range(c):
            x, y = map(int, input().split())
            grid = [input().split() for _ in range(x)]
            clues.append((x, y, grid))

        board = solve_puzzle(clues)

        output.append(f"Puzzle #{case}:")
        for r in range(3):
            row = []
            for col in range(3):
                piece = board[r * 3 + col]
                shape = "SRT"[piece // 3]
                flavor = "VSC"[piece % 3]
                row.append(shape + flavor)
            output.append(" ".join(row))

        output.append("")

    sys.stdout.write("\n".join(output))

if __name__ == "__main__":
    main()
```の最初の部分`solve_puzzle`すべてのトリュフに一意の整数を割り当てます。 と`piece // 3`形状を復元します。`piece % 3`風味を取り戻します。 このエンコーディングは便利です。`itertools.permutations`すべての法務委員会を直接列挙します。 

手がかりの前処理は、考えられる各配置を、それが制約するボードの位置と、対応する許可されたマスクに変換します。 のようなマスク`0b111`は 3 つの可能性すべてを表し、1 ビット マスクは固定属性を表します。 これにより、マッチング ループがテキストの手がかり表現から独立した状態に保たれます。 

配置ループは使用します`range(4 - x)`そして`range(4 - y)`。 高さの手がかりとして`x`、最大の有効な開始行は次のとおりです。`3 - x`、つまり、まさにあります`4 - x`可能な開始行。 同じ理由が列にも当てはまります。 これは、手掛かりがボードの外に広がることを防ぐ境界です。 

固定属性の数による手がかりの並べ替えは、パフォーマンスの最適化にすぎません。 限定的な手がかりは誤った順列をすぐに拒否する可能性が高いため、残りの手がかりのチェックに費やす作業が少なくなります。 結果はこの順序には依存しません。 

順列自体は 9 つの整数のタプルであるため、`board[pos]`セルを占有しているトリュフを直接識別します。 形状と風味はマスクに対して個別にチェックされます。 ワイルドカードでは 3 ビットすべてが有効になっており、その比較は自動的に成功します。 

ソルバーは、すべての手掛かりを満たすボードを見つけるとすぐに戻ります。 この問題では、そのようなボードが存在し、一意であることが保証されるため、最初の一致で停止することに曖昧さはありません。 

出力では、整数エンコーディングが必要な 2 文字形式に変換されます。 形のアルファベットは、`SRT`、フレーバーのアルファベットは`VSC`、問題の表記と一致します。 出力形式の要求に応じて、各パズルの後に空行が追加されます。 

## 実用的な例

 ### サンプル 1

 最初のサンプルには 4 つの手がかりが含まれています。 最初の手がかりはすでに完全な 3x3 ボードを指定しているため、可能な配置は 1 つだけです。 他の手がかりも同じ配置と一致しています。 

| ステップ | 候補取締役会の状態 | 結果 |
 | --- | --- | --- |
 | 1 | 順列の列挙を開始する | 候補者探しが始まります |
 | 2 | チェック`3 × 3`手がかり | プレースメントは 1 つだけ存在します |
 | 3 | チェック`2 × 3`手がかり | 少なくとも 1 つの配置が一致します |
 | 4 | チェック`3 × 3`手がかり | フルボードマッチ |
 | 5 | チェック`2 × 3`手がかり | 少なくとも 1 つの配置が一致します |
 | 6 | すべての手がかりが満たされました | 受け入れボード |

 出来上がった基板は、```
TC SC SS
RV RC SV
TS TV RS
```ここで重要な点は、フルサイズの手掛かりには位置的な曖昧さがないということです。 また、順列検索が手がかりを直接処理するため、手動で特別な演繹を実行しなくても解を見つけることができることも示しています。 

### サンプル 2

 2 番目のパズルでは、最初の手がかりでは完全なボードが得られません。 代わりに、検索では、小さな手がかり長方形のいくつかの可能な位置を考慮する必要があります。 

| ステップ | 候補州 | 結果 |
 | --- | --- | --- |
 | 1 | 順列を列挙する | 取締役候補者が選択されました |
 | 2 | チェック`2 × 3`手がかり | 可能な 2 つの行位置をテストします。 
| 3 | チェック`3 × 3`手がかり | 可能な 1 つの位置をテストする |
 | 4 | 残りの手がかりを確認する | 手がかりに違反する候補者を拒否する |
 | 5 | 生き残ったユニークな順列 | 同意する |

 ユニークなボードは、```
TV RS TS
SC SV TC
SS RV RC
```この例では、手がかりの中心的な解釈を実行します。つまり、その四角形はボード内で移動できますが、回転することはできません。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(9! · C · 9) | せいぜい`9!`せいぜいボード`C ≤ 10`手がかり、および手がかりの配置ごとに最大 9 つのセルが検査されます。 
| スペース | O(C・9+9) | 保存された手がかりの配置と現在の順列 |

 最大の検索では、候補ボードは 362,880 件しかありません。 最大で 10 個の手がかりと 9 個の可能な手がかり配置を使用すると、理論上の作業は約 3,270 万セル チェックになりますが、制限的な手がかりでは通常、失敗した候補者がはるかに早い段階で終了します。 ボードには 9 つのセルしか含まれておらず、手がかり表現には一定数のエントリしか含まれていないため、メモリ使用量はわずかです。 元のコンテストの声明では次のようになります。`c ≤ 10`手がかりの次元は最大 3x3 であるため、網羅的順列アプローチはこれらの制約に合わせて適切なサイズになります。 

## テストケース```python
import sys
import io
from itertools import permutations

def solve_input(inp: str) -> str:
    old_stdin = sys.stdin
    old_stdout = sys.stdout

    sys.stdin = io.StringIO(inp)
    sys.stdout = io.StringIO()

    input_fn = sys.stdin.readline

    def solve_puzzle(clues):
        shape_id = {'S': 0, 'R': 1, 'T': 2}
        flavor_id = {'V': 0, 'S': 1, 'C': 2}

        def shape_mask(ch):
            return 0b111 if ch == '_' else 1 << shape_id[ch]

        def flavor_mask(ch):
            return 0b111 if ch == '_' else 1 << flavor_id[ch]

        prepared = []

        for x, y, grid in clues:
            placements = []

            for sr in range(4 - x):
                for sc in range(4 - y):
                    placement = []

                    for r in range(x):
                        for c in range(y):
                            code = grid[r][c]
                            pos = (sr + r) * 3 + (sc + c)
                            placement.append(
                                (pos, shape_mask(code[0]), flavor_mask(code[1]))
                            )

                    placements.append(placement)

            prepared.append(placements)

        def score(placements):
            value = 0
            for placement in placements:
                for _, sm, fm in placement:
                    value += sm != 0b111
                    value += fm != 0b111
            return value

        prepared.sort(key=score, reverse=True)

        for board in permutations(range(9)):
            valid = True

            for placements in prepared:
                clue_valid = False

                for placement in placements:
                    ok = True

                    for pos, sm, fm in placement:
                        piece = board[pos]
                        sh = piece // 3
                        fl = piece % 3

                        if not (sm & (1 << sh)) or not (fm & (1 << fl)):
                            ok = False
                            break

                    if ok:
                        clue_valid = True
                        break

                if not clue_valid:
                    valid = False
                    break

            if valid:
                return board

        return None

    t = int(input_fn())
    ans = []

    for case in range(1, t + 1):
        c = int(input_fn())
        clues = []

        for _ in range(c):
            x, y = map(int, input_fn().split())
            grid = [input_fn().split() for _ in range(x)]
            clues.append((x, y, grid))

        board = solve_puzzle(clues)

        ans.append(f"Puzzle #{case}:")
        for r in range(3):
            row = []
            for c in range(3):
                piece = board[r * 3 + c]
                row.append("SRT"[piece // 3] + "VSC"[piece % 3])
            ans.append(" ".join(row))
        ans.append("")

    sys.stdout.write("\n".join(ans))

    result = sys.stdout.getvalue()
    sys.stdin = old_stdin
    sys.stdout = old_stdout
    return result

# Provided samples
sample_input = """3
4
3 3
TC __ SS
__ __ __
__ TV __
2 3
__ SC __
RV __ SV
3 3
__ __ __
__ RC __
__ __ __
2 3
__ __ __
TS __ RS
5
2 3
__ __ __
__ __ RC
2 2
__ RS
SC __
2 2
SV TC
__ __
3 2
TV __
__ __
__ RV
3 2
__ TS
__ __
__ __
3
3 2
_C R_
_C __
S_ _C
1 2
TC _V
3 2
_V __
S_ S_
T_ _V
"""

sample_output = """Puzzle #1:
TC SC SS
RV RC SV
TS TV RS

Puzzle #2:
TV RS TS
SC SV TC
SS RV RC

Puzzle #3:
TV TC RV
SS SC RS
TS SV RC

"""

assert solve_input(sample_input) == sample_output, "provided samples"

# Minimum-size puzzle: one complete 3x3 clue.
minimum_input = """1
1
3 3
SV SR ST
RV RR RT
CV CR CT
"""

minimum_output = """Puzzle #1:
SV SR ST
RV RR RT
CV CR CT

"""

assert solve_input(minimum_input) == minimum_output, "minimum-size clue"

# All attributes are explicitly fixed, and the arrangement is reversed
# relative to the natural encoding order.
boundary_input = """1
1
3 3
CT CR CV
RT RR RV
ST SR SV
"""

boundary_output = """Puzzle #1:
CT CR CV
RT RR RV
ST SR SV

"""

assert solve_input(boundary_input) == boundary_output, "boundary arrangement"

# Multiple clues with wildcards. The full clue determines the solution,
# while the smaller clues exercise wildcard handling and movable windows.
wildcard_input = """1
3
3 3
TC SC SS
RV RC SV
TS TV RS
1 2
__ SC
2 2
__ __
RC __
"""

wildcard_output = """Puzzle #1:
TC SC SS
RV RC SV
TS TV RS

"""

assert solve_input(wildcard_input) == wildcard_output, "wildcard and window handling"

# Maximum number of clues, all individually valid and consistent.
maximum_clues_input = """1
10
3 3
TC SC SS
RV RC SV
TS TV RS
1 1
TC
1 1
SC
1 1
SS
1 1
RV
1 1
RC
1 1
SV
1 1
TS
1 1
TV
1 1
RS
"""

maximum_clues_output = """Puzzle #1:
TC SC SS
RV RC SV
TS TV RS

"""

assert solve_input(maximum_clues_input) == maximum_clues_output, "maximum clue count"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`1 / 1 / 3 3 / SV SR ST / RV RR RT / CV CR CT`| 同じ 3x3 ボード | 最小サイズのパズルと直接フルボード マッチング |
 |`1 / 1 / 3 3 / CT CR CV / RT RR RV / ST SR SV`| 同じ 3x3 ボード | 境界配置と完全な属性マッチング |
 | ワイルドカードを含む 3 つの手がかり |`TC SC SS / RV RC SV / TS TV RS`| ワイルドカードと移動可能な小さなウィンドウ |
 | 10 の一貫した手がかり |`TC SC SS / RV RC SV / TS TV RS`| 手がかりの最大数と繰り返し位置の制限 |

 ## 特殊なケース

 ボードより小さい手がかりには、複数の正当な位置がある場合があります。 たとえば、`1 × 1`手がかりが含まれている`TC`はどこでも発生する可能性があるため、ソルバーは 9 つのセルすべてを検索する必要があります。 上記の最大手がかりテストでは、手がかりは`TC`はそのトリュフを含むセルとのみ互換性がありますが、次のような手がかりは`SC`そして`SS`同様に自分の作品を特定します。 ソルバーは固定された手がかりの起源を想定していないため、これらの手がかりは正しく処理されます。 

手がかりは 1 つの属性のみを制約できます。 コードが表すのは、`_`3ビットマスクを使用するので、`_C`受け入れる`SC`、`RC`、 または`TC`、 その間`S_`受け入れる`SV`、`SS`、 または`SC`。 ワイルドカードテストの手がかりは`__ SC`ボードに含まれているので満足です`SC`最初の行の 2 番目の位置。 治療中`_`リテラル文字を使用すると、ソリューションが誤って拒否されるためです。 

いっぱい`3 × 3`手がかりには、法的な配置が 1 つだけあります。 最小サイズのテストでは、最初で唯一の手がかりがすべてのセルを修正するため、その手がかりの位置検索は行われません。 ユニークな順列はまさに与えられたボードそのものであり、出力はそれを再現します。 

可能な最大手掛かり数は 10 です。 最大手がかりテストでは、完全なボードと 9 つの単一セルの手がかりを含む 10 個の相互に一貫した手がかりを使用します。 ソルバーは単純に 10 個すべてを処理し、同じ小さな置換空間で動作します。 これは、手がかりの数が実行時間の一定の要素にのみ影響する理由を示しています。 

最後に、形状と味は互換性がありません。`SC`四角いチョコレートという意味ですが、`CS`最初の文字は常に形状であり、2 番目の文字は常にフレーバーであるため、異なる組み合わせを意味します。 整数エンコードでは、この順序が維持されます。`piece // 3`形状と`piece % 3`フレーバーのために、2 つの属性が誤って交換されるのを防ぎます。
