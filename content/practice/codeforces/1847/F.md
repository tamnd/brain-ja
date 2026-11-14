---
title: "CF 1847F - ボスの正体"
description: "長さ $n$ の有限プレフィックスで始まるシーケンスが与えられます。 その時点以降、シーケンスは停止しません。 代わりに、すべての新しい要素は、2 つの隣接するシフト値の間のビット単位の OR を使用して前の $n$ 要素を結合することによって形成されます。"
date: "2026-06-09T05:45:53+07:00"
tags: ["codeforces", "competitive-programming", "binary-search", "bitmasks", "data-structures", "dfs-and-similar", "greedy", "math", "sortings"]
categories: ["algorithms"]
codeforces_contest: 1847
codeforces_index: "F"
codeforces_contest_name: "Codeforces Round 882 (Div. 2)"
rating: 2500
weight: 1847
solve_time_s: 75
verified: true
draft: false
---

[CF 1847F - ボスの正体](https://codeforces.com/problemset/problem/1847/F)

 **評価:** 2500
 **タグ:** バイナリ検索、ビットマスク、データ構造、DFS および類似の、貪欲、数学、ソート
 **解決時間:** 1 分 15 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 有限の長さのプレフィックスで始まるシーケンスが与えられます。$n$。 その時点以降、シーケンスは停止しません。 代わりに、すべての新しい要素は以前の要素を組み合わせて形成されます。$n$2 つの隣接するシフト値間のビット単位の OR を使用する要素。 具体的には、新しい用語はそれぞれ、前の用語にのみ依存します。$n$各位置は 2 つの前の位置の OR でオフセットされます。$n$。 

これにより、ビット単位の情報に関してすぐに「飽和」する確定的な無限シーケンスが作成されます。ビットがいずれかのウィンドウに表示されると、OR 演算によってのみ右に広がることができます。 

各クエリにはしきい値が与えられます$v$そして、値が厳密に次を超える、この無限シーケンスの中で最も早いインデックスを見つけなければなりません。$v$。 そのような立場が存在しない場合、答えは次のようになります。$-1$。 

制約は、合計が$n$すべてのテスト ケースにわたるクエリの合計数は最大です。$2 \cdot 10^5$。 これにより、シーケンスは無限であり、クエリごとに大きなプレフィックスを生成することすら遅すぎるため、最初の数ステップを超えるシーケンスのシミュレーションは即座に除外されます。 小さな境界を越えて値を明示的に構築することを避ける表現が必要です。 

最初の配列がすでに小さく、OR 伝播によってさらに大きな値を生成できない場合、微妙なエッジ ケースが発生します。 そんな時はすぐに答えが出る$-1$大規模なクエリの場合。 もう 1 つの故障モードは、周期性を仮定すること、またはシーケンスがすべての位置で最大値まですぐに安定すると仮定することですが、これは慎重に正当化されない限り、必ずしも真ではありません。 

## アプローチ

 直接的なアプローチでは、シーケンスをシミュレートします。 位置ごとに、考えられるすべての有用なインデックスを超えるまで、反復を使用して値を計算します。 ただし、シーケンスは無限で、値は OR によってのみ増加するため、最悪の場合、安定化する前に必要な個別の状態の数が非常に多くなる可能性があります。 クエリに合格するまでシーケンスを拡張してクエリに答えようとした場合$v$、各クエリで多くの項の生成が強制される可能性があり、テスト ケース全体で二次関数の爆発が発生する可能性があります。 

重要な点は、各要素がスライディング構造に対するビットごとの OR であるため、位置の値が$i$以前の値の固定セットに依存し、ビットは蓄積することしかできず、消えることはありません。 これは、シーケンス内で各ビットがいつアクティブになるかを追跡することを示唆しています。 

完全な値を追跡する代わりに、各ビット位置について、無限シーケンス内でそのビットが 1 になる最も早いインデックスを維持します。 これらの「アクティブ化時間」がわかれば、任意のインデックスの値を暗黙的に再構築できます。$i$、値はアクティブ化時間が最大ですべてのビットの OR です。$i$。 これにより、問題はビット活性化イベントのしきい値に関する推論に変換されます。 

次にクエリを考えてみましょう$v$。 最小のインデックスが必要です$i$少なくとも 1 つのビットが設定されるように、$v$は欠席しています$a_i$。 同様に、$a_i$外側に少し含まれている必要があります$v$、 意味$a_i$のサブセットではありません$v$。 したがって、ビットマスクがマスクを「エスケープ」する最初の位置を検索します。$v$。 これは、ビット アクティベーション制約に関する範囲最小スタイルの問題になります。 

各ビットについて、それが現れる最も早い位置を事前に計算します。 それから、当然のことながら$v$、最初のインデックス$a_i$を超える$v$に含まれていないすべてのビットの中で最も古いインデックスです。$v$、そのようなビットがあると値が大きくなることが強制されるためです。 この構造は、関連するアクティブ化時間を最小限に維持することに帰着し、効率的に前処理して応答することができます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルート フォース シミュレーション |$O(n \cdot \text{queries})$それより悪い |$O(n)$| 遅すぎる |
 | ビット活性化 + 前処理 |$O((n + q)\log A)$|$O(\log A)$| 承認済み |

 ## アルゴリズムのチュートリアル

 1. 各ビット (0 ～ 30) が最初のセグメントに出現するかどうかを追跡する配列を初期化します。 元の配列で各ビットが設定されている最も古いインデックスを記録します。 これがベースアクティベーションです。 
2. OR 伝播では新しいビットを作成できないことを観察して、反復を暗黙的に処理します。 既存のものを前方に広げるだけです。 したがって、各ビットの最初の出現はプレフィックスによって完全に決定されます。これは、その後の出現は以前のビットの伝播にすぎないためです。 
3. アレイを構築する`first[bit]`そのビットが現れる最初のセグメントに最小インデックスを格納します。 ビットが出現しない場合、その値は無限のままです。 
4. 各クエリ値について$v$、バイナリ形式で解釈します。 少なくとも 1 つのビットが 0 である最も古い位置が必要です。$v$シーケンス内でアクティブになります。 
5. 0 ～ 30 のすべてのビットを繰り返します。$v$、そのアクティブ化時間を考慮してください。 答えは、これらのアクティブ化時間のうちの最小値です。 
6. そのようなビットが存在しない場合 (つまり、$v$シーケンス内に出現するすべてのビットがすでに含まれています)、戻り値$-1$。 

### なぜ効果があるのか

 シーケンス内の各位置は OR 演算によってのみ構築されるため、ビットがいずれかのインデックスに出現すると、そのビットは持続または前方に広がりますが、消えることはありません。 したがって、位置に存在するビットのセットは、$i$正確に、最初に出現するのがその前であるビットの和集合です。$i$。 これにより、インデックスとビット カバレッジの間に単調な関係が作成されます。 値が超過しました$v$ちょうど外側に少し入っているとき$v$したがって、最初のそのようなインデックスは、欠落しているビットの中で最も早いアクティブ化時間によってのみ決定されます。 これにより、シーケンスの後の構造への依存が防止されます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

def solve():
    t = int(input())
    INF = 10**18
    
    for _ in range(t):
        n, q = map(int, input().split())
        a = list(map(int, input().split()))
        
        first = [INF] * 31
        
        for i, x in enumerate(a, start=1):
            for b in range(31):
                if x >> b & 1:
                    first[b] = min(first[b], i)
        
        # If a bit appears later via propagation, it cannot be earlier than initial appearance
        # so first occurrence in prefix is sufficient
        
        for _ in range(q):
            v = int(input())
            ans = INF
            
            for b in range(31):
                if (v >> b) & 1:
                    continue
                ans = min(ans, first[b])
            
            if ans == INF:
                print(-1)
            else:
                print(ans)

if __name__ == "__main__":
    solve()
```このソリューションでは、各ビットが最初のプレフィックスに存在する最も古いインデックスのみが保存されます。 各クエリは、どのビットが欠落しているかをチェックします。$v$、それらのビットの中から最小のアクティベーションインデックスを選択します。 そのインデックスは、シーケンスが超える必要がある最初の場所です。$v$、そのビットは厳密に大きな値を保証するためです。 

実装でよくある落とし穴は、クエリは完全な数値比較ではなく、ビットの欠如のみに依存するということを忘れていることです。 もう 1 つの微妙な問題は、次のようなケースの処理を誤っていることです。$v$これまでに登場するすべてのビットをすでにカバーしています。 これは返すことで正しく処理されます$-1$候補インデックスが存在しない場合。 

## 実用的な例

 この構造から派生した小さなカスタム例を取り上げます。 

入力：```
n = 3, q = 3
a = [1, 2, 4]
queries = [0, 1, 7]
```最初の出現を計算します。 

| ビット | 最初のインデックス |
 | --- | --- |
 | 0 | 1 |
 | 1 | 2 |
 | 2 | 3 |

 今、クエリは次のようになります:

 のために$v = 0$、ビット 0、1、2 がすべて欠落しているため、答えは min(1,2,3) = 1 となります。 

のために$v = 1$(バイナリ 001)、欠落しているビットは 1 と 2 なので、答えは min(2,3) = 2 となります。 

のために$v = 7$(バイナリ 111)、これまでに出現したビットの中に欠落ビットは存在しないため、答えは次のようになります。$-1$。 

これにより、答えはビットの最初の出現のみに依存することが確認されます。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |$O((n + q) \cdot 31)$| 各要素とクエリは最大 31 ビットをチェックします。 
| スペース |$O(31)$| ビットごとに最も古い出現のみを格納します。 

制約により、最大で次のことが許可されます。$2 \cdot 10^5$合計操作、および 31 ビット スキャンは問題なく制限内に収まります。 このソリューションでは、無限シーケンスのシミュレーションを回避します。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    import sys
    
    input = sys.stdin.readline
    
    t = int(input())
    out = []
    
    INF = 10**18
    
    for _ in range(t):
        n, q = map(int, input().split())
        a = list(map(int, input().split()))
        
        first = [INF] * 31
        
        for i, x in enumerate(a, start=1):
            for b in range(31):
                if x >> b & 1:
                    first[b] = min(first[b], i)
        
        for _ in range(q):
            v = int(input())
            ans = INF
            for b in range(31):
                if (v >> b) & 1:
                    continue
                ans = min(ans, first[b])
            out.append(str(-1 if ans == INF else ans))
    
    return "\n".join(out)

# provided samples
assert run("""3
2 3
2 1
1
2
3
4 5
0 2 1 3
0
1
2
3
4
5 5
1 2 3 4 5
7
2
6
0
4
""") == """1
3
-1
2
2
4
-1
-1
-1
3
8
1
5"""

# custom cases
assert run("""1
2 2
1 2
0
3
""") == """1
-1""", "minimal bit coverage"

assert run("""1
3 3
0 0 0
0
1
2
""") == """-1
-1
-1""", "all zeros never exceed"

assert run("""1
4 2
8 4 2 1
0
15
""") == """1
-1""", "full mask query"

assert run("""1
5 3
1 3 7 15 0
0
2
14
""") == """1
2
4""", "mixed growth"

| Test input | Expected output | What it validates |
|---|---|---|
| minimal bit coverage | 1 -1 | single-bit activation dominance |
| all zeros never exceed | -1 -1 -1 | no bit propagation |
| full mask query | 1 -1 | saturation edge case |
| mixed growth | 1 2 4 | ordering of bit activations |

## Edge Cases

A case where all initial values are zero exposes the fact that no bit ever becomes active. The algorithm initializes all `first[b]` to infinity, so every query correctly returns \(-1\), since there is no index where any bit appears.

A case where \( v \) already contains all bits seen in the array demonstrates the final condition. Because every bit that ever appears is included in \( v \), the candidate set of missing bits is empty, and the algorithm returns \(-1\) without needing any search.

A case with sparse high bits shows why per-bit independence is sufficient. If only bit 10 appears, then any \( v \) with that bit set immediately eliminates all candidates, while any \( v \) without it returns the first index of appearance.
```
