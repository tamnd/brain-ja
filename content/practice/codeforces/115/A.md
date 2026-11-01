---
title: "CF 115A - パーティー"
description: "私たちは、直接的または間接的に、グループにマネージャーとその部下の両方が含まれないように会社のパーティーを組織するように求められています。 入力は、各従業員の直属の上司を説明します。1 から n までの数字、または上司がいない場合は -1 です。"
date: "2026-05-28T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "dfs-and-similar", "graphs", "trees"]
categories: ["algorithms"]
codeforces_contest: 115
codeforces_index: "A"
codeforces_contest_name: "Codeforces Beta Round 87 (Div. 1 Only)"
rating: 900
weight: 115
solve_time_s: 111
verified: true
draft: false
---

[CF 115A - パーティー](https://codeforces.com/problemset/problem/115/A)

 **評価:** 900
 **タグ:** dfs および類似の、グラフ、ツリー
 **解決時間:** 1 分 51 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 私たちは、直接的または間接的に、グループにマネージャーとその部下の両方が含まれないように会社のパーティーを組織するように求められています。 入力は、各従業員の直属の上司を表します。1 から _n_ までの数字、または上司がいない場合は -1 です。 私たちの目標は、どのグループ内でも他の従業員より優れている従業員が存在しないように、必要なグループの最小数を計算することです。 

階層はツリーの森を形成します。マネージャーのいない各従業員はルートであり、他のすべての従業員はマネージャーにリンクします。 サイクルがないため、各従業員はルートまでのパスを 1 つだけ持ちます。 上司と部下の対立なしに従業員をグループ化する際の問題は、これらのツリー内のレベルの選択に帰着します。 ツリー内の同じ深さの従業員は、従業員間に上位/下位関係がないため、安全に同じグループに配置できます。 

制約 _n_ ≤ 2000 は、O(n²) アルゴリズムがかろうじて適合する可能性があるが、O(n) または O(n log n) が優先されることを示唆しています。 エッジ ケースには、すべての従業員が独立している (すべて -1) ことが含まれます。この場合、必要となるのは 1 つのグループだけです。または、グループ数がチェーンの長さに等しい従業員の単一の長いチェーンがあります。 不注意な実装では、フォレストではなく単一のツリーを想定し、一部のルートを見逃してしまう可能性があります。 

## アプローチ

 ブルートフォースアプローチでは、従業員を可能な限りすべてのグループに分割して、各グループ内の優れた関係をチェックします。 パーティションの数は指数関数的に増加するため、これは明らかに実行不可能です。 すべての従業員のペアをシミュレートし、貪欲にグループを割り当てようとしても O(n²) になる可能性があり、深さの計算で 1 つずつ誤差が生じる傾向があります。 

重要な洞察はツリー構造から得られます。 各ツリーの高さによって、そのツリーに必要なグループの最小数が決まります。これは、上位のチェーンが最長になると、競合を避けるために多くの個別のグループが必要になるためです。 したがって、問題は森林内の各木の高さを見つけて最大値を取ることに帰着します。 深さ優先検索 (DFS) は、ルートから開始して各ノードの深さを計算する自然な方法です。 各ルートの深さは 1 で、すべての子は親の深さに 1 を加えた深さになります。 

この観察により、組み合わせ問題が単純な走査問題に変換されます。 すべてのペアをチェックする代わりに、各ツリーを 1 回走査して最大の深さを追跡し、O(n) の解を与えます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(2ⁿ × n²) | O(n²) | 遅すぎる |
 | DFS 深さの計算 | O(n) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. _n_ と直属の管理者のリストを読みます。 必要に応じて、マネージャーのインデックスをゼロベースに変換します。 マネージャーの下にある各従業員を収集して、森林内の木を表す隣接リストを作成します。 
2. すべてのルートを特定します。 これらは、マネージャーが -1 である従業員です。 
3. 変数を初期化する`max_depth`ゼロに。 これは最も高い木の高さを保持します。 
4. 特定のノードについて、そのサブツリーの深さを計算する再帰的 DFS 関数を定義します。 リーフの深さは 1 です。子を持つノードの場合、深さは 1 にその子の最大の深さを加えたものになります。 
5. ルートごとに、DFS 機能を呼び出して更新します。`max_depth`返された深さが現在より大きい場合`max_depth`。 
6. すべての木を越えた後、`max_depth`必要なグループの最小数です。 この値を出力します。 

Why it works: By placing all employees at the same depth in the same group, we guarantee no superior-subordinate pairs exist in a single group. The longest chain in any tree dictates the number of groups because each level of that chain must be separated into distinct groups. DFS は木の高さを正確に計算するため、すべての木の最大の高さを取得すると、フォレスト全体のグループの最小数が得られます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline
sys.setrecursionlimit(3000)

n = int(input())
managers = [int(input()) - 1 for _ in range(n)]

# build the tree forest
children = [[] for _ in range(n)]
roots = []

for i in range(n):
    if managers[i] == -2:
        roots.append(i)
    else:
        children[managers[i]].append(i)

def dfs(node):
    if not children[node]:
        return 1
    depth = 0
    for child in children[node]:
        depth = max(depth, dfs(child))
    return depth + 1

max_depth = 0
for root in roots:
    max_depth = max(max_depth, dfs(root))

print(max_depth)
```便宜上、まず -1 を -2 に変換します。 次に、`children`リストどこ`children[i]`従業員 _i_ が直接管理するすべての従業員を保存します。 DFS 関数は、子の深さを再帰的に計算して 1 を加算することによって、ノードの深さを計算します。ルートごとに、その深さを計算し、最大値を追跡します。 ここで再帰を使用すると、ツリーの高さが最大 ​​_n_ となり、再帰の制限に適合するため便利です。 

## 実用的な例

 ### 例 1

 入力:```
5
-1
1
2
1
-1
```| ステップ | ノード | 子どもたち | 深度が返されました | 最大深度 |
 | --- | --- | --- | --- | --- |
 | DFS0 | 0 | [1,3] | 3 | 3 |
 | DFS1 | 1 | [2] | 2 | 3 |
 | DFS2 | 2 | [] | 1 | 3 |
 | DFS3 | 3 | [] | 1 | 3 |
 | DFS4 | 4 | [] | 1 | 3 |

 深さ 3 のルート 0 と深さ 1 のルート 4 の 2 つのツリーが表示されます。最も高いツリーには 3 つのグループが必要です。 

### 例 2

 入力:```
4
-1
-1
-1
-1
```従業員は全員独立しています。 

| ノード | 子どもたち | 深度が返されました | 最大深度 |
 | --- | --- | --- | --- |
 | DFS0 | [] | 1 | 1 |
 | DFS1 | [] | 1 | 1 |
 | DFS2 | [] | 1 | 1 |
 | DFS3 | [] | 1 | 1 |

 すべての従業員が 1 つのグループに所属できます。 アルゴリズムは正しく 1 を出力します。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n) | 各従業員は DFS で 1 回訪問されます |
 | スペース | O(n) | 子のリストと再帰スタックの保存 |

 このアルゴリズムは従業員数に対して線形時間で実行され、n ≤ 2000 の場合は 3 秒の制限内に問題なく収まります。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    sys.setrecursionlimit(3000)
    
    n = int(input())
    managers = [int(input()) - 1 for _ in range(n)]

    children = [[] for _ in range(n)]
    roots = []
    for i in range(n):
        if managers[i] == -2:
            roots.append(i)
        else:
            children[managers[i]].append(i)

    def dfs(node):
        if not children[node]:
            return 1
        depth = 0
        for child in children[node]:
            depth = max(depth, dfs(child))
        return depth + 1

    max_depth = 0
    for root in roots:
        max_depth = max(max_depth, dfs(root))
    return str(max_depth)

# provided sample
assert run("5\n-1\n1\n2\n1\n-1\n") == "3", "sample 1"

# custom cases
assert run("4\n-1\n-1\n-1\n-1\n") == "1", "all independent"
assert run("3\n-1\n1\n2\n") == "3", "single chain"
assert run("6\n-1\n1\n1\n2\n2\n-1\n") == "3", "multiple small trees"
assert run("1\n-1\n") == "1", "single employee"
assert run("2\n1\n-1\n") == "2", "two employees, manager first"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`4\n-1\n-1\n-1\n-1\n`| 1 | すべての独立した従業員 |
 |`3\n-1\n1\n2\n`| 3 | 単一チェーン、深さは n に等しい |
 |`6\n-1\n1\n1\n2\n2\n-1\n`| 3 | 複数の木、正しい最大高さ |
 |`1\n-1\n`| 1 | 最小入力サイズ |
 |`2\n1\n-1\n`| 2 | マネージャーが最初にリストされます。順序は関係ありません |

 ## 特殊なケース

 すべての従業員が独立している場合、DFS は各ルートに対して 1 を正しく返し、max_ Depth は正しい最小グループ数である 1 になります。 単一の長いチェーンでは、各従業員が個別のレベルを形成し、DFS はチェーンの長さを正しく計算します。 複数のツリーが存在する場合、アルゴリズムはすべてのルートにわたる最大の深さを追跡し、ツリーの高さが異なる場合でも正しいグループ数を確保します。
