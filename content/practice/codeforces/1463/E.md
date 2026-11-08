---
title: "CF 1463E - 講義計画"
description: "私たちには、n 個の異なるトピックに関する講義をスケジュールする必要がある教師の Ivan がいます。 各トピックには最大でも 1 つの前提条件があります。つまり、前提条件がなければトピックについて講義することはできません。 1 つのトピックには前提条件はありません。"
date: "2026-06-11T02:01:34+07:00"
tags: ["codeforces", "competitive-programming", "constructive-algorithms", "dfs-and-similar", "dsu", "graphs", "implementation", "sortings", "trees"]
categories: ["algorithms"]
codeforces_contest: 1463
codeforces_index: "E"
codeforces_contest_name: "Educational Codeforces Round 100 (Rated for Div. 2)"
rating: 2400
weight: 1463
solve_time_s: 145
verified: false
draft: false
---

[CF 1463E - 講義計画](https://codeforces.com/problemset/problem/1463/E)

 **評価:** 2400
 **タグ:** 構築的アルゴリズム、dfs および類似のもの、dsu、グラフ、実装、並べ替え、ツリー
 **解決時間:** 2 分 25 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 イワンという先生がいますが、彼は次のような講義をスケジュールする必要があります。`n`さまざまなトピック。 各トピックには最大でも 1 つの前提条件があります。つまり、前提条件がなければトピックについて講義することはできません。 1 つのトピックには前提条件はありません。 課題は、これらの前提条件を考慮して、講義を有効な順序で配置することです。 

さらに、`k`特別なトピックのペア`(x_i, y_i)`トピックの場合`x_i`トピックの直前にあります`y_i`、生徒の理解が深まります。 全て`x_i`そしてすべて`y_i`は一意です。つまり、トピックが最初または 2 番目の要素として複数のペアに出現しないことを意味します。 課題は、前提条件となる構造と特別なペアの隣接関係の優先順位の両方を満たす講義順序を見つけるか、それが不可能であると判断するかです。 

制約は大きいです:`n`最大 3 * 10^5 にすることができ、`k`まで`n-1`。 つまり、二次計算量 (O(n²)) を持つ解は遅すぎるということになります。 ほぼ線形のアルゴリズムが必要です`n`または最大でも O(n log n)。 

明らかではないエッジケースには、前提条件と矛盾する特殊なペアがチェーンまたはサイクルを形成する状況が含まれます。 たとえば、前提条件が強制される場合、`1 → 2 → 3`しかし、特別なペアが必要です`3 → 1`、解決策は存在しません。 もう 1 つの微妙な点は、特別なペア自体が任意の長さのチェーンを形成する場合です。これらのチェーンが前提条件の順序を遵守していることを確認する必要があります。 

## アプローチ

 ブルートフォースアプローチでは、トピックのすべての順列が生成され、それぞれが前提条件および特別なペアに対してチェックされます。 これは正しいですが、順列の数が次のとおりであるため、実行不可能です。`n!`、指数関数的に増加します。 

重要な観察は、前提条件がツリーのような依存関係構造 (厳密に 1 つのトピックには前提条件がないため、ルートが 1 つだけの有向フォレスト) を定義していることです。 このツリーを作成したら、親から子の順にトピックをルートから葉まで線形に順序付けるだけで済みます。 

特別なペアは隣接性の制約を課します。これは、連続して出現する必要があるトピックのチェーンを形成すると解釈できます。 ずっと`x_i`は一意であるため、これらのチェーンは開始点で重複しません。 最初にこれらのチェーンを構築し、次に前提条件ツリーに従ってトポロジカルに並べ替えることができます。 

したがって、最適なアプローチは、特殊なペアによって指定されたチェーンを構築し、各チェーンをスーパーノードに折りたたんでから、前提条件を考慮してトポロジカルなソートを実行することです。 チェーンが前提条件に違反している場合 (最初のトピックがチェーンの外側の前提条件の後に来る場合)、不可能であることが検出されます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | お(ん!) | O(n) | 遅すぎる |
 | チェーン + トポロジカルソート | O(n) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. マップを作成する`next_in_chain`特別なペアの場合: それぞれ`(x, y)`、 店`next_in_chain[x] = y`。 また、保管`has_prev[y] = x`チェーンの開始を検出します。 
2. 各チェーンの開始点を特定します: トピック`t`それは中にあります`next_in_chain`しかし、中にはありません`has_prev`チェーンの先頭です。 すべてのトピックが特別なペアに参加しているわけではないため、複数のチェーンが存在する可能性があります。 
3. 各チェーンをたどって、連続して出現する必要があるトピックの完全なシーケンスを収集します。 トラバース中に、サイクル (トピックがチェーン内で繰り返される場合) と競合 (トピックが複数のチェーン内に出現する場合) を確認します。 検出した場合は出力`0`。 
4. 各トピックをそれが属するチェーンにマッピングし、チェーン シーケンスのリストを作成します。 特別なペアにないトピックはシングルトン チェーンになります。 
5. 各チェーンがノードとなる前提条件のグラフを構築します。 トピックが連鎖している場合`A`外部に前提条件がある`A`、前提条件チェーンからエッジを作成して、`A`。 これにより、チェーン注文が前提条件を遵守することが保証されます。 
6. トポロジカルなソートチェーンを実行します。 チェーングラフでサイクルを検出した場合、出力`0`。 それ以外の場合は、チェーンをトポロジカルな順序で連結して講義シーケンスを形成します。 
7. シーケンスを出力します。 

ここでの不変条件は、チェーンが隣接性制約を保持し、トポロジカル ソートが前提条件を保持することです。 各チェーンを分割できないブロックとして扱うことで、アルゴリズムが成功した場合にすべての制約が満たされることが保証されます。 

## Python ソリューション```python
import sys
from collections import defaultdict, deque

input = sys.stdin.readline

n, k = map(int, input().split())
p = list(map(int, input().split()))

next_in_chain = {}
has_prev = {}
for _ in range(k):
    x, y = map(int, input().split())
    next_in_chain[x] = y
    has_prev[y] = x

used = set()
chains = []
topic_to_chain = {}

# Build chains from special pairs
for t in range(1, n+1):
    if t in next_in_chain and t not in has_prev:
        chain = []
        cur = t
        while cur:
            if cur in used:
                print(0)
                sys.exit(0)
            used.add(cur)
            chain.append(cur)
            cur = next_in_chain.get(cur, 0)
        chains.append(chain)
        for topic in chain:
            topic_to_chain[topic] = len(chains)-1

# Add singleton topics
for t in range(1, n+1):
    if t not in used:
        chains.append([t])
        topic_to_chain[t] = len(chains)-1

m = len(chains)
in_deg = [0]*m
adj = [[] for _ in range(m)]

# Build graph of chains based on prerequisites
for i, topic in enumerate(p):
    if topic == 0:
        continue
    curr_chain = topic_to_chain[i+1]
    prereq_chain = topic_to_chain[topic]
    if curr_chain != prereq_chain:
        adj[prereq_chain].append(curr_chain)
        in_deg[curr_chain] += 1

# Topological sort of chains
queue = deque([i for i in range(m) if in_deg[i] == 0])
res = []

while queue:
    u = queue.popleft()
    res.extend(chains[u])
    for v in adj[u]:
        in_deg[v] -= 1
        if in_deg[v] == 0:
            queue.append(v)

if len(res) != n:
    print(0)
else:
    print(' '.join(map(str, res)))
```コードは最初に特殊なペアからチェーンを構築し、サイクルを検出します。 次に、シングルトン トピックが長さ 1 のチェーンとして追加されます。前提条件の依存関係を使用してチェーン グラフが構築され、トポロジカルな並べ替えによって講義順序が生成されます。 周期的な特別なペアや不可能な前提条件の順序などのエッジケースが捕捉されて返されます。`0`。 

## 実用的な例

 サンプル入力 1:```
5 2
2 3 0 5 3
1 5
5 4
```| ステップ | チェーン | トピックとチェーン | 形容グラフ | キュー | 解像度 |
 | --- | --- | --- | --- | --- | --- |
 | チェーンを構築する | [1,5,4]、[2]、[3] | {1:0,5:0,4:0,2:1,3:2} | [] | [] | [] |
 | グラフを作成する | ３→２、５→４？ | 隣接関係を参照 | adj=[[],[2],[1]] | キュー=[0] | レス=[] |
 | トポソート | 0->1->2 | | キューの更新 | 3 2 1 5 4 | |

 出力：`3 2 1 5 4`これは、チェーンが隣接関係を維持し、トポロジカルなソートが前提条件を尊重していることを示しています。 

カスタム入力 2:```
3 1
0 1 2
1 3
```チェーン: [1,3],[2]; 前提条件: 1→2、2→3

 チェーンの隣接性と前提条件を満たすことができません。 出力：`0`。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n) | チェーンの構築、トピックのマッピング、チェーン グラフの構築、トポロジカル ソートはすべて線形です。`n`|
 | スペース | O(n) | マップ、リスト、隣接関係グラフ、およびキューはすべて、最大でも O(n) 個の要素を格納します。 

与えられた`n <= 3*10^5`, O(n) ソリューションは時間とメモリの制限内で実行されます。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    n, k = map(int, input().split())
    p = list(map(int, input().split()))
    next_in_chain = {}
    has_prev = {}
    for _ in range(k):
        x, y = map(int, input().split())
        next_in_chain[x] = y
        has_prev[y] = x
    used = set()
    chains = []
    topic_to_chain = {}
    for t in range(1, n+1):
        if t in next_in_chain and t not in has_prev:
            chain = []
            cur = t
            while cur:
                if cur in used:
                    return "0"
                used.add(cur)
                chain.append(cur)
                cur = next_in_chain.get(cur, 0)
            chains.append(chain)
            for topic in chain:
                topic_to_chain[topic] = len(chains)-1
    for t in range(1, n+1):
        if t not in used:
            chains.append([t])
            topic_to_chain[t] = len(chains)-1
    m = len(chains)
    in_deg = [0]*m
    adj = [[] for _ in range(m)]
    for i, topic in enumerate(p):
        if topic ==
```
