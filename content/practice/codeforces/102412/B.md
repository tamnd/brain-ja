---
title: "CF 102412B - 六道の賢者アレクセイ"
description: "パーティーメンバー全員を頂点として考えてください。 左側には (1) ～ (n) の番号が付けられた (n) 個の頂点があり、右側には (n+1) ～ (2n) の番号が付けられた (n) 個の頂点があります。 すべての問題で、左の 1 つの頂点と右の 1 つの頂点の間に 1 つのエッジが作成されます。"
date: "2026-08-10T13:43:45+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102412
codeforces_index: "B"
codeforces_contest_name: "MEX Foundation Contest (supported by AIM Tech)"
rating: 0
weight: 102412
solve_time_s: 352
verified: true
draft: false
---

[CF 102412B - 六道の賢者アレクセイ](https://codeforces.com/problemset/problem/102412/B)

 **評価:** -
 **タグ:** -
 **解決時間:** 5 分 52 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 パーティーメンバー全員を頂点として考えてください。 左側には (1) ～ (n) の番号が付けられた (n) 個の頂点があり、右側には (n+1) ～ (2n) の番号が付けられた (n) 個の頂点があります。 すべての問題で、左の 1 つの頂点と右の 1 つの頂点の間に 1 つのエッジが作成されます。 いくつかの問題により同じメンバーのペアが接続される可能性があるため、平行なエッジが許可されます。 

すべての課題が完了したら、各メンバーは割り当てられた問題の中から 1 つを選択します。 問題は、その 2 つのエンドポイントの両方がその問題を選択したときに正確に解決されます。 すべてのメンバーが選択する問題は最大でも 1 つであるため、解決できる問題の最大数は、正確にこの 2 部マルチグラフの最大一致サイズになります。 タスクは、各頂点の次数によって決定される給与の合計を最小限に抑えながら、最大のマッチングのサイズが (l) と (r) の間になるように正確に (m) 個のエッジを選択することです。 

頂点 (i) が次数 (d_i) を受け取る場合、その寄与は (p_{i,d_i}) です。 したがって、実際のグラフ構造は 2 つのことによってのみ重要になります。すべての頂点の次数がコストを決定し、それらの次数の接続方法が最大のマッチングを決定します。 

境界 (n,m\le30) が主な手がかりです。 (30) は (2^{30}) でさえ約 10 億になるほど十分大きいため、(m) または (n) の指数関数アルゴリズムはすでに大きすぎます。 一方、(30) で区切られた少数の次元を持つ多項式アルゴリズムは現実的です。 目的のソリューションでは、(0) から (n) または (m) までの範囲の複数のカウンターを使用し、(O(n^3m^3)) の動的プログラムを提供します。 受け入れられている C++ 実装では、まさにこの漸近的なアプローチが使用されています。 

少し驚くべき入力境界が 1 つあります。 例には (m=0) が含まれていますが、ステートメントの一部のコピーでは (m) が正の下限とともに記述されています。 実装では当然 (m=0) がサポートされるはずです。 例えば、```
2 0 2 2
8
9
3
4
```にはエッジがまったくないため、最大一致は (2) ではなく (0) になります。 正しい出力は次のとおりです```
DEFEAT
```少なくとも 1 つの問題を想定し、やみくもにマッチングを構築しようとする不注意な解決策は、ここで失敗する可能性があります。 

別のエッジ ケースは (l=0) です。 必要な間隔にゼロが含まれる場合、いつでも (m=0) エッジのないグラフが許可されます。 例えば、```
1 0 0 0
7
9
```両方のメンバーが次数 0 を受け取り、最大一致が 0 であるため、答えは (16) になります。 常に正のサイズのマッチングを作成しようとするソリューションでは、失敗が誤って報告されます。 

マッチングの上限も重要です。 例えば、```
2 1 2 2
0 0
0 0
0 0
0 0
```には問題が 1 つだけ含まれているため、エッジが 1 つしかないため、サイズ (2) のマッチングは不可能です。 正しい出力は次のとおりです`DEFEAT`。 使用可能なメンバーの数のみに基づく単純なチェックでは、両側に 2 人のメンバーがあれば十分であると誤って判断される可能性があります。 

最後に、平行エッジを個別のマッチング機会として扱ってはなりません。 と```
2 2 2 2
0 0 0
0 0 0
0 0 0
0 0 0
```両方の問題が同じメンバーのペアを接続する可能性がありますが、同じ 2 つのメンバーがそれぞれ 2 つの異なる問題を選択することはできないため、これら 2 つの問題によっても解決される問題は 1 つだけになります。 グラフには 2 つの平行なエッジがありますが、最大一致は (1) です。 (m) 個の問題を独立して照合可能なペアとして扱う実装では、これが間違ってしまいます。 

## アプローチ

 最も直接的なアプローチは、すべての問題の 2 つのエンドポイントを決定することです。 1 つの問題に対して (n^2) 個の可能なペアがあるため、すべての割り当てを列挙すると、

 [
 (n^2)^m=n^{2m}
 】

 グラフ。 最大値 (n=m=30) の場合、各グラフの最大一致を計算する前に、これは (900^{30})、およそ (10^{88}) 個の可能性になります。 最終的には考えられるすべての割り当てが考慮されるため、このアプローチは正しいですが、すぐに役に立たなくなります。 

有益な観察は、給与は頂点次数のみに依存するということです。 最後まで正確なエンドポイントを決定することは避けるべきです。 代わりに、慎重に選択したマッチングと頂点カバーによってグラフを記述することができます。これは、二部グラフには、最大マッチングのサイズが最小頂点カバーのサイズに等しいという特性があるためです。 これがケーニッヒの定理です。 

最終的な最大一致を正確に (k) にしたいとします。 (k) 個の一致するエッジと、正確に (k) 個の頂点を含む頂点カバーを明示的に選択できます。 一致するすべてのエッジには、カバー頂点が 1 つだけ含まれている必要があります。 1 つおきのエッジには少なくとも 1 つのカバー頂点が含まれている必要があります。 次に、選択されたカバーのサイズは (k) であるため、(k) 個を超えるエッジを持つマッチングはありませんが、明示的に構築されたマッチングには (k) 個のエッジがあります。 したがって、最大の一致は正確に (k) になります。 

これが重要な削減です。 任意のグラフの接続性について推論する代わりに、すべてのメンバーについて、その次数、それが (k) 個の一致するエッジの 1 つの端点であるかどうか、およびカバーに属しているかどうかを決定するだけで済みます。 

左側については、(x_1) を一致する端点の数として定義し、(x_2) をカバーに属する一致する端点の数として、(x_3) を左側に入射する不一致エッジの数として、(x_4) を左端がカバー内にある不一致発生数として定義します。 右側で同様に (y_1,y_2,y_3,y_4) を定義します。 

最後に必要なのは

 [
 x_1=y_1=k、
 】

 なぜなら、マッチングには (k) 個のエッジがあり、すべてのマッチング エッジには各側に 1 つの端点があるからです。 私たちにも必要です

 [
 x_2+y_2=k、
 】

 一致するすべてのエッジには、カバー内に端点が 1 つだけ存在する必要があるためです。 (m-k) 個の不一致エッジがあるため、

 [
 x_3=y_3=m-k。 
】

 最後に、一致しないエッジはすべてカバーに接触する必要があります。 量 (x_4) と (y_4) のカウントは、これらのエッジの出現数をカバーします。 2 つの端点が両方ともカバー内にあるエッジは 2 回寄与するため、必要十分条件は次のようになります。 

[
 x_4+y_4\ge m-k.
 】

 DP は、これらのカウンターを満たす最も安価な左側と右側の割り当てを独自に見つけます。 次に、互換性のある状態を組み合わせます。 

元の編集では、同じ 4 つのカウンターと (O(n^3m^3)) DP について説明しており、その後、選択された次数情報を実際のエッジとして実現する構成的な手順が説明されています。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | (O(n^{2m}\cdot n^3)) | (O(n^2+m)) | 遅すぎる |
 | 最適なDP | (O(n^3m^3)) | (O(n^3m^2)) スパース Python 表現の場合 | 承認済み |

 ## アルゴリズムのチュートリアル

1. 問題を 2 部マルチグラフのエッジとして解釈します。 メンバーの学位は、そのメンバーに割り当てられた問題の数と正確に一致するため、学位の選択によって給与への貢献が決まります。 
2. 必要な最大一致サイズ (k) を (l\le k\le r) で修正します。 (k) 個のエッジのマッチングと (k) 個の頂点の頂点カバーを明示的に構築します。 ケーニヒの定理によれば、両方の構造のサイズを (k) にすれば、最大の一致を正確に (k) にするのに十分です。 
3. 各辺について、その (n) 個の頂点を動的プログラムで 1 つずつ処理します。 状態は ((x_1,x_2,x_3,x_4)) です。 ここで、(x_1) は選択された一致する端点をカウントし、(x_2) はカバー頂点である一致する端点をカウントし、(x_3) は一致しないエッジの発生をカウントし、(x_4) は端点がカバー内にあるそれらの発生をカウントします。 
4. 頂点を処理するとき、(c) をその不一致の入射エッジの数とする。 考えられる役割は 3 つあります。 頂点はマッチングの外側およびカバーの外側にある場合があり、次数 (c) が与えられます。 一致する端点にはできますが、カバー頂点にはできず、次数 (c+1) が与えられます。 または、一致する端点とカバー頂点の両方であり、再び次数 (c+1) を与え、その (c) 一致しないエッジも (x_4) に寄与する可能性があります。 
5. 対応する給与 (p_{i,d}) を DP コストに加算します。 給与は結果の次数 (d) にのみ依存するため、DP ではエッジの正確な宛先は問題になりません。 
6. 片側のすべての頂点が処理されたら、2 つの辺を結合します。 固定 (k) の場合、両側に (k) 個の一致するエンドポイントと正確に (m-k) 個の不一致発生率があることが必要です。 一致するエンドポイントのカバー数の合計が (k) になり、一致しないエッジでのカバーの発生数の合計が少なくとも (m-k) になるペア状態。 
7. 互換性のあるすべての州間で最低給与総額を維持します。 (k\in[l,r]) に対して互換性のあるペアが存在しない場合は、出力します。`DEFEAT`。 
8. DP 親ポインタからすべての頂点の選択された次数と役割を回復します。 回復された情報により、どの頂点がエンドポイントに一致しているか、そのうちのどれがカバー頂点であるか、および各頂点の最終次数がわかります。 
9. まず、(k) 個の一致するエッジを構築します。 カバーの外側の左側の一致する頂点は、カバー内の右側の一致する頂点とペアになります。 カバー内の左側の一致する頂点は、カバーの外側の右側の一致する頂点とペアになります。 (x_2+y_2=k) であるため、2 つのグループは必要なサイズが正確に一致します。 
10. 一部の非一致エッジでは、カバー内の両方の端点が必要です。 このようなエッジの数は、一致するエッジが配置された後のカバー頂点の残りの次数によって決まります。 必要な数の二重カバーされたエッジが作成されるまで、両側のカバー頂点を接続します。 
11. 残りの次数はすべて、一方の側のカバー頂点ともう一方の側の非カバー一致頂点の間のエッジを使用して満たすことができます。 残りのすべてのエッジにはカバー エンドポイントが 1 つだけあるため、すべてのエッジがカバーされます。 
12. 結果として得られる (m) ペアを出力します。 平行なエッジが許可されるため、構築では同じペアを複数回使用することを避ける必要がありません。 

DP の背後にある不変条件は、保存されたすべての状態が、正確に記録されたマッチングとカバーの発生数、およびそれらのカウンターの可能な最小限の給与を使用して、最初に処理された頂点の実現可能な部分割り当てを記述することです。 2 つの最終状態が 4 つの互換性方程式を満たす場合、構築によりサイズ (k) のマッチングとサイズ (k) のカバーが作成されます。 マッチングはグラフに少なくとも (k) 個のマッチングがあることを証明し、カバーは最大 (k) 個のマッチングがあることを証明します。 したがって、その最大一致は正確に (k) であり、(k\in[l,r]) であるため、グラフは有効です。 DP はすべての州の給与を最小化し、最終的な列挙ではすべての互換性のある州のペアが考慮されるため、選択されたグラフのコストはグローバルに最小になります。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

INF = 10**30

def dp_side(cost, n, m, r):
    """
    DP over one side.

    State:
        (x1, x2, x3, x4)

    x1 = number of matching endpoints
    x2 = number of matching endpoints in the cover
    x3 = number of nonmatching edge incidences
    x4 = number of those incidences whose endpoint is in the cover

    Returns:
        dp      : final-state -> minimum cost
        parents : parent information for reconstruction
    """

    dp = {(0, 0, 0, 0): 0}
    parents = [None] * (n + 1)
    parents[0] = {}

    for i in range(1, n + 1):
        ndp = {}
        par = {}

        for state, old_cost in dp.items():
            x1, x2, x3, x4 = state

            remaining = m - x1 - x3

            for c in range(remaining + 1):
                nx3 = x3 + c

                # Vertex is neither a matching endpoint nor a cover vertex.
                ns = (x1, x2, nx3, x4)
                value = old_cost + cost[i - 1][c]

                if value < ndp.get(ns, INF):
                    ndp[ns] = value
                    par[ns] = (state, 1, c)

                # The vertex is a matching endpoint, but not in the cover.
                if x1 < r and remaining - c > 0:
                    ns = (x1 + 1, x2, nx3, x4)
                    value = old_cost + cost[i - 1][c + 1]

                    if value < ndp.get(ns, INF):
                        ndp[ns] = value
                        par[ns] = (state, 2, c + 1)

                    # The vertex is both a matching endpoint and a cover vertex.
                    ns = (x1 + 1, x2 + 1, nx3, x4 + c)
                    value = old_cost + cost[i - 1][c + 1]

                    if value < ndp.get(ns, INF):
                        ndp[ns] = value
                        par[ns] = (state, 3, c + 1)

        dp = ndp
        parents[i] = par

    return dp, parents

def reconstruct(parents, final_state, n):
    degree = [0] * n
    matched = [False] * n
    cover = [False] * n

    state = final_state

    for i in range(n, 0, -1):
        prev, kind, d = parents[i][state]

        degree[i - 1] = d

        if kind == 2:
            matched[i - 1] = True
        elif kind == 3:
            matched[i - 1] = True
            cover[i - 1] = True

        state = prev

    return degree, matched, cover

def solve_case(n, m, l, r, costs):
    left = costs[:n]
    right = costs[n:]

    left_dp, left_parents = dp_side(left, n, m, r)
    right_dp, right_parents = dp_side(right, n, m, r)

    best = INF
    best_states = None

    max_k = min(r, n, m)

    for k in range(l, max_k + 1):
        nonmatching = m - k

        for x2 in range(k + 1):
            y2 = k - x2

            for x4 in range(nonmatching + 1):
                min_y4 = nonmatching - x4

                for y4 in range(min_y4, nonmatching + 1):
                    ls = (k, x2, nonmatching, x4)
                    rs = (k, y2, nonmatching, y4)

                    lc = left_dp.get(ls)
                    rc = right_dp.get(rs)

                    if lc is None or rc is None:
                        continue

                    value = lc + rc

                    if value < best:
                        best = value
                        best_states = (ls, rs)

    if best_states is None:
        return None

    left_state, right_state = best_states

    left_degree, left_matched, left_cover = reconstruct(
        left_parents, left_state, n
    )
    right_degree, right_matched, right_cover = reconstruct(
        right_parents, right_state, n
    )

    # Vectors are indexed by cover status and matching status.
    groups = [[[], []], [[], []]]

    for i in range(n):
        if left_matched[i]:
            groups[0][1 if left_cover[i] else 0].append(i)
        if right_matched[i]:
            groups[1][1 if right_cover[i] else 0].append(i)

    edges = []

    def add_edge(u, v):
        edges.append((u + 1, v + n + 1))
        left_degree[u] -= 1
        right_degree[v] -= 1

    # Construct the k matching edges.
    #
    # Left non-cover matching vertices pair with right cover
    # matching vertices, and vice versa.
    if len(groups[0][0]) != len(groups[1][1]):
        raise AssertionError("invalid matching partition")
    if len(groups[0][1]) != len(groups[1][0]):
        raise AssertionError("invalid matching partition")

    for u, v in zip(groups[0][0], groups[1][1]):
        add_edge(u, v)

    for u, v in zip(groups[0][1], groups[1][0]):
        add_edge(u, v)

    # Rebuild groups using remaining degrees.
    rem_groups = [[[], []], [[], []]]

    for side in range(2):
        for i in range(n):
            if side == 0:
                d = left_degree[i]
                is_cover = left_cover[i]
            else:
                d = right_degree[i]
                is_cover = right_cover[i]

            if d > 0:
                rem_groups[side][1 if is_cover else 0].append(i)

    # First create edges covered at both endpoints.
    #
    # The amount is exactly the excess cover incidence after all
    # edges with one cover endpoint are accounted for.
    left_cover_degree = sum(
        left_degree[i] for i in range(n) if left_cover[i]
    )
    right_noncover_degree = sum(
        right_degree[i] for i in range(n) if not right_cover[i]
    )

    double_edges = left_cover_degree - right_noncover_degree

    p = rem_groups[0][1]
    q = rem_groups[1][1]

    while double_edges > 0:
        if not p or not q:
            raise AssertionError("failed to construct double-covered edges")

        u = p[-1]
        v = q[-1]
        add_edge(u, v)
        double_edges -= 1

        if left_degree[u] == 0:
            p.pop()
        if right_degree[v] == 0:
            q.pop()

    # Finish all remaining edges. Every such edge has exactly one
    # cover endpoint.
    for side in range(2):
        p = rem_groups[side][0]
        q = rem_groups[1 - side][1]

        while p:
            if not q:
                raise AssertionError("failed to construct remaining edges")

            if side == 0:
                u = p[-1]
                v = q[-1]
                add_edge(u, v)
            else:
                u = q[-1]
                v = p[-1]
                add_edge(u, v)

            if left_degree[u] == 0:
                p.pop()
            if right_degree[v] == 0:
                q.pop()

    if len(edges) != m:
        raise AssertionError("wrong number of edges")

    if any(left_degree) or any(right_degree):
        raise AssertionError("degrees were not fully constructed")

    return best, edges

def solve():
    n, m, l, r = map(int, input().split())

    costs = []
    for _ in range(2 * n):
        costs.append(list(map(int, input().split())))

    result = solve_case(n, m, l, r, costs)

    if result is None:
        print("DEFEAT")
        return

    answer, edges = result

    out = [str(answer)]
    out.extend(f"{u} {v}" for u, v in edges)
    sys.stdout.write("\n".join(out))

if __name__ == "__main__":
    solve()
```実装の最初の部分では、`dp_side`、1 つのグループを独立して処理します。 4 つのカウンターはタプルとして保存され、辞書には実際に到達可能な状態のみが保持されます。 完全な 6 次元配列には大量のオブジェクト オーバーヘッドが必要となるため、これは Python で特に役立ちます。 

(c) 一致しないエッジを持つ頂点の場合、最初の遷移によって次数 (c) が割り当てられます。 頂点がさらに 1 つの一致するエッジを受け取るため、他の 2 つの遷移は次数 (c+1) を割り当てます。 3 番目の遷移も、これらの一致しないエッジのすべてがこの頂点にカバーの終点を持っているため、カバー入射カウンターを (c) だけ増加させます。 

条件`remaining - c > 0`一致する遷移の前に、すべての (m) エッジ スロットが既に消費された後に DP が一致するエッジを作成することを防ぎます。 これは最も間違いやすい境界条件です。 一致するエンドポイントには、(c) 一致しないエッジを超える追加のエッジが常に 1 つ必要です。 

再構成は、保存されている親辞書を逆方向に実行します。 処理された各メンバーについて、その次数、一致するエンドポイントであるかどうか、およびカバー頂点であるかどうかを回復します。 

最終状態の列挙では、

 [
 (k,x_2,m-k,x_4)
 】

 左側と

 [
 (k,k-x_2,m-k,y_4)
 】

 右側にあります。 下限

 [
 y_4\ge m-k-x_4
 】

 これは、(m-k) 個の一致しないエッジがそれぞれ少なくとも 1 つのカバー エンドポイントを持つという要件とまったく同じです。 

この構築では、最初にマッチングを意図的に作成します。 これら (k) 個のエッジが残りの次数要件から削除された後、両側の残りの次数の合計は等しくなります。 一部の残りのエッジは 2 回カバーする必要があり、これらはカバー頂点の間に配置されます。 これらを削除すると、残りのすべてのエッジをカバー頂点と非カバー頂点の間に配置できます。 平行エッジは完全に合法であるため、追加の制限は必要ありません。 

Python の整数には任意の精度があるため、(10^9) もの大きな給与値と、最大 (2n) 個のメンバーにわたるその合計には、特別なオーバーフロー処理は必要ありません。 

## 実用的な例

 ### サンプル 1

 最初のサンプルは```
2 0 2 2
8
9
3
4
```問題はないので、すべての頂点の次数は 0 になります。 可能なグラフは最大一致 (0) のみです。 

関連する最終 DP 状態は、両側のゼロ状態です。 

| 数量 | 左 | 右 |
 | --- | --- | --- |
 | 一致するエンドポイント | 0 | 0 |
 | 一致するエンドポイントをカバーする | 0 | 0 |
 | 不一致の発生率 | 0 | 0 |
 | 不一致の発生をカバーする | 0 | 0 |
 | コスト | 17 | 7 |

 必要な一致サイズは (2) と (2) の間にある必要がありますが、可能な値は (0) のみです。 互換性のある最終状態が存在しないため、アルゴリズムは次のように出力します。`DEFEAT`。 

この例では、(m=0) の場合に DP がエッジを作成しないこと、および要求された下限が実際の一致サイズと比較してチェックされることを確認します。 

### サンプル 2

 2 番目のサンプルには、(n=2)、(m=8)、および (l=r=2) があります。 最適な次数パターンの 1 つは次のとおりです。 

[
 d_L=(4,4),\qquad d_R=(5,3),
 】

 誰の費用は

 [
 p_{1,4}+p_{2,4}+p_{3,5}+p_{4,3}
 =-10+0-9-2=-21。 
】

 対応する最終状態は次のように説明できます。 

| 数量 | 左 | 右 |
 | --- | --- | --- |
 | (k) | 2 | 2 |
 | 一致するエンドポイント (x_1,y_1) | 2 | 2 |
 | カバー内の一致するエンドポイント (x_2,y_2) | 2 | 0 |
 | 不一致の発生率 (x_3,y_3) | 6 | 6 |
 | カバーされた不一致の発生率 (x_4,y_4) | 6 | 0 |
 | コスト | -10 | -11 |

 一致するサイズは (k=2) です。 左の一致する頂点は両方ともカバー内にあるため、一致しないすべてのエッジも左からカバーできます。 (m-k=6) 個の一致しないエッジがあり、左側は 6 つのカバー出現数を提供するため、6 つすべてがカバーされます。 

サンプル出力では、エッジの 4 つのコピー ((1,3))、頂点 (2) と頂点 (4) を含む 3 つのエッジ、および 1 つのエッジ ((2,3)) を使用します。 その次数シーケンスはまさに上記のものであり、左側の 2 つの頂点が唯一非ゼロの左側の頂点であるため、最大一致は正確に (2) になります。 公式サンプルでは総コスト (-21) が示されています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O(n^3m^3)) | 4 つのカウンター全体で (O(n^2m^2)) 個の DP 状態があり、(n) 個の頂点と 2 つの辺のそれぞれについて、遷移ごとに最大 (O(m)) 個の次数の選択肢があります。 
| スペース | (O(n^3m^2)) | Python 実装は、すべて (n) 層の到達可能な状態と再構築情報を保存します。 

意図した範囲は (n,m\le30) のみであるため、低レベルの実装では 6 カウンターの定式化が実用的になります。 公式の制限時間は 2 秒、メモリ制限は 1024 MiB です。 

オリジナルで受け入れられた実装は、同じ (O(n^3m^3)) DP を使用し、完全な DP 構造を配列に格納します。 Python バージョンでは、スパース辞書を使用して巨大な多次元オブジェクト配列の割り当てを回避し、一定係数の速度と引き換えにメモリ管理を大幅に簡素化します。 

## テストケース

 以下のテスト ハーネスは、送信されたコードが次のように保存されることを前提としています。`solution.py`。 問題では任意の最適解が明示的に許可されているため、有効な構築を許可しながら、正確な答えの値をチェックします。```python
# helper: run solution on input string, return output string
import subprocess
import sys

def run(inp: str) -> str:
    result = subprocess.run(
        [sys.executable, "solution.py"],
        input=inp.encode(),
        stdout=subprocess.PIPE,
        check=True,
    )
    return result.stdout.decode().strip()

sample1 = """\
2 0 2 2
8
9
3
4
"""

assert run(sample1) == "DEFEAT", "sample 1"

sample2 = """\
2 8 2 2
2 5 5 10 -10 -1 3 5 9
8 -10 9 9 0 1 -3 1 -1
0 5 -1 5 3 -9 1 10 6
5 -4 8 -2 2 -8 6 3 -3
"""

out = run(sample2).splitlines()
assert int(out[0]) == -21, "sample 2"

sample3 = """\
3 5 2 3
100 75 125 150 175 200
125 100 75 100 125 150
225 200 175 200 225 250
225 200 175 200 225 250
125 100 75 100 125 150
100 75 125 150 175 200
"""

out = run(sample3).splitlines()
assert int(out[0]) == 650, "sample 3"

# Minimum-size case: no problems, matching number must be zero.
case_min = """\
1 0 0 0
7
9
"""

assert run(case_min) == "16", "minimum-size case"

# Boundary case: one edge cannot create a matching of size two.
case_boundary = """\
2 1 2 2
0 0
0 0
0 0
0 0
"""

assert run(case_boundary) == "DEFEAT", "matching upper-bound case"

# All costs are equal, so every feasible construction has the same cost.
case_equal = """\
2 2 1 1
5 5 5
5 5 5
5 5 5
5 5 5
"""

out = run(case_equal).splitlines()
assert int(out[0]) == 20, "all-equal costs"

# Maximum-size instance. With 30 problems and a required matching
# of 30, every one of the 60 vertices must have degree exactly one.
rows = ["0 1" for _ in range(60)]
case_max = "30 30 30 30\n" + "\n".join(rows) + "\n"

out = run(case_max).splitlines()
assert int(out[0]) == 60, "maximum-size case"
assert len(out) == 31, "maximum-size edge count"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | サンプル 1 |`DEFEAT`| 問題はなく、一致の下限も不可能です。 
| サンプル 2 |`-21`| 最適なコストと重要な平行エッジ構造 |
 | サンプル 3 |`650`| 2番目の正式に実現可能な建設 |
 | (n=1,m=0,l=r=0) |`16`| 最小サイズとゼロ一致の場合 |
 | (n=2,m=1,l=r=2) |`DEFEAT`| 上限のマッチングとオフバイワンの処理 |
 | (n=2,m=2,l=r=1)、全コスト (5) |`20`| すべて等しいコストと意図的に不完全なマッチング |
 | (n=m=30,l=r=30) |`60`| 最大サイズの境界と完全一致 |

 ## 特殊なケース

 問題がゼロの場合、```
1 0 0 0
7
9
```DP は ((0,0,0,0)) で開始し、終了します。 分散するエッジがないため、遷移の選択肢はありません。 左側のコストは (7)、右側のコストは (9)、一致するサイズは (0) で、要求された間隔に属します。 答えは(16)です。 

不可能な下限については、```
2 1 2 2
0 0
0 0
0 0
0 0
```一致するエンドポイントは 1 つの完全なエッジを消費し、使用可能なエッジは 1 つだけであるため、DP は (k=2) で最終状態を生成できません。 最終的な列挙には候補州がないため、`DEFEAT`が生産されます。 

繰り返されるエッジについては、次の点を考慮してください。```
2 2 1 1
5 5 5
5 5 5
5 5 5
5 5 5
```両方の問題を同じペアに割り当てることができます。 左右の次数の両方に、次数 2 の頂点が 1 つと次数 0 の頂点が 1 つ含まれます。 グラフには 2 つの平行なエッジがありますが、一致する可能性のあるペアは 1 つだけであるため、最大一致は (1) です。 DP は、ペアの繰り返しを禁止するのではなく、度数を記録するため、これを許可します。 合計コストは (20) です。 

最大一致境界については、```
30 30 30 30
```すべての給与行が次と等しい`0 1`、サイズ (30) のマッチングが必要です。 問題は (30) 個しかないため、すべての問題はマッチングに属している必要があるため、(60) 個の頂点のすべての次数は正確に 1 になります。 したがって、給与総額は (60) となります。 DP は (k=30) および (m-k=0) に達するため、一致しないカウンターは両方とも 0 になります。 これにより、残留エッジが作成されない正確な境界が実行されます。 

給与がマイナスの場合、DP は学位が高ければ高いほど高額であると決して想定してはなりません。 2 番目の公式サンプルでは、​​一部のエントリが負であり、最適化では意図的に最初の 2 つの左頂点に次数 4 を割り当て、右 1 つの頂点に次数 5 を割り当てます。 各メンバーに対して最も安価な次数を個別に選択することに基づく貪欲な戦略は失敗します。これは、次数の各辺の合計が (m) になる必要があり、必要なマッチングとカバー構造を同時にサポートする必要があるためです。 DP は互換性のある学位の選択肢をすべて考慮し、総コストを最小限に抑えます。 このサンプルの正式な答えは (-21) です。 

主な概念のエッジ ケースは、最大一致が両側の非ゼロ度の頂点の数よりも小さいグラフです。 アクティブな頂点を数えるだけでは、一致する頂点の数を決定するのに十分ではありません。 DP のカバー部分は、まさにこの状況を処理します。つまり、(k) 個の頂点のみでカバーされる構造にいくつかのアクティブな頂点を強制的に入れることができ、最大マッチングが (k) に制限されます。 角度だけを追跡したり、ゼロ以外の頂点の数だけを追跡すると、重要な情報が失われるのはこのためです。 

最終的な構築では、両方の端点でカバーされるエッジも処理します。 このようなエッジは 2 つのカバー入射に寄与するため、(x_4+y_4) は厳密には (m-k) より大きくなる可能性があります。 この構築では、最初にこれらの二重カバーされたエッジを必要な数だけ正確に作成し、次に 1 つのカバーの終点を持つエッジを使用して残りの次数をすべて分散します。 これにより、最終次数シーケンスが給与 DP と頂点カバー条件を同時に満たすようになります。 

同様の問題に取り組むための中心となる考え方は、最初に正確なグラフについて考えるのをやめるということです。 給与は度数を気にしますが、マッチング制約はマッチングと頂点カバーを組み合わせることで証明できます。 これら 2 つの構造が小さなカウンターによってエンコードされると、グラフ自体は後で再構築できます。
