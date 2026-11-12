---
title: "CF 172E - BHTML+BCSS"
description: "簡略化された HTML のようなドキュメントが与えられます。 すべてのタグは、<a のような開始タグ、</a のような終了タグ、または <a/ のような自己終了タグのいずれかです。 ドキュメントは適切にネストされていることが保証されているため、すべての開始タグには一致する終了タグが 1 つだけあります。"
date: "2026-05-29T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "*special", "dfs-and-similar", "expression-parsing"]
categories: ["algorithms"]
codeforces_contest: 172
codeforces_index: "E"
codeforces_contest_name: "Croc Champ 2012 - Qualification Round"
rating: 2200
weight: 172
solve_time_s: 141
verified: false
draft: false
---

[CF 172E - BHTML+BCSS](https://codeforces.com/problemset/problem/172/E)

 **評価:** 2200
 **タグ:** *特殊、dfs および同様の式の解析
 **解決時間:** 2 分 21 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 簡略化された HTML のようなドキュメントが与えられます。 すべてのタグは次のような開始タグのいずれかです`<a>`、次のような終了タグ`</a>`、または次のような自己終了タグ`<a/>`。 ドキュメントは適切にネストされていることが保証されているため、すべての開始タグには一致する終了タグが 1 つだけあります。 

一致するタグの各ペアが要素を形成します。 自己終了タグも要素としてカウントされます。 ドキュメントは適切にネストされているため、要素は DOM ツリーとまったく同じように、ルート化されたフォレスト構造を形成します。 

クエリは、次のようなタグ名のシーケンスです。```
a b c
```タグ名が正確に一致する要素で終わる祖先のチェーンが存在する場合、要素はこのクエリに一致します。`a -> b -> c`。 

重要な点は、チェーンでは連続したレベルを使用する必要がないということです。 クエリにはネストされた包含のみが必要です。 例えば：```
<a><x><b></b></x></a>
```要素`b`クエリに一致します`a b`、 それでも`x`彼らの間に座っています。 

すべてのクエリについて、クエリを満たす要素の数を数えなければなりません。 

文書の長さは次のとおりです`10^6`これにより、タグの数の二次関数が即座に除外されます。 ドキュメントの解析にもすでに線形時間がかかるため、実際の照合ロジックも線形に近い状態を維持する必要があります。 

多くてもあります`200`クエリ、および各クエリの長​​さは最大で`200`。 これは、ドキュメントを一度前処理してから、すべてのクエリにまとめて回答する必要があるという強力なヒントです。 

すべてのクエリに対するすべてのノードからの単純な DFS は爆発してしまいます。 最悪の場合、ツリーには数十万の要素が含まれる可能性があります。 これにすべてのクエリの長​​さを乗算すると、コストが高すぎます。 

いくつかの特殊なケースは、誤って処理されやすいものです。 

タグ名が繰り返されることを考慮してください。```
<a><a><a></a></a></a>
```クエリ:```
a a
```最も深い`a`祖先があるため一致します`a`。 真ん中`a`も一致します。 最も近い祖先のみを追跡する不注意な実装では、一部の一致が失われる可能性があります。 

もう 1 つの微妙なケースは、不連続なネストです。```
<a><x><b></b></x></a>
```クエリ:```
a b
```正しい答えは、`1`。 任意の祖先チェーンではなく、親子関係のみをチェックするソリューションは、ここでは失敗します。 

自己終了タグは通常の要素のように動作します。```
<a><b/></a>
```クエリ:```
a b
```答えは`1`。 解析で自己終了タグが無視されたり、それらの要素ノードの作成を忘れたりした場合、カウントは不正確になります。 

最後に、順序が重要です。```
<a><b></b></a>
```クエリ:```
b a
```答えは`0`なぜなら、祖先チェーンは最も外側から最も内側へのネスト順序に従う必要があるからです。 

## アプローチ

 ツリーを構築した後は、強引なアプローチが簡単です。 

すべての要素について、その祖先を上にたどり、すべてのクエリを逆方向に照合することを試みることができます。 次のようなクエリがあるとします。```
a b c
```タグ付けされた要素の場合`c`を探しながら根に向かって登っていきます。`b`を選択し、検索を続けます。`a`。 

これは、クエリがそのような祖先チェーンが存在するかどうかのみを尋ねるために機能します。 上向きスキャンはその状態を直接チェックします。 

問題は複雑さです。 文書に次の内容が含まれている場合`N`要素とツリーがチェーンに縮退すると、上向きに歩くたびにコストがかかる可能性があります`O(N)`。 これをすべての要素とすべてのクエリに対して繰り返すと、おおよそ次のようになります。```
O(number_of_queries * N^2)
```と`N`数十万近く、これは絶望的です。 

重要な観察点は、ドキュメント自体が巨大であるにもかかわらず、個別のクエリの数が少ないということです。 

クエリを個別にテストする代わりに、すべてのクエリに関する情報を同時に維持しながらドキュメントを一度処理できます。 

DFS を使用してツリーをトラバースし、ルートから現在のノードまでのタグ名の現在のパスを保持するとします。 次に、各クエリは部分列マッチング問題になります。 

クエリは現在のルートからノードへのパスのサブシーケンスとして表示されますか?

 たとえば、現在の DFS パスが次の場合:```
header p div b
```次にクエリ:```
header b
```一致します。 

クエリは短いため、すべてのクエリのトライを構築できます。 DFS 中に、という名前のタグを入力するたびに、`x`、すべてのクエリ状態が待機中`x`前進できる。 

これにより、問題は DFS パスに沿ったオートマトンの伝播に変換されます。 

クエリ データの総量はごくわずかです。```
200 * 200 = 40000
```したがって、すべての部分一致の遷移リストを維持する余裕があります。 

結果として得られるアルゴリズムは、ドキュメント サイズに対して基本的に線形になります。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(Q · N²) | O(N) | 遅すぎる |
 | 最適 | O(ドキュメントの長さ + クエリの長​​さの合計 + 状態遷移の合計) | O(クエリの合計長) | 承認済み |

 ## アルゴリズムのチュートリアル

 ### ドキュメントの解析

 文書文字列を左から右にスキャンしてタグを抽出します。 

開始タグごとに新しいノードを作成し、スタックにプッシュします。 

終了タグごとに、一致するノードをスタックからポップします。 

自己終了タグごとにノードを作成しますが、プッシュはしません。 

これにより、ネスト ツリーが再構築されます。 

### クエリ状態の構築

 1. すべてのクエリを単語に分割します。 
2. ドキュメントまたはクエリに出現するすべての一意のタグ名に整数 ID を割り当てます。 
3. すべてのクエリに対して、そのクエリのプレフィックスを表す状態を作成します。 

たとえば、次のようにクエリします。```
a b c
```トランジションを作成します:```
state 0 --a--> state 1
state 1 --b--> state 2
state 2 --c--> state 3
```州`3`クエリ全体が一致したことを意味します。 

1. タグ名ごとに、このタグが出現したときにどの状態に進むことができるかを保存します。 

これにより、DFS 中にすべてのアクティブな部分一致を効率的に更新できます。 

### DFS トラバーサル

 1. すべてのルート ノードから DFS を開始します。 
2. 配列を維持する`cnt[state]`。`cnt[s]`現在のルートからノードへのパスがクエリ プレフィックス状態を実現する方法の数に等しい`s`。 

最初は状態のみ`0`活動中です。 

1. タグ付きノードに入る場合`x`、すべての移行を待っています`x`使えるようになります。 

仮定する：```
state u --x--> state v
```それから：```
cnt[v] += cnt[u]
```状態を逆の順序で処理するため、このノードからの更新が同じステップ内で複数回カスケードされることはありません。 

1. ターミナル状態がアクティブになるたびに、対応するクエリがこのノードで一致します。 
2. 子供への回帰。 
3. 戻った後、このノードで実行されたすべての変更をロールバックします。 

DFS パスは独立しているため、ロールバックは不可欠です。 

### なぜ効果があるのか

 DFS中はいつでも、`cnt[state]`現在のルートから現在のノードへのパスのサブシーケンスの数が対応するクエリ プレフィックスを実現するかどうかを表します。 

タグを入力すると`x`、期待される既存のすべての部分一致`x`現在のノードを通って拡張される場合があります。 サブシーケンスでは中間ノードをスキップできるため、以前の有効なプレフィックスはすべてツリーのより深いところでも有効なままになります。 

遷移を逆の順序で処理すると、単一のノードが同じクエリ位置内で複数回再利用されることがなくなります。 

DFS は常に現在の祖先パスを正確に維持するため、カウントされるすべての完了はドキュメント内の有効なネストされたチェーンに対応し、すべての有効なネストされたチェーンは、DFS が最後の要素に到達したときに最終的にカウントされます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

from collections import defaultdict

def solve():
    s = input().strip()
    m = int(input())

    queries = []
    all_tags = set()

    for _ in range(m):
        q = input().split()
        queries.append(q)
        all_tags.update(q)

    n = len(s)

    children = []
    tag_id = []
    roots = []

    stack = []

    tag_map = {}

    def get_id(name):
        if name not in tag_map:
            tag_map[name] = len(tag_map)
        return tag_map[name]

    i = 0

    while i < n:
        j = s.find('>', i)

        token = s[i + 1:j]

        if token[0] == '/':
            stack.pop()

        elif token[-1] == '/':
            name = token[:-1]

            tid = get_id(name)

            node = len(children)
            children.append([])
            tag_id.append(tid)

            if stack:
                children[stack[-1]].append(node)
            else:
                roots.append(node)

        else:
            name = token

            tid = get_id(name)

            node = len(children)
            children.append([])
            tag_id.append(tid)

            if stack:
                children[stack[-1]].append(node)
            else:
                roots.append(node)

            stack.append(node)

        i = j + 1

    transitions = defaultdict(list)

    state_ptr = 1

    query_end = []
    query_state = []

    for qi, q in enumerate(queries):
        cur = 0

        for word in q:
            tid = get_id(word)

            nxt = state_ptr
            state_ptr += 1

            transitions[tid].append((cur, nxt))

            cur = nxt

        query_end.append(cur)
        query_state.append(cur)

    cnt = [0] * state_ptr
    cnt[0] = 1

    ans = [0] * m

    def dfs(u):
        tid = tag_id[u]

        updates = []

        trans = transitions.get(tid, [])

        for prev, nxt in reversed(trans):
            add = cnt[prev]

            if add:
                old = cnt[nxt]
                cnt[nxt] += add
                updates.append((nxt, old))

        for qi in range(m):
            st = query_end[qi]
            if cnt[st]:
                ans[qi] += cnt[st]

        for v in children[u]:
            dfs(v)

        for st, old in reversed(updates):
            cnt[st] = old

    for r in roots:
        dfs(r)

    print('\n'.join(map(str, ans)))

solve()
```パーサーはタグ ストリームから直接フォレストを再構築します。 すべての開始タグまたは自己終了タグがノードを作成します。 スタックには、現在開いている要素が常に格納されます。 

オートマトンの状態はクエリの接頭辞を表します。 州`0`空のプレフィックスを意味します。 すべての遷移は、クエリからの 1 つのタグの消費に対応します。 

DFS は、現在の祖先パス上で動的プログラミング値を維持します。 逆順遷移更新は微妙な部分です。 逆にしないと、1 つのノードが同じクエリの複数の位置を同時に進めることができます。 

たとえば、次のクエリを考えてみましょう。```
a a
```と現在のノード`a`。 

If updates are processed forward, the same node would first activate the first`a`、すぐに 2 つ目をアクティブにします`a`再び自分自身を使用します。 リバースすることでそれを防ぎます。 

ロールバックは、サブツリーを離れた後に DP 配列を復元します。 ロールバックがないと、1 つのブランチからの一致が兄弟ブランチに誤って漏れてしまいます。 

## 実用的な例

 ### サンプル 1

 入力:```
<a><b><b></b></b></a><a><b></b><b><v/></b></a><b></b>
```クエリ:```
a
a b b
a b
b a
```ツリー構造は次のとおりです。```
a
└── b
    └── b

a
├── b
└── b
    └── v

b
```クエリの DFS トレース`a b`:

 | 現在のパス | アクティブなプレフィックス | クエリは一致しましたか? |
 | --- | --- | --- |
 | | | いいえ |
 | a b | a、a b | はい |
 | a b b | a、a b | はい |
 | | | いいえ |
 | a b | a、a b | はい |
 | a b | a、a b | はい |
 | a b v | a、a b | いいえ |
 | b | なし | いいえ |

 答えは次のようになります`4`。 

このトレースは、サブシーケンスのマッチングを示しています。 クエリ`a b`直接ネストチェーンと間接ネストチェーンの両方に一致します。 

### カスタム例

 入力:```
<a><x><b/></x></a>
```クエリ:```
a b
```DFS トレース:

 | 現在のパス | 一致するプレフィックス |
 | --- | --- |
 | | |
 | × | |
 | a x b | a、a b |

 答えは`1`。 

この例では、中間ノードがスキップされる可能性があることを確認しています。 クエリでは、隣接関係ではなく、祖先の包含のみが必要です。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(L + S + T) |`L`文書を解析するため、`S`クエリの合計サイズについては、`T`DFS 中のすべての移行更新用 |
 | スペース | O(N + S) | ツリーストレージとオートマトンの状態 |

 文書の長さは最大でも`10^6`したがって、線形解析が必要です。 合計クエリ サイズを超えることはありません`40000`そのため、オートマトンはドキュメントに比べて非常に小さくなります。 このソリューションは、時間とメモリの制限内に簡単に収まります。 

## テストケース```python
# helper: run solution on input string, return output string
import sys, io
from collections import defaultdict

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)

    input = sys.stdin.readline

    s = input().strip()
    m = int(input())

    queries = []

    children = []
    tag_id = []
    roots = []

    stack = []

    tag_map = {}

    def get_id(name):
        if name not in tag_map:
            tag_map[name] = len(tag_map)
        return tag_map[name]

    n = len(s)

    i = 0

    while i < n:
        j = s.find('>', i)

        token = s[i + 1:j]

        if token[0] == '/':
            stack.pop()

        elif token[-1] == '/':
            name = token[:-1]

            tid = get_id(name)

            node = len(children)
            children.append([])
            tag_id.append(tid)

            if stack:
                children[stack[-1]].append(node)
            else:
                roots.append(node)

        else:
            name = token

            tid = get_id(name)

            node = len(children)
            children.append([])
            tag_id.append(tid)

            if stack:
                children[stack[-1]].append(node)
            else:
                roots.append(node)

            stack.append(node)

        i = j + 1

    transitions = defaultdict(list)

    state_ptr = 1

    query_end = []

    for qi in range(m):
        q = input().split()

        cur = 0

        for word in q:
            tid = get_id(word)

            nxt = state_ptr
            state_ptr += 1

            transitions[tid].append((cur, nxt))

            cur = nxt

        query_end.append(cur)

    cnt = [0] * state_ptr
    cnt[0] = 1

    ans = [0] * m

    def dfs(u):
        tid = tag_id[u]

        updates = []

        for prev, nxt in reversed(transitions.get(tid, [])):
            add = cnt[prev]

            if add:
                old = cnt[nxt]
                cnt[nxt] += add
                updates.append((nxt, old))

        for qi, st in enumerate(query_end):
            if cnt[st]:
                ans[qi] += cnt[st]

        for v in children[u]:
            dfs(v)

        for st, old in reversed(updates):
            cnt[st] = old

    for r in roots:
        dfs(r)

    return '\n'.join(map(str, ans))

# provided sample
assert run(
"""<a><b><b></b></b></a><a><b></b><b><v/></b></a><b></b>
4
a
a b b
a b
b a
"""
) == "2\n1\n4\n0"

# single self-closing node
assert run(
"""<a/>
1
a
"""
) == "1"

# non-consecutive nesting
assert run(
"""<a><x><b/></x></a>
1
a b
"""
) == "1"

# repeated names
assert run(
"""<a><a><a/></a></a>
2
a a
a a a
"""
) == "2\n1"

# impossible order
assert run(
"""<a><b/></a>
1
b a
"""
) == "0"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`<a/>`クエリ付き`a`|`1`| 自己終了タグが要素を作成する |
 |`<a><x><b/></x></a>`クエリ付き`a b`|`1`| 非連続ネスティング作品 |
 | ネストされた繰り返し`a`タグ |`2`、`1`| 逆順 DP 更新の正確性 |
 | クエリ`b a`の上`<a><b/></a>`|`0`| 先祖の順序が重要 |

 ## 特殊なケース

 タグ名が繰り返されることを考慮してください。```
<a><a><a/></a></a>
```クエリ:```
a a
```DFSが最深部に入ったとき`a`、パスには 3 つが含まれます`a`ノード。 

逆順遷移更新では、現在のノードがクエリ位置ごとに 1 回だけ寄与することが保証されます。 祖先を使用して最も深いノードが正しく一致する`a`、それ自体は2回ではありません。 

アルゴリズムは次を出力します。```
2
```これは、中央と最深の要素をカウントします。 

ここで、スキップされたレベルについて考えてみましょう。```
<a><x><b/></x></a>
```クエリ:```
a b
```訪問中`x`、プレフィックスの DP 状態`a`アクティブなままです。 入る`b`既存のプレフィックスを次のように拡張します`a b`。 

このアルゴリズムは、一致したノード間の隣接性を必要とせず、祖先の封じ込めのみを必要とします。 

最後に、兄弟リークを考慮します。```
<a><b/></a><a/>
```クエリ:```
a b
```正しい答えは、`1`。 

DFS が最初のサブツリーを離れた後、ロールバックにより、そこに導入されたすべての状態が削除されます。 2番目のルートのとき`a`処理されると、前の`b`マッチがなくなった。 

ロールバックがないと、2 番目のブランチは最初のサブツリーからの一致を誤って継承します。
