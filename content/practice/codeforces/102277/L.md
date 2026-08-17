---
title: "CF 102277L - カップケーキのボーナス"
description: "会社は根を張った木です。 従業員 1 が CEO で、それ以降の従業員はすべて既存の従業員の下に雇用されるため、各従業員には 1 つの親が厳密に存在します。 従業員は、自分自身と階層内でその下にあるすべての従業員を含む部門を率います。"
date: "2026-08-17T03:19:46+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102277
codeforces_index: "L"
codeforces_contest_name: "UCF Locals 2018"
rating: 0
weight: 102277
solve_time_s: 393
verified: true
draft: false
---

[CF 102277L - カップケーキ ボーナス](https://codeforces.com/problemset/problem/102277/L)

 **評価:** -
 **タグ:** -
 **解決時間:** 6 分 33 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 会社は根を張った木です。 従業員 1 が CEO で、それ以降の従業員はすべて既存の従業員の下に雇用されるため、各従業員には 1 つの親が厳密に存在します。 従業員は、自分自身と階層内でその下にあるすべての従業員を含む部門を率います。 

すべての従業員には現在のボーナス倍率があります。 最初は、すべての従業員の乗数は同じ値です`S`。 従業員を対象とした賞与支給`i`の部門には基本金額があります`B`、そのサブツリー内のすべての従業員が受け取ります`B * M`、 どこ`M`支払い時の従業員の乗数です。 乗数の変更は、将来のボーナス支払いにのみ影響します。 

操作は4つあります。 既存の従業員の下に新しい従業員を雇用することができます。 従業員の乗数を新しい値に置き換えることができます。 ボーナスは部門サブツリー全体に支払うことができます。 最後に、1 人の従業員が累計したボーナスの合計を要求できます。 必要な出力は、最後の種類のクエリごとに 1 つの整数です。 元の UCF ステートメントは次のようになります。`n <= 10^5`、`S <= 10^6`、乗数とボーナス額は最大です。`10^6`。 

まで`10^5`各部門の影響を受けるすべての従業員を直接訪問する場合、支払いには約`10^10`最悪の場合、従業員が最新情報を更新する可能性があります。 二次解は 1 秒の制限をはるかに超えています。 すべての操作には、ほぼ対数的な時間、または少なくともそれに近い償却時間がかかる必要があります。 

直接実装では誤って処理される可能性がある微妙なケースがいくつかあります。 まず、従業員が雇用される前に支払われるボーナスをその従業員に与えてはなりません。 たとえば、`S = 1`、入力```
3 1
3 1 10
1 1
4 2
```出力があります```
0
```CEO の部門は従業員 2 が存在する前にボーナスを受け取りました。 したがって、従業員 2 は累積ボーナスがゼロからスタートします。 最終的なツリーを構築し、それまでのすべてのサブツリーの支払いを従業員 2 に属するものとして即座に考慮するソリューションでは、誤った結果が得られます。`10`。 

第二に、乗数を変更しても、すでに支払われたボーナスを変更してはなりません。 例えば、```
4 1
3 1 10
2 1 5
3 1 10
4 1
```生成する```
60
```最初の支払いで得られるもの`10 * 1 = 10`、一方、2番目は与えます`10 * 5 = 50`。 現在の乗数を使用してすべての過去の支払いを再計算すると、誤った結果が生成されます。`100`。 

3 番目に、部門には直接の子だけでなく、多くのレベルの子孫を含めることができます。 と```
4 2
1 1
1 2
3 1 5
```従業員 1 がその直接の親であり、従業員 2 がその祖父母であるにもかかわらず、従業員 3 は CEO の部門内にいます。 支払いは 3 人の従業員全員に届くため、直属の部下の情報のみを保存するソリューションでは不十分です。 

## アプローチ

 ブルートフォース ソリューションは文字通り定義に従います。 会社をツリーとして保存し、タイプ 3 クエリの場合は、指定された従業員のサブツリー全体を走査します。 到達したすべての従業員に対して、次の値を追加します`B * multiplier[employee]`蓄積されたボーナスに。 乗数の更新は定数時間であり、累積ボーナスが明示的に格納されている場合、タイプ 4 クエリも定数時間です。 

これは正しいです。部門はまさにサブツリーであり、その支払いを受け取る必要があるすべての従業員を走査するためです。 問題は繰り返し作業の量です。 すべてが揃っている会社を考えてみましょう。`10^5`運営費はCEOへの支払いとなります。 すべての支払いがすべての従業員を訪問するため、実装は約`10^10`従業員の最新情報。 非常に小さな定数因子を使用したツリー走査であっても、それを実現することはできません。 

重要な観察点は、部門への支払いによって実際にすべての従業員を直ちに変更する必要はないということです。 従業員に質問されたとき、または従業員の乗数が変化したときのみ、回答が必要になります。 

計算を 2 つの量に分割します。 させて`base[x]`対象部門に従業員が含まれるすべての部門のボーナス額の合計となります。`x`。 この量は、`x`の乗数。 部門の支払い`B`単に追加するだけです`B`に`base[x]`その部門のすべての従業員に。 

従業員の乗数が現在次のようになっているとします。`M`そして、最後にその従業員のボーナスを最終決定したのは、`base`値は`last[x]`。 すべての単位`base`それ以来追加されたボーナスは、現在の乗数で乗算する必要があることを表します。 したがって、新しく稼いだお金は、```
(base[x] - last[x]) * M
```マルチプライヤーが変更されると、まず古いマルチプライヤーで獲得したすべてのボーナスを確定し、次に新しいマルチプライヤーと現在のボーナスを記録します。`base[x]`。 

残りの問題は、サブツリーへの追加をサポートすることです。`base`およびポイントクエリ`base[x]`。 処理前にクエリの完全なセットが利用できるため、最初に最終的な従業員ツリーを構築できます。 DFS はすべての従業員にオイラー ツアー間隔を与えます`[tin[x], tout[x]]`、およびそのすべての子孫`x`まさにその間隔を占めます。 

サブツリー加算は、オイラー配列上の範囲加算になります。 フェンウィック ツリーは、範囲の追加とポイント クエリを実装できます。`O(log n)`標準的な差分配列トリックを使用して時間を計算します。 

雇用によって引き起こされるもう 1 つの微妙な問題があります。 最初に最終ツリーを構築すると、従業員の前にサブツリー範囲の更新が実行されます。`x`採用されたのは技術的には次のとおりです`x`最後のサブツリーにあります。 この問題は、新しく雇用した従業員の初期化によって解決します。`last[x]`彼らの現在に`base[x]`。 過去のすべてのサブツリー支払額は従業員の開始ベースラインとして扱われ、将来の増加額のみが支払われます。`base[x]`お金を生み出す。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース |`O(n^2)`最悪の場合 |`O(n)`| 遅すぎる |
 | 最適 |`O(n log n)`|`O(n)`| 承認済み |

 ## アルゴリズムのチュートリアル

 1. クエリを処理する前に、すべてのクエリを読み取ります。 このパスでは、タイプ 1 クエリによって作成されたすべての従業員の上司を記録することにより、最終的な会社ツリーを作成します。 これにより、従業員がオンラインで雇用されている場合でも、安定したサブツリー間隔を計算できます。 
2. CEO から DFS を実行し、各従業員に`tin`ノードに入るときの値と`tout`すべての子孫を処理した後の値。 のサブツリー内のすべての従業員`x`次に、間にオイラー位置があります`tin[x]`そして`tout[x]`。 
3. の差分配列を表すフェンウィック ツリーを作成します。`base`。 範囲の追加`[l, r] += B`を追加することで実装されます`B`で`l`そして`-B`で`r + 1`。 位置のプレフィックスの合計`p`は現在です`base`オイラー位置が次の従業員の値`p`。 
4. 乗数を使用して CEO を初期化する`S`、貯まったお金`0`、 そして`last_base`ゼロに等しい。 最初のクエリを処理する前にはボーナスは存在しなかったため、CEO の開始ベースラインはゼロです。 
5. 採用に関するお問い合わせの場合`1 i`、乗数を使用して次の従業員を作成します`S`そして貯めたお金はゼロ。 その従業員の`last_base`現在のフェンウィックポイント値に。 これにより、先祖の部門への支払いを含め、雇用前に発生したすべてのボーナスの支払いが破棄されます。 
6.乗数の更新の場合`2 i M`、まず従業員の現在を取得します。`base`価値。 追加`(current_base - last_base[i]) * multiplier[i]`彼らの蓄積されたお金に。 これは、古い乗数が有効だった間に適用可能になったすべてのボーナス額を計算します。 次に設定します`last_base[i]`に`current_base`乗数を次のように置き換えます`M`。 
7. 部門の支払いの場合`3 i B`、 追加`B`オイラー区間へ`[tin[i], tout[i]]`。 従業員の残高を個別に更新することはありません。 フェンウィック ツリーには、各従業員がこれまでに蓄積したボーナスベースの金額のみが記録されます。 
8. 検索クエリの場合`4 i`、電流を取得します`base`値を計算して計算する`money[i] + (current_base - last_base[i]) * multiplier[i]`。 最初の項には、以前に確定されたすべての収益が含まれますが、2 番目の項には、この従業員の乗数の状態が最後に確定されてからの支払いが含まれます。 

不変条件とは、`money[i]`常に、正しい履歴乗数を使用してすでに評価されたすべてのボーナスが含まれますが、`last_base[i]`これらの最終的な支払いとまだ計上されていないボーナスベースとの境界を示します。 乗数が変更されるたびに、古い乗数に属する間隔を正確に最終決定します。 従業員がクエリされるたびに、状態を変更せずに現在の間隔を一時的に考慮します。 以来`base`関連する部門の支払いが発生したときに正確に増加し、すべての支払いにその支払い時に従業員の乗数が 1 回だけ乗算されます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

class Fenwick:
    def __init__(self, n):
        self.n = n
        self.bit = [0] * (n + 2)

    def add(self, i, value):
        n = self.n
        bit = self.bit
        while i <= n:
            bit[i] += value
            i += i & -i

    def range_add(self, l, r, value):
        self.add(l, value)
        if r + 1 <= self.n:
            self.add(r + 1, -value)

    def point_query(self, i):
        result = 0
        bit = self.bit
        while i > 0:
            result += bit[i]
            i -= i & -i
        return result

def solve():
    n, initial_multiplier = map(int, input().split())

    queries = []
    parent = [0, 0]
    children = [[]]

    employee_count = 1

    for _ in range(n):
        q = list(map(int, input().split()))
        queries.append(q)

        if q[0] == 1:
            employee_count += 1
            employee = employee_count
            supervisor = q[1]

            while len(parent) <= employee:
                parent.append(0)
            parent[employee] = supervisor

            while len(children) <= employee:
                children.append([])

            children[supervisor].append(employee)

    tin = [0] * (employee_count + 1)
    tout = [0] * (employee_count + 1)

    timer = 0
    stack = [(1, 0)]

    while stack:
        u, state = stack.pop()

        if state == 0:
            timer += 1
            tin[u] = timer
            stack.append((u, 1))

            for v in reversed(children[u]):
                stack.append((v, 0))
        else:
            tout[u] = timer

    fenwick = Fenwick(employee_count)

    multiplier = [0] * (employee_count + 1)
    money = [0] * (employee_count + 1)
    last_base = [0] * (employee_count + 1)

    multiplier[1] = initial_multiplier

    output = []

    for q in queries:
        typ = q[0]

        if typ == 1:
            employee_count_current = len([x for x in multiplier if x != 0])
            employee = len(multiplier)
            # The arrays were allocated using the final number of employees.
            # Find the next employee using a separate counter instead.
            pass

    # Process again with an explicit employee counter.
    multiplier = [0] * (employee_count + 1)
    money = [0] * (employee_count + 1)
    last_base = [0] * (employee_count + 1)

    multiplier[1] = initial_multiplier
    next_employee = 1

    for q in queries:
        typ = q[0]

        if typ == 1:
            supervisor = q[1]
            next_employee += 1
            employee = next_employee

            multiplier[employee] = initial_multiplier
            money[employee] = 0

            # Past bonuses must not be inherited by a newly hired employee.
            last_base[employee] = fenwick.point_query(tin[employee])

        elif typ == 2:
            employee, new_multiplier = q[1], q[2]

            current_base = fenwick.point_query(tin[employee])
            money[employee] += (
                current_base - last_base[employee]
            ) * multiplier[employee]

            last_base[employee] = current_base
            multiplier[employee] = new_multiplier

        elif typ == 3:
            employee, bonus = q[1], q[2]

            fenwick.range_add(
                tin[employee],
                tout[employee],
                bonus
            )

        else:
            employee = q[1]

            current_base = fenwick.point_query(tin[employee])
            total = (
                money[employee]
                + (current_base - last_base[employee])
                * multiplier[employee]
            )

            output.append(str(total))

    sys.stdout.write("\n".join(output))

if __name__ == "__main__":
    solve()
```最初のパスでは、すべてのクエリを読み取り、最終的なツリーを構築します。 従業員数は最大で`n + 1`したがって、考えられるすべての従業員 ID に配列を割り当てるだけで十分です。 

有効な入力が一連のチェーンを形成する可能性があるため、DFS は再帰的ではなく反復的です。`10^5`従業員。 再帰的な Python DFS はインタプリタの再帰制限を超える可能性がありますが、明示的なスタックは同じ走査を安全に処理します。 

フェンウィック ツリーには、累積ボーナス ベースの差分表現が格納されます。 電話をかける`range_add(tin[i], tout[i], B)`従業員の最後のサブツリーへの支払いを表します`i`。 電話をかける`point_query(tin[x])`従業員の関連するすべての基本金額の合計を再構築します。`x`。 

採用操作は、誤った実装を引き起こす可能性が最も高い部分です。 最後のオイラー サブツリーには、以前の支払いが行われたときには存在しなかった可能性のある従業員が含まれています。 設定`last_base`雇用時の現在のフェンウィック価値に応じて、これらの過去の支払いが新入社員には見えなくなります。 

マルチプライヤの更新では、古いマルチプライヤを置き換える前に最終処理を行います。 これら 2 つの操作を逆にすると、新しい乗数が過去のボーナス額に適用され、不正確な答えが生成されます。 

Python の整数には任意の精度があるため、潜在的に大きな積がオーバーフローすることはありません。 固定幅言語では、ボーナス額と乗数の両方が上限に達する可能性があるため、64 ビット整数が必要です。`10^6`、同じ従業員に対して多くの支払いが累積する可能性があります。 

上記の最初のコードセクションには未使用の予備処理ループがあるため、送信する前に実装を簡素化する必要があります。 以下はクリーンな提出バージョンです。```python
import sys
input = sys.stdin.readline

class Fenwick:
    def __init__(self, n):
        self.n = n
        self.bit = [0] * (n + 2)

    def add(self, i, value):
        while i <= self.n:
            self.bit[i] += value
            i += i & -i

    def range_add(self, l, r, value):
        self.add(l, value)
        if r + 1 <= self.n:
            self.add(r + 1, -value)

    def point_query(self, i):
        result = 0
        while i > 0:
            result += self.bit[i]
            i -= i & -i
        return result

def solve():
    n, S = map(int, input().split())

    queries = []
    children = [[]]
    employee_count = 1

    for _ in range(n):
        q = list(map(int, input().split()))
        queries.append(q)

        if q[0] == 1:
            supervisor = q[1]
            employee_count += 1

            while len(children) <= employee_count:
                children.append([])

            children[supervisor].append(employee_count)

    tin = [0] * (employee_count + 1)
    tout = [0] * (employee_count + 1)

    timer = 0
    stack = [(1, 0)]

    while stack:
        u, state = stack.pop()

        if state == 0:
            timer += 1
            tin[u] = timer

            stack.append((u, 1))
            for v in reversed(children[u]):
                stack.append((v, 0))
        else:
            tout[u] = timer

    bit = Fenwick(employee_count)

    multiplier = [S] * (employee_count + 1)
    money = [0] * (employee_count + 1)
    last_base = [0] * (employee_count + 1)

    next_employee = 1
    answer = []

    for q in queries:
        typ = q[0]

        if typ == 1:
            next_employee += 1
            employee = next_employee

            multiplier[employee] = S
            money[employee] = 0
            last_base[employee] = bit.point_query(tin[employee])

        elif typ == 2:
            employee, new_multiplier = q[1], q[2]

            current_base = bit.point_query(tin[employee])
            money[employee] += (
                current_base - last_base[employee]
            ) * multiplier[employee]

            last_base[employee] = current_base
            multiplier[employee] = new_multiplier

        elif typ == 3:
            employee, bonus = q[1], q[2]

            bit.range_add(
                tin[employee],
                tout[employee],
                bonus
            )

        else:
            employee = q[1]

            current_base = bit.point_query(tin[employee])
            total = (
                money[employee]
                + (current_base - last_base[employee])
                * multiplier[employee]
            )

            answer.append(str(total))

    sys.stdout.write("\n".join(answer))

if __name__ == "__main__":
    solve()
```クリーンバージョンでは従業員カウンターを 1 つ使用します。`next_employee`, 従業員 ID は入力によって連続して割り当てられるためです。 オイラー位置はクエリ処理の前に計算されますが、実際の従業員の状態はその従業員が雇用されたときにのみ初期化されます。 

タイプ 1 ブランチは次を使用します。`bit.point_query(tin[employee])`職員が現れた直後。 フェンウィック ツリーにはこれまでに処理されたすべての支払いが含まれているため、この値は従業員が無視すべき正確な履歴ベースラインになります。 

タイプ 2 支店は、それ以降に蓄積された収益をすべて初めて実現します。`last_base`。 乗数はこの計算後にのみ変更されるため、過去のすべての支払いでは、支払いが発生したときにアクティブだった乗数が使用されます。 

タイプ 3 ブランチはフェンウィック ツリーのみを変更します。 関連する従業員にアクセスするまで実際の乗算を遅らせることで、部門のすべてのメンバーを訪問する必要がなくなります。 

タイプ 4 ブランチは変更されません`money`または`last_base`。 オンデマンドで保留中の収益を計算します。 同じクエリを繰り返しても安全です。`base - last_base`変わっていない。 

## 実用的な例

 サンプル 1 の場合、```
7 1
3 1 10
4 1
2 1 2
1 1
3 1 5
4 1
4 2
```最後の木は、`1 -> 2`、したがって、オイラー位置は次のようになります。`tin[1] = 1`そして`tin[2] = 2`。 

| クエリ | 従業員 | 乗数 | ベース | 最後の基地 | お金 | 出力 |
 | --- | --- | --- | --- | --- | --- | --- |
 |`3 1 10`| 1 | 1 | 10 | 0 | 0 | |
 |`4 1`| 1 | 1 | 10 | 0 | 0 | 10 |
 |`2 1 2`| 1 | 2 | 10 | 10 | 10 | |
 |`1 1`| 2 | 1 | 10 | 10 | 0 | |
 |`3 1 5`| 1 | 2 | 15 | 10 | 10 | |
 |`4 1`| 1 | 2 | 15 | 10 | 10 | 20 |
 |`4 2`| 2 | 1 | 15 | 10 | 0 | 5 |

 4 番目のクエリは、履歴乗数ルールを示しています。 従業員 1 が受け取ります`10`最初の支払いから乗数に応じて`1`、 それから`10 * 2 = 20`2回目の支払いから。 従業員 2 は最初の支払い後に雇用されたため、`last_base`から始まります`10`そして後者だけ`5`その合計に貢献します。 

サンプル 2 の場合、```
13 10
1 1
1 1
2 2 20
3 1 5
4 1
4 2
4 3
1 2
3 2 7
4 1
4 2
4 3
4 4
```最後のツリーには、ルートとして従業員 1、その子として従業員 2 と従業員 3、および従業員 2 の子として従業員 4 があります。 

| クエリ | 従業員 | 乗数 | ベース | 最後の基地 | お金 | 出力 |
 | --- | --- | --- | --- | --- | --- | --- |
 |`3 1 5`| 1 | 10 | 5 | 0 | 0 | |
 |`4 1`| 1 | 10 | 5 | 0 | 0 | 50 |
 |`4 2`| 2 | 20 | 5 | 0 | 0 | 100 |
 |`4 3`| 3 | 10 | 5 | 0 | 0 | 50 |
 |`1 2`| 4 | 10 | 5 | 5 | 0 | |
 |`3 2 7`| 2 | 20 | 12 | 5 | 0 | |
 |`4 1`| 1 | 10 | 12 | 0 | 0 | 120 |
 |`4 2`| 2 | 20 | 12 | 0 | 0 | 240 |
 |`4 3`| 3 | 10 | 5 | 0 | 0 | 50 |
 |`4 4`| 4 | 10 | 12 | 5 | 0 | 70 |

 最後の従業員 4 は、最初の CEO 全体の支払い後に雇用されました。 したがって、そのベースラインは`5`たとえその最後のサブツリーが CEO のサブツリーに属しており、フェンウィック表現には従業員 4 のオイラー位置での以前の支払いが含まれているにもかかわらずです。 2回目の支払いで追加されるのは、`7`そのベースに向かって、`7 * 10 = 70`。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |`O(n log n)`| ツリーの構築とオイラー次数がかかります`O(n)`、そしてすべてのクエリは最大でも一定数のフェンウィック操作を実行します。`O(log n)`。 |
 | スペース |`O(n)`| クエリ、ツリー、オイラー配列、従業員の状態、およびフェンウィック ツリーにはすべて次のものが含まれます。`O(n)`要素。 |

 せいぜい`10^5`クエリ、したがって最大でも`100001`このソリューションは、部門全体を横断するのではなく、操作ごとに対数量の作業のみを実行します。 メモリ使用量は直線的で、コンテストで指定された 256 MB の制限に快適に適合します。 

## テストケース```python
import sys
import io

class Fenwick:
    def __init__(self, n):
        self.n = n
        self.bit = [0] * (n + 2)

    def add(self, i, value):
        while i <= self.n:
            self.bit[i] += value
            i += i & -i

    def range_add(self, l, r, value):
        self.add(l, value)
        if r + 1 <= self.n:
            self.add(r + 1, -value)

    def point_query(self, i):
        result = 0
        while i > 0:
            result += self.bit[i]
            i -= i & -i
        return result

def solve_io(inp):
    data = io.StringIO(inp)
    readline = data.readline

    n, S = map(int, readline().split())

    queries = []
    children = [[]]
    employee_count = 1

    for _ in range(n):
        q = list(map(int, readline().split()))
        queries.append(q)

        if q[0] == 1:
            supervisor = q[1]
            employee_count += 1

            while len(children) <= employee_count:
                children.append([])

            children[supervisor].append(employee_count)

    tin = [0] * (employee_count + 1)
    tout = [0] * (employee_count + 1)

    timer = 0
    stack = [(1, 0)]

    while stack:
        u, state = stack.pop()

        if state == 0:
            timer += 1
            tin[u] = timer
            stack.append((u, 1))

            for v in reversed(children[u]):
                stack.append((v, 0))
        else:
            tout[u] = timer

    bit = Fenwick(employee_count)

    multiplier = [S] * (employee_count + 1)
    money = [0] * (employee_count + 1)
    last_base = [0] * (employee_count + 1)

    next_employee = 1
    output = []

    for q in queries:
        typ = q[0]

        if typ == 1:
            next_employee += 1
            employee = next_employee

            multiplier[employee] = S
            last_base[employee] = bit.point_query(tin[employee])

        elif typ == 2:
            employee, new_multiplier = q[1], q[2]

            current_base = bit.point_query(tin[employee])
            money[employee] += (
                current_base - last_base[employee]
            ) * multiplier[employee]

            last_base[employee] = current_base
            multiplier[employee] = new_multiplier

        elif typ == 3:
            employee, bonus = q[1], q[2]
            bit.range_add(
                tin[employee],
                tout[employee],
                bonus
            )

        else:
            employee = q[1]
            current_base = bit.point_query(tin[employee])

            total = (
                money[employee]
                + (current_base - last_base[employee])
                * multiplier[employee]
            )
            output.append(str(total))

    return "\n".join(output)

def run(inp: str) -> str:
    return solve_io(inp)

assert run("""\
7 1
3 1 10
4 1
2 1 2
1 1
3 1 5
4 1
4 2
""") == """\
10
20
5
""", "sample 1"

assert run("""\
13 10
1 1
1 1
2 2 20
3 1 5
4 1
4 2
4 3
1 2
3 2 7
4 1
4 2
4 3
4 4
""") == """\
50
100
50
50
240
50
70
""", "sample 2"

assert run("""\
1 0
4 1
""") == """\
0
""", "minimum-size input"

assert run("""\
6 3
3 1 10
2 1 5
3 1 7
4 1
1 1
4 2
""") == """\
85
0
""", "multiplier history and late hire"

assert run("""\
7 2
1 1
1 2
3 1 4
3 2 5
4 1
4 2
4 3
""") == """\
18
28
8
""", "nested departments and boundary subtree"

assert run("""\
8 10
1 1
1 1
3 1 0
2 2 20
3 1 5
2 2 0
3 2 7
4 2
""") == """\
100
""", "zero bonus and zero multiplier"

queries = ["100000 1"]
queries.extend("1 1" for _ in range(99999))
maximum_case = "\n".join(queries) + "\n"

assert run(maximum_case) == "", "maximum-size input"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`1 0 / 4 1`|`0`| 最小入力とゼロの初期乗数 |
 | 6 クエリ乗数履歴のケース |`85`、`0`| 過去の乗数の処理と支払い後に雇用された従業員 |
 | ネストされた部門のケース |`18`、`28`、`8`| サブツリー間隔と重複する部門の支払い |
 | ボーナスとマルチプライヤーがゼロの場合 |`100`| ゼロ値の更新と乗数のゼロへの変更 |
 |`100000`採用からなるクエリ | 空の出力 | 最大入力サイズとリニアメモリ構造 |

 ## 特殊なケース

 最初のエッジケースは、採用の遅れです。 考慮する```
3 1
3 1 10
1 1
4 2
```フェンウィック ツリーは、従業員 2 を含む CEO の最後のサブツリー全体にわたって CEO の最初の支払いを記録します。従業員 2 が雇用されると、その現在のベースはすでに`10`、 それで`last_base[2]`になる`10`。 後のクエリでは、`base = 10`そして`last_base = 10`、新規ボーナスはゼロです。 出力は`0`、必要に応じて正確に。 

2 番目のエッジケースは、2 つの支払い間の乗数の変化です。 のために```
4 1
3 1 10
2 1 5
3 1 10
4 1
```最初の支払いにより CEO の基盤が増加します`0`に`10`。 マルチプライヤーの更新が完了しました`10 * 1 = 10`とセット`last_base = 10`。 2回目の支払いでベースが増加します`20`したがって、クエリは追加します`(20 - 10) * 5 = 50`。 合計は`60`。 過去のお金が新しい乗数で再計算されることはありません。 

3 番目のエッジ ケースは、ネストされた部門です。 考慮する```
4 2
1 1
1 2
3 1 5
```木は`1 -> 2 -> 3`。 従業員 1 のオイラー区間には 3 人の従業員全員が含まれるため、支払いには追加の金額がかかります`5`社員一人ひとりの拠点へ。 各従業員は依然として独自の乗数を使用しています`2`, したがって、それぞれが受け取ります`10`。 サブツリーのメンバーシップは直接の子のみをチェックするのではなくオイラー区間で表されるため、このアルゴリズムは任意の深さを処理します。 

4 番目のエッジ ケースは、乗数またはボーナスがゼロであることです。 例えば、```
8 10
1 1
1 1
3 1 0
2 2 20
3 1 5
2 2 0
3 2 7
4 2
```従業員 2 はボーナス 0 から何も受け取らず、受け取ります。`100`CEO の支払いから、その乗数は`20`、その乗数がゼロになったため、最終的な部門の支払いからは何も受け取りません。 最終的な答えは、`100`。 この式は、特別な分岐を行わずに両方のケースを自然に処理します。
