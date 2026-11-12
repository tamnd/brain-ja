---
title: "CF 1703F - 不等式を満たすペアに関するさらに別の問題"
description: "$[-1,1]$ 上で非自明な分布を持つ確率変数 $X$ を生成するための 2 つの構成が提案されています。 1 つ目は、三角関数変換を通じて単一の均一偏差をマップします。"
date: "2026-06-09T21:38:52+07:00"
tags: ["codeforces", "competitive-programming", "binary-search", "data-structures", "dp", "greedy", "sortings"]
categories: ["algorithms"]
codeforces_contest: 1703
codeforces_index: "F"
codeforces_contest_name: "Codeforces Round 806 (Div. 4)"
rating: 1300
weight: 1703
solve_time_s: 161
verified: false
draft: false
---

[CF 1703F - 不等式を満たすペアに関するさらに別の問題](https://codeforces.com/problemset/problem/1703/F)

 **評価:** 1300
 **タグ:** 二分探索、データ構造、dp、貪欲、ソート
 **解決時間:** 2 分 41 秒
 **確認済み:** いいえ

 ## 解決策
 ## 解決策

 確率変数を生成するために 2 つの構造が提案されています$X$自明ではない分布を持つ$[-1,1]$。 1 つ目は、三角関数変換を通じて単一の均一偏差をマップします。 2 番目の方法では、単位ディスクで拒否サンプリングを使用し、受け入れられた点の有理変換を適用します。 

問題は、これら 2 つの手順が同じ分布を引き起こすかどうかです。$X$。 

させて$U$～に均一に分布する$[0,1]$。 方法 1 の定義$$X = \sin\!\left(\frac{\pi}{2}U\right).$$以来$U$一様、変数$T = \frac{\pi}{2}U$均一です$\left[0, \frac{\pi}{2}\right]$。 変革$X = \sin T$はこの区間では単調増加であるため、次の分布になります。$X$マッピングの反転によって決定されます。 

のために$-1 \le x \le 1$、イベント$X \le x$と同等です$$\sin T \le x \quad \Longleftrightarrow \quad T \le \arcsin x,$$以来$\sin T$増加しています。 したがって$$\Pr(X \le x) = \Pr\!\left(T \le \arcsin x\right) = \frac{2}{\pi}\arcsin x.$$したがって、方法 1 は分布関数を生成します。$$F_1(x) = \frac{2}{\pi}\arcsin x.$$方法 2 は独立したユニフォームを生成します$U, V$の上$[-1,1]$条件付き$U^2 + V^2 < 1$。 これは、単位円板上の均一な分布です。 ジョイント密度はディスク全体で一定であるため、受け入れられる点は放射状に対称になります。 私たちは定義します$$X = \frac{U^2 - V^2}{U^2 + V^2}.$$円盤上に極座標を導入します。 させて$$U = R\cos\Theta, \quad V = R\sin\Theta,$$どこ$R \in [0,1]$そして$\Theta \in [0,2\pi)$、 と$\Theta$均一で独立した$R^2$面積の測定によって引き起こされるという意味で。 それから$$X = \frac{R^2\cos^2\Theta - R^2\sin^2\Theta}{R^2\cos^2\Theta + R^2\sin^2\Theta} = \frac{\cos^2\Theta - \sin^2\Theta}{1} = \cos(2\Theta).$$ラジアル変数は完全にキャンセルされます。 の配布$X$したがって、次によってのみ決定されます。$\Theta$、これは均一です$[0,2\pi)$。 

させて$T = 2\Theta$、 それで$T$均一です$[0,4\pi)$。 以来$\cos T$期間あり$2\pi$、1 つのピリオドに制限すると、次のような同等の表現が得られます。$T$ユニフォームオン$[0,2\pi)$。 したがって、次の分布を計算できます。$X = \cos T$と$T \sim \mathrm{Unif}[0,2\pi)$。 

のために$-1 \le x \le 1$、$$\Pr(X \le x) = \Pr(\cos T \le x).$$の上$[0,2\pi)$、セット${\cos T \le x}$全長が$2\pi - 2\arccos x$。 したがって$$F_2(x) = 1 - \frac{1}{\pi}\arccos x.$$アイデンティティの使用$\arccos x = \frac{\pi}{2} - \arcsin x$を取得します。$$F_2(x) = 1 - \frac{1}{\pi}\left(\frac{\pi}{2} - \arcsin x\right) = \frac{1}{2} + \frac{1}{\pi}\arcsin x.$$次に、2 つの分布関数を比較します。 

方法 1 で得られるのは$$F_1(x) = \frac{2}{\pi}\arcsin x,$$一方、方法 2 では次のようになります。$$F_2(x) = \frac{1}{2} + \frac{1}{\pi}\arcsin x.$$これらの関数は等価ではありません。 たとえば、$x=0$、$$F_1(0) = 0, \qquad F_2(0) = \frac{1}{2}.$$分布関数はサポートの点で異なるため、誘導された分布は同じではありません。 

これで解決策は完了です。 ∎
