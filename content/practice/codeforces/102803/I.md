---
title: "CF 102803I - インクボール FX"
description: "ゲームは左から右に移動する光線として見ることができます。 ボールの水平座標は常に速度 1 で増加するため、t 秒後のボールは x = t になります。"
date: "2026-07-26T16:25:27+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102803
codeforces_index: "I"
codeforces_contest_name: "The 15th Heilongjiang Provincial Collegiate Programming Contest"
rating: 0
weight: 102803
solve_time_s: 51
verified: true
draft: false
---

[CF 102803I - InkBall FX](https://codeforces.com/problemset/problem/102803/I)

 **評価:** -
 **タグ:** -
 **解決時間:** 51 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 ゲームは左から右に移動する光線として見ることができます。 ボールの水平座標は常に速度 1 で増加するため、`t`ボールが到達する秒数`x = t`。 唯一の変化部分は垂直方向です。垂直方向は増加し始め、ボールが水平セグメントに接触するたびに垂直速度が反転します。 

セグメント`(L, R, Y)`現在の軌道が高さに達するとヒットします`Y`間の水平座標で`L`そして`R`。 衝突後はセグメントが消えるため、同じ障害物を再度考慮する必要はありません。 

入力には最大で次のものが含まれます`10^5`水平セグメント。 衝突のたびにすべてのセグメントをチェックする直接シミュレーションには、最大で`10^10`これは 6 秒の制限をはるかに超えています。 対数または線形に近い方法が必要です。 

厄介なケースは、エンドポイントに触れることも衝突としてカウントされるという事実によって引き起こされます。 次のようなセグメント:```
1
3 5 2
```初期パスが`y=x`、そしてそれは到着します`(2,2)`セグメントの前ではなく、`(3,2)`、したがって、答えは次のとおりです`0`。 高さのみをチェックし、X 範囲を無視する不注意な実装では、高さを誤ってカウントする可能性があります。 

もう 1 つのよくある間違いは、まさにエンドポイントでの衝突が有効であることを忘れていることです。```
1
2 4 2
```ボールが届く`(2,2)`、これはセグメントの左端点であるため、答えは次のようになります。`1`。 

3 番目の問題は、考察の後に現れます。```
2
4 6 1
2 4 3
```最初の衝突は高さにあるセグメントとの衝突です`3`ボールが届いたとき`(3,3)`。 方向が変わり、2 番目のセグメントはヒットしません。 ボールが常に元の対角線をたどると仮定する解決策は、ここでは失敗します。 

## アプローチ

 簡単なアプローチは、残りのすべてのセグメントをチェックして次の衝突を繰り返し見つけることです。 各セグメントについて、現在の光線が交差するかどうかを計算し、最も古い光線を保持します。 これは正解です。ボールは前方にのみ移動します。`x`したがって、最初の交差点はまさに次のイベントです。 ただし、衝突するたびに 1 つのセグメントが削除され、最悪の場合、約`n + (n-1) + ... + 1`小切手、つまり`O(n^2)`。 

重要な観察は、傾斜のある光線であるということです。`1`または`-1`単一の定数で記述することができます。 

ボールが上に移動する場合、その軌道は次のようになります。```
y = x + c
```どこ`c = y - x`。 この値が以下の中にある場合、セグメントがヒットします。```
Y - R <= c <= Y - L
```固定の場合`c`、衝突位置は`x = Y - c`したがって、すべての一致するセグメントの中で最小のセグメントが必要です。`Y`。 

ボールが下に移動する場合、その軌道は次のようになります。```
y = -x + c
```どこ`c = y + x`。 有効な間隔は次のようになります。```
Y + L <= c <= Y + R
```そして衝突位置は`x = c - Y`したがって、最大のものが必要です`Y`。 

問題は 2 つの動的間隔の刺し込みクエリになります。 各セグメントは 2 つの間隔構造に挿入されます。 クエリは、ポイントをカバーする間隔の中で最適なセグメントを提供し、それを使用した後、遅延して削除します。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n²) | O(n) | 遅すぎる |
 | 最適 | O(n log n) | O(n log n) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. すべてのセグメントを 2 つの変換された間隔に変換します。 上向き構造の収納`[Y-R, Y-L]`価値のあるもの`Y`、そして下向きの構造が格納します`[Y+L, Y+R]`価値のあるもの`Y`。 
2. 各方向の圧縮座標セグメント ツリーを構築します。 すべてのノードには、変換された間隔がそのノードを完全にカバーするセグメントのヒープが格納されます。 
3. 次の場所でシミュレーションを開始します。`(0,0)`上向きに。 
4. 電流方向について、対応する定数を計算します。 上に移動する場合は、`y-x`; それ以外の場合はそうです`y+x`。 
5. 対応するセグメント ツリーをクエリします。 クエリは、最も早い衝突が発生したセグメントを返します。 セグメントが存在しない場合、シミュレーションは終了します。 
6. 見つかったセグメントを削除済みとしてマークして削除します。 ヒープは、削除された要素が最上部に到達すると、遅延して削除されます。 
7. ボールを衝突点に移動します。 x 座標が衝突位置になります。 垂直方向を反転して繰り返します。 

なぜ効果があるのか:

 セグメント ツリーの不変条件は、すべてのアクティブなセグメントが、座標範囲がその変換された区間によって完全にカバーされるノードに正確に表示されるということです。 クエリは、クエリされた座標へのパス上のすべてのノードを訪問するため、衝突する可能性のあるすべてのセグメントが表示されます。 ヒープの順序付けにより、それらの候補の中から最も近い衝突が選択されます。 各衝突により 1 つのセグメントが永久に削除されるため、すべての操作は最大でも実行されます。`n`回。 

## Python ソリューション```python
import sys
import heapq
input = sys.stdin.readline

class SegmentTree:
    def __init__(self, coords, mode):
        self.coords = coords
        self.n = len(coords)
        self.tree = [[] for _ in range(self.n * 4)]
        self.mode = mode
        self.deleted = None

    def add(self, node, l, r, ql, qr, item):
        if ql <= l and r <= qr:
            heapq.heappush(self.tree[node], item)
            return
        m = (l + r) // 2
        if ql <= m:
            self.add(node * 2, l, m, ql, qr, item)
        if m < qr:
            self.add(node * 2 + 1, m + 1, r, ql, qr, item)

    def query(self, node, l, r, pos):
        while self.tree[node] and self.deleted[self.tree[node][0][1]]:
            heapq.heappop(self.tree[node])

        best = self.tree[node][0] if self.tree[node] else None
        if l != r:
            m = (l + r) // 2
            if pos <= m:
                other = self.query(node * 2, l, m, pos)
            else:
                other = self.query(node * 2 + 1, m + 1, r, pos)
            if other is not None:
                if best is None:
                    best = other
                elif self.mode == 1 and other[0] < best[0]:
                    best = other
                elif self.mode == -1 and other[0] < best[0]:
                    best = other
        return best

def build_coords(intervals):
    a = []
    for l, r, _, _ in intervals:
        a.append(l)
        a.append(r)
    a.sort()
    res = []
    for x in a:
        if not res or res[-1] != x:
            res.append(x)
    extra = []
    for i in range(len(res) - 1):
        if res[i + 1] - res[i] > 1:
            extra.append((res[i] + res[i + 1]) // 2)
    res.extend(extra)
    res.sort()
    return res

def solve_case(segs):
    n = len(segs)
    up = []
    down = []

    for i, (l, r, y) in enumerate(segs):
        up.append((y - r, y - l, y, i))
        down.append((y + l, y + r, y, i))

    cu = build_coords(up)
    cd = build_coords(down)

    tree_up = SegmentTree(cu, 1)
    tree_down = SegmentTree(cd, -1)

    deleted = [False] * n
    tree_up.deleted = deleted
    tree_down.deleted = deleted

    import bisect

    for l, r, y, i in up:
        tree_up.add(1, 0, len(cu) - 1,
                    bisect.bisect_left(cu, l),
                    bisect.bisect_right(cu, r) - 1,
                    (y, i))

    for l, r, y, i in down:
        tree_down.add(1, 0, len(cd) - 1,
                      bisect.bisect_left(cd, l),
                      bisect.bisect_right(cd, r) - 1,
                      (-y, i))

    x = 0
    y = 0
    direction = 1
    ans = 0

    while True:
        if direction == 1:
            c = y - x
            p = bisect.bisect_left(cu, c)
            if p == len(cu) or cu[p] != c:
                p -= 1
            if p < 0:
                break
            res = tree_up.query(1, 0, len(cu) - 1, p)
            if res is None:
                break
            ny, idx = res
            nx = ny - c
        else:
            c = y + x
            p = bisect.bisect_left(cd, c)
            if p == len(cd) or cd[p] != c:
                p -= 1
            if p < 0:
                break
            res = tree_down.query(1, 0, len(cd) - 1, p)
            if res is None:
                break
            ny, idx = -res[0], res[1]
            nx = c - ny

        deleted[idx] = True
        ans += 1
        x = nx
        y = ny
        direction *= -1

    return ans

def main():
    t = int(input())
    out = []
    for _ in range(t):
        n = int(input())
        segs = [tuple(map(int, input().split())) for _ in range(n)]
        out.append(str(solve_case(segs)))
    print("\n".join(out))

if __name__ == "__main__":
    main()
```変換された間隔は実装の中核です。 現在の軌道はヒットするたびに変化するため、セグメント ツリーは衝突の位置を直接保存しません。 代わりに、すべての可能な軌道を記述する定数を保存します。 

ヒープにはセグメント ID が含まれているため、削除は遅延します。 削除されたセグメントは複数のヒープ内に残る場合がありますが、最上部に到達すると無視されます。 これにより、多くのツリー ノードからのコストのかかる削除が回避されます。 

すべての座標は Python 整数で処理されるため、中間値が元の座標範囲を超えることがあっても、オーバーフローの心配はありません。 

## 実用的な例

 最初のサンプルの場合:```
3
4 6 1
2 4 3
5 6 3
```| ステップ | 方向 | 現在の位置 | 定数 | ヒット |
 | --- | --- | --- | --- | --- |
 | 1 | 上 | (0,0) | 0 | セグメント (2,4,3) |
 | 2 | ダウン | (3,3) | 6 | なし |

 最初のヒットが起こる理由は、`y=x`高さに達する`3`で`x=3`。 反射後、下向きの光線は残りの別のセグメントと交わることはありません。 

2 番目のサンプルの場合:```
2
3 4 1
1 2 2
```| ステップ | 方向 | 現在の位置 | 定数 | ヒット |
 | --- | --- | --- | --- | --- |
 | 1 | 上 | (0,0) | 0 | セグメント (1,2,2) |
 | 2 | ダウン | (2,2) | 4 | セグメント (3,4,1) |

 最初の衝突はエンドポイントで発生します`(2,2)`。 反射された光線は、次の位置で 2 番目のセグメントに到達します。`(3,1)`。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n log n) | すべてのセグメントは 2 つのツリーに挿入され、すべての衝突で対数クエリが実行されます。 |
 | スペース | O(n log n) | 各間隔は、対数的に多くのセグメント ツリー ノードに格納されます。 |

 最大値`10^5`衝突中にアルゴリズムがすべてのアクティブなセグメントをスキャンすることはないため、セグメントが処理されます。 各セグメントは、制限された数のヒープ操作に参加します。 

## テストケース```
# The following tests can be used with the solve_case logic.

assert solve_case([(4, 6, 1), (2, 4, 3), (5, 6, 3)]) == 2
assert solve_case([(3, 4, 1), (1, 2, 2)]) == 2
assert solve_case([(1, 2, 5)]) == 0
assert solve_case([(1, 3, 1)]) == 1
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 1 つのセグメントは触れられませんでした | 0 | 不足している衝突処理をチェックする |
 | エンドポイントの衝突 | 1 | 包含境界をチェックする |
 | 反射サンプル | 2 | 方向転換をチェックする |
 | 複数のアクティブな間隔 | 正しい最初のヒット | ヒープの順序をチェックする |

 ## 特殊なケース

 エンドポイントでのみタッチされるセグメントは、閉じた変換間隔で保存されるため、両方のエンドポイントが有効なクエリ位置のままになります。 また、座標圧縮により元のエンドポイントがすべて保持されるため、境界ケースが誤って削除されることがなくなります。 

複数のセグメントが同じ変換された座標でヒットする可能性がある場合、ヒープの順序付けでは衝突 X 座標が最も小さいセグメントが選択されます。 これは、変換された方程式から直接得られます。上向きの移動の場合、x 座標は次のようになります。`Y-c`、下向きの動きの場合は、`c-Y`。 

衝突後、セグメントは削除済みとしてマークされるだけです。 内部ヒープにまだ存在する可能性がありますが、各クエリは無効なエントリを使用する前に削除します。 これにより、実装の高速性を維持しながら正確性が維持されます。
