---
title: "CF 105948E - テラフォーミングのためのコロニー形成評価"
description: "各頂点が惑星を表し、各頂点がその居住可能性と呼ばれる数値を持つグラフが与えられます。 グラフには方向性がなく、エッジは惑星間の双方向の移動ルートを表します。"
date: "2026-06-22T16:06:08+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 105948
codeforces_index: "E"
codeforces_contest_name: "CCF CAT NAEC 2025 (Provincial)"
rating: 0
weight: 105948
solve_time_s: 55
verified: true
draft: false
---

[CF 105948E - テラフォーミングの植民地化評価](https://codeforces.com/problemset/problem/105948/E)

 **評価:** -
 **タグ:** -
 **解決時間:** 55 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 各頂点が惑星を表し、各頂点がその居住可能性と呼ばれる数値を持つグラフが与えられます。 グラフには方向性がなく、エッジは惑星間の双方向の移動ルートを表します。 

各テスト ケースでは、選択されたすべての惑星が選択されたセット内に完全に留まることで他の選択されたすべての惑星に到達できるように、正確に k 個の惑星を選択する必要があります。 言い換えれば、選択されていない頂点を通過することはできないため、選択された惑星は接続された誘導サブグラフを形成する必要があります。 

このようなサイズ k の有効な接続された選択すべての中で、選択されたセット内の最小の居住可能性の値を調べます。 目標は、この最小値を最大化することです。 

これは、答えが、しきい値 x の選択と、重みが少なくとも x である少なくとも k 個の頂点を含む連結成分が存在するかどうかのチェックに依存することを直ちに示唆しています。 そのようなセットが存在する場合、その接続された領域内でそれらのうち k 個を選択できます。 

この制約は、n と m がテスト ケースごとに最大 100000 であり、テスト全体の合計が 200000 で制限されることを意味します。これにより、多くの候補しきい値について接続性を最初から再計算するアプローチが除外されます。 ソリューションは、すべてのテスト ケースにわたって効果的に線形またはほぼ線形である必要があります。 

少なくとも k 以上のサイズを持つ連結成分がないようにグラフが切り離されている場合、微妙なエッジ ケースが発生します。 たとえば、n = 5、k = 3 で、すべてのエッジが分離されている場合、有効な選択は存在せず、すべての重みが大きい場合でも答えは -1 でなければなりません。 

別の失敗例は、走査が除外されたノードを通過できないという制約を無視して、グラフ全体のコンポーネントを誤って考慮した場合に発生します。 これは、その内部のすべてのノードが選択したしきい値以下で有効でない限り、グローバルに接続されたコンポーネントを取得できないことを意味します。 

## アプローチ

 ブルートフォースのアイデアは、k 個のノードのすべての可能なサブセットを考慮し、それが接続された誘導サブグラフを形成しているかどうかを確認し、その中の最小重みを計算することです。 サブセットの数は n では指数関数的であり、サブセットごとの接続性チェックも n + m では線形であるため、これは組み合わせ的に不可能です。 これにより、およそ O(n selected k · (n + m)) の操作が必要になりますが、これは非常に小さい n の場合でも完全に実行不可能です。 

より構造化されたブルートフォースは、答え x を修正し、少なくとも x の重みを持つノードによって引き起こされる部分グラフに、少なくとも k サイズの連結成分が含まれているかどうかをチェックすることです。 これにより、問題は x ごとの実現可能性チェックに集約されます。 これを効率的にチェックできれば、x に対する二分探索が可能になります。 

重要な洞察は、接続性は「アクティブな」ノード間のエッジにのみ依存し、フィルタリングされたグラフ内の接続されたコンポーネントのサイズのみを考慮するということです。 重みを減らすことでノードを並べ替え、それらを段階的にアクティブ化すると、union-find 構造を使用して接続されたコンポーネントを維持できます。 コンポーネントがサイズ k に達するとすぐに、現在の重量しきい値が適用可能になります。 

これにより、問題はソートと DSU メンテナンスに変わります。これはテスト ケースごとに線形ですが、実際には制約が与えられると全体的に線形になります。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(2^n · (n + m)) | O(n + m) | 遅すぎる |
 | しきい値 + DSU | O(n log n + m α(n)) | O(n + m) | 承認済み |

 ## アルゴリズムのチュートリアル

 ノードがアクティブ化されたときに、その重みが現在の候補の最小値を表すように、居住可能性が低い順にノードを処理します。 

DSU はアクティブ ノード上でのみ維持されます。

1. すべてのノードを重みの降順に並べ替えます。 これにより、より大きな最小値を最初に試すことが保証され、これは最終的な答えの最大化に直接対応します。 
2. 最初は、DSU 内でアクティブなノードはありません。 
3. ソートされた順序でノードを繰り返し、一度に 1 つのノードをアクティブにします。 
4. ノードをアクティブ化するときは、そのノードをアクティブとしてマークし、すでにアクティブなすべての隣接ノードと結合します。 これにより、現在アクティブ化されているノードの誘導サブグラフの接続コンポーネントが段階的に構築されます。 
5. アクティブ化するたびに、そのノードを含む DSU コンポーネントのサイズを計算します。 k 以上になれば、即座に現在のノードの重みを答えとして返すことができます。 
6. サイズ k のコンポーネントが見つからずにすべてのノードの処理が終了した場合は、-1 を返します。 

ステップ 4 が正しい理由は、非アクティブなノードへのエッジが接続に寄与してはいけないためです。 アクティブな近傍を結合するだけで、DSU が誘導されたサブグラフ内の接続を正確に表現するようになります。 

### なぜ効果があるのか

 プロセスのどの時点においても、アクティブ セットは、少なくとも現在のしきい値以上の重みを持つノードに正確に対応します。 DSU コンポーネントは、これらのアクティブ ノードによって引き起こされるサブグラフ内の接続コンポーネントを表します。 コンポーネントが最初にサイズ k に達すると、すべての重みが現在のしきい値以上である k ノードの接続されたサブグラフが存在することを意味します。 重みを降順で処理するため、このしきい値は、サイズ k の有効な接続セットの最大可能最小値になります。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

class DSU:
    def __init__(self, n):
        self.parent = list(range(n))
        self.size = [1] * n

    def find(self, x):
        while self.parent[x] != x:
            self.parent[x] = self.parent[self.parent[x]]
            x = self.parent[x]
        return x

    def union(self, a, b):
        ra, rb = self.find(a), self.find(b)
        if ra == rb:
            return
        if self.size[ra] < self.size[rb]:
            ra, rb = rb, ra
        self.parent[rb] = ra
        self.size[ra] += self.size[rb]

    def comp_size(self, x):
        return self.size[self.find(x)]

t = int(input())
for _ in range(t):
    n, m, k = map(int, input().split())
    w = list(map(int, input().split()))

    edges = [[] for _ in range(n)]
    for _ in range(m):
        u, v = map(int, input().split())
        u -= 1
        v -= 1
        edges[u].append(v)
        edges[v].append(u)

    order = sorted(range(n), key=lambda i: -w[i])

    active = [False] * n
    dsu = DSU(n)

    answer = -1

    for node in order:
        active[node] = True
        for nei in edges[node]:
            if active[nei]:
                dsu.union(node, nei)

        if dsu.comp_size(node) >= k:
            answer = w[node]
            break

    print(answer)
```DSU は、それぞれのコンポーネント内の各ノードで初期化されますが、重要な点は、コンポーネントが意味を持つのはノードがアクティブ化されたときだけであるということです。 アクティブな配列により、非アクティブなノードを介してマージすることがなくなり、誘導されたサブグラフ制約が維持されます。 

ソートのステップは、初めてサイズ k のコンポーネントに到達したときに可能な限り高い最小重みを使用することが保証されるため、重要です。 

comp_size チェックでは、新しくアクティブ化されたノードの代表値が使用されます。そのノードに関係するすべてのマージはアクティブ化直後に行われるため、これで十分です。 

## 実用的な例

 5 つのノードと k = 3 を持つ小さなグラフを考えてみましょう。 

入力:```
n = 5, m = 4, k = 3
w = [5, 1, 4, 3, 2]
edges: (1-2), (2-3), (3-4), (4-5)
```重みでソートされたノード: 1(5)、3(4)、4(3)、5(2)、2(1)

 アクティベーションを追跡します:

 | ステップ | ノード | アクティブセット | DSU の合併 | コンポーネントのサイズ | 答え |
 | --- | --- | --- | --- | --- | --- |
 | 1 | 1 | {1} | なし | 1 | -1 |
 | 2 | 3 | {1,3} | なし | 1,1 | -1 |
 | 3 | 4 | {1,3,4} | 3 は 4 に接続します | 2 | -1 |
 | 4 | 5 | {1,3,4,5} | 4-5 | 3 | 3 |

 ノード 5 (重み 2) が追加されると、チェーン 3-4-5 がアクティブになり、サイズ 3 の接続コンポーネントを形成します。ただし、ここでは最小重みが 2 ではなく 3 であることを確認する必要があります。表には微妙な問題が示されています。実際には、接続されたアクティブ コンポーネント {3,4,5} は 5 が含まれるまで完全には接続されていないため、ノード 4 が追加されるときに答えがトリガーされますが、そのセットの最小重みは 2 なので、最初の有効な k コンポーネントが実際にはセットになります。 {3,4,5} は最小値が 2 です。サイズ 3 のより重みの高いコンポーネントが以前に存在していた場合、順序付けによりそれが最初に選択されます。 

これは、正確さがアクティベーションの降順に依存することを示しており、常に可能な限り最良のしきい値を最初に考慮することが保証されます。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | テストあたり O((n + m) α(n)) | 各ノードは 1 回アクティブ化され、各エッジは DSU ユニオンを通じて最大 2 回処理されます。 
| スペース | O(n + m) | 隣接リストと DSU アレイ |

 すべてのテスト ケースにわたって、合計 n と m は 2 × 10^5 によって制限されるため、ソリューションは制限内で快適に実行されます。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    import sys
    input = sys.stdin.readline

    class DSU:
        def __init__(self, n):
            self.parent = list(range(n))
            self.size = [1] * n

        def find(self, x):
            while self.parent[x] != x:
                self.parent[x] = self.parent[self.parent[x]]
                x = self.parent[x]
            return x

        def union(self, a, b):
            ra, rb = self.find(a), self.find(b)
            if ra == rb:
                return
            if self.size[ra] < self.size[rb]:
                ra, rb = rb, ra
            self.parent[rb] = ra
            self.size[ra] += self.size[rb]

        def comp_size(self, x):
            return self.size[self.find(x)]

    t = int(input())
    out = []

    for _ in range(t):
        n, m, k = map(int, input().split())
        w = list(map(int, input().split()))

        edges = [[] for _ in range(n)]
        for _ in range(m):
            u, v = map(int, input().split())
            u -= 1
            v -= 1
            edges[u].append(v)
            edges[v].append(u)

        order = sorted(range(n), key=lambda i: -w[i])

        active = [False] * n
        dsu = DSU(n)

        ans = -1
        for node in order:
            active[node] = True
            for nei in edges[node]:
                if active[nei]:
                    dsu.union(node, nei)
            if dsu.comp_size(node) >= k:
                ans = w[node]
                break

        out.append(str(ans))

    return "\n".join(out)

# simple constructed tests
assert run("""1
1 0 1
5
""") == "5"

assert run("""1
3 0 2
5 4 3
""") == "-1"

assert run("""1
3 2 2
1 2 3
1 2
2 3
""") == "2"

assert run("""1
5 4 3
5 1 4 3 2
1 2
2 3
3 4
4 5
""") == "2"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 単一ノード | 5 | 最小限のケース |
 | 切断されたグラフ | -1 | 不可能 |
 | 小さなチェーン k=2 | 2 | しきい値の正確性 |
 | フルチェーン k=3 | 2 | 接続の蓄積 |

 ## 特殊なケース

 重要なエッジ ケースは、グラフに十分なノードがあるにもかかわらず、重みを考慮する前であってもサイズ k に達する連結成分がない場合です。 その状況では、アルゴリズムはすべてのノードをアクティブにし、サイズ条件をトリガーしないため、正しく -1 が出力されます。 

もう 1 つのエッジ ケースは、決して接続されない高重みの孤立したノードから応答が得られる場合です。 Since connectivity is required, a single node cannot satisfy k > 1, and DSU sizes prevent false positives because unions never occur without edges.

 3 番目のケースでは、複数のコンポーネントが並行して成長します。 The algorithm still works because each activation only merges local neighborhoods, and the first time any component reaches size k, it corresponds to the best possible threshold due to descending weight processing.
