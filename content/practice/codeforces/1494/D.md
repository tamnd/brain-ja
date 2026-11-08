---
title: "CF 1494D - ドージフォース"
description: "部分的な給与情報から会社の階層を再構築するように求められます。 具体的には、すべての最下位レベルの従業員の給与と、各従業員のペアごとに、その最下位の共通上司の給与がわかります。"
date: "2026-06-10T22:10:48+07:00"
tags: ["codeforces", "competitive-programming", "constructive-algorithms", "data-structures", "dfs-and-similar", "divide-and-conquer", "dsu", "greedy", "sortings", "trees"]
categories: ["algorithms"]
codeforces_contest: 1494
codeforces_index: "D"
codeforces_contest_name: "Educational Codeforces Round 105 (Rated for Div. 2)"
rating: 2300
weight: 1494
solve_time_s: 162
verified: false
draft: false
---

[CF 1494D - ドージフォース](https://codeforces.com/problemset/problem/1494/D)

 **評価:** 2300
 **タグ:** 建設的アルゴリズム、データ構造、dfs および同様のもの、分割統治、dsu、貪欲、ソート、ツリー
 **解決時間:** 2 分 42 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 部分的な給与情報から会社の階層を再構築するように求められます。 具体的には、すべての最下位レベルの従業員の給与と、各従業員のペアごとに、その最下位の共通上司の給与がわかります。 階層はツリー構造になっており、リーフ以外の各従業員には少なくとも 2 人の部下がおり、すべての上司には直属の部下全員よりも厳密に高い給与が与えられます。 会社のトップは木の根です。 私たちの目標は、このツリーを再構築し、リーフ以外の従業員を含むすべての従業員に番号を割り当て、上司との関係とともに給与を出力することです。 

入力により提供されるのは、$n \times n$行列$a_{i,j}$下位レベルの従業員の最も低い共通上司の給与です。$i$そして$j$。 対角線$a_{i,i}$従業員の給料です$i$自体。 制約は中程度です。$n$は最大 500、給与は最大 5000 の整数です。これは、$O(n^3)$アプローチはかろうじて適合するかもしれませんが、$O(n^4)$遅すぎるでしょう。 

明らかではないエッジケースには、複数のペアが同じ監督者の給与を共有する状況が含まれます。 各ペアを個別に接続する単純なアプローチでは、重複したノードまたはサイクルが作成される可能性があります。 たとえば、従業員が 3 人の場合、$i, j, k$持っている$a_{i,j} = a_{i,k} = a_{j,k}$、アルゴリズムは、3 つの個別のスーパーバイザーではなく、単一のスーパーバイザーを共有していることを認識する必要があります。 

## アプローチ

 強引なアプローチは、考えられるすべてのツリー構造を試し、マトリックスに対して監督者の給与を検証することです。 のために$n=500$、木の数が超指数関数的に増加するため、これは明らかに実行不可能です。 

重要な洞察は、これが「最大リンケージ」または「完全リンケージ」基準を使用して階層クラスタリング ツリー (樹状図) を構築するのと同等であるということです。この場合、監督者の給与は、従業員のグループを結び付けることができる最小の給与に相当します。 すべての固有の給与を昇順に並べ替えることで、従業員をその給与を持つ共通の上司の下にあるグループに徐々に統合することができます。 給与でグループが形成されると$s$、より少ない給与でグループと合併することはできないため、厳しく増加する監督者の条件を確実に尊重します。 

したがって、最適なアプローチは、建設的な共用体検索スタイルのアルゴリズムです。 各下位レベルの従業員は独自のノードとして開始します。 すべてのペアを考慮します$(i,j)$スーパーバイザーの給与が増加する順に、ペアが別のグループに属する場合は常に、指定された給与を持つ新しいスーパーバイザー ノードの下にそれらをマージします。 給与を最低額から最高額まで繰り返すことで、部下の給与が常に上司よりも低いことが保証されます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | お(ん!) | O(n) | 遅すぎる |
 | 構成的樹状図 / Union-Find | O(n^3) | O(n^2) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 各下位レベルの従業員を初期化します$1$に$n$Union-find 構造内のシングルトン セットとして。 マトリックスの対角線で与えられる給与を割り当てます。 
2.すべてのペアを集める$(i,j)$上司の給料で$a_{i,j}$。 給与の昇順でペアを並べ替えます。 これにより、ツリーのより深いレベルに対応する、より少ない給与を最初に処理することが保証されます。 
3. から始まる新しい従業員番号を割り当てるためのカウンタを初期化します。$n+1$監督者向け。 
4. ペアごとに$(i,j)$監督者の給料が上がる順に：

 - 現在のルートの代表者を見つける$i$そして$j$。 
・代表者が異なる場合：

 - どちらの給与も同等でない場合$a_{i,j}$、給与を含む新しいスーパーバイザー ノードを作成します。$a_{i,j}$そして、この新しいノードの下で 2 つのセットをマージします。 
- 代表者のうちの1人がすでに給与を受け取っている場合$a_{i,j}$、その下に他のセットをマージします。 
- グループ化を効率的に維持するには、union-find を使用します。 
5. すべてのペアを処理した後、1 つのノードだけがマージされずに残ります。 このノードは会社の責任者です。 
6. 給与と監督者の関係の最終リストを収集します。 

機能する理由: 各ステップで、最低共通スーパーバイザーが指定された給与と一致するグループのみをマージします。 昇給順に並べ替えることで、すべての合併で上司の方が部下よりも多くの収入を得られるという条件が確実に尊重されます。 Union-find は、サイクルが導入されないことを保証します。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

n = int(input())
a = [list(map(int, input().split())) for _ in range(n)]

parent = list(range(2*n))  # union-find parent
salary = [0]*(2*n)
edges = []

for i in range(n):
    salary[i] = a[i][i]

next_node = n

def find(x):
    while parent[x] != x:
        parent[x] = parent[parent[x]]
        x = parent[x]
    return x

pairs = []
for i in range(n):
    for j in range(i+1, n):
        pairs.append((a[i][j], i, j))
pairs.sort()

for s, u, v in pairs:
    ru, rv = find(u), find(v)
    if ru == rv:
        continue
    if salary[ru] == s:
        parent[rv] = ru
        edges.append((rv+1, ru+1))
    elif salary[rv] == s:
        parent[ru] = rv
        edges.append((ru+1, rv+1))
    else:
        salary[next_node] = s
        parent[ru] = next_node
        parent[rv] = next_node
        edges.append((ru+1, next_node+1))
        edges.append((rv+1, next_node+1))
        next_node += 1

k = next_node
roots = [i for i in range(k) if parent[i]==i]
head = roots[0]+1

print(k)
print(' '.join(str(salary[i]) for i in range(k)))
print(head)
for u,v in edges:
    print(u,v)
```このソリューションでは、まず従業員とその給与を初期化します。 グループを結合するための Union-Find 構造を作成します。 給与ペアをソートすると、上司の順序に従ってマージが行われるようになります。 新しいスーパーバイザが必要な場合、新しいノード番号を段階的に割り当てます。 最後の頭は残りの根です。 

## 実用的な例

 サンプル 1 入力:```
3
2 5 7
5 1 7
7 7 4
```| ステップ | ペア | 給与 | 代表者 | アクション | エッジ |
 | --- | --- | --- | --- | --- | --- |
 | 1 | (1,2) | 5 | 1,2 | 新しいノード 4 の下にマージ | 1->4、2->4 |
 | 2 | (1,3) | 7 | 4,3 | 新しいノード 5 の下にマージ | 4->5,3->5 |
 | 3 | (2,3) | 7 | 5,5 | 同じグループ | スキップ |

 最終的なノード数: 5

 エッジ: 1-4、2-4、4-5、3-5

 ヘッド: 5

 このトレースは、最小の監督者の給与が最初に新しいノードを作成し、マージでは行列の制約が尊重されることを示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n^3) | n 人の従業員 (≈ n^2/2) のすべてのペアを処理し、各結合検索操作はほぼ一定です。 
| スペース | O(n^2) | 給与行列と共用体検索配列の保存 |

 n ≤ 500 の場合、n^3 ≈ 1.25e8 の操作は 2 秒以内に許容されます。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    import builtins
    output = io.StringIO()
    sys.stdout = output
    # call the main code
    n = int(input())
    a = [list(map(int, input().split())) for _ in range(n)]

    parent = list(range(2*n))
    salary = [0]*(2*n)
    edges = []

    for i in range(n):
        salary[i] = a[i][i]

    next_node = n

    def find(x):
        while parent[x] != x:
            parent[x] = parent[parent[x]]
            x = parent[x]
        return x

    pairs = []
    for i in range(n):
        for j in range(i+1, n):
            pairs.append((a[i][j], i, j))
    pairs.sort()

    for s, u, v in pairs:
        ru, rv = find(u), find(v)
        if ru == rv:
            continue
        if salary[ru] == s:
            parent[rv] = ru
            edges.append((rv+1, ru+1))
        elif salary[rv] == s:
            parent[ru] = rv
            edges.append((ru+1, rv+1))
        else:
            salary[next_node] = s
            parent[ru] = next_node
            parent[rv] = next_node
            edges.append((ru+1, next_node+1))
            edges.append((rv+1, next_node+1))
            next_node += 1

    k = next_node
    roots = [i for i in range(k) if parent[i]==i]
    head = roots[0]+1

    print(k)
    print(' '.join(str(salary[i]) for i in range(k)))
    print(head
```
