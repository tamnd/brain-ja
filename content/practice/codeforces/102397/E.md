---
title: "CF 102397E - バシャールと悪い土地 (ハード)"
description: "不変条件は、各新しい右端点を処理する前に、以前のすべての縮小後に left が可能な最小の左境界を指すことです。"
date: "2026-08-11T15:48:55+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102397
codeforces_index: "E"
codeforces_contest_name: "Asu Coding Cup 4"
rating: 0
weight: 102397
solve_time_s: 91
verified: true
draft: false
---

[CF 102397E - バシャールと悪い土地 (ハード)](https://codeforces.com/problemset/problem/102397/E)

 **評価:** -
 **タグ:** -
 **解決時間:** 1 分 31 秒
 **確認済み:** はい

 ## 解決策
 ## なぜ機能するのか

 不変条件は、新しい各右エンドポイントを処理する前に、`left`これまでのすべての縮小後の、可能な最小の左境界を指します。 窓際はいつでも`[left, right]`目標に到達すると、目標が満たされる限り、アルゴリズムは左側から家を削除します。 したがって、この特定の場合、`right`、最後の有効なウィンドウは、次で終了する最も短い有効なウィンドウです。`right`。 考えられるすべての最適なセグメントには適切なエンドポイントがあり、そのエンドポイントが処理されると、アルゴリズムはそれを超えないウィンドウを見つけます。 したがって、すべての正しいエンドポイントの最小値を取ると、グローバルに最も短い有効なセグメントが生成されます。 住宅数に換算すると`k`歩いて行ける距離まで`k - 1`必要な答えを返します。 

# Python ソリューション```python
import sys
input = sys.stdin.readline

def solve():
    x, n = map(int, input().split())
    a = list(map(int, input().split()))

    left = 0
    current_sum = 0
    best = n + 1

    for right in range(n):
        current_sum += a[right]

        while current_sum >= x:
            best = min(best, right - left + 1)
            current_sum -= a[left]
            left += 1

    if best == n + 1:
        print(-1)
    else:
        print(best - 1)

if __name__ == "__main__":
    solve()
```入力は 1 回読み取られ、配列は保存されるため、後で左のエンドポイントが対応する値を累計から削除できます。 変数`left`そして`right`現在の連続セグメントを定義します。`current_sum`セグメント全体を繰り返し合計することなく、その合計を保存します。 

外側のループは各家を 1 回だけ追加します。 一度`current_sum >= x`、内側のループは左側から家を削除しようとします。 表現`right - left + 1`は現在セグメント内の住宅の数であるため、スライディング ウィンドウ プロセス中に最小化する必要がある数量です。 

縮小ループ内の操作の順序が重要です。 現在のウィンドウは以前に有効です`a[left]`は削除されるため、変更する前にその長さを考慮する必要があります`left`。 削除後、ウィンドウが無効になる場合がありますが、その場合、ループは自然に停止します。 

Python には整数オーバーフローの問題はありません。 考えられる最大の合計は、`10^5 * 10^5 = 10^10`、これは Python 整数でも安全に表現できます。 

ついに、`best - 1`訪問した家の最小数を徒歩距離に換算します。 一軒の家で十分なら、`best`は`1`印刷された距離は正確です`0`。 

# 実用的な例

 ## サンプル 1

 検討してください`x = 12`そして`a = [1, 3, 4, 5, 2]`。 便利なウィンドウは最終的には`[3, 4, 5]`、3つの家が含まれており、徒歩2分の距離にあります。 

|`right`| 付加価値 |`current_sum`縮小前 |`left`| 最適なウィンドウの長さ |
 | --- | --- | --- | --- | --- |
 | 0 | 1 | 1 | 0 | 見つかりません |
 | 1 | 3 | 4 | 0 | 見つかりません |
 | 2 | 4 | 8 | 0 | 見つかりません |
 | 3 | 5 | 13 | 0 | 3 |
 | 4 | 2 | 11 | 1 | 3 |

 で`right = 3`、合計は 13 になります。ウィンドウ`[1, 3, 4, 5]`が有効な場合、アルゴリズムは削除します`1`、出発`[3, 4, 5]`合計は 12 です。合計が 12 を下回るため、別の家を削除することはできません。したがって、距離に応じて、このエンドポイントには 3 つの家が必要です。`3 - 1 = 2`。 

## サンプル 2

 のために`x = 13`そして`a = [5, 1, 2, 3, 4]`、合計は 15 ですが、13 に達する適切な部分配列はありません。配列全体が必要です。 

|`right`| 付加価値 |`current_sum`|`left`| 最適なウィンドウの長さ |
 | --- | --- | --- | --- | --- |
 | 0 | 5 | 5 | 0 | 見つかりません |
 | 1 | 1 | 6 | 0 | 見つかりません |
 | 2 | 2 | 8 | 0 | 見つかりません |
 | 3 | 3 | 11 | 0 | 見つかりません |
 | 4 | 4 | 15 | 0 | 5 |

 最後のハウスを追加すると、完全な配列は 15 に達します。最初のハウスを削除すると、目標を下回る 10 のみが残るため、5 ハウスのウィンドウは最小限になります。 その歩行距離は、`5 - 1 = 4`。 

# 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |`O(n)`| 右ポインタは左から右に 1 回移動し、左ポインタも前方向にのみ移動するため、各家の追加と削除は最大 1 回です。 |
 | スペース |`O(n)`| 配列はメモリに保存されるため、左側のエンドポイントはその値を削除できます。 |

 と`n <= 100000`、 だいたい`200000`スライディングウィンドウ部分はポインタの動きだけで十分です。 これは必要な制限時間内に余裕で保存できます。`100000`整数はメモリ制限内に十分収まります。 

# テストケース```python
import sys
import io

def solve():
    input = sys.stdin.readline

    x, n = map(int, input().split())
    a = list(map(int, input().split()))

    left = 0
    current_sum = 0
    best = n + 1

    for right in range(n):
        current_sum += a[right]

        while current_sum >= x:
            best = min(best, right - left + 1)
            current_sum -= a[left]
            left += 1

    print(-1 if best == n + 1 else best - 1)

def run(inp: str) -> str:
    old_stdin = sys.stdin
    old_stdout = sys.stdout

    sys.stdin = io.StringIO(inp)
    sys.stdout = io.StringIO()

    solve()
    result = sys.stdout.getvalue().strip()

    sys.stdin = old_stdin
    sys.stdout = old_stdout

    return result

# Provided samples, interpreted according to the displayed input.
assert run("12 5\n1 3 4 5 2\n") == "2", "sample 1"
assert run("13 5\n5 1 2 3 4\n") == "4", "sample 2"
assert run("6 5\n1 1 1 1 1\n") == "-1", "sample 3"

# Minimum-size input, one house is enough, so no walking is required.
assert run("7 1\n7\n") == "0", "single house exactly reaches target"

# One house is enough even though other houses exist.
assert run("5 3\n2 5 1\n") == "0", "single-house window"

# Entire array is required.
assert run("10 4\n1 2 3 4\n") == "3", "whole array required"

# All values equal, target reached by the first three houses.
assert run("9 5\n3 3 3 3 3\n") == "2", "equal values"

# Target cannot be reached.
assert run("100 4\n10 20 30 39\n") == "-1", "insufficient total"

# Maximum-size input.
n = 100000
assert run(f"{n} {n}\n" + " ".join(["1"] * n) + "\n") == str(n - 1), \
    "maximum-size all-equal input"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`7 1 / 7`|`0`| 最小サイズと歩行距離ゼロ |
 |`5 3 / 2 5 1`|`0`| 目標を満たす一戸建て住宅 |
 |`10 4 / 1 2 3 4`|`3`| 配列全体が必要であり、キャッチされます。`length`対`distance`エラー |
 |`9 5 / 3 3 3 3 3`|`2`| 繰り返される値と正確な縮小 |
 |`100 4 / 10 20 30 39`|`-1`| 総ゴールドが不足しています |
 |`100000 100000 / 1 ... 1`|`99999`| 最大`n`と線形時間の動作 |

 # エッジケース

 一軒家でも目標は達成できます。 のために`x = 5`、`n = 3`、 そして`a = [2, 7, 1]`、ウィンドウがターゲットに到達したとき`right = 1`。 アルゴリズムはウィンドウの長さを記録します。`1`前のを削除した後`2`窓から。 を削除することはできません。`7`そうすると合計がゼロになってしまうので、`best = 1`そして答えは`best - 1 = 0`。 重要な点は、家から家までの移動にかかる費用はゼロであるということです。 

金の総量が不十分な場合、縮小ループは有効な最終的な答えを生み出すことができません。 のために`x = 10`そして`a = [2, 3, 4]`、到達する最大の合計は 9 です。`best`センチネル値のままであるため、アルゴリズムは次のように出力します。`-1`最後の部分ウィンドウを解決策として扱う代わりに。 

配列全体が必要な場合、ウィンドウは最後の位置でのみターゲットに到達します。 のために`x = 10`そして`a = [1, 2, 3, 4]`、合計は 10 になります`right = 3`。 ウィンドウの長さは 4 で、削除すると`1`すぐに合計が 9 になるため、最小になります。 答えは、`4 - 1 = 3`、4つのハウス間の3つの動きを表します。 

距離換算は、ターゲットに複数の家が到達する場合にも関係します。 最適なウィンドウが`[l, r]`、 がある`r - l + 1`家だけ`r - l`最初の家と最後の家の間の階段。 ウィンドウの長さを直接返すと、正しい距離が 0 である最も単純なケースを含め、すべての有効な答えに off-by-1 エラーが発生します。 

配列の正の性質により、スライディング ウィンドウが有効になります。 負の値が許可されている場合、左端の値を削除すると合計が増加する可能性があり、単調縮小の議論は成り立たなくなります。 与えられた制約の下では、すべてのハウスが正の量を寄与するため、各ポインターは前方にのみ移動し、線形境界が保証されます。
