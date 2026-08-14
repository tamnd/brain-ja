---
title: "CF 102375J - \u041f\u043e\u0440\u0442\u0430\u043b\u044b"
description: "迷路は (N 倍 M) のグリッドです。 セルは通常の自由空間、ガラスの壁、または固体の壁のいずれかです。 通常の移動は隣接するフリーセル間でのみ可能です。 ガラスは動きを妨げますが、ポータルショットは通過できます。 外側の境界は完全な壁で構成されています。"
date: "2026-08-14T13:19:44+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102375
codeforces_index: "J"
codeforces_contest_name: "\u041a\u0432\u0430\u043b\u0438\u0444\u0438\u043a\u0430\u0446\u0438\u043e\u043d\u043d\u044b\u0439 \u0440\u0430\u0443\u043d\u0434 \u0427\u0435\u043c\u043f\u0438\u043e\u043d\u0430\u0442\u0430 \u0421\u0435\u0432\u0435\u0440\u043e-\u0417\u0430\u043f\u0430\u0434\u0430 \u0420\u043e\u0441\u0441\u0438\u0438 \u0438 \u041c\u043e\u0441\u043a\u0432\u044b ICPC 2019"
rating: 0
weight: 102375
solve_time_s: 866
verified: false
draft: false
---

[CF 102375J - \u041f\u043e\u0440\u0442\u0430\u043b\u044b](https://codeforces.com/problemset/problem/102375/J)

 **評価:** -
 **タグ:** -
 **解決時間:** 14 分 26 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 迷路は (N \times M) のグリッドです。 セルは通常の自由空間、ガラスの壁、または固体の壁のいずれかです。 通常の移動は隣接するフリーセル間でのみ可能です。 ガラスは動きを妨げますが、ポータルショットは通過できます。 外側の境界は完全な壁で構成されています。 

ポータルは固体壁セルの片側に取り付けられています。 オレンジとブルーの 2 色があり、各色のポータルは一度に 1 つだけ存在できます。 射撃すると、その色の古いポータルが置き換えられます。 ある方向にショットが行われると、光線は最も近い固体の壁に到達するまで継続します。 ガラス細胞は光線を止めません。 ポータルは、射手側の壁の側に配置されます。 

ポータルは、その側面がフリー セルに隣接している場合にのみ役立ちます。 対応する面がガラスである場合、ポータルへの出入りは不可能または致命的となるため、そのようなポータルは有効なルートに参加できません。 

興味深いのは、2 つのポータルの位置が永続的なことです。 1 つのポータルを使用した後、もう 1 つのポータルは元の場所に残ります。 これにより、1 つの追加ショットを使用して 1 つの色を新しい目的地ポータルに置き換え、次に他の色を入力して新しい目的地にテレポートすることができます。 

通常の移動においては最短ルートを求めた出力ではありません。 主な目的はショット数を最小限に抑えることです。 その最小数のショットを持つすべてのソリューションの中で、最大 (2NM) の移動ステップを持つルートが受け入れられます。 この問題では (N,M\le1000) が許可されるため、最大 (10^6) 個のセルが存在する可能性があります。 セル数に二次または三次依存するアルゴリズムはすでに大きすぎますが、(O(NM)) 解が自然な目標です。 元のコンテストの制限は 2 秒および 512 MiB です。 

最初のエッジ ケースは、開始と終了が同じ通常の接続コンポーネント内にある場合です。 例えば、```
3 3
WWW
W.W
WWW
2 2
2 2
```正しい出力は次のとおりです```
0 0
```アクションはまったくありません。 通常の移動を考慮する前に常に 2 つのポータルを構築するソリューションでは、不必要に銃が使用されてしまいます。 

2 番目のエッジ ケースは、固体壁の直前のガラス セルです。 考慮する```
5 5
WWWWW
W.GWW
WWWWW
W...W
WWWWW
2 2
4 2
```開始と終了は別のコンポーネントにあります。 最初から射撃するとガラスセルの先にある固体の壁に到達しますが、ポータルはガラスセルの隣に設置されているため安全に使用することはできません。 他の方向への射撃は、開始コンポーネントの周囲の固体の壁にのみ到達します。 正しい出力は次のとおりです`-1 -1`。 ガラス越しに見えるすべての壁を使用可能な目的地として扱う不用意な実装は、2 つのコンポーネントが接続できると誤って主張することになります。 

3 番目のエッジ ケースは、同じ通常のコンポーネントをターゲットとするポータルです。 完全に開いた内部では、境界壁に向かってショットするとポータルが作成されることがありますが、ルートに関連する両側は通常の移動によってすでに接続されています。 このようなポータルは進行状況としてカウントしてはなりません。 考えられるすべてのショットをグラフのエッジとして扱うと、無駄な自己ループが多数作成されてしまいます。 

4 番目のエッジ ケースは、コンポーネントにフリー セルが含まれていても、固体の壁に直接隣接するフリー セルがない場合です。 このようなコンポーネントは、ガラスを突き抜けて別の場所にポータルを作成することがありますが、それ自体で使用可能なソース ポータルを配置することはできません。 したがって、テレポートの現在の側として使用することはできません。 この区別は、実際にどのグラフのエッジを通過できるかを決定する際に重要になります。 

## アプローチ

 直接的なブルート フォース モデルは、完全な物理状態から開始されます。 状態には、現在のフリー セルと両方の色付きポータルの位置が含まれている必要があります。 1 つのポータルに関連する壁面は (O(NM)) 個ある可能性があるため、ポータルのペアはすでに (O((NM)^2)) 個の構成を与えています。 現在の位置を乗算すると、(O((NM)^3)) 個の可能な状態が得られます。 (NM=10^6) の場合、これは最悪の場合でも (10^{18}) 状態程度になります。 状態ごとに 1 バイトを保存することさえ不可能であるため、完全な状態空間検索は実行可能なアプローチではありません。 

重要な観察は、通常の動きにより、ショットを数えるときに個々のセルを区別する必要が完全になくなることです。 フリー セルの 1 つの接続されたコンポーネント内では、銃を発砲せずに他のセルに移動できます。 唯一の意味のある遷移は、ある自由空間コンポーネントから別の空き空間コンポーネントへのテレポートです。 

固体の壁を含まない水平または垂直の一連のセルを考えてみましょう。 その右端点の後に固体の壁があり、その壁の直前のセルが空いていると仮定します。 シーケンス内のすべてのフリー セルは壁に向かって発射できます。 結果として得られるポータルは、その最後の自由セルに隣接する壁側に配置されます。 射手と壁の間のガラスセルは問題ではありません。 したがって、そのシーケンスで表されるすべてのフリー コンポーネントは、使用可能なエンドポイントが最後のフリー セルを含むコンポーネントに属するポータルを作成できます。 

これにより、頂点が自由セルの接続されたコンポーネントである有向グラフが得られます。 コンポーネント (A) のセルが固体の壁に向かって発射でき、その壁の射手側がコンポーネント (B) の自由セルに隣接している場合、エッジ (A\to B) が存在します。 コンポーネント (B) は、新しく作成されたポータルの宛先となる可能性があります。 

方向性は重要です。 (A) が (B) に属する使用可能なポータル側を確認できる場合、(A) からその宛先ポータルを作成できます。 新しいショット 1 つでは逆トランジションが不可能な場合があります。 

ポータルの色は、このグラフで十分である理由を説明しています。 どちらの色も最初はポータルを持たないため、最初のテレポートには 2 ショットが必要です。 1 つの色を現在のコンポーネント内の使用可能な壁面に配置し、もう 1 つの色をグラフの端で示される移動先に配置します。 テレポート後、1 つのポータルは現在のコンポーネント内にあり、もう 1 つは新しいコンポーネント内にあります。 別のコンポーネントに到達するには、1 回のショットのみが必要です。 まだ後ろにあるポータルを次の目的地にある新しいポータルに置き換えて、現在のコンポーネントに残っているポータルを入力します。 

したがって、(K>0) の場合、(K) 個のグラフ エッジを使用するルートには正確に (K+1) 個のショットが必要です。 ゼロ エッジを使用するルートには、ゼロ ショットが必要です。 したがって、ショット数を最小限に抑えることは、コンポーネント グラフで最短の有向パスを見つけることと同じです。

考えられるすべての光線を明示的に検索する必要はありません。 2 つの固体壁の間の水平セグメントは、射撃方向ごとに 1 つずつ、合計 2 つのスイープで処理できます。 垂直セグメントも同様に処理されます。 各フリーセルは一定数の操作に参加するため、完全なグラフを線形時間で構築できます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | 完全な状態空間検索 | (O((NM)^3)) 状態 | (O((NM)^3)) | 遅すぎる |
 | 最適成分グラフ | (O(NM)) | (O(NM)) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 接続されているすべてのコンポーネントにラベルを付けます`.`BFSを使用したセル。 2 つのフリー セルが交差せずに通常の移動を使用して接続できる場合、それらは正確に同じコンポーネントに属します。`G`または`W`。 

コンポーネントを検出するときは、固体の壁に直接隣接する 1 つのフリー セルとその壁の方向を覚えておいてください。 このようなセルは、コンポーネントがテレポートを実行する必要があるときはいつでも、ソース ポータルの有効な場所を提供します。 
2. すべての行をスキャンして有向成分グラフを構築します。 

固定行の場合は、何も含まれない最大間隔を調べます。`W`。 右側の固体壁の直前のセルがフリーの場合、その間隔内のすべてのフリー セルは右に発射し、その最後のフリー セルのコンポーネントに属する使用可能なポータルを作成できます。 各シューターのコンポーネントからそのターゲット コンポーネントにエッジを追加します。 

同じスキャンを左から右に繰り返して、左向きのエッジを取得します。 
3. すべての列に対して同様の 2 つのスイープを実行します。 

下向きのスイープは、壁が射手の下にあるポータルを処理します。 上向きスイープは、壁が射手の上にあるポータルを処理します。 

ガラスセルは掃引を決して停止しません。 重要なのは、固体壁にすぐ隣接するセルが空いているかどうかだけです。 もしその細胞が`G`の場合、ポータルの使用可能な側にはガラスの壁があり、エッジとして追加してはなりません。 
4. ソースコンポーネントとデスティネーションコンポーネントが等しいグラフエッジを無視します。 

このようなコンポーネントでは、通常の動きによってすでにセルのすべてのペアが接続されているため、ポータルではショット数を改善できません。 
5. 開始コンポーネントから有向コンポーネント グラフに対して BFS を実行します。 

グラフの各エッジは、新しいコンポーネントへの 1 つのテレポートを表します。 すべてのエッジのコストは等しいため、通常の BFS では最小限のテレポート数が得られます。 先行コンポーネントと、検出されたすべてのエッジの正確な撮影セルと方向を保存します。 これらの証人は、後で実際の行動を再現するために必要になります。 
6. exit コンポーネントに到達できない場合は、次のように出力します。`-1 -1`。 

コンポーネント グラフは、新しい使用可能なポータル宛先を導入するためのあらゆる可能な方法を説明します。 このグラフで出口コンポーネントに到達できない場合は、ポータル ショットを連続して実行しても到達できません。 
7. start と exit が同じコンポーネント内にある場合、それらのセル間の通常のパスを再構築し、ゼロ ショットで出力します。 
8. それ以外の場合は、BFS によって見つかったコンポーネントのシーケンスを再構築します。 

最初のグラフ エッジがシューター セル (u)、方向 (d) を使用し、セル (v) に到着すると仮定します。 最初のポータルの位置として、開始コンポーネントの記憶されている壁に隣接するセル (q) を選択します。 スタートから(q)まで歩き、そこに青いポータルを配置し、(u)まで歩き、(d)の方向でオレンジのポータルを配置し、(q)に戻り、青いポータルに移動します。 テレポートは (v) に進みます。 

2 発のショットが使用されました。これは、まさに最初のテレポートに必要なコストです。 
9. 以降のグラフ エッジごとに、現在のコンポーネントにすでに 1 つのポータルが存在します。 現在の到着セルから射撃目撃者 (u) まで歩き、次の目的地に向かって他の色を撃ち、古いポータルに戻り、そこに入ります。 

新しいポータルは次のコンポーネントにあります。 これにはちょうど 1 ショットの費用がかかります。 
10. 出口コンポーネントに到着したら、到着セルから出口まで通常通り歩きます。 

すべてのコンポーネントに対して、そのコンポーネント内の単純な BFS パスを使用します。 各コンポーネントは最短コンポーネント パス上で最大 1 回発生するため、通常の歩行の総量は必要な (2NM) 境界内に収まります。 

### なぜ効果があるのか

 不変条件は、各グラフ遷移の直前に、現在のコンポーネントの使用可能な壁側に 1 つのポータルが配置されることです。 他のポータルは無関係であるか、次のショットに置き換えられています。 保存されたグラフのエッジは、新しいポータルが次のコンポーネントで使用可能な壁側に配置されるように、どこで撮影すればよいかを正確に示します。 次に、既存のポータルに戻り、それを横切り、新しいコンポーネントに到達します。 

最初は両方の色が存在しないため、最初のテレポートには必ず 2 ショットが必要です。 ポータルの位置を変更しない限り、目的地のペアは新しいコンポーネントを取得できないため、新しく到達したコンポーネントへのその後のテレポートには少なくとも 1 つの新しいショットが必要です。 逆に、この構築では、後続のグラフ エッジごとに 1 つの新しいショットが使用されます。 したがって、(K) 個のエッジを持つ最短のグラフ パスにより、可能な最小の (K+1) ショットが得られます。 

グラフには、有用なテレポート遷移が正確に含まれています。 トランジションは、目的の壁側がフリー セルまたはガラス セルを通して表示され、その直接のセルがフリーの場合に正確に作成できます。 4 つのスイープはまさにそれらの状況を列挙しています。 したがって、このグラフの BFS は、必要な最適値である新しいポータル宛先の可能な最小限の数を見つけます。 

## Python ソリューション```python
import sys
from collections import deque
from array import array

input = sys.stdin.readline

# Direction codes:
# 0 = U, 1 = D, 2 = L, 3 = R
DR = (-1, 1, 0, 0)
DC = (0, 0, -1, 1)
DIR_CHARS = b"UDLR"
OPPOSITE = (1, 0, 3, 2)

def solve():
    n, m = map(int, input().split())
    g = [input().strip() for _ in range(n)]

    sr, sc = map(int, input().split())
    er, ec = map(int, input().split())
    sr -= 1
    sc -= 1
    er -= 1
    ec -= 1

    total = n * m
    start = sr * m + sc
    finish = er * m + ec

    # Component id of every cell, -1 for walls and glass.
    comp = array('i', [-1]) * total

    # One usable portal position for each component.
    portal_cell = array('i')
    portal_dir = bytearray()

    component_count = 0
    q = deque()

    for r in range(1, n - 1):
        row = g[r]
        for c in range(1, m - 1):
            pos = r * m + c
            if row[c] != '.' or comp[pos] != -1:
                continue

            cid = component_count
            component_count += 1
            portal_cell.append(-1)
            portal_dir.append(0)

            comp[pos] = cid
            q.clear()
            q.append(pos)

            while q:
                p = q.popleft()
                pr = p // m
                pc = p - pr * m

                # Find one free cell with a solid wall next to it.
                if portal_cell[cid] == -1:
                    if g[pr - 1][pc] == 'W':
                        portal_cell[cid] = p
                        portal_dir[cid] = 0
                    elif g[pr + 1][pc] == 'W':
                        portal_cell[cid] = p
                        portal_dir[cid] = 1
                    elif g[pr][pc - 1] == 'W':
                        portal_cell[cid] = p
                        portal_dir[cid] = 2
                    elif g[pr][pc + 1] == 'W':
                        portal_cell[cid] = p
                        portal_dir[cid] = 3

                np = p - m
                if g[pr - 1][pc] == '.' and comp[np] == -1:
                    comp[np] = cid
                    q.append(np)

                np = p + m
                if g[pr + 1][pc] == '.' and comp[np] == -1:
                    comp[np] = cid
                    q.append(np)

                np = p - 1
                if g[pr][pc - 1] == '.' and comp[np] == -1:
                    comp[np] = cid
                    q.append(np)

                np = p + 1
                if g[pr][pc + 1] == '.' and comp[np] == -1:
                    comp[np] = cid
                    q.append(np)

    start_comp = comp[start]
    finish_comp = comp[finish]

    # If ordinary movement is already enough, construct that path later.
    # Otherwise build the component graph.
    head = array('i', [-1]) * component_count
    to = array('i')
    nxt = array('i')
    witness = array('i')
    target_cell = array('i')
    edge_dir = bytearray()

    def add_edge(a, b, u, v, d):
        if a == b:
            return
        idx = len(to)
        to.append(b)
        witness.append(u)
        target_cell.append(v)
        edge_dir.append(d)
        nxt.append(head[a])
        head[a] = idx

    # Horizontal edges: shooting right.
    for r in range(1, n - 1):
        target = -1
        base = r * m
        for c in range(m - 1, 0, -1):
            ch = g[r][c]
            if ch == 'W':
                if g[r][c - 1] == '.':
                    target = base + c - 1
                else:
                    target = -1
            elif ch == '.' and target != -1:
                u = base + c
                add_edge(comp[u], comp[target], u, target, 3)

    # Horizontal edges: shooting left.
    for r in range(1, n - 1):
        target = -1
        base = r * m
        for c in range(0, m - 1):
            ch = g[r][c]
            if ch == 'W':
                if g[r][c + 1] == '.':
                    target = base + c + 1
                else:
                    target = -1
            elif ch == '.' and target != -1:
                u = base + c
                add_edge(comp[u], comp[target], u, target, 2)

    # Vertical edges: shooting down.
    for c in range(1, m - 1):
        target = -1
        for r in range(n - 1, 0, -1):
            ch = g[r][c]
            if ch == 'W':
                if g[r - 1][c] == '.':
                    target = (r - 1) * m + c
                else:
                    target = -1
            elif ch == '.':
                if target != -1:
                    u = r * m + c
                    add_edge(comp[u], comp[target], u, target, 1)

    # Vertical edges: shooting up.
    for c in range(1, m - 1):
        target = -1
        for r in range(0, n - 1):
            ch = g[r][c]
            if ch == 'W':
                if g[r + 1][c] == '.':
                    target = (r + 1) * m + c
                else:
                    target = -1
            elif ch == '.' and target != -1:
                u = r * m + c
                add_edge(comp[u], comp[target], u, target, 0)

    # BFS on the component graph.
    parent_comp = array('i', [-1]) * component_count
    parent_edge = array('i', [-1]) * component_count

    parent_comp[start_comp] = start_comp
    cq = deque([start_comp])

    while cq:
        a = cq.popleft()

        if a == finish_comp:
            break

        # A component without a free cell directly adjacent to W
        # cannot serve as the source of a usable portal.
        if portal_cell[a] == -1:
            continue

        e = head[a]
        while e != -1:
            b = to[e]
            if parent_comp[b] == -1:
                parent_comp[b] = a
                parent_edge[b] = e
                cq.append(b)
            e = nxt[e]

    if parent_comp[finish_comp] == -1:
        print("-1 -1")
        return

    # Temporary arrays for paths inside ordinary components.
    cell_parent = array('i', [-1]) * total

    def get_path(a, b, cid):
        """Return direction codes of a shortest ordinary path a -> b."""
        if a == b:
            return []

        bfsq = [a]
        visited = [a]
        cell_parent[a] = a
        qi = 0

        while qi < len(bfsq):
            p = bfsq[qi]
            qi += 1

            if p == b:
                break

            r = p // m
            c = p - r * m

            np = p - m
            if comp[np] == cid and cell_parent[np] == -1:
                cell_parent[np] = p
                bfsq.append(np)
                visited.append(np)

            np = p + m
            if comp[np] == cid and cell_parent[np] == -1:
                cell_parent[np] = p
                bfsq.append(np)
                visited.append(np)

            np = p - 1
            if comp[np] == cid and cell_parent[np] == -1:
                cell_parent[np] = p
                bfsq.append(np)
                visited.append(np)

            np = p + 1
            if comp[np] == cid and cell_parent[np] == -1:
                cell_parent[np] = p
                bfsq.append(np)
                visited.append(np)

        path = []
        cur = b
        while cur != a:
            p = cell_parent[cur]
            delta = cur - p
            if delta == -m:
                path.append(0)
            elif delta == m:
                path.append(1)
            elif delta == -1:
                path.append(2)
            else:
                path.append(3)
            cur = p

        path.reverse()

        for v in visited:
            cell_parent[v] = -1

        return path

    # Reconstruct component path and corresponding graph edges.
    components = []
    edges = []

    cur = finish_comp
    while cur != start_comp:
        components.append(cur)
        e = parent_edge[cur]
        edges.append(e)
        cur = parent_comp[cur]

    components.append(start_comp)
    components.reverse()
    edges.reverse()

    actions = bytearray()
    shots = 0
    steps = 0

    def add_move(d):
        nonlocal steps
        actions.extend((77, DIR_CHARS[d], 10))
        steps += 1

    def add_shot(color, d):
        nonlocal shots
        actions.extend((color, DIR_CHARS[d], 10))
        shots += 1

    if not edges:
        path = get_path(start, finish, start_comp)
        for d in path:
            add_move(d)

        out = bytearray()
        out.extend(f"{shots} {steps}\n".encode())
        out.extend(actions)
        sys.stdout.buffer.write(out)
        return

    # First teleport.
    first_edge = edges[0]
    first_comp = start_comp

    q_cell = portal_cell[first_comp]
    q_dir = portal_dir[first_comp]

    u = witness[first_edge]
    v = target_cell[first_edge]
    d = edge_dir[first_edge]

    # Move to the source portal position.
    path = get_path(start, q_cell, first_comp)
    for x in path:
        add_move(x)

    # Blue is the initial source portal.
    add_shot(ord('B'), q_dir)

    # Move to the shooting position for the destination portal.
    path = get_path(q_cell, u, first_comp)
    for x in path:
        add_move(x)

    # Orange becomes the destination portal.
    add_shot(ord('O'), d)

    # Return to the blue portal.
    for x in reversed(path):
        add_move(OPPOSITE[x])

    # Enter the blue portal and arrive at v.
    add_move(q_dir)

    current_cell = v
    current_portal_dir = d
    current_portal_color = ord('O')

    # Remaining teleports.
    for i in range(1, len(edges)):
        e = edges[i]
        cid = components[i]

        u = witness[e]
        v = target_cell[e]
        d = edge_dir[e]

        # Move from the arrival point to the shooting position.
        path = get_path(current_cell, u, cid)
        for x in path:
            add_move(x)

        # Replace the portal of the opposite color.
        new_color = ord('B') if current_portal_color == ord('O') else ord('O')
        add_shot(new_color, d)

        # Return to the existing portal.
        for x in reversed(path):
            add_move(OPPOSITE[x])

        # Enter the existing portal.
        add_move(current_portal_dir)

        current_cell = v
        current_portal_dir = d
        current_portal_color = new_color

    # Finish by ordinary movement.
    final_cid = finish_comp
    path = get_path(current_cell, finish, final_cid)
    for x in path:
        add_move(x)

    out = bytearray()
    out.extend(f"{shots} {steps}\n".encode())
    out.extend(actions)
    sys.stdout.buffer.write(out)

if __name__ == "__main__":
    solve()
```最初のフェーズのラベルのみ`.`細胞。 の`comp`array は Python リストではなくコンパクトな整数配列ですが、セルが 100 万個も存在する可能性があるため、これは重要です。 同じ BFS 中に、コードは固体の壁に隣接する空きセルを記憶します。 これは、テレポート シーケンスの最初のポータルを配置できる場所です。 

グラフの構築では、セルごとに 4 つの最も近い壁の値を保存することを意図的に避けています。 代わりに、各行と列が 2 回スイープされます。 右から左へのスイープ中に硬い壁に遭遇すると、コードはそのすぐ左側にある空きセルを記憶します。 別の固体壁がその壁に向かって発射できるようになる前に、その後のすべてのフリー セル。 左、上、下のスイープは対称です。 

固体壁のすぐ隣のセルの状態をチェックするのは微妙な部分です。 ガラス製のセルは射手と壁の間のどこにでも配置できますが、ポータルに接触するセルは空いていなければなりません。 そうであれば`G`、ポータルを安全に使用できないため、ターゲットは破棄されます。 

コンポーネント BFS には、すべてのコンポーネントに到達するために使用される正確なグラフ エッジが格納されます。 そのエッジには、撮影セル、方向、目的地のセルが含まれます。 したがって、グラフ検索は単に遷移が存在することを知らせるだけでなく、対応するグラフを出力するのに十分な幾何学的情報も提供します。`O`または`B`アクション。 

の`get_path`関数は、1 つのコンポーネントに限定された通常の BFS を実行します。 これは、最終コンポーネント パスに現れるコンポーネントに対してのみ呼び出されます。 コンポーネント内の単純なパスにはセルの数が少ないため、合計作業量は線形のままです。 先行配列は、毎回 100 万要素の構造を再構築するのではなく、その BFS が接触したセルに対してのみリセットされます。 

出力は、`bytearray`。 有効なソリューションには最大数百万のアクションを含めることができるため、すべてのアクションを個別の Python 文字列として保存すると、不必要なオブジェクトのオーバーヘッドが発生します。 バイト表現はコンパクトなので、最後に直接書き込むことができます。 

最初のテレポート後、ポータルの色が交互に表示されます。 最初の青いポータルが送信元で、オレンジ色が最初の送信先です。 その目的地に到達すると、オレンジ色が現在のコンポーネントで現在利用可能なポータルになります。 次のショットでは青い目的地が作成され、その後オレンジ色のポータルが使用されます。 この交互パターンはまさに、その後のテレポートのコストを 1 ショットにするものです。 

通常の動きの境界は、この構造から導き出されます。 最終ではない各コンポーネント内では、ルートはエントリ セルからシューティング セルまで往復し、そのコンポーネント内のセル数の最大 2 倍のコストがかかります。 最後のコンポーネントは 1 回だけ横断されます。 各ポータルに入ると移動アクションが 1 つ追加され、その合計は最大 (2NM) になります。 

## 実用的な例

 ### サンプル 1

 自由セルは 2 つの通常のコンポーネントに分割されます。 開始コンポーネントには以下が含まれます`(2,3)`そして`(2,4)`一方、exit コンポーネントには行 4 の空きセルが含まれています。 

列 3 を通る垂直コリドーには、開始コンポーネントと下部コンポーネントの間にガラス セルが含まれています。 その下の固体境界壁のすぐ上にはフリーセルがあるため、最初から撃ち落とすことで、下部コンポーネントに使用可能なポータルを作成できます。 

| アクション | 現在のセル | 既存の便利なポータル | 操作 | 結果 |
 | --- | --- | --- | --- | --- |
 |`OD`|`(2,3)`| なし | ガラス越しにオレンジを下に置きます | オレンジ色は下位コンポーネントに属します |
 |`BL`|`(2,3)`| 下のオレンジ色 | 左側の壁に青を置きます | 青はソースポータル |
 |`ML`|`(2,3)`| 青左 | 青いポータルに入る | 下のコンポーネントにテレポート |
 |`ML`| 下部コンポーネント | 下のオレンジ色 | 通常の動き | 出口に到達 |

 これは最初のテレポートであるため、解決策には 2 つのショットが必要です。 重要な幾何学的な点は、ガラス セルはショットが固体境界に到達するのを妨げず、境界の直前にあるフリー セルにより結果として得られるポータルが使用可能になるということです。 

### サンプル 2

 開始と終了は、異なる通常のコンポーネント内にあります。 コンポーネント グラフには 1 つのテレポートを使用するパスが含まれているため、ショットの最小数は 2 です。 

| フェーズ | コンポーネント | 操作 | 目的 |
 | --- | --- | --- | --- |
 | 1 | 開始 | 通常の動き | 有効な撮影位置に到達する |
 | 2 | 開始 |`OU`| 宛先ポータルの作成 |
 | 3 | 開始 | 通常の動き | ソースポータルにアクセスする |
 | 4 | 開始 |`BR`| ソースポータルの作成 |
 | 5 | 開始 |`M`青いポータルへ | テレポート |
 | 6 | コンポーネントを終了 | 通常の動き | 出口に到達する |

 正確な通常のパスはサンプル出力とは異なる場合があります。 チェッカーは最短の移動ステップ数を必要としません。最大でも (2NM) だけです。 グラフ BFS は、最小ショット数を決定する量であるテレポートの数のみに関係します。 

このサンプルでは、​​ポータルの置き換えが重要である理由も示しています。 最初のテレポートの後、1 つのポータルは古いコンポーネントに残り、もう 1 つは新しいコンポーネントに残ります。 後のショットでは、古いポータルを目的のポータルに置き換えることができ、追加のショットを 1 回だけ別のテレポートできるようになります。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O(NM)) | コンポーネント BFS、4 つのグリッド スイープ、コンポーネント BFS、およびパス再構築は、それぞれ一定数のグリッド セルまたはグラフ エッジのみを処理します。 
| スペース | (O(NM)) | コンポーネント ID、グラフ エッジ、BFS 先行要素、および出力アクション バッファーはすべて、グリッド サイズ内で線形です。 

最大で (10^6) 個のセルが存在します。 グラフの構築では、空きセルごとに一定数の候補エッジのみが作成され、すべてのグラフ検索はセルとエッジの数が線形になります。 コンパクト`array`構造体は、完全な状態空間表現のはるかに大きな Python オブジェクトのオーバーヘッドではなく、入力サイズに比例したメモリ使用量を維持します。 

## テストケース

 出力は一意ではないため、出力文字列全体を比較することは、この問題のテストには役立ちません。 以下のテストでは、代わりに、解決策が到達不能かどうか、最小ショット数、移動制限などの必須プロパティを比較します。 また、印刷されたアクションの数がヘッダーと一致することも検証します。```python
# Save the submitted solution as solution.py.
# The helper imports its solve() function.

import io
import sys

from solution import solve

def run(inp: str) -> str:
    old_stdin = sys.stdin
    old_stdout = sys.stdout
    sys.stdin = io.StringIO(inp)
    sys.stdout = io.StringIO()
    try:
        solve()
        return sys.stdout.getvalue()
    finally:
        sys.stdin = old_stdin
        sys.stdout = old_stdout

def header(out: str):
    first = out.splitlines()[0].split()
    return tuple(map(int, first))

def check_valid_header(inp: str, out: str, expected_p: int):
    lines = out.splitlines()
    p, s = map(int, lines[0].split())

    assert p == expected_p
    assert s >= 0

    n, m = map(int, inp.splitlines()[0].split())
    assert s <= 2 * n * m
    assert len(lines) - 1 == p + s

sample1 = """\
5 5
WWWWW
WW..W
WWGWW
W...W
WWWWW
2 3
4 2
"""

sample2 = """\
7 6
WWWWWW
W..W.W
W.W..W
W.W..W
W.WG.W
W...WW
WWWWWW
2 3
2 5
"""

sample3 = """\
5 5
WWWWW
W.G.W
WW.GW
W.G.W
WWWWW
2 2
4 2
"""

assert header(run(sample1))[0] == 2, "sample 1 must use two shots"
check_valid_header(sample1, run(sample1), 2)

assert header(run(sample2))[0] == 2, "sample 2 must use two shots"
check_valid_header(sample2, run(sample2), 2)

assert header(run(sample3))[0] == 4, "sample 3 must use four shots"
check_valid_header(sample3, run(sample3), 4)

# Minimum-size grid, start equals exit.
minimum_case = """\
3 3
WWW
W.W
WWW
2 2
2 2
"""

assert run(minimum_case) == "0 0\n", "same start and exit need no actions"

# Different components with no usable portal transition.
unreachable_case = """\
5 5
WWWWW
W.GWW
WWWWW
W...W
WWWWW
2 2
4 2
"""

assert run(unreachable_case) == "-1 -1\n", "glass directly before a solid wall must not create an edge"

# Boundary-adjacent free cells, but ordinary movement is already sufficient.
boundary_case = """\
5 5
WWWWW
W...W
W.W.W
W...W
WWWWW
2 2
4 4
"""

out = run(boundary_case)
assert header(out)[0] == 0
check_valid_header(boundary_case, out, 0)

# Maximum-size all-open interior. Everything is one ordinary component.
# The minimum number of shots is zero and a shortest Manhattan path has
# 1994 movement steps.
n = 1000
m = 1000
rows = []
rows.append("W" * m)
for _ in range(n - 2):
    rows.append("W" + "." * (m - 2) + "W")
rows.append("W" * m)

maximum_case = (
    f"{n} {m}\n"
    + "\n".join(rows)
    + "\n2 2\n999 999\n"
)

out = run(maximum_case)
p, s = header(out)
assert p == 0
assert s == 1994
check_valid_header(maximum_case, out, 0)
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | サンプル 1 | (P=2)、有効 (S\le2NM) | ガラスを通した最初のポータル移行 |
 | サンプル 2 | (P=2)、有効 (S\le2NM) | ポータル移行周りの自明ではない通常の動き |
 | サンプル 3 | (P=4)、有効 (S\le2NM) | 複数の連続したワンショットポータルの置き換え |
 |`3 x 3`、開始と終了が等しい |`0 0`| 最小グリッドとゼロショットの場合 |
 | 分離されたコンポーネント`G`前に`W`|`-1 -1`| 危険なポータルを使用可能として扱うことを防ぎます。 
| 境界隣接の普通の迷路 | (P=0) | 境界処理と通常の接続 |
 |`1000 x 1000`オープンインテリア |`P=0`、`S=1994`| 最大グリッド サイズ、メモリ使用量、線形時間動作 |

 ## 特殊なケース

 最小グリッドの場合```
3 3
WWW
W.W
WWW
2 2
2 2
```コンポーネントのラベル付けにより、開始と終了を含む自由なコンポーネントが 1 つだけ作成されます。 2 つのセルは同一であるため、コンポーネント BFS は必要ありません。 パスの再構築では空のパスが返されるため、プログラムは次のように出力します。`0 0`。 

ソリッドウォールの前にガラスを使用する場合```
5 5
WWWWW
W.GWW
WWWWW
W...W
WWWWW
2 2
4 2
```開始コンポーネントには上下の固体壁の隣にフリーセルがありますが、どちらも他のコンポーネントにアクセスできません。 右方向のスイープでは、列 4 の固体壁の直前の列 3 にガラス セルがあります。 その結果、ターゲットは破棄されます。 他の方向は固体壁に到達し、そのポータル側は開始コンポーネントに残ります。 exit コンポーネントはコンポーネント BFS によって決して検出されないため、答えは次のようになります。`-1 -1`。 

同じコンポーネント内に複数の空きセルがあるグリッドの場合、次のようになります。```
5 5
WWWWW
W...W
W.W.W
W...W
WWWWW
2 2
4 4
```コンポーネントにラベルを付けると、すべての空きセルが 1 つのコンポーネントに入れられます。 目に見える境界壁によって生成されるグラフ エッジはすべて自己エッジであり、無視されます。 通常のパスで十分なので、結果はショットがゼロになります。 これにより、グラフ構築時に可能なポータル配置と必要なポータル遷移が混同されるのを防ぎます。 

ガラスで囲まれたコンポーネントの場合、ショットはガラスを通過する可能性があるため、コンポーネントは可視性スイープで射手として表示される場合があります。 ただし、コンポーネントに固体壁に直接隣接するフリーセルがない場合は、`portal_cell[cid]`滞在します`-1`。 その後、BFS はそのコンポーネントをテレポートのソースとして使用することを拒否します。 これは物理的なルールに一致します。コンポーネントは起動できますが、プレイヤーがそこから抜けることができる安全なポータルがありません。 

(1000\times1000) グリッドと開いた内部を持つ最大サイズのケースでは、内部全体が 1 つのコンポーネントになります。 からの通常の最短経路`(2,2)`に`(999,999)`には (997+997=1994) ステップがあるため、プログラムはゼロショットと 1994 移動ステップを返します。 グラフの構築では、依然として 100 万個のセルにわたって一定回数だけスキャンされており、線形定式化が制約に適合する理由がわかります。
