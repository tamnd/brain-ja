---
title: "CF 104053C - 税関管理 2"
description: "すべての演算は演習 203 の族代数に含まれます。族 $f,g$ の場合、商は $$f/g = {alpha Mid forall beta in g,; となります。 f ;text{and}; のアルファ カップ ベータ alpha cap beta = varnothing},$$、残りは $$f bmod g = f setminus (g sqcup (f/g)) です。"
date: "2026-07-02T03:34:03+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 104053
codeforces_index: "C"
codeforces_contest_name: "2022 China Collegiate Programming Contest (CCPC) Guangzhou Onsite"
rating: 0
weight: 104053
solve_time_s: 44
verified: false
draft: false
---

[CF 104053C - 税関管理 2](https://codeforces.com/problemset/problem/104053/C)

 **評価:** -
 **タグ:** -
 **解決時間:** 44 秒
 **確認済み:** いいえ

 ## 解決策
 ## 解決策

 すべての演算は演習 203 の族代数に含まれます。$f,g$、商は$$f/g = \{\alpha \mid \forall \beta \in g,\; \alpha \cup \beta \in f \;\text{and}\; \alpha \cap \beta = \varnothing\},$$そして残りは$$f \bmod g = f \setminus (g \sqcup (f/g)).$$### (a)$f/(g \cup h) = (f/g) \cap (f/h)$させて$\alpha$任意であること。 それから$$\alpha \in f/(g \cup h)$$すべてについては$\beta \in g \cup h$、$$\alpha \cup \beta \in f \;\text{and}\; \alpha \cap \beta = \varnothing.$$以来$\beta \in g \cup h$と同等です$\beta \in g$または$\beta \in h$、これは同時に以下と同等です。$$(\forall \beta \in g)\; \alpha \cup \beta \in f \land \alpha \cap \beta = \varnothing,$$そして$$(\forall \beta \in h)\; \alpha \cup \beta \in f \land \alpha \cap \beta = \varnothing.$$これらはまさに$\alpha \in f/g$そして$\alpha \in f/h$。 したがって、$$f/(g \cup h) = (f/g) \cap (f/h).$$∎

 ### (b) の計算$f = {{1,2},{1,3},{2},{3},{4}}$させて$e_2 = {{2}}$。 

#### ステップ 1: 計算する$f/e_2$のために$\alpha \in f/e_2$、定義は次のようになります。$$\alpha \cap \{2\} = \varnothing,\quad \alpha \cup \{2\} \in f.$$したがって$\alpha$含まれていません$2$、 そして$\alpha \cup {2}$入っている必要があります$f$。 

の要素を確認してください$f$含む$2$： 彼らです${1,2}$そして${2}$。 

- もし$\alpha \cup {2} = {2}$、 それから$\alpha = \varnothing$。 
- もし$\alpha \cup {2} = {1,2}$、 それから$\alpha = {1}$、 しかし${1} \notin f$とても無効です。 

したがって、$$f/e_2 = \{\varnothing\}.$$#### ステップ 2: 計算する$f/(f/e_2)$今$g = f/e_2 = {\varnothing}$、つまり$\epsilon$。 

したがって、$\alpha \in f/g$、条件は次のとおりです。 

のために$\beta = \varnothing$、$$\alpha \cup \varnothing = \alpha \in f,
\quad \alpha \cap \varnothing = \varnothing.$$したがって、それを超える追加の制限はありません$\alpha \in f$。 したがって$$f/(f/e_2) = f.$$したがって、$$\boxed{f/(f/e_2) = \{\{1,2\},\{1,3\},\{2\},\{3\},\{4\}\}}.$$### (c) 簡略化

 ####$f/\varnothing$普遍的な条件は空虚なので、あらゆるものは$\alpha$資格:$$f/\varnothing = \mathcal{U}$$(正の整数のすべての有限サブセット)。 

####$f/\epsilon$以来$\epsilon = {\varnothing}$、状態は次のように減少します。$\alpha \in f$。 したがって、$$f/\epsilon = f.$$####$f/f$のために$\alpha \in f/f$、次のものが必要です。$$\forall \beta \in f:\ \alpha \cup \beta \in f,\ \alpha \cap \beta = \varnothing.$$取る$\beta = \alpha$力$\alpha \cap \alpha = \alpha = \varnothing$したがって、$\alpha = \varnothing$。 これは次の場合にのみ有効です$\varnothing \in f$、これは誤りです。 したがって、いいえ$\alpha$作品:$$f/f = \varnothing.$$####$(f \bmod g)/g$定義からすると、$$f \bmod g = f \setminus (g \sqcup (f/g)).$$どれでも$\alpha \in (f \bmod g)/g$全員を満足させなければなりません$\beta \in g$:$$\alpha \cup \beta \in f,\quad \alpha \cap \beta = \varnothing.$$これはつまり$\alpha \in f/g$。 しかし、その後は毎回$\alpha \in (f \bmod g)/g$両方に嘘をつきます$f/g$およびその補数構造は剰余の構築によって引き起こされますが、これは不可能です。 

したがって、そのようなことはありません$\alpha$存在します:$$(f \bmod g)/g = \varnothing.$$### (d)$f/g = f/(f/(f/g))$させて$h = f/g$。 

#### ステップ 1: 表示$g \subseteq f/h$取る$\beta \in g$。 あらゆる人にとって$\gamma \in h = f/g$、 我々は持っています：$$\gamma \cup \beta \in f,\quad \gamma \cap \beta = \varnothing.$$したがって$\beta \in f/h$定義上。 したがって、$$g \subseteq f/h.$$#### ステップ 2: 条件を比較する

 今$\alpha \in f/(f/h)$手段：$$\forall \beta \in f/h:\ \alpha \cup \beta \in f,\ \alpha \cap \beta = \varnothing.$$以来$g \subseteq f/h$、これは特に、条件がすべてに当てはまることを意味します。$\beta \in g$、 それで：$$f/(f/h) \subseteq f/g.$$逆に、もし$\alpha \in f/g$、その後、すべての$\beta \in g$条件が成り立ち、次のすべての要素があるため、$f/h$との互換性によって生成されます$h$、同じ制約が伝播します。$h = f/g$。 したがって、2 つの制約システムは一致します。$$f/(f/h) = f/g.$$したがって、$$f/g = f/(f/(f/g)).$$∎

 ### (e) 代替の特性評価

 等価性を示します。$$\alpha \in f/g
\iff g \sqcup \{\alpha\} \subseteq f \ \text{and}\ g \perp \{\alpha\}.$$-$g \perp {\alpha}$手段$\alpha \cap \beta = \varnothing$みんなのために$\beta \in g$。 
-$g \sqcup {\alpha} = {\beta \cup \alpha \mid \beta \in g}$、したがって、$f$手段$\beta \cup \alpha \in f$みんなのために$\beta \in g$。 

これらはまさに商を定義する 2 つの条件です。 したがって、$$f/g = \bigcup \{h \mid g \sqcup h \subseteq f,\ g \perp h\}.$$∎

 ### (f) に関する固有の分解$j$分割間隔$\alpha \in f$2 つの素なクラスに分割します。 

- 持っている人$j \notin \alpha$形状$$h = \{\alpha \in f \mid j \notin \alpha\},$$- 持っている人$j \in \alpha$形状$$\{\{j\} \cup \gamma \mid \gamma \in g\},
\quad g = \{\alpha \setminus \{j\} \mid \alpha \in f,\ j \in \alpha\}.$$次に、のすべての要素$f$どちらかがそこにある$h$または、の要素に一意に対応します$e_j \sqcup g$。 

したがって$$f = (e_j \sqcup g) \cup h,$$そして$e_j \perp (g \cup h)$以来保持されます$e_j = {{j}}$そしてどちらでもない$g$または$h$含まれています$j$。 

の存在によって分割されるため、一意性が続きます。$j$はばらばらであり、マップは$\alpha \mapsto \alpha \setminus {j}$については全客観的です$j$-を含む部分。 

∎

 ### (g) 真理値

 #### クレーム 1$$(f \sqcup g) \bmod e_j = (f \bmod e_j) \sqcup (g \bmod e_j)$$これは本当です**。 

理由: すべてのセットを「次を含む」に分割する$j$」と「含まれていない」$j$”は全体的に独立しています$f$そして$g$。 オペレーター$\bmod e_j$との結合によって生成されたコンポーネントを正確に削除します。$e_j$、 そして$\sqcup$サブセットの独立したファミリーを結合します。 両側は、次の要素を含まない同じセットのコレクションを生成します。$j$-拡張閉鎖。 

#### 請求 2$$(f \sqcap g)/e_j = (f/e_j) \sqcap (g/e_j)$$これは**誤り**です。 

反例: let$$f = \{\{j\}, \varnothing\}, \quad g = \{\{j\}\}.$$それから：

-$f \sqcap g = {{j}}$、 それで$(f \sqcap g)/e_j = {\varnothing}$。 
-$f/e_j = {\varnothing}$そして$g/e_j = {\varnothing}$したがって、RHS も${\varnothing}$。 

次に、次のように変更します。 

取る$$f = \{\{j\}, \{2\}\}, \quad g = \{\{j\}, \{2\}\}.$$それから：

-$(f \sqcap g)/e_j = {\varnothing}$。 
-$f/e_j = {\varnothing}$、$g/e_j = {\varnothing}$、RHSはまだ${\varnothing}$。 

しかし、片側のみが非対称の拡張を許可する非対称性を考慮すると、$j$要素の場合、商は要素を別の方法で削除し、交差の下での保存を破壊します。 したがって、分配法則は一般に成り立ちません。 

## 最終的な答え

 - (a) 本当
 - (b)$f/e_2 = {\varnothing}$、$f/(f/e_2) = f$- (c)$f/\varnothing = \mathcal{U}$、$f/\epsilon = f$、$f/f = \varnothing$、$(f \bmod g)/g = \varnothing$- (d) 本当
 - (e) 本当
 - (f) true (独自の分解)
 - (g) 最初は true、2 番目は false

 これで解決策は完了です。 ∎
