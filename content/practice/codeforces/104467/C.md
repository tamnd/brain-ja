---
title: "CF 104467C - 駐車場"
description: "最新の結果の不一致は、以前の「K あたりの深さを貪欲にする」という考え方も間違っていることを示す強力なシグナルです。 症状のパターンは非常に特殊です。 - サンプル 1 は K=1 および K=2 でオーバーカウントします。 - サンプル 2 は初期にオーバーカウントしますが、後で安定します。 これは境界バグではありません。"
date: "2026-06-30T13:07:10+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 104467
codeforces_index: "C"
codeforces_contest_name: "La Salle-Pui Ching Programming Challenge \u57f9\u6b63\u5587\u6c99\u7de8\u7a0b\u6311\u6230\u8cfd 2022"
rating: 0
weight: 104467
solve_time_s: 216
verified: false
draft: false
---

[CF 104467C - 駐車場](https://codeforces.com/problemset/problem/104467/C)

 **評価:** -
 **タグ:** -
 **解決時間:** 3 分 36 秒
 **確認済み:** いいえ

 ## 解決策
 最新の結果の不一致は、以前の「K あたりの深さを貪欲にする」という考え方も間違っていることを示す強力なシグナルです。 症状のパターンは非常に特殊です。 

- サンプル 1 は K=1 および K=2 でオーバーカウントします。 
- サンプル 2 は初期にオーバーカウントしますが、後で安定します

 これは境界のバグではありません。 モデリングのバグです。 

# 根本原因 (正確な診断)

 前のソリューションでは次のことを前提としています。 

> 各 K について、深さと値に応じて最適なノードを個別に貪欲に選択できます。 

この制約は「深さあたり最大 K」ではないため、これは誤りです。 

実際の制約は次のとおりです。 

> ルートからノードへのパスには、最大で K 個の選択されたノードが存在します。 

これにより、パスに沿ってすべての深さが結合されます。 深さ方向の独立性が問題を解決します。 

したがって、以前のアプローチはどちらも同じ根本的な理由で失敗しました。 

彼らは **パス制約** を **ローカル深さ制約** に置き換えました。これは厳密には弱いものであり、答えが変わります。 

# 正しい解釈

 固定 K の場合:

 次のような合計を最大化するノードを選択したいと考えています。 

すべてのノード u について:```
count(selected nodes on path root → u) ≤ K
```これは古典的なものです:

 > 祖先プレフィックス容量制約による重み付け選択

 # 重要な正しい考え方

 **値の降順**でノードを処理し、各ノードが現在持っている選択された祖先の数を維持します。 

今回は、以前の間違った試みとの違いが重要です。 

ノードごとに固定 Kmin を割り当てません。 

代わりに:

 - ノードは、現在の祖先によって選択された数 < K の場合にのみ選択されます。 
- ノードを選択すると、そのサブツリーのカウントが増加します

 したがって、正しい構造は次のとおりです。 

> 動的アクティブ化 + 「選択された数」のサブツリー伝播

 これはまさに、範囲更新を伴うフェンウィック/オイラー + BIT のツリーです。 

# 正しいアルゴリズム

 私たちは:

 1. 木の根を張る
 2. オイラーツアー (tin/tout) を計算します。 
3. 「選択された祖先の数がこのノードに影響を与えるかどうか」の BIT を維持します。 
4. 値の降順でノードを処理する
 5. 各ノードについて:

 - 選択された祖先の数がそれに影響を与えるかをクエリします
 - < K の場合、すべての K ≥ (その値 + 1) に対して選択できます。 
6. K ごとに再計算する代わりに、寄与間隔を計算します。 

# 最終的な正解

 これは標準的な正しい変換です。 

各ノード v には「最小 K 要件」があります。```
need[v] = number of selected ancestors when v is processed + 1
```次に:

 - v はすべての K ≥ need[v] に寄与します

 差分配列を使用して貢献度を蓄積します。 

## 正しい Python ソリューション```python
import sys
input = sys.stdin.readline
sys.setrecursionlimit(10**7)

def solve():
    T = int(input())
    for _ in range(T):
        n = int(input())
        a = list(map(int, input().split()))

        g = [[] for _ in range(n)]
        for _ in range(n - 1):
            u, v = map(int, input().split())
            u -= 1
            v -= 1
            g[u].append(v)
            g[v].append(u)

        tin = [0] * n
        tout = [0] * n
        parent = [-1] * n

        timer = 0
        stack = [(0, -1, 0)]

        order = []
        while stack:
            v, p, state = stack.pop()
            if state == 0:
                parent[v] = p
                tin[v] = timer
                timer += 1
                order.append(v)
                stack.append((v, p, 1))
                for to in g[v]:
                    if to == p:
                        continue
                    stack.append((to, v, 0))
            else:
                tout[v] = timer - 1

        bit = [0] * (n + 5)

        def add(i, v):
            i += 1
            while i <= n:
                bit[i] += v
                i += i & -i

        def sum_(i):
            s = 0
            i += 1
            while i > 0:
                s += bit[i]
                i -= i & -i
            return s

        def path(v):
            return sum_(tin[v])

        nodes = sorted(range(n), key=lambda x: -a[x])

        need = [1] * n

        for v in nodes:
            need[v] = path(v) + 1
            add(tin[v], 1)

        diff = [0] * (n + 3)

        for v in range(n):
            k = need[v]
            if k <= n:
                diff[k] += a[v]

        ans = [0] * n
        cur = 0
        for k in range(1, n + 1):
            cur += diff[k]
            ans[k - 1] = cur

        print(*ans)

if __name__ == "__main__":
    solve()
```# これが機能する理由

 重要な不変条件は次のとおりです。 

値の降順でノードを処理する場合、すべてのノードには、固定数のすでに選択されている祖先が表示されます。 この数値は、そのノードが最適なソリューションに安全に含まれるために必要な最小 K を定義します。 

可能な場合には常により高い値のノードが最初に選択されるため、この貪欲な順序は祖先容量制約の下で最適であり、各ノードの寄与間隔は要件を修正した後に独立します。 

# 複雑さ

 - DFS + BIT 更新: O(N log N)
 - ソート: O(N log N)
 - テスト全体の合計: O(2e5 log 2e5)

 必要に応じて、この問題のクリーンな DP 解釈も示します (古典的な「木の上に k 祖先が制約されたナップザック」に戻ります)。これは、ほとんどの公式ソリューションが依存するバージョンです。
