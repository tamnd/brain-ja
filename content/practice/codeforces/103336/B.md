---
title: "CF 103336B - フェスティバル デ プレゼント"
description: "$rs,dots,r0$ が $$t = rs + cdots + r1 + r0,qquad 0 le rj le mjquad (s ge j ge 0).$$ を満たすようにします。$$Mj = sum{i=0}^j mi,qquad Tj = t - sum{i=j+1}^s ri,$$ と書きます。したがって、$Tj$ はインデックス間で分配される残りの合計になります。 $rs,dots,r{j+1}$ を修正した後の $0,dots,j$。"
date: "2026-07-03T14:00:06+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 103336
codeforces_index: "B"
codeforces_contest_name: "OPEI 2021 - Senior"
rating: 0
weight: 103336
solve_time_s: 157
verified: false
draft: false
---

[CF 103336B - フェスティバル デ プレゼンツ](https://codeforces.com/problemset/problem/103336/B)

 **評価:** -
 **タグ:** -
 **解決時間:** 2 分 37 秒
 **確認済み:** いいえ

 ## 解決策
 ## 解決策

 しましょう$r_s,\dots,r_0$満足する$$t = r_s + \cdots + r_1 + r_0,\qquad 0 \le r_j \le m_j \quad (s \ge j \ge 0).$$書く$$M_j = \sum_{i=0}^j m_i,\qquad T_j = t - \sum_{i=j+1}^s r_i,$$それで$T_j$インデックス間で分配される残りの合計です。$0,\dots,j$修正後$r_s,\dots,r_{j+1}$。 

位置で$j$、の値$r_j$構成を完成させる実現可能性によって制約されます。 選択した後$r_j$、残りの値$T_{j-1} = T_j - r_j$満たさなければなりません$$0 \le T_{j-1} \le M_{j-1}.$$したがって、$$T_j - M_{j-1} \le r_j \le T_j,$$と共に$0 \le r_j \le m_j$。 したがって、許容される間隔は次のようになります。$$L_j = \max(0,\, T_j - M_{j-1}),\qquad U_j = \min(m_j,\, T_j).$$辞書編集上の順序$(r_s,\dots,r_0)$連れて行かれる$r_s$最も重要なので、$r_0$最も速く変化します。 

最初の解決策は、次の条件で各コンポーネントを実行可能な最小値で選択することによって得られます。$T_s = t$:$$r_j = L_j \quad (s \ge j \ge 0).$$### アルゴリズム B (境界のある構成)

 センチネル$M_{-1} = 0$、$r_{s+1} = 0$均一なインデックス付けに使用されます。 

**B1. [初期化します。]** 設定$r_j \leftarrow 0$のために$0 \le j \le s$。 セット$r_{s+1} \leftarrow 0$。 セット$T \leftarrow t$。 コンピューティング$M_j = \sum_{i=0}^j m_i$のために$0 \le j \le s$。 

のために$j$から$s$まで$0$、 セット$$r_j \leftarrow \max(0,\, T - M_{j-1}),$$それから更新します$T \leftarrow T - r_j$。 

**B2. [訪問]** 訪問$(r_s,\dots,r_0)$。 

**B3. [探す$j$。]** セット$j \leftarrow 0$。 その間$j \le s$そして$$r_j = U_j,$$セット$j \leftarrow j+1$。 

**B4。 [完了?]** の場合$j > s$、終了します。 

**B5。 [増加$r_j$。]** セット$T \leftarrow T + r_j$。 交換する$r_j \leftarrow r_j + 1$。 それから$k = j-1, j-2, \dots, 0$、 セット$$r_k \leftarrow L_k(T),$$どこ$L_k(T) = \max(0,, T - M_{k-1})$現在の残りの合計で計算されます$T$、更新します$T \leftarrow T - r_k$。 B2に戻ります。 

正確さは不変条件から生まれる$T_j$そして実現可能性の限界。 各ステップ B3 で、インデックス$0,\dots,j-1$は実現可能な最大値にあるため、$r_j$辞書編集上の接尾辞の最小性を保持します。 ステップ B5 は、更新された残りの合計の下で実行可能な最小限の完了を復元し、次の辞書編集構成が生成されることを保証します。 枯渇はインデックスを増やすことができないときに発生します。これは次の場合と同等です。$r_j = U_j$みんなのために$j$したがって、すべての境界付きコンポジションは 1 回だけ生成されます。 

これで証明は完了です。 ∎
