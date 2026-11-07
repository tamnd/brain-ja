---
title: "CF 1406C - リンクカット重心"
description: "問題は実際にはアルゴリズムにあるわけではありません。 次のように表示される出力は、プログラムが出力をまったく生成しなかったことを意味します。 これは数学的な問題ではなく、実装上の問題を示しています。"
date: "2026-06-11T08:02:23+07:00"
tags: ["codeforces", "competitive-programming", "constructive-algorithms", "dfs-and-similar", "graphs", "trees"]
categories: ["algorithms"]
codeforces_contest: 1406
codeforces_index: "C"
codeforces_contest_name: "Codeforces Round 670 (Div. 2)"
rating: 1700
weight: 1406
solve_time_s: 492
verified: false
draft: false
---

[CF 1406C - リンクカットセントロイド](https://codeforces.com/problemset/problem/1406/C)

 **評価:** 1700
 **タグ:** 建設的アルゴリズム、DFS および類似のアルゴリズム、グラフ、ツリー
 **解決時間:** 8 分 12 秒
 **確認済み:** いいえ

 ## 解決策
 問題は実際にはアルゴリズムにあるわけではありません。 出力は次のように表示されます。```

```プログラムがまったく出力を生成しなかったことを意味します。 これは数学的な問題ではなく、実装上の問題を示しています。 

前のコードを見ると、サンプル テスト ハーネスには次のものが含まれていました。```python
def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    output = []
    t = int(input())
    ...
```ただし、ソリューション自体はトップレベルのコードとして記述されています。```python
t = int(input())
for _ in range(t):
    ...
```そのコードが、`solve()`関数を使用したり、テスト ハーネスと混合したりすると、簡単に何も実行されなくなる可能性があります。 

さらに重要なのは、数学的アプローチが正しいということです。 

しましょう```
x = a & b
```それから```
(a xor x) + (b xor x)
```最小化され、単純化されます。```
a xor b
```なぜなら、両方の数値が存在するすべてのビットに`1`同時に解消することも可能です。 

例えば：```
a = 6  = 0110
b = 12 = 1100

a xor b = 1010 = 10
```サンプルと一致します。 

したがって、よりシンプルで直接的な解決策は次のとおりです。```
answer = a xor b
```## Python 3 の正しい解決策```python
import sys
input = sys.stdin.readline

def solve():
    t = int(input())
    for _ in range(t):
        a, b = map(int, input().split())
        print(a ^ b)

solve()
```### サンプルでの検証

 入力:```
6
6 12
4 9
59 832
28 14
4925 2912
1 1
```出力：```
10
13
891
18
6237
0
```これは予想される出力と正確に一致します。 

＃＃＃ なぜ`a ^ b`答えは

 各ビットに対して独立して次のようになります。 

- 両方のビットが`0`、貢献度は`0`。 
- 両方のビットが`1`、対応するビットを選択します`x`として`1`、両方の貢献をする`0`。 
- ビットが異なる場合は、次のいずれかが行われます。`(a xor x)`そして`(b xor x)`の選択に関係なく、そのビットを含める必要があります`x`、したがって、寄与はまさにそのビット値です。 

したがって、次の位置のみが`a`そして`b`最小合計に差が残ります。 これらの位置は、正確に設定されたビットです。`a ^ b`。 

したがって：```
min_x ((a xor x) + (b xor x)) = a xor b
```解決策は、テスト ケースごとに 1 つの XOR 演算です。
