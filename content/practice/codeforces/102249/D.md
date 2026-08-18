---
title: "CF 102249D - サービスとしてのツリー"
description: "頂点 1 ... N にルート付きツリーを構築する必要があります。すべての要件は (x, y, z) の形式をとります。つまり、x と y から上に向かって進むと、それらの最初の共通頂点が正確に z になる必要があります。"
date: "2026-08-17T21:56:20+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102249
codeforces_index: "D"
codeforces_contest_name: "2019 Facebook Hacker Cup, Qualification Round"
rating: 0
weight: 102249
solve_time_s: 264
verified: true
draft: false
---

[CF 102249D - サービスとしてのツリー](https://codeforces.com/problemset/problem/102249/D)

 **評価:** -
 **タグ:** -
 **解決時間:** 4 分 24 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 頂点にルート付きツリーを構築する必要があります`1 ... N`。 すべての要件には形式があります`(x, y, z)`、つまり、上に向かって歩いていくと、`x`そして`y`、それらの最初の共通頂点は正確に一致する必要があります`z`。 結果として得られる親ポインタが 1 つのルート付きツリーを記述し、すべての要件が満たされている限り、頂点ごとに任意の親を選択できます。 

出力には各頂点の親が示されます。 ちょうど 1 つの頂点に親があります`0`、これがルートです。 この問題では有効なツリーを受け入れるため、2 つの異なる親配列が両方とも正しい可能性があります。 

制約は意図的に小さくなっています。 最大で 60 個の頂点と 120 個の要件があるため、次のようなアルゴリズムが必要になります。`O(NM + N^2)`簡単に十分に高速です。 小さいもの`N`また、頂点セットを再帰的に分割しながら、単純な素セット構造を繰り返し使用することもできます。 除外されるのは、根を張ったラベル付きツリーの徹底的な列挙であり、その数は`N^(N-1)`。 で`N = 60`、これはおおよそです`10^105`, そのため、1 つの候補を急いでチェックしても無駄です。 

誤って対処しやすい特殊なケースがいくつかあります。 

まず、LCA は、クエリされた 2 つの頂点のいずれかになります。 例えば、```
2 1
1 2 1
```有効です。 頂点`1`頂点の祖先です`2`したがって、LCA は次のようになります。`1`、親配列は次のようになります。`0 1`。 常に想定した構造`z`とは異なる必要があります`x`そして`y`このケースは誤って拒否されてしまいます。 

次に、任意のルートを選択するのは安全ではありません。 のために```
3 1
1 2 3
```唯一可能なルートは`3`、 なぜなら`3`両方の祖先でなければなりません`1`そして`2`。 選択する`1`ルートが要求を即座に不可能にするためです。 構築では、現在のセット内に祖先を持つことが強制されない頂点を識別する必要があります。 

第三に、いくつかの要件により、祖先関係にサイクルが強制される可能性があります。 考慮する```
3 3
1 2 2
2 3 3
3 1 1
```最初の要件により強制されるのは、`2`その上`1`、第二勢力`3`その上`2`、そして第三勢力`1`その上`3`。 ルートのあるツリーに 3 つの関係すべてを含めることはできないため、答えは次のようになります。`Impossible`。 

最後に、LCA が現在のルートと等しい要件は、グループ化制約ではなく分離制約です。 例えば、```
3 2
1 2 3
1 3 1
```すべてのトリプルを単にグループ化するだけでは処理できません。 最初の要件が欲しいのは`1`そして`2`の異なる子サブツリー内`3`、一方、2番目は次のように述べています`1`の先祖です`3`。 これらの要件は矛盾します。 すべての要件を通常の結合操作として扱うと、「一緒でなければならない」と「分離しなければならない」の区別が失われます。 

## アプローチ

 ブルートフォースアプローチは概念的には単純です。 根を張ったすべての木を列挙します。`N`ラベル付き頂点を作成し、クエリされたすべてのペアの LCA を計算し、すべての要件を満たす最初のツリーを維持します。 がある`N^(N-1)`根が張ったラベルの付いた木。 検証を考慮する前でさえ、最悪のケースは、`N = 60`は`60^59`、 約`10^105`候補者たち。 チェック中`M`の要件`O(N)`LCA計算では大まかに作業が行われます`O(N^(N-1)MN)`、それは完全に不可能です。 

有益な観察は、1 つの LCA 要件に 2 つの異なる種類の情報が含まれていることです。 

もし`LCA(x, y) = z`、 それから`z`両方の先祖に違いない`x`そして`y`。 もし`z`現在のサブツリーのルートではない場合、`x`、`y`、 そして`z`すべてがそのルートの同じ子サブツリー内に存在する必要があります。 それらを異なる子サブツリーに分割することはできません。 

一方、次の場合は、`z`が現在のサブツリーのルートである場合、`x`そして`y`それらの 1 つがルート自体でない限り、異なる子サブツリーに属している必要があります。 そうでない場合、LCA は以下になります`z`。 

これにより、再帰的な分割手順が得られます。 適切な根を選ぶ`r`現在の頂点セットの場合。 素集合共用体構造を使用して、強制的に同じ子サブツリーに留まる頂点をマージします。`r`。 次に、結果として得られるすべてのコンポーネントは、次の 1 つの子サブツリーになります。`r`。 LCA が適用される要件`r`2 つの非ルート エンドポイントが異なるコンポーネントに配置されるように要求することでチェックされます。 その後、同じプロセスがすべてのコンポーネントに独立して適用されます。 

ルート自体を任意に指定することはできません。 あらゆる要件に対応`LCA(x, y) = z`、 もし`x != z`、 それから`z`力`x`以下になる`z`; 同様に、もし`y != z`、 それから`z`力`y`以下になる`z`。 したがって、現在のセットの有効なルートには、そのセット内の別の頂点からの強制祖先エッジが入ってはいけません。 このような頂点は、強制された祖先関係の最小限の要素です。 

すべての最小頂点を試す必要がない理由は、これらの制約の有用な特性です。 セットが子コンポーネントに分割されると、1 つのコンポーネントに完全に含まれる制約は、他のコンポーネントで行われた選択から独立します。 一部のコンポーネントを構築できない場合、その上で選択したルートを変更しても、同じ制約を同時に消すことはできません。 コンテストの議論では、頂点の失敗したサブセットを含む最小のサブツリーを採用するという点で同じ正当性が与えられています。 

ブルートフォース手法が機能するのは、考えられるすべての階層を明示的に試行するためです。 階層の数が膨大であるため、失敗します。 すべての LCA 要件が頂点を同じ子サブツリーに強制するか、異なる子サブツリーに強制するかのいずれかであるという観察により、問題は分割の繰り返し、つまり多項式に帰着します。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース |`O(N^(N-1) M N)`|`O(N)`| 遅すぎる |
 | 再帰的な DSU パーティション |`O(NM + N^2 α(N))`|`O(N^2)`| 承認済み |

 ## アルゴリズムのチュートリアル

 1. あらゆる要件を保存する`(x, y, z)`そしてそれを 2 つの可能な祖先関係として解釈します。`z -> x`いつ`x != z`そして`z -> y`いつ`y != z`。 これらの関係は、どの頂点が現在の同じサブツリーのルートとして選択できないかを示します。 
2. 完全な頂点セットから開始します`{1, ..., N}`そしてそれに対してツリーを再帰的に構築します。 再帰呼び出しはセットを受け取ります`S`その頂点は、接続された 1 つのルート付きサブツリーを形成すると想定されています。 
3. ルートを選択する前に、ルートを選択するすべての要件を検査します。`z`に属します`S`。 もし`z`中にあります`S`、 両方`x`そして`y`中にもいるはずです`S`、 なぜなら`z`彼らの先祖です。 1 つのエンドポイントがすでに外部に配置されている場合`S`、建設は不可能です。 
4. 頂点を見つける`r`で`S`の別の頂点から強制的に入ってくるエッジがありません。`S`。 言い換えれば、次のような要件があってはなりません`z != r`そして`x = r`、要件はありません`z != r`そして`y = r`。 このような頂点は、このサブツリーのルートとして機能できます。 そのような頂点が存在しない場合、強制された祖先関係には循環が含まれるため、答えは不可能です。 
5. すべての頂点を含む DSU を作成します。`S`。 あらゆる要件に対応`(x, y, z)`完全に含まれている`S`と`z != r`、マージ`x`、`y`、 そして`z`。 以来`r`上にあります`z`、3 つの頂点はすべて、同じ子の下に存在する必要があります。`r`。 3 つのレコードすべてをマージすることで、まさにその要件が満たされます。 
6. すべての要件を検査します`(x, y, r)`。 どちらのエンドポイントも存在しない場合、`r`の場合、DSU コンポーネントは異なる必要があります。 それらが同じコンポーネント内にある場合、両方とも同じ子サブツリー内に存在します。`r`、LCA を厳密に以下にします`r`。 エンドポイントが 1 つある場合、`r`の場合、ルートとその子孫の LCA がルートであるため、要件は自動的に満たされます。 
7. DSU コンポーネント`S - {r}`は現在、の子サブツリーです`r`。 すべてのコンポーネントについて、そのツリーを再帰的に構築します。 再帰的構築が root を返す場合`v`、 セット`parent[v] = r`。 
8. 再帰呼び出しに含まれる頂点が 1 つだけの場合、その頂点はそのサブツリーのルートとなり、それ以上の作業は必要ありません。 すべてのコンポーネントが処理されたら、戻ります`r`発信者に。 
9. 完全なツリーを構築した後、親配列には 1 つだけが含まれます。`0`、ルート、および他のすべての頂点には 1 つの親があります。 必要に応じて、すべての要件の LCA を再計算することで構築を検証できます。 これだけの費用がかかります`O(MN)`防御的な実装チェックとして役立ちます。 

### なぜ効果があるのか

 重要な不変条件は、すべての再帰セットが`S`最終ツリーの 1 つのサブツリーと、LCA が含まれるすべての要件を表します。`S`3 つの頂点がすべて内側にあります`S`。 

現在のルートが`r`。 要件としては`(x, y, z)`と`z != r`、頂点`z`ちょうど 1 人の子の下にあります`r`。 以来`z`両方の先祖に違いない`x`そして`y`、3 つの頂点すべてが同じ子の下にある必要があります。 DSU はそれらをマージするため、一緒に留まる必要がある頂点が構築によって分離されることはありません。 

要件としては`(x, y, r)`、LCA は正確に現在のルートである必要があります。 したがって、`x`そして`y`は異なる子コンポーネントを占有する必要があり、DSU チェックでは、これらが一緒に存在するケースが正確に拒否されます。 

これらのチェックの後、すべての DSU コンポーネントは安全に次の子サブツリーになることができます。`r`。 異なるコンポーネントに属する要件は、厳密にそれらのコンポーネントの 1 つの内部に LCA を持つことはできません。そのような要件では、その 3 つの頂点すべてが同じコンポーネントに強制されるためです。 したがって、再帰的問題は独立しています。 

ルート選択ルールにより、頂点がその祖先となることがすでに必要とされている頂点の上に配置されることが防止されます。 強制祖先グラフにサイクルがある場合、有効なルートは存在しません。 インスタンスが実行可能な場合、最小限の頂点を取得し、制約に従って分割することを繰り返しても実行可能性が維持されるため、ルートの選択を後戻りする必要はありません。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

class DSU:
    def __init__(self, nodes):
        self.parent = {v: v for v in nodes}
        self.size = {v: 1 for v in nodes}

    def find(self, x):
        p = self.parent[x]
        if p != x:
            self.parent[x] = self.find(p)
        return self.parent[x]

    def union(self, a, b):
        a = self.find(a)
        b = self.find(b)
        if a == b:
            return

        if self.size[a] < self.size[b]:
            a, b = b, a

        self.parent[b] = a
        self.size[a] += self.size[b]

def construct_tree(n, constraints):
    parent = [-1] * (n + 1)

    def build(nodes):
        if len(nodes) == 1:
            return nodes[0]

        inside = set(nodes)

        # Every constraint whose LCA is inside this subtree
        # must have all of its vertices inside it.
        for x, y, z in constraints:
            if z in inside and (x not in inside or y not in inside):
                return -1

        # Find a minimal vertex in the forced ancestor relation.
        incoming = {v: False for v in nodes}

        for x, y, z in constraints:
            if z not in inside:
                continue

            if x in inside and x != z:
                incoming[x] = True
            if y in inside and y != z:
                incoming[y] = True

        root = -1
        for v in nodes:
            if not incoming[v]:
                root = v
                break

        if root == -1:
            return -1

        dsu = DSU(nodes)

        # If the current root is r and z != r, then x, y, z
        # must all lie in the same child subtree of r.
        for x, y, z in constraints:
            if z not in inside or z == root:
                continue

            dsu.union(x, y)
            dsu.union(x, z)

        # If z is the current root, x and y must be in
        # different child subtrees unless one of them is root.
        for x, y, z in constraints:
            if z != root:
                continue

            if x == root or y == root:
                continue

            if dsu.find(x) == dsu.find(y):
                return -1

        # Build the DSU components excluding the root.
        groups = {}

        for v in nodes:
            if v == root:
                continue

            r = dsu.find(v)
            groups.setdefault(r, []).append(v)

        # Every component becomes one child subtree of root.
        for component in groups.values():
            child_root = build(component)
            if child_root == -1:
                return -1
            parent[child_root] = root

        return root

    root = build(list(range(1, n + 1)))

    if root == -1:
        return None

    parent[root] = 0
    return parent[1:]

def lca(parent, a, b):
    ancestors = set()

    while a != 0:
        ancestors.add(a)
        a = parent[a]

    while b != 0:
        if b in ancestors:
            return b
        b = parent[b]

    return 0

def valid_tree(parent, constraints):
    n = len(parent)
    if parent.count(0) != 1:
        return False

    # Check that every parent pointer stays inside the vertex range.
    for p in parent:
        if p < 0 or p > n:
            return False

    # Check that the parent pointers contain no cycle.
    for v in range(1, n + 1):
        seen = set()
        u = v

        while u != 0:
            if u in seen:
                return False
            seen.add(u)
            u = parent[u]

    for x, y, z in constraints:
        if lca(parent, x, y) != z:
            return False

    return True

def solve_case(n, constraints):
    answer = construct_tree(n, constraints)

    if answer is None:
        return None

    # Defensive verification. The construction itself is sufficient,
    # but this catches implementation mistakes without changing
    # the asymptotic complexity.
    if not valid_tree(answer, constraints):
        return None

    return answer

def main():
    t = int(input())
    out = []

    for case_id in range(1, t + 1):
        n, m = map(int, input().split())
        constraints = [
            tuple(map(int, input().split()))
            for _ in range(m)
        ]

        answer = solve_case(n, constraints)

        if answer is None:
            out.append(f"Case #{case_id}: Impossible")
        else:
            out.append(
                f"Case #{case_id}: " +
                " ".join(map(str, answer))
            )

    sys.stdout.write("\n".join(out))

if __name__ == "__main__":
    main()
```の`DSU`コンポーネントは現在のサブツリーに対してのみ意味があるため、クラスは各再帰呼び出しに対してローカルです。 パス圧縮が繰り返され続ける`find`これらの制約に対して操作は事実上一定です。 

の`build`関数はまず、LCA が現在のセットに属する要件がそのセットの外に及んでいないかどうかを確認します。 この条件は祖先から直接引き継がれます。`z`がサブツリー内にある場合、クエリされた両方の頂点はその子孫である必要があります`z`したがって、それらもサブツリー内に存在する必要があります。 

の`incoming`配列は強制的な祖先関係を記録します。 いつ`z != x`、要件には次のように書かれています`z`上にある必要があります`x`、 それで`x`現在のルートにすることはできません。 同じ理由が次の場合にも当てはまります`y`。 のような自己関係`LCA(x, y) = x`マークしません`x`入ってくるエッジがあるため、`x`の祖先であることが許可されています`y`。 

最初の DSU パスは、LCA が現在のルートではない要件のみを処理します。 間の区別`z != root`そして`z == root`が不可欠です。 前者は、3 つの頂点が 1 つの子の下に一緒に留まらなければならないことを意味します。`root`; 後者は、クエリされた 2 つのエンドポイントが次の位置で分離されている必要があることを意味します。`root`。 

グループは、現在のルートに関係するすべての制約がチェックされた後にのみ構築されます。 各グループは、「一緒に留まらなければならない」関係によって内部的に接続されることが保証されており、再帰的に構築されたルートを現在のルートの直下に配置すると、そのグループに対して 1 つの子サブツリーが作成されます。 

最終的な検証では、祖先セットを使用して各 LCA を計算します。`O(N)`。 Python には整数オーバーフローの問題はなく、再帰の深さは最大でも`N`このコードは全体で 1 から始まる頂点番号を使用し、出力境界でのみ最終の親配列を 0 から始まる Python リストに変換します。 

## 実用的な例

 ### サンプル 1

 入力は```
3 1
1 2 3
```要件が 1 つあります。`LCA(1, 2) = 3`。 

| 現在のセット | 強制入力エッジ | 選択されたルート | グループ化後の DSU コンポーネント | 分離チェック |
 | --- | --- | --- | --- | --- |
 |`{1,2,3}`|`3 -> 1`、`3 -> 2`|`3`|`{1}`、`{2}`|`1`そして`2`違います |

 強制入力エッジがない唯一の頂点は、`3`, つまりルートになります。 要件があるため、`z = 3`、エンドポイント`1`そして`2`異なる子コンポーネント内に存在する必要があります。 彼らはすでに別居しているので、両方とも子供になります`3`。 

結果の親配列は次のようになります。`3 3 0`、サンプル出力と一致します。 

### サンプル 2

 入力は```
3 3
1 2 2
2 3 3
3 1 1
```強制された祖先関係は次のとおりです。 

| 要件 | 強制的な関係 |
 | --- | --- |
 |`LCA(1,2)=2`|`2 -> 1`|
 |`LCA(2,3)=3`|`3 -> 2`|
 |`LCA(3,1)=1`|`1 -> 3`|

 ルート検索では、すべての頂点に強制リレーションが入っていることがわかります。 

| 頂点 | 強制祖先を入力 |
 | --- | --- |
 |`1`|`2`、`1`|
 |`2`|`3`|
 |`3`|`1`|

 可能なルートがないため、構築はすぐに戻ります`Impossible`。 

このトレースは、ローカル LCA 方程式だけをチェックするだけでは不十分である理由を示しています。 これらの方程式は集合的に、根のあるツリーでは表現できない循環的な祖先関係を課します。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |`O(NM + N^2 α(N))`| せいぜい`N`再帰呼び出しはスキャンします`M`制約があり、すべての再帰レベルにわたる DSU 作業は次の制限を受けます。`O(N^2 α(N))`。 最終検証では次のことが追加されます`O(MN)`。 |
 | スペース |`O(N^2)`| 再帰呼び出しと一時的な頂点セットは、`O(N^2)`最悪の場合、制約リストと親配列はスペースを使用します。`O(M + N)`。 |

 と`N <= 60`そして`M <= 120`、単純な二次因数でさえ小さいです。 このアルゴリズムは、最悪の構造ケースでは、テスト ケースごとに数十万のプリミティブ操作のみを実行します。これは、ルート付きツリーの指数関数的な列挙に必要な量をはるかに下回ります。 

## テストケース

 サンプルには場合によっては複数の有効な出力があるため、完全な出力文字列を比較するアサートは不必要に厳密になります。 次のテスト ハーネスは、報告されたすべてのツリーが有効であり、不可能な 2 つのサンプル ケースが実際には拒否されることを主張します。```python
import sys
import io

class DSU:
    def __init__(self, nodes):
        self.parent = {v: v for v in nodes}
        self.size = {v: 1 for v in nodes}

    def find(self, x):
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])
        return self.parent[x]

    def union(self, a, b):
        a = self.find(a)
        b = self.find(b)
        if a == b:
            return
        if self.size[a] < self.size[b]:
            a, b = b, a
        self.parent[b] = a
        self.size[a] += self.size[b]

def solve_case(n, constraints):
    parent = [-1] * (n + 1)

    def build(nodes):
        if len(nodes) == 1:
            return nodes[0]

        inside = set(nodes)

        for x, y, z in constraints:
            if z in inside and (x not in inside or y not in inside):
                return -1

        incoming = {v: False for v in nodes}

        for x, y, z in constraints:
            if z not in inside:
                continue
            if x in inside and x != z:
                incoming[x] = True
            if y in inside and y != z:
                incoming[y] = True

        root = -1
        for v in nodes:
            if not incoming[v]:
                root = v
                break

        if root == -1:
            return -1

        dsu = DSU(nodes)

        for x, y, z in constraints:
            if z in inside and z != root:
                dsu.union(x, y)
                dsu.union(x, z)

        for x, y, z in constraints:
            if z == root and x != root and y != root:
                if dsu.find(x) == dsu.find(y):
                    return -1

        groups = {}
        for v in nodes:
            if v == root:
                continue
            r = dsu.find(v)
            groups.setdefault(r, []).append(v)

        for component in groups.values():
            child_root = build(component)
            if child_root == -1:
                return -1
            parent[child_root] = root

        return root

    root = build(list(range(1, n + 1)))
    if root == -1:
        return None

    parent[root] = 0
    return parent[1:]

def lca(parent, a, b):
    seen = set()

    while a != 0:
        seen.add(a)
        a = parent[a]

    while b != 0:
        if b in seen:
            return b
        b = parent[b]

    return 0

def valid_answer(n, constraints, answer):
    if answer is None:
        return False

    if len(answer) != n:
        return False

    if answer.count(0) != 1:
        return False

    for i, p in enumerate(answer, 1):
        if p < 0 or p > n or p == i:
            return False

    for v in range(1, n + 1):
        seen = set()
        u = v
        while u != 0:
            if u in seen:
                return False
            seen.add(u)
            u = answer[u - 1]

    for x, y, z in constraints:
        if lca(answer, x, y) != z:
            return False

    return True

def run(inp: str) -> str:
    data = io.StringIO(inp)
    t = int(data.readline())
    outputs = []

    for case_id in range(1, t + 1):
        n, m = map(int, data.readline().split())
        constraints = [
            tuple(map(int, data.readline().split()))
            for _ in range(m)
        ]

        answer = solve_case(n, constraints)

        if answer is None:
            outputs.append(f"Case #{case_id}: Impossible")
        else:
            outputs.append(
                f"Case #{case_id}: " +
                " ".join(map(str, answer))
            )

    return "\n".join(outputs)

def parse_outputs(out):
    return out.strip().splitlines()

def check_case(line, case_id, n, constraints, must_be_impossible=False):
    prefix = f"Case #{case_id}: "
    assert line.startswith(prefix), line

    body = line[len(prefix):]

    if must_be_impossible:
        assert body == "Impossible", line
        return

    assert body != "Impossible", line
    answer = list(map(int, body.split()))
    assert valid_answer(n, constraints, answer), line

# Provided samples
sample = """6
3 1
1 2 3
3 3
1 2 2
2 3 3
3 1 1
4 2
2 1 2
1 4 3
6 3
2 4 3
6 5 4
1 2 6
7 4
7 3 5
4 1 2
6 3 6
6 4 5
12 9
1 5 7
11 2 6
9 4 12
8 12 6
10 1 7
4 3 12
3 10 6
8 11 8
2 5 10
"""

out = parse_outputs(run(sample))
assert len(out) == 6

check_case(out[0], 1, 3, [(1, 2, 3)])
check_case(
    out[1], 2, 3,
    [(1, 2, 2), (2, 3, 3), (3, 1, 1)],
    must_be_impossible=True
)
check_case(
    out[2], 3, 4,
    [(2, 1, 2), (1, 4, 3)]
)
check_case(
    out[3], 4, 6,
    [(2, 4, 3), (6, 5, 4), (1, 2, 6)],
    must_be_impossible=True
)
check_case(
    out[4], 5, 7,
    [(7, 3, 5), (4, 1, 2), (6, 3, 6), (6, 4, 5)]
)
check_case(
    out[5], 6, 12,
    [
        (1, 5, 7), (11, 2, 6), (9, 4, 12),
        (8, 12, 6), (10, 1, 7), (4, 3, 12),
        (3, 10, 6), (8, 11, 8), (2, 5, 10)
    ]
)

# Minimum-size input.
minimum = """1
2 1
1 2 1
"""
out = parse_outputs(run(minimum))
check_case(out[0], 1, 2, [(1, 2, 1)])

# All requirements use the same LCA.
all_equal = """4
5 4
1 2 5
1 3 5
1 4 5
2 3 5
"""
out = parse_outputs(run(all_equal))
check_case(
    out[0], 1, 5,
    [(1, 2, 5), (1, 3, 5), (1, 4, 5), (2, 3, 5)]
)

# Maximum-size instance, with 60 vertices and 120 consistent constraints.
# Vertex 60 is the root and every other vertex can be its direct child.
constraints = []
for i in range(120):
    x = 1 + (i % 59)
    y = 1 + ((i + 1) % 59)
    if x == y:
        y = 59
    constraints.append((x, y, 60))

maximum = "1\n60 120\n"
maximum += "\n".join(f"{x} {y} {z}" for x, y, z in constraints)
maximum += "\n"

out = parse_outputs(run(maximum))
check_case(out[0], 1, 60, constraints)

# Contradictory ancestor cycle.
cycle = """1
3 3
1 2 2
2 3 3
3 1 1
"""
out = parse_outputs(run(cycle))
check_case(
    out[0], 1, 3,
    [(1, 2, 2), (2, 3, 3), (3, 1, 1)],
    must_be_impossible=True
)

print("All tests passed.")
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`2 1 / 1 2 1`| 任意の有効なツリー | 最小`N`LCA が 1 つのクエリされた頂点と等しい場合 |
 |`5 4 / ... 5`| 任意の有効なツリー | 同じ LCA を共有する複数の要件と複数のペアの分離 |
 |`60 120 / ... 60`| 任意の有効なツリー | 最大`N`、最大`M`、境界スケールの DSU 操作を繰り返しました。 
|`3 3 / 1 2 2 / 2 3 3 / 3 1 1`|`Impossible`| 循環的な強制祖先関係 |

 ## 特殊なケース

 エンドポイント LCA の場合```
2 1
1 2 1
```強制的な関係は`1 -> 2`、頂点の間`1`それ自体には入力強制エッジがありません。 アルゴリズムが選択する`1`根として。 要件があるため、`z = root`、必要ありません`1`そして`2`別れる事に。 シングルトンコンポーネント`{2}`の子供になります`1`、親配列を与える`0 1`。 のLCA`1`そして`2`まさに`1`。 

ルート選択の場合```
3 1
1 2 3
```強制的な関係は`3 -> 1`そして`3 -> 2`。 頂点`1`そして`2`どちらも新たな関係を持っていますが、`3`そうでないため、アルゴリズムは`3`。 なぜなら`z`根に等しい、`1`そして`2`異なるコンポーネントを占有する必要があります。 彼らはそうして木を生み出します`3 3 0`。 

周期的なケースの場合```
3 3
1 2 2
2 3 3
3 1 1
```強制的な関係は`2 -> 1`、`3 -> 2`、 そして`1 -> 3`。 すべての頂点には受信リレーションがあるため、DSU パーティションが試行される前にルート検索は失敗します。 戻る`Impossible`これは正しいです。すべてのルート ツリーには、頂点セット全体の中に祖先を持たない頂点が少なくとも 1 つあるからです。 

微妙な分離のケースは、```
3 2
1 2 3
1 3 1
```最初の要件により強制されるのは、`3`その上`1`そして`2`。 第二勢力`1`その上`3`。 したがって、祖先関係にはすでに以下が含まれています`1 -> 3 -> 1`。 ルート選択中は、どちらも`1`または`3`は有効な最小ルートとして選択でき、構築ではインスタンスが拒否されます。 

もう 1 つの有用なケースは、一連の独立した要件です。```
5 1
1 2 3
```頂点`4`そして`5`どの要件でも決して発生しません。 すべての DSU コンポーネントは再帰的にサブツリーに変換されるため、アルゴリズムは依然としてそれらをツリー内のどこかに配置します。 それらは単に追加のブランチになる可能性があります。 それらの位置は必要な LCA に影響を与えないため、拘束されていない頂点は特別な処理を必要としません。
