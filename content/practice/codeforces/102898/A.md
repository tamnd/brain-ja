---
title: "CF 102898A - 雪だるま"
description: "$U$ が、(6) を満たすすべての多重組み合わせ $dt ldots d2 d1$ のセット、つまり $$s ge dt ge cdots ge d2 ge d1 ge 0 を表すものとします。$$ ヒントで説明されている補数演算は、$s$ に関する反転と補数によって引き起こされる $U$ の標準的なインボリューションです。"
date: "2026-07-04T08:24:24+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102898
codeforces_index: "A"
codeforces_contest_name: "Innopolis Open 2020-2021, qualification, contest 2"
rating: 0
weight: 102898
solve_time_s: 65
verified: false
draft: false
---

[CF 102898A - 雪だるま](https://codeforces.com/problemset/problem/102898/A)

 **評価:** -
 **タグ:** -
 **解決時間:** 1 分 5 秒
 **確認済み:** いいえ

 ## 解決策
 ## 解決策

 しましょう$U$すべての複数の組み合わせのセットを表します$d_t \ldots d_2 d_1$(6) を満たす、つまり$$s \ge d_t \ge \cdots \ge d_2 \ge d_1 \ge 0.$$ヒントで説明されている補数演算は、$U$～に対する逆転と補完によって引き起こされる$s$。 それぞれについて$d = d_t \ldots d_1 \in U$、その補数を定義します$d^\ast = d_t^\ast \ldots d_1^\ast$による$$d_j^\ast = s - d_{t+1-j}, \qquad 1 \le j \le t.$$以来$0 \le d_{t+1-j} \le s$、 それぞれ$d_j^\ast$にある${0,1,\ldots,s}$。 不平等$$d_{t+1-j} \ge d_{t-j}$$暗示する$$s - d_{t+1-j} \le s - d_{t-j},$$それで$$d_j^\ast \ge d_{j+1}^\ast,$$どの場所$d^\ast$またまたで$U$。 変換を 2 回適用すると、元のシーケンスが返されます。$$(d^\ast)_j^\ast = s - d^\ast_{t+1-j} = s - (s - d_j) = d_j.$$したがって、マッピングは$U$。 

ヒントに示されているケースの場合、次の要素は$U$は$4$- エントリを含むタプル${0,1,2,3}$非昇順で、補数演算によりリストが逆になり、各エントリが置き換えられます。$x$による$3-x$。 これにより、リストされたペアが生成されます$$3211 \leftrightarrow 1100,\quad 3210 \leftrightarrow 2100,\quad \ldots,\quad 3000 \leftrightarrow 0003,$$これは、ヒントに記載されているすべての補足を説明します。 

系 C は、複数の組み合わせのファミリーを、エントリのしきい値条件によって決定される 2 つの相補的な部分に分割します。 2 つの半分はインボリューションによって交換されます$d \mapsto d^\ast$、各エントリを反転して置き換えるので、$d_j$による$s-d_{t+1-j}$フォームのあらゆる条件を変換します$d_1 \le k$補完的な状態に$d_t \ge s-k$、後半を定義するあらゆる極限状態についても同様です。 

したがって、「」のすべての要素は、$\partial$系 C の「半分」は、以下の相補的な半分の要素に全単射的に写像されます。$d \mapsto d^\ast$、その逆も同様です。 したがって、2 つの半分は等しいカーディナリティを持ち、一方の半分に確立されたアイデンティティは、このインボリューションを適用することですぐにもう一方の半分に転送されます。 

これで証明は完了です。 ∎
