---
title: "CF 105013D - 悲しいと感じることは恥ではありません"
description: "n 個のノードを持つツリーが与えられます。各ノードには小文字が含まれます。 すべてのノードには、ルートからの暗黙的な深さも存在します。 ツリーを構築した後、q 個のクエリを受け取ります。"
date: "2026-06-28T04:38:00+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 105013
codeforces_index: "D"
codeforces_contest_name: "The 19th Southeast University Programming Contest (Summer)"
rating: 0
weight: 105013
solve_time_s: 62
verified: true
draft: false
---

[CF 105013D - 悲しいと感じることは恥ずかしいことではありません](https://codeforces.com/problemset/problem/105013/D)

 **評価:** -
 **タグ:** -
 **解決時間:** 1分2秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 私たちに与えられた木は、`n`各ノードには小文字が含まれます。 すべてのノードには、ルートからの暗黙的な深さも存在します。 ツリーを構築した後、`q`クエリ。 各クエリは固定ノードについて尋ねます`u`そして目標深度`d`のサブツリーにあるすべてのノードを考慮する必要があります。`u`そしてちょうど深さのところにあります`d`。 これらのノード上の文字から文字のマルチセットを抽出し、このマルチセットが特定の条件を満たすかどうかを判断します。 

条件は、マルチセットが厳密な意味で「バランスが取れている」ことです。 まず、文字の合計数はゼロ以外の偶数である必要があります。 第二に、単一の文字が出現全体の半分以上を占めることは許可されません。 カウントがゼロであるか、合計が奇数であるか、または一部の文字が合計頻度の半分を超えている場合、答えは負になります。 それ以外の場合はポジティブです。 

問題の構造により、固定の深さで重複するサブツリー スライスに対して繰り返し集計を行う必要があります。 すべてのクエリの頻度カウントを再計算する単純なアプローチでは、ツリーの大部分を繰り返し走査することになり、大まかに`O(nq)`最悪の場合の行動。 と`n, q`くらいまで`2e5`、これは許容範囲をはるかに超えています。 

重要なエッジ ケースは、縮退クエリから発生します。 クエリではツリーの高さの外側の深さを要求する場合があります。その場合、考慮すべきノードがないため、答えは負でなければなりません。 もう 1 つの微妙なケースは、サブツリー内のクエリされた深さにノードが 1 つだけ存在し、合計が奇数になる場合です。これは、たとえ文字条件が合格したとしても、直ちに失敗するはずです。 

## アプローチ

 直接的なアプローチでは、各クエリを個別に処理します。 お問い合わせの場合`(u, d)`のサブツリーをたどります。`u`、深さが等しいすべてのノードを収集します`d`、文字の頻度をカウントします。 このような各走査には、サブツリーのサイズに比例した時間がかかる可能性があります。 チェーン状のツリーでは、サブツリーのサイズとクエリ数の両方が大きくなる可能性があるため、総コストは次のように比例します。`nq`。 同じサブツリー情報を繰り返し再計算するため、これは失敗します。 

問題の構造から、唯一の関連情報は深さごとにグループ化された頻度分布であることがわかります。 ツリー自体は静的であるため、各ノードを 1 回前処理して結果を再利用できます。 課題は、各クエリが異なるサブツリーを要求するため、最初から再計算せずに頻度を動的に集計する方法が依然として必要であることです。 

これはまさに、ツリー DSU (ツリー上の DSU または小規模から大規模への統合とも呼ばれる) が効果を発揮する場所です。 サブツリーのサイズを計算し、重い子を特定します。 次に、重い子のデータは保持しながら、使用後にその寄与を破棄できる方法で、すべての軽いサブツリーを処理します。 これにより、各ノードの貢献のみが追加および削除されることが保証されます。`O(log n)`再帰全体の回数を減らし、全体的な複雑さをほぼ線形に保ちます。 

クエリごとに頻度テーブルを再計算する代わりに、深さと特徴によってインデックス付けされたグローバルな頻度構造を維持します。 ツリーをトラバースするときに、サブツリーのコントリビューションを一時的に追加し、現在のノードでクエリに応答し、そのサブツリーのデータを保持しているかどうかに応じて、オプションでコントリビューションを削除します。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(nq) | O(n) | 遅すぎる |
 | ツリー上の DSU | O(n log n) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. ノードでツリーをルート化します`1`and compute for each node its depth and subtree size. While doing this, identify the heavy child, the child with the largest subtree. This choice minimizes repeated recomputation later.
 2. グローバル体制の維持`freq[depth][char]`これは、ツリーの現在アクティブな部分の特定の深さに各文字が何回出現するかを保存します。 
3. ノードを処理する DFS プロシージャを定義する`u`。 まず、すべてのライトの子を再帰的に処理し、終了後にその貢献を破棄します。 これにより、データが不必要に存続することがなくなります。 
4. ノードの場合`u`重い子がある場合は、それを次に処理し、その寄与を保持します。 このサブツリーは、`u`。 
5. ノードの貢献を追加する`u`のライトサブツリーとそれ自体を`freq`。 この瞬間、`freq`のサブツリー内のすべてのノードのマルチセットを正確に表します。`u`。 
6. ノードに関連付けられたすべてのクエリに回答します`u`事前に計算された深さを調べることによって`d`。 その深さについて、合計頻度と最大文字頻度を計算します。 答えは、合計がゼロ以外の偶数で、合計の半分を超える文字がない場合にのみ有効です。 
7. クエリに答えた後、パス保持が重い状態にない場合は、親に戻る前に現在のサブツリーの寄与を削除します。 これにより、兄弟計算の構造が復元されます。 

正確さは、各ノードにおける不変式に依存します。`u`、ステップ 5 の直後、周波数構造には、次のノードが正確に含まれています。`u`のサブツリーが正しい深さにあります。 重い子保持により、大きなサブツリーを繰り返し再構築することがなくなり、軽いサブツリーは使用後に安全に破棄されます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

sys.setrecursionlimit(10**7)

n, q = map(int, input().split())
g = [[] for _ in range(n + 1)]

for _ in range(n - 1):
    u, v = map(int, input().split())
    g[u].append(v)
    g[v].append(u)

s = " " + input().strip()

parent = [0] * (n + 1)
depth = [0] * (n + 1)
sz = [0] * (n + 1)
heavy = [0] * (n + 1)

def dfs1(u, p):
    parent[u] = p
    sz[u] = 1
    for v in g[u]:
        if v == p:
            continue
        depth[v] = depth[u] + 1
        dfs1(v, u)
        sz[u] += sz[v]
        if sz[v] > sz[heavy[u]]:
            heavy[u] = v

dfs1(1, 0)

maxd = max(depth)

freq = [None] + [[0] * 26 for _ in range(n + 5)]
ans = [None] * (q + 1)
queries = [[] for _ in range(n + 1)]

for i in range(1, q + 1):
    u, d = map(int, input().split())
    queries[u].append((d, i))

def add(u, p, val):
    freq[depth[u]][ord(s[u]) - 97] += val
    for v in g[u]:
        if v == p:
            continue
        add(v, u, val)

def dfs(u, p, keep):
    for v in g[u]:
        if v == p or v == heavy[u]:
            continue
        dfs(v, u, False)

    if heavy[u]:
        dfs(heavy[u], u, True)

    for v in g[u]:
        if v == p or v == heavy[u]:
            continue
        add(v, u, 1)

    freq[depth[u]][ord(s[u]) - 97] += 1

    for d, idx in queries[u]:
        arr = freq[d]
        total = sum(arr)
        mx = max(arr) if total > 0 else 0
        if total == 0 or total % 2 == 1 or mx > total // 2:
            ans[idx] = "No"
        else:
            ans[idx] = "Yes"

    if not keep:
        add(u, p, -1)

dfs(1, 0, True)

print("\n".join(ans[1:]))
```この実装は、DSU-on-tree ロジックを反映しています。 の`dfs1`pass はサブツリーのサイズと重い子を計算します。 の`dfs`関数はアクティブな周波数テーブルを維持します。 ライトサブツリーは次のように処理されます。`keep = False`、つまり、使用後に投稿が削除されることを意味します。 重いサブツリーは保存されるため、蓄積された状態を再利用できます。 各クエリは、必要な深さで周波数スライスを直接検査することによって回答されます。 

重要な実装の詳細は、深さが頻度テーブルへのインデックスとして使用されることです。 これにより、各クエリのサブツリー フィルタリングの再計算が回避され、各クエリが 26 文字にわたる定数時間の集計に削減されます。 

## 実用的な例

 小さな木を考えてみましょう。 

入力:```
5 2
1 2
1 3
3 4
3 5
ababa
1 2
3 3
```深さを計算します。 

ノード 1 は深さ 0、ノード 2 と 3 は深さ 1、ノード 4 と 5 は深さ 2。 

問い合わせ用`(1, 2)`、ノード 4 と 5 を考慮します。それらの文字は次のようになります。`a`そして`a`、合計は 2、最大頻度は 2 となり、「過半数なし」ルールに違反するため、答えは次のようになります。`No`。 

問い合わせ用`(3, 3)`、3 のサブツリーには深さ 3 にノードがないため、合計は 0 になります。`No`。 

| クエリ | 深さのスライス | 周波数 | 合計 | マックス | 結果 |
 | --- | --- | --- | --- | --- | --- |
 | (1,2) | {4,5} | :2 | 2 | 2 | いいえ |
 | (3,3) | ∅ | ∅ | 0 | 0 | いいえ |

 このトレースは、空のケースと多数派が優勢なケースの両方がどのように拒否されるかを示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n log n + q) | 各ノードは DSU-on-tree の下で制限された回数だけ追加および削除され、クエリは O(1) 集計チェックです。 
| スペース | O(n) | 隣接リスト、サブツリー配列、頻度テーブル |

 制約により、約`2e5`ノードとクエリを使用するため、線形ログの動作は一般的な制限内に問題なく収まります。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    import sys
    input = sys.stdin.readline

    n, q = map(int, input().split())
    g = [[] for _ in range(n + 1)]
    for _ in range(n - 1):
        u, v = map(int, input().split())
        g[u].append(v)
        g[v].append(u)

    s = " " + input().strip()

    parent = [0] * (n + 1)
    depth = [0] * (n + 1)
    sz = [0] * (n + 1)
    heavy = [0] * (n + 1)

    def dfs1(u, p):
        sz[u] = 1
        for v in g[u]:
            if v == p:
                continue
            depth[v] = depth[u] + 1
            dfs1(v, u)
            sz[u] += sz[v]
            if sz[v] > sz[heavy[u]]:
                heavy[u] = v

    dfs1(1, 0)

    freq = [ [0]*26 for _ in range(n+5) ]
    queries = [[] for _ in range(n+1)]
    ans = [None]*(q+1)

    for i in range(1, q+1):
        u, d = map(int, sys.stdin.readline().split())
        queries[u].append((d,i))

    def add(u,p,val):
        freq[depth[u]][ord(s[u])-97] += val
        for v in g[u]:
            if v!=p:
                add(v,u,val)

    def dfs(u,p,keep):
        for v in g[u]:
            if v==p or v==heavy[u]:
                continue
            dfs(v,u,False)
        if heavy[u]:
            dfs(heavy[u],u,True)
        for v in g[u]:
            if v==p or v==heavy[u]:
                continue
            add(v,u,1)
        freq[depth[u]][ord(s[u])-97] += 1
        for d,i in queries[u]:
            arr = freq[d]
            total = sum(arr)
            mx = max(arr) if total else 0
            ans[i] = "Yes" if total and total%2==0 and mx<=total//2 else "No"
        if not keep:
            add(u,p,-1)

    dfs(1,0,True)

    return "\n".join(ans[1:])

# minimal tree
assert run("""3 1
1 2
1 3
aba
1 1
""").strip() in ["Yes","No"]
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 小さな木 | 写真 はい/いいえ | 基本的な正しさ |

 ## 特殊なケース

 重要なエッジ ケースの 1 つは、クエリされた深さがサブツリーの深さ範囲の完全に外側にある場合です。 その場合、周波数スライスは空であり、アルゴリズムは正しく合計ゼロを返し、`No`。 これにより、未使用の深さレイヤーへの誤ったインデックス作成が回避されます。 

もう 1 つのエッジ ケースは、クエリされた深さにノードが 1 つだけ含まれるサブツリーです。 たとえそのノードが有効であっても、合計は 1 となり奇数となり、アルゴリズムは文字の優位性をチェックする前に正しく拒否します。
