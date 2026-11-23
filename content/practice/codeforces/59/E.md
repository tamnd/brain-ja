---
title: "CF 59E - 最短パス"
description: "私たちは、迷信のため、連続する 3 つの都市の特定のシーケンスが禁止されているというツイストを加えて、双方向の道路で接続された都市のグラフをナビゲートするように求められます。 正式には、n 個のノードと m 個のエッジがあり、すべて重み付けされておらず、都市と道路を表しています。"
date: "2026-05-28T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "graphs", "shortest-paths"]
categories: ["algorithms"]
codeforces_contest: 59
codeforces_index: "E"
codeforces_contest_name: "Codeforces Beta Round 55 (Div. 2)"
rating: 2000
weight: 59
solve_time_s: 78
verified: true
draft: false
---

[CF 59E - 最短パス](https://codeforces.com/problemset/problem/59/E)

 **評価:** 2000
 **タグ:** グラフ、最短パス
 **解決時間:** 1 分 18 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 私たちは、迷信のため、連続する 3 つの都市の特定のシーケンスが禁止されているというツイストを加えて、双方向の道路で接続された都市のグラフをナビゲートするように求められます。 正式には、_n_ 個のノードと _m_ 個のエッジがあり、すべて重み付けされておらず、都市と道路を表しています。 都市 1 から開始し、連続ノードとして _k_ 個の禁止トリプルが含まれないパスに沿って都市 _n_ に到達したいと考えています。 出力は、そのようなパスに沿った最小数の道路と 1 つの有効なパス自体でなければなりません。 パスが存在しない場合は、-1 を返します。 

制約を考慮すると、_n_ は最大 3000、_m_ は最大 20000 であることがわかります。これにより、複雑度が O(n^3) のアルゴリズムが境界線になりますが、O(n^2) のアルゴリズムは許容されます。 禁止されたトリプル _k_ の数は 10^5 に達する可能性があるため、すべての潜在的なパス上のすべてのトリプルを単純にチェックするのは遅すぎます。 

明らかではないエッジケースは、禁止されたトリプルによってターゲットへのすべての直接パスが妨げられる場合、または開始都市に隣接する都市が 1 つしかなく、それが禁止されたトリプル シーケンスの一部である場合に発生します。 たとえば、n = 4、m = 3、道路が 1-2、2-3、3-4 を接続し、禁止されたトリプルが 1-2-3 である場合、単純な BFS はトリプルを無視すると、パスが存在すると誤って主張します。 もう 1 つの注意が必要なシナリオは、パスが複数の短い形式で存在し、アルゴリズムはすべての禁止されたトリプルを回避する最小限のものを選択する必要がある場合です。 

## アプローチ

 ブルート フォース ソリューションでは、これまでに通過したパス全体を記憶しながら、グラフに対して標準的な BFS を試みます。 新しい都市のそばでパスを延長するたびに、最後の 3 つの都市が禁止されたトリプルを形成しているかどうかを確認します。 これは原理的には機能しますが、可能なパスの数は _n_ と _m_ とともに指数関数的に増加し、パスを明示的に保存すると法外に遅くなります。 最悪の場合、この単純なアプローチの複雑さは O(m * 2^n) となり、n = 3000 では明らかに実行不可能です。 

重要な観察は、禁止されたトリプルは 3 つの連続するノードのシーケンスのみを制限するということです。 これは、私たちが必要とする「記憶」は、道全体ではなく、最後に訪れた 2 つの都市だけであることを意味します。 現在の都市と以前の都市を含むように BFS 状態を再定義すると、新しく訪問したすべての都市が禁止されたトリプルを完了しないことを保証できます。 この洞察により、サイズ n^2 (前の都市 × 現在の都市) の状態空間で BFS を実行できます。これは、制約を考慮すると管理可能です。 禁止されたトリプルは、辞書マッピング (前、現在) → セット (次の禁止都市) に保存され、パスを延長するときに O(1) 検索が可能になります。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(m * 2^n) | O(n * 2^n) | 遅すぎる |
 | BFS と状態 (前、現在) | O(n * m) | O(n^2 + k) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 入力を解析して、無向グラフの隣接リストを構築します。 都市ごとに、直接接続されている近隣都市をすべて保存します。 
2. 禁止されたトリプルを解析して辞書マッピングのペアにする`(prev_city, current_city)`のセットに`next_city`価値観。 これにより、パスを延長するときに O(1) チェックを実行して、新しい都市を追加すると禁止されたトリプルが完了するかどうかを確認できるようになります。 
3. BFS キューを初期化します。 キューの各要素はタプルです`(prev_city, current_city)`。 から始めることができます`(0, 1)`, ここで、0 は開始前のダミー都市を表します。 また、`distance`によってキーが設定された辞書`(prev_city, current_city)`最短経路長と`parent`パスを再構築するための辞書。 
4. キューが空でない間に状態をポップします`(prev_city, current_city)`すべての近傍を反復処理します`next_city`。 もし`next_city`以降は禁止されています`(prev_city, current_city)`、スキップしてください。 もし`(current_city, next_city)`訪問されていない場合は更新してください`distance`そして`parent`、次にエンキューします`(current_city, next_city)`。 
5. 状態が`(prev_city, n)`に到達したら、バックトラックしてパスを再構築します。`parent`辞書、から始まる`(prev_city, n)`。 都市を逆に収集し、リストを逆にして正しい順序を作成します。 
6. 都市に到達せずに BFS が完了した場合`n`、-1を出力します。 それ以外の場合は、距離と再構成されたパスを出力します。 

これが機能する理由は、BFS が最初に特定の状態に到達することを保証しているためです。`(prev_city, n)`は、禁止されたトリプルを尊重する最短パスを経由します。 州内の最後の 2 つの都市のみを維持することで、不必要なメモリ使用を行わずにすべての制約を正しく適用できるようになります。 

## Python ソリューション```python
import sys
from collections import deque, defaultdict
input = sys.stdin.readline

n, m, k = map(int, input().split())
graph = [[] for _ in range(n + 1)]
for _ in range(m):
    u, v = map(int, input().split())
    graph[u].append(v)
    graph[v].append(u)

forbidden = defaultdict(set)
for _ in range(k):
    a, b, c = map(int, input().split())
    forbidden[(a, b)].add(c)

queue = deque()
queue.append((0, 1))
distance = dict()
distance[(0, 1)] = 0
parent = dict()

found = False
end_state = None

while queue:
    prev, curr = queue.popleft()
    if curr == n:
        found = True
        end_state = (prev, curr)
        break
    for nxt in graph[curr]:
        if nxt in forbidden.get((prev, curr), set()):
            continue
        if (curr, nxt) not in distance:
            distance[(curr, nxt)] = distance[(prev, curr)] + 1
            parent[(curr, nxt)] = (prev, curr)
            queue.append((curr, nxt))

if not found:
    print(-1)
else:
    path = []
    state = end_state
    while state != (0, 1):
        _, curr = state
        path.append(curr)
        state = parent[state]
    path.append(1)
    path.reverse()
    print(len(path) - 1)
    print(' '.join(map(str, path)))
```このソリューションでは、最初にグラフと禁止マップを構築します。 次に、BFS は最後の 2 つの都市を追跡しながらグラフを探索します。 BFS は最短パスの検出を保証し、親辞書によりパスを効率的に再構築できます。 使用する`(0, 1)`初期ダミー状態により、開始時に禁止されたトリプルが均一に処理されることが保証されます。 

## 実用的な例

 サンプル入力 1:```
4 4 1
1 2
2 3
3 4
1 3
1 4 3
```| キュー | 現在の状態 (前、現在) | 隣人 | 新しい州がキューに追加されました | 距離 |
 | --- | --- | --- | --- | --- |
 | [(0,1)] | (0,1) | 2,3 | (1,2)、(1,3) | 1 |
 | [(1,2),(1,3)] | (1,2) | 1,3 | (2,3) | 2 |
 | [(1,3),(2,3)] | (1,3) | 1,4 | (3,4) | 2 |

 都市 4 である (3,4) に到達します。バックトラックすると、距離 2 のパス 1 → 3 → 4 が得られ、禁止されたトリプル (1,4,3) を回避します。 

カスタム入力:```
3 2 1
1 2
2 3
1 2 3
```BFS 訪問 (0,1) → (1,2) → 禁止トリプルのため 3 に行くことができません。 パスが存在しません。 -1 を出力します。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n*m) | 各 BFS 状態は (prev、curr) であり、各エッジは状態ごとに 1 回チェックされます。 |
 | スペース | O(n^2 + k) | 距離/親の場合は O(n^2)、禁止されたトリプルの場合は O(k)。 |

 n ≤ 3000 および m ≤ 20000 の場合、n*m ≈ 6×10^7 の演算が 3 秒以内に実行可能です。 メモリは256MB以内です。 

## テストケース```python
def run(inp: str) -> str:
    import sys, io
    sys.stdin = io.StringIO(inp)
    from collections import deque, defaultdict
    n, m, k = map(int, input().split())
    graph = [[] for _ in range(n + 1)]
    for _ in range(m):
        u, v = map(int, input().split())
        graph[u].append(v)
        graph[v].append(u)
    forbidden = defaultdict(set)
    for _ in range(k):
        a, b, c = map(int, input().split())
        forbidden[(a, b)].add(c)
    queue = deque()
    queue.append((0, 1))
    distance = dict()
    distance[(0, 1)] = 0
    parent = dict()
    found = False
    end_state = None
    while queue:
        prev, curr = queue.popleft()
        if curr == n:
            found = True
            end_state = (prev, curr)
            break
        for nxt in
```
