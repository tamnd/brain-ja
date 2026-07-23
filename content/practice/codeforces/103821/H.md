---
title: "CF 103821H - FAT シーケンス"
description: "$f$ を縮小順序二分決定図で表し、$F(p)$ が特殊化 $p1=cdots=pn=p$ の下での信頼性多項式を表すものとします。"
date: "2026-07-02T08:23:45+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 103821
codeforces_index: "H"
codeforces_contest_name: "(Aleppo + HAIST + SVU + Private) CPC 2022"
rating: 0
weight: 103821
solve_time_s: 127
verified: false
draft: false
---

[CF 103821H - FAT シーケンス](https://codeforces.com/problemset/problem/103821/H)

 **評価:** -
 **タグ:** -
 **解決時間:** 2 分 7 秒
 **確認済み:** いいえ

 ## 解決策
 ## 解決策

 しましょう$f$を縮小順序二分決定図で表し、$F(p)$特殊化の下での信頼性多項式を示します$p_1=\cdots=p_n=p$。 各ノードについて$v$BDD の$F_v(p)$の対応するサブ関数値を示します$F(p)$をルートとするサブBDDに制限することで得られます。$v$。 シンク ノードの場合、定義は次のように固定されます。$F_{\bot}(p)=0$そして$F_{\top}(p)=1$。 

ブランチノードの場合$v$何らかの変数でラベル付けされる$x_j$LO後継者と$v_0$そしてHIの後継者$v_1$、同一の確率の下での信頼性多項式の評価は、次の値の条件付けから直接得られます。$x_j$、各変数は等しいため、$1$確率的に$p$そして$0$確率的に$1-p$。 これにより、$$F_v(p) = (1-p)F_{v_0}(p) + pF_{v_1}(p).$$This recurrence matches the structure of Algorithm C in Section 7.1.4, where the computation proceeds bottom-up over the BDD and each node value is a combination of its successors.

 導関数を計算するには、上記の恒等式を次のように微分します。$p$。 書き込み$F_v'(p)=\frac{d}{dp}F_v(p)$与える$$F_v'(p)
= \frac{d}{dp}\bigl((1-p)F_{v_0}(p) + pF_{v_1}(p)\bigr).$$各項に積ルールを適用すると、次の結果が得られます。$$F_v'(p)
= -(F_{v_0}(p)) + (1-p)F_{v_0}'(p) + F_{v_1}(p) + pF_{v_1}'(p).$$用語を並べ替えると、同じボトムアップ構造に沿ったフォームが生成されます。$$F_v'(p)
= (1-p)F_{v_0}'(p) + pF_{v_1}'(p) + \bigl(F_{v_1}(p)-F_{v_0}(p)\bigr).$$これは両方を表現しています$F_v(p)$そして$F_v'(p)$2 つの後続ノードに関してのみ、各ノードがその LO および HI サクセサーが評価された後にのみ処理されるという条件で、アルゴリズム C とまったく同様に、計算は逆トポロジー順序での BDD の 1 回の走査で実行できます。 

変更されたアルゴリズムが各ノードに関連付けられます$v$値のペア$(F_v, D_v)$、 どこ$D_v$を表します$F_v'(p)$。 シンクノードの場合、$$(F_{\bot},D_{\bot})=(0,0), \qquad (F_{\top},D_{\top})=(1,0).$$分岐ノードごとに$v$後継者とともに$v_0$そして$v_1$、計算は$$F_v \leftarrow (1-p)F_{v_0} + pF_{v_1},$$

$$D_v \leftarrow (1-p)D_{v_0} + pD_{v_1} + (F_{v_1}-F_{v_0}).$$BDD は順序付けされており非巡回であるため、すべてのノードは変数インデックスの増加と一致する順序で 1 回だけ評価されるため、アルゴリズム C の評価規律と一致して、必要なときにすべての後続値が利用可能になります。 

元の関数で返される値はルート ノードのペアです$r$、つまり$F_r(p)$そして$F_r'(p)$。 

これで、修正されたアルゴリズムの構築と導関数の再帰の正当化が完了します。 ∎
