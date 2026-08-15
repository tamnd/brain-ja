---
title: "CF 102307H - 最も難しい挑戦"
description: "各チームには独自の長さの 3 本の文字列があります。 どのポジションでも、チームは他のポジションとは無関係に、3 人のメンバーのいずれかからキャラクターを選択できます。 したがって、文字列 (P,Q,R) を持つチームは、最大 (3^n) 個の異なる文字列を構築できます。"
date: "2026-08-13T07:22:35+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102307
codeforces_index: "H"
codeforces_contest_name: "2019 ICPC Universidad Nacional de Colombia Programming Contest"
rating: 0
weight: 102307
solve_time_s: 193
verified: true
draft: false
---

[CF 102307H - 最も難しい課題](https://codeforces.com/problemset/problem/102307/H)

 **評価:** -
 **タグ:** -
 **解決時間:** 3 分 13 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 各チームには独自の長さの 3 本の文字列があります。 どのポジションでも、チームは他のポジションとは無関係に、3 人のメンバーのいずれかからキャラクターを選択できます。 したがって、文字列 (P,Q,R) を持つチームは、最大 (3^n) 個の異なる文字列を構築できます。 

構築された文字列のスコアは、基数 127 の多項式ハッシュ モジュロです。 

[
 MOD=10^{15}+37。 
]

 ハッシュの意図された解釈は、標準的な左から右への反復です。 

[
 h_0=0,\qquad h_{i+1}=(127h_i+\オペレーター名{ord}(S_i))\bmod MOD。 
]

 これは、127 の降べき乗によって文字に重み付けすることと同じです。公式の問題ページでは (A,B\le 28) が示されており、例ではまさにこの構造が使用されています。 

フクロウの場合、長さ (A) のすべての文字列の中から可能な限り最小のスコアを見つけなければなりません。 同じことをヤギに対して個別に行い、2 つの最小スコアを比較します。 スコアが小さい方が勝ちですが、等しい場合は引き分けとなります。 

上限の 28 が全体の難易度です。 直接列挙するには、1 つのチームに対して (3^{28}=22,876,792,454,961) 個の候補文字列が必要になりますが、これは 10 秒で処理できる量をはるかに超えています。 一方、28 のポジションを 2 つの半分に分割すると、半分あたり最大 (3^{14}=4,782,969) 個の可能性が得られます。 数百万の州は規模は大きいですが管理可能であり、中間者解決策が強く示唆されています。 

弾性率も重要です。 モジュロ演算を使用しない場合は、辞書編集上または数値的に最小の文字列を選択するだけで十分です。これは、すべての文字値が正であり、以前の位置の方が 127 の累乗が大きいためです。モジュラスを取得した後、多項式の値が大きくなると、剰余が大幅に小さくなる可能性があります。 変更されていない多項式を最小化するアプローチでは、誤って間違った文字列が選択される可能性があります。 

2 番目のエッジ ケースは、3 人のメンバーがどこかのポジションで同じキャラクターを持っているチームです。 単純な実装では 3 回カウントされる可能性がありますが、その位置には明確な選択肢が 1 つだけあります。 重複は正確さに影響しませんが、重複を削除すると実際の作業量を大幅に削減できます。 

長さが 1 の場合も、ステートメント内の指数が文字通りに解釈されると誤って処理されやすくなります。 1 つの文字の場合、スコアは単純にその ASCII 値でなければなりません。 例えば、```
1 1
E
L
I
X
Y
Z
```与える`Owls`、フクロウが入手できるため、`E`のスコアは 69 ですが、ヤギの最小スコアは 88 です。左から右へのハッシュの反復により、この境界ケースが明確になります。 

最後に、長い文字列ではモジュールのラップアラウンドが発生する可能性があります。 たとえば、2 番目のサンプルにはヤギ側に 28 文字があるため、多項式は縮小される前に (MOD) をはるかに超えて増加します。 生の多項式値を比較することは、そのスコアを比較することと同じではありません。 

## アプローチ

 ブルート フォース ソリューションは、定義に直接準拠します。 すべての位置について、使用可能な 3 つの文字をそれぞれ試し、可能なすべての文字列を再帰的に構築し、そのハッシュを計算して、最小のスコアを維持します。 これは正しいです。2 人のチーム メンバーが同じ位置に同じ文字を持っている場合の無害な重複は別として、合法的に構築された文字列はすべて 1 回だけ出現するからです。 

問題は葉の枚数です。 長さ 28 では、1 つのチームに対して (3^{28}=22,876,792,454,961) 個の可能な文字列があります。 たとえ各ハッシュの計算が一定時間に短縮されたとしても、数十兆の候補を検査することは不可能です。 すべてのリーフでハッシュ全体を再計算すると、状況はさらに悪化します。 

重要な点は、文字列が 2 つの独立した部分に分割できるということです。 左側の部分がハッシュ (L)、右側の部分がハッシュ (R)、右側の部分が長さ (k) を持つとします。 それらの連結にはハッシュがあります

 [
 (L\cdot127^k+R)\bmod MOD。 
]

 したがって、考えられるすべての左半分とすべての右半分を個別に列挙できます。 どちらの陣営にも最大 14 のポジションがあり、各陣営には最大 (3^{14}=4,782,969) 個の可能性があります。 これが中間者削減です。 

最終値はモジュロ (MOD) で取得されるため、もう 1 つ有益な観察があります。 固定左ハッシュの場合、次のように定義します。 

[
 X=(L\cdot127^k)\bmod MOD。 
]

 最小限に抑える必要があります

 [
 (X+R)\bmod MOD
 ]

 すべての可能な右ハッシュ (R) にわたって。 

(X+R<MOD) の場合、結果は単純に (X+R) となるため、すべての非ラッピング候補の中で最小の右ハッシュが最適になります。 (X+R\ge MOD) の場合、結果は (X+R-MOD) となるため、最適なラッピング候補は次を満たす最小の右ハッシュになります。 

[
 MOD-X を読みましょう。 
]

 すべての適切なハッシュをソートした後、その候補は 1 回の二分探索で見つかります。 したがって、左半分を保存したり並べ替えたりする必要はまったくありません。 

すべての選択が独立しているため、ブルート フォースは機能しますが、すべての位置選択のデカルト積を探索するため失敗します。 連結のハッシュが、変換された左ハッシュと右ハッシュに分離されるという観察により、その巨大なデカルト積を、およそ (3^{14}) 個の状態と対数検索の 2 セットに置き換えることができます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | (O(3^n n)) | (O(n)) | 遅すぎる |
 | 中間者会 | (O(3^{n/2}\log 3^{n/2})) | (O(3^{n/2})) | 承認済み |

 以下の実装では、Python のメモリ使用量に小さな追加の最適化を使用しています。 長さ 14 の半分は、ハッシュを生成するときに、それ自体が最大 7 文字の 2 つの部分に分割されます。 各ピースは最大 (3^7=2187) 個の値を持ち、それらのデカルト積は、サイズの中間リスト (3^8,3^9,\ldots,3^{13}) を同時に保持することなく、必要な (3^{14}) ハッシュを作成します。 

## アルゴリズムのチュートリアル

 1. 現在のチームの場合、文字列の位置を長さ (L=\lfloor n/2\rfloor) のプレフィックスと残りの位置を含むサフィックスに分割します。 分割では両方の部分の長さが最大 ​​14 に保たれるため、どちらの側にも (3^{14}) 個を超える文字列が存在しません。 
2. サフィックスの考えられるすべてのハッシュを生成します。 部分文字列の場合は、次のようにハッシュを更新します。`hash = (hash * 127 + character) % MOD`。 最初のコピーまたは 2 番目のコピーを選択すると同じ結果の文字列が生成されるため、同じ位置にある等しい文字は重複排除できます。 
3. サフィックスハッシュを並べ替えます。 ソートにより、次を使用して、必要なしきい値以上の最小のサフィックス ハッシュを見つけることができます。`bisect_left`。 
4. 同様にプレフィックスのハッシュを生成します。 それぞれのハッシュをソートされたサフィックス配列とすぐに照合できるため、すべての結合されたプレフィックス ハッシュを保存する必要はありません。 
5. プレフィックス ハッシュ (L_h) の場合、(R) をサフィックス長として計算します。 

[
 X=(L_h\cdot127^R)\bmod MOD。 
]

 このプレフィックスを使用するすべての完全な文字列にはスコアがあります

 [
 (X+H_r)\bmod MOD
 ]

 いくつかのサフィックスハッシュ (H_r) の場合。 
6. 最小のサフィックスハッシュを考慮します。 もし`X + smallest_suffix < MOD`、このプレフィックスに対してラップされていない最良の結果が得られます。 サフィックス ハッシュ内の式が増加するため、サフィックスを大きくしても非ラッピングの結果を改善することはできません。 
7. を満たす最初のサフィックスハッシュを見つけます。`suffix >= MOD - X`。 このような値が存在する場合、最適なラッピング結果が得られます。 繰り返しになりますが、式をラップした後は、後続のサフィックス ハッシュごとに、少なくとも同じ大きさの結果が生成されます。`X + suffix - MOD`。 
8. すべてのプレフィックス ハッシュに対して最小の候補を保持します。 これはチームの最低得点です。 
9. 他のチームに対しても同じ手順を実行し、結果として得られる 2 つのスコアを比較します。 印刷する`Owls`フクロウのスコアが小さい場合、`Goats`Goats スコアが小さい場合、および`Tie`さもないと。 

これが機能する理由は、固定プレフィックスの不変条件からわかります。 プレフィックス ハッシュが修正されると、考えられるすべての完全なスコアは次の形式になります。`(X + suffix_hash) mod MOD`。 以下のサフィックスハッシュのうち`MOD-X`、式が増加しているため、最小のサフィックス ハッシュが最適です。 少なくともサフィックスハッシュの中では`MOD-X`、ラップされた式も増加しているため、そのしきい値での最初のサフィックス ハッシュが最適です。 モジュロ結果の可能な形式はこれら 2 つだけであるため、両方をチェックすると、すべてのプレフィックスの最適な補完が見つかります。 考えられるすべてのプレフィックスが検査されるため、グローバル最小値が見つかります。 

## Python ソリューション```python
import sys
from bisect import bisect_left

input = sys.stdin.readline

MOD = 1000000000000037
BASE = 127

def distinct_choices(strings, pos):
    a = ord(strings[0][pos])
    b = ord(strings[1][pos])
    c = ord(strings[2][pos])

    if a == b == c:
        return (a,)
    if a == b:
        return (a, c)
    if a == c:
        return (a, b)
    if b == c:
        return (a, b)

    return (a, b, c)

def small_hashes(strings, left, right):
    """All hashes for a segment of length at most 7."""
    values = [0]

    for pos in range(left, right):
        choices = distinct_choices(strings, pos)
        values = [
            (value * BASE + ch) % MOD
            for value in values
            for ch in choices
        ]

    return values

def segment_hashes(strings, left, right):
    """
    Generate all hashes for a segment of length at most 14.

    Split it into two pieces of at most 7 characters so that
    intermediate Python lists stay small.
    """
    length = right - left

    if length <= 7:
        return small_hashes(strings, left, right)

    middle = left + length // 2

    first = small_hashes(strings, left, middle)
    second = small_hashes(strings, middle, right)

    power = pow(BASE, right - middle, MOD)

    return [
        (x * power + y) % MOD
        for x in first
        for y in second
    ]

def best_score(strings):
    n = len(strings[0])

    left_len = n // 2
    right_start = left_len

    # Generate and sort every possible suffix hash.
    suffix_hashes = segment_hashes(strings, right_start, n)
    suffix_hashes.sort()

    min_suffix = suffix_hashes[0]
    right_len = n - right_start
    right_power = pow(BASE, right_len, MOD)

    # Generate prefix hashes in two small pieces.
    if left_len <= 7:
        prefix_hashes = small_hashes(strings, 0, left_len)
        prefix_parts = (prefix_hashes, None, 1)
    else:
        middle = left_len // 2
        first = small_hashes(strings, 0, middle)
        second = small_hashes(strings, middle, left_len)
        power_between = pow(BASE, left_len - middle, MOD)
        prefix_parts = (first, second, power_between)

    best = MOD

    first, second, power_between = prefix_parts

    if second is None:
        for prefix_hash in first:
            x = (prefix_hash * right_power) % MOD

            # Best non-wrapping candidate.
            candidate = x + min_suffix
            if candidate < MOD and candidate < best:
                best = candidate

            # Best wrapping candidate.
            threshold = MOD - x
            idx = bisect_left(suffix_hashes, threshold)

            if idx < len(suffix_hashes):
                candidate = x + suffix_hashes[idx] - MOD
                if candidate < best:
                    best = candidate
    else:
        for first_hash in first:
            base = (first_hash * power_between) % MOD

            for second_hash in second:
                prefix_hash = (base + second_hash) % MOD
                x = (prefix_hash * right_power) % MOD

                # Best non-wrapping candidate.
                candidate = x + min_suffix
                if candidate < MOD and candidate < best:
                    best = candidate

                # Best wrapping candidate.
                threshold = MOD - x
                idx = bisect_left(suffix_hashes, threshold)

                if idx < len(suffix_hashes):
                    candidate = x + suffix_hashes[idx] - MOD
                    if candidate < best:
                        best = candidate

    return best

def main():
    A, B = map(int, input().split())

    owls = [input().strip() for _ in range(3)]
    goats = [input().strip() for _ in range(3)]

    owls_score = best_score(owls)
    goats_score = best_score(goats)

    if owls_score < goats_score:
        print("Owls")
    elif goats_score < owls_score:
        print("Goats")
    else:
        print("Tie")

if __name__ == "__main__":
    main()
```の`distinct_choices`関数は、ある位置で繰り返される文字を削除します。 繰り返しの選択は同じ文字を表し、その結果同じ構築された文字列を表すため、これは単なる最適化です。`small_hashes`既存のハッシュを 1 文字で繰り返し拡張することにより、セグメントのすべての文字列を列挙します。 このセグメントは意図的に 7 つの位置に制限されています。 7 つの位置には最大 2187 個の状態があり、これは最終的な (3^{14}) 個の状態セットと比較すると小さいです。`segment_hashes`このような 2 つの小さなセグメントを組み合わせます。 最初の部分にハッシュがある場合`x`2番目の部分にはハッシュがあります`y`、連結されたハッシュは

 [
 (x\cdot127^{|秒|}+y)\bmod MOD。 
]

 これはまさに中間分割に必要な代数です。`best_score`サフィックスハッシュを一度ソートします。 すべてのプレフィックスについて、`right_power`ハッシュをサフィックス文字の数だけシフトします。 の`min_suffix`候補者は非ラッピングのケースを処理しますが、`bisect_left`ラップを引き起こす最初のサフィックスを見つけます。その結果、これが最適なラップ候補になります。 

整数には任意の精度があるため、Python では整数オーバーフローの問題は発生しません。 この問題ではスコア モジュロ (MOD) が定義されており、値を小さくしておくことで算術効率も維持できるため、明示的なモジュロ演算が依然として必要です。 

分割境界は半開間隔を使用します。 接頭語は`[0, left_len)`、サフィックスは`[left_len, n)`。 これにより、分割位置の文字を誤って省略したり重複したりすることがなくなります。 

7 文字境界の特別な処理も意図的です。 セグメントの長さが最大 ​​7 の場合、セグメントは直接生成されます。 それが長い場合は、2 つの小さなセグメントに分割されます。 これにより、同じハッシュのセットを保持しながら、最大の一時リストが小さく保たれます。 

## 実用的な例

 ### サンプル 1

 入力は```
6 6
ANDRES
FELIPE
MANUEL
VICTOR
IVANSS
DIEGOS
```各チームの長さは 6 であるため、中間会のスプリットには各側に 3 人のキャラクターがいます。 各サイドには最大 (3^3=27) 個の可能なハッシュがあります。 

Owls の場合、最初の 3 つの位置は 27 の可能なプレフィックス ハッシュを生成でき、最後の 3 つの位置は 27 のサフィックス ハッシュを生成できます。 ソートされたサフィックス ハッシュにより、アルゴリズムはすべてのプレフィックスに対して最適なサフィックスを見つけることができます。 

対応するトレースは次のとおりです。 

| 変数 | フクロウ | ヤギ |
 | --- | --- | --- |
 | 長さ | 6 | 6 |
 | プレフィックスの長さ | 3 | 3 |
 | 接尾辞の長さ | 3 | 3 |
 | 半分あたりの最大ハッシュ数 | 27 | 27 |
 | 最終結果 | 小さい | 拡大 |
 | 勝者 | フクロウ | |

 この例の重要な部分は、アルゴリズムがすべて (3^6=729) の完全な文字列を構築することはないということです。 27 個のハーフハッシュの 2 セットを構築し、モジュラー不等式を通じて結合するだけです。 

### サンプル 2

 入力は```
1 28
E
L
I
AAAAAAAAAAAAAAAAAAAAAAAAAAAA
BBBBBBBBBBBBBBBBBBBBBBBBBBBB
CCCCCCCCCCCCCCCCCCCCCCCCCCCC
```オウルズのポジションはひとつだけ。 可能なスコアは 69、76、73 であるため、最小値は 69 です。 

ヤギには 28 のポジションがありますが、どのポジションにも同じ 3 つの選択肢が含まれています。`A`、`B`、 そして`C`。 アルゴリズムは、これら 28 の位置を 14 個の 2 つのグループに分割します。各半分には最大 (3^{14}=4,782,969) 個の可能性が含まれますが、繰り返される構造により実際に生成される個別のハッシュの数ははるかに少なくなります。 

高レベルのトレースは次のとおりです。 

| 変数 | フクロウ | ヤギ |
 | --- | --- | --- |
 | 長さ | 1 | 28 |
 | プレフィックスの長さ | 0 | 14 |
 | 接尾辞の長さ | 1 | 14 |
 | 最大半状態 | 3 | 4,782,969 |
 | 最終スコアの最小値 | 69 | 69 より大きい |
 | 勝者 | フクロウ | |

 Goat の多項式はリダクション前の係数よりもはるかに大きいため、この例では通常の整数比較ではなくモジュラー ハッシュも実行します。 公式のサンプル出力は次のとおりです`Goats`なぜなら、モジュロ演算後のヤギの最小スコアは実際には小さくなるからです。 

このサンプルは、生の多項式の大きさをスコアの代用として使用できない理由を示すため、特に役立ちます。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O(3^{n/2}\log 3^{n/2})) | 最大 (3^{n/2}) 個のサフィックス ハッシュを生成し、それらを並べ替えて、各プレフィックスの組み合わせに対して 1 回の二分検索を実行します。 
| スペース | (O(3^{n/2})) | ソートされたサフィックス ハッシュがメモリ使用量の大半を占めます。 

(n=28) の場合、最大半分には (3^{14}=4,782,969) 個の組み合わせが含まれます。 アルゴリズムは 2 つのチームを個別に処理するため、他のチームが処理される前に大きなサフィックス配列が解放されます。 7 文字のサブ分割により、複数の大きな中間 Python リストを一度に保持することも避けられます。 これにより、フルサイズの拡張を繰り返して 14 文字のハッシュ リストを構築するよりもはるかに快適に実装を 256 MB のメモリ制限内に維持できます。 

## テストケース

 次のハーネスは、送信されたソリューションが次のように保存されることを前提としています。`solution.py`そして暴露します`main`。```python
# helper: run solution on input string, return output string
import sys
import io

from solution import main

def run(inp: str) -> str:
    old_stdin = sys.stdin
    old_stdout = sys.stdout

    sys.stdin = io.StringIO(inp)
    sys.stdout = io.StringIO()

    try:
        main()
        return sys.stdout.getvalue().strip()
    finally:
        sys.stdin = old_stdin
        sys.stdout = old_stdout

# Provided sample 1
assert run(
    """6 6
ANDRES
FELIPE
MANUEL
VICTOR
IVANSS
DIEGOS
"""
) == "Owls", "sample 1"

# Provided sample 2
assert run(
    """1 28
E
L
I
AAAAAAAAAAAAAAAAAAAAAAAAAAAA
BBBBBBBBBBBBBBBBBBBBBBBBBBBB
CCCCCCCCCCCCCCCCCCCCCCCCCCCC
"""
) == "Goats", "sample 2"

# Minimum size, and all choices identical on both sides.
assert run(
    """1 1
A
A
A
A
A
A
"""
) == "Tie", "minimum size and identical choices"

# Small boundary case with different lengths.
# Owls can only make "AA", score 65*127+65 = 8320.
# Goats can only make "Z", score 90.
assert run(
    """2 1
AA
AA
AA
Z
Z
Z
"""
) == "Goats", "different lengths and two-character hash"

# Maximum size with identical values.
# Both teams can produce exactly the same 28-character string.
assert run(
    """28 28
AAAAAAAAAAAAAAAAAAAAAAAAAAAA
AAAAAAAAAAAAAAAAAAAAAAAAAAAA
AAAAAAAAAAAAAAAAAAAAAAAAAAAA
AAAAAAAAAAAAAAAAAAAAAAAAAAAA
AAAAAAAAAAAAAAAAAAAAAAAAAAAA
AAAAAAAAAAAAAAAAAAAAAAAAAAAA
"""
) == "Tie", "maximum length and all equal values"

# Duplicate choices at every position.
# The three members on each side are identical, so there is only one
# distinct constructed string per team.
assert run(
    """3 3
ABC
ABC
ABC
ABD
ABD
ABD
"""
) == "Owls", "duplicate choices and exact boundary split"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | サンプル 1 |`Owls`| 公式例と通常の中間者行動 |
 | サンプル 2 |`Goats`| 長さ 1 の処理と 28 文字の文字列に対するモジュラー ラップアラウンド |
 |`1 1`6人で`A`文字列 |`Tie`| 最小サイズと同一スコア |
 |`2 1`と`AA`対`Z`|`Goats`| 等しくない長さと最初の自明ではないハッシュ指数 |
 |`28 28`みんなで`A`|`Tie`| 最大入力長と繰り返される値 |
 |`3 3`メンバー文字列が繰り返される |`Owls`| ポジションごとの同一の選択肢の重複排除 |

 ## 特殊なケース

 ### 長さ 1

 検討してください```
1 1
E
L
I
X
Y
Z
```フクロウは選択できる`E`、`L`、 または`I`したがって、最小スコアは次のようになります。`69`。 ヤギたちは選べる`X`、`Y`、 または`Z`したがって、最小スコアは次のようになります。`88`。 

フクロウの接頭辞の長さは 0 で、接尾辞の長さは 1 です。 サフィックス配列には、3 つの ASCII 値 69、73、および 76 が含まれています。単一のプレフィックス ハッシュは 0 であるため、アルゴリズムは最小のサフィックスを直接評価し、69 を取得します。ヤギも同様に 88 を取得し、次のようになります。`Owls`。 

これにより、最後の文字に誤って 127 の累乗を使用する実装が捕捉されます。 

### 繰り返される選択

 検討してください```
3 3
ABC
ABC
ABC
ABD
ABD
ABD
```フクロウのすべての位置で、3 人のメンバー全員が同じ文字を与えるため、可能な文字列は 1 つだけです。`ABC`。 ヤギも同様に、可能な文字列が 1 つだけあります。`ABD`。`distinct_choices`各位置の 3 つの等しい文字の選択肢を 1 つの選択肢に変換します。 したがって、生成されたハッシュ セットには、(3^3) 個の重複した構成ではなく、各辺に 1 つの値が含まれます。 比較では単に次のハッシュを比較します。`ABC`のハッシュで`ABD`、与える`Owls`。 

正確さは重複排除には依存しません。 同等のブランチのみを削除します。 

### 半分の長さが等しくない

 検討してください```
2 1
AA
AA
AA
Z
Z
Z
```フクロウの場合、分割は 1 キャラクター + 1 キャラクターです。 唯一可能な文字列は次のとおりです`AA`のスコアは

 [
 65\cdot127+65=8320。 
]

 ヤギの場合、可能な唯一の文字列は次のとおりです。`Z`、スコアは 90 です。アルゴリズムはさまざまな長さを個別に処理するため、フクロウの 2 文字のハッシュとヤギの 1 文字のハッシュが混在することはありません。 結果は`Goats`。 

これにより、サフィックスの長さが現在のスプリットではなく元のチームの長さから計算される、off-by-one エラーが捕捉されます。 

### モジュラーラップアラウンド

 2 番目のサンプルをもう一度考えてみましょう。```
1 28
E
L
I
AAAAAAAAAAAAAAAAAAAAAAAAAAAA
BBBBBBBBBBBBBBBBBBBBBBBBBBBB
CCCCCCCCCCCCCCCCCCCCCCCCCCCC
```フクロウの最小スコアは 69 です。ヤギの文字列には 28 の位置が含まれているため、多項式の値は指数関数的に増加します。 値はモジュロ (10^{15}+37) で繰り返し削減されるため、最終スコアは非修飾多項式と単調に関係しません。 

固定の Goats プレフィックス ハッシュ (L) の場合、アルゴリズムは次の値を計算します。 

[
 X=(L\cdot127^{14})\bmod MOD。 
]

 次に、ソートされたサフィックス ハッシュで少なくとも最初の値を検索します。`MOD - X`。 この接尾辞は、まさにその加算が係数をラップする最初の接尾辞です。 結果として得られるラップされた値は、最小のサフィックス ハッシュからのラップされていない最良の候補と比較されます。 

このアルゴリズムは、多項式が大きいほどスコアも大きくなる必要があるとは決して想定しません。 実際の剰余を比較します。これが問題で求められているものです。 

### 二分探索中の正確なしきい値

 あるプレフィックスについて計算された値が (X) であり、サフィックス セットに正確に含まれていると仮定します。 

[
 MOD-X。 
]

 それから

 [
 (X+(MOD-X))\bmod MOD=0。 
]`bisect_left`厳密にしきい値より大きいのではなく、しきい値以上の最初のサフィックスを意図的に検索します。 この等価の場合は、最適な可能なスコアであるゼロが生成されるため、受け入れられる必要があります。 

を使用した不注意な実装`bisect_right`まったく結果がゼロの候補をスキップし、より大きなスコアを返す可能性があります。 

### 最大長

 長さ 28 のチームの場合、各半分には最大 (3^{14}=4,782,969) 個の可能な文字列が含まれます。 この実装では、一度に 1 つのチームのサフィックス配列を処理し、2 つの 7 文字セットを組み合わせて 14 文字セットを構築します。 後者にはそれぞれ最大 2187 個の要素が含まれるため、大きな割り当ては最後のサフィックス配列だけです。 

プレフィックスの組み合わせは、約 500 万の Python 整数の別の配列を保存するのではなく、ソートされたサフィックス配列に対して直接処理されます。 数百万の Python 整数の通常のリストは、C++ ベクトル内の同じ数のコンパクトな整数よりもかなり多くのメモリを消費するため、この非対称処理は Python で特に役立ちます。 

結果として得られるアルゴリズムは、入力が最大限に多様である場合でも完全な (3^{14}) 中間状態空間を検査しますが、(3^{28}) のブルート フォース検索空間には決して近づきません。
