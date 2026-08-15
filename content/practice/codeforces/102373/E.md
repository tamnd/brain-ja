---
title: "CF 102373E - 市松模様"
description: "セルが黒または白の (n 倍 m) の長方形のボードがあります。 任意の数のセルを変更した後、黒いセルは空ではない接続されたグラフを形成する必要があります。このグラフでは、辺を共有するセルが隣接しており、そのグラフにはサイクルが含まれていてはなりません。"
date: "2026-08-14T12:39:19+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102373
codeforces_index: "E"
codeforces_contest_name: "\u0426\u0438\u043a\u043b \u0418\u043d\u0442\u0435\u0440\u043d\u0435\u0442-\u043e\u043b\u0438\u043c\u043f\u0438\u0430\u0434 \u0434\u043b\u044f \u0448\u043a\u043e\u043b\u044c\u043d\u0438\u043a\u043e\u0432, \u0421\u0435\u0437\u043e\u043d 2019-2020, \u041f\u0435\u0440\u0432\u0430\u044f \u043a\u043e\u043c\u0430\u043d\u0434\u043d\u0430\u044f \u043e\u043b\u0438\u043c\u043f\u0438\u0430\u0434\u0430"
rating: 0
weight: 102373
solve_time_s: 587
verified: false
draft: false
---

[CF 102373E - 市松模様](https://codeforces.com/problemset/problem/102373/E)

 **評価:** -
 **タグ:** -
 **解決時間:** 9 分 47 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 セルが黒または白の (n \times m) 個の長方形のボードがあります。 任意の数のセルを変更した後、黒いセルは空ではない接続されたグラフを形成する必要があります。このグラフでは、辺を共有するセルが隣接しており、そのグラフにはサイクルが含まれていてはなりません。 グラフの用語では、黒いセルの最終セットは正確に 1 つのツリーを誘導する必要があります。 

変更されたすべてのセルはコストに 1 単位を寄与するため、タスクは、元のボードからブラック セル グラフがツリーであるボードへの最小ハミング距離変換を見つけることです。 

寸法は意図的に非対称になっています。 高さは (100) まで可能ですが、幅は最大でも (10) です。 水平境界に関するすべての関連性を記述する状態は、結果的に (m) に指数関数的に依存する可能性がありますが、(m) は (10) にすぎないため実用的であり続けます。 (n m) で指数関数的なアルゴリズムは (1000) 個のセルが存在する可能性があるため不可能ですが、指数関数部分が (m) のみに依存するアルゴリズムが自然なターゲットです。 

最初のエッジ ケースは、完全にホワイト ボードです。 例えば、```
.
```必要な黒色のグラフは空ではない必要があるため、変更しないままにすることはできません。 正しい結果は```
#
```1 つの変更で。 接続とサイクルのみをチェックするソリューションでは、誤って空のグラフを受け入れる可能性があります。 

2 番目のエッジ ケースは、切断されたフォレストです。 例えば、```
#.#
```すでにサイクルはありませんが、2 つの黒いセルは別個のコンポーネントです。 1 つの変更が必要であり、```
###
```は有効な最適な結果です。 最終的なグラフにはコンポーネントが 1 つだけ含まれている必要があるため、サイクルだけをチェックするだけでは不十分です。 

3 番目のエッジ ケースは、完全に黒い (2 \times 2) 正方形を含まないサイクルです。 考慮する```
###
#.#
###
```8 つの黒い境界セルは、白い中心の周囲に 1 つのサイクルを形成します。 1 つの境界セルを削除すると、そのサイクルが中断され、1 つの変更が最適化されます。 完全な (2 \times 2) 個の黒い四角だけを探すテストでは、このサイクルが失われます。 

最後に、大きなボード内の 1 つの黒いセルは常に有効なツリーです。 たとえば、(100 \times 10) のオールホワイト ボードの場合、変更は 1 回だけ必要です。 これは、大きな高さの境界と、少なくとも 1 つの黒セルが存在するという要件の両方をチェックするので便利です。 

## アプローチ

 直接的な解決策は、考えられるすべての最終的な色を列挙することになります。 黒である可能性のあるセルのサブセットは (2^{nm}) 個あります。 各サブセットについて、その誘導グラフを構築し、接続性と非循環性をテストし、入力からの距離を計算できます。 これは正しいです。考えられるすべての最終パターンが明示的に考慮されますが、最悪の場合の動作は (O(nm2^{nm})) になります。 最大値 (nm=1000) では、およそ (1000\cdot2^{1000}) 回の演算となり、実行可能な演算をはるかに超えています。 

有益な観察は、既に処理されたボード全体を記憶する必要がないということです。 ボードを行ごとに、各行内で左から右にスキャンします。 セルがスキャン境界の背後にある場合、その黒コンポーネントが未処理のセルと相互作用できる唯一の方法は、処理済みセルと未処理セルの間の現在の境界を通過することです。 

したがって、すべての列について、フロンティア セルが黒かどうか、黒の場合はどの連結成分に属しているかを記憶します。 同じラベルが付いた 2 つのフロンティア位置は、基板の処理された部分の同じコンポーネントに属します。 ラベル自体には意味がないため、正規化されます。たとえば、((4,4,0,7)) は ((1,1,0,2)) になります。 

新しい黒セルが挿入されると、すでに処理された隣接セルは最大で 2 つ、左の隣接セルと上側の隣接セルになります。 両方が存在し、同じコンポーネントに属している場合、新しいセルを追加するとサイクルが作成されます。 それらが異なるコンポーネントに属している場合、新しいセルはそれらのコンポーネントをマージします。 どちらも存在しない場合は、新しいコンポーネントが開始されます。 

接続には追加の状態が 1 つ必要です。 コンポーネントがフロンティアから完全に消滅すると、将来のセルはそのコンポーネントに触れることができなくなります。 このようなコンポーネントは永久に完成します。 有効な最終ソリューションには最大 1 つの完成コンポーネントを含めることができ、それが完成すると、それ以上黒いセルを選択できなくなります。 これにより、無用な状態を保持する代わりに、切断された部分解を即座に拒否できるようになります。 

状態の動的プログラミング値は、その状態に到達するために必要な再着色の最小回数です。 将来の決定は、正確な履歴ではなくフロンティアの接続状態のみに依存するため、各状態に到達するための最も安価な方法のみを維持するだけで十分です。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | (O(nm2^{nm})) | (O(nm)) | 遅すぎる |
 | フロンティアDP | (O(nm^2S_m)) | (O(nS_m)) 再構成あり | 承認済み |

 ここで (S_m) は、幅 (m) に対して到達可能なフロンティア シグネチャの数を示します。 (m\le10) なので、これはこの問題の幅に依存する定数です。 コンポーネントは平面グリッド フォレストから生じるため、実際に到達可能な状態は、すべての任意のラベル付けよりもはるかに少なくなります。 

## アルゴリズムのチュートリアル

1. セルを行優先の順序でスキャンし、正確に (m) 個の位置のフロンティアを維持します。 位置 (c) は、列 (​​c) の現在の境界に接する処理済みの黒コンポーネントを表します。 ゼロは、フロンティア セルが白であるか、境界に接する黒いコンポーネントがないことを意味します。 
2. すべての遷移後にすべてのコンポーネント ラベルを正規化します。 たとえば、状態 ((7,7,0,3)) と ((1,1,0,2)) はまったく同じ接続を表すため、同じ DP 状態として保存する必要があります。 正規化がなければ、同等の状態の数が不必要に増加してしまいます。 
3. すべてのセルを白くしてみます。 すでに白だった場合のコストは (0)、元々黒だった場合は (1) です。 フロンティアの位置をゼロに置き換えます。 これにより、コンポーネントの最後のフロンティア オカレンスが削除された場合、そのコンポーネントは永久に閉じられたことになります。 閉じたコンポーネントは後で何にも接続できないため、この遷移は他にアクティブなコンポーネントがない場合にのみ保持してください。 
4. セルを黒にしてみます。 そのコストは、元のセルが黒の場合は (0)、そうでない場合は (1) です。 以前に終了したコンポーネントが存在する場合は、新しい黒いセルによって別のコンポーネントが作成されるため、この遷移は拒否されます。 
5. 上部と左側のフロンティア ラベルを確認します。 両方がゼロ以外で等しい場合、新しいセルは同じコンポーネントの既に接続されている 2 つの頂点を結合するため、新しいエッジはサイクルを閉じます。 移行を拒否します。 
6. 上と左のラベルがゼロ以外の異なる値である場合、それらのコンポーネントをマージし、マージされたコンポーネントを新しいセルに割り当てます。 1 つだけ存在する場合は、新しいセルをそのコンポーネントにアタッチします。 どちらも存在しない場合は、新しいコンポーネントを作成します。 
7. すべてのセルを処理した後、アクティブなコンポーネントが 1 つだけ、または以前に終了したコンポーネントが 1 つだけ存在する場合は、状態を受け入れます。 少なくとも 1 つの黒セルが必要であるため、完全に空の状態は拒否されます。 
8. 状態がより良いコストを取得するたびに、行ごとに、先行状態と選択された黒セル マスクを保存します。 最適な最終状態を見つけた後、これらの先行レコードを逆方向にたどり、出力ボードのすべての行を回復します。 

### なぜ効果があるのか

 中心となる不変条件は、ゼロ以外のすべてのフロンティア ラベルが、これまでに処理された黒セルの 1 つの接続コンポーネントを正確に表し、フロンティアに触れなくなったすべての処理済みコンポーネントが永久に閉じられていることです。 セルの処理時に導入される唯一のエッジは、すでに処理された左および上に隣接するセルのエッジです。 したがって、両方が存在し、同じコンポーネントに属している場合に、サイクルが正確に作成されます。これはまさに、私たちが拒否する遷移です。 

フロンティアから消えるコンポーネントには未処理のセルへのエッジがないため、後で作成されたコンポーネントに接続することはできません。 したがって、最終的なグラフを接続するには、1 つのコンポーネントを閉じて別のコンポーネントを残す遷移を拒否することが必要です。 最後に、コンポーネントが 1 つ残っているということは、黒のセルが接続されている一方で、受け入れられたすべての挿入がサイクルの作成を回避していることを意味します。 したがって、最後の黒いグラフはツリーになります。 

最適化のため、各 DP 状態は、同じフロンティア情報を持つすべての部分ボード間で最小限の変更数を維持します。 将来の可能性はすべてその情報のみに依存するため、同じ状態に到達するためのより高価な方法では、より良い最終的な答えは得られません。 したがって、最小コストで受け入れられる最終状態は全体的に最適です。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

def normalize(a):
    mp = {}
    nxt = 1
    for i, x in enumerate(a):
        if x:
            y = mp.get(x)
            if y is None:
                y = nxt
                mp[x] = y
                nxt += 1
            a[i] = y
    return tuple(a)

def solve_case(grid):
    n = len(grid)
    m = len(grid[0])

    start_state = (0,) * m
    start_key = (start_state, 0)

    # dp[(frontier, finished)] = minimum number of changes
    dp = {start_key: 0}

    # parents[r][state] = (previous_state, row_mask)
    parents = []

    INF = 10 ** 9

    for r in range(n):
        # value = (cost, state_before_this_row, row_mask)
        cur = {
            key: (cost, key, 0)
            for key, cost in dp.items()
        }

        for c in range(m):
            nxt = {}

            for (state, finished), (cost, prev_key, row_mask) in cur.items():
                old = state[c]
                left = state[c - 1] if c > 0 else 0
                up = old

                # Choose white.
                white_cost = cost + (grid[r][c] == '#')
                a = list(state)
                a[c] = 0

                new_finished = finished

                if old:
                    still_alive = old in a
                    if not still_alive:
                        # A component disappeared from the frontier.
                        # It is safe only if it is the only component.
                        if any(a):
                            still_alive = False
                            new_finished = -1
                        else:
                            new_finished = 1

                if new_finished != -1:
                    ns = normalize(a)
                    nk = (ns, new_finished)

                    if white_cost < nxt.get(nk, (INF, None, None))[0]:
                        nxt[nk] = (
                            white_cost,
                            prev_key,
                            row_mask
                        )

                # Choose black.
                if not finished:
                    # If left and up belong to the same component,
                    # the two edges from the new cell close a cycle.
                    if not (left and up and left == up):
                        a = list(state)

                        if up and left and up != left:
                            # Merge left's component into up's component.
                            for i in range(m):
                                if a[i] == left:
                                    a[i] = up
                            new_label = up
                        elif up:
                            new_label = up
                        elif left:
                            new_label = left
                        else:
                            new_label = max(a) + 1

                        a[c] = new_label
                        ns = normalize(a)

                        black_cost = cost + (grid[r][c] == '.')
                        nk = (ns, finished)
                        nmask = row_mask | (1 << c)

                        if black_cost < nxt.get(
                            nk, (INF, None, None)
                        )[0]:
                            nxt[nk] = (
                                black_cost,
                                prev_key,
                                nmask
                            )

            cur = nxt

        ndp = {}
        par = {}

        for key, (cost, prev_key, row_mask) in cur.items():
            ndp[key] = cost
            par[key] = (prev_key, row_mask)

        dp = ndp
        parents.append(par)

    best_key = None
    best_cost = INF

    for (state, finished), cost in dp.items():
        if finished:
            if cost < best_cost:
                best_cost = cost
                best_key = (state, finished)
        else:
            components = len({x for x in state if x})
            if components == 1 and cost < best_cost:
                best_cost = cost
                best_key = (state, finished)

    # A one-cell black tree always exists, so best_key must exist.
    row_masks = [0] * n

    key = best_key
    for r in range(n - 1, -1, -1):
        prev_key, mask = parents[r][key]
        row_masks[r] = mask
        key = prev_key

    answer = []
    for r in range(n):
        row = []
        for c in range(m):
            row.append('#' if (row_masks[r] >> c) & 1 else '.')
        answer.append(''.join(row))

    return answer

def main():
    n, m = map(int, input().split())
    grid = [input().strip() for _ in range(n)]

    answer = solve_case(grid)
    sys.stdout.write('\n'.join(answer))

if __name__ == "__main__":
    main()
```DP キーはフロンティア タプルと`finished`フラグ。 タプルには接続情報のみが含まれており、黒いセルの数や古いコンポーネントの正確な座標は含まれていません。これは、どちらも将来の遷移に影響を及ぼさないためです。 

白い遷移は、現在のフロンティア位置をゼロに置き換えます。 微妙な部分は、消えるコンポーネントの検出です。 その後古いラベルがなくなり、別のゼロ以外のラベルが残った場合、部分グラフは永久に切断されたことになり、その遷移は破棄されます。 他のコンポーネントが残っていない場合は、その 1 つのコンポーネントが単に終了したことになり、`finished`flag は、今後黒セルを追加できないことを記録します。 

ブラックトランジションの場合、`up`は現在の列の古い値であり、`left`は、前の列ですでに更新されている値です。 この順序付けは不可欠です。 セル ((r,c)) が処理される時点では、これらはまさにすでに処理済みの隣接セルです。 

テスト`left and up and left == up`新しく作成されたすべてのサイクルを検出します。 両方の隣接セルが同じコンポーネントに属している場合、新しいセルはそれらの間に 2 番目のルートを提供します。 それらが異なるコンポーネントに属している場合、新しいセルは 2 つのツリーをより大きなツリーに安全に結合します。 

各親レコードに格納されている行マスクは、行内のすべての選択肢がそのマスクによって表されるため、再構築には十分です。 DP 自体は結果として得られるフロンティア状態のみを必要としますが、先行レコードはそれを取得するためにどの行が選択されたかを記憶しています。 

Python の整数には制限がありませんが、コストは最大 (nm) であるため、オーバーフローの心配はありません。 (m\le10) のため、ビット マスクには最大 10 ビットが含まれます。 

## 実用的な例

 サンプル 1 の場合、最適な最終パターンの 1 つは次のとおりです。`##.`、`#.#`、`###`。 右上のセルのみを変更します。 次のトレースでは、標準コンポーネント ラベルが使用されています。ラベルが等しいということは、対応するフロンティア セルが接続されていることを意味します。 

| 処理された行 | 選択された行 | フロンティアに次ぐ行 | 完了 |
 | --- | --- | --- | --- |
 | 開始 | なし |`(0,0,0)`| 0 |
 | 1 |`##.`|`(1,1,0)`| 0 |
 | 2 |`#.#`|`(1,0,2)`| 0 |
 | 3 |`###`|`(1,1,1)`| 0 |
 | 終わり |`##./#.#/###`| 1 つのコンポーネント | 0 |

 最初の行の後、2 つの黒いセルが 1 つのコンポーネントを形成します。 2行目では、中央のセルが白なので、左右の黒グループが一時的に分離されます。 最後の行では、中央のセルがこれら 2 つの異なるコンポーネントを結合します。 その場合ラベルが異なるため、サイクルは作成されません。 7 つの黒いセルと 6 つのエッジが残るため、結果は木になります。 コストは1つです。 

サンプル 2 では、サンプル出力自体をトレースされた最適パターンとして使用できます。 

| 処理された行 | 選択された行 | フロンティアに次ぐ行 | 完了 |
 | --- | --- | --- | --- |
 | 開始 | なし |`(0,0,0)`| 0 |
 | 1 |`##.`|`(1,1,0)`| 0 |
 | 2 |`.##`|`(0,1,1)`| 0 |
 | 3 |`#.#`|`(2,1,1)`| 0 |
 | 4 |`###`|`(1,1,1)`| 0 |
 | 終わり |`##./.##/#.#/###`| 1 つのコンポーネント | 0 |

 3 行目は一時的に 2 つのコンポーネントを作成します。 4 行目の最初のセルは左側のコンポーネントを拡張し、2 番目のセルはそれを再度拡張します。 最後のセルは、1 つのコンポーネントから左隣を認識し、もう 1 つのコンポーネントから上隣を認識するため、サイクルを作成するのではなく、それらをマージします。 ちょうど 2 つのセルが入力と異なり、記載された最適値と一致します。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O(nm^2S_m)) | 各 (nm) 個のセルは、到達可能なすべてのフロンティア状態と、正規化とコンポーネントのマージ コスト (O(m)) を処理します。 |
 | スペース | (O(nS_m)) | 現在の DP は (O(S_m)) 個の状態を使用しますが、行先行レコードは再構築に (O(nS_m)) 個のスペースを使用します。 |

 ここで (S_m) は幅のみに依存します。 幅の上限は (10) ですが、高さは (100) のみです。これはまさにフロンティア動的プログラミングが役立つ領域です。 このアルゴリズムは、(2^{nm}) 個の完全なボードを列挙することはありません。 

## テストケース

 以下のテストでは、問題ではあらゆる最適なパターンが許容されるため、正確なテキストを比較するのではなく、出力を構造的に検証します。 チェッカーは、出力がツリーであること、およびその再カラーリングの数が既知の最適値と等しいことを検証します。```python
import sys
import io
from collections import deque

def normalize(a):
    mp = {}
    nxt = 1
    for i, x in enumerate(a):
        if x:
            y = mp.get(x)
            if y is None:
                y = nxt
                mp[x] = y
                nxt += 1
            a[i] = y
    return tuple(a)

def solve_case(grid):
    n = len(grid)
    m = len(grid[0])

    dp = {((0,) * m, 0): 0}
    parents = []
    INF = 10 ** 9

    for r in range(n):
        cur = {
            key: (cost, key, 0)
            for key, cost in dp.items()
        }

        for c in range(m):
            nxt = {}

            for (state, finished), (cost, prev, mask) in cur.items():
                old = state[c]
                left = state[c - 1] if c else 0
                up = old

                # White
                a = list(state)
                a[c] = 0
                nf = finished

                if old and old not in a:
                    if any(a):
                        nf = -1
                    else:
                        nf = 1

                if nf != -1:
                    ns = normalize(a)
                    key = (ns, nf)
                    value = cost + (grid[r][c] == '#')
                    if value < nxt.get(key, (INF, None, None))[0]:
                        nxt[key] = (value, prev, mask)

                # Black
                if not finished and not (
                    left and up and left == up
                ):
                    a = list(state)

                    if left and up and left != up:
                        for i in range(m):
                            if a[i] == left:
                                a[i] = up
                        label = up
                    elif up:
                        label = up
                    elif left:
                        label = left
                    else:
                        label = max(a) + 1

                    a[c] = label
                    ns = normalize(a)
                    key = (ns, finished)
                    value = cost + (grid[r][c] == '.')
                    nmask = mask | (1 << c)

                    if value < nxt.get(key, (INF, None, None))[0]:
                        nxt[key] = (value, prev, nmask)

            cur = nxt

        dp = {key: value[0] for key, value in cur.items()}
        parents.append({
            key: (value[1], value[2])
            for key, value in cur.items()
        })

    best = None
    best_cost = INF

    for (state, finished), cost in dp.items():
        if finished or len({x for x in state if x}) == 1:
            if cost < best_cost:
                best_cost = cost
                best = (state, finished)

    masks = [0] * n
    key = best

    for r in range(n - 1, -1, -1):
        key, masks[r] = parents[r][key]

    return [
        ''.join('#' if (masks[r] >> c) & 1 else '.'
                for c in range(len(grid[0])))
        for r in range(n)
    ]

def run(inp: str) -> str:
    old_stdin = sys.stdin
    sys.stdin = io.StringIO(inp)
    n, m = map(int, input().split())
    grid = [input().strip() for _ in range(n)]
    ans = solve_case(grid)
    sys.stdin = old_stdin
    return '\n'.join(ans)

def is_tree(board):
    n = len(board)
    m = len(board[0])

    cells = [
        (r, c)
        for r in range(n)
        for c in range(m)
        if board[r][c] == '#'
    ]

    if not cells:
        return False

    seen = {cells[0]}
    q = deque([cells[0]])

    while q:
        r, c = q.popleft()
        for dr, dc in ((1, 0), (-1, 0), (0, 1), (0, -1)):
            nr, nc = r + dr, c + dc
            if (
                0 <= nr < n and
                0 <= nc < m and
                board[nr][nc] == '#'
                and (nr, nc) not in seen
            ):
                seen.add((nr, nc))
                q.append((nr, nc))

    if len(seen) != len(cells):
        return False

    edges = 0
    for r, c in cells:
        if r + 1 < n and board[r + 1][c] == '#':
            edges += 1
        if c + 1 < m and board[r][c + 1] == '#':
            edges += 1

    return edges == len(cells) - 1

def check(inp, expected_cost):
    first = inp.splitlines()
    n, m = map(int, first[0].split())
    original = first[1:n + 1]

    output = run(inp)
    board = output.splitlines()

    assert len(board) == n
    assert all(len(row) == m for row in board)
    assert all(ch in '.#' for row in board for ch in row)
    assert is_tree(board)

    cost = sum(
        original[r][c] != board[r][c]
        for r in range(n)
        for c in range(m)
    )
    assert cost == expected_cost

# Provided samples
check(
    """3 3
###
#.#
###
""",
    1
)

check(
    """4 3
##.
.##
###
##.
""",
    2
)

check(
    """2 3
...
...
""",
    1
)

# Minimum-size input, already valid
check(
    """1 1
#
""",
    0
)

# Minimum-size input, empty black graph
check(
    """1 1
.
""",
    1
)

# Disconnected forest, one change is enough
check(
    """1 3
#.#
""",
    1
)

# Maximum-size board, one black cell is optimal
check(
    "100 10\n" + "\n".join(["." * 10] * 100) + "\n",
    1
)
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`1 x 1`含む`#`|`#`| 単一のセルはすでに有効なツリーです。 |
 |`1 x 1`含む`.`|`#`| 黒いグラフは空であってはなりません。 |
 |`1 x 3`含む`#.#`| コストのある任意の木 (1) | サイクルがない場合でも接続を強制する必要があります。 |
 |`100 x 10`のみを含む`.`| 任意の 1 つの黒セル | 最大次元と空からシングルトンへのケース。 |

 ## 特殊なケース

 1セル入力の場合```
.
```初期の DP 状態は空のフロンティアです。 白を選択すると空のままになりますが、黒のコンポーネントがないため、その状態は最終的に拒否されます。 黒を選択すると 1 つのコンポーネントが作成され、最終状態には 1 つのコンポーネントだけが含まれます。 コストは 1 なので、出力は次のようになります。`#`。 

すでに有効な 1 セル入力の場合```
#
```黒を選択するとコストはゼロになります。 フロンティアには 1 つのコンポーネントが含まれており、最終状態はすぐに受け入れられます。 白を選択すると、完成した空の状態が作成されますが、黒のセルが含まれていないため拒否されます。 その結果、アルゴリズムは次の結果を返します。`#`変更ゼロで。 

のために```
#.#
```最初の黒いセルはコンポーネント (1) を作成し、中央の白いセルはコンポーネント (1) をアクティブのままにし、最後の黒いセルはコンポーネント (2) を開始します。 したがって、最後のフロンティアには 2 つのコンポーネントが含まれるため、変更されていないボードは拒否されます。 代わりに、DP は中央のセルを黒にして、両側を 1 つのパスにマージするか、どちらかのエンドポイントを削除することができます。 どちらの選択肢もコストが 1 であるため、最適なのは 1 です。 

サイクルのために```
###
#.#
###
```最初の 2 行には、一時的にいくつかのフロンティア コンポーネントを含めることができます。 最後の行が形状を閉じると、既に同じコンポーネントに属している 2 つのフロンティア セルを接続する遷移はサイクルとして拒否されます。 1 つのセルを削除すると、接続された非巡回セットが残るため、DP はコスト 1 のソリューションを保持します。 元のボード自体は周期的であるため、変更がゼロであることは最適ではなく、変更が 1 であることが最小であることがわかります。 

全白 (100\×10) ボードの場合、DP は黒のセルを 1 つ選択するまですべてのセルを白に保つことができます。 これにより、すでにツリーになっているシングルトン コンポーネントが作成されます。 コストは正確に 1 であり、変更されていないボードには黒のセルがないため、コストがゼロになるソリューションは存在しません。 これは、空ではないツリー条件を実行しながら、可能な限り最大のボードを処理します。
