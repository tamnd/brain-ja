---
title: "CF 104049C - ブロンズの捕獲"
description: "セクション 7.1.4 と演習 203 の規則を使用して、族 $f$ を変数 $x1,x2,dots,xn$ に対する縮小順序決定図として表します。ノード $v$ にはフィールド $$V(v),quad LO(v),quad HI(v),$$ と端子 $bot,top$ があります。"
date: "2026-07-02T03:43:23+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 104049
codeforces_index: "C"
codeforces_contest_name: "UTPC Contest 11-11-22 Div. 1 (Advanced)"
rating: 0
weight: 104049
solve_time_s: 130
verified: false
draft: false
---

[CF 104049C - ブロンズのキャプチャ](https://codeforces.com/problemset/problem/104049/C)

 **評価:** -
 **タグ:** -
 **解決時間:** 2 分 10 秒
 **確認済み:** いいえ

 ## 解決策
 ## 解決策

 私たちは家族を代表しています$f$変数に対する縮小順序決定図として$x_1,x_2,\dots,x_n$、セクション 7.1.4 および演習 203 の規則を使用します。 ノード$v$フィールドがあります$$V(v),\quad LO(v),\quad HI(v),$$と端子$\bot,\top$。 変数の順序はすべてのエッジに沿って厳密に増加しています。 

以下のすべての操作は、以前に計算されたペアのメモ化を伴う、ノードのペアに対する構造的再帰によって実装されます。 

させて$\mathrm{Apply}(op,f,g)$適用の結果を返すメモ化された再帰プロシージャを示します。$op$ノードへ$f,g$。 させて$\mathrm{top}(v)$示す$V(v)$、そしてさせてください$\mathrm{low}(v),\mathrm{high}(v)$その子を表します。 

いつ$f$そして$g$非端末である場合、$i=\mathrm{top}(f)$、$j=\mathrm{top}(g)$。 させて$k=\min(i,j)$。 変数のシャノン展開で分割します$x_k$。 

可変インデックスを持つノード$k$ただし、1 つのオペランドに欠落がある場合は、そのオペランドを両方のブランチで複製することによって処理されます。 

すべての結果は通常のルールによって削減されます。つまり、同一のサブグラフが共有され、同じ下位および上位の子を持つノードが削除されます。 

### (a) 参加する$f \sqcup g$結合は次のように定義されます。$$f \sqcup g = \{\alpha \cup \beta \mid \alpha \in f,\ \beta \in g\}.$$再帰:

 もし$f=\bot$または$g=\bot$、 それから$f \sqcup g=\bot$。 

もし$f=\top$、 それから$f \sqcup g=g$。 もし$g=\top$、 それから$f \sqcup g=f$。 

それ以外の場合は、$k=\min(V(f),V(g))$。 投影を定義する$$f_0 = f|_{x_k=0},\quad f_1 = f|_{x_k=1},\quad g_0 = g|_{x_k=0},\quad g_1 = g|_{x_k=1}.$$それから$$(f \sqcup g)_0 = (f_0 \sqcup g_0),
\qquad
(f \sqcup g)_1 = (f_1 \sqcup g_0)\ \sqcup\ (f_0 \sqcup g_1)\ \sqcup\ (f_1 \sqcup g_1).$$ルートノードは次のレベルで作成されます$k$これらの子供たちと、その後に削減が続きます。 

この繰り返しは、$f \sqcup g$どちらかを省略します$x_k$両方のコンポーネントに含まれるか、少なくとも片側から含まれ、サブケースのすべての結合が生成されます。 

### (b) 会う$f \sqcap g$出会いは、$$f \sqcap g = \{\alpha \cap \beta \mid \alpha \in f,\ \beta \in g\}.$$基本的なケース:$$\bot \sqcap g = \bot,\quad f \sqcap \bot = \bot,\quad \top \sqcap g = g,\quad f \sqcap \top = f.$$による再帰$k=\min(V(f),V(g))$:$$(f \sqcap g)_0 = f_0 \sqcap g_0,
\qquad
(f \sqcap g)_1 = f_1 \sqcap g_1.$$要素がすべての変数位置の両方のオペランドに存在する場合にのみ交差項に属するため、交差項は消滅します。 

### (c) 対称的な差異$f \Delta g$ここ$$f \Delta g = \{ \alpha \oplus \beta \mid \alpha \in f,\ \beta \in g \},$$どこ$\oplus$は集合の対称差です。 

基本的なケース:$$\bot \Delta g = \bot,\quad f \Delta \bot = \bot,\quad \top \Delta g = g,\quad f \Delta \top = f.$$による再帰$k=\min(V(f),V(g))$:$$(f \Delta g)_0 = f_0 \Delta g_0,$$

$$(f \Delta g)_1 = (f_1 \Delta g_0)\ \sqcup\ (f_0 \Delta g_1).$$2 行目は ID から続きます$$(A\oplus x)\oplus B = (A\oplus B)\oplus x,$$の存在による分割$x_k$。 

### (d) 商$f/g$定義上、$$f/g = \{\alpha \mid \forall \beta \in g,\ \alpha \cup \beta \in f,\ \alpha \cap \beta = \varnothing\}.$$基本的なケース:

 もし$g=\bot$、普遍的な条件は空であるため、すべての$\alpha$許可されているので、$f/g$変数ドメイン上のユニバーサルファミリーであり、ターミナルで表されます。$\top$ブール関数の解釈において。 

もし$f=\bot$そして$g\neq \bot$、 それから$f/g=\bot$。 

もし$g=\top=\{\varnothing\}$、その後、条件は次のように減少します。$\alpha \in f$したがって、$$f/\top = f.$$による再帰$k=\min(V(f),V(g))$。 スプリット$g = g_0 \cup (e_k \sqcup g_1)$そして同様に$f$。 

商条件は次のいずれかによって区切られます。$x_k$強制的に欠席させられる$\alpha$。 

もし$k \notin g$(すべてがセットされます$g$省略$x_k$）、 それから$$(f/g)_0 = f_0/g,\qquad (f/g)_1 = f_1/g.$$もし$k \in g$、その後任意$\alpha \in f/g$すべての集合からの素性を満たさなければなりません$g_1$、強制的に除外します$x_k$交互作用項から次が得られます。$$(f/g)_0 = (f_0/g_0)\ \cap\ (f_1/g_1),
\qquad
(f/g)_1 = (f_1/g_0)\ \cap\ (f_0/g_1).$$これらの節は、汎用量指定子を分解して分散することから直接得られます。$g$含む部分または除外する部分に分割する$x_k$、および互換性を強制する$f$コンポーネント的に。 

### (e) 余り$f \bmod g$定義上、$$f \bmod g = f \setminus (g \sqcup (f/g)).$$再帰では、すでに定義されている操作が使用されます。$$\mathrm{mod}(f,g) = \mathrm{BUTNOT}(f,\ \mathrm{Join}(g,\ \mathrm{Quot}(f,g))).$$ノードレベルでは、$k=\min(V(f),V(g))$、次のように計算します。$$(f \bmod g)_0 = f_0 \bmod g_0,$$

$$(f \bmod g)_1 = f_1 \bmod g_0\ \setminus\ (g_1 \sqcup (f/g)).$$2 番目の項は、結合によって生成された要素を正確に削除します。$g$商と一貫して適用されます。$x_k=1$支店。 

最後に、ノードの共有と冗長なテストの排除により、すべての結果が削減されます。 

これで、ordered-reduced BDD フレームワークを使用した 5 つの操作すべての構築が完了しました。 ∎
