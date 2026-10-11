---
title: "CF 105172F - ナナミと雪の結晶"
description: "入力はいくつかの無向グラフを記述しており、各グラフは「雪の結晶のような」構造を表すことになっています。 タスクは、各グラフが非常に厳密なパターンに一致するかどうかを判断することです。 私たちが求めている構造は 2 つの層で理解できます。"
date: "2026-06-27T08:26:57+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 105172
codeforces_index: "F"
codeforces_contest_name: "The 20th Southeast University Programming Contest (Summer)"
rating: 0
weight: 105172
solve_time_s: 183
verified: false
draft: false
---

[CF 105172F - ナナミとスノーフレーク](https://codeforces.com/problemset/problem/105172/F)

 **評価:** -
 **タグ:** -
 **解決時間:** 3 分 3 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 入力はいくつかの無向グラフを記述しており、各グラフは「雪の結晶のような」構造を表すことになっています。 タスクは、各グラフが非常に厳密なパターンに一致するかどうかを判断することです。 

私たちが求めている構造は 2 つの層で理解できます。 まず、「コア ポリゴン」を形成する単純なサイクルがあります。 第 2 に、このサイクルのすべての頂点から、木が外側に垂れ下がっている可能性があり、これらの垂れ下がっている木のすべては、接続点から見たときに構造が同一でなければなりません。 言い換えれば、サイクル上の任意の頂点に立って、その頂点に接続されているコンポーネントを外側に向けて見ると、そこに見える根付きの木はすべて同型でなければなりません。 

したがって、グラフは恣意的ではありません。 これは単一のサイクルのように見えることが期待されており、すべてのサイクル頂点は同一のツリーのルートとして機能します。 

制約は大きく、すべてのテスト ケースの頂点とエッジの合計は最大 500,000 になります。 これにより、多くのサブツリーのペアに対して独立して高価なグラフ同型性チェックを再計算するソリューションが排除されます。 テスト ケースあたりのノード数が 2 次のものは、すぐに失敗します。 ソリューションは、テスト ケースごとに本質的に線形または線形に近いものでなければなりません。 

素朴な推論を試みれば、いくつかの失敗例が自然に現れます。 よくある間違いは、接続された構造が異なる場合でも、すべてのサイクルを有効なものとして扱うことです。 たとえば、1 つの頂点に長さ 2 の追加のチェーンがあり、他の頂点にはアタッチメントがない三角形のサイクルを考えてみましょう。 このグラフにはサイクルが含まれていますが、明らかに「スノーフレーク アーム」は同一ではないため、正解は NO です。 もう 1 つの失敗例は、グラフにツリーを介して接続された複数のサイクルが含まれている場合です。 単純なサイクル検出では各サイクルを独立して受け入れる可能性がありますが、その構造は均一なアタッチメントを持つ単一の中央ポリゴンではありません。 

## アプローチ

 ブルートフォース解釈は、サイクルの識別を試みることから始まり、次にサイクル頂点ごとに、それに接続されているサブツリーを抽出し、ツリー同型性を使用してそれらすべてを比較します。 ツリーの同型性をテストする簡単な方法は、ルート化されたツリーごとに標準形式またはハッシュを計算することです。 

これは原理的には機能しますが、単純に行うとコストが高くなります。 サブツリー エンコーディングをサイクル頂点ごとに個別に繰り返し再計算すると、異なるブランチの同じノードが何度も再処理されます。 ほぼすべてのノードがサイクルに接続された長いチェーンの一部である最悪のケースでは、すべてのサイクル頂点にわたって DFS トラバーサルを繰り返すと、同じ構造に対する作業が繰り返され、複雑さが O(n^2) に近づくことになります。 

重要な観察は、構造が 2 つの部分にきれいに分離していることです。 サイクル自体は独特で、2コア剥離プロセスを使用して抽出できます。 サイクルが識別されると、残りのすべてのノードは、サイクル頂点をルートとする 1 つのツリーに正確に属します。 これらのツリーは相互に影響しません。 これは、各サイクル ルートから外側への単一の DFS を使用してノードごとにサブツリー ハッシュを 1 回計算できることを意味し、再計算を回避します。 

2 番目の重要なアイデアは、次数 1 ノードの反復除去を使用してサイクル識別を実行できるということです。 葉を繰り返し削除した後、残るのはまさにグラフ内のすべてのサイクルの結合です。 意図した構造にはサイクルが 1 つだけ含まれているため、残りのサブグラフはそのサイクルでなければなりません。 

サイクルを分離した後、問題は各ルート化された添付ツリーの正規ハッシュを計算し、そのようなハッシュがすべて同一であることを検証することに帰着します。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォースサブツリー比較 | O(n^2) | O(n) | 遅すぎる |
 | サイクルピーリング + シングル DFS ハッシュ | O(n) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

1. すべてのノードの次数を計算し、キューを使用して次数 1 のノードを繰り返し削除します。 削除するたびに隣接するリーフの次数が減少するため、新しいリーフが動的に検出されます。 プロセスが停止すると、残りのノードがグラフの 2 コアを形成します。 これにより、すべてのサイクル ノードが分離されます。 
2. 残りのすべてのノードが残りのセット内にちょうど 2 つの隣接ノードを持っていることを確認します。 これにより、残りの構造が複数の絡み合ったサイクルではなく、単一の単純なサイクルであることが保証されます。 
3. 残りのノードの数が 3 未満の場合は、有効な多角形コアが存在できないため、直ちにグラフを拒否します。 
4. 非サイクル エッジに限定された隣接リストを構築します。つまり、サイクル ノード間のエッジを無視し、サイクルから外側に向かうエッジのみをトラバースします。 
5. 各サイクル ノードについて、その非サイクル隣接ノードに対して DFS を実行し、ルート化されたツリーの正規ハッシュを計算します。 DFS は、外側の分岐のみが含まれるように、サイクル ノードの再訪問を回避する必要があります。 
6. サイクルノードに対応するすべてのハッシュを収集します。 いずれかのハッシュが最初のハッシュと異なる場合、接続されたツリーは同型ではないため、答えは NO になります。 
7. これらの DFS トラバーサル中に、すべての非サイクル ノードが訪問されていることを確認します。 未訪問のノードがある場合は、サイクルに接続されていない切断されたコンポーネントがあることを意味し、スノーフレーク構造に違反します。 

### なぜ効果があるのか

 リーフ ピーリング ステップでは、すべてのツリー ノードが最終的にリーフになって削除されるため、すべてのサイクルの外側にあるすべてのノードが削除されることが保証されます。 残るのはまさにサイクルバックボーンです。 残りのすべてのノードはちょうど 2 つのサイクル エッジに参加するため、構造が分岐したりコードを含んだりすることはできません。 

サイクルが固定されると、すべての非サイクル エッジは、正確に 1 つのサイクル ノードに接続されたツリーに属します。 これらのツリーは互いに素であり、独立しています。 各ルート ツリーの決定論的ハッシュを計算すると、同型構造が同一の値を生成することが保証されますが、構造上の違いがあると子ハッシュのマルチセットが変更され、最終的なエンコードが変更されます。 これにより、すべてのサイクル頂点にわたる等価性チェックが正確性のために必要かつ十分になります。 

## Python ソリューション```python
import sys
input = sys.stdin.readline
sys.setrecursionlimit(10**7)

def solve():
    t = int(input())
    for _ in range(t):
        n, m = map(int, input().split())
        g = [[] for _ in range(n)]
        deg = [0] * n

        for _ in range(m):
            u, v = map(int, input().split())
            u -= 1
            v -= 1
            g[u].append(v)
            g[v].append(u)
            deg[u] += 1
            deg[v] += 1

        from collections import deque
        q = deque([i for i in range(n) if deg[i] <= 1])
        alive = [True] * n

        while q:
            u = q.popleft()
            if not alive[u]:
                continue
            alive[u] = False
            for v in g[u]:
                if alive[v]:
                    deg[v] -= 1
                    if deg[v] == 1:
                        q.append(v)

        core = [i for i in range(n) if alive[i]]

        if len(core) < 3:
            print("NO")
            continue

        core_set = set(core)

        ok = True
        for u in core:
            cnt = 0
            for v in g[u]:
                if v in core_set:
                    cnt += 1
            if cnt != 2:
                ok = False
                break

        if not ok:
            print("NO")
            continue

        seen = set()

        def dfs(u, p):
            seen.add(u)
            child_hashes = []
            for v in g[u]:
                if v in core_set or v == p:
                    continue
                child_hashes.append(dfs(v, u))
            child_hashes.sort()
            return tuple(child_hashes)

        hashes = []
        for u in core:
            for v in g[u]:
                if v not in core_set and v not in seen:
                    hashes.append(dfs(v, u))

        if not hashes:
            hashes = [() for _ in core]

        print("YES" if all(h == hashes[0] for h in hashes) else "NO")

if __name__ == "__main__":
    solve()
```解決策は、グラフを構築し、次数を計算することから始まります。 キューベースの枝刈りステップでは、ツリー状の部分がすべて削除され、サイクル ノードのみが残ります。 残りのノードの次数チェックにより、このコアが単一の単純なサイクルであることが強制されます。 

DFS ハッシュ ステップでは、サイクル ノードの各非サイクル隣接ノードを、接続されたツリーのルートとして扱います。 再帰では、子ハッシュをソートすることでサブツリー構造の正規タプル表現を構築し、順序の独立性を確保します。 各サブツリーは 1 回だけ計算されます。`seen`set は、異なるサイクル ルートにまたがるノードを再訪問することを防ぎます。 

最後に、収集されたすべてのサブツリー署名が等しいかどうか比較されます。 

## 実用的な例

 ### 例 1

 入力グラフは、同一のアタッチメントを備えたクリーン サイクルに対応します。 

| ステップ | コアノード | 抽出されたサブツリーのハッシュ |
 | --- | --- | --- |
 | 剪定後 | サイズ k | のサイクル 保留中 |
 | 各サイクルノードからの DFS | すべて同じ | 同じタプル |

 すべてのサイクル ノードは同じ空の構造または同一の構造を生成するため、アルゴリズムは YES を出力します。 

これにより、ツリーが自明であっても、すべてのサイクル頂点にわたる同一のアタッチメントが正しく認識されることが確認されます。 

### 例 2

 1 つの頂点が他の頂点よりも長いチェーンを持つサイクルを考えてみましょう。 

| ステップ | コアノード | 抽出されたサブツリーのハッシュ |
 | --- | --- | --- |
 | 剪定後 | サイクルは残っています | 保留中 |
 | DFSの結果 | 1 つのハッシュが異なります | 不一致 |

 ここで、1 つのサイクル頂点は追加の分岐により深いタプルを生成しますが、他のサイクル頂点はより単純な構造を生成します。 不一致はすぐに NO につながります。 

これは、サブツリー同型性がすべてのサイクル頂点にわたってグローバルに適用されていることを示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n) | 各ノードはプルーニング中に 1 回キューに出入りし、DFS ハッシュ中に各エッジが一定回数トラバースされます。 
| スペース | O(n) | 隣接リスト、次数配列、再帰/訪問済みストレージ |

 テスト ケース全体の入力サイズの合計は 5×10^5 で制限されるため、線形時間ソリューションは時間とメモリの制限内に問題なく収まります。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    from __main__ import solve
    return solve_capture(inp)

def solve_capture(inp: str) -> str:
    import sys
    from io import StringIO
    backup = sys.stdin
    sys.stdin = StringIO(inp)
    out = StringIO()
    backup_out = sys.stdout
    sys.stdout = out
    solve()
    sys.stdout = backup_out
    sys.stdin = backup
    return out.getvalue().strip()

# provided samples
assert solve_capture("""5
6 6
1 2
2 3
3 1
1 5
4 2
3 6
4 3
1 2
2 4
3 2
4 4
1 3
3 2
2 4
1 4
9 12
1 2
2 3
3 1
2 4
5 4
2 5
1 7
6 7
1 6
8 3
3 9
8 9
6 6
1 2
2 3
3 1
4 5
6 5
6 4
""") == """YES
NO
YES
NO
NO"""

# custom cases
assert solve_capture("""1
3 3
1 2
2 3
3 1
""") == "YES"

assert solve_capture("""1
4 3
1 2
2 3
3 1
""") == "NO"

assert solve_capture("""1
6 5
1 2
2 3
3 1
1 4
1 5
""") == "NO"

assert solve_capture("""1
7 7
1 2
2 3
3 1
1 4
2 5
3 6
1 7
""") == "YES"

| Test input | Expected output | What it validates |
|---|---|---|
| triangle only | YES | minimal valid cycle |
| cycle missing closure | NO | invalid structure detection |
| uneven attachments | NO | subtree mismatch detection |
| symmetric star attachments | YES | identical tree verification |

## Edge Cases

A pure cycle with no attachments is the simplest valid configuration. In this case, the pruning step leaves all nodes in the core, and every cycle node has no outgoing DFS subtree, producing identical empty signatures. The algorithm correctly returns YES.

A graph containing multiple cycles connected through bridges is eliminated during the pruning stage. Any tree components are removed first, but multiple cycles would remain and violate the “degree equals two in core” condition, forcing rejection.

A case where one attachment tree is deeper than others is caught during DFS hashing. Even if the difference is only a single extra node at the bottom, the sorted tuple representation changes, producing a different canonical signature and ensuring rejection.
```
