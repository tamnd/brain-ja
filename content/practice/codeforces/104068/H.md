---
title: "CF 104068H - トクセル\u4e0e\u5b9d\u53ef\u68a6\u5bf9\u6218\u7279\u8bad"
description: "$Gamma = (alpha0,ldots,alpha{t'-1})$、$Gamma' = (alpha'0,ldots,alpha'{t'-1})$、$Gamma'' = (alpha''0,ldots,alpha''{t''-1})$ とします。"
date: "2026-07-02T03:05:53+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 104068
codeforces_index: "H"
codeforces_contest_name: "The 17-th Beihang University Collegiate Programming Contest (BCPC 2022) - Preliminary"
rating: 0
weight: 104068
solve_time_s: 94
verified: false
draft: false
---

[CF 104068H - トクセル \u4e0e\u5b9d\u53ef\u68a6\u5bf9\u6218\u7279\u8bad](https://codeforces.com/problemset/problem/104068/H)

 **評価:** -
 **タグ:** -
 **解決時間:** 1 分 34 秒
 **確認済み:** いいえ

 ## 解決策
 ## 解決策

 しましょう$\Gamma = (\alpha_0,\ldots,\alpha_{t-1})$、$\Gamma' = (\alpha'_0,\ldots,\alpha'_{t'-1})$、 そして$\Gamma'' = (\alpha''_0,\ldots,\alpha''_{t''-1})$。 ボストロフェドン製品$\Gamma ,\≀, \Gamma'$すべての連結のシーケンスを形成します$\alpha_i\alpha'_j$どこ$0 \le i < t$そして$0 \le j < t'$、スイープで注文$j$左から右の場合$i$が偶数で右から左の場合$i$奇数です。 

積の構造は、(5) のグレイ シーケンスの標準的な再帰的構築と同一であり、この観察により証明戦略が決定されます。 重要な事実は、あらゆる人にとって、$n \ge 1$、グレーシーケンス$\Gamma_n$満足する$$\Gamma_n = (0,1) \,\≀\, \Gamma_{n-1}.$$このアイデンティティにより、次の動作が修正されます。$\Gamma_n$(5) は次からシーケンス全体を再帰的に決定するため、一意です。$\Gamma_0 = \epsilon$。 

二項演算を定義する$\star$シーケンスについて$\Gamma \star \Gamma' = \Gamma ,\≀, \Gamma'$。 まず、すべての場合にそれを示します$m,n \ge 0$、シーケンス$\Gamma_m \star \Gamma_n$と同じ再帰定義を満たします。$\Gamma_{m+n}$。 

ケース$m=0$収量$\Gamma_0 \star \Gamma_n = (\epsilon) \star \Gamma_n = \Gamma_n$、一致します$\Gamma_{0+n}$。 仮定する$\Gamma_m \star \Gamma_n = \Gamma_{m+n}$一定期間保持される$m$。 グレイコードの再帰定義を使用すると、$\Gamma_{m+1} = (0,1) \star \Gamma_m$。 したがって$$\Gamma_{m+1} \star \Gamma_n
= ((0,1) \star \Gamma_m) \star \Gamma_n.$$操作の場合$\star$結合性がある、これは等しい$$(0,1) \star (\Gamma_m \star \Gamma_n)
= (0,1) \star \Gamma_{m+n}
= \Gamma_{m+n+1}.$$したがって、結合性は合成規則を意味します$\Gamma_{m+n} = \Gamma_m \star \Gamma_n$。 

定義された順序付けルールから直接結合性を検証する必要があります。 生み出されるあらゆる要素は、$\Gamma \star \Gamma'$ペアによって一意に決定されます$(i,j)$、シーケンス内のその位置は、$i$偶数または奇数です。 書き込み$\Gamma \star \Gamma'$ペアでインデックス付けされたシーケンスとして$(i,j)$、ペアの相対的な順序は辞書編集的です。$i$、それぞれの順序は固定されていますが、$i$増加または減少しています$j$のパリティに従って$i$。 との連結$\Gamma''$同じルールを再度ペアのインデックス セットに適用します。$(i,j)$。 

で$(\Gamma \star \Gamma') \star \Gamma''$、各トリプル$(i,j,k)$ペアで最初に注文されます$(i,j)$ボストロフェドン規則に従い、そのような各ペア内で次のようにします。$k$の位置のパリティに応じて増加または減少します。$(i,j)$で$\Gamma \star \Gamma'$。 で$\Gamma \star (\Gamma' \star \Gamma'')$、同じトリプル$(i,j,k)$最初に注文されるのは$i$、その後、$(j,k)$各ブロック内で、反転はパリティによってのみ制御されます。$i$そして$j$。 

重要な点は、次の位置が同等であるということです。$(i,j)$で$\Gamma \star \Gamma'$のみに依存する$i$そして$j$固定線形規則モジュロを介して$2$、シーケンスがどのように括弧で囲まれているかとは関係ありません。 したがって、次のことを決定するルールは、$k$-順序付けは順方向か逆方向かにのみ依存します$(i,j)$グループ分けではありません。 どちらの構造でもすべてのトリプルで同じ順序が生成されるため、$(i,j,k)$、結果のシーケンスは一致します。 

したがって$$(\Gamma \star \Gamma') \star \Gamma'' = \Gamma \star (\Gamma' \star \Gamma'').$$これで証明は完了です。 ∎
