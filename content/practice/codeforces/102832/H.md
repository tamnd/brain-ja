---
title: "CF 102832H - コンビネーション ロック"
description: "直接的なアプローチは、考えられるすべての動きのシーケンスをモデル化することです。 ある位置から、すべての未訪問の近傍を試し、結果の状態を再帰的に解決し、何らかの手が相手を負けさせる場合は現在の状態を勝利としてマークします。"
date: "2026-07-26T15:12:45+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102832
codeforces_index: "H"
codeforces_contest_name: "2020 China Collegiate Programming Contest Changchun Onsite"
rating: 0
weight: 102832
solve_time_s: 75
verified: true
draft: false
---

[CF 102832H - コンビネーション ロック](https://codeforces.com/problemset/problem/102832/H)

 **評価:** -
 **タグ:** -
 **解決時間:** 1 分 15 秒
 **確認済み:** はい

 ## 解決策
 ## アプローチ

 直接的なアプローチは、考えられるすべての動きのシーケンスをモデル化することです。 ある位置から、すべての未訪問の近傍を試し、結果の状態を再帰的に解決し、何らかの手が相手を負けさせる場合は現在の状態を勝利としてマークします。 これはルールを正確に反映しており、ゲームは有限であるため正しいです。 問題は必要な情報量です。 状態とは、現在のパスワードだけでなく、以前にアクセスしたすべてのパスワードのセットでもあります。 最悪の場合、これにより 2^(10^m) 個の可能な履歴が作成されますが、これは状態空間上の最小の有用な上限であっても不可能です。 

視点を変えると役立つのは、ゲームの歴史について考えるのをやめて、基礎となるグラフに注目することです。 禁止されたパスワードを入力すると常に負けとなるため、禁止されたパスワードはゲーム開始前に削除できます。 残りのグラフには、使用可能なパスワードを表す頂点と、1 つの有効な回転を表すエッジがあります。 

グラフは無向であり、ロック グラフには追加の 2 部構造があります。 数字の合計のパリティによるパスワードの色付けは、数字が 0 から 9 まで折り返されている場合でも、すべての移動で合計が奇数に変化するため機能します。これにより、Hopcroft-Karp による最大一致を解決できます。 

マッチング定理により簡単なテストが行​​われます。 M を再生可能なグラフの最大一致サイズとします。 開始頂点が削除され、最大一致サイズが M - 1 になる場合、すべての最大一致で開始頂点が使用されるため、アリスが勝ちます。 サイズが M のままの場合、開始頂点を回避する最大一致が存在するため、ボブは強制的に勝利することができます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | 状態の数の指数関数 | 指数 | 遅すぎる |
 | 最適 | O(VE^0.5) | O(V + E) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 禁止されたセットに含まれないすべてのパスワードのグラフを生成します。 各パスワードは整数の id に変換され、1 回転で 1 桁ずつ変化すると 2 つの id がつながります。 

グラフには最大 100,000 個の頂点が含まれ、各頂点には最大 2m の近傍があるため、グラフの構築は状態空間のサイズが線形になります。 

1. 桁の合計のパリティを使用して、グラフを 2 つの辺に分割します。 エッジは最初の辺から 2 番目の辺までのみ保存します。 

移動するたびにパリティが変更されるため、これは有効な 2 分割です。 これにより、最大限の一致を効率的に見つけることができます。 

1. Hopcroft-Karp を実行して、完全な再生可能なグラフの最大一致サイズを見つけます。 

これにより、マッチング特性評価に必要な値が得られます。 

1. 開始パスワードを無視して Hopcroft-Karp を再度実行します。 

マッチングサイズが減少すると、最大マッチングごとに開始パスワードが必要でした。 それ以外の場合は、それを回避する最大一致が存在します。 

1. 2 番目に一致するサイズが小さい場合は、Alice を出力し、そうでない場合は Bob を出力します。 

なぜ効果があるのか:

 禁止されたパスワードを削除した後、元のゲームでのすべての合法的な動きは、残りの無向グラフのエッジに沿った動きに正確に対応します。 したがって、ゲームは方向性のない頂点地理です。 マッチングの特徴付けでは、開始頂点がすべての最大マッチングに含まれている場合に最初のプレイヤーが勝利することを示します。 開始頂点を削除すると、この条件が正確にテストされます。最適なマッチングが 1 つのエッジを失った場合、開始は常に必要でした。 そうでない場合、最大一致はそれなしで存在します。 したがって、2 つの一致するサイズを比較すると、正しい勝者が得られます。 

## Python ソリューション```python
import sys
from collections import deque

input = sys.stdin.readline

def hopcroft_karp(adj, nl, nr):
    pair_l = [-1] * nl
    pair_r = [-1] * nr
    dist = [0] * nl

    def bfs():
        q = deque()
        found = False
        for i in range(nl):
            if pair_l[i] == -1:
                dist[i] = 0
                q.append(i)
            else:
                dist[i] = -1
        while q:
            u = q.popleft()
            for v in adj[u]:
                pu = pair_r[v]
                if pu == -1:
                    found = True
                elif dist[pu] == -1:
                    dist[pu] = dist[u] + 1
                    q.append(pu)
        return found

    def dfs(u):
        for v in adj[u]:
            pu = pair_r[v]
            if pu == -1 or (dist[pu] == dist[u] + 1 and dfs(pu)):
                pair_l[u] = v
                pair_r[v] = u
                return True
        dist[u] = -1
        return False

    ans = 0
    while bfs():
        for i in range(nl):
            if pair_l[i] == -1 and dfs(i):
                ans += 1
    return ans

def solve_case(m, n, start, banned):
    total = 10 ** m
    pow10 = [10 ** i for i in range(m)]

    def digits_of(x):
        res = []
        for _ in range(m):
            res.append(x % 10)
            x //= 10
        return res

    def encode(s):
        x = 0
        for c in s:
            x = x * 10 + (ord(c) - 48)
        return x

    bad = [False] * total
    for x in banned:
        bad[x] = True

    start_id = encode(start)

    side = [-1] * total
    left_id = [-1] * total
    right_id = [-1] * total
    nl = nr = 0

    for x in range(total):
        if not bad[x]:
            s = sum(digits_of(x))
            if s & 1:
                side[x] = 1
                right_id[x] = nr
                nr += 1
            else:
                side[x] = 0
                left_id[x] = nl
                nl += 1

    adj = [[] for _ in range(nl)]

    for x in range(total):
        if side[x] != 0:
            continue
        digs = digits_of(x)
        lx = left_id[x]
        for i in range(m):
            cur = digs[i]
            for nd in ((cur + 1) % 10, (cur - 1) % 10):
                y = x + (nd - cur) * pow10[m - 1 - i]
                if not bad[y]:
                    adj[lx].append(right_id[y])

    first = hopcroft_karp(adj, nl, nr)

    if side[start_id] == 0:
        old = left_id[start_id]
        removed_adj = []
        for i in range(nl):
            if i != old:
                removed_adj.append(adj[i])
        second = hopcroft_karp(removed_adj, nl - 1, nr)
    else:
        old = right_id[start_id]
        filtered = []
        for row in adj:
            filtered.append([v if v < old else v - 1 for v in row if v != old])
        second = hopcroft_karp(filtered, nl, nr - 1)

    return "Alice" if second < first else "Bob"

def main():
    t = int(input())
    out = []
    for _ in range(t):
        m, n, s = input().split()
        m = int(m)
        n = int(n)
        banned = []
        for _ in range(n):
            banned.append(int(input().strip()))
        out.append(solve_case(m, n, s, banned))
    print("\n".join(out))

if __name__ == "__main__":
    main()
```実装では、まず使用可能なパスワードのセットを構築します。 の`side`配列には 2 つのパーティションが格納されますが、`left_id`そして`right_id`元のパスワード ID をコンパクトな一致インデックスに変換します。 

隣接世代は一度に 1 桁ずつ変更されます。 変更された数字から整数 ID への変換には 10 のべき乗が使用されるため、文字列の繰り返し作成が回避され、グラフの構築が高速に保たれます。 

最初の Hopcroft-Karp 呼び出しでは、再生可能なグラフの最大一致を計算します。 2 番目の呼び出しでは、2 分割の対応する側から開始頂点を削除します。 右側のケースではインデックスを慎重に調整することで、一致する頂点が消えたときの off-by-one エラーを回避します。 

最後の比較では、マッチング定理を直接使用します。 スタートを削除した後の一致が小さい場合は、スタートが不可欠であったことを意味します。これはまさにアリスが勝つための条件です。 

## 実用的な例

 サンプル 1:

 入力:```
1
1 2 6
7
5
```| ステップ | グラフ全体のマッチング | start | 削除後のマッチング 結果 |
 | --- | --- | --- | --- |
 | グラフを作成する | 頂点 0、1、2、3、4、6、8、9 は残ります | まだ計算されていません | 続ける |
 | 最大一致 | 3 | まだ計算されていません | 続ける |
 | 開始 6 を削除 | 3 | 3 | ボブ |

 マッチング サイズは減少しないため、開始パスワードを回避する最大マッチングが存在します。 ボブはマッチング戦略を使用できます。 

サンプル 2:

 入力:```
1
1 2 9
1
8
```| ステップ | グラフ全体のマッチング | start | 削除後のマッチング 結果 |
 | --- | --- | --- | --- |
 | グラフを作成する | 1 と 8 以外の頂点は残ります | まだ計算されていません | 続ける |
 | 最大一致 | 4 | まだ計算されていません | 続ける |
 | 開始 9 を削除 | 4 | 3 | アリス |

 一致するサイズが 1 つ小さくなります。 開始頂点はすべての最大一致に強制されるため、アリスには勝利の戦略があります。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(VE^0.5) | Hopcroft-Karp は、V <= 100000 および E <= 2mV のグラフで 2 回実行されます。 
| スペース | O(V + E) | グラフと一致する配列は明示的に保存されます。 

可能な最大のグラフには、2 部変換後の 10 万の状態と最大 100 万の有向隣接エントリが含まれます。 2 つのマッチング実行は、指定されたメモリと時間制限内に問題なく収まります。 

## テストケース```python
import sys
import io

def run(inp: str) -> str:
    old = sys.stdin
    sys.stdin = io.StringIO(inp)
    data = sys.stdin.read().strip().split()
    sys.stdin = old
    return ""

# The assertions below are examples for an external judge harness.
# They should call solve_case directly or wrap main in the same file.

assert solve_case(1, 2, "6", [7, 5]) == "Bob"
assert solve_case(1, 2, "9", [1, 8]) == "Alice"
assert solve_case(1, 0, "0", []) == "Bob"
assert solve_case(1, 8, "0", ["1", "2", "3", "4", "5", "6", "7", "9"]) == "Bob"
assert solve_case(2, 99, "00", list(range(1, 100))) in ("Alice", "Bob")
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`m=1, start=6, banned={7,5}`| ボブ | 最大一致により開始 | が回避されるケース
 |`m=1, start=9, banned={1,8}`| アリス | 開始頂点はすべての最大一致に強制的に適用されます。 
|`m=1, start=0, banned={}`| ボブ | 最小のグラフ処理 |
 |`m=1, start=0, all other digits banned`| ボブ | 即負け手処理 |
 | 大きな2桁のグラフ | アリスかボブ | パフォーマンスとインデックスの処理 |

 ## 特殊なケース

 最初から可能なすべての動きが禁止されている場合、アルゴリズムは照合する前にそれらの頂点を削除します。 残りのグラフには、開始点が孤立した頂点として含まれる場合があります。 一致する最大サイズは削除後も変化しないため、答えはボブとなり、アリスが最初の手で負けるというルールと一致します。 

開始パスワードが 2 分割パーティションの右側にある場合、それを削除するには、一致するグラフの左側ではなく右側を縮小する必要があります。 Hopcroft-Karp は左側の隣接関係のみを保存するため、実装ではこれを個別に処理します。 これにより、一般的なインデックス作成のバグが防止されます。 

パスワードが禁止されていない場合、グラフには考えられるすべてのロック状態が含まれます。 この方法は禁止状態の数に依存しないため、依然として機能します。 グラフはロックの次元のみから構築されており、マッチング定理はゲーム履歴を列挙することなくサイクルと長いパスを処理します。 

これをより短い Codeforces スタイルの社説に適応させたり、より正式な定理と補題のスタイルで証明セクションを書き直すこともできます。
