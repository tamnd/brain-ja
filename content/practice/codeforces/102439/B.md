---
title: "CF 102439B - バルバラとマトリックス"
description: "(0) から (k) までの整数をエントリとする (n 倍 m) 行列があります。 ゼロは不明なセルを示します。特別な条件は、すべての行とすべての列に含まれるゼロが最大 1 つであることです。 すべてのゼロを独立して (A) または (B) のいずれかに置き換える必要があります。"
date: "2026-08-10T06:41:49+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102439
codeforces_index: "B"
codeforces_contest_name: "2018-2019 9th BSUIR Open Programming Championship. Semifinal"
rating: 0
weight: 102439
solve_time_s: 364
verified: true
draft: false
---

[CF 102439B - バルバラとマトリックス](https://codeforces.com/problemset/problem/102439/B)

 **評価:** -
 **タグ:** -
 **解決時間:** 6 分 4 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 (n \times m) 行列があり、そのエントリは (0) から (k) までの整数です。 ゼロは不明なセルを示します。特別な条件は、すべての行とすべての列に含まれるゼロが最大 1 つであることです。 すべてのゼロを独立して (A) または (B) のいずれかに置き換える必要があります。 

マトリックスの美しさは、4 つの交差セルがすべて同じ値を含む行のペアと列のペアを数えることです。 美しい長方形の実際のセットを保存するように求められるのではなく、その合計数だけを保存するように求められます。 重要な結果は、行ごとに 1 つのゼロと列ごとに 1 つのゼロの条件下では 4 つのゼロの角は不可能であるため、ゼロを含むすべての四角形は置換前は美しくないということです。 したがって、考えられる唯一の変化は、以前は美しくなかった長方形が、ゼロが埋められた後に美しくなるということです。 タスクは、まさにそのような新しい四角形をすべて防ぐことです。 

次元は最大 (1000) であるため、行列全体をスキャンするか、セルごとに一定量の作業を実行するのが実用的です。 対照的に、行のすべてのペアと列のすべてのペアを列挙すると、すでに次のようになります。 

249,500,250,000
 ]

 最大の正方形のケースに長方形。 これにより、すべての四角形を検査するアルゴリズムが即座に排除されます。 ゼロは行と列の一致を形成するため、ゼロの数も最大 (1000) です。 この少数の未知のセルにより、ブール制約の定式化が可能になります。 

値 (k) は (nm) ほど大きくても構いませんが、その大きさはアルゴリズムに影響しません。 固定セルが (A) に等しいか、(B) に等しいか、あるいはどちらにも等しいかどうかのみを気にします。 

直接的なアプローチでは誤って処理される可能性があるエッジケースがいくつかあります。 まず、単一のゼロが 2 つの値のうちの 1 つを取ることを禁止することができます。 例えば、```
2 2
2
1 2
0 1
1 1
```ゼロが (1) になることはありません。そうすると、すべて (1) の長方形が作成されてしまうからです。 (2) になる可能性があるので、正しい結果は次のようになります。`Yes`、たとえば```
2 1
1 1
```すべてのゼロを自由に割り当て可能として扱う不注意なアルゴリズムにより、誤って四角形が作成される可能性があります。 

より微妙なケースは、1 つのゼロが両方の値を取ることを禁止されている場合です。 考慮する```
3 3
2
1 2
1 1 1
1 0 2
1 2 2
```中心が(1)になると、左上(2\times2)の長方形はすべて(1)になります。 (2) になると、右下の (2\times2) 長方形はすべて (2) になります。 代替品は存在しないので、答えは次のとおりです。`No`。 2 つの制限は異なる四角形に由来するため、組み合わせる必要があります。 

別の特殊なケースには 2 つのゼロが含まれます。 考慮する```
2 2
2
1 2
0 1
1 0
```2 つのゼロは反対側の角で、他の 2 つの角は (1) です。 両方を (1) で置き換えることはできません。そうすると、すべて (1) の四角形が作成されてしまいます。 ただし、異なる値を受け取る可能性があるため、答えは次のようになります。`Yes`。 ゼロが 1 つ含まれる制約のみを処理すると、この条件が見逃されます。 

これらは、以下で使用する 2-SAT 定式化によって捉えられる制限のタイプとまったく同じです。 元のステートメントと制約は、Codeforces Gym の公式アーカイブで入手できます。 

## アプローチ

 直接的な総当たりの解決策では、すべての (2^z) 個の置換 (z) はゼロの数を試行し、すべての置換後の美しさを計算します。 割り当てごとに、すべての四角形を列挙するには次の時間がかかります

 [
 O(n^2m^2)
 ]

 時間。 最悪のケース (n=m=1000) では、ちょうど (249,500,250,000) 個の長方形が存在するため、総当たりの作業は次のようになります。 

[
 249,500,250,000 \cdot 2^{1000}
 ]

 長方形のチェック。 すべての四角形を一度チェックするだけでも、すでに限界をはるかに超えています。 

次の観察は、実際には美しさを計算する必要がないということです。 以前は美しかったすべての長方形にはゼロが含まれておらず、その 4 つの値は変更されません。 したがって、元の美しい長方形が自動的に保存されます。 ゼロコーナーを置き換えると美しくなる長方形を禁止すればよいだけです。 

すべての行と列には最大 1 つのゼロが含まれるため、長方形には最大 2 つのゼロが含まれます。 ゼロが 1 つだけ含まれている場合、他の 3 つの角は固定されます。 ゼロは共通の値を受け取ってはなりません。 2 つのゼロが含まれる場合、それらは反対側の角を占める必要があり、他の 2 つの角は固定されます。 これら 2 つの固定角が両方とも (A) に等しい場合、2 つのゼロは両方とも (A) を受け取ることはできません。 (B)についても同様です。 

これにより与えられる論理制限は 2 種類のみです。 ゼロが (A) または (B) を取ることを禁止したり、2 つのゼロが同時に (A) または (B) を取ることを禁止したりすることができます。 どちらも最大 2 つのブール変数を含む節であるため、問題全体は 2-SAT になります。 

残りの困難は、すべての 1 と 0 の制限を迅速に見つけることです。 ((x,y)) にゼロがある場合、それに (A) を割り当てるとすべて (A) の長方形が作成されるかどうかを知りたいとします。 を満たす別の行 (r) と別の列 (c) が必要です。 

[
 a_{x,c}=A,\qquad
 a_{r,c}=A,\qquad
 a_{r,y}=A.
 ]

 すべての行 (x) について、行 (x) にも (A) がある列のどこかに (A) を持つすべての行 (r) を含むビットセットを構築します。 次に、それを列 (y) に (A) を持つ行のビットセットと交差させます。 空でない交差は、必要な四角形が存在することを意味します。 

(B) についても同様の構築が行われます。 これは、この問題に対する標準的なソリューションで使用されるビットセットの最適化です。 

ゼロが 2 つの長方形の場合、ゼロの数は最大 (1000) 個であるため、すべてのペアを直接チェックするコストは (O(z^2)) のみです。 次に、結果として得られる強結合コンポーネントを使用して 2-SAT インスタンスを解決します。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | (O(2^z n^2m^2)) | (O(nm)) | 遅すぎる |
 | 長方形の列挙 | (O(n^2m^2)) | (O(nm)) | 遅すぎる |
 | 最適 | (O(nm\lceil n/w\rceil + z^2 + E)) | (O(nm + E)) | 承認済み |

 ここで、(z\le\min(n,m))、(w) はビットセット表現で使用されるマシンワードのサイズ、(E=O(zn+z^2)) は含意エッジの数です。 (n,m\le1000) の場合、ビットセット操作は、意図したアプローチ内に快適に収まるほど十分に小さいです。 

## アルゴリズムのチュートリアル

 1. マトリックスを読み取り、すべてのゼロをブール変数として記録します。 変数 (i) について、次のようにします。`A_i`は、そのゼロが (A) に置き換えられることを意味し、`B_i`(B)に置き換えられることを意味します。 各ゼロは正確に 1 つの値を受け取る必要があるため、`A_i`そして`B_i`は補完的なリテラルです。 
2. (A) と (B) を含む位置のビットセットを構築します。 すべての行について、(A) を含む列を格納し、(B) を含む列を個別に格納します。 すべての列について、(A) を含む行を保存し、(B) を含む行を個別に保存します。 
3. すべての行 (x) について、次のように構築します。`coverA[x]`。 その設定ビットは、(a_{x,c}=A) と (a_{r,c}=A) の両方を持つ列 (c) が存在する行 (r) です。 構築する`coverB`同じように。 

これにより、行と列の考えられるすべてのペアをスキャンするのではなく、1 から 0 の四角形の検索が 1 つのビットセットの交差に変わります。 
4. ((x,y)) のすべてのゼロについて、`colA[y] & coverA[x]`空ではない。 そうであれば、長方形の他の 3 つの角がすべて (A) になるような行 (r) と列 (c) が存在します。 したがって、このゼロを割り当てることはできません (A)。そのため、次の含意を追加します。 

[
 A_i \右矢印 B_i。 
]

 (B) についても同様のチェックを実行します。 成功したら追加します

 [
 B_i \rightarrow A_i。 
]
 5. 個別のゼロのすべてのペア (i=(x,y)) および (j=(r,c)) を考慮します。 すべての行と列には最大でも 1 つのゼロが含まれるため、それらの行と列は自動的に異なります。 両方のゼロを含む唯一の長方形は、反対側の角にゼロを持っています。 

(a_{x,c}=A) および (a_{r,y}=A) の場合、両方のゼロに (A) を代入すると、新しい美しい長方形が作成されます。 したがって、この条項は

 [
 \neg(A_i\land A_j),
 ]

 これは2つの意味になります

 [
 A_i\右矢印 B_j,
 \qquad
 A_j\右矢印 B_i。 
]

 2 つの固定コーナーが両方とも (B) の場合、対称制限を追加します。 

[
 B_i\右矢印 A_j,
 \qquad
 B_j\右矢印 A_i。 
]
 6. 含意グラフは、新しい美しい長方形を作成するすべての割り当てを表します。 これに対して強接続コンポーネント アルゴリズムを実行します。 変数とその補数が同じ強連結成分に属している場合、2-SAT インスタンスは正確には不可能です。 
7. ゼロがある場合`A_i`そして`B_i`同じコンポーネント内で印刷します`No`。 それ以外の場合は、コンポーネントの順序に従って値を選択し、すべてのゼロを置き換えて、結果の行列を出力します。 

### なぜ効果があるのか

 置き換える前は美しかったすべての長方形にはゼロが含まれていないため、その四隅は変化しません。 したがって、美しさは古い長方形が変化する場合にのみ減少する可能性がありますが、そのようなことは起こり得ず、美しさは 1 つまたは 2 つのゼロを含む長方形が単色になる場合にのみ増加します。 

行または列に 2 つのゼロが含まれていないため、長方形には最大 2 つのゼロが含まれます。 1 つのゼロがあると、他の 3 つの角は固定されるため、唯一の制限は、ゼロが共通の値を取ることができないことです。 2 つのゼロの場合、それらは反対側の角であり、他の 2 つの角は固定されているため、これらの固定角が (A) または (B) と一致する場合にのみ制限が発生し、両方のゼロがその値を取ることが禁止されます。 

このアルゴリズムはまさにこれらの制限を作成し、他の制限は作成しません。 グラフ内のすべての含意は、新しい美しい長方形を回避するための必要な条件を表しており、考えられるすべての新しい美しい長方形は、これらの含意の 1 つを生成します。 したがって、2-SAT インスタンスの満足のいく割り当ては、すべてのゼロの有効な置換に正確に対応します。 SCC テストは、2-SAT の標準満足基準です。 

## Python ソリューション```python
import sys
from array import array

input = sys.stdin.readline
sys.setrecursionlimit(1_000_000)

def solve():
    n, m = map(int, input().split())
    k = int(input())
    A, B = map(int, input().split())

    matrix = []
    row_a = [0] * n
    row_b = [0] * n
    col_a = [0] * m
    col_b = [0] * m
    zeros = []

    for i in range(n):
        row = list(map(int, input().split()))
        matrix.append(array('I', row))

        ma = 0
        mb = 0

        for j, value in enumerate(row):
            if value == 0:
                zeros.append((i, j))
            elif value == A:
                ma |= 1 << j
                col_a[j] |= 1 << i
            elif value == B:
                mb |= 1 << j
                col_b[j] |= 1 << i

        row_a[i] = ma
        row_b[i] = mb

    z = len(zeros)

    if z == 0:
        out = ["Yes"]
        out.extend(" ".join(map(str, row)) for row in matrix)
        sys.stdout.write("\n".join(out))
        return

    # cover_a[x] contains every row r that shares an A-column
    # with row x. cover_b is analogous.
    cover_a = [0] * n
    cover_b = [0] * n

    for i in range(n):
        bits = row_a[i]
        cur = 0
        while bits:
            low = bits & -bits
            c = low.bit_length() - 1
            cur |= col_a[c]
            bits -= low
        cover_a[i] = cur

        bits = row_b[i]
        cur = 0
        while bits:
            low = bits & -bits
            c = low.bit_length() - 1
            cur |= col_b[c]
            bits -= low
        cover_b[i] = cur

    nodes = 2 * z

    # Forward-star representation of the implication graph.
    head = [-1] * nodes
    to = array('i')
    nxt = array('i')

    def add_edge(u, v):
        e = len(to)
        to.append(v)
        nxt.append(head[u])
        head[u] = e

    # Literal encoding:
    # 2*i     = zero i is assigned A
    # 2*i + 1 = zero i is assigned B
    #
    # The complement of a literal is literal ^ 1.

    # Restrictions involving exactly one zero.
    for i, (x, y) in enumerate(zeros):
        a_lit = 2 * i
        b_lit = a_lit + 1

        if col_a[y] & cover_a[x]:
            # A_i is forbidden: A_i -> B_i
            add_edge(a_lit, b_lit)

        if col_b[y] & cover_b[x]:
            # B_i is forbidden: B_i -> A_i
            add_edge(b_lit, a_lit)

    # Restrictions involving two zeros.
    for i in range(z):
        x, y = zeros[i]
        ai = 2 * i
        bi = ai + 1

        for j in range(i + 1, z):
            r, c = zeros[j]
            aj = 2 * j
            bj = aj + 1

            if matrix[x][c] == A and matrix[r][y] == A:
                # Not (A_i and A_j).
                add_edge(ai, bj)
                add_edge(aj, bi)

            if matrix[x][c] == B and matrix[r][y] == B:
                # Not (B_i and B_j).
                add_edge(bi, aj)
                add_edge(bj, ai)

    # Tarjan SCC.
    index = [-1] * nodes
    low = [0] * nodes
    on_stack = [False] * nodes
    stack = []
    component = [-1] * nodes
    timer = 0
    comp_id = 0

    def dfs(v):
        nonlocal timer, comp_id

        index[v] = timer
        low[v] = timer
        timer += 1

        stack.append(v)
        on_stack[v] = True

        e = head[v]
        while e != -1:
            w = to[e]

            if index[w] == -1:
                dfs(w)
                if low[w] < low[v]:
                    low[v] = low[w]
            elif on_stack[w] and index[w] < low[v]:
                low[v] = index[w]

            e = nxt[e]

        if low[v] == index[v]:
            while True:
                w = stack.pop()
                on_stack[w] = False
                component[w] = comp_id
                if w == v:
                    break
            comp_id += 1

    for v in range(nodes):
        if index[v] == -1:
            dfs(v)

    # A variable and its complement in the same SCC means
    # that the 2-SAT instance is unsatisfiable.
    for i in range(z):
        if component[2 * i] == component[2 * i + 1]:
            sys.stdout.write("No\n")
            return

    # Tarjan numbers SCCs in reverse topological order of the
    # condensation graph, so the larger component id is chosen.
    for i, (x, y) in enumerate(zeros):
        if component[2 * i] > component[2 * i + 1]:
            matrix[x][y] = A
        else:
            matrix[x][y] = B

    out = ["Yes"]
    out.extend(" ".join(map(str, row)) for row in matrix)
    sys.stdout.write("\n".join(out))

if __name__ == "__main__":
    solve()
```入力フェーズでは、通常のネストされた Python 整数リストではなく、コンパクトな整数配列に行列が格納されます。 これが重要なのは、Python 整数の (1000\times1000) Python リストには、行列に必要な実際の (4) MB の 32 ビット値よりもかなり多くのオーバーヘッドがあるためです。`row_a`そして`row_b`整数ビットセットで、そのビット (j) は列 (j) にその行の対応する値が含まれているかどうかを示します。`col_a`そして`col_b`同じアイデアを別の方向にも使用します。 Python の整数を使用すると、これらのビットセットが特に便利になります。`&`、`|`、ビット シフトはネイティブ コードで一度に多くのビットを操作します。 

の`cover_a`建設は注目に値します。 行 (x) の列 (c_1,c_2,\ldots) に (A) が含まれているとします。 このような列ごとに、`col_a[c]`その列に (A) を含むすべての行が含まれます。 これらの和集合を取得すると、行 (x) と少なくとも 1 つの (A) 列を共有するすべての行が得られます。 これを交差させると`col_a[y]`次に、そのような行の列 (y) にも (A) があり、ゼロ ((x,y)) の周りの長方形に必要な 3 つの固定角に正確に一致するかどうかを尋ねます。 

含意グラフでは、ゼロごとに 2 つのノードが使用されます。 ノード`2*i`(A) を選択することを表し、ノード`2*i+1`(B)を選択することを表します。 禁止された値は、反対の値に対する一方向の暗示となります。 2 ゼロの禁止は、2 リテラル条項の標準的な暗黙のペアになります。 

ペアループは使用します`i + 1`すべての順序ペアではなく。 条件は対称であるため、両方の順序をチェックしても、同じ句が重複するだけになります。 マトリックスアクセス`matrix[x][c]`そして`matrix[r][y]`ゼロは異なる行と異なる列にあるため安全です。 

Tarjan のアルゴリズムは、2 番目の反転グラフの保存を回避します。 理論上の最悪のケースでは最大約 400 万の含意エッジがあるため、これは Python での有用なメモリ最適化です。 再帰の深さは (2z\le2000) グラフの頂点によって制限され、再帰の制限はその境界をはるかに超えて引き上げられます。 

最終的な割り当てでは、SCC コンポーネントの順序付けが使用されます。 変数の 2 つのリテラルが異なるコンポーネントを持つ場合、通常の 2-SAT トポロジー順序に従って 1 つだけを選択できます。 すべてのグラフのインデックスと行列の値は最大でも入力次元の多項式であるため、整数のオーバーフローは発生しません。 

## 実用的な例

 ### サンプル 1

 入力には 3 つのゼロがあります。 

[
 z_0=(1,3)、\qquad z_1=(2,1)、\qquad z_2=(4,4)、
 ]

 1 ベースの座標を使用します。 ここでは (A=3) と (B=5) です。 

| ステップ | ゼロ | 単一ゼロの制限 | ペア制限 | 結果として得られる選択肢 |
 | --- | --- | --- | --- | --- |
 | 1 | (z_0=(1,3)) | なし | (z_0,z_2) を両方とも (3) にすることはできません。 (5) |
 | 2 | (z_1=(2,1)) | なし | 他のゼロについては制限なし | (5) |
 | 3 | (z_2=(4,4)) | なし | (z_0,z_2) を両方とも (3) にすることはできません。 (3) |
 | 4 | すべての変数 | SCC は一貫しています | 補数と競合する変数はありません |`Yes`|

 唯一の関連する新しい四角形は、最初と 4 番目のゼロと 2 つの固定 (3) によって形成されます。 したがって、これら 2 つのゼロが両方とも (3) になることはありません。 割り当て (z_0=5、z_1=5、z_2=3) は制限を満たし、サンプルの有効な構造と一致します。 

### サンプル 2

 ここでは (A=1) と (B=2)、((2,2)) と ((3,3)) にゼロがあります。 

| ステップ | ゼロ | (A) 制限 | (B) 制限 | 結果 |
 | --- | --- | --- | --- | --- |
 | 1 | ((2,2)) | すべて (1) の長方形によって禁止されています | 許可 | (2) | である必要があります。 
| 2 | ((3,3)) | 対応する制約によって禁止されています | 別の四角形によって制限されています | 強制的な競合 |
 | 3 | 両方の変数 | 影響は伝播する | (A_i) と (B_i) が互いに到達します | 同じ SCC |
 | 4 | 式 | 変数はその補数に等しい | 満足できない |`No`|

 最初のゼロは、他の 3 つの角が (1) である長方形からすでに制限を受けています。 2 番目のゼロは追加の含意に寄与し、結果として得られる含意グラフでは、変数の考えられる両方のリテラルが同じ強連結成分に入れられます。 2-SAT インスタンスは満足のいくものではないため、その美しさを維持できる代替手段はありません。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O(nm\lceil n/w\rceil + z^2 + E)) | ビットセット構造とすべてのゼロ ペアおよび SCC エッジ |
 | スペース | (O(nm + E)) | 行列、ビットセット、および含意グラフ |
 | 変数の数 | (z\le\min(n,m)) | 行と列ごとに最大 1 つのゼロ |
 | グラフのエッジの数 | (E=O(zn+z^2)) | 1 つのゼロと 2 つのゼロの制限 |

 (n,m\le1000) の場合、ブール変数は最大で (1000) 個あります。 ビットセットの作業は一度に最大 (1000) ビットを操作しますが、ペア列挙には最大約 (500,000) 個のゼロ ペアが含まれます。 SCC グラフには頂点が (2000) しかありません。 これは、(1000\times1000) 行列内でおよそ (2.5\times10^{11}) 個の可能な長方形を列挙することとは根本的に異なります。 

標準の C++ 定式化では、正方形サイズの次元のビットセット部分を (O(n^3/w)) として記述します。これは、ここで使用されているのと同じワード並列の考え方です。 

## テストケース

 次のテスト ハーネスは同じように動作します`solve()`関数、検証`Yes`小さな行列の美しさを再計算して答え、チェックします`No`これは直接答え、(O(n^2m^2)) の美しさの計算を試行せずに (1000\times1000) の境界ケースを含みます。```python
import sys
import io
from array import array

# Paste the solve() implementation from the solution above here.

def run(inp: str) -> str:
    old_stdin = sys.stdin
    old_stdout = sys.stdout
    try:
        sys.stdin = io.StringIO(inp)
        sys.stdout = io.StringIO()
        solve()
        return sys.stdout.getvalue()
    finally:
        sys.stdin = old_stdin
        sys.stdout = old_stdout

def parse_input(inp: str):
    lines = inp.strip().splitlines()
    n, m = map(int, lines[0].split())
    k = int(lines[1])
    A, B = map(int, lines[2].split())
    mat = [list(map(int, lines[3 + i].split())) for i in range(n)]
    return n, m, A, B, mat

def beauty(mat):
    n = len(mat)
    m = len(mat[0])
    ans = 0

    for r1 in range(n):
        for r2 in range(r1 + 1, n):
            for c1 in range(m):
                x = mat[r1][c1]
                for c2 in range(c1 + 1, m):
                    if (
                        x != 0
                        and x == mat[r1][c2]
                        and x == mat[r2][c1]
                        and x == mat[r2][c2]
                    ):
                        ans += 1

    return ans

def validate_yes(inp, out):
    n, m, A, B, original = parse_input(inp)
    lines = out.strip().splitlines()

    assert lines[0] == "Yes"
    assert len(lines) == n + 1

    result = [list(map(int, lines[i + 1].split())) for i in range(n)]

    assert all(len(row) == m for row in result)

    for i in range(n):
        for j in range(m):
            if original[i][j] == 0:
                assert result[i][j] in (A, B)
            else:
                assert result[i][j] == original[i][j]

    assert beauty(original) == beauty(result)

def validate_no(inp, out):
    assert out.strip() == "No"

# Provided sample 1.
sample1 = """\
4 4
5
3 5
1 1 0 3
0 5 4 5
1 1 4 4
2 5 3 0
"""

out = run(sample1)
validate_yes(sample1, out)

# Provided sample 2.
sample2 = """\
4 4
4
1 2
1 1 3 3
1 0 2 3
1 2 0 3
1 3 1 3
"""

out = run(sample2)
validate_no(sample2, out)

# Custom 1: minimum-size matrix, all equal, no zeros.
case1 = """\
2 2
2
1 2
1 1
1 1
"""

out = run(case1)
validate_yes(case1, out)

# Custom 2: two opposite zeros. They cannot both become A,
# but assigning different values is valid.
case2 = """\
2 2
2
1 2
0 1
1 0
"""

out = run(case2)
validate_yes(case2, out)

# Custom 3: one zero is forbidden from both A and B.
case3 = """\
3 3
2
1 2
1 1 1
1 0 2
1 2 2
"""

out = run(case3)
validate_no(case3, out)

# Custom 4: maximum-size boundary case.
# There are no zeros, so the matrix must simply remain unchanged.
n = 1000
row = "7 " * 999 + "7"
case4 = f"{n} {n}\n1000\n1 2\n" + "\n".join([row] * n) + "\n"

out = run(case4)
lines = out.strip().splitlines()

assert lines[0] == "Yes"
assert len(lines) == n + 1
assert lines[1] == row
assert lines[-1] == row
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`2 x 2`、すべてのエントリは等しい |`Yes`| 最小寸法とゼロ以外の場合 |
 |`2 x 2`反対のゼロを持つ |`Yes`| 2 つのゼロ句 |
 |`3 x 3`ゼロが 1 つ |`No`| 単一の変数は両方の値を禁止 |
 |`1000 x 1000`、ゼロはありません |`Yes`| 行列の最大次元と境界メモリ使用量 |
 | 提供サンプル 1 |`Yes`| 通常の充足可能なインスタンス |
 | 提供サンプル 2 |`No`| SCCの矛盾 |

 ## 特殊なケース

 1 ゼロの場合```
2 2
2
1 2
0 1
1 1
```((1,1)) にゼロがあります。 その行には列 (2) に (1) が含まれ、その列には行 (2) に (1) が含まれます。 残りの角も (1) なので、(1) にゼロを代入すると、新しい美しい長方形が作成されます。 アルゴリズムが検出するのは、`col_a[y] & cover_a[x]`空でないものとして、(A_i\rightarrow B_i) を追加します。 (B) には対応する制限がないため、SCC ソルバーは (B=2) を選択します。 出力は次のとおりです```
Yes
2 1
1 1
```ゼロが 2 つある場合```
2 2
2
1 2
0 1
1 0
```対角にゼロがあります。 両方が (1) になった場合、4 隅すべてが (1) になるため、アルゴリズムは次の値を追加します。 

[
 A_0\右矢印 B_1
 ]

 そして

 [
 A_1\右矢印 B_0。 
]

 代入 (A_0=B)、(A_1=A) は両方の意味を満たしており、次のようになります。```
Yes
2 1
1 2
```ゼロを両方の値から同時に禁止することもできます。```
3 3
2
1 2
1 1 1
1 0 2
1 2 2
```中心のゼロについては、左上の長方形には (1) が 3 つ固定されているため、中心は (1) になることはできません。 右下の長方形は(2)が3つ固定されているので(2)にはなりません。 したがって、グラフには (A_0\rightarrow B_0) と (B_0\rightarrow A_0) の両方の意味が含まれており、2 つのリテラルが同じ SCC に配置されます。 ソルバーは出力します`No`。 

ゼロがない場合、ブール変数も意味もありません。 どの長方形も四隅が全く同じになるので、自動的に美しさが変わりません。 アルゴリズムはすぐに出力します`Yes`そしてオリジナルのマトリックス。 

(A) または (B) が元の行列のどこにも出現しない場合、対応するビットセットは単に空のままになります。 その値にゼロを割り当てることはできますが、その値が存在しない固定コーナーを使用する新しい四角形を作成することはできません。 ビットセットの共通部分では、当然その値に制限は生じません。 

行ごとに 1 つのゼロと列ごとに 1 つのゼロという条件も重要です。 これにより、長方形に最大 2 つのゼロが含まれることが保証されます。 このプロパティがなければ、長方形には 3 つまたは 4 つのゼロが含まれる可能性があり、このソリューションで使用される 2 種類の句は、考えられるすべての新しい美しい長方形をカバーできなくなります。
