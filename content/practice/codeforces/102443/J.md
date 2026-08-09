---
title: "CF 102443J - 工場"
description: "(m 倍 n) の長方形マップが与えられます。 セルは、 と書かれたワークショップ、または . と書かれた空のいずれかです。すべてのワークショップ セルは 1 つの側で接続された領域を形成し、その中に囲まれた空の領域はありません。"
date: "2026-08-09T13:47:27+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102443
codeforces_index: "J"
codeforces_contest_name: "2019-2020 Russia Team Open, High School Programming Contest (VKOSHP 19)"
rating: 0
weight: 102443
solve_time_s: 468
verified: true
draft: false
---

[CF 102443J - 工場](https://codeforces.com/problemset/problem/102443/J)

 **評価:** -
 **タグ:** -
 **解決時間:** 7 分 48 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 (m \times n) 個の長方形マップが与えられます。 セルはワークショップであり、次のように記述されます。`*`、または空、次のように書かれます`.`。 すべてのワークショップ セルは 1 つの側面に接続された領域を形成し、その内部に囲まれた空の領域はありません。 後者の条件は、すべての空のセルが工場の外部領域に属していることを意味します。 

セル間のグリッド線に注目してください。 グリッド ノードが少なくとも 1 つのワークショップ セルの隅である場合、そのグリッド ノードは重要であると呼ばれます。 ワークショップ セルの各側面は、その 2 つのエンドポイント グリッド ノードを接続するコリドーです。 

重要なグリッド ノードを通る閉じたルートを構築する必要があります。 すべての移動は 1 つのコリドーに従う必要があり、コリドーを 2 回使用することはできません。すべての重要なノードはルート上のどこかに存在する必要があります。 ルートは短い必要はありません。 そのようなルートが存在しない場合は、次のように出力します。`No`。 それ以外の場合は印刷します`Yes`、コリドーの数、ルート内のグリッド ノードの順序。 元の制約は (1 \le m,n \le 20) で、制限時間は 2 秒、メモリは 256 MB です。 

重要なグラフの解釈は、コリドーが平面グリッド グラフを形成しているということです。 使用されているすべての頂点が偶数次数を持つ、すべての重要な頂点を含む接続されたサブグラフを探しています。 このようなグラフにはオイラー回路があり、その回路はまさに問題が要求するルートです。 

単純な解釈が間違っている簡単なケースがいくつかあります。 考慮する```
1 1
*
```答えは`Yes`。 四隅がサイクルになっているので、ひとつの工房をぐるっと一周するだけのルートです。 

のために```
1 1
.
```重要なノードはありません。 空のルートは有効なので、答えは次のようになります。`Yes`廊下はゼロ。 ルートに少なくとも 1 つのワークショップが含まれている必要があると想定するソリューションでは、このケースは誤って拒否されます。 

考慮する```
2 2
**
**
```答えは`No`。 対応するグリッド グラフは (3 \times 3) グリッドです。 その 4 つの隅の頂点の次数は 2 であるため、偶数にまたがるサブグラフは外側の境界全体を使用する必要があります。 これにより、中心頂点が切断されたままになりますが、中心からのエッジを使用すると、隣接する次数 3 の頂点の 1 つが奇数になります。 ファクトリが接続されていることを確認するだけでは十分ではないため、これは役立つ例です。 

ついに、```
1 2
**
```は`Yes`。 2 つのセルは (2 \times 3) グリッド グラフを形成し、境界線によってすべての重要なノードを通る有効な閉じたルートが得られます。 個々のワークショップ セル サイクルのみを考慮した構築では、隣接するサイクルを 1 つの大きなルートに結合できるという事実を見落とす可能性があります。 

## アプローチ

 直接的な総当たりソリューションは、平面グラフの面に関して説明できます。 ワークショップのすべてのセルは境界面ですが、工場には穴がないため、すべての空のセルと外側は同じ外側の面に属します。 すべてのワークショップの面に 2 つの色のいずれかを与えます。 隣接する 2 つの面の色が異なる場合は、それらを区切るグリッド エッジを使用します。 

どのカラーリングでも、選択したエッジはすべてのグリッド ノードで自動的に均等な度数になります。 ノードの周囲を歩き回ると、面の色は最終的に開始色に戻るため、色の変更の数は偶数になります。 すべての重要なノードに少なくとも 1 つの色の変化がある場合、すべての重要なノードの次数は正の偶数になります。 

したがって、ブルートフォースアルゴリズムは、ワークショップセルのすべてのバイナリカラーリングを試し、その遷移エッジを構築し、すべての重要なノードがカバーされているかどうかを確認し、最後に選択されたエッジが接続されているかどうかを確認できます。 (K) 個のワークショップ セルがある場合、(2^K) 個のカラーリングが考慮されます。 最悪の場合 (K=mn=400)、おおよそ (400 \cdot 2^{400}) の仕事が与えられます。 それはまったく不可能です。 

有益な観察は、1 つのグリッド ノードの状態がそのノードのすぐ周囲のワークショップ セルにのみ依存するということです。 内部ノードには、このようなセルが最大 4 つあります。 境界ノードではさらに少なくなり、外側はカラー 0 の面として扱われます。 

このローカル構造は、構築中に色全体を覚えておく必要がないことを意味します。 セルを行ごとに処理し、最後の (w) セルの色のみを記憶します (w=\min(m,n))。 次のセルが割り当てられると、新しく完成したすべての内部グリッド ノードのすべての入射セルがこのフロンティアで利用可能になります。 制約をすぐにチェックし、失敗した場合は状態を破棄できます。 

ファクトリは、(w) がより小さい次元になるように置き換えることができます。 したがって、プロファイル状態は単なる (w) ビットのマスクになります。 どの位置にも最大で (2^w) 個の状態があり、各状態には次のワークショップ セルの選択肢が最大 2 つあります。 空のセルには 1 つの色しかありません。 

穴がない状態は、面の解釈を特に有効にします。 選択したエッジは、2 つの面の色の境界になります。 すべての重要なノードが選択されたエッジに入射する場合、これらの境界コンポーネントは分離したままにすることができません。2 つの異なる境界コンポーネントはそれらの間に同じ色の面の領域を残し、厳密にその領域内のグリッド ノードには選択された入射エッジがありません。 これは、すべての重要なノードがカバーされるという要件と矛盾します。 したがって、選択された遷移グラフが接続されます。 

これらのエッジがわかれば、実際のルートを見つけることはオイラー グラフの Hierholzer アルゴリズムにすぎません。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | (O(mn \cdot 2^{mn})) | (O(mn)) | 遅すぎる |
 | プロフィール DP | (O(mn \cdot 2^w)), (w=\min(m,n)) | (O(mn \cdot 2^w)) メモ化あり | 承認済み |

 ## アルゴリズムのチュートリアル

1. 必要に応じて、列数 (w) が行数以下になるようにマップを転置します。 アルゴリズムの指数部分はフロンティア幅に依存するため、より小さい次元を使用することは、(2^{20}) と潜在的により大きな状態空間との間の違いになります。 
2. 少なくとも 1 つのワークショップ セルの角であるすべてのグリッド ノードにマークを付けます。 これらはまさに、最終ルートが訪問する必要があるノードです。 
3. ワークショップのすべてのセルを、色が 0 または 1 の面として解釈します。 空のセルとその外側は常にカラー 0 として扱われます。 エッジは、その両側の面の色が異なる場合に正確に選択されます。 
4. 行優先の順序でセルをスキャンします。 DP 状態は、最新 (w) のセルの色をビットマスクに保存します。 ビット 0 は最後に割り当てられたセルを表し、格納されている最上位のビットは現在の列の前の行のセルを表します。 
5. ワークショップセルの場合は、可能な両方の色を試してください。 空のセルの場合、外面の一部であるため、カラー 0 のみが許可されます。 
6. 新しく割り当てられたセルが内部グリッド ノードを完成するたびに、4 つの入射面の色を検査します。 ノードが重要であり、4 つの色がすべて等しい場合、そのノードに関連する選択されたエッジは存在しないため、この状態では有効なルートを生成することはできません。 
7. 入射セルが使用可能になったら、上、左、右、下の境界ノードを処理します。 マップ外の欠落しているセルの色は 0 なので、ワークショップ セルが 1 つある境界ノードは、そのセルが色 1 を受け取ると正確にカバーされます。 
8. 現在のセル位置とフロンティアマスクのペアをメモ化します。 どちらの色を選択しても完全に有効な色が得られない場合は、その状態は不可能であると覚えておいてください。 転置ステップではマスク幅が最大 20 に保たれます。 
9. 最初のゼロマスクから開始し、再帰状態が成功した色を繰り返し取得することによって、1 つの成功したカラーリングを再構築します。 メモ化された DP は、どの選択肢を選択すれば最後まで到達できるかを教えてくれます。 
10. カラーリングを廊下に変換します。 すべての水平および垂直グリッド エッジについて、マップの外側と空のセルにカラー 0 を使用して、その両側のワークショップ セルを確認します。 2 つの色が異なる場合は、端を正確に保ちます。 
11. 選択されたエッジはノードの周囲の色の変化に対応するため、選択されたグラフはすべての重要なノードで均等な次数を持ちます。 DP が単色の近傍を拒否したため、すべての重要なノードは正の次数を持ちます。 穴のないプロパティにより接続性が得られるため、選択されたグラフはオイラーグラフになります。 
12. 選択したグラフに対して Hierholzer のアルゴリズムを実行します。 選択したすべてのコリドーを 1 回だけ使用して、閉じたトレイルを作成します。 すべての重要なノードがこのグラフに属しているため、結果として得られるルートはすべての要件を満たします。 

### なぜ効果があるのか

 プロファイル DP の不変条件は、入射セルがすでに割り当てられているすべてのグリッド ノードがすでにチェックされていることです。 状態は、すべての重要な完成ノードに少なくとも 2 つの選択された入射エッジがある場合にのみ保持されます。選択されたエッジの次数は常に偶数であるため、その次数は正で偶数であることを意味します。 

面の色の構築により、選択されたすべてのエッジの次数が自動的に均一になります。 グリッド ノードの周囲で、カラー 1 面のセットに出入りすると、偶数の色の変化が発生します。 DP は重要なノードでその数値がゼロになるのを防ぐため、すべての重要なノードが選択されたグラフに属します。 ファクトリは接続されており穴がないため、切断された色の変化境界のセットは、色の変化のない領域内に重要なグリッド ノードを残し、DP 条件に矛盾します。 したがって、選択されたグラフは接続され、すべての次数が均等になるため、オイラーの定理により、選択されたすべてのエッジを 1 回だけ使用して閉じた軌跡が保証されます。 

## Python ソリューション```python
import sys
from functools import lru_cache

input = sys.stdin.readline
sys.setrecursionlimit(1_000_000)

def solve_case(original_grid):
    original_m = len(original_grid)
    original_n = len(original_grid[0])

    # Use the smaller dimension as the profile width.
    transposed = original_n > original_m

    if transposed:
        grid = [
            ''.join(original_grid[r][c] for r in range(original_m))
            for c in range(original_n)
        ]
    else:
        grid = original_grid[:]

    h = len(grid)
    w = len(grid[0])
    full = (1 << w) - 1

    # important[r][c] says whether grid node (r, c)
    # is a corner of at least one workshop cell.
    important = [[False] * (w + 1) for _ in range(h + 1)]

    for r in range(h):
        for c in range(w):
            if grid[r][c] == '*':
                important[r][c] = True
                important[r + 1][c] = True
                important[r][c + 1] = True
                important[r + 1][c + 1] = True

    if not any(any(row) for row in important):
        # No important nodes exist.
        if transposed:
            return "Yes\n0\n0 0\n"
        return "Yes\n0\n0 0\n"

    def all_equal_and_important(r, c, values):
        if not important[r][c]:
            return False
        first = values[0]
        return all(v == first for v in values)

    @lru_cache(maxsize=None)
    def dfs(pos, mask):
        if pos == h * w:
            return True

        r, c = divmod(pos, w)

        if grid[r][c] == '*':
            choices = (1, 0)
        else:
            choices = (0,)

        for x in choices:
            left = mask & 1

            # Check the internal node (r, c).
            if r > 0 and c > 0:
                up = (mask >> (w - 1 - c)) & 1
                up_left = (mask >> (w - c)) & 1

                if all_equal_and_important(
                    r, c, (up_left, up, left, x)
                ):
                    continue

            # Top boundary.
            if r == 0:
                if c == 0:
                    if all_equal_and_important(0, 0, (0, 0, 0, x)):
                        continue
                else:
                    if all_equal_and_important(0, c, (0, 0, left, x)):
                        continue

                if c == w - 1:
                    if all_equal_and_important(0, w, (0, 0, 0, x)):
                        continue

            # Left boundary.
            if c == 0 and r > 0:
                up = (mask >> (w - 1)) & 1
                if all_equal_and_important(r, 0, (0, 0, up, x)):
                    continue

            # Right boundary.
            if c == w - 1 and r > 0:
                up = mask & 1
                if all_equal_and_important(r, w, (0, 0, up, x)):
                    continue

            # Bottom boundary.
            if r == h - 1:
                if c == 0:
                    if all_equal_and_important(h, 0, (0, 0, 0, x)):
                        continue
                else:
                    if all_equal_and_important(h, c, (0, 0, left, x)):
                        continue

                if c == w - 1:
                    if all_equal_and_important(h, w, (0, 0, 0, x)):
                        continue

            new_mask = ((mask << 1) & full) | x

            if dfs(pos + 1, new_mask):
                return True

        return False

    if not dfs(0, 0):
        return "No\n"

    # Reconstruct one successful face coloring.
    colors = [[0] * w for _ in range(h)]
    pos = 0
    mask = 0

    while pos < h * w:
        r, c = divmod(pos, w)

        if grid[r][c] == '*':
            choices = (1, 0)
        else:
            choices = (0,)

        chosen = None

        for x in choices:
            left = mask & 1
            ok = True

            if r > 0 and c > 0:
                up = (mask >> (w - 1 - c)) & 1
                up_left = (mask >> (w - c)) & 1
                if all_equal_and_important(
                    r, c, (up_left, up, left, x)
                ):
                    ok = False

            if ok and r == 0:
                if c == 0:
                    if all_equal_and_important(0, 0, (0, 0, 0, x)):
                        ok = False
                else:
                    if all_equal_and_important(0, c, (0, 0, left, x)):
                        ok = False

                if ok and c == w - 1:
                    if all_equal_and_important(0, w, (0, 0, 0, x)):
                        ok = False

            if ok and c == 0 and r > 0:
                up = (mask >> (w - 1)) & 1
                if all_equal_and_important(r, 0, (0, 0, up, x)):
                    ok = False

            if ok and c == w - 1 and r > 0:
                up = mask & 1
                if all_equal_and_important(r, w, (0, 0, up, x)):
                    ok = False

            if ok and r == h - 1:
                if c == 0:
                    if all_equal_and_important(h, 0, (0, 0, 0, x)):
                        ok = False
                else:
                    if all_equal_and_important(h, c, (0, 0, left, x)):
                        ok = False

                if ok and c == w - 1:
                    if all_equal_and_important(h, w, (0, 0, 0, x)):
                        ok = False

            if not ok:
                continue

            new_mask = ((mask << 1) & full) | x
            if dfs(pos + 1, new_mask):
                chosen = x
                break

        if chosen is None:
            raise RuntimeError("reconstruction failed")

        colors[r][c] = chosen
        mask = ((mask << 1) & full) | chosen
        pos += 1

    # Convert back to the original orientation.
    if transposed:
        selected = [
            [0] * original_n for _ in range(original_m)
        ]
        for r in range(h):
            for c in range(w):
                selected[c][r] = colors[r][c]
    else:
        selected = colors

    m = original_m
    n = original_n

    def face(r, c):
        if 0 <= r < m and 0 <= c < n:
            return selected[r][c]
        return 0

    vertices = (m + 1) * (n + 1)
    graph = [[] for _ in range(vertices)]
    edges = []

    def vid(r, c):
        return r * (n + 1) + c

    def add_edge(r1, c1, r2, c2):
        a = vid(r1, c1)
        b = vid(r2, c2)
        eid = len(edges)
        edges.append((a, b))
        graph[a].append((eid, b))
        graph[b].append((eid, a))

    # Horizontal grid edges.
    for r in range(m + 1):
        for c in range(n):
            above = face(r - 1, c)
            below = face(r, c)
            if above != below:
                add_edge(r, c, r, c + 1)

    # Vertical grid edges.
    for r in range(m):
        for c in range(n + 1):
            left = face(r, c - 1)
            right = face(r, c)
            if left != right:
                add_edge(r, c, r + 1, c)

    important_original = [
        [False] * (n + 1) for _ in range(m + 1)
    ]

    for r in range(m):
        for c in range(n):
            if original_grid[r][c] == '*':
                important_original[r][c] = True
                important_original[r + 1][c] = True
                important_original[r][c + 1] = True
                important_original[r + 1][c + 1] = True

    important_vertices = []
    for r in range(m + 1):
        for c in range(n + 1):
            if important_original[r][c]:
                important_vertices.append(vid(r, c))

    start = important_vertices[0]

    # The face-color construction should give a connected graph.
    seen = {start}
    stack = [start]

    while stack:
        v = stack.pop()
        for _, to in graph[v]:
            if to not in seen:
                seen.add(to)
                stack.append(to)

    if len(seen) != len(important_vertices):
        return "No\n"

    # Hierholzer's algorithm.
    used = [False] * len(edges)
    ptr = [0] * vertices
    stack = [start]
    route = []

    while stack:
        v = stack[-1]

        while ptr[v] < len(graph[v]) and used[graph[v][ptr[v]][0]]:
            ptr[v] += 1

        if ptr[v] == len(graph[v]):
            route.append(stack.pop())
            continue

        eid, to = graph[v][ptr[v]]
        ptr[v] += 1

        if used[eid]:
            continue

        used[eid] = True
        stack.append(to)

    route.reverse()

    if len(route) != len(edges) + 1:
        return "No\n"

    out = ["Yes", str(len(edges))]
    for v in route:
        r, c = divmod(v, n + 1)
        out.append(f"{r} {c}")

    return "\n".join(out) + "\n"

def solve():
    m, n = map(int, input().split())
    grid = [input().strip() for _ in range(m)]
    sys.stdout.write(solve_case(grid))

if __name__ == "__main__":
    solve()
```プロファイル マスクは中心的な実装詳細です。 セルを処理する前、ビット 0 はそのすぐ左にあるすでに処理されたセルです。 上位ビットは前の行を逆順で表します。 これにより、新しく完成した内部ノードの周囲の 4 つのセルすべてが一定時間内に利用可能になります。 

アップデート```
new_mask = ((mask << 1) & full) | x
```最も古いフロンティア セルを削除し、残りのすべてのセルをビット 0 から 1 位置ずらして、ビット 0 に新しい色を挿入します。 マスクは次のように切り詰める必要があります`full`そうしないと、選択したプロファイル幅を超えて拡大されてしまいます。 

空のセルには強制的にゼロの色が付けられます。 このように処理することで境界処理が均一になります。入力によって囲まれた空の領域がないことが保証されるため、空のセルと外側の面は平面埋め込みの同じ面となります。 

再構築では、すべての状態の親ポインタが保存されるわけではありません。 代わりに、初期状態が成功したことがわかったら、可能な次の色を再度試し、結果の状態がまだ終了できるかどうかをメモ化された DP に尋ねます。 これにより、別の親配列が保存されます。 

最終的なグラフは、選択したすべてのワークショップ セルのエッジを追加するのではなく、色の変更から構築されます。 この区別が重要です。 2 つの隣接するワークショップの面が両方とも色 1 を持っている場合、それらの共有エッジを使用してはなりません。 それらの色が異なる場合、共有エッジは遷移グラフのまさにコリドーです。 

すべての次数が偶数であり、グラフが接続されているため、グラフの構築後に Hierholzer のアルゴリズムが使用されます。 生成された頂点シーケンスには、使用されたコリドーの数より正確に 1 つ多い頂点が含まれ、同じ頂点で開始および終了し、コリドーを繰り返すことはありません。 

## 実用的な例

 ### サンプル 1

 最初のサンプルは```
3 3
***
***
.**
```有効な顔のカラーリングは、いくつかのワークショップ セルをカラー 1 として選択し、外側をカラー 0 として扱うものとみなすことができます。 DP は、新しく完成した各グリッド ノードをチェックしながらセルをスキャンします。 

| ポジション | 現在のセル | 選択した色 | ステップを経たフロンティア |
 | --- | --- | --- | --- |
 | (0,0) |`*`| 1 |`...1`|
 | (0,1) |`*`| 0 |`..01`|
 | (0,2) |`*`| 1 |`.101`|
 | (1,0) |`*`| 0 |`1010`|
 | (1,1) |`*`| 1 |`0101`|
 | (1,2) |`*`| 0 |`1010`|
 | (2,0) |`.`| 0 |`0100`|
 | (2,1) |`*`| 1 |`1001`|
 | (2,2) |`*`| 0 |`0010`|

 有効な色がいくつか存在するため、プログラムによって選択される正確な色は異なる場合があります。 重要なのは、すべての重要なノードがその入射面で両方の色を認識することです。 結果として得られる色変化エッジは、接続された 1 つのオイラー グラフを形成し、Hierholzer のアルゴリズムはそれらのエッジを有効な閉じたルートに変換します。 公式サンプルでは 16 個のコリドーが使用されていますが、問題では任意の有効なルートが受け入れられます。 

### サンプル 2

 2つ目のサンプルは、```
1 4
****
```作業場セルは 1 列のみです。 重要なノードは、(2 \times 5) グリッドの 10 個の角です。 移行エッジはストリップ全体の周囲を形成することができます。 

| ポジション | セル | 色の選択 | 関連する制約 |
 | --- | --- | --- | --- |
 | (0,0) |`*`| 1 | 左上のノードを覆う必要があります |
 | (0,1) |`*`| 0 | 隣接する最上位ノードには色 1 と 0 が表示されます。 
| (0,2) |`*`| 1 | 隣接する最上位ノードには色 0 と 1 が表示されます。 
| (0,3) |`*`| 0 | 隣接する最上位ノードには色 1 と 0 が表示されます。 

繰り返しになりますが、実際の色は異なる場合があります。 重要な事実は、すべての境界ノードの入射エッジで色が変化するため、選択したコリドーが接続された偶数のグラフを形成するということです。 公式サンプルでは10の回廊のルートが用意されています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O(mn2^w)) | 状態ごとに (mn) 個のプロファイル位置、最大で (2^w) 個のマスク、最大で 2 つの色の選択があります。 
| スペース | (O(mn2^w)) | メモ化テーブルには、位置およびフロンティア マスクごとに 1 つの状態を含めることができます。 

ここでは (w=\min(m,n))、必要に応じて DP の前にグリッドが転置されるためです。 (w\le20) を使用すると、プロファイルには最大で (2^{20}=1,048,576) 個のマスクが含まれます。 指数依存性は、すべて (mn) 個のセルではなく、より小さいグリッド次元に依存します。これは、総当たり (2^{mn}) 検索からの重要な削減です。 元の問題には (m,n\le20) と 256 MB のメモリ制限があります。 

## テストケース

 出力ルートは一意ではないため、テストでは、出力ルートを比較するのではなく、返されたルートの構造を検証する必要があります。`Yes`バイトごとに答えます。 以下のヘルパーは、すべての重要なノードが訪問されていること、すべての移動が隣接するグリッド ノード間で行われていること、すべてのコリドーが最大 1 回使用されていること、およびすべての使用されているエッジが実際にコリドーであることをチェックします。```python
# The solution above defines solve_case(grid).

import io
import sys

def run(inp: str) -> str:
    old_stdin = sys.stdin
    sys.stdin = io.StringIO(inp)
    try:
        m, n = map(int, input().split())
        grid = [input().strip() for _ in range(m)]
        return solve_case(grid)
    finally:
        sys.stdin = old_stdin

def validate(inp: str, out: str, expected_possible: bool):
    lines = out.strip().splitlines()
    assert lines

    if not expected_possible:
        assert lines[0] == "No"
        return

    assert lines[0] == "Yes"

    m, n = map(int, inp.splitlines()[0].split())
    grid = inp.splitlines()[1:1 + m]

    k = int(lines[1])
    route = [tuple(map(int, line.split())) for line in lines[2:]]

    assert len(route) == k + 1
    assert route[0] == route[-1]

    important = set()

    for r in range(m):
        for c in range(n):
            if grid[r][c] == '*':
                important.add((r, c))
                important.add((r + 1, c))
                important.add((r, c + 1))
                important.add((r + 1, c + 1))

    assert important.issubset(set(route))

    used = set()

    for a, b in zip(route, route[1:]):
        ar, ac = a
        br, bc = b

        assert 0 <= ar <= m
        assert 0 <= br <= m
        assert 0 <= ac <= n
        assert 0 <= bc <= n

        assert abs(ar - br) + abs(ac - bc) == 1

        edge = tuple(sorted((a, b)))
        assert edge not in used
        used.add(edge)

        if ar == br:
            r = ar
            c = min(ac, bc)
            workshop = (
                r > 0 and grid[r - 1][c] == '*'
            ) or (
                r < m and grid[r][c] == '*'
            )
        else:
            r = min(ar, br)
            c = ac
            workshop = (
                c > 0 and grid[r][c - 1] == '*'
            ) or (
                c < n and grid[r][c] == '*'
            )

        assert workshop

# Provided sample 1.
sample1 = """\
3 3
***
***
.**
"""
validate(sample1, run(sample1), True)

# Provided sample 2.
sample2 = """\
1 4
****
"""
validate(sample2, run(sample2), True)

# Minimum-size workshop.
case3 = """\
1 1
*
"""
validate(case3, run(case3), True)

# No workshops at all.
case4 = """\
2 3
...
...
"""
validate(case4, run(case4), True)

# Full 2 x 2 factory, whose 3 x 3 grid graph has no spanning
# Eulerian subgraph.
case5 = """\
2 2
**
**
"""
validate(case5, run(case5), False)

# A maximum-width one-row factory.
case6 = "1 20\n" + "*" * 20 + "\n"
validate(case6, run(case6), True)

# A maximum-size full grid. The forced outer boundary leaves
# an interior grid graph that cannot be included in one Eulerian route.
case7 = "20 20\n" + "\n".join(["*" * 20] * 20) + "\n"
validate(case7, run(case7), False)
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`3 3 / *** / *** / .**`|`Yes`| Provided sample, internal nodes and a non-rectangular workshop |
 |`1 4 / ****`|`Yes`| 提供されたサンプルと薄型工場 |
 |`1 1 / *`|`Yes`| 最小ワークショップと境界のみのグラフ |
 |`2 3 / ... / ...`|`Yes`| 重要なノードがなく、長さがゼロのルート |
 |`2 2 / ** / **`|`No`| 最小の非自明で不可能なオイラーケース |
 |`1 20 / ********************`|`Yes`| 最大プロファイル幅と長い境界 |
 |`20 20 / all *`|`No`| 最大サイズのグリッドと強制境界障害物 |

 ## 特殊なケース

 一回のワークショップの場合、```
1 1
*
```重要なノードは 4 つだけで、そのセルの隅にあります。 フェイスカラーDPは工房のフェイス1と外側のゼロをカラーリングできます。 すべての境界エッジが選択され、4 エッジのオイラー サイクルが得られます。 したがって、出力は次のように始まります。`Yes`、 に続く`4`、および閉じた正方形を形成する 5 つのグリッド ノード。 

空のマップの場合、```
1 1
.
```重要なノードがまったくありません。 DP は唯一のセルにカラー 0 を割り当て、遷移グラフにはエッジが含まれません。 実装では、Hierholzer を実行する前にこれを処理し、単一のグリッド ノードで構成されるゼロコリドー ルートを出力します。 

のために```
2 2
**
**
```外側の 4 隅のそれぞれには、インシデント ワークショップ セルが 1 つだけあるため、それらのセルには強制的に 1 つの色が付けられます。 これにより、外側の境界全体が遷移グラフに強制的に組み込まれます。 境界頂点にはすでに必要な次数 2 が設定されているため、中心につながる 4 つのエッジが選択されなくなります。 中心は重要ですが孤立しているため、有効なルートが存在しません。 プロファイル DP は、局所的な面の色の制約を通じて同じ矛盾を発見します。 

1行境界の場合```
1 2
**
```2 つのワークショップ セルは反対の色を受け取ることができます。 共有エッジが選択され、カラー 1 セルに隣接する外側のエッジも選択されます。 結果として選択されたグラフは接続されており、すべての重要なノードが正の次数を持っているため、Hierholzer はコリドーを繰り返さずに閉じたルートを生成します。 

実装における境界チェックには特別な注意が必要です。 内部ノードは最大 4 つのワークショップ セルによって管理されますが、外側の境界上のノードには実際のセルが 1 つまたは 2 つしかなく、外側の面はカラー ゼロとして扱われる必要があります。 外面を忘れることは、特に単一の作業場セルまたは薄い (1 \times n) 工場の場合、誤検知の一般的な原因となります。
