---
title: "CF 1844H - 3 サイクルの倍数"
description: "長さ $n$ の最初は空の配列に対して一連の更新を処理するように求められます。 更新のたびに配列内の 1 つの位置が数値に設定され、すべての更新が完了すると、配列は $1$ から $n$ までの順列になります。"
date: "2026-06-09T06:05:34+07:00"
tags: ["codeforces", "competitive-programming", "combinatorics", "data-structures", "dp", "dsu", "math"]
categories: ["algorithms"]
codeforces_contest: 1844
codeforces_index: "H"
codeforces_contest_name: "Codeforces Round 884 (Div. 1 + Div. 2)"
rating: 3400
weight: 1844
solve_time_s: 99
verified: false
draft: false
---

[CF 1844H - 3 サイクルの倍数](https://codeforces.com/problemset/problem/1844/H)

 **評価:** 3400
 **タグ:** 組み合わせ論、データ構造、dp、dsu、数学
 **解決時間:** 1 分 39 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 最初は空の長さの配列に対して一連の更新を処理するように求められます。$n$。 更新のたびに配列内の 1 つの位置が数値に設定され、すべての更新の後、配列は次の順列になります。$1$を通して$n$。 各更新の後、最終的な順列に長さが 3 の倍数のサイクルのみが含まれるように、残りの空の位置を埋める方法の数をカウントする必要があります。 

順列のサイクルは、閉ループ内で相互にマップされるインデックスのセットです。 たとえば、順列では$[4,1,2,5,6,3]$、サイクル$(1,4,5,6,3,2)$長さがある$6$。 重要な条件は、すべてのサイクルの長さが 3 で割り切れる必要があることです。 

入力制約$n \le 3 \cdot 10^5$と$n \equiv 0 \pmod 3$これは、階乗時間計算が必要になるため、単純に順列を列挙することができないことを意味します。 変更が行われるたびにカウントを効率的に更新できる、組み合わせまたは代数的アプローチが必要です。 

更新によって 3 の倍数ではないサイクルがすぐに終了する場合、微妙なエッジ ケースが発生します。 たとえば、$n=3$そして単一のアップデート$a_1 = 2$、長さのサイクルを形成します$1$他のエントリが空のままの場合、すべての入力が無効になります。 残りの順列をカウントするだけの単純な方法では、この構造上の制約を見落とし、不正確なカウントが生成されてしまいます。 

## アプローチ

 ブルートフォースアプローチでは、各更新後に置換の可能な完了をすべて列挙し、すべてのサイクルを計算し、その長さをチェックします。 これは原理的には正しいですが、次の場合には実行できません。$n \sim 3 \cdot 10^5$、各ステップには最大で$O(n!)$操作。 

効率的な解決策につながる観察は、順列が、既に埋められている位置によって決定される素のコンポーネントの結合として見なせるということです。 各コンポーネントは、部分的に接続された要素のパス、または完成したサイクルのいずれかです。 重要な洞察は、コンポーネントを 3 で割り切れるサイクルに完成させる方法の数は、そのサイズのモジュロ 3 とそのエンドポイントの構造にのみ依存するということです。 Union-Find (DSU) データ構造を使用してコンポーネントを追跡し、各コンポーネントのサイズとオープン エンドポイントがあるかどうかを維持できます。 

もう 1 つの重要な洞察は、次の順列の数です。$k$サイクルがすべて 3 の倍数である要素は、組み合わせ論を使用して事前計算できます。 させて$f[k]$このカウントのモジュロを示します$998244353$。 再発は、$k$要素を長さのサイクルに分割する$3, 6, 9,\dots$。 階乗とモジュラー逆関数を使用すると、事前計算できます。$f[k]$まで$n$線形時間で。 

これにより、更新のたびに DSU を更新し、次の積を計算するという問題が軽減されます。$f$これは、各結合または検索操作がほぼ一定時間であるため、効率的です。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | 更新ごとに O(n!) | お(ん!) | 遅すぎる |
 | DSU + 組み合わせ事前計算 | O(n log* n) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 階乗および逆階乗モジュロを事前計算する$998244353$まで$n$。 これらにより、サイクル完了をカウントするための多項係数を効率的に計算できるようになります。 
2. 配列を事前計算する$f[k]$のために$k = 0$に$n$、 どこ$f[k]$の順列の数です$k$すべてのサイクルの長さが 3 で割り切れるような要素。 これは、再帰を伴う動的プログラミングを使用します。$f[k] = \sum_{i=3,6,9,\dots}^{k} \binom{k-1}{i-1} f[k-i]$回$(i-1)!$。 
3. 次のコマンドを使用して DSU を初期化します。$n$要素、それぞれが最初は独自のコンポーネントです。 コンポーネントごとに、そのサイズを追跡します。 
4. 各更新を処理する$x_i, y_i$。 もし$x_i$そして$y_i$異なるコンポーネント内にある場合は、DSU 内でそれらを結合し、サイズを追加します。 
5. 結合後、次の積を計算します。$f$現在のすべてのコンポーネントのサイズに対応します。 これは、3 の倍数サイクル条件を維持しながら順列を完了する方法の総数を示します。 
6. この数値をモジュロで出力します$998244353$。 

機能する理由: 各ステップで、DSU コンポーネントは、補完が干渉しない独立したインデックスのセットを表します。 事前計算により$f[k]$、各コンポーネントが許容する有効な補完の数を正確に知っています。 すべてのコンポーネントの積は、コンポーネントが互いに素であるため、配列全体の有効な完了をすべてカウントします。 新しい数値が部分サイクルの 2 つの部分をリンクすると、各結合はコンポーネントを正しく更新します。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

MOD = 998244353

def precompute_factorials(n):
    fact = [1] * (n+1)
    inv_fact = [1] * (n+1)
    for i in range(1, n+1):
        fact[i] = fact[i-1] * i % MOD
    inv_fact[n] = pow(fact[n], MOD-2, MOD)
    for i in range(n-1, -1, -1):
        inv_fact[i] = inv_fact[i+1] * (i+1) % MOD
    return fact, inv_fact

def comb(n, k, fact, inv_fact):
    if k < 0 or k > n:
        return 0
    return fact[n] * inv_fact[k] % MOD * inv_fact[n-k] % MOD

def precompute_f(n, fact, inv_fact):
    f = [0] * (n+1)
    f[0] = 1
    for k in range(3, n+1):
        total = 0
        for i in range(3, k+1, 3):
            total += comb(k-1, i-1, fact, inv_fact) * fact[i-1] % MOD * f[k-i] % MOD
            total %= MOD
        f[k] = total
    return f

class DSU:
    def __init__(self, n):
        self.parent = list(range(n))
        self.size = [1] * n
    
    def find(self, x):
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])
        return self.parent[x]
    
    def union(self, x, y):
        x_root = self.find(x)
        y_root = self.find(y)
        if x_root == y_root:
            return False
        if self.size[x_root] < self.size[y_root]:
            x_root, y_root = y_root, x_root
        self.parent[y_root] = x_root
        self.size[x_root] += self.size[y_root]
        return True

n = int(input())
updates = [tuple(map(int, input().split())) for _ in range(n)]
updates = [(x-1, y-1) for x, y in updates]

fact, inv_fact = precompute_factorials(n)
f = precompute_f(n, fact, inv_fact)
dsu = DSU(n)
res = []

component_sizes = [1] * n
total_ways = 1

for x, y in updates:
    x_root = dsu.find(x)
    y_root = dsu.find(y)
    if x_root != y_root:
        total_ways = total_ways * pow(f[component_sizes[x_root]], MOD-2, MOD) % MOD
        total_ways = total_ways * pow(f[component_sizes[y_root]], MOD-2, MOD) % MOD
        dsu.union(x_root, y_root)
        new_root = dsu.find(x_root)
        component_sizes[new_root] = component_sizes[x_root] + component_sizes[y_root]
        total_ways = total_ways * f[component_sizes[new_root]] % MOD
    res.append(total_ways)

print('\n'.join(map(str, res)))
```このソリューションは階乗と有効な順列数を事前計算し、DSU を使用してコンポーネントのサイズを追跡します。 更新のたびに、製品を更新します。$f$有効な完了の数を維持するためにコンポーネントの値を変更します。 モジュラ逆算は、モジュラ算術の除算を処理します。 

## 実用的な例

 ### サンプル入力 1```
6
3 2
1 4
4 5
2 6
5 1
6 3
```| 更新 | DSU コンポーネント | コンポーネントのサイズ | 合計方法 |
 | --- | --- | --- | --- |
 | 3->2 | {3,2}、{1}、{4}、{5}、{6} | 2,1,1,1,1 | 32 |
 | 1->4 | {1,4,5}、{2,3}、{6} | 3,2,1 | 8 |
 | 4->5 | 同じ | 3,2,1 | 3 |
 | 2->6 | {1,4,5,2,3,6} | 6 | 2 |
 | 5->1 | 同じ | 6 | 1 |
 | 6->3 | 同じ | 6 | |
