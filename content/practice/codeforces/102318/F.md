---
title: "CF 102318F - 複合輸送"
description: "当社は最大 400 都市との交通ネットワークを持っています。 どの都市にも関連するスイッチング コストがかかります。 荷物は、航空、海上、鉄道、トラックの 4 つの輸送モードのいずれかを使用して都市間を移動できます。 各ルート セグメントは方向性がなく、1 つのトランスポート モードにのみ属します。"
date: "2026-08-14T04:42:47+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102318
codeforces_index: "F"
codeforces_contest_name: "UCF Locals 2017"
rating: 0
weight: 102318
solve_time_s: 60
verified: true
draft: false
---

[CF 102318F - マルチモーダルトランスポート](https://codeforces.com/problemset/problem/102318/F)

 **評価:** -
 **タグ:** -
 **解決時間:** 1 分
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 当社は最大 400 都市との交通ネットワークを持っています。 どの都市にも関連するスイッチング コストがかかります。 荷物は、次の 4 つの輸送モードのいずれかを使用して都市間を移動できます。`AIR`、`SEA`、`RAIL`、 または`TRUCK`。 

各ルート セグメントは方向性がなく、1 つのトランスポート モードにのみ属します。 そのコストは、そのモードの使用中にそのセグメントに沿ってパッケージを移動する料金です。 中間の都市では、パッケージは同じモードを無料で使用し続けることも、別のモードに切り替えてその都市の切り替えコストを支払うこともできます。 出発都市は任意のモードを使用でき、目的地には任意のモードを使用できます。 タスクは、指定された出発地から目的地までの可能な最小総コストを見つけることです。 入力には複数の独立したテスト ケースが含まれており、各テスト ケースは 1 つの最小コストを要求します。 

決定的に難しいのは、ある都市から別の都市への移動費用が 2 つの都市だけで決まるわけではないということです。 また、現在使用されているトランスポート モードによっても異なります。 都市に到着`A`空路で市内に到着`A`鉄道による場合は州が異なります。将来の選択により切り替えコストが異なる可能性があるためです。 

都市の数は最大 400 であるため、4 つの交通モードを明示的に表すと、グラフには最大で 400 の都市が存在します。`4 * 400 = 1600`州。 最大 40,000 のルート セグメントが存在する可能性がありますが、都市ごとに可能なスイッチング接続は 6 つだけです。 これにより、最大約 42400 個の無向エッジを持つまばらなグラフが得られます。 拡張グラフ上の 2 次アルゴリズムでは、パスごとにすでに約 256 万回の状態比較が行​​われますが、全ペアの 3 次アルゴリズムではおよそ 256 万回の状態比較が必要になります。`1600^3 = 4.096 * 10^9`これは 4 秒の制限をはるかに超えています。 グラフは厳密に正のコストでも重み付けされているため、ダイクストラのアルゴリズムは自然に適合します。 

1 つ目のエッジ ケースは、モードを変更する方が 1 つのモードに留まるよりもコストが高くなる直接的なルートです。 例えば、```
1
2
A 100
B 100
1
A B AIR 7
A B
```答えは、`7`。 すべての旅行で両方のエンドポイントでスイッチング コストを支払う必要があると想定する不注意な実装では、誤って追加される可能性があります。`100`または`200`。 出発地ではモードの切り替えは必要ありませんし、目的地に到着する場合もモードの切り替えは必要ありません。 

2 番目のエッジ ケースは、最も安価なルートを取得するために中間都市での乗り換えが必要な場合です。```
1
3
A 5
B 2
C 5
2
A B AIR 4
B C RAIL 3
A C
```答えは、`9`、荷物が移動するため`A -> B`航空便、有料`2`で`B`鉄道に切り替えて走行します`B -> C`鉄道で。 都市ごとに 1 つの最短距離のみを維持する実装では、荷物が到着したという情報が失われます。`B`航空便による場合、スイッチングコストを誤って省略したり、間違ったタイミングで適用したりする可能性があります。 

3 番目のエッジ ケースは、複数のモードが同じ都市のペアを接続できることです。 例えば、```
1
2
A 10
B 10
2
A B AIR 100
A B TRUCK 3
A B
```答えは、`3`。 交通手段を含めずに 2 つの都市名間のエッジを 1 つだけ保存する不注意なグラフ表現は、より安価な、またはより関連性の高い状態を上書きする可能性があります。 

4 番目のエッジ ケースは、最も安価なパスがあるモードで開始され、別のモードで終了する可能性があることです。 例えば、```
1
2
A 50
B 50
2
A B AIR 10
A B TRUCK 3
A B
```答えは、`3`、パッケージは単に出発地でトラックを選択できるためです。 より一般的には、宛先は、最初に選択されたモードの最終ルート セグメントで使用されるモードだけでなく、4 つのモード状態すべてで成功したと見なされなければなりません。 

## アプローチ

 直接的な総当たり最短パスの定式化では、すべての都市を 4 つの州 (各交通モードに 1 つずつ) に拡張し、拡張されたすべての州に対してフロイド-ウォーシャルなどの高密度の最短パス アルゴリズムを実行します。 州`(city, mode)`は、荷物が現在その都市にあり、現在の輸送モードが`mode`。 ルート セグメントは、同じモードを持つ州間のエッジになりますが、同じ都市でのモードから別のモードへの変更は、その都市のスイッチング コストを重みとするエッジになります。 すべての法的な行程はこの拡張されたグラフ内のパスに対応するため、全ペアの最短パス アルゴリズムが正しいことになります。 

問題は 3 次の実行時間です。 400 の都市には 1600 の拡張州があるため、フロイド ウォーシャルのパフォーマンスは約`1600^3 = 4.096 * 10^9`1 つのテスト ケースに対する緩和反復。 グラフには約 40,000 のルート セグメントしか含まれていないため、これを密として扱うと、入力によって得られる疎性が正確に失われます。 

より高速な解決策を明らかにする観察は、状態のすべてのペア間に最短パスが必要ないということです。 出発地と目的地は 1 つだけです。 すべてのエッジの重みは正であるため、ダイクストラは原点状態から直接最短パスを見つけることができます。 

トランスポート モードを正しくキャプチャしているため、同じ拡張グラフを保持します。 各都市は 4 つの州に貢献します。 すべての都市について、6 つの異なるモードのペアすべてが都市のスイッチング コストで有利になります。 すべてのルート セグメントについて、両方のエンドポイントで同じモードで対応する状態を接続します。 最後に、ダイクストラを 4 回実行する代わりに、ゼロコスト エッジで接続されたスーパー ソースを原点の 4 つのモードに概念的に導入します。 同様に、4 つの原点モード距離をゼロに初期化できます。 答えは、目的地の 4 つのモード状態間の最小距離です。 

結果として得られるグラフには最大 1600 個の頂点があり、およそ`40000 + 6 * 400 = 42400`無向エッジ。 バイナリ ヒープの場合、ダイクストラは次のようにします。`O((V + E) log V)`、ここでは簡単に十分小さいです。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルート・フォース、フロイド・ウォーシャル | O((4c)^3) | O((4c)^2) | 遅すぎる |
 | 最適、拡大グラフ上のダイクストラ | O((4c + r) log c) | O(c + r) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 都市を読み取り、各都市に 4 つの整数の州 ID (各交通モードに 1 つずつ) を割り当てます。 の状態`city + mode`将来の意思決定を行うために必要な情報を正確に表します。 
2. すべての都市について、異なる輸送モードの各ペアを、その都市のスイッチング コストを重みとする無向エッジに接続します。 4 つのモードが次の結果を与えるため、そのようなペアは 6 つあります。`4 choose 2 = 6`可能なスイッチ。 ルート エッジは既にそのモードを使用して連続する都市を接続しているため、同じモードを継続する場合はスイッチ エッジは必要ありません。 
3. 路線区間ごと`(u, v, mode, cost)`、 接続する`(u, mode)`そして`(v, mode)`指定されたコストの無向エッジを使用します。 このモードは州の一部であるため、空路で使用できるルートを誤って鉄道やトラックで使用できるようにしてはなりません。 
4. 起点都市に属する 4 つの州すべてのダイクストラ距離をゼロに初期化します。 これは、ゼロコスト エッジを持つ新しいスーパー ソースをこれら 4 つの状態に追加することに相当します。 パッケージは既存のモードなしで開始されるため、最初のトランスポート モードを選択するときに切り替えコストはかかりません。 
5. このマルチソース初期化から Dijkstra を実行します。 状態が優先キューから削除されるたびに、そのグラフのエッジをすべて緩和してみてください。 すべてのエッジの重みは正であるため、状態の最初に最終的に決定された距離が、その状態の真の最短距離になります。 
6. ダイクストラが終了したら、目的地の都市に属する 4 つの州を視察し、最小距離を取ります。 荷物はどの輸送モードを使用しても到着できるため、4 つの州のいずれも除外されるべきではありません。 

機能する理由: 実際のあらゆる交通移動を、展開されたグラフ内のパスに変換できます。 ルート セグメントは同じモードを維持し、ルート エッジによって表されますが、すべてのモード変更は、都市のスイッチング コストを負担する 1 つのスイッチング エッジによって表されます。 展開されたグラフのすべてのエッジが合法的な輸送行為に対応するため、その逆も当てはまります。 したがって、拡張されたグラフのパス コストは、元の問題の輸送コストとまったく同じです。 4 つの距離ゼロの元の状態はすべての正当な初期モードを表し、4 つの宛先状態の最小値をとることはすべての正当な最終モードを表します。 次に、ダイクストラは、そのようなすべてのパスの中での最小コストを返します。 

## Python ソリューション```python
import sys
import heapq

input = sys.stdin.readline

MODES = {
    "AIR": 0,
    "SEA": 1,
    "RAIL": 2,
    "TRUCK": 3,
}

INF = 10**30

def solve_case():
    city_count = int(input())

    city_id = {}
    switch_cost = [0] * city_count

    for i in range(city_count):
        name, cost = input().split()
        city_id[name] = i
        switch_cost[i] = int(cost)

    state_count = city_count * 4
    graph = [[] for _ in range(state_count)]

    def state(city, mode):
        return city * 4 + mode

    # Six mode-switch edges inside every city.
    for city in range(city_count):
        base = city * 4
        cost = switch_cost[city]

        for a in range(4):
            for b in range(a + 1, 4):
                u = base + a
                v = base + b
                graph[u].append((v, cost))
                graph[v].append((u, cost))

    route_count = int(input())

    for _ in range(route_count):
        u_name, v_name, mode_name, cost = input().split()
        u = city_id[u_name]
        v = city_id[v_name]
        mode = MODES[mode_name]
        cost = int(cost)

        a = state(u, mode)
        b = state(v, mode)

        graph[a].append((b, cost))
        graph[b].append((a, cost))

    origin_name, destination_name = input().split()
    origin = city_id[origin_name]
    destination = city_id[destination_name]

    dist = [INF] * state_count
    heap = []

    # Any transport mode can be chosen at the origin for free.
    for mode in range(4):
        s = state(origin, mode)
        dist[s] = 0
        heapq.heappush(heap, (0, s))

    while heap:
        current_dist, u = heapq.heappop(heap)

        if current_dist != dist[u]:
            continue

        for v, weight in graph[u]:
            new_dist = current_dist + weight
            if new_dist < dist[v]:
                dist[v] = new_dist
                heapq.heappush(heap, (new_dist, v))

    return min(dist[state(destination, mode)] for mode in range(4))

def main():
    test_cases = int(input())
    answers = []

    for _ in range(test_cases):
        answers.append(str(solve_case()))

    sys.stdout.write("\n".join(answers))

if __name__ == "__main__":
    main()
```の`city_id`Dictionary は、入力された都市名をコンパクトな整数インデックスに変換します。 都市名は入力の読み取り中にのみ使用されるため、実際のグラフに文字列を保持する理由はありません。 

の`state`関数マップ`(city, mode)`に`city * 4 + mode`。 これにより、すべての状態に一意のインデックスが与えられます。`0`を通して`4 * city_count - 1`。 The fixed factor of four also makes the mode transitions easy to construct.

 The six switching edges are created before the route edges. For a city with switching cost`c`、異なるモードのすべてのペアに重みのエッジが与えられます。`c`。 この問題のルールでは、航空から鉄道への変更は鉄道から航空への変更と同じコストがかかるため、グラフには方向性がありません。 

ルート エッジは、同じモードの状態のみを接続します。 入力ルートに次のような場合`A B AIR 7`、対応する唯一の輸送エッジは`(A, AIR) <-> (B, AIR)`。 このパッケージは、6 つのスイッチング エッジの 1 つを通過することによってのみ、都市でモードを変更できます。 

4 つの原点状態は距離 0 に初期化されます。 これは、実際のスーパーソース頂点を追加するよりもすっきりしており、余分なグラフ ノードを回避できます。 また、原点での誤ったスイッチング充電も防止します。 

優先キューには、同じ状態の複数のエントリが含まれる場合があります。 古いエントリがポップされると、`current_dist != dist[u]`古いものとして識別し、スキップします。 これは標準的なヒープベースのダイクストラ パターンであり、別の訪問配列の必要性を回避します。 

Python の整数はオーバーフローしないため、パスに多くのエッジが含まれている場合でも距離の計算は安全です。`INF`考えられるすべての有効なパス コストよりも大きくする必要があるだけであり、`10**30`快適に十分です。 

宛先は原点に対して対称的に扱われます。 パッケージはどの輸送モードを使用しても終了する可能性があるため、4 つの宛先州すべてで最小値を採用します。 

## 実用的な例

 ### サンプル 1

 最初のサンプルには 4 つの都市と 7 つのルート セグメントがあります。 からの最安ルート`JACKSONVILLE`に`TAMPA`単に単一の路線区間が最も安いというわけではありません。 荷物は通過することができます`MIAMI`、途中でモードを変更します。 

関連する状態の進行は次のとおりです。 

| ステップ | 都市 | 現在のモード | 距離 |
 | --- | --- | --- | --- |
 | 0 | ジャクソンビル | 海 | 0 |
 | 1 | マイアミ | 海 | 15 |
 | 2 | マイアミ | レール | 20 |
 | 3 | ジャクソンビル | レール | 65 |
 | 4 | タンパ | レール | 75 |

 この特定のルートは最適ではありません。`MIAMI`最適なパスには不要です。 実際の最適コストは、次のルートをたどることによって得られます。`MIAMI`そして`SEA`その後に、適切な安価な継続が続き、サンプルの回答が生成されます`55`。 グラフ モデルによって示された重要な点は、異なるモードを持つ都市に到着すると、真に異なる状態が作成されるため、ダイクストラはそれらを区別する必要があるということです。 

サンプル出力は次のとおりです。```
55
```### サンプル 2

 都市は二つしかない。 ご利用可能なルートは空路です。`7`、トラックルートの費用がかかります`3`、鉄道路線の費用もかかります`19`。 

| ステップ | 状態 | 距離 |
 | --- | --- | --- |
 | 0 | オーランド、エア | 0 |
 | 0 | オーランド、海 | 0 |
 | 0 | オーランド、鉄道 | 0 |
 | 0 | オーランド、トラック | 0 |
 | 1 | タンパ、トラック | 3 |
 | 1 | エア州タンパ | 7 |
 | 1 | タンパ、鉄道 | 19 |

 目的地の最小距離は次のとおりです。`3`、したがって、答えは次のようになります。```
3
```このサンプルは、アルゴリズムが開始モードを自由に選択できることを確認します。 また、単に別のモードが入力の最初にリストされているという理由だけで、あるモードの安価なルートを非表示にしてはいけないことも確認します。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O((c + r) log c) | がある`4c`州と`O(c + r)`エッジ、およびヒープベースのダイクストラはそれらを対数時間で処理します。 
| スペース | O(c + r) | 拡張された隣接リストには、都市ごとに 4 つの州、都市ごとに 6 つのスイッチ エッジ、ルート セグメントごとに 2 つのダイレクト エントリが格納されます。 

と`c <= 400`、展開されたグラフには最大 1600 個の頂点があります。 でさえ`r = 40000`、グラフはまばらなままで、数万のエッジしかありません。 したがって、ヒープベースの実装は、4 秒および 256 MB の制限内に快適に収まります。 

## テストケース```python
import sys
import io
import heapq

MODES = {
    "AIR": 0,
    "SEA": 1,
    "RAIL": 2,
    "TRUCK": 3,
}

INF = 10**30

def solve_case():
    city_count = int(input())
    city_id = {}
    switch_cost = [0] * city_count

    for i in range(city_count):
        name, cost = input().split()
        city_id[name] = i
        switch_cost[i] = int(cost)

    state_count = city_count * 4
    graph = [[] for _ in range(state_count)]

    def state(city, mode):
        return city * 4 + mode

    for city in range(city_count):
        base = city * 4
        cost = switch_cost[city]
        for a in range(4):
            for b in range(a + 1, 4):
                u = base + a
                v = base + b
                graph[u].append((v, cost))
                graph[v].append((u, cost))

    route_count = int(input())

    for _ in range(route_count):
        u_name, v_name, mode_name, cost = input().split()
        u = city_id[u_name]
        v = city_id[v_name]
        mode = MODES[mode_name]
        cost = int(cost)

        a = state(u, mode)
        b = state(v, mode)

        graph[a].append((b, cost))
        graph[b].append((a, cost))

    origin_name, destination_name = input().split()
    origin = city_id[origin_name]
    destination = city_id[destination_name]

    dist = [INF] * state_count
    heap = []

    for mode in range(4):
        s = state(origin, mode)
        dist[s] = 0
        heapq.heappush(heap, (0, s))

    while heap:
        current_dist, u = heapq.heappop(heap)

        if current_dist != dist[u]:
            continue

        for v, weight in graph[u]:
            nd = current_dist + weight
            if nd < dist[v]:
                dist[v] = nd
                heapq.heappush(heap, (nd, v))

    return min(dist[state(destination, mode)] for mode in range(4))

def solve_all(data: str) -> str:
    global input
    old_input = input
    input = io.StringIO(data).readline

    test_cases = int(input())
    result = []

    for _ in range(test_cases):
        result.append(str(solve_case()))

    input = old_input
    return "\n".join(result)

# Provided samples.
sample = """\
2
4
ORLANDO 10
TAMPA 15
MIAMI 5
JACKSONVILLE 10
7
TAMPA JACKSONVILLE AIR 100
MIAMI TAMPA SEA 70
JACKSONVILLE MIAMI RAIL 45
ORLANDO JACKSONVILLE TRUCK 85
TAMPA ORLANDO RAIL 10
MIAMI JACKSONVILLE SEA 15
ORLANDO MIAMI TRUCK 15
JACKSONVILLE TAMPA
2
ORLANDO 15
TAMPA 10
3
ORLANDO TAMPA AIR 7
TAMPA ORLANDO TRUCK 3
ORLANDO TAMPA RAIL 19
ORLANDO TAMPA
"""
assert solve_all(sample) == "55\n3", "provided samples"

# Minimum-size graph, direct route.
case_min = """\
1
2
A 100
B 100
1
A B AIR 7
A B
"""
assert solve_all(case_min) == "7", "minimum-size case"

# All route modes between the same two cities.
case_all_modes = """\
1
2
A 5
B 5
4
A B AIR 10
A B SEA 20
A B RAIL 30
A B TRUCK 4
A B
"""
assert solve_all(case_all_modes) == "4", "all modes between one pair"

# Switching at an intermediate city is necessary for the best route.
case_switch = """\
1
3
A 5
B 2
C 5
2
A B AIR 4
B C RAIL 3
A C
"""
assert solve_all(case_switch) == "9", "intermediate mode switch"

# Boundary-style case with many route edges.
# 10 cities, all four modes on every consecutive pair and both directions.
# The cheapest route is the chain using TRUCK throughout.
def build_dense_case():
    n = 10
    lines = ["1", str(n)]

    for i in range(n):
        lines.append(f"C{i} 1000")

    routes = []
    for i in range(n - 1):
        for mode, cost in [
            ("AIR", 100),
            ("SEA", 80),
            ("RAIL", 60),
            ("TRUCK", 1),
        ]:
            routes.append(f"C{i} C{i+1} {mode} {cost}")

    lines.append(str(len(routes)))
    lines.extend(routes)
    lines.append("C0 C9")

    return "\n".join(lines) + "\n"

assert solve_all(build_dense_case()) == "9", "dense route case"

# Large-state construction with 400 cities.
# Only 399 route segments are needed, so this also checks that the
# four-state expansion scales to the maximum city count.
def build_max_city_case():
    n = 400
    lines = ["1", str(n)]

    for i in range(n):
        lines.append(f"C{i} 1")

    lines.append(str(n - 1))

    for i in range(n - 1):
        lines.append(f"C{i} C{i+1} TRUCK 2")

    lines.append("C0 C399")
    return "\n".join(lines) + "\n"

assert solve_all(build_max_city_case()) == str(399 * 2), "maximum city count"

print("All tests passed.")
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 2 つのサンプルを提供 |`55`、`3`| 公式の例と基本的なグラフの構築 |
 |`A -> B`費用のために飛行機で`7`|`7`| 最小サイズのネットワークとエンドポイントでのスイッチング コストなし |
 | 間の4つのモード`A`そして`B`|`4`| 同じ都市間の複数の交通モード |
 |`A -> B`空路で、`B -> C`鉄道で |`9`| 中間モードスイッチの正しい充電 |
 | 連続したペアで 4 つのモードすべてを備えた 10 都市 |`9`| 高密度のルート データと繰り返される同じモードのトラバーサル |
 | 400都市チェーン |`798`| 都市の最大数と正しい州の展開 |

 ## 特殊なケース

 モード変更を行わない直行旅行の場合は、次のことを考慮してください。```
1
2
A 100
B 100
1
A B AIR 7
A B
```の 4 つの状態`A`距離ゼロから開始します。 から`(A, AIR)`、ルートエッジに到達します`(B, AIR)`費用がかかる`7`。 したがって、目的地の最小距離は次のようになります。`7`。 スイッチングエッジ`A`そして`B`決して必要ないため、スイッチング コストが誤って請求されることはありません。 

中間のモード変更の場合は、次のことを考慮してください。```
1
3
A 5
B 2
C 5
2
A B AIR 4
B C RAIL 3
A C
```の初期状態`A`すべての距離はゼロです。 エアエッジが与える`(B, AIR)`距離`4`。 で`B`、からのスイッチングエッジ`(B, AIR)`に`(B, RAIL)`費用`2`、距離を生み出す`6`。 レールの端が到達します`(C, RAIL)`遠くで`9`。 目的地の最小距離は次のとおりです。`9`。 状態表現は、スイッチング電荷を正確に正しいポイントに表示させるものです。 

同じ都市間で複数のモードを使用する場合は、次のことを考慮してください。```
1
2
A 10
B 10
2
A B AIR 100
A B TRUCK 3
A B
```州`(A, AIR)`そして`(A, TRUCK)`どちらもゼロから始まります。 対応するルート エッジが距離につながります`100`そして`3`。 答えは、`3`。 各モードには独自の状態があるため、2 番目のルートを読み取っても最初のルートを破壊したり、その逆を行うことはできません。 

モードを複数回変更するパスの場合、同じ構成が繰り返し適用されます。 仮定する`A -> B`空気です、`B -> C`レールです、そして`C -> D`トラックです。 展開されたグラフを通るパスは次の形式になります。`(A, AIR)`、`(B, AIR)`、`(B, RAIL)`、`(C, RAIL)`、`(C, TRUCK)`、`(D, TRUCK)`。 合計コストは、正確に 3 つのルート コストとスイッチング コストを足したものです。`B`そして`C`。 各スイッチは 1 つのグラフ エッジで表されるため、スイッチング料金がいつ請求されるかについて曖昧さはありません。 

最後に、送信元と送信先には必須の受信モードまたは送信モードがないため、特別な処理が必要です。 4 つの原点状態をすべてゼロに初期化すると、最初のモードを自由に選択できるようになります。 4 つの目的地すべての状態で最小値を取ると、どのモードでも終了できる自由がモデル化されます。 どちらかの側を 1 つの任意のモードに制限すると、別の問題が解決され、より大きな答えが得られる可能性があります。
