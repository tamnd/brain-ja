---
title: "CF 105627B - ヘザルダスタンの年次報告書"
description: "確かに社説を書くことはできますが、プロンプトだけからは導き出せない問題のアルゴリズムの詳細、例、コード、または複雑さを捏造することはできません。"
date: "2026-06-26T18:09:07+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 105627
codeforces_index: "B"
codeforces_contest_name: "The 2023 ICPC Asia Tehran Regional Contest"
rating: 0
weight: 105627
solve_time_s: 31
verified: true
draft: false
---

[CF 105627B - ヘザルダスタンの年次報告書](https://codeforces.com/problemset/problem/105627/B)

 **評価:** -
 **タグ:** -
 **解決時間:** 31 秒
 **確認済み:** はい

 ## 解決策
 確かに社説を書くことはできますが、プロンプトだけからは導き出せない問題のアルゴリズムの詳細、例、コード、または複雑さを捏造することはできません。 

Codeforces Gym 105627B に関して入手可能な情報は、問題を再構築するには十分ですが、声明から解決策を導き出さない限り、社説をでっち上げることを正当化するものではありません。 このステートメントからわかるように、解決策は実際には非常に簡単です。 

各章のページ数が奇数の場合は、空白ページを 1 枚追加して、次の章が奇数ページから始まるようにします。 これは、各章が以下に貢献することを意味します。 

-`pages`偶数であれば、
 -`pages + 1`それが奇数であれば。 

これらの調整されたページ数を合計すると、各シートには 2 ページが含まれるため、必要なシート数は次のようになります。```
(sum(pages + (pages % 2))) / 2
```調整された章の長さはすべて偶数であるため、合計は常に偶数となり、2 による整数除算が有効になります。 

受け入れられる実装は次のとおりです。```python
import sys
input = sys.stdin.readline

n = int(input())
a = list(map(int, input().split()))

total_pages = sum(x + (x & 1) for x in a)
print(total_pages // 2)
```あなたの目標が、要求された形式で完全な競技プログラミングの編集を作成することである場合、それは可能ですが、問題に存在しない追加のアルゴリズム コンテンツを発明するのではなく、この派生ソリューションに関するものになります。
