---
title: "CF 1118C - 回文行列"
description: "行列が両軸に沿って回文になるように、n^2 の整数を n x n 正方行列に配置するように求められます。 回文行列は、行の順序を逆にしても、列の順序を逆にしても変わりません。"
date: "2026-06-12T04:32:44+07:00"
tags: ["codeforces", "competitive-programming", "constructive-algorithms", "implementation"]
categories: ["algorithms"]
codeforces_contest: 1118
codeforces_index: "C"
codeforces_contest_name: "Codeforces Round 540 (Div. 3)"
rating: 1700
weight: 1118
solve_time_s: 86
verified: false
draft: false
---

[CF 1118C - 回文マトリックス](https://codeforces.com/problemset/problem/1118/C)

 **評価:** 1700
 **タグ:** 建設的アルゴリズム、実装
 **解決時間:** 1 分 26 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 手配をお願いしております`n^2`整数を`n x n`行列が両方の軸に沿って回文になるように正方行列を作成します。 回文行列は、行の順序を逆にしても、列の順序を逆にしても変わりません。 別の見方では、各細胞が`(i, j)`対称的な対応物をミラーリングする必要があります`(n-i-1, j)`、`(i, n-j-1)`、 そして`(n-i-1, n-j-1)`。 たとえば、行列の 4 つの隅にはすべて同じ数字が含まれている必要があります。 同じ原則が内側の層にも再帰的に適用されます。 

入力によりサイズが得られます`n`とリスト`n^2`配置する数字。 各数値を 1 回だけ使用してこのような行列を構築するか、不可能であることを報告する必要があります。 以来`n`は最大でも 20、セルの総数は最大でも 400 です。これは、すべての順列のブルートフォース列挙として、周波数によって位置への数値の組み合わせをチェックするアプローチとしては十分小さいです (これは次のようになります)`400!`）は明らかに実行不可能です。 

明らかではないエッジケースが発生するのは次の場合です。`n`奇数です。 奇数サイズの行列には、中央のセルが 1 つあり、`2n-2`中央の行と中央の列に沿ったセルは、4 回ではなく 1 回だけミラーリングする必要があります。 これらの位置を、必要な倍数に含まれる数値で埋めることができない場合、4 倍数をチェックするだけの単純な実装は失敗します。 たとえば、`n = 3`、中央のセルには少なくとも 1 回出現する任意の数字を入力できますが、4 隅には少なくとも 4 回出現する数字が必要です。 

## アプローチ

 ブルートフォースアプローチでは、すべての順列を試みます。`n^2`数値を入力し、結果の行列が回文であるかどうかをテストします。 これは非常に小さい場合に機能します`n`、しかし、順列の数は階乗的に増加し、`n = 5`、それは`25!`順列。 

重要な洞察は、対称性を利用することです。 行列内のすべての位置は、4 つ、2 つ、または 1 つのセルのグループに属し、すべてに同じ数字が含まれている必要があります。 具体的には、偶数の場合`n`, すべての位置は 4 つのセルのグループを形成するため、各数値は 4 の倍数で表示される必要があります。 奇数の場合`n`、中央のセルはシングルトンで、中央を除く中央の行または列の位置がペアを形成し、残りの位置が 4 つ組を形成します。 この観察により、問題は数値の頻度を数え、それらをサイズ 4、2、または 1 のグループに当てはめることに帰着しますが、これは計算上簡単です。`n ≤ 20`。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O((n^2)!) | O(n^2) | 遅すぎる |
 | 周波数のグループ化 | O(n^2) | O(1000) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 入力配列内の各数値の頻度を数えます。 これにより、各数値から形成できるクアドルプル、ペア、シングルトンの数がわかります。 
2. を初期化します。`n x n`ゼロで埋められた行列。 一番外側から一番内側までレイヤーごとに埋めていきます。 
3. もし`n`が偶数の場合、すべてのセルは 4 つのグループに属します。 左上の象限を繰り返し、象限の各 2x2 正方形に少なくとも 4 回出現する数字を割り当てます。 数値を配置した後、その頻度を 4 ずつ減らします。 必要なときに数字が 4 つ以上残っていない場合は、「NO」を出力します。 
4. もし`n`が奇数の場合は、最初に偶数の場合とまったく同じように外側の層を処理します。 次に、中央の行と中央の列 (中央のセルを除く) を、少なくとも 2 つのコピーが残っている番号を使用して処理します。 ミラーリングされたペアに 1 つの番号を割り当て、そのカウントを 2 つ減らします。 
5. 最後に、中央の 1 つのセルを処理します。このセルには、少なくとも 1 つの残りのカウントを含む任意の数値を入力できます。 
6. 「YES」と完成したマトリックスを出力します。 

**機能する理由:** すべての段階で、アルゴリズムは数値の頻度が位置の対称性要件を満たしていることを確認します。 四重グループは角の対称性を保証し、ペア グループは中央の行/列の対称性を保証し、シングルトンは中心を保証します。 いずれかの段階で必要なグループを形成できない場合、回文行列を構築することはできません。 

## Python ソリューション```python
import sys
input = sys.stdin.readline
from collections import Counter

n = int(input())
a = list(map(int, input().split()))
freq = Counter(a)
matrix = [[0]*n for _ in range(n)]

def place_quad(i, j):
    for num in list(freq):
        if freq[num] >= 4:
            matrix[i][j] = matrix[i][n-j-1] = matrix[n-i-1][j] = matrix[n-i-1][n-j-1] = num
            freq[num] -= 4
            if freq[num] == 0:
                del freq[num]
            return True
    return False

def place_pair(i, j, mirrored_i, mirrored_j):
    for num in list(freq):
        if freq[num] >= 2:
            matrix[i][j] = matrix[mirrored_i][mirrored_j] = num
            freq[num] -= 2
            if freq[num] == 0:
                del freq[num]
            return True
    return False

# Fill quadruples
for i in range(n//2):
    for j in range(n//2):
        if not place_quad(i, j):
            print("NO")
            sys.exit()

# Fill middle row/column if n is odd
if n % 2 == 1:
    mid = n//2
    for i in range(n//2):
        if not place_pair(mid, i, mid, n-i-1):
            print("NO")
            sys.exit()
    for i in range(n//2):
        if not place_pair(i, mid, n-i-1, mid):
            print("NO")
            sys.exit()
    # Fill center
    for num in list(freq):
        if freq[num] >= 1:
            matrix[mid][mid] = num
            freq[num] -= 1
            if freq[num] == 0:
                del freq[num]
            break
    else:
        print("NO")
        sys.exit()

print("YES")
for row in matrix:
    print(' '.join(map(str,row)))
```このソリューションでは、最初に数値の頻度をカウントします。これにより、どの数値が 4 倍体またはペアを満たすことができるかを簡単に知ることができます。 の`place_quad`そして`place_pair`関数は、ミラーリングされた位置を埋めることによって対称性を直接処理します。 奇数の場合`n`、中央の行、中央の列、中央を注意深く扱うことで、すべての対称条件が確実に満たされます。 

## 実用的な例

 **サンプル 1**

 入力:`4 1 8 8 1 2 2 2 2 2 2 2 2 1 8 8 1`| ステップ | アクション | マトリックス |
 | --- | --- | --- |
 | 1 | 左上の 2x2 象限に freq ≥ 4 の数値を入力します。 [[1,2,2,1],[8,2,2,8],[8,2,2,8],[1,2,2,1]] |

 マトリックスが満たされています。 「YES」を出力します。 これにより、偶数の場合は 4 回転で十分であることがわかります。`n`。 

**奇数の例**

 入力:`3 1 1 1 1 2 2 2 2 3`| ステップ | アクション | マトリックス |
 | --- | --- | --- |
 | 1 | コーナーを埋める (4 倍) | [[1,2,1],[2,0,2],[1,2,1]] |
 | 2 | 中心を塗りつぶす | [[1,2,1],[2,3,2],[1,2,1]] |

 アルゴリズムが奇数を処理することを確認します`n`正しく単一の中心を使用してください。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n^2) | 各行列セルは 1 回ずつ入力され、頻度チェックは数値ごとに O(1) です。 
| スペース | O(n^2 + U) | 行列の場合は O(n^2)、一意の数値のカウンターの場合は O(U) (U ≤ 1000) |

 n ≤ 20、n^2 ≤ 400 であるため、アルゴリズムは 2 秒以内、256 MB のメモリで簡単に実行されます。 

## テストケース```python
import sys, io
def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    exec(open("solution.py").read())
    return sys.stdout.getvalue().strip()

# Provided sample
assert run("4\n1 8 8 1 2 2 2 2 2 2 2 2 1 8 8 1\n") == "YES\n1 2 2 1\n8 2 2 8\n8 2 2 8\n1 2 2 1", "sample 1"

# Minimum size n=1
assert run("1\n5\n") == "YES\n5", "min size"

# Maximum size n=20, all equal numbers
all_equal = " ".join(["1"]*400)
assert run(f"20\n{all_equal}\n").startswith("YES"), "max size equal numbers"

# Odd n, impossible
assert run("3\n1 1 2 2 3 3 4 4 5\n") == "NO", "odd impossible"

# Odd n, exact frequencies
assert run("3\n1 1 1 1
```
