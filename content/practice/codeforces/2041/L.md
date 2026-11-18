---
title: "CF 2041L - 城の構築"
description: "主要な幾何最適化ステップが自明ではないため、問題ステートメントだけから Codeforces 2041L の正しい編集を提供することはできません。また、正しいと証明された受け入れられたアルゴリズムを再構築するための公式ソリューションに関する十分な情報がありません。"
date: "2026-06-08T09:46:53+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 2041
codeforces_index: "L"
codeforces_contest_name: "2024 ICPC Asia Taichung Regional Contest (Unrated, Online Mirror, ICPC Rules, Preferably Teams)"
rating: 2900
weight: 2041
solve_time_s: 82
verified: false
draft: false
---

[CF 2041L - 城の構築](https://codeforces.com/problemset/problem/2041/L)

 **評価:** 2900
 **タグ:** -
 **解決時間:** 1 分 22 秒
 **確認済み:** いいえ

 ## 解決策
 主要な幾何最適化ステップが自明ではないため、問題ステートメントだけから Codeforces 2041L の正しい編集を提供することはできません。また、正しいと証明された受け入れられたアルゴリズムを再構築するための公式ソリューションに関する十分な情報がありません。 

2900 レベルの幾何学問題の場合、欠落している導出を発明すると、誤った編集と誤ったコードが生成される可能性があります。 

厳密に導き出すことができる有用な観察の 1 つは次のとおりです。 

しましょう$P$元の凸多角形とし、$P_c$点を通した反射である$c$。 固定中心の場合$c$、中心を持つすべての中心対称セットの中で$c$、までの可能な最小対称差分距離。$P$によって達成されます$$P \cap P_c.$$結果として生じるコストは、$$\frac{1}{2}\operatorname{area}(P \triangle P_c) = \operatorname{area}(P)-\operatorname{area}(P\cap P_c).$$したがって、問題全体は次のようになります。$$\max_c \operatorname{area}(P\cap P_c),$$これは、次の中心対称の最大の凸部分集合を見つけることと等価です。$P$。 

サンプル三角形の場合、最大交差面積は次のとおりです。$22/3$、与える$$11-\frac{22}{3}=\frac{11}{3},$$これはサンプル出力と一致します。 

ただし、残りのステップである計算は、$$\max_c \operatorname{area}(P\cap P_c)$$凸多角形の場合$n\le 500$、まさに問題の難しい部分であり、公式の幾何学的機構が必要です。 その導出がなければ、信頼できる編集や受け入れられた実装を提供することはできなかったでしょう。 

いずれかを提供できる場合:

 1. 公式社説、
 2. 説明が必要なソリューションコード、
 3. コンテストのチュートリアル PDF、
 4. または社説からの短いヒントでも、

 そうすれば、私はあなたが要求した形式で完全な Codeforces スタイルの論説を書くことができます。
