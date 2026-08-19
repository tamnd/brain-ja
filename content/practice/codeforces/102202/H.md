---
title: "CF 102202H - 嫉妬深い教師たち"
description: "入力を 2 部グラフとして考えてください。 左側にはまだ学校に通っている (N-1) 人の生徒が含まれ、右側には (N) 人の教師が含まれています。 エッジ ((s,t)) は、生徒 (s) が教師 (t) に花を贈ることができることを意味します。"
date: "2026-08-18T01:19:20+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102202
codeforces_index: "H"
codeforces_contest_name: "2019 KAIST RUN Spring Contest"
rating: 0
weight: 102202
solve_time_s: 390
verified: true
draft: false
---

[CF 102202H - 嫉妬深い教師](https://codeforces.com/problemset/problem/102202/H)

 **評価:** -
 **タグ:** -
 **解決時間:** 6 分 30 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 入力を 2 部グラフとして考えてください。 左側にはまだ学校に通っている (N-1) 人の生徒が含まれ、右側には (N) 人の教師が含まれています。 エッジ ((s,t)) は、生徒 (s) が教師 (t) に花を贈ることができることを意味します。 各生徒はちょうど (N) 個の花を持っていますが、すべての教師はちょうど (N-1) 個の花を受け取らなければなりません。 

指定されたエッジごとに、そのエッジを通過する花の数を表す非負の整数を出力する必要があります。 すべての生徒に付随するエッジの値の合計は (N) でなければならず、すべての教師に付随するエッジの値の合計は (N-1) でなければなりません。 グラフ内に存在するがフラワーを受け取らないエッジは完全に有効です。 

総供給量は ((N-1)N)、総需要量は (N(N-1)) であるため、グローバル合計は自動的に一致します。 この困難は完全にエッジの欠落によって引き起こされます。 これは、疎な 2 部グラフ上の輸送問題です。 

この制約は、一般的な最大流量を除外するのに十分な大きさです。 (10^5) 人の生徒と (2\cdot10^5) 個の許可されたペアが存在する可能性があるため、(O(NM)) かそれより悪いアルゴリズムはすでに大きすぎます。 意図したソリューションでは、高価な部分を 2 部マッチングに削減します。これは (O(M\sqrt N)) で実行でき、その後は線形グラフ処理のみが続きます。 元のコンテスト ソリューションでも、一般的なフロー アルゴリズムではなく、このマッチング ベースの構造が使用されています。 

一見合理的な構築が失敗するエッジケースがいくつかあります。 孤立した教師はすぐに答えを不可能にしてしまいます。 例えば、```
2 1
1 1
```教師 2 には入力エッジの可能性がないため、正しい出力は次のようになります。`-1`。 すべての生徒に対応可能な教師がいるかどうかのみを確認する不注意な実装では、これを見逃す可能性があります。 

孤立した生徒も同様に致命的です。 例えば、```
3 1
1 1
```生徒 2 には 3 本の花を送る場所がないため、正しい出力は次のようになります。`-1`。 

マッチングは必要ですが、十分ではありません。 たとえば、2 番目のサンプルにはサイズ 5 のマッチングがありますが、グラフは生徒 5 と教師 5 および 6 を含むコンポーネントに分割され、他の生徒はこれらの教師と対話できません。 正しい出力は次のとおりです`-1`。 サイズ (N-1) の一致が見つかった直後に停止するソリューションは、無効なインスタンスを黙って受け入れます。 

最後に、ゼロフローエッジを許可する必要があります。 最初のサンプルでは、​​有効なソリューションでは、多くの入力ペアがフラワーを受け取りません。 すべての入力エッジを正の流れを運ぶ必要があるかのように扱うと問題が変わり、有効なグラフが誤って拒否される可能性があります。 

## アプローチ

 最も直接的なアプローチは、問題を最大流量としてモデル化することです。 すべての生徒に接続されたソースを容量 (N) で追加し、許可されたすべての生徒と教師のペアを無限の容量で接続し、すべての教師を容量 (N-1) のシンクに接続します。 (N(N-1)) のフローはまさに望ましい割り当てです。 

このモデルは正しいですが、一度に 1 つずつ花を拡張する意図的に単純な Ford-Fulkerson 実装では、(N(N-1)) 個の拡張を実行できます。 各拡張では (O(M+N)) 個のグラフ エッジを検査して、おおよその結果を得ることができます。 

[
 N(N-1)(M+2N)
 】

 エッジ検査。 最大境界では、これは (4\cdot10^{15}) 程度であり、制限時間をはるかに超えています。 公式の解決策は、小さなサブタスクに対するこの最大流量の定式化を記述しており、そこに (O(N^4)) を与えます。 

有益な観察は、生徒のサブセット (S) を含むカットを見ることで得られます。 これらの学生は集合的に (N|S|) 個の花を所有しています。 近隣の教師のみが花を受け取ることができ、各教師は最大で (N-1) 個の花を受け取ることができます。 したがって、空ではないすべての学生サブセットには少なくとも

 [
 N|S|\le (N-1)|N(S)|
 】

 近所の先生たち。 (1\le |S|\le N-1) なので、この不等式は次と同等です。 

[
 |N(S)|\ge |S|+1。 
】

 この追加の教師が問題の重要な構造です。 通常のホールの状態よりも強いです。 

まず、すべての (N-1) 人の生徒をカバーする一致を見つけます。 まさに一人の教師が比類のないままです。 その先生をルートと呼びます。 次に、特別な方法で 2 部グラフの向きを変えます。 一致するエッジはすべて生徒から教師に向けられ、一致しないエッジはすべて教師から生徒に向けられます。 

一致しない教師から始めて、これらの有向エッジを使用して検索を実行します。 すべての生徒に到達すると、検索エッジはすべて (N 人) の教師を接続するツリーを形成します。 一部の生徒に連絡が取れない場合、必要なより強力なホール条件は失敗するため、花の割り当ては存在しません。 

残りの構造は驚くほど簡単です。 このツリーでは、一致するエッジが生徒からその教師に向けられているため、すべての生徒に 1 人の子教師が割り当てられます。 ルートを除くすべての教師には、親生徒が 1 人だけいます。 すべての部分木の生徒の数がわかれば、すべての木の端の花の数は保護から直接導き出されます。 

ブルートフォース フロー モデルは、必要な供給と需要を正確に表現するため機能します。 合計フローは (N) で二次関数であるため、失敗します。 容量がちょうど 1 だけ異なるという観察は、実現可能性の問題をマッチングと交互ツリーの問題に変換し、その後、実際のフロー値をサブツリー サイズから取得できます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | (O(N(N-1)(M+N))) ユニット増強 | (O(M+N)) | 遅すぎる |
 | 最適 | (O(M\sqrt N)) | (O(M+N)) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 左側に生徒、右側に教師を配置した 2 部グラフを作成します。 最終的な回答は入力順序どおりに出力する必要があるため、元の入力エッジ インデックスをすべての隣接エントリとともに保存します。 
2. 最大の 2 部一致を見つけます。 すべての (N-1) 人の学生が一致する必要があります。 マッチングに含まれるエッジが (N-1) 個未満の場合は、すぐに出力します`-1`。 

有効な花の割り当てはいずれもより強力なホール条件を満たし、その条件は特に通常のホール条件を意味するため、マッチングが必要です。 

1. ユニークで一致しない教師を見つけて、それを交互検索のルートとして使用します。 
2. 生徒から一致するすべてのエッジを、一致する教師に向けます。 一致しないすべてのエッジを教師から生徒に向けます。 

比類のない教師から始めて、次の指示に従ってください。 教師がまだ見ていない生徒に連絡するたびに、その生徒をツリーに追加します。 生徒の一致するエッジをすぐにたどり、一致した教師に到達し、その教師も追加します。 

1. 検索結果が (N-1) 人未満の学生に到達した場合は、印刷します。`-1`。 

検索が (k) 人の生徒のセットに到達したとします。 また、最初はルート教師が存在し、新しく到達した生徒はそれぞれ、その固有の一致する教師を紹介するため、ちょうど (k+1) 人の教師に到達します。 グラフに有効な割り当てがある場合、より強力なホール条件により、この交互グラフ内の比類のない教師からすべての生徒に連絡できることが保証されます。 これを確認する 1 つの方法は、現在のマッチングを、現在の生徒のマッチングされた教師を回避したマッチングと比較することです。 それらの対称的な違いには、一致しないルートからその生徒までの交互のパスが含まれています。 

1. 検索エッジを通常のルート付きツリーとして扱います。 すべての生徒ノードに (1) の初期サブツリー重みを与え、すべての教師ノードに (0) の初期サブツリー重みを与えます。 すべてのノードがそのサブツリー内の生徒の総数を受け取るように、ツリーを逆の順序で処理します。 
2. 子が生徒であるツリー エッジの場合、次のように割り当てます。 

[
 f = \演算子名{サイズ}(秒)。 
】

 エッジはその生徒をその親教師に接続するため、(f) はまさに生徒のサブツリー全体が上向きに送信する必要がある花の数です。 

1. 子が教師 (t) であるツリー エッジの場合、次のように割り当てます。 

[
 f = N-1-\オペレーター名{サイズ}(t)。 
】

 同様に、その親学生が (s) の場合、

 [
 f=N-\オペレータ名{サイズ}(s)。 
】

 木のエッジではないすべての入力エッジは花を受け取りません。 

1. オリジナルの各エッジに割り当てられたフローを入力順に出力します。 

### なぜ効果があるのか

 重要な不変条件は、すべての生徒サブツリーには同数の生徒と教師が含まれる一方、すべての非ルート教師サブツリーには生徒より 1 人多い教師が含まれるということです。 学生の場合を考えてみましょう。 その親エッジには (\operatorname{size}(s)) 個の花があり、子教師エッジには (N-\operatorname{size}(s)) 個の花が付いています。 それらの合計は正確に (N) であるため、各生徒はすべての花を送ります。 

次に、非ルート教師 (t) について考えてみましょう。 そのサブツリーに (k) 人の生徒が含まれるようにします。 その親スチューデントは (N-1-k) 個の花を (t) に送信し、子スチューデントのサブツリーは集合的に (k) 個の花を送信します。 合計は (N-1) です。 ルートには親エッジがなく、その子スチューデントのサブツリーにはすべての (N-1) 個のスチューデントが含まれるため、正確に (N-1) 個の花も受け取ります。 

学生サブツリーには最大でも (N-1) 人の学生が含まれるため、割り当てられたすべての値は負ではありません。 ツリー以外のすべてのエッジはゼロを運ぶため、元のグラフの制限はすべて尊重されます。 

## Python ソリューション```python
import sys
from collections import deque

input = sys.stdin.readline

def hopcroft_karp(adj, n_left, n_right):
    pair_u = [-1] * n_left
    pair_v = [-1] * n_right
    dist = [-1] * n_left

    def bfs():
        q = deque()

        for u in range(n_left):
            if pair_u[u] == -1:
                dist[u] = 0
                q.append(u)
            else:
                dist[u] = -1

        found = False

        while q:
            u = q.popleft()

            for v in adj[u]:
                w = pair_v[v]

                if w == -1:
                    found = True
                elif dist[w] == -1:
                    dist[w] = dist[u] + 1
                    q.append(w)

        return found

    def dfs(start):
        stack = [start]
        chosen = []
        it = {}

        while stack:
            u = stack[-1]

            if u not in it:
                it[u] = 0

            i = it[u]

            while i < len(adj[u]):
                v = adj[u][i]
                i += 1
                it[u] = i

                w = pair_v[v]

                if w == -1:
                    pair_u[u] = v
                    pair_v[v] = u

                    for j in range(len(chosen) - 1, -1, -1):
                        left = stack[j]
                        right = chosen[j]
                        pair_u[left] = right
                        pair_v[right] = left

                    return True

                if dist[w] == dist[u] + 1:
                    stack.append(w)
                    chosen.append(v)
                    break
            else:
                dist[u] = -1
                stack.pop()
                if chosen:
                    chosen.pop()

        return False

    matching = 0

    while bfs():
        for u in range(n_left):
            if pair_u[u] == -1 and dfs(u):
                matching += 1

    return matching, pair_u, pair_v

def solve():
    n, m = map(int, input().split())
    left = n - 1

    adj = [[] for _ in range(left)]
    edges = []

    for idx in range(m):
        s, t = map(int, input().split())
        s -= 1
        t -= 1
        adj[s].append((t, idx))
        edges.append((s, t))

    # The matching algorithm only needs the teacher number.
    match_adj = [[t for t, _ in adj[s]] for s in range(left)]

    matching, pair_u, pair_v = hopcroft_karp(
        match_adj, left, n
    )

    if matching != left:
        print(-1)
        return

    # Find the original edge corresponding to every matching pair.
    match_edge = [-1] * left
    for s in range(left):
        mt = pair_u[s]
        for t, idx in adj[s]:
            if t == mt:
                match_edge[s] = idx
                break

    root = -1
    for t in range(n):
        if pair_v[t] == -1:
            root = t
            break

    # Nodes 0..n-1 are teachers.
    # Nodes n..n+left-1 are students.
    total_nodes = n + left
    parent = [-1] * total_nodes
    parent_edge = [-1] * total_nodes

    root_node = root
    parent[root_node] = root_node

    visited_students = [False] * left
    teacher_stack = [root_node]
    order = [root_node]

    while teacher_stack:
        tnode = teacher_stack.pop()
        t = tnode

        for s, idx in []:
            pass

        # Every adjacency entry is stored as (teacher, edge_index),
        # so scan all students that are adjacent to this teacher.
        # The graph is stored from students to teachers, therefore
        # build this reverse adjacency lazily once below.
        # This branch is intentionally replaced after reverse_adj exists.
        break

    # Reverse adjacency: teacher -> (student, original edge).
    reverse_adj = [[] for _ in range(n)]
    for s in range(left):
        for t, idx in adj[s]:
            reverse_adj[t].append((s, idx))

    # Restart the alternating-tree search.
    parent = [-1] * total_nodes
    parent_edge = [-1] * total_nodes
    parent[root_node] = root_node

    visited_students = [False] * left
    teacher_stack = [root_node]
    order = [root_node]

    while teacher_stack:
        t = teacher_stack.pop()

        for s, idx in reverse_adj[t]:
            # Matching edges point student -> teacher, so they cannot
            # be followed from a teacher.
            if pair_u[s] == t:
                continue

            if visited_students[s]:
                continue

            visited_students[s] = True

            snode = n + s
            parent[snode] = t
            parent_edge[snode] = idx
            order.append(snode)

            mt = pair_u[s]
            tchild = mt

            # A newly reached student's matched teacher is new as well.
            if parent[tchild] == -1:
                parent[tchild] = snode
                parent_edge[tchild] = match_edge[s]
                order.append(tchild)
                teacher_stack.append(tchild)

    if sum(visited_students) != left:
        print(-1)
        return

    # Count students in every subtree.
    size = [0] * total_nodes

    for node in range(n, total_nodes):
        size[node] = 1

    for node in reversed(order[1:]):
        size[parent[node]] += size[node]

    answer = [0] * m

    # Assign the flow on every tree edge.
    for node in order[1:]:
        idx = parent_edge[node]

        if node >= n:
            # Child is a student.
            answer[idx] = size[node]
        else:
            # Child is a teacher.
            answer[idx] = n - 1 - size[node]

    sys.stdout.write("\n".join(map(str, answer)))

if __name__ == "__main__":
    solve()
```入力は概念的に 2 回保存されます。`adj`元の生徒と教師の隣接関係をエッジ インデックスとともに維持しますが、`reverse_adj`はマッチング後に作成されるため、交互検索が教師から隣の生徒に効率的に移行できます。 

マッチングは教師番号のみから計算されます。 一致がわかった後、各学生の隣接リストを 1 回スキャンすることで、各学生の一致エッジが回復されます。 入力ペアの総数は (M) であるため、この回復には (O(M)) のコストがかかります。 

交互ツリーは次のように使用します。`parent`訪問したノードをマークします。 教師ノードがインデックスを占有する`0`を通して`N-1`、学生ノードが占有している間、`N+s`。 これにより、ツリーに個別のグラフ オブジェクトを割り当てることが回避され、サブツリーの計算が簡単になります。 

前の最初の探索ループ`reverse_adj`は不要であり、洗練された実装には存在すべきではありません。 次のクリーンアップされたバージョンが送信するバージョンです。```python
import sys
from collections import deque

input = sys.stdin.readline

def hopcroft_karp(adj, n_left, n_right):
    pair_u = [-1] * n_left
    pair_v = [-1] * n_right
    dist = [-1] * n_left

    def bfs():
        q = deque()

        for u in range(n_left):
            if pair_u[u] == -1:
                dist[u] = 0
                q.append(u)
            else:
                dist[u] = -1

        found = False

        while q:
            u = q.popleft()

            for v in adj[u]:
                w = pair_v[v]
                if w == -1:
                    found = True
                elif dist[w] == -1:
                    dist[w] = dist[u] + 1
                    q.append(w)

        return found

    def dfs(start):
        stack = [start]
        chosen = []
        it = [0] * n_left

        while stack:
            u = stack[-1]

            while it[u] < len(adj[u]):
                v = adj[u][it[u]]
                it[u] += 1

                w = pair_v[v]

                if w == -1:
                    pair_u[u] = v
                    pair_v[v] = u

                    for j in range(len(chosen) - 1, -1, -1):
                        left = stack[j]
                        right = chosen[j]
                        pair_u[left] = right
                        pair_v[right] = left

                    return True

                if dist[w] == dist[u] + 1:
                    stack.append(w)
                    chosen.append(v)
                    break
            else:
                dist[u] = -1
                stack.pop()
                if chosen:
                    chosen.pop()

        return False

    matching = 0

    while bfs():
        for u in range(n_left):
            if pair_u[u] == -1 and dfs(u):
                matching += 1

    return matching, pair_u, pair_v

def solve():
    n, m = map(int, input().split())
    left = n - 1

    adj = [[] for _ in range(left)]
    reverse_adj = [[] for _ in range(n)]
    edges = [None] * m

    for idx in range(m):
        s, t = map(int, input().split())
        s -= 1
        t -= 1

        adj[s].append((t, idx))
        reverse_adj[t].append((s, idx))
        edges[idx] = (s, t)

    match_adj = [[t for t, _ in adj[s]] for s in range(left)]

    matching, pair_u, pair_v = hopcroft_karp(
        match_adj, left, n
    )

    if matching != left:
        print(-1)
        return

    match_edge = [-1] * left
    for s in range(left):
        mt = pair_u[s]
        for t, idx in adj[s]:
            if t == mt:
                match_edge[s] = idx
                break

    root = -1
    for t in range(n):
        if pair_v[t] == -1:
            root = t
            break

    total_nodes = n + left
    parent = [-1] * total_nodes
    parent_edge = [-1] * total_nodes

    parent[root] = root
    visited_student = [False] * left

    stack = [root]
    order = [root]

    while stack:
        t = stack.pop()

        for s, idx in reverse_adj[t]:
            if pair_u[s] == t:
                continue

            if visited_student[s]:
                continue

            visited_student[s] = True

            snode = n + s
            parent[snode] = t
            parent_edge[snode] = idx
            order.append(snode)

            mt = pair_u[s]
            parent[mt] = snode
            parent_edge[mt] = match_edge[s]
            order.append(mt)
            stack.append(mt)

    if not all(visited_student):
        print(-1)
        return

    size = [0] * total_nodes

    for s in range(left):
        size[n + s] = 1

    for node in reversed(order[1:]):
        size[parent[node]] += size[node]

    answer = [0] * m

    for node in order[1:]:
        idx = parent_edge[node]

        if node >= n:
            answer[idx] = size[node]
        else:
            answer[idx] = n - 1 - size[node]

    sys.stdout.write("\n".join(map(str, answer)))

if __name__ == "__main__":
    solve()
```2 番目のバージョンでは、冗長な初期化が削除され、実際の送信バージョンになります。 Hopcroft-Karp 内の反復 DFS は、グラフの片側に (10^5) 個の頂点を含めることができるため、Python の再帰の深さの問題を回避します。 

Python では整数オーバーフローの問題はありません。 最大のフロー値は (N-1) ですが、サブツリー数は最大でも (N-1) です。 唯一の微妙な境界線は、教師と子供の公式です。`n - 1 - size[node]`、 ない`n - size[node]`。 (k) 人の生徒を含む教師サブツリーには、親生徒からの (N-1-k) 個の花が必要です。 

## 実用的な例

 ### サンプル 1

 次の有効な一致をトレースとして使用します。 

[
 s_1\から t_5,\quad
 s_2\から t_2,\quad
 s_3\から t_3,\quad
 s_4\から t_1,\quad
 s_5\から t_6 まで。 
】

 先生(t_4)は絶倫なのでルートになります。 有向交互ツリーは

 [
 t_4
 \to s_1\to t_5、
 \クアッド
 t_4\から s_4\から t_1\から s_3\から t_3、
 \クアッド
 t_4\から s_5\から t_6、
 \クアッド
 t_4\から s_2\から t_2。 
】

 | ステップ | 現在の教師 | 新しく到着した学生 | マッチした先生 | Student サブツリーのサイズ |
 | --- | --- | --- | --- | --- |
 | 1 | (t_4) | (s_1) | (t_5) | (1) |
 | 2 | (t_4) | (s_4) | (t_1) | (2) |
 | 3 | (t_4) | (s_5) | (t_6) | (1) |
 | 4 | (t_4) | (s_2) | (t_2) | (1) |
 | 5 | (t_1) | (s_3) | (t_3) | (1) |

 最終的なサブツリーのサイズは、(s_1,s_4,s_5,s_2,s_3) に対してそれぞれ (1,2,1,1,1) です。 結果として生じる非ゼロのツリー フローは次のとおりです。 

| エッジ | 子のサブツリーのサイズ | 花 |
 | --- | --- | --- |
 | (s_1,t_4) | (1) | (1) |
 | (s_1,t_5) | (0) 教師サブツリー | (5) |
 | (s_4,t_4) | (2) | (2) |
 | (s_4,t_1) | (1) 教師サブツリー | (4) |
 | (s_3,t_1) | (1) | (1) |
 | (s_3,t_3) | (0) 教師サブツリー | (5) |
 | (s_5,t_4) | (1) | (1) |
 | (s_5,t_6) | (0) 教師サブツリー | (5) |
 | (s_2,t_4) | (1) | (1) |
 | (s_2,t_2) | (0) 教師サブツリー | (5) |

 すべての生徒は合計 (6) を取得し、すべての教師は合計 (5) を取得します。 これはサブツリーの不変条件を直接示しています。 

### サンプル 2

 次のような一致を選択します

 [
 s_1\から t_2,\quad
 s_2\から t_4,\quad
 s_3\から t_3,\quad
 s_4\から t_1,\quad
 s_5\から t_6 まで。 
】

 先生(t_5)は絶倫で根幹に成ります。 

| ステップ | 現在の教師 | 新しく到着した学生 | マッチした先生 | 結果 |
 | --- | --- | --- | --- | --- |
 | 1 | (t_5) | (s_5) | (t_6) | (s_5) に達しました |
 | 2 | (t_6) | なし | なし | 検索が停止します |
 | 3 | 連絡が取れない教師 | (s_1,s_2,s_3,s_4) | いろいろ | 決して到達しませんでした |

 到達できるのは 1 人の生徒だけですが、4 人の生徒は交互のツリーの外に残ります。 その結果、構築は次のように出力します。`-1`。 

その理由は、選択したマッチングの偶然ではなく、構造的なものです。 生徒 (s_1、s_2、s_3、s_4) と教師はグラフの別の部分を形成しますが、(s_5) は教師 (5) と (6) に限定されます。 強化されたホール条件に必要な追加の教師が、より大きな生徒セットでは欠落しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O(M\sqrt N)) | ホップクロフト・カープは一致するものを見つけます。 残りのグラフ検索とサブツリー計算は線形です。 
| スペース | (O(M+N)) | 隣接リスト、一致する配列、ツリー配列、および答えはすべて線形サイズです。 

(N\le10^5) と (M\le2\cdot10^5) を使用すると、線形部分は容易に十分に小さくなります。 マッチングは唯一の非線形成分であり、(O(M\sqrt N)) がこの問題の意図された範囲です。 元のコンテストの議論では、意図されたアプローチとして (O(E\sqrt V)) 二部マッチングが明示的に特定されています。 

## テストケース

 出力は一意ではないため、テスト ハーネスは出力を 1 つの特定の割り当てと比較するのではなく、検証する必要があります。 以下のバリデータは、出力されたすべての値が入力エッジに属していること、すべての値が非負であること、すべての生徒が正確に (N) 個の花を送信し、すべての教師が正確に (N-1) 個を受信して​​いることをチェックします。```python
# Run this block after defining solve() from the solution above.

import sys
import io
from contextlib import redirect_stdout

def run(inp: str) -> str:
    old_stdin = sys.stdin
    sys.stdin = io.StringIO(inp)

    out = io.StringIO()
    with redirect_stdout(out):
        solve()

    sys.stdin = old_stdin
    return out.getvalue().strip()

def check_feasible(inp: str, out: str):
    data = list(map(int, inp.split()))
    it = iter(data)

    n = next(it)
    m = next(it)

    edges = []
    for _ in range(m):
        s = next(it) - 1
        t = next(it) - 1
        edges.append((s, t))

    assert out != "-1", "expected a feasible assignment"

    values = list(map(int, out.split()))
    assert len(values) == m

    student_sum = [0] * (n - 1)
    teacher_sum = [0] * n

    for value, (s, t) in zip(values, edges):
        assert value >= 0
        student_sum[s] += value
        teacher_sum[t] += value

    assert student_sum == [n] * (n - 1)
    assert teacher_sum == [n - 1] * n

sample1 = """\
6 12
1 3
1 4
1 5
2 2
2 4
3 1
3 3
4 1
4 2
4 4
5 4
5 6
"""

sample2 = """\
6 12
1 2
1 3
1 4
2 2
2 4
3 1
3 3
4 1
4 2
4 4
5 5
5 6
"""

check_feasible(sample1, run(sample1))
assert run(sample2) == "-1", "sample 2"

# Custom 1: minimum-size feasible instance.
case1 = """\
2 2
1 1
1 2
"""
check_feasible(case1, run(case1))

# Custom 2: minimum-size impossible instance because one teacher is isolated.
case2 = """\
2 1
1 1
"""
assert run(case2) == "-1", "isolated teacher"

# Custom 3: a chain-like instance that exercises nested subtree sizes.
case3 = """\
3 4
1 1
1 2
2 2
2 3
"""
check_feasible(case3, run(case3))

# Custom 4: maximum-size boundary test.
# N = 100000, M = 199998. Student i can use teachers i and i+1.
n = 100000
lines = [f"{n} {2 * (n - 1)}"]
for s in range(1, n):
    lines.append(f"{s} {s}")
    lines.append(f"{s} {s + 1}")
case4 = "\n".join(lines)

check_feasible(case4, run(case4))

print("all tests passed")
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | サンプル 1 | 12 個の値を持つ有効な代入 | 通常の実行可能な構築とサブツリー フロー |
 | サンプル 2 |`-1`| 一致するが有効な花の割り当てがないグラフ |
 |`2 2 / 1 1 / 1 2`| それぞれの端に 1 つの花 | 写真 最小実現可能 (N=2) ケース |
 |`2 1 / 1 1`|`-1`| 孤立した教師と境界の処理 |
 |`3 4 / 1 1 / 1 2 / 2 2 / 2 3`| 有効な割り当て | 教師フロー内のネストされた交互ツリーとオフバイワン |
 | 生成された (N=100000) チェーン | 199998 個の値を持つ有効な割り当て | 最大 (N)、最大スパース スケール、およびパフォーマンス |

 ## 特殊なケース

 孤立した教師は、一致しない固有の教師である場合にのみルートになれるため、交互検索中に処理されます。 別の教師が孤立している場合、代替ツリーがすべての教師に到達する間、マッチングはすべての生徒をカバーできません。 のために```
2 1
1 1
```マッチングでは生徒 1 と教師 1 がカバーされ、教師 2 はルートとして残ります。 ルートには一致しない発信エッジがないため、スチューデント 1 に到達することはありません。 アルゴリズムは出力します`-1`。 

孤立した生徒はさらに早くに拒否されます。 と```
3 1
1 1
```生徒は 2 人ですが、一致するサイズは 1 人だけです。 マッチングはすべての生徒をカバーしていないため、アルゴリズムは次のように出力します。`-1`交互ツリーを構築する前に。 

すべての生徒をカバーするマッチングではまだ不十分な場合があります。 サンプル 2 では、マッチングのサイズは 5 ですが、一致しない教師をルートとして選択すると、生徒 5 を含むコンポーネントにのみ到達します。検索は 5 人全員ではなく 1 人の生徒に到達するため、アルゴリズムはグラフを拒否します。 まさに、通常のホールマッチングを超えた追加条件です。 

最低限実現可能なケース```
2 2
1 1
1 2
```生徒が 1 人、教師が 2 人います。 割り当てられる唯一の割り当ては、各教師に 1 本の花です。 一致すると 1 人の教師が一致せず、根は一致しないエッジを通って生徒に到達し、2 つの木のエッジの両方に 1 つの花が咲きます。 式では、`N - 1 - size[teacher_subtree] = 1`。 

チェーンケース```
3 4
1 1
1 2
2 2
2 3
```ツリーはスターではなくネストされているため便利です。 ルート教師は生徒 2 に到達し、その教師が生徒 2 に到達し、その教師が生徒 1 に到達します。生徒 1 の教師はリーフです。 スチューデントのサブツリーのサイズは、スチューデント 2 では (2)、スチューデント 1 では (1) になります。その結果、ルートからスチューデントへのエッジは (2) フラワーを取得し、スチューデント 2 は一致する教師に (1) フラワーを送信し、スチューデント 1 は一致する教師に (2) フラワーを送信します。 各生徒は正確に (3) を送信し、各教師は正確に (2) を受信します。 

ゼロフローエッジには特別な処理は必要ありません。 交互ツリーが選択されると、そのツリーの外側のすべての入力エッジは単純にゼロのままになります。 この構造では、単にエッジが存在するという理由だけで、エッジを介して正の流れを強制しようとすることはありません。 

最大サイズの場合は、マッチング実装と線形構造を同時に強調します。 (N=100000) と (M=199998) の場合、グラフは次のようになります。```
s -> t_s
s -> t_{s+1}
```for (1\le s<N) は長いチェーンを形成します。 反復マッチングとツリー走査により再帰深度の失敗が回避され、サブツリーの計算では頂点とエッジの数が線形のままになります。
