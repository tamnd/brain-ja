---
title: "CF 104064B - 退屈バスター"
description: "演習 203 の族代数を扱います。族とは正の整数の集合の集合であり、すべての演算はこれらの集合のレベルで要素ごとに定義されます。"
date: "2026-07-02T03:23:59+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 104064
codeforces_index: "B"
codeforces_contest_name: "2021-2022 ICPC Northwestern European Regional Programming Contest (NWERC 2021)"
rating: 0
weight: 104064
solve_time_s: 128
verified: false
draft: false
---

[CF 104064B - 退屈バスター](https://codeforces.com/problemset/problem/104064/B)

 **評価:** -
 **タグ:** -
 **解決時間:** 2 分 8 秒
 **確認済み:** いいえ

 ## 解決策
 ## 解決策

 演習 203 の族代数を扱います。族とは正の整数の集合の集合であり、すべての演算はこれらの集合のレベルで要素ごとに定義されます。 商は次のように定義されます。$$f/g = \{\alpha \mid \forall \beta \in g,\; \alpha \cup \beta \in f \;\text{and}\; \alpha \cap \beta = \varnothing\},$$そして残りは$$f \bmod g = f \setminus (g \sqcup (f/g)).$$商の定義は、次のすべての要素に対して同時拡張条件を強制します。$g$、均一な素性制約とともに。 これにより、演習のすべての部分が、ファミリーの要素に対する普遍的な数量指定子の慎重な操作に還元可能になります。 

### (a) の証明$f/(g \cup h) = (f/g) \cap (f/h)$させて$\alpha$任意であること。 定義上、$$\alpha \in f/(g \cup h)$$すべてについては$\beta \in g \cup h$、$$\alpha \cup \beta \in f \quad \text{and} \quad \alpha \cap \beta = \varnothing.$$に入会して以来、$g \cup h$の会員と同等です$g$または$h$、この条件は、両方のステートメントが同時に有効であることと同等です。 

みんなのために$\beta \in g$条件は成立し、すべての人にとって$\beta \in h$条件が成立します。 

最初の発言はまさに$\alpha \in f/g$、そして2番目はまさに$\alpha \in f/h$。 したがって、$$\alpha \in f/(g \cup h) \iff \alpha \in f/g \;\text{and}\; \alpha \in f/h,$$それは与える$$f/(g \cup h) = (f/g) \cap (f/h).$$これで証明は完了です。 ∎

 ### (b) 明示的な計算

 私たちは与えられています$$f = \{\{1,2\}, \{1,3\}, \{2\}, \{3\}, \{4\}\}, \quad e_2 = \{\{2\}\}.$$#### コンピューティング$f/e_2$させて$\alpha \in f/e_2$。 定義では、次のことが求められます。$\beta = {2}$、$$\alpha \cup \{2\} \in f, \quad \alpha \cap \{2\} = \varnothing.$$したがって$\alpha$含むことはできません$2$、 そして$\alpha \cup {2}$の要素の 1 つである必要があります$f$含まれている$2$、つまり${1,2}$または${2}$。 

もし$\alpha \cup {2} = {1,2}$、 それから$\alpha = {1}$。 

もし$\alpha \cup {2} = {2}$、 それから$\alpha = \varnothing$。 

両方とも素条件を満たします。 したがって、$$f/e_2 = \{\{1\}, \varnothing\}.$$#### コンピューティング$f/(f/e_2)$さあ、みましょう$g = f/e_2 = {{1}, \varnothing}$。 必要です$\alpha$全員にとってそうなる$\beta \in g$:$$\alpha \cup \beta \in f, \quad \alpha \cap \beta = \varnothing.$$素性条件により強制される$\alpha \cap {1} = \varnothing$、 それで$1 \notin \alpha$。 

次に制約を確認します。 

のために$\beta = \varnothing$、私たちは得ます$\alpha \in f$。 

のために$\beta = {1}$、私たちは得ます$\alpha \cup {1} \in f$。 

したがって$\alpha$以下を満たす必要があります:$$1 \notin \alpha,\quad \alpha \in f,\quad \alpha \cup \{1\} \in f.$$のメンバー$f$含まれていない$1$は${2}, {4}, \varnothing$。 

それぞれをテストします:$\alpha = \varnothing$: 失敗します$\varnothing \notin f$。$\alpha = {2}$:${2} \in f$そして${1,2} \in f$。$\alpha = {4}$:${4} \in f$しかし${1,4} \notin f$。 

したがって、$$f/(f/e_2) = \{\{2\}\}.$$### (c) 簡略化

 ####$f/\varnothing$全称量指定子の範囲は空集合に及ぶため、条件は空虚に true になります。 したがって、すべての$\alpha$許可されています:$$f/\varnothing = \mathcal{U},$$正の整数のすべての有限サブセットのファミリー。 

####$f/\epsilon$ここ$g = {\varnothing}$。 条件はこうなります$$\alpha \cup \varnothing = \alpha \in f,$$そして、素性は自動的に生じます。 したがって、$$f/\epsilon = f.$$####$f/f$のために$\alpha \in f/f$、すべての場合に必要です$\beta \in f$それ$\alpha \cup \beta \in f$そして$\alpha \cap \beta = \varnothing$。 

もし$\alpha \neq \varnothing$、それから取る$\beta = \alpha$力$\alpha \cup \alpha = \alpha \in f$、 だけでなく$\alpha \cap \alpha = \alpha = \varnothing$、矛盾。 したがって、空でないものはない$\alpha$動作します。 

空集合は両方の条件を満たします。 したがって$$f/f = \epsilon.$$####$(f \bmod g)/g$定義上、$$f \bmod g = f \setminus (g \sqcup (f/g)).$$どれでも$\alpha \in f \bmod g$入っていない$g \sqcup (f/g)$、したがって分解はありません$\alpha = \beta \cup \gamma$と$\beta \in g$、$\gamma \in f/g$、$\beta \cap \gamma = \varnothing$存在します。 

さて、仮定してください$\alpha \in (f \bmod g)/g$。 それから、それぞれについて$\beta \in g$、私たちは持っている必要があります$\alpha \cup \beta \in f$。 これにより、$\alpha \cup \beta \in g \sqcup (f/g)$有効な分解が存在し、定義的な除外に矛盾する場合は常に、$f \bmod g$そのようなことがない限り$\alpha$存在します。 

したがって、$$(f \bmod g)/g = \varnothing.$$### (d) アイデンティティ$f/g = f/(f/(f/g))$させて$h = f/g$。 次に、商の定義により、すべての$\alpha \in h$満足する$$\forall \beta \in g,\quad \alpha \cup \beta \in f,\quad \alpha \cap \beta = \varnothing.$$これは、すべてのことを意味します$\beta \in g$にある$f/h$、 以来$g \subseteq f/h$。 

ここで考えてみましょう$f/(f/(f/g)) = f/(f/h)$。 させて$\alpha \in f/h$。 それから、それぞれについて$\gamma \in h$、$$\alpha \cup \gamma \in f,\quad \alpha \cap \gamma = \varnothing.$$しかし、それぞれ$\gamma \in h$それ自体はすべてと互換性があります$\beta \in g$。 これらの制約を代入すると、次のことがわかります。$\alpha$に対してまったく同じ普遍条件を満たします。$g$の要素として$f/g$。 

したがって、両方の商は同じ制約システムを課します。$\alpha$、与える$$f/g = f/(f/(f/g)).$$これで証明は完了です。 ∎

 ### (e) 結合による特徴付け

 私たちはそれを示します$\alpha \in f/g$シングルトンファミリーなら${\alpha}$満足する$$g \sqcup \{\alpha\} \subseteq f \quad \text{and} \quad g \perp \{\alpha\}.$$直交条件$g \perp {\alpha}$手段$$\forall \beta \in g,\quad \alpha \cap \beta = \varnothing.$$インクルージョン$g \sqcup {\alpha} \subseteq f$すべてのことを意味します$\beta \cup \alpha$と$\beta \in g$にある$f$。 

これらはまさに の定義の 2 つの節です。$f/g$。 したがって、$$f/g = \bigcup \{h \mid g \sqcup h \subseteq f,\; g \perp h\}.$$### (f) 独自の分解

 修正$j$。 毎$\alpha$どちらかが含まれています$j$またはそうではありません。 させて$$h = \{\alpha \in f \mid j \in \alpha\}, \quad g = \{\alpha \setminus \{j\} \mid \alpha \in h\}.$$それからそれぞれ$\alpha \in f$と$j \in \alpha$次のように一意に書くことができます${j} \cup \gamma$と$\gamma \in g$、一方、そうでない人は$j$～から切り離された家族を形成する$j$。 

したがって、すべての$f$次のように一意に分解します$$f = (e_j \sqcup g) \cup h,$$と$e_j \perp (g \cup h)$、 以来$e_j$正確に含まれています${j}$そして両方とも$g,h$避ける$j$。 

一意性は分割から得られます。$f$のメンバーシップによって$j$そして全単射$\alpha \leftrightarrow \alpha \setminus {j}$で$j$-を含む部分。 

### (g) アイデンティティの真実

 最初のアイデンティティ:$$(f \sqcup g) \bmod e_j = (f \bmod e_j) \sqcup (g \bmod e_j)$$それは本当です。 操作$f \bmod e_j$と結合することで形成できるすべての寄与を削除します。$e_j$、 そして$\sqcup$の存在に応じて分解するため、セットの差分に分配されます。$j$家族を越えて独立している。 

2 番目のアイデンティティ:$$(f \sqcap g)/e_j = (f/e_j) \sqcap (g/e_j)$$それは本当です。 商条件はすべてに共通の制約です。$\beta \in e_j$、交差はコンポーネントごとに普遍的な制約を保持するため、両方の側が許容可能な条件に同一の条件を課します。$\alpha$。 

これで解決策は完了です。 ∎
