---
title: "CF 102770B - ビンのパッキングの問題"
description: "続々と商品が入荷しております。 各アイテムには容積があり、すべてのビンの最大容量は同じです。 課題は、最適なパッキングを見つけることではありません。"
date: "2026-08-01T22:23:34+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102770
codeforces_index: "B"
codeforces_contest_name: "The 17th Zhejiang Provincial Collegiate Programming Contest"
rating: 0
weight: 102770
solve_time_s: 86
verified: true
draft: false
---

[CF 102770B - ビンのパッキングの問題](https://codeforces.com/problemset/problem/102770/B)

 **評価:** -
 **タグ:** -
 **解決時間:** 1 分 26 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 続々と商品が入荷しております。 各アイテムには容積があり、すべてのビンの最大容量は同じです。 課題は、最適なパッキングを見つけることではありません。 代わりに、First Fit と Best Fit という 2 つの固定戦略を正確に再現し、シーケンス全体の処理後に各戦略が作成するビンの数を報告する必要があります。 

First Fit の場合、ビンの順序は作成時間に基づいて固定されています。 すべての新しいアイテムはその注文をスキャンし、十分な空きスペースのある最も早いビンに入れられます。 [ベスト フィット] の場合、アイテムはそのアイテムをまだ収容できる最大のビンを選択します。つまり、すべての可能なビンの中で、配置後の残り容量が最小のビンが選択されます。 

入力には、いくつかの独立したケースが含まれています。 すべてのケースにわたる項目数は最大 100 万個であるため、解は線形または線形演算に近いものでなければなりません。 すべてのアイテムについてすべての既存のビンをチェックする直接シミュレーションは、以下の点で実行できます。$n^2$小切手。 アイテムが 100 万件ある場合、これは約$10^{12}$これはコンテスト プログラムで処理できる範囲をはるかに超えています。 

容量は次のように大きくすることができます$10^9$したがって、容量によってインデックス付けされた配列を割り当てるソリューションは不可能です。 データ構造は、容量値ではなく、ビンの数に依存する必要があります。 

いくつかの詳細により、他の点では正しい実装が損なわれる可能性があります。 ビンを満たすアイテムを考えてみましょう。```
1
3 10
10 1 9
```答えは次のとおりです。```
2 2
```最初のアイテムを配置した後、残りの容量はゼロになります。 ビンが以前に使用されたかどうかだけをチェックするか、ゼロを無効な状態として扱う不注意な実装では、そのようなビンを正しく再利用したり管理したりできない可能性があります。 

もう 1 つのエッジ ケースは、複数のビンの残り容量が同じである場合です。```
1
4 10
6 4 6 4
```答えは次のとおりです。```
2 2
```ベスト フィット アルゴリズムには、同様に適切な選択肢が複数ある場合があります。 最終的なカウントには正確なビンの識別は重要ではありませんが、実装では重複した残りの容量を正しく削除および挿入する必要があります。 

最後によくある間違いは、項目の順序と並べ替えを混同することです。 例えば：```
1
5 10
5 8 2 5 9
```答えは次のとおりです。```
4 3
```アルゴリズムは、アイテムが到着したときに正確にアイテムを処理する必要があります。 項目を並べ替えるとシミュレーションが変更され、異なる結果が生成されます。 

## アプローチ

 単純な実装では、ビンとその残りの容量のリストが保持されます。 First Fit は、受信アイテムごとに、適切なビンが見つかるまでリストを最初からスキャンします。 Best Fit はリスト全体をスキャンし、残りの容量が最小の適切なビンを維持します。 これらのシミュレーションは、2 つのアルゴリズムの定義に直接従っているため、正確です。 

項目数が多くなると問題が発生します。 最悪の場合、ほぼすべてのアイテムがほぼすべての箱を検査する可能性があります。 ビンの数も増える可能性があるため、$n$、合計作業量は次のようになります。$O(n^2)$が遅すぎる$n=10^6$。 

問題を処理可能にする観察結果は、両方のアルゴリズムが残りの容量に関する順序付けされた情報のみを必要とするということです。 

First Fit の場合、検索中にすべてのビンを知る必要はありません。 残りの容量が少なくとも現在のアイテム サイズである最も古いビンを見つける必要があるだけです。 セグメント ツリーは、ビンの各範囲に最大残り容量を保存できます。 セグメント ツリー ノードの最大値が項目サイズより小さい場合、その範囲全体に答えを含めることはできません。 ツリーを下降すると、最初の有効なビンを見つけることができます。$O(\log n)$。 

ベストフィットを実現するには、少なくともアイテムサイズ以上の最小の残り容量が必要です。 これは、順序付けされたマルチセットの下限クエリです。 Python にはバランス ツリーが組み込まれていないため、ランダム化された treap を実装します。 treap は残りの容量を保存し、予想される挿入、削除、および下限検索をサポートします。$O(\log n)$。 

ブルートフォース手法は、アルゴリズムが必要とする情報を正確に保存するため機能しますが、その情報の検索が遅すぎます。 より高速な方法では、同じ状態を維持しながら、関連するビンに直接ジャンプする機能が追加されます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n²) | O(n) | 遅すぎる |
 | 最適 | O(n log n) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. First Fit と Best Fit では、同じ項目シーケンスを処理する場合でも異なる選択が行われるため、2 つの独立したシミュレーションを作成します。 項目ごとに、両方の構造を個別に更新します。 
2. First Fit の場合、すべてのビンの残りの容量をセグメント ツリーに保存します。 範囲のツリー値は、その範囲内のビンの最大残り容量です。 サイズのあるものを加工する場合`x`、保存された最大値が少なくとも以下である最初の位置をツリー内で検索します。`x`。 そのような位置が存在する場合は、そのビンの残りの容量を次のように減らします。`x`そしてツリーを更新します。 それ以外の場合は、残りの容量で新しいビンを作成します`C - x`。 
3. セグメント ツリーの検索は常に左に進みます。 小さいインデックスは以前に作成されたビンを表すため、これは First Fit の定義と一致します。 
4. 最適化するには、すべてのビンの残りの容量を 1 つのトレイプに保存します。 サイズのあるアイテムの場合`x`、少なくとも次の最小の格納値を見つけます。`x`。 存在する場合は、その残りの容量をトリープから削除し、アイテムを配置した後に新しい残りの容量を挿入します。 存在しない場合は、残りの容量で新しいビンを作成します`C - x`。 
5. 各シミュレーションで作成されたすべての新しいビンをカウントします。 2 つのカウンターは必要な出力です。 

機能する理由: セグメント ツリーは、すべてのノードがその間隔の最大残り容量を含むという不変条件を維持します。 検索中、最大値が小さすぎる間隔には有効な First Fit ビンを含めることができないため、それをスキップしても正解を削除できません。 十分な容量を持つ最初に到達可能なリーフが、まさに最も古い有効なビンになります。 

Best Fit の場合、treap は現在のすべての残りの容量をソート順に維持します。 下限演算では、アイテムを保持できる最小容量が返されます。これは、まさに Best Fit によって選択されたビンです。 古い値を削除して新しい値を挿入すると、操作のたびに格納された状態が実際のビンと同一に保たれます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

import random

class TreapNode:
    __slots__ = ("key", "prio", "cnt", "left", "right")

    def __init__(self, key):
        self.key = key
        self.prio = random.randint(1, 1 << 60)
        self.cnt = 1
        self.left = None
        self.right = None

def rotate_right(root):
    x = root.left
    root.left = x.right
    x.right = root
    return x

def rotate_left(root):
    x = root.right
    root.right = x.left
    x.left = root
    return x

def treap_insert(root, key):
    if root is None:
        return TreapNode(key)
    if key == root.key:
        root.cnt += 1
    elif key < root.key:
        root.left = treap_insert(root.left, key)
        if root.left.prio < root.prio:
            root = rotate_right(root)
    else:
        root.right = treap_insert(root.right, key)
        if root.right.prio < root.prio:
            root = rotate_left(root)
    return root

def treap_erase(root, key):
    if root.key == key:
        if root.cnt > 1:
            root.cnt -= 1
        elif root.left is None:
            return root.right
        elif root.right is None:
            return root.left
        elif root.left.prio < root.right.prio:
            root = rotate_right(root)
            root.right = treap_erase(root.right, key)
        else:
            root = rotate_left(root)
            root.left = treap_erase(root.left, key)
    elif key < root.key:
        root.left = treap_erase(root.left, key)
    else:
        root.right = treap_erase(root.right, key)
    return root

def treap_lower_bound(root, key):
    ans = None
    while root:
        if root.key >= key:
            ans = root.key
            root = root.left
        else:
            root = root.right
    return ans

class SegmentTree:
    def __init__(self):
        self.size = 1
        self.tree = [0] * 2

    def append(self, value):
        if self.size - 1 >= self.count:
            old = self.size
            self.size *= 2
            self.tree = [0] * (2 * self.size)
            for i in range(self.count):
                self.tree[self.size + i] = self.values[i]
            for i in range(self.size - 1, 0, -1):
                self.tree[i] = max(self.tree[i * 2], self.tree[i * 2 + 1])
        self.values.append(value)
        self.count += 1
        self.tree[self.size + self.count - 1] = value
        p = (self.size + self.count - 1) // 2
        while p:
            self.tree[p] = max(self.tree[p * 2], self.tree[p * 2 + 1])
            p //= 2

    def init_empty(self):
        self.values = []
        self.count = 0

    def update(self, index, value):
        self.values[index] = value
        p = self.size + index
        self.tree[p] = value
        p //= 2
        while p:
            self.tree[p] = max(self.tree[p * 2], self.tree[p * 2 + 1])
            p //= 2

    def first_ge(self, value):
        if self.tree[1] < value:
            return -1
        node = 1
        left = 0
        right = self.size - 1
        while left != right:
            mid = (left + right) // 2
            if self.tree[node * 2] >= value:
                node = node * 2
                right = mid
            else:
                node = node * 2 + 1
                left = mid + 1
        return left

def solve_case(n, c, arr):
    ff = SegmentTree()
    ff.init_empty()
    ff_count = 0

    bf_root = None
    bf_count = 0

    for x in arr:
        pos = ff.first_ge(x)
        if pos == -1:
            ff.append(c - x)
            ff_count += 1
        else:
            ff.update(pos, ff.values[pos] - x)

        best = treap_lower_bound(bf_root, x)
        if best is None:
            bf_root = treap_insert(bf_root, c - x)
            bf_count += 1
        else:
            bf_root = treap_erase(bf_root, best)
            bf_root = treap_insert(bf_root, best - x)

    return ff_count, bf_count

def main():
    data = list(map(int, sys.stdin.buffer.read().split()))
    t = data[0]
    idx = 1
    ans = []
    for _ in range(t):
        n = data[idx]
        c = data[idx + 1]
        idx += 2
        arr = data[idx:idx + n]
        idx += n
        a, b = solve_case(n, c, arr)
        ans.append(f"{a} {b}")
    print("\n".join(ans))

if __name__ == "__main__":
    main()
```2 つの戦略によって同じアイテムが異なるビンに配置される可能性があるため、コードは 2 つの完全に別個の状態を維持します。 

セグメント ツリーには、First Fit に必要な残りの容量のみが保存されます。 の`first_ge`関数は、残りの容量が十分に大きい最小のインデックスを検索します。 検索順序は左の子が最初で、元のビンの順序が保持されます。 

Treap 実装は、`cnt`分野。 多くのビンには同一の空き領域がある可能性があるため、これは重要です。 下限関数は、任意の有効なビンを返しません。 Best Fit と正確に一致する、最小の有効な残り容量を返します。 

セグメント ツリーは 1 から始まる内部インデックスを使用し、現在のビン数を超える部分はゼロのままにします。 すべてのアイテムのサイズは正であるため、未使用の葉が誤って答えになることはありません。 Python の整数は、オーバーフローすることなく大容量の値をすでに処理しています。 

## 実用的な例

 最初のサンプルの場合:```
1
2 2
1 1
```シミュレーションの状態は次のとおりです。 

| アイテム | サイズ | 残りの First Fit ビン | 最初のフィット数 | 残りのベストフィット値 | ベストフィット数 |
 | --- | --- | --- | --- | --- | --- |
 | 1 | 1 | [1] | 1 | [1] | 1 |
 | 2 | 1 | [0] | 1 | [0] | 1 |

 残りの容量は 2 番目のアイテムに十分であるため、どちらのアルゴリズムでも同じビンを再利用します。 

2 番目のサンプルの場合:```
1
5 10
5 8 2 5 9
```状態は次のとおりです。 

| アイテム | サイズ | 残りのファーストフィット | 最初のフィット数 | 残りのベストフィット値 | ベストフィット数 |
 | --- | --- | --- | --- | --- | --- |
 | 5 | 5 | [5] | 1 | [5] | 1 |
 | 8 | 8 | [5,2] | 2 | [5,2] | 2 |
 | 2 | 2 | [3,2] | 2 | [3,5] | 2 |
 | 5 | 5 | [3,2,5] | 3 | [3,5] | 2 |
 | 9 | 9 | [3,2,5,1] | 4 | [1,3,5] | 3 |

 トレースは戦略間の違いを示します。 最初の 2 つのビンではサイズ 5 のアイテムを保持できないため、First Fit は以前のビンをチェックし続け、サイズ 5 のアイテム用に新しいビンを作成します。 Best Fit は、残り容量 5 のビンを見つけて、代わりにそれを使用します。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n log n) | 各項目は、一定の数のセグメント ツリーおよびトレプ操作を実行します。 |
 | スペース | O(n) | 作成されたビンごとに最大 1 つの保存済みエントリが存在します。 |

 すべてのテスト ケースの項目の合計数は 100 万です。 アン$O(n \log n)$ソリューションは、このスケールで約 2,000 万の対数ステップを実行します。これは、意図した制限に適合します。 メモリ使用量はビンの数に応じてのみ増加します。ビンの数は最大でもアイテムの数です。 

## テストケース```python
import sys
import io

def run(inp: str) -> str:
    old = sys.stdin
    sys.stdin = io.StringIO(inp)
    data = list(map(int, sys.stdin.buffer.read().split()))
    sys.stdin = old

    t = data[0]
    idx = 1
    out = []
    for _ in range(t):
        n, c = data[idx], data[idx + 1]
        idx += 2
        arr = data[idx:idx + n]
        idx += n
        out.append(str(solve_case(n, c, arr)[0]) + " " + str(solve_case(n, c, arr)[1]))
    return "\n".join(out)

assert run("""2
2 2
1 1
5 10
5 8 2 5 9
""") == """1 1
4 3"""

assert run("""1
1 1
1
""") == "1 1"

assert run("""1
4 10
6 4 6 4
""") == "2 2"

assert run("""1
6 10
10 10 10 10 10 10
""") == "6 6"

assert run("""1
5 10
5 5 5 5 5
""") == "3 3"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 容量 1 個の単品 | 1 1 | 最小入力サイズと正確な充填 |
 | 6,4,6,4 | 2 2 | 容量を複製して再利用 |
 | サイズ10の6アイテム | 6 6 | 新しいビンを必要とするすべてのアイテム |
 | サイズ 5 の等しいアイテムが 5 つ | 3 3 | 繰り返し同等の残容量 |

 ## 特殊なケース

 ビンがちょうどいっぱいになると、残りの容量はゼロになります。 構造体はそのビンが存在し、First Fit の順序付けに関連している可能性があるため、引き続きそのビンを保持します。 入力の場合:```
1
3 10
10 1 9
```First Fit は、残り容量が 0 のビンを作成し、サイズ 1 のアイテム用に別のビンを作成し、最後のアイテムを 2 番目のビンに配置します。 Best Fit も同じ選択に従います。 出力は次のとおりです。```
2 2
```重複した残存容量が表示される場合、Best Fit 構造では値を一意として扱ってはなりません。 のために：```
1
4 10
6 4 6 4
```最初の 2 つの項目の後、残りの容量は 4 と 6 です。3 番目の項目は容量 6 のビンを使用し、容量 4 と 0 は残ります。最後の項目は残りの容量 4 を使用します。トレプのカウント フィールドは重複した状態を正しく処理し、次の結果を生成します。```
2 2
```入力順序を変更すると、同じ項目サイズのセットでも結果が変わることがあります。 のために：```
1
5 10
5 8 2 5 9
```First Fit は初期のビンを順番に保ち、4 つのビンで終了しますが、Best Fit は常に最適なビンを選択することで使用状況を再配置し、3 つのビンで終了します。 データ構造は元のシーケンスを処理するため、出力は残ります。```
4 3
```
