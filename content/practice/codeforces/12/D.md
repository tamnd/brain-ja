---
title: "CF 12D - ボール"
description: "それぞれの女性は、美しさ、知性、豊かさという 3 つの独立した価値観によって表現されます。 3 つの次元すべてにおいて厳密に値が大きい女性が他に存在する場合、その女性は「自殺犯の可能性」があります。"probable self-mur"
date: "2026-05-28T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "data-structures", "sortings"]
categories: ["algorithms"]
codeforces_contest: 12
codeforces_index: "D"
codeforces_contest_name: "Codeforces Beta Round 12 (Div 2 Only)"
rating: 2400
weight: 12
solve_time_s: 112
verified: true
draft: false
---
[CF 12D - ボール](https://codeforces.com/problemset/problem/12/D)

 **評価:** 2400
 **タグ:** データ構造、並べ替え
 **解決時間:** 1 分 52 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 それぞれの女性は、美しさ、知性、豊かさという 3 つの独立した価値観によって表現されます。 3 つの次元すべてにおいて厳密に値が大きい女性が他に存在する場合、その女性は「自殺の可能性のある人」になります。 

正式には、女性の場合`i`が存在するかどうかを知る必要があります。`j`次のような:

 -`B[j] > B[i]`-`I[j] > I[i]`-`R[j] > R[i]`この条件を満たす女性が何人いるかを数えることです。 

入力は 3 つの長さの配列を与えます`N`。 の`k`3 つの配列の 3 番目の位置が 1 人の女性を形成します。 出力は単一の整数、つまり支配された女性の数です。 

制約によってソリューションが完全に形作られます。`N`と同じ大きさにすることができます`500000`、二次関数は除外されます。 単純なペアごとの比較には約`2.5 * 10^11`2 秒の制限内では不可能な最悪の場合のチェックを実行します。 平`O(N sqrt N)`この規模では慎重な実装が必要ですが、`O(N log N)`自然なターゲットです。 

厳密な不等式により、いくつかの微妙なエッジケースが発生します。 

次の入力を考えてみましょう。```
2
5 5
3 4
1 2
```どちらの女性も同じ美しさを持っています。 たとえ二番目の女性がより大きな知性と豊かさを持っていたとしても、美しさも厳密に大きくなければならないため、どちらも相手を支配しません。 不注意なスイープ ラインの実装により、同じ美しさを誤ってまとめて処理すると、そのうちの 1 つがカウントされる可能性があります。 

もう 1 つの注意が必要なケースは、重複した知性値です。```
2
1 2
5 5
1 2
```二番目の女性の方が美しさと豊かさはありますが、知性は同等であるため、支配は起こりません。 確認するだけなら`>=`一次元を間違えると答えが間違ってしまいます。 

3 番目の落とし穴は、複数の女性が同じ美しさと知性を共有している場合に発生します。```
3
1 1 2
1 1 2
1 2 3
```ファーストレディはサードレディによって支配されています。 2番目の女性も3番目の女性に支配されています。 しかし、最初の女性と二番目の女性は、美しさと知性が同等であるため、処理中に互いに影響を及ぼしてはなりません。 等しいグループ内で順序を間違えると、正確性が静かに損なわれます。 

問題全体は、厳密な不等式を伴う 3 次元の優勢クエリです。 

## アプローチ

 ブルート フォース ソリューションは定義に直接従います。 すべての女性のために`i`、他のすべての女性を反復処理します`j`そして、3 つの属性すべてが厳密に大きいかどうかを確認します。 そのような女性がいる場合は、回答をインクリメントします。 

これが機能するのは、定義自体がペアごとであるためです。 実装は簡単です。```
for every i:
    for every j:
        if Bj > Bi and Ij > Ii and Rj > Ri:
            dominated
```問題は規模だ。 と`N = 500000`、これは大まかに実行されます`500000^2 = 2.5 * 10^11`比較。 高度に最適化された C++ であっても、制限内でこれを処理することはできません。 

重要な観察は、並べ替えによって 1 つの次元を削除できるということです。 

女性を美しさの降順に並べ替えるとします。 女性を処理するとき、以前に処理されたすべての女性はすでに厳密にそれ以上の美しさ、または同等の美しさを持っています。 つまり、3 つの次元すべてを検索する必要がなくなりました。 知っておく必要があるのは次のことだけです。 

> より大きな美しさを持った女性の中に、知性と豊かさの両方がより大きい女性が存在しますか？ 

これにより、問題は動的な 2 次元の優勢クエリに帰着します。 

今は知性に焦点を当てましょう。 女性を美しい順に処理すれば、知性によってインデックスされた情報を維持できます。 あらゆる知的価値について、私たちは大きな美しさを持つ女性の間でこれまでに見られた最大の豊かさを知りたいと思っています。 

じゃあ今の女性の場合は`(B, I, R)`:

 - 厳密に次の値よりも大きいすべての知性をクエリします。`I`- その中で最大の豊かさを見つけてください
 - その最大リッチ度が次の値よりも大きい場合`R`、支配が存在する

 これは範囲最大クエリの問題になります。 

知性値は以下まで`10^9`, そのため、直接インデックスを作成することは不可能です。 座標圧縮は、すべての個別の知性値を範囲にマッピングすることでこの問題を修正します。`[1..M]`。 

最終的な構造は次のとおりです。 

- 美しさの降順で並べ替えます
 - 同等の美人グループをまとめて処理する
 - 圧縮された知性座標に対してセグメント ツリーを使用する
 - それぞれの知性に最大限の豊かさを蓄える

 平等な美しさのグループ化が不可欠です。 支配には厳密により大きな美しさが必要であるため、同じ美しさを持つ女性が互いに影響を及ぼしてはなりません。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(N²) | お(1) | 遅すぎる |
 | 最適 | O(N log N) | O(N) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. トリプルを構築する`(beauty, intellect, richness)`すべての女性のために。 

女性ごとに 1 つのタプルを使用すると、並べ替えと処理が容易になります。 
2. すべての知性値を座標圧縮します。 

セグメント ツリーには相対的な順序付けのみが必要で、実際の値は必要ありません。 圧縮では、次のような大きな値が変換されます。`10^9`から密なインデックスに変換`1`に`M`。 
3. すべての女性を美しさの降順に並べ替えます。 

ソート後、すでに処理されたすべてのグループは、現在のグループよりも厳密に大きな美しさを持っています。 
4. 女性を同等の美しさを持つグループに分けて処理します。 

これは重要な正確性の詳細です。 同じ美容グループ内の女性はお互いを支配することはできないため、最初にクエリを実行し、後で更新する必要があります。 
5. 現在のグループの各女性について:

 - 圧縮された知性インデックスを次のようにします。`idx`- 範囲に関するセグメントツリーをクエリします。`(idx + 1, M)`- これは質問です：以前に処理され、より大きな知性を備えた女性の中で、最大の豊かさは何ですか？ 
6. クエリされた最大の豊かさが現在の豊かさよりも大きい場合、この女性を支配されているとみなします。 

以前に挿入されたすべての女性も厳密に大きな美しさを持っているため、3 つの厳密な不平等はすべて当てはまります。 
7. グループ内のすべてのクエリが終了したら、セグメント ツリーを更新します。 

各女性について:

 - 位置で`idx`、これまでにない最大の豊かさを蓄えます

 更新を遅らせると、同等の美しさの女性が互いに影響を与えることができなくなります。 
8. すべてのグループが処理されるまで続行します。 

### なぜ効果があるのか

 不変条件は次のとおりです。 

> 美人グループを処理する前に`B`、セグメント ツリーには、美しさが厳密に以下よりも大きい女性が正確に含まれています。`B`。 

降順での並べ替えと更新の遅延のため、この不変条件は常に当てはまります。 

現役女性の場合`(B, I, R)`、より優れた知性を問う`I`以前に処理された以下の女性をすべて検索します。 

- より大きな美しさ
 - より大きな知性

 セグメントツリーには、それらの中で最大の豊富さが格納されます。 その最大値を超えた場合`R`、その後、少なくとも 1 人の女性が 3 次元すべてで現在の女性を支配します。 

そのような豊かさが存在しない場合、より大きな美しさと知性を備えたすべての候補者が依然として豊かさの条件を満たさないため、支配は不可能です。 

このアルゴリズムは、有効なドミネーターを見逃したり、無効なドミネーターを導入したりしません。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

class SegmentTree:
    def __init__(self, n):
        self.n = n
        self.tree = [0] * (4 * n)

    def update(self, node, left, right, idx, value):
        if left == right:
            self.tree[node] = max(self.tree[node], value)
            return

        mid = (left + right) // 2

        if idx <= mid:
            self.update(node * 2, left, mid, idx, value)
        else:
            self.update(node * 2 + 1, mid + 1, right, idx, value)

        self.tree[node] = max(
            self.tree[node * 2],
            self.tree[node * 2 + 1]
        )

    def query(self, node, left, right, ql, qr):
        if ql > right or qr < left:
            return 0

        if ql <= left and right <= qr:
            return self.tree[node]

        mid = (left + right) // 2

        return max(
            self.query(node * 2, left, mid, ql, qr),
            self.query(node * 2 + 1, mid + 1, right, ql, qr)
        )

def solve():
    n = int(input())

    B = list(map(int, input().split()))
    I = list(map(int, input().split()))
    R = list(map(int, input().split()))

    ladies = []

    intellects = sorted(set(I))
    compress = {
        value: idx + 1
        for idx, value in enumerate(intellects)
    }

    for b, i, r in zip(B, I, R):
        ladies.append((b, compress[i], r))

    ladies.sort(reverse=True)

    m = len(intellects)

    seg = SegmentTree(m)

    ans = 0
    ptr = 0

    while ptr < n:
        nxt = ptr

        while nxt < n and ladies[nxt][0] == ladies[ptr][0]:
            nxt += 1

        for k in range(ptr, nxt):
            _, intellect, richness = ladies[k]

            if intellect < m:
                best = seg.query(
                    1,
                    1,
                    m,
                    intellect + 1,
                    m
                )

                if best > richness:
                    ans += 1

        for k in range(ptr, nxt):
            _, intellect, richness = ladies[k]

            seg.update(
                1,
                1,
                m,
                intellect,
                richness
            )

        ptr = nxt

    print(ans)

solve()
```セグメント ツリーには、圧縮された知性によってインデックス付けされた最大の豊かさの値が格納されます。 

の`query`この活動は、現在の知性より厳密に優れたすべての知性の中で最大限の豊かさを要求します。 ツリーにはより大きな美しさを持つ女性のみが含まれているため、クエリは残りの 2 つの条件に正確に一致します。 

同等の美容グループ内での更新の遅れは、最も重要な実装の詳細です。 女性にクエリを行った直後に更新すると、同じ美しさを持つ別の女性が彼女を有効な支配者として誤って扱う可能性があります。 

条件:```
if intellect < m:
```現在のインテリジェンスがすでに最大圧縮値である場合に、空の範囲をクエリすることを回避します。 

セグメント ツリーには、リッチ度が現在のものを超えるかどうかだけが問題となるため、カウントではなく最大リッチ度が保存されます。 最大値だけを保持すれば十分です。 

Python の再帰の深さは、セグメント ツリーの再帰の深さは`O(log N)`。 

## 実用的な例

 ### 例 1

 入力:```
3
1 4 2
4 3 2
2 5 3
```女性たちは次のとおりです。```
(1,4,2)
(4,3,5)
(2,2,3)
```美しさの降順で並べ替えた後:```
(4,3,5)
(2,2,3)
(1,4,2)
```圧縮された知性:```
2 -> 1
3 -> 2
4 -> 3
```| ステップ | 現在の女性 | クエリ範囲 | 最大の豊かさが見つかりました | 支配された？ | 更新後のツリー |
 | --- | --- | --- | --- | --- | --- |
 | 1 | (4,2,5) | (3,3) | 0 | いいえ | idx 2 = 5 |
 | 2 | (2,1,3) | (2,3) | 5 | はい | idx 1 = 3 |
 | 3 | (1,3,2) | 空 | 0 | いいえ | idx 3 = 2 |

 最終的な答え:```
1
```2 番目の女性は、3 つの属性がすべて小さいため、最初の女性よりも優勢です。 

### 例 2

 入力:```
4
5 5 4 3
1 2 3 4
1 2 3 4
```女性:```
(5,1,1)
(5,2,2)
(4,3,3)
(3,4,4)
```| ステップ | 現在のグループ | クエリ結果 | 支配されたカウント |
 | --- | --- | --- | --- |
 | 1 | 美しさ = 5 | 以前の女性はいません | 0 |
 | 2 | 美しさ = 4 | 高等知性者の間では 3 を超える豊かさはありません。 0 |
 | 3 | 美しさ = 3 | 高等知性者の間では 4 を超える豊かさはありません。 0 |

 最終的な答え:```
0
```このトレースは、均等な美しさのグループ化が重要である理由を示しています。 美しさを持った二人の女性`5`must not affect each other.

 ## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(N log N) | sorting plus one query and one update per lady |
 | スペース | O(N) | compressed arrays and segment tree |

 と`N = 500000`、`O(N log N)`ソリューションは制限内で快適にパフォーマンスを発揮します。 セグメント ツリーの操作は対数的であり、メモリ使用量は線形のままです。 

## テストケース```python
# helper: run solution on input string, return output string
import sys
import io

def run(inp: str) -> str:
    input_data = io.StringIO(inp)
    output_data = io.StringIO()

    sys.stdin = input_data
    sys.stdout = output_data

    import sys
    input = sys.stdin.readline

    class SegmentTree:
        def __init__(self, n):
            self.n = n
            self.tree = [0] * (4 * n)

        def update(self, node, left, right, idx, value):
            if left == right:
                self.tree[node] = max(self.tree[node], value)
                return

            mid = (left + right) // 2

            if idx <= mid:
                self.update(node * 2, left, mid, idx, value)
            else:
                self.update(node * 2 + 1, mid + 1, right, idx, value)

            self.tree[node] = max(
                self.tree[node * 2],
                self.tree[node * 2 + 1]
            )

        def query(self, node, left, right, ql, qr):
            if ql > right or qr < left:
                return 0

            if ql <= left and right <= qr:
                return self.tree[node]

            mid = (left + right) // 2

            return max(
                self.query(node * 2, left, mid, ql, qr),
                self.query(node * 2 + 1, mid + 1, right, ql, qr)
            )

    def solve():
        n = int(input())

        B = list(map(int, input().split()))
        I = list(map(int, input().split()))
        R = list(map(int, input().split()))

        intellects = sorted(set(I))

        compress = {
            value: idx + 1
            for idx, value in enumerate(intellects)
        }

        ladies = []

        for b, i, r in zip(B, I, R):
            ladies.append((b, compress[i], r))

        ladies.sort(reverse=True)

        seg = SegmentTree(len(intellects))

        ans = 0
        ptr = 0
        m = len(intellects)

        while ptr < n:
            nxt = ptr

            while nxt < n and ladies[nxt][0] == ladies[ptr][0]:
                nxt += 1

            for k in range(ptr, nxt):
                _, intellect, richness = ladies[k]

                if intellect < m:
                    best = seg.query(
                        1,
                        1,
                        m,
                        intellect + 1,
                        m
                    )

                    if best > richness:
                        ans += 1

            for k in range(ptr, nxt):
                _, intellect, richness = ladies[k]

                seg.update(
                    1,
                    1,
                    m,
                    intellect,
                    richness
                )

            ptr = nxt

        print(ans)

    solve()

    sys.stdout = sys.__stdout__

    return output_data.getvalue()

# provided samples
assert run(
"""3
1 4 2
4 3 2
2 5 3
"""
) == "1\n", "sample 1"

# minimum size
assert run(
"""1
5
5
5
"""
) == "0\n", "single lady"

# all equal
assert run(
"""4
1 1 1 1
2 2 2 2
3 3 3 3
"""
) == "0\n", "all equal"

# strictly increasing domination chain
assert run(
"""4
1 2 3 4
1 2 3 4
1 2 3 4
"""
) == "3\n", "every lady except maximum dominated"

# equal beauty should not dominate
assert run(
"""2
5 5
1 2
1 2
"""
) == "0\n", "strict beauty inequality"

# equal intellect should not dominate
assert run(
"""2
1 2
5 5
1 2
"""
) == "0\n", "strict intellect inequality"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 独身女性 | 0 | 最小入力サイズ |
 | すべて等しい値 | 0 | 厳密な不等式が必要 |
 | トリプルを厳密に増やす | 3 | 複数の有効な支配 |
 | イコールビューティーケース | 0 | 同じ美しさでは支配することはできません。 
| 知性が等しい場合 | 0 | 同じ知性が支配することはできません。 

## 特殊なケース

 同等の美しさの値を考慮します。```
2
5 5
1 2
1 2
```並べ替えると、両方の女性が同じグループに属します。 このアルゴリズムは、更新の前に、まずすべてのクエリを実行します。 どちらのクエリでもセグメント ツリーはまだ空であるため、どちらの女性も支配されているとは見なされません。 

更新がすぐに行われると、同じ美しさにもかかわらず、2 番目の女性はツリー内の最初の更新を誤って認識してしまいます。 

ここで、等しい知性値を考えてみましょう。```
2
1 2
5 5
1 2
```圧縮された知性インデックスは同一です。 クエリを実行するとき、アルゴリズムは現在の知性より厳密に大きい範囲のみを検索します。```
idx + 1 ... M
```平等な知性の立場は排除されるので、支配は正しく拒否されます。 

最後に、繰り返される美しさと知性を一緒に考えてみましょう。```
3
1 1 2
1 1 2
1 2 3
```The first two ladies share both beauty and intellect. 3番目が両方を支配します。 

処理順序:

 - グループ`beauty = 2`ツリーを豊かに更新します`3`- グループ`beauty = 1`ツリーにクエリを実行します
 - クエリ範囲には同等の知性は含まれません
 - 発見された最大の豊かさは、`3`、両方を上回ります`1`そして`2`答えは次のようになります`2`、それは正しいです。
