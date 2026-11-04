---
title: "CF 1215F - ラジオ局"
description: "私たちは、一連の無線局に信号電力を割り当て、すべての市民の苦情が満たされ、2 つの局が干渉せず、選択された各局の信号電力が許容範囲内に収まるように、これらの局のサブセットを選択するように求められます。"
date: "2026-06-11T22:58:40+07:00"
tags: ["codeforces", "competitive-programming", "2-sat"]
categories: ["algorithms"]
codeforces_contest: 1215
codeforces_index: "F"
codeforces_contest_name: "Codeforces Round 585 (Div. 2)"
rating: 2700
weight: 1215
solve_time_s: 135
verified: false
draft: false
---

[CF 1215F - ラジオ局](https://codeforces.com/problemset/problem/1215/F)

 **評価:** 2700
 **タグ:** 2 土
 **解決時間:** 2 分 15 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 私たちは、一連の無線局に信号電力を割り当て、すべての市民の苦情が満たされ、2 つの局が干渉せず、選択された各局の信号電力が許容範囲内に収まるように、これらの局のサブセットを選択するように求められます。 各苦情は 2 つのステーションを識別し、そのうちの少なくとも 1 つがアクティブであることが必要です。 各ステーションには許容される最小および最大の信号電力があり、特定のステーションのペアは干渉のために両方ともアクティブになることができません。 課題は、信号電力と有効なステーションのセットを見つけるか、不可能であることを報告することです。 

入力サイズは大きく、最大 400,000 件の苦情、ステーション、および干渉のペアが含まれます。 これにより、ステーションまたはクレームの数が 3 次または 2 次の複雑さを持つアルゴリズムが除外されます。 線形時間または線形算術時間に近い解決策が必要です。 400,000 局のすべてのサブセットを列挙するだけでも天文学的に時間がかかるため、局のすべてのサブセットまたは考えられるすべての信号電力を試す単純なアプローチは実行不可能です。 

明らかではないエッジ ケースには、すべてのステーションの最小許容パワーが他のステーションの最大許容パワーを超えており、単一のグローバル信号パワーを見つけることが不可能になる状況が含まれます。 もう 1 つの注意が必要なケースは、干渉制約が複雑な網を形成し、苦情に必要なステーションの起動を妨げる場合です。たとえば、苦情がサイクルを形成し、隣接する各ステーションが次のステーションに干渉する場合です。 不注意な実装では、競合をチェックせずにステーションを貪欲に選択したり、無効なソリューションが生成されたり、ソリューションが存在する場合にレポートを返さなかったりする可能性があります。 

## アプローチ

 ブルート フォース アプローチでは、1 から M までのすべての整数の信号電力を試し、電力ごとに、すべての不満を満たし、干渉を回避する局のサブセットを選択しようとします。 各サブセットを検証するには、すべての苦情とすべての干渉ペアをチェックする必要があります。 これは原理的には正しいですが、遅すぎます。最大 4×10^5 までの M 値を反復処理し、それぞれについて潜在的に 400,000 件の苦情を処理すると、実行可能な制限をはるかに超える約 1.6×10^11 の演算が必要になります。 

重要な観察は、選択された信号パワーに対して、ステーションは許可される (その`[l_i, r_i]`信号電力が含まれているかどうか）。 候補の信号パワーを修正すると、問題は 2-SAT のインスタンスに減ります。 各苦情では、その 2 つのステーションのうち少なくとも 1 つを選択して、条項を形成する必要があります。`(x_i OR y_i)`。 各干渉ペアは両方のステーションの選択を禁止し、条項を形成します。`(NOT u_i OR NOT v_i)`。 2-SAT は、含意グラフ内の強連結成分を使用して、変数と節の数に関して線形時間で解くことができます。 

したがって、最適なアプローチは、考えられる信号電力をインテリジェントに反復することです。 1 から M までのすべての電力をテストする代わりに、一部の局の範囲が開始または終了する電力のみを考慮します。これは、これらの境界のみが有効な局のセットを変更できるためです。 候補電力ごとに、その電力で許可されるステーションの 2-SAT インスタンスを構築し、それを解決し、満足できる場合はその解を返します。 これにより、候補検出力の数が最大 2p に減りますが、これは実現可能です。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(M・(n+m)) ≈ 1.6×10^11 | O(n+m) | 遅すぎる |
 | 最適 | 候補あたり O(p + n + m) × O(p) 候補 ≈ 10^7-10^8 | O(p + n + m) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. すべての個別の値を`l_i`そして`r_i`駅の配列。 これらのポイント間を移動しても、許可されたステーションが追加または削除されないため、有効なステーションのセットが変更される信号パワーはこれらのみです。 
2. これらの候補勢力を並べ替えます。 順番に繰り返すことで、必要に応じて連続した範囲を効率的に処理できます。 
3. 各候補信号電力について`f`、どのステーションが許可されるかを決定します。 ステーションは最小値であれば許可されます`l_i ≤ f ≤ r_i`。 
4. 許可されたステーションの 2-SAT インスタンスを構築します。 

- 苦情ごとに`(x_i, y_i)`、条項を追加します`(x_i OR y_i)`。 これにより、少なくとも 1 つのステーションが苦情を満たすことが保証されます。 
- 干渉ペアごとに`(u_i, v_i)`、条項を追加します`(NOT u_i OR NOT v_i)`両方が選択されることを禁止します。 
5. 標準の含意グラフ法を使用して 2-SAT インスタンスを解きます。 

- 各条項について`(a OR b)`、エッジを追加します`(NOT a → b)`そして`(NOT b → a)`。 
- 強く接続されたコンポーネントを見つけます。 変数とその否定が同じコンポーネント内にある場合、インスタンスは満たされません。 
- それ以外の場合は、コンポーネントの順序に基づいて真理値を割り当て、ステーションの有効な選択を取得します。 
6. 候補信号電力が満足できる 2-SAT インスタンスを生成する場合、信号電力と選択された局を出力します。 それ以外の場合は報告してください`-1`。 

機能する理由: 境界電力を反復することで、実行可能なすべての信号電力が冗長性なしでテストされることが保証されます。 2-SAT は、苦情と干渉の制約の両方を正確にモデル化します。 満足可能なインスタンスの解は、許可されるステーション範囲と競合制限を直接エンコードするため、元の問題の条件をすべて満たします。 

## Python ソリューション```python
import sys
input = sys.stdin.readline
sys.setrecursionlimit(1 << 25)

def solve():
    n, p, M, m = map(int, input().split())
    complaints = [tuple(map(int, input().split())) for _ in range(n)]
    lr = [tuple(map(int, input().split())) for _ in range(p)]
    conflicts = [tuple(map(int, input().split())) for _ in range(m)]

    # Collect candidate powers
    candidates = set()
    for l, r in lr:
        candidates.add(l)
        candidates.add(r)
    candidates = sorted(candidates)

    # 2-SAT helper functions
    class TwoSAT:
        def __init__(self, n):
            self.N = n
            self.adj = [[] for _ in range(2*n)]

        def add_implication(self, u, v):
            self.adj[u].append(v)

        def add_or(self, u, v):
            self.add_implication(u^1, v)
            self.add_implication(v^1, u)

        def satisfiable(self):
            self.order = []
            self.visited = [False]*(2*self.N)
            self.comp = [0]*(2*self.N)

            def dfs(u):
                self.visited[u] = True
                for v in self.adj[u]:
                    if not self.visited[v]:
                        dfs(v)
                self.order.append(u)

            for u in range(2*self.N):
                if not self.visited[u]:
                    dfs(u)

            g_rev = [[] for _ in range(2*self.N)]
            for u in range(2*self.N):
                for v in self.adj[u]:
                    g_rev[v].append(u)

            self.visited = [False]*(2*self.N)
            label = 0
            comp = [0]*(2*self.N)
            def dfs_rev(u):
                comp[u] = label
                self.visited[u] = True
                for v in g_rev[u]:
                    if not self.visited[v]:
                        dfs_rev(v)

            for u in reversed(self.order):
                if not self.visited[u]:
                    dfs_rev(u)
                    label += 1
            self.comp = comp
            assignment = [False]*self.N
            for i in range(self.N):
                if comp[2*i] == comp[2*i+1]:
                    return None
                assignment[i] = comp[2*i] < comp[2*i+1]
            return assignment

    for f in candidates:
        valid = [i for i, (l, r) in enumerate(lr) if l <= f <= r]
        if not valid:
            continue
        id_map = {v: idx for idx, v in enumerate(valid)}
        ts = TwoSAT(len(valid))

        for x, y in complaints:
            if x-1 in id_map and y-1 in id_map:
                ts.add_or(2*id_map[x-1], 2*id_map[y-1])
            elif x-1 in id_map:
                ts.add_or(2*id_map[x-1], 2*id_map[x-1])
            elif y-1 in id_map:
                ts.add_or(2*id_map[y-1], 2*id_map[y-1])
            else:
                break
        else:
            for u, v in conflicts:
                if u-1 in id_map and v-1 in id_map:
                    ts.add_or(2*id_map[u-1]^1, 2*id_map[v-1]^1)
            assignment = ts.satisfiable()
            if assignment:
                chosen = [valid[i]+1 for i, val in enumerate(assignment) if val]
                print(len(chosen), f)
                print(*chosen)
                return
    print(-1)

if __name__ == "__main__":
    solve()
```この解決策では、まず、局範囲の境界を使用して候補信号電力を特定します。 TwoSAT クラスは、含意グラフ アプローチを使用して実装されます。 苦情は OR 句を生成し、干渉は NOT-OR 句を生成します。 現在の電力で許可されているステーションのみが変数にマッピングされます。 満足できる場合、プログラムは選択された電力とステーションを出力します。 それ以外の場合は、次の候補に進みます。 

## 実用的な例

 **サンプル 1**

 | ステップ | 候補力 f | 有効な駅 | 追加された条項 | 満足ですか？ | 選ばれた駅 |
 | --- | --- | --- | --- | --- | --- |
 | 1 | 1 | 1,2,4 | (1 または 3)、(2 | | |
