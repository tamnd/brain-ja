---
title: "CF 102203C - \u0424\u0430\u0431\u0440\u0438\u043a\u0430"
description: "工場は木です。 すべての部屋は頂点であり、すべての廊下はエッジであり、各部屋のペア間には正確に (n-1) 個の廊下と正確に 1 つのパスがあるため、2 つの部屋間のパスは一意です。"
date: "2026-08-18T11:23:23+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102203
codeforces_index: "C"
codeforces_contest_name: "\u0427\u0435\u0442\u0432\u0435\u0440\u0442\u0430\u044f \u041b\u0438\u043f\u0435\u0446\u043a\u0430\u044f \u043a\u043e\u043c\u0430\u043d\u0434\u043d\u0430\u044f \u043e\u043b\u0438\u043c\u043f\u0438\u0430\u0434\u0430 \u0448\u043a\u043e\u043b\u044c\u043d\u0438\u043a\u043e\u0432 \u043f\u043e \u043f\u0440\u043e\u0433\u0440\u0430\u043c\u043c\u0438\u0440\u043e\u0432\u0430\u043d\u0438\u044e (8-11 \u043a\u043b\u0430\u0441\u0441\u044b)"
rating: 0
weight: 102203
solve_time_s: 153
verified: true
draft: false
---

[CF 102203C - \u0424\u0430\u0431\u0440\u0438\u043a\u0430](https://codeforces.com/problemset/problem/102203/C)

 **評価:** -
 **タグ:** -
 **解決時間:** 2 分 33 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 工場は木です。 すべての部屋は頂点であり、すべての廊下はエッジであり、各部屋のペア間には正確に (n-1) 個の廊下と正確に 1 つのパスがあるため、2 つの部屋間のパスは一意です。 

すべてのリクエスト ((s_i,f_i)) について、すべての廊下が一方通行になった後、人々は (s_i) から (f_i) まで歩くことができなければなりません。 基礎となるグラフはツリーであるため、(s_i) から (f_i) への可能なルートは 1 つだけです。 その結果、そのルート上のすべてのエッジには強制的な方向が設定されます。 タスクは、これらすべての強制方向が相互に一貫しているかどうかを判断し、相互に一貫している場合は、すべての要求を満たすすべてのコリドーの 1 つの方向を出力することです。 

制約は、部屋の数とリクエストの数の両方について (2\cdot10^5) に達します。 (n-1) 個のエッジを持つ木には (2^{n-1}) 個の方向があるため、考えられるすべての方向を検査する解決策は直ちに不可能です。 要求されたすべてのパスに沿って明示的に歩くアプローチでも、(O(nm)) に達する可能性があります。これは、最大サイズで約 (4\cdot10^{10}) 個のエッジ アクセスに相当します。 意図したソリューションでは、ツリーとすべてのリクエストをほぼ直線的に処理する必要があります。 

実装を簡単に破壊してしまういくつかの特殊なケースがあります。 1 つ目は、エンドポイントが等しいリクエストです。 例えば、```
1 1
1 1
```には廊下がまったくなく、明らかに満足できるため、答えは次のようになります。`YES`。 2 つのエンドポイントが別個であると想定するパス差の実装では、誤って偽の制約が導入される可能性があります。 

2 つ目は、同じエッジで反対方向を要求する 2 つのリクエストです。```
2 2
1 2
1 2
2 1
```正しい答えは、`NO`。 どちらのリクエストもエッジのみを使用しますが、一方は (1\to2) を必要とし、もう一方は (2\to1) を必要とします。 リクエストを個別にチェックし、最初にエッジが見つかったときにエッジを方向付けると、このケースを黙って受け入れることができます。 

3 つ目は、複数の先祖を経由するリクエストです。 考慮する```
3 1
1 2
2 3
3 1
```有効な方向は (3\to2\to1) のみです。 選択したルートからすべてのエッジを単純にその子に向けるアプローチでは (1\to2\to3) が生成され、ツリー構造は満たされますが、要求には違反します。 

## アプローチ

 最も直接的なアプローチは、(n-1) 個のエッジのすべての方向を列挙することです。 方向ごとに、すべてのリクエストを検査し、その一意のパス上のすべてのエッジが開始から終了までを指しているかどうかを確認します。 これは正しいです。考えられるすべての答えが明示的に考慮されているからです。 単純なパス トラバーサルでは、最悪の場合、1 つの方向に (O(mn)) 個の作業が必要となるため、合計は (O(2^{n-1}mn)) になります。 (n) が大きな値に達する前であっても、これは役に立たなくなります。 

より良い方向は、完全な方向について考えるのをやめ、代わりに個々のエッジが何をする必要があるかを考えることです。 部屋(1)で木を根付かせます。 すべての非ルート頂点 (v) とその親 (p(v)) の間にエッジがあります。 リクエストは 2 つの方法のうちの 1 つでこのエッジを通過できます。 (v) のサブツリーから親に向かう場合、エッジは (v\to p(v)) でなければなりません。 親側からサブツリーに入る場合、エッジは (p(v)\to v) でなければなりません。 

1 つのリクエスト (s\to f) について、(l) を (s) と (f) の最下位共通祖先とします。 パスは (s) から (l) までの上向き部分と、(l) から (f) までの下向き部分に分かれます。 これが重要な構造観察です。 すべての上向きの要件を 1 つのツリー差分配列で記録し、すべての下向きの要件を別の配列で記録できます。 

上向きの部分 (s\to l) は、(s) で (1) を加算し、(l) で (1) を減算します。 子から親に向かって値を合計した後、エッジ ((p(v),v)) は、リクエストが (v\to p(v)) を必要とするときに正確に正の上向きカウントを受け取ります。 

下向きの部分 (l\to f) については、(f) で (1) を加算し、(l) で (1) を減算します。 同じボトムアップ累積により、リクエストが (p(v)\to v) を必要とするときに正確に正の下向きカウントが得られます。 

したがって、両方のカウントが正の場合、正確にはエッジは不可能です。 上向きのカウントのみが正の場合は、エッジを上向きにします。 下向きのカウントのみが正の場合は、下向きにします。 どちらのカウントも正でない場合、エッジは制限されず、任意の方向に向けることができます。 

残りの問題は、すべての LCA を効率的に見つけることです。 すべてのリクエストは処理が開始される前にわかっているため、Tarjan のオフライン LCA アルゴリズムを使用できます。 DSU はすでに完了したサブツリーを表しますが、ツリーはポストオーダーで処理されます。 すべての LCA リクエストは、1 つのエンドポイントが処理され、もう 1 つのエンドポイントがすでに処理されているときに応答されます。 パスの圧縮とランクによるユニオンを使用すると、これにはほぼ直線的な時間がかかります。 

ブルート フォースは、考えられるすべての向きを明示的にテストするため機能しますが、向きの数が指数関数的なため失敗します。 すべてのリクエストが個々のツリーのエッジに独立した方向を課すだけであるという観察により、ツリーの差異を持つすべてのリクエストを集約できる一方で、オフライン LCA は各パスを分割するために必要な唯一の構造情報を提供します。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | (O(2^{n-1}mn)) | (O(n+m)) | 遅すぎる |
 | 最適 | (O((n+m)\alpha(n))) | (O(n+m)) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 頂点 (1) でツリーをルートし、DFS 次数とともにすべての頂点の親を計算します。 この順序の逆は有効な事後順序であるため、再帰を行わずにすべての子を親の前に処理できます。 
2. すべてのリクエストをクエリ隣接構造に 2 回保存します。 リクエスト (i=(s_i,f_i)) の場合、(f_i) を (s_i) に、(s_i) を (f_i) にアタッチします。 Tarjan のオフライン LCA アルゴリズムは、いずれかのエンドポイントが処理されるときにリクエストに応答するため、この構造が必要です。 
3. Tarjan のオフライン LCA アルゴリズムを実行します。 すべての頂点は最初に独自の DSU セットを形成します。 頂点が終了すると、そのセットはその親にマージされ、DSU 代表はそのセットの現在のツリーの祖先を保存します。 リクエストの両方のエンドポイントが処理されると、`ancestor[find(other)]`はLCAです。 
4. すべてのリクエスト (s\to f) について、(l=\operatorname{LCA}(s,f)) とします。 インクリメント`up[s]`そして減少します`up[l]`。 これはセグメント (s\to l) を表しており、すべての交差したエッジはルートの方向を向いている必要があります。 
5. 同じリクエストの場合、インクリメントします。`down[f]`そして減少します`down[l]`。 これは (l\to f) を表し、すべての交差したエッジはルートから離れる方向を指さなければなりません。 
6. DFS の逆順でツリーをトラバースし、すべての頂点を追加します。`up`そして`down`値を親に渡します。 この累積の後、ルート以外の頂点 (v) ごとに、`up[v]`(v\toparent[v]) を必要とするリクエストをカウントしますが、`down[v]`(parent[v]\to v) を必要とするリクエストをカウントします。 
7. 両方の場合`up[v]`そして`down[v]`正の場合、出力`NO`。 両方向に同じコリドーが必要なため、どの方向でもすべての要求を満たすことはできません。 
8. それ以外の場合は、エッジを (v) と (v) の間の方向に向けます。`parent[v]`利用可能な要件に従って。 上向きの要件は (v\toparent[v]) を与え、下向きの要件は (parent[v]\to v) を与え、制約のないエッジは (parent[v]\to v) を使用できます。 

これが機能する理由: すべてのリクエスト (s\to f) について、その一意のツリー パスは正確に (s\to l) と (l\to f) を連結したものになります。ここで (l) は LCA です。 差分更新により、最初のセグメントのすべてのエッジが上方向を必要とするものとしてマークされ、2 番目のセグメントのすべてのエッジが下方向を必要とするものとしてマークされます。 したがって、蓄積後、各エッジはすべてのリクエストによって要求されるすべての方向を認識します。 両方の方向が発生した場合、インスタンスは不可能です。 多くても 1 つの方向が発生する場合、その方向を選択すると、エッジを使用するすべての要求が満たされます。 すべてのリクエストは完全にそのようなエッジで構成されているため、結果として得られる方向はすべてのリクエストを満たします。 

## Python ソリューション```python
import sys
from array import array

input = sys.stdin.readline

def solve():
    input = sys.stdin.readline
    n, m = map(int, input().split())

    # Compact forward-star representation of the tree.
    head = array('i', [-1]) * n
    to = array('i')
    nxt = array('i')

    eu = array('i')
    ev = array('i')

    for _ in range(n - 1):
        u, v = map(int, input().split())
        u -= 1
        v -= 1

        eid = len(to)
        to.append(v)
        nxt.append(head[u])
        head[u] = eid

        eid += 1
        to.append(u)
        nxt.append(head[v])
        head[v] = eid

        eu.append(u)
        ev.append(v)

    # Store requests.
    qs = array('i')
    qf = array('i')

    # Query adjacency for Tarjan's offline LCA.
    qhead = array('i', [-1]) * n
    qto = array('i')
    qnext = array('i')
    qid = array('i')

    for i in range(m):
        s, f = map(int, input().split())
        s -= 1
        f -= 1

        qs.append(s)
        qf.append(f)

        idx = len(qto)
        qto.append(f)
        qid.append(i)
        qnext.append(qhead[s])
        qhead[s] = idx

        idx = len(qto)
        qto.append(s)
        qid.append(i)
        qnext.append(qhead[f])
        qhead[f] = idx

    # Root the tree at 0 and build a DFS order.
    parent = array('i', [-1]) * n
    parent[0] = 0
    order = []

    stack = [0]
    while stack:
        v = stack.pop()
        order.append(v)

        e = head[v]
        while e != -1:
            u = to[e]
            if u != parent[v]:
                parent[u] = v
                stack.append(u)
            e = nxt[e]

    # Tarjan offline LCA.
    dsu = array('i', range(n))
    rank = array('b', [0]) * n
    ancestor = array('i', range(n))
    visited = bytearray(n)
    lca = array('i', [-1]) * m

    def find(x):
        root = x
        while dsu[root] != root:
            root = dsu[root]

        while dsu[x] != x:
            y = dsu[x]
            dsu[x] = root
            x = y

        return root

    for pos in range(n - 1, -1, -1):
        v = order[pos]

        # All child subtrees have already been merged into v.
        rv = find(v)
        ancestor[rv] = v
        visited[v] = 1

        # Answer queries whose other endpoint is already processed.
        e = qhead[v]
        while e != -1:
            other = qto[e]
            idx = qid[e]

            if visited[other] and lca[idx] == -1:
                lca[idx] = ancestor[find(other)]

            e = qnext[e]

        # Merge v into its parent after processing queries at v.
        if v != 0:
            p = parent[v]
            rv = find(v)
            rp = find(p)

            if rv != rp:
                if rank[rv] < rank[rp]:
                    dsu[rv] = rp
                    ancestor[rp] = p
                elif rank[rv] > rank[rp]:
                    dsu[rp] = rv
                    ancestor[rv] = p
                else:
                    dsu[rp] = rv
                    rank[rv] += 1
                    ancestor[rv] = p

    # We no longer need the query graph or DSU.
    del qhead, qto, qnext, qid
    del dsu, rank, ancestor, visited

    # Difference arrays for upward and downward requirements.
    up = array('i', [0]) * n
    down = array('i', [0]) * n

    for i in range(m):
        s = qs[i]
        f = qf[i]
        l = lca[i]

        up[s] += 1
        up[l] -= 1

        down[f] += 1
        down[l] -= 1

    del qs, qf, lca

    # Accumulate subtree differences from children to parents.
    possible = True

    for pos in range(n - 1, 0, -1):
        v = order[pos]

        if up[v] > 0 and down[v] > 0:
            possible = False
            break

        p = parent[v]
        up[p] += up[v]
        down[p] += down[v]

    if not possible:
        print("NO")
        return

    # Orient every original edge.
    answer = ["YES"]

    for i in range(n - 1):
        a = eu[i]
        b = ev[i]

        if parent[a] == b:
            child = a
            par = b
        else:
            child = b
            par = a

        if up[child] > 0:
            answer.append(f"{child + 1} {par + 1}")
        else:
            # This covers both down[child] > 0 and the unconstrained case.
            answer.append(f"{par + 1} {child + 1}")

    sys.stdout.write("\n".join(answer))

if __name__ == "__main__":
    solve()
```ツリーは、リストの Python リストではなく、コンパクトなフォワードスター表現で保存されます。 (2\cdot10^5) 頂点では、これによりメモリ フットプリントが予測可能に保たれます。 必要な出力ではエッジを任意の順序でリストできるため、元の端点も保持されますが、元のエッジごとに方向を再構築する必要があります。 

最初の DFS は確立のみを行います`parent`そして`order`。 グラフはツリーであることが保証されているため、`u != parent[v]`親に向かって歩いて戻らないようにするには十分です。 ツリーは (2\cdot10^5) 個の頂点のチェーンになる可能性があり、Python の再帰制限を超える可能性があるため、再帰的 DFS は使用されません。 

Tarjan の部分は、別の DSU 親配列を使用します。 これはツリーのものとは意図的に異なります`parent`配列。 ツリーの親は実際のルート ツリーを記述し、DSU の親は既に処理されたサブツリーの一時的なセットを記述します。 の`ancestor`配列は、DSU 代表を、現在そのセットの祖先として機能するツリー頂点に接続します。 

Tarjan ループ内の順序が重要です。 頂点は処理済みとしてマークされ、頂点がその親にマージされる前にそのクエリが回答されます。 マージが最初に行われた場合、LCA が現在の頂点であるクエリは上位の祖先を観察し、誤った応答を受け取る可能性があります。 

2 つの差分配列では、符号付き 32 ビット整数が使用されます。 すべての値はリクエストの数によって制限されるため、この範囲に問題なく収まります。 Python 自体にも任意精度の整数がありますが、コンパクトな配列によりメモリ消費量が大幅に削減されます。 

最終的な方向では、ルート化されたすべてのエッジの子エンドポイントが使用されます。`up[child] > 0`少なくとも 1 つのリクエストでは、エッジが子からその親に向かう必要があることを意味します。 そうでない場合は、下向きのリクエストでエッジが必要か、誰もそれを気にしないため、エッジは安全に親から子を指すことができます。 

## 実用的な例

 サンプル 1 では、頂点 (1) でツリーをルートします。 ルート化されたエッジは (1-2)、(1-4)、(4-3)、および (3-5) です。 LCA と 2 つのパス セグメントは次のとおりです。 

| リクエスト | LCA | 上向きセグメント | 下向きセグメント |
 | --- | --- | --- | --- |
 | (1\to2) | 1 | 空 | (1\to2) |
 | (5\to3) | 3 | (5\to3) | 空 |
 | (5\to4) | 4 | (5\to3\to4) | 空 |
 | (1\to4) | 1 | 空 | (1\to4) |
 | (3\to4) | 4 | 空 | ルート分割に対して空 |

 (4) は (3) の祖先であるため、最後のリクエストには実際には (4) が LCA として含まれており、その上向きセグメントは (3\to4) です。 蓄積後、制約されたエッジは次の方向になります。 

| エッジ | アップカウント | ダウンカウント | 選択された方向 |
 | --- | --- | --- | --- |
 | (1-2) | 0 | 1 | (1\to2) |
 | (1-4) | 0 | 1 | (1\to4) |
 | (4-3) | 2 | 0 | (3\to4) |
 | (3-5) | 2 | 0 | (5\to3) |

 両方向で正のカウントを持つエッジはないため、インスタンスは実行可能です。 ステートメントに示されている出力は 1 つの有効な方向ですが、拘束されていないエッジは任意の方向に向けられる可能性があるため、アルゴリズムによって別の方向が生成される可能性があります。 

サンプル 2 の場合、重要な中間状態は一連の LCA です。 

| リクエスト | LCA | 更新情報 | ダウンアップデート |
 | --- | --- | --- | --- |
 | (6\to10) | 1 | (アップ[6]++、アップ[1]--) | (ダウン[10]++、ダウン[1]--) |
 | (13\to1) | 1 | (アップ[13]++、アップ[1]--) | 純変化なし |
 | (5\to14) | 1 | (アップ[5]++、アップ[1]--) | (ダウン[14]++、ダウン[1]--) |
 | (15\to12) | 12 | (アップ[15]++、アップ[12]--) | 純変化なし |
 | (2\to8) | 2 | 純変化なし | (ダウン[8]++、ダウン[2]--) |

 ボトムアップ累算の後、各エッジは最大 1 つの正方向カウントを取得します。 たとえば、エッジ (1-2) は (6\to10) から上向きの要件を受け取るため、(2\to1) である必要があります。 エッジ (1-3) は (6\to10) から下向きの要件を受け取るため、(1\to3) である必要があります。 エッジ (2-8) は (2\to8) から下向きの要件を受け取るため、(2\to8) である必要があります。 

結果として得られるルートにはパスが含まれます```
6 -> 2 -> 1 -> 3 -> 10
13 -> 11 -> 4 -> 1
5 -> 1 -> 3 -> 9 -> 12 -> 14
15 -> 12
2 -> 8
```これは中心不変式を示しています。つまり、要求されたすべてのパスは、対応する差分カウントによって方向が独立して固定されたエッジから完全に組み立てられます。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O((n+m)\alpha(n))) | ツリーの走査、Tarjan の DSU 操作、および差分の蓄積はすべてほぼ線形です。 
| スペース | (O(n+m)) | ツリー、クエリ リスト、DSU 状態、リクエスト、および 2 つの差分配列が保存されます。 

最大の入力には、(2\cdot10^5) 個の頂点と (2\cdot10^5) 個のリクエストがあります。 このアルゴリズムは、ツリーとリクエストに対して一定数のパスのみを実行し、DSU 操作には逆アッカーマン償却コストがかかります。 これは、必要な漸近境界に適合し、指数関数的な列挙と、要求されたすべてのパスの明示的な走査の両方を回避します。 

## テストケース

 この問題の出力は一意ではないため、テストでは成功した方向を 1 つの固定文字列と比較するべきではありません。 以下のテスト ハーネスは次のことを確認します。`NO`必要に応じて報告されます。`YES`は、生成されたすべてのエッジが有効な元のエッジであること、および要求されたすべてのルートが実際に送信元から宛先に向けられていることを検証します。 

大規模なテストの場合、完全なグラフ検索によってすべてのリクエストをチェックすること自体が不必要にコストがかかるため、代わりに出力の構造プロパティをチェックします。```python
# Save the editorial solution as solution.py before running these tests.

import sys
import io
from solution import solve

def run(inp: str) -> str:
    old_stdin = sys.stdin
    old_stdout = sys.stdout

    sys.stdin = io.StringIO(inp)
    sys.stdout = io.StringIO()

    try:
        solve()
        return sys.stdout.getvalue().strip()
    finally:
        sys.stdin = old_stdin
        sys.stdout = old_stdout

def validate_orientation(inp: str, out: str) -> bool:
    data = list(map(int, inp.split()))
    it = iter(data)

    n = next(it)
    m = next(it)

    edges = set()
    for _ in range(n - 1):
        u = next(it)
        v = next(it)
        edges.add((u, v))
        edges.add((v, u))

    queries = []
    for _ in range(m):
        s = next(it)
        f = next(it)
        queries.append((s, f))

    lines = out.splitlines()
    if not lines:
        return False

    if lines[0] == "NO":
        return False

    if lines[0] != "YES":
        return False

    if len(lines) != n:
        return False

    directed = [[] for _ in range(n + 1)]

    for line in lines[1:]:
        a, b = map(int, line.split())
        if (a, b) not in edges:
            return False
        directed[a].append(b)

    # This validator is intended for small tests.
    for s, f in queries:
        seen = [False] * (n + 1)
        stack = [s]
        seen[s] = True

        while stack:
            v = stack.pop()
            if v == f:
                break

            for u in directed[v]:
                if not seen[u]:
                    seen[u] = True
                    stack.append(u)

        if not seen[f]:
            return False

    return True

# Sample 1
sample1 = """\
5 5
2 1
4 1
5 3
3 4
1 2
5 3
5 4
1 4
3 4
"""

out = run(sample1)
assert validate_orientation(sample1, out), "sample 1"

# Sample 2
sample2 = """\
15 5
1 2
1 3
1 4
1 5
2 6
2 7
2 8
3 9
3 10
4 11
9 12
11 13
12 14
12 15
6 10
13 1
5 14
15 12
2 8
"""

out = run(sample2)
assert validate_orientation(sample2, out), "sample 2"

# Sample 3
sample3 = """\
5 5
1 3
5 1
4 2
3 4
4 3
4 3
3 2
1 2
5 4
"""

assert run(sample3) == "NO", "sample 3"

# Minimum-size tree, equal endpoints, no edges to orient.
case_min = """\
1 1
1 1
"""

out = run(case_min)
assert out == "YES", "minimum-size case"

# Two opposite requirements on the only edge.
case_conflict = """\
2 2
1 2
1 2
2 1
"""

assert run(case_conflict) == "NO", "opposite directions"

# A request from a deep leaf to the root.
case_reverse_chain = """\
3 1
1 2
2 3
3 1
"""

out = run(case_reverse_chain)
assert validate_orientation(case_reverse_chain, out), "reverse chain"

# All requests have equal endpoints, so every edge is unconstrained.
case_equal = """\
4 4
1 2
2 3
3 4
2 2
2 2
2 2
2 2
"""

out = run(case_equal)
assert validate_orientation(case_equal, out), "equal endpoints"

# Maximum-size stress shape: a chain and many identical requests.
n = 200000
m = 200000

parts = [f"{n} {m}"]
for v in range(1, n):
    parts.append(f"{v} {v + 1}")
for _ in range(m):
    parts.append(f"1 {n}")

large_case = "\n".join(parts) + "\n"
out = run(large_case)

large_lines = out.splitlines()
assert large_lines[0] == "YES", "maximum-size case must be feasible"
assert len(large_lines) == n, "wrong number of output edges"

print("all tests passed")
```最初のカスタム ケースは (n=1) 境界を検証します。ここで、答えには次の値のみが含まれます。`YES`そしてエッジの説明はありません。 2 番目の方法では、矛盾が最小限に抑えられ、エッジごとに 1 つの方向のみを記憶する実装が捕捉されます。 

リバースチェーンのケースでは、ルートの向きに関する誤った仮定が捕らえられます。 エンドポイントが等しい場合は、リクエスト (s\to s) がエッジ制約をまったく課さないことが確認されます。 最大サイズのチェーンは、反復走査、コンパクトなストレージ、オフライン LCA 処理、および実際の上限での出力構築に重点を置きます。 

| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`1 1 / 1 1`|`YES`| 最小サイズのツリーと空のパス |
 |`2 2 / 1 2 / 1 2 / 2 1`|`NO`| 片側の直接矛盾 |
 |`3 1 / 1 2 / 2 3 / 3 1`|`YES`(3\to2\to1) で | 葉から根への方向とパスの境界 |
 |`4 4 / 1 2 / 2 3 / 3 4 / 2 2 ...`|`YES`| すべてのリクエストには長さ 0 のパスがあります。 
| (n=m=200000)、リクエストによるチェーン (1\to n) |`YES`すべてのエッジ付き (v\to v+1) | 最大入力サイズと反復実装 |

 ## 特殊なケース

 エンドポイントが等しい場合```
1 1
1 1
```LCA も頂点 (1) です。 どちらの差分更新もすぐにキャンセルされます。`up[1] += 1`に続く`up[1] -= 1`、同じことが起こります`down`。 検査するエッジがないため、アルゴリズムは次のように出力します。`YES`。 

直接的な矛盾については```
2 2
1 2
1 2
2 1
```ルート頂点 (1)。 最初のリクエストはエッジ (1-2) で下向きの要件を生成し、2 番目のリクエストはまったく同じエッジで上向きの要件を生成します。 積み上げた後は、`down[2] = 1`そして`up[2] = 1`。 競合条件が発生し、アルゴリズムが出力されます。`NO`。 

リバースチェーンの場合```
3 1
1 2
2 3
3 1
```(3) と (1) の LCA は (1) です。 上向きの差分更新は次のとおりです。`up[3] += 1`そして`up[1] -= 1`。 ボトムアップ累積では、値が頂点 (3) から頂点 (2) に転送され、次に頂点 (1) に転送されます。 したがって、両方のエッジには正の上向きカウントがあり、下向きカウントはありません。 これらは (3\to2) および (2\to1) の方向を向いており、要求されたパスと正確に一致します。 

制約のないエッジ状況の場合```
4 4
1 2
2 3
3 4
2 2
2 2
2 2
2 2
```すべてのリクエストには同一のエンドポイントがあります。 すべての LCA はそのエンドポイントに等しいため、すべての差分更新は同じ頂点でキャンセルされます。 すべてのエッジの両方向のカウントはゼロです。 アルゴリズムはデフォルトの親から子への方向を選択し、(1\to2)、(2\to3)、(3\to4) を生成します。これは、リクエストがエッジを横断する必要がないため有効です。
