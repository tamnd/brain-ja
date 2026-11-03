---
title: "CF 120H - 簡潔さは機知の魂です"
description: "最大 200 個の異なる小文字の単語が与えられます。 すべての単語に対して、長さが 1 ～ 4 文字で、文字が元の単語に同じ順序で現れる短い略語を選択する必要があります。"
date: "2026-05-28T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "graph-matchings"]
categories: ["algorithms"]
codeforces_contest: 120
codeforces_index: "H"
codeforces_contest_name: "School Regional Team Contest, Saratov, 2011"
rating: 1800
weight: 120
solve_time_s: 153
verified: false
draft: false
---

[CF 120H - 簡潔さは機知の精神です](https://codeforces.com/problemset/problem/120/H)

 **評価:** 1800
 **タグ:** グラフのマッチング
 **解決時間:** 2 分 33 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 最大 200 個の異なる小文字の単語が与えられます。 すべての単語に対して、長さが 1 ～ 4 文字で、文字が元の単語に同じ順序で現れる短い略語を選択する必要があります。 文字は隣接している必要はないため、すべての略語は単語の一部にすぎません。 

重要な制限は一意性です。 2 つの異なる単語に同じ略語を使用することはできません。 このような割り当てが存在する場合は、単語ごとに有効な略語を 1 つ出力する必要があります。 それ以外の場合は印刷します`-1`。 

単語の長さが短いと、問題の性質が完全に変わります。 各単語の長さは最大 10 で、略語の長さは最大 4 に制限されています。つまり、単語あたりの可能なサブシーケンスの総数は、直接列挙するには十分小さいということです。 

長さ 10 のワードの場合、長さ 1 ～ 4 のサブシーケンスの数は次のとおりです。$$\binom{10}{1} + \binom{10}{2} + \binom{10}{3} + \binom{10}{4}$$

$\binom{10}{1}+\binom{10}{2}+\binom{10}{3}+\binom{10}{4}$これは 385 に相当します。重複を削除した後でも、各単語が提供する略語の候補は数百のみです。 最大 200 単語であれば、グラフベースの一致領域内に問題なく収まります。 

危険な部分は、サブシーケンスを生成しないことです。 本当に難しいのは、衝突を正しく処理することです。 

次の例を考えてみましょう。```
2
abcd
abdc
```どちらの単語も生成できます`ab`、`ac`、`ad`、他にもたくさんあります。 最初の未使用の略語を各単語に割り当てる貪欲なアルゴリズムは、順序によっては失敗する可能性があります。 私たちが与えるなら`abcd`略語`abd`の場合、別のグローバル割り当てが存在する場合でも、2 番目の単語は唯一残っているオプションを失う可能性があります。 

別の微妙なケースは、異なるインデックスの選択によって単語が重複するサブシーケンスを生成する場合に発生します。 

例：```
1
aaaa
```続いて`aa`はさまざまな方法で作成できますが、候補リストに 1 回だけ表示する必要があります。 重複排除を忘れるとグラフが肥大化し、不要なエッジの繰り返しが作成される可能性があります。 

不可能なケースもあります。```
3
a
aa
aaa
```すべての単語は略語のみを生成できます`a`。 略語は区別できる必要があるため、正しい出力は次のようになります。```
-1
```局所的な実現可能性のみをチェックする不注意な実装では、繰り返しの略語が誤って出力されてしまいます。 

## アプローチ

 最も直接的なアイデアは、総当たりバックトラッキングです。 すべての単語について、すべての有効な略語を生成し、未使用の略語を再帰的に割り当ててみます。 最後まで正常に到達したら、課題を印刷します。 

これが機能するのは、検索スペースが有限であるためです。 問題は分岐要素です。 1 つの単語には約 300 の異なる略語がある場合があります。 最悪の場合、すべての組み合わせを試すと天文学的な規模になります。$$300^{200}$$

$300^{200}$枝刈りを行ったとしても、無制限に後戻りすることは絶望的です。 

重要な点は、これは実際にはシーケンスの問題ではないということです。 課題の問題です。 

各単語には、許容される一連の略語があります。 単語ごとに異なる略語を 1 つ選択する必要があります。 これはまさに二部マッチングです。 

2 つの辺を持つグラフを作成します。 

- 左側: 言葉。 
- 右側: 単語の部分列として現れるすべての個別の略語。 

単語を、その単語で使用できるすべての略語に結び付けます。 

ここでのタスクは、すべての単語をカバーする一致を見つけることになります。 このような一致が存在する場合、一致した各エッジは選択された略語を与えます。 

この変換は、最大の 2 部マッチングによってグローバルな競合が自動的に解決されるため、強力です。 ある単語が別の単語をブロックする略語を採用している場合、拡張パス プロセスによって以前の選択肢が再配置されることがあります。 

グラフは、標準の DFS ベースの Kuhn アルゴリズムには十分小さいです。 存在する単語は最大 200 個で、それぞれが最大数百個のエッジに寄与します。 グラフの合計サイズは管理可能なままです。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | 指数 | O(n) 再帰 | 遅すぎる |
 | 最適 | O(VE) | お(え) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. すべての単語を読みます。 
2. すべての単語について、長さ 1 ～ 4 のすべての個別のサブシーケンスを生成します。 

再帰または組み合わせを使用して、位置のサブセットを列挙します。 元の順序を保持する必要があるため、インデックスを選択すると、有効なサブシーケンスが自動的に生成されます。 
3. すべての個別の略語を整数 ID に圧縮します。 

マッチング アルゴリズムは、生の文字列よりもコンパクトなインデックスを使用すると効率的に機能します。 
4. 2 部グラフを作成します。 

左側には、単語ごとに 1 つのノードが含まれます。 右側には、一意の略語ごとに 1 つのノードが含まれます。 Word からエッジを追加する`i`略語に`j`その単語からその略語を形成できる場合。 
5. 最大の 2 部マッチングを行うために Kuhn のアルゴリズムを実行します。 

すべての単語について、一意の略語を割り当てる拡張パスを見つけてください。 略語がすでに使用されている場合は、現在一致している単語を別の場所に再帰的に再ルーティングしようとします。 
6. 一致した単語の数を数えます。 

マッチングサイズが以下の場合`n`、有効な割り当てが存在しないため、印刷します`-1`。 
7. それ以外の場合は、答えを再構築します。 

一致するすべての略語は、正確に 1 つの単語に対応します。 これらの略語を入力順に出力します。 

### なぜ効果があるのか

 グラフには、すべての単語に対する有効な選択肢が正確に含まれています。 マッチングでは、2 つの単語が同じ略語を共有しないようにエッジが選択されます。 

完全一致が存在する場合、すべての単語は 1 つの固有の有効な略語を受け取ります。 逆に、有効な解決策はすべて、すべての単語をカバーする一致に直接対応します。 

クーンのアルゴリズムは、拡張パスを繰り返し見つけます。 拡張パスごとに、一意性を維持しながら、一致する単語の数が 1 つずつ増加します。 拡張パスは最大の 2 部マッチングの基本的な特徴であるため、最終的なマッチングは最大限可能です。 サイズが等しい場合`n`、完全な割り当てが存在します。 

## Python ソリューション```python
import sys
from itertools import combinations

input = sys.stdin.readline

def generate(word):
    s = set()
    m = len(word)

    for length in range(1, min(4, m) + 1):
        for idxs in combinations(range(m), length):
            cur = ''.join(word[i] for i in idxs)
            s.add(cur)

    return list(s)

def solve():
    n = int(input())
    words = [input().strip() for _ in range(n)]

    abbr_id = {}
    abbr_list = []

    graph = []

    for word in words:
        subs = generate(word)

        edges = []

        for sub in subs:
            if sub not in abbr_id:
                abbr_id[sub] = len(abbr_list)
                abbr_list.append(sub)

            edges.append(abbr_id[sub])

        graph.append(edges)

    m = len(abbr_list)

    match_right = [-1] * m

    def dfs(v, vis):
        for to in graph[v]:
            if vis[to]:
                continue

            vis[to] = True

            if match_right[to] == -1 or dfs(match_right[to], vis):
                match_right[to] = v
                return True

        return False

    matched = 0

    for v in range(n):
        vis = [False] * m

        if dfs(v, vis):
            matched += 1

    if matched != n:
        print(-1)
        return

    ans = [""] * n

    for abbr_idx, word_idx in enumerate(match_right):
        if word_idx != -1:
            ans[word_idx] = abbr_list[abbr_idx]

    print('\n'.join(ans))

solve()
```の`generate`関数は、長さ 1 ～ 4 のすべてのサブシーケンスを列挙します。`combinations`増加するインデックスを選択すると、文字の順序が自動的に保持されるため、便利です。 の`set`複数の形成方法などの重複を削除します。`"aa"`から`"aaaa"`。 

グラフ構築フェーズでは、すべての一意の略語に整数 ID を割り当てます。 多くの単語が同じ略語候補を共有する可能性があるため、この圧縮は重要です。 

マッチング自体は、Kuhn アルゴリズムの標準 DFS 拡張パス実装を使用します。 再帰呼び出しは重要なステップです。 略語がすでに使用されている場合は、現在割り当てられている単語を別の場所に移動しようとします。 割り当てを再シャッフルできるこの機能により、マッチングが貪欲な選択よりも根本的に強力になります。 

の`vis`DFS を試行するたびに配列を再作成する必要があります。 これを反復間で再利用すると、有効な拡張パスが誤ってブロックされてしまいます。 

最後に、略語から単語までの一致が保存されます。 アルゴリズムが完了したら、そのマッピングを必要な出力順序に反転します。 

## 実用的な例

 ### 例 1

 入力:```
6
privet
spasibo
codeforces
java
marmelad
normalno
```生成された略語は膨大であるため、成功した一致の決定のみを追跡します。 

| 単語 | いくつかの略語候補 | 選ばれた |
 | --- | --- | --- |
 | イボタノキ`pr`、`pret`、`pvt`、`riv`|`pret`|
 | スパシーボ |`sps`、`spo`、`sbo`|`sps`|
 | コードフォース |`cdfs`、`code`、`cofs`|`cdfs`|
 | ジャワ |`j`、`ja`、`java`|`java`|
 | マーメラド |`mama`、`mard`、`mela`|`mama`|
 | ノーマルノ |`norm`、`nrml`、`nalo`|`norm`|

 出力：```
pret
sps
cdfs
java
mama
norm
```このトレースは、マッチングの中心的な特性を示しています。 複数の単語が候補を共有する場合がありますが、最終的な割り当てはグローバルに一意性を保証します。 

### 例 2

 入力:```
3
a
aa
aaa
```| 単語 | 候補の略語 |
 | --- | --- |
 | |`a`|
 | ああ |`a`、`aa`|
 | ああ |`a`、`aa`、`aaa`|

 考えられるマッチングプロセス:

 | ステップ | 割り当てられた略語 |
 | --- | --- |
 | 一致する単語 1 |`a`|
 | 一致する単語 2 |`aa`|
 | 一致する単語 3 |`aaa`|

 出力：```
a
aa
aaa
```これは、1 より長いサブシーケンスが重要である理由を示しています。 すべての言葉が共通しているにもかかわらず`a`、大きな単語は別の略語に移動できます。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(VE) | 二部グラフ上の Kuhn のアルゴリズム |
 | スペース | お(え) | 隣接リストと一致する配列 |

 ここ、`V`は単語数に略語を加えたもので、`E`有効な単語と略語のペアの数です。 

各単語は最大 385 個のサブシーケンスを生成するため、次のようになります。$$E \le 200 \times 385$$

$200\times385$これは、DFS ベースのマッチング実装としては 2 秒の制限内で快適に小さいです。 

## テストケース```python
# helper: run solution on input string, return output string
import sys
import io
from itertools import combinations

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)

    input = sys.stdin.readline

    def generate(word):
        s = set()
        m = len(word)

        for length in range(1, min(4, m) + 1):
            for idxs in combinations(range(m), length):
                s.add(''.join(word[i] for i in idxs))

        return list(s)

    n = int(input())
    words = [input().strip() for _ in range(n)]

    abbr_id = {}
    abbr_list = []
    graph = []

    for word in words:
        edges = []

        for sub in generate(word):
            if sub not in abbr_id:
                abbr_id[sub] = len(abbr_list)
                abbr_list.append(sub)

            edges.append(abbr_id[sub])

        graph.append(edges)

    m = len(abbr_list)

    match_right = [-1] * m

    def dfs(v, vis):
        for to in graph[v]:
            if vis[to]:
                continue

            vis[to] = True

            if match_right[to] == -1 or dfs(match_right[to], vis):
                match_right[to] = v
                return True

        return False

    matched = 0

    for v in range(n):
        vis = [False] * m

        if dfs(v, vis):
            matched += 1

    if matched != n:
        return "-1"

    ans = [""] * n

    for i, v in enumerate(match_right):
        if v != -1:
            ans[v] = abbr_list[i]

    return "\n".join(ans)

# provided-style sample
out = run(
"""6
privet
spasibo
codeforces
java
marmelad
normalno
"""
)

assert len(out.splitlines()) == 6

# minimum case
assert run(
"""1
a
"""
) == "a"

# impossible case
assert run(
"""2
a
a
"""
) == "-1"

# repeated-character subsequences
out = run(
"""3
a
aa
aaa
"""
)

lines = out.splitlines()
assert len(set(lines)) == 3

# boundary length 10
out = run(
"""2
abcdefghij
jihgfedcba
"""
)

assert len(out.splitlines()) == 2
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 一言`a`|`a`| 最小サイズの入力 |
 | 同じものを 2 つ`a`言葉 |`-1`| 不可能な割り当ての検出 |
 |`a`、`aa`、`aaa`| 3 つの異なる略語 | 再割り当てロジックの一致 |
 | 長さ 10 の 2 つの単語 | 任意の有効なペア | 最大サブシーケンス生成 |

 ## 特殊なケース

 文字が繰り返される場合を考えてみましょう。```
1
aaaa
```続いて`aa`多くのインデックス ペアを使用して生成できます。 

- ポジション`(0,1)`- ポジション`(0,2)`- ポジション`(1,3)`このアルゴリズムは、グラフ エッジを追加する前にサブシーケンスをセットに格納します。 その結果、グラフにはエッジが 1 つだけ含まれます。`aa`。 これにより、不要な重複エッジが防止され、マッチング動作がクリーンな状態に保たれます。 

次に、不可能な衝突の場合を考えてみましょう。```
2
a
a
```どちらの単語も略語を 1 つだけ生成します。 

| 単語 | 候補者 |
 | --- | --- |
 | 1 |`a`|
 | 2 |`a`|

 マッチングでは最大 1 つの単語をカバーできます。 Kuhn のアルゴリズムを実行した後のマッチング サイズは次のようになります。`1 < 2`、アルゴリズムは次のように出力します`-1`。 

最後に、貪欲は失敗するがマッチングは成功するケースを考えてみましょう。```
2
abcd
abdc
```最初の単語が最初に取得したと仮定します`abd`。 2 番目の単語は後でその省略形が必要になる場合があります。 DFS 中に、アルゴリズムは拡張パスを探索し、最初の単語を次のような別の有効な略語に再割り当てできます。`abc`。 この柔軟性こそが、二部マッチングがグローバルな一意性制約を正しく解決する理由です。
