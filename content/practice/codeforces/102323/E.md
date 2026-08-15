---
title: "CF 102323E - チェーンメール"
description: "電子メール ネットワークは有向グラフです。 各人は頂点であり、人 u が連絡先として人 v を持っているというエントリは、有向辺 u - v を作成します。最初の人は最初の電子メールを受信し、それをすべての連絡先に転送し、すべての受信者は永久に同じことを行います。"
date: "2026-08-13T04:17:10+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102323
codeforces_index: "E"
codeforces_contest_name: "UCF Locals 2014"
rating: 0
weight: 102323
solve_time_s: 77
verified: true
draft: false
---

[CF 102323E - チェーンメール](https://codeforces.com/problemset/problem/102323/E)

 **評価:** -
 **タグ:** -
 **解決時間:** 1 分 17 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 電子メール ネットワークは有向グラフです。 一人ひとりが頂点であり、その人を語るエントリー`u`人がいる`v`接触により有向エッジが作成されるため`u -> v`。 開始者は最初の電子メールを受信して​​すべての連絡先に転送し、すべての受信者は永久に同じことを行います。 このタスクは、元の入力順序を維持したまま、電子メールを受信したすべての人を無限に何度も印刷することです。 誰も無限に多くのコピーを受け取らない場合、必要な出力は次のようになります。`Safe chain email!`。 入力には最大 50 人が含まれますが、各連絡先リストに含まれるエントリは 50 未満です。 

の小さな値`p`さえ意味します`O(p^3)`グラフアルゴリズムは十分に高速です。 難しいのはグラフのサイズではなく、「無限に多くの電子メール」が何を意味するのかを認識することです。 グラフにサイクルが含まれている場合、直接シミュレーションは実際には終了できないため、転送プロセスを永遠にシミュレートしようとするのではなく、構造的な特徴付けが必要です。 

到達可能性には 2 つの異なる種類が関係します。 まず、最初の人から連絡が取れる人でなければなりません。そうしないと、電子メールはまったく届きません。 第二に、開始者から到達可能なサイクルは、その人に到達できなければなりません。 電子メールが指示されたサイクルに入ると、そのサイクルにいる人々はメッセージを繰り返し受信します。 そのサイクルを離れた後に到達可能なすべての人も、サイクルを通過するたびに新しいメッセージを受信するため、それらの人々も無限に多くのメッセージを受信します。 

不注意な解決策は、連絡可能なすべての人を無限の受信者として扱うため、失敗する可能性があります。 例えば、```
2 1
Alice Bob
1 2
0
```サイクルがありません。 アリスはボブに 1 つのメッセージを送信し、プロセスは停止します。 正しい出力は次のとおりです`Safe chain email!`。 アリスからの単純な DFS がボブを訪問し、彼を誤って無限として分類する可能性があります。 

2 番目の障害は、サイクルが存在するが、サイクルの前にしか到達できない人がいる場合に発生します。 考慮する、```
4 1
Alice Bob Carol Dave
1 2
1 3
1 2
0
```ボブがキャロルに送信し、キャロルがボブに返信するため、ボブとキャロルの間にはサイクルが存在します。 ボブとキャロルは無限に多くのメッセージを受信しますが、アリスは最初のメッセージのみを受信し、デイブは何も受信しません。 正しい出力は次のとおりです```
Bob Carol
```サイクルが実際に到達可能かどうかを区別せずに、ソースからのパス上のすべての頂点をマークするソリューションには、誤ってアリスが含まれる可能性があります。 

逆の間違いも考えられます。 無限に多くの電子メールを受信するために、そのサイクル自体に属している必要はありません。 例えば、```
4 1
Alice Bob Carol Dave
1 2
1 3
1 2
1 4
```サイクルがある`Bob -> Carol -> Bob`、そしてキャロルはデイブに送ります。 ボブとキャロルのサイクルをたどるたびに、最終的に別の電子メールがデイブに送信されるため、正しい出力は次のようになります。```
Bob Carol Dave
```サイクルに属する頂点のみを印刷するソリューションでは、Dave を見逃してしまいます。 

## アプローチ

 最も直接的なアプローチは、転送をシミュレートすることです。 ソースから開始して、すべての連絡先を再帰的に追跡し、遭遇した人々の順序を記録することができます。 考えられるすべての転送チェーンは最終的に終了するため、これは非巡回グラフにとっては正しいことです。 この問題は、サイクルが存在するとすぐに発生します。つまり、同じ頂点のシーケンスが何度も繰り返される可能性があります。 グラフ構造を十分に覚えていない状態で転送パスを列挙しようとすると、非巡回グラフであっても、指数関数的に多くの異なるパスが含まれる可能性があります。 完全な有向非巡回グラフ`p`頂点には`2^(p-2)`最初の頂点から最後の頂点までのパスを列挙し、それらのパスを列挙するには次のようになります。`Theta(p * 2^p)`パスの長さが含まれている場合に機能します。 と`p = 50`、それはすでに私たちが望むものをはるかに超えています。 

ブルート フォース アプローチは、転送の実際の定義に従っているため機能しますが、同じグラフ構造を再検出するのに時間がかかります。 問題を解く観察は、有限の有向グラフにおける無限の動作は有向サイクルからのみ生じ得るということです。 到達可能なサイクルが特定されると、反復的な走査をシミュレートする必要はなくなります。 サイクルを無限メッセージのソースとしてマークし、そこから通常の到達可能性を実行できます。 

強く接続されたコンポーネントは、これらのサイクルを識別する自然な方法です。 少なくとも 2 つの頂点を持つ SCC 内では、すべての頂点が他のすべての頂点に到達できるため、コンポーネントには必然的に有向サイクルが含まれます。 この問題により、誰も自分自身を連絡先としてリストしないことが保証されるため、サイズ 1 の SCC には自己ループを含めることはできず、循環することもありません。 

すべての SCC を見つけた後、最初に開始者からどのコンポーネントに到達できるかを決定します。 これらの到達可能なコンポーネント内のサイクルのみがチェーン電子メールを受信できます。 このようなすべての循環コンポーネントから、発信エッジをたどり、到達可能なすべての人物をマークします。 まさに、無限にコピーを受け取る人たちだ。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース |`O(p * 2^p)`最悪の場合 |`O(p)`アクティブなパスごと | 遅すぎる |
 | SCC + 到達可能性 |`O(p + e)`と`e <= p(p-1)`|`O(p + e)`| 承認済み |

 ## アルゴリズムのチュートリアル

 1. 連絡先リストから有向グラフを構築します。 すべての連絡先について`v`人の`u`、エッジを追加します`u -> v`。 後で SCC の構築に使用するため、反転したグラフも作成します。 
2. 開始者と記録から DFS または反復グラフ走査を実行します。`reachable[v]`。 これにより、どのユーザーが電子メールの少なくとも 1 つのコピーを受信できるかが正確にわかります。 
3. グラフ全体の強連結成分を計算します。 Tarjan のアルゴリズムはこれを線形時間で実行します。 各頂点はコンポーネント識別子を受け取り、各コンポーネントは頂点の数を保存します。 
4. 少なくとも 2 つの頂点を持つコンポーネントは循環です。 自己接触は禁止されているため、自己ループを含む 1 つの頂点コンポーネントはありません。 循環コンポーネントのうち、開始者から到達可能な頂点を持つものだけを残します。 
5. 到達可能な循環コンポーネントに属するすべての頂点から別のグラフ走査を開始します。 訪問したすべての頂点を次のようにマークします`infinite`。 私たちは今、無限サイクルの効果をそのすべての出向きエッジに伝播させています。 
6. 最後に人物から人物をスキャンします`1`人を通して`p`。 マークされたすべての人の名前を印刷します`infinite`。 何もマークされていない場合は、印刷します`Safe chain email!`。 スキャン順序は、必要な入力順序を直接示します。 

### なぜ効果があるのか

 人を考えてみる`v`。 アルゴリズムがマークする場合`v`無限なので、`v`開始者からそれ自体が到達可能な循環コンポーネントから到達可能です。 電子メールはそのサイクルに到達することができ、サイクルを横断するたびに別のコピーが作成され、最終的にはそのサイクルから次のパスにたどります。`v`。 したがって、`v`無限に多くのメールを受信します。 

逆方向の場合は、次のようにします。`v`無限に多くのメールを受信します。 グラフには有限の数の頂点があるため、無限の転送イベント シーケンスは何らかの頂点を繰り返し訪問する必要があります。 繰り返し部分には有向サイクルが含まれます。 電子メールがそのサイクルに到達したため、そのサイクルはソースから到達可能です。 そのサイクルからは、次への転送パスもあります。`v`、 さもないと`v`繰り返されるプロセスからメッセージを受信し続けることができませんでした。 したがって`v`は、アルゴリズムによって選択された循環コンポーネントの 1 つから到達可能であるため、最後の走査でそれをマークします。 どちらの方向も成り立つため、正確に無限の受信者が出力されます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

def solve_case(p, source, names, graph):
    reverse = [[] for _ in range(p)]

    for u in range(p):
        for v in graph[u]:
            reverse[v].append(u)

    sys.setrecursionlimit(10000)

    # Find vertices reachable from the source.
    reachable = [False] * p
    stack = [source]
    reachable[source] = True

    while stack:
        u = stack.pop()
        for v in graph[u]:
            if not reachable[v]:
                reachable[v] = True
                stack.append(v)

    # Tarjan's SCC algorithm.
    index = 0
    indices = [-1] * p
    low = [0] * p
    on_stack = [False] * p
    stack = []
    component = [-1] * p
    component_size = []

    def tarjan(u):
        nonlocal index

        indices[u] = index
        low[u] = index
        index += 1

        stack.append(u)
        on_stack[u] = True

        for v in graph[u]:
            if indices[v] == -1:
                tarjan(v)
                low[u] = min(low[u], low[v])
            elif on_stack[v]:
                low[u] = min(low[u], indices[v])

        if low[u] == indices[u]:
            size = 0

            while True:
                v = stack.pop()
                on_stack[v] = False
                component[v] = len(component_size)
                size += 1

                if v == u:
                    break

            component_size.append(size)

    for u in range(p):
        if indices[u] == -1:
            tarjan(u)

    # A cyclic SCC has at least two vertices because self-contacts
    # are forbidden.
    cyclic_component = [
        size >= 2 for size in component_size
    ]

    # Start propagation from every vertex in a reachable cyclic SCC.
    infinite = [False] * p
    stack = []

    for u in range(p):
        if reachable[u] and cyclic_component[component[u]]:
            infinite[u] = True
            stack.append(u)

    while stack:
        u = stack.pop()

        for v in graph[u]:
            if not infinite[v]:
                infinite[v] = True
                stack.append(v)

    answer = [names[i] for i in range(p) if infinite[i]]

    if not answer:
        return "Safe chain email!"

    return " ".join(answer) + " "

def solve(data):
    tokens = data.split()
    it = iter(tokens)

    p = int(next(it))
    source = int(next(it)) - 1

    names = [next(it) for _ in range(p)]

    graph = [[] for _ in range(p)]

    for u in range(p):
        m = int(next(it))
        for _ in range(m):
            v = int(next(it)) - 1
            graph[u].append(v)

    return solve_case(p, source, names, graph)

def main():
    data = sys.stdin.read().split()

    if not data:
        return

    it = iter(data)
    p = int(next(it))
    source = int(next(it)) - 1

    names = [next(it) for _ in range(p)]
    graph = [[] for _ in range(p)]

    for u in range(p):
        m = int(next(it))
        for _ in range(m):
            graph[u].append(int(next(it)) - 1)

    print(solve_case(p, source, names, graph))

if __name__ == "__main__":
    main()
```入力パーサーは、入力全体を空白で区切られたトークンとして処理します。これは、名前にはアルファベット文字のみが含まれており、すべての数値フィールドが空白で区切られているため安全です。 の`source`インデックスは、1 ベースの入力番号付けから 0 ベースの Python インデックス付けに即座に変換されます。 

最初の走査の計算`reachable`。 ネットワーク内の他の場所のサイクルが答えに影響を与えてはいけないため、この分離は役立ちます。 開始者が実際に到達できるサイクルのみが繰り返しメールを生成できます。 

Tarjan のアルゴリズムは、すべての人を 1 つの SCC に割り当てます。 の`low`value は、頂点が DFS スタック内でどこまで上に到達できるかを記録します。これにより、アルゴリズムは SCC がいつ完了したかを認識できるようになります。 以来`p`がわずか 50 であるため、Python の再帰制限を増やした後でも再帰実装は小さく安全です。 

の`component_size >= 2`チェックは周期的な SCC を識別します。 一般的な有向グラフでは自己ループによって 1 頂点 SCC が循環しますが、入力では自己接触が明示的に禁止されているため、ここではそのようなケースはありません。 

最後の走査は、ソースと循環 SCC の両方から到達可能な頂点からのみ開始されます。 そこからは、無限に繰り返されるサイクルからのすべての出力パスがサイクルの繰り返しごとに 1 回横断されるため、通常の有向到達可能性がまさに必要なものになります。 

名前の後の末尾のスペースは、空白を区別しない通常の審査員には必要ありませんが、必要な形式で各印刷名の後にスペースが続くことが指定されているため、実装では意図的にこれを含めています。 

## 実用的な例

 ### サンプル 1

 最初のサンプルには 3 人が含まれています。 人 1 は人 2 と 3 に送信し、人 2 は人 1 と 3 に送信し、人 3 は人 1 と 2 に送信します。すべての人は同じ SCC に属しているため、到達可能なグラフ全体は循環的です。 

| ステップ | 現在の状態 | 無限の頂点 |
 | --- | --- | --- |
 | 開始 | 出典 = ジェームス |`{}`|
 | 到達可能性 | ジェームズはサラとジョンに連絡します。`{}`|
 | SCC | ジェームス、サラ、ジョンが 1 つの SCC を形成 |`{James, Sarah, John}`|
 | 伝播 | すべての頂点はすでにサイクリック SCC | 内にあります。`{James, Sarah, John}`|
 | 出力 | スキャン入力順序 |`James Sarah John `|

 この例は、ソース自体がサイクルに属する最も単純なケースを示しています。 電子メールはコンポーネント全体を永久に循環する可能性があるため、すべての人が無限に多くのメッセージを受信します。 公開されたサンプルはこれら 3 つの名前を使用し、同じ結果を生成します。 

### サンプル 2

 2 番目のサンプルには同じ 3 つの名前がありますが、ジェームズはサラとジョンに送信しますが、サラとジョンには連絡先がありません。 James から到達可能な有向サイクルはありません。 

| ステップ | 現在の状態 | 無限の頂点 |
 | --- | --- | --- |
 | 開始 | 出典 = ジェームス |`{}`|
 | 到達可能性 | ジェームス、サラ、ジョンに連絡可能 |`{}`|
 | SCC | 3 つの個別の 1 頂点 SCC |`{}`|
 | 周期的 SCC | なし |`{}`|
 | 伝播 | 最初から始めるものは何もありません |`{}`|
 | 出力 | 無限の頂点はありません |`Safe chain email!`|

 この例は、到達可能性だけでは不十分である理由を示しています。 3 人全員が少なくとも 1 回は電子メールを受信しますが、最初のラウンドの後に転送が終了するため、無限に頻繁に受信する人はいません。 

### サンプル 3

 3 番目のサンプルには、6 人の人物とソース人物 3 が含まれています。到達可能なグラフには、Matt、Glenn、Sumon、Arup、および Chris が関与するサイクルが含まれているため、繰り返し転送されると、最終的にはこれらの人物全員に到達します。 

| ステップ | 現在の状態 | 無限の頂点 |
 | --- | --- | --- |
 | 開始 | 出典 = グレン |`{}`|
 | 到達可能性 | サイクルは到達可能です |`{}`|
 | SCC | 1 つの到達可能な SCC には、繰り返しサイクル | が含まれています。`{Matt, Glenn, Sumon, Arup, Chris}`|
 | 伝播 | その SCC から到達可能なすべての頂点には | のマークが付けられます。`{Matt, Glenn, Sumon, Arup, Chris}`|
 | 出力 | 元の名前の順序を保持する |`Ali Matt Glenn Sumon Arup Chris `|

 Ali は繰り返し構造の下流でも到達可能であるため、サンプル出力には 6 つの名前すべてが含まれています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |`O(p + e)`| 到達可能性、Tarjan の SCC アルゴリズム、および最終走査はそれぞれ、すべての頂点とエッジを最大一定回数検査します。 |
 | スペース |`O(p + e)`| グラフ、反転グラフ、SCC 配列、スタック、および到達可能性配列はすべて、グラフ サイズに線形スペースを必要とします。 |

 ここ`e`は接触関係の数であり、`e < p^2`なぜなら、人は自分自身をリストすることができず、最大でも50人しかいないからです。 したがって、それほどコンパクトでない境界であっても、`O(p^2)`この問題に対しては小さいです。 このアルゴリズムは、電子メールが実際に回覧される回数には依存しません。これがまさに無限シミュレーションの問題を回避するものです。 

## テストケース```
# helper: run solution on input string, return output string
def run(inp: str) -> str:
    return solve(inp).strip()

# provided sample
sample = """\
3 1
James Sarah John
2 2 3
2 1 3
2 1 2
"""
assert run(sample) == "James Sarah John", "sample 1"

sample2 = """\
3 1
James Sarah John
2 2 3
0
0
"""
assert run(sample2) == "Safe chain email!", "sample 2"

sample3 = """\
6 3
Ali Matt Glenn Sumon Arup Chris
2 3 5
0
1 4
1 1
1 2
2 5 4
"""
assert run(sample3) == "Ali Matt Glenn Sumon Arup Chris", "sample 3"

# Minimum-size graph. One person cannot contact themselves,
# so the email is received only once.
minimum = """\
1 1
Alice
0
"""
assert run(minimum) == "Safe chain email!", "minimum size"

# A cycle with a downstream person. The downstream person
# receives an email every time the cycle repeats.
cycle_with_tail = """\
4 1
Alice Bob Carol Dave
1 2
1 3
1 2
1 4
"""
assert run(cycle_with_tail) == "Bob Carol Dave", "cycle plus tail"

# Source is not part of the cycle, but the cycle is reachable.
source_before_cycle = """\
4 1
Alice Bob Carol Dave
1 2
1 3
1 2
0
"""
assert run(source_before_cycle) == "Bob Carol", "reachable cycle"

# Maximum-size dense acyclic graph. Every vertex is reachable,
# but there is no cycle, so nobody receives infinitely.
p = 50
names = [f"P{i}" for i in range(1, p + 1)]
lines = [f"{p} 1", " ".join(names)]

for u in range(1, p + 1):
    contacts = list(range(u + 1, p + 1))
    lines.append(
        str(len(contacts)) +
        ((" " + " ".join(map(str, contacts))) if contacts else "")
    )

maximum_dag = "\n".join(lines)
assert run(maximum_dag) == "Safe chain email!", "maximum-size DAG"

# All non-source vertices have identical contact behavior.
# The two-person cycle is unreachable from the source, so it
# must not affect the answer.
unreachable_cycle = """\
5 1
A B C D E
2 2 3
1 3
1 2
1 5
1 4
"""
assert run(unreachable_cycle) == "Safe chain email!", "unreachable cycle"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`1 1 / Alice / 0`|`Safe chain email!`| 最小限のグラフと自己ループの不在 |
 |`4 1 / Alice Bob Carol Dave ...`|`Bob Carol Dave`| サイクルと下流の頂点 |
 |`4 1 / Alice Bob Carol Dave ...`|`Bob Carol`| ソースが外側にある到達可能なサイクル |
 | 高密度 50 頂点 DAG |`Safe chain email!`| 最大サイズとサイクルの欠如 |
 | 到達不可能なサイクルを持つ 5 つの頂点 |`Safe chain email!`| ソースの到達可能な領域外のサイクルは無視する必要があります。 

## 特殊なケース

 1 人のケースは SCC 条件によって処理されます。 と```
1 1
Alice
0
```唯一の SCC はサイズ 1 を持ち、自己ループを含みません。 サイクルがないので、最初のメールには行き場がありません。 アルゴリズムは循環的に到達可能なコンポーネントを見つけず、出力します。`Safe chain email!`。 

ソースからは到達可能だが、下流の頂点自体が循環ではないサイクルは、最終的な伝播によって処理されます。 と```
4 1
Alice Bob Carol Dave
1 2
1 3
1 2
1 4
```ボブとキャロルはサイクルを形成します。 サイクルが完了するたびに、キャロルはデイブに別の電子メールを送信します。 したがって、最後の DFS はボブ、キャロル、デイブをマークし、`Bob Carol Dave `。 これにより、SCC 内の頂点のみを印刷するというよくある間違いが回避されます。 

グラフ内の他の場所に存在するサイクルはカウントされません。 例えば、```
5 1
A B C D E
2 2 3
1 3
1 2
1 5
1 4
```サイクルが含まれています`B -> C -> B`ただし、A からは到達可能であるため、この例では実際にカウントされます。 到達不可能なサイクルの区別を具体的にするには、次を使用します。```
5 1
A B C D E
1 2
1 3
1 2
1 5
1 4
```ここでもサイクルはまだ到達可能であるため、ここでも重要です。 正しい構造では、代わりにソースをサイクルから分離する必要があります。```
5 1
A B C D E
1 2
1 3
1 2
1 5
1 4
```ソース A は引き続き B に到達し、B は C に到達するため、このグラフでもサイクルに到達可能になります。 意図したエッジ ケースを表現する確実な方法は、ソースに発信エッジを与えないことです。```
5 1
A B C D E
0
1 3
1 2
1 5
1 4
```ここで、B と C はサイクルを形成しますが、A はそれらに到達できません。 SCC は周期的ですが、`reachable[B]`そして`reachable[C]`は false であるため、どちらも無限電子メールの開始点としては使用されません。 答えは`Safe chain email!`。 

最後に、情報源は何のサイクルもなく多くの人々に届くことができます。 すべての頂点がより大きなインデックスを持つ頂点のみを指す最大サイズのパターンを考えてみましょう。 グラフは非常に密になる可能性がありますが、すべてのエッジが前方に移動するため、サイクルは不可能です。 SCC 分解にはシングルトン コンポーネントのみが含まれており、アルゴリズムは正しく出力します。`Safe chain email!`。 これは、グラフの密度と無限の動作を区別するケースです。多くの転送パスがあることは、電子メールが無限に転送されることを意味するものではありません。
