---
title: "CF 1615D - X(or)-mas ツリー"
description: "n 個のノードと n-1 個のエッジを持つツリーが与えられます。各エッジにはライトを表す非負の整数、または値が不明な場合は -1 を格納できます。"
date: "2026-06-10T06:41:35+07:00"
tags: ["codeforces", "competitive-programming", "bitmasks", "dfs-and-similar", "dsu", "graphs", "trees"]
categories: ["algorithms"]
codeforces_contest: 1615
codeforces_index: "D"
codeforces_contest_name: "Codeforces Global Round 18"
rating: 2200
weight: 1615
solve_time_s: 206
verified: false
draft: false
---

[CF 1615D - X(or)-mas ツリー](https://codeforces.com/problemset/problem/1615/D)

 **評価:** 2200
 **タグ:** ビットマスク、dfs および類似のもの、dsu、グラフ、ツリー
 **解決時間:** 3 分 26 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 私たちに与えられた木は、`n`ノードと`n-1`エッジ。各エッジにはライトを表す非負の整数を格納できます。または`-1`値が不明な場合。 数人のエルフが、関心のある 2 つのノードと、これらのノードを接続する単純なパスに沿ったエッジ値の XOR で設定されたビット数のパリティを教えてくれました。 私たちの仕事は、すべてのエルフの記憶が一貫しているように、未知のエッジへの整数の割り当てが存在するかどうかを判断することです。 可能であれば、1 つの有効な割り当てを再構築する必要があります。 

入力サイズはかなり大きくなります: 最大`2*10^5`テスト ケースごとのノードとエルフ、最大`2*10^4`テストケース。 クエリごとにすべてのパスの XOR を計算する単純なアプローチでは、クエリごとに O(n) となり、非常に遅すぎます。 したがって、ツリー構造のプロパティを活用し、パス XOR クエリを効率的に伝播できるローカル制約に減らす必要があります。 この制約は、エルフが完全な XOR 値ではなくパリティを報告するため、ビット単位のプロパティがモジュロ 2 のみに重要であることも示唆しています。 

エッジ ケースには、すべてのエッジ値が不明であるか、既知の値がすでにエルフ メモリと競合しているツリーが含まれます。 たとえば、エッジのある 3 つのノードからなる単純なツリーの場合、`(1,2,-1)`、`(2,3,0)`そしてクエリ`(1,3,0)`パリティを満たすために未知のエッジを 0 として割り当てる必要がありますが、クエリにパリティ 1 がある場合、有効な割り当てはありません。 

## アプローチ

 ブルートフォース手法では、エッジに対して可能なすべての割り当てを列挙します。`-1`、各クエリのパス XOR を計算し、パリティをチェックします。 ある木については、`k`未知のエッジ、あります`2^k`パリティのみの組み合わせ。 と`k`2*10^5 になる可能性がありますが、これは完全に不可能です。 

重要な洞察は、ツリー内のパスに沿った XOR 制約が GF(2) (バイナリ フィールド) 上の線形システムを定義するということです。 各エルフ メモリはパスのパリティ方程式を提供します。 ツリー エッジの割り当ては、ルートを使用してノード値に変換されます。`xor_to_root[u]`ルートからノードまでのエッジの XOR として`u`。 次に、パスの XOR`u-v`は`xor_to_root[u] XOR xor_to_root[v]`。 各クエリ`u-v-p`一次方程式になります`xor_to_root[u] XOR xor_to_root[v] = p`。 エッジの既知の値は、エンドポイントの相対 XOR を制約します。 これらすべての制約は、2 色分け問題として表すことができます (制約に 0 または 1 を割り当てます)。`xor_to_root[u]`これは、DFS または Union-find で解決できます。 競合が発生した場合 (2 つのノードの XOR が競合している必要があります)、割り当ては不可能になります。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(2^k * n * m) | O(n) | 遅すぎる |
 | 最適 (XOR パリティ伝播) | O(n + m) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. ツリーのエッジを読み取り、既知の値を持つものと未知の値を分離します。 既知の値をバイナリ パリティ (偶数セット ビットの場合は 0、奇数の場合は 1) に変換します。 
2. ツリーの隣接リストを作成します。 既知のパリティを持つエッジの場合は、エンドポイント間の XOR パリティ制約を記録します。 
3. 各ノードを変数としてモデル化します。`xor_to_root[node]`モジュロ 2。既知のパリティを持つ各エッジは次のようになります。`xor_to_root[u] XOR xor_to_root[v] = edge_parity`。 
4. Elf クエリを同様に処理します: 各クエリ`(u,v,p)`与える`xor_to_root[u] XOR xor_to_root[v] = p`。 これらをエッジ制約と組み合わせて、union-find または DFS カラーリング構造を作成します。 
5. DFS を使用してツリーを走査します。 各連結成分について、ルートに色 (0 または 1) を任意に割り当て、制約に従って色を伝播します。 競合が発生した場合 (ノードに 0 と 1 の両方が割り当てられている場合)、NO を返します。 
6. すべての制約が競合することなく処理された後、エッジ値を再構築します。 未知のエッジの場合は、次のように割り当てます。`xor_to_root[u] XOR xor_to_root[v]`エッジパリティとして（そのパリティに一致する任意の有効な整数で十分です）。 
7. YES を出力し、続いて再構築されたエッジを出力します。 

機能する理由: ツリー構造により、ノードの任意のペア間に単純なパスが 1 つだけ存在することが保証されるため、各パリティ制約はノードの XOR 値を関連付ける GF(2) の線形方程式に対応します。 DFS を介して割り当てを伝達すると、接続されたコンポーネント内のすべての制約が一貫して満たされるか、競合が検出されることが保証されます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline
sys.setrecursionlimit(1 << 25)

def solve():
    t = int(input())
    for _ in range(t):
        n, m = map(int, input().split())
        edges = []
        adj = [[] for _ in range(n+1)]
        unknown_edges = []
        for _ in range(n-1):
            x, y, v = map(int, input().split())
            edges.append([x, y, v])
            adj[x].append((y, len(edges)-1))
            adj[y].append((x, len(edges)-1))
            if v != -1:
                v_parity = bin(v).count('1') % 2
                edges[-1][2] = v_parity
            else:
                unknown_edges.append(len(edges)-1)

        uf_parent = [i for i in range(n+1)]
        color = [None]*(n+1)

        def find(u):
            if uf_parent[u] != u:
                uf_parent[u] = find(uf_parent[u])
            return uf_parent[u]

        def union(u, v, w):
            ru, rv = find(u), find(v)
            if ru == rv:
                if (color[u] ^ color[v]) != w:
                    return False
                return True
            if color[ru] is None:
                color[ru] = 0
            if color[rv] is None:
                color[rv] = 0
            parity = color[u] ^ color[v] ^ w
            uf_parent[rv] = ru
            color[rv] = parity
            return True

        constraints = []
        for idx, (x, y, v) in enumerate(edges):
            if v != -1:
                constraints.append((x, y, v))
        for _ in range(m):
            a, b, p = map(int, input().split())
            constraints.append((a, b, p))

        possible = True
        for u, v, p in constraints:
            if color[u] is None:
                color[u] = 0
            if color[v] is None:
                color[v] = 0
            if not union(u, v, p):
                possible = False
                break

        if not possible:
            print("NO")
            continue

        # Assign final edge values
        def dfs(u, par):
            for v, idx in adj[u]:
                if v == par:
                    continue
                if edges[idx][2] == -1:
                    edges[idx][2] = color[u] ^ color[v]
                dfs(v, u)

        for i in range(1, n+1):
            if color[i] is None:
                color[i] = 0
                dfs(i, -1)
                break
        dfs(1, -1)

        print("YES")
        for x, y, v in edges:
            print(x, y, v)

if __name__ == "__main__":
    solve()
```このコードは、まずすべてのエッジ値をパリティに変換し、隣接リストを構築します。 次に、ユニオン検索とカラーリング手法を使用して XOR 制約を伝播し、競合が発生しないようにします。 一貫性を検証した後、ノードに割り当てられた色を使用して DFS によって未知のエッジを再構築します。 

## 実用的な例

 ### サンプル 1

 入力:```
6 5
1 2 -1
1 3 1
4 2 7
6 3 0
2 5 -1
2 3 1
2 5 0
5 6 1
6 1 1
4 5 1
```| ノード | 色 | コメント |
 | --- | --- | --- |
 | 1 | 0 | 根元の色 |
 | 2 | 0 | エッジ 1-2 パリティ不明 |
 | 3 | 1 | エッジ 1-3 パリティ 1 |
 | 4 | 1 | エッジ 2-4 パリティ 1 |
 | 5 | 0 | エッジ 2-5 パリティが不明 |
 | 6 | 1 | エッジ 3-6 パリティ 0 |

 再構築されたエッジ パリティはすべての elf 制約を満たします。 

### サンプル 2

 入力:```
3 3
1 2 -1
1 3 -1
1 2 0
1 3 1
2 3 0
```DFS の伝播により、パス パリティ間の競合が明らかになります。 出力は「NO」です。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n + m) | テスト ケースごとの DFS 伝播と共用体検索は、ノードとクエリで線形にスケールします。 
| スペース | O(n + m) | 隣接リスト、色、および共用体検索配列 |

 これは、最大の制約があっても 2 秒の制限内に快適に収まります。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    from contextlib import redirect_stdout
    f = io.StringIO()
    with redirect_stdout(f):
        solve()
    return f.getvalue().strip()

# Provided samples
assert run
```
