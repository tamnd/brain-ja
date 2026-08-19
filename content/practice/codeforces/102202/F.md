---
title: "CF 102202F - 経済的に食べる"
description: "正確に (2N) 個の異なるメニューがあります。 メニュー(j)にはランチ価格(lj)とディナー価格(dj)があります。 (1) から (N) までの (k) ごとに、昼食には正確に (k) 個の異なるメニューを選択し、夕食には別の (k) 個の異なるメニューを選択する必要があります。 メニューを両方のグループに表示することはできません。"
date: "2026-08-18T11:16:17+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102202
codeforces_index: "F"
codeforces_contest_name: "2019 KAIST RUN Spring Contest"
rating: 0
weight: 102202
solve_time_s: 1002
verified: false
draft: false
---

[CF 102202F - 経済的に食べる](https://codeforces.com/problemset/problem/102202/F)

 **評価:** -
 **タグ:** -
 **解決時間:** 16 分 42 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 正確に (2N) 個の異なるメニューがあります。 メニュー (j) にはランチ価格 (l_j) とディナー価格 (d_j) があります。 (1) から (N) までの (k) ごとに、昼食には正確に (k) 個の異なるメニューを選択し、夕食には別の (k) 個の異なるメニューを選択する必要があります。 メニューを両方のグループに表示することはできません。 (k) に必要な答えは、選択したすべてのランチとディナーの価格の最小合計です。 

入力には (N) の後に (2N) 個のペア ((l_j,d_j)) が含まれます。 出力には (N) 個の値が含まれます。行 (k) は (k) 個のランチと (k) 個のディナーに最適です。 公式声明では、これらの範囲と以下で使用される 3 つの公式サンプルが確認されています。 

(N\le250000) の場合、最大 (500000) 個のメニューがあります。 すべての答えに対してペアまたはサブセットを検査するアルゴリズムは、限界をはるかに超えています。 (O(N^2)) であっても、最悪の場合、およそ (6.25\times10^{10}) 回の反復を意味します。これは、3 秒の制限にはまったく適していません。 おおよそ (O(N\log N))、または最悪の場合でも線形時間に近い時間が必要です。 

最初のエッジ ケースは (N=1) です。 メニューは 2 つしかないため、答えは単純に、1 つのメニューの最も安いランチの価格と、もう 1 つのメニューの最も安いディナーの価格です。 例えば、```
1
4 9
5 3
```答えがあります`7`, ランチは最初のメニュー、ディナーは2番目のメニューを使用します。 最小ランチと最小ディナーを個別に取る不注意な実装は、同じメニューから両方の価格を選択し、誤って報告する可能性があります。`4`。 

最も安いランチと最も安いディナーが同じメニューに属する場合、別の微妙なケースが発生します。 例えば、```
2
1 100
2 2
100 1
100 100
```答えがある`2`そして`104`。 最初の答えは、ランチの値段です。`1`そしてディナーの値段`1`異なるメニューから選択できるため、競合することはありません。 より一般的には、2 つの最も安価な選択肢が同じメニューからのものである場合、少なくとも一方の側で次善の選択肢を考慮する必要があります。 2 つの最小ヒープを追加するだけでは十分ではありません。 

3 番目のエッジケースは、すでに選択されているメニューをランチからディナーに、またはディナーからランチに変更すると、コストがマイナスになる可能性があることです。 例えば、```
2
1 100
2 3
100 4
100 5
```最初の答えがあります`4`、ランチにはメニュー 1、ディナーにはメニュー 2 を使用します。 2 番目の答えについては、メニュー 2 を夕食から昼食に移動し、寄与度を (2-3=-1) だけ変更してから、メニュー 3 と 4 を夕食に使用する方がよいでしょう。 結果は (1+2+4+5=12) となります。 未使用のメニューを追加するだけのアルゴリズムでは、この交換が見逃されます。 

最後に、答えは 32 ビット整数よりもはるかに大きくなる可能性があります。 (250,000) のランチと (250,000) のディナーでは、最大 (500,000) の価格を選択でき、それぞれのサイズは (10^9) なので、合計は (5\times10^{14}) に達する可能性があります。 Python の整数はこれを自動的に処理しますが、C++ 実装では次のことが必要になります。`long long`。 

## アプローチ

 直接的な総当たりソリューションでは、ランチ、ディナー、または未使用のメニューの有効な割り当てをすべて列挙できます。 固定 (k) の場合、次のようになります。 

[
 \binom{2N}{k}\binom{2N-k}{k}
 】

 最初に (k) ランチ メニューを選択し、次に残ったものから (k) ディナー メニューを選択するため、可能な割り当てが可能になります。 (k) ごとに、割り当ての総数は次のようになります。 

[
 \sum_{k=0}^{N}\frac{(2N)!}{k!k!(2N-2k)!}、
 】

 これは中心の三項次数係数 (2N) であり、指数関数的に増加します。 単一の場合 (k=N) であっても、すでに (\binom{2N}{N}) 個の可能性があります。 このアプローチは、最適値の定義を直接表すため、小さなインスタンスにのみ役立ちますが、その操作数はずっと前 (N=250000) で天文学的なものになります。 

より構造化されたブルート フォース アプローチは、動的プログラミングです。 最初の (i) メニューを処理した後、ランチとディナーの選択肢のすべての可能な数の最小コストを保存できます。 自然状態には 3 つの次元 (DP[i][j][k]) があり、各メニューを無視したり、昼食に割り当てたり、夕食に割り当てたりすることができます。 これにより問題は指数関数から多項式に軽減されますが、結果として得られる (O(N^3)) 計算は依然として (N=250000) に対して大きすぎます。 コンテストのチュートリアルでは、この DP を小さなサブタスクのソリューションとして説明し、その後、完全な制約に対する最小コスト フローの解釈に移ります。 

有益な観察は、すべての (k) を最初から解く必要はないということです。 (k-1) 個のランチ メニューと (k-1) 個のディナー メニューを含む最適なソリューションがすでにあると仮定します。 すべてのメニューを 3 つのセットに分割します: (U)、未使用のメニュー、(L)、現在昼食に割り当てられているメニュー、(D)、現在夕食に割り当てられているメニュー。 

(k-1) から (k) に移動するには、ランチ メニューとディナー メニューがもう 1 つ必要です。 現在の割り当てを再構築するのではなく、変更することを検討してください。 新しいメニューは、コスト (l_i) で (U) から (L) に移動することも、コスト (d_i) で (U) から (D) に移動することもできます。 また、選択したディナー メニューを (l_i-d_i) でコストを変更してランチに交換したり、選択したランチ メニューを (d_i-l_i) で変更してディナーに交換したりすることもできます。 

これらの可能性は、正確に 3 つの有用なパターンに分類されます。 使われなかったメニューを 2 つ持ち込んで、1 つをランチに、もう 1 つをディナーに送ることができます。 既存のディナー メニュー 1 つをランチに移動し、未使用の 2 つのメニューをディナーに使用できます。 あるいは、既存のランチ メニュー 1 つをディナーに移動し、未使用の 2 つのメニューをランチに使用することもできます。 両方向の同時スワップでは、以前の最適な状態を改善することはできません。これは、2 つのスワップでは昼食と夕食のカウントが変更されず、それらの合計の負のコストがすでに前の割り当ての最適性と矛盾するためです。 

これは、最小コスト フロー ソリューションで使用されるものと同じ残差グラフ構造です。 候補の 4 つの関連セットは、正確に、ランチの価格順に並べられた未使用のメニュー、ディナーの価格順に並べられた未使用のメニュー、(d-l) の順に並べられた選択されたランチ メニュー、および (l-d) の順に並べられた選択されたディナー メニューです。 

この総当り攻撃は、考えられるすべての割り当てが明示的に考慮されるため機能しますが、割り当ての数が指数関数的なため失敗します。 残差フローの観察により、これらすべての割り当てを 3 つの最小コストのローカル変換に置き換えることができ、ヒープを使用すると、対数時間で必要な最小値をすべて取得できるようになります。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | 指数 | (O(N)) | 遅すぎる |
 | トライステート動的計画法 | (O(N^3)) | (O(N^2)) | 遅すぎる |
 | ヒープのある残余貪欲 | (O(N\log N)) | (O(N)) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. すべて (2N) のメニューを読み取り、最初にすべてのメニューを未使用のセット (U) に入れます。 ランチの価格で順序付けされた最小ヒープと、ディナーの価格で順序付けされた別の最小ヒープを構築します。 また、スワップの可能性のために 2 つの空のヒープも維持します。1 つは現在 (L) にあるメニューの (d_i-l_i) を含み、もう 1 つは現在 (D) にあるメニューの (l_i-d_i) を含みます。 
2. 3 つの値を含む状態配列を維持します。 州`0`メニューが未使用であることを意味します。状態`1`ランチに選択されたことを意味し、状態を示します`2`ディナーに選ばれたことを意味します。 ヒープには古いエントリを含めることが許可されているため、ヒープを検査するたびに、メニューが必要な状態でなくなったエントリは破棄されます。 この遅延削除により、高価な Python ヒープからの任意の削除が回避されます。 
3. 現在のステップ (k) では、まず 2 つの未使用のメニューが独立して選択されるパターンを検討します。 1つはランチ、もう1つはディナ​​ーになります。 そのコストは、(i\ne j) を使用した最小の (l_i+d_j) です。この場合、両方のメニューは現在使用されていません。 最小ランチエントリと最小ディナーエントリが異なるメニューを参照している場合、それらの合計はこのパターンにとって直ちに最適になります。 同じメニューを参照している場合は、2 番目に優れたランチまたは 2 番目に優れたディナーの選択を使用して比較します。 
4. 現在の夕食メニュー 1 つが昼食になり、未使用のメニュー 2 つが夕食になるパターンを考えます。 現在メニュー (v) が (D) にある場合、ランチコスト (l_v-d_v) に変更します。 2 つの新しいディナー メニューは、(U) の中でディナー価格が最も低い 2 つである必要があります。 したがって、このパターンの最適コストは次のようになります。 

[
 \min_{v\in D}(l_v-d_v)
 +
 \operatorname{twoMin}_{i\in U}(d_i)。 
】

 交換されたメニューは (D) に属し、新しいメニューは (U) に属するため、2 つの項は独立しています。 

1. 対称的に、現在のランチ メニュー 1 つをディナーに移動し、未使用の 2 つのメニューをランチに使用することを検討してください。 その費用は

 [
 \min_{v\in L}(d_v-l_v)
 +
 \operatorname{twoMin}_{i\in U}(l_i)。 
】

 1. 3つのパターンのうち最も安いものを選択します。 対応する状態変更を正確にメニューに適用します。 最初のパターンが勝った場合は、未使用のメニューを 1 つ (L) に移動し、もう 1 つの未使用のメニューを (D) に移動します。 2 番目が勝った場合は、1 つの (D) メニューを (L) に移動し、2 つの (U) メニューを (D) に移動します。 3 番目が勝った場合は、1 つの (L) メニューを (D) に移動し、2 つの (U) メニューを (L) に移動します。 
2. 選択した増分コストを現在の合計に加算します。 変換後は、正確に (k) 個のランチ メニューと (k) 個のディナー メニューが存在するため、結果の合計が (k) の答えになります。 (k=N) まで繰り返します。 

### なぜ効果があるのか

 すべての反復の開始時に、現在の割り当ては (k-1) 件のランチと (k-1) 件のディナーに最適です。 (k) の任意の解は、その割り当ての残差変換と見なすことができます。 無駄なサイクルを削除した後、このような変換では、未使用のメニューを両側に 1 つ追加するか、未使用のメニューから 2 つの夕食を追加しながら 1 つの夕食メニューをランチに移動するか、または未使用のメニューから 2 つのランチを追加しながら 1 つのランチ メニューをディナーに移動する必要があります。 各パターンのコストは、対応するヒープ最小値によって正確に表されます。 したがって、最も安価なパターンを選択すると、最適な ((k-1,k-1)) 割り当てから最適な ((k,k)) 割り当てへの増加が最小限に抑えられます。 不変条件は、反復ごとに維持されるパーティション (U、L、D) が現在の (k) の最適解を表すということです。 

## Python ソリューション```python
import sys
import heapq

input = sys.stdin.readline

def solve():
    n = int(input())
    m = 2 * n

    lunch = [0] * m
    dinner = [0] * m

    for i in range(m):
        lunch[i], dinner[i] = map(int, input().split())

    # state:
    # 0 = unused
    # 1 = lunch
    # 2 = dinner
    state = [0] * m

    # Two heaps for currently unused menus.
    by_lunch = [(lunch[i], i) for i in range(m)]
    by_dinner = [(dinner[i], i) for i in range(m)]
    heapq.heapify(by_lunch)
    heapq.heapify(by_dinner)

    # For a lunch menu, changing it to dinner costs d - l.
    lunch_swap = []

    # For a dinner menu, changing it to lunch costs l - d.
    dinner_swap = []

    def clean(heap, wanted_state):
        while heap and state[heap[0][1]] != wanted_state:
            heapq.heappop(heap)
        return heap[0] if heap else None

    def two_min(heap, wanted_state):
        while heap and state[heap[0][1]] != wanted_state:
            heapq.heappop(heap)

        if not heap:
            return None

        first = heapq.heappop(heap)

        while heap and state[heap[0][1]] != wanted_state:
            heapq.heappop(heap)

        if not heap:
            heapq.heappush(heap, first)
            return None

        second = heapq.heappop(heap)
        heapq.heappush(heap, first)
        heapq.heappush(heap, second)

        return first, second

    def move_unused_to_lunch(i):
        state[i] = 1
        heapq.heappush(lunch_swap, (dinner[i] - lunch[i], i))

    def move_unused_to_dinner(i):
        state[i] = 2
        heapq.heappush(dinner_swap, (lunch[i] - dinner[i], i))

    def move_dinner_to_lunch(i):
        state[i] = 1
        heapq.heappush(lunch_swap, (dinner[i] - lunch[i], i))

    def move_lunch_to_dinner(i):
        state[i] = 2
        heapq.heappush(dinner_swap, (lunch[i] - dinner[i], i))

    def first_two_unused_lunch():
        return two_min(by_lunch, 0)

    def first_two_unused_dinner():
        return two_min(by_dinner, 0)

    total = 0
    answer = []

    for _ in range(n):
        best_cost = None
        best_type = -1
        best_ids = None

        # Type 1:
        # U -> L and U -> D, using two distinct menus.
        a = clean(by_lunch, 0)
        b = clean(by_dinner, 0)

        if a is not None and b is not None:
            if a[1] != b[1]:
                cost = a[0] + b[0]
                ids = (a[1], b[1])
            else:
                pair_l = first_two_unused_lunch()
                pair_d = first_two_unused_dinner()

                candidates = []

                if pair_l is not None:
                    l1, l2 = pair_l
                    candidates.append((l2[0] + b[0], l2[1], b[1]))

                if pair_d is not None:
                    d1, d2 = pair_d
                    candidates.append((a[0] + d2[0], a[1], d2[1]))

                if candidates:
                    cost, lid, did = min(candidates)
                    ids = (lid, did)

            if best_cost is None or cost < best_cost:
                best_cost = cost
                best_type = 1
                best_ids = ids

        # Type 2:
        # D -> L, plus two U -> D.
        sw = clean(dinner_swap, 2)
        pair_d = first_two_unused_dinner()

        if sw is not None and pair_d is not None:
            d1, d2 = pair_d
            cost = sw[0] + d1[0] + d2[0]

            if best_cost is None or cost < best_cost:
                best_cost = cost
                best_type = 2
                best_ids = (sw[1], d1[1], d2[1])

        # Type 3:
        # L -> D, plus two U -> L.
        sw = clean(lunch_swap, 1)
        pair_l = first_two_unused_lunch()

        if sw is not None and pair_l is not None:
            l1, l2 = pair_l
            cost = sw[0] + l1[0] + l2[0]

            if best_cost is None or cost < best_cost:
                best_cost = cost
                best_type = 3
                best_ids = (sw[1], l1[1], l2[1])

        total += best_cost

        if best_type == 1:
            lid, did = best_ids
            move_unused_to_lunch(lid)
            move_unused_to_dinner(did)

        elif best_type == 2:
            sid, d1, d2 = best_ids
            move_dinner_to_lunch(sid)
            move_unused_to_dinner(d1)
            move_unused_to_dinner(d2)

        else:
            sid, l1, l2 = best_ids
            move_lunch_to_dinner(sid)
            move_unused_to_lunch(l1)
            move_unused_to_lunch(l2)

        answer.append(total)

    sys.stdout.write("\n".join(map(str, answer)))

if __name__ == "__main__":
    solve()
```2 つの配列`lunch`そして`dinner`元の価格を保存しながら、`state`は、未使用、ランチ、ディナーのメニューへの現在のパーティションを表します。 新しく選択されたメニューはその価格を正確に支払うため、2 つの未使用のヒープは実際の価格で注文されます。 

2 つのスワップ ヒープには、選択したメニューの寄与が変化する量のみが保存されます。 ランチ メニューにはスワップ値 (d-l) があります。これは、ディナーに変更すると (l) が (d) に置き換えられるためです。 ディナー メニューには、対称的な理由からスワップ値 (l-d) があります。 これらの値は負の値になる可能性があるため、ヒープは元の価格ではなく、符号付きの差によって順序付けする必要があります。 

の`clean`関数は遅延削除を実装します。 メニューは状態間を何度も移動できますが、Python では`heapq`は、任意の要素の効率的な削除をサポートしていません。 代わりに、古いエントリはヒープ内に残り、最上位に到達してその状態がヒープの意味と一致しなくなったときに破棄されます。 

の`two_min`ヘルパーは、最初の 2 つの有効なエントリを一時的に削除してから、それらを復元します。 これにより、メニュー ID による削除をサポートするデータ構造を必要とせずに、現在有効な 2 つの最も安価なメニューが得られます。 各メニューは全体の反復ごとに (O(1)) 回だけ状態を変更するため、作成されるヒープ エントリの総数は (O(N)) になります。 

同じメニューを昼食と夕食の両方にすることはできないため、最初の候補の区別性チェックが必要です。 最安のランチとディナーのエントリが同じ ID を持つ場合、最適な選択肢は 2 つだけです。2 番目に安いランチと 2 番目に安いディナーを食べるか、2 番目に安いランチと 2 番目に安いディナーを食べることです。 

すべての演算は整数演算です。 合計の最大値は (5\times10^{14}) 程度で、Python はオーバーフローせずに処理します。 

## 実用的な例

 最初の公式サンプルには 1 日分と 2 つのメニューが含まれています。```
1
4 9
5 3
```最初は両方のメニューは使用されません。 一番安いランチはメニュー1の料金込みです`4`、最も安いディナーは費用がかかるメニュー 2 です。`3`。 これらは異なるメニューなので、最初のパターンが有効です。 

| ステップ | 未使用の昼食の最小値 | 未使用の夕食最低 | ベストパターン | インクリメント | 合計 |
 | --- | --- | --- | --- | --- | --- |
 | (k=1) | 4、メニュー1 | 3、メニュー2 | U→L + U→D | 7 | 7 |

 変換後は、メニュー 1 が (L) に、メニュー 2 が (D) になります。 答えは`7`。 

2つ目の公式サンプルは、```
2
1 6
2 4
5 3
3 1
```(k=1) の場合、メニュー 1 が原価で最も安いランチです`1`、一方、メニュー 4 は原価で最も安いディナーです。`1`。 それらは異なるものであるため、最初のパターンはコストがかかります`2`。 

(k=2) の場合、メニュー 1 と 4 はすでに選択されています。 未使用のメニューは価格付きのメニュー 2 です`(2,4)`メニュー 3 と価格`(5,3)`。 両方を追加すると直接コストがかかります`2+3=5`。 スワップの代替案にはより多くの費用がかかります。 

| ステップ | (U) メニュー | 最適な候補者 | インクリメント | 合計 |
 | --- | --- | --- | --- | --- |
 | (k=1) | 1:(1,6)、2:(2,4)、3:(5,3)、4:(3,1) | メニュー1→L、メニュー4→D | 2 | 2 |
 | (k=2) | 2:(2,4)、3:(5,3) | メニュー2→L、メニュー3→D | 5 | 7 |

 結果の出力は次のとおりです。`2`そして`7`、公式サンプルと一致します。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O(N\log N)) | 反復は (N) 回あり、反復ごとのヒープ操作の数は一定です。 |
 | スペース | (O(N)) | (2N) 個のメニューと (O(N)) 個のヒープ エントリがあり、遅延した古いエントリも含まれます。 |

 メニューは最大 (500,000) 個あり、状態遷移ごとに一定数のヒープ エントリのみが追加されます。 したがって、対数ヒープ操作は (N=250000) の意図した複雑さの範囲内に容易に収まり、メモリ使用量は線形のままです。 

## テストケース

 次のテストでは、公式の 3 つのサンプルに加えて、最小サイズ、等しい値、矛盾する最小値、両方のスワップ方向、および許容される最大値 (N) を対象とした小規模なケースを使用します。 公式のサンプル データと出力は、Codeforces ステートメントから取得されています。```python
import sys
import io
import heapq

def solve_data(inp: str) -> str:
    data = list(map(int, inp.split()))
    it = iter(data)

    n = next(it)
    m = 2 * n

    lunch = [0] * m
    dinner = [0] * m

    for i in range(m):
        lunch[i] = next(it)
        dinner[i] = next(it)

    state = [0] * m

    by_lunch = [(lunch[i], i) for i in range(m)]
    by_dinner = [(dinner[i], i) for i in range(m)]
    heapq.heapify(by_lunch)
    heapq.heapify(by_dinner)

    lunch_swap = []
    dinner_swap = []

    def clean(heap, wanted):
        while heap and state[heap[0][1]] != wanted:
            heapq.heappop(heap)
        return heap[0] if heap else None

    def two_min(heap, wanted):
        while heap and state[heap[0][1]] != wanted:
            heapq.heappop(heap)

        if not heap:
            return None

        first = heapq.heappop(heap)

        while heap and state[heap[0][1]] != wanted:
            heapq.heappop(heap)

        if not heap:
            heapq.heappush(heap, first)
            return None

        second = heapq.heappop(heap)
        heapq.heappush(heap, first)
        heapq.heappush(heap, second)

        return first, second

    def to_lunch(i):
        state[i] = 1
        heapq.heappush(lunch_swap, (dinner[i] - lunch[i], i))

    def to_dinner(i):
        state[i] = 2
        heapq.heappush(dinner_swap, (lunch[i] - dinner[i], i))

    total = 0
    ans = []

    for _ in range(n):
        best = None

        a = clean(by_lunch, 0)
        b = clean(by_dinner, 0)

        if a is not None and b is not None:
            if a[1] != b[1]:
                candidate = (a[0] + b[0], 1, (a[1], b[1]))
            else:
                pl = two_min(by_lunch, 0)
                pd = two_min(by_dinner, 0)
                candidates = []

                if pl is not None:
                    candidates.append((pl[1][0] + b[0], 1,
                                       (pl[1][1], b[1])))

                if pd is not None:
                    candidates.append((a[0] + pd[1][0], 1,
                                       (a[1], pd[1][1])))

                candidate = min(candidates) if candidates else None

            if candidate is not None:
                best = candidate

        sw = clean(dinner_swap, 2)
        pd = two_min(by_dinner, 0)

        if sw is not None and pd is not None:
            candidate = (sw[0] + pd[0][0] + pd[1][0],
                         2, (sw[1], pd[0][1], pd[1][1]))
            if best is None or candidate[0] < best[0]:
                best = candidate

        sw = clean(lunch_swap, 1)
        pl = two_min(by_lunch, 0)

        if sw is not None and pl is not None:
            candidate = (sw[0] + pl[0][0] + pl[1][0],
                         3, (sw[1], pl[0][1], pl[1][1]))
            if best is None or candidate[0] < best[0]:
                best = candidate

        cost, typ, ids = best
        total += cost

        if typ == 1:
            to_lunch(ids[0])
            to_dinner(ids[1])
        elif typ == 2:
            to_lunch(ids[0])
            to_dinner(ids[1])
            to_dinner(ids[2])
        else:
            to_dinner(ids[0])
            to_lunch(ids[1])
            to_lunch(ids[2])

        ans.append(total)

    return "\n".join(map(str, ans))

def run(inp: str) -> str:
    return solve_data(inp)

# Official samples
assert run("""1
4 9
5 3
""") == "7", "sample 1"

assert run("""2
1 6
2 4
5 3
3 1
""") == "2\n7", "sample 2"

assert run("""4
7 5
5 7
7 4
4 2
2 5
6 4
3 2
1 9
""") == "3\n7\n16\n26", "sample 3"

# Minimum-size case
assert run("""1
7 3
2 9
""") == "5", "N=1 with different cheapest roles"

# All prices equal
assert run("""2
5 5
5 5
5 5
5 5
""") == "10\n20", "all equal values"

# The cheapest lunch and dinner candidates initially conflict
assert run("""2
1 100
2 2
100 1
100 100
""") == "2\n104", "conflicting minima"

# D -> L swap is useful
assert run("""2
1 100
2 3
100 4
100 5
""") == "4\n12", "useful D-to-L swap"

# L -> D swap is useful
assert run("""2
100 1
3 2
4 100
5 100
""") == "4\n12", "useful L-to-D swap"

# Maximum-size case, all prices equal.
# The answer for k is exactly 2*k.
n = 250000
max_input = str(n) + "\n" + "1 1\n" * (2 * n)
max_output = "\n".join(str(2 * k) for k in range(1, n + 1))
assert run(max_input) == max_output, "maximum N"
```最小サイズのテストでは、最初の答えを生成する前に、アルゴリズムが要素を含むためにスワップ ヒープを必要としないことを確認します。 すべて等しいテストでは、メニュー ID によるヒープの順序付けが誤って個別メニューの要件に違反してはいけないため、多数の同順位がチェックされます。 

競合最小テストでは、絶対最小の昼食と夕食を個別に選択することで同じメニューが使用できるかどうかをチェックします。 2 つのスワップ テストでは、マイナスのスワップ コストを含む残差の再割り当ての両方向を検証します。 最終的に生成されたテストは (N=250000) に達するため、実際の最大入力サイズを実行し、すべての価格が同一である場合でも答えが正しいままであることを確認します。 

| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`1 / 7 3 / 2 9`|`5`| 最小 (N)、個別のメニュー要件 |
 | 各価格の 4 つのメニュー`5`|`10 / 20`| 同点と同じ値の繰り返し |
 |`1 100 / 2 2 / 100 1 / 100 100`|`2 / 104`| 矛盾する最小値と次善の選択 |
 |`1 100 / 2 3 / 100 4 / 100 5`|`4 / 12`| 負の (D\to L) スワップ |
 |`100 1 / 3 2 / 4 100 / 5 100`|`4 / 12`| 負の (L\to D) スワップ |
 | (N=250000)、すべての価格`1 1`|`2,4,...,500000`| 最大サイズと大出力 |

 ## 特殊なケース

 (N=1) の場合、入力```
1
4 9
5 3
```両方のメニューが未使用の状態で開始されます。 一番安いランチは`4`メニュー1の中で一番安いディナーは`3`メニュー 2 からのものであるため、最初のパターンが有効であり、コストがかかります`7`。 状態は (L={1}) および (D={2}) となり、必要な答えが正確に得られます。 

矛盾最小状況の場合、```
2
1 100
2 2
100 1
100 100
```最初の反復では、ランチにメニュー 1 を選択し、ディナーにメニュー 3 を選択します。`2`。 残りのメニューには価格が付いています`(2,2)`そして`(100,100)`。 2 回目の反復では、昼食と夕食の費用に直接割り当てます。`2+100=102`、合計は次のようになります`104`。 スワップの代替手段はより高価であり、アルゴリズムは直接割り当てを維持します。 

ディナーからランチへの便利な交換には、```
2
1 100
2 3
100 4
100 5
```最初の答えは、ランチにはメニュー 1 を使用し、ディナーにはメニュー 2 を使用します。`4`。 2 番目の答えでは、メニュー 2 がディナーからランチに変わり、コストも変わります (2-3=-1)。 メニュー 3 と 4 はディナーになります。`4+5=9`したがって、増分は次のようになります。`8`そして合計は`12`。 アルゴリズムが見ているのは、`-1`ディナー スワップ ヒープの最上部に配置され、単に 2 つの未使用のメニューを追加するのではなく、このパターンを正しく選択します。 

左右対称のケース、```
2
100 1
3 2
4 100
5 100
```ランチとしてメニュー 2、ディナーとしてメニュー 1 から始まり、やはり費用がかかります`4`。 メニュー 2 をランチからディナーに移動すると、コストが (2-3=-1) だけ変わりますが、メニュー 3 と 4 では 2 つの新しいランチが提供されます。`4+5=9`。 2 番目の増分は、`8`、生産`12`。 これにより、残差交換の両方向を表現する必要があることが確認されます。 

たとえば、すべての価格が等しい場合、```
2
5 5
5 5
5 5
5 5
```有効な 1 日の割り当てごとにコストがかかります`10`、2 日ごとの課題費用がかかります`20`。 すべての選択肢に同じコストがかかるため、アルゴリズムによるメニュー ID 間のタイブレークは問題になりませんが、明示的な個別 ID チェックにより、1 つのメニューが両方の食事に割り当てられることは依然として防止されます。 

最後に、(N=250000) ですべての価格は次のようになります。`1 1`、ランチとディナーの追加料金はすべて正確にかかります`2`。 したがって、答えは (2,4,6,\ldots,500000) となります。 ヒープには、メニューの状態が変化するにつれて多くの結合されたエントリと多くの古いエントリが含まれるため、このケースでは、可能な最大の入力の下で遅延削除メカニズムも検証されます。
