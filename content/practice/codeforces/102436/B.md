---
title: "CF 102436B - 最小化の試み"
description: "小文字の文字列のコレクションが与えられます。 個々の文字を置換する場合がありますが、置換ごとに 1 回の操作が必要です。 すべての置換の後、結果の文字列から通常のトライを構築します。 目標は、置き換えの数を直接的に最小限に抑えることではありません。"
date: "2026-08-09T00:12:16+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102436
codeforces_index: "B"
codeforces_contest_name: "Innopolis Open 2019-2020, qualification, contest 1"
rating: 0
weight: 102436
solve_time_s: 81
verified: true
draft: false
---

[CF 102436B - 最小化の試行](https://codeforces.com/problemset/problem/102436/B)

 **評価:** -
 **タグ:** -
 **解決時間:** 1 分 21 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 小文字の文字列のコレクションが与えられます。 個々の文字を置換する場合がありますが、置換ごとに 1 回の操作が必要です。 すべての置換の後、結果の文字列から通常のトライを構築します。 目標は、置き換えの数を直接的に最小限に抑えることではありません。 代わりに、まず、結果として得られるトライのノード数が最小限になるようにし、その最小トライ サイズを達成するすべての変換の中で、置換が最も少ない変換を必要とします。 

重要な構造上の事実は、文字を置き換えても文字列の長さは変わらないということです。 最長の文字列の長さが L であるとします。長さ L の文字列を含むトライには、深さ 0 がルートである 0 から L までのすべての深さに少なくとも 1 つの頂点が含まれている必要があります。 したがって、L+1 個より少ない頂点を持つトライは存在できません。 すべての文字列が存在するすべての位置で同じ文字を使用するようにすることで、常に正確に L+1 個の頂点を実現できます。 結果のトライは 1 つのチェーンにすぎず、短い文字列は中間の頂点で終わります。 

したがって、実際の最適化問題ははるかに単純になります。 位置 j では、長さが j より大きい文字列のみを考慮します。 すべての文字列がその位置で使用する 1 つの文字を選択します。 これらの文字列の中で文字が k 回出現する場合、その文字を選択するには 1 文字おきに変更する必要があるため、コストはアクティブな文字列の数から k を引いたものになります。 最良の選択は、最も頻繁に使用される文字です。 

制約があるため、線形に近い解決策が必要になります。 最大 100,000 個の文字列を含めることができ、個々の文字列の長さは 100,000 で、合計文字数は最大 1000,000 です。合計入力サイズに比例するソリューションが理想的です。 アルファベットには 26 個の小文字の英字しか含まれていないため、定数係数 26 を追加しても無害です。 文字列のすべてのペアに対する二次スキャンや、変換される可能性のある文字列を列挙する方法は、まったく実用的ではありません。 

不注意な実装によって誤って処理される可能性のあるエッジケースがいくつかあります。 まず、単一の文字列にはすでに最小限のトライが含まれているため、置換は必要ありません。```
1
abc
```正しい出力は次のとおりです`0`。 トライはすでにチェーンであるため、マージするものは何もありません。 

2 番目のケースには、異なる長さの文字列が含まれます。 短い文字列に存在しない位置では、その文字列は頻度の計算に参加してはなりません。```
3
a
ab
bb
```位置 0 の文字は次のようになります。`a, a, b`ですので、`b`に`a`1つかかります。 位置 1 のみ`ab`そして`bb`存在し、両方ともすでに含まれています`b`なのでコストはゼロです。 正しい答えは、`1`。 すべての入力文字列をすべての位置でカウントすると、位置 1 に 1 文字の文字列が誤って含まれてしまいます。 

3 番目のエッジ ケースは、周波数のタイです。```
2
ab
ba
```位置ゼロでは、`a`そして`b`それぞれ 1 回ずつ発生するため、1 回の置き換えは避けられません。 同じことが位置 1 でも起こります。 答えは、`2`。 同点で選択された特定の文字は、置換の数に影響しません。 

## アプローチ

 この問題を直接的に強引に考える方法は、ポジションごとに最終的なキャラクターを個別に決定することです。 位置ごとに、可能な 26 文字をすべて試して、変更する必要があるアクティブな文字列の数を数えることができます。 入力の合計長が S の場合、可能性のあるすべての文字について関連する文字を再スキャンするには、最悪の場合でも 26S 文字の検査が必要になります。 S=1000000 の場合、データ構造と Python のオーバーヘッドを考慮する前の検査は 26000000 回になります。 1 秒の制限の下では、特に周波数情報を直接収集できる場合には、これは不必要な作業量となります。 

さらに悪い総当たり解釈もあり、最大長の可能な最終文字列をすべて列挙します。 それには、最大 26 個の L の選択肢を考慮する必要がありますが、非常に小さい L の場合でも不可能になります。この列挙が不要な理由は、異なる位置での選択肢が相互作用しないためです。 置換によって文字が挿入または削除されることはないため、位置 j の文字を置換しても、位置 j+1 に出現する文字には影響しません。 

重要な洞察は、トライレベルをレベルごとに見ることです。 深さ j では、j より長いすべての文字列は、深さ j の接頭辞から次の文字までのエッジを 1 つだけ提供します。 このような 2 つの文字列で異なる文字が使用されている場合、トライは分岐する必要があります。 絶対的な下限は深さごとに 1 つの頂点であるため、最小のトライは各深さで使用される文字が 1 つだけの場合に正確に取得されます。 

それが認識されると、あらゆる深さでの最適化が独立して行われます。 その位置に 26 文字の c a 、c b 、…、c z が出現する場合、文字 x を選択するとコストがかかります

 (アクティブな文字列の数)−c x 。 

最小値は c x を最大化することで得られます。 したがって、ポジションの貢献度は単純に次のようになります。 

アクティブな文字列 - 最大周波数。 

文字列を読み取るときに、これらの頻度を蓄積できます。 公式リファレンス ソリューションは、まさにこの位置ごとの周波数引数を使用します。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | 26 の選択肢を超えるブルート フォース | O(26S) | O(S) | 不必要に遅い |
 | 最終的な文字列を列挙する | O(26L) | 指数 | 不可能 |
 | 最適な周波数計数 | O(S+26L) | O(26L) | 承認済み |

 ここで、S はすべての入力文字列の合計長、L は最大文字列長です。 

## アルゴリズムのチュートリアル

 1. すべての文字列を読み取り、すべての文字列位置の頻度配列を維持します。 位置 j の場合、配列には 26 個のカウンターがあり、各小文字に 1 つずつ対応します。 文字列に文字が含まれる場合`c`位置 j で、対応するカウンタをインクリメントします。 一部の文字列内で実際に発生する位置のみを作成するため、短い文字列は自然に寄与しなくなります。 
2. すべての文字列が処理された後、すべての位置を個別に調べます。 26 個のカウンターの合計が、その位置に到達した文字列の数になります。 現在の位置より短い文字列はその深さのトライには存在しないため、これは必ずしも n である必要はありません。 
3. その位置にある 26 文字の中で最大の頻度を見つけます。 最後のトライにその文字を選択すると、それらの文字列にはすでに目的の文字が含まれているため、正確にその数の置換が保存されます。 
4.追加`active - best`答えに。 他のすべてのアクティブな文字列には異なる文字があるため、この位置で 1 つだけ置換する必要があります。 
5. 蓄積された回答を印刷します。 置換の数のみが重要であるため、結果の文字列やトライを構築する必要はありません。 

### なぜ効果があるのか

 任意の位置 j を考えます。 文字を置き換えても長さは変わらないため、j より長い文字列はすべて、その位置に 1 文字含まれている必要があります。 トライのノード数を最小限にするには、深さ j+1 にトライの頂点が 1 つだけ存在する必要があるため、すべてのアクティブな文字列が同じ文字を持つ必要があります。 

その文字が x であるとします。 j の元の文字が x ではないアクティブな文字列はすべて 1 回の置換を必要としますが、すでに x を含む文字列は置換の必要がありません。 したがって、正確なコストは次のようになります。`active - count[x]`。 最も安価な選択は、最大の頻度を持つキャラクターです。 

この議論は、すべての位置に独立して適用されます。 ある位置で文字を選択しても、別の位置の文字や長さを変更することはできないため、各位置を個別に最小化すると、置換の総数が最小になります。 結果として得られる文字列は、すべての深さで同じプレフィックスを共有し、単一のチェーンを生成するため、可能な最小のトライ サイズが生成されます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

def solve():
    n = int(input())

    # counts[j][c] = number of strings having character c at position j
    counts = []

    for _ in range(n):
        s = input().strip()

        while len(counts) < len(s):
            counts.append([0] * 26)

        for j, ch in enumerate(s):
            counts[j][ord(ch) - ord('a')] += 1

    ans = 0

    for row in counts:
        active = sum(row)
        best = max(row)
        ans += active - best

    print(ans)

if __name__ == "__main__":
    solve()
```の`counts`配列は最適化全体に必要な情報を表します。`counts[j]`位置にあるすべての文字の頻度が含まれます`j`。 の`while`ループは、新しく読み取られた文字列が以前のすべての文字列よりも長い場合にのみ構造を拡大します。 

すべての入力文字列のすべての文字ごとに、カウンターが 1 つ増加します。 これは、アルゴリズムの最初のステップで説明したデータ収集とまったく同じであり、トライを構築する必要はありません。 

入力処理後、`active = sum(row)`実際にその位置に文字がある文字列のみをカウントします。 これにより、さまざまな文字列の長さが自動的に処理されます。`best = max(row)`すでに最も頻繁に出現する文字を選択します。 違いは、その深さで必要な置換の最小数です。 

Python には整数オーバーフローの問題はありません。 固定幅言語であっても、答えは最大でも入力長の合計で、最大でも 1000000 です。実装では、ソート、個々の文字列のハッシュ化、トライ ノードの構築も回避され、ホット ループが入力サイズに比例するように維持されます。 

アルファベットが固定されているため、各位置の 26 要素の行は小さくなります。 結果として得られる 26L のストレージは、指定された最大長未満で最大 260 万個のカウンターになります。 

## 実用的な例

 公式サンプルは次のとおりです。```
4
min
trie
task
mini
```最大長は 4 であるため、位置には 0 から 3 までのインデックスが付けられます。 

| ポジション | アクティブキャラクター | 周波数 | ベスト | 貢献 | 答え |
 | --- | --- | --- | --- | --- | --- |
 | 0 | m、t、t、m | m:2、t:2 | 2 | 2 | 2 |
 | 1 | 私、r、a、i | i:2、r:1、a:1 | 2 | 2 | 4 |
 | 2 | n、i、s、n | n:2、i:1、s:1 | 2 | 2 | 6 |
 | 3 | e、k、i | e:1、k:1、i:1 | 1 | 2 | 8 |

 最初の 3 つの位置では、2 つの文字列がすでに最適な文字と一致しており、2 つは変更する必要があります。 最後の位置には 3 つの文字列のみが存在し、3 つの文字はすべて異なるため、2 回の置換が必要です。 

考えられる最適な結果の 1 つは次のとおりです。`min`、`mine`、`mine`、`mine`と公式の説明にある通りです。 結果として得られるトライは、ルートを含む 5 つのノードを含むチェーンであり、これは長さ 4 の最長文字列の最小値です。 

2 番目の例は、さまざまな長さを示しています。```
3
a
ab
bb
```| ポジション | アクティブキャラクター | 周波数 | ベスト | 貢献 | 答え |
 | --- | --- | --- | --- | --- | --- |
 | 0 | a、a、b | a:2、b:1 | 2 | 1 | 1 |
 | 1 | b、b | b:2 | 2 | 0 | 1 |

 位置ゼロで変化`bb`の中へ`ab`1回の交換費用がかかります。 位置 1 には 1 文字の文字列`a`この深さには存在しないため、数えてはなりません。 残りの 2 つの文字列はすでに一致しています`b`なので、追加の交換は必要ありません。 

最終的な文字列は次のようになります。`a`、`ab`、`ab`、そのトライはチェーンです。 答えは、`1`。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(S+26L) | すべての入力文字は 1 回処理され、その後 26 カウンターの行ごとにスキャンされます。 
| スペース | O(26L) | 可能なすべての位置に対して 1 つの 26 カウンタ配列が保存されます。 

ここで、S≤1000000、L≤100000です。 このアルゴリズムは、入力文字ごとに数回の操作と最大 260 万回のカウンタ チェックのみを実行するため、1 秒の制限時間で意図した線形スケールのアプローチに快適に適合します。 

## テストケース

 以下のテスト ハーネスは、提出されたソリューションと同じロジックを使用しますが、それを関数として公開しているため、各ケースを次のようにチェックできます。`assert`。```python
import io
import sys

def solve_data(inp: str) -> str:
    data = inp.split()
    it = iter(data)

    n = int(next(it))
    counts = []

    for _ in range(n):
        s = next(it)

        while len(counts) < len(s):
            counts.append([0] * 26)

        for j, ch in enumerate(s):
            counts[j][ord(ch) - ord('a')] += 1

    ans = 0

    for row in counts:
        ans += sum(row) - max(row)

    return str(ans)

# Provided sample
assert solve_data(
    """4
min
trie
task
mini
"""
) == "8", "sample 1"

# Minimum-size input
assert solve_data(
    """1
a
"""
) == "0", "single string needs no replacement"

# All strings are already identical
assert solve_data(
    """4
abc
abc
abc
abc
"""
) == "0", "all strings already form a chain"

# Different lengths, shorter strings must not affect later positions
assert solve_data(
    """3
a
ab
bb
"""
) == "1", "short strings must be ignored at deeper positions"

# Tie at every position
assert solve_data(
    """2
ab
ba
"""
) == "2", "ties require one replacement at each position"

# Maximum-size shape: 100000 strings of length 1
# 50000 are 'a', 50000 are 'b', so exactly 50000 replacements are needed.
inp = "100000\n" + "a\n" * 50000 + "b\n" * 50000
assert solve_data(inp) == "50000", "maximum-size input"

| Test input | Expected output | What it validates |
|---|---:|---|
| `1 / a` | `0` | Minimum-size input and already optimal trie |
| Four copies of `abc` | `0` | All-equal strings |
| `a`, `ab`, `bb` | `1` | Different lengths and inactive positions |
| `ab`, `ba` | `2` | Frequency ties and per-position independence |
| 100000 one-character strings, half `a`, half `b` | `50000` | Maximum input size and linear processing |

## Edge Cases

For a single string such as

```テキスト
 1
 ABC```

there is only one path in the trie. At position zero the only active character is `a`, so the contribution is zero. The same holds for positions one and two. The algorithm returns `0`, correctly recognizing that no branching exists.

For strings of different lengths,

```3
 ある
 腹筋
 bb```

the first position contains `a, a, b`, giving a contribution of `1`. At the second position, only `ab` and `bb` remain active. Both contain `b`, so the contribution is zero and the final answer is `1`. The implementation handles this because it increments a counter only when the current string actually has that position.

For tied frequencies,

```2
 腹筋
 ば```

position zero has one `a` and one `b`, so whichever final character we choose, one replacement is necessary. Position one has the same situation. The answer is `2`. The algorithm only needs the maximum frequency, so ties require no special handling.

For strings whose lengths reach the maximum allowed value, such as many strings of length 100000, the algorithm does not create trie nodes or compare strings against each other. It records one counter update per character and later scans 26 counters per position. This keeps the work bounded by the total input size plus a small alphabet factor.

The most common conceptual mistake is to optimize the trie by thinking about complete strings rather than trie depths. The example

```3
 ある
 腹筋
 bb
 「」

 それが失敗する理由を示します。 文字列`a`深さ 0 では重要ですが、深さ 1 ではトライから消えます。 問題をレベルごとに検討すると、位置の独立性が明確になり、解決策は頻度のカウントに帰着します。
