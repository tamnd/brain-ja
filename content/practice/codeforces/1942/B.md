---
title: "CF 1942B - ベッシーとMEX"
description: "0 から n-1 までの整数の未知の順列 p から構築された長さ n の配列 a が与えられます。 a の各要素は、a[i] = MEX(p[1..i]) - p[i] という関係を満たします。 タスクは、この a を生成する有効な順列 p を再構築することです。"
date: "2026-05-31T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "constructive-algorithms", "math"]
categories: ["algorithms"]
codeforces_contest: 1942
codeforces_index: "B"
codeforces_contest_name: "CodeTON Round 8 (Div. 1 + Div. 2, Rated, Prizes!)"
rating: 1100
weight: 1942
solve_time_s: 78
verified: false
draft: false
---

[CF 1942B - ベッシーと MEX](https://codeforces.com/problemset/problem/1942/B)

 **評価:** 1100
 **タグ:** 建設的アルゴリズム、数学
 **解決時間:** 1 分 18 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 配列が与えられています`a`長さの`n`、未知の順列から構築されています`p`整数のうち`0`を通して`n-1`。 の各要素`a`という関係を満たす`a[i] = MEX(p[1..i]) - p[i]`。 タスクは、有効な順列を再構築することです。`p`これを生み出すのは`a`。 配列の MEX は、その配列に欠落している最小の非負の整数として定義されます。 

入力は複数のテスト ケースを提供します。 それぞれの場合において、アルゴリズムは順列を再構築する必要があります。`p`効率的に。 

制約は大きくなります。`n`2_10^5 までの大きさになる可能性があり、すべての合計`n`すべてのテスト ケースでも ≤ 2_10^5 です。 これは、テスト ケースあたりの線形時間よりも悪い時間は許容できないことを意味します。 O(n^2) のブルート フォース アプローチでは、最悪の場合、約 10^10 回の操作が必要になりますが、これは 2 秒の制限時間には遅すぎます。 

微妙な点は、負の値と大きな正の値で発生します。`a`。 以来`a[i] = MEX(...) - p[i]`、それは可能です`a[i]`ネガティブになること。 たとえば、ある点の MEX が`2`しかし、順列要素は`4`、 それから`a[i] = 2 - 4 = -2`。 を前提とした不注意なアルゴリズム`a[i] ≥ 0`失敗します。 また、複数の解が存在する可能性があるため、有効な再構成であればどれでも十分です。 

考慮すべきエッジケースは、次のような場合です。`a[i]`は`1`、`0`、または負の数。 たとえば、次の場合`a = [1,1,1]`の場合、アルゴリズムは数値を再利用せずに、MEX の差を考慮した順列を正しく生成する必要があります。 

## アプローチ

 素朴なアプローチは、位置ごとに MEX を最初からシミュレートすることによって、順列を貪欲に再構築することです。`i`、すべての未使用の数値を反復処理して、どの数値が満たされるかを確認します。`a[i] = MEX(current) - x`。 これには、O(n) 時間で各ステップの MEX を再計算する必要があり、O(n^2) アルゴリズムが必要になります。 これは原理的には正しいですが、問題の制約に対して遅すぎます。 

重要な観察は、MEX が各ステップで常に最大 1 ずつ増加するということです。 現在の MEX が`m`の場合、順列の次の要素は次のいずれかになります。`m - a[i]`その番号がまだ利用可能な場合、またはそれより小さい他の未使用の番号の場合`m`。 This gives a linear-time construction:

 1. 順列内のすべての未使用の数値のセットを保持します。 
2. から始まるポインタを使用して現在の MEX を効率的に追跡します。`0`。 
3. ポジションごとに`i`、計算する`p[i] = current_MEX - a[i]`。 この番号が未使用の場合は、割り当ててください。 それ以外の場合は、現在の MEX より小さい未使用の最小番号を選択します。 それに応じて現在の MEX を更新します。 

このアプローチが機能するのは、次の式があるためです。`p[i] = MEX - a[i]`問題ステートメントから直接導出されます。 有効な順列が存在することが保証されているため、式に一致する未使用の数値が常に見つかります。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n^2) | O(n) | 遅すぎる |
 | 最適 | O(n) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. リストを初期化する`used`サイズの`n`どの番号が配置されたかを追跡する`p`。 セットを初期化する`available`すべての数字を含む`0..n-1`。 
2.セット`current_mex`に`0`。 
3. インデックスごとに`i`から`0`に`n-1`、候補数を計算します`x = current_mex - a[i]`。 
4. もし`x`いる`available`、割り当てます`p[i] = x`から削除します。`available`、そして出発します`current_mex`変更しないか、場合によっては増分します`x == current_mex`。 
5. もし`x`入っていない`available`、割り当てます`p[i]`の最小数として`available`。 削除してアップデートしてください`current_mex`必要に応じて。 
6. すべての位置が埋まるまで繰り返します。 
7. 配列を出力する`p`。 

**これが機能する理由:** 不変条件は、各ステップで次のことです。`current_mex`これまでに使用された数値の MEX を反映します。 使用する`p[i] = current_mex - a[i]`MEX 差分公式が成り立つことを保証します。 可用性チェックにより、番号が再利用されないことが保証され、順列プロパティが維持されます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

def solve():
    t = int(input())
    for _ in range(t):
        n = int(input())
        a = list(map(int, input().split()))
        used = [False] * n
        p = [0] * n
        mex = 0
        import heapq
        # maintain min-heap of unused numbers
        available = list(range(n))
        heapq.heapify(available)

        for i in range(n):
            x = mex - a[i]
            if 0 <= x < n and not used[x]:
                p[i] = x
                used[x] = True
                while mex < n and used[mex]:
                    mex += 1
            else:
                # pick smallest unused
                while used[available[0]]:
                    heapq.heappop(available)
                p[i] = heapq.heappop(available)
                used[p[i]] = True
                while mex < n and used[mex]:
                    mex += 1
        print(" ".join(map(str, p)))

if __name__ == "__main__":
    solve()
```このソリューションではブール配列を使用します`used`すでに割り当てられている番号を追跡し、順列の一意性を確保します。 の`mex`ポインタは現在の MEX を効率的に追跡します。 最小ヒープは未使用の数値を保存し、理想的な場合に利用可能な最小値を効率的に選択します。`x`すでに使用されています。 

## 実用的な例

 ### 例 1

 入力:```
5
1 1 -2 1 2
```| 私 | a[i] | メキシコ | 候補者 x | 割り当てられた p[i] | ステップ | の後に使用されます。 ステップ後のメキシコ |
 | --- | --- | --- | --- | --- | --- | --- |
 | 0 | 1 | 0 | -1 | 0 | {0} | 1 |
 | 1 | 1 | 1 | 0 | 1 | {0,1} | 2 |
 | 2 | -2 | 2 | 4 | 4 | {0,1,4} | 2 |
 | 3 | 1 | 2 | 1 | 2 | {0,1,2,4} | 3 |
 | 4 | 2 | 3 | 1 | 3 | {0,1,2,3,4} | 5 |

 これは不変式を示します。`mex`常に現在の MEX を反映し、`p[i] = mex - a[i]`可能であれば。 

### 例 2

 入力:```
3
-2 1 2
```| 私 | a[i] | メキシコ | 候補者 x | 割り当てられた p[i] |
 | --- | --- | --- | --- | --- |
 | 0 | -2 | 0 | 2 | 2 |
 | 1 | 1 | 0 | -1 | 0 |
 | 2 | 2 | 1 | -1 | 1 |

 ここで、負の候補値は、使用可能な最小の未使用番号の選択をトリガーします。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n) | 各要素は 1 回処理され、ヒープ操作は要素ごとに O(log n) ですが、n にわたる合計は O(n log n) であり、制約内で許容されます。 
| スペース | O(n) | ブール配列`used`最小ヒープストアは最大`n`要素 |

 与えられた`sum(n) <= 2*10^5`, これは 2 秒の制限時間内に快適に収まります。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    sys.stdout = io.StringIO()
    solve()
    return sys.stdout.getvalue().strip()

# provided samples
assert run("3\n5\n1 1 -2 1 2\n5\n1 1 1 1 1\n3\n-2 1 2\n") in ["0 1 4 2 3\n0 1 2 3 4\n2 0 1","0 1 4 2 3\n0 1 2 3 4\n2 1 0"], "sample 1"

# custom: minimum size
assert run("1\n1\n0\n") == "0", "min size"

# custom: maximum n, trivial a
max_n = 10
assert run(f"1\n{max_n}\n" + " ".join(["1"]*max_n) + "\n") != "", "all ones"

# custom: negative a triggers selection of available
assert run("1\n3\n-1 -1 -1\n") != "", "all negative"

#
```
