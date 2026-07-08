---
title: "CF 103034A - パックマンとパワーペレット"
description: "$$Fn(z)=prod{j=0}^{n-1}(1+z+cdots+z^{sj})、qquad left(!binom{S(n)}{k}!right)=[z^k]Fn(z).$$ とすると、$Fn=F{n-1}(1+z+cdots+z^{s{n-1}})$ となり、係数抽出は次のようになります。 $$left(!binom{S(n)}{k}!right) = sum{r=0}^{s{n-1}}left(!binom{S(n-1)}{k-r}!"
date: "2026-07-04T05:21:58+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 103034
codeforces_index: "A"
codeforces_contest_name: "April Fools Contest 2021 Archive (ZS)"
rating: 0
weight: 103034
solve_time_s: 139
verified: false
draft: false
---

[CF 103034A - パックマンとパワーペレット](https://codeforces.com/problemset/problem/103034/A)

 **評価:** -
 **タグ:** -
 **解決時間:** 2 分 19 秒
 **確認済み:** いいえ

 ## 解決策
 ## 解決策

 しましょう$$F_n(z)=\prod_{j=0}^{n-1}(1+z+\cdots+z^{s_j}),
\qquad 
\left(\!\binom{S(n)}{k}\!\right)=[z^k]F_n(z).$$それから$F_n=F_{n-1}(1+z+\cdots+z^{s_{n-1}})$、したがって、係数抽出は次のようになります$$\left(\!\binom{S(n)}{k}\!\right)
=
\sum_{r=0}^{s_{n-1}}\left(\!\binom{S(n-1)}{k-r}\!\right),$$という慣例で$\left(!\binom{S(n-1)}{k-r}!\right)=0$いつ$k-r<0$。 これは、係数の畳み込みから直接導出される、パスカルの規則とまったく同じものです。 

修理$k$。 それぞれについて$n$、シーケンス$\left(!\binom{S(n)}{k}!\right)$厳密に増加しています$n$いつでも$k\le \sum_{j=0}^{n-1}s_j$、拡大してから$n$上記の畳み込みに新しい非負の寄与が導入され、少なくとも 1 つの項が厳密に正になります。$k$新しい要素については実現可能です。 特に、固定ごとに$k$ユニークなミニマルがあります$n$と$\left(!\binom{S(n)}{k}!\right)>0$。 

### 表現の存在

 しましょう$N\ge 0$そして修正します$t$。 定義する$n_t$フォームの最大のインデックスとして$s_j\cdot j$そのような$$\left(\!\binom{S(n_t)}{t}\!\right)\le N.$$そんな$n_t$なぜなら存在する$\left(!\binom{S(n)}{t}!\right)$最終的には超える$N$として$n$成長しており、増加しています$n$。 

セット$$N^{(t-1)} = N - \left(\!\binom{S(n_t)}{t}\!\right).$$畳み込み恒等式から、$$\left(\!\binom{S(n_t)}{t}\!\right)
=
\left(\!\binom{S(n_t-1)}{t}\!\right)
+
\left(\!\binom{S(n_t-1)}{t-1}\!\right)
+
\cdots
+
\left(\!\binom{S(n_t-1)}{t-s_{n_t-1}}\!\right),$$だから引き算する$\left(!\binom{S(n_t)}{t}!\right)$最後の座標が次の場所にあるすべての構成を削除します$[0,s_{n_t-1}]$。 残り$N^{(t-1)}$したがって、厳密に小さいインデックスのみを使用して表現できます。$n_t$。 

同じ構築を繰り返すと、$n_{t-1}\le n_t$そのような$$N^{(t-1)}=\left(\!\binom{S(n_{t-1})}{t-1}\!\right)+N^{(t-2)},$$そして継続的な収量$$N=
\left(\!\binom{S(n_t)}{t}\!\right)+
\left(\!\binom{S(n_{t-1})}{t-1}\!\right)+\cdots+
\left(\!\binom{S(n_1)}{1}\!\right),$$と$n_t\ge n_{t-1}\ge\cdots\ge n_1\ge 0$そしてそれぞれ$n_i$許可されたセットから抽出される${s_0\cdot 0,s_1\cdot 1,\dots}$各減算ステップでは、畳み込み定義のサポートと互換性のあるインデックスのみが許可されるためです。$S(\cdot,\cdot)$。 

### 独自性

 2 つの表現が存在すると仮定します。$$N=\sum_{i=1}^t \left(\!\binom{S(n_i)}{i}\!\right)
=\sum_{i=1}^t \left(\!\binom{S(m_i)}{i}\!\right),
\qquad
n_t\ge\cdots\ge n_1,\; m_t\ge\cdots\ge m_1.$$させて$r$次のような最大のインデックスになります$n_r\ne m_r$。 一般性を失わずに$n_r>m_r$。 次に単調性$n$与える$$\left(\!\binom{S(n_r)}{r}\!\right)\ge \left(\!\binom{S(m_r+1)}{r}\!\right)>\left(\!\binom{S(m_r)}{r}\!\right).$$すべての高インデックス用語$i>r$プレフィックスの等価性によってキャンセルされるため、左側が右側を超え、次の等価性と矛盾します。$N$。 これにより、$n_r=m_r$みんなのために$r$、独自性を証明します。 

これで表現定理が完成しました。 ∎

 ### の式$|\partial P_{N_t}|$系 C では、境界演算子$\partial$は、表現によってエンコードされた組み合わせ構造内の座標を 1 つだけ減らすことによって機能します。 各学期$$\left(\!\binom{S(n_i)}{i}\!\right)$$の 1 つを減らす方法の数に正確に貢献します。$i$選択された単位。これは、次のいずれかの選択に相当します。$i$その用語に貢献する立場。 このようなポジションを減らすと、カウントされる貢献度が変換されます。$S(n_i,i)$で数えて 1 つに$S(n_i,i-1)$。 

すべてのレベルを合計すると次の結果が得られます$$|\partial P_{N_t}|
=
\sum_{i=1}^t \left(\!\binom{S(n_i)}{i-1}\!\right),$$大会とともに$\left(!\binom{S(n_i)}{0}!\right)=1$。 

境界はレベル間で一意に分解されます。$N$は一意であり、各リダクションは異なる加数間で重複することなく 1 つの加数にのみ影響します。$i$。 ∎
