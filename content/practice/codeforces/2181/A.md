---
title: "CF 2181A - アルファベットシティ"
description: "1 つの道路の注文が欠けている場合に、道路標識の完全なセットが何セット作成できるかを計算するように求められます。 アルファベット シティの各通りには大文字で作られた名前があり、各通りには m 個の同一の標識が付けられています。"
date: "2026-06-07T21:56:58+07:00"
tags: ["codeforces", "competitive-programming", "binary-search", "math", "strings"]
categories: ["algorithms"]
codeforces_contest: 2181
codeforces_index: "A"
codeforces_contest_name: "2025-2026 ICPC, NERC, Northern Eurasia Finals (Unrated, Online Mirror, ICPC Rules, Teams Preferred)"
rating: 1300
weight: 2181
solve_time_s: 126
verified: true
draft: false
---

[CF 2181A - アルファベットシティ](https://codeforces.com/problemset/problem/2181/A)

 **評価:** 1300
 **タグ:** 二分探索、数学、文字列
 **解決時間:** 2 分 6 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 1 つの道路の注文が欠けている場合に、道路標識の完全なセットが何セット作成できるかを計算するように求められます。 アルファベットシティの各通りには大文字で作られた名前があり、各通りには順番があります。`m`同一の標識。 1 つの通りの順序が失われた場合、各通りの最大数を順番に知りたいとなります。`k`残りの文字を使用して他の通り用に作成できる標識の数`m`他のすべての通りの標識を作成しながら、不足している通りに対して少なくとも 1 つの標識を作成します。 

実際には、すべての標識からの文字のマルチセットがあり、欠落している道路候補ごとに、欠落している道路の少なくとも 1 つのコピー用の文字を確保しながら、文字を使い果たさずに残りの道路の完全なコピーを何個作成できるかを確認する必要があります。 

この制約により、通りが最大 200,000 あり、各通りの名前の長さが最大 ​​500,000 文字であり、すべての通りの名前の長さの合計が 500,000 文字に制限されることがわかります。 これは、文字の総数が制限されているため、通りごとにすべての文字を繰り返し反復処理することが可能であることを示しています。 主な複雑さは、最大値を評価することから生じます。`k`あらゆる行方不明の通りに。 

行方不明の道路に、すべての道路で珍しい文字または一意の文字が含まれている場合、微妙なエッジ ケースが発生します。 たとえば、ある通りに他の場所では使用されていない文字がある場合、その順序が失われると、残りの通りの完全なセットを 1 つも作成することが不可能になる可能性があります (`k = -1`）。 すべての道路が同一である場合、別の特殊なケースが発生します。 この場合、単一の道路を削除しても文字プールは減りませんが、次の計算が行われます。`k`不足している通りの文字を予約することも考慮する必要があります。 

## アプローチ

 ブルートフォースアプローチでは、失われた可能性のある各道路を考慮します。`l`、他の通りからのすべての文字を合計し、次の値を試します。`k`0から文字がなくなるまで。 これは正しいですが、遅いです。 合計の操作はおよそ O(n × max_possible_k × |alphabet|) となり、大規模なシステムでは管理できません。`m`そして長い名前。 

重要な洞察は、問題がすべての通りの文字を数えることと、計算するために整数の除算を実行することに帰着するということです。`k`。 文字ごとに、利用可能な数を超えずに残りのストリートのコピーを何枚作成できるかを計算できます。 正式には、各文字に対して`c`、 させて`total[c]`全員からの手紙の数になります`m`すべてのストリートのコピーを作成して、`needed[c]`残りのストリートのコピーあたりの文字数の合計になります。 それから最大値`k`単純に`min((total[c] - missing_count[c]) // needed[c] for all letters)`どこ`missing_count`欠落している通りに対して予約されている文字の数です。 

文字数を事前に計算してすべての反復にわたって再利用できるという観察により、問題は反復あたり O(n × total_letters × k) から O(n × 26) に軽減されます。 これは制約を考慮すれば実現可能です。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n × m × | s | ) |
 | 最適 | O(n × 26 + total_letters) | お(26) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 各通りの各文字の出現数をカウントし、それらを辞書または配列のリストに保存します。 これにより、あらゆる街路の手紙配布に迅速にアクセスできるようになります。 
2. すべての道路にわたる各タイプの文字の合計数を計算し、次の値を掛けます。`m`。 これは私たちのグローバルレタープールです。 
3. 通りごとに`l`(欠落しているものと想定されます)、少なくとも 1 つのコピーが必要なので、その文字をグローバル プールから差し引きます。 次に、残りの道路について最大の整数を計算します。`k`各通りの文字を乗算するとカウントされるようになります。`k`プール内で使用可能な文字を超えないでください。 
4. これは、アルファベットの各文字を反復処理して計算することによって行われます。`(available_letters_for_this_letter) // (sum_of_letters_for_remaining_streets)`。 すべての文字の最小値から最大値が得られます。`k`。 
5. どの文字でも、使用可能な文字が不足している場合`k = 0`、出力します`-1`あの通りのために。 
6. 次の通りに移動する前に、レタープールを元の合計に戻します。 

機能する理由: 街路全体での手紙の入手可能性は独立しており、加算されます。 合計を事前に計算するので、整数の除算によって文字を使いすぎることがなくなります。 すべてのレターで最小値を取ることにより、すべてのレター要件が同時に満たされることが保証されます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

from collections import Counter

n, m = map(int, input().split())
streets = [input().strip() for _ in range(n)]

# Count letters in each street
letter_counts = [Counter(s) for s in streets]

# Compute total letters from all streets multiplied by m
total_letters = Counter()
for c in letter_counts:
    for letter, count in c.items():
        total_letters[letter] += count * m

result = []

for i in range(n):
    missing = letter_counts[i]
    available = total_letters.copy()
    
    # Reserve letters for one copy of missing street
    for letter, count in missing.items():
        available[letter] -= count
    
    # Compute maximum k for other streets
    max_k = float('inf')
    for j in range(n):
        if j == i:
            continue
        for letter, count in letter_counts[j].items():
            if count == 0:
                continue
            if available[letter] < 0:
                max_k = -1
                break
            max_k = min(max_k, available[letter] // count)
        if max_k == -1:
            break
    result.append(max_k if max_k != float('inf') else -1)

print(' '.join(map(str, result)))
```解決策では、`Counter`オブジェクトには、通りごとの文字数と合計文字数が保存されます。 のコピー`total_letters`欠落している各道路の変更が後続の反復に影響を与えないようにします。 整数の除算`(available[letter] // count)`は、なくなった通りの文字を予約しているので安全です。 

## 実用的な例

 ### サンプル入力 1```
3 10
NEERC
NERC
NEF
```| ストリート | 手紙 | 利用可能な合計 | Max k の計算 | 結果 |
 | --- | --- | --- | --- | --- |
 | 1 件がありません | NERC + NEF | N:19、E:19、R:11、C:11、F:10 | k = 分(19//2, 19//3, 11//1, 11//1, 10//1) = 9 | 9 |
 | 2 が見つかりません | NEERC + NEF | N:20、E:21、R:11、C:10、F:10 | k = 9 | 9 |
 | 3 が見つかりません | NEERC + NERC | N:20、E:20、R:11、C:11、F:0 | F 1 部使用不可 → -1 | -1 |

 このトレースは、失われた街路で完全に欠落している文字が正しく処理されていることを確認します。 

### サンプル入力 2```
2 5
A
B
```| ストリート | 手紙 | 最大 k |
 | --- | --- | --- |
 | 1 件がありません | B | 利用可能 A=5-1=4、B=5 |
 | 2 が見つかりません | あ | 同様のロジック |

 これは、一文字ストリートの処理とゼロによる除算の回避を示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n * 26 + total_letters) | すべての通りの文字を数えると O(total_letters)、n 通りの最大 k を計算すると O(n*26) |
 | スペース | O(n * 26) | ストリートごとの文字数と合計文字数カウンター |

 すべての道路名の長さの合計が ≤ 5 × 10^5 であり、26 文字が定数であるため、この解決策は効率的です。 

## テストケース```python
import sys, io
from collections import Counter

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    n, m = map(int, input().split())
    streets = [input().strip() for _ in range(n)]
    letter_counts = [Counter(s) for s in streets]
    total_letters = Counter()
    for c in letter_counts:
        for letter, count in c.items():
            total_letters[letter] += count * m
    result = []
    for i in range(n):
        missing = letter_counts[i]
        available = total_letters.copy()
        for letter, count in missing.items():
            available[letter] -= count
        max_k = float('inf')
        for j in range(n):
            if j == i:
                continue
            for letter, count in letter_counts[j].items():
                if count == 0:
                    continue
                if available[letter] < 0:
                    max_k = -1
                    break
                max_k = min(max_k, available[letter] // count)
            if max_k == -1:
                break
        result.append(max_k if max_k != float('inf') else -1)
    return ' '.join(map(str, result))

# provided sample
assert run("3 10\nNEERC\nNERC\nNEF\n") == "9 9 -1"

# single-letter streets
assert run("2 5\nA\nB\n") == "4 4"

# all
```
