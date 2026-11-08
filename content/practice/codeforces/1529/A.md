---
title: "CF 1529A - Eshag は大きな配列が大好き"
description: "$U(x)$ と $V(x)$ を整数係数を持つ次数 $r$ の多項式とし、$W(x) = U(x)V(x),$ とすると、$W(x)$ の次数は最大 $2r$ になります。 $U(x) = sum{i=0}^{r} Ui x^i, qquad V(x) = sum{i=0}^{r} Vi x^i, qquad W(x) = sum{k=0}^{2r} Wk x^k と書きます。"
date: "2026-06-10T16:59:23+07:00"
tags: ["codeforces", "competitive-programming", "constructive-algorithms", "greedy", "math"]
categories: ["algorithms"]
codeforces_contest: 1529
codeforces_index: "A"
codeforces_contest_name: "Codeforces Round 722 (Div. 2)"
rating: 800
weight: 1529
solve_time_s: 151
verified: false
draft: false
---

[CF 1529A - Eshag は大きな配列が大好き](https://codeforces.com/problemset/problem/1529/A)

 **評価:** 800
 **タグ:** 建設的アルゴリズム、貪欲、数学
 **解決時間:** 2 分 31 秒
 **確認済み:** いいえ

 ## 解決策
 ## 解決策

 しましょう$U(x)$そして$V(x)$次数の多項式になる$r$整数係数を使用して、$W(x) = U(x)V(x),$それで$W(x)$せいぜい学位を持っている$2r$。 

書く$U(x) = \sum_{i=0}^{r} U_i x^i, \qquad V(x) = \sum_{i=0}^{r} V_i x^i, \qquad W(x) = \sum_{k=0}^{2r} W_k x^k.$修理$2r+1$個別の整数$x_0, x_1, \ldots, x_{2r}$。 定義する$P_j = U(x_j), \qquad Q_j = V(x_j), \qquad R_j = W(x_j) = P_jQ_j.$以来$W(x)$せいぜい学位を持っている$2r$、マッピング$W(x) \mapsto (W(x_0), W(x_1), \ldots, W(x_{2r}))$は単射です。 確かに、最大次数の多項式の場合、$2r$で消える$2r+1$異なる点がある場合、それは同じようにゼロになります。 

したがって、値は$R_0, \ldots, R_{2r}$係数を決定する$W_0, \ldots, W_{2r}$ユニークに。 

を定義します$(2r+1)\times(2r+1)$ヴァンデルモンド行列$A$による$A_{j,k} = x_j^k, \qquad 0 \le j,k \le 2r.$次に、次の評価識別子$W(x)$は$R = AW,$どこ$R = (W(x_0), \ldots, W(x_{2r}))^T, \qquad W = (W_0, \ldots, W_{2r})^T.$の区別性$x_j$ということを暗示します$A$その行列式は以下に等しいため、可逆です。$\prod_{0 \le i < j \le 2r} (x_j - x_i) \ne 0.$したがって$W = A^{-1}R.$させて$\lambda_{k,j}$のエントリを示します$A^{-1}$。 それからそれぞれについて$k$、

 W_k = \sum_{j=0}^{2r} \lambda_{k,j} W(x_j)。 \タグ{25}

 各係数$W_k$したがって、値の固定線形結合になります。$W(x_j)$, ここで定数は$\lambda_{k,j}$選択した補間点のみに依存します$x_0, \ldots, x_{2r}$そしてオンではない$U$または$V$。 

以来$W(x_j) = U(x_j)V(x_j)$そしてそれぞれ$U(x_j), V(x_j)$学位を評価することで得られます-$r$固定整数引数での多項式の場合、そのような各評価は、次のように決定される固定整数による有限数の加算と乗算で構成されます。$x_j$。 それぞれ$W(x_j)$入力サイズ パラメータに対する制限されたサイズの 2 つの整数の 1 つの乗算になります。$n$セクション 4.3.3 の再帰乗算モデルで。 

再構成式 (25) は固定式を適用します。$(2r+1)\times(2r+1)$ベクトルへの線形変換$(W(x_0), \ldots, W(x_{2r}))$。 係数があるので、$\lambda_{k,j}$は独立した固定整数です$n$、この変換は定数による加算と乗算のみで構成されます。 

これにより、各係数が$W_k$値から取得されます$W(x_0), \ldots, W(x_{2r})$線形関係 (25) によります。 

これで証明は完了です。 ∎
