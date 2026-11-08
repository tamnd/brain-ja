---
title: "CF 1499A - Windowsill 上のドミノ"
description: "$2 倍 n$ のサイズのボードが与えられます。ここで、上の行の最初の $k1$ セルと下の行の $k2$ セルは白、残りは黒です。 また、いくつかの白いドミノ $w$ と黒いドミノ $b$ も与えられます。"
date: "2026-06-10T21:26:40+07:00"
tags: ["codeforces", "competitive-programming", "combinatorics", "constructive-algorithms", "math"]
categories: ["algorithms"]
codeforces_contest: 1499
codeforces_index: "A"
codeforces_contest_name: "Educational Codeforces Round 106 (Rated for Div. 2)"
rating: 800
weight: 1499
solve_time_s: 270
verified: true
draft: false
---

[CF 1499A - Windowsill 上の Domino](https://codeforces.com/problemset/problem/1499/A)

 **評価:** 800
 **タグ:** 組み合わせ論、構成的アルゴリズム、数学
 **解決時間:** 4 分 30 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 サイズのボードが与えられます$2 \times n$、最初の$k_1$一番上の行のセルと、$k_2$一番下の行のセルは白、残りは黒です。 白いドミノもいくつか与えられます$w$そして黒いドミノ$b$。 それぞれのドミノは、$2 \times 1$水平方向または垂直方向に配置できますが、同じ色のセルを覆う必要があります。 タスクは、すべてを配置できるかどうかを判断することです。$w + b$重ならないようにボード上のドミノ。 

入力は複数のテスト ケースを記述します。 テストケースごとに、$n$まで行くことができます$1000$とテストケースの数$t$まで行くことができます$3000$したがって、どのアルゴリズムでも最大で処理する必要があります。$3 \cdot 10^6$ボードセルの合計。 これにより、すべてのドミノ配置を明示的にシミュレートしようとするソリューションは除外されます。$O(n^2)$テストケースごとに作業します。 

明らかではないエッジ ケースは、1 つの行に白または黒のセルが 0 つある場合、ドミノを垂直に配置する必要があるが 1 つの行に十分な対応するセルがない場合、またはドミノを形成できない単色のセルが奇数個ある場合に発生します。 たとえば、次のようなボードがあります。$n=1$、$k_1=0$、$k_2=1$、 そして$w=1$白のセルが 1 つしかないため、白のドミノを収容できません。 白セルの総数のみをチェックする不注意なアプローチでは、配置が可能であると誤って判断されてしまいます。 

## アプローチ

 ブルート フォース アプローチでは、すべての有効な位置にドミノを配置することをシミュレートしようとします。 すべての水平配置を試してから垂直配置を試したり、すべての順列を再帰的に試したりすることもできます。 これは有効な構成をすべて列挙するので正しいですが、遅すぎます。 のために$n = 1000$、配置の数は組み合わせで増加します。$t = 3000$テストケースでは、これは実用的な限界をすぐに超えます。 

重要な洞察は、配置をシミュレーションする必要がないということです。 各ドミノは、同じ色の 2 つのセルを正確にカバーします。 垂直ドミノの場合、配置できる最大数は次のとおりです。$\min(k_1, k_2)$白いドミノと$\min(n - k_1, n - k_2)$黒ドミノ用。 垂直ドミノを使用した後は、各行の残りのセルは同じ行内で水平方向にのみペアにすることができます。 したがって、ある色のドミノの最大数は、垂直に配置されたドミノと残りの単色のセルの半分の合計になります。 これにより、問題は色付きセルの数に関する単純な算術に縮小されます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n^2 * t) | O(n) | 遅すぎる |
 | 細胞を数える | O(t) | お(1) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 各テスト ケースについて、次の内容を読んでください。$n$、$k_1$、$k_2$、$w$、$b$。 ここ$k_1$そして$k_2$は、それぞれ上の行と下の行の白セルの数です。 
2. 配置できる白いドミノの最大数を計算します。 垂直ドミノの制限は次のとおりです。$\min(k_1, k_2)$。 一番上の行に残っている白いセルは、$k_1 - \min(k_1, k_2)$、そして一番下の行に$k_2 - \min(k_1, k_2)$。 各水平ドミノは 2 つのセルを占めるため、配置できる水平ドミノの数は次のとおりです。$(\text{remaining top} + \text{remaining bottom}) // 2$。 垂直方向と水平方向のカウントを加算すると、`max_white`。 
3. 同様に黒のドミノの最大数を計算します。 一番上の行の黒マスの数は$n - k_1$、そして一番下の行に$n - k_2$。 垂直方向のドミノの最大数は次のとおりです。$\min(n - k_1, n - k_2)$水平ドミノは、残りの黒マスを 2 で割ったものです。これらを合計すると、`max_black`。 
4. もし$w \le max_white$そして$b \le max_black$、「YES」を出力します。 それ以外の場合は、「NO」を出力します。 これにより、明示的にドミノを配置しなくても、ボードにすべてのドミノを配置するのに十分なスペースが確保されます。 

**機能する理由**: このアルゴリズムは、各色のドミノの最大数を正確にカウントします。 垂直ドミノは使用率を最大化するために常に最適であり、水平ドミノは残っている単色のセルを使用します。 ドミノは重なることができず、各ドミノはちょうど 2 つのセルをカバーするため、これらの最大値を超えることは不可能です。 この演算により正確さが保証されます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

t = int(input())
for _ in range(t):
    n, k1, k2 = map(int, input().split())
    w, b = map(int, input().split())
    
    # Maximum white dominoes
    vertical_white = min(k1, k2)
    remaining_white_top = k1 - vertical_white
    remaining_white_bottom = k2 - vertical_white
    horizontal_white = (remaining_white_top + remaining_white_bottom) // 2
    max_white = vertical_white + horizontal_white
    
    # Maximum black dominoes
    black_top = n - k1
    black_bottom = n - k2
    vertical_black = min(black_top, black_bottom)
    remaining_black_top = black_top - vertical_black
    remaining_black_bottom = black_bottom - vertical_black
    horizontal_black = (remaining_black_top + remaining_black_bottom) // 2
    max_black = vertical_black + horizontal_black
    
    if w <= max_white and b <= max_black:
        print("YES")
    else:
        print("NO")
```コードは最初にテスト ケースの数を読み取り、次にそれぞれのテスト ケースを反復処理します。 各色の最大ドミノは個別に計算されます。 整数除算を使用すると、完全なドミノのみがカウントされます。 条件`w <= max_white and b <= max_black`が重要な決定点です。 慎重に減算を行うと、負のカウントが回避され、除算がセルのペアを正しく表すことが保証されます。 

## 実用的な例

 **例 1**

 入力:`1 0 1`と`w=1, b=0`| 変数 | 値 |
 | --- | --- |
 | 垂直_白 | 0 |
 | 残り_白_トップ | 0 |
 | 残りの白の下 | 1 |
 | 水平_白 | 0 |
 | マックスホワイト | 0 |

 以来`w = 1 > max_white = 0`、出力は「NO」です。 

**例 2**

 入力:`4 3 1`と`w=2, b=2`| 変数 | 値 |
 | --- | --- |
 | 垂直_白 | 1 |
 | 残り_白_トップ | 2 |
 | 残りの白の下 | 0 |
 | 水平_白 | 1 |
 | マックスホワイト | 2 |
 | ブラックトップ | 1 |
 | 黒底 | 3 |
 | 垂直_黒 | 1 |
 | 残り_黒_トップ | 0 |
 | 残り_黒_底 | 2 |
 | 水平_黒 | 1 |
 | マックスブラック | 2 |

 両方`w <= max_white`そして`b <= max_black`、「YES」を出力します。 

これらのトレースは、アルゴリズムが不均等な行と垂直方向と水平方向の両方のドミノ配置を正しく処理していることを示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(t) | 各テスト ケースには単純な算術演算と比較が含まれます。 
| スペース | お(1) | テスト ケースごとに整数変数のみ、大きな配列は不可 |

 ソリューションは、テスト ケースの数に関係なく、直線的にスケールします。$n$制約に対して十分に効率的になります$t \le 3000$そして$n \le 1000$。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    output = io.StringIO()
    sys.stdout = output
    t = int(input())
    for _ in range(t):
        n, k1, k2 = map(int, input().split())
        w, b = map(int, input().split())
        vertical_white = min(k1, k2)
        remaining_white_top = k1 - vertical_white
        remaining_white_bottom = k2 - vertical_white
        horizontal_white = (remaining_white_top + remaining_white_bottom) // 2
        max_white = vertical_white + horizontal_white
        black_top = n - k1
        black_bottom = n - k2
        vertical_black = min(black_top, black_bottom)
        remaining_black_top = black_top - vertical_black
        remaining_black_bottom = black_bottom - vertical_black
        horizontal_black = (remaining_black_top + remaining_black_bottom) // 2
        max_black = vertical_black + horizontal_black
        if w <= max_white and b <= max_black:
            print("YES")
        else:
            print("NO")
    return output.getvalue().strip()

# provided samples
assert run("5\n1 0 1\n1 0\n1 1 1\n0 0\n3 0 0\n1 3\n4 3 1\n2 2\n5 4 3\n3 1") == "NO\nYES\nNO\nYES\nYES"

# custom cases
assert run("1\n1 0 0\n0 1") == "YES", "single column, only black domino"
assert run("1\n1 1 1\n1 0") == "YES", "single column, only white domino"
assert run("1\n2 1 1\n1 1") == "YES", "two
```
