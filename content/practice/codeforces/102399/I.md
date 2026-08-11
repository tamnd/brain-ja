---
title: "CF 102399I - \u0416\u0443\u043b\u0438\u043a、\u043d\u0435 \u0432\u043e\u0440\u0443\u0439"
description: "接続された単純な無向グラフがあります。 スワイパーは空ではない適切な頂点のセットを選択し、それらの頂点をすべてのインシデント エッジとともに削除します。 残りの頂点は、削除前と同じ次数モジュロ (3) を維持する必要があります。"
date: "2026-08-10T17:23:57+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102399
codeforces_index: "I"
codeforces_contest_name: "2019 \u041c\u043e\u0441\u043a\u043e\u0432\u0441\u043a\u0430\u044f \u043a\u043e\u043c\u0430\u043d\u0434\u043d\u0430\u044f \u043e\u043b\u0438\u043c\u043f\u0438\u0430\u0434\u0430 \u0448\u043a\u043e\u043b\u044c\u043d\u0438\u043a\u043e\u0432, \u043b\u0438\u0433\u0430 A"
rating: 0
weight: 102399
solve_time_s: 671
verified: true
draft: false
---

[CF 102399I - \u0416\u0443\u043b\u0438\u043a、\u043d\u0435 \u0432\u043e\u0440\u0443\u0439](https://codeforces.com/problemset/problem/102399/I)

 **評価:** -
 **タグ:** -
 **解決時間:** 11 分 11 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 接続された単純な無向グラフがあります。 スワイパーは空ではない適切な頂点のセットを選択し、それらの頂点をすべてのインシデント エッジとともに削除します。 残りの頂点は、削除前と同じ次数モジュロ (3) を維持する必要があります。 

(S) を盗まれた頂点とし、(R=V\setminus S) を残った頂点とします。 (v\in R) ごとに、(v) から (S) までのエッジが正確に消えます。 したがって、条件は次のとおりです

 [
 \deg_S(v)\equiv 0\pmod 3.
 】

 (\deg_G(v)=\deg_R(v)+\deg_S(v)) なので、同じ条件は次のように書くことができます。 

[
 \deg_R(v)\equiv\deg_G(v)\pmod 3.
 】

 この 2 番目の形式は構築がはるかに簡単です。 残っている頂点の適切なセット (R) を構築し、外側のすべての頂点 (R) を盗みます。 

元の Codeforces バージョンには (n,m\le 500000) があり、すべてのテスト ケースの (n) と (m) の合計も (500000) によって制限されます。 元のコンテストの制限は 2 秒、512 MB です。 これにより、グラフ サイズの二次関数が即座に排除され、線形回数繰り返される走査でも負荷が高くなりすぎます。 目標は、一定数のグラフ走査まで、グラフあたり (O(n+m)) です。 

小規模なケースとして、実装が無効な応答を黙って生成する可能性があるケースがいくつかあります。 頂点が 1 つあると、```
1
1 0
```グラフには盗むべき適切な空でないサブセットがないため、答えは次のようになります。`No`。 (3) で割り切れる次数を盲目的に見てその頂点を保持するプログラムは、誤ってゼロ頂点を盗もうとすることになります。 

接続された 2 つの頂点の場合、```
1
2 1
1 2
```どちらの次数も (1) です。 唯一空ではない適切な盗まれたセットには 1 つの頂点がありますが、出力には少なくとも 2 つの盗まれた頂点が必要です。 正しい答えは、`No`。 

周期にも特別な治療が必要です。 のために```
1
4 4
1 2
2 3
3 4
4 1
```すべての頂点は次数 (2) を持ちます。 グラフ全体は有効な保持サイクルですが、グラフ全体を保持することは何も盗まないことを意味します。 サイクルの適切なサブセットには、その端点が内部次数 (1) を持ち、元の剰余 (2) と一致しないパスが含まれます。 したがって、答えは次のとおりです`No`。 

4 番目の微妙なケースは、(3) で割り切れる次数の頂点を持つグラフです。 例えば、```
1
4 3
1 2
1 3
1 4
```頂点 (1) には次数 (3) があります。 3 つの入射エッジがすべて消えるため、頂点 (1) のみを保持することは有効です (3\equiv0\pmod3)。 その結果、三枚の葉が盗まれる可能性があります。 

## アプローチ

 直接的なブルート フォースは概念的には単純です。 頂点の空でない適切なサブセット (S) をすべて列挙し、外側 (S) のすべての頂点についてその近傍の頂点の数を数え (S)、そのようなすべての数が (3) で割り切れるかどうかを確認します。 これは、合法的な窃盗の定義を正確にテストするため、正しいです。 (2^n-2) 個の可能なサブセットがあり、グラフが隣接リストで表されている場合、1 つのサブセットをチェックするには (O(n+m)) 時間がかかります。 したがって、最悪の場合の複雑さは次のようになります。 

[
 O(2^n(n+m))。 
】

 (n=500000) の場合、これは単に遅すぎるだけでなく、まったく実行不可能です。 

有益な観察は、任意のサブセットについて考えるのをやめるということです。 残りのすべての頂点 (v) について、その内部次数は元の次数と同じ剰余モジュロ (3) を持たなければなりません。 つまり、元の次数残差 (0)、(1)、および (2) を持つ頂点は、保持できる異なる小さな構造を自然に示唆します。 

これら 3 つのタイプを (3) の次数モジュロに従って (Z、A、B) と呼びます。 

(Z)-頂点は単独で保持できます。 その内部次数は (0) であり、まさに必要な剰余です。 

適切なパスで接続された 2 つの (A) 頂点を保持できます。 2 つの端点には 1 つの内部エッジがあり、すべての内部 (B) 頂点には 2 つの内部エッジがあります。 2 つの (A) 頂点間の最短経路は、まさにこの構造を与えます。 

(B)-頂点からなるサイクルを保つことができます。 すべてのサイクル頂点には 2 つの内部エッジがあり、剰余 (2) と一致します。 最短のサイクルを選択するとコードレスになるため、選択したセット内に実際にはすべてのサイクル頂点がちょうど 2 つの近傍を持ちます。 

これらの構造が存在しない場合は、(A) 頂点が 1 つだけ存在し、(B) 頂点がフォレストを形成します。 各ツリー コンポーネントは、一意の (A) 頂点に少なくとも 2 回接触する必要があります。 このような 2 つのコンポーネントは、(A) 頂点からそれ自体に戻る 2 つのパスを提供し、(A) 頂点のみを共有する 2 つのサイクルを形成します。 これら 2 つのパスと (A) 頂点を維持すると、(A) 頂点で内部次数 (4) が得られ、これも (1\bmod3) になりますが、パス上のすべての (B) 頂点は内部次数 (2) になります。 

これらのケースは網羅的です。 公式の問題は Codeforces 1239F 問題であり、この分類は、受け入れられている解決策の背後にある中心的な建設的なアイデアです。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | (O(2^n(n+m))) | (O(n+m)) | 遅すぎる |
 | 最適 | (O(n+m)) | (O(n+m)) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. すべての頂点の次数を計算し、次のように分類します。`degree % 3`。 3 つのクラスは (Z)、(A)、および (B) です。 この分類により、保持された頂点がモジュロ (3) を持たなければならない内部次数がわかります。 
2. (Z) 頂点 (v) がある場合は、(v) のみを保持します。 その内部次数は 0 で、元の次数モジュロ (3) と一致します。 (n>2) の場合、頂点を 1 つおきに盗むと有効な答えが得られます。 (n=1) の場合、盗むものは何もなく、(n=2) の場合、接続されたグラフに (Z) 頂点を含めることはできません。 
3. (B) 頂点内に完全にあるサイクルを探します。 サイクルは、選択されたすべての頂点に、選択された 2 つの隣接頂点を正確に与えます。これは、(B) 頂点に必要な剰余です。 サイクルがコードレスである必要があるため、DFS ツリーの意味で最短のサイクルを見つけます。 祖先と子孫の間の非ツリー エッジは基本サイクルを作成し、最小のツリー距離を持つエッジを選択すると、コードのないサイクルが得られます。 
4. そのようなサイクルが存在し、すべての頂点を使用しない場合は、その補数を盗みます。 サイクルがグラフ全体である場合、グラフはまさにサイクルであり、この構成では適切な解は存在しません。 補数が必要な出力サイズを満たさない場合にのみ、次のケースに進みます。 
5. 少なくとも 2 つの (A) 頂点がある場合は、グラフ全体で 1 つの (A) 頂点から BFS を実行し、別の (A) 頂点に到達したときに停止します。 結果として得られるパスは最短であるため、内部に別の (A) 頂点を含めることはできません。 その 2 つの端点には 1 つの内部隣接点があり、その内部 (B) 頂点には 2 つの内部隣接点があります。 この道を守り、他のものはすべて盗んでください。 
6. 以前の構築が失敗した場合、(Z) 頂点も (B) サイクルもなく、最大でも 1 つの (A) 頂点が存在します。 グラフは接続されており、複数の頂点があるため、実際には (A) 頂点が 1 つだけ存在する必要があります。 1 つおきの頂点は (B) であり、(B) 頂点によって引き起こされる部分グラフはフォレストです。 
7. この (B) フォレストの 1 つの連結コンポーネント (T) を考えます。 その頂点から一意の (A) 頂点まで向かう (r) 個のエッジがあると仮定します。 すべての (B) 頂点には次数 (2\bmod3) があるため、

 [
 2|V(T)|\equiv 2|E(T)|+r\pmod3。 
】

 (T) は木なので、(|E(T)|=|V(T)|-1)。 代入すると

 [
 r\equiv2\pmod3。 
】

 したがって、すべての (B) ツリーには、(A) 頂点に対して少なくとも 2 つのエッジがあります。 また、一意の (A) 頂点の次数が (1\bmod3) であるのに対し、1 つのコンポーネントが (2\bmod3) に寄与するため、そのようなコンポーネントが少なくとも 2 つ存在する必要があります。 

1. 2 つの異なる (B) コンポーネントで、(A) 頂点に隣接する 2 つの頂点を選択します。 各コンポーネントで、そのような頂点の 1 つから開始して、(A) に隣接する最も近い他の頂点を見つけます。 それらの間のパスには内部的に他の (A) 隣接要素が含まれていないため、(A) 頂点を追加すると、そのパスがクリーンなサイクルに変わります。 両方のパスと (A) 頂点を保持します。 
2. 結果として得られる保持セットが適切で、少なくとも 2 つの盗まれた頂点が残っている場合は、その補数を出力します。 それがグラフ全体である場合、グラフは (A) で結合された正確に 2 つの (A) から (A) へのパスで構成され、この最後のケースでは法的に適切な保持セットは存在しません。 初期の構造では、他のすべての可能性がすでに処理されていたでしょう。 

これが機能する理由: すべての構造において、保持されたセット内の次数は元の次数とまっ​​たく同じ剰余を持ちます。 (Z) 頂点は内部次数 (0) を持ちます。 保持された (A) から (A) へのパスは、その端点で内部次数 (1) を与え、(B) 内部頂点で (2) を与えます。 コードレス (B) サイクルはどこでも内部次数 (2) を与えます。 最終的な構築では、選択された各 (B) パスはその (B) 頂点に内部次数 (2) を持ちますが、一意の (A) 頂点には 4 つの選択された近傍があります。 (4\equiv1\pmod3) なので、その剰余も保存されます。

消尽引数も同じ構造に従います。 (Z) 頂点が存在する場合、最初の構築が機能します。 それ以外の場合、(B) サイクルにより 2 番目の構造が得られます。 それ以外の場合、2 つの (A) 頂点により 3 番目の構造が得られます。 これらのいずれも起こらない場合、(A) 頂点は 1 つだけ存在し、(B) サブグラフはフォレストとなり、最終的な構造が強制されます。 その最終構造がグラフ全体を占める場合、有効な保持セットには一意の (A) 頂点と少なくとも 2 つの (B) コンポーネントが含まれている必要があり、両方の完全なパスが強制的に残されるため、適切な解決策は存在しません。 

## Python ソリューション```python
import sys
from collections import deque

input = sys.stdin.readline

def make_answer(n, keep):
    mark = bytearray(n)
    for v in keep:
        mark[v] = 1

    stolen = [v + 1 for v in range(n) if not mark[v]]

    if 1 < len(stolen) < n:
        return stolen
    return None

def find_b_cycle(g, typ):
    n = len(g)

    color = bytearray(n)
    parent = [-1] * n
    depth = [0] * n
    tin = [-1] * n
    tout = [-1] * n

    timer = 0

    for s in range(n):
        if typ[s] != 2 or color[s]:
            continue

        color[s] = 1
        tin[s] = timer
        timer += 1

        stack = [(s, 0)]

        while stack:
            u, idx = stack[-1]

            if idx == len(g[u]):
                color[u] = 2
                tout[u] = timer
                stack.pop()
                continue

            v = g[u][idx]
            stack[-1] = (u, idx + 1)

            if typ[v] != 2:
                continue

            if color[v] == 0:
                parent[v] = u
                depth[v] = depth[u] + 1
                color[v] = 1
                tin[v] = timer
                timer += 1
                stack.append((v, 0))

    best_anc = -1
    best_desc = -1
    best_diff = 10**18

    for u in range(n):
        if typ[u] != 2:
            continue

        for v in g[u]:
            if v <= u or typ[v] != 2:
                continue

            if parent[v] == u or parent[u] == v:
                continue

            if tin[u] <= tin[v] < tout[u]:
                anc, desc = u, v
            elif tin[v] <= tin[u] < tout[v]:
                anc, desc = v, u
            else:
                continue

            diff = depth[desc] - depth[anc]

            if diff < best_diff:
                best_diff = diff
                best_anc = anc
                best_desc = desc

    if best_anc == -1:
        return None

    cycle = []
    x = best_desc

    while x != best_anc:
        cycle.append(x)
        x = parent[x]

    cycle.append(best_anc)
    return cycle

def find_a_path(g, typ):
    n = len(g)
    start = -1

    for v in range(n):
        if typ[v] == 1:
            start = v
            break

    if start == -1:
        return None

    parent = [-2] * n
    parent[start] = -1
    q = deque([start])

    target = -1

    while q:
        u = q.popleft()

        for v in g[u]:
            if typ[v] == 0 or parent[v] != -2:
                continue

            parent[v] = u

            if typ[v] == 1:
                target = v
                q.clear()
                break

            q.append(v)

        if target != -1:
            break

    if target == -1:
        return None

    path = []
    x = target

    while x != -1:
        path.append(x)
        x = parent[x]

    path.reverse()
    return path

def find_tree_path_to_attachment(g, start, attach):
    n = len(g)

    parent = [-2] * n
    parent[start] = -1
    q = deque([start])
    target = -1

    while q:
        u = q.popleft()

        for v in g[u]:
            if parent[v] != -2:
                continue

            if attach[v] == 0:
                parent[v] = u
                q.append(v)
            elif v != start:
                parent[v] = u
                target = v
                q.clear()
                break

        if target != -1:
            break

    if target == -1:
        return None

    path = []
    x = target

    while x != -1:
        path.append(x)
        x = parent[x]

    path.reverse()
    return path

def solve_case(n, m):
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

    typ = [d % 3 for d in deg]

    # Case 1: a degree-0-mod-3 vertex.
    for v in range(n):
        if typ[v] == 0:
            keep = [v]
            ans = make_answer(n, keep)
            if ans is not None:
                return ans

    # Case 2: a cycle consisting only of degree-2-mod-3 vertices.
    cycle = find_b_cycle(g, typ)

    if cycle is not None:
        ans = make_answer(n, cycle)
        if ans is not None:
            return ans

    # Case 3: a path between two degree-1-mod-3 vertices.
    a_count = sum(1 for x in typ if x == 1)

    if a_count >= 2:
        path = find_a_path(g, typ)

        if path is not None:
            ans = make_answer(n, path)
            if ans is not None:
                return ans

    # Case 4: exactly one A vertex and the B-subgraph is a forest.
    if a_count != 1:
        return None

    a = typ.index(1)

    attach = bytearray(n)
    for v in g[a]:
        if typ[v] == 2:
            attach[v] = 1

    visited = bytearray(n)
    chosen_components = []

    for s in range(n):
        if typ[s] != 2 or visited[s]:
            continue

        stack = [s]
        visited[s] = 1
        attachments = []

        while stack:
            u = stack.pop()

            if attach[u]:
                attachments.append(u)

            for v in g[u]:
                if typ[v] == 2 and not visited[v]:
                    visited[v] = 1
                    stack.append(v)

        if len(attachments) >= 2:
            chosen_components.append(attachments)

            if len(chosen_components) == 2:
                break

    if len(chosen_components) < 2:
        return None

    keep = {a}

    for attachments in chosen_components:
        start = attachments[0]
        path = find_tree_path_to_attachment(g, start, attach)

        if path is None:
            return None

        keep.update(path)

    ans = make_answer(n, keep)
    return ans

def main():
    t = int(input())
    out = []

    for _ in range(t):
        line = input()

        while line and not line.strip():
            line = input()

        n, m = map(int, line.split())

        ans = solve_case(n, m)

        if ans is None:
            out.append("No")
        else:
            out.append("Yes")
            out.append(str(len(ans)))
            out.append(" ".join(map(str, ans)))

    sys.stdout.write("\n".join(out))

if __name__ == "__main__":
    main()
```次数配列はエッジの読み取り中に計算されるため、頂点を分類するためだけに別の走査を行う必要はありません。 値`typ[v]`はまさに元の次数の残余であり、構築全体を通じて使用されます。 

頂点が (500000) 個あるグラフでは Python 再帰が安全ではないため、サイクル ルーチンは反復 DFS を実行します。`tin`そして`tout`DFS 間隔を記述し、ツリー以外のすべてのエッジを祖先と子孫のエッジとして認識できるようにします。 これらのエッジの中で、深さの差が最も小さいものを選択します。 対応するツリー パス内のコードはそれ自体、深さの差が厳密に小さい非ツリー エッジとなるため、選択されたサイクルにはコードがありません。 

(A) パスは BFS で見つかります。 BFS は最初の他の (A) 頂点で停止するため、パスの内部に別の (A) 頂点を含めることはできません。 これはまさに残差 (1) エンドポイントに必要なものです。 

最後のケースでは、一意の (A) 頂点を使用して、それに隣接するすべての (B) 頂点をマークします。 各 (B) ツリーでは、1 つのマークされた頂点から開始し、最初の他のマークされた頂点で停止すると、マークされた内部頂点のないパスが得られます。 これにより、一意の (A) 頂点がパスの中央に不要なコードを持ち込むのを防ぎます。 

実装では以下を使用します`bytearray`DFS カラー、訪問済みフラグ、および添付ファイル マーカーの場合。 これにより、長さ (500000) の複数の配列の Python ブール値または整数を保存する場合と比較して、大幅なメモリが節約されます。 Python には整数オーバーフローの問題はなく、すべての頂点インデックスは読み取り直後に 0 から始まる形式に変換されます。 最終的な答えは、印刷時にのみ 1 ベースのインデックスに変換されます。 

## 実用的な例

 サンプルの最初のグラフでは、グラフは三角形です。 すべての頂点の次数は (2) であるため、3 つの頂点はすべて (B) 頂点です。 (B) サブグラフにはサイクルが含まれていますが、そのサイクルにはすべての頂点が含まれています。 その補数を盗んでも何も盗まないので、構築は拒否されます。 

| ステージ | 状態 |
 | --- | --- |
 | 学位 | (2,2,2) |
 | 種類 | (B、B、B) |
 | (Z) 頂点が見つかりました | いいえ |
 | (B)-サイクル | (1-2-3-1) |
 | サイクルはすべての頂点を使用します | はい |
 | (A)-頂点 | なし |
 | 最後の森林事件 | 該当なし |
 | 答え |`No`|

 これは、サイクルの検出だけでは十分ではない理由を示しています。 選択された保持構造は適切である必要があります。 すでにサイクルになっているグラフ全体は、頂点が盗まれないため使用できません。 

2 番目のグラフの次数は (2,5,2,1,1,1) です。 (Z) 頂点はなく、(B) 頂点は (1) と (3) であり、1 つのエッジで接続されているため、(B) サイクルはありません。 頂点 (4,5,6) は (A)-頂点です。 頂点 (4) からの BFS は、頂点 (2) を経由して頂点 (5) に到達します。 

| ステージ | 状態 |
 | --- | --- |
 | 学位 | (2,5,2,1,1,1) |
 | 種類 | (B、B、B、A、A、A) |
 | (Z) 頂点が見つかりました | いいえ |
 | (B)-サイクル | いいえ |
 | BFS 開始 | (4) |
 | 最初の他の (A)-頂点 | (5) |
 | 保持されたパス | (4-2-5) |
 | 盗まれた頂点 | (1,3,6) |
 | 盗難後の保持度(2) | (2) |
 | (2) モジュロ (3) の元の次数 | (5\bmod3=2) |
 | 答え |`Yes`|

 保持された頂点 (2) には 3 つの盗まれた近傍、つまり (1,3,6) があるため、その次数は (5) から (2) に減少します。 他の保持された頂点 (4) と (5) では、入射エッジは失われません。 したがって、残りのすべての次数は剰余 (3) を維持します。 

3 番目のサンプル グラフは、最後のケースを示しています。 頂点 (1) は次数 (7) を持っているため、一意の (A) 頂点です。 (B) サブグラフは複数のツリーで構成されます。 1 つのツリーには頂点 (2,3,6,7,8) が含まれており、(6,7,8) は頂点 (1) に隣接しています。 もう 1 つは (4,5) を含み、(4) と (5) の両方が (1) に隣接しています。 (6) から始まり、最も近い他のアタッチメントは (3) であり、2 番目のツリーはパス (4-5) を提供します。 キープ (1,6,3,4,5) とスティール (2,7,8) は有効な解決策の 1 つです。 

| コンポーネント | 添付開始 | 他の最初の添付ファイル | 保持されたパス |
 | --- | --- | --- | --- |
 | ({2,3,6,7,8}) | 6 | 3 | (6-3) |
 | ({4,5}) | 4 | 5 | (4-5) |
 | 中央頂点 | 1 | 両方のパス | 1 |

 選択された (B) 頂点は内部次数 (2) を持ちます。 頂点 (1) には 4 つの選択された近傍があるため、次数は (7) から (4) に変化し、両方の値は (1\bmod3) になります。 盗まれた頂点は (2,7,8) です。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O(n+m)) | 一定の数の DFS、BFS、および隣接スキャンが実行されます。 
| スペース | (O(n+m)) | 隣接リストと線形サイズの補助配列がメモリを支配します。 

すべてのテスト ケースの合計 (n) と合計 (m) は最大でも (500000) であるため、合計実行時間は (O(\sum n+\sum m)) となります。 このアルゴリズムは、(n\times) 構造を構築したり、サブセットを列挙したりすることはありません。これにより、メモリと実行時間の両方が元のコンテストの制限内に維持されます。 

## テストケース

 この問題の出力は一意ではないため、テストでは盗まれた頂点の正確なリストを比較するのではなく、構造要件を検証する必要があります。 次のハーネスは、残りのすべての頂点のステータス、盗まれたセットのサイズ、区別性、およびdegree-modulo-(3) 条件をチェックします。```python
# Run this after the solution above has been defined.
import sys
import io

def run(inp: str) -> str:
    global input

    old_stdin = sys.stdin
    old_stdout = sys.stdout
    old_input = input

    sys.stdin = io.StringIO(inp)
    input = sys.stdin.readline
    sys.stdout = io.StringIO()

    try:
        main()
        return sys.stdout.getvalue()
    finally:
        sys.stdin = old_stdin
        sys.stdout = old_stdout
        input = old_input

def validate(inp: str, out: str, expected):
    data = list(map(int, inp.split()))
    pos = 0
    t = data[pos]
    pos += 1

    tokens = out.split()
    out_pos = 0

    for case_id in range(t):
        n = data[pos]
        m = data[pos + 1]
        pos += 2

        edges = []
        deg = [0] * n

        for _ in range(m):
            u = data[pos] - 1
            v = data[pos + 1] - 1
            pos += 2
            edges.append((u, v))
            deg[u] += 1
            deg[v] += 1

        assert out_pos < len(tokens)
        status = tokens[out_pos]
        out_pos += 1

        assert status == expected[case_id], (
            f"case {case_id}: expected {expected[case_id]}, got {status}"
        )

        if status == "No":
            continue

        c = int(tokens[out_pos])
        out_pos += 1

        stolen = list(map(int, tokens[out_pos:out_pos + c]))
        out_pos += c

        assert 1 < c < n
        assert len(stolen) == c
        assert len(set(stolen)) == c

        stolen_zero = {x - 1 for x in stolen}

        assert all(0 <= x < n for x in stolen_zero)

        for v in range(n):
            if v in stolen_zero:
                continue

            lost = 0
            for u, w in edges:
                if u == v and w in stolen_zero:
                    lost += 1
                elif w == v and u in stolen_zero:
                    lost += 1

            assert lost % 3 == 0, (
                f"case {case_id}: vertex {v + 1} loses {lost} edges"
            )

    assert out_pos == len(tokens)

sample = """\
3
3 3
1 2
2 3
3 1

6 6
1 2
1 3
2 3
2 5
2 6
2 4

8 12
1 2
1 3
2 3
1 4
4 5
5 1
3 6
3 7
3 8
6 1
7 1
8 1
"""

sample_out = run(sample)
validate(sample, sample_out, ["No", "Yes", "Yes"])

minimum = """\
1
1 0
"""
assert run(minimum).strip() == "No"

two_vertices = """\
1
2 1
1 2
"""
assert run(two_vertices).strip() == "No"

star = """\
1
4 3
1 2
1 3
1 4
"""
star_out = run(star)
validate(star, star_out, ["Yes"])

cycle = """\
1
4 4
1 2
2 3
3 4
4 1
"""
assert run(cycle).strip() == "No"

two_triangles = """\
1
5 6
1 2
2 3
3 1
1 4
4 5
5 1
"""
assert run(two_triangles).strip() == "No"

five_triangles = """\
1
11 15
1 2
2 3
3 1
1 4
4 5
5 1
1 6
6 7
7 1
1 8
8 9
9 1
1 10
10 11
11 1
"""
five_triangles_out = run(five_triangles)
validate(five_triangles, five_triangles_out, ["Yes"])

# Maximum-size connected graph, a star with 500000 vertices.
# The center has degree 499999, which is 1 modulo 3.
max_n = 500000
max_edges = "\n".join(f"1 {v}" for v in range(2, max_n + 1))
max_case = f"1\n{max_n} {max_n - 1}\n{max_edges}\n"

max_out = run(max_case)
validate(max_case, max_out, ["Yes"])

print("All tests passed.")
```最小サイズのテストでは、合法的な盗難が存在しない (n=1) 境界をチェックします。 2 頂点テストでは、厳密な要件 (1<c<n) がチェックされます。 スターは (0\bmod3) の構造をチェックします。 4 サイクルは、グラフ全体が (B) サイクルである場合をチェックします。 1 つの頂点を共有する 2 つの三角形は、最終的に不可能な構造の最小の例です。 1 つの (A) 頂点を共有する 5 つの三角形は 5 つの (B) コンポーネントを作成し、最終的な建設的なケースを実行します。 最大サイズのスターは、大きな入力境界と線形時間動作の両方をチェックします。 

| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | (n=1、m=0) |`No`| 最小サイズのグラフ |
 | (n=2、m=1) |`No`| 厳密な (1<c<n) 境界 |
 | 四頂点星 |`Yes`| 度 (0\bmod3) 建設 |
 | 4サイクル |`No`| グラフ全体は (B) サイクルです。 
| 1 つの頂点を共有する 2 つの三角形 |`No`| 最終不可能構造 |
 | 1 つの頂点を共有する 5 つの三角形 |`Yes`| 複数の (B) ツリー コンポーネント |
 | (500000)-頂点スター |`Yes`| 最大サイズの入力と線形複雑度 |

 ## 特殊なケース

 1頂点グラフの場合```
1
1 0
```唯一の頂点は次数 (0) を持っているため、クラス (Z) に属します。 最初の構築ではそれを保持しますが、盗まれた頂点は残りません。`make_answer`盗まれた頂点の数が (1) を超えておらず、他に使用するケースがないため、構築は拒否されます。 出力は`No`。 

2頂点グラフの場合```
1
2 1
1 2
```両方の頂点の次数は (1) なので、どちらもクラス (A) です。 BFS はすぐにパス (1-2) を見つけますが、このパスにはすべての頂点が含まれています。 その補数が空であるため、構築は拒否されます。 少なくとも 2 つの盗まれた頂点を含む適切なサブセットは他に存在せず、出力は次のようになります。`No`。 

4サイクルの場合```
1
4 4
1 2
2 3
3 4
4 1
```4 つの頂点はすべてクラス (B) です。 DFS は非ツリー エッジを見つけて、4 つの頂点すべてを含むサイクルを再構築します。 補体のサイズはゼロであるため、盗むことはできません。 サイクルの適切に保持されるサブセットはパスのコレクションであり、パスの終点には内部次数 (1) があり、これは剰余 (2) と一致しません。 その結果、アルゴリズムは次の結果を返します。`No`。 

四頂点星の場合```
1
4 3
1 2
1 3
1 4
```中心は次数 (3) を持っているため、クラス (Z) です。 中心のみを保持すると内部次数 (0) が得られますが、元の次数は (3) であるため、剰余は保存されます。 3 枚の葉が盗まれているため、(c=3) と有効な答えが得られます。 

最終的な不可能構造としては、```
1
5 6
1 2
2 3
3 1
1 4
4 5
5 1
```頂点 (1) は次数 (4) を持っているため、一意の (A) 頂点です。 (B) 頂点は 2 つのツリー コンポーネントを形成し、それぞれが 1 つのエッジであり、各コンポーネントには頂点 (1) に対して正確に 2 つのエッジがあります。 (A) 頂点に必要な内部次数剰余を与える唯一の方法は、両方の成分を使用することです。 各コンポーネントはすでに 2 つのアタッチメント間のパスになっているため、両方のパスを保持することは 5 つの頂点をすべて保持することを意味します。 適切な有効な保持セットがないため、答えは次のようになります。`No`。 

For a graph containing several such components, the situation changes. With five triangles sharing vertex (1), vertex (1) has degree (10), which is (1\bmod3). The five (B)-components are separate edges, each attached to (1) at both endpoints. Keeping the paths in any two components gives vertex (1) internal degree (4), while all selected (B)-vertices have internal degree (2). The other three components can be stolen, so the answer becomes`Yes`。
