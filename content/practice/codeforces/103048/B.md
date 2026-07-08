---
title: "CF 103048B - ブレスレット"
description: "$2 x 2 x 3$ トーラスをデカルト積 $C2 x C2 x C3,$ とすると、その要素は $i が {0,1}$、$j が {0,1}$、$k が {0,1,2}$ のトリプル $(i,j,k)$ となり、それぞれの座標で $2,2,3$ を法として加算が行われます。"
date: "2026-07-04T01:50:26+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 103048
codeforces_index: "B"
codeforces_contest_name: "2021 ECNU Campus Invitational Contest"
rating: 0
weight: 103048
solve_time_s: 75
verified: false
draft: false
---

[CF 103048B - ブレスレット](https://codeforces.com/problemset/problem/103048/B)

 **評価:** -
 **タグ:** -
 **解決時間:** 1 分 15 秒
 **確認済み:** いいえ

 ## 解決策
 ## 解決策

 させてください$2 \times 2 \times 3$トーラスはデカルト積になります$C_2 \times C_2 \times C_3,$したがって、その要素はトリプルです$(i,j,k)$と$i \in {0,1}$、$j \in {0,1}$、$k \in {0,1,2}$、加算はモジュロで計算されます$2,2,3$それぞれの座標で。 

機能$\alpha$そして$\beta$トーラスに関連付けられた 2 つは、最初の 2 つの循環座標で単位変換として機能しますが、3 番目の座標は変更されません。 したがって$\alpha(i,j,k) = (i+1 \bmod 2,\; j,\; k), \qquad \beta(i,j,k) = (i,\; j+1 \bmod 2,\; k).$各座標サイクルは独立しているため、$\alpha$または$\beta$残りの座標には影響しません。 3番目のコーディネート$k$は両方のマップの下で不変のままです。 

関数を明示的に計算するには、すべてをリストします。$12$頂点を選択し、定義を直接適用します。 

のために$k=0$、$\alpha(0,0,0)=(1,0,0), \quad \beta(0,0,0)=(0,1,0),$

$\alpha(1,0,0)=(0,0,0), \quad \beta(1,0,0)=(1,1,0),$

$\alpha(0,1,0)=(1,1,0), \quad \beta(0,1,0)=(0,0,0),$

$\alpha(1,1,0)=(0,1,0), \quad \beta(1,1,0)=(1,0,0).$のために$k=1$、$\alpha(0,0,1)=(1,0,1), \quad \beta(0,0,1)=(0,1,1),$

$\alpha(1,0,1)=(0,0,1), \quad \beta(1,0,1)=(1,1,1),$

$\alpha(0,1,1)=(1,1,1), \quad \beta(0,1,1)=(0,0,1),$

$\alpha(1,1,1)=(0,1,1), \quad \beta(1,1,1)=(1,0,1).$のために$k=2$、$\alpha(0,0,2)=(1,0,2), \quad \beta(0,0,2)=(0,1,2),$

$\alpha(1,0,2)=(0,0,2), \quad \beta(1,0,2)=(1,1,2),$

$\alpha(0,1,2)=(1,1,2), \quad \beta(0,1,2)=(0,0,2),$

$\alpha(1,1,2)=(0,1,2), \quad \beta(1,1,2)=(1,0,2).$これら 2 つのマップは最初の 2 つの座標に関与します。$\alpha^2 = \beta^2 = \mathrm{id},$そして彼らは通勤し、$\alpha\beta(i,j,k) = \beta\alpha(i,j,k) = (i+1 \bmod 2,\; j+1 \bmod 2,\; k),$したがって、トーラスは、最初の 2 つの周期因子に独立して作用する 2 つの可換インボリューションによって生成されます。 これにより、完全な値が得られます$\alpha$そして$\beta$の構造$2 \times 2 \times 3$トーラス。 

これで解決策は完了です。 ∎
