---
title: "CF 102348I - ラジオ局"
description: "私たちには (p) 個のラジオ局があります。 局 (i) を選択することは、その局と契約を結ぶことを意味します。これは、選択された信号電力 (f) がその区間 ([li,ri]) 内にある場合にのみ可能です。 固定 (f) の場合、その間隔外のステーションは強制的に選択されないままになります。"
date: "2026-08-13T01:06:06+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102348
codeforces_index: "I"
codeforces_contest_name: "ICPC 2019-2020 NERC (NEERC), Southern and Volga Russia Qualifier"
rating: 0
weight: 102348
solve_time_s: 362
verified: true
draft: false
---

[CF 102348I - ラジオ局](https://codeforces.com/problemset/problem/102348/I)

 **評価:** -
 **タグ:** -
 **解決時間:** 6 分 2 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 私たちには (p) 個のラジオ局があります。 局 (i) を選択することは、その局と契約を結ぶことを意味します。これは、選択された信号電力 (f) がその区間 ([l_i,r_i]) 内にある場合にのみ可能です。 固定 (f) の場合、その間隔外のステーションは強制的に選択されないままになります。 

すべての苦情にはペア ((x_i,y_i)) が与えられ、これら 2 つのステーションのうち少なくとも 1 つを選択する必要があります。 すべての干渉ペア ((u_i,v_i)) では、これら 2 つのステーションのうち最大 1 つを選択する必要があります。 タスクは、(f) とこれらすべての条件を満たすステーションのセットの両方を選択するか、そのような選択肢が存在しないことを報告することです。 公式の制約では、4 つの主要パラメータすべてが (4\cdot10^5) に達することが許可されており、制限時間は 7 秒、メモリは 256 MB です。 

局選択部分だけでも、これはすでに 2-SAT インスタンスです。 (S_i) はステーション (i) が選択されたことを意味するとします。 苦情は (S_x\lor S_y) になり、干渉ペアは (\lnot S_u\lor\lnot S_v) になります。 難しいのは、(f) が事前には分からず、(f) を選択すると多くのステーションが強制的に false になる可能性があることです。 

直接実装では、(f) ごとに試行し、対応する 2-SAT インスタンスを構築し、毎回 SCC を実行できます。 1 つのチェックのコストは (O(p+n+m)) なので、すべて (M) の選択肢のコストは (O(M(p+n+m))) になります。 最大制約では、これは約 (4\cdot10^5\cdot1.2\cdot10^6=4.8\cdot10^{11}) 個の入力スケール演算となり、制限時間の許容範囲をはるかに超えています。 

実装を簡単に壊す境界ケースがいくつかあります。 間隔が ([l,r]) であるステーションは両方のエンドポイントで使用できるため、条件を (l<f<r) に置き換えると、有効な応答が自動的に失われます。 例えば、```
2 3 2 2
1 2
2 3
1 1
1 2
2 2
1 2
2 3
```は有効な答え (f=2) を持ち、ステーション 2 のみが選択されています。 ステーション 2 はその上部のエンドポイントで正確に使用でき、これを選択すると両方の不満が満たされます。 

別の失敗は、さまざまな苦情が独立した権限でしか処理できない場合に発生します。 例えば、```
2 4 2 2
1 2
3 4
1 1
1 1
2 2
2 2
1 2
3 4
```答えがあります`-1`。 (f=1) ではステーション 1 とステーション 2 しか選択できないため、2 番目の不満は満たされません。 (f=2) ではステーション 3 とステーション 4 のみが使用可能であるため、最初の不満は満たされません。 (f) を表現せずに、ステーション選択式が満たされるかどうかだけをチェックすると、このインスタンスが誤って受け入れられてしまいます。 

3 番目の微妙なケースは、信号パワー自体を 1 から (M) までの有効な整数で表す必要があることです。 構造が人為的な値 0 または (M+1) を許可する場合、対応する信号電力を持たないブール代入が生成される可能性があります。 以下のしきい値の構築は、それを明示的に防止します。 

## アプローチ

 ブルートフォースソリューションは概念的には単純です。 (f) の値を修正し、間隔に (f) が含まれないすべてのステーションを強制選択解除としてマークし、苦情条項と干渉条項を追加して、強接続コンポーネントを使用して結果の 2-SAT インスタンスを解決します。 式が満たされる場合、SCC 割り当てにより選択されたステーションが得られます。 すべての可能な答えはいずれか 1 つを使用するため、すべての (M) べき乗を試すことは正しいです。 

問題は SCC 計算の繰り返しです。 単一の 2-SAT チェックは線形ですが、(M) を乗算すると、最悪の場合、およそ (4.8\cdot10^{11}) の演算が生成されます。 大きい (M) は、特にこのアプローチを除外するためにあります。 

重要な観察は、実際には (f) を列挙する必要がないということです。 代わりに、「(f) は少なくとも (t) である」というステートメントを別のブール変数として表します。 呼んでください(T_t)。 (T_{t+1}\rightarrow T_t) を追加するので、これらの変数の真理値は真の値のプレフィックスを形成する必要があります。 また、(T_1) を true に、(T_{M+1}) を false に強制します。 したがって、満足のいく代入には必ず 1 つのカットオフが含まれており、そのカットオフは (f) の正当な値です。 

これにより、ステーション間隔が通常の 2-SAT に影響するようになります。 ステーション (i) が選択されている場合は (f\ge l_i)、つまり (S_i\rightarrow T_{l_i}) になります。 また (f\le r_i) は (f<r_i+1) と同等なので、(S_i\rightarrow\lnot T_{r_i+1}) となります。 どちらもブール リテラル間の通常の含意です。 

したがって、問題全体は、(p+M+1) 個のブール変数を持つ 1 つの 2-SAT インスタンスになります。 この構造には (O(n+p+m+M)) 節しかなく、1 回の SCC 計算で解くことができます。 これは、この問題で一般的に使用されるのと同じプレフィックス最適化の観点です。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | (O(M(p+n+m))) | (O(p+n+m)) | 遅すぎる |
 | 最適 | (O(p+n+m+M)) | (O(p+n+m+M)) | 承認済み |

 ## アルゴリズムのチュートリアル

1. すべてのステーションに対してブール変数 (S_i) を作成します。 true 値はステーション (i) が選択されていることを意味し、false 値はステーション (i) が選択されていないことを意味します。 すべてのブール変数を 2 つの含意グラフ頂点 (リテラル用とその否定用) で表します。 
2. すべての苦情 ((x,y)) を条項 (S_x\or S_y) に変換します。 含意グラフでは、これにより 2 つのエッジ (\lnot S_x\rightarrow S_y) と (\lnot S_y\rightarrow S_x) が得られます。 これらのエッジは、苦情が強制的に保持される可能性がある 2 つの方法を正確に表しています。 
3. すべての干渉ペア ((u,v)) を節 (\lnot S_u\lor\lnot S_v) に変換します。 その含意エッジは (S_u\rightarrow\lnot S_v) と (S_v\rightarrow\lnot S_u) です。 したがって、いずれかのエンドポイントを選択すると、もう一方のエンドポイントの選択が強制的に解除されます。 
4. 1 から (M+1) までのすべての (t) に対してブール変数 (T_t) を導入します。ここでの意図された意味は (T_t=(f\ge t)) です。 1 から (M) までの (t) ごとに句 (\lnot T_{t+1}\lor T_t) を追加します。 これらの節は、しきい値変数を強制的に単調にします。 
5. (T_1) を true に、(T_{M+1}) を false に強制します。 しきい値変数は単調であるため、真の値と偽の値の間には境界が 1 つだけ存在します。 最大の真のしきい値が (f) である場合、(T_t) は (t\le f) に対して正確に真となるため、このブール代入は整数の信号パワー (f) を表します。 
6. すべてのステーション (i) に、(S_i\rightarrow T_{l_i}) を追加します。 ステーション (i) が選択された場合、(l_i) のしきい値は true、つまり (f\ge l_i) である必要があります。 
7. すべてのステーション (i) に、(S_i\rightarrow\lnot T_{r_i+1}) を追加します。 (T_{r_i+1}) は (f\ge r_i+1) を意味するので、その否定は (f\le r_i) を意味します。 前の意味と合わせて、ステーション (i) を選択すると (l_i\le f\le r_i) が強制されます。 
8. これらすべての節から含意グラフを構築し、Tarjan のアルゴリズムを使用してその強結合成分を計算します。 ある変数とその否定が同じ SCC に属している場合、2-SAT インスタンスは正確には不可能です。 これをステーション変数としきい値変数の両方についてチェックします。 
9. 式が満足できる場合は、SCC 順序を使用して各変数を割り当てます。 Tarjan のコンポーネント番号付けでは、コンポーネントは逆トポロジー順序で生成されるため、コンポーネント番号の小さいリテラルが選択された真理値になります。 ステーションの場合、真のリテラルのコンポーネント番号が偽のリテラルよりも小さい場合、(S_i) は真になります。 
10. (T_1,\ldots,T_M) をスキャンし、真のリテラルが選択されている最大のしきい値を取得します。 単調性により、これらの真のしきい値がプレフィックスを形成することが保証されるため、この最大のインデックスがまさに必要な (f) になります。 (S_i) 変数が true であるすべてのステーションを出力します。 

この構築の背後にある不変条件は、しきい値変数のすべての満足のいく割り当てが正確に 1 つの正当な整数 (f) に対応する一方、選択されたすべてのステーションにはその間隔全体にその (f) が含まれるように強制されるということです。 逆に、(f) とステーションの有効な選択は、構築されたすべての節を満たすすべての (S_i) と (T_t) の真理値に変換できます。 したがって、構築された 2-SAT インスタンスは、元の問題に答えがある場合に正確に満たされます。 

## Python ソリューション```python
import sys
from array import array

input = sys.stdin.readline

def solve():
    input = sys.stdin.readline

    n, p, M, m = map(int, input().split())

    # Store complaints temporarily. Conflicts can be processed later.
    complaints = array('i')
    for _ in range(n):
        x, y = map(int, input().split())
        complaints.append(x - 1)
        complaints.append(y - 1)

    left = array('i')
    right = array('i')
    for _ in range(p):
        l, r = map(int, input().split())
        left.append(l)
        right.append(r)

    # Variables:
    #   0 .. p-1          : station variables
    #   p .. p+M          : threshold variables T_1 .. T_{M+1}
    variables = p + M + 1
    vertices = variables * 2

    # Store clauses as pairs of literal IDs.
    # Literal 2*v is v=True, literal 2*v+1 is v=False.
    clauses = array('i')

    # Complaint: S_x OR S_y
    for i in range(0, 2 * n, 2):
        x = complaints[i]
        y = complaints[i + 1]
        clauses.append(2 * x)
        clauses.append(2 * y)

    del complaints

    # Station interval:
    # S_i -> T_l
    # S_i -> !T_{r+1}
    for i in range(p):
        station_true = 2 * i

        tl_var = p + left[i] - 1
        tr1_var = p + right[i]

        clauses.append(station_true ^ 1)
        clauses.append(2 * tl_var)

        clauses.append(station_true ^ 1)
        clauses.append(2 * tr1_var + 1)

    del left
    del right

    # Conflict: !S_u OR !S_v
    for _ in range(m):
        u, v = map(int, input().split())
        u -= 1
        v -= 1
        clauses.append(2 * u + 1)
        clauses.append(2 * v + 1)

    # T_{t+1} -> T_t
    # Equivalent to !T_{t+1} OR T_t.
    for t in range(1, M + 1):
        cur = p + t - 1
        nxt = cur + 1
        clauses.append(2 * nxt + 1)
        clauses.append(2 * cur)

    # T_1 must be true.
    t1 = p
    clauses.append(2 * t1)

    # T_{M+1} must be false.
    tm1 = p + M
    clauses.append(2 * tm1 + 1)

    # Build adjacency in CSR form.
    # For clause (a OR b):
    #   !a -> b
    #   !b -> a
    degree = array('i', [0]) * vertices

    clen = len(clauses)
    for i in range(0, clen, 2):
        a = clauses[i]
        b = clauses[i + 1]
        degree[a ^ 1] += 1
        degree[b ^ 1] += 1

    start = array('i', [0]) * (vertices + 1)
    total = 0
    for i in range(vertices):
        start[i] = total
        total += degree[i]
    start[vertices] = total

    edge = array('i', [0]) * total
    pos = array('i', start)

    for i in range(0, clen, 2):
        a = clauses[i]
        b = clauses[i + 1]

        u = a ^ 1
        idx = pos[u]
        edge[idx] = b
        pos[u] = idx + 1

        u = b ^ 1
        idx = pos[u]
        edge[idx] = a
        pos[u] = idx + 1

    del clauses
    del degree
    del pos

    # Iterative Tarjan SCC.
    #
    # Recursive Tarjan is unsafe here because the graph can have more
    # than 1.6 million vertices.
    dfn = array('i', [0]) * vertices
    low = array('i', [0]) * vertices
    comp = array('i', [0]) * vertices
    on_stack = bytearray(vertices)

    scc_stack = array('i')
    dfs_vertices = array('i')
    dfs_edges = array('i')

    timer = 0
    component_count = 0

    for root in range(vertices):
        if dfn[root]:
            continue

        timer += 1
        dfn[root] = timer
        low[root] = timer
        on_stack[root] = 1
        scc_stack.append(root)

        dfs_vertices.append(root)
        dfs_edges.append(start[root])

        while dfs_vertices:
            v = dfs_vertices[-1]
            e = dfs_edges[-1]

            if e < start[v + 1]:
                w = edge[e]
                dfs_edges[-1] = e + 1

                if dfn[w] == 0:
                    timer += 1
                    dfn[w] = timer
                    low[w] = timer
                    on_stack[w] = 1
                    scc_stack.append(w)

                    dfs_vertices.append(w)
                    dfs_edges.append(start[w])
                elif on_stack[w]:
                    dw = dfn[w]
                    if dw < low[v]:
                        low[v] = dw
            else:
                dfs_vertices.pop()
                dfs_edges.pop()

                if dfs_vertices:
                    parent = dfs_vertices[-1]
                    lv = low[v]
                    if lv < low[parent]:
                        low[parent] = lv

                if low[v] == dfn[v]:
                    component_count += 1
                    while True:
                        w = scc_stack.pop()
                        on_stack[w] = 0
                        comp[w] = component_count
                        if w == v:
                            break

    del dfn
    del low
    del on_stack
    del scc_stack
    del dfs_vertices
    del dfs_edges

    # Every variable must be different from its negation.
    for v in range(variables):
        if comp[2 * v] == comp[2 * v + 1]:
            print(-1)
            return

    # Tarjan numbers SCCs in reverse topological order.
    # Smaller component number means the literal is chosen.
    selected = []
    for i in range(p):
        if comp[2 * i] < comp[2 * i + 1]:
            selected.append(i + 1)

    # Recover f from the threshold variables.
    f = 1
    for t in range(1, M + 1):
        var = p + t - 1
        if comp[2 * var] < comp[2 * var + 1]:
            f = t

    print(len(selected), f)
    print(*selected)

if __name__ == "__main__":
    solve()
```実装の最初の部分では、苦情と間隔を読み取ります。 ステーション間隔データはその後に到着するのに対し、干渉ペアは間隔後に到着し、文節に直接変換できるため、苦情は一時的に保存する必要があります。 

各ブール変数は、2 つの連続するリテラル ID を占有します。 偶数の ID は真のリテラルを表し、奇数の ID は偽のリテラルを表すため、否定は単純に`literal ^ 1`。 これにより、含意の構造がコンパクトになり、リテラル用に別個のオブジェクトを保存する必要がなくなります。 

このコードは、すべての 2-SAT 句を一時的に`array('i')`。 通常の Python リストは、その整数要素が Python オブジェクトであるため、かなり多くのメモリを消費します。 同じ理由で使用する動機も`array('i')`グラフと SCC 配列の場合。 

含意グラフは、Python リストのリストとしてではなく、CSR 形式で保存されます。 の`start[v]`そして`start[v+1]`範囲には頂点の外向きのエッジが正確に含まれます`v`。 これにより、何百万もの Python リスト オブジェクトが回避され、すべてのグラフ インデックスが 4 バイトに保たれます。 

SCC 計算は反復的な Tarjan です。 再帰的 DFS は、グラフ頂点の数に比例する深​​さに達することができ、ここでは 160 万を超える可能性があります。 2 つの明示的な DFS スタックは、再帰呼び出しで保持されるのと同じ状態、つまり現在の頂点とまだ検査する必要がある次の出力エッジを保持します。 

間隔エンコードでは意図的に (r_i+1) が使用されます。 上の条件は (f\le r_i) で、正確には (\lnot(f\ge r_i+1)) です。 (r_i) は (M) と等しくなる可能性があるため、追加のしきい値 (T_{M+1}) が必要です。 これは強制的に false になるため、(r_i=M) を持つステーションは正しい無制限の上限を受け取ります。 

Python では整数がオーバーフローすることはなく、各グラフのインデックスは最大でも約 160 万であり、実装で使用される 4 バイトの配列内にも快適に収まります。 

## 実用的な例

 サンプル 1 では、(f=3) およびステーション 1 と 3 が選択された有効な割り当てを検討します。 しきい値変数は、(f=3) を (T_1=T_2=T_3=\text{true}) および (T_4=\text{false}) として記述します。 

| ステージ | 状態 |
 | --- | --- |
 | 信号電力 | (f=3) |
 | (T_1,T_2,T_3,T_4) | 真、真、真、偽 |
 | 選択されたステーション | 1、3 |
 | ステーション 1 間隔 | ([1,4])、有効 |
 | ステーション 3 間隔 | ([3,4])、有効 |
 | 苦情 ((1,3)) | ステーション 1 または 3 で満足 |
 | 苦情 ((2,3)) | ステーション 3 に満足 |
 | 競合 ((1,4)) | ステーション 4 は選択されていません |
 | 紛争 ((3,4)) | ステーション 4 は選択されていません |
 | 結果 | 有効 |

 この問題では任意の回答が可能であるため、SCC 割り当てでは別の満足のいく割り当てが選択される可能性があります。 重要なのは、選択されたしきい値変数がプレフィックスを形成し、選択されたすべてのステーションが結果のカットオフと互換性があることです。 

サンプル 2 では、ステーション 1 と 2 はパワー 1 と 2 でのみ使用でき、ステーション 3 と 4 はパワー 3 と 4 でのみ使用できます。 

| 信号電力 | 利用可能な駅 | 最初の苦情 | 2番目の苦情 | 紛争 | 結果 |
 | --- | --- | --- | --- | --- | --- |
 | 1 | 1、2 | 1 または 2 が必要です | 2 または 4 が必要なので、2 | 1と2を両方選択することはできません | 不可能 |
 | 2 | 1、2 | 1 または 2 が必要です | 2 または 4 が必要なので、2 | 1と2を両方選択することはできません | 不可能 |
 | 3 | 3、4 | 1 または 3 が必要なので、3 | 2 または 4 が必要なので、4 | 3 と 4 を両方選択することはできません | 不可能 |
 | 4 | 3、4 | 1 または 3 が必要なので、3 | 2 または 4 が必要なので、4 | 3 と 4 を両方選択することはできません | 不可能 |

 しきい値の定式化は、式を 4 回再構築するのではなく、1 つの 2-SAT インスタンスで 4 つの可能性すべてを表します。 SCC の計算により、必要な選択と干渉条項の間の矛盾が検出されるため、プログラムは次のように出力します。`-1`。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O(n+p+m+M)) | (O(n+p+m+M)) 個の節と頂点があり、Tarjan はすべての頂点と含意エッジを 1 回検査します。 |
 | スペース | (O(n+p+m+M)) | 一時句、CSR グラフ、SCC 配列、および入力間隔はすべて、入力サイズにおいて線形です。 |

 最大制約では、最大約 (8\cdot10^5) 個のブール変数と約 (4\cdot10^6) 個の含意エッジが存在します。 この実装では、パックされた 4 バイトの整数配列と反復 SCC トラバーサルが使用され、メモリが 256 MB の制限を大幅に下回ります。 線形構築と SCC パスは、数百万のグラフ操作による総当たり (4.8\cdot10^{11}) 規模の作業を置き換えます。 

## テストケース

 出力は一意ではないため、正確な文字列比較はこれらのテストには適切ではありません。 以下のテスト ハーネスは、返されたソリューションが意味的に有効であることを確認します。 最大サイズのテストでは、数十万行を完全に解析して個別に検証すると、テスト ハーネス自体が不必要に高価になるため、ソルバーが解決策を見つけたかどうかのみをチェックします。```python
# Assume the submitted solution is saved as solution.py.
# Its solve() function reads stdin and writes stdout.

import sys
import io
from solution import solve

def run(inp: str) -> str:
    old_stdin = sys.stdin
    old_stdout = sys.stdout
    try:
        sys.stdin = io.StringIO(inp)
        sys.stdout = io.StringIO()
        solve()
        return sys.stdout.getvalue()
    finally:
        sys.stdin = old_stdin
        sys.stdout = old_stdout

def validate(inp: str, out: str, possible: bool):
    data = list(map(int, inp.split()))
    it = iter(data)

    n = next(it)
    p = next(it)
    M = next(it)
    m = next(it)

    complaints = []
    for _ in range(n):
        complaints.append((next(it), next(it)))

    intervals = []
    for _ in range(p):
        intervals.append((next(it), next(it)))

    conflicts = []
    for _ in range(m):
        conflicts.append((next(it), next(it)))

    out = out.strip()

    if not possible:
        assert out == "-1"
        return

    tokens = list(map(int, out.split()))
    assert len(tokens) >= 2

    k, f = tokens[0], tokens[1]
    chosen = tokens[2:]

    assert 1 <= f <= M
    assert k == len(chosen)
    assert len(set(chosen)) == k
    assert all(1 <= x <= p for x in chosen)

    chosen_set = set(chosen)

    for x, y in complaints:
        assert x in chosen_set or y in chosen_set

    for u, v in conflicts:
        assert not (u in chosen_set and v in chosen_set)

    for x in chosen:
        l, r = intervals[x - 1]
        assert l <= f <= r

# Provided sample 1
sample1 = """\
2 4 4 2
1 3
2 3
1 4
1 2
3 4
1 4
1 2
3 4
"""
validate(sample1, run(sample1), True)

# Provided sample 2
sample2 = """\
2 4 4 2
1 3
2 4
1 2
1 2
3 4
3 4
1 2
3 4
"""
validate(sample2, run(sample2), False)

# Minimum feasible size under the distinct-pair condition.
case_min = """\
2 3 2 2
1 2
2 3
1 1
1 2
2 2
1 2
2 3
"""
validate(case_min, run(case_min), True)

# All intervals are equal, so the signal power is unrestricted inside [1, 2].
case_equal = """\
2 4 2 2
1 2
3 4
1 2
1 2
1 2
1 2
1 2
3 4
"""
validate(case_equal, run(case_equal), True)

# The two complaints require disjoint signal ranges.
case_impossible = """\
2 4 2 2
1 2
3 4
1 1
1 1
2 2
2 2
1 2
3 4
"""
validate(case_impossible, run(case_impossible), False)

# Endpoint test: station 2 is usable at both l=1 and r=2,
# and f=2 gives a valid solution using only station 2.
case_endpoint = """\
2 3 2 2
1 2
2 3
1 1
1 2
2 2
1 2
2 3
"""
validate(case_endpoint, run(case_endpoint), True)

# Maximum-size stress test.
# An even cycle is both the complaint graph and the conflict graph.
# Every interval is [1, M], so an alternating selection is valid.
N = 400000
P = 400000
MM = 400000
E = 400000

parts = [f"{N} {P} {MM} {E}\n"]

for i in range(1, N):
    parts.append(f"{i} {i + 1}\n")
parts.append(f"1 {N}\n")

parts.extend(["1 400000\n"] * P)

for i in range(1, N):
    parts.append(f"{i} {i + 1}\n")
parts.append(f"1 {N}\n")

maximum_case = "".join(parts)
assert not run(maximum_case).startswith("-1")
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | サンプル 1 | 有効な割り当て | 異なるステーション間隔を持つ基本的な満足可能なインスタンス |
 | サンプル 2 |`-1`| 苦情、対立、およびばらばらの周波数範囲間の相互作用 |
 |`case_min`| 有効な割り当て | 個別ペア要件の下での実際に有効な最小の構成 |
 |`case_equal`| 有効な割り当て | すべての間隔は等しく、可能な複数の信号パワー |
 |`case_impossible`|`-1`| 相互に分離された周波数範囲を必要とする苦情 |
 |`case_endpoint`| (f=2) が可能な有効な代入 | 範囲の下限と上限を含む境界 |
 |`maximum_case`| 有効な割り当て | 4 つの主要な入力パラメータすべての最大値とメモリ負荷 |

 ## 特殊なケース

 包括的なエンドポイントの場合は、上位の含意 (T_{r_i+1} ではなく S_i\rightarrow\l) によって処理されます。 で`case_endpoint`、ステーション 2 には間隔 ([1,2]) があります。 SCC 割り当てで (T_2=\text{true}) および (T_3=\text{false}) を選択すると、必要な不等式が (f\ge1) および (f<3) となり、(f\le2) が得られるため、ステーション 2 が許可されます。 その結果、アルゴリズムは (f=2) でステーション 2 を返すことができます。 

周波数が素である場合は、しきい値変数が個別にテストされるのではなくグローバルであるため、処理されます。 で`case_impossible`、第 1 の準拠勢力 (f=1) からステーションを選択し、第 2 の準拠勢力 (f=2) からステーションを選択します。 しきい値句は、これらの要件を同じブール システムの一部にし、結果として生じる矛盾により、変数とその否定が同じ SCC に配置されます。 アルゴリズムは戻ります`-1`。 

許容電力範囲の境界は、強制変数 (T_1=\text{true}) および (T_{M+1}=\text{false}) によって保護されます。 (f=0) に対応する仮想割り当ての場合、(T_1) は false でなければならず、最初の単位節と矛盾します。 (f=M+1) に対応する代入の場合、(T_{M+1}) は true でなければならず、2 番目の単位節と矛盾します。 したがって、残っているすべてのしきい値割り当ては、何らかの (f\in[1,M]) に対応します。 

最後に、アルゴリズムは干渉関係が推移的であることを前提としていません。 リストされた各ペアは、「両方とも選択されていない」句を 1 つだけ提供します。 ステーション 1 がステーション 2 と競合し、ステーション 2 がステーション 3 と競合する場合、この構築ではステーション 1 と 3 の間に競合は発生しません。これは入力内のグラフと一致し、干渉ペアが誤って接続コンポーネントとして扱われるというよくある間違いを防ぎます。
