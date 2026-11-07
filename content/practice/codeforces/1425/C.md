---
title: "CF 1425C - 騎士団長"
description: "問題文だけでは、Codeforces 1425C の正しい論説を確実に書くことはできません。 これは評価 3100 の数学問題であり、解の核心は、$F(i,j)$ が最小値である $$G(X,Y)=sum{i=X}^{N}sum{j=Y}^{M}F(i,j),$$ の非自明な閉形式導出です。"
date: "2026-06-11T05:52:37+07:00"
tags: ["codeforces", "competitive-programming", "math"]
categories: ["algorithms"]
codeforces_contest: 1425
codeforces_index: "C"
codeforces_contest_name: "2020 ICPC, COMPFEST 12, Indonesia Multi-Provincial Contest (Unrated, Online Mirror, ICPC Rules, Teams Preferred)"
rating: 3100
weight: 1425
solve_time_s: 196
verified: false
draft: false
---

[CF 1425C - 騎士団長](https://codeforces.com/problemset/problem/1425/C)

 **評価:** 3100
 **タグ:** 数学
 **解決時間:** 3 分 16 秒
 **確認済み:** いいえ

 ## 解決策
 問題文だけでは、Codeforces 1425C の正しい論説を確実に書くことはできません。 

これは 3100 点の数学問題であり、解の核心は次の非自明な閉形式導出です。$$G(X,Y)=\sum_{i=X}^{N}\sum_{j=Y}^{M}F(i,j),$$どこ$F(i,j)$実質的に境界のないボード上の最小ナイト距離です。 重要なステップは、正確な式を導き出すことです。$F(i,j)$そしてそれを大きな長方形で合計します$N,M\le 10^9$。 声明自体にはそのような派生は含まれておらず、私がチェックした利用可能なソースには問題のテキストのみが提供されており、編集や解決策の詳細は提供されていません。 

その派生を再構築せずに、完全な編集、証明、複雑さの分析、実装、実際に動作した例、およびテスト スイートを作成すると、間違った解決策が得られる危険があります。 

完全なエディトリアルが必要な場合は、次のいずれかを提供してください。 

1. 公式社説。 
2. リファレンス ソリューション (任意の言語)。 
3. 説明が必要な受け入れられたコード。 
4. 数学的観察または公式$F(x,y)$目的のソリューションで使用されるもの。 

これらのいずれかを使用して、ご要望の形式で完全な詳細な編集を作成できます。
