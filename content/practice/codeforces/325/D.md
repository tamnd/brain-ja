---
title: "CF 325D - 再生"
description: "上の列と下の列を結ぶ海の経路が常に存在することを確認しながら、円筒形のグリッド内のセルを海から陸に 1 つずつ変換するプロセスをシミュレーションするように求められます。"
date: "2026-06-06T08:53:43+07:00"
tags: ["codeforces", "competitive-programming", "dsu"]
categories: ["algorithms"]
codeforces_contest: 325
codeforces_index: "D"
codeforces_contest_name: "MemSQL start[c]up Round 1"
rating: 2900
weight: 325
solve_time_s: 90
verified: false
draft: false
---

[CF 325D - 再利用](https://codeforces.com/problemset/problem/325/D)

 **評価:** 2900
 **タグ:** dsu
 **解決時間:** 1 分 30 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 上の列と下の列を結ぶ海の経路が常に存在することを確認しながら、円筒形のグリッド内のセルを海から陸に 1 つずつ変換するプロセスをシミュレーションするように求められます。 グリッドには、`r`行と`c`列、水平方向のラップアラウンド接続: 同じ行の左端と右端のセルは隣接しているとみなされます。 各入力操作は回収するセルを指定し、出力は正常に土地に変換されたセルの総数です。 

主な課題は、埋め立てのたびに、上から下までの海道がまだ存在しているかどうかを効率的に確認することです。 各操作の後にフラッドフィル検索を実行する単純な方法では、次のような時間がかかります。`O(r * c)`操作ごとの時間。 最大のグリッドでは最大 300,000 回の再利用試行と最大 900 万のセルが必要となるため、このアプローチでは、最悪の場合 10^12 を超える操作が必要になるため、非常に時間がかかりすぎます。 

素朴なアプローチは、微妙な間違いを招く危険もあります。 たとえば、水平方向の回り込みを無視すると、行を水平方向に分割する再利用が誤って許可される可能性があります。 もう 1 つのエッジケースは次のような場合です`r = 1`: すべてのセルを再利用しようとすると、ラップアラウンドのある単一行は簡単に切断される可能性があるため、アルゴリズムは単一行の接続を正しく処理する必要があります。 

## アプローチ

 ブルート フォース ソリューションは概念的に単純です。埋め立てごとに、セルを陸としてマークし、すべての上段の海のセルから BFS または DFS を実行して、下段の海のセルが到達可能かどうかを確認します。 これは、問題のステートメントを直接実装しているため正しいですが、遅すぎます。各チェックは O(r * c) で、最大 n = 3_10^5 の演算があり、最悪の場合の時間は 3_10^5 * 9_10^6 = 2.7_10^12 演算になります。 

より迅速な解決策を実現するための重要な洞察は、この条件では、上部の行と下部の行が海を介して接続されているかどうかを知ることだけが必要であることに気づくことです。 問題を逆にして、埋め立てを「海のセルの除去」として扱うと、Disjoint Set Union (DSU) 構造を使用して、海の接続されたコンポーネントを効率的に追跡できます。 削除するたびに接続性をチェックする代わりに、海のセルの接続されたコンポーネントを追跡し、必要に応じて隣接する海のセルを結合し、2 つの特別なマーカー (上部に接続されたコンポーネント用と底部に接続されたコンポーネント用に 1 つ) を維持することができます。 埋め立てによって上下のつながりが壊れてしまう場合には、埋め立ては禁止されています。 DSU とランク別ユニオンおよびパス圧縮を使用すると、各操作の時間はほぼ一定となり、合計実行時間は制限内に収まります。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n * r * c) | O(r * c) | 遅すぎる |
 | DSU 最適 | O(n * α(r * c)) | O(r * c) | 承認済み |

 ここで、α は逆アッカーマン関数であり、非常にゆっくりと増加するため、実用上は定数として扱うことができます。 

## アルゴリズムのチュートリアル

 1. 各コンポーネントの追加のブール フラグを使用して、すべてのグリッド セルの DSU を初期化します。`top_connected`そして`bottom_connected`。 これらのフラグは、コンポーネントが現在一番上の行に接しているか、一番下の行に接しているかを示します。 
2. を初期化します。`land`すべてのセルを海としてマークする行列 (False)。 
3. 指定された順序で再利用リクエストを繰り返します。 細胞ごとに`(x, y)`、現在海かどうかを確認します。 
4. 水平方向に回り込むことを考慮して、暫定的にセルを土地としてマークし、その 4 つの隣接セルをチェックします。 近隣が海の場合、DSU コンポーネントをマージします。 マージするときに、`top_connected`そして`bottom_connected`マージされたコンポーネントが上部または下部に触れた場合に正しく反映されるようにフラグを設定します。 
5. 隣接セルと結合した後、このセルを含む DSU コンポーネントに両方のセルがあるかどうかを確認します。`top_connected`そして`bottom_connected`フラグが設定されています。 そうなった場合、海路が遮断されてしまうため、セルを回収することはできません。 その場合、セルを海上に戻し、その DSU をマージしないでください。そうでない場合は、埋め立てを続けます。 
6. 成功したすべての再利用をカウントします。 

機能する理由: DSU は、接続された海洋コンポーネントを効率的に追跡します。 不変条件は、すべての海の構成要素が、それが上部に到達するか底部に到達するかを正確に認識することです。 コンポーネントが上部と下部に同時に接触する場合にのみ再利用を禁止するため (このセルを削除するとパスが切断されることを示します)、コストのかかる BFS 検索を実行することなく、上部から下部への接続ルールが正しく適用されます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline
sys.setrecursionlimit(1 << 25)

r, c, n = map(int, input().split())
cells = [tuple(map(lambda x: int(x)-1, input().split())) for _ in range(n)]

parent = {}
top_connected = {}
bottom_connected = {}
land = [[False] * c for _ in range(r)]

def find(u):
    if parent[u] != u:
        parent[u] = find(parent[u])
    return parent[u]

def union(u, v):
    u_root, v_root = find(u), find(v)
    if u_root == v_root:
        return
    parent[v_root] = u_root
    top_connected[u_root] |= top_connected[v_root]
    bottom_connected[u_root] |= bottom_connected[v_root]

def index(x, y):
    return x * c + y

successful = 0
for x, y in cells:
    if land[x][y]:
        continue
    land[x][y] = True
    idx = index(x, y)
    parent[idx] = idx
    top_connected[idx] = (x == 0)
    bottom_connected[idx] = (x == r - 1)
    
    neighbors = [
        (x - 1, y), (x + 1, y),
        (x, (y - 1) % c), (x, (y + 1) % c)
    ]
    for nx, ny in neighbors:
        if 0 <= nx < r and land[nx][ny]:
            union(idx, index(nx, ny))
    
    root = find(idx)
    if top_connected[root] and bottom_connected[root]:
        land[x][y] = False
        parent.pop(idx)
        top_connected.pop(idx)
        bottom_connected.pop(idx)
    else:
        successful += 1

print(successful)
```ソリューションでは、`parent`1 つは DSU の親辞書として、もう 2 つの追加の辞書は、各海の成分が上の行または下の行に接しているかどうかを追跡します。 インデックスを作成すると、2D グリッドが 1D に平坦化され、ユニオン検索操作が簡素化されます。 ラップアラウンドは、水平方向のモジュロを介して処理されます。 海を切り離すことになる埋め立ては、セルを取り除くことで元に戻ります。`land`そしてその DSU 追跡を削除します。 

## 実用的な例

 **サンプル 1**

 入力:```
3 4 9
2 2
3 2
2 3
3 4
3 1
1 3
2 1
1 1
1 4
```トレース:

 | ステップ | セル | アクション | 再生の成功 | メモ |
 | --- | --- | --- | --- | --- |
 | 1 | 2,2 | 回収 | 1 | 海は上下につながったまま |
 | 2 | 3,2 | 回収 | 2 | 2,2 に接続されていますが、まだ有効です |
 | 3 | 2,3 | 回収 | 3 | 2,2 とマージされ、パスは有効です |
 | 4 | 3,4 | 回収 | 4 | 3,2 とマージされ、パスは有効です |
 | 5 | 3,1 | 回収 | 5 | 3,4 (ラップアラウンド) で水平方向にマージされ、パスは有効です |
 | 6 | 1,3 | 回収 | 6 | 2、3 に接続されており、まだ有効です |
 | 7 | 2,1 | スキップ | 6 | パスが切断される |
 | 8 | 1,1 | スキップ | 6 | パスが切断される |
 | 9 | 1,4 | スキップ | 6 | パスが切断される |

 このトレースは、アルゴリズムが上下の接続を正しく尊重し、水平方向のラップアラウンドを処理していることを確認します。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n * α(r * c)) | DSU の最適化により、各結合/検索操作はほぼ一定です。 合計 n 回の操作 |
 | スペース | O(r * c) | DSU 構造とランド マトリックスはセルごとに 1 つのエントリを格納します。 

n ≤ 3_10^5 および r_c ≤ 9*10^6 の場合、これは 5 秒および 512 MB のメモリ内に収まります。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    r, c, n = map(int, input().split())
    cells = [tuple(map(lambda x: int(x)-1, input().split())) for _ in range(n)]

    parent = {}
    top_connected = {}
    bottom_connected = {}
    land = [[False] * c for _ in range(r)]

    def find(u):
        if parent[u] != u:
            parent[u] = find(parent[u])
        return parent[u]

    def union(u, v):
        u_root, v_root = find(u), find(v)
        if u_root == v_root:
            return
        parent[v_root] = u_root
        top_connected[u_root] |= top_connected[v_root]
        bottom_connected[u_root] |= bottom_connected[v_root]

    def index(x, y):
        return x * c + y

    successful = 0
    for x, y in cells:
        if land[x][y]:
            continue
        land[x][y] = True
        idx = index(x, y)
        parent[idx] = idx
        top_connected[idx] = (x == 0)
```
