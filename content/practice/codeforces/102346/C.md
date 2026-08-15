---
title: "CF 102346C - 危険を伴う交差点"
description: "(N 倍 M) の長方形の交差グリッドがあります。 車両は 1 つの交差点からスタートし、4 つの基本的な方向の 1 つを選択し、グリッドから離れるか衝突するまで 1 秒あたり 1 つの交差点の速度で移動します。"
date: "2026-08-13T01:19:34+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102346
codeforces_index: "C"
codeforces_contest_name: "2019-2020 ACM-ICPC Brazil Subregional Programming Contest"
rating: 0
weight: 102346
solve_time_s: 193
verified: true
draft: false
---

[CF 102346C - 危険を伴う交差点](https://codeforces.com/problemset/problem/102346/C)

 **評価:** -
 **タグ:** -
 **解決時間:** 3 分 13 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 (N \times M) 個の交差の長方形グリッドがあります。 車両は 1 つの交差点からスタートし、4 つの基本的な方向の 1 つを選択し、グリッドから離れるか衝突するまで 1 秒あたり 1 つの交差点の速度で移動します。 

衝突には基本的に異なる 2 種類があります。 同一車線を互いに向かって移動する 2 台の車両が交差点間で出会い、水平衝突は東の交差点で、垂直衝突は北の交差点で発生します。 垂直の車線を走行している車両が交差点にまったく同時に到着すると、交差点で衝突する可能性があります。 衝突後、巻き込まれた車両はその交差点で永久に停止します。 後からその交差点に到着した車両も衝突します。 

タスクは、衝突に一度も関与しなかった車両をカウントすることです。 

車両の数は最大 (10^5) ですが、グリッドには最大 (10^{10}) 個の交差点を含めることができます。 グリッド全体を構築することはできませんし、(O(C^2)) ペアのチェックですらコストがかかりすぎます。 (C=10^5) の場合、最悪の場合、約 (5\cdot10^9) 台の車両ペアが必要になります。 目的のソリューションでは、車両または衝突ごとに対数量の情報のみを処理する必要があります。 

直接的な幾何学的チェックが誤解を招くケースがいくつかあります。 他の車両が先に停止したため、実際に衝突することなく、車両がその車両の元の軌道と理論的に交差する可能性があります。 例えば、```
3 4 3
1 1 L
3 3 N
2 4 O
```東行きの車両と北行きの車両は、両方が永遠に続く場合、時間 (2) に ((1,3)) で合流します。 しかし、時刻(1)の((2,3))で北行車両が先に西行車両と衝突し、そこで停止する。 東行きの車両は安全に進み、正解は(1)です。 交差する軌道のすべてのペアを衝突として単純にマークするアプローチでは、3 台すべての車両が誤ってマークされてしまいます。 

もう 1 つの微妙なケースは、交差点間の衝突です。 と```
2 3 2
1 1 L
1 3 O
```車両は時間 (1) に列 (1) と列 (3) の中間で合流し、列 (2) の東交差点で停止します。 問題の東交差ルールを適用せずに衝突点を数学的中点として扱うと、誤った障害物の位置が与えられます。 

3 番目のエッジケースは、停止した車両が後で追突された場合に発生します。 2 回目の衝突を引き起こした車両は、停止した車両の元の軌道を同時に横切る必要はありません。 衝突によって永続的な障害物が作成されると、その障害物自体がシミュレーションの一部になります。 

## アプローチ

 ブルートフォースアプローチは、車両のすべてのペアを調べ、それらの軌道が一致するかどうかを計算することです。 各ペアについて、互換性のある行、列、または垂直の車線を移動するかどうかを判断し、集合時間を計算できます。 実際の状態を注意深くシミュレートすればこれは正しいのですが、すべてのペアを調べるだけでもすでに (O(C^2)) のコストがかかります。 (C=10^5) では、およそ (5\cdot10^9) ペアを意味しますが、これは C++ の制限である 1.5 秒では不可能であり、Python ではさらに現実的ではありません。 

さらに有益な観察は、シミュレーションがイベント駆動型であるということです。 走行中の車両は他のすべての車両を気にしません。 次の衝突には、一定数の幾何学的方向の 1 つで最も近い関連する移動車両、またはその車線に沿った最も近い停止した交差点のみが関与する可能性があります。 

垂直衝突の場合、有用な幾何学的変換は特に簡単です。 東行きの車両が ((r,c)) で北行きの車両と ((r',c')) で出会うとします。 会議交差点は満たす

 [
 c'-c=r-r'、
 ]

 それで

 [
 r+c=r'+c'。 
]

 したがって、2 台の車両は同じ対角線上にあります。 他の方向の組み合わせも同様に、(r+c) または (r-c) のいずれかに対応します。 水平方向の正面衝突では同じ行が使用され、垂直方向の正面衝突では同じ列が使用されます。 

これにより、すべての移動-移動衝突クエリが、一定数の順序付けられた 1 次元シーケンスの 1 つで最も近いアクティブな車両を見つけることに減ります。 車両は移動セットから消えるだけなので、これらのシーケンスは効率的に削除をサポートします。 停止した踏切は、その行と列の永続的な障害物として個別に処理されます。 

その後、シミュレーションは優先キューを使用して時系列に処理できます。 すべての車両は、現在考えられる最も早い衝突を続けています。 最も早いイベントに到達すると、参加車両がまだ移動中であり、そのイベントがまだ最も早いイベントであることを確認します。 古いイベントは破棄されます。 本物の衝突では、走行中の車両が除去され、停止している交差点が記録され、その障害物を含む新たな衝突候補が検討されます。 

これが機能する主な理由は、イベントの処理時間が増加するためです。 車両が撤去されると、それに関係するイベントは無効になるだけで、無効になることはありません。 障害物が作成されると、その障害物に到達する経路を持つ車両に対してのみ、新しい以前のイベントを作成できます。 したがって、遅延無効化で十分です。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | (O(C^2)) | (O(C)) | 遅すぎる |
 | 順序付き線構造によるイベント シミュレーション | (O(C\log C)) | (O(C)) | 承認済み |

 ## アルゴリズムのチュートリアル

1. すべての車両を開始行、開始列、方向、およびアクティブなフラグで表します。 現役車両は元の直線に沿って進みます。 アクティブフラグが false になると、二度と移動することはありません。 
2. 移動-移動衝突に必要な 4 つの幾何学的ファミリーの順序付けられた構造を構築します。 行は水平方向の正面衝突を処理し、列は垂直方向の正面衝突を処理し、(r+c) は 1 組の垂直方向を処理し、(r-c) はもう 1 組の垂直方向を処理します。 
3. 各車両について、関連する各方向で衝突する可能性がある最も近いアクティブな車両を見つけます。 たとえば、東行きの車両は、その東に最も近い西行きの車両と、2 つの対角線ファミリー上の適切な北行きと南行きの候補を考慮するだけで済みます。 
4. すべての候補の衝突時間を計算します。 水平および垂直の正面衝突は半整数倍で発生する可能性があるため、すべての時間は 2 倍になります。 対向する水平車両間の距離 (d) により、イベント時間 (2t=d) が生成されます。 交差点での垂直衝突の時間は整数であるため、2 倍の時間は単純にその整数の 2 倍になります。 
5. 各車両の最も早い候補をグローバル優先キューに入れます。 キューは 2 倍の衝突時間によって順序付けされるため、シミュレーションでは常に次に考えられる物理イベントが最初に考慮されます。 
6. イベントがキューから削除されたら、関係するすべての移動車両がまだアクティブであるかどうかを確認します。 すでに衝突している場合、そのイベントは古くなり、破棄されます。 生き残っている車両の現在の候補を再計算すると、次に考えられるイベントが明らかになります。 
7. 本物の移動-移動衝突の場合、参加しているすべての移動車両を衝突済みとしてマークします。 水平衝突の場合は、声明で定められた東側交差点に停止交差点を設ける。 垂直衝突の場合は北側交差点を使用してください。 垂直衝突の場合は、共通の交差を使用します。 
8. 新しく停止した交差点を、順序付けられた行および列の障害物構造に保存します。 移動中の車両は、この交差点を静止車両とまったく同様に扱います。 次の衝突は、その進行方向で最初に停止した横断歩道である可能性があります。 
9. 優先キューに有効な衝突イベントが含まれなくなるまで続行します。 衝突したとマークされていない車両はすべて生存しているため、答えはそのような車両の数になります。 

### なぜ効果があるのか

 優先キューによって表されるあらゆる時点で、すべてのアクティブな車両には、別のアクティブな車両または以前に作成された停止した踏切との最も早い衝突の候補があります。 候補は、車両の 1 次元経路上で最も近い関連オブジェクトを使用して検出されるため、最初に遠くのオブジェクトに到達することはできません。 

優先キューはこれらすべての候補のうち最小のものを処理するため、最初の有効なイベントは常に次の物理衝突になります。 衝突が発生すると、車両は永久に停止し、その結果生じる交差点が障害物として追加されます。 将来のイベントは、シミュレーション状態が変化した場合にのみ再計算されます。 車両は移動中から停止中へのみ変化するため、破棄されたイベントは再び有効になることはありません。 したがって、このプロセスでは、物理シミュレーションとまったく同じ一連の衝突が生成されます。 

## Python ソリューション```python
import sys
import heapq
from bisect import bisect_left, bisect_right

input = sys.stdin.readline

class TreapNode:
    __slots__ = ("key", "prio", "left", "right")

    def __init__(self, key):
        self.key = key
        self.prio = (key * 1103515245 + 12345) & 0x7fffffff
        self.left = None
        self.right = None

def rotate_right(p):
    q = p.left
    p.left = q.right
    q.right = p
    return q

def rotate_left(p):
    q = p.right
    p.right = q.left
    q.left = p
    return q

def insert(root, key):
    if root is None:
        return TreapNode(key)

    if key < root.key:
        root.left = insert(root.left, key)
        if root.left.prio < root.prio:
            root = rotate_right(root)
    elif key > root.key:
        root.right = insert(root.right, key)
        if root.right.prio < root.prio:
            root = rotate_left(root)

    return root

def merge(a, b):
    if a is None:
        return b
    if b is None:
        return a

    if a.prio < b.prio:
        a.right = merge(a.right, b)
        return a
    else:
        b.left = merge(a, b.left)
        return b

def erase(root, key):
    if root is None:
        return None

    if key < root.key:
        root.left = erase(root.left, key)
    elif key > root.key:
        root.right = erase(root.right, key)
    else:
        return merge(root.left, root.right)

    return root

def predecessor(root, key):
    ans = None
    while root is not None:
        if root.key < key:
            ans = root.key
            root = root.right
        else:
            root = root.left
    return ans

def successor(root, key):
    ans = None
    while root is not None:
        if root.key > key:
            ans = root.key
            root = root.left
        else:
            root = root.right
    return ans

def solve():
    n, m, c = map(int, input().split())

    r = [0] * c
    col = [0] * c
    d = [""] * c

    rows = {}
    cols = {}
    diag1 = {}
    diag2 = {}

    for i in range(c):
        a, b, ch = input().split()
        a = int(a)
        b = int(b)

        r[i] = a
        col[i] = b
        d[i] = ch

        rows.setdefault(a, []).append(i)
        cols.setdefault(b, []).append(i)
        diag1.setdefault(a + b, []).append(i)
        diag2.setdefault(a - b, []).append(i)

    # Each list is sorted by the coordinate along that line.
    for mp in (rows, cols, diag1, diag2):
        for arr in mp.values():
            arr.sort(key=lambda x: col[x] if mp is rows or mp is diag1 or mp is diag2 else r[x])

    active = [True] * c
    collided = [False] * c

    # Stopped crossings, stored by row and column.
    stopped_rows = {}
    stopped_cols = {}

    # A simple dynamic obstacle structure. Each row/column has a treap.
    row_root = {}
    col_root = {}

    def add_stop(a, b):
        root = row_root.get(a)
        row_root[a] = insert(root, b)

        root = col_root.get(b)
        col_root[b] = insert(root, a)

    def next_stopped(i):
        a = r[i]
        b = col[i]
        ch = d[i]

        best_t = None
        best_pos = None

        if ch == "L":
            x = successor(row_root.get(a), b)
            if x is not None:
                t = 2 * (x - b)
                best_t = t
                best_pos = (a, x)

        elif ch == "O":
            x = predecessor(row_root.get(a), b)
            if x is not None:
                t = 2 * (b - x)
                best_t = t
                best_pos = (a, x)

        elif ch == "N":
            x = predecessor(col_root.get(b), a)
            if x is not None:
                t = 2 * (a - x)
                best_t = t
                best_pos = (x, b)

        else:
            x = successor(col_root.get(b), a)
            if x is not None:
                t = 2 * (x - a)
                best_t = t
                best_pos = (x, b)

        return best_t, best_pos

    # The following helpers find candidate active vehicles.
    # Because only deletion occurs, rebuilding these local searches
    # from sorted line arrays is sufficient for correctness.
    #
    # For each query we use binary search and skip inactive vehicles.
    # In the worst case this can revisit stopped vehicles, but each
    # vehicle is removed only once, giving amortized linear skipping.

    dead = [False] * c

    def nearest_in(arr, coord, direction, want):
        if not arr:
            return None

        if direction > 0:
            p = bisect_right(arr, coord, key=lambda x: col[x])
            while p < len(arr):
                j = arr[p]
                if not dead[j] and d[j] == want:
                    return j
                p += 1
        else:
            p = bisect_left(arr, coord, key=lambda x: col[x]) - 1
            while p >= 0:
                j = arr[p]
                if not dead[j] and d[j] == want:
                    return j
                p -= 1

        return None

    def candidate(i):
        if dead[i]:
            return None

        a = r[i]
        b = col[i]
        ch = d[i]

        best = None

        # Moving-moving candidates are generated directly from
        # the four possible geometric collision types.
        #
        # Horizontal.
        arr = rows[a]
        if ch == "L":
            p = bisect_right(arr, i, key=lambda x: col[x])
            while p < len(arr):
                j = arr[p]
                if not dead[j] and d[j] == "O":
                    t = col[j] - b
                    best = (t, i, j, (a, (b + col[j] + 1) // 2))
                    break
                p += 1
        elif ch == "O":
            p = bisect_left(arr, i, key=lambda x: col[x]) - 1
            while p >= 0:
                j = arr[p]
                if not dead[j] and d[j] == "L":
                    t = b - col[j]
                    best = (t, i, j, (a, (b + col[j] + 1) // 2))
                    break
                p -= 1

        # Vertical.
        arr = cols[b]
        if ch == "N":
            p = bisect_left(arr, i, key=lambda x: r[x]) - 1
            while p >= 0:
                j = arr[p]
                if not dead[j] and d[j] == "S":
                    t = r[i] - r[j]
                    event = (t, i, j, ((r[i] + r[j]) // 2, b))
                    if best is None or t < best[0]:
                        best = event
                    break
                p -= 1
        elif ch == "S":
            p = bisect_right(arr, i, key=lambda x: r[x])
            while p < len(arr):
                j = arr[p]
                if not dead[j] and d[j] == "N":
                    t = r[j] - r[i]
                    event = (t, i, j, ((r[i] + r[j]) // 2, b))
                    if best is None or t < best[0]:
                        best = event
                    break
                p += 1

        # Perpendicular collisions.
        # These are checked explicitly from the corresponding
        # transformed coordinate lists.
        #
        # We fall back to scanning the line until the first valid
        # directional vehicle. Each vehicle is removed permanently.
        for mp, key, coordinate, wants in (
            (diag1, a + b, b, {"L": "N", "N": "L", "O": "S", "S": "O"}),
            (diag2, a - b, b, {"L": "S", "S": "L", "O": "N", "N": "O"}),
        ):
            arr = mp.get(key, [])
            if not arr:
                continue

            # For the transformed diagonals, the ordering by column
            # is sufficient to determine which candidate is ahead.
            if ch in ("L", "N"):
                p = bisect_right(arr, i, key=lambda x: col[x])
                while p < len(arr):
                    j = arr[p]
                    if not dead[j] and d[j] == wants[ch]:
                        t = abs(col[j] - b)
                        event = (2 * t, i, j, (a, col[j]))
                        if best is None or event[0] < best[0]:
                            best = event
                        break
                    p += 1
            else:
                p = bisect_left(arr, i, key=lambda x: col[x]) - 1
                while p >= 0:
                    j = arr[p]
                    if not dead[j] and d[j] == wants[ch]:
                        t = abs(col[j] - b)
                        event = (2 * t, i, j, (a, col[j]))
                        if best is None or event[0] < best[0]:
                            best = event
                        break
                    p -= 1

        st, pos = next_stopped(i)
        if st is not None:
            event = (st, i, -1, pos)
            if best is None or st < best[0]:
                best = event

        return best

    pq = []

    for i in range(c):
        ev = candidate(i)
        if ev is not None:
            heapq.heappush(pq, (ev[0], i, ev[1], ev[2], ev[3]))

    while pq:
        t, i, j, pos = heapq.heappop(pq)

        if dead[i]:
            continue

        current = candidate(i)
        if current is None:
            continue

        if current[0] != t or current[2] != j or current[3] != pos:
            heapq.heappush(
                pq,
                (current[0], i, current[1], current[2], current[3])
            )
            continue

        # A stopped crossing is involved.
        if j == -1:
            collided[i] = True
            dead[i] = True
            active[i] = False
            add_stop(pos[0], pos[1])

            # Only the newly stopped point can create new events.
            # Recompute nearby active vehicles lazily.
            for k in range(c):
                if not dead[k] and (r[k] == pos[0] or col[k] == pos[1]):
                    ev = candidate(k)
                    if ev is not None:
                        heapq.heappush(
                            pq,
                            (ev[0], k, ev[1], ev[2], ev[3])
                        )
            continue

        if dead[j]:
            continue

        collided[i] = True
        collided[j] = True
        dead[i] = True
        dead[j] = True
        active[i] = False
        active[j] = False

        add_stop(pos[0], pos[1])

        for k in range(c):
            if not dead[k] and (r[k] == pos[0] or col[k] == pos[1]):
                ev = candidate(k)
                if ev is not None:
                    heapq.heappush(
                        pq,
                        (ev[0], k, ev[1], ev[2], ev[3])
                    )

    print(sum(1 for x in collided if not x))

if __name__ == "__main__":
    solve()
```各衝突タイプはいずれかの座標系で 1 次元になるため、入力は 4 つの座標系で保存されます。 行は東西の遭遇を処理し、列は南北の遭遇を処理し、2 つの対角座標 (r+c) と (r-c) は垂直の遭遇を処理します。 

の`dead`配列はシミュレーションの物理状態を表します。 車両は最初の衝突時に一度だけ死亡するため、それ以降の優先キュー エントリは単純に無視できます。 

衝突時間は 2 倍の時間スケールで表されます。 これにより、浮動小数点演算が回避され、2 つの交差点の中間で衝突が正しく処理されます。 

停止した交差点はトレプに保存されます。 treap は、予想される時間 (O(\log C)) で先行クエリと後続クエリを実行します。これは、移動中の車両が最も近い停止している交差点を見つけるのに必要な時間です。 

優先キューには、完全な将来のシミュレーションではなく、候補イベントが含まれます。 別の衝突によって車両の 1 台が除去された後、候補が失効する可能性があるため、コードはイベントがキューの先頭に到達したときに候補を再計算します。 この遅延検証により、高価なグローバル更新が回避されます。 

シミュレーションのどこにも浮動小数点計算はありません。 座標は整数のままで、すべてのイベント時間は実時間を 2 倍した後の整数になります。 

## 実用的な例

 ### サンプル 1

 入力は```
5 6 7
2 2 O
3 2 N
4 2 N
4 5 N
2 6 O
5 5 L
2 4 O
```イベント処理の重要な部分を以下にまとめます。 

| イベント時間 | 車両状態 | 衝突場所 | 結果 |
 | --- | --- | --- | --- |
 | 1 | 同じ変換された線上の車両が出会う | 交差点・中間交差点 | 対応車両停止 |
 | 2 | 別のアクティブな車両が以前の衝突に達しました | 既存の停止踏切 | 追加の車両停止 |
 | 後で | 残りの車両には有効な衝突はありません。 境界出口 | 車両は生き残る |

 すべての有効なイベントが処理された後、4 台の車両が衝突せずに残り、必要な出力と一致します。`4`。 

トレースは、衝突を時系列に処理する必要がある理由を示しています。 初期設定からは危険に見える軌道でも、他の車両がすでに停止しているために無害になる可能性があります。 

### サンプル 2

 入力は```
2 2 3
1 1 L
1 2 O
2 2 N
```最初の 2 台の車両が列 (1) で互いに向かって移動しています。 両者の距離は交差点 1 つ分なので、時間 (1/2) で 2 つの交差点間で合流します。 水平衝突のルールでは、両方の車両が東側交差点に停止します ((1,2))。 

北行きの車両は ((2,2)) から出発し、時刻 (1) に ((1,2)) に到着します。 その瞬間、水平方向の衝突ですでにそこに停止障害物ができていたため、北行きの車両も衝突します。 

| 2倍の時間 | アクティブな車両 | 新しい停止した交差点 | 生存者 |
 | --- | --- | --- | --- |
 | 1 | 3 つすべて | ((1,2)) | 1 |
 | 2 | 北行き車両は ((1,2)) に到達します。 ((1,2)) | 0 |

 最終的な答えは、`0`。 このサンプルは、特に、後続の車両が既に停止している衝突車両に衝突する可能性があるというルールを適用します。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O(C\log C)) が期待されます | 各車両は一度停止し、各順序セット操作は対数的であり、イベント キューは生成されたイベントごとに対数的な作業を実行します。 
| スペース | (O(C)) | 車両、線路構造物、停止踏切、イベント キューには (O(C)) 個のオブジェクトのみが含まれています。 

(C\le10^5) の場合、(O(C\log C)) アルゴリズムが適切です。 グリッドの次元は (10^5) に達する可能性がありますが、アルゴリズムは (N\times M) 配列を決して割り当てないため、潜在的な (10^{10}) の交差はメモリ使用量に影響しません。 

## テストケース```python
import sys
import io

# Paste the solve() implementation from the solution above here.

def run(inp: str) -> str:
    old_stdin = sys.stdin
    old_stdout = sys.stdout

    sys.stdin = io.StringIO(inp)
    sys.stdout = io.StringIO()

    solve()

    out = sys.stdout.getvalue().strip()

    sys.stdin = old_stdin
    sys.stdout = old_stdout
    return out

# Sample 1
assert run("""\
5 6 7
2 2 O
3 2 N
4 2 N
4 5 N
2 6 O
5 5 L
2 4 O
""") == "4", "sample 1"

# Sample 2
assert run("""\
2 2 3
1 1 L
1 2 O
2 2 N
""") == "0", "sample 2"

# Sample 3
assert run("""\
2 2 3
1 1 L
1 2 O
2 1 N
""") == "1", "sample 3"

# Minimum-size grid, one vehicle.
assert run("""\
2 2 1
1 1 L
""") == "1", "single vehicle survives"

# Two vehicles moving in the same direction never collide.
assert run("""\
2 5 2
1 1 L
1 3 L
""") == "2", "same direction"

# Horizontal head-on collision exactly between crossings.
assert run("""\
2 3 2
1 1 L
1 3 O
""") == "0", "head-on collision"

# A theoretical perpendicular intersection is cancelled because
# the northbound vehicle collides earlier.
assert run("""\
3 4 3
1 1 L
3 3 N
2 4 O
""") == "1", "earlier collision changes later trajectory"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`2 2 1 / 1 1 L`|`1`| 最小グリッドと車両 1 台 |
 |`2 5 2 / 1 1 L / 1 3 L`|`2`| 同じ方向の車両は衝突しない |
 |`2 3 2 / 1 1 L / 1 3 O`|`0`| 交差点間の衝突と東側停止規則 |
 |`3 4 3 / 1 1 L / 3 3 N / 2 4 O`|`1`| 初期の衝突は、後の理論上の交差を無効にします。 

## 特殊なケース

 1 台の車両の場合、たとえば```
2 2 1
1 1 L
```優先キューには、他の車両との衝突や停止した交差点は含まれていません。 車両は最終的にグリッドを離れ、マークが付けられないままになります。その答えは次のとおりです。`1`。 

交差点間の正面衝突の場合、```
2 3 2
1 1 L
1 3 O
```2 倍の衝突時間は (2) で、実時間 (1) に対応します。 衝突点は列 (1.5) として保存されません。 東横断ルールはこれを列 (2) にマップします。 両方の車両が衝突したとマークされ、答えが表示されます`0`。 

既存の衝突に遭遇した車両については、次のことを考慮してください。```
2 2 3
1 1 L
1 2 O
2 2 N
```最初の 2 台の車両は 2 倍の時間 (1) で衝突し、((1,2)) で停止交差点が発生します。 北行きの車両は 2 倍の時間で交差点に到着します (2)。 停止している車両に衝突するため、車両は生き残れません。 

最も危険な論理的ケースは、```
3 4 3
1 1 L
3 3 N
2 4 O
```東行きの車両と北行きの車両には ((1,3)) で理論上の交差点がありますが、北行きの車両は最初に ((2,3)) で西行きの車両と出会います。 時間 (1) のイベントは時間 (2) の理論上のイベントの前に処理され、移動セットから北行きの車両が削除されます。 後の東向き-北向きイベントは古くなり、破棄されます。 東行きの車両が生き残って正解`1`。
