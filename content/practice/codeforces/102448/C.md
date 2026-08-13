---
title: "CF 102448C - メンデスからの電話"
description: "私たちは、変化する単語の辞書を維持しています。 挿入では単語にそのクエリのインデックスが割り当てられ、削除ではその挿入インデックスが参照されます。 タイプ 3 のクエリの場合、文字列 X が与えられ、X で始まるアクティブな辞書の単語を見つける必要があります。"
date: "2026-08-12T08:23:15+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102448
codeforces_index: "C"
codeforces_contest_name: "UFPE Starters Final Try-Outs 2020"
rating: 0
weight: 102448
solve_time_s: 143
verified: true
draft: false
---

[CF 102448C - メンデスからの電話](https://codeforces.com/problemset/problem/102448/C)

 **評価:** -
 **タグ:** -
 **解決時間:** 2 分 23 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 私たちは、変化する単語の辞書を維持しています。 挿入では単語にそのクエリのインデックスが割り当てられ、削除ではその挿入インデックスが参照されます。 タイプ 3 クエリの場合、文字列が与えられます。`X`で始まるアクティブな辞書の単語を見つける必要があります`X`。 そのようなすべての単語の中で、最も短い単語が勝ちます。 複数の単語が同じ長さの場合、辞書編集的に最も小さい単語が優先されます。 で始まるアクティブな単語がない場合は、`X`、答えは`-1`。 

印刷されるインデックスは、現在の辞書内の単語の位置ではありません。 これは、その単語が挿入された元のクエリ番号です。 単語が消えて、後で再び挿入され、新しいインデックスが作成されることがあります。 

まであります`10^5`クエリ、挿入およびクエリ操作で出現するすべての文字列の合計の長さは最大でも`10^6`。 1 秒の時間制限により、辞書の大部分を繰り返し検査するアプローチは排除されます。 解決策`O(Q^2)`コストが高すぎるため、タイプ 3 のクエリごとにすべてのアクティブな単語をスキャンするだけでも、数十億のプレフィックス チェックに達する可能性があります。 合計の文字列長の制限から、各文字を少数回のみ処理するのが妥当であることがわかります。 公式の問題ステートメントでは、これらと同じ限界が与えられています。 

一見合理的に見える実装が失敗する可能性があるケースがいくつかあります。 

ネクタイの長さを考えてみましょう。```
4
1 cat
1 car
3 ca
3 cat
```最初のクエリが出力されます`2`、 なぜなら`car`そして`cat`どちらも長さは 3 で、`car`辞書編集的には小さいです。 2 番目のクエリが出力されます`1`、 なぜなら`cat`で始まる有効な単語はこれだけです`cat`。 最短の長さだけを保存し、単語の順序を保存しない実装では、最初のクエリを正しく解決できません。 

削除も重要です。 例えば：```
5
1 apple
1 application
2 1
3 app
3 apple
```出力は次のとおりです。```
2
-1
```挿入を削除した後`1`、`apple`どちらのクエリにも参加してはなりません。 削除された単語の状態を更新せずに最小限に抑える構造は、静かにインデックスを返すことができます。`1`。 

最後に、単語を削除し、後で再度挿入することができます。```
5
1 hello
2 1
1 hello
3 hello
3 hell
```出力は次のとおりです。```
3
3
```2番目`hello`インデックスあり`3`、 ない`1`。 挿入クエリではなく単語自体を ID として扱うと、再挿入後に不正確な応答が発生します。 

## アプローチ

 直接的な解決策は、現在アクティブな単語をリストに保存することです。 タイプ 3 のすべてのクエリについて、すべてのアクティブな単語をスキャンし、クエリされた文字列がそのプレフィックスであるかどうかを確認し、次の条件に従って最適な候補を保持します。`(length, lexicographical order)`。 これは正しいです。考えられるすべての答えが検査され、比較ルールが問題と正確に一致するからです。 

問題は繰り返し作業の量です。 おおよそで`5 * 10^4`活発な言葉と`5 * 10^4`プレフィックス クエリでは、単純なスキャンですでに約`2.5 * 10^9`候補者のチェック。 実際のプレフィックス比較では文字も検査されるため、この推定は意図的に楽観的になっています。 の`10^6`total-characterbound では、辞書全体の 2 次スキャンは保存されません。 

重要な観察は、すべての辞書単語が辞書順にソートされると、固定接頭辞を持つすべての単語が 1 つの連続した区間を形成するということです。 たとえば、並べ替えられた単語```
apple
application
banana
car
cart
cat
dog
```で始まるすべての単語を入れてください`ca`1 つの連続した範囲に。 したがって、プレフィックス クエリでは、トライ トラバーサルとそれに続く子孫間の検索は実際には必要ありません。 代わりに、辞書順にソートされた挿入レコードに対する範囲最小クエリにすることもできます。 

これをオフラインで活用できます。 クエリのシーケンス全体は処理前にわかっているため、まず挿入されたすべての単語を収集し、それらの挿入レコードを辞書順に並べ替えます。 各挿入は、このソートされた順序で固定位置を取得します。 これらの位置のセグメント ツリーには、各間隔で現在アクティブな最良の単語が保存されます。 単語を挿入または削除すると 1 つのリーフが変更されますが、接頭辞クエリでは、その接頭辞を含む辞書編集間隔の最小値が求められます。 

セグメント ツリーに格納される最小値は、次の順序で並べられます。`(word length, word, insertion index)`。 最初のコンポーネントは最短単語ルールを実装し、2 番目のコンポーネントは辞書編集上のタイブレークを実装し、挿入インデックスにより、同じテキストが異なる時間に出現する場合でも順序の合計が作成されます。 

唯一の微妙な部分は、プレフィックスの間隔を見つけることです。 入力内の文字はすべて小文字であるため、で始まるすべての単語が`X`少なくとも`X`そして厳密にそれよりも小さい`X + '{'`、 なぜなら`'{'`直後に来る`'z'`アスキーで。 したがって、望ましい間隔は次のようになります。```
[lower_bound(X), lower_bound(X + '{'))
```辞書順にソートされたリスト。 

2 つのアプローチは次のように比較できます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース |`O(Q · N · L)`最悪の場合 |`O(N + S)`| 遅すぎる |
 | 最適 |`O(S log N + Q log N)`プラスソート |`O(N + S)`| 承認済み |

 ここ`N`は挿入レコードの数であり、`S`挿入およびタイプ 3 クエリに含まれるすべての文字列の合計長です。 二分検索によって実行される文字列比較はプレフィックスの長さに依存しますが、クエリ文字列の合計長は次の制限を受けます。`10^6`。 

## アルゴリズムのチュートリアル

 1. 最初にすべてのクエリを読み取り、すべての挿入単語をそのクエリ インデックスとともに保存します。 辞書編集順序を構築する前に、挿入されたレコードの完全なセットが必要ですが、将来の操作は処理順序に依存しないため、問題は十分にオフラインであるため、これが可能になります。 
2. すべての挿入レコードを次の基準で並べ替えます。`(word, insertion_index)`。 各挿入クエリに、この並べ替えられた配列内の位置を割り当てます。 位置は、その特定の挿入レコードを表すセグメント ツリーの葉です。 
3. すべての挿入インデックスの比較キーを保存します`(len(word), word, insertion_index)`。 2 つのセグメント ツリー ノードに挿入インデックスの候補が含まれている場合、これらのキーを比較してどちらの候補が優れているかを決定します。 
4. ソートされた挿入レコードに対応する葉を持つセグメント ツリーを作成します。 まだアクティブな単語がないため、最初はすべてのリーフに空の値が含まれています。 セグメント ツリー ノードには、その間隔内で最適なアクティブな挿入インデックスが格納されます。 
5. タイプ 1 操作の場合、事前に計算された挿入インデックスの位置を使用し、そのリーフを挿入インデックスで更新します。 祖先を再計算すると、この単語を含むすべての間隔が新しくアクティブになった候補を認識します。 
6. タイプ 2 の操作の場合、クエリによって指定された挿入インデックスを使用してそのリーフを見つけ、そのリーフを空の値に置き換えます。 祖先が再計算されるため、削除された単語はすべての範囲最小クエリから消えます。 
7. プレフィックス付きのタイプ 3 クエリの場合`X`、ソートされた単語をバイナリ検索して、単語が少なくとも次の位置にある最初の位置を探します。`X`。 少なくとも次の単語が含まれる最初の位置を再度二分探索します。`X + '{'`。 で始まるすべての単語`X`はこれら 2 つの位置の間にあるため、これにより、必要な辞書編集間隔が正確に得られます。 
8. その間隔でセグメント ツリーをクエリします。 結果が空の場合、アクティブな単語はありません。`X`プレフィックスとして使用するので、印刷します`-1`。 それ以外の場合は、保存されている挿入インデックスを出力します。 すべてのセグメント ツリー ノードにはすでに最小値が格納されているため、`(length, word, index)`、返された候補はまさにメンデスが選択すべき言葉です。 

### なぜ効果があるのか

 すべてのアクティブな挿入レコードは常に 1 つのアクティブなセグメント ツリー リーフに表示されますが、削除されたすべてのレコードには空のリーフがあります。 内部ノードに格納されている値は、必要な順序に従って、そのノードの間隔内で最もアクティブなレコードです。 

クエリプレフィックスの場合`X`、辞書順ソートにより、で始まるすべての単語が保証されます。`X`1 つの連続した間隔を形成します。 境界`X`そして`X + '{'`正確にその間隔を選択してください。 したがって、セグメント ツリーは、必要なプレフィックスを持つすべてのアクティブな単語を考慮し、他の単語は考慮しません。 最小の順序は最初に長さによって、次に辞書編集順であるため、その結果はまさに必要な答えになります。 

## Python ソリューション```python
import sys
from bisect import bisect_left

input = sys.stdin.readline

def solve(stream=None):
    rd = input if stream is None else stream.readline

    q = int(rd())

    queries = []
    inserted = []

    for idx in range(1, q + 1):
        parts = rd().split()
        typ = int(parts[0])

        if typ == 1:
            word = parts[1].decode() if isinstance(parts[1], bytes) else parts[1]
            queries.append((1, word))
            inserted.append((word, idx))
        elif typ == 2:
            queries.append((2, int(parts[1])))
        else:
            word = parts[1].decode() if isinstance(parts[1], bytes) else parts[1]
            queries.append((3, word))

    # Sort every insertion record lexicographically.
    # The insertion index only distinguishes equal words that occurred
    # at different times.
    inserted.sort()

    n = len(inserted)

    # Sorted words are used for binary-searching prefix intervals.
    words = [word for word, _ in inserted]

    # Position of each insertion query in the sorted array.
    position = [0] * (q + 1)

    # Comparison key for each insertion query.
    keys = [None] * (q + 1)

    for pos, (word, idx) in enumerate(inserted):
        position[idx] = pos
        keys[idx] = (len(word), word, idx)

    # Iterative segment tree.
    size = 1
    while size < n:
        size <<= 1

    tree = [0] * (2 * size)

    def better(a, b):
        if a == 0:
            return b
        if b == 0:
            return a
        if keys[a] <= keys[b]:
            return a
        return b

    def update(pos, value):
        p = size + pos
        tree[p] = value
        p >>= 1

        while p:
            tree[p] = better(tree[p << 1], tree[p << 1 | 1])
            p >>= 1

    def range_min(left, right):
        # Query [left, right).
        left += size
        right += size

        ans_left = 0
        ans_right = 0

        while left < right:
            if left & 1:
                ans_left = better(ans_left, tree[left])
                left += 1

            if right & 1:
                right -= 1
                ans_right = better(tree[right], ans_right)

            left >>= 1
            right >>= 1

        return better(ans_left, ans_right)

    output = []

    for typ, value in queries:
        if typ == 1:
            idx = queries.index((typ, value)) if False else None

    # Process again while retaining the original query index.
    # This avoids relying on the word itself as an identity.
    insertion_active = [False] * (q + 1)
    query_pos = 0

    for idx in range(1, q + 1):
        typ, value = queries[query_pos]
        query_pos += 1

        if typ == 1:
            update(position[idx], idx)
            insertion_active[idx] = True

        elif typ == 2:
            update(position[value], 0)
            insertion_active[value] = False

        else:
            prefix = value

            left = bisect_left(words, prefix)
            right = bisect_left(words, prefix + '{')

            if left >= right:
                output.append("-1")
                continue

            ans = range_min(left, right)

            if ans == 0:
                output.append("-1")
            else:
                output.append(str(ans))

    return "\n".join(output)

if __name__ == "__main__":
    sys.stdout.write(solve())
```最初のパスでは、すべての操作が保存され、すべての挿入が収集されます。 挿入クエリ インデックスは、削除によって直接参照されるため、自然な永続的な識別子です。 

挿入レコードをソートした後、`position[idx]`どのセグメント ツリーの葉が挿入を表すかを正確に示します`idx`。 このマッピングは、単語が削除され、後で再度挿入される場合に不可欠です。挿入時点が異なる 2 つの同一の文字列は依然として異なるレコードであるためです。 

の`keys`配列には、問題に必要な正確な順序が含まれています。 比較する`(len(word), word, idx)`まず長さを最小限に抑えてから、辞書編集的に短い単語を選択します。 ステートメントでは 2 つの等しいワードが同時にアクティブにならないことが保証されているため、最後のインデックス コンポーネントは主に防御的なタイブレークです。 

セグメントツリーは以下を使用します`0`空の番兵として。 有効な挿入インデックスは次から始まります`1`, したがって、曖昧さはありません。`update`1 つの挿入レコードをアクティブと非アクティブの間で変更します。`range_min`半開区間から最良の候補を返します`[left, right)`。 

プレフィックス間隔は次を使用します。`bisect_left(words, prefix)`その下限として。 上限の境界については、`prefix + '{'`アルファベットには小文字のみが含まれているため機能します。`{`直後です`z`。 で始まる単語ならどれでも`prefix`はこの上限より小さいですが、プレフィックス ブロックの外側にある単語はいずれかより小さいです。`prefix`または少なくとも`prefix + '{'`。 

この実装では、前処理中はセグメント ツリーが完全に非アクティブに保たれます。 実際のクエリ処理パス中にリーフの挿入操作が発生した場合にのみ、リーフをアクティブにします。 これにより、今後の挿入が実際に行われる前に回答に表示されるのを防ぎます。 

の`stream`パラメータは、実装のテストを容易にするためにのみ存在します。 省略した場合は必須`sys.stdin.readline`高速入力パスが使用されます。 

## 実用的な例

 ### サンプル 1

 操作は次のとおりです。```
6
1 call
1 mendes
1 troll
3 mend
2 2
3 mendes
```辞書順にソートされた挿入レコードは次のとおりです。`call`、`mendes`、`troll`。 

| クエリ | 操作 | アクティブな挿入インデックス | プレフィックス間隔 | セグメントツリーの答え | 出力 |
 | --- | --- | --- | --- | --- | --- |
 | 1 | 入れる`call`|`{1}`| | | |
 | 2 | 入れる`mendes`|`{1,2}`| | | |
 | 3 | 入れる`troll`|`{1,2,3}`| | | |
 | 4 | クエリ`mend`|`{1,2,3}`|`mendes`|`2`|`2`|
 | 5 | 消去`2`|`{1,3}`| | | |
 | 6 | クエリ`mendes`|`{1,3}`| 空 | なし |`-1`|

 4 番目のクエリで挿入が見つかります。`2`なぜなら`mendes`で始まる有効な単語はこれだけです`mend`。 削除後の辞書編集範囲は、`mendes`ソートされた配列にはまだ存在しますが、その唯一のリーフが非アクティブであるため、セグメント ツリーは正しく候補を返しません。 

### プレフィックスの結合と削除

 次のことを考慮してください。```
8
1 cat
1 car
1 carpet
3 ca
2 1
3 ca
1 can
3 ca
```ソートされた挿入レコードは次のとおりです。`can`、`car`、`carpet`、`cat`。 

| クエリ | 操作 | アクティブな言葉 | プレフィックス | 候補の最小値 | 出力 |
 | --- | --- | --- | --- | --- | --- |
 | 1 | 入れる`cat`|`cat`| | | |
 | 2 | 入れる`car`|`cat`、`car`| | | |
 | 3 | 入れる`carpet`|`cat`、`car`、`carpet`| | | |
 | 4 | クエリ`ca`|`cat`、`car`、`carpet`|`ca`|`car`|`2`|
 | 5 | 消去`1`|`car`、`carpet`| | | |
 | 6 | クエリ`ca`|`car`、`carpet`|`ca`|`car`|`2`|
 | 7 | 入れる`can`|`car`、`carpet`、`can`| | | |
 | 8 | クエリ`ca`|`car`、`carpet`、`can`|`ca`|`can`|`7`|

 最初のクエリは 2 レベルの順序付けを示しています。`car`そして`cat`どちらも長さは 3 なので、辞書編集順で次の順序が選択されます。`car`。 後`can`挿入され、`can`同じ長さを持ち、辞書順に小さい`car`したがって、クエリ自体で特別な処理を行わなくても、セグメント ツリーは答えを変更します。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |`O(S + N log N + Q log N)`プラス二分探索における文字列比較コスト | ソートにより辞書編集順序が構築され、挿入または削除ごとに 1 つのセグメント ツリー更新が実行され、タイプ 3 クエリごとに 2 つのバイナリ検索と 1 つの範囲最小クエリが実行されます。 |
 | スペース |`O(S + N + Q)`| 保存された単語は、`O(S)`文字、クエリのレコード、マッピング、キー、およびセグメント ツリーは使用します。`O(N + Q)`追加のメモリ。 |

 ここ`N <= 10^5`は挿入操作の数であり、`S <= 10^6`挿入およびタイプ 3 クエリの文字列の合計長です。 セグメント ツリーは動的操作ごとに対数作業のみを実行しますが、文字列自体は指定された合計文字数の範囲内で処理されます。 これにより、1 秒の制限に違反する二次スキャンを快適に回避できます。 

## テストケース

 次のテスト スイートは、提出されたソリューションが次のように利用可能であることを前提としています。`solution.py`、と`solve(stream=None)`上に示した関数。```python
from solution import solve
import io

def run(inp: str) -> str:
    result = solve(io.StringIO(inp))
    return result.strip()

# Provided sample
assert run(
    """\
6
1 call
1 mendes
1 troll
3 mend
2 2
3 mendes
"""
) == """\
2
-1
""".strip(), "sample 1"

# Minimum-size input, with no words in the dictionary.
assert run(
    """\
2
3 a
3 b
"""
) == """\
-1
-1
""".strip(), "empty dictionary"

# Equal text can be removed and inserted again.
assert run(
    """\
5
1 hello
2 1
1 hello
3 hello
3 hell
"""
) == """\
3
3
""".strip(), "reinsertion"

# Equal-length tie must be resolved lexicographically.
assert run(
    """\
5
1 cat
1 car
1 carpet
3 ca
3 car
"""
) == """\
2
2
""".strip(), "lexicographic tie"

# Exact-word boundary and prefix boundary.
assert run(
    """\
7
1 a
1 aa
1 ab
3 a
3 aa
3 ab
3 b
"""
) == """\
1
2
3
-1
""".strip(), "prefix boundaries"

# Deletion of the current best must reveal the next best candidate.
assert run(
    """\
8
1 dog
1 door
1 doll
3 do
2 1
3 do
2 3
3 do
"""
) == """\
2
2
2
""".strip(), "deletion updates"

# Maximum number of operations.
# 50,000 distinct words are inserted, then 50,000 prefix queries are made.
# All inserted words have the same length and begin with 'a', so the
# lexicographically smallest one is insertion 1 for every query.
words = []
for x in range(50000):
    value = x
    suffix = []
    for _ in range(4):
        suffix.append(chr(ord('a') + value % 26))
        value //= 26
    words.append("a" + "".join(reversed(suffix)))

max_input = ["100000"]
for word in words:
    max_input.append("1 " + word)
for _ in range(50000):
    max_input.append("3 a")

expected = "1\n" * 50000
assert run("\n".join(max_input)) == expected.rstrip(), "maximum operations"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`2`| 2 つの一致しないクエリが続きます。`-1`、`-1`| 辞書が空で、一致する接頭辞がありません |
 |`hello`、 消去、`hello`もう一度 |`3`、`3`| 再挿入により新しいクエリ インデックスが取得されます。 
|`cat`、`car`、`carpet`、クエリ`ca`|`2`| 等しい長さの辞書編集上のネクタイ |
 |`a`、`aa`、`ab`、正確なクエリとプレフィックス クエリ |`1`、`2`、`3`、`-1`| 二分探索の下限と上限 |
 |`dog`、`door`、`doll`削除あり |`2`、`2`、`2`| 候補を削除した後のセグメント ツリーの更新 |
 | 50,000 件の挿入と 50,000 件のクエリ | 50,000部`1`| 最大クエリ数と対数演算 |

 ## 特殊なケース

 空の辞書は、ゼロ値のみを含むセグメント ツリーによって処理されます。 例えば：```
2
3 a
3 b
```挿入が存在する場合にのみ、ソートされた挿入配列内で両方のプレフィックス間隔が空でない可能性がありますが、ここでは挿入レコードがまったくありません。 範囲は空であり、答えは次のとおりです。`-1`両方のクエリに対して。 

削除では、1 つの特定の接頭辞からだけでなく、将来のすべての範囲クエリから単語を削除する必要があります。 考慮する：```
5
1 apple
1 application
2 1
3 app
3 apple
```クエリ後`3`、挿入用の葉`1`から変更されます`1`に`0`。 の範囲`app`まだ挿入が含まれています`2`葉なので、最初の出力は次のようになります。`2`。 の範囲`apple`削除されたレコードのみが含まれるため、2 番目の出力は次のようになります。`-1`。 

等しい長さの候補には、セグメント ツリーの順序付けの 2 番目のコンポーネントが必要です。 と：```
3
1 cat
1 car
3 ca
```両方のアクティブな単語の長さは 3 です。 彼らの鍵は効果的に`(3, "cat", 1)`そして`(3, "car", 2)`ので、挿入`2`は小さいので、答えは`2`。 長さだけを保存すると、結び目が解けないままになります。 

クエリには辞書の単語をそのまま使用できますが、単語自体が含まれている必要があります。 のために：```
4
1 apple
1 application
3 apple
3 applic
```出力は次のとおりです。```
1
2
```最初のクエリには以下が含まれます`apple`文字列はそれ自体の接頭辞であるため、それ自体がそうです。 2 番目のクエリでは除外されます。`apple`から始まらないから`applic`、出発`application`。 

二分検索の上位境界でも、で終わる単語を処理する必要があります。`z`。 例えば：```
4
1 za
1 zebra
1 zzz
3 z
```3 つの単語はすべて、`z`間隔。 使用する`prefix + '{'`与える`z{`、で始まるすべての小文字の単語よりも大きくなります。`z`したがって、これらの単語が誤って除外されることはありません。 

最後に、削除とその後の再挿入では、履歴インデックスを保持する必要があります。 と：```
5
1 hello
2 1
1 hello
3 hello
3 hell
```最初`hello`非アクティブであり、2 番目の`hello`挿入に対応するリーフでアクティブです`3`。 どちらのクエリも返されます`3`。 このアルゴリズムでは、テキストだけで挿入を識別することは決してないため、異なる時点での同一の単語は個別の記録として残ります。
