---
title: "CF 2136D - チャンピオンのために"
description: "式(4)のように、$p = beta - alpha$を、単一の$Uj$が区間$[alpha, beta)$内に存在する確率とします。 長さ $r$ のギャップは、$r$ の連続する $U$ が $[alpha, beta)$ の外側にあり、その後に $U$ が間隔内にある場合に発生します。"
date: "2026-06-09T04:10:41+07:00"
tags: ["codeforces", "competitive-programming", "constructive-algorithms", "interactive", "math"]
categories: ["algorithms"]
codeforces_contest: 2136
codeforces_index: "D"
codeforces_contest_name: "Codeforces Round 1046 (Div. 2)"
rating: 1700
weight: 2136
solve_time_s: 124
verified: false
draft: false
---

[CF 2136D - チャンピオンのために](https://codeforces.com/problemset/problem/2136/D)

 **評価:** 1700
 **タグ:** 建設的アルゴリズム、インタラクティブ、数学
 **解決時間:** 2 分 4 秒
 **確認済み:** いいえ

 ## 解決策
 ## 解決策

 しましょう$p = \beta - \alpha$単一の$U_j$狭間にある$[\alpha, \beta)$、式(4)のように。 長さの_ギャップ$r$_ が発生する場合$r$連続した$U$が外に落ちる$[\alpha, \beta)$、続いて$U$間隔の中。 ランダム シーケンスの場合、ギャップの長さがある確率$r$は$\Pr(\text{gap length } = r) = (1-p)^r p, \quad r \ge 0.$で示す$X$の数$U$'s required to observe one gap. ギャップ長が$r$、 それから$X = r+1$、ギャップは次のもので構成されているため、$r$失敗の後には一度の成功が続く。 したがって、$X$成功確率を持つ幾何学的確率変数です$p$。 

の期待値$X$は$$E[X] = \sum_{r=0}^{\infty} (r+1)(1-p)^r p = \sum_{r=0}^{\infty} (1-p)^r p + \sum_{r=0}^{\infty} r (1-p)^r p.$$最初の合計は$$\sum_{r=0}^{\infty} (1-p)^r p = 1,$$これは、合計確率が 1 である等比級数の合計であるため、2 番目の合計は次のようになります。$$\sum_{r=0}^{\infty} r (1-p)^r p = \frac{1-p}{p},$$失敗をカウントする幾何分布の平均の標準公式による。 これらを組み合わせると、$$E[X] = 1 + \frac{1-p}{p} = \frac{1}{p}.$$したがって、予想される数は、$U$単一のギャップを観察することは、$1/p$。 

させて$X_1, X_2, \ldots, X_n$～の独立したコピーであること$X$の数を表します。$U$それぞれを観察するには が必要です$n$ギャップ。 すると、合計数は、$U$を観察する必要があります$n$ギャップは$$S_n = X_1 + X_2 + \cdots + X_n.$$期待値の直線性により、$$E[S_n] = n E[X] = \frac{n}{p}.$$の分散$X$は$$\operatorname{Var}(X) = \frac{1-p}{p^2},$$これも、最初の成功の前に失敗をカウントする幾何学的変数の標準公式によって計算されます。 以来、$X_j$独立していて、$$\operatorname{Var}(S_n) = n \operatorname{Var}(X) = \frac{n(1-p)}{p^2}.$$標準偏差は分散の平方根です。$$\sigma = \sqrt{\operatorname{Var}(S_n)} = \frac{\sqrt{n(1-p)}}{p}.$$これで証明は完了です。 

∎$$\boxed{\text{Expected number of $U$'s: } \frac{n}{p}, \quad \text{Standard deviation: } \frac{\sqrt{n(1-p)}}{p}}$$
