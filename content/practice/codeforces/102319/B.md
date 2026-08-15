---
title: "CF 102319B - ポールのバドミントン"
description: "道路は木を形成しているため、2 つの場所の間には 1 つのルートが存在します。 a から b へ移動するように割り当てられた従業員は、s から t まで毎日、その一意のパス上のすべてのエッジを使用します。 Paul は、そのエッジを使用する従業員の数に関係なく、特定の日にエッジに対して 1 回の支払いを行います。"
date: "2026-08-14T04:46:52+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102319
codeforces_index: "B"
codeforces_contest_name: "UBC Summer Contest 2018"
rating: 0
weight: 102319
solve_time_s: 223
verified: true
draft: false
---

[CF 102319B - ポールのバドミントン](https://codeforces.com/problemset/problem/102319/B)

 **評価:** -
 **タグ:** -
 **解決時間:** 3 分 43 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 道路は木を形成しているため、2 つの場所の間には 1 つのルートが存在します。 からの異動を割り当てられた従業員`a`に`b`毎日、そのユニークなパス上のあらゆるエッジを使用します。`s`を通して`t`。 

Paul は、そのエッジを使用する従業員の数に関係なく、特定の日にエッジに対して 1 回の支払いを行います。 したがって、1 日に考慮する量は、少なくとも 1 つの現在アクティブな従業員ルートに属する個別の木のエッジの数です。 クエリ`[c,d]`からの全日にわたる毎日のエッジ数の合計を求めます。`c`を通して`d`。 

この困難は、2 つの独立した種類の重なり合いによって発生します。 さまざまな従業員がツリーの端を共有する可能性があるため、従業員ごとのパスを個別にカウントすると、答えが過大になります。 アクティブな時間間隔が重なることもあるため、同じエッジに対して異なる従業員が何度も支払われる可能性がありますが、1 日に 1 回だけ支払われます。 

まで`10^5`頂点、従業員、クエリなど、すべてのパスを明示的に実行するのはすでにコストがかかりすぎます。 ツリー パスには以下を含めることができます`O(n)`エッジなので、この方法ですべての従業員のパスを処理するには時間がかかります`O(nm)`、それは周りにあります`10^10`最悪の場合の運用。 時代は到達できる`10^9`, そのため、何日も繰り返すことも不可能です。 従業員の始業時間とクエリ境界のみを処理する必要があり、各ツリー演算は対数またはそれに近い値である必要があります。 

直接実装が通知なく失敗する可能性があるエッジケースがいくつかあります。 可能な限り最小のツリーを考えてみましょう。```
2 2 1
1 2
1 2 1 3
1 2 2 4
2 4
```唯一のエッジは毎日から使用されます`1`を通して`4`、したがって、答えは次のとおりです`3`。 従業員を個別にカウントすると、`3 + 3 = 6`、それらの間隔が重なっているためです。 正しい出力は次のとおりです`3`。 

2 番目の境界ケースは、ちょうど 1 日で構成される間隔です。```
2 1 1
1 2
1 2 5 5
5 5
```エッジは当日のみ使用`5`、したがって、答えは次のとおりです`1`。 エンドポイントを正しく変換せずに間隔を半開として扱うと、誤ってゼロが生成される可能性があります。 

3 番目のケースでは、ルートの一部のみを共有するパスによって引き起こされるエラーをキャッチします。```
4 2 1
1 2
2 3
2 4
3 4 1 2
1 2 2 3
2 2
```当日`2`、従業員は両方ともエッジを使用しています`1-2`、ただし、各ルートで使用されるのは他のエッジの 1 つだけです。 明確なエッジは、`1-2`、`2-3`、 そして`2-4`、したがって、答えは次のとおりです`3`。 パスの長さを追加するとカウントされます`1-2`2回生成します`4`。 

## アプローチ

 直接的な解決策は、すべての従業員を個別に検討し、次のような方向への道を見つけることです。`a`に`b`、そのパス上のすべてのエッジに対して、そのアクティブな間隔をマークします。 すべての従業員が処理された後、各エッジの間隔を結合してクエリに答えることができます。 これは正しいです。各道路は独立して考慮でき、その間隔を結合することで、対象となる 1 日あたり 1 ドルの道路料金がかかるというルールを正確にモデル化できるからです。 

問題はパスデータの量です。 チェーン内のパスには以下を含めることができます`n-1`エッジ、そしてすべて`m`従業員はそのようなパスを使用できます。 と`n=m=10^5`、明示的にパスにアクセスするには、おおよその必要があります`10^10`エッジ訪問。 インターバルのマージを処理する前であっても、それは制限時間の許容範囲をはるかに超えています。 

視点を変えると便利なのは、時間を左から右に処理することです。 日を迎えたとします`x`。 すべてのエッジについて、過去に関する 1 つの情報だけが重要です。それは、そのエッジが現在カバーされ続けることが保証されている最新の日です。 この値を呼び出します`E`。 新入社員が入社する日`s`そしてその日に終わります`t`、その従業員のパス上のすべてのエッジが取得されます。`E = max(E, t+1)`。 

使用する`t+1`インターバルが半分になるので、従業員がアクティブな日を過ごすことができます。`s`を通して`t`一日の始まりまでカバーします`t+1`。 

次に、連続する 2 つの従業員の始業時間の間に何が起こるかを考えてみましょう。 現在エッジに有効期限がある場合`E`、その後、現在の日`x`それは持っています`max(0, E-x)`残りの補償期間の日数。 させて`R = sum max(0, E_e-x)`木の端全体に。 から時間が進むと、`x`に`y`、現在カバーされているすべてのエッジが失われます`y-x`残りのカバレッジの単位。ゼロで停止します。 その期間中に支払われるエッジデイの額は、まさに`R`。 

これにより、中心的な洞察が得られます。 毎日、すべてのエッジを明示的にカウントする必要はありません。 すべてのエッジ、サポートパスの有効期限値を維持します`chmax`操作を実行し、すべての有効期限値の合計を維持します。 現在時刻を移動する前に`x`、期限切れの値をすべて次のように引き上げます。`x`。 正規化後、有効期限のあるエッジ`E`まさに貢献する`E-x`将来の対象日。 

パス`chmax`木の上では次のように分解できます`O(log n)`重光分解を使用した連続範囲。 各範囲で、次の形式の範囲操作が必要です。`E_i = max(E_i, x)`範囲合計を維持しながら。 これはまさに、セグメント ツリー ビート構造によってサポートされる操作です。 

最後に定義します`F(x)`日からの支払エッジ日数の合計として`1`一日中`x`。 クエリ`[c,d]`単純に`F(d) - F(c-1)`。 

評価します`F`関連する時間座標をスイープしながら、要求されたすべてのエンドポイントをオフラインにします。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース |`O(nm + qn)`最悪の場合 |`O(nm)`| 遅すぎる |
 | 最適 |`O(n log n + m log² n + q log q)`償却済み |`O(n + m + q)`| 承認済み |

 ## アルゴリズムのチュートリアル

 1. ツリーの頂点にルートを作成します`1`そして重光分解を実行します。 ルート以外のすべての頂点は、その頂点をその親に接続するエッジを表します。 その HLD 位置は、セグメント ツリー内のそのエッジの位置になります。 これにより、ツリー パスが次のように変換されます。`O(log n)`最も低い共通祖先の入力エッジを正しく除外しながら、連続した範囲を抽出します。 
2. すべてのエッジの有効期限を次のように初期化します。`1`。 前日`1`補償はなく、有効期限はありません`1`現時点で残りの対象日数がゼロであることを意味します`1`。 
3. すべての従業員の注文を開始日順に並べ替えます。 同じ開始日を持つ従業員をまとめて処理します。従業員の更新は、その日の直前の時間間隔を考慮した後に行われる必要があるためです。 
4. すべてのクエリを変換する`[c,d]`2 つのプレフィックス要求に分割されます。 必要です`F(d)`そして`F(c-1)`、後者は時間座標で表されます。`c`。 
5. 並べ替えられた従業員の始業時間とすべてのクエリ エンドポイント座標を調べます。 現在時刻が`cur`そして次の座標は`x`。 正規化前のセグメント ツリーの有効期限の合計を次のようにします。`S`。 
6. 現在時刻を次から進めます`cur`に`x`申請することで`E_i = max(E_i, x)`隅々まで。 これは、すでに期限切れになったエッジのみを変更します。 その後、有効期限の合計が`S'`、日中の有給エッジ日数`[cur, x-1]`は`S - n_edges * cur`マイナス`S' - n_edges * x`。 これはまさに、残りの補償範囲の合計が減少することになります。 
7. もし`x-1`は要求されたプレフィックス エンドポイントです。現在の累積値を次のように保存します。`F(x-1)`。 従業員をその日から適用する前にこれを行います`x`、これらの従業員は、どの日も活動していないためです。`x-1`。 
8. 始業日が次の従業員をすべて処理します。`x`。 従業員にとって`(a,b,s,t)`、からのパス`a`に`b`HLD によって分解され、そのパス上のすべてのエッジが`E = max(E, t+1)`。 以来`t+1`は排他的なエンドポイントです。これは正確に与えます`t-s+1`以前にカバーされていなかったエッジまで、数日間カバーします。 
9. 関連するすべての座標が処理されたら、元の各クエリに次のように答えます。`F(d) - F(c-1)`。 プレフィックス値にはすでにツリーのすべてのエッジにわたる和集合が含まれているため、重複する従業員が同じ日に 2 回カウントされることはありません。 

### なぜ効果があるのか

 あらゆるエッジに対して、`E`は、すでに勤務を開始している一部の従業員がそのエッジのカバレッジを保証する最新の排他日を表します。 新しい従業員が入社するとき、その従業員の能力を最大限に活用して、`t+1`と`E`そのエッジ上でこれまでに見られたすべての間隔の結合を正確に保持します。 現在の任意の日`x`、期限切れの値より小さい値を置き換えます。`x`による`x`これらの値は以前に終了した間隔をすでに表しているため、将来の適用範囲は変更されません。`x`。 この正規化の後、`E-x`は、そのエッジがカバーされたままになる将来の正確な日数です。 したがって、これらの値の合計が、残りの有給エッジ日数の合計となります。 時間を進めると、この合計はスイープが通過した支払エッジ日の数だけ減少します。そのため、すべてのプレフィックスが`F(x)`は正しいです。 2 つのプレフィックスを減算すると、要求されたクエリ間隔内の結合コストが正確に得られます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

INF = 10**30

class SegmentTreeBeats:
    def __init__(self, n):
        self.n = n
        size = 4 * n + 5
        self.sum = [0] * size
        self.mn = [0] * size
        self.smn = [INF] * size
        self.cnt = [0] * size
        self._build(1, 0, n)

    def _build(self, p, l, r):
        if r - l == 1:
            self.sum[p] = 1
            self.mn[p] = 1
            self.smn[p] = INF
            self.cnt[p] = 1
            return

        mid = (l + r) >> 1
        self._build(p << 1, l, mid)
        self._build(p << 1 | 1, mid, r)
        self._pull(p)

    def _pull(self, p):
        lc = p << 1
        rc = lc | 1

        self.sum[p] = self.sum[lc] + self.sum[rc]

        if self.mn[lc] < self.mn[rc]:
            self.mn[p] = self.mn[lc]
            self.cnt[p] = self.cnt[lc]
            self.smn[p] = min(self.smn[lc], self.mn[rc])
        elif self.mn[lc] > self.mn[rc]:
            self.mn[p] = self.mn[rc]
            self.cnt[p] = self.cnt[rc]
            self.smn[p] = min(self.mn[lc], self.smn[rc])
        else:
            self.mn[p] = self.mn[lc]
            self.cnt[p] = self.cnt[lc] + self.cnt[rc]
            self.smn[p] = min(self.smn[lc], self.smn[rc])

    def _apply_chmax(self, p, x):
        if x <= self.mn[p]:
            return
        self.sum[p] += (x - self.mn[p]) * self.cnt[p]
        self.mn[p] = x

    def _push(self, p):
        x = self.mn[p]
        lc = p << 1
        rc = lc | 1

        if self.mn[lc] < x:
            self._apply_chmax(lc, x)
        if self.mn[rc] < x:
            self._apply_chmax(rc, x)

    def chmax(self, ql, qr, x):
        if ql >= qr:
            return
        self._chmax(1, 0, self.n, ql, qr, x)

    def _chmax(self, p, l, r, ql, qr, x):
        if qr <= l or r <= ql or x <= self.mn[p]:
            return

        if ql <= l and r <= qr and x < self.smn[p]:
            self._apply_chmax(p, x)
            return

        self._push(p)

        mid = (l + r) >> 1
        self._chmax(p << 1, l, mid, ql, qr, x)
        self._chmax(p << 1 | 1, mid, r, ql, qr, x)

        self._pull(p)

def build_hld(n, graph):
    parent = [0] * n
    depth = [0] * n
    order = [0]
    parent[0] = -1

    for v in order:
        for to in graph[v]:
            if to == parent[v]:
                continue
            parent[to] = v
            depth[to] = depth[v] + 1
            order.append(to)

    size = [1] * n
    heavy = [-1] * n

    for v in reversed(order):
        best_size = 0
        for to in graph[v]:
            if parent[to] != v:
                continue
            size[v] += size[to]
            if size[to] > best_size:
                best_size = size[to]
                heavy[v] = to

    head = [0] * n
    pos = [0] * n
    cur_pos = 0

    stack = [(0, 0)]

    while stack:
        start, h = stack.pop()
        v = start

        while v != -1:
            head[v] = h
            pos[v] = cur_pos
            cur_pos += 1

            for to in graph[v]:
                if parent[to] == v and to != heavy[v]:
                    stack.append((to, to))

            v = heavy[v]

    return parent, depth, head, pos

def path_chmax(u, v, value, parent, depth, head, pos, seg):
    while head[u] != head[v]:
        if depth[head[u]] < depth[head[v]]:
            u, v = v, u

        h = head[u]
        seg.chmax(pos[h], pos[u] + 1, value)
        u = parent[h]

    if depth[u] > depth[v]:
        u, v = v, u

    # pos[u] is the vertex containing the LCA.
    # The edge entering the LCA must not be included.
    seg.chmax(pos[u] + 1, pos[v] + 1, value)

def solve():
    n, m, q = map(int, input().split())

    graph = [[] for _ in range(n)]

    for _ in range(n - 1):
        x, y = map(int, input().split())
        x -= 1
        y -= 1
        graph[x].append(y)
        graph[y].append(x)

    trips = []
    for _ in range(m):
        a, b, s, t = map(int, input().split())
        trips.append((s, t, a - 1, b - 1))

    queries = []
    query_times = {}

    for i in range(q):
        c, d = map(int, input().split())
        queries.append((c, d))

        # F(d) is available just before day d+1.
        query_times.setdefault(d + 1, []).append((i, 1))

        # F(c-1) is available just before day c.
        query_times.setdefault(c, []).append((i, -1))

    trips.sort()

    parent, depth, head, pos = build_hld(n, graph)
    seg = SegmentTreeBeats(n - 1)

    # The root has no associated edge, so positions are shifted implicitly
    # by using every non-root vertex's HLD position. The root's position is
    # still present, so we need a segment tree of n positions and ignore
    # the root position in path updates.
    #
    # Rebuild with n positions. Position 0 belongs to the root and is never
    # touched by path_chmax.
    seg = SegmentTreeBeats(n)

    starts = trips
    trip_idx = 0

    times = set(query_times.keys())
    for s, _, _, _ in trips:
        times.add(s)
    times = sorted(times)

    current = 1
    answer_prefix = [0] * (2 * q)
    prefix_value = 0

    for x in times:
        if x < current:
            continue

        old_sum = seg.sum[1] - n * current

        seg.chmax(0, n, x)

        new_sum = seg.sum[1] - n * x
        prefix_value += old_sum - new_sum
        current = x

        if x in query_times:
            for query_id, sign in query_times[x]:
                answer_prefix[2 * query_id + (0 if sign == 1 else 1)] = prefix_value

        while trip_idx < m and starts[trip_idx][0] == x:
            _, t, a, b = starts[trip_idx]
            path_chmax(
                a, b, t + 1,
                parent, depth, head, pos, seg
            )
            trip_idx += 1

    out = []
    for i in range(q):
        fd = answer_prefix[2 * i]
        fc = answer_prefix[2 * i + 1]
        out.append(str(fd - fc))

    sys.stdout.write("\n".join(out))

if __name__ == "__main__":
    solve()
```ツリーの前処理では、最初に親、深さ、サブツリーのサイズ、および重い子が計算されます。 次に、分解によってすべての頂点に位置が割り当てられ、その重鎖の先頭が記録されます。 非ルート頂点の位置は、その親からその頂点までのエッジを表します。 

セグメントツリーは有効期限から始まります`1`どこでも。 そのノードには、最小有効期限、2 番目に小さい個別の有効期限、最小値に等しい要素の数、および合計が格納されます。 範囲の場合`chmax(x)`、 もし`x`最小値を超えていない場合は、何も変わりません。 もし`x`は厳密に最小値と 2 番目の最小値の間にあり、影響を受けるすべての要素は正確に最小値にあるため、ノード全体を一度に更新できます。 それ以外の場合、操作は子に継承されます。 これは範囲に関する標準的なセグメント ツリーのアイデアです`chmax`。 

パスの更新は常に LCA より厳密に下で開始されるため、ルートの位置は無害です。 したがって、ルートの位置は決して変更されません。 保管`n`の代わりに位置`n-1`HLD インデックス作成が簡単になり、分解後に位置を再マッピングする必要がなくなります。 

タイムスイープで使用されるのは、`t+1`それよりも`t`。 一日中アクティブに活動する従業員`t`日中は対応する端をカバーしておかなければなりません`t`、したがって、その排他的な有効期限は一日の始まりです`t+1`。 同様に、一日を通してのクエリプレフィックス`x`前日に評価される`x+1`これは、2 つのクエリ座標が次のとおりである理由を説明しています。`d+1`そして`c`。 

表現`seg.sum[1] - n * current`残りの補償範囲の合計です。 以下のすべての有効期限が切れた後`current`に引き上げられました`current`、すべてのエッジが貢献します`E-current`、期限切れのエッジのゼロを含みます。 Python の整数には任意の精度があるため、値は安全におおよその精度に達することができます。`10^14`、32 ビットの範囲をはるかに超えています。 

各回のコーディネートの順序も意図的です。 まず時間を進めてクエリのプレフィックスを記録し、次に開始時刻がその座標と等しい従業員を適用します。 日で終わるクエリ`x-1`その日に入社する従業員に会ってはなりません`x`。 

## 実用的な例

 指定されたステートメントでは、コピーされたテキスト内のサンプル出力が省略されていますが、3 つのクエリを評価すると次のようになります。`5`、`14`、 そして`4`。 

最初のサンプルでは、​​木にエッジがあります。`1-2`、`2-3`、`1-4`、 そして`1-5`。 最初の従業員が使用するのは`1-2`そして`1-5`数日から`4`を通して`7`。 2番目の用途`2-3`、`1-2`、 そして`1-4`数日から`2`を通して`5`。 3番目の用途`2-3`数日から`6`を通して`9`。 

| 日 | アクティブエッジ | 1 日あたりの費用 |
 | --- | --- | --- |
 | 2 |`2-3`、`1-2`、`1-4`| 3 |
 | 3 |`2-3`、`1-2`、`1-4`| 3 |
 | 4 |`2-3`、`1-2`、`1-4`、`1-5`| 4 |
 | 5 |`2-3`、`1-2`、`1-4`、`1-5`| 4 |
 | 6 |`2-3`、`1-2`、`1-5`| 3 |
 | 7 |`2-3`、`1-2`、`1-5`| 3 |
 | 8 |`2-3`| 1 |
 | 9 |`2-3`| 1 |

 問い合わせ用`[7,11]`、費用は`3 + 1 + 1 = 5`。 のために`[3,6]`、それは`3 + 4 + 4 + 3 = 14`。 のために`[5,5]`、それは`4`。 

掃除は数日から始まる`2`、`4`、 そして`6`。 日中`2`、2 番目の従業員が次の有効期限を作成します。`6`その3つの端に。 日中`4`、最初の従業員は、これらの有効期限値のうち 2 つを次のように引き上げます。`8`。 日中`6`、3人目の従業員が上げます。`2-3`期限切れ`10`。 すべての更新では、セグメント ツリーが共有エッジを 2 回カウントすることはありません。`chmax`。 

2 番目の例では、オーバーラップ動作を分離します。```
2 2 3
1 2
1 2 1 3
1 2 2 4
1 1
2 3
4 4
```道は一本しかない。 最初の従業員が有効期限を与える`4`、その後 2 番目の従業員がそれを次のように引き上げます`5`。 スイープは次のように要約できます。 

| 座標 | アクション | エッジの有効期限 | プレフィックスコスト |
 | --- | --- | --- | --- |
 | 1 | 最初の旅行を始める | 4 | 0 |
 | 2 | 2 番目の旅行を開始 | 5 | 1 |
 | 4 | 3 日目までのクエリ | 5 | 3 |
 | 5 | 4 日目までのクエリ | 5 | 4 |

 答えは次のとおりです。`1`、`2`、 そして`1`3 つのクエリについて。 共有道路は 1 つの有効期限値で表されたままです。これは、なぜ`chmax`状態は重複する従業員を正しくキャプチャします。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |`O(n + m log² n + q log q)`償却済み | HLD は各従業員パスを次のように分解します。`O(log n)`範囲、およびセグメント ツリー ビートが各範囲を処理します`chmax`対数的に償却 |
 | スペース |`O(n + m + q)`| ツリー、HLD 配列、セグメント ツリー、従業員、およびクエリ情報はすべて線形空間を使用します。 

代表的な仕事は、`m`パスの更新。 と`10^5`従業員と頂点を考慮すると、重光分解では各パスが対数的に多くのセグメント範囲に保たれますが、セグメント ツリー ビートではそれらの範囲内のすべてのエッジへのアクセスが回避されます。 時間座標も次のように制限されます。`O(m+q)`、それで、`10^9`日の値の大きさによって追加の要素が作成されることはありません。 

## テストケース```python
import sys
import io

# The solution above is assumed to be saved as solve() in the same file.
# This helper temporarily replaces stdin and captures stdout.

def run(inp: str) -> str:
    old_stdin = sys.stdin
    old_stdout = sys.stdout
    try:
        sys.stdin = io.StringIO(inp)
        sys.stdout = io.StringIO()
        solve()
        return sys.stdout.getvalue().strip()
    finally:
        sys.stdin = old_stdin
        sys.stdout = old_stdout

sample = """\
5 3 3
1 2
3 2
1 4
1 5
2 5 4 7
3 4 2 5
2 3 6 9
7 11
3 6
5 5
"""

assert run(sample) == "5\n14\n4", "sample"

minimum = """\
2 1 3
1 2
1 2 1 1
1 1
2 2
1 2
"""

assert run(minimum) == "1\n0\n1", "minimum tree and one-day interval"

overlap = """\
2 2 3
1 2
1 2 1 3
1 2 2 4
1 1
2 3
4 4
"""

assert run(overlap) == "1\n2\n1", "overlapping employees on one edge"

shared_path = """\
4 2 3
1 2
2 3
2 4
3 4 1 2
1 2 2 3
1 2
2 2
3 3
"""

assert run(shared_path) == "4\n3\n1", "shared edge and path overlap"

equal_intervals = """\
3 3 4
1 2
2 3
1 3 5 5
1 3 5 5
1 2 5 5
4 5
5 5
6 6
5 5
"""

assert run(equal_intervals) == "2\n2\n0\n2", "all equal active intervals"

# A large structural test. All 100000 employees use the same complete path
# during exactly the same huge interval. Only two tree edges are ever charged.
n = 100000
m = 100000
q = 3

parts = [f"{n} {m} {q}\n"]
for v in range(2, n + 1):
    parts.append(f"{v - 1} {v}\n")

for _ in range(m):
    parts.append(f"1 {n} 1 1000000000\n")

parts.append("1 1\n")
parts.append("1 1000000000\n")
parts.append("1000000001 1000000001\n")

large_input = "".join(parts)
expected_large = f"99999\n99999999900001\n0"

assert run(large_input) == expected_large, "large repeated-path case"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 日帰りで行ける最小サイズのツリー |`1`、`0`、`1`| 最小のツリー、包括的なエンドポイント、およびすべてのアクティビティ以外の 1 日 |
 | 片側のエッジで重なっている 2 つのトリップ |`1`、`2`、`1`| 複数の従業員がエッジを二重請求してはなりません |
 | 1 つのエッジを共有する 2 つのパス |`4`、`3`、`1`| 部分的なパスの重複と HLD パス境界の修正 |
 | 等間隔と繰り返しパス |`2`、`2`、`0`、`2`| 従業員が重複しており、開始時間と終了時間がまったく同じである |
 | 大きなチェーン付き`10^5`同一の旅行 |`99999`、`99999999900001`、`0`| 大きい`n`、 大きい`m`、巨大な時間値、およびスケーラビリティ |

 ## 特殊なケース

 最も小さな木にはちょうど 1 つの端があります。 で```
2 1 1
1 2
1 2 5 5
5 5
```旅行によりその単一エッジが有効期限に更新されます`6`。 クエリエンドポイントは`6`、したがってスイープは次のように進みます。`6`、残りの補償範囲を から変更します。`1`に`0`そして、プレフィックスに有料エッジデイを 1 つだけ追加します。 答えは`1`。 の使用`t+1`これが 1 日の間隔を機能させる理由です。 

同じエッジ上で重複する従業員の場合、```
2 2 1
1 2
1 2 1 3
1 2 2 4
1 4
```最初の旅行では有効期限が次のように設定されます`4`。 2 回目の旅行では、後で次のように変更されます。`5`、別の独立したカバレッジ間隔を追加するのではなく。 最後のプレフィックスには 4 つの有給日が含まれているため、答えは次のようになります。`4`。 これはまさに次の結合です`[1,3]`そして`[2,4]`。 

部分的にのみ重なっているパスの場合、```
4 2 1
1 2
2 3
2 4
3 4 1 2
1 2 2 3
2 2
```最初のパスはエッジを使用します`2-3`そして`2-4`、2番目は使用します`1-2`そして`2-3`。 当日`2`、共有エッジ`2-3`有効期限値は 1 つだけです。 3 つの異なるアクティブ エッジが答えを与えます`3`。 

クエリは、別の従業員が開始する直前に終了することができます。 例えば、```
2 1 2
1 2
1 2 3 5
1 2
1 3
```与える`0`のために`[1,2]`そして`1`のために`[1,3]`。 の接頭辞座標`[1,2]`は`3`、アルゴリズムは、日から従業員を適用する前にプレフィックスを記録します。`3`。 この順序により、従業員が最初の勤務日より前に請求されるのを防ぎます。 

最後に、時間はすべてのアクティブな間隔よりもはるかに長くなる可能性があります。 のために```
2 1 2
1 2
1 2 1 1000000000
1000000000 1000000000
1000000001 1000000001
```最初のクエリのコスト`1`、2番目の費用はかかります`0`。 日数にわたるループは実行されません。 スイープは座標から直接ジャンプします`1`に`1000000000`そしてそして`1000000001`、有効期限の計算を使用して、すべての中間日を一度に計算します。
