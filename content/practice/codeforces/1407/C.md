---
title: "CF 1407C - チョコレートバニー"
description: "これで、ソリューションが失敗する理由が明らかになりました。 トレースバック: これは、入力が 1413C ギターの問題ではないときに、range(6) の j の a[j] にアクセスしようとしたことから生じます (元のギター ソリューションは 6 弦を想定しています)。"
date: "2026-06-11T07:52:13+07:00"
tags: ["codeforces", "competitive-programming", "constructive-algorithms", "interactive", "math", "two-pointers"]
categories: ["algorithms"]
codeforces_contest: 1407
codeforces_index: "C"
codeforces_contest_name: "Codeforces Round 669 (Div. 2)"
rating: 1600
weight: 1407
solve_time_s: 286
verified: false
draft: false
---

[CF 1407C - チョコレートバニー](https://codeforces.com/problemset/problem/1407/C)

 **評価:** 1600
 **タグ:** 構築的アルゴリズム、インタラクティブ、数学、2 つのポインター
 **解決時間:** 4 分 46 秒
 **確認済み:** いいえ

 ## 解決策
 これで、ソリューションが失敗する理由が明らかになりました。 トレースバック:```
IndexError: list index out of range
pairs.append((bi - a[j], i))
```これはアクセスしようとしたときに発生します`a[j]`のために`j`で`range(6)`(元のギター ソリューションは 6 弦を想定しています)、入力は **1413C ギターの問題ではありません**。 

新しい入力は、複数の行列またはグリッドのように構造化されています。```
5         <- probably number of test cases
3         <- first test case has 3 rows
1 1 1     <- first row
2 2 2     <- second row
3 3 3     <- third row
...
```古いコードは、**正確に 6 つの文字列**、1 行に 6 つの数字、その後に音符の数と音符シーケンスが続くことを想定しています。 ここではそうではありません。 それが理由です`IndexError`アルゴリズムが利用可能な配列の外側でインデックスを作成していることが起こります。 

### 診断

 - 古いアルゴリズム: 期待されます`len(a) == 6`。 
- 新しい入力: 最初のテスト ケースには 3 行しかありません (`len(a) = 3`)、2 番目のテスト ケースは 4x? など
 -`pairs.append((bi - a[j], i))`失敗する理由`j`5まで上がります。 

**結論:** ランタイム エラーはロジックのバグではなく、**入力形式の不一致**です。 この入力に対して Codeforces 1413C のソリューションを実行することはできません。 

### 可変長入力の修正 (同じアルゴリズムを維持したい場合)

 目標が、任意の数の「文字列」(入力の行) に対してコードを堅牢にすることである場合は、ハードコードされたコードを置き換えます。`6`と`len(a)`:```
for j in range(len(a)):
    pairs.append((bi - a[j], i))
```これにより、「文字列」行に含まれる数値の数に関係なく、同じアルゴリズムが機能することができます。 

以下は、元のアルゴリズム用に修正された **安全な Python 3 ソリューション**であり、可変長文字列をサポートするようになりました。```python
import sys
input = sys.stdin.readline

def solve():
    a = list(map(int, input().split()))
    n = int(input())
    b = list(map(int, input().split()))

    pairs = []
    for i in range(n):
        bi = b[i]
        for j in range(len(a)):
            pairs.append((bi - a[j], i))

    pairs.sort()
    cnt = [0] * n
    have = 0
    l = 0
    ans = float('inf')

    for r in range(len(pairs)):
        fret_r, idx_r = pairs[r]
        if cnt[idx_r] == 0:
            have += 1
        cnt[idx_r] += 1

        while have == n:
            ans = min(ans, fret_r - pairs[l][0])
            fret_l, idx_l = pairs[l]
            cnt[idx_l] -= 1
            if cnt[idx_l] == 0:
                have -= 1
            l += 1

    print(ans if ans != float('inf') else 0)

if __name__ == "__main__":
    solve()
```### 重要なポイント

 - 交換`6`と`len(a)`避ける`IndexError`可変長入力の場合。 
- 入力が実際に**ギターの問題形式**であることを確認する必要があります。 この修正は **任意の行列をギターの問題に変換しません**。 
- これをマルチ行列入力で実行しようとすると、データが **別の問題**を表すため、アルゴリズムは論理的に失敗します。 IndexError はなくなりましたが、アルゴリズムは任意のグリッドではなくメロディーの音符と文字列を期待します。 

必要に応じて、*このアルゴリズムを書き換えて、各テスト ケースに可変長の「文字列」とメモが含まれる複数のテスト ケースを自動的に処理する**ことができます。これにより、最後の例のような入力に対して安全に実行されます。これにより、アルゴリズムは保持されますが、クラッシュは回避されます。 

私にそうしてほしいですか？
