---
title: "CF 102375J - \u041f\u043e\u0440\u0442\u0430\u043b\u044b"
description: "迷路は (N 倍 M) のグリッドです。 セルは自由であるか、固体壁 W によって占有されているか、またはガラス壁 G によって占有されています。通常の移動は、隣接する自由セル間でのみ可能です。 外側の境界は固体の壁で構成されているため、すべての光線は最終的に固体の壁に到達します。"
date: "2026-08-15T18:29:27+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102375
codeforces_index: "J"
codeforces_contest_name: "\u041a\u0432\u0430\u043b\u0438\u0444\u0438\u043a\u0430\u0446\u0438\u043e\u043d\u043d\u044b\u0439 \u0440\u0430\u0443\u043d\u0434 \u0427\u0435\u043c\u043f\u0438\u043e\u043d\u0430\u0442\u0430 \u0421\u0435\u0432\u0435\u0440\u043e-\u0417\u0430\u043f\u0430\u0434\u0430 \u0420\u043e\u0441\u0441\u0438\u0438 \u0438 \u041c\u043e\u0441\u043a\u0432\u044b ICPC 2019"
rating: 0
weight: 102375
solve_time_s: 1543
verified: false
draft: false
---

[CF 102375J - \u041f\u043e\u0440\u0442\u0430\u043b\u044b](https://codeforces.com/problemset/problem/102375/J)

 **評価:** -
 **タグ:** -
 **解決時間:** 25 分 43 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 迷路は (N \times M) のグリッドです。 セルは自由であるか、固体の壁によって占有されているかのいずれかです`W`、またはガラスの壁で占有されている`G`。 通常の移動は隣接するフリーセル間でのみ可能です。 外側の境界は固体の壁で構成されているため、すべての光線は最終的に固体の壁に到達します。 

ポータル ショットは、最初の固体の壁にぶつかるまで、4 つのグリッド方向のいずれかに移動します。 ガラスの壁がショットを止めることはありません。 ポータルは、射手側の壁の側に配置されます。 重要な結果は、ポータルの有用な側がその固体壁の前の最後のセルであるということです。 そのセルが空いていれば、そこからポータルに安全に入ることができます。 そのセルがガラスである場合、反対側からポータルに入ると、旅行者はガラスの壁の中に取り残されてしまうため、そのようなポータルは有効なソリューションでは使用できません。 

ポータルの色は 2 つあります。 各色には最大でも 1 つのポータルがあり、通常、その色を撃つと以前のポータルが置き換えられます。 ポータルへの移動は 1 つとしてカウントされます`M`action, but after entering it the traveler appears at the other portal immediately.

 The input gives the grid and two free cells, the start (S) and the exit (E). The output must contain a valid sequence of shots and movements. 主な目的は、ショット数 (P) を最小限に抑えることです。 Among solutions with that minimum (P), the number of movement actions only has to stay within (2NM). The official statement gives (N,M\le 1000), a 2 second limit, and 512 MiB of memory.

 (N,M\le1000) の場合、(10^6) 個のセルが存在できます。 これにより、状態空間に 3 次状態空間はもちろんのこと、2 次数のグリッド セルさえも含まれるアルゴリズムが除外されます。 A linear or near-linear traversal of the grid is appropriate. The output itself can contain (O(NM)) movements, so spending (O(NM)) time and memory is the natural target.

 ### 開始と終了はすでに接続されています

 If (S) and (E) belong to the same connected component of free cells, portals are unnecessary. 正解は (P=0) です。 例えば、```
3 3
WWW
W.W
WWW
2 2
2 2
```出力があります```
0 0
```常に 2 つのポータルを初期化するソリューションは、すでに最適ではありません。 

### ショットはガラスを通過する可能性があります

 サンプル 1 を考えてみましょう。開始セルから下向きに射撃すると、ガラス セルを通過し、固体の壁に到達します。 有用なポータルの終点は、その固体壁の直前の自由セルです。 すぐ隣接する固体壁での射撃のみを許可する単純な実装では、この移行が見逃され、迷路にはさらに射撃が必要であるか、不可能であると誤って報告される可能性があります。 

### ガラスエンドポイントは使用できません

 エンドポイントが`G`cell は有用なテレポート エンドポイントではありません。 治療中`G`射撃とテレポートの両方に対して単に透明であるという考えは間違っています。ポータルを反対側に残すと、旅行者はガラスの壁の中に入れられてしまうからです。 

例えば、```
5 5
WWWWW
W.GGW
WGWGW
WGG.W
WWWWW
2 2
4 4
```2 つの孤立したフリーセルがあります。 どちらからの重要な光線も、固体壁の直前のガラス セルで終わります。 唯一の安全なショットは現在のセル自体を指すため、コンポーネント間のテレポートは不可能です。 正しい出力は次のとおりです```
-1 -1
```ガラス エンドポイントを受け入れる不注意な実装では、パスが誤って検索されてしまいます。 

### ポータルの 2 つの色は交互に使用する必要があります

 テレポート後、現在のエンドポイントのポータルはそこに残ります。 新しいエンドポイントに移動するには、他の色が別の場所で撮影され、その色の古いポータルが置き換えられます。 次に、現在のエンドポイントでまだ存在するポータルに入ると、テレポートが実行されます。 

これが、最初のテレポート以降のすべてのテレポートに 1 回のショットで十分である理由です。 最初はどちらのポータルも存在しないため、最初のテレポートには 2 ショットのコストがかかります。 

## アプローチ

 直接的なブルート フォース ソリューションでは、完全な物理状態がモデル化されます。 このような状態には、プレーヤーの現在のセルと、ポータルがまだ存在しない可能性を含む両方のポータルの位置と方向が含まれます。 有効なポータル位置は (O(NM)) 個あるため、状態の数は (O((NM)^3)) になります。 各状態には最大 4 つの移動アクションと 8 つの射撃アクションがあり、最悪の場合の移行カウントは次のようになります。 

[
 12(NM)(NM+1)^2。 
】

 (NM=10^6) の場合、これは (10^{19}) 回の遷移になります。 ブルート フォースは、考えられるすべての構成を明示的に表すため、概念的には正しいですが、ポータルの位置によって作成される組み合わせが多すぎます。 

重要な観察は、実際には両方のポータルの位置を記憶する必要はないということです。 テレポートが発生すると、現在のセルのポータルの方向は既知となり、他のポータルは次のショットで置き換えることができます。 したがって、次のテレポートに関連する唯一の情報は、現在のコンポーネントと新しいポータルが配置されるセルです。 

フリー セルが少なくとも 1 方向の固体壁の直前にある場合、フリー セルをポータル エンドポイントと呼びます。 セル (x) からある方向に射撃すると、端点 (y) が 1 つだけあります。つまり、その光線上の最初の固体壁の直前のセルです。 (y) が空いている場合、(x) から射撃すると (y) に安全なポータルを作成できます。 

ここで、自由セルの 2 つの異なる連結成分を考えてみましょう。 コンポーネント (A) に端点 (x) があり、そのショットがコンポーネント (B) に安全な端点 (y) を作成する場合、それを有向エッジ (A\to B) として使用できます。 最初のそのようなエッジには 2 ショットかかります。 (B) に到着した後、到着エンドポイントのポータルはすでに存在しているため、別のコンポーネントのエッジに沿って移動する場合の追加のコストは 1 回だけです。 

その結果、この問題は、フリーセルの接続されたコンポーネント上の通常の重み付けされていない最短経路問題になりました。 BFS を使用してこれらのコンポーネントを見つけ、(O(NM)) で考えられるすべてのポータル遷移を生成し、コンポーネント グラフに対して別の BFS を実行します。 

比較は次のとおりです。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | (O((NM)^3)) 状態 | (O((NM)^3)) | 遅すぎる |
 | 最適 | (O(NM)) | (O(NM)) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. グリッドを読み取り、開始セルと終了セルを見つけます。 治療のみ`.`セルは歩くことができます。 ガラスの壁と固体の壁はどちらも通常の動きを妨げます。 
2. すべての空きセルに対してフラッド フィルを実行します。 すべての空きセルに接続コンポーネント識別子を割り当てます。 この横断中に、固体の壁に隣接するすべての空きセルにマークを付けます。 このようなセルは、安全なポータル エンドポイントとなり得ます。 
3. すべてのエンドポイントについて、ショットが 4 方向のそれぞれにどのエンドポイントを作成するかを決定します。 たとえば、左方向に最初の`W`セルを左に移動し、すぐにセルを右に移動します。 これらの値は、行全体で 2 回、列全体で 2 回の 4 回の線形スイープで計算できます。 
4. 暗黙的なコンポーネント グラフを構築します。 すべての終点 (x) とすべての方向について、(y) をその方向に射撃したときに到達する終点とします。 (y) がガラスセルである場合、または (x) と (y) が同じコンポーネントに属している場合は、遷移を無視します。 それ以外の場合は、(x)、(y)、撮影方向を記憶しながら、(x) のコンポーネントから (y) のコンポーネントへの有向遷移を追加します。 
5. 開始と終了が同じコンポーネント識別子を持つ場合、(S) から (E) までの通常の BFS パスを見つけて、ゼロ ショットで出力します。 ゼロはショット数の最小値であるため、これ以上最適化する必要はありません。 
6. それ以外の場合は、(S) を含むコンポーネントから開始して、コンポーネント グラフに対して BFS を実行します。 新しく到達したコンポーネントごとに、送信元エンドポイント、宛先エンドポイント、およびそこに到達した射撃方向を保存します。 すべてのコンポーネントの遷移が 1 つの追加テレポートを表し、したがって最初のテレポート後に 1 つの追加ショットを表すため、BFS が適切です。 
7. このグラフで終了コンポーネントに到達できない場合、出力`-1 -1`。 考えられる安全なポータル遷移はすべてエッジとして表現されているため、フリーセル コンポーネント間を移動する方法は残りません。 
8. 最短のコンポーネント パスを再構築します。 その遷移が次のようになっているとします。 

[
 x_0\to y_0、\quad x_1\to y_1、\quad \ldots、\quad x_{k-1}\to y_{k-1}。 
】

 最初のトランジションには 2 つのショットが必要です。 (x_0) から (y_0) に向かってオレンジを撃ち、次に (x_0) から隣接する固体の壁に向かって青を撃ちます。これにより、青いポータルが (x_0) に配置されます。 その青いポータルに入ると (y_0) にテレポートします。 
9. すべての中間コンポーネント内で、前の遷移の宛先 (y_{i-1}) から次の遷移のソース (x_i) まで歩きます。 これらは通常のフリーセルの動きであり、ショットは必要ありません。 
10. その後のトランジション (x_i\to y_i) ごとに、(x_i) で現在存在するポータルの反対の色を撮影します。 次に、(x_i) にある既存のポータルに入ります。 これには、ちょうど 1 回の新しいショットが必要で、旅行者は (y_i) にテレポートされます。 ポータルに入るために使用される方向は、前のトランジションで (x_i) を作成した射撃方向とまったく同じです。 
11. 最後のテレポートの後、目的地のエンドポイントから (E) まで通常どおり歩きます。 
12. 毎回数えます`O`そして`B`ショットとして、そしてすべての`M`動きとして。 ショットの数は (k+1) です。ここで、(k) はコンポーネントの遷移の数です。 

### なぜ効果があるのか

 不変条件は、テレポートのたびに旅行者が最新のショットによって作成されたエンドポイントに立ち、そのエンドポイントのポータルが利用可能なままであることです。 他の色は、次のコンポーネントの遷移に必要なエンドポイントに移動できます。 したがって、コンポーネント グラフのすべての有向エッジは実現可能であり、最初のエッジは 2 ショットのコストがかかり、その後のエッジはすべて 1 ショットのコストがかかります。 

逆に、異なるフリーセル コンポーネント間のすべての安全なテレポートは、入口エンドポイントが固体壁の直前のフリーセルであるポータルを使用する必要があります。 もう一方のエンドポイントを作成するショットは、4 方向のスイープによって生成されるトランジションの 1 つを正確に定義します。 したがって、有効な解決策はすべて、コンポーネント グラフにパスを誘導します。 BFS はそのような遷移の最小数を見つけるため、(k+1) が可能な最小ショット数になります。 

動きの境界もコンポーネントの構造から導き出されます。 最短のコンポーネント パスでは、コンポーネントが繰り返されることはありません。 訪問された各コンポーネント内では、生成されたウォークでは最大 (\text{size(component)}-1) 個の通常の移動が使用されます。 コンポーネントの遷移ごとに 1 つのポータル エントリの動きがあります。 パスが (k+1) 個のコンポーネントを使用する場合、移動の総数は最大で

 \sum\text{サイズ(コンポーネント)}-1
 \le NM-1、
 】

 これは必要な値 (2NM) を十分に下回っています。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

from array import array
from collections import deque

DIRS = "UDLR"
DR = (-1, 1, 0, 0)

def solve_stream(readline):
    n, m = map(int, readline().split())

    rows = []
    grid = bytearray()
    for _ in range(n):
        s = readline().strip().encode()
        rows.append(s)
        grid.extend(s)

    sr, sc = map(int, readline().split())
    er, ec = map(int, readline().split())
    sr -= 1
    sc -= 1
    er -= 1
    ec -= 1

    vcount = n * m
    start = sr * m + sc
    finish = er * m + ec

    # 0 = not a boundary endpoint, 1 = safe portal endpoint.
    boundary = bytearray(vcount)

    # Direction of any solid wall adjacent to an endpoint.
    # Encoding: U=0, D=1, L=2, R=3.
    first_dir = bytearray(vcount)

    # Connected components of free cells.
    comp = array('i', [-1]) * vcount
    component_count = 0

    for i in range(vcount):
        if grid[i] != 46 or comp[i] != -1:
            continue

        cid = component_count
        component_count += 1

        q = deque([i])
        comp[i] = cid

        while q:
            x = q.popleft()
            r = x // m
            c = x - r * m

            is_boundary = False

            if grid[x - m] == 87:
                is_boundary = True
                first_dir[x] = 0
            elif grid[x + m] == 87:
                is_boundary = True
                first_dir[x] = 1
            elif grid[x - 1] == 87:
                is_boundary = True
                first_dir[x] = 2
            elif grid[x + 1] == 87:
                is_boundary = True
                first_dir[x] = 3

            if is_boundary:
                boundary[x] = 1

            y = x - m
            if grid[y] == 46 and comp[y] == -1:
                comp[y] = cid
                q.append(y)

            y = x + m
            if grid[y] == 46 and comp[y] == -1:
                comp[y] = cid
                q.append(y)

            y = x - 1
            if grid[y] == 46 and comp[y] == -1:
                comp[y] = cid
                q.append(y)

            y = x + 1
            if grid[y] == 46 and comp[y] == -1:
                comp[y] = cid
                q.append(y)

    start_comp = comp[start]
    finish_comp = comp[finish]

    # If ordinary movement already reaches the exit, no shot is needed.
    if start_comp == finish_comp:
        seen = bytearray(vcount)
        parent = array('i', [-1]) * vcount
        pdir = bytearray(vcount)

        q = deque([start])
        seen[start] = 1

        while q and not seen[finish]:
            x = q.popleft()

            for d, delta in enumerate((-m, m, -1, 1)):
                y = x + delta
                if grid[y] == 46 and not seen[y]:
                    seen[y] = 1
                    parent[y] = x
                    pdir[y] = d
                    q.append(y)

        path = []
        cur = finish
        while cur != start:
            path.append(pdir[cur])
            cur = parent[cur]
        path.reverse()

        out = ["0 {}".format(len(path))]
        out.extend("M" + DIRS[d] for d in path)
        return "\n".join(out) + "\n"

    # For every boundary cell, these arrays store the endpoint obtained
    # by shooting in the corresponding direction.
    left = array('i', [-1]) * vcount
    right = array('i', [-1]) * vcount
    up = array('i', [-1]) * vcount
    down = array('i', [-1]) * vcount

    # Horizontal sweeps.
    for r in range(n):
        base = r * m
        last_w = -1

        for c in range(m):
            x = base + c
            if grid[x] == 87:
                last_w = c
            elif boundary[x]:
                left[x] = base + last_w + 1

        next_w = m
        for c in range(m - 1, -1, -1):
            x = base + c
            if grid[x] == 87:
                next_w = c
            elif boundary[x]:
                right[x] = base + next_w - 1

    # Vertical sweeps.
    for c in range(m):
        last_w = -1

        for r in range(n):
            x = r * m + c
            if grid[x] == 87:
                last_w = r
            elif boundary[x]:
                up[x] = (last_w + 1) * m + c

        next_w = n
        for r in range(n - 1, -1, -1):
            x = r * m + c
            if grid[x] == 87:
                next_w = r
            elif boundary[x]:
                down[x] = (next_w - 1) * m + c

    # Linked lists of boundary cells, one list per component.
    head = array('i', [-1]) * component_count
    bnext = array('i', [-1]) * vcount

    for x in range(vcount):
        if boundary[x]:
            cid = comp[x]
            bnext[x] = head[cid]
            head[cid] = x

    # BFS over connected components.
    parent_comp = array('i', [-1]) * component_count
    edge_src = array('i', [-1]) * component_count
    edge_dst = array('i', [-1]) * component_count
    edge_dir = bytearray(component_count)

    parent_comp[start_comp] = start_comp
    q = deque([start_comp])

    target_arrays = (up, down, left, right)

    while q and parent_comp[finish_comp] == -1:
        cid = q.popleft()
        x = head[cid]

        while x != -1:
            for d, arr in enumerate(target_arrays):
                y = arr[x]

                if y == -1 or grid[y] != 46:
                    continue

                nc = comp[y]
                if nc == cid or parent_comp[nc] != -1:
                    continue

                parent_comp[nc] = cid
                edge_src[nc] = x
                edge_dst[nc] = y
                edge_dir[nc] = d
                q.append(nc)

                if nc == finish_comp:
                    break

            x = bnext[x]

            if parent_comp[finish_comp] != -1:
                break

    if parent_comp[finish_comp] == -1:
        return "-1 -1\n"

    # Recover component transitions in forward order.
    transitions = []
    cid = finish_comp

    while cid != start_comp:
        transitions.append(
            (edge_src[cid], edge_dst[cid], edge_dir[cid])
        )
        cid = parent_comp[cid]

    transitions.reverse()

    # Local BFS inside one free-cell component.
    seen = array('i', [0]) * vcount
    parent = array('i', [-1]) * vcount
    pdir = bytearray(vcount)
    stamp = 0

    def walk_path(a, b, cid):
        nonlocal stamp

        if a == b:
            return []

        stamp += 1
        q = deque([a])
        seen[a] = stamp

        while q:
            x = q.popleft()

            for d, delta in enumerate((-m, m, -1, 1)):
                y = x + delta

                if grid[y] != 46:
                    continue
                if comp[y] != cid:
                    continue
                if seen[y] == stamp:
                    continue

                seen[y] = stamp
                parent[y] = x
                pdir[y] = d

                if y == b:
                    q.clear()
                    break

                q.append(y)

        result = []
        cur = b

        while cur != a:
            result.append(pdir[cur])
            cur = parent[cur]

        result.reverse()
        return result

    actions = []

    # First component: walk from S to the source of the first transition.
    x0, y0, d0 = transitions[0]
    path = walk_path(start, x0, start_comp)
    actions.extend("M" + DIRS[d] for d in path)

    # First teleport needs two shots.
    actions.append("O" + DIRS[d0])
    actions.append("B" + DIRS[first_dir[x0]])

    # Enter the blue portal at x0.
    actions.append("M" + DIRS[first_dir[x0]])

    current = y0
    active_dir = d0

    # Every later teleport needs one new shot.
    for i in range(1, len(transitions)):
        x, y, d = transitions[i]

        cid = comp[current]
        path = walk_path(current, x, cid)
        actions.extend("M" + DIRS[move_d] for move_d in path)

        # The active portal at x was created by the previous transition.
        # Replace the other color with a portal at y.
        color = "B" if i % 2 == 1 else "O"
        actions.append(color + DIRS[d])

        # Enter the still existing portal at x.
        actions.append("M" + DIRS[active_dir])

        current = y
        active_dir = d

    # Final component: walk from the last portal endpoint to E.
    final_cid = comp[current]
    path = walk_path(current, finish, final_cid)
    actions.extend("M" + DIRS[d] for d in path)

    shots = len(transitions) + 1
    moves = len(actions) - shots

    out = [f"{shots} {moves}"]
    out.extend(actions)
    return "\n".join(out) + "\n"

def main():
    sys.stdout.write(solve_stream(input))

if __name__ == "__main__":
    main()
```最初のフェーズでは、通常のフリーセル接続にラベルを付けます。 ガラス細胞は BFS に挿入されることはありません。これはまさに通常の運動に必要なものです。 同時に、その 4 つの隣接セルのうち少なくとも 1 つが境界エンドポイントである場合、フリー セルは境界エンドポイントとしてマークされます。`W`。 保存されている`first_dir`ポータルをそのセルに直接配置できる方向を示します。 

4 方向のスイープは、潜在的な二次光線シミュレーションを削除する部分です。 左から右への行スイープでは、`last_w`左側に最も近い固体の壁です。 境界セル (x) の場合、左方向のショットの終点は単に直後のセルになります。`last_w`。 他の 3 つの配列は対称的に計算されます。 すべてのセルは一定数のスイープ操作に参加するため、このフェーズは線形です。 

コンポーネント グラフは、すべてのグラフ エッジを保存するのではなく、BFS 中に遅延生成されます。 コンポーネントには多くの境界セルが含まれる可能性があるため、これによりメモリが節約されます。 コンポーネントがキューから削除されると、その境界セルのリンクされたリストがスキャンされ、考えられる 4 つのターゲットがそれぞれ検査されます。 各コンポーネントは 1 回処理されます。 

再構築には 2 番目の種類の BFS が使用されます。 コンポーネント グラフは、どのセルをポータルで接続する必要があるかを示しますが、前のテレポート エンドポイントから次の射撃位置までコンポーネント内を歩く方法は示しません。`walk_path`まさにそのローカルな問題を解決します。 コンポーネント グラフ パスはコンポーネントを繰り返すことがないため、これらすべてのローカル BFS 実行によって探索されるセルの総数は依然として (O(NM)) です。 

最初の 2 つのショットの順序は意図的です。 オレンジは最初の宛先に配置され、青は現在のソースに配置されます。 青いポータルに入ると、オレンジ色のポータルに到達します。 その後の遷移ごとに、現在ソース エンドポイントを占有していない色が置き換えられます。 ソースの既存のポータルは、使用される前に破棄されることはありません。 

すべてのグリッドのインデックス付けは内部的には 0 から始まります。 すべての自由セルは厳密に実線の外枠の内側にあるため、次のような式は`x - 1`、`x + 1`、`x - m`、 そして`x + m`フリーセルとして評価されるときはいつでも安全です。 整数オーバーフローは Python では問題ではなく、コンパクトな`array`コンテナーは、適切なメモリ使用量の範囲内で 100 万セルの状態を維持します。 

## 実用的な例

 ### サンプル 1

 フリー セルは、開始を含む上部コンポーネントと終了を含む下部コンポーネントに分割されます。 開始セル自体は、左側の固体の壁に隣接しているため、有効なポータルの終点です。 

便利なトランジションは最初から下向きに進みます。 光線はガラス セルを横切り、底部の固体壁に到達し、その壁の真上にあるフリー セルにポータルを作成します。 

| ステージ | 現在のセル | アクション | ポータルエンドポイント | コンポーネント |
 | --- | --- | --- | --- | --- |
 | 1 | ((2,3)) |`OD`| ((4,3)) のオレンジ色 | コンポーネントを開始 |
 | 2 | ((2,3)) |`BL`| ((2,3)) の青 | コンポーネントを開始 |
 | 3 | ((2,3)) |`ML`| ((4,3)) | にテレポートします。 コンポーネントを終了 |
 | 4 | ((4,3)) |`ML`| ポータルは必要ありません | コンポーネントを終了 |

 最初の 2 つのアクションにより最初のテレポートが確立されます (P=2)。 で青いポータルに入った後、`ML`、プレイヤーは((4,3))に登場し、通常の1動作で出口((4,2))に到達します。 これは、光線がガラスを通過できる理由も示しています。 

### サンプル 2

 最初のコンポーネントには開始が含まれます。 最初の有用なポータル遷移は、そのコンポーネントを ((6,4)) までたどった後に準備されます。 上向きに撃つとガラスのセルを通過し、固体の壁に到達し、((3,4)) にオレンジ色のポータルが作成されます。 

次に、プレイヤーは ((2,3)) に戻り、すぐ右側に青いポータルを配置して、そこに入ります。 オレンジ色のポータルはプレイヤーを ((3,4)) に送り、その後最後の動きが出口に到達します。 

| ステージ | 現在のセル | アクション | アクティブな便利なポータル | コンポーネント |
 | --- | --- | --- | --- | --- |
 | 1 | ((2,3)) | ((6,4)) まで歩きます | なし | コンポーネントを開始 |
 | 2 | ((6,4)) |`OU`| ((3,4)) のオレンジ色 | コンポーネントを開始 |
 | 3 | ((6,4)) | ((2,3)) まで歩きます | ((3,4)) のオレンジ色 | コンポーネントを開始 |
 | 4 | ((2,3)) |`BR`| ((2,3)) の青 | コンポーネントを開始 |
 | 5 | ((2,3)) |`MR`| ((3,4)) | にテレポートします。 コンポーネントを終了 |
 | 6 | ((3,4)) |`MR`| 通常の動き | コンポーネントを終了 |
 | 7 | ((3,5)) |`MU`| 通常の動き | コンポーネントを終了 |

 ここでも必要なショットは 2 つだけです。 ショット数がすでに最小限であるため、長い歩行セクションは目的に影響しません。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O(NM)) | コンポーネント BFS、4 方向スイープ、コンポーネント グラフ BFS、およびローカル パス再構築は共に、セルごとに一定数のグリッド構造のみを訪問します。 
| スペース | (O(NM)) | コンポーネント ラベル、4 つのエンドポイント配列、境界リスト、BFS 親、および再構築状態はすべて線形です。 

(N,M\le1000) の場合、(NM\le10^6)。 このアルゴリズムは、これら 100 万個のセルに対して一定数のパスのみを実行し、線形量の補助情報を保存するため、意図した制限に適合します。 生成される移動シーケンスも、ポータルの場合は最大 (NM-1) 移動であり、必要な (2NM) 境界を下回ります。 

## テストケース

 建設的な問題の出力は一意ではないため、テストではサンプルとバイトごとに比較するのではなく、生成されたアクション シーケンスを検証する必要があります。 次のハーネスは、報告されたショット数をチェックし、ポータルの交換やテレポートを含むすべてのアクションをシミュレートし、出口に到達したことを確認し、(2NM) 移動限界を検証します。```python
import io

# Import solve_stream from the submitted solution.
# If this code is appended directly after the solution, simply remove
# the import and use solve_stream from the same file.

def run(inp: str) -> str:
    return solve_stream(io.StringIO(inp).readline)

def verify(inp: str, expected_p: int):
    out = run(inp)
    lines = out.strip().splitlines()

    first = list(map(int, lines[0].split()))
    p, s = first
    assert p == expected_p
    assert len(lines) == p + s + 1

    it = iter(inp.strip().splitlines())
    n, m = map(int, next(it).split())
    grid = [next(it) for _ in range(n)]
    sr, sc = map(int, next(it).split())
    er, ec = map(int, next(it).split())
    sr -= 1
    sc -= 1
    er -= 1
    ec -= 1

    pos = (sr, sc)
    portals = [None, None]  # 0 = orange, 1 = blue

    dirs = {
        'U': (-1, 0),
        'D': (1, 0),
        'L': (0, -1),
        'R': (0, 1),
    }

    shots = 0
    moves = 0

    def shoot_endpoint(r, c, d):
        dr, dc = dirs[d]
        nr, nc = r + dr, c + dc

        while grid[nr][nc] != 'W':
            nr += dr
            nc += dc

        return nr - dr, nc - dc, nr, nc

    for action in lines[1:]:
        typ = action[0]
        d = action[1]
        r, c = pos

        if typ in 'OB':
            color = 0 if typ == 'O' else 1
            erow, ecol, wrow, wcol = shoot_endpoint(r, c, d)

            # A glass endpoint is deadly and cannot be used by a valid solution.
            assert grid[erow][ecol] == '.'

            side = (wrow, wcol, d)

            occupied = False
            for portal in portals:
                if portal is not None and portal[2] == side:
                    occupied = True
                    break

            if not occupied:
                portals[color] = (erow, ecol, side)

            shots += 1

        else:
            assert typ == 'M'
            dr, dc = dirs[d]
            nr, nc = r + dr, c + dc

            if grid[nr][nc] == '.':
                pos = (nr, nc)
            else:
                assert grid[nr][nc] == 'W'

                used = None
                for color, portal in enumerate(portals):
                    if portal is None:
                        continue

                    pr, pc, side = portal
                    if (pr, pc) == (r, c) and side[2] == d:
                        used = color
                        break

                assert used is not None

                other = 1 - used
                assert portals[other] is not None

                tr, tc, _ = portals[other]
                assert grid[tr][tc] == '.'
                pos = (tr, tc)

            moves += 1

    assert shots == p
    assert moves == s
    assert pos == (er, ec)
    assert s <= 2 * n * m

# Provided samples.

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

verify(sample1, 2)
verify(sample2, 2)
verify(sample3, 4)

# Custom case 1: minimum grid, start equals exit.
minimum_case = """\
3 3
WWW
W.W
WWW
2 2
2 2
"""

verify(minimum_case, 0)

# Custom case 2: glass cells form a complete barrier and every nontrivial
# portal endpoint is glass, so no safe component transition exists.
impossible_case = """\
5 5
WWWWW
W.GGW
WGWGW
WGG.W
WWWWW
2 2
4 4
"""

out = run(impossible_case).strip()
assert out == "-1 -1"

# Custom case 3: the shot must cross several glass cells before reaching
# the solid border. The endpoint is the free cell immediately before W.
multi_glass_case = """\
5 5
WWWWW
W...W
WGGGW
W...W
WWWWW
2 2
4 2
"""

verify(multi_glass_case, 2)

# Custom case 4: maximum-size all-free grid. Ordinary BFS is enough,
# so the optimal number of shots is zero.
n = 1000
m = 1000
rows = ["W" + "." * (m - 2) + "W"] * (n - 2)
maximum_case = (
    f"{n} {m}\n"
    + "W" * m + "\n"
    + "\n".join(rows) + "\n"
    + "W" * m + "\n"
    + "2 2\n"
    + f"{n - 1} {m - 1}\n"
)

verify(maximum_case, 0)
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | サンプル 1 | (P=2) | 最初のテレポート、ガラス越しの射撃、ポータルへの侵入 |
 | サンプル 2 | (P=2) | テレポートの前後の長い普通の散歩 |
 | サンプル 3 | (P=4) | いくつかの連続したポータル遷移と色の変更 |
 |`minimum_case`| (0) ショット | 開始はすでに終了とゼロショット処理に等しい |
 |`impossible_case`|`-1 -1`| Glass エンドポイントを安全なポータルとして扱ってはなりません。 
|`multi_glass_case`| (P=2) | 複数のガラスセルの後の最も近い固体壁 |
 |`maximum_case`| (0) ショット | (1000\times1000) 入力と線形時間動作 |

 ## 特殊なケース

 最小グリッドの場合、可能な空きセルは 1 つだけです。 開始と終了の両方である場合、コンポーネント チェックは直ちに成功します。 アルゴリズムがポータル グラフを構築して出力することはありません。`0 0`ゼロではどのショットも改善できないため、これが最適です。 

開始と出口が通常のフリー セルで接続されている迷路の場合、同じコンポーネントのテストにより、ポータル処理の前にアルゴリズムが終了します。 これは最適化のためにも必要です。 1 つ以上のショットを含むポータルベースのソリューションでは勝てません (P=0)。 

ガラス バリアの場合、指向性スイープでターゲット セルが見つかる可能性はありますが、ターゲットは`G`、したがって、対応するグラフの端は破棄されます。 これは、ガラス製セルが銃に対して透明であるか、テレポート後に占有しても安全であるかの正確な違いです。 したがって、不可能なカスタム ケースにはコンポーネント グラフ パスがなく、次のものが生成されます。`-1 -1`。 

いくつかのガラス セル、その後に自由セル、そして固体の壁が含まれる光線の場合、スイープはガラスで止まりません。 固体壁の直前のセルを終点として記録します。 サンプル1と`multi_glass_case`どちらもこの条件を実行します。 隣接する壁のみを検索するソリューションでは、有効な 2 ショット ルートを見逃してしまいます。 

繰り返されるコンポーネントの場合、個々のエンドポイント グラフではなくコンポーネント グラフが検索されます。 これにより、1 つの自由なコンポーネント内に留まる無駄な遷移が防止されます。 また、最短のコンポーネント パスは各コンポーネントを最大 1 回訪問するため、移動の制限が無料になります。 

最初のテレポートでは、ソースに既存のポータルがありません。 この構築では、宛先とソースの 2 つのショットを明示的に費やします。 ソースには前のトランジションによって作成されたポータルがすでに含まれているため、以降のトランジションには 1 回のショットだけが必要です。 これがまさに、(k) 個のエッジを含むコンポーネント パスに (k+1) 個のショットが必要な理由です。 

最後のコンポーネントの場合、到着後にポータルは必要ありません。 アルゴリズムは、最終エンドポイントから出口まで単に歩きます。 これは、目標が移動の長さではなくショットを最小限に抑えるため、重要であり、不必要な最終ポータルを作成すると、答えが悪化するだけである可能性があります。
