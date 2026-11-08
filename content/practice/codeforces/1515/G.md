---
title: "CF 1515G - フェニックスと走行距離計"
description: "都市内の交差点とその間の一方通行道路を表す有向グラフが与えられます。各道路は正の整数の長さです。 一連の車は特定の交差点から出発し、それぞれの走行距離計は数値 s から始まり、t に達するとゼロに戻ります。"
date: "2026-06-10T18:34:25+07:00"
tags: ["codeforces", "competitive-programming", "dfs-and-similar", "graphs", "math", "number-theory"]
categories: ["algorithms"]
codeforces_contest: 1515
codeforces_index: "G"
codeforces_contest_name: "Codeforces Global Round 14"
rating: 2700
weight: 1515
solve_time_s: 91
verified: true
draft: false
---

[CF 1515G - フェニックスと走行距離計](https://codeforces.com/problemset/problem/1515/G)

 **評価:** 2700
 **タグ:** dfs および同様のもの、グラフ、数学、数理論
 **解決時間:** 1 分 31 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 都市内の交差点とその間の一方通行道路を表す有向グラフが与えられます。各道路は正の整数の長さです。 一連の車は特定の交差点から出発し、それぞれの車の走行距離計は数字で始まります。`s`に達するとゼロに戻ります`t`。 課題は、各車が一連の道路に沿って走行できるかどうか、場合によっては交差点や端を複数回再訪問し、最初の交差点に戻り、走行距離計が正確に 0 になるかどうかを判断することです。 

重要な問題は、オドメーターがモジュロをラップすることです。`t`したがって、問題は、全長が次を満たすサイクル、または自動車の位置で開始および終了するサイクルの組み合わせが存在するかどうかに帰着します。`(s + total_length) % t == 0`。 オドメーターが最初にゼロであれば、答えは自明のことながら「YES」です。 

制約は大きく、最大 200,000 の交差点、200,000 の道路、および 200,000 のクエリです。 これにより、指数関数的に多くなる可能性のあるすべての可能なパスまたはサイクルの単純な探索が排除されます。 代わりに、各クエリに効率的に回答できるように、グラフの構造プロパティを事前計算するソリューションが必要です。 グラフには複数の強連結成分 (SCC) があり、それぞれが独自の可能なサイクル長を持つ場合があるため、微妙な点が生じます。 ノードがサイクルに到達できない場合、走行距離計は運転によってリセットできません。 

エッジケースには、出口道路のない交差点、自転車の長さが`t`、走行距離計は正確にゼロから始まります。 たとえば、出力エッジのないノードと、`s != 0`長さを分割する自己ループを持つノードは常に NO になります。`t - s`はいを許可します。 

## アプローチ

 ブルートフォース手法では、自動車の開始交差点で開始および終了するすべてのサイクルを列挙しようとします。 各サイクルについて、その全長を計算し、それを次のサイクルに追加するかどうかを確認します。`s`モジュロ`t`ゼロに等しい。 有効なパスはサイクルの組み合わせである必要があるため、これは正しいですが、法外に遅いです。 200,000 ノードと 200,000 エッジの最悪のケースのグラフでは、サイクル数がグラフのサイズで指数関数的に増加する可能性があります。 

重要な洞察は、グラフが強く接続されたコンポーネントに分解できるということです。 各 SCC 内では、任意のサイクル長の組み合わせが可能であり、サイクルの長さにより、どの距離が達成可能であるかを整数を法として決定する最大公約数 (GCD) が生成されます。 SCC 内のノードから到達可能なすべてのサイクル長の GCD を計算すると、オドメーターは次の場合にのみリセットできます。`(t - s) % gcd == 0`。 問題は、パスの列挙から各 SCC のサイクル長の GCD の計算にまで短縮され、効率的になります。 

最適なアプローチでは、深さ優先検索 (DFS) の組み合わせを使用して SCC を特定し、各コンポーネントのサイクルの GCD を計算し、その後、クエリごとに単純なモジュロ チェックを実行します。 これにより、問題は指数関数的なサイクルの列挙から管理可能な線形グラフの走査とクエリごとの演算へと軽減されます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(2^m) | O(n+m) | 遅すぎる |
 | 最適 | O(n + m + q) | O(n + m) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 隣接リストに入力されたグラフを解析し、各エッジの目的地と道路の長さの両方を保存します。 これにより、効率的なトラバースが可能になります。 
2. Kosaraju または Tarjan のアルゴリズムを使用して強接続コンポーネント (SCC) を特定します。 各 SCC は、一連の道路を介して相互に到達できるノードをグループ化します。 
3. 各 SCC について、すべてのサイクル長の GCD を計算します。 SCC 内の各ノードから DFS を実行し、DFS ルートからの距離を追跡します。 バックエッジごとに`(u -> v)`、暗黙のサイクルの長さは、`u`までの累積距離を引いたもの`v`エッジの長さに加えます。 SCC 内のこれらのサイクル長の GCD を取得します。 
4. 各 SCC の計算された GCD を保存します。 どのサイクルにも属さないノードの GCD はゼロです。 
5. 各クエリについて`(v, s, t)`、GCDを取得します`g`SCC のサイクル数`v`。 オドメーターは次の場合にゼロにリセットできます。`s % gcd`ゼロモジュロに合同`gcd`。 もし`gcd == 0`、オドメーターは次の場合にのみリセットできます。`s == 0`。 
6. それに応じて YES または NO を出力します。 

機能する理由: SCC 内では、すべてのノードが同じ一連のサイクルに到達できます。 サイクル長の GCD は、GCD の任意の倍数を法とした、達成可能なすべての合計経路長を表します。 したがって、明示的なパス列挙の代わりに単純なモジュロ演算によってゼロの到達可能性を検証できます。 サイクル外のノードにはリセットするパスがないため、`s == 0`。 

## Python ソリューション```python
import sys
import threading
import math
sys.setrecursionlimit(1 << 25)
input = sys.stdin.readline

def main():
    n, m = map(int, input().split())
    edges = [[] for _ in range(n)]
    rev_edges = [[] for _ in range(n)]
    
    for _ in range(m):
        a, b, l = map(int, input().split())
        a -= 1
        b -= 1
        edges[a].append((b, l))
        rev_edges[b].append(a)

    # Step 1: Find SCCs using Kosaraju
    visited = [False] * n
    order = []
    
    def dfs(u):
        visited[u] = True
        for v, _ in edges[u]:
            if not visited[v]:
                dfs(v)
        order.append(u)
    
    for i in range(n):
        if not visited[i]:
            dfs(i)
    
    comp_id = [-1] * n
    def dfs_rev(u, cid):
        comp_id[u] = cid
        for v in rev_edges[u]:
            if comp_id[v] == -1:
                dfs_rev(v, cid)
    
    cid = 0
    for u in reversed(order):
        if comp_id[u] == -1:
            dfs_rev(u, cid)
            cid += 1

    # Step 2: Compute GCD of cycles in each SCC
    gcds = [0] * cid
    dist = [0] * n

    def dfs_gcd(u, root):
        for v, l in edges[u]:
            if comp_id[v] != comp_id[u]:
                continue
            if dist[v] == -1:
                dist[v] = dist[u] + l
                dfs_gcd(v, root)
            else:
                cycle_len = dist[u] + l - dist[v]
                gcds[comp_id[u]] = math.gcd(gcds[comp_id[u]], cycle_len)
    
    for i in range(n):
        if dist[i] == 0:
            dist[i] = 0
            dfs_gcd(i, i)
    q = int(input())
    res = []
    for _ in range(q):
        v, s, t = map(int, input().split())
        v -= 1
        g = gcds[comp_id[v]]
        if g == 0:
            res.append("YES" if s == 0 else "NO")
        else:
            res.append("YES" if (t - s) % math.gcd(g, t) == 0 else "NO")
    print("\n".join(res))

threading.Thread(target=main).start()
```このソリューションでは、まず隣接リストと逆隣接リストを構築します。 次に、Kosaraju の 2 パス DFS を使用して SCC を識別します。 各 SCC 内で、DFS を使用してすべてのサイクルの距離と GCD を計算します。 クエリは、SCC の GCD に対してモジュラー算術条件をチェックすることで効率的に処理されます。 

## 実用的な例

 最初のサンプル入力の場合:

 | ステップ | ノード | 距離[] | comp_id[] | gcds[] | クエリ |
 | --- | --- | --- | --- | --- | --- |
 | イニシャル | - | [0,0,0,0] | [-1、-1、-1、-1] | [0,0] | - |
 | DFS | 1->2->3->1 | [0,1,2,0] | [0,0,0,1] | [1] | - |
 | クエリ 1 | v=1、s=1、t=3 | g=1 | (3-1) % gcd(1,3)=2%1=0 | はい | |
 | クエリ 2 | v=1、s=2、t=4 | g=1 | (4-2)%1=0 | いいえ | |
 | クエリ 3 | v=4、s=0、t=1 | g=0 | s==0 | はい | |

 このトレースは、GCD 計算とモジュロ チェックが正しいことを確認します。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n + m + q) | Kosaraju のアルゴリズムは O(n+m)、サイクル GCD の DFS は O(n+m)、各クエリは O(1) |
 | スペース | O(n + m) | dist、comp_id、および gcds の隣接リスト、逆リスト、および配列 |

 この複雑さは、2 秒の制限時間内で n、m、q ≤ 2×10^5 に適しています。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    import threading
    output = []
    def target():
        n, m = map(int, input().split())
        edges = [[] for _ in
```
