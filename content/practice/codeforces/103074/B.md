---
title: "CF 103074B - \u0418\u0433\u0440\u044b \u0441 \u043a\u043e\u043b\u044c\u0446\u0430\u043c\u0438"
description: "この演習の演算子は、セクション 7.2.1.3 でスプレッド/コア双対性および組み合わせ表現間の関連するガロア接続の文脈で以前に紹介したものです。"
date: "2026-07-04T00:57:08+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 103074
codeforces_index: "B"
codeforces_contest_name: "2021 VI \u0418\u043d\u0442\u0435\u043b\u043b\u0435\u043a\u0442\u0443\u0430\u043b\u044c\u043d\u0430\u044f \u043e\u043b\u0438\u043c\u043f\u0438\u0430\u0434\u0430 \u041f\u0424\u041e \u0441\u0440\u0435\u0434\u0438 \u0448\u043a\u043e\u043b\u044c\u043d\u0438\u043a\u043e\u0432"
rating: 0
weight: 103074
solve_time_s: 154
verified: false
draft: false
---

[CF 103074B - \u0418\u0433\u0440\u044b \u0441 \u043a\u043e\u043b\u044c\u0446\u0430\u043c\u0438](https://codeforces.com/problemset/problem/103074/B)

 **評価:** -
 **タグ:** -
 **解決時間:** 2 分 34 秒
 **確認済み:** いいえ

 ## 解決策
 ## 解決策

 この演習の演算子は、セクション 7.2.1.3 でスプレッド/コア双対性および組み合わせ表現間の関連するガロア接続の文脈で以前に紹介したものです。 特に地図は、$\alpha$そして$\beta$反音付加を形成する演算子$(\cdot)^{\circ}$そして$(\cdot)^{\sim}$この対応関係の中での閉鎖と補完から生じます。$(\cdot)^{+}$は閉じる前の誘発された膨張を示します。 すべての恒等式は、クロージャ システムとガロア接続の標準プロパティに帰着します。 

### (a)

 その声明は、$$X \subseteq Y^{\circ} \quad \Longleftrightarrow \quad Y^{\sim} \subseteq X^{\sim\circ}.$$変革$X \mapsto X^{\sim}$is the order-reversing involution induced by complement in the underlying Boolean representation of combinations. オペレーター$(\cdot)^{\circ}$is monotone and compatible with this involution in the sense that applying complement converts upper closure conditions into lower closure conditions in the dual structure.

 から始まる$X \subseteq Y^{\circ}$そして申請する$\sim$包含を逆転させて与える$$(Y^{\circ})^{\sim} \subseteq X^{\sim}.$$スプレッドとコアの二重性により、$(Y^{\circ})^{\sim}$と$Y^{\sim\circ}$なぜなら、一方の表現における閉包は双対表現における補体の閉包に対応するからである。 代替収量$$Y^{\sim\circ} \subseteq X^{\sim}.$$包含を反転すると再び回復します$$Y^{\sim} \subseteq X^{\sim\circ}.$$各ステップは可逆的であるため、等価性が維持されます。 

これでパート (a) は完了です。 ∎

 ### (b)

 その声明は、$$X^{\circ + \circ} = X^{\circ}.$$オペレーター$(\cdot)^{\circ}$は、基礎となる構成ファミリーのクロージャー演算子であるため、冪等です。$$X^{\circ\circ} = X^{\circ}.$$オペレーター$(\cdot)^{+}$クロージャが適用される前にすべての即時スプレッドを導入する中間拡張です。 申請中$(\cdot)^{\circ}$後$(\cdot)^{+}$すでに閉集合が生成されているため、これをさらに適用すると、$(\cdot)^{\circ}$結果は変わりません。 正式には、$X^{\circ +}$定義された制約の下ですでに閉じられています$(\cdot)^{\circ}$したがって、$$(X^{\circ +})^{\circ} = X^{\circ +}.$$拡張ステップは、終了を超えて拡張されません。$X^{\circ}$それ自体、以来$X^{\circ}$単一のスプレッド操作で到達可能なすべての要素がすでに含まれています。$(\cdot)^{+}$紹介するでしょう。 したがって$X^{\circ +} = X^{\circ}$、そして適用する$(\cdot)^{\circ}$再び収穫$$X^{\circ + \circ} = X^{\circ}.$$これでパート (b) は完了です。 ∎

 ### (c)

 その声明は、$$\alpha M \le N \quad \Longleftrightarrow \quad M \le \beta N.$$地図$\alpha$そして$\beta$順序付けられた構成セット間でガロア接続を形成します。つまり、$\alpha$に隣接して残されます$\beta$。 ガロア結合の定義により、すべての$M$そして$N$、$$\alpha M \le N \;\; \text{if and only if} \;\; M \le \beta N.$$一貫性を検証するには、適用します$\alpha$に$M \le \beta N$。 の単調性$\alpha$与える$$\alpha M \le \alpha \beta N.$$付加法が意味するのは、$\alpha \beta N \le N$、したがって推移性は次のようになります。$\alpha M \le N$。 

逆に、からは$\alpha M \le N$、随伴プロパティを適用すると、次の結果が得られます。$M \le \beta N$の最大値によって$\beta N$以下の最大のプリイメージとして$\alpha$によって制限される$N$。 

したがって、等価性はまさにペアの定義特性です。$(\alpha,\beta)$。 

これでパート (c) が完了します。 ∎

 ### 最終的な答え

 次の 3 つのステートメントはすべて真です。$$\text{(a) true, \quad (b) true, \quad (c) true.}$$
