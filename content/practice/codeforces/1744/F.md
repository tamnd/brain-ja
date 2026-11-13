---
title: "CF 1744F - メキシコ対中東"
description: "(A(0,0))、(B(1,0))、(C(1,1))、(D(0,1)) の座標に正方形を配置します。 次に、(E) は (AB) の上にあり、(F) は (BC) の上にあり、(BE=BF) になります。 (E=(x,0)) と書くと (BE=1-x) が得られ、(F=(1,y)) と書くと (BF=y) となるため、(y=1-x) および (F=(1,1-x)) となります。"
date: "2026-06-09T15:58:55+07:00"
tags: ["codeforces", "competitive-programming", "math", "two-pointers"]
categories: ["algorithms"]
codeforces_contest: 1744
codeforces_index: "F"
codeforces_contest_name: "Codeforces Round  828 (Div. 3)"
rating: 2000
weight: 1744
solve_time_s: 114
verified: false
draft: false
---

[CF 1744F - MEX 対 MED](https://codeforces.com/problemset/problem/1744/F)

 **評価:** 2000
 **タグ:** 数学、2 つのポインタ
 **解決時間:** 1 分 54 秒
 **確認済み:** いいえ

 ## 解決策
 ## 探検

 (A(0,0))、(B(1,0))、(C(1,1))、(D(0,1)) の座標に正方形を配置します。 次に、(E) は (AB) の上にあり、(F) は (BC) の上にあり、(BE=BF) になります。 (E=(x,0)) と書くと (BE=1-x) が得られ、(F=(1,y)) と書くと (BF=y) となるため、(y=1-x) および (F=(1,1-x)) となります。 

中間点は

 [
 L=\left(\frac{x+1}{2},\frac{1-x}{2}\right),\quad
 N=\left(\frac12,1-\frac{x}{2}\right),\quad
 O=\left(\frac12,\frac12\right)。 
]

 点(K)は(AL)と(DF)の交点から得られます。 

目標は、(C(1,1),K,L,O,N) が共環的であることを証明することです。 すべての座標は (x) の明示的な有理式であるため、最も安定したアプローチは、(x) に応じて 3 点を通る円を決定し、残りの 2 点がその上にあることを確認することです。 

最もデリケートな点は、1 つの点 (L) がパラメーター (x) に依存するため、円の係数も (x) に依存する構成から円が正しく計算されるようにすることです。 

## 問題の理解

 課題は、正方形内に明示的に構築された 5 つの点が共通の円上にあることを証明することです。 

重要な問題は、円の定義点の 1 つ (L) が自由パラメータ (x) に依存するため、円は
