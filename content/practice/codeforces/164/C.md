---
title: "CF 164C - マシンプログラミング"
description: "いくつかのジョブがあり、それぞれに開始時間、期間、利益が設定されています。 ジョブを開始するマシンは、s から s + t - 1 までの期間全体にわたって占有され続けます。k 台のマシンしか所有していないため、最大で k 個のジョブが同時に実行されます。 目標は、すべてのジョブをスケジュールすることではありません。"
date: "2026-05-29T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "flows", "graphs"]
categories: ["algorithms"]
codeforces_contest: 164
codeforces_index: "C"
codeforces_contest_name: "VK Cup 2012 Round 3"
rating: 2400
weight: 164
solve_time_s: 136
verified: true
draft: false
---

[CF 164C - Machine Programming](https://codeforces.com/problemset/problem/164/C)

 **評価:** 2400
 **タグ:** フロー、グラフ
 **解決時間:** 2 分 16 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 いくつかのジョブがあり、それぞれに開始時間、期間、利益が設定されています。 A machine that starts a job stays occupied for the entire interval from`s`を通して`s + t - 1`。 せいぜい`k`所有しているのは私たちだけであるため、ジョブは同時に実行される可能性があります`k`機械。 

目標は、すべてのジョブをスケジュールすることではありません。 一部を廃棄させていただく場合がございます。 利益の合計が最大であり、それを決して超えないサブセットが必要です。`k`いつでもジョブが重複します。 

出力はバイナリ配列です。 位置`i`等しい`1`もし`i`- 番目のジョブが選択されており、`0`さもないと。 

制約によってソリューションが完全に形作られます。 多くてもあります`1000`ジョブ数は、約`O(n^2)`エッジですが、指数関数的な検索には大きすぎます。 マシンの数は最大で`50`、これは、限られた容量を超えるある種のフローまたは DP を示唆しています。 到達時間座標`10^9`したがって、あらゆる瞬間を反復することは不可能です。 有効なソリューションでは、タイムラインを意味のあるイベント ポイントのみに圧縮する必要があります。 

細かい点は間隔の定義です。 占領している仕事`[s, s+t-1]`前に開始された別のジョブと競合する`s+t`。 例えば：```
2 1
1 2 5
3 1 7
```最初のジョブに時間がかかります`1,2`。 2番目はから始まります`3`, したがって、それらは重複せず、両方を選択することができます。 閉じた間隔を使用した不注意な実装`[s, s+t]`このペアは誤って拒否されてしまいます。 

もう 1 つの簡単な間違いは、複数のジョブが同じ開始時刻または終了時刻を共有する場合に発生します。```
3 1
1 2 5
1 1 4
2 1 4
```最初の仕事は`1,2`。 2番目が占めます`1`。 3番目が占めます`2`。 最初の仕事をどちらかの小規模な仕事と一緒に行うことはできませんが、合計の利益を得るために 2 番目と 3 番目の仕事を一緒に行うことはできます。`8`。 不正にソートしたり、一貫性なく等しい座標を処理する実装では、誤って一度に 3 つの重複ジョブが許可される可能性があります。`1`または`2`。 

最後の危険なケースは、多くのジョブが重なっている場合です。`k > 1`。```
4 2
1 5 10
2 3 8
3 2 7
4 1 6
```ここでは、最大 2 つのジョブを同時に実行できます。 重複しないジョブを常に選択するという貪欲な戦略は、重複が許容されるまで許容されるため失敗します。`k`。 問題は、通常の加重間隔スケジューリングではありません。 

## アプローチ

 ブルートフォースのアイデアは単純です。 ジョブのすべてのサブセットを試し、いつでもそれ以上かどうかを確認します。`k`仕事が重なり、最高の利益を維持します。 考えられるすべての答えが検討されるため、正しさは明らかです。 

問題は探索空間のサイズです。 と`n = 1000`、サブセットの数は`2^1000`、それは天文学的に不可能です。 チェックだけでも`2^40`サブセットはすでに制限をはるかに超えています。 

より洗練されたブルート フォースの方向性は、インターバルでの動的プログラミングです。 のために`k = 1`の場合、問題は古典的な加重間隔スケジューリングになります。 ジョブを終了時間で並べ替え、二分探索を使用して各間隔を次の互換性のある間隔に接続します。 それは`O(n log n)`解決。 

障害となるのは、`k`を超える可能性があります`1`。 複数のオーバーラップが許可されると、状態は最後に選択された間隔だけに依存しなくなります。 現在、異なるマシンが、異なる時点で終了する異なるジョブによって占有されている可能性があります。 マシン状態に対する直接的な DP は組み合わせ的に爆発します。 

重要な観察は、制約は基本的に時間の経過に伴う容量に関するものであるということです。 いつでも、せいぜい`k`ジョブはそのポイントを通過する可能性があります。 これはまさに、最小コスト最大フロー モデルの自然な構造です。 

すべての個別の時間座標を圧縮します。 連続する時間の間に、容量のあるタイムライン エッジを作成します`k`。 フローの 1 単位の送信は、時間の経過とともに 1 台のマシンが前進することを表します。 ジョブを選択するということは、利益を収集しながら開始時間から終了時間までジャンプする特別なエッジを通じて 1 つのフロー単位を迂回させることを意味します。 

利益は最大化する必要があるため、利益をマイナスのコストに変換し、最小コストのフローを計算します。 タイムラインのエッジにはコストがかかります`0`、各ジョブエッジにはコストがかかります`-c_i`。 フロー パスは、時間の経過とともにアイドル状態になるか、有益な場合はいつでも有益なジョブ エッジを使用する可能性があります。 

これにより、スケジューリング制約が標準的なフロー保存とエッジ キャパシティに変換されます。 容量`k`タイムラインの端では以下を保証します`k`同時進行の仕事。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(2^n * n^2) | O(n) | 遅すぎる |
 | 最適な最小コストフロー | O(n^3) | O(n^2) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. すべてのジョブについて、その終了瞬間を次のように計算します。`e = s + t`。 

使用する`s + t`の代わりに`s + t - 1`占有区間を半開範囲に変換します`[s, e)`。 2 つのジョブは、一方が他方のジョブと同時に開始された場合、またはその後に開始された場合に正確に互換性があります。`e`。 
2. すべての個別の開始座標と終了座標を収集し、並べ替えます。 

実際の数値は次のように大きくなる可能性があります。`10^9`ただし、相対的な順序のみが重要です。 座標圧縮により、グラフのサイズが最大でも小さくなります。`2n`ノード。 
3. 圧縮された時間インデックスに対して有向グラフを構築します。 

圧縮時間が次の場合`T[0], T[1], ..., T[m-1]`、エッジを作成します。`i -> i+1`容量あり`k`そしてコスト`0`。 

これらのエッジは、ジョブを実行せずに時間の経過とともに前進するマシンを表します。 
4. 容量のある初めてのノードに接続されたソースを追加します`k`、前回のノードを容量のあるシンクに接続します`k`。 

これは正確に作成します`k`利用可能な機械流量の単位。 
5. すべてのジョブに対して、圧縮された開始インデックスから圧縮された終了インデックスまでエッジを追加します。 

エッジには次のものがあります。 

容量`1`料金`-profit`このエッジを利用するということは、1 台のマシンがその占有期間中にそのジョブを実行することを意味します。 
6. ソースからシンクまで min-cost max-flow を実行します。 

フローにより、どのジョブ エッジを使用しても十分な収益が得られるかが自然に決まります。 
7. 選択したジョブを復元します。 

キャパシティが大きくなったすべてのジョブエッジ`0`フローによって完全に使用されたため、そのジョブは最適なスケジュールに属します。 

### なぜ効果があるのか

 フローの各単位は、時間の経過とともに前進する 1 台のマシンを表します。 タイムラインのエッジは最大で次の制限を維持します`k`同時マシンのみなので、`k`単位は任意の時間セグメントをまたぐことができます。 

ジョブ エッジは、開始座標から終了座標まで 1 つのマシンを占有します。 そのエッジは中間のタイムライン エッジをバイパスするため、マシンは別の重複するジョブを同時に実行できません。 

フロー保存により、すべてのマシンがジョブの有効な時系列シーケンスに従うことが保証されます。 合計フローコストは、選択したジョブのマイナスの合計利益と等しくなります。 コストを最小化することは、利益を最大化することと同じです。 

すべての能力とコストはスケジュールの制約と目標を正確にエンコードしているため、すべての実行可能なフローは同一の利益をもたらす有効なスケジュールに対応し、すべての有効なスケジュールは実行可能なフローに対応します。 

## Python ソリューション```python
import sys
from collections import deque

input = sys.stdin.readline

INF = 10**18

class Edge:
    def __init__(self, to, rev, cap, cost):
        self.to = to
        self.rev = rev
        self.cap = cap
        self.cost = cost

class MinCostFlow:
    def __init__(self, n):
        self.n = n
        self.g = [[] for _ in range(n)]

    def add_edge(self, fr, to, cap, cost):
        fwd = Edge(to, len(self.g[to]), cap, cost)
        rev = Edge(fr, len(self.g[fr]), 0, -cost)
        self.g[fr].append(fwd)
        self.g[to].append(rev)

    def min_cost_flow(self, s, t, maxf):
        n = self.n
        flow = 0
        cost = 0

        potential = [0] * n

        while flow < maxf:
            dist = [INF] * n
            parent_v = [-1] * n
            parent_e = [-1] * n

            dist[s] = 0

            inq = [False] * n
            q = deque([s])
            inq[s] = True

            while q:
                v = q.popleft()
                inq[v] = False

                for i, e in enumerate(self.g[v]):
                    if e.cap <= 0:
                        continue

                    nd = dist[v] + e.cost + potential[v] - potential[e.to]

                    if nd < dist[e.to]:
                        dist[e.to] = nd
                        parent_v[e.to] = v
                        parent_e[e.to] = i

                        if not inq[e.to]:
                            inq[e.to] = True
                            q.append(e.to)

            if dist[t] == INF:
                break

            for v in range(n):
                if dist[v] < INF:
                    potential[v] += dist[v]

            addf = maxf - flow
            v = t

            while v != s:
                pv = parent_v[v]
                pe = parent_e[v]
                addf = min(addf, self.g[pv][pe].cap)
                v = pv

            v = t

            while v != s:
                pv = parent_v[v]
                pe = parent_e[v]
                e = self.g[pv][pe]

                e.cap -= addf
                self.g[v][e.rev].cap += addf

                cost += addf * e.cost
                v = pv

            flow += addf

        return flow, cost

def solve():
    n, k = map(int, input().split())

    jobs = []
    coords = []

    for i in range(n):
        s, t, c = map(int, input().split())
        e = s + t

        jobs.append((s, e, c, i))

        coords.append(s)
        coords.append(e)

    coords = sorted(set(coords))
    idx = {x: i for i, x in enumerate(coords)}

    m = len(coords)

    SRC = m
    SNK = m + 1

    mcmf = MinCostFlow(m + 2)

    mcmf.add_edge(SRC, 0, k, 0)

    for i in range(m - 1):
        mcmf.add_edge(i, i + 1, k, 0)

    mcmf.add_edge(m - 1, SNK, k, 0)

    job_edges = [None] * n

    for s, e, c, original_idx in jobs:
        u = idx[s]
        v = idx[e]

        edge_index = len(mcmf.g[u])

        mcmf.add_edge(u, v, 1, -c)

        job_edges[original_idx] = (u, edge_index)

    mcmf.min_cost_flow(SRC, SNK, k)

    ans = [0] * n

    for i in range(n):
        u, ei = job_edges[i]
        e = mcmf.g[u][ei]

        if e.cap == 0:
            ans[i] = 1

    print(*ans)

if __name__ == "__main__":
    solve()
```グラフには、圧縮時間座標ごとに 1 つのノードが含まれます。 タイムラインのエッジは連続する座標を接続し、最大で`k`マシンが前進します。 

最も重要な実装の詳細は、間隔の変換です。 元の間隔には両端が含まれます。`[s, s+t-1]`。 に変換すると、`[s, s+t)`オフバイワンの問題を回避し、互換性チェックをよりクリーンにします。 

各ジョブエッジの保存容量`1`。 フロー終了後、使用済みエッジには残存能力があります`0`。 そうやって選んだ仕事が再構築されるのです。 

最短パスの計算では、潜在的なコストを削減して使用します。 収益性の高いジョブには負のエッジの重みがあるため、コストが負になる場合があります。 ポテンシャルにより、各増強後のコストがマイナスにならないことが保証されます。 

もう 1 つの微妙な詳細は、正確に送信することです。`k`たとえ一部のマシンがアイドル状態のままであっても、フローの単位。 アイドル状態のマシンは、コストゼロでタイムラインのエッジを単純に横断します。 

## 実用的な例

 ### 例 1

 入力:```
3 1
2 7 5
1 3 3
4 1 3
```間隔は次のようになります。 

| 仕事 | 間隔 | 利益 |
 | --- | --- | --- |
 | 1 | [2, 9) | 5 |
 | 2 | [1, 4) | 3 |
 | 3 | [4,5) | 3 |

 圧縮された座標:

 | インデックス | 時間 |
 | --- | --- |
 | 0 | 1 |
 | 1 | 2 |
 | 2 | 4 |
 | 3 | 5 |
 | 4 | 9 |

 ジョブエッジ:

 | 仕事 | エッジ |
 | --- | --- |
 | 1 | 1 -> 4 |
 | 2 | 0 -> 2 |
 | 3 | 2 -> 3 |

 最適な流路は次のとおりです。 

| ステップ | 使用済みエッジ | 総利益 |
 | --- | --- | --- |
 | 1 | ジョブ 2 | 3 |
 | 2 | ジョブ 3 | 6 |

 ジョブ1を選択するだけで利益が得られます`5`, そのため、フローではジョブ 2 と 3 が優先されます。 

このトレースは、互換性のある順次ジョブがタイムライン エッジを通じてどのように自然に連鎖するかを示しています。 

### 例 2

 入力:```
4 2
1 5 10
2 3 8
3 2 7
4 1 6
```変換された間隔:

 | 仕事 | 間隔 | 利益 |
 | --- | --- | --- |
 | 1 | [1,6) | 10 |
 | 2 | [2, 5) | 8 |
 | 3 | [3,5) | 7 |
 | 4 | [4,5) | 6 |

 時間範囲で`[4,5)`、4 つのジョブが重複していますが、存在するマシンは 2 つだけです。 

フローでは以下が選択されます。 

| 機械 | 求人 |
 | --- | --- |
 | 1 | ジョブ 1 |
 | 2 | ジョブ 2 |

 合計利益は`18`。 

トレースは容量を示します`k=2`タイムラインのエッジ容量によって自動的に適用されます。 同じ時間セグメントを通過できるフローの単位は 2 つまでです。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n^3) | グラフには O(n) 個のノードと O(n) 個のエッジがあり、最小コスト フローは最短パスで最大 k 個の拡張を実行します。 
| スペース | O(n^2) | 残差グラフストレージ |

 と`n <= 1000`、これは制限内に快適に収まります。 グラフには数千のエッジしか含まれておらず、`k <= 50`, そのため、増強の数は少ないです。 

## テストケース```python
# helper: run solution on input string, return output string
import sys
import io

def run(inp: str) -> str:
    from collections import deque

    input_data = io.StringIO(inp)
    output_data = io.StringIO()

    input = input_data.readline

    INF = 10**18

    class Edge:
        def __init__(self, to, rev, cap, cost):
            self.to = to
            self.rev = rev
            self.cap = cap
            self.cost = cost

    class MinCostFlow:
        def __init__(self, n):
            self.n = n
            self.g = [[] for _ in range(n)]

        def add_edge(self, fr, to, cap, cost):
            fwd = Edge(to, len(self.g[to]), cap, cost)
            rev = Edge(fr, len(self.g[fr]), 0, -cost)
            self.g[fr].append(fwd)
            self.g[to].append(rev)

        def min_cost_flow(self, s, t, maxf):
            n = self.n
            flow = 0
            potential = [0] * n

            while flow < maxf:
                dist = [INF] * n
                pv = [-1] * n
                pe = [-1] * n

                dist[s] = 0

                q = deque([s])
                inq = [False] * n
                inq[s] = True

                while q:
                    v = q.popleft()
                    inq[v] = False

                    for i, e in enumerate(self.g[v]):
                        if e.cap <= 0:
                            continue

                        nd = dist[v] + e.cost + potential[v] - potential[e.to]

                        if nd < dist[e.to]:
                            dist[e.to] = nd
                            pv[e.to] = v
                            pe[e.to] = i

                            if not inq[e.to]:
                                inq[e.to] = True
                                q.append(e.to)

                if dist[t] == INF:
                    break

                for i in range(n):
                    if dist[i] < INF:
                        potential[i] += dist[i]

                addf = maxf - flow
                v = t

                while v != s:
                    addf = min(addf, self.g[pv[v]][pe[v]].cap)
                    v = pv[v]

                v = t

                while v != s:
                    e = self.g[pv[v]][pe[v]]
                    e.cap -= addf
                    self.g[v][e.rev].cap += addf
                    v = pv[v]

                flow += addf

    n, k = map(int, input().split())

    jobs = []
    coords = []

    for i in range(n):
        s, t, c = map(int, input().split())
        e = s + t
        jobs.append((s, e, c, i))
        coords.extend([s, e])

    coords = sorted(set(coords))
    idx = {x: i for i, x in enumerate(coords)}

    m = len(coords)

    SRC = m
    SNK = m + 1

    mcf = MinCostFlow(m + 2)

    mcf.add_edge(SRC, 0, k, 0)

    for i in range(m - 1):
        mcf.add_edge(i, i + 1, k, 0)

    mcf.add_edge(m - 1, SNK, k, 0)

    pos = [None] * n

    for s, e, c, i in jobs:
        u = idx[s]
        v = idx[e]

        edge_id = len(mcf.g[u])

        mcf.add_edge(u, v, 1, -c)

        pos[i] = (u, edge_id)

    mcf.min_cost_flow(SRC, SNK, k)

    ans = []

    for i in range(n):
        u, ei = pos[i]
        ans.append("1" if mcf.g[u][ei].cap == 0 else "0")

    return " ".join(ans)

# provided sample
assert run(
"""3 1
2 7 5
1 3 3
4 1 3
"""
) == "0 1 1"

# minimum size
assert run(
"""1 1
1 1 10
"""
) == "1"

# overlapping jobs, only one machine
assert run(
"""2 1
1 5 10
2 3 9
"""
) == "1 0"

# touching intervals should both work
assert run(
"""2 1
1 2 5
3 1 6
"""
) == "1 1"

# two machines allow overlap
assert run(
"""3 2
1 5 10
2 3 9
3 2 8
"""
) == "1 1 0"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 単発の仕事 |`1`| 最小サイズ境界 |
 | k=1 の 2 つの重複ジョブ |`1 0`| 容量制限 |
 | 接触間隔 |`1 1`| 正しい半開区間の処理 |
 | k=2 で重なり合う |`1 1 0`| 複数の同時マシン |

 ## 特殊なケース

 間隔がエンドポイントでのみ接触するジョブを検討してください。```
2 1
1 2 5
3 1 6
```間隔は`[1,3)`そして`[3,4)`。 圧縮されたエッジは重なり合うことなく連続して接続されます。 1 つのフロー ユニットが両方のジョブ エッジを順番に通過する可能性があるため、両方のジョブが選択されます。 

ここで、開始時刻が同一であることを考えてみましょう。```
3 1
1 5 10
1 1 4
2 1 4
```最初の仕事は`[1,6)`。 残りの2人が占拠`[1,2)`そして`[2,3)`。 マシンが 1 台しか存在しないため、フローは大きな間隔のエッジと小さな間隔のチェーンを同時に使用することはできません。 チェーンが小さいほど利益が得られる`8`、長い仕事は成果をもたらしますが、`10`したがって、アルゴリズムは最初のジョブのみを正しく選択します。 

最後に、複数のマシンについて考えてみましょう。```
4 2
1 5 10
2 3 8
3 2 7
4 1 6
```4 つのジョブはすべて時間的に重複します`4`ただし、タイムラインのエッジには容量があります`2`。 そのセグメントを通過できるフロー ユニットは最大 2 つまでであるため、残差グラフは物理的に 3 つ以上の重複するジョブを選択することを防ぎます。 結果として得られるスケジュールは、建設によって常に実現可能です。
