---
title: "CF 103652B - 線形合同ジェネレーター"
description: "$G$ を生成子 $(alpha1,dots,alphak)$ を持つ対称群 $Sn$ のケイリー グラフとし、各生成子が {1,dots,n}$ 内の固定の個別シンボル $x,y に対して $$alphaj(x)=y$$ を満たすと仮定します。"
date: "2026-07-02T21:59:09+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 103652
codeforces_index: "B"
codeforces_contest_name: "2019 Summer Petrozavodsk Camp, Day 8: XIX Open Cup Onsite"
rating: 0
weight: 103652
solve_time_s: 130
verified: false
draft: false
---

[CF 103652B - 線形合同ジェネレーター](https://codeforces.com/problemset/problem/103652/B)

 **評価:** -
 **タグ:** -
 **解決時間:** 2 分 10 秒
 **確認済み:** いいえ

 ## 解決策
 ## 解決策

 しましょう$G$対称群のケイリーグラフであること$S_n$発電機付き$(\alpha_1,\dots,\alpha_k)$、各ジェネレーターが次を満たすと仮定します。$$\alpha_j(x)=y$$固定された個別のシンボルの場合$x,y \in {1,\dots,n}$。 

ハミルトニアンパスを導入する$G$恒等置換から始める$e=12\cdots n$与えられる:$$v_0=e,\ v_1,\dots,v_{N-1}, \quad N=n!.$$各ステップにはインデックスが存在します$j_i$そのような$$v_{i+1}=v_i \alpha_{j_i}.$$定義する$$a_i = v_i(x), \quad b_i = v_i(y).$$遷移ルール​​と仮定から$\alpha_{j}(x)=y$を取得します。$$v_{i+1}(x)=v_i(\alpha_{j_i}(x))=v_i(y),$$したがって$$a_{i+1}=b_i \quad \text{for } 0 \le i \le N-2.$$このアイデンティティは、シーケンスの連続する値をリンクします。$(a_i)$そして$(b_i)$シフトにより:$$b_i=a_{i+1}.$$シーケンス$(a_i)$関数によって取得される値のリストです$g \mapsto g(x)$ハミルトニアンパスに沿って。 それぞれの頂点があるので、$S_n$ちょうど 1 回出現し、$(a_0,a_1,\dots,a_{N-1})$の順列です${1,\dots,n}$。 

から$b_i=a_{i+1}$のために$0 \le i \le N-2$、シーケンス$(b_0,\dots,b_{N-2})$まさにそのサブシーケンスです$$(a_1,a_2,\dots,a_{N-1}).$$したがって$(b_0,\dots,b_{N-2})$～のあらゆる要素が含まれています${1,\dots,n}$を除外する$a_0$。 

最初の頂点で$v_0=e$、 我々は持っています$a_0=e(x)=x$。 したがって、値は$x$には現れない$(b_0,\dots,b_{N-2})$。 以来$(b_0,\dots,b_{N-1})$の順列でもあります${1,\dots,n}$、欠損値は最後の位置に存在する必要があります。$$b_{N-1}=x.$$したがって$$v_{N-1}(y)=x.$$これで証明は完了です。 ∎
