---
title: "CF 102191I - プロジェクトのプレゼンテーション"
description: "会社の階層は根を張ったツリーです。 従業員 u は p[u] に直接報告し、親の指示に従って繰り返した結果、最終的に CEO に到達します。 すべての従業員は、厳密に 1 つのプロジェクトに属します。"
date: "2026-08-24T10:57:07+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102191
codeforces_index: "I"
codeforces_contest_name: "PSUT Coding Marathon 2019"
rating: 0
weight: 102191
solve_time_s: 2145
verified: true
draft: false
---

[CF 102191I - プロジェクト プレゼンテーション](https://codeforces.com/problemset/problem/102191/I)

 **評価:** -
 **タグ:** -
 **解決時間:** 35 分 45 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 会社の階層は根を張ったツリーです。 従業員`u`～に直接報告する`p[u]`、親ポインタを繰り返したどることで、最終的に CEO に到達します。 すべての従業員は、厳密に 1 つのプロジェクトに属します。 

特定のプロジェクトのプレゼンテーションには、そのプロジェクトに割り当てられたすべての従業員と、そのような各従業員から CEO に至るまでのすべてのマネージャーが出席します。 2 人のプロジェクト メンバーに共通のマネージャーがいる場合、そのマネージャーは 1 回だけカウントされる必要があります。 したがって、プロジェクトに必要な答えは、ルートからプロジェクト メンバーへのすべてのパスの和集合に含まれる個別の頂点の数です。 

入力には最大 (10^6) 人の従業員が含まれるため、プロジェクト メンバーごとに個別に CEO のもとへ向かうアルゴリズムは、(O(n^2)) 個の作業を実行できます。 頂点が 100 万個ある場合、直線的なパスでも 3 秒以内の制限内ではすでに相当な量になるため、ターゲットは直線に近いものでなければなりません。 (10^6 \log n) の整数がメモリ バジェットを超えるため、頂点ごとに (O(\log n)) の祖先テーブルを保存することも Python では望ましくありません。 このソリューションでは、線形サイズの配列を使用し、再帰的 DFS を回避する必要があります。 

いくつかのケースは誤って処理されやすいものです。 同じプロジェクトの 2 人の従業員が同じルート パス上にある場合、それらの共通の祖先は 2 回カウントされてはなりません。 例えば、```
3 1
1 1 1
0 1 2
```3 人の従業員全員がプロジェクト 1 に属しており、答えは次のとおりです。`3`。 3 つのルートパスの長さを個別に追加するソリューションでは、従業員 1 と 2 が複数回カウントされます。 

従業員自体が、同じプロジェクトに属する別の従業員の祖先になる可能性があります。 例えば、```
4 2
1 2 1 2
0 1 2 3
```プロジェクト 1 には従業員 1 と 3 が含まれています。彼らのパスは次のとおりです。`1`そして`1 -> 2 -> 3`したがって、プロジェクト 1 の答えは次のようになります。`3`、 ない`4`。 出力は`3 4`。 

CEO がプロジェクトの唯一のメンバーになることもできます。 のために```
1 1
1
0
```答えは`1`。 エッジ数から始まりルート自体を忘れた式はゼロを生成します。 

最後に、プロジェクトにはまったく異なるブランチにメンバーが所属する場合があります。 で```
5 2
1 1 2 1 2
0 1 1 2 2
```プロジェクト 1 には従業員 1、2、および 4 が含まれています。その参加者は次のとおりです。`{1, 2, 4}`、したがって、答えは次のとおりです`3`。 プロジェクト 2 には従業員 3 と 5 が含まれており、そのパスは次のとおりです。`{1, 2, 3, 5}`、したがって、その答えは次のとおりです`4`。 正しい出力は次のとおりです`3 4`。 

## アプローチ

 直接的なソリューションでは、その従業員からすべてのマネージャーまでのすべてのプロジェクト メンバーを追跡し、訪問した各従業員をプロジェクトのセットに挿入します。 これは正しいです。なぜなら、まさにそれらの先祖が出席することになっているからです。 問題は繰り返し歩く量です。 すべての従業員が同じプロジェクトに属している (n 人) 人の従業員のチェーンを考えてみましょう。 最初の従業員には 1 つの親ステップ、次の 2 つのステップなどが必要になる場合があります。 

[
 1 + 2 + \cdots + (n-1) = \frac{n(n-1)}2 = O(n^2)
 】

 親のトラバース。 (n=10^6) の場合、これは約 (5\cdot10^{11}) 回の演算であり、制限をはるかに超えています。 

有益な観察は、1 つのプロジェクトではパスの結合全体を明示的に構築する必要がないということです。 DFS 事前注文でプロジェクトの従業員を並べ替えます。 それらの順序が (v_1,v_2,\ldots,v_k) であると仮定します。 最初の従業員は、ルートからのパス全体を提供します。`depth[v1] + 1`頂点。 その後、(v_i) を追加すると、前のプロジェクト メンバーによって既にカバーされていたルート パスの部分は次で終了します。 

[
 LCA(v_{i-1},v_i)。 
】

 したがって、(v_i) によって提供される新しい頂点の数は次のようになります。 

[
 深さ[v_i]-深さ[LCA(v_{i-1},v_i)]。 
】

 したがって、答えは次のとおりです

 [
 1+深さ[v_1]
 +\sum_{i=2}^{k}
 \left(深さ[v_i]-深さ[LCA(v_{i-1},v_i)]\right)。 
】

 連続した事前順序の発生で十分な理由は、サブツリーが事前順序で連続した間隔を形成するためです。 マークされた 2 つのノードが事前順序で分離されている場合、それらの間のすべての分岐は連続する遷移の 1 つによって表されます。 それらの LCA は、重複するルート パスの部分を正確に考慮します。 

したがって、各プロジェクトが連続して発生する間に必要な LCA クエリは 1 つだけです。 このようなクエリは合計で最大 (n-1) 個あります。 

従来のバイナリリフティング LCA は、これらのクエリにそれぞれ (O(\log n)) 回で応答し、(O(n\log n)) 個のメモリを必要とします。 (10^6) 頂点の場合、そのメモリ使用量は Python では特に魅力的ではありません。 すべての LCA クエリは事前注文トラバーサル後に既知であるため、代わりに Tarjan のオフライン LCA アルゴリズムを使用できます。 ほぼ線形な時間と線形メモリの素セット構造を使用して、これらすべてのクエリにまとめて答えます。 

したがって、完全な戦略は、事前順序で階層を 1 回横断し、同じプロジェクトを持つ連続する従業員のペアごとに 1 つの LCA クエリを生成し、Tarjan のオフライン LCA アルゴリズムの反復バージョンを使用してすべてのクエリを処理することです。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | (O(n^2)) | (O(n)) | 遅すぎる |
 | 最適 | (O(n\alpha(n))) | (O(n)) | 承認済み |

 ここで (\alpha(n)) は逆アッカーマン関数であり、非常にゆっくりと増加するため、この制約範囲では実質的に一定になります。 

## アルゴリズムのチュートリアル

 1. 親配列をルート化された子表現に変換します。 CEO を除く各従業員には 1 人のマネージャーがいるだけなので、ルート以外の各従業員をそのマネージャーの子のリンク リストに挿入できます。 100 万個のネストされた Python オブジェクトはメモリを大量に消費するため、リストの Python リストではなく配列を使用します。 
2. CEO から反復的な DFS を実行します。 従業員が初めて入社したときに、その深さを記録し、そのプロジェクトを検査します。 プロジェクトごとに、`prev[project]`、予約注文でそのプロジェクトに最近遭遇した従業員。 
3. 現在の従業員がそのプロジェクトの最初の出現者である場合、そのプロジェクトの回答を次のように初期化します。`depth[current] + 1`。 これは、CEO から最初のプロジェクト メンバーまでの完全なパスをカウントします。 
4. プロジェクトが以前に登場したことがある場合は、次の間の LCA クエリを作成します。`prev[project]`そして現社員。 現在の従業員は、事前注文の後のエンドポイントです。 クエリを両方のエンドポイントに保存すると、いずれかのエンドポイントが終了したときに Tarjan のアルゴリズムがクエリを処理できるようになります。 それから交換してください`prev[project]`今の社員と一緒に。 
5. DFS はポストオーダー シーケンスも記録します。 Tarjan のオフライン LCA アルゴリズムは、その子孫がすべて処理された後でのみ頂点を処理するため、この 2 番目の順序付けが必要になります。 
6. 従業員ごとに 1 つのセットを含む素セット構造を初期化します。 すべてのセットについて、その現在のツリーの祖先も維持します。 Tarjan のアルゴリズムとまったく同様に、子が終了した後に結合操作が実行されます。 ランクによるユニオンは DSU を浅く保ちますが、パス圧縮により繰り返しが行われます。`find`運用時間はほぼ一定です。 
7. 従業員を事後処理します。 現在の従業員を処理済みとしてマークします。 関連付けられている各 LCA クエリについて、他のエンドポイントがすでに処理されている場合、LCA は`ancestor[find(other)]`。 現時点では、もう一方のエンドポイントを含む DSU コンポーネントは、LCA までのツリーのすでに完了した部分を正確に表しています。 
8. 後の事前注文エンドポイントが次のクエリの場合`v`、追加

 [
 深さ[v]-深さ[LCA]
 】

 そのプロジェクトの答えに。 これはまさに、へのパスの部分です。`v`これは、前回のプロジェクトではまだ含まれていなかったものです。 

1. 従業員の問い合わせを処理した後`u`、その DSU コンポーネントをその親とマージします。 もし`u`が CEO の場合、親は存在しないため、処理は終了します。 それ以外の場合は、マージ後に新しい DSU 代表の祖先を次のように設定します。`parent[u]`。 

### なぜ効果があるのか

 固定プロジェクトの場合、事前注文の従業員を (v_1,\ldots,v_k) とします。 (v_1) へのルート パスが正確に寄与します`depth[v1] + 1`頂点。 後で検討してください (v_i)。 (v_{i-1}) は事前注文の直前のプロジェクト メンバーであるため、(v_i) へのルート パスの既にカバーされている部分は (LCA(v_{i-1},v_i)) で終了します。 (v_i) へのパス上の LCA より下にあるものはすべて新しく、正確に寄与します。`depth[v_i] - depth[LCA]`。 これらのばらばらの新しい部分を合計すると、すべての出席従業員が 1 回だけカウントされます。 

Tarjan のオフライン LCA 不変式は、必要な LCA 値を提供します。 頂点が処理されると、完成した子サブツリーはすべてその DSU コンポーネントにすでにマージされていますが、コンポーネントはまだこの頂点を介して親にマージされていません。 したがって、他のエンドポイントがすでに処理されているクエリの場合、そのエンドポイントの DSU 代表に格納されている祖先は、まさに 2 つのクエリ エンドポイントの最も低い共通の祖先になります。 

## Python ソリューション```python
import sys
from array import array

input = sys.stdin.readline

def solve():
    n, m = map(int, input().split())

    color = array('i', map(int, input().split()))

    # parent[v] is zero-based, -1 for the CEO.
    parent = array('i', (x - 1 for x in map(int, input().split())))

    # First-child linked lists.
    head = array('i', [-1]) * n
    nxt = array('i', [-1]) * n

    root = 0
    for v in range(n):
        p = parent[v]
        if p == -1:
            root = v
        else:
            nxt[v] = head[p]
            head[p] = v

    depth = array('i', [0]) * n

    # Last occurrence of every project in preorder.
    prev = array('i', [-1]) * (m + 1)

    # At most n-1 consecutive-occurrence queries exist.
    q_u = array('i', [0]) * n
    q_v = array('i', [0]) * n
    q1 = array('i', [-1]) * n
    q2 = array('i', [-1]) * n
    q_count = 0

    answer = array('i', [0]) * (m + 1)

    # Iterative DFS. head[u] is consumed as the current child iterator.
    stack = array('i', [root])
    postorder = array('i')

    # Enter the root.
    c = color[root]
    prev[c] = root
    answer[c] = 1

    while stack:
        u = stack[-1]
        e = head[u]

        if e == -1:
            stack.pop()
            postorder.append(u)
            continue

        # Consume this child edge.
        head[u] = nxt[e]
        v = e
        depth[v] = depth[u] + 1

        c = color[v]
        old = prev[c]

        if old == -1:
            answer[c] = depth[v] + 1
        else:
            qid = q_count
            q_count += 1

            q_u[qid] = old
            q_v[qid] = v

            if q1[old] == -1:
                q1[old] = qid
            else:
                q2[old] = qid

            if q1[v] == -1:
                q1[v] = qid
            else:
                q2[v] = qid

        prev[c] = v
        stack.append(v)

    # Tarjan offline LCA.
    dsu = array('i', range(n))
    ancestor = array('i', range(n))
    rank = bytearray(n)
    processed = bytearray(n)

    def find(x):
        r = x
        while dsu[r] != r:
            r = dsu[r]

        while dsu[x] != x:
            y = dsu[x]
            dsu[x] = r
            x = y

        return r

    for u in postorder:
        processed[u] = 1

        qid = q1[u]
        if qid != -1:
            v = q_v[qid] if q_u[qid] == u else q_u[qid]
            if processed[v]:
                r = find(v)
                lca = ancestor[r]
                cur = q_v[qid]
                answer[color[cur]] += depth[cur] - depth[lca]

        qid = q2[u]
        if qid != -1:
            v = q_v[qid] if q_u[qid] == u else q_u[qid]
            if processed[v]:
                r = find(v)
                lca = ancestor[r]
                cur = q_v[qid]
                answer[color[cur]] += depth[cur] - depth[lca]

        p = parent[u]
        if p != -1:
            ru = find(u)
            rp = find(p)

            if ru != rp:
                if rank[ru] < rank[rp]:
                    dsu[ru] = rp
                    new_root = rp
                elif rank[ru] > rank[rp]:
                    dsu[rp] = ru
                    new_root = ru
                else:
                    dsu[rp] = ru
                    rank[ru] += 1
                    new_root = ru

                ancestor[new_root] = p

    sys.stdout.write(' '.join(map(str, answer[1:])))

if __name__ == "__main__":
    solve()
```最初の入力行は通常どおり読み取られますが、2 つの大きな配列は反復子から直接構築されます。 使用する`array('i')`はるかに大きな Python 整数表現の代わりに、各整数を 4 バイトに保ちます。 この違いは、複数の配列にそれぞれ 100 万個の要素が含まれる場合に重要になります。 

親配列はすぐに 0 から始まるインデックスに変換されます。 CEOは、`-1`これは、別の DSU コンポーネントにマージすべきではない唯一の頂点に便利なセンチネルを提供します。 

子ツリーは次のように表されます。`head`そして`nxt`。 従業員一人ひとりにとって`v`、`nxt[v]`の別の子を指します`parent[v]`。 DFS は消費します`head[u]`現在の子ポインターとして使用されるため、別の反復子配列は必要ありません。 

事前注文 DFS は、一度に 2 つのジョブを実行します。 深さを計算し、連続するプロジェクトの発生を検出し、同時に同じ反復トラバーサルで後で必要となる事後順序を記録します。 スタックには頂点インデックスのみが含まれており、Python の再帰と 100 万人の従業員のチェーンでの失敗を回避します。 

各プロジェクトの最初の出現ではクエリが作成されないため、LCA クエリは最大で (n-1) 個存在します。 各頂点は、最大 2 つのそのようなクエリのエンドポイントになることができ、1 つは前の出現に接続し、もう 1 つは次の出現に接続します。 これにより、`q1`そして`q2`頂点ごとに Python オブジェクトの大規模なリストを作成せずにクエリ ID を保存します。 

答えは次のように初期化されます`depth[v] + 1`各プロジェクトの最初の発生時。 の`+1`CEO を深さ 0 として深さを測定すると、従業員自体が考慮されます。 

第 2 フェーズでは、Tarjan のオフライン LCA アルゴリズムを繰り返し実装します。`processed[v]`タージャンの黒い色の役割を果たします。 クエリは、他のエンドポイントがすでに処理されている場合にのみ応答されます。 その時点で、その DSU コンポーネントは、そのエンドポイントを含む完成したブランチを表し、`ancestor[find(v)]`正しい LCA が得られます。 

DSU がツリー トラバーサルを表す場合でも、ランクによる結合が使用されます。 セットの代表者は実際のツリーの祖先である必要はありません。`ancestor`どのツリー頂点がそのコンポーネントの最も関連性の高い祖先を表すかを個別に記録します。 この違いにより、標準のほぼ一定時間の DSU 保証が可能になります。 

すべてのプロジェクトの答えは最大でも (n\le10^6) であるため、すべての算術演算は符号付き 32 ビット整数に快適に収まります。 Python の整数でも値は安全に処理されますが、コンパクトな整数配列はメモリ使用量を考慮すると便利です。 

## 実用的な例

 ### サンプル 1

 階層は```
1
├── 3
│   ├── 4
│   └── 5
└── 2
    └── 6
```子広告掲載オーダーにより、実際の DFS プレオーダーが作成されます。`1, 3, 5, 4, 2, 6`。 プロジェクトのオカレンスとその結果の LCA クエリを以下に示します。 

| 予約注文の位置 | 従業員 | プロジェクト | 以前の同じプロジェクト | 新しい初期貢献 |
 | --- | --- | --- | --- | --- |
 | 1 | 1 | 1 | なし | 1 |
 | 2 | 3 | 4 | なし | 2 |
 | 3 | 5 | 2 | なし | 3 |
 | 4 | 4 | 3 | なし | 3 |
 | 5 | 2 | 2 | 5 | 0 |
 | 6 | 6 | 4 | 3 | 0 |

 プロジェクト 2 のクエリは次のとおりです。`(5, 2)`。 彼らの LCA は従業員 1 です。従業員 5 は最初に 3 つの頂点を提供しました。`{1,3,5}`。 従業員 2 を追加すると貢献します`depth[2] - depth[1] = 1`、出席者は4人になります。 

プロジェクト 4 のクエリは次のとおりです。`(3, 6)`。 彼らの LCA は従業員 1 です。最初の出現は 2 つの頂点に寄与します。`{1,3}`、従業員 6 は従業員 1 から 2 つの追加レベルを提供し、4 人の出席者を生成します。 

最終的な答えは、`1 4 3 4`。 

### カスタム例

 検討してください```
5 2
1 1 2 1 2
0 1 1 2 2
```木は```
1
├── 2
│   ├── 4
│   └── 5
└── 3
```DFS の予約注文の可能性は次のとおりです。`1, 3, 2, 5, 4`。 関連する状態は次のとおりです。 

| 従業員 | プロジェクト | 以前の同じプロジェクト | LCA クエリ | 貢献 |
 | --- | --- | --- | --- | --- |
 | 1 | 1 | なし | なし | 1 |
 | 3 | 2 | なし | なし | 2 |
 | 2 | 1 | 1 |`(1,2)`| 1 |
 | 5 | 2 | 3 |`(3,5)`| 2 |
 | 4 | 1 | 2 |`(2,4)`| 1 |

 プロジェクト 1 の場合、最初の従業員が CEO であるため、最初の貢献は 1 です。 クエリ`(1,2)`LCA 1 があり、頂点が 1 つ増えています。 クエリ`(2,4)`には LCA 2 があり、頂点が 1 つ増えています。 結果は`3`。 

プロジェクト 2 では、従業員 3 が貢献します`{1,3}`、従業員 5 は LCA 1 の下のパスに貢献します。`{2,5}`。 結果は`4`。 

出力は`3 4`。 この例は、祖先と子孫のペアによってパス全体が再度カウントされてはならない理由を示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O(n\alpha(n))) | DFS、クエリ構築、ポストオーダー トラバーサルは線形ですが、Tarjan の DSU 操作は償却されます (O(\alpha(n)))。 |
 | スペース | (O(n)) | すべてのツリー、クエリ、トラバーサル、および DSU 構造は、線形サイズのコンパクトな配列を使用します。 |

 制約 (n\le10^6) により、線形メモリ設計が特に重要になります。 実装では整数データをコンパクトに格納します`array`オブジェクトを取得し、反復トラバーサルを使用するため、Python の再帰呼び出しスタックと、入れ子になったリストによる大きなオブジェクトのオーバーヘッドの両方が回避されます。 このアルゴリズムは、ツリー上で一定数のパスと、ほぼ一定に償却される DSU 操作のみを実行します。 

## テストケース```python
import sys
import io
from array import array

def solve():
    n, m = map(int, input().split())
    color = array('i', map(int, input().split()))
    parent = array('i', (x - 1 for x in map(int, input().split())))

    head = array('i', [-1]) * n
    nxt = array('i', [-1]) * n

    root = 0
    for v in range(n):
        p = parent[v]
        if p == -1:
            root = v
        else:
            nxt[v] = head[p]
            head[p] = v

    depth = array('i', [0]) * n
    prev = array('i', [-1]) * (m + 1)
    answer = array('i', [0]) * (m + 1)

    q_u = array('i', [0]) * n
    q_v = array('i', [0]) * n
    q1 = array('i', [-1]) * n
    q2 = array('i', [-1]) * n
    q_count = 0

    stack = array('i', [root])
    postorder = array('i')

    c = color[root]
    prev[c] = root
    answer[c] = 1

    while stack:
        u = stack[-1]
        e = head[u]

        if e == -1:
            stack.pop()
            postorder.append(u)
            continue

        head[u] = nxt[e]
        v = e
        depth[v] = depth[u] + 1

        c = color[v]
        old = prev[c]

        if old == -1:
            answer[c] = depth[v] + 1
        else:
            qid = q_count
            q_count += 1

            q_u[qid] = old
            q_v[qid] = v

            if q1[old] == -1:
                q1[old] = qid
            else:
                q2[old] = qid

            if q1[v] == -1:
                q1[v] = qid
            else:
                q2[v] = qid

        prev[c] = v
        stack.append(v)

    dsu = array('i', range(n))
    ancestor = array('i', range(n))
    rank = bytearray(n)
    processed = bytearray(n)

    def find(x):
        r = x
        while dsu[r] != r:
            r = dsu[r]

        while dsu[x] != x:
            y = dsu[x]
            dsu[x] = r
            x = y

        return r

    for u in postorder:
        processed[u] = 1

        for qid in (q1[u], q2[u]):
            if qid == -1:
                continue

            v = q_v[qid] if q_u[qid] == u else q_u[qid]

            if processed[v]:
                lca = ancestor[find(v)]
                cur = q_v[qid]
                answer[color[cur]] += depth[cur] - depth[lca]

        p = parent[u]
        if p != -1:
            ru = find(u)
            rp = find(p)

            if ru != rp:
                if rank[ru] < rank[rp]:
                    dsu[ru] = rp
                    new_root = rp
                elif rank[ru] > rank[rp]:
                    dsu[rp] = ru
                    new_root = ru
                else:
                    dsu[rp] = ru
                    rank[ru] += 1
                    new_root = ru

                ancestor[new_root] = p

    sys.stdout.write(' '.join(map(str, answer[1:])))

def run(inp: str) -> str:
    global input
    old_stdin = sys.stdin
    old_input = input

    sys.stdin = io.StringIO(inp)
    input = sys.stdin.readline

    try:
        solve()
        return ""
    finally:
        sys.stdin = old_stdin
        input = old_input

# Provided sample.
sample1 = """\
6 4
1 2 4 3 2 4
0 1 1 3 3 2
"""

# The helper above writes directly to stdout in solve(), so capture it.
def run(inp: str) -> str:
    global input
    old_stdin = sys.stdin
    old_input = input
    old_stdout = sys.stdout

    sys.stdin = io.StringIO(inp)
    sys.stdout = io.StringIO()
    input = sys.stdin.readline

    try:
        solve()
        return sys.stdout.getvalue()
    finally:
        sys.stdin = old_stdin
        sys.stdout = old_stdout
        input = old_input

assert run(sample1) == "1 4 3 4", "sample 1"

assert run("""\
1 1
1
0
""") == "1", "single employee"

assert run("""\
4 1
1 1 1 1
0 1 2 3
""") == "4", "all employees same project"

assert run("""\
4 2
1 2 1 2
0 1 2 3
""") == "3 4", "ancestor-descendant overlap"

assert run("""\
5 2
1 1 2 1 2
0 1 1 2 2
""") == "3 4", "different branches"

# Maximum-size shape, one project, one million employees in a chain.
n = 1_000_000
colors = "1 " * (n - 1) + "1"
parents = "0 " + " ".join(map(str, range(1, n)))
max_case = f"{n} 1\n{colors}\n{parents}\n"

assert run(max_case) == "1000000", "maximum-size chain"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`1 1 / 1 / 0`|`1`| 最小サイズのツリーと CEO 専用プロジェクト |
 |`4 1 / 1 1 1 1 / 0 1 2 3`|`4`| 全従業員が 1 つのプロジェクトを共有し、すべてのパスが重複します。 
|`4 2 / 1 2 1 2 / 0 1 2 3`|`3 4`| 祖先と子孫の重なりと境界の深さ |
 |`5 2 / 1 1 2 1 2 / 0 1 1 2 2`|`3 4`| さまざまなブランチのプロジェクト メンバー |
 | 1 つのプロジェクトで 100 万人の従業員がチェーンを形成 |`1000000`| 最大`n`、長い深さ、反復 DFS、および線形メモリ |

 ## 特殊なケース

 従業員が一人の場合```
1 1
1
0
```プロジェクトメンバーもCEOのみです。 事前注文トラバーサル中、プロジェクト 1 には以前の出現がないため、その応答は次のように初期化されます。`depth[1] + 1 = 1`。 LCA クエリはなく、CEO には結合する親がありません。 結果は`1`。 

全てが等しいチェーンのために```
4 1
1 1 1 1
0 1 2 3
```予約注文は`1,2,3,4`。 最初に出現したものは 1 つの頂点に寄与します。 連続したクエリは、`(1,2)`、`(2,3)`、 そして`(3,4)`。 それぞれの LCA は次のとおりです。`1`、`2`、 そして`3`したがって、すべてのクエリは 1 つの新しい頂点を提供します。 答えは次のようになります`1+1+1+1=4`。 すべてのプロジェクト メンバーが同じルート パス上に存在する場合でも、従業員は 2 回カウントされません。 

祖先と子孫の重複については、```
4 2
1 2 1 2
0 1 2 3
```プロジェクト 1 は従業員 1 と 3 で発生します。最初の発生は従業員 1 に寄与します。従業員 1 と 3 の LCA は従業員 1 であるため、2 番目の発生は従業員 1 に寄与します。`depth[3] - depth[1] = 2`。 答えは`3`、従業員に相当`{1,2,3}`。 プロジェクト 2 も同様に 4 人の従業員全員を対象にしており、`4`。 

異なる支部のメンバーにとっては、```
5 2
1 1 2 1 2
0 1 1 2 2
```プロジェクト 2 には従業員 3 と 5 がいます。彼らの LCA は従業員 1 です。最初のメンバーは 2 つの頂点を提供します。`{1,3}`、2番目は貢献します`depth[5] - depth[1] = 2`、追加`{2,5}`。 結果は`4`。 共有された CEO は、両方のプロジェクト メンバーがその CEO を必要とする場合でも、1 回としてカウントされます。 

最大深さの場合、100 万人の従業員のチェーンでは、トラバースの深さとメモリの両方が重視されます。 実装では DFS を再帰的に呼び出すことはないため、Python の再帰制限は関係ありません。 各従業員は一定数のコンパクト配列のみに貢献し、Tarjan の DSU は (O(n\log n)) 個の祖先テーブルを構築せずに連続するプロジェクト クエリを処理します。 すべての従業員を含む単一プロジェクトの場合、ルート パスの結合がチェーン全体となるため、答えは正確に次のようになります。`1000000`。
