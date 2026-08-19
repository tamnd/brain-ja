---
title: "CF 102192I - ZYB を幸せにする"
description: "(n) 個のタイトルがあります。 タイトル (ti) には幸福値 (hi) があります。 任意の文字列 (x) について、(x) が少なくとも 1 回出現するすべてのタイトルを調べます。 ZYB の幸福度 (x) は、対応する (hi) の積です。"
date: "2026-08-18T02:09:20+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102192
codeforces_index: "I"
codeforces_contest_name: "2018 Chinese Multi-University Training, Nanjing U Contest"
rating: 0
weight: 102192
solve_time_s: 267
verified: true
draft: false
---

[CF 102192I - ZYB を幸せにする](https://codeforces.com/problemset/problem/102192/I)

 **評価:** -
 **タグ:** -
 **解決時間:** 4 分 27 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 (n) 個のタイトルがあります。 タイトル (t_i) は幸福値 (h_i) を持ちます。 任意の文字列 (x) について、(x) が少なくとも 1 回出現するすべてのタイトルを調べます。 ZYB の幸福度は、(x) が対応する (h_i) の積であると言えます。 同じタイトル内で複数回発生しても、値が再度乗算されることはありません。 

クエリ (m) の場合、長さが最大 ​​(m) の空ではないすべての小文字文字列が等しい確率で選択されます。 文字列がタイトルの部分文字列ではない場合、その幸福度はゼロです。 期待される幸福度のモジュロ (10^9+7) が必要です。 

入力には最大 (10^4) 個のタイトルが含まれますが、その合計の長さは最大 (3\cdot10^5) です。 サフィックスオートマトンは入力の合計長が線形であるため、この合計の長さは文字列データ構造にとって重要なパラメータです。 クエリ数は (3\cdot10^5) に達する場合もあるため、すべての長さをスキャンして各クエリに答えるのはコストがかかりすぎます。 クエリの長​​さは (10^6) に達する可能性があります。これは、確率分布の分母をタイトルの長さと独立して処理する必要があることを意味します。 

固定 (m) の場合、可能な文字列の数は次のとおりです。 

[
 D_m=26^1+26^2+\cdots+26^m。 
】

 分子は、最大 (m) の長さのすべての個別の文字列の幸福度の値の合計です。 直接列挙することは不可能です。 長さ (300000) の 1 つのタイトルでも、重複を削除する前に (300000\cdot300001/2=45000150000) 個の部分文字列が出現します。 

気づかぬうちに間違いを犯しやすい場所がいくつかあります。 まず、1 つのタイトル内で繰り返される出来事によって幸福度が 2 回以上倍増してはなりません。 例えば、```
1
aaa
2
1
1
```唯一有用な長さ 1 の文字列は次のとおりです。`a`、その幸福度は (2^3) ではなく (2) であるため、答えは (2/26=1/13)、つまり、指定された素数の (153846155) モジュロです。 出現ベースの実装では、次の 3 つのコピーが誤って処理されます。`a`3 つの独立した貢献として。 

次に、複数のタイトルに同じ文字列が出現する場合、その値を乗算する必要があります。 例えば、```
2
a
a
2 3
1
1
```文字列`a`は両方のタイトルで発生するため、幸福度は (2\cdot3=6) になります。 正解は(6/26=461538465)です。 タイトルのセットを持たずにそれぞれの個別の文字列を 1 回だけ保存する実装では、(2+3) または 2 つの値のうちの 1 つが誤って使用される可能性があります。 

第三に、クエリはすべてのタイトルよりも長くなる可能性があります。 考慮する```
1
a
1
1
2
```(m=2) の場合のみ`a`は分子に寄与するため、分子は (1) のままですが、分母は (26+26^2=702) になります。 答えは (702^{-1}=206552708) です。 分母の上限を最長タイトルに設定してはなりません。 

公式アーカイブには、オリジナルの問題とサンプル データが含まれています。 

## アプローチ

 ブルートフォースのアイデアは概念的には単純です。 最大 (m) の長さのすべての候補文字列を列挙し、すべてのタイトルでそれを検索し、どのタイトルにその文字列が含まれているかを判断し、それらの幸福度の値を乗算して、結果を加算します。 これは、期待値の定義に直接従うため、正しいです。 残念ながら候補者数は

 [
 26+26^2+\cdots+26^m=\シータ(26^m)。 
】

 (m=10^6) の場合、これは意味のある計算を超えています。 決して出現しない候補の列挙を避け、その代わりにタイトルのすべての部分文字列を列挙したとしても、長さ (300000) の 1 つのタイトルには (45000150000) 個の部分文字列が出現します。 

すべての個々の文字列を独立して評価できるため、ブルート フォースは機能しますが、ほとんどすべての作業が高度に重複する部分文字列間で繰り返されるため、失敗します。 重要な観察は、サフィックス オートマトンが同じ終了位置のセットを持つ部分文字列をグループ化するということです。 特に、1 つの接尾辞オートマトン状態によって表されるすべての文字列はまったく同じ出現セットを持ち、したがって、それらはまったく同じタイトルのコレクションにも出現します。 したがって、それらの幸福値は同一になります。 

これにより、一般化されたサフィックス オートマトンが自然な圧縮になります。 すべてのタイトルを含む 1 つのオートマトンを構築し、新しいタイトルを挿入する前に現在の状態をルートにリセットします。 すべてのタイトルについて、オートマトンで順を追って説明します。 到達した各状態では、その接尾辞リンクの祖先が現在の接頭辞の接尾辞を表すため、これらの状態はすべて、このタイトル内に出現する部分文字列に対応します。 状態の値にタイトルの (h_i) を乗算しますが、これはタイトルごとに 1 回だけです。 

その後も、1 つの接尾辞オートマトンの状態は依然として複数の異なる部分文字列長を表します。 状態 (v) に長さがある場合`len[v]`およびサフィックス リンク (fa[v]) の場合、それは、長さごとに 1 つの異なる部分文字列を表します。 

[
 [\text{len}[fa[v]]+1,\text{len}[v]]。 
】

 これらの部分文字列はすべて同じオカレンス セットを持ち、したがって同じ幸福度の値になります。 差分配列を使用して、その値を間隔に追加できます。 次に、2 つの接頭語の合計により、これらの状態間隔が正確な長さごとの幸福度の合計に変換され、最終的には (m) までの長さごとの幸福度の合計に変換されます。 

ここで使用される一般化された接尾辞オートマトンの構造は、ルートから独立して複数の文字列を挿入するための標準的な構造です。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | (\Theta(26^m))、または (\Theta(L^2)) は、部分文字列の出現を列挙するだけです。 潜在的に (\Theta(26^m)) | 遅すぎる |
 | 一般化された SAM | (O(L+M+Q)) が償却されます。ここで、(L) はタイトルの合計長、(M) は最大クエリ長です。 (O(L+M+Q)) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 最初にすべてのタイトルを読み、(L) をその全長とします。 通常の拡張によって構築された接尾辞オートマトンは最大 (2L+1) 個の状態を持つため、構築する前に十分なストレージを確保できます。 
2. 一般化された接尾辞オートマトンを構築します。 すべてのタイトルを挿入する前に、`last`根元まで。 現在の状態からの遷移がすでに存在する場合、その長さが現在の状態の長さよりちょうど 1 大きい場合は、それを再利用します。 それ以外の場合は、通常の接尾辞オートマトンの構築とまったく同様に、クローンを作成します。 

既存のトランジションの特別な処理により、人為的な区切り文字を挿入することなく、複数の独立したタイトルが同じオートマトンを共有できるようになります。 
3. 各状態の幸福度の値を (1) に初期化します。 各タイトル (t_i) について、ルートからタイトルをたどります。 現在のプレフィックスに対応する状態に到達したら、サフィックス リンクを上にたどります。 訪問されたすべての状態は (t_i) 内に発生する部分文字列を表すため、その格納された値に (h_i) を掛けます。 

状態を更新する必要があるのは、1 つのタイトルに対して 1 回だけです。 現在のタイトルのインデックスを保存します`seen[state]`。 上向きの歩行が、すでにそのタイトル インデックスでマークされている状態に到達したら、停止します。 そのサフィックス リンクの祖先はすべて、以前のウォーク中にすでにマークされています。 
4. すべてのタイトルを処理した後、状態 (v) は、(v) で表される部分文字列を含むタイトル全体にわたる (h_i) の積に等しい値を持ちます。 

これが機能するのは、サフィックス オートマトンの状態が、等しい終了位置セットを持つ文字列の等価クラスであるためです。 実際の出現位置がタイトル間で異なる場合でも、終了位置セットが等しいことは、タイトル メンバーシップ セットが等しいことを意味します。 
5. すべての非ルート状態 (v) に対して、次を追加します。`value[v]`長さの間隔まで

 [
 [\text{len}[fa[v]]+1,\text{len}[v]]。 
】

 これを差分配列とともに保存します。 

[
 diff[\text{len}[fa[v]]+1] += 値[v],
 】

 [
 diff[\text{len}[v]+1] -= 値[v]。 
】

 ここではサフィックスリンクツリーをたどる必要はありません。 すべての状態はそのサフィックス リンクとその長さをすでに知っているため、すべての間隔を直接処理できます。 
6. 差分配列の 1 つのプレフィックス合計を取得します。 この通過後、`by_len[k]`は、正確な長さ (k) のすべての個別の文字列の幸福度の値の合計です。 
7. 2 番目のプレフィックス合計を計算します。 今`prefix[k]`長さが (1) から (k) までの間のすべての個別の文字列の合計幸福度です。 タイトルの部分文字列ではない文字列は、自動的にゼロになります。 
8. すべてのクエリ (m) について、望ましい期待値は次のとおりです。 

[
 \frac{\text{接頭辞}[m]}
 {26^1+26^2+\cdots+26^m}
 \pmod {10^9+7}。 
】

 (m) が最長タイトルを超えると分子は一定になりますが、分母は増加し続けます。 これが、最長のタイトルよりも大きなクエリでも元の (m) を使用する必要がある理由です。 
9. (3\cdot10^5) 個のクエリが存在する可能性があるため、分母ごとに個別に逆モジュラーを計算すると、(O(Q\log MOD)) の作業が追加されます。 代わりに、個別のクエリ値を並べ替え、長さを一度進めながら分母を計算し、バッチ反転を使用します。 すべての分母の積が 1 回反転され、その後、個々の逆数が線形時間で回復されます。 

### なぜ効果があるのか

 一般化された接尾辞オートマトンの状態 (v) を考えてみましょう。 表現された文字列はすべて同じ終了位置セットを持っているため、まったく同じタイトルに出現します。 したがって、すべてのタイトルの処理中に実行される乗算により、(v) で表されるすべての文字列のハピネス値が正確に得られます。 suffix-link 構造は、すべての個別の部分文字列を互いに素な長さの間隔 ((\text{len}[fa[v]],\text{len}[v]]) に分割し、その間隔内の長さごとに 1 つの個別の部分文字列を使用します。したがって、状態の値をこの間隔に追加すると、出現するすべての個別の文字列が 1 回だけカウントされます。2 つのプレフィックスの合計は、これらの長さごとの寄与をすべてのクエリの分子に変換し、分母は考えられるすべてのランダムな文字列をカウントします。 どこにも出現しない文字列であるため、最後の部分はまさに必要な期待値となります。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

from array import array

MOD = 1000000007
ALPHA = 26

def solve():
    n = int(input())
    titles = [input().strip().encode() for _ in range(n)]
    total_len = sum(len(s) for s in titles)
    max_title_len = max(len(s) for s in titles)

    happiness = list(map(int, input().split()))

    q = int(input())
    queries = [int(input()) for _ in range(q)]
    max_query = max(queries)

    # A SAM built from L characters has at most 2L+1 states.
    max_states = 2 * total_len + 5

    # Compact 32-bit arrays are necessary in Python.
    # transitions[state * 26 + c] stores the destination.
    trans = array('i', [0]) * (max_states * ALPHA)
    link = array('i', [0]) * max_states
    length = array('i', [0]) * max_states
    seen = array('i', [0]) * max_states
    value = array('i', [1]) * max_states

    # Root is state 1.
    size = 1

    for s in titles:
        last = 1

        for ch in s:
            c = ch - 97
            p = last
            edge = trans[p * ALPHA + c]

            if edge:
                # The transition already exists.
                qstate = edge

                if length[qstate] == length[p] + 1:
                    last = qstate
                    continue

                # The existing transition is too long, so clone it.
                clone = size + 1
                size = clone

                length[clone] = length[p] + 1
                link[clone] = link[qstate]

                src = qstate * ALPHA
                dst = clone * ALPHA
                trans[dst:dst + ALPHA] = trans[src:src + ALPHA]

                while p and trans[p * ALPHA + c] == qstate:
                    trans[p * ALPHA + c] = clone
                    p = link[p]

                link[qstate] = clone
                last = clone
                continue

            # Create the usual new state.
            new_state = size + 1
            size = new_state
            length[new_state] = length[p] + 1
            last = new_state

            while p and trans[p * ALPHA + c] == 0:
                trans[p * ALPHA + c] = new_state
                p = link[p]

            if p == 0:
                link[new_state] = 1
                continue

            qstate = trans[p * ALPHA + c]

            if length[qstate] == length[p] + 1:
                link[new_state] = qstate
                continue

            # Split qstate with a clone.
            clone = size + 1
            size = clone

            length[clone] = length[p] + 1
            link[clone] = link[qstate]

            src = qstate * ALPHA
            dst = clone * ALPHA
            trans[dst:dst + ALPHA] = trans[src:src + ALPHA]

            link[qstate] = clone
            link[new_state] = clone

            while p and trans[p * ALPHA + c] == qstate:
                trans[p * ALPHA + c] = clone
                p = link[p]

    # For each title, mark every SAM state whose represented strings occur
    # in that title, and multiply its happiness exactly once.
    for tag, (s, h) in enumerate(zip(titles, happiness), 1):
        cur = 1

        for ch in s:
            cur = trans[cur * ALPHA + ch - 97]

            v = cur
            while v and seen[v] != tag:
                seen[v] = tag
                value[v] = value[v] * h % MOD
                v = link[v]

    # Difference array over substring lengths.
    diff = array('i', [0]) * (max_title_len + 2)

    for v in range(2, size + 1):
        left = length[link[v]] + 1
        right = length[v]

        diff[left] += value[v]
        if diff[left] >= MOD:
            diff[left] -= MOD

        diff[right + 1] -= value[v]
        if diff[right + 1] < 0:
            diff[right + 1] += MOD

    # First prefix sum gives the contribution of each exact length.
    # Second prefix sum gives the contribution of all lengths <= m.
    current = 0
    cumulative = 0

    for i in range(1, max_title_len + 1):
        current += diff[i]
        if current >= MOD:
            current -= MOD

        cumulative += current
        if cumulative >= MOD:
            cumulative -= MOD

        diff[i] = cumulative

    # Compute denominators for the distinct queried lengths.
    unique_queries = sorted(set(queries))
    denominators = []

    power = 1
    denominator = 0
    position = 0

    for m in unique_queries:
        while position < m:
            power = power * 26 % MOD
            denominator += power
            if denominator >= MOD:
                denominator -= MOD
            position += 1

        denominators.append(denominator)

    # Batch inversion of all distinct denominators.
    k = len(denominators)
    prefix_product = [1] * k
    product = 1

    for i, d in enumerate(denominators):
        prefix_product[i] = product
        product = product * d % MOD

    inverse_product = pow(product, MOD - 2, MOD)
    inverses = [0] * k

    for i in range(k - 1, -1, -1):
        inverses[i] = inverse_product * prefix_product[i] % MOD
        inverse_product = inverse_product * denominators[i] % MOD

    inverse_by_query = {
        m: inv for m, inv in zip(unique_queries, inverses)
    }

    output = []

    for m in queries:
        if m <= max_title_len:
            numerator = diff[m]
        else:
            numerator = diff[max_title_len]

        answer = numerator * inverse_by_query[m] % MOD
        output.append(str(answer))

    sys.stdout.write("\n".join(output))

if __name__ == "__main__":
    solve()
```実装の最初の部分では、オートマトンを割り当てる前にすべてのタイトルを読み取ります。 あらかじめ全長がわかるので、遷移表をコンパクトに1枚に収納できます。`array('i')`(26(2L+5)) エントリあり。 リストの Python リストは、すべての整数とすべてのリストに Python オブジェクトのオーバーヘッドがあるため、はるかに多くのメモリを使用します。 

挿入コードは、通常のサフィックス オートマトン拡張の一般化されたバージョンです。 現在の状態はタイトルごとにルートにリセットされます。 既存のトランジションが必要な長さを正確に持っている場合、新しいプレフィックスを直接表すことができます。 必要以上に長い場合は、等価クラスを分離するためにクローンを作成する必要があります。 クローンは発信トランジションのコピーを受け取り、古いサフィックス リンクを継承します。 

の`seen`配列にはタイトル番号によってインデックスが付けられます。 これは、タイトルごとにブール配列をクリアするよりもコストがかかりません。 1 つのタイトルのトラバース中に、次の状態に到達します。`seen`値がすでに現在のタイトルと等しいということは、状態とその上のすべてのサフィックスリンクの祖先がすでに処理されていることを意味します。 

差分配列を構築する際、ルートは意図的にスキップされます。 これは空の文字列を表しますが、問題内のランダムな文字列は空ではない必要があります。 間隔は次から始まります`length[link[v]] + 1`、エンドポイントは`length[v]`、したがって、右側の境界は包括的です。 減算は次の場所に配置されます。`right + 1`、これは標準的な差分配列規則です。 

すべての幸福積は、直ちに剰余 (10^9+7) で減算されます。 Python の整数はオーバーフローしませんが、モジュラー リダクションを遅らせると、値が不必要に大きくなり、乗算が遅くなります。 

分母は長さごとに保存されるわけではありません。 クエリはソートされ、(26) の実行力は必要な範囲のみに拡張されます。 これは (O(M)) 時間を使用します。(M) が最大のクエリです。 次に、バッチ逆変換により、すべての分母逆変換が 1 つのべき乗剰余と線形仕事に減ります。 

分子配列は最長のタイトルまで拡張するだけで済みます。 その点を超えると、新たに発生する文字列は存在しないため、分子は一定のままです。 ただし、分母はクエリが大きくなるたびに増加し続けます。 

## 実用的な例

 公式サンプルは、```
2
zybnb
ybyb
3 5
4
1
2
3
4
```1 番目のタイトルでは幸福度の値は (3)、2 番目のタイトルでは (5) になります。 長さ別にグループ化された個別に出現する文字列には、次の合計幸福度があります。 

| 長さ | 個別に出現する文字列 | この長さの貢献 | 累積分子 |
 | --- | --- | --- | --- |
 | 1 |`z`、`y`、`b`、`n`| (3+15+15+3=36) | 36 |
 | 2 |`zy`、`yb`、`bn`、`nb`、`by`| (3+15+3+3+5=29) | 65 |
 | 3 |`zyb`、`ybn`、`bnb`、`yby`、`byb`| (3+3+3+5+5=19) | 84 |
 | 4 |`zybn`、`ybnb`、`ybyb`| (3+3+5=11) | 95 |

 (m=1) の場合、分母は (26) であるため、期待値は (36/26=18/13) となり、次のようになります。`769230776`。 (m=2) の場合、分母は (26+676=702)、分子は (65) となり、次のようになります。`425925929`。 残りの 2 つのクエリは分子 (84) と (95) を使用し、公式の出力を生成します。`891125950`そして`633120399`。 

状態レベルの SAM 計算は、これらの長さの寄与に圧縮されます。 たとえば、間隔が長さ (2) から (4) である状態は、その 1 つの幸福度の値がそれらの長さの 3 つすべてに寄与します。これはまさに差分配列が表すものです。 

2 番目の例として、1 つのタイトルを取り上げます。```
1
ab
2
3
1
2
3
```便利な文字列は次のとおりです`a`、`b`、 そして`ab`、それぞれの幸せ（2）。 長さによる処理は次のとおりです。 

| 長さ | サフィックスリンク間隔後の状態寄与 | 正確な長さの合計 | 累積分子 | 分母 |
 | --- | --- | --- | --- | --- |
 | 1 |`a`、`b`それぞれが 2 を貢献します | 4 | 4 | 26 |
 | 2 |`ab`貢献2 | 2 | 6 | 702 |
 | 3 | 文字列が存在しません | 0 | 6 | 18278 |

 したがって、答えは (4/26=307692310)、(6/702=239316241)、および (6/18278) モジュロ (10^9+7) になります。 

この例では、状態区間の境界を調べます。 部分文字列`ab`正確に長さ (2) で寄与する必要がありますが、そのサフィックスは`a`そして`b`他の州を通じて表現されます。 また、クエリがタイトルの最大長を超えると、分子は変化しなくなりますが、分母は変化しないことも示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O(L+M+Q)) 償却 | SAM の構築とタイトル マーキングはタイトルの合計サイズにおいて線形であり、長さの集計は線形で、分母は (O(M)) をとり、バッチ反転と出力は (O(Q)) をとります。 
| スペース | (O(L+M+Q)) | SAM には (O(L)) 個の状態と遷移があり、長さの差の配列には (O(L)) 個のエントリがあり、クエリとバッチ反転配列は (O(Q)) 個のスペースを使用します。 

ここでは (L\le3\cdot10^5)、(M\le10^6)、および (Q\le3\cdot10^5) です。 コンパクトな整数配列は、Python では特に便利です。これは、遷移テーブルに数百万の Python オブジェクトではなく、およそ (26\cdot2L) 個の 4 バイト整数が含まれるためです。 結果として得られるメモリ フットプリントは、規定の 256 MB 制限を快適に下回りますが、アルゴリズムはクエリの長​​さに指数関数的に依存するすべての操作を回避します。 

## テストケース

 次のハーネスは次のことを前提としています。`solve()`上記のソリューションの関数。 ヘルパーさん`fraction_mod`小さな期待値を直接計算しますが、最後のケースでは、文字列を列挙せずに、許可されるクエリの最大長をチェックします。```python
import sys
import io

MOD = 1000000007

def run(inp: str) -> str:
    global input

    old_stdin = sys.stdin
    old_stdout = sys.stdout
    old_input = input

    sys.stdin = io.StringIO(inp)
    sys.stdout = io.StringIO()
    input = sys.stdin.readline

    try:
        solve()
        return sys.stdout.getvalue()
    finally:
        sys.stdin = old_stdin
        sys.stdout = old_stdout
        input = old_input

def fraction_mod(numerator, m):
    denominator = 26 * (pow(26, m, MOD) - 1) % MOD
    denominator = denominator * pow(25, MOD - 2, MOD) % MOD
    return numerator * pow(denominator, MOD - 2, MOD) % MOD

# Provided sample
sample = """\
2
zybnb
ybyb
3 5
4
1
2
3
4
"""

assert run(sample) == (
    "769230776\n"
    "425925929\n"
    "891125950\n"
    "633120399\n"
), "sample"

# Minimum-size input
case_min = """\
1
a
1
1
1
"""

assert run(case_min) == "576923081\n", "minimum case"

# Same string in three titles, all happiness values equal.
# The string a must contribute 2*2*2 = 8, not 2.
case_equal = """\
3
a
a
a
2 2 2
2
1
2
"""

assert run(case_equal) == (
    str(fraction_mod(8, 1)) + "\n" +
    str(fraction_mod(8, 2)) + "\n"
), "equal values and repeated titles"

# Boundary between exact substring lengths.
# a and b have contribution 2 each, while ab contributes 2.
case_boundary = """\
1
ab
2
3
1
2
3
"""

assert run(case_boundary) == (
    str(fraction_mod(4, 1)) + "\n" +
    str(fraction_mod(6, 2)) + "\n" +
    str(fraction_mod(6, 3)) + "\n"
), "substring-length boundary"

# Maximum permitted query length.
# The numerator is always 1, but the denominator contains 10^6 length levels.
case_max_query = """\
1
a
1
1
1000000
"""

expected_max = fraction_mod(1, 1000000)
assert run(case_max_query) == str(expected_max) + "\n", "maximum query length"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 提供される`zybnb`、`ybyb`サンプル |`769230776`、`425925929`、`891125950`、`633120399`| 完全な参照ケースと重複する部分文字列 |
 | 1タイトル`a`、幸福 1、クエリ 1 |`576923081`| 最小サイズの入力とルート/空でない文字列の境界 |
 | 同じタイトルが 3 つある`a`、すべての幸福 2 |`307692310`、`652421657`| タイトル間の乗算と幸福度の等しい値 |
 | 1タイトル`ab`、幸福 2、クエリ 1、2、3 |`307692310`、`239316241`、`6/18278 mod MOD`| タイトルを超える正確な間隔のエンドポイントとクエリ |
 | 1タイトル`a`、幸福 1、クエリ (10^6) |`S_1000000^{-1} mod MOD`| クエリの最大長と分母が増加し続けるという事実 |

 ## 特殊なケース

 単一のタイトル内での繰り返し発生は、`seen`配列。 のために```
1
aaa
2
1
```タイトルは左から右に処理され、サフィックスリンクのウォークは次の状態を表す可能性があります。`a`数回。 最初の遭遇ではその値が (2) 倍され、その後の遭遇では`seen[state] == 1`そしてその時点で止まります。 したがって`a`、`aa`、 そして`aaa`それぞれが幸福度 (2) を受け取り、発生ごとに (2) を掛け合わせるのではありません。 

タイトルのメンバーシップと出現回数の区別も、次のように正しく処理されます。```
2
a
a
2 3
1
1
```最初のタイトルは状態を示します。`a`そして値を(1)から(2)に変更します。 2 番目のタイトルには別のタグが付いているため、同じ状態を再度マークし、値を (2) から (6) に変更します。 長さ 1 の分子は (6) となり、答えは (6/26=461538465) となります。 回数`a`どちらのタイトル内に表示されても計算には入りません。 

サフィックスとリンクの間隔の境界は、状態の値を追加することで処理されます。`len[fa[v]] + 1`そしてそれを削除します`len[v] + 1`。 のために```
1
ab
2
1
2
```1 文字の部分文字列に対応する状態は長さ 1 で寄与し、状態を表す状態は`ab`長さ 2 で貢献します。 結果の分子は (4) と (6) になります。 減算が次の場所に置かれた場合`len[v]`の代わりに`len[v] + 1`、長さ 2 の寄与は消えます。 

すべてのタイトルよりも長いクエリは、異なる境界を適用します。 のために```
1
a
1
1
2
```唯一の肯定的な幸福の文字列は`a`したがって、どちらのクエリでも分子は (1) のままです。 For (m=2), however, there are (26+676=702) possible strings, giving (1/702=206552708). The implementation keeps the numerator at its last computed value while continuing to extend the denominator through every queried length.

 Finally, the root state is never added to the difference array. Its represented string is the empty string, while the random choice contains at least one character. Including the root would add a fictitious length-zero contribution and shift every answer.
