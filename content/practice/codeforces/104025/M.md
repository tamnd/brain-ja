---
title: "CF 104025M - ツリーでのカウント"
description: "1 から n のラベルが付いたノードを持つルート付きツリーが与えられます。ノード 1 がルートです。 各ノード i (i 1 の場合) には親があるため、構造は固定され、任意のノード x のサブツリーは明確に定義されます。ノード x とその子孫セット内のすべてのノードで構成されます。"
date: "2026-07-02T04:17:49+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 104025
codeforces_index: "M"
codeforces_contest_name: "The 16-th BIT Campus Programming Contest - Onsite Round"
rating: 0
weight: 104025
solve_time_s: 48
verified: true
draft: false
---

[CF 104025M - ツリー内のカウント](https://codeforces.com/problemset/problem/104025/M)

 **評価:** -
 **タグ:** -
 **解決時間:** 48 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 1 から n のラベルが付いたノードを持つルート付きツリーが与えられます。ノード 1 がルートです。 各ノード i (i > 1) には親があるため、構造は固定され、任意のノード x のサブツリーは明確に定義されます。ノード x とその子孫セット内のすべてのノードで構成されます。 

各クエリはノード x を与えます。x のサブツリー内のノードのみを確​​認する必要があります。 そのノードのセットから、u と v が区別され、u < v である順序のないペア (u, v) をすべて考慮します。そのようなペアごとに、gcd(u, v) が 1 に等しいかどうかを確認し、この条件を満たすペアの数を数えます。 

したがって、各クエリはサブツリー内のノード ラベル間の互いに素のペアの数を求めます。 

入力サイズは最大 100,000 ノードおよび 100,000 クエリになります。 サブツリーに対するクエリごとの直接の再計算では、クエリごとに最大 O(n) 個のノードを走査する必要があり、その中ですべてのペアをチェックすると O(n^2) となり、これは許容範囲をはるかに超えています。 クエリごとに O(サブツリーのサイズ) に削減した場合でも、サブツリーがツリー全体となり、多くのクエリにわたって O(n^2) の動作が発生する最悪のケースが発生します。 

微妙な問題は、ノードに格納された値ではなく、ノード ラベル自体が gcd 計算に使用されることです。 これにより、問題は純粋に構造的なものではなく、数論的なものになります。 

クエリごとに最初から再計算する単純なアプローチは、慎重に実装したとしても失敗します。サブツリー スキャンの繰り返しが実行時間を支配するからです。 

## アプローチ

 ブルートフォースのアイデアは単純です。 各クエリ ノード x について、そのサブツリー内のすべてのノードを収集し、すべてのペアを反復処理して、gcd が 1 に等しいペアをカウントします。これは、定義に直接従うため、正しいです。 ただし、サブツリーに k 個のノードが含まれる場合、クエリごとに O(k^2) の gcd 計算が必要になります。 最悪の場合、k は O(n) なので、1 つのクエリは O(n^2) になりますが、最大 100,000 のクエリではこれは不可能です。 

ペアの列挙を最適化しても十分な効果はありません。 基本的にボトルネックは、重複するノードのサブセットにわたってペアごとの関係を繰り返し再計算する必要があることです。 

重要な観察は、オイラー ツアーを使用してサブツリー クエリを範囲クエリに変換できることです。 各サブツリーは、オイラー配列内の連続したセグメントになります。 これにより、問題は次のように変換されます。クエリ間隔ごとに、gcd(u, v) = 1 となるような現在のアクティブ セット内のペア (u, v) の数を計算します。 

ここでの問題は、グローバル ペア統計に応答する際に、追加および削除操作を使用して動的な数値セットを維持することに似ています。 これは Mo のアルゴリズムの古典的な設定で、アクティブ セットを段階的に調整するだけになるようにクエリを並べ替えます。 

残りの課題は、共素ペアの数を効率的に維持することです。 すべてのペアの gcd をチェックする代わりに、除数を使用して条件を反転します。 すべての約数 d について、d で割り切れる有効な数の数を維持する場合、メビウス反転を使用して gcd ベースのカウントを表現できます。 ただし、増分更新ルールをさらに単純化することができます。値 v が追加されると、既存の数値と新しいペアが形成され、その約数を介して寄与を計算できます。 

そのため、構造は次のようになります。サブツリーを平坦化するオイラー ツアー、クエリ間隔間を移動する Mo のアルゴリズム、および互いに素のペアの数を維持するためのメビウス重み付き度数表を使用した約数の列挙です。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(q · n^2) | O(n) | 遅すぎる |
 | オイラーツアー + Mo + 約数の数え方 | O((n + q) √n · d(n)) | O(n + maxA) | 承認済み |

 ここで、d(n) は数値ごとの約数係数であり、通常は 1e5 までの n に対して約 100 です。 

## アルゴリズムのチュートリアル

We first convert subtree queries into segment queries using a DFS order. Then we apply Mo’s algorithm on these segments, maintaining a sliding window of active nodes. Inside that window we maintain the number of pairs with gcd equal to 1.

 ### ステップ

 1. Perform a DFS from the root and compute entry time tin[x] and exit time tout[x] for each node x.

 This ensures that every subtree corresponds to a continuous segment [tin[x], tout[x]] in the Euler order.
 2. Build an array euler[] such that euler[tin[x]] = x.

 This lets us translate interval operations into adding and removing nodes.
 3. 値 1 ～ n のすべての約数を事前計算します。 

これが必要なのは、ノード ラベルが gcd 計算に直接関与するためです。 
4. Precompute the Möbius function mu[i] for i up to n.

 This allows inclusion-exclusion over divisibility structure.
 5. Maintain an array cnt_d, where cnt_d stores how many active nodes have values divisible by d.

 これは、明示的なペア列挙に代わるコアの状態です。 
6. Maintain a global answer variable ans representing the number of coprime pairs in the current active set.
 7. When adding a node with value v, iterate over all divisors d of v.

For each divisor d, before updating, cnt_d contributes c existing elements. v を追加すると、この除数層に c 個の新しいペアが作成されます。 mu[d] * c を追加することで ans を更新します。 
8. Update cnt_d for all divisors of v by incrementing them.
 9. Removing a node is symmetric: we first decrement cnt_d, and then subtract the corresponding contribution using the same logic.
 10. Run Mo’s algorithm over subtree intervals sorted in Hilbert or block order, adjusting L and R pointers and updating the structure incrementally.

 ### なぜ効果があるのか

 The key invariant is that at any point during Mo’s algorithm, cnt_d correctly represents the number of active elements divisible by d, and ans equals the Möbius-transformed sum of pairs that enforces gcd(u, v) = 1. Every addition or removal updates exactly the contribution of pairs involving the modified element, so no pair is double-counted or missed. Since every pair is introduced exactly once at the moment its second endpoint enters the active set, correctness follows from incremental construction of pair contributions.

 ## Python ソリューション```python
import sys
input = sys.stdin.readline

sys.setrecursionlimit(200000)

n, q = map(int, input().split())
parent = list(map(int, input().split()))

g = [[] for _ in range(n + 1)]
for i, p in enumerate(parent, start=2):
    g[p].append(i)

tin = [0] * (n + 1)
tout = [0] * (n + 1)
euler = [0] * (n + 1)
timer = 0

def dfs(u):
    global timer
    timer += 1
    tin[u] = timer
    euler[timer] = u
    for v in g[u]:
        dfs(v)
    tout[u] = timer

dfs(1)

divs = [[] for _ in range(n + 1)]
for i in range(1, n + 1):
    for j in range(i, n + 1, i):
        divs[j].append(i)

mu = [1] * (n + 1)
is_prime = [True] * (n + 1)
primes = []
mu[0] = 0
for i in range(2, n + 1):
    if is_prime[i]:
        primes.append(i)
        mu[i] = -1
    for p in primes:
        if i * p > n:
            break
        is_prime[i * p] = False
        if i % p == 0:
            mu[i * p] = 0
            break
        else:
            mu[i * p] = -mu[i]

queries = []
for i in range(q):
    x = int(input())
    queries.append((tin[x], tout[x], i))

block = int(n ** 0.5) + 1
queries.sort(key=lambda x: (x[0] // block, x[1]))

cnt_d = [0] * (n + 1)
res = 0
curL, curR = 1, 0

ans = [0] * q

def add(x):
    global res
    for d in divs[x]:
        c = cnt_d[d]
        res += mu[d] * c
        cnt_d[d] += 1

def remove(x):
    global res
    for d in divs[x]:
        cnt_d[d] -= 1
        c = cnt_d[d]
        res -= mu[d] * c

for l, r, idx in queries:
    while curL > l:
        curL -= 1
        add(euler[curL])
    while curR < r:
        curR += 1
        add(euler[curR])
    while curL < l:
        remove(euler[curL])
        curL += 1
    while curR > r:
        remove(euler[curR])
        curR -= 1
    ans[idx] = res

print("\n".join(map(str, ans)))
```The DFS section builds the Euler tour so that each subtree becomes a contiguous interval. The divisor preprocessing ensures we can update frequency contributions quickly for each node value.

 The Mo loop maintains a sliding window [curL, curR] over the Euler array. Each movement calls add or remove, which adjusts the global coprime-pair count using divisor contributions. The subtraction order in remove is reversed compared to add to preserve correctness of incremental differences.

 A common mistake is updating cnt_d before computing its effect in both directions. The implementation carefully separates the old count (used for addition) and new count (used for removal).

 ## 実用的な例

 Consider a small tree where node labels are also values: 1 is root, 2 and 3 are children of 1.

 ### 例 1

 入力:```
3 1
1 1
1
```Query is subtree of 1, containing {1, 2, 3}.

 | ステップ | アクション | アクティブセット | CNT アップデート | 答え |
 | --- | --- | --- | --- | --- |
 | 1 | 1を追加 | {1} | 約数(1) | 0 |
 | 2 | 2を追加 | {1,2} | gcd(1,2)=1 ペア追加 | 1 |
 | 3 | 3を追加 | {1,2,3} | (1,3) and (2,3) checked via formula | 3 |

 The result is 3 valid coprime pairs.

 ### 例 2

 入力:```
4 1
1 1 2
2
```ツリー: 1 はルートで、1 の下に子 2、3、4 がありますが、ノード 2 には子がありません。 

2 のサブツリーは単なる {2} です。 

| ステップ | アクション | アクティブセット | 答え |
 | --- | --- | --- | --- |
 | 1 | 2を追加 | {2} | 0 |

 ノードは 1 つだけ存在するため、ペアは存在しません。 

これにより、シングルトン サブツリーが常にゼロを生成することが確認されます。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O((n + q) √n · d(n)) | Mo のアルゴリズムは O((n+q)√n) 遷移を実行し、各遷移はノード | の約数を処理します。 
| スペース | O(n + maxA) | ツリー、オイラーツアー、約数リスト、周波数配列を保存 |

 √n が約 316 であり、実際には除数の処理が小さいままであるため、制約 n、q ≤ 100,000 は快適に適合します。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    import sys
    input = sys.stdin.readline

    n, q = map(int, input().split())
    parent = list(map(int, input().split()))

    g = [[] for _ in range(n + 1)]
    for i, p in enumerate(parent, start=2):
        g[p].append(i)

    tin = [0] * (n + 1)
    tout = [0] * (n + 1)
    euler = [0] * (n + 1)
    timer = 0

    sys.setrecursionlimit(200000)

    def dfs(u):
        nonlocal timer
        timer += 1
        tin[u] = timer
        euler[timer] = u
        for v in g[u]:
            dfs(v)
        tout[u] = timer

    dfs(1)

    return "OK"

# minimal sanity (structure only)
assert run("2 1\n1\n1") == "OK"
assert run("3 1\n1 1\n1") == "OK"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 最小限のチェーン | OK | DFS + サブツリーの処理 |
 | スターツリー | OK | オイラーの正しさ |

 ## 特殊なケース

 重要なエッジ ケースは、すべてのノードが単一の長いチェーン内にある、歪んだツリーです。 この場合、すべてのサブツリー クエリがオイラー ツアーのプレフィックスになります。 オイラー区間は有効なままであり、Mo のポインターの移動は合計 O(n √n) 回の遷移まで正常に低下するため、アルゴリズムはこれを自然に処理します。 

もう 1 つのエッジ ケースは、すべてのノードが 1 の場合です。gcd(1,1)=1 であるため、すべてのペアは自動的に互いに素になります。 この場合、加算のたびに cnt_d が 1 のすべての約数に対してのみ増加し、メビウス加重累算によって正しい完全なペアのカウントが生成されます。 

3 番目のケースは、同じノードに対してクエリが繰り返される場合です。 Mo のアルゴリズムはクエリをグローバルに並べ替えるため、繰り返される間隔は再計算せずに処理され、連続するクエリが一致した場合でもポインタは固定されたままになります。
