---
title: "CF 102423G - ジャンプパス"
description: "うちには根が生えた木があります。 すべての頂点には整数のラベルが付いています。 ジャンプ パスは、ツリーを厳密に下向きに取得した頂点のシーケンスであり、以前のすべての頂点が後のすべての頂点の祖先になります。"
date: "2026-08-12T01:17:53+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102423
codeforces_index: "G"
codeforces_contest_name: "North American Southeast Regional 2019 (Div 1)"
rating: 0
weight: 102423
solve_time_s: 161
verified: true
draft: false
---

[CF 102423G - ジャンプ パス](https://codeforces.com/problemset/problem/102423/G)

 **評価:** -
 **タグ:** -
 **解決時間:** 2 分 41 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 うちには根が生えた木があります。 すべての頂点には整数のラベルが付いています。 ジャンプ パスは、ツリーを厳密に下向きに取得した頂点のシーケンスであり、以前のすべての頂点が後のすべての頂点の祖先になります。 通常のツリー エッジはいくつでもスキップできるため、シーケンス内の連続する頂点が親と子である必要はありません。 

選択した頂点に沿ったラベルは減少していなければなりません。 たとえば、ルートからリーフへのチェーン上のラベルが (2,5,3,7) である場合、(2,5,7) または (2,3,7) を選択できますが、(2,5,3) を選択することはできません。 

すべての頂点 (v) について、動的プログラミングの自然な質問は、最後の頂点が (v) である最長の有効なジャンプ パスです。 前に選択した頂点 (u) が (v) の祖先であり、最大でも (v) のラベルを持つ場合、(u) で終わる有効なパスは (v) によって拡張できます。 

入力には、(n) 個の頂点、そのラベル、その後にルートを除くすべての頂点の親が含まれます。 頂点 (i) の親は頂点 (i) より前に与えられるため、頂点はすでにルートからリーフに向かって位相順序になっています。 出力には、考えられるすべてのエンドポイントにわたる最大パス長と、その長さを持つパスの数のモジュロ (11092019) が含まれます。 公式の問題では (n\le 10^6) と ([0,10^6]) のラベル値が使用されます。 

(n) のサイズは二次関数を除外します。 チェーンでは、すべての頂点をすべての祖先に対してチェックするには、すでに約 (n(n-1)/2) 個の祖先比較が必要です。これは、(n=10^6) の場合、約 (5\cdot10^{11}) 回の操作に相当します。 (O(n\sqrt n)) アプローチでさえ、10 秒のコンテスト制限には大きすぎます。 およそ (O(n\log 10^6)) の作業が必要です。 

最初の微妙なケースは単一の頂点です。```
1
7
```その頂点から構成されるパスが 1 つだけ存在するため、答えは次のようになります。```
1 1
```パスの数をゼロに初期化し、既存の祖先を拡張することによってのみパスを作成する実装では、誤ってゼロのパスが生成されます。 

2 番目のエッジ ケースは、ラベルが等しい場合です。 考慮する```
3
5
5
5
1
2
```ツリーはラベル (5,5,5) が付いたチェーンです。 すべての頂点はその親で終わるパスを延長できるため、最長のパスの長さは (3) であり、そのようなパスは 1 つだけ存在します。 

比較する必要があるのは、`ancestor_label <= current_label`、厳密ではありません`<`。 厳密な比較を使用すると、誤った答えが返されます`1 3`。 

3 番目のケースは、同じ最適な長さを持ついくつかの異なる先行処理に関するものです。 考慮する```
3
1
3
2
1
1
```ルートにはラベル (1) があり、両方の子がそれに従うことができます。 どちらのパスも長さは (2) なので、答えは次のようになります。```
2 2
```よくある数え間違いは、複数の先行要素が同じ最適長である場合に、先行要素を 1 つだけ保持することです。 すべての最適な先行者の数を加算する必要があります。 

最後に、ノードはそれ自体をその先行ノードとして使用してはなりません。 現在のラベルをクエリする前にデータ構造に挿入すると、ラベルが等しいと誤って現在の頂点自体が拡張されてしまう可能性があります。 クエリが最初に実行され、その後に挿入が実行される必要があります。 

## アプローチ

 直接動的プログラムは概念的に簡単に記述できます。 させて`dp[v]`を頂点 (v) で終わる最長の有効なジャンプ パスとし、`ways[v]`はそのようなパスの数になります。 ラベルが (v) のラベル以下である (v) のすべての祖先 (u) を検査します。 その中で最大のものを見つけます`dp[u]`。 それから`dp[v]`その値より 1 大きい値であり、`ways[v]`の合計です`ways[u]`すべての祖先がその最大値に達しました。 適切な祖先がない場合、1 つの頂点のパス`[v]`与える`dp[v] = 1`そして`ways[v] = 1`。 

(v) で終わるすべての有効なパスには一意の前の頂点 (u) があり、その前の頂点は (v) のラベル以下のラベルを持つ (v) の祖先でなければならないため、このブルートフォース再帰は正しいです。 問題は、それらの祖先を見つけるためのコストです。 チェーンでは、頂点 (i) には (i-1) 個の可能な先行者があり、次のようになります。 

[
 1+2+\cdots+(n-1)=\frac{n(n-1)}2
 ]

 小切手。 (n=10^6) の場合、つまり (499,999,500,000) 個のチェックになります。 

私たちを救ってくれる構造は、すべてのクエリがルートから現在の頂点までの 1 つのパス上でのみ実行されることです。 ツリーの任意の部分からの情報は必要ありません。 固定された現在の頂点 (v) の場合、その祖先パスに対して 1 つの操作が必要です。最大のラベル (x_v) の間で、最大パス長とその長さに達するパスの総数を見つけます。 

現在のルートから (v) へのパスに属する情報を、ラベルによってインデックス付けされたセグメント ツリーに維持することを想像してください。 ラベル (x) では、セグメント ツリーには、そのラベルを持つアクティブな祖先の中で最適なパス長と、その長さに達するパスの数が格納されます。 ラベル (0) から (x_v) に対するプレフィックス クエリにより、繰り返しに必要な先行情報が正確に得られます。 

複雑な問題が 1 つあります。 ツリーのあるブランチから別のブランチに移動するとき、データ構造はルートから頂点までの異なるパスを表す必要があります。 通常の可変セグメント ツリーでは、すべてのブランチを同時に保持することはできません。 きれいな解決策は持続性です。 すべての頂点は、現在の頂点を挿入することによって親のバージョンから取得された、独自のバージョンのセグメント ツリーを取得します。 

すべての頂点の親のインデックスは小さいため、入力順に頂点を直接処理できます。 バージョン`root[v]`(v) 自体を含め、(v) の先祖を正確に表します。 (v) を計算するとき、クエリを実行します。`root[parent[v]]`, したがって、現在の頂点はまだ挿入されていません。 

ラベルは最大でも (10^6) であるため、セグメント ツリーには約 20 レベルしかありません。 永続的な更新では、ルートからリーフへの 1 つのパス上のノードのみがコピーされます。 したがって、すべての頂点は (O(\log 10^6)) 個の新しいノードを作成します。 

Python の場合、Python 整数の通常のリストは、この規模ではメモリを大量に消費します。 以下の実装では、子インデックスを次の場所に保存します。`array('i')`そして、各セグメント ツリー値を 1 つの 64 ビット整数にパックします。 上位ビットにはパス長が格納され、下位 24 ビットにはカウント モジュロ (11092019) が格納されます。 これにより、永続的な構造が適切なメモリ フットプリント内に維持されます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | (O(n^2)) | (O(n)) | 遅すぎる |
 | 永続セグメント ツリー | (O(n\log X)) | (O(n\log X)) | 承認済み |

 ここで (X\le 10^6) は最大ラベル値です。 

## アルゴリズムのチュートリアル

 1. すべての頂点とその親のラベルを保存します。 すべての親のインデックスは小さいため、頂点 (v) に到達した時点では、頂点 (v) に必要なすべての情報がすでに利用可能です。 
2. 定義する`root[v]`(v) の祖先を表す永続的なセグメント ツリー バージョンのルートとして、ラベルによってインデックス付けされた最適パス情報とともに。 ルート頂点の場合、開始バージョンは空です。 
3. (0) から (0) までのラベル間隔で親バージョンをクエリします。`label[v]`。 結果は、先行パスの最大長とその長さの先行パスの総数で構成されるペアになります。 
4. クエリが先行データを返さない場合は、次の値を割り当てます。`dp[v] = 1`そして`ways[v] = 1`。 単一の頂点自体が有効なパスを形成します。 
5. それ以外の場合は割り当てます`dp[v] = best_length + 1`そして`ways[v] = best_count`。 有効な先行パスで終了するすべての最適なパスは (v) によって一意に拡張できるため、結果のパスの数は正確に先行パスの数になります。 
6.作成`root[v]`しつこく挿入することで`(dp[v], ways[v])`で`label[v]`親のバージョンに。 複数の祖先が同じラベルと同じ最適長を持っている場合、それらのカウントはそのリーフでマージされます。 
7. 頂点の処理中にグローバルな答えを維持します。 頂点のサイズが大きい場合`dp`、グローバルの長さとカウントを置き換えます。 同じ長さの場合は、その長さを追加します。`ways`グローバルカウントモジュロ (11092019) に変換します。 

クエリは更新前に実行する必要があります。 この順序は、現在の頂点がそれ自体の先行頂点とみなされるのを防ぐ重要な詳細です。 

### なぜ効果があるのか

 不変条件は、頂点 (v) を処理する直前に、`root[parent[v]]`(v) のすべての祖先のパス情報が正確に含まれており、他の頂点は含まれていません。 各ラベルで、保存された値は、そのラベルを持つ祖先で終わる最適なパスと、その最適な長さを達成する方法の数を表します。 プレフィックスクエリ`label[v]`したがって、法的に先行できる祖先を正確に考慮します (v)。 

次に、再帰では、(v) で終わる最適なパスの可能な最後の先行者がすべて考慮されます。 先行文字の最大長を選択すると、(v) を追加した後の最大長が得られます。 複数の先行ノードが同じ長さである場合、最終頂点が異なるため、それらのパス セットは互いに素になるため、それらのカウントを加算できます。 永続的な更新では、別のブランチに属するバージョンを変更することなく、すべての子孫の結果の状態が記録されます。 

すべての有効なジャンプ パスには最終頂点が 1 つだけあるため、最大値を取得すると、`dp[v]`すべての頂点にわたって大域的な最適値が得られ、合計すると`ways[v]`その最適化に達する頂点を超えると、すべての最長パスが 1 回だけカウントされます。 

## Python ソリューション```python
import sys
from array import array

input = sys.stdin.readline

MOD = 11092019
COUNT_MASK = (1 << 24) - 1

def solve():
    n = int(input())

    labels = array('i')
    max_label = 0

    for _ in range(n):
        x = int(input())
        labels.append(x)
        if x > max_label:
            max_label = x

    parent = array('i', [0]) * n
    for v in range(1, n):
        parent[v] = int(input()) - 1

    # Use a complete binary range [0, size - 1].
    # size is a power of two larger than every possible label.
    size = 1
    while size <= max_label:
        size <<= 1

    height = size.bit_length() - 1

    # Node 0 is the null node.
    left = array('i', [0])
    right = array('i', [0])
    value = array('Q', [0])

    roots = array('i', [0]) * n

    # Reused fixed-size buffer for the nodes copied on one update.
    path = [0] * (height + 1)

    best_global = 0
    count_global = 0

    for v in range(n):
        if v == 0:
            base_root = 0
        else:
            base_root = roots[parent[v]]

        x = labels[v]

        # Query [0, x] in the persistent binary segment tree.
        node = base_root
        best_len = 0
        best_cnt = 0

        for bit in range(height - 1, -1, -1):
            if node == 0:
                break

            if (x >> bit) & 1:
                child = left[node]
                if child:
                    z = value[child]
                    zlen = z >> 24
                    zcnt = z & COUNT_MASK

                    if zlen > best_len:
                        best_len = zlen
                        best_cnt = zcnt
                    elif zlen == best_len:
                        best_cnt += zcnt

                node = right[node]
            else:
                node = left[node]

        # Include the exact leaf x.
        if node:
            z = value[node]
            zlen = z >> 24
            zcnt = z & COUNT_MASK

            if zlen > best_len:
                best_len = zlen
                best_cnt = zcnt
            elif zlen == best_len:
                best_cnt += zcnt

        best_cnt %= MOD

        if best_len == 0:
            dp = 1
            ways = 1
        else:
            dp = best_len + 1
            ways = best_cnt

        # Persistently insert (dp, ways) at label x.
        #
        # Copy the root first, then copy one child per level.
        old = base_root

        new_root = len(value)
        left.append(left[old])
        right.append(right[old])
        value.append(value[old])
        path[0] = new_root

        cur_old = old
        cur_new = new_root

        for level, bit in enumerate(range(height - 1, -1, -1), 1):
            if (x >> bit) & 1:
                old_child = right[cur_old]

                new_child = len(value)
                left.append(left[old_child])
                right.append(right[old_child])
                value.append(value[old_child])

                right[cur_new] = new_child
                cur_old = old_child
                cur_new = new_child
            else:
                old_child = left[cur_old]

                new_child = len(value)
                left.append(left[old_child])
                right.append(right[old_child])
                value.append(value[old_child])

                left[cur_new] = new_child
                cur_old = old_child
                cur_new = new_child

            path[level] = cur_new

        # Merge the new value with whatever was already stored at label x.
        old_leaf_value = value[cur_new]
        old_len = old_leaf_value >> 24
        old_cnt = old_leaf_value & COUNT_MASK

        if dp > old_len:
            value[cur_new] = (dp << 24) | ways
        elif dp == old_len:
            value[cur_new] = (dp << 24) | ((old_cnt + ways) % MOD)

        # Rebuild the copied ancestors bottom-up.
        for level in range(height - 1, -1, -1):
            p = path[level]
            lv = value[left[p]]
            rv = value[right[p]]

            llen = lv >> 24
            rlen = rv >> 24

            if llen > rlen:
                value[p] = lv
            elif rlen > llen:
                value[p] = rv
            else:
                if llen == 0:
                    value[p] = 0
                else:
                    cnt = (lv & COUNT_MASK) + (rv & COUNT_MASK)
                    value[p] = (llen << 24) | (cnt % MOD)

        roots[v] = new_root

        if dp > best_global:
            best_global = dp
            count_global = ways
        elif dp == best_global:
            count_global += ways
            count_global %= MOD

    print(best_global, count_global % MOD)

if __name__ == "__main__":
    solve()
```入力配列は次を使用します`array('i')`100 万個の Python 整数にはかなりのオブジェクト オーバーヘッドがかかるため、Python リストではなく。 永続セグメント ツリーは主要なメモリ消費者であるため、Python ではこの表現が重要になります。 

セグメントツリー値は次のようにパックされます。`(length << 24) | count`。 係数は (2^{24}) より小さいため、カウントには 24 ビットで十分です。 最大パス長は (10^6) のみであるため、残りの上位ビットには長さが十分に格納されます。 

クエリはラベルのバイナリ表現に従います。 (x) の対応するビットが 1 である場合、左のサブツリー全体には (x) より小さいラベルが含まれるため、その集合体は右のサブツリーに進む直前に含めることができます。 ビットが 0 の場合、右側のサブツリーには (x) より大きい値が含まれているため、無視する必要があります。 最後の葉は別途付属します。 

更新では、ルートからリーフへのパスが 1 つだけコピーされます。 コピーされたすべてのノードは最初に古い子と集合体を継承し、次に現在のラベルへの分岐が新しくコピーされた子に置き換えられます。 葉に到達した後、新しい`(dp, ways)`ペアがそこでマージされ、コピーされた祖先が 2 つの子から再構築されます。 

固定された`path`array は、頂点ごとに新しい Python リストを割り当てることを回避します。 ラベルセグメントツリーのツリー深さは最大20であるため、そのサイズは(n)に関して一定である。 

ツリー処理にもセグメント ツリーにも再帰はありません。 ツリー自体は 100 万頂点のチェーンになる可能性があるため、再帰的 DFS は Python の再帰制限を超える危険があり、また不要な関数呼び出しのオーバーヘッドも追加されます。 

## 実用的な例

 公式サンプルには、ラベルがすべて等しい 5 つの頂点のチェーンが含まれています。 入力は次のとおりです。```
5
3
3
3
3
3
1
2
3
4
```期待される出力は次のとおりです`5 1`。 

このチェーンでは、すべての新しい頂点がその親で終わる一意のパスを延長できます。 

| 頂点 | ラベル | 最適な先行製品の長さ |`dp`|`ways`| グローバルな結果 |
 | --- | --- | --- | --- | --- | --- |
 | 1 | 3 | 0 | 1 | 1 | 1、1 |
 | 2 | 3 | 1 | 2 | 1 | 2、1 |
 | 3 | 3 | 2 | 3 | 1 | 3、1 |
 | 4 | 3 | 3 | 4 | 1 | 4、1 |
 | 5 | 3 | 4 | 5 | 1 | 5、1 |

 クエリが包括的であるため、等しいラベル条件は正しく処理されます。 頂点はそれ自身の後にのみ挿入されます`dp`値はすでに計算されているため、それ自体を先行値として使用することはありません。 

2 番目の公式サンプルでは、​​ラベルが (4) から (0) に減少します。```
5
4
3
2
1
0
1
2
3
4
```期待される出力は次のとおりです`1 5`。 

後のラベルはすべて小さいため、どの頂点も前の頂点に従うことはできません。 

| 頂点 | ラベル | 最適な先行製品の長さ |`dp`|`ways`| グローバルな結果 |
 | --- | --- | --- | --- | --- | --- |
 | 1 | 4 | 0 | 1 | 1 | 1、1 |
 | 2 | 3 | 0 | 1 | 1 | 1、2 |
 | 3 | 2 | 0 | 1 | 1 | 1、3 |
 | 4 | 1 | 0 | 1 | 1 | 1、4 |
 | 5 | 0 | 0 | 1 | 1 | 1、5 |

 これは、答えが任意の頂点から始まるパスをカウントする理由を示しています。 個々の頂点はそれ自体、長さ 1 の有効なパスであるため、最長のパスは 5 つあります。 

3 番目のサンプルは次のとおりです。```
4
1
5
3
6
1
2
3
```予想される答えは`3 2`。 

ツリーはラベル (1、5、3、6) が付いたチェーンです。 (5>3) であるため、頂点 3 は頂点 2 に従うことはできませんが、ルートに従うことはできます。 頂点 4 は、頂点 2 または頂点 3 の後に続くことができます。 

| 頂点 | ラベル | 最適な先行製品の長さ |`dp`|`ways`|
 | --- | --- | --- | --- | --- |
 | 1 | 1 | 0 | 1 | 1 |
 | 2 | 5 | 1 | 2 | 1 |
 | 3 | 3 | 1 | 2 | 1 |
 | 4 | 6 | 2 | 3 | 2 |

 最長の 2 つのパスは次のとおりです。`[1,2,4]`そして`[1,3,4]`。 頂点 4 での 2 というカウントは、2 つの同等に良好な先行カウントを加算することから直接得られます。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O(n\log X)) | 各頂点は 1 つのプレフィックス クエリと 1 つの永続ポイント更新を実行し、それぞれ (O(\log X)) | を受け取ります。 
| スペース | (O(n\log X)) | 永続的な更新ごとに (O(\log X)) 個の新しいセグメント ツリー ノードが作成されます。 

ここでは (X\le 10^6) なので、対数係数は最大でも約 20 です。 頂点が 100 万個ある場合、永続ツリーには最悪の場合でも約 2,000 万個のコピーされたノードが含まれます。 詰め込まれた`array`表現は、そのスケールを Python で実用化するために特に使用されます。 公式の入力限界は (10^6) 頂点ですが、コンテストの問題には 10 秒の制限時間があります。 

## テストケース

 次のテストでは同じものを使用します`solve()`提出物としてのルーチン。 ヘルパーは一時的に標準入力を置き換え、標準出力をキャプチャします。```python
import sys
import io
from array import array

MOD = 11092019
COUNT_MASK = (1 << 24) - 1

def solve():
    input = sys.stdin.readline

    n = int(input())

    labels = array('i')
    max_label = 0

    for _ in range(n):
        x = int(input())
        labels.append(x)
        max_label = max(max_label, x)

    parent = array('i', [0]) * n
    for v in range(1, n):
        parent[v] = int(input()) - 1

    size = 1
    while size <= max_label:
        size <<= 1

    height = size.bit_length() - 1

    left = array('i', [0])
    right = array('i', [0])
    value = array('Q', [0])
    roots = array('i', [0]) * n

    path = [0] * (height + 1)

    best_global = 0
    count_global = 0

    for v in range(n):
        base_root = 0 if v == 0 else roots[parent[v]]
        x = labels[v]

        node = base_root
        best_len = 0
        best_cnt = 0

        for bit in range(height - 1, -1, -1):
            if node == 0:
                break

            if (x >> bit) & 1:
                child = left[node]
                if child:
                    z = value[child]
                    zlen = z >> 24
                    zcnt = z & COUNT_MASK

                    if zlen > best_len:
                        best_len = zlen
                        best_cnt = zcnt
                    elif zlen == best_len:
                        best_cnt += zcnt

                node = right[node]
            else:
                node = left[node]

        if node:
            z = value[node]
            zlen = z >> 24
            zcnt = z & COUNT_MASK

            if zlen > best_len:
                best_len = zlen
                best_cnt = zcnt
            elif zlen == best_len:
                best_cnt += zcnt

        best_cnt %= MOD

        if best_len == 0:
            dp = 1
            ways = 1
        else:
            dp = best_len + 1
            ways = best_cnt

        old = base_root

        new_root = len(value)
        left.append(left[old])
        right.append(right[old])
        value.append(value[old])
        path[0] = new_root

        cur_old = old
        cur_new = new_root

        for level, bit in enumerate(range(height - 1, -1, -1), 1):
            if (x >> bit) & 1:
                old_child = right[cur_old]

                new_child = len(value)
                left.append(left[old_child])
                right.append(right[old_child])
                value.append(value[old_child])

                right[cur_new] = new_child
            else:
                old_child = left[cur_old]

                new_child = len(value)
                left.append(left[old_child])
                right.append(right[old_child])
                value.append(value[old_child])

                left[cur_new] = new_child

            cur_old = old_child
            cur_new = new_child
            path[level] = cur_new

        old_leaf_value = value[cur_new]
        old_len = old_leaf_value >> 24
        old_cnt = old_leaf_value & COUNT_MASK

        if dp > old_len:
            value[cur_new] = (dp << 24) | ways
        elif dp == old_len:
            value[cur_new] = (dp << 24) | ((old_cnt + ways) % MOD)

        for level in range(height - 1, -1, -1):
            p = path[level]
            lv = value[left[p]]
            rv = value[right[p]]

            llen = lv >> 24
            rlen = rv >> 24

            if llen > rlen:
                value[p] = lv
            elif rlen > llen:
                value[p] = rv
            elif llen == 0:
                value[p] = 0
            else:
                cnt = (lv & COUNT_MASK) + (rv & COUNT_MASK)
                value[p] = (llen << 24) | (cnt % MOD)

        roots[v] = new_root

        if dp > best_global:
            best_global = dp
            count_global = ways
        elif dp == best_global:
            count_global = (count_global + ways) % MOD

    return f"{best_global} {count_global % MOD}\n"

def run(inp: str) -> str:
    old_stdin = sys.stdin
    try:
        sys.stdin = io.StringIO(inp)
        return solve()
    finally:
        sys.stdin = old_stdin

# Provided sample 1
assert run(
    """5
3
3
3
3
3
1
2
3
4
"""
) == "5 1\n", "sample 1"

# Provided sample 2
assert run(
    """5
4
3
2
1
0
1
2
3
4
"""
) == "1 5\n", "sample 2"

# Provided sample 3
assert run(
    """4
1
5
3
6
1
2
3
"""
) == "3 2\n", "sample 3"

# Provided sample 4
assert run(
    """6
1
2
3
4
5
6
1
1
1
1
1
"""
) == "2 5\n", "sample 4"

# Minimum-size input
assert run(
    """1
42
"""
) == "1 1\n", "single vertex"

# All labels equal, chain
assert run(
    """4
7
7
7
7
1
2
3
"""
) == "4 1\n", "equal labels"

# Equal best predecessors, catches counting mistakes
assert run(
    """3
1
3
2
1
1
"""
) == "2 2\n", "two optimal predecessors"

# Boundary case where the root cannot precede a child
assert run(
    """3
5
4
3
1
2
"""
) == "1 3\n", "strictly decreasing chain"

# Maximum-size structural test.
# A million equal labels in a chain have exactly one longest path.
n = 1_000_000
max_input = (
    str(n)
    + "\n"
    + ("1\n" * n)
    + "".join(f"{i}\n" for i in range(1, n))
)
assert run(max_input) == "1000000 1\n", "maximum-size chain"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`1 / 42`|`1 1`| 最小サイズと先行技術のない基本ケース |
 | 4 つの頂点、すべてのラベル`7`、チェーンで |`4 1`| 包括的なラベルの比較と等しい値の繰り返し |
 | 根`1`、 子供たち`3`そして`2`|`2 2`| 複数の同等に最適な先行処理からのカウントを加算する |
 | 鎖`5,4,3`|`1 3`| より大きなラベルを持つ先行者は拒否される必要があります。 
| チェーン内の 100 万個の等しいラベル |`1000000 1`| 最大頂点数と線形深さツリー |

 最大サイズのテストは、文字通りの 100 万行のブロックではなく、意図的に生成されたテストです。 テスト ソースを読み取り可能な状態に保ちながら、同じ入力構造を実行します。 実際には、このテストは日常的な単体テストではなく、主にメモリと漸近動作をチェックするのに役立ちます。 

## 特殊なケース

 単一の頂点の場合、次のように```
1
7
```親バージョンは空であるため、プレフィックス クエリは長さ 0 を返します。 その結果、アルゴリズムはパスを作成します`[7]`長さ 1、カウント 1 です。 出力は`1 1`。 

ラベルが等しい場合は、次のことを考慮してください。```
3
5
5
5
1
2
```頂点 2 が処理されると、ルートのラベル (5) は包括的なクエリ範囲内にあるため、頂点 2 は`dp = 2`。 頂点 3 が処理されると、頂点 2 の永続バージョンにはラベル (5) を持つ両方の祖先が含まれ、そのラベルでの集合体の長さは 2、カウントは 1 になります。 頂点 3 が受信します`dp = 3`。 出力は`3 1`。 

複数の最適な先行処理については、次のことを考慮してください。```
3
1
3
2
1
1
```ルートは長さ 1 のパスを 1 つ作成します。 両方のラベルが少なくとも 1 つであるため、両方の子はそれを拡張できます。 各子は長さ 2 とカウント 1 を受け取ります。 グローバルな答えは、これら 2 つのエンドポイント数を組み合わせて次のようになります。`2 2`。 

ラベルを減らすには、```
3
5
4
3
1
2
```頂点 2 のクエリは最大 4 つのラベルに制限されているため、ラベル 5 を持つルートは除外されます。 頂点 2 は独自のパスを開始します。 頂点 3 についても同じことが起こります。すべての頂点には`dp = 1`、与える`1 3`。 

最も危険な実装のエッジケースは、クエリの前に挿入することです。 現在の頂点にラベル 5 があり、その親にもラベル 5 があるとします。 現在の頂点が最初に挿入された場合、クエリは新しく挿入された状態を確認し、実際に可能なものよりも 1 つ長いパスを生成することができます。 実装ではクエリを実行することでこれを回避します。`root[parent[v]]`最初に構築する`root[v]`その後のみ`dp[v]`そして`ways[v]`決定されています。 

最後の微妙な点は、セグメント ツリー ノードでのカウントです。 2 つの子は同じ最適長を持つことができますが、異なるパスのセットを表すため、それらの数を加算する必要があります。 1 つの子の長さが厳密に大きい場合、その子の数だけが残ります。 同じマージ ルールがすべての内部ノードと、複数の祖先がラベルを共有する場合のラベル リーフで使用されます。
