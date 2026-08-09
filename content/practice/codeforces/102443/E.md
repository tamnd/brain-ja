---
title: "CF 102443E - ロボットのかくれんぼ"
description: "(m 倍 n) 個のグリッドがあります。 ロボットはいくつかのセルを占有し、すべてのロボットは 4 つの基本的な方向のいずれかを指します。 下を見ているロボットには、三角形の領域が広がっているのが見えます。そのすぐ下に 1 つのセル、次に 2 行下に 3 つのセル、そして 3 行下に 5 つのセルというように続きます。"
date: "2026-08-08T12:59:33+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102443
codeforces_index: "E"
codeforces_contest_name: "2019-2020 Russia Team Open, High School Programming Contest (VKOSHP 19)"
rating: 0
weight: 102443
solve_time_s: 485
verified: true
draft: false
---

[CF 102443E - ロボットのかくれんぼ](https://codeforces.com/problemset/problem/102443/E)

 **評価:** -
 **タグ:** -
 **解決時間:** 8 分 5 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 (m\times n) 個のグリッドがあります。 ロボットはいくつかのセルを占有し、すべてのロボットは 4 つの基本的な方向のいずれかを指します。 下を見ているロボットには、三角形の領域が広がっているのが見えます。そのすぐ下に 1 つのセル、次に 2 行下に 3 つのセル、そして 3 行下に 5 つのセルというように続きます。 他の 3 つの方向は対称的に定義されます。 

ロボットのペアがお互いに見えないようにロボットを回転させなければなりません。 ロボットを (90^\circ) 回転させるには 1 回の操作が必要となるため、たとえば (U) を (D) に変更するには 2 回の操作が必要になります。 出力はロボットの位置をまったく同じに保ち、最小総回転数を達成する必要があります。 

有用な幾何学的観察は、2 つのロボットが反対方向を向いており、その方向の変位が垂直変位よりも厳密に大きい場合にのみ互いを見ることができるということです。 垂直ペアの場合、これは、上のロボットが下を向き、下のロボットが上を向いている場合に危険であることを意味します。 

[
 |\デルタ c|<|\デルタ r|。 
】

 水平ペアの場合、同様の条件は次のようになります。 

[
 |\デルタ r|<|\デルタ c|。 
】

 平等は安全です。 この厳密な不等式により、1 つずつ異なるエラーが発生しやすくなります。 

グリッドには最大で (4\cdot 10^6) 個のセルがあります。 (O(mn\min(m,n))) の解にはすでに数十億回の演算が必要となるため、列ごとに可能な等高線を個別に列挙し、すべての行を再度チェックするのは遅すぎます。 ターゲットは (O(mn))、または多くてもそのようなパスの少数の一定数です。 

重要なエッジケースがいくつかあります。 

考慮する```
1 3
R.L
```2 台のロボットは同じ列にあり、お互いの方向を向いているため、お互いが見えます。 どちらかを (90^\circ) 回すれば十分であり、正しい最小コストは (1) です。 対角条件を包括的として扱う不用意な実装や、隣接するセルのみをチェックする実装では、この競合を見落とす可能性があります。 

ここで考えてみましょう```
3 1
R
.
L
```2 台のロボットは同じ列にいますが、どちらも水平方向を向いているため、どちらも相手を見ることができません。 正しい最小コストは (0) です。 これにより、反対方向のすべてのペアが自動的に不良であると想定するソリューションが捕捉されます。 

厳密な対角境界については、次のことを考慮してください。```
2 2
D.
.U
```ロボットは斜めに隣接しています。 それらの行と列の差は両方とも (1) であるため、どちらのビュー コーンにも他のロボットが含まれません。 正しい出力は、コスト (0) の入力と同一である可能性があります。 厳密な不等式を非厳密な不等式に置き換えると、誤って回転が強制されます。 

最後に、次のような空のグリッド```
2 2
..
..
```すでに条件を満たしているため、変更せずに返す必要があります。 ロボットを発明したり、空のセルを改造したりする理由はありません。 

## アプローチ

 直接的なアプローチは、すべてのロボットを検討し、その可能な 4 つの方向を試し、結果の構成が有効かどうかを確認することです。 ロボットごとに 4 つの選択肢があるため、(k) 台のロボットでは (4^k) となり、数十台のロボットであっても役に立たなくなります。 もう少し素朴なアプローチは、構成の構築中にロボットのすべてのペアを検査することですが、ペアの数だけでも (O(k^2)) であり、指数関数的に多くの方向の割り当てが存在します。 

有用な構造は、最初に垂直方向のみに注目することによって生まれます。 上を向いたすべてのロボットの視円錐を描くことを想像してください。 それらの禁止領域は単調な輪郭を形成します。 すべての列に境界行 (d_i) があります。 輪郭の一方の側のロボットは上方向を使用し、もう一方の側のロボットは下方向を使用する必要があります。 輪郭に正確に沿ったロボットは柔軟性があります。 等高線は、隣接する列間で 1 行を超えてジャンプすることはできません。 

[
 |d_i-d_{i+1}|\le 1.
 】

 これはまさに、1 次元の動的プログラムが処理できる種類のローカル条件です。 この等高線の特徴付けは、既知の (O(mn)) 解の背後にある中心的な観察です。 

もう 1 つ詳細があります。 基本的な垂直方向を使用したくないロボットは、標準構造内のすべての水平ロボットが同じ水平方向を使用する限り、安全に水平にすることができます。 水平ロボットはすべて同じ方向を向いているため、お互いを見ることができません。 垂直方向を使用するロボットと水平方向を使用するロボットは、相互に視認することができません。相互に視認するには、両方のロボットが変位軸に沿って向く必要があるからです。 

したがって、垂直輪郭の周りに標準的な構成を構築できます。 輪郭の上では、すべてのロボットは (U) または 1 つの固定水平方向を選択します。 その下では、すべてのロボットが (D) または同じ固定水平方向のいずれかを選択します。 ロボットは輪郭上で (U)、(D)、または固定の水平方向を選択できます。 

水平な輪郭を持つ対称的な構造があります。 グリッドを転置し、(L/R) を主方向とみなし、1 つの固定垂直方向を代替として使用します。 各向きで固定の代替方向について両方の選択肢を試します。 これにより、DP 実行は 4 回のみになり、依然として (O(mn)) になります。 

輪郭の定式化は、単に有効な構成を構築する方法ではありません。 標準の非交差引数を使用すると、ローテーション コストを増加させることなく、最適で有効な構成をこれらの標準形式のいずれかに変換できます。 競合するペアが主に垂直である場合、それらの境界は垂直の輪郭で表すことができます。 対応する構造が水平の場合、引数を転置します。 輪郭が固定されると、すべてのロボットを個別に最適化できるため、残る問題はまさに以下で説明する DP になります。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | (O(4^k k^2)) | (O(k)) | 遅すぎる |
 | 任意の向きでのペアワイズ チェック | 指数 | (O(k)) | 遅すぎる |
 | 輪郭DP | (O(mn)) | (O(mn)) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. まず垂直輪郭を考慮します。 すべての列 (c) について、(0) と (m+1) の間の整数境界 (d_c) を選択します。 (d_c=0) の場合、柱全体が輪郭の下にあります。 (d_c=m+1) の場合、列全体がその上にあります。 それ以外の場合は、行 (d_c) が輪郭セルになります。 
2. 必須

 [
 |d_c-d_{c-1}|\le 1.
 】

 これはまさに、1 列を水平に移動するときに等高線は 1 行だけ上下に移動できるという幾何学的条件です。

1. 水平逃げ方向 (H) を (L) または (R) のいずれか 1 つ固定します。 輪郭より上のロボットの場合は、(U) と (H) のみが考慮されます。 輪郭より下のロボットの場合は、(D) と (H) のみが考慮されます。 等高線では（U、D、H）が使用可能です。 
2. それらの選択肢をローテーションコストに変換します。 元の方向が (x) の場合、(y) を選択するコストは 4 つの方向間の円周距離になります。 したがって、(U\to R) と (U\to L) は両方とも (1) のコストがかかりますが、(U\to D) のコストは (2) になります。 
3. 固定列と固定境界 (d) の場合、その総コストを計算します。 行 (r) が等高線の上にある場合の最も安いコストを (A_r) とし、下にある場合の最も安いコストを (B_r) とし、行自体が等高線である場合の最も安いコストを (C_r) とします。 それから

 \sum_{r<d} A_r
 +
 C_d
 +
 \sum_{r>d} B_r.
 】

 2 つの合計はプレフィックスとサフィックスの合計で取得されるため、1 つの列の (d) のすべての (m+2) 個の値が (O(m)) で計算されます。 

1. (d) を列 (c) の輪郭位置として、列 (0\ldots c) を処理した後の最小コストとして (dp_c[d]) を定義します。 可能な以前の輪郭位置は (d-1,d,d+1) だけです。 

\オペレーター名{コスト}_c(d)
 +
 \min(dp_{c-1}[d-1],dp_{c-1}[d],dp_{c-1}[d+1])。 
】

 1. 3 つの以前の状態のうちどれが使用されたかを保存します。 最後の列の後で、最も安価な境界位置を選択し、これらの親の選択肢を逆方向にたどり、輪郭全体を再構築します。 
2. 再構成された輪郭を使用して、すべてのロボットの実際の方向を選択します。 輪郭の上では、(U) と (H) の安い方を選択します。 その下で (D) と (H) の安い方を選択します。 輪郭上で、(U、D、H) の中で最も安いものを選択します。 
3. (H=L) と (H=R) についても同じ手順を繰り返します。 次に、グリッドを転置し、主方向が (L/R) に対応し、エスケープ方向が (U) または (D) に対応するように、対称の構築をさらに 2 回実行します。 
4. 結果として得られる 4 つの構成のうち、最も安価な構成を維持します。 生成されたすべての構成で、エスケープ方向を使用するすべてのロボットは同じ方向を指すため、お互いを見ることができません。 主（U/D）または（L/R）ロボットは輪郭で区切られており、主方向ロボットと退避方向ロボットは方向が直交しているため相互に視認できません。 

### なぜ効果があるのか

 DP の不変条件は、列 (c) を処理した後、`dp[d]`は、輪郭が行 (d) で終わるすべての標準構成の中での最小回転コストです。 この遷移では、1 セルの勾配制限と互換性のある 3 つの可能な輪郭位置が正確に考慮されるため、すべての正当な輪郭が表現されます。 

固定輪郭の場合、1 台のロボットに選択された方向は、他のロボットのコストには影響しません。 すべての水平脱出ロボットは同じ方向を向いていますが、主な垂直ロボットは上向き領域と下向き領域に分かれています。 等高線では、等高線の 1 セルの勾配制限により、2 つの反対の主方向が互いに見えるほど垂直方向に近づくことができません。 したがって、すべての DP 状態は有効な構成に対応します。 

対称的な議論は転置後に適用されます。 輪郭補題は、最適な有効な構成を、回転数を増やすことなくこれらの標準形式のいずれかに解くことができることを示しています。 両方の軸と両方の可能なエスケープ方向を列挙するため、4 つの DP 結果の最小値がグローバル最適値になります。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

INF = 10**9

# Directions are arranged clockwise.
ORDER = "URDL"
IDX = {ch: i for i, ch in enumerate(ORDER)}

def turn_cost(a, b):
    x = abs(IDX[a] - IDX[b])
    return min(x, 4 - x)

def solve_family(g, up, down, side):
    """
    Solve the contour problem on g.

    Above the contour:
        choose up or side.

    Below the contour:
        choose down or side.

    On the contour:
        choose up, down, or side.

    Returns:
        (minimum_cost, contour)
    """
    h = len(g)
    w = len(g[0])
    states = h + 2

    # parent[c * states + d]:
    # 0 -> previous d-1
    # 1 -> previous d
    # 2 -> previous d+1
    parent = bytearray(w * states)

    prev = [INF] * states

    for c in range(w):
        # Prefix costs for rows above the contour.
        pref = [0] * (h + 1)

        # Suffix costs for rows below the contour.
        suff = [0] * (h + 1)

        col = g

        for r in range(h):
            ch = col[r][c]

            a = min(turn_cost(ch, up), turn_cost(ch, side))
            pref[r + 1] = pref[r] + a

        for r in range(h - 1, -1, -1):
            ch = col[r][c]

            b = min(turn_cost(ch, down), turn_cost(ch, side))
            suff[r] = suff[r + 1] + b

        # Cost of every possible contour position.
        cost = [0] * states

        # d = 0, everything is below.
        cost[0] = suff[0]

        # d = h + 1, everything is above.
        cost[h + 1] = pref[h]

        for d in range(1, h + 1):
            ch = col[d - 1][c]

            boundary = min(
                turn_cost(ch, up),
                turn_cost(ch, down),
                turn_cost(ch, side),
            )

            cost[d] = pref[d - 1] + boundary + suff[d]

        if c == 0:
            prev = cost
            continue

        cur = [INF] * states
        base = c * states

        for d in range(states):
            best = prev[d]
            code = 1

            if d > 0 and prev[d - 1] < best:
                best = prev[d - 1]
                code = 0

            if d + 1 < states and prev[d + 1] < best:
                best = prev[d + 1]
                code = 2

            cur[d] = best + cost[d]
            parent[base + d] = code

        prev = cur

    best_d = min(range(states), key=prev.__getitem__)
    best_cost = prev[best_d]

    contour = [0] * w
    d = best_d

    for c in range(w - 1, -1, -1):
        contour[c] = d

        if c == 0:
            break

        code = parent[c * states + d]

        if code == 0:
            d -= 1
        elif code == 2:
            d += 1

    return best_cost, contour

def build_family(g, up, down, side, contour):
    h = len(g)
    w = len(g[0])

    ans = [list(row) for row in g]

    for c in range(w):
        d = contour[c]

        for r in range(h):
            ch = g[r][c]

            if ch == '.':
                continue

            if d == 0:
                choices = (down, side)
            elif d == h + 1:
                choices = (up, side)
            elif r < d - 1:
                choices = (up, side)
            elif r > d - 1:
                choices = (down, side)
            else:
                choices = (up, down, side)

            best = choices[0]
            best_cost = turn_cost(ch, best)

            for cand in choices[1:]:
                cur = turn_cost(ch, cand)
                if cur < best_cost:
                    best_cost = cur
                    best = cand

            ans[r][c] = best

    return [''.join(row) for row in ans]

def transpose_problem(g):
    """
    Transform the problem so that original horizontal directions
    become vertical directions.

    Original:
        L -> transformed U
        R -> transformed D
        U -> transformed L
        D -> transformed R
    """
    h = len(g)
    w = len(g[0])

    mp = {
        'L': 'U',
        'R': 'D',
        'U': 'L',
        'D': 'R',
        '.': '.',
    }

    t = []
    for c in range(w):
        row = []
        for r in range(h):
            row.append(mp[g[r][c]])
        t.append(''.join(row))

    return t

def untranspose_answer(t):
    """
    Inverse of transpose_problem.
    """
    h = len(t)
    w = len(t[0])

    mp = {
        'U': 'L',
        'D': 'R',
        'L': 'U',
        'R': 'D',
        '.': '.',
    }

    ans = [['.'] * h for _ in range(w)]

    for r in range(h):
        for c in range(w):
            ans[c][r] = mp[t[r][c]]

    return [''.join(row) for row in ans]

def solve_grid(g):
    best_cost = INF
    best_answer = None

    # Vertical contour.
    for side in ('L', 'R'):
        cost, contour = solve_family(g, 'U', 'D', side)

        if cost < best_cost:
            best_cost = cost
            best_answer = build_family(
                g, 'U', 'D', side, contour
            )

    # Horizontal contour, obtained by transposing.
    tg = transpose_problem(g)

    for side in ('L', 'R'):
        cost, contour = solve_family(tg, 'U', 'D', side)

        if cost < best_cost:
            transformed = build_family(
                tg, 'U', 'D', side, contour
            )
            best_cost = cost
            best_answer = untranspose_answer(transformed)

    return best_answer

def main():
    m, n = map(int, input().split())
    g = [input().strip() for _ in range(m)]

    ans = solve_grid(g)

    sys.stdout.write('\n'.join(ans))

if __name__ == "__main__":
    main()
```方向性の順序`URDL`回転距離を円周距離とします。 例えば、`U`に`D`は 2 ターンですが、`U`どちらかに`L`または`R`は1ターンです。 

の`solve_family`関数はコア DP です。 状態は、(m+2) 個の可能な輪郭位置です。 2 つの追加の状態は、グリッドの完全に上または完全に下の等高線を表すため、実際のグリッドに人為的な行が含まれる特別なケースはありません。 

各列について、`pref`輪郭の上に行を配置するための累積コストを`up-or-side`カテゴリ。`suff`輪郭の下の行に対しても同じことを行います。 その結果、2 回の線形スキャンの後、すべての可能な輪郭位置が一定時間内に評価されます。 

移行では、正確に 3 つの先行者を検査します。 バイト配列`parent`各状態は、前の輪郭が 1 行上か、等しいか、または 1 行下かを記憶するだけでよいため、これで十分です。 を使用して`bytearray`Python の整数リストの代わりに、(O(mn)) 個の再構築メモリを小さく保ちます。 

再構築では、DP とまったく同じ選択肢が使用されます。 輪郭の上のロボットは、主方向と固定脱出方向のどちらかを選択します。 以下のロボットは、反対の主方向で同様のことを行います。 輪郭上のロボットには、他の主な方向の追加の選択があります。 

2 番目の実行ペアは、グリッドを転置した後の同じアルゴリズムです。 オリジナルなのでディレクションマッピングが必要です。`L`変身する`U`、オリジナル`R`変身する`D`、オリジナル`U`変身する`L`、そしてオリジナル`D`変身する`R`。 

Python には整数オーバーフローの問題は存在しません。 最大有効コストは最大でもロボットの数の 2 倍であるため、大規模な有限`INF`で十分です。 

## 実用的な例

 ### サンプル 1

 入力は```
2 3
RDL
.U.
```1 つの最適な輪郭を垂直方向に表示できます。 使用を検討してください`L`固定の水平エスケープ方向として。 等高線は、すべての列の行 (1) に留まることができます。 

| コラム | 境界 | 上記のコスト | 境界コスト | コスト以下 | 合計 |
 | --- | --- | --- | --- | --- | --- |
 | 1 | 1 | 0 |`R -> U`= 1 | 0 | 1 |
 | 2 | 1 | 0 |`D -> D`= 0 |`U -> L`= 1 | 1 |
 | 3 | 1 | 0 |`L -> L`= 0 | 0 | 0 |

 合計は(2)となります。 考えられる最適値の 1 つは、サンプル出力です。```
UDL
.R.
```最初のロボットは、`R`に`U`、2列目のロボットはから向きを変える`U`に`R`サンプル出力で。 どちらの変更もコストは 1 です。 

重要な点は、最初の部分を残すことです。`R`そして3番目`L`変更しないと、これら 2 台のロボットがお互いを水平方向に認識することになります。 輪郭 DP は、必要な分離を正確に実現します。 

### サンプル 2

 入力は```
2 2
..
..
```ロボットが存在しないため、すべての輪郭にはコストがかかりません。 

| コラム | 境界 | DPコスト |
 | --- | --- | --- |
 | 1 | 0 | 0 |
 | 2 | 0 | 0 |

 再構築されたグリッドは空のままです。```
..
..
```これは、人工輪郭状態がロボットを作成したり、空のセルを変更したりしないことを確認します。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O(mn)) | 4 つの等高線 DP 実行は一定の要素にすぎず、各実行はすべてのセルを一定の回数スキャンします。 
| スペース | (O(mn)) | 親配列には、すべての輪郭状態に対する 3 方向の先行選択が保存されます。 

グリッドには最大で (4\cdot10^6) 個のセルが含まれます。 このアルゴリズムは、ロボットのペアや方向の割り当てを列挙するのではなく、これらのセル上で一定数の線形パスのみを実行します。 (O(mn)) メモリは 512 MB の制限内ですが、Python はコンパクトです。`bytearray`親の表現はここで特に役立ちます。 

## テストケース

 出力は一意ではないため、テストでは、返された構成を文字ごとに比較するのではなく、検証する必要があります。 小規模なケースでは、すべてのペアをブルート フォースして妥当性をチェックし、正確なローテーション コストを計算できます。```python
# helper: run solution on input string, return output string
import sys
import io
from itertools import product

# Assume the editorial solution above has been placed in a module
# named solution, or copy solve_grid into this test file.

def run(inp: str) -> str:
    old_stdin = sys.stdin
    sys.stdin = io.StringIO(inp)

    m, n = map(int, sys.stdin.readline().split())
    g = [sys.stdin.readline().strip() for _ in range(m)]

    ans = solve_grid(g)

    sys.stdin = old_stdin
    return '\n'.join(ans)

ORDER = "URDL"
IDX = {c: i for i, c in enumerate(ORDER)}

def dist(a, b):
    x = abs(IDX[a] - IDX[b])
    return min(x, 4 - x)

def sees(r1, c1, d, r2, c2):
    dr = r2 - r1
    dc = c2 - c1

    if d == 'U':
        return dr < 0 and abs(dc) < -dr
    if d == 'D':
        return dr > 0 and abs(dc) < dr
    if d == 'L':
        return dc < 0 and abs(dr) < -dc
    return dc > 0 and abs(dr) < dc

def validate(inp, out):
    data = inp.strip().splitlines()
    m, n = map(int, data[0].split())
    original = data[1:]

    answer = out.splitlines()

    assert len(answer) == m
    assert all(len(row) == n for row in answer)

    robots = []

    for r in range(m):
        for c in range(n):
            assert (original[r][c] == '.') == (answer[r][c] == '.')

            if answer[r][c] != '.':
                robots.append((r, c, answer[r][c]))

    for i in range(len(robots)):
        r1, c1, d1 = robots[i]

        for j in range(i + 1, len(robots)):
            r2, c2, d2 = robots[j]

            assert not (
                sees(r1, c1, d1, r2, c2)
                and sees(r2, c2, d2, r1, c1)
            )

    cost = 0

    for r in range(m):
        for c in range(n):
            if original[r][c] != '.':
                cost += dist(original[r][c], answer[r][c])

    return cost

# Provided sample 1.
sample1 = """\
2 3
RDL
.U.
"""

out = run(sample1)
assert validate(sample1, out) == 2, "sample 1"

# Provided sample 2.
sample2 = """\
2 2
..
..
"""

out = run(sample2)
assert validate(sample2, out) == 0, "sample 2"

# Minimum-size input.
case3 = """\
1 1
U
"""

out = run(case3)
assert validate(case3, out) == 0, "single robot needs no rotation"

# All robots already point in the same direction.
case4 = """\
3 4
RRRR
RRRR
RRRR
"""

out = run(case4)
assert validate(case4, out) == 0, "all equal directions"

# Opposite horizontal directions in one row.
case5 = """\
1 3
R.L
"""

out = run(case5)
assert validate(case5, out) == 1, "horizontal mutual visibility"

# Opposite horizontal directions in one column.
case6 = """\
3 1
R
.
L
"""

out = run(case6)
assert validate(case6, out) == 0, "same column is safe"

# Equal row/column displacement is not visible.
case7 = """\
2 2
D.
.U
"""

out = run(case7)
assert validate(case7, out) == 0, "diagonal equality is safe"

# Maximum-size input shape, chosen so the expected cost is obvious.
m = 2000
n = 2000
large = str(m) + " " + str(n) + "\n" + "\n".join(["U" * n] * m) + "\n"

out = run(large)
assert all(row == "U" * n for row in out.splitlines()), \
    "maximum-size all-U case"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`2 3 / RDL / .U.`| コスト 2 の有効な構成 | 提供されたサンプルと輪郭の再構成 |
 |`2 2 / .. / ..`| 空のグリッド | 空の入力 |
 |`1 1 / U`|`U`| 最小サイズの入力 |
 |`3 4`あらゆる細胞とともに`R`| 同じグリッド | すべて等しい方向 |
 |`1 3 / R.L`| コスト 1 | の有効な構成。 水平方向の相互可視性 |
 |`3 1 / R / . / L`| 同じグリッド | 1 つの列で反対の水平方向 |
 |`2 2 / D. / .U`| 同じグリッド | 厳密な対角境界 |
 |`2000 x 2000`全て`U`| 同じグリッド | 最大サイズの入力とパフォーマンス |

 ## 特殊なケース

 最初のエッジケースは空のグリッドです。 のために```
2 2
..
..
```すべての列は輪郭状態 (0)、(1)、(2)、または (3) を使用でき、すべての列のコストはゼロです。 その結果、DP はゼロを返し、再構築ではすべてのセルが次のようになります。`.`。 

2 番目のエッジ ケースは単一のロボットです。 のために```
1 1
L
```そのセルを通じて輪郭を直接選択できるため、ロボットは輪郭を保持できます。`L`コストゼロで。 DP の境界遷移には、元の方向から許容方向の最小値までが含まれるため、不必要な回転が強制されることはありません。 

3 番目のエッジ ケースは、厳密な対角境界です。```
2 2
D.
.U
```2 台のロボットは位置がずれています ((1,1))。 下向きの円錐には最初の行に同じ列のセルのみが含まれるため、対角のセルは表示されません。 上向きの円錐についても同様です。 輪郭引数は元のジオメトリと一致する厳密な円錐を使用するため、アルゴリズムは等価制約を導入しません。 

4 番目のエッジ ケースは、1 つの列で水平方向が反対です。```
3 1
R
.
L
```これらのロボットは水平方向の変位がゼロであるため、お互いを見ることができません。 垂直等高線表現は、両方向を保持するのにあまり便利ではないかもしれませんが、グリッドを転置した後、それらは同じ水平等高線境界上に位置します。 対称 DP は両方を処理します`L`そして`R`回転を強制することなく、コストはゼロになります。 

最後の微妙なケースは、多くのロボットが輪郭上に横たわっている場合です。 連続する輪郭行の違いは最大 1 つであるため、2 つの境界ロボットの垂直変位は水平変位以下になります。 これは、垂直方向の相互ビューに必要な厳格な条件とはまったく逆です。 これが、輪郭上のロボットが隠れた垂直方向の衝突を引き起こすことなく 2 つの主要な方向を使用できる理由です。 

実際の実装メモ: 4 つの定数因子 DP の実行は、Python で最適化する価値のある部分です。 の`bytearray`親ストレージと DP 状態のネストされた Python オブジェクトの回避は、2000×2000 の制限を意図的に選択したものです。
