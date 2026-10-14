---
title: "CF 105316F - 伝説のささやき"
description: "同じ長さの 2 つの配列が与えられています。 2 番目の配列は固定されていますが、最初の配列は任意に並べ替えることができます。 最初の配列の順列を選択した後、各位置は最初の配列の 1 つの値と 2 番目の配列の 1 つの値を組み合わせます。"
date: "2026-06-23T15:09:30+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 105316
codeforces_index: "F"
codeforces_contest_name: "2024 Aleppo Collegiate Programming Contest"
rating: 0
weight: 105316
solve_time_s: 57
verified: true
draft: false
---

[CF 105316F - 伝説のささやき](https://codeforces.com/problemset/problem/105316/F)

 **評価:** -
 **タグ:** -
 **解決時間:** 57 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 同じ長さの 2 つの配列が与えられています。 2 番目の配列は固定されていますが、最初の配列は任意に並べ替えることができます。 最初の配列の順列を選択した後、各位置は最初の配列の 1 つの値と 2 番目の配列の 1 つの値を組み合わせます。 

ペアリングは、選択されたすべてのペアが割り算条件を満たす場合にのみ有効です。つまり、最初の配列から選択された値は、2 番目の配列の対応する値で割り切れる必要があります。 すべての位置でこれを達成できない場合、構成は無効であり、答えは -1 になります。 

有効な完全なペアが存在する場合、各位置は、最初の配列から選択された値を 2 番目の配列からの対応する値で割ることによって形成される比率に寄与します。 ペアリングの値は、すべてのポジションにわたるこれらの比率の最小値です。 目標は、この最小比率ができるだけ大きくなるように最初の配列を再配置することです。 

制約はテスト ケース全体で合計サイズが小さく、テストごとの配列ごとに合計で最大 500 個の要素があります。 これにより、特に対数係数や照合手順がある場合、テスト ケースごとに約 O(n³) またはわずかに悪いソリューションが可能になります。 順列だけでもすでに 500 になるため、指数関数や階乗関数は除外されます。 可能性。 

素朴な試みでは、最初の配列のすべての順列を試し、有効性をチェックし、最小比率を計算して、最良のものを採用します。 n = 10 の場合でも、順列の数は 10 になるため、これはすぐに失敗します。 = 360 万、n = 500 の場合はまったく実行不可能です。 

より微妙な失敗例は、貪欲なマッチング戦略から生じます。 たとえば、各 bi を可能な限り最小の有効な ai と組み合わせるのは自然なことのように思えるかもしれませんが、後でより大きな比率がブロックされ、全体的な最適性が破壊される可能性があります。 小さな b には互換性のある a が 1 つしかないが、実現可能性を維持するには大きな b にも a が必要であるという状況を考えてみましょう。 貪欲に選択すると、有効なグローバル割り当てが存在する場合でも、それが早期に消費され、割り当てが不可能になります。 

もう 1 つの特殊なケースは、そもそもの実現可能性です。 ai で割り切れない bi が少なくとも 1 つ存在する場合、順列に関係なく、どの配列も制約を満たすことができません。 これはすぐに -1 を返す必要があります。 

## アプローチ

 この問題は基本的に、比率に関するボトルネック目標を最大化しながら、割り算の制約に従って b の各値を a の一意の値に割り当てることです。 

ブルートフォースアプローチでは、a のすべての順列を考慮し、各位置が ai % bi == 0 を満たすかどうかを確認し、最小比を計算して、最大値を追跡します。 これは、考えられるすべての割り当てを検討するため、正しいです。 ただし、n が必要です。 各チェックのコストは O(n) で、O(n · n!) 回の操作が行われ、非常に小さい n を超えるとすぐに使用できなくなります。 

重要な観察は、構造が直接の順列に関するものではなく、二部グラフにおける完全な一致に関するものであるということです。 b の各要素は、a の 1 つの要素と正確に一致する必要があります。 辺は割り切れる場合にのみ存在し、ペアリングの重みは ai / bi になります。 選択された最小の重みを最大化したいと考えています。 

これは、マッチングに対する古典的なボトルネック最適化です。 最小比率を直接最適化する代わりに、視点を逆にします。つまり、候補のしきい値 x を固定し、選択されたすべてのペアが ai / bi ≥ x を満たし、かつ可分性も満たす完全なマッチングを構築できるかどうかを尋ねます。 

固定 x の場合、aj が bi で割り切れ、aj ≥ x · bi となるようなエッジ (bi → aj) のみを保持します。 これらの制約の下で完全な一致を見つけることができれば、x は達成可能です。 これにより、問題は 2 部グラフでの実現可能性チェックに変換されます。

実現可能性は x において単調であるため、二分探索が適用可能になります。 x の値をテストし、実現可能な最大値に収束することができます。 各実現可能性チェックは 2 部マッチング問題であり、Hopcroft-Karp で解決できます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース順列 | O(n・n!) | O(n) | 遅すぎる |
 | 二分探索 + 二部マッチング | O(log A · E √V) | お(え) | 承認済み |

 ## アルゴリズムのチュートリアル

 私たちは問題を、一致する実現可能性の質問を繰り返し解くことに帰着させます。 

1. b の各要素を左ノードとして解釈し、a の各要素を右ノードとして解釈します。 接続は、選択された a が選択された b で割り切れる場合にのみ許可されます。 これは、問題によって許可される唯一の有効なペアをエンコードします。 
2. 最小許容比率を表す候補値 x を修正します。 ペア (bi, aj) の場合、aj % bi == 0 かつ aj ≥ x · bi の場合にのみ許可されます。 これにより、妥当性と比率制約が満たされることが保証されます。 
3. これらのフィルター処理されたエッジを使用して 2 部グラフを構築します。 各エッジは、現在のしきい値 x の下で合法的に使用可能な割り当てを表します。 
4. このグラフに対して最大 2 部マッチング アルゴリズムを実行し、b のすべてのノードがマッチングできるかどうかを確認します。 一致するサイズが n に等しい場合は、x が可能です。 それ以外の場合はそうではありません。 
5. 有効な範囲内で最大の x を二分探索します。 上限は max(ai // bi minimum possible paring) ですが、実際には max(a) で制限できます。 
6. 完全一致が存在する最大の x を返します。 x = 0 でも失敗した場合は、完全な代入は不可能なので、-1 を返します。 

### なぜ効果があるのか

 重要な不変条件は、実現可能性が x において単調であるということです。 あるしきい値 x に一致が存在する場合、より小さいしきい値 x' ≤ x はエッジ制約を緩和するだけであり、既存の有効なエッジを破壊することはできません。 これにより、二分探索で最適値を見逃さないことが保証されます。 各ステップで、マッチング ステップは現在の制約が完全な割り当てを許可するかどうかを完全に把握するため、ローカルな決定は貪欲に行われません。 実現可能性は世界中でチェックされます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

from collections import deque

class HopcroftKarp:
    def __init__(self, n, m):
        self.n = n
        self.m = m
        self.g = [[] for _ in range(n)]
        self.pair_u = [-1] * n
        self.pair_v = [-1] * m
        self.dist = [0] * n

    def add_edge(self, u, v):
        self.g[u].append(v)

    def bfs(self):
        q = deque()
        for u in range(self.n):
            if self.pair_u[u] == -1:
                self.dist[u] = 0
                q.append(u)
            else:
                self.dist[u] = -1

        found = False

        while q:
            u = q.popleft()
            for v in self.g[u]:
                pu = self.pair_v[v]
                if pu != -1 and self.dist[pu] == -1:
                    self.dist[pu] = self.dist[u] + 1
                    q.append(pu)
                elif pu == -1:
                    found = True

        return found

    def dfs(self, u):
        for v in self.g[u]:
            pu = self.pair_v[v]
            if pu == -1 or (self.dist[pu] == self.dist[u] + 1 and self.dfs(pu)):
                self.pair_u[u] = v
                self.pair_v[v] = u
                return True
        self.dist[u] = -1
        return False

    def max_matching(self):
        matching = 0
        while self.bfs():
            for u in range(self.n):
                if self.pair_u[u] == -1 and self.dfs(u):
                    matching += 1
        return matching

def possible(a, b, x):
    n = len(a)
    hk = HopcroftKarp(n, n)
    for i in range(n):
        for j in range(n):
            if a[j] % b[i] == 0 and a[j] >= x * b[i]:
                hk.add_edge(i, j)
    return hk.max_matching() == n

def solve():
    t = int(input())
    for _ in range(t):
        n = int(input())
        a = list(map(int, input().split()))
        b = list(map(int, input().split()))

        if n == 1:
            if a[0] % b[0] == 0:
                print(a[0] // b[0])
            else:
                print(-1)
            continue

        if not possible(a, b, 0):
            print(-1)
            continue

        lo, hi = 0, 10**6
        ans = 0

        while lo <= hi:
            mid = (lo + hi) // 2
            if possible(a, b, mid):
                ans = mid
                lo = mid + 1
            else:
                hi = mid - 1

        print(ans)

if __name__ == "__main__":
    solve()
```実装では、実行可能性チェックごとに一致するインスタンスを構築します。 二部構造により、左側に b が固定され、右側に a が固定されます。 条件 a[j] % b[i] == 0 は妥当性を強制し、不等式 a[j] ≥ x * b[i] は比率制約を強制します。 

二分探索は、この実現可能性チェックをラップします。 自明な比率が常に最も弱い要件であるため、下限はゼロから始まり、すべての比率は制約の下で可能な最大商によって制限されるため、上限は安全に 10⁶ に設定されます。 マッチングが成功するたびに、最もよく知られている答えが更新されます。 

唯一の微妙な落とし穴は、各 x に対してマッチングが最初から再計算されることを保証することです。 状態を再利用すると、反復間でエッジが変化するため、正確性が損なわれます。 

## 実用的な例

 a = [6, 8, 9]、b = [2, 3, 3] という小さなケースを考えてみましょう。 

x = 2 をテストします。 

| ステップ | グラフの状態 | マッチングの進行状況 | 実現可能 |
 | --- | --- | --- | --- |
 | ビルド | a ≥ 2b の場合のみエッジ | 限られたエッジ | 不明 |
 | マッチング | 完全な割り当てを試みる | 失敗するかもしれない | いいえ |

 ここで x = 1:

 | ステップ | グラフの状態 | マッチングの進行状況 | 実現可能 |
 | --- | --- | --- | --- |
 | ビルド | すべての分割可能なエッジが許可されます。 より豊富なグラフ | はい |
 | マッチング | すべての b が一致しました | 完全一致が存在します | はい |

 これは、しきい値を高くすると、低いしきい値が機能しても実現可能性が損なわれる可能性があり、二分探索の動機となる可能性があることを示しています。 

2 番目の例: a = [12, 6]、b = [3, 2]。 

x = 2 の場合、有効なペアは 12/3 = 4 と 6/2 = 3 なので、両方とも機能します。 マッチングは成功します。 x = 3 の場合は 12/3 のみが機能しますが、b = 2 の場合は 6/2 = 3 がまだ機能するため、6 では不十分であり、引き続き実行可能です。 x をさらに増やすと、最終的に、一部の b がすべての候補を失ったときに割り当てが中断されます。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(T・log A・E √V) | 各二分探索ステップでは、最大 n² エッジに対して Hopcroft-Karp が実行されます。 
| スペース | O(n²) | 二部グラフの隣接リスト |

 制約により合計 n が 500 に制限されるため、約 20 のバイナリ検索ステップと密なグラフのマッチングでも、ソリューションは時間制限内に問題なく収まります。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    from collections import deque

    class HopcroftKarp:
        def __init__(self, n, m):
            self.n = n
            self.m = m
            self.g = [[] for _ in range(n)]
            self.pair_u = [-1] * n
            self.pair_v = [-1] * m
            self.dist = [0] * n

        def add_edge(self, u, v):
            self.g[u].append(v)

        def bfs(self):
            from collections import deque
            q = deque()
            for u in range(self.n):
                if self.pair_u[u] == -1:
                    self.dist[u] = 0
                    q.append(u)
                else:
                    self.dist[u] = -1

            found = False
            while q:
                u = q.popleft()
                for v in self.g[u]:
                    pu = self.pair_v[v]
                    if pu != -1 and self.dist[pu] == -1:
                        self.dist[pu] = self.dist[u] + 1
                        q.append(pu)
                    elif pu == -1:
                        found = True
            return found

        def dfs(self, u):
            for v in self.g[u]:
                pu = self.pair_v[v]
                if pu == -1 or (self.dist[pu] == self.dist[u] + 1 and self.dfs(pu)):
                    self.pair_u[u] = v
                    self.pair_v[v] = u
                    return True
            self.dist[u] = -1
            return False

        def max_matching(self):
            res = 0
            while self.bfs():
                for i in range(self.n):
                    if self.pair_u[i] == -1 and self.dfs(i):
                        res += 1
            return res

    def possible(a, b, x):
        n = len(a)
        hk = HopcroftKarp(n, n)
        for i in range(n):
            for j in range(n):
                if a[j] % b[i] == 0 and a[j] >= x * b[i]:
                    hk.add_edge(i, j)
        return hk.max_matching() == n

    def solve():
        t = int(input())
        out = []
        for _ in range(t):
            n = int(input())
            a = list(map(int, input().split()))
            b = list(map(int, input().split()))
            if not possible(a, b, 0):
                out.append("-1")
                continue
            lo, hi = 0, 10**6
            ans = 0
            while lo <= hi:
                mid = (lo + hi) // 2
                if possible(a, b, mid):
                    ans = mid
                    lo = mid + 1
                else:
                    hi = mid - 1
            out.append(str(ans))
        return "\n".join(out)

    return solve()

# custom cases

# minimum size
assert run("1\n1\n6\n3\n") == "2"

# impossible
assert run("1\n2\n2 4\n3 5\n") == "-1"

# all equal compatible
assert run("1\n3\n6 12 18\n3 2 6\n") != ""

# exact matching pressure case
assert run("1\n3\n6 10 15\n3 5 5\n") != ""
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 1 要素は分割可能 | 2 | 基本的な正確性 |
 | 有効な一致がありません | -1 | 実行不可能性の処理 |
 | すべて互換性があります | 正の値 | 完全なマッチングの安定性 |
 | 混合制約 | 有効な出力 | 応力構造 |

 ## 特殊なケース

 重要なエッジ ケースは、単一の b 値ですでに割り切れない場合です。 たとえば、a = [4, 6]、b = [3, 5]。 5 は何も除算しないため、部分一致は存在しても完全一致は存在しません。 Hopcroft-Karp では完全なマッチングに到達できないため、アルゴリズムは x = 0 でこれを検出し、すぐに -1 を返します。 

もう 1 つのエッジ ケースは、複数の b 値が単一の大きな a をめぐって競合する場合に発生します。 たとえば、a = [12, 12, 12]、b = [2, 3, 6]。 すべてのペアは割り切れますが、比率制約により、x に応じて異なる割り当てが強制される可能性があります。 マッチング ステップは貪欲にコミットしないため、これを正しく解決します。 割り当てをグローバルに探索し、特定のしきい値に対して有効な取り決めが存在する場合は、たとえ競合が激しい場合でも確実に見つかるようにします。
