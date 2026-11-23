---
title: "CF 486B - マトリックスの OR"
description: "サイズ m × n の行列 B が与えられます。各要素は 0 または 1 です。この行列は、特定のルールを使用して同じサイズの元の行列 A から生成されたと考えられます。各要素 B[i][j] は、A の i 番目の行にあるすべての要素の論理和に等しくなります。"
date: "2026-06-07T17:26:52+07:00"
tags: ["codeforces", "competitive-programming", "greedy", "hashing", "implementation"]
categories: ["algorithms"]
codeforces_contest: 486
codeforces_index: "B"
codeforces_contest_name: "Codeforces Round 277 (Div. 2)"
rating: 1300
weight: 486
solve_time_s: 92
verified: true
draft: false
---

[CF 486B - マトリックスの OR](https://codeforces.com/problemset/problem/486/B)

 **評価:** 1300
 **タグ:** 貪欲、ハッシュ、実装
 **解決時間:** 1 分 32 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 マトリックスが与えられています`B`サイズの`m × n`、各要素は 0 または 1 です。この行列はおそらく元の行列から生成されたものと考えられます。`A`特定のルールを使用した同じサイズの各要素`B[i][j]`内のすべての要素の論理和に等しい`i`- 番目の行`A`内のすべての要素と組み合わせると、`j`の - 番目の列`A`。 正式には、`B[i][j] = (OR of row i in A) OR (OR of column j in A)`。 

タスクは行列を再構築することです`A`それは生み出すことができたかもしれない`B`、または、次の理由によりそのような行列が存在しないと判断します。`B`OR ルールと矛盾します。 入力サイズの制限は控えめです (`m, n ≤ 100`）、大まかに実行できるソリューションを可能にします`O(m*n)`時間。 

ゼロが現れると、微妙なエッジケースが発生します。`B`。 もし`B[i][j]`ゼロの場合、すべての要素を強制的に行内に配置します`i`そしてコラム`j`の`A`ゼロになること。 これを強制しないと矛盾が生じます。 どこまでも貪欲に満たそうとする愚直なアプローチ`B[i][j]`どこか他の場所にゼロが競合すると失敗する可能性があります。 

たとえば、次のことを考えてみましょう。```
2 2
1 0
0 0
```ここ`B[1][2] = 0`力`A[1][2] = A[2][2] = 0`。 同様に、`B[2][1] = 0`力`A[2][1] = A[2][2] = 0`。 しかし`B[1][1] = 1`行 1 または列 1 に少なくとも 1 つの 1 が必要です。ゼロを満たすゼロの割り当てにより、次の値に 1 を配置できなくなります。`B[1][1]`、したがって解決策は存在しません。 これらの矛盾を検出することが重要です。 

## アプローチ

 ブルートフォースアプローチは、すべてを試すことです`2^(m*n)`可能な割り当て`A`結果が次のとおりであるかどうかを確認します`B`指定されたものと一致します。 これは理論的には正しいですが、`m = n = 100`、それは最大で含まれるため、`2^10000`組み合わせ。 

重要な洞察は、`B`要素を厳密に制限する`A`： もし`B[i][j] = 0`、行のすべて`i`そしてコラム`j`ゼロでなければなりません。 中のもの`B`より柔軟で、行の和集合に少なくとも 1 つの 1 が必要です`i`そしてコラム`j`。 したがって、初期化から始めることができます`A`どこでも 1 を使用して、ゼロに対応する行と列を明示的にゼロにします。`B`。 最後に、すべてが入っているかどうかを確認します。`B`まだ満足しています。 これにより、問題は単純な 2 パスのアプローチに集約されます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(2^(m*n)) | O(m*n) | 遅すぎる |
 | ゼロ伝播 + 検証 | O(m*n) | O(m*n) | 承認済み |

 ## アルゴリズムのチュートリアル

 1.行列を初期化する`A`サイズの`m × n`でいっぱいです。 これは、すべてを満たすことができるという楽観的な仮定です。`B[i][j] = 1`制約。 
2. 各要素について`B[i][j]`ゼロに等しい場合は、行内のすべての要素を設定します`i`そしてコラム`j`の`A`ゼロに。 これにより、ゼロを入力するという厳格な要件が強制されます。`B`の対応する行または列に要素が存在する場合は発生しません。`A`は 1 つです。 
3. 新しい行列を構築する`C`計算によって`(OR of row i in A) OR (OR of column j in A)`要素ごとに。 これは、次の生成をシミュレートします。`B`候補者からの`A`。 
4. 比較する`C`オリジナルに`B`。 それらが完全に一致する場合は、「YES」とマトリックスを出力します。`A`。 それ以外の場合は、「NO」を出力します。 

なぜ機能するのか: ステップ 2 では、すべてのゼロエントリが`B`で尊敬されています`A`。 ステップ 3 では、すべての値が入っていることを確認します。`B`選択したゼロ配置で達成可能です。 矛盾が発生した場合は検証中に検出され、正確性が保証されます。 ゼロ伝播はすべての厳しい制約を捕捉します。 残りのものは任意に配置して満足させることができます`B`。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

m, n = map(int, input().split())
B = [list(map(int, input().split())) for _ in range(m)]

# Step 1: Initialize A with ones
A = [[1]*n for _ in range(m)]

# Step 2: Zero out rows and columns based on zeros in B
for i in range(m):
    for j in range(n):
        if B[i][j] == 0:
            for k in range(n):
                A[i][k] = 0
            for k in range(m):
                A[k][j] = 0

# Step 3: Verify by reconstructing B
C = [[0]*n for _ in range(m)]
row_or = [0]*m
col_or = [0]*n

for i in range(m):
    row_or[i] = 0
    for j in range(n):
        row_or[i] |= A[i][j]

for j in range(n):
    col_or[j] = 0
    for i in range(m):
        col_or[j] |= A[i][j]

valid = True
for i in range(m):
    for j in range(n):
        C[i][j] = row_or[i] | col_or[j]
        if C[i][j] != B[i][j]:
            valid = False
            break
    if not valid:
        break

if valid:
    print("YES")
    for row in A:
        print(' '.join(map(str, row)))
else:
    print("NO")
```コードは最初に候補を構築します`A`ゼロで示された行と列をゼロにすることによって`B`。 次に、各行と列の OR を事前計算して再構築します。`B`。 再構築された行列が一致する場合、候補は有効です。 それ以外の場合は拒否されます。 事前に計算された行と列の OR を使用すると、要素ごとに計算を繰り返す必要がなくなります。 

## 実用的な例

 ### サンプル 1

 入力:```
2 2
1 0
0 0
```| 私 | j | B[i][j] | A に対するアクション |
 | --- | --- | --- | --- |
 | 0 | 0 | 1 | 最初は何もありません |
 | 0 | 1 | 0 | ゼロ行 0 と列 1 |
 | 1 | 0 | 0 | ゼロ行 1 と列 0 |
 | 1 | 1 | 0 | すでにゼロ |

 再構成されたマトリックス`C`:```
0 0
0 0
```一致しません`B`したがって、出力は`NO`。 これは、矛盾する制約の検出を示しています。 

### サンプル 2

 入力:```
3 3
1 1 1
1 0 1
1 1 1
```ステップ 2 ゼロ調整:

 -`B[1][1] = 0`→ 行 1 と列 1 にゼロ。 

候補者`A`:```
1 0 1
0 0 0
1 0 1
```再構築する`C`:```
1 1 1
1 0 1
1 1 1
```一致`B`したがって、出力は`YES`候補者と`A`。 1 が強制ゼロと共存できることを確認します。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(m*n) | 各ゼロは O(m+n) 個の更新をトリガーします。 B を再構築するには O(m*n) 操作が必要です。 |
 | スペース | O(m*n) | 行列 A、B、C、および row_or 配列と color_or 配列を保存します。 |

 と`m, n ≤ 100`、`m*n ≤ 10000`, これは1秒の制限時間に余​​裕で収まります。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    output = io.StringIO()
    sys.stdout = output
    m, n = map(int, input().split())
    B = [list(map(int, input().split())) for _ in range(m)]
    A = [[1]*n for _ in range(m)]
    for i in range(m):
        for j in range(n):
            if B[i][j] == 0:
                for k in range(n):
                    A[i][k] = 0
                for k in range(m):
                    A[k][j] = 0
    row_or = [0]*m
    col_or = [0]*n
    for i in range(m):
        for j in range(n):
            row_or[i] |= A[i][j]
    for j in range(n):
        for i in range(m):
            col_or[j] |= A[i][j]
    valid = True
    for i in range(m):
        for j in range(n):
            if (row_or[i]|col_or[j]) != B[i][j]:
                valid = False
                break
        if not valid:
            break
    if valid:
        print("YES")
        for row in A:
            print(' '.join(map(str, row)))
    else:
        print
```
