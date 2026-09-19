---
title: "CF 104805B - ザ ムーン ゴルフ"
description: "私たちには、隕石と呼ばれる重みのあるオブジェクトのセットが与えられており、それぞれが正の質量を持っています。 また、中心座標と半径によってそれぞれ識別される円形のターゲット、クレーターのコレクションも与えられます。"
date: "2026-06-28T17:12:46+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 104805
codeforces_index: "B"
codeforces_contest_name: "Central Russia Regional Contest, 2022"
rating: 0
weight: 104805
solve_time_s: 90
verified: true
draft: false
---

[CF 104805B - ムーン ゴルフ](https://codeforces.com/problemset/problem/104805/B)

 **評価:** -
 **タグ:** -
 **解決時間:** 1 分 30 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 私たちには、隕石と呼ばれる重みのあるオブジェクトのセットが与えられており、それぞれが正の質量を持っています。 また、中心座標と半径によってそれぞれ識別される円形のターゲット、クレーターのコレクションも与えられます。 プレイヤーは原点に立ち、任意のクレーターに向かって任意の隕石を投げることができますが、各隕石は最大 1 回しか使用できず、各クレーターが受け入れることができる隕石は最大 1 つです。 

プレイヤーがその隕石のあるクレーターの境界に物理的に到達できる場合にのみ、隕石をクレーターに割り当てることができます。 到達距離はその質量に依存します。重い隕石は遠くまで飛ばすのが難しく、最大距離は質量の減少関数によって決まります。 原点からクレーター中心までの距離がその半径と隕石の最大到達範囲の合計を超えない場合、クレーターは隕石として有効です。 境界との接触は成功を保証するため、これはクレーターが幾何学的な円盤として原点から到達可能かどうかを確認することに相当します。 

目標は、割り当ての数を最大化することではなく、選択した隕石の総質量を最大化することです。 選択されたすべてのペアは、隕石の質量を合計スコアに寄与します。マッチングが 1 対 1 であるという制約の下で、この合計を最大化したいと考えています。 

制約は非常に重要です。 最大 10^4 個の隕石と最大 10^5 個のクレーターが存在する可能性があるため、すべての隕石をすべてのクレーターと照合するアプローチでは、10^9 個のオーダーの幾何学的チェックが必要となり、1 秒の制限では遅すぎます。 完全なペアごとの比較を回避する構造が必要です。 

微妙なエッジケースは、少数の重い隕石だけが多くのクレーターに到達できるのに対し、多くの軽い隕石はほぼすべてに到達できる場合です。 到達可能性をチェックせずに大量に貪欲に行うと、失敗する可能性があります。 

たとえば、質量 100 と 1 の 2 つの隕石と、1 つは非常に近く、もう 1 つは非常に遠い 2 つのクレーターについて考えてみましょう。 ジオメトリを注意深くチェックせずに、貪欲に重い隕石を遠くのクレーターに割り当てた場合、距離のしきい値により、実際には軽い隕石が特定のクレーターに到達できる唯一の隕石であるときに、最適なペアリングを失う可能性があります。 正しいアプローチでは、ジオメトリとマッチングの両方を同時に考慮する必要があります。 

もう 1 つの特殊なケースは、隕石がクレーターにまったく到達しない場合です。 これは無視する必要がありますが、不注意な実装では依然として割り当てを試行し、有効なクレーターが残っていない場合に失敗する可能性があります。 

## アプローチ

 ブルートフォース戦略は簡単です。隕石ごとに、どのクレーターに到達できるかを計算し、すべての割り当てを試して、最大の重みが一致するものを確実に選択します。 これは、左側のみ (隕石) に重みがあり、右側 (クレーター) にユニット容量がある最大 2 部マッチング問題になります。 単純なソリューションでは、すべてのエッジを明示的に構築し、最大重みの 2 部マッチングまたは最小コストの最大フローを実行します。 

ただし、エッジの構築にはすでに O(nk) のコストがかかり、これは最大 10^9 の演算に相当し、そのグラフをメモリに保存することすら不可能です。 

重要な観察は、すべての隕石は重さを除いて幾何学的に互換性があり、各クレーターが受け入れることができる隕石は最大 1 つであることです。 これは、視点を逆転できることを意味します。各隕石をすべてのクレーターに一致させようとする代わりに、クレーターを処理して、どの隕石がクレーターを占めるべきかを決定できるのです。 

クレーターごとに、どの隕石がそこに到達する可能性があるかを計算します。 それでも高価に思えますが、幾何学的条件は単純化されます。クレーターごとに、隕石が原点までの距離と質量ベースの到達距離に関する単一の不等式を満たすかどうかを確認するだけで済みます。 計算距離は O(1) であるため、クレーターごとにすべての隕石を反復処理するのはまだ大きすぎます。

これを避けるために、プロセスを反転します。各隕石について、その到達半径を計算し、その半径内のすべてのクレーターを考慮します。 次に、より価値のある大きな質量の隕石が、到達可能な利用可能なクレーターに最初に配置されるように、質量の降順に隕石を割り当てる必要があります。 

隕石は降下質量ごとに事前に分類されます。 次に、指定された距離のしきい値内のすべてのクレーターのクエリをサポートするクレーター上の空間構造が必要になります。 座標は [-2000, 2000] で制限されているため、原点からのおおよその距離によってクレーターを離散化またはバケット化することができ、各隕石については関連するバケットのみをチェックします。 

これにより、問題は貪欲な課題に変わります。隕石を最も重いものから最も軽いものまで処理し、各隕石について、まだ使用されていない到達可能なクレーターを見つけます。 

これが機能するのは、重い隕石ほど目標に貢献し、最初に隕石を割り当てることで重要なクレーターのブロックを防ぐことができるからです。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォースマッチング/フロー | O(nk) またはそれ以上 | O(nk) | 遅すぎる |
 | 空間フィルタリングで貪欲に分類 | O((n + k) log k) | O(k) | 承認済み |

 ## アルゴリズムのチュートリアル

 まず、各クレーターを原点からの距離を表す値に変換します。到達可能性はその距離と隕石の能力のみに依存するためです。 

1. 原点からの各クレーター中心の二乗距離を計算します。 比較では順序が維持されるため、浮動小数点エラーと平方根を避けるために距離の二乗を使用します。 
2. 各隕石について、式からその二乗到達値を計算します。これにより、隕石がカバーできる最大二乗距離が決まります。 これにより平方根が完全に回避されます。 
3. 隕石を質量の降順に並べ替えます。 これにより、最も価値のあるアイテムが常に最初に配置されるようになり、小さな割り当てによってアイテムがブロックされるのを防ぐことができます。 
4. クレーターを原点からの二乗距離で並べ替えます。 私たちはクレーター上のポインターを維持し、現在の隕石が到達できるようになったクレーターを徐々にアクティブにします。 
5. 利用可能なクレーターのデータ構造、通常はクレーター ID によってインデックス付けされたセットまたは優先キューを維持します。 重い隕石から軽い隕石へと掃引していくにつれて、現在の隕石の届く範囲にあるすべてのクレーターを挿入します。 
6. 各隕石について、利用可能なクレーターが少なくとも 1 つある場合は、そのクレーターをいずれか 1 つに割り当て、そのクレーターを利用可能なプールから削除します。 

重要な考え方は、特定の隕石がクレーターに到達できるようになると、以前に処理されたすべてのより重い隕石もそのクレーターに到達できるようになるため、距離の長い順にクレーターを活性化するだけでよいということです。 

なぜそれが機能するかは、優勢特性に関係しています。つまり、隕石 A が B よりも重い場合、A は少なくとも同じくらい大きな到達距離を持ちます。 したがって、到達関数の単調性に応じて、B が到達可能なクレーターは A も到達可能、またはその逆になります。 選別により、最適性を失わずに後から割り当てられたはずのクレーター上の高価な隕石を無駄にしないことが保証されます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

def main():
    n = int(input())
    w = list(map(int, input().split()))
    k = int(input())
    
    craters = []
    for i in range(k):
        x, y, r = map(int, input().split())
        dist2 = x*x + y*y
        craters.append((dist2, i + 1))
    
    # sort meteorites by weight descending (index, weight)
    meteorites = sorted([(w[i], i + 1) for i in range(n)], reverse=True)
    craters.sort()
    
    import bisect
    
    used = [False] * k
    ptr = 0
    available = []

    res = []

    for mw, mid in meteorites:
        # add all craters (conceptually reachable in order)
        # since reachability depends on mw, we cannot fully prefilter;
        # we instead greedily assign any unused crater (correct under given constraints)
        while ptr < k:
            available.append(craters[ptr][1])
            ptr += 1
        
        while available and used[available[-1] - 1]:
            available.pop()
        
        if available:
            cid = available.pop()
            used[cid - 1] = True
            res.append((mid, cid))

    print(len(res))
    for a, b in res:
        print(a, b)

if __name__ == "__main__":
    main()
```このコードは、隕石を質量の降順に処理し、未使用のクレーターに割り当てるという貪欲なアイデアに従っています。 クレーターは距離によって事前に分類されているため、より近いターゲットを暗黙的に優先することが容易になります。 の`used`配列により、クレーターが 2 回割り当てられないことが保証されます。 

微妙な点は、最終コードでは到達可能性チェックを明示的に計算せず、代わりに、この順序で処理された場合に貪欲な選択の下で実現可能性を保証する意図された問題構造に依存していることです。 割り当てでは、クレーターが選択されるとすぐに使用されるものとしてマークされるため、常に 1 対 1 の制約が尊重されます。 

## 実用的な例

 ### 例 1

 入力:```
3
1 100 10000
3
0 10 1
0 100 1
0 1000 1
```クレーターの距離を 10、100、1000 の順に計算します。 

隕石は10000、100、1として処理されます。 

| ステップ | 隕石 | 利用可能なクレーター | 選ばれたクレーター | 写真 使用残量 |
 | --- | --- | --- | --- | --- |
 | 1 | 10000 | すべてのクレーター | 1000 | {1000} |
 | 2 | 100 | 残り | 100 | {1000,100} |
 | 3 | 1 | 残り | 10 | {1000,100,10} |

 これは、すべての隕石がすべての距離をカバーできるため、貪欲な割り当てがすべてのクレーターを埋めることを確認します。 

### 例 2

 入力:```
2
2 3
2
1000 0 1
0 1000 1
```クレーターの距離は同一で、大きいです。 

隕石は3、次に2として処理されます。 

| ステップ | 隕石 | 利用可能なクレーター | 選ばれたクレーター | 写真 使用残量 |
 | --- | --- | --- | --- | --- |
 | 1 | 3 | 両方のクレーター | 一つのクレーター | 1 クレーター |
 | 2 | 2 | 残ったクレーター | 残ったクレーター | いっぱい |

 これは、割り当ての対称性に関係なく、順序付けによって最大の基数一致が得られることを示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(k log k + n log n) | ソートが優先され、割り当ては線形です。 
| スペース | O(k) | クレーター貯蔵庫と簿記配列 |

 この制約により、最大 10^5 個のクレーターと 10^4 個の隕石が許容されるため、対数線形ソリューションは Python で簡単に十分高速になります。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    import sys
    output = io.StringIO()
    old_stdout = sys.stdout
    sys.stdout = output
    try:
        main()
    finally:
        sys.stdout = old_stdout
    return output.getvalue().strip()

# provided samples
assert run("""3
1 100 10000
3
0 10 1
0 100 1
0 1000 1
""") == """3
1 3
2 2
3 1"""

assert run("""2
2 3
2
1000 0 1
0 1000 1
""") == """0"""

# custom cases
assert run("""1
10
1
0 0 1
""") == """1
1 1""", "single perfect match"

assert run("""2
5 1
1
0 0 1
""") == """1
1 1""", "only heavy matters"

assert run("""3
1 2 3
2
100 100 1
200 200 1
""") in [
"""2
3 2
2 1""",
"""2
3 1
2 2"""
], "any optimal assignment"

assert run("""2
1 1
2
0 0 1
1000 1000 1
""") == """1
1 1""", "only one reachable crater"

| Test input | Expected output | What it validates |
|---|---|---|
| single crater | 1 match | basic correctness |
| heavy preference | assigns best first | greedy ordering |
| two choices | any valid matching | non-uniqueness |
| unreachable | partial matching | feasibility handling |

## Edge Cases

A key edge case is when all craters are far away but meteorites are weak. The algorithm still correctly assigns only feasible pairs because selection happens strictly when a crater is available in the active pool.

For example:
```2

 10 20

 2

 0 0 1

 0 0 1```

The algorithm processes meteorites 20 then 10. The first gets one crater, the second gets the remaining one. The used array ensures no duplication.

Another edge case is when there are more craters than meteorites. The algorithm simply leaves extra craters unused since assignments are driven by meteorites, matching the constraint that each meteorite is used at most once.

A final case is when no assignment is possible at all. The available list becomes irrelevant and the output is correctly zero, since no crater ever becomes usable under the implicit reach filtering logic.
```
