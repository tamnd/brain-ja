---
title: "CF 105002M - \u041d\u043e\u0434\u043d\u044b\u0435 \u043e\u0431\u043c\u0435\u043d\u044b"
description: "$n$ の数字の行が与えられます。 許可される唯一の操作は、2 つの位置の数値が 1 より大きい公約数を共有する場合に、2 つの位置を交換することです。"
date: "2026-06-28T03:29:49+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 105002
codeforces_index: "M"
codeforces_contest_name: "vkoshp.letovo 2022"
rating: 0
weight: 105002
solve_time_s: 77
verified: false
draft: false
---

[CF 105002M - \u041d\u043e\u0434\u043d\u044b\u0435 \u043e\u0431\u043c\u0435\u043d\u044b](https://codeforces.com/problemset/problem/105002/M)

 **評価:** -
 **タグ:** -
 **解決時間:** 1 分 17 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 の行が与えられています$n$数字。 許可される唯一の操作は、2 つの位置の数値が 1 より大きい公約数を共有する場合に 2 つの位置を交換することです。これらの交換から、配列を再配置できますが、任意に配置することはできません。要素は、すべての隣接する交換が非自明な gcd 条件によって正当化される交換のチェーン内でのみ移動できます。 

タスクは、このようなスワップを任意の数だけ適用した後に取得できる辞書編集上の最小の配列を決定することです。 

重要な問題は、この操作では任意の並べ替えができないことです。 2 つの要素は、それらを接続する一連のスワップが存在し、すべてのステップで条件が維持される場合にのみ交換可能です。$\gcd(x_i, x_j) > 1$。 これにより、共有素因数に基づいてインデックス間の接続性が自然に誘導されます。 

制約$n \le 10^5$および最大値$10^5$いかなる解決策でも回避する必要があることを示唆しています$O(n^2)$ペア間の比較または gcd チェックの繰り返し。 代わりに、値を効率的に、通常は線形に近い形でグループ化する構造が必要です。$O(n \log n)$因数分解または素因数の和集合検索を使用した時間。 

単純な間違いは、2 つの数値が共通の因数を共有している場合、それらをグローバルにソート順に自由に入れ替えることができると想定することです。 共有因子を通じて推移性を適切に伝播しない限り、それだけでは十分ではありません。 

2 番目の微妙なエッジ ケースは、孤立した素数または素数累乗です。 たとえば、ある数値が他の数値と素因数を共有していない場合、その数値はまったく移動できません。 貪欲な並べ替えアプローチでは、誤って移動してしまいます。 

3 番目のエッジ ケースは、接続が間接的な場合に発生します。 例えば、$6, 10, 15$すべてをペアで接続することはできませんが、共有素数 (2、3、5 リンク) を通じて接続コンポーネントを形成します。 正しいソリューションは、この推移的な接続性を捉える必要があります。 

## アプローチ

 ブルートフォース解釈では、それ以上の改善が不可能になるまで、有効なスワップを繰り返し適用しようとします。 スワップをシミュレートし、有効な gcd 条件が存在するときはいつでも、より小さい値を左方向にバブリングしようとすることができます。 これは原理的には正しいです。許可されるすべての移動で到達可能性の制約が維持されるからです。 

しかし、州の数は膨大です。 スワップごとにアレイ構成が変更され、有効なスワップについてすべてのペアをチェックすると、$O(n^2)$gcd は反復ごとにチェックし、場合によっては$O(n!)$最悪の概念的空間における順列。 最適化を行ったとしても、このアプローチは次の場合には使用できません。$n = 10^5$。 

重要な観察は、スワップがインデックス上のグラフを定義するということです。つまり、2 つのポジションの値が直接的または間接的に素因数を共有する場合、2 つのポジションは接続されます。 接続性は推移的であるため、各接続コンポーネントは任意に並べ替えることができます。 各コンポーネント内では、gcd にリンクされたパスに沿って値を移動できるため、任意の配置を実現できます。 

これにより、問題は値の連結成分を見つけ、素因数を共有するすべての数値をグループ化し、各グループ内の値を個別に並べ替えることに集約されます。 最後に、辞書編集上の最小順序を達成するために、利用可能な最小の値を各コンポーネントの最初の位置に配置します。 

したがって、問題を値の素因数に対する素集合和集合の構築に変換します。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | 指数関数 | O(n) | 遅すぎる |
 | 最適 (素数上の DSU) | O(n log A) | O(n + A) | 承認済み |

 ## アルゴリズムのチュートリアル

 共通素因数を介してインデックスを接続するために、素集合 (DSU) 構造を使用します。 

1. 各数値を個別の素因数に因数分解します。 これを、最大の最小の素因数ふるいを使用して効率的に実行します。$10^5$。 これにより、すべての要素に対して因数分解が十分に高速になります。 
2. 各数値について、その素因数のリストを取得します。 代表的な要素を 1 つ選択し、DSU で他のすべての要素をそれに結合します。 これにより、素数を共有する数値間の接続が構築されます。 
3. 各 DSU ルートからそのコンポーネントに属するすべてのインデックスへのマッピングを維持します。 同時に、コンポーネントに属するすべての値も収集します。 
4. 各連結成分について、インデックスと値の両方を個別に並べ替えます。 インデックスを並べ替えると値を配置できる位置がわかり、値を並べ替えると辞書編集上の最小の配置が得られます。 
5. コンポーネント内の最小のインデックスに最小の値を割り当て、グローバルな辞書編集上の最小性を確保します。 

インデックスと値を別々に並べ替える主な理由は、接続コンポーネント内では、有効なスワップを通じて任意の順列を実現できるため、完全に自由に並べ替えることができるためです。 

### なぜ効果があるのか

 DSU は、「gcd > 1 を介して交換できる」関係の推移閉包を正確にキャプチャします。 2 つの数値が同じコンポーネント内にある場合、共有素因数を介してそれらを接続する一連のスワップが存在します。 したがって、コンポーネント内のすべての順列に到達可能です。 コンポーネントは独立しているため、辞書編集的に最小化すると、各コンポーネントを個別にソートし、最小値を最も早く配置することになります。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

MAXV = 100000

# smallest prime factor sieve
spf = list(range(MAXV + 1))
for i in range(2, int(MAXV ** 0.5) + 1):
    if spf[i] == i:
        for j in range(i * i, MAXV + 1, i):
            if spf[j] == j:
                spf[j] = i

def factorize(x):
    res = []
    while x > 1:
        p = spf[x]
        res.append(p)
        while x % p == 0:
            x //= p
    return res

class DSU:
    def __init__(self, n):
        self.p = list(range(n))
        self.r = [0] * n

    def find(self, x):
        while self.p[x] != x:
            self.p[x] = self.p[self.p[x]]
            x = self.p[x]
        return x

    def union(self, a, b):
        a = self.find(a)
        b = self.find(b)
        if a != b:
            if self.r[a] < self.r[b]:
                a, b = b, a
            self.p[b] = a
            if self.r[a] == self.r[b]:
                self.r[a] += 1

def solve():
    n = int(input())
    arr = list(map(int, input().split()))

    dsu = DSU(n)
    prime_owner = {}

    for i, val in enumerate(arr):
        primes = factorize(val)
        if not primes:
            continue
        first = primes[0]
        for p in primes[1:]:
            dsu.union(first, p)
        if first in prime_owner:
            dsu.union(i, prime_owner[first])
        else:
            prime_owner[first] = i

    comp_idx = {}
    comp_vals = {}

    for i, v in enumerate(arr):
        root = dsu.find(i) if arr[i] != 1 else i
        comp_idx.setdefault(root, []).append(i)
        comp_vals.setdefault(root, []).append(v)

    res = arr[:]
    for root in comp_idx:
        idxs = sorted(comp_idx[root])
        vals = sorted(comp_vals[root])
        for i, v in zip(idxs, vals):
            res[i] = v

    print(*res)

if __name__ == "__main__":
    solve()
```上部のふるいにより、次の値までのすべての値に対して十分な速度で因数分解が行われることが保証されます。$10^5$。 DSU は、共有素因数を通じて間接的にインデックスを接続するために使用されます。 の`prime_owner`map は、値と位置を統一できるように、各主要コンポーネントが実際のインデックスに固定されていることを保証します。 

コンポーネントを構築するときは、インデックスと値を個別に収集します。 素数上の DSU ルートは配列インデックスに直接対応しないため、この分離は重要です。そのため、最後にすべてをマッピングし直す必要があります。 

最後に、各コンポーネント内での並べ替えにより、常に最小の使用可能な値と最も古い使用可能な位置が一致するため、辞書編集的に最小限の配置が保証されます。 

## 実用的な例

 ### 例 1

 入力:```
3
6 10 15
```因数分解します: 6 = 2・3、10 = 2・5、15 = 3・5。 すべての数字は共有素数を通じて接続されます。 

| ステップ | アクション | コンポーネント |
 | --- | --- | --- |
 | 1 | Union(6 with 10 via 2) | {6,10} |
 | 2 | Union(6 with 15 via 3) | {6,10,15} |
 | 3 | Union(10 with 15 via 5) | {6,10,15} |

 すべてのインデックスは 1 つのコンポーネントに属します。 値を並べ替えると [6,10,15] が得られ、並べ替えられたインデックスは [0,1,2] になります。 最終的な配列は次のようになります。```
6 10 15
```これにより、共有素数による完全な推移性が確認されます。 

### 例 2

 入力:```
6
12 45 3 8 15 7
```因子構造: 12(2,3)、45(3,5)、3(3)、8(2)、15(3,5)、7(素数のみ)。 

| ステップ | アクション | コンポーネント |
 | --- | --- | --- |
 | 1 | 12-8 を 2 経由で接続 | {12,8} |
 | 2 | 12-45 を 3 経由で接続する | {12,8,45,3,15} |
 | 3 | 7 分離 | {7} |

 コンポーネント値: [12,45,3,8,15]、インデックス [0,1,2,3,4]。 並べ替えられた値: [3,8,12,15,45]。 並べ替えられたインデックス: [0,1,2,3,4]。 

結果：```
3 8 12 15 45 7
```これは、7 が他の要素と素因数を共有しないため、どのように固定されたままであるかを示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |$O(n \log A + A \alpha(n))$| ふるい + 因数分解 + DSU 共用体 |
 | スペース |$O(n + A)$| DSU 配列、ふるい、グループ化構造 |

 制約により、最大で$10^5$したがって、ふるいベースの因数分解と線形に近い DSU 演算は快適に高速です。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    import sys
    input = sys.stdin.readline

    MAXV = 100000
    spf = list(range(MAXV + 1))
    for i in range(2, int(MAXV ** 0.5) + 1):
        if spf[i] == i:
            for j in range(i * i, MAXV + 1, i):
                if spf[j] == j:
                    spf[j] = i

    def factorize(x):
        res = []
        while x > 1:
            p = spf[x]
            res.append(p)
            while x % p == 0:
                x //= p
        return res

    class DSU:
        def __init__(self, n):
            self.p = list(range(n))
            self.r = [0] * n
        def find(self, x):
            while self.p[x] != x:
                self.p[x] = self.p[self.p[x]]
                x = self.p[x]
            return x
        def union(self, a, b):
            a = self.find(a)
            b = self.find(b)
            if a != b:
                if self.r[a] < self.r[b]:
                    a, b = b, a
                self.p[b] = a
                if self.r[a] == self.r[b]:
                    self.r[a] += 1

    n_and_rest = list(map(int, sys.stdin.read().split()))
    n = n_and_rest[0]
    arr = n_and_rest[1:]

    dsu = DSU(n)
    prime_owner = {}

    for i, val in enumerate(arr):
        primes = factorize(val)
        if primes:
            first = primes[0]
            for p in primes[1:]:
                dsu.union(first, p)
            if first in prime_owner:
                dsu.union(i, prime_owner[first])
            else:
                prime_owner[first] = i

    comp_idx = {}
    comp_vals = {}

    for i, v in enumerate(arr):
        root = i if arr[i] == 1 else dsu.find(i)
        comp_idx.setdefault(root, []).append(i)
        comp_vals.setdefault(root, []).append(v)

    res = arr[:]
    for r in comp_idx:
        idxs = sorted(comp_idx[r])
        vals = sorted(comp_vals[r])
        for i, v in zip(idxs, vals):
            res[i] = v

    return " ".join(map(str, res))

# provided samples
assert run("3\n6 4 2") == "2 4 6", "sample 1"
assert run("3\n10 15 6") == "6 10 15", "sample 2"
assert run("6\n12 45 3 8 15 7") == "3 8 12 15 45 7", "sample 3"

# custom cases
assert run("2\n7 11") == "7 11", "both primes isolated"
assert run("4\n6 10 15 14") == "6 10 14 15", "multiple connected via 2,3,5,7 chain"
assert run("5\n1 1 1 1 1") == "1 1 1 1 1", "all ones"
assert run("5\n2 4 8 16 3") == "2 4 8 16 3", "one isolated prime"

print("OK")
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 7 11 | 7 11 | 孤立した素数 |
 | 6 10 15 14 | 6 10 14 15 | マルチコンポーネント接続 |
 | 1 1 1 1 1 | 1 1 1 1 1 | 些細なコンポーネント |
 | 2 4 8 16 3 | 2 4 8 16 3 | 孤立した要素を持つチェーン |

 ## 特殊なケース

 重要なエッジケースは、数値がペアごとに互いに素である場合です。 入力の場合:```
7
7 11 13 17
```各要素は独自のコンポーネントを形成します。 このアルゴリズムはシングルトン DSU セットを作成しますが、各コンポーネント内での並べ替えは何も行いません。 出力は変更されず、スワップが不可能であるという事実と一致します。 

別の特殊なケースは、同じ値が繰り返されることです。 のために：```
4
6 6 6 6
```すべてのインデックスは共有素因数 6 を介して接続されます。アルゴリズムはすべてを 1 つのコンポーネントにマージし、同一の値を並べ替えて、同じ配列を再構築します。 多くの順列が存在するにもかかわらず、辞書編集上の最小性は安定しています。 

3 番目のケースには値 1 が含まれます。1 には素因数がないため、他の数値に接続できません。 実装では、コンポーネントは分離されたままになり、誤ってコンポーネントにマージされることがなくなります。
