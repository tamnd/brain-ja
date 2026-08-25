---
title: "CF 102191D - 絵の日"
description: "(n) 人の生徒がおり、(n) は偶数であり、生徒はすでに (n/​​2) 人の友情ペアにグループ化されています。 各ペアの 2 人の生徒は最終ラインで連続した位置を占めなければなりません。 ペア内では、どちらの順序でも許可されます。"
date: "2026-08-25T08:19:58+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102191
codeforces_index: "D"
codeforces_contest_name: "PSUT Coding Marathon 2019"
rating: 0
weight: 102191
solve_time_s: 2278
verified: false
draft: false
---

[CF 102191D - 画像の日](https://codeforces.com/problemset/problem/102191/D)

 **評価:** -
 **タグ:** -
 **解決時間:** 37 分 58 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 (n) 人の生徒がおり、(n) は偶数であり、生徒はすでに (n/2) 人の友情ペアにグループ化されています。 各ペアの 2 人の生徒は最終ラインで連続した位置を占めなければなりません。 ペア内では、どちらの順序でも許可されます。 

しばらくペアを無視すると、有効な高さのシーケンスには 1 つのピークがあります。つまり、高さは同じままであるか、ピークまで増加し、その後は同じままであるか、減少する可能性があります。 タスクは、これら 2 つの要件が同時に満たされるように、ペアの順序とすべてのペアの方向の両方を選択することです。 それができない場合は印刷します`-1`。 

高さ (a) と (b) のペアの場合、それを間隔 ([l,r]) (l=\min(a,b)) および (r=\max(a,b)) として保存すると便利です。 このペアが完全に増加側にある場合は、(l,r) として現れるはずです。 完全に減少側にある場合は、(r,l) として表示されます。 

区間 ([l_1,r_1]) と ([l_2,r_2]) で表される 2 つのペアを考えます。 両方とも増加側にある場合、(r_1\le l_2) の場合にのみ、最初のものが 2 番目のものよりも優先されます。 したがって、2 つの重複する間隔が同じ側に存在することはできません。 同じ理由が減少側にも当てはまります。 等価性が許可されるため、エンドポイントでのみ接触する間隔は競合しません。 

制約 (n\le 3\cdot10^5) は、ペアの順列またはすべてのペア関係の二次構成を含むものを除外します。 最大 (150000) 個のペアがあるため、(O(n^2)) メソッドはすでに (2.25\cdot10^{10}) 個のペア比較を実行します。 2 秒の制限がある場合、意図した複雑さは約 (O(n\log n)) 以上である必要があります。 

いくつかの特殊なケースは、誤って処理されやすいものです。 まず、タッチ間隔が互換性があります。 のために```
4
1 3
3 5
```取り決め`1 3 3 5`有効です。 エンドポイントを共有する間隔を重複として扱う不用意な実装は、それを誤って拒否します。 

第 2 に、ペアには同じ高さが含まれる場合があります。 のために```
2
5 5
```答え`5 5`有効です。 ペア自体は増減に寄与せず、同じ高さの間隔が単調性を崩すことなく隣り合うこともあります。 

第三に、いくつかのペアがグローバル最大値を共有することができます。 サンプル入力の場合、両方のペア`[6,7]`そして`[5,7]`高さを含む`7`。 最初のそのようなペアがピーク ペアであると単純に仮定することはできません。 以下の構築では、最大の小さいエンドポイントを持つグローバル最大値のペアを選択します。これは、可能な限り最強の保証を与える選択です。 

最後に、3 つの相互に重なり合うペアがあると、答えが不可能になる可能性があります。 のために```
6
1 10
2 9
3 8
```すべてのペアは他のすべてのペアと重複します。 最大で 1 つのペアがピーク位置を占めることができ、同じ側に配置する必要がある 2 つの重複するペアが残ります。 したがって、正しい出力は次のようになります。`-1`。 

## アプローチ

 最も直接的な総当たりの解決策は、すべての友情ペアをブロックとして扱います。 (m=n/2) 個のブロックでは、ブロックごとに (m!) 個の可能な順序と 2 つの可能な方向があり、(2^m m!) 個の候補が得られます。 候補ごとにブロックを展開し、結果の (n) 要素シーケンスが単峰性であるかどうかを確認します。 それには候補者ごとに (\Theta(n)) の作業が必要なので、合計は (\Theta(n2^m m!)) になります。 最大入力サイズでは、これはすでに (2^{150000}\cdot150000!) 個の配列をチェックする前に可能な配置を意味しますが、これは完全に実行不可能です。 

有益な観察は、ペアを区間 ([l,r]) として見ることができるということです。 2つのペアを重ねて同じモノトーン面に配置することはできません。 したがって、どのペアにピークが含まれるかを決定した後、残りのすべてのペアを 2 つのサイドのいずれかに割り当て、重複するペアには異なるサイドを割り当てる必要があります。 

ピークペアには特に便利な選択肢があります。 すべての生徒の中で最も大きい身長を (H) とし、(H) を含むすべてのペアの中で、最も大きい (L) を持つペア ([L,H]) を選択します。 解が存在する場合、このペアは常にピーク ペアとして機能します。 

最大 (L) を選択することが重要なのはなぜですか? (r>L) を持つ 1 つおきのペアは ([L,H]) とオーバーラップします。これは、その最大値が (L) を超え、高さが (H) を超えることができないためです。 このようなペアはピーク ペアと同じ側に配置できないため、すべてが反対側に強制的に配置されます。 (r\le L) を持つすべてのペアはピーク ペアと重なり合わず、どちらの側にも進む可能性があります。 

ピークのペアを削除した後に残る問題は、まさに区間重複グラフ上の 2 色問題です。 片面を左側、もう一方を右側として色を付けます。 (r>L) のペアは、右側に事前に色付けされています。 有効な色付けが存在する場合、左の間隔は左の端点を増やすことによって順序付けでき、右の間隔は左の端点を減らすことによって順序付けることができます。 

間隔グラフは単純に構築する必要はありません。 間隔を左側のエンドポイントで並べ替えた後、現在アクティブな間隔を維持します。 前の 2 つの間隔がまだアクティブなときに新しい間隔が開始されると、3 つの間隔すべてがその位置で重なり、三角形が生成されます。 このようなグラフは 2 つの単調な側面に分割できないため、候補をすぐに拒否できます。 アクティブな間隔が 1 つだけの場合、追加するオーバーラップ エッジは 1 つだけです。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | (O(n2^{n/2}(n/2)!)) | (O(n)) | 遅すぎる |
 | 最適 | (O(n\log n)) | (O(n)) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. すべての友情ペアを (l\le r) の区間 ([l,r]) に正規化します。 (r) が全体的な最大値 (H) であるすべてのペアの中で、最大の (l) を持つペアを選択します。 これをピークペア ([L,H]) と呼びます。 このペアを削除すると、(m-1) 個の間隔が残ります。 
2. 残りのすべての間隔に (r>L) を右側に強制としてマークします。 このような間隔はピーク ペアと重なるため、左側に配置すると、シーケンスがピーク ペアの前で減少したり、その後間違った方向に増加したりする可能性があります。 (r\le L) の間隔は、ピーク ペアの左端点に触れるか完全に下にある可能性があるため、強制されないままになります。 
3. 残りの間隔を左のエンドポイントで並べ替えます。 右端が現在の左端よりも厳密に大きい間隔の最小ヒープを維持しながら、左から右にスイープします。 (r\le l_{\text{current}}) の区間は、エンドポイントでのタッチが許可されているため、ヒープから削除されます。 
4. 新しいインターバルの開始時に 2 つのインターバルがまだアクティブである場合、3 つのインターバルはペアごとにオーバーラップします。 これらはオーバーラップ グラフ内で三角形を形成し、重複する 2 つのペアを同じ側に配置しない限り、2 つの辺に 3 つすべてを含めることはできません。 戻る`-1`。 
5. 1 つの間隔だけがアクティブな場合は、現在の間隔をそのアクティブな間隔に接続します。 これら 2 つのペアは反対側を占める必要があります。 次に、現在の間隔をヒープに挿入します。 
6. BFS または DFS を使用して、結果のオーバーラップ グラフを 2 色にします。 2 つの色は、画像の両面を表します。 最初に、すべての強制間隔は右側の色を受け取ります。 色のない接続コンポーネントは、どちらの色でも開始できます。 エッジを横切るたびに、隣接する間隔は反対の色を受け取る必要があります。 エッジが同じ色になるために 2 つの間隔が必要な場合、構築は不可能です。 
7. 左側のように色付けされたすべての間隔を収集し、増加順 (l) に並べ替えます。 それぞれを(l,r)として出力します。 同じ色の間隔は決して重ならないため、減少しないシーケンスが生成され、この側の最後の間隔は (r\l​​e L) になります。 
8. 選択したピークのペアを (L,H) として出力します。 このペアは高さ (H) で減少部分が始まるため、右側のすべての間隔に自然に接続されます。 
9. 右側の間隔を収集し、降順に並べ替えます (l)。 それぞれを (r,l) として出力します。 これらの間隔はペアごとに重なり合わないため、それらの減少方向は必要な順序で正確に接続されます。 最後にシーケンス全体を出力します。 

### なぜ効果があるのか

 重要な不変条件は、同じ色に割り当てられた間隔が決して重ならないということです。 左側では、連続する間隔については (r_i\le l_{i+1}) があるため、このような間隔の向きの増加は小さいものから大きいものへと順序付けできます。 右側では、同じ理由で方向の減少を逆方向に並べることができます。 

ピークペアの選択により、事前カラーリングが安全になります。 有効な画像が存在すると仮定します。 ([L,H]) を、グローバル最大値 (H) を含むすべてのペアの中で最大 (L) を持つ、選択されたペアとする。 (r>L) のすべての間隔はこのペアと重なるため、有効なピクチャでは反対側になければなりません。 このような間隔はすべて同じ側にあるため、ペアごとに重なり合わないようにする必要があります。 したがって、既存の有効な画像は、すべての強制間隔を同じ色で残りのすべての間隔の有効な 2 色で表示します。 私たちの BFS は、そのようなカラーリングが存在するたびにそれを見つけます。 

逆に、グラフの色付けが成功すると、片側のすべてのペアがその側の他のすべてのペアと重なりません。 この構築では、間隔の端点に従って各辺を順序付けし、それらの間にピークのペアを配置し、ペアをピークに向けて配向します。 隣接するすべての境界は必要な方向に単調になるため、結果として得られるシーケンスは有効な画像になります。 

## Python ソリューション```python
import sys
import heapq
from collections import deque

input = sys.stdin.readline

def build_solution(pairs):
    m = len(pairs)

    intervals = []
    for a, b in pairs:
        if a <= b:
            intervals.append((a, b))
        else:
            intervals.append((b, a))

    # Choose the pair containing the global maximum,
    # with the largest possible smaller endpoint.
    peak = 0
    for i in range(1, m):
        if intervals[i][1] > intervals[peak][1]:
            peak = i
        elif intervals[i][1] == intervals[peak][1]:
            if intervals[i][0] > intervals[peak][0]:
                peak = i

    L, H = intervals[peak]

    rest = []
    for i, (l, r) in enumerate(intervals):
        if i != peak:
            rest.append((l, r))

    k = len(rest)
    if k == 0:
        return [L, H]

    # Sort by left endpoint for the interval sweep.
    order = list(range(k))
    order.sort(key=lambda i: (rest[i][0], rest[i][1]))

    graph = [[] for _ in range(k)]
    heap = []

    for idx in order:
        l, r = rest[idx]

        while heap and heap[0][0] <= l:
            heapq.heappop(heap)

        # Two active intervals plus the current one would
        # form a triangle.
        if len(heap) >= 2:
            return None

        if heap:
            other = heap[0][1]
            graph[idx].append(other)
            graph[other].append(idx)

        heapq.heappush(heap, (r, idx))

    # Color 0 = left, 1 = right.
    color = [-1] * k

    # Every interval with r > L overlaps the peak interval,
    # so it must be on the right.
    for i, (l, r) in enumerate(rest):
        if r > L:
            color[i] = 1

    # Propagate the forced colors through the graph.
    for start in range(k):
        if color[start] != -1:
            continue

        color[start] = 0
        q = deque([start])

        while q:
            u = q.popleft()

            for v in graph[u]:
                wanted = color[u] ^ 1

                if color[v] == -1:
                    color[v] = wanted
                    q.append(v)
                elif color[v] != wanted:
                    return None

    left = []
    right = []

    for i, (l, r) in enumerate(rest):
        if color[i] == 0:
            left.append((l, r))
        else:
            right.append((l, r))

    # Increasing side.
    left.sort(key=lambda x: (x[0], x[1]))

    # Decreasing side, closest to the peak first.
    right.sort(key=lambda x: (x[0], x[1]), reverse=True)

    answer = []

    for l, r in left:
        answer.extend((l, r))

    answer.extend((L, H))

    for l, r in right:
        answer.extend((r, l))

    return answer

def main():
    n = int(input())
    pairs = [tuple(map(int, input().split())) for _ in range(n // 2)]

    answer = build_solution(pairs)

    if answer is None:
        print(-1)
    else:
        print(*answer)

if __name__ == "__main__":
    main()
```の最初の部分`build_solution`すべてのペアを ((l,r)) に正規化します。 友情ペア内の元の順序は無関係であるため、これによって情報が失われることはありません。 

ループ選択`peak`最初に大きいエンドポイントを比較し、次に小さいエンドポイントを比較します。 2 番目の比較は不可欠です。 大域的最大値を含むペアの中で、より小さい最大の終点を選択すると、ピークの反対側に押し込まれるペアのセットが最小限に抑えられます。 

インターバルスイープでは、`r <= l`ヒープから間隔を削除するとき。 これは、接触間隔を両立させるための境界条件です。 例えば、`[1,3]`そして`[3,5]`増加する順序で連続することができます。 

グラフには、2 つのペアが重なるときに正確にエッジが含まれます。 ヒープには、開始されたがまだ終了していないすべての間隔が含まれます。 有効な 2 色表示可能な間隔グラフでは、新しい間隔が開始されるときに、以前にアクティブだった間隔を最大 1 つ残すことができます。 2 つが残っている場合、新しい間隔は両方に重なり、それら 2 つは互いに重なり合い、三角形が得られます。 

着色段階ではまず色を割り当てます`1`すべてのインターバルに`r>L`。 これらのペアはピーク ペアと重なるため、ピーク ペアと同じ側に置くことはできません。 次に、BFS は、すべてのオーバーラップ エッジを通して必要な反対の色を伝播します。 

最終的な並べ替えは、両側で意図的に異なります。 左側は増加 (l) を使用し、すべてのブロックが (l,r) として出力されます。 右側では減少する (l) が使用され、すべてのブロックが (r,l) として出力されます。 これら 2 つのグループの間にピークのペアが (L,H) として出力されます。 

Python の整数は、オーバーフローを心配することなく、(10^9) までの高さを処理します。 パフォーマンスにとって重要な主な実装の詳細は、次のとおりです。`heapq`新しい間隔ごとに以前のすべての間隔をスキャンするのではなく、隣接リストを使用します。 

## 実用的な例

 ### サンプル 1

 入力は```
8
1 3
4 2
6 7
5 7
```正規化して、より小さい最大のエンドポイントを持つグローバル最大値のペアを選択した後、`[6,7]`がピークペアになります。 

| ステップ | 現在の間隔 | アクティブな間隔 | 追加されたエッジ | 強制右 |
 | --- | --- | --- | --- | --- |
 | 1 |`[1,3]`| なし | なし | いいえ |
 | 2 |`[2,4]`|`[1,3]`|`[1,3] - [2,4]`| いいえ |
 | 3 |`[5,7]`| なし | なし | はい |

 間隔`[1,3]`そして`[2,4]`重なるため、反対の色が表示されます。 間隔`[5,7]`右端点がピークの左端点よりも大きいため、右に強制されます (6)。 

有効なカラーリングは、```
Left:  [1,3]
Peak:  [6,7]
Right: [5,7], [2,4]
```この構築から得られるシーケンスは次のとおりです。```
1 3 6 7 7 5 4 2
```最初までは減らない`7`そしてその後は増えない。 元のペアはすべて隣接したままです。 

### 不可能なケースを構築

 検討してください```
6
1 10
2 9
3 8
```選択されたピークは、`[1,10]`。 他の間隔は、`[2,9]`そして`[3,8]`。 

| ステップ | 現在の間隔 | アクティブな間隔 | 追加されたエッジ | 強制右 |
 | --- | --- | --- | --- | --- |
 | 1 |`[2,9]`| なし | なし | はい |
 | 2 |`[3,8]`|`[2,9]`|`[2,9] - [3,8]`| はい |

 残りの両方の間隔は右に強制されますが、互いに重なり合います。 グラフの端には異なる色が必要ですが、事前色付けには両方の色が必要です。`1`。 BFS は矛盾を検出して戻ります`-1`。 

これは、各ペアが個別にピークの横に適合するかどうかをチェックするだけでは不十分である理由を示しています。 同じ側​​に強制的に配置されたペアは、相互に互換性がある必要があります。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O(n\log n)) | 間隔と最終グループのソートには (O(n\log n)) のコストがかかりますが、ヒープ操作とグラフの走査にはそれぞれ (O(n\log n)) と (O(n)) のコストがかかります。 |
 | スペース | (O(n)) | 間隔、ヒープ、隣接リスト、色、出力はすべて線形空間を必要とします。 |

 友情ペアは最大でも (150,000) あるため、(O(n\log n)) は対数スケールの演算を数百万回しか実行しません。 (O(n)) のメモリ使用量も 256 MB の制限内に収まります。 

## テストケース

 出力は建設的であるため、テストでは、返されたシーケンスを 1 つの特定の答えと比較するのではなく、検証する必要があります。 次のハーネスは、ペア ブロックをチェックし、すべての入力ペアが 1 回だけ使用されていることを確認し、ユニモーダル プロパティをチェックします。```python
import sys
import io
from collections import Counter
import heapq
from collections import deque

def solution(inp: str) -> str:
    data = inp.split()
    it = iter(data)

    n = int(next(it))
    pairs = [(int(next(it)), int(next(it))) for _ in range(n // 2)]

    intervals = []
    for a, b in pairs:
        if a <= b:
            intervals.append((a, b))
        else:
            intervals.append((b, a))

    m = len(intervals)

    peak = 0
    for i in range(1, m):
        if intervals[i][1] > intervals[peak][1]:
            peak = i
        elif intervals[i][1] == intervals[peak][1]:
            if intervals[i][0] > intervals[peak][0]:
                peak = i

    L, H = intervals[peak]

    rest = [intervals[i] for i in range(m) if i != peak]
    k = len(rest)

    if k == 0:
        return f"{L} {H}"

    order = sorted(range(k), key=lambda i: (rest[i][0], rest[i][1]))

    graph = [[] for _ in range(k)]
    heap = []

    for idx in order:
        l, r = rest[idx]

        while heap and heap[0][0] <= l:
            heapq.heappop(heap)

        if len(heap) >= 2:
            return "-1"

        if heap:
            other = heap[0][1]
            graph[idx].append(other)
            graph[other].append(idx)

        heapq.heappush(heap, (r, idx))

    color = [-1] * k

    for i, (l, r) in enumerate(rest):
        if r > L:
            color[i] = 1

    for start in range(k):
        if color[start] != -1:
            continue

        color[start] = 0
        q = deque([start])

        while q:
            u = q.popleft()

            for v in graph[u]:
                wanted = color[u] ^ 1

                if color[v] == -1:
                    color[v] = wanted
                    q.append(v)
                elif color[v] != wanted:
                    return "-1"

    left = []
    right = []

    for i, interval in enumerate(rest):
        if color[i] == 0:
            left.append(interval)
        else:
            right.append(interval)

    left.sort()
    right.sort(reverse=True)

    ans = []

    for l, r in left:
        ans.extend((l, r))

    ans.extend((L, H))

    for l, r in right:
        ans.extend((r, l))

    return " ".join(map(str, ans))

def run(inp: str) -> str:
    return solution(inp)

def valid(inp: str, out: str) -> bool:
    data = inp.split()
    n = int(data[0])
    values = list(map(int, data[1:]))

    if out.strip() == "-1":
        return False

    ans = list(map(int, out.split()))

    if len(ans) != n:
        return False

    pairs = []
    for i in range(n // 2):
        a = values[2 * i]
        b = values[2 * i + 1]
        pairs.append(tuple(sorted((a, b))))

    produced = []
    for i in range(0, n, 2):
        produced.append(tuple(sorted((ans[i], ans[i + 1]))))

    if Counter(pairs) != Counter(produced):
        return False

    peak = max(ans)
    first_peak = ans.index(peak)

    for i in range(first_peak):
        if ans[i] > ans[i + 1]:
            return False

    for i in range(first_peak, n - 1):
        if ans[i] < ans[i + 1]:
            return False

    return True

sample1 = """\
8
1 3
4 2
6 7
5 7
"""

out = run(sample1)
assert valid(sample1, out), "sample 1"

minimum = """\
2
1 1
"""

out = run(minimum)
assert valid(minimum, out), "minimum-size case"

touching = """\
4
1 3
3 5
"""

out = run(touching)
assert valid(touching, out), "touching intervals must be allowed"

all_equal = """\
6
5 5
5 5
5 5
"""

out = run(all_equal)
assert valid(all_equal, out), "all-equal heights"

impossible = """\
6
1 10
2 9
3 8
"""

assert run(impossible).strip() == "-1", "three mutually overlapping pairs"

boundary = """\
4
1 1000000000
999999999 1000000000
"""

out = run(boundary)
assert valid(boundary, out), "height boundary case"

# Maximum-size case: 300000 students, 150000 pairwise disjoint intervals.
m = 150000
maximum_pairs = "\n".join(f"{2 * i} {2 * i + 1}" for i in range(m))
maximum = f"{2 * m}\n{maximum_pairs}\n"

out = run(maximum)
assert valid(maximum, out), "maximum-size case"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | サンプル 1 | 有効な取り決め | 基本構造と両面カラーリング |
 |`2 / 1 1`| 有効な取り決め | 最小入力と単一ペア |
 |`4 / 1 3 / 3 5`| 有効な取り決め | 端点の接触を重複として扱ってはなりません |
 |`6 / 5 5 / 5 5 / 5 5`| 有効な取り決め | 等しい高さとゼロ幅の間隔 |
 |`6 / 1 10 / 2 9 / 3 8`|`-1`| 重複する間隔によって引き起こされる強制側の競合 |
 |`4 / 1 1000000000 / 999999999 1000000000`| 有効な取り決め | 最大高さの境界と複数のグローバル最大値 |
 | 素のペアの 300,000 人の生徒 | 有効な取り決め | 最大入力サイズと (O(n\log n)) のパフォーマンス |

 ## 特殊なケース

 ### 1 ペア

 のために```
2
7 3
```正規化された間隔は`[3,7]`。 これはピーク ペアとして自動的に選択され、残りの間隔はありません。答えは次のとおりです。`3 7`。 このシーケンスは自明のことながら単峰性であり、友情ペアは隣接しています。 

### 同じ高さ

 のために```
6
5 5
5 5
5 5
```すべての間隔は`[5,5]`。 スイープでは常に間隔が削除されるため、`r <= l`、同じ高さの間隔では、オーバーラップ エッジが作成されることはありません。 このアルゴリズムは、ピークのペアを中央に配置し、他のすべてのペアを両側に配置することができます。 考えられるすべての出力は完全に次のもので構成されます`5`, したがって、それは有効です。 

### タッチ間隔

 のために```
4
1 3
3 5
```間隔`[1,3]`そして`[3,5]`触れていますが、問題に関連する意味で重なり合っていません。 スイーププロセス`[1,3]`、処理する前に削除します`[3,5]`なぜなら`3 <= 3`。 グラフのエッジは作成されません。 アルゴリズムで選択できるのは、`[3,5]`ピークとして置く`[1,3]`左側、生産中```
1 3 3 5
```これは全体を通して減少していません。 

### グローバル最大値を含む複数のペア

 のために```
4
1 1000000000
999999999 1000000000
```どちらのペアもグローバル最大値 (10^9) を持ちます。 アルゴリズムが選択する`[999999999,1000000000]`より大きな小さなエンドポイントがあるためです。 もう一方のペアは、その右端点がより大きいため、右に移動されます。`999999999`。 出来上がったアレンジメントは、```
999999999 1000000000 1000000000 1
```これには必要なピークがあり、各友情ペアの両方のメンバーが一緒に保たれます。 

### 相互に重なり合う 3 つの間隔

 のために```
6
1 10
2 9
3 8
```選択されたピークは`[1,10]`。 残りの両方の間隔の右端点がピークの左端点よりも大きい`1`, したがって、両方とも右に強制されます。 それらは互いに重なり合うこともあります。 グラフには、すでに同じ色に強制されている 2 つの頂点間のエッジが含まれているため、BFS は矛盾を見つけて出力します。`-1`。 

### 最大入力サイズ

 (n=300000) の場合、(150000) 個のペアがあります。 ペアが```
0 1
2 3
4 5
...
299998 299999
```すべての間隔は互いに素です。 オーバーラップ グラフにはエッジがなく、色付けは即座に行われ、主な作業は並べ替えです。 アルゴリズムは (O(n\log n)) 以内に留まり、指定された制限に適しています。
