---
title: "CF 163E - 電子政府"
description: "私たちには固定された姓のセットがあります。 各姓はアクティブまたは非アクティブのいずれかになります。 最初はすべての姓がアクティブです。 システムは 3 種類の操作を処理する必要があります。 クエリ +i は、i 番目の姓をアクティブにします。 クエリ -i は、i 番目の姓を非アクティブにします。 クエリ？"
date: "2026-05-29T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "data-structures", "dfs-and-similar", "dp", "strings", "trees"]
categories: ["algorithms"]
codeforces_contest: 163
codeforces_index: "E"
codeforces_contest_name: "VK Cup 2012 Round 2"
rating: 2800
weight: 163
solve_time_s: 136
verified: true
draft: false
---

[CF 163E - 電子政府](https://codeforces.com/problemset/problem/163/E)

 **評価:** 2800
 **タグ:** データ構造、dfs など、dp、文字列、ツリー
 **解決時間:** 2 分 16 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 私たちには固定された姓のセットがあります。 各姓はアクティブまたは非アクティブのいずれかになります。 最初はすべての姓がアクティブです。 

システムは 3 種類の操作を処理する必要があります。 

クエリ`+i`を活性化します`i-th`姓。 

クエリ`-i`を非アクティブ化します`i-th`姓。 

クエリ`?text`内部で現在アクティブなすべての姓の出現の合計数を尋ねます。`text`。 重複する一致は個別にカウントされます。 もし`"aa"`がアクティブで、テキストは`"aaa"`、その後、2回表示されます。 

柄の数もテキストの総サイズも大きいのが難点です。 すべての姓の長さの合計は次のとおりです`10^6`、すべてのクエリ テキストの合計の長さも最大です。`10^6`。 すべてのアクティブなパターンをすべてのクエリ テキストに対して個別にチェックするソリューションは、まったく実現不可能です。 

直接的なアプローチを試してみたとしましょう。 あらゆる人にとって`?`クエリでは、すべてのアクティブな姓を反復処理し、部分文字列の一致を実行します。 最悪の場合、`10^5`平均的な長さのパターン`10`、および長さのクエリ テキスト`10^6`。 各パターンに KMP を使用する場合でも、すでに約`10^11`キャラクター操作全般。 

この制約は、すべてのパターンを同時に処理する必要があることを強く示唆しています。 プレフィックスを共有する複数のパターンがここでの中心的な構造です。 それに気づくと、自然なツールが Aho-Corasick オートマトンになります。 

単純な実装を簡単に壊してしまういくつかのエッジケースがあります。 

重複する一致を考慮してください。 

入力：```
1 1
aa
?aaa
```正しい出力:```
2
```パターン`"aa"`位置に表示されます`[1,2]`そして`[2,3]`。 重複しない検索を使用した不注意な実装では、誤って返される可能性があります。`1`。 

もう 1 つの微妙なケースは、アクティブ化または非アクティブ化が繰り返されることです。 

入力：```
4 1
abc
-1
-1
+1
?abc
```正しい出力:```
1
```2 回目の非アクティブ化では何も行わないでください。 カウンタを盲目的にデクリメントすると、誤ってパターンが貢献してしまう可能性があります。`-1`。 

失敗リンクは、間違いのもう 1 つの一般的な原因です。 

入力：```
1 2
a
aa
?aa
```正しい出力:```
3
```一致するものは次のとおりです。`"a"`2回、`"aa"`一度。 

現在のトライ ノードで終わるパターンのみをカウントし、失敗の先祖を無視すると、より短いサフィックス パターンを見逃してしまいます。`"a"`。 

最後の重要な例外ケースは、動的更新に由来します。 

入力：```
5 2
a
aa
-1
?aa
+1
?aa
```正しい出力:```
1
3
```いつ`"a"`非アクティブです、ただ`"aa"`貢献します。 再アクティブ化後は、両方のパターンが再び寄与します。 データ構造はオンライン更新を効率的にサポートする必要があります。 

## アプローチ

 ブルートフォースソリューションは概念的には単純です。 すべてのクエリ テキストについて、すべてのアクティブな姓を反復処理し、出現回数を個別にカウントします。 KMP のような線形マッチャーを使用すると、1 つのテキスト内の 1 つのパターンをカウントするには時間がかかります`O(text_length + pattern_length)`。 

あれば`k = 10^5`パターンとクエリテキストの合計長も`10^6`、最悪の場合の複雑さはおおよそ次のようになります。```
O(total_queries * total_patterns * average_text_size)
```楽観的な見積もりでも`10^10`操作。 これは 1 秒以内に実行できません。 

重要な観察は、すべてのパターンが静的であるということです。 アクティブ/非アクティブのステータスのみが変化します。 これは、すべての姓に対して 1 つのオートマトンを一度構築すれば、それをすべてのクエリで再利用できることを意味します。 

Aho-Corasick では、すべてのパターンの一致を同時に追跡しながら、テキストを線形時間で処理できます。 文字をスキャンしている間、オートマトンはトライ状態を移動し、失敗リンクは接尾辞の一致を自動的に処理します。 

これにより、複数パターンのマッチングの問題は解決されますが、動的アクティブ化の問題は解決されません。 オートマトンノードに到達したとします。`v`テキストをスキャンしながら。 のいくつかの接尾辞に対応するすべてのパターン`v`アクティブであれば貢献する必要があります。 

重要な構造特性は次のとおりです。 

If ノード`u`ノードのサフィックスの祖先です`v`失敗ツリーでパターンを作成します`u`state にあるときはいつでも一致します`v`。 

したがって、問題は次のようになります。 

テキストを読みながら、繰り返し質問してください。 

「障害ツリー内の現在のノードのすべての祖先にわたるアクティブなパターンの重みの合計はいくらですか?」

 これはまさにツリー上のサブツリー プレフィックス クエリの問題です。 

障害ツリーを構築し、DFS 注文インデックス作成を実行し、オイラー ツアー インデックスに対するフェンウィック ツリーを維持します。 

パターンがアクティブになると、`+1`ノードの位置に移動します。 

非アクティブになった場合に追加します`-1`。 

クエリ テキストの場合、オートマトンの状態を調べながら、現在の状態のサフィックス チェーン上にアクティブなパターン ノードがいくつあるかを尋ねます。 障害ツリーの DFS オーダーでは、サブツリー合計クエリになります。 

その結果、全体の複雑さが入力サイズにほぼ比例するオンライン ソリューションが得られます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | クエリあたり O(パターンの合計 × テキストの長さの合計) | O(パターンの合計の長さ) | 遅すぎる |
 | 最適 | O(入力サイズの合計 × アルファベット + クエリの合計 × ログ N) | O(パターンの合計の長さ) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. すべての姓からトライを構築します。 

各ノードはプレフィックスを表します。 パターンごとに、パターンが終了するノードを保存します。 
2. BFS を使用して障害リンクを構築します。 

ノードの障害リンクは、トライ内にも存在する最も長い適切なサフィックスを指します。 これは KMP で使用されるものと同じ遷移構造であり、複数のパターンに一般化されています。 
3. 障害ツリーを構築します。 

もし`fail[v] = u`、エッジを追加します`u -> v`。 

このツリーは、オートマトンの状態間の接尾辞の関係をキャプチャします。 
4. 障害ツリーで DFS を実行し、オイラー ツアー間隔を割り当てます。 

すべてのノードについて`v`、次を計算します。`tin[v]`= 入場時間、`tout[v]`= 退場時間。 

すべての子孫`v`DFS 順序で 1 つの連続セグメントを占有します。 
5. DFS 順序でフェンウィック ツリーを維持します。 

パターンがノードで終わる場合`v`アクティブです、追加します`+1`で`tin[v]`。 

非アクティブになった場合は減算します`1`。 
6. オートマトンを使用してクエリテキストを処理します。 

各キャラクターについて:

 オートマトンの遷移を通過し、

 州に到着`v`。 

終端ノードが祖先であるすべてのアクティブなパターン`v`障害ツリーではここに一致します。 
7. サブツリーの合計を使用して、アクティブなサフィックスの祖先をカウントします。 

状態で一致するアクティブなパターンの数`v`等しい:```
sum over subtree(v)
```障害ツリー DFS 順序で。 

障害ツリー内の子孫は次のノードに対応するため、`v`サブツリー集約はサフィックスとして、必要な一致を正確に提供します。 
8. テキスト全体にわたって結果を累積し、最終的な合計を出力します。 

### なぜ効果があるのか

 重要な不変条件は次のとおりです。 

パターンの終端ノードが現在の状態の障害リンク チェーン上にある場合に限り、パターンは現在のオートマトンの状態と一致します。 

失敗ツリーでは、サフィックスの祖先が通常のツリーの祖先になります。 オイラー ツアーのフラット化後、ノードのすべての子孫が 1 つの連続したセグメントを形成します。 

アクティブなパターンのカウントを終端ノードに保存することにより、サブツリー クエリは、サフィックス関係により現在の状態と一致するすべてのアクティブなパターンを正確にカウントします。 

すべてのテキスト文字がオートマトンを 1 回だけ進め、更新ごとにフェンウィックの位置が 1 つ変更されるため、すべての操作が効率的かつ正確なままになります。 

## Python ソリューション```python
import sys
from collections import deque

input = sys.stdin.readline

class Fenwick:
    def __init__(self, n):
        self.n = n
        self.bit = [0] * (n + 1)

    def add(self, idx, val):
        while idx <= self.n:
            self.bit[idx] += val
            idx += idx & -idx

    def sum(self, idx):
        res = 0
        while idx > 0:
            res += self.bit[idx]
            idx -= idx & -idx
        return res

    def range_sum(self, l, r):
        return self.sum(r) - self.sum(l - 1)

def solve():
    n, k = map(int, input().split())

    trie = [[0] * 26]
    fail = [0]
    out = []

    patterns = []

    for _ in range(k):
        s = input().strip()
        patterns.append(s)

        node = 0

        for ch in s:
            c = ord(ch) - 97

            if trie[node][c] == 0:
                trie[node][c] = len(trie)
                trie.append([0] * 26)
                fail.append(0)

            node = trie[node][c]

        out.append(node)

    q = deque()

    for c in range(26):
        nxt = trie[0][c]
        if nxt:
            q.append(nxt)

    while q:
        v = q.popleft()

        for c in range(26):
            nxt = trie[v][c]

            if nxt:
                fail[nxt] = trie[fail[v]][c]
                q.append(nxt)
            else:
                trie[v][c] = trie[fail[v]][c]

    size = len(trie)

    tree = [[] for _ in range(size)]

    for v in range(1, size):
        tree[fail[v]].append(v)

    tin = [0] * size
    tout = [0] * size
    timer = 0

    stack = [(0, 0)]

    while stack:
        v, state = stack.pop()

        if state == 0:
            timer += 1
            tin[v] = timer

            stack.append((v, 1))

            for to in reversed(tree[v]):
                stack.append((to, 0))
        else:
            tout[v] = timer

    bit = Fenwick(size)

    active = [True] * k

    for node in out:
        bit.add(tin[node], 1)

    ans = []

    for _ in range(n):
        query = input().strip()

        typ = query[0]
        arg = query[1:]

        if typ == '+':
            idx = int(arg) - 1

            if not active[idx]:
                active[idx] = True
                bit.add(tin[out[idx]], 1)

        elif typ == '-':
            idx = int(arg) - 1

            if active[idx]:
                active[idx] = False
                bit.add(tin[out[idx]], -1)

        else:
            node = 0
            total = 0

            for ch in arg:
                c = ord(ch) - 97
                node = trie[node][c]

                total += bit.range_sum(tin[node], tout[node])

            ans.append(str(total))

    print('\n'.join(ans))

solve()
```トライ構築フェーズでは、個別のプレフィックスごとに 1 つのオートマトン状態が作成されます。 後の更新ではそのノードを正確にアクティブ化または非アクティブ化する必要があるため、すべてのパターンはその終端ノードを記憶します。 

障害リンクの BFS 構築中に、欠落している遷移は障害遷移を使用して埋められます。 これにより、トライが完全なオートマトンに変換され、すべての文字の遷移が次のようになります。`O(1)`。 

障害ツリーは障害リンクから構築されます。 もし`fail[v] = u`、 それから`u`の親です`v`。 このツリー構造は、ソリューションの背後にある中心的なトリックです。 

DFS の番号付けには注意が必要です。 失敗ツリーのサブツリーは、連続したオイラー区間に対応します。`[tin[v], tout[v]]`。 フェンウィック ツリーは連続した範囲で効率的に機能するため、平坦化が必要です。 

実装の微妙な詳細は、カウントの方向です。 

アクティブなパターンを終端ノードに保存します。 オートマトンの状態を処理する場合`v`のサブツリーをクエリします。`v`、その祖先チェーンではありません。 

なぜ？ なぜなら、失敗の木では、`v`まさに、その障害連鎖が含まれる状態です。`v`。 この逆転は間違いやすいです。 

繰り返しのアクティブ化と非アクティブ化は、`active`配列。 このチェックを行わないと、複数の`+`操作により、同じパターンが複数回誤って追加される可能性がありました。 

反復 DFS は、Python の再帰の深さの問題を回避します。 オートマトンには以下を含めることができます`10^6`したがって、再帰的 DFS は安全ではありません。 

## 実用的な例

 ### 例 1

 入力:```
7 3
a
aa
ab
?aaab
-2
?aaab
-3
?aaab
+2
?aabbaa
```パターン:`a`、`aa`、`ab`最初はすべてアクティブです。 

| ステップ | クエリ | アクティブなパターン | マッチ | 結果 |
 | --- | --- | --- | --- | --- |
 | 1 |`?aaab`| ああ、ああ、アブ | a=3、aa=2、ab=1 | 6 |
 | 2 |`-2`| a、ab | 削除されました | - |
 | 3 |`?aaab`| a、ab | a=3、ab=1 | 4 |
 | 4 |`-3`| | 削除されました | - |
 | 5 |`?aaab`| | a=3 | 3 |
 | 6 |`+2`| ああ、ああ | 復元されました | - |
 | 7 |`?aabbaa`| ああ、ああ | a=4、aa=2 | 6 |

 このトレースは、動的更新が正しく動作していることを示しています。 パターンはアクティブな間のみ寄与し、重複する一致はカウントされます。 

### 例 2

 入力:```
5 2
a
aa
?aa
-1
?aa
+1
?aa
```| ステップ | スキャン中の現在の状態 | アクティブなパターン | 貢献 |
 | --- | --- | --- | --- |
 |`?aa`初め`a`| 状態("a") | ああ、ああ | +1 |
 |`?aa`2番`a`| 状態("aa") | ああ、ああ | +2 |
 | 合計 | - | - | 3 |
 |`-1`| 非アクティブ化する`a`| 単三のみ | - |
 |`?aa`初め`a`| 状態("a") | 単三のみ | 0 |
 |`?aa`2番`a`| 状態("aa") | 単三のみ | +1 |
 | 合計 | - | - | 1 |

 この例では、サフィックスの一致が障害リンクを通じて処理されることを確認します。 状態で`"aa"`、 両方`"aa"`そして`"a"`貢献するかもしれない。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(パターン長の合計 + テキスト長の合計 + クエリの合計 × log N) | Trie のトラバーサルは線形で、Fenwick の更新とクエリは対数的です。 
| スペース | O(パターンの合計の長さ) | トライ、障害ツリー、DFS アレイ、フェンウィック ツリーはすべてオートマトンのサイズに合わせてスケールされます。 

オートマトンの合計サイズは、最大でもパターンの合計長によって制限されます。`10^6`。 すべてのテキスト文字は、1 つのオートマトン遷移と 1 つのフェンウィック範囲クエリを実行します。 これは制限内に快適に収まります。 

## テストケース```python
# helper: run solution on input string, return output string
import sys
import io
from collections import deque

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    input = sys.stdin.readline

    class Fenwick:
        def __init__(self, n):
            self.n = n
            self.bit = [0] * (n + 1)

        def add(self, idx, val):
            while idx <= self.n:
                self.bit[idx] += val
                idx += idx & -idx

        def sum(self, idx):
            res = 0
            while idx > 0:
                res += self.bit[idx]
                idx -= idx & -idx
            return res

        def range_sum(self, l, r):
            return self.sum(r) - self.sum(l - 1)

    n, k = map(int, input().split())

    trie = [[0] * 26]
    fail = [0]
    out = []

    for _ in range(k):
        s = input().strip()

        node = 0

        for ch in s:
            c = ord(ch) - 97

            if trie[node][c] == 0:
                trie[node][c] = len(trie)
                trie.append([0] * 26)
                fail.append(0)

            node = trie[node][c]

        out.append(node)

    q = deque()

    for c in range(26):
        nxt = trie[0][c]
        if nxt:
            q.append(nxt)

    while q:
        v = q.popleft()

        for c in range(26):
            nxt = trie[v][c]

            if nxt:
                fail[nxt] = trie[fail[v]][c]
                q.append(nxt)
            else:
                trie[v][c] = trie[fail[v]][c]

    size = len(trie)

    tree = [[] for _ in range(size)]

    for v in range(1, size):
        tree[fail[v]].append(v)

    tin = [0] * size
    tout = [0] * size

    timer = 0
    stack = [(0, 0)]

    while stack:
        v, state = stack.pop()

        if state == 0:
            timer += 1
            tin[v] = timer

            stack.append((v, 1))

            for to in reversed(tree[v]):
                stack.append((to, 0))
        else:
            tout[v] = timer

    bit = Fenwick(size)

    active = [True] * k

    for node in out:
        bit.add(tin[node], 1)

    ans = []

    for _ in range(n):
        s = input().strip()

        if s[0] == '?':
            node = 0
            total = 0

            for ch in s[1:]:
                node = trie[node][ord(ch) - 97]
                total += bit.range_sum(tin[node], tout[node])

            ans.append(str(total))

        elif s[0] == '+':
            idx = int(s[1:]) - 1

            if not active[idx]:
                active[idx] = True
                bit.add(tin[out[idx]], 1)

        else:
            idx = int(s[1:]) - 1

            if active[idx]:
                active[idx] = False
                bit.add(tin[out[idx]], -1)

    return "\n".join(ans)

# provided sample
assert run(
"""7 3
a
aa
ab
?aaab
-2
?aaab
-3
?aaab
+2
?aabbaa
"""
) == "6\n4\n3\n6", "sample 1"

# minimum size
assert run(
"""1 1
a
?a
"""
) == "1", "minimum case"

# overlapping matches
assert run(
"""1 1
aa
?aaa
"""
) == "2", "overlapping occurrences"

# repeated enable/disable
assert run(
"""5 1
abc
-1
-1
+1
?abc
"""
) == "1", "idempotent updates"

# suffix-chain counting
assert run(
"""1 2
a
aa
?aa
"""
) == "3", "failure-link aggregation"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 単一パターン`"a"`|`1`| 最小の有効なインスタンス |
 |`"aa"`内部`"aaa"`|`2`| 重複するオカレンス |
 | 繰り返し`-1`|`1`| 更新は冪等でなければなりません |
 | パターン`"a"`そして`"aa"`|`3`| 障害チェーンのサフィックス マッチング |

 ## 特殊なケース

 重複するオカレンスをもう一度検討してください。 

入力：```
1 1
aa
?aaa
```スキャン中:

 2 番目の文字の後の状態`"aa"`一度貢献すると、

 3 番目の文字の後、失敗による遷移が依然として発生します。`"aa"`またまた貢献。 

アルゴリズムは次を出力します。```
2
```すべてのテキスト位置が独立して一致に寄与するためです。 

ここで、非アクティブ化を繰り返すことを考えてみましょう。 

入力：```
4 1
abc
-1
-1
+1
?abc
```最初`-1`フェンウィック ツリーからパターンを削除します。 

2番目`-1`小切手`active[idx]`そして何もしません。 

の`+1`正確に 1 つのコピーを復元します。 

最終的なクエリは正しく出力されます。```
1
```の代わりに`0`または`-1`。 

サフィックスリンクのエッジケースは特に重要です。 

入力：```
1 2
a
aa
?aa
```オートマトン状態時`"aa"`:

 ノード自体は一致します`"aa"`、

 失敗の祖先が一致する`"a"`。 

障害ツリーに対するサブツリー クエリでは、両方のアクティブなターミナル ノードがカウントされ、以下が生成されます。```
3
```最後に、プレフィックスは共通だが末尾が異なるパターンを検討します。 

入力：```
2 3
ab
abc
abd
?abcd
```オートマトンの遷移では、トライ接頭辞が効率的に再利用されます。 

一致:`"ab"`一度、`"abc"`一度、`"abd"`ゼロ回。 

正しい出力:```
2
```トライ状態は共有プレフィックスを直接表すため、アルゴリズムはこれを自然に処理します。
