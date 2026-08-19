---
title: "CF 102192G - カードゲーム"
description: "各カードには 2 つの数字があり、たとえば現在の表面に (x)、裏面に (y) があります。 カードを裏返すと、これら 2 つの数字のどちらが表示されるかが変わります。 できるだけ少ないカードをめくりながら、目に見えるすべての数字を異なるものにする必要があります。"
date: "2026-08-18T20:27:20+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102192
codeforces_index: "G"
codeforces_contest_name: "2018 Chinese Multi-University Training, Nanjing U Contest"
rating: 0
weight: 102192
solve_time_s: 242
verified: true
draft: false
---

[CF 102192G - カード ゲーム](https://codeforces.com/problemset/problem/102192/G)

 **評価:** -
 **タグ:** -
 **解決時間:** 4 分 2 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 各カードには 2 つの数字があり、たとえば現在の表面に (x)、裏面に (y) があります。 カードを裏返すと、これら 2 つの数字のどちらが表示されるかが変わります。 できるだけ少ないカードをめくりながら、目に見えるすべての数字を異なるものにする必要があります。 すべての最小反転ソリューションの中で、何セットのカードを反転できるかをカウントする必要もあります。 2 つのソリューションは、まったく同じカード インデックスを反転した場合にのみ同じとみなされます。 

便利な表現はグラフです。 すべての個別の数字を頂点として扱い、すべてのカード ((x,y)) を (x) と (y) の間の無向エッジとして扱います。 現在表示されている番号は、エッジのどの端点が選択されているかを示します。 したがって、すべてのカードは最初にその目に見える数字を指します。 カードを裏返すと、カードの端がもう一方の端点の方向を指します。 

複数のエッジが同じ頂点を向いている場合、複数のカードの表面に数字が表示されます。 したがって、目に見えるすべての数値が一意であるという要件は、最終的な向きですべての頂点の次数が最大 1 であるという要件と正確に一致します。 

入力により (n) が (10^5) に達し、すべてのテスト ケースの合計 (n) が (10^6) に達します。 (O(n^2)) 解はすでに大規模なテストには遅すぎますが、指数関数的な検索はまったく実行不可能です。 目的のソリューションでは、すべてのカードとすべての数字を一定回数だけ処理し、テスト ケースごとに (O(n)) 個のソリューションを提供する必要があります。 

いくつかのケースは誤って処理されやすいものです。 

考慮する```
1
1
1 1
```答えは```
0 1
```カードはセルフループですが、両面に同じ数字が含まれているため、裏返しても何も変わりません。 ループを通常の可逆エッジとして扱う不用意なグラフ実装は、偽の 2 番目の方向をカウントする可能性があります。 

考慮する```
1
2
1 2
1 3
```答えは```
1 2
```グラフはツリーです。 ルートとして頂点 2 を選択するには 2 番目のカードを裏返す必要があり、ルートとして頂点 3 を選択するには最初のカードを裏返す必要があります。 どちらのソリューションも 1 つのフリップを使用します。 現在重複している番号だけを見て、それを貪欲に修正すると、これら 2 つの全体的に最適な選択肢のうちの 1 つが見逃される可能性があります。 

考慮する```
1
3
1 2
2 3
1 3
```グラフは単一サイクルです。 サイクルには、正確に 2 つの有効な方向が考えられます。 この例では、どちらも 1 回のフリップが必要なので、答えは次のようになります。```
1 2
```すべてのサイクルに最適な向きが 1 つだけあると仮定した解決策では、カウントが間違ってしまいます。 

最後に考えてみましょう```
1
2
1 1
1 1
```答えは```
-1 -1
```カードは 2 枚ありますが、使用できる番号は 1 つだけです。 より一般的には、頂点よりも多くのエッジを含む接続コンポーネントは、すべての頂点が最大 1 つの入力エッジを受け取るように方向付けることはできません。 重複した個々の番号をローカルで修復できるかどうかを確認するだけでは十分ではありません。 

## アプローチ

 直接的なアプローチは、すべてのカードを裏返すかどうかを独立して決定することです。 (2^n) 個の可能な反転セットがあります。 各セットについて、すべて (n) 個の表示される数値を検査し、それらが区別できるかどうかを確認し、反転の最小数とその頻度を維持することができます。 これは、考えられるすべての最終状態を明示的に考慮しているため、正しいです。 最悪の場合の作業は (O(n2^n)) で、これは (n=10^5) の場合、およそ (10^5\cdot2^{100000}) の演算となり、実現可能性をはるかに超えています。 

グラフの定式化により、検索空間が (2^n) よりもはるかに多くの構造を持つ理由が明らかになります。 すべてのカードはエッジになり、選択されたエンドポイントはその目に見える番号になります。 最終条件は単純に最大でも 1 度です。 

(v) 頂点と (e) エッジを含む連結コンポーネントの場合、すべての度数の合計は正確に (e) に​​なります。 すべての頂点は最大でも 1 つの次数を持つことができるため、(e\le v) を持たなければなりません。 (e<v) で接続された無向グラフはツリーですが、(e=v) で接続されたグラフは単環式です。 (e>v) の場合、そのコンポーネントは不可能です。 

これにより、問題は 2 つの非常に構造化されたケースに集約されます。 

ツリーの場合、(v-1) 個のエッジと (v) 個の頂点があるため、すべての有効な方向には次数 0 の頂点が 1 つだけあります。 その頂点がルートとして選択されると、すべてのエッジが強制的にルートから離れる方向を向くようになります。 初期方向がこの強制方向と一致しないエッジの数を最小にするルートを見つけるだけで済みます。 これは、動的プログラミングの再ルートに関する標準的な問題です。 

単環式コンポーネントの場合、すべての頂点の次数が正確に 1 でなければなりません。 サイクルに接続されているツリー内のすべてのエッジは、強制的にサイクルから離れる方向を指すようになります。 サイクル自体には、時計回りまたは反時計回りの 2 つの方向しかありません。 両方の反転コストを計算し、同点の場合は両方をカウントして、より良い方を保持します。 自己ループは 1 頂点サイクルですが、その 2 つの側面は同一であるため、可能な反転セットは 1 つだけ提供されます。 

したがって、重要な構造的観察は、すべての解決可能なコンポーネントがツリーまたは単環式グラフのいずれかであるということです。 1 次頂点を繰り返し削除することでサイクルを見つけることができます。 削除されたエッジは接続されたツリーを形成し、残されたエッジはサイクルを形成します。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | (O(n2^n)) | (O(n)) | 遅すぎる |
 | 最適 | (O(n)) | (O(n)) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 無向マルチグラフを構築します。 頂点(x)は番号(x)を表し、カード(i=(x,y))が辺(i)となります。 (x) を最初のフロント サイドで選択されたエンドポイントとして格納します。 自己ループは許可されます。 
2. 初期状態を、現在のフロント番号に向かうすべてのエッジの向きとみなします。 有効な最終状態は、すべての頂点の次数が最大 1 である方向です。 すべての接続コンポーネントについて、概念的にその頂点とエッジを数えます。 コンポーネントに頂点よりも多くのエッジがある場合、その合計入次数が大きすぎて頂点に収まらないため、(-1\ -1) が出力されます。 
3. 次数 1 の頂点を繰り返し削除します。 リーフ (v) がエッジ (e) に​​よって残りの隣接 (u) に接続されている場合、そのエッジを削除し、(u) を (v) の親として記録します。 生き残った部分にルートまたはサイクルが含まれるすべての有効な方向において、このツリーのエッジは (u) から (v) を指す必要があります。 最初に選択されたエンドポイントが (u) の場合、フリップ カウントへの寄与は正確に 1 です。

ピーリング処理によりグラフ構造も同時に特定されます。 ツリーである有効なコンポーネントでは、すべてのエッジが最終的に削除されます。 有効な一周期コンポーネントでは、正確にサイクル エッジが残ります。 
4. 剥離後、各頂点の残量を検査します。 有効なコンポーネントの残りの頂点は、自己ループを 2 回カウントして、次数がちょうど 2 でなければなりません。 残りの頂点の次数が異なる正の場合、コンポーネントには複数のサイクルが含まれており、不可能です。 
5. すべての接続コンポーネントを処理します。 頂点とエッジを 1 回トラバースして、頂点と、皮をむいたツリー エッジによってすでに寄与されている合計コストを収集します。 コンポーネントにエッジが残っていない場合、それはツリーです。 それ以外の場合は一環式です。 
6. ツリー コンポーネントの場合、ピーリング プロセスにより、親のない頂点が 1 つだけ残ります。 その頂点は自然の根です。 させて`base`はがされたすべてのエッジのコストの合計になります。 これは、最後に残った頂点がルートである場合のコストです。これは、記録されたすべての親子エッジが親から子の方向を向いているためです。 
7. このルートからツリーを再ルートします。 エッジが現在のルート側頂点 (v) をその子 (u) に接続し、最初のフロント エンドポイントが (x) であると仮定します。 ルートが (v) の場合、望ましい方向は (v\to u) であるため、このエッジのコストは ([x=v]) になります。 ルートを (u) に移動すると、目的の方向は (u\to v) になるため、コストは ([x=u]) になります。 したがって、

 [
 コスト[u]=コスト[v]+[x=u]-[x=v]。 
】

 各エッジはこの走査中にルートを 1 回だけ変更するため、考えられるすべてのルート コストが線形時間で取得されます。 最小値を維持し、それを達成するルートの数を数えます。 
8. 単環式コンポーネントの場合、剥がされたすべてのツリー エッジにはすでに強制的な方向が設定されています。 残りの頂点から削除されていないエッジをたどって、残りのサイクルを見つけます。 
9. サイクルを一方向に移動します。 サイクル エッジが (u) から (v) に移動する場合、時計回りの方向では (v) が選択され、反対方向の場合は (u) が選択されます。 最初のフロント エンドポイントが反対側のエンドポイントである場合は常に、対応するコストに 1 を加算します。 複数の頂点を持つサイクルの場合、2 つの方向により裏返されたカードの異なるセットが生成されます。 コストが等しい場合は、両方ともカウントされます。 
10. 自己ループの場合、両方のエンドポイントが同じ番号であるため、有効な方向は 1 つだけです。 その寄与はゼロであり、二方向ではなく一方向に寄与します。 
11. すべてのコンポーネントの最小フリップ数を加算します。 さまざまな接続コンポーネントの選択は独立しているため、最適なフリップ セットの数を法 (998244353) で乗算します。 

アルゴリズム全体の背後にある不変条件は、生き残った側が固定されると、有効な解決策において、皮をむかれた木のエッジはすべて 1 つの可能な方向しか持たないということです。 ツリーに残された唯一の自由はルートの選択です。 単循環コンポーネントでは、接続されたツリーには自由がなく、残された唯一の選択肢はサイクルの方向です。 したがって、アルゴリズムは、(2^n) 個の元のフリップ セットを列挙することなく、圧縮された形式ですべての可能な有効な方向を列挙します。 

## Python ソリューション```python
import sys
from array import array

input = sys.stdin.readline

MOD = 998244353

def solve():
    t = int(input())
    out = []

    for _ in range(t):
        n = int(input())
        m = 2 * n
        V = m + 1

        # Forward-star adjacency.
        head = array('i', [-1]) * V
        to = array('i', [0]) * (2 * n)
        nxt = array('i', [0]) * (2 * n)

        # For edge i, x[i] is the initially visible endpoint.
        x = array('i', [0]) * n
        y = array('i', [0]) * n

        degree = array('i', [0]) * V

        for i in range(n):
            a, b = map(int, input().split())
            x[i] = a
            y[i] = b

            p = 2 * i
            to[p] = b
            nxt[p] = head[a]
            head[a] = p

            to[p + 1] = a
            nxt[p + 1] = head[b]
            head[b] = p + 1

            if a == b:
                degree[a] += 2
            else:
                degree[a] += 1
                degree[b] += 1

        # parent[v] is the vertex that survived when v was peeled.
        parent = array('i', [-1]) * V

        # child_cost[v] is the cost of edge parent[v] -> v
        # in the orientation forced by the surviving side.
        child_cost = bytearray(V)

        removed = bytearray(n)

        # Peel all trees from the outside toward their roots/cycles.
        queue = []
        for v in range(1, V):
            if head[v] != -1 and degree[v] == 1:
                queue.append(v)

        qpos = 0
        while qpos < len(queue):
            v = queue[qpos]
            qpos += 1

            if degree[v] != 1:
                continue

            arc = head[v]
            while arc != -1:
                e = arc >> 1
                if not removed[e]:
                    break
                arc = nxt[arc]

            if arc == -1:
                continue

            removed[e] = 1

            a = x[e]
            b = y[e]
            u = b if a == v else a

            parent[v] = u
            child_cost[v] = 1 if x[e] == u else 0

            degree[v] -= 1
            degree[u] -= 1

            if degree[u] == 1:
                queue.append(u)

        # After peeling, every surviving vertex must have degree 2.
        possible = True
        for v in range(1, V):
            if degree[v] != 0 and degree[v] != 2:
                possible = False
                break

        if not possible:
            out.append("-1 -1")
            continue

        seen = bytearray(V)
        root_cost = array('i', [0]) * V

        answer_cost = 0
        answer_ways = 1

        # Process one connected component at a time.
        for start in range(1, V):
            if head[start] == -1 or seen[start]:
                continue

            stack = [start]
            seen[start] = 1
            vertices = array('i')

            base = 0
            cycle_start = -1

            while stack:
                v = stack.pop()
                vertices.append(v)

                base += child_cost[v]
                if degree[v] > 0:
                    cycle_start = v

                arc = head[v]
                while arc != -1:
                    u = to[arc]
                    if not seen[u]:
                        seen[u] = 1
                        stack.append(u)
                    arc = nxt[arc]

            if cycle_start == -1:
                # The component is a tree.
                root = -1
                for v in vertices:
                    if parent[v] == -1:
                        root = v
                        break

                root_cost[root] = base

                best = base
                ways = 1

                stack = [root]

                while stack:
                    v = stack.pop()
                    cv = root_cost[v]

                    if cv < best:
                        best = cv
                        ways = 1
                    elif cv == best and v != root:
                        ways += 1

                    arc = head[v]
                    while arc != -1:
                        u = to[arc]
                        e = arc >> 1

                        # In a peeled tree, parent[u] == v means u
                        # is a child of v.
                        if parent[u] == v:
                            delta = (1 if x[e] == u else 0) - \
                                    (1 if x[e] == v else 0)
                            root_cost[u] = cv + delta
                            stack.append(u)

                        arc = nxt[arc]

                answer_cost += best
                answer_ways = answer_ways * ways % MOD

            else:
                # The component is unicyclic.
                # Find the remaining cycle.
                cycle_vertices = [cycle_start]
                cycle_edges = []

                cur = cycle_start
                prev_edge = -1

                while True:
                    arc = head[cur]
                    chosen = -1

                    while arc != -1:
                        e = arc >> 1
                        if not removed[e] and e != prev_edge:
                            chosen = e
                            break
                        arc = nxt[arc]

                    if chosen == -1:
                        break

                    cycle_edges.append(chosen)

                    a = x[chosen]
                    b = y[chosen]
                    nxt_vertex = b if a == cur else a

                    if nxt_vertex == cycle_start:
                        break

                    cycle_vertices.append(nxt_vertex)
                    prev_edge = chosen
                    cur = nxt_vertex

                k = len(cycle_vertices)

                if k == 1:
                    # The only possible cycle is a self-loop.
                    cycle_cost = 0
                    cycle_ways = 1
                else:
                    clockwise = 0
                    counterclockwise = 0

                    for i in range(k):
                        u = cycle_vertices[i]
                        v = cycle_vertices[(i + 1) % k]
                        e = cycle_edges[i]

                        # Clockwise wants u -> v, so v is visible.
                        if x[e] == u:
                            clockwise += 1

                        # Counterclockwise wants v -> u, so u is visible.
                        if x[e] == v:
                            counterclockwise += 1

                    if clockwise < counterclockwise:
                        cycle_cost = clockwise
                        cycle_ways = 1
                    elif clockwise > counterclockwise:
                        cycle_cost = counterclockwise
                        cycle_ways = 1
                    else:
                        cycle_cost = clockwise
                        cycle_ways = 2

                answer_cost += base + cycle_cost
                answer_ways = answer_ways * cycle_ways % MOD

        out.append(f"{answer_cost} {answer_ways}")

    sys.stdout.write("\n".join(out))

if __name__ == "__main__":
    solve()
```隣接構造では、リストの Python リストではなく、フォワードスター表現が使用されます。 (2n) 個のエンドポイントが発生する可能性があり、メモリ制限が 128 MB しかないため、これは重要です。 の`array('i')`コンテナは頂点とエッジのインデックスをコンパクトに保ちますが、`bytearray`削除されたエッジや訪問された頂点などのブール状態には十分です。 

エッジ インデックスは、次のように隣接インデックスから復元されます。`arc >> 1`。 すべてのカードは 2 つの連続する隣接レコードを提供するため、個別のエッジ ID 配列は必要ありません。 

ピーリング中、`parent[v]`(v) が削除されたときに存続する一意の近隣を記録します。 対応する`child_cost[v]`生き残った頂点から (v) に向かうときにエッジを反転する必要があるかどうかを記録します。 これらの値を合計すると、強制的なツリー方向のコストが求められます。 

ツリーの再ルート化ではリレーションが使用されます。 

[
 コスト[u]-コスト[v]=[x_e=u]-[x_e=v]。 
】

 実装では、現在のルートコストが次の場所に保存されます。`root_cost`したがって、DFS スタックには大きなタプルではなく頂点インデックスのみが含まれます。 これにより、(10^5) 個の頂点を持つパスであってもメモリ使用量が少なく抑えられます。 

サイクルトラバーサルは意図的にチェックします`e != prev_edge`。 この条件がなければ、トラバースは使用したばかりのエッジに沿ってすぐに戻ります。 両方のサイクル方向がまったく同じ可視数値を表し、したがって同じフリップ セットを表すため、自己ループは個別に処理されます。 

ウェイの数に関係するすべての算術は、剰余を求めて計算されます (998244353)。 フリップ数は最大 (n) であるため、通常の Python 整数で十分であり、オーバーフローの問題はありません。 

## 実用的な例

 サンプル 1 の場合、グラフは 2 つの独立したツリー コンポーネントで構成されます。 

最初のコンポーネントには、カード ((1,2)) と ((1,3)) が含まれています。 その剥離プロセスでは、最終的に 1 つの頂点がルートとして選択されます。 関連する再ルート状態は次のとおりです。 

| ルート | エッジ (1-2) のコスト | エッジ (1-3) のコスト | 合計 |
 | --- | --- | --- | --- |
 | 1 | 1 | 1 | 2 |
 | 2 | 0 | 1 | 1 |
 | 3 | 1 | 0 | 1 |

 したがって、その最小値は 1 であり、最適な根は 2 つあります。 

2 番目のコンポーネントは頂点 (4、5、6) を持つまったく同じ形状をしているため、その最小値も 1 で、最適なルートが 2 つあります。 

コンポーネントは独立しており、合計の最小値は (1+1=2)、および (2\cdot2=4) の最小フリップ セットになります。 

| コンポーネント | ベースツリーのコスト | 最小サイクルコスト | 極小値 | ローカルな方法 |
 | --- | --- | --- | --- | --- |
 | (1,2,3) | 1 | 0 | 1 | 2 |
 | (4,5,6) | 1 | 0 | 1 | 2 |
 | 合計 | 2 | 0 | 2 | 4 |

 したがって、出力は次のとおりです`2 4`、サンプルと一致します。 

サンプル 2 の場合、2 つのカードはどちらも頂点 1 で自己ループです。各ループは次数 2 に寄与するため、頂点 1 の次数は 4 になります。 葉を取り除くことはできず、残りの次数は 0 でも 2 でもありません。 

| 頂点 | 初期学位 | 剥離後 | 有効なコア学位? |
 | --- | --- | --- | --- |
 | 1 | 4 | 4 | いいえ |

 コンポーネントには 2 つのエッジが含まれていますが、頂点は 1 つだけです。 その合計入次数は 2 である必要がありますが、単一の頂点は最大 1 つを受け入れることができます。 アルゴリズムはテスト ケースを拒否し、次のように出力します。`-1 -1`。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O(n)) | 各カードは 2 つの隣接レコードを提供し、ピーリング、コンポーネント トラバーサル、再ルート、およびサイクル トラバーサルはそれぞれ、すべてのレコードを一定の回数だけ検査します。 |
 | スペース | (O(n)) | グラフに関連する (O(n)) 個の頂点と (O(n)) 個のエッジがあり、コンパクトな配列に格納されます。 |

 最大のテスト ケースには (n=10^5) があり、すべてのテスト ケースの合計は (10^6) になります。 このアルゴリズムは一定数の線形グラフ パスを実行するため、合計作業量は (O(\sum n)) になります。 また、コンパクトな隣接表現により、メモリが (n) に比例するように維持されるため、128 MB の制限に適しています。 

## テストケース```python
import sys
import io
from array import array

MOD = 998244353
input = sys.stdin.readline

def solve():
    t = int(input())
    out = []

    for _ in range(t):
        n = int(input())
        V = 2 * n + 1

        head = array('i', [-1]) * V
        to = array('i', [0]) * (2 * n)
        nxt = array('i', [0]) * (2 * n)
        x = array('i', [0]) * n
        y = array('i', [0]) * n
        degree = array('i', [0]) * V

        for i in range(n):
            a, b = map(int, input().split())
            x[i] = a
            y[i] = b

            p = 2 * i
            to[p] = b
            nxt[p] = head[a]
            head[a] = p

            to[p + 1] = a
            nxt[p + 1] = head[b]
            head[b] = p + 1

            if a == b:
                degree[a] += 2
            else:
                degree[a] += 1
                degree[b] += 1

        parent = array('i', [-1]) * V
        child_cost = bytearray(V)
        removed = bytearray(n)

        queue = []
        for v in range(1, V):
            if head[v] != -1 and degree[v] == 1:
                queue.append(v)

        q = 0
        while q < len(queue):
            v = queue[q]
            q += 1

            if degree[v] != 1:
                continue

            arc = head[v]
            while arc != -1:
                e = arc >> 1
                if not removed[e]:
                    break
                arc = nxt[arc]

            if arc == -1:
                continue

            removed[e] = 1

            a = x[e]
            b = y[e]
            u = b if a == v else a

            parent[v] = u
            child_cost[v] = 1 if x[e] == u else 0

            degree[v] -= 1
            degree[u] -= 1

            if degree[u] == 1:
                queue.append(u)

        if any(degree[v] not in (0, 2) for v in range(1, V)):
            out.append("-1 -1")
            continue

        seen = bytearray(V)
        root_cost = array('i', [0]) * V

        total_cost = 0
        total_ways = 1

        for start in range(1, V):
            if head[start] == -1 or seen[start]:
                continue

            stack = [start]
            seen[start] = 1
            vertices = []
            base = 0
            cycle_start = -1

            while stack:
                v = stack.pop()
                vertices.append(v)
                base += child_cost[v]

                if degree[v] > 0:
                    cycle_start = v

                arc = head[v]
                while arc != -1:
                    u = to[arc]
                    if not seen[u]:
                        seen[u] = 1
                        stack.append(u)
                    arc = nxt[arc]

            if cycle_start == -1:
                root = next(v for v in vertices if parent[v] == -1)

                root_cost[root] = base
                best = base
                ways = 0

                stack = [root]
                while stack:
                    v = stack.pop()
                    cv = root_cost[v]

                    if cv < best:
                        best = cv
                        ways = 1
                    elif cv == best:
                        ways += 1

                    arc = head[v]
                    while arc != -1:
                        u = to[arc]
                        e = arc >> 1

                        if parent[u] == v:
                            delta = (x[e] == u) - (x[e] == v)
                            root_cost[u] = cv + delta
                            stack.append(u)

                        arc = nxt[arc]

                total_cost += best
                total_ways = total_ways * ways % MOD

            else:
                cv = [cycle_start]
                ce = []

                cur = cycle_start
                prev = -1

                while True:
                    arc = head[cur]
                    e = -1

                    while arc != -1:
                        z = arc >> 1
                        if not removed[z] and z != prev:
                            e = z
                            break
                        arc = nxt[arc]

                    if e == -1:
                        break

                    ce.append(e)

                    a = x[e]
                    b = y[e]
                    nxt_v = b if a == cur else a

                    if nxt_v == cycle_start:
                        break

                    cv.append(nxt_v)
                    prev = e
                    cur = nxt_v

                k = len(cv)

                if k == 1:
                    cycle_cost = 0
                    ways = 1
                else:
                    a = 0
                    b = 0

                    for i in range(k):
                        u = cv[i]
                        v = cv[(i + 1) % k]
                        e = ce[i]

                        if x[e] == u:
                            a += 1
                        if x[e] == v:
                            b += 1

                    if a < b:
                        cycle_cost = a
                        ways = 1
                    elif b < a:
                        cycle_cost = b
                        ways = 1
                    else:
                        cycle_cost = a
                        ways = 2

                total_cost += base + cycle_cost
                total_ways = total_ways * ways % MOD

        out.append(f"{total_cost} {total_ways}")

    return "\n".join(out)

def run(inp: str) -> str:
    global input

    old_stdin = sys.stdin
    old_stdout = sys.stdout
    old_input = input

    try:
        sys.stdin = io.StringIO(inp)
        input = sys.stdin.readline
        sys.stdout = io.StringIO()

        ans = solve()
        if ans is None:
            ans = sys.stdout.getvalue()

        return ans.strip()
    finally:
        sys.stdin = old_stdin
        sys.stdout = old_stdout
        input = old_input

sample = """\
3
4
1 2
1 3
4 5
4 6
2
1 1
1 1
3
1 2
3 4
5 6
"""

assert run(sample) == "2 4\n-1 -1\n0 1", "provided samples"

assert run("""\
1
1
1 2
""") == "0 1", "minimum-size ordinary card"

assert run("""\
1
1
1 1
""") == "0 1", "minimum-size self-loop"

assert run("""\
1
2
1 2
1 3
""") == "1 2", "tree with two optimal roots"

assert run("""\
1
2
1 1
1 1
""") == "-1 -1", "all-equal values are impossible"

assert run("""\
1
2
1 4
2 3
""") == "0 1", "maximum endpoint value 2n"

# Maximum-size linear case.
n = 100000
lines = ["1", str(n)]
for i in range(1, n + 1):
    lines.append(f"{i} {i + 1}")

assert run("\n".join(lines) + "\n") == "0 1", "maximum-size path"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`1 / 1 / 1 2`|`0 1`| 最小限の通常のケースと不要なフリップなし |
 |`1 / 1 / 1 1`|`0 1`| セルフループ処理 |
 |`1 / 2 / 1 2 / 1 3`|`1 2`| 木の再ルートと複数の最適なルートのカウント |
 |`1 / 2 / 1 1 / 1 1`|`-1 -1`| 頂点に対してエッジが多すぎるコンポーネントは不可能です |
 |`1 / 2 / 1 4 / 2 3`|`0 1`| 境界値 (2n) とすでに有効な方向 |
 | (100000) 枚のカードのパス |`0 1`| 最大サイズの入力と線形複雑度 |

 ## 特殊なケース

 次のような自己ループ```
1
1
1 1
```1 つのループが 2 つの次数に寄与するため、グラフ内の次数は 2 になります。 葉の剥離時に除去されないため、1頂点サイクルとして認識されます。 サイクル ハンドラーはこのケースを個別に処理し、ゼロの反転コストと 1 つの方向を割り当てます。 出力は`0 1`。 

次のような完全に等しいという不可能なコンポーネントの場合、```
1
2
1 1
1 1
```頂点 1 の次数は 4 です。 葉がありません。残りの次数は 2 ではありません。 アルゴリズムは、方向の計算を試行する前にコンポーネントを拒否し、`-1 -1`。 

木のために```
1
2
1 2
1 3
```皮をむいた葉は2枚と3枚で、1枚が最後に残った根です。 1 をルートとする基本方向には 2 つの反転が必要です。 2 で再ルートすると、コストが (-1) 変更され、コスト 1 になります。3 で再ルートすると、コスト 1 になります。両方のルートが最適化されるため、コンポーネントが寄与します。`1 2`。 

サイクルのために```
1
3
1 2
2 3
1 3
```剥がすべき木の端はありません。 残りのサイクルは 2 つの方向に向けることができます。 一方向で 1 枚のカードを裏返し、もう 1 つの方向でも 1 枚のカードを裏返します。 2 つの方向はすべてのサイクル エッジを反対の方法で反転するため、異なる反転セットに対応し、コンポーネントが寄与します。`1 2`。 

次のようなすでに有効な入力の場合```
1
3
1 2
3 4
5 6
```すべてのコンポーネントは単一のエッジであり、その初期方向はすでに一意の番号条件を満たしている可能性があります。 各ツリーは、最初に選択された方向とは反対の端点に最適なルートを持ち、反転はありません。 最小の反転セット、つまり空のセットが 1 つだけ存在するため、結果は次のようになります。`0 1`。
