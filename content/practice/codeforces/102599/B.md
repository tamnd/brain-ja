---
title: "CF 102599B - \u041b\u0438\u043f\u0435\u0446\u043a\u043e\u0435 \u043c\u0435\u0442\u0440\u043e"
description: "N 駅が含まれる地下鉄の地図が渡されます。 各ステーションは、接続されている他のステーションを最大 1 つ指定できます。 p[i] が -1 でない場合、ステーション i とステーション p[i] の間に無向トンネルが存在します。"
date: "2026-07-31T16:38:39+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102599
codeforces_index: "B"
codeforces_contest_name: "The fifth Lipetsk collegiate programming contest. Finals. 8-11 form"
rating: 0
weight: 102599
solve_time_s: 122
verified: true
draft: false
---

[CF 102599B - \u041b\u0438\u043f\u0435\u0446\u043a\u043e\u0435 \u043c\u0435\u0442\u0440\u043e](https://codeforces.com/problemset/problem/102599/B)

 **評価:** -
 **タグ:** -
 **解決時間:** 2 分 2 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 メトロマップを渡されました。`N`駅。 各ステーションは、接続されている他のステーションを最大 1 つ指定できます。 もし`p[i]`ではありません`-1`、駅の間に方向のないトンネルがあります`i`そして駅`p[i]`。 このタスクは、トンネルのみを通過して、すべての駅を正確に 1 回訪れるルートが存在するかどうかを判断することです。 そのような路線が存在する場合は、駅順を出力する必要があります。 

グラフは特殊な構造になっています。 各ステーションは最大でも 1 つのエッジに寄与するため、トンネルの総数は最大でも`N`。 一般的なハミルトニアン経路問題は難しいですが、最大でも次のようなグラフを作成できます。`N`edges は、ツリー、または追加のエッジが 1 つあるツリーで構成されるグラフのいずれかです。 この制限により、線形ソリューションが可能になります。 

と`N`まで`2 * 10^5`、異なる開始点、順列、または指数関数的なバックトラッキングを試みるソリューションは不可能です。 に近いアルゴリズムが必要です`O(N)`なぜなら、制限時間内に無理なく収まる操作は数百万件だけだからです。 

危険なケースは、切断されたグラフだけではありません。 ステーションを再訪問せずに分岐ツリーを通過できないため、接続されたグラフでも失敗する可能性があります。 例えば：```
4
2 1 1 -1
```グラフは`3-1-2`追加の葉っぱ付き`4`に付いている`1`。 すべての駅を訪れるルートでは駅に入る必要があります`1`3回も、それは不可能です。 正しい答えは次のとおりです。```
NO
```もう 1 つの注意が必要なケースは、複数の分岐があるサイクルです。 サイクルだけでも機能しますが、多くの分岐が接続されると、ハミルトニアン パスに十分な自由端がなくなります。 

## アプローチ

 強引なアプローチでは、可能な限り次の駅を選択して、駅ごとにルートを構築しようとします。 これは、可能なすべてのハミルトニアン パスを列挙するため正しいですが、可能性の数は階乗的に増加します。 頂点が数十しかないグラフであっても、このアプローチは使用できません。 

有益な観察は、グラフが非常にまばらであるということです。 最大で接続されたグラフ`N`edges はツリーまたは単環式グラフのいずれかです。 ツリー内のハミルトニアン パスは、ツリー自体が単純なパスである場合にのみ可能です。 単循環グラフでは、サイクルによって柔軟性が得られますが、接続されたツリーも単純なパスである必要があり、ルートのエンドポイントに対して十分な分岐しか存在できません。 

解決策は、グラフを分類し、サイクルが存在する場合はそれを抽出し、ハミルトニアン パスの可能な唯一の形状を構築することです。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | お(ん!) | O(N) | 遅すぎる |
 | 最適 | O(N) | O(N) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 指定された接続から無向グラフを構築し、エッジをカウントします。 単一のルートは切断されたパーツ間を横断できないため、最初にすべてのステーションが 1 つの接続されたコンポーネントに属していることを確認します。 
2. グラフに`N - 1`端、それは木です。 ツリーは、すべての頂点の次数が最大 2 である場合にのみ、ハミルトニアン パスを持ちます。 その場合は、葉から開始して、毎回唯一の未使用のエッジに沿って歩きます。 
3. グラフに`N`エッジでは、葉を繰り返し取り除いてサイクルを見つけます。 このプロセスの後に残った頂点が独自のサイクルを形成します。 
4. サイクル頂点ごとに、そこにぶら下がっている木を検査します。 各ぶら下がり部分はそれ自体が 1 つのパスである必要があります。 そのリーフからサイクルに向かうパスを保存します。 
5. ぶら下がっているパスがない場合は、サイクル自体が答えです。 
6. ぶら下がっているパスが 1 つだけある場合は、そのリーフから開始してサイクルの頂点に到達し、サイクルを一周続けます。 
7. 吊り下げパスが 2 つある場合は、隣接するサイクル頂点に接続する必要があります。 1 つのリーフから開始して最初のブランチを通過し、これら 2 つのサイクル頂点間のエッジを使用せずにサイクルを一周し、2 番目のブランチを通過して終了します。 
8. 他の構造にはハミルトニアン パスを含めることはできません。 

構築の背後にある不変条件は、最終ルートのすべての内部ステーションには使用されるインシデント エッジが 2 つだけ存在する必要があるのに対し、2 つのエンドポイントには 1 つだけしか存在しないということです。 このアルゴリズムは、3 つの異なる方向から駅を訪問する必要があるすべての構造を拒否します。 

## Python ソリューション```python
import sys
from collections import deque

input = sys.stdin.readline

def solve():
    n = int(input())
    p = list(map(int, input().split()))

    if n == 1:
        print("YES")
        print(1)
        return

    g = [[] for _ in range(n)]
    edges = 0

    for i, x in enumerate(p):
        if x != -1:
            x -= 1
            g[i].append(x)
            g[x].append(i)
            edges += 1

    seen = [False] * n
    stack = [0]
    seen[0] = True
    while stack:
        v = stack.pop()
        for u in g[v]:
            if not seen[u]:
                seen[u] = True
                stack.append(u)

    if not all(seen):
        print("NO")
        return

    def tree_path():
        for i in range(n):
            if len(g[i]) <= 1:
                start = i
                break
        ans = []
        prev = -1
        cur = start
        while cur != -1:
            ans.append(cur)
            nxt = -1
            for u in g[cur]:
                if u != prev:
                    nxt = u
                    break
            prev, cur = cur, nxt
        return ans

    if edges == n - 1:
        if max(map(len, g)) > 2:
            print("NO")
        else:
            print("YES")
            print(*[x + 1 for x in tree_path()])
        return

    if edges != n:
        print("NO")
        return

    deg = [len(x) for x in g]
    q = deque(i for i in range(n) if deg[i] == 1)
    removed = [False] * n

    while q:
        v = q.popleft()
        removed[v] = True
        for u in g[v]:
            if not removed[u]:
                deg[u] -= 1
                if deg[u] == 1:
                    q.append(u)

    cycle = [i for i in range(n) if not removed[i]]
    cycle_set = set(cycle)

    order = []
    start = cycle[0]
    prev = -1
    cur = start
    while True:
        order.append(cur)
        nxt = -1
        for u in g[cur]:
            if u != prev and u in cycle_set:
                nxt = u
                break
        prev, cur = cur, nxt
        if cur == start:
            break

    def get_branch(c, nxt):
        res = [c]
        prev = c
        cur = nxt
        while True:
            res.append(cur)
            candidates = [u for u in g[cur] if u != prev and u not in cycle_set]
            if len(candidates) > 1:
                return None
            if not candidates:
                break
            prev, cur = cur, candidates[0]
        return res[::-1]

    branches = {}
    bad = False
    for c in cycle:
        arr = []
        for u in g[c]:
            if u not in cycle_set:
                b = get_branch(c, u)
                if b is None:
                    bad = True
                else:
                    arr.append(b)
        if len(arr) > 1:
            bad = True
        if arr:
            branches[c] = arr[0]

    if bad or len(branches) > 2:
        print("NO")
        return

    def rotate_after(x):
        k = order.index(x)
        return order[k + 1:] + order[:k]

    if not branches:
        ans = order
    elif len(branches) == 1:
        c, b = next(iter(branches.items()))
        ans = b + rotate_after(c)
    else:
        c1, c2 = list(branches.keys())
        if c2 not in g[c1]:
            print("NO")
            return
        i = order.index(c1)
        if order[(i + 1) % len(order)] == c2:
            middle = order[i + 2:] + order[:i + 1]
        else:
            middle = order[i - 1:i - len(order):-1]
        ans = branches[c1] + middle + branches[c2][::-1][1:]

    if len(ans) != n:
        print("NO")
    else:
        print("YES")
        print(*[x + 1 for x in ans])

if __name__ == "__main__":
    solve()
```この実装では、まず、切断されたグラフ、ツリー、および単環式グラフという 3 つの構造ケースが分離されます。 リーフ削除プロセスが使用されるのは、次数 1 の頂点を繰り返し削除すると、サイクルからぶら下がっているすべての木が削除され、サイクル頂点が正確に残るためです。 

ブランチの検証は微妙な部分です。 ブランチはチェーンのみになれます。 非サイクル頂点に 2 つの未使用の子がある場合、ルートを分割する必要がありますが、これは単一のパスでは不可能です。 

すべての部分が 1 回だけ追加されるため、この構築ではステーションを再訪することはありません。最初に可能な分岐、次にサイクルのセクション、次に可能な 2 番目の分岐が追加されます。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(N) | すべての頂点とエッジは一定の回数だけ処理されます。 
| スペース | O(N) | 隣接リスト、キュー、およびヘルパー配列には、グラフが格納されます。 

線形複雑さは次の場合に必要です。`N = 2 * 10^5`。 このアルゴリズムはグラフの走査とローカル チェックのみを実行するため、制限内に簡単に収まります。
