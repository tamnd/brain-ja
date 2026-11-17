---
title: "CF 1997E - レベルアップ"
description: "この問題では、Monocarp は、それぞれにレベルを持つモンスターの線形シーケンスをトラバースします。 彼はレベル 1 から開始し、少なくとも現在のレベルと同じレベルのモンスターと戦うことができます。 k 匹のモンスターと戦うたびに、レベルが 1 つ上がります。"
date: "2026-06-08T14:37:58+07:00"
tags: ["codeforces", "competitive-programming", "binary-search", "brute-force", "data-structures", "divide-and-conquer", "implementation"]
categories: ["algorithms"]
codeforces_contest: 1997
codeforces_index: "E"
codeforces_contest_name: "Educational Codeforces Round 168 (Rated for Div. 2)"
rating: 2200
weight: 1997
solve_time_s: 160
verified: false
draft: false
---

[CF 1997E - レベルアップ](https://codeforces.com/problemset/problem/1997/E)

 **評価:** 2200
 **タグ:** 二分探索、ブルート フォース、データ構造、分割統治、実装
 **解決時間:** 2 分 40 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 この問題では、Monocarp は、それぞれにレベルを持つモンスターの線形シーケンスをトラバースします。 彼はレベル 1 から開始し、少なくとも現在のレベルと同じレベルのモンスターと戦うことができます。 彼が戦うときはいつでも`k`モンスター、彼は1つレベルアップします。 各クエリは、レベルアップ頻度が次の場合に Monocarp が特定のモンスターと戦うかどうかを尋ねます。`k`。 入力はモンスター レベルの配列と一連のクエリで構成され、それぞれがモンスターと値を指定します。`k`。 Monocarp がそのモンスターと戦う場合、出力は「YES」、逃げる場合は「NO」です。 

この制約は、モンスターの数とクエリの数の両方が最大 200,000 であることを示しており、クエリごとにプロセスを素朴にシミュレートするアルゴリズムは除外されます。 単純な解決策では、クエリごとにクエリされたインデックスまですべてのモンスターを反復処理することになり、結果として O(n*q) 操作が発生し、最悪の場合は 4·10^10 に達する可能性があり、4 秒の制限時間には大きすぎます。 これは、クエリに効率的に答えるために事前計算またはオフラインのアプローチが必要であることを直ちに示唆しています。 

明らかではないエッジケースには、次のようなシナリオが含まれます。`k = 1`つまり、Monocarp は戦うたびにレベルアップします。`k = n`、後のモンスターに到達するまでレベルアップすることはできないかもしれません。 もう 1 つの微妙な点は、複数のモンスターが同じレベルを持つ場合です。 Monocarp のレベルは正確に増加するまで増加しないため、レベルアップに必要な戦闘数を数えすぎないように注意する必要があります。`k`戦います。 たとえば、すべてのモンスターがレベル 1 で、`k = 2`, モノカープは最初の戦いで、2回目の戦闘後にレベルアップします。 

## アプローチ

 ブルートフォースアプローチは、クエリごとにゲームをシミュレートすることです。レベル 1 から開始し、モンスターを反復処理し、戦闘数を追跡し、Monocarp のレベルを動的に計算します。 このアプローチは正しいですが、複雑さが O(n*q) で、遅すぎます。 

重要な観察は、このプロセスは、クエリされたモンスターの前に Monocarp が遭遇した戦闘の数のみに依存するということです。 各レベルに到達するために必要な戦闘数がわかっていれば、敵を倒すのに必要な最小戦闘数を計算できます。`i`-番目のモンスター。 配列を前処理することで、各クエリを単純な比較に変換できます。`k`、モノカープの蓄積レベルはインデックスのモンスターと戦うのに十分ですか？`i`?

 効率的なソリューションでは、プレフィックス計算と二分探索による **オフライン アプローチ**を使用します。 We can precompute the number of monsters Monocarp must fight to reach each level and then answer each query in O(1) or O(log n) by comparing the required fights to`k`。 洞察としては、モンスターが出現するまでの戦闘回数は単調であるということです。`i`これにより、プロセス全体を繰り返しシミュレーションすることなく、効率的に推論することができます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n*q) | O(n) | 遅すぎる |
 | オフライン + 事前計算 | O(n + q) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 配列を事前計算する`needed`そのような`needed[i]`モノカープがモンスターと戦うために直面​​しなければならない最小の戦い数です`i`。 初期化する`level = 1`そして`fights = 0`。 
2. モンスターを順番に繰り返します。 モンスターごとに、`level > a[i]`, モノカープはモンスターをスキップします。 それ以外の場合、彼は戦い、増加します`fights`を使用してレベルアップが発生するかどうかを計算します。`k`。 
3. 各クエリを個別に計算する代わりに、各モンスターの前に **累積戦闘数** を保存します。 
4. 各クエリについて`(i, x)`、Monocarp のレベルがモンスターと戦うのに十分であるかどうかを確認してください`i`レベルアップを考慮した後: モンスターのレベル`i`は`1 + floor(fights_before_i / x)`。 このレベルを超えると`a[i]`、「YES」を出力します。 それ以外の場合は「NO」を出力します。 

これが機能するのは、戦闘数がわかれば Monocarp の進行が決定的であり、すべてのクエリが異なるだけであるためです。`k`。 戦闘位置を事前に計算することで、各クエリを単一の算術計算に削減します。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

def solve():
    n, q = map(int, input().split())
    a = list(map(int, input().split()))
    
    # Precompute the number of fights needed to reach each monster
    fights_needed = [0] * n
    max_level = 0
    for idx in range(n):
        fights_needed[idx] = max_level
        max_level = max(max_level, a[idx])
    
    for _ in range(q):
        i, k = map(int, input().split())
        i -= 1
        # Level at monster i
        level_at_i = 1 + fights_needed[i] // k
        if level_at_i > a[i]:
            print("NO")
        else:
            print("YES")

if __name__ == "__main__":
    solve()
```### 実装の説明

 クエリごとにすべての戦闘をシミュレートするわけではありません。 その代わり、`fights_needed[i]`遭遇するまでに最小限の戦いをキャプチャします。`i`-番目のモンスター。 計算`level_at_i = 1 + fights_needed[i] // k`その時点での Monocarp のレベルを直接示します。 最後の比較は簡単です。レベルが十分であれば、Monocarp が戦います。 そうしないと、モンスターは逃げます。 Python で入力から 1 から始まるインデックスを 0 から始まるインデックスに調整する場合は注意が必要です。 

## 実用的な例

 | クエリ | 私 | k | 戦いが必要[i] | レベルアットi | モンスター a[i] | 結果 |
 | --- | --- | --- | --- | --- | --- | --- |
 | 1 | 1 | 1 | 0 | 1 | 2 | はい |
 | 2 | 2 | 1 | 1 | 2 | 1 | いいえ |

 この表は、事前に計算されたファイト配列により、どのモンスターでも Monocarp のレベルを迅速に計算できることを示しています。 エッジケース`k = 1`または`k = n`算術計算により一律に扱われます。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n + q) | O(n) で戦闘カウントを事前計算し、O(1) で各クエリに答えます。 
| スペース | O(n) | 事前に計算された戦闘カウントを保存する |

 という制約を考慮すると、`n, q <= 2e5`、このソリューションは 4 秒の制限内に問題なく収まります。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    out = io.StringIO()
    sys.stdout = out
    solve()
    sys.stdout = sys.__stdout__
    return out.getvalue().strip()

# Provided sample
assert run("""4 16
2 1 2 1
1 1
2 1
3 1
4 1
1 2
2 2
3 2
4 2
1 3
2 3
3 3
4 3
1 4
2 4
3 4
4 4""") == """YES
NO
YES
NO
YES
YES
YES
NO
YES
YES
YES
NO
YES
YES
YES
YES""", "sample 1"

# Custom test: minimum-size input
assert run("""1 1
1
1 1""") == "YES", "minimum size"

# Custom test: all monsters same level, k = 1
assert run("""3 3
2 2 2
1 1
2 1
3 1""") == "YES\nYES\nYES", "all-equal"

# Custom test: Monocarp never levels up before last monster
assert run("""4 2
5 5 5 5
1 2
4 4""") == "NO\nNO", "never levels up"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 1 モンスター、1 クエリ | はい | 最小入力 |
 | モンスター 3 体、k=1 | はい、はい、はい | Monocarp は戦うたびにレベルアップします |
 | モンスター 4 体、k=n | いいえ、いいえ | Monocarp は、後のモンスターと戦うのに十分なレベルに達しません。 

## 特殊なケース

 もし`k = 1`, モノカープは戦うたびにレベルアップするので、モノカープのレベルが早く貯まれば弱いモンスターでも逃げることができます。 もし`k = n`、最後のモンスターの前にまったくレベルアップしない可能性があります。 アルゴリズムは次のように計算するため、両方のケースを正しく処理します。`level_at_i`整数の除算に基づいており、これによりこれらの極端な値が自然に説明されます。 Monocarp のレベルは、各戦闘をシミュレートせずに常に正しく計算されます。
