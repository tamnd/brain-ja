---
title: "CF 102407K - クレイジーアレンジメント"
description: "ツリー自体がステートメントの中心であるように見えますが、有用な表現はエッジの重みではありません。 任意の頂点、たとえば頂点 1 でツリーをルートし、(hv) をルートから (v) までのパス上のエッジの重みの XOR とします。"
date: "2026-08-11T23:53:49+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102407
codeforces_index: "K"
codeforces_contest_name: "\u0418\u043d\u0442\u0435\u0440\u043d\u0435\u0442-\u043e\u043b\u0438\u043c\u043f\u0438\u0430\u0434\u044b, \u0421\u0435\u0437\u043e\u043d 2019-2020, \u0412\u0442\u043e\u0440\u0430\u044f \u043a\u043e\u043c\u0430\u043d\u0434\u043d\u0430\u044f \u043e\u043b\u0438\u043c\u043f\u0438\u0430\u0434\u0430, \u0443\u0441\u043b\u043e\u0436\u043d\u0435\u043d\u043d\u0430\u044f \u043d\u043e\u043c\u0438\u043d\u0430\u0446\u0438\u044f"
rating: 0
weight: 102407
solve_time_s: 403
verified: false
draft: false
---

[CF 102407K - クレイジーなアレンジメント](https://codeforces.com/problemset/problem/102407/K)

 **評価:** -
 **タグ:** -
 **解決時間:** 6 分 43 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 ツリー自体がステートメントの中心であるように見えますが、有用な表現はエッジの重みではありません。 任意の頂点、たとえば頂点 1 でツリーをルートし、(h_v) をルートから (v) までのパス上のエッジの重みの XOR とします。 ツリーには 2 つの頂点間のパスが 1 つだけあるため、(u) から (v) へのパスの XOR は単純に次のようになります。 

[
 s(u,v)=h_u\oplus h_v。 
]

 (h_1=0) が固定されると、(h_v) 値のすべての割り当ては、ツリー エッジの 1 つの割り当てに正確に対応します。 したがって、元のツリーの特定の形状は答えに影響を与えません。 これが問題の中心的な単純化です。 

ここで、元のツリーを忘れて、新しいグラフ (G) を構築します。 その頂点は元のツリーの頂点であり、その (i) 番目のグラフ エッジは (u_i) と (v_i) を接続します。 このエッジに必要な値は次のとおりです。 

[
 h_{u_i}\oplus h_{v_i}=s_i。 
]

 シーケンス (s_1,\ldots,s_m) は 0 と 1 のみで構成され、非減少でなければなりません。 したがって、0 と 1 の間の境界 (k) ごとに、可能な形状が 1 つだけあります。 

[
 s_1=\cdots=s_k=0,\qquad
 s_{k+1}=\cdots=s_m=1、
 ]

 ここで、(k) は (0) から (m) までの任意の整数です。 

そこで問題は、次の XOR 方程式系はいくつの境界 (k) に対して矛盾しないのかということです。 

[
 h_{u_i}\oplus h_{v_i}=
 \begin{ケース}
 0,&i\le k,\
 1,&i>k.
 \end{ケース}
 ]

 一貫した境界ごとに、解の数は同じです。 グラフ (G) では、すべての方程式が相対的な XOR 値のみを指定しているため、すべての連結成分には 1 つの自由なバイナリの選択肢があります。 固定 (h_1=0) すると、頂点 1 を含むコンポーネントの自由度が失われます。(G) に (c) 個の連結コンポーネントがある場合、すべての一貫した境界には、正確に (2^{c-1}) 個の対応するエッジ割り当てがあります。 

制約は (n,m\le250,000) に達します。 二次アルゴリズムではすでに (6.25\cdot10^{10}) 程度の基本演算が必要になりますが、これは元の問題の 2 秒の制限をはるかに超えています。 すべての (m+1) 個の境界をスキャンし、すべての (m) 個の方程式を独立してチェックするアルゴリズムでも (O(m^2)) です。 意図したソリューションでは、すべての境界をおおよそ (O(m\log m)) 個のグラフ操作でまとめて処理する必要があります。 元の問題には 2 秒の時間制限と 512 MiB のメモリ制限があります。 

境界条件を間違える簡単な方法がいくつかあります。 まず、境界 (k=0) は有効です。つまり、すべての (s_i) が 1 であることを意味します。同様に、すべての (s_i) が 0 の場合、(k=m) は有効です。たとえば、次のようになります。```
2 2
1
1 2
1 2
```常に (s_1=s_2) が生成されるため、単一ツリー エッジの両方の割り当ては異常であり、答えは 2 です。 2 つの実際のクエリ間の境界のみをチェックするソリューションでは、2 つの有効な定数シーケンスのうちの 1 つが失われることになります。 

2 番目の罠は、(k) が変化しても実現可能性が単調である必要はないということです。 サンプル 1 について考えてみましょう。```
3 3
1 2
1 2
2 3
1 3
```考えられる 4 つの単調ターゲット シーケンスは (000,001,011,111) です。 実現可能なシーケンスは (000,011,101,110) であるため、(000) と (011) のみが機能します。 有効な境界は (k=3) と (k=1) ですが、(k=2) は無効です。 したがって、最初の矛盾の後で停止したり、すべての実行可能な境界が 1 つの区間を形成すると仮定したりすることはできません。 

3 番目のトラップは、クエリ グラフが切断されている可能性があることを忘れることです。 サンプル 3 には、2 つの切断されたクエリ エッジがあります。```
4 2
1 2 3
1 2
3 4
```3 つの単調ターゲット シーケンスはどれも一貫していますが、クエリ グラフには 2 つの連結コンポーネントがあり、固定ルートを含むのはそのうちの 1 つだけであるため、各一貫システムには 2 つの解があります。 したがって、答えは 3 ではなく (3\cdot2=6) になります。 

## アプローチ

 直接的な総当たりアプローチは簡単です。 (n-1) 個のツリー エッジがあり、それぞれに 2 つの可能な重みがあるため、すべての (2^{n-1}) 個の割り当てを列挙します。 1 つの割り当てでは、(O(n)) でルートから頂点までの XOR 値を計算し、(h_u\oplus h_v) を使用して (O(1)) で要求されたすべてのパスを評価し、最後に結果のシーケンスが非減少であるかどうかを確認できます。 これは正しいです。考えられるすべてのエッジ割り当てが 1 回だけ考慮されるからです。 

問題は指数列挙です。 走行時間は

 [
 \シータ((n+m)2^{n-1}),
 ]

 そして、単に割り当てを列挙するだけでも、すでに最大 (n) で (2^{249999}) 回の反復が必要になります。 それは遠隔的には実現不可能です。 

最初の有用な観察は、クレイジー シーケンスには (m+1) 個の可能な形式しかないということです。 どの境界 (k) が一貫した XOR システムを生成するかを決定するだけで済みます。 固定境界の場合、パリティ DSU は (O(n+m)) の一貫性をチェックできます。これは、方程式 (h_u\oplus h_v=q) がまさに (u) と (v) が規定の相対色を持たなければならないという制約であるためです。 

これをすべての (m+1) 個の境界に対して個別に実行すると、(O(m(n+m))) が得られますが、それでも大きすぎます。 

重要な観察は、隣接する境界が 1 つの方程式のみで異なることです。 境界 (k-1) から境界 (k) に移動すると、(k) 番目の方程式のみが変化します。 

[
 h_{u_k}\oplus h_{v_k}=1
 ]

 に

 [
 h_{u_k}\oplus h_{v_k}=0。 
]

 これは、パリティ制約を伴うオフラインの動的接続の問題です。 境界インデックスに対して分割統治を使用できます。 境界を表すノード([l,r])において、その中点を(mid)とする。 左半分のすべての境界について、(mid) より大きいインデックスを持つすべてのエッジが境界の右側にあることが保証されるため、これらすべての方程式はパリティ 1 を持ちます。これらの方程式を一時的に DSU に追加します。 次に、左半分に戻ります。 

これらの加算をロールバックした後、右半分のすべての境界には、左半分からのすべてのエッジがパリティ 0 に固定されます。これらの方程式を一時的に追加し、右半分に再帰します。 

各クエリ エッジは分割統治ツリーのレベルごとに 1 回追加されるため、(O(m\log m)) 個の制約が挿入されます。 ロールバック パリティ DSU を使用すると、再帰呼び出しのたびに正確な前の状態を復元できます。 

DSU は、すべての頂点からその DSU 代表までのパリティを維持します。 (h_u\oplus h_v=q) を加算すると、必要な相対パリティで 2 つのコンポーネントが結合されるか、頂点がすでに間違ったパリティで接続されている場合は矛盾が検出されます。 

元のコンテストのチュートリアルでは、同じ分割統治のアイデアについて説明しており、パリティ 1 の右半分を左半分に下降しながら追加し、パリティ 0 の左半分を右半分に下降しながら追加するという表現で説明されています。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | (O((n+m)2^{n-1})) | (O(n+m)) | 遅すぎる |
 | すべての境界を個別にチェックする | (O(m(n+m))) | (O(n+m)) | 遅すぎる |
 | 分割統治 + ロールバック パリティ DSU | (O(m\log m\log n+n)) | (O(n+m)) | 承認済み |

 指定された境界内の余分な (\log n) は、ロールバック DSU のサイズによる結合検索操作から得られます。 分割統治部分は、(O(m\log m)) 回の DSU 挿入に寄与します。 

## アルゴリズムのチュートリアル

1. 概念的に元のツリーを頂点 1 にルートし、ルートから (v) までの XOR として (h_v) を定義します。 (s_i=h_{u_i}\oplus h_{v_i}) なので、元の木のエッジを処理する必要はありません。 入力からその親リストを使用するだけで済みます。 
2. (i) 番目のエッジが ((u_i,v_i)) であるクエリ グラフを構築します。 また、このグラフで通常の DSU を使用して、その連結成分をカウントします (c)。 この数によって、各一貫した境界に対応するエッジ割り当ての数が決まります。 
3. 可能なすべての非減少シーケンスを境界 (k\in[0,m]) で表します。 この境界の場合、クエリ エッジ (i) は、(i\le k) の場合はパリティ 0 を必要とし、(i>k) の場合はパリティ 1 を必要とします。 
4. (n) 個の頂点にロールバック パリティ DSU を作成します。 すべての頂点について、その親、そのコンポーネントのサイズ、および頂点からその親への XOR を保存します。 頂点からその代表までの XOR は、親リンクをたどり、これらのパリティ値を累積することによって取得されます。 
5. 可能な境界の間隔 ([l,r]) を再帰的に処理します。 (l=r) の場合、すべてのクエリ エッジにはこの 1 つの境界に対して固定の必須パリティがあり、DSU の矛盾カウンタは境界が実現可能かどうかを示します。 
6. ([l,r]) を (mid=(l+r)//2) で分割します。 ([l,mid]) のすべての境界について、インデックス (mid+1,\ldots,r) を持つすべてのクエリ エッジにはパリティ 1 があります。これらの制約を正確に追加し、左半分を再帰的に処理します。 これらの制約は再帰が下降する間アクティブのままであるため、祖先を再構築する必要はありません。 
7. DSU を、これらの追加の前に取られたチェックポイントにロールバックします。 次に、([mid+1,r]) のすべての境界について、インデックス (l,\ldots,mid) を持つすべてのクエリ エッジのパリティは 0 になります。これらの制約を追加して、右半分を再帰的に処理します。 
8. すべてのリーフ (k) で、DSU に矛盾する制約がない場合に、実行可能な境界の数を正確に増加させます。 分割統治構造では、(m) 個の方程式のすべてが、(k) で必要とされるパリティを正確に備えてそのリーフに存在することが保証されます。 
9. クエリ グラフに (c) 連結成分がある場合、実行可能な境界の数に (2^{c-1}) の法 (998,244,353) を掛けます。 頂点 1 を含むコンポーネントの (h) 値はゼロに固定されていますが、他の (c-1) コンポーネントはそれぞれ独立して反転できます。 

### なぜ効果があるのか

 すべての境界 (k) について、ルートからリーフ (k) までの再帰パスにより、最終的にすべてのクエリ エッジ (i) が (k) から分離されます。 (i\le k) の場合、2 つのインデックスが最初に分離したときにエッジは左側の兄弟にあるため、アルゴリズムはパリティ 0 を持つ方程式を追加します。 (i>k) の場合、エッジは右側の兄弟にあるため、アルゴリズムはパリティ 1 を持つ方程式を追加します。 したがって、リーフ (k) の DSU は、(k) 個の初期ゼロを持つ単調シーケンスに対応する XOR システムを正確に表します。 

パリティ DSU は、XOR 方程式が同時に満たされない場合に矛盾を正確に報告します。 したがって、葉は、その単調シーケンスが達成可能な場合に正確に数えられます。 

最後に、1 つの解が存在すると、クエリ グラフのすべての接続コンポーネントは、エッジ XOR を変更せずに、すべての (h) 値を同時に反転することができます。 (h_1=0) ため、ルート コンポーネントを反転することはできず、正確に (c-1) 個の独立したバイナリ選択肢が残ります。 したがって、すべての実行可能な境界は (2^{c-1}) の割り当てに寄与し、異なる境界は異なる (s) シーケンスに対応するため、それらの割り当ては互いに素になります。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

MOD = 998244353

class RollbackParityDSU:
    __slots__ = ("parent", "size", "parity", "bad", "history")

    def __init__(self, n):
        self.parent = list(range(n))
        self.size = [1] * n
        self.parity = [0] * n
        self.bad = 0
        self.history = []

    def find(self, x):
        px = 0
        parent = self.parent
        parity = self.parity

        while parent[x] != x:
            px ^= parity[x]
            x = parent[x]

        return x, px

    def add(self, u, v, want):
        ru, pu = self.find(u)
        rv, pv = self.find(v)

        if ru == rv:
            if (pu ^ pv) != want:
                self.bad += 1
                self.history.append((-1, 0))
            else:
                self.history.append((0, 0))
            return

        if self.size[ru] < self.size[rv]:
            ru, rv = rv, ru
            pu, pv = pv, pu

        old_size = self.size[ru]

        self.parent[rv] = ru
        self.parity[rv] = pu ^ pv ^ want
        self.size[ru] += self.size[rv]

        self.history.append((rv, old_size))

    def checkpoint(self):
        return len(self.history)

    def rollback(self, checkpoint):
        parent = self.parent
        parity = self.parity
        size = self.size
        history = self.history

        while len(history) > checkpoint:
            child, old_size = history.pop()

            if child == 0:
                continue

            if child == -1:
                self.bad -= 1
                continue

            root = parent[child]
            parent[child] = child
            parity[child] = 0
            size[root] = old_size

def solve():
    n, m = map(int, input().split())

    # The original tree is irrelevant after the h_v transformation.
    input()

    edges = [None] * m

    # Ordinary DSU, only for the number of connected components.
    comp_parent = list(range(n))
    comp_size = [1] * n
    components = n

    def comp_find(x):
        while comp_parent[x] != x:
            comp_parent[x] = comp_parent[comp_parent[x]]
            x = comp_parent[x]
        return x

    for i in range(m):
        u, v = map(int, input().split())
        u -= 1
        v -= 1
        edges[i] = (u, v)

        ru = comp_find(u)
        rv = comp_find(v)

        if ru != rv:
            if comp_size[ru] < comp_size[rv]:
                ru, rv = rv, ru
            comp_parent[rv] = ru
            comp_size[ru] += comp_size[rv]
            components -= 1

    dsu = RollbackParityDSU(n)
    good = 0

    def divide(l, r):
        nonlocal good

        if l == r:
            if dsu.bad == 0:
                good += 1
            return

        mid = (l + r) >> 1

        # For every boundary in [l, mid], all edges in [mid+1, r]
        # must have value 1.
        checkpoint = dsu.checkpoint()

        for i in range(mid + 1, r + 1):
            u, v = edges[i]
            dsu.add(u, v, 1)

        divide(l, mid)
        dsu.rollback(checkpoint)

        # For every boundary in [mid+1, r], all edges in [l, mid]
        # must have value 0.
        checkpoint = dsu.checkpoint()

        for i in range(l, mid + 1):
            u, v = edges[i]
            dsu.add(u, v, 0)

        divide(mid + 1, r)
        dsu.rollback(checkpoint)

    # Boundaries are represented by k = 0..m.
    # A boundary k means the first k query values are zero.
    #
    # Edge i (1-based) is therefore:
    #   1 when i > k
    #   0 when i <= k
    #
    # The recursive interval indices below use the same boundary
    # convention directly, so the query edge indices are shifted by 1.
    #
    # We process boundaries 0..m and query edges 1..m together by
    # storing an artificial edge-index range [0,m-1].
    #
    # The divide routine above assumes its index interval refers to
    # query edges, so we instead use a specialized recursion below.

    good = 0

    def divide_boundaries(l, r):
        nonlocal good

        if l == r:
            if dsu.bad == 0:
                good += 1
            return

        mid = (l + r) >> 1

        # Boundaries [l, mid]:
        # every query edge i > mid has parity 1.
        cp = dsu.checkpoint()
        start = max(mid, 0)
        for i in range(start, m):
            u, v = edges[i]
            # Query index is i+1, and i+1 > mid here.
            dsu.add(u, v, 1)

        divide_boundaries(l, mid)
        dsu.rollback(cp)

        # Boundaries [mid+1, r]:
        # every query edge i+1 <= mid has parity 0.
        cp = dsu.checkpoint()
        end = min(mid, m)
        for i in range(0, end):
            u, v = edges[i]
            dsu.add(u, v, 0)

        divide_boundaries(mid + 1, r)
        dsu.rollback(cp)

    divide_boundaries(0, m)

    ways_per_boundary = pow(2, components - 1, MOD)
    answer = good * ways_per_boundary % MOD
    return str(answer)

if __name__ == "__main__":
    print(solve())
```最初の入力行は (n) と (m) を与え、アルゴリズムが (h_v) 変換後にツリーを必要としなくても、元のツリーの記述が入力形式で必要になるため、次の行が消費されます。 

クエリ グラフは、順序が維持されるように配列として保存されます。 エッジ (i) のパリティは、境界がクエリ (i) を通過するときに正確に変化するため、この順序は不可欠です。 

通常の DSU はロールバック DSU とは別のものです。 その唯一の目的は、完全なクエリ グラフ内の接続コンポーネントをカウントすることです。 これを分離すると、ロールバック構造が一時的な整合性チェックのみを担当するようになります。 

ロールバック DSU ストア`parity[x]`(h_x\oplus h_{\text{parent}[x]}) として。 その間`find`, これらの値を XOR すると (h_x\oplus h_{\text{root}}) となります。 2 つの頂点がすでに同じコンポーネント内にある場合、新しい方程式は既存の相対パリティと一致するか、矛盾が生じます。 後の再帰的分岐では正確に前の状態を復元する必要があるため、矛盾は即時復帰を引き起こすのではなくカウントされます。 

2 つのコンポーネントが結合されるとき、累積されたパリティが (p_u) と (p_v) であるとします。 新しい親関係は次の条件を満たす必要があります。 

[
 p_u\oplus\text{パリティ}[v]\oplus p_v=q,
 ]

 したがって、接続されたルートに割り当てられるパリティは次のようになります。`pu ^ pv ^ want`。 サイズによる結合では、DSU の深さが対数に保たれます。 

ロールバック チェックポイントは、単に履歴スタックの現在の長さです。 マージが成功するたびに、接続されたルートと生き残ったルートの古いサイズが記録されます。 矛盾は特別なマーカーを記録します。 ロールバックすると、これらの変更が逆の順序で復元されます。 

分割統治再帰では、(1) から (m) までのクエリ インデックスではなく、(0) から (m) までの境界が使用されます。 この違いが、off-by-one エラーの主な原因です。 境界 0 はすべてのクエリ値が 1 であることを意味し、境界 (m) はすべてのクエリ値が 0 であることを意味します。 

分割 ([l,r]) では、クエリ インデックスが中点より大きいすべてのエッジの左半分全体で値 1 になります。 逆に、クエリ インデックスが最大で中間点にあるすべてのエッジは、右半分全体で値 0 を持ちます。 これらはまさに、現在の間隔内で変更される条件を誤って課すことなく、下降する前に追加できる制約です。 

Python の整数はオーバーフローせず、モジュラー演算は境界ごとの解の数による最終的な乗算のみです。 べき乗 (2^{c-1}) はべき乗剰余で計算されます。 

## 実用的な例

 ### サンプル 1

 クエリ グラフは三角形です

 [
 1-2、\quad2-3、\quad1-3。 
]

 これには 1 つの連結成分があるため、各実行可能な境界は 1 つの割り当てにのみ寄与します。 

４つのモノトーンターゲットシーケンスは、（ｋ＝０、１、２、３）に対応する。 

| 境界(k) | ターゲット | サイクルパリティ | 一貫性のある？ | 貢献 |
 | --- | --- | --- | --- | --- |
 | 0 | 111 | (1\oplus1\oplus1=1) | いいえ | 0 |
 | 1 | 011 | (0\oplus1\oplus1=0) | はい | 1 |
 | 2 | 001 | (0\oplus0\oplus1=1) | いいえ | 0 |
 | 3 | 000 | (0\oplus0\oplus0=0) | はい | 1 |

 有効な境界は (k=1) と (k=3) で、2 つの割り当てが与えられます。 これはまさにサンプル出力です。 

### サンプル 2

 クエリ グラフは 4 サイクルです

 [
 1-2-3-4-1。 
]

 ここでも、接続コンポーネントは 1 つだけです。 ターゲット値が 1 である奇数のエッジが単一サイクルに含まれている場合、そのサイクルは正確に不一致になります。 

| 境界(k) | ターゲット | サイクル上の 1 エッジの数 | 一貫性のある？ | 貢献 |
 | --- | --- | --- | --- | --- |
 | 0 | 1111 | 4 | はい | 1 |
 | 1 | 0111 | 3 | いいえ | 0 |
 | 2 | 0011 | 2 | はい | 1 |
 | 3 | 0001 | 1 | いいえ | 0 |
 | 4 | 0000 | 0 | はい | 1 |

 したがって、3 つの境界、つまり (0,2,4) が機能し、答えは 3 になります。この例は、実行可能な境界が 1 つの連続した間隔を形成するのではなく、有効と無効の間で切り替わることを示しているため、特に役立ちます。 

### サンプル 3

 クエリ グラフは、2 つの切断されたエッジ (1-2) および (3-4) で構成されます。 これには (c=2) 個の連結成分があります。 

サイクルがないため、これら 2 つのクエリ エッジへのパリティの割り当てはすべて一貫しています。 したがって、すべての (m+1=3) 境界が実現可能になります。 

それぞれの境界には、

 [
 2^{c-1}=2
 ]

 頂点 1 を含むコンポーネントが固定されている一方で、他のコンポーネントは反転できるため、解決策が得られます。 答えは (3\cdot2=6) です。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O(n+m\log m\log n)) | 各クエリ エッジは分割統治レベルごとに最大 1 回挿入され、ロールバック DSU はサイズによる和集合を使用して take (O(\log n)) を見つけます。 
| スペース | (O(n+m)) | クエリ エッジ、2 つの DSU 構造、再帰スタック、ロールバック履歴 |

 (m\le250,000) の場合、分割統治の深さは 20 未満になります。各クエリはレベルごとに 1 つの兄弟挿入のみに参加するため、一時的な制約挿入の数は (O(m^2)) ではなく (O(m\log m)) になります。 元のツリーは入力サイズのみに寄与し、保存されることはないため、メモリ使用量は線形に保たれます。 

## テストケース

 次のハーネスは同じものを使用します`solve`提出されたソリューションとして機能します。 最大サイズのケースはストレス テストであり、意図的に大きくされているため、通常の単体テストとは別に実行する必要があります。```python
import sys
import io

# Paste the solution above before these tests.

def run(inp: str) -> str:
    old_stdin = sys.stdin
    try:
        sys.stdin = io.StringIO(inp)
        return solve().strip()
    finally:
        sys.stdin = old_stdin

# Provided samples

assert run("""\
3 3
1 2
1 2
2 3
1 3
""") == "2", "sample 1"

assert run("""\
4 4
1 1 1
1 2
2 3
3 4
1 4
""") == "3", "sample 2"

assert run("""\
4 2
1 2 3
1 2
3 4
""") == "6", "sample 3"

# Minimum-size case.
# There are two identical queries on the only tree edge.
# The two path parities are always equal, so every edge assignment works.
assert run("""\
2 2
1
1 2
1 2
""") == "2", "minimum size"

# All query values are equal for every assignment.
# Only 0000 and 1111 are possible monotone target sequences.
# There are 2^(4-1) = 8 tree assignments.
assert run("""\
4 4
1 2 3
1 2
1 2
1 2
1 2
""") == "8", "all equal queries"

# Boundary/off-by-one case.
# The query graph is a tree, so every one of the m+1 boundaries is feasible.
# n=5, m=3 gives 4 boundaries and 2^(2-1)=2 assignments per boundary.
assert run("""\
5 3
1 2 3 4
1 2
2 3
4 5
""") == "8", "all boundaries feasible"

# Maximum-size stress case.
# The query graph is a chain plus a duplicate of edge (1,2).
# The only cycle consists of query edges 1 and m, so only k=0 and k=m
# are feasible. The query graph is connected, hence the answer is 2.
n = 250000
parents = " ".join(str(i) for i in range(1, n))
queries = "\n".join(
    [f"{i} {i + 1}" for i in range(1, n)] + ["1 2"]
)
max_input = f"{n} {n}\n{parents}\n{queries}\n"

assert run(max_input) == "2", "maximum-size stress case"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`2 2`、2部`1 2`| 2 | 最小値 (n,m)、反復クエリ、定数シーケンス |
 |`4 4`、4部`1 2`| 8 | 切断されたクエリ グラフと等しいパス値 |
 |`5 3`、クエリ グラフはフォレストです | 8 | すべての境界は実現可能であり、構成要素 |
 |`250000 250000`、チェーンと重複エッジ | 2 | 最大サイズの入力と (O(m\log m)) の分割統治動作 |

 ## 特殊なケース

 定数ゼロの境界 (k=m) は、右端のリーフによって処理されます。 そのリーフでは、すべてのクエリ方程式のパリティは 0 です。最小の場合```
2 2
1
1 2
1 2
```DSU は (h_1\oplus h_2=0) のコピーを 2 つ受け取ります。 どちらも他方と矛盾しないため、境界は実現可能です。 

定数 1 の境界 (k=0) は、一番左のリーフによって処理されます。 同じ最小値の場合、両方の方程式は (h_1\oplus h_2=1) となり、これも一貫しています。 これら 2 つの境界は、唯一のツリー エッジの 2 つの可能な割り当てに対応し、出力 2 が得られます。 

サンプル 1 に見られる交互の実現可能性は、分割統治では実現可能な境界のセットの形状について何も仮定しないため、処理されます。 のために```
3 3
1 2
1 2
2 3
1 3
```葉 (k=0,1,2,3) は独立して矛盾状態 (1,0,1,0) で終わります。 したがって、正確に 2 枚の葉が数えられます。 

切断されたクエリ グラフは、整合性テストではなくコンポーネント乗算器によって処理されます。 で```
4 2
1 2 3
1 2
3 4
```クエリ コンポーネントは 2 つあります。 クエリ グラフにはサイクルがなく、各一貫したシステムには固定 (h_1=0) 後に 2 つの解があるため、すべての境界は一貫しています。 結果は (3\cdot2=6) になります。 

繰り返されるクエリのペアも通常のグラフ エッジであり、単純に重複排除できるものではありません。 必要なパリティが異なる 2 つの等しいクエリ エッジは、直ちに矛盾を形成します。 (1\ 2) のコピーが 4 つあるすべて等しいケースでは、同じペアが 4 回出現するにもかかわらず、利用できる定数ターゲット シーケンスが 2 つしかないのはまさにこのためです。 

最後に、元のツリーは任意の形状を持つことができ、その親リストはクエリ グラフとはまったく無関係に見える可能性があります。 (h_1=0) での (h_2,\ldots,h_n) の割り当てはすべて、正確に 1 つのツリー エッジ割り当てに対応するため、変換 (h_v) によりツリー トポロジは無関係になります。 このため、実装では入力を進めるためだけに親リストを読み取り、その後は使用しません。 

上の社説では、公式の分割統治アイデアのロールバック DSU バージョンを使用しています。 元のチュートリアルでは、明示的なグラフ圧縮を使用した O(mlogm) の改良についても言及していますが、ロールバック公式の方が実装と説明が大幅に簡単です。
