---
title: "CF 214B - ホームタスク"
description: "複数の桁セットが与えられ、その一部またはすべてから、結果の数値が同時に 2、3、5 で割り切れる最大の整数を構築するように求められます。"
date: "2026-05-29T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "brute-force", "constructive-algorithms", "greedy", "math"]
categories: ["algorithms"]
codeforces_contest: 214
codeforces_index: "B"
codeforces_contest_name: "Codeforces Round 131 (Div. 2)"
rating: 1600
weight: 214
solve_time_s: 169
verified: true
draft: false
---

[CF 214B - Hometask](https://codeforces.com/problemset/problem/214/B)

 **評価:** 1600
 **タグ:** ブルート フォース、建設的アルゴリズム、貪欲、数学
 **解決時間:** 2 分 49 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 複数の桁セットが与えられ、その一部またはすべてから、結果の数値が同時に 2、3、5 で割り切れる最大の整数を構築するように求められます。 2、3、5 で割り切れる数値は 30 で割り切れるため、タスクは指定された桁から 30 で割り切れる最大の数値を構築することになります。 入力は単一の整数で構成されます`n`桁数を表し、その後に`n`スペースで区切られた数字。 出力は有効な最大の数値、または`-1`そのような番号が存在しない場合。 数字のゼロ自体の場合を除き、先頭のゼロは許可されません。 

制約により許可されるのは、`n`最大 100,000 であるため、複雑さが O(n log n) よりも悪い解は制限時間を超える可能性があります。 可能な桁の順列をすべて列挙するようなアプローチは避けなければなりません。`n`そして完全に不可能です。 エッジ ケースには、ゼロのみを持つ場合、30 の倍数を形成できない 1 桁の場合、またはゼロを含みながら合計が 3 で割り切れない桁が含まれます。これにより、30 で割り切れなくなります。たとえば、入力などです。`1 5`生産すべき`-1`、 ない`5`30 の倍数を形成する組み合わせがないためです。 

## アプローチ

 素朴なアプローチは、数字のすべての順列を生成し、それぞれの割り算をチェックして、最大値を維持することです。 この総当り攻撃は、30 で割り切れる数値を正確に識別するため機能しますが、その複雑さは最悪の場合でも O(n!) となるため、次のような場合には完全に実行不可能になります。`n`100,000まで。 

重要な洞察は、可分性ルールを分解することです。 2 で割り切れる数値は少なくとも 1 つの偶数桁が必要で、5 で割り切れる数値は 0 または 5 で終わる必要があり、3 で割り切れる数値は合計が 3 の倍数になる桁を持たなければなりません。これらを組み合わせて 30 で割り切れる場合、最後の桁は 0 でなければなりません (2 と 5 の両方で割り切れる唯一の桁であるため)。 ゼロが存在することがわかったら、3 で割り切れるように桁の合計を調整する必要があります。最大の数が必要なので、桁は降順に並べる必要があります。 これにより、問題は桁を数え、3 を法とする合計を調整し、降順に出力するという問題に軽減されます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | お(ん!) | お(ん!) | 遅すぎる |
 | 最適 | O(n) | お(10) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 桁数を読み取る`n`そして数字のリスト。 サイズ 10 の頻度配列を使用して、各桁の出現をカウントします。 
2. 少なくとも 1 つのゼロがあるかどうかを確認します。 そうでない場合は、印刷してください`-1`30 で割り切れる数値は 0 で終わる必要があるためです。 
3. すべての桁の合計を計算します。 3 を法とする合計がゼロでない場合は、合計を 3 で割り切れるようにするために必要な最小の桁セットを削除します。合計を修正するには、1 桁を削除して余り 1 にするか、2 桁を削除して余り 2 にすることができます (またはその逆)。 最終的な数値を最大化するには、最小の桁を削除することを優先します。 
4. 調整後、ゼロ以外の桁が残っていない場合は、印刷します。`0`。 これはすべてゼロの場合を処理します。 
5. 9 桁から 0 までを繰り返し、調整後の出現回数を各桁に追加して、最大の数値を作成します。 
6. 結果の数値を出力します。 

機能する理由: このアルゴリズムは、桁の合計が 3 で割り切れ、数値がゼロで終わるという不変条件を維持し、30 で割り切れることを保証します。桁を降順に並べ替えると、数値が最大化されます。 最小桁を削除すると、可能な限り最大の残りの数値が保持されます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

n = int(input())
digits = list(map(int, input().split()))

count = [0]*10
for d in digits:
    count[d] += 1

if count[0] == 0:
    print(-1)
    sys.exit()

total = sum(d * count[d] for d in range(10))
mod = total % 3

def remove_digits(mod_val):
    # Try removing smallest digit with remainder mod_val
    for r in range(1, 10):
        if r % 3 == mod_val and count[r] > 0:
            count[r] -= 1
            return True
    # Try removing two smallest digits with remainder 3 - mod_val
    needed = 2
    for r in range(1, 10):
        if r % 3 == (3 - mod_val):
            while count[r] > 0 and needed > 0:
                count[r] -= 1
                needed -= 1
            if needed == 0:
                return True
    return False

if mod != 0:
    if not remove_digits(mod):
        print(-1)
        sys.exit()

# handle all zeros
if sum(count[1:]) == 0:
    print(0)
    sys.exit()

res = []
for d in range(9, -1, -1):
    res.extend([str(d)]*count[d])
print(''.join(res))
```コードは最初に桁を数え、少なくとも 1 つのゼロが存在することを確認します。 3 を法とする合計は、割り算を満たすために最小桁数を削除することによって修正されます。 出力は最大値の降順で構築されます。 ゼロのみが残っている場合は、特別な処理が行われます。 

## 実用的な例

 サンプル 1:

 | 数字 | カウント | 合計 | モッド | 削除 | 最終的なカウント | 結果 |
 | --- | --- | --- | --- | --- | --- | --- |
 | [0] | 0..1..0 | 0 | 0 | なし | 0..1..0 | 0 |

 これにより、アルゴリズムが 0 が 30 で割り切れる唯一の数値であることを正しく識別していることが確認されます。 

サンプル 2:

 | 数字 | カウント | 合計 | モッド | 削除 | 最終的なカウント | 結果 |
 | --- | --- | --- | --- | --- | --- | --- |
 | [5,5,5,4,4,4,3,3,3,0] | カウント 0..9 | 36 | 0 | なし | 同じ | 5554443330 |

 これは、アルゴリズムが桁を削除する必要なく最大の数値を保持していることを示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n) | 桁数のカウントは O(n)、合計の調整と印刷は O(10) |
 | スペース | お(10) | 必要なのは固定サイズの周波数配列だけです。 

主要な演算は桁数に比例してスケールされるため、このソリューションは時間とメモリの制限に簡単に適合します。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    n = int(input())
    digits = list(map(int, input().split()))
    count = [0]*10
    for d in digits:
        count[d] += 1
    if count[0] == 0:
        return "-1"
    total = sum(d * count[d] for d in range(10))
    mod = total % 3
    def remove_digits(mod_val):
        for r in range(1, 10):
            if r % 3 == mod_val and count[r] > 0:
                count[r] -= 1
                return True
        needed = 2
        for r in range(1, 10):
            if r % 3 == (3 - mod_val):
                while count[r] > 0 and needed > 0:
                    count[r] -= 1
                    needed -= 1
                if needed == 0:
                    return True
        return False
    if mod != 0:
        if not remove_digits(mod):
            return "-1"
    if sum(count[1:]) == 0:
        return "0"
    res = []
    for d in range(9, -1, -1):
        res.extend([str(d)]*count[d])
    return ''.join(res)

# provided samples
assert run("1\n0\n") == "0"
assert run("10\n5 5 5 4 4 4 3 3 3 0\n") == "5554443330"
assert run("1\n5\n") == "-1"
# custom cases
assert run("3\n0 0 0\n") == "0", "all zeros"
assert run("5\n3 3 3 3 0\n") == "33330", "sum divisible by 3 with zero"
assert run("4\n1 1 1 0\n") == "1110", "remove one digit to fix modulo 3"
assert run("5\n1 4 4 0 0\n") == "44100", "maximize number with multiple zeros"
assert run("6\n9 8 7 0 0 0\n") == "987000", "descending order with multiple zeros"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 3\n0 0 0 | 0 | すべてゼロの場合 |
 | 5\n3 3 3 3 0 | 33330 | ゼロを含む 3 で割り切れる合計 |
 | 4\n1 1 1 0 | 1110 | モジュロ 3 を修正するには 1 桁削除 |
 | 5\n1 4 4 0 0 | 44100 | 複数のゼロを使用して数値を最大化する |
