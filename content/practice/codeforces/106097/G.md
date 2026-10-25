---
title: "CF 106097G - 迫撃砲の騒乱"
description: "当初想定していた情報と実際の発言の間の不一致を解決する必要があるため、まだ正確な完全な社説を安全に書くことはできません。 コンテスト ページからわかるように、実際の問題は次のとおりです。各 $n$ ポイント (迫撃砲) は 2D グリッド上にあります。"
date: "2026-06-25T11:58:43+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 106097
codeforces_index: "G"
codeforces_contest_name: "UTPC Contest 10-1-25 Div. 1 (Advanced)"
rating: 0
weight: 106097
solve_time_s: 26
verified: false
draft: false
---

[CF 106097G - 迫撃砲の騒乱](https://codeforces.com/problemset/problem/106097/G)

 **評価:** -
 **タグ:** -
 **解決時間:** 26 秒
 **確認済み:** いいえ

 ## 解決策
 当初想定していた情報と実際の発言の間の不一致を解決する必要があるため、まだ正確な完全な社説を安全に書くことはできません。 

コンテスト ページからわかるように、実際の問題は次のとおりです。 

それぞれの$n$ポイント (モルタル) は 2D グリッド上にあります。 あらゆるモルタルに対して$i$、別の迫撃砲を選択する必要があります$j$マンハッタン距離としきい値を含む距離ルールに従って「ターゲット」であること$d$、最小距離と最小インデックスによるタイブレークを使用します。 意図した困難は、これを効率的に実行することから生まれます。$n \le 10^5$すべてのペアをチェックするわけではありません。 

まだ不明瞭なこと（そして正しい社説にとって重要なこと）は、正確なターゲティングルールです。なぜなら、私たちが持っている声明の断片は、どの候補者が有効であるか、そしてどのような「少なくとも距離のある最も近い敵」を定義する部分が不完全または曖昧であるためです。$d$” precisely means in combination with the sample behavior. The sample alone is not sufficient to reconstruct the intended condition reliably.

 To avoid hallucinating a wrong solution (which would completely break the editorial), I need one of the following:

 Codeforces からの完全なステートメント (特に迫撃砲が他の迫撃砲を有効な目標とみなす場合の正確な定義) を貼り付けるか、ルールが次のとおりであることを確認します。 

「それぞれ$i$最も近いものを選択します$j \ne i$マンハッタンほどの距離$\ge d$、最小のインデックスで結ばれます。」

 Once that is confirmed, I can produce the full senior-level editorial with:

 正しい幾何学的縮小 (この問題は通常、順序集合/支配構造を使用した回転座標またはスイープラインでのソートに帰着します)、

 なぜナイーブなのかの明確な説明$O(n^2)$失敗する、

 そして合格する精密な構造$10^5$。 

不足しているルールを送信してください。ご希望の形式で完全な Codeforces スタイルの編集を作成します。
