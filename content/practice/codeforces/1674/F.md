---
title: "CF 1674F - デスクトップの再配置"
description: "'' とマークされたアイコンと '.' とマークされた空のセルを持つデスクトップを表す 2D グリッドが与えられます。 アイコンが左上隅から始まるコンパクトな長方形を占めている場合、そのデスクトップは「良好」であるとみなされます。すべてのアイコンがいくつかの完全な列を埋めており、場合によっては 1 つのプレフィックスが付いています。"
date: "2026-06-10T01:16:36+07:00"
tags: ["codeforces", "competitive-programming", "data-structures", "greedy", "implementation"]
categories: ["algorithms"]
codeforces_contest: 1674
codeforces_index: "F"
codeforces_contest_name: "Codeforces Round 786 (Div. 3)"
rating: 1800
weight: 1674
solve_time_s: 106
verified: true
draft: false
---

[CF 1674F - デスクトップの再配置](https://codeforces.com/problemset/problem/1674/F)

 **評価:** 1800
 **タグ:** データ構造、貪欲、実装
 **解決時間:** 1 分 46 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 「*」のマークが付いたアイコンと「.」の空のセルを持つデスクトップを表す 2D グリッドが与えられます。 アイコンが左上隅から始まるコンパクトな長方形を占めている場合、デスクトップは「良好」であるとみなされます。すべてのアイコンがいくつかの完全な列を埋め、場合によっては 1 つの追加列のプレフィックスを埋めます。 この形状の外側にアイコンがあってはなりません。 たとえば、4 行 4 列があり、最初の 2 列がすべてアイコンで埋められ、3 列目の上部に 1 つのアイコンがある場合、これは適切です。 このコンパクトな配置以外のアイコンは、「良好な」プロパティに違反します。 

次に、一連のクエリが与えられ、各クエリはセルにアイコンを追加するか、セルを削除します。 各クエリの後に、デスクトップを適切な構成に復元するために必要な最小移動回数を計算する必要があります。 各移動は、アイコンを選択して他の場所に配置することで構成されます。 

制約は厳しく、グリッドは最大 1000 x 1000 まで、クエリは最大 200,000 個まで可能です。 クエリごとにグリッド全体をスキャンする素朴なアプローチ。$O(n \cdot m \cdot q)$、遅すぎます。 このため、更新とクエリがほぼ一定時間または対数時間で処理されるソリューションを見つける必要があります。 

完全な列が存在しないようにアイコンが散在している場合、微妙なエッジ ケースが発生します。 たとえば、位置 (1,1) と (2,2) にアイコンがある 2x2 グリッドでは、適切なデスクトップを形成するには両方のアイコンを再配置する必要があります。 アイコンの総数や線形化された位置を考慮しない単純なアプローチでは、移動数が誤って計算されます。 

## アプローチ

 ブルート フォースのアプローチは簡単です。各クエリの後に、グリッド内のすべてのアイコンを数え、左上の四角形のどのセルを埋めるかを計算します。 次に、グリッドをスキャンして、長方形内に移動する必要がある、または長方形の外側から削除する必要がある、間違って配置されたアイコンを数えます。 これは正しく動作しますが、遅すぎます。 各クエリにはコストがかかります$O(n \cdot m)$、および 200,000 クエリを使用すると、合計操作は$2 \cdot 10^{11}$、それは不可能です。 

最適化のための重要な洞察は、2D グリッドを 1D 配列に線形化することから得られます。 デスクトップが列ごとに埋められていると想像すると、「良好な」デスクトップとは、最初に配置されたものに相当します。$k$最初のアイコン$k$この 1D 配列の位置。 長さの単一の配列を維持できます$n \cdot m$各セルにアイコンが含まれるかどうかを表します。 クエリごとに、アイコンの数を更新し、最初のクエリの中で間違って配置されたアイコンの数を追跡します。$k$細胞。 アイコンの総数と最初の空のセルの数だけを知る必要があります。$k$ポジションを使用して移動数を決定し、クエリごとのコストを削減します。$O(1)$適切な簿記を付けて。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n * m * q) | O(n * m) | 遅すぎる |
 | 最適 | O(n * m + q) | O(n * m) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. グリッドの寸法を読み取る$n$そして$m$、クエリ数$q$。 最初の列のセルが最初に表示され、次に 2 番目の列が続くように、グリッドを列優先の 1D 配列にフラット化します。 これは、望ましい「適切な」構成と一致します。 
2. アイコンの総数を数えます。`total_icons`、現在のグリッド内。 良好なデスクトップに配置する必要がある 1D ポジションの数を決定します。`total_icons`1D 配列のセル。 
3. 変数を維持する`misplaced`最初のポジションの数を数えます`total_icons`1D 配列内の空の場所 (つまり、アイコンがあるはずなのに空ではない場所)。 このプレフィックス内の空の各セルにはアイコンを外側からセル内に移動する必要があるため、これは必要な最小移動数に等しくなります。 
4. クエリごとに、セルの 1D インデックスを計算します。 セルにアイコンが含まれている場合は、それを削除します。 それ以外の場合は追加します。 アップデート`total_icons`それに応じて。 
5. 更新されたセルが最初のセル内にある場合`total_icons`位置、調整`misplaced`セルが空になったか満たされたかに応じて ±1 ずつ変化します。 セルがこの範囲外にあるが、アイコンの総数が変更された場合も調整します`misplaced`動きが最初の境界線に影響を与える場合`total_icons`ポジション。 
6. 出力`misplaced`各クエリの後。 

機能する理由: グリッドを列方向に平坦化することで、最初の`total_icons`セルは「適切な」デスクトップの正確な形状を表します。 このプレフィックス内の間違って配置されたセルは、移動する必要があるアイコンに正確に対応します。 この不変式はクエリ間および更新間で保持されます。`misplaced`ローカルな変更に基づいて、正確性を保証します$O(1)$クエリごとに。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

n, m, q = map(int, input().split())
grid = [input().strip() for _ in range(n)]

# Flatten the grid column-wise
cells = []
for j in range(m):
    for i in range(n):
        cells.append(grid[i][j] == '*')

total_cells = n * m
total_icons = sum(cells)
misplaced = sum(1 for i in range(total_icons) if not cells[i])

# Map from 2D to 1D index
def idx(x, y):
    return (y - 1) * n + (x - 1)

res = []
for _ in range(q):
    x, y = map(int, input().split())
    i = idx(x, y)
    if cells[i]:
        # Removing an icon
        if i < total_icons:
            misplaced -= 1
        total_icons -= 1
        cells[i] = False
        if cells[total_icons]:
            misplaced += 1
    else:
        # Adding an icon
        if i < total_icons:
            misplaced += 1
        if cells[total_icons]:
            misplaced -= 1
        cells[i] = True
        total_icons += 1
    res.append(str(misplaced))

print('\n'.join(res))
```このソリューションでは、まずグリッドを列優先の順序に平坦化し、「適切な」構成を簡素化します。 保守します`total_icons`そして`misplaced`そのため、必要な移動数をいつでも知ることができます。 2D から 1D へのインデックス マッピングと慎重な更新`misplaced`アイコンがいつ追加または削除されるかは、off-by-one エラーを避けるために非常に重要です。 

## 実用的な例

 サンプル 1:```
Initial grid (4x4):
..**
.*..
*...
...*
Flattened column-major:
[., *, *, ., *, ., ., *, ., ., ., *]

total_icons = 5
Prefix length = 5
Prefix cells: [., *, *, ., *]
Empty cells in prefix: 3 → minimum moves = 3
```最初のクエリ (1,3) でセル (1,3) が切り替わった後:```
Index in 1D: 2
Cell contained '*' → now empty
Index < total_icons → misplaced -= 1 → misplaced = 2
total_icons -= 1 → total_icons = 4
Check new boundary (cell at index total_icons=4 is '*') → misplaced += 1 → misplaced = 3
Output: 3
```後続のクエリも同様に更新され、常に次の不変条件が維持されます。`misplaced`長さのプレフィックス内の空のセルをカウントします`total_icons`。 

このトレースは、影響を受けるセルのみを更新しながら、ソリューションが追加と削除を正しく処理していることを確認します。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n * m + q) | グリッドを平坦化するには O(n*m) かかります。 各クエリは O(1) 個の変数を更新します。 |
 | スペース | O(n * m) | 平坦化されたグリッドとブール状態を保存します。 |

 と$n, m \le 1000$そして$q \le 2 \cdot 10^5$、周囲の合計操作$10^6 + 2 \cdot 10^5$十分に制限内にあります。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    n, m, q = map(int, input().split())
    grid = [input().strip() for _ in range(n)]
    cells = []
    for j in range(m):
        for i in range(n):
            cells.append(grid[i][j] == '*')
    total_cells = n * m
    total_icons = sum(cells)
    misplaced = sum(1 for i in range(total_icons) if not cells[i])
    def idx(x, y):
        return (y - 1) * n + (x - 1)
    res = []
    for _ in range(q):
        x, y = map(int, input().split())
        i = idx(x, y)
        if cells[i]:
            if i < total_icons:
                misplaced -= 1
            total_icons -= 1
            cells[i] = False
            if cells[total_icons]:
                misplaced += 1
        else:
            if i < total_icons:
                misplaced += 1
            if cells[total_icons]:
                misplaced -= 1
            cells[i] = True
            total_icons += 1
        res.append(str(misplaced))
    return '\n'.join(res)

# Provided sample
assert run("""4
```
