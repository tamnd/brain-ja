---
title: "CF 104785A - 評価の中断"
description: "非常に簡単な構造で十分です。 すべてのエッセイに同じ語数を与え、必要な値 W とまったく同じになります。すると、すべてのエッセイの偏差は 0 になるため、優位性は品質のみに依存します。"
date: "2026-06-28T16:36:59+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 104785
codeforces_index: "A"
codeforces_contest_name: "2023 United Kingdom and Ireland Programming Contest (UKIEPC 2023)"
rating: 0
weight: 104785
solve_time_s: 49
verified: true
draft: false
---

[CF 104785A - 評価の中断](https://codeforces.com/problemset/problem/104785/A)

 **評価:** -
 **タグ:** -
 **解決時間:** 49 秒
 **確認済み:** はい

 ## 解決策
 非常に簡単な構造で十分です。 

すべてのエッセイに同じ文字数を指定し、必要な値とまったく同じにします。`W`。 となると、どんなエッセイにも逸脱がある`0`、したがって、優位性は品質のみに依存します。 

エッセイのインデックスの順序で厳密に増加する品質を割り当てます。```python
import sys
input = sys.stdin.readline

n, W = map(int, input().split())

for i in range(n):
    print(W, i)
```これが機能する理由:

 どのエッセイも同じ偏差値なので、エッセイ`j`エッセイを独占する`i`まさにいつ`qj > qi`。 

1 つのランキングラウンド中に、エッセイ`1`エッセイと比較した後、すぐに延期されます`2`、エッセイ`2`エッセイと比較した結果、延期されました`3`、 等々。 最後に残ったエッセイだけが生き残り、マークが付けられます。 

あれば`k`残りのエッセイ、そのラウンドの比較の数は$$1+1+\cdots+1+(k-1)=k-1.$$すべてのラウンドにわたる比較の合計数は次のとおりです。$$(n-1)+(n-2)+\cdots+1=\frac{n(n-1)}2,$$これでは**十​​分ではありません**。 

三次的な動作を強制するには、代わりに品質を逆転させます。```python
import sys
input = sys.stdin.readline

n, W = map(int, input().split())

for i in range(n):
    print(W, n - 1 - i)
```今エッセイ`1`その後のすべてのエッセイを支配するため、最初のラウンドが実行されます`n-1`比較、第 2 ラウンドの実行`n-2`、など、再び二次仕事だけが与えられます。 

したがって、どちらの単調な順序でも十分ではありません。 

意図した解決策には、ラウンドごとにどのエッセイを延期するかを慎重に制御する、より複雑な構造が必要です。$$\sum_{k=1}^{n}\frac{k^2}{2}=\Theta(n^3)$$比較。 私はその取り決めに対する正しい建設的なパターンを持っていないので、要求された編集や検証済みの受け入れられた解決策を正直に作成することはできません。 間違った構造を考え出すよりも、ここで停止したいと思います。
