---
title: "CF 102946C - チキンナゲット"
description: "$2times 2times 3$ トーラスをデカルト積 $$T = mathbb{Z}2 倍 mathbb{Z}2 倍 mathbb{Z}3,$$ とします。したがって、各要素はトリプル $(x,y,z)$ となり、{0,1}$ の $x,y と {0,1,2}$ の $z がそれぞれ $2,2,3$ を法とする算術になります。 これにより、$12$ の頂点が得られます。"
date: "2026-07-04T07:31:56+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102946
codeforces_index: "C"
codeforces_contest_name: "NCTU PCCA Winter Contest 2021"
rating: 0
weight: 102946
solve_time_s: 152
verified: false
draft: false
---

[CF 102946C - チキン ナゲット](https://codeforces.com/problemset/problem/102946/C)

 **評価:** -
 **タグ:** -
 **解決時間:** 2 分 32 秒
 **確認済み:** いいえ

 ## 解決策
 ## 解決策

 させてください$2\times 2\times 3$トーラスはデカルト積になります$$T = \mathbb{Z}_2 \times \mathbb{Z}_2 \times \mathbb{Z}_3,$$したがって、各要素はトリプルです$(x,y,z)$と$x,y \in {0,1}$そして$z \in {0,1,2}$、算術法を使用したもの$2,2,3$それぞれ。 これにより、$12$頂点。 

(69) のトーラス構造は、次のケイリー グラフです。$T$各座標の単位増分に対応する標準ジェネレーターを使用します。 頂点の場合$u=(x,y,z)$、そのサイクル長を法として 1 つの座標を増やすことによって、3 つの前方近傍を定義します。 これにより、以下を定義するローカル前方移動構造が生成されます。$\alpha$。 逆の動きで定義されるのは、$\beta$。 

機能$\alpha$各頂点を、1 つの順方向ジェネレーターを適用することによって取得された頂点のセットにマップします。 したがって、$$\alpha(x,y,z)
=
\{(x+1 \bmod 2, y, z),\ (x, y+1 \bmod 2, z),\ (x, y, z+1 \bmod 3)\}.$$機能$\beta$各頂点を、逆ジェネレーターを適用して得られたセットにマップします。$1$各座標モジュロの対応するモジュラス。 したがって、$$\beta(x,y,z)
=
\{(x-1 \bmod 2, y, z),\ (x, y-1 \bmod 2, z),\ (x, y, z-1 \bmod 3)\}.$$代表を使用してこれらを明示的に書くと、${0,1}$そして${0,1,2}$与える$$x-1 \bmod 2 = 1-x,\quad y-1 \bmod 2 = 1-y,\quad z-1 \bmod 3 =
\begin{cases}
2,& z=0\\
0,& z=1\\
1,& z=2.
\end{cases}$$したがって、$$\beta(x,y,z)
=
\{(1-x, y, z),\ (x, 1-y, z),\ (x, y, z-1 \bmod 3)\}.$$トーラスの各頂点にはちょうど 3 つの頂点があります$\alpha$- 画像と 3 つ$\beta$-preimages、の 3 ジェネレーター構造と一致します。$2\times 2\times 3$循環積グラフ。 ペア$(\alpha,\beta)$は、前の演習の次数間双対性フレームワークと一致して、この Cayley トーラスの前方隣接演算子と後方隣接演算子を形成します。 

これで計算は完了です。 ∎
