---
title: "CF 1344A - ヒルベルトのホテル"
description: "私たちは、整数でラベル付けされた各部屋に 1 人のゲストがいる無限のホテルを扱っています。 各ゲストが現在のルーム $k$ から新しいルーム $k + a{k bmod n}$ に移動する場合、シャッフル ルールが適用されます。ここで、$a$ は長さ $n$ の配列です。"
date: "2026-06-11T15:07:52+07:00"
tags: ["codeforces", "competitive-programming", "math", "number-theory", "sortings"]
categories: ["algorithms"]
codeforces_contest: 1344
codeforces_index: "A"
codeforces_contest_name: "Codeforces Round 639 (Div. 1)"
rating: 1600
weight: 1344
solve_time_s: 320
verified: false
draft: false
---

[CF 1344A - ヒルベルトのホテル](https://codeforces.com/problemset/problem/1344/A)

 **評価:** 1600
 **タグ:** 数学、整数論、並べ替え
 **解決時間:** 5 分 20 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 私たちは、整数でラベル付けされた各部屋に 1 人のゲストがいる無限のホテルを扱っています。 各ゲストが現在の部屋から移動する場合、シャッフル ルールが適用されます$k$新しい部屋へ$k + a_{k \bmod n}$、 どこ$a$長さの配列です$n$。 タスクは、このシャッフルによってすべての部屋に固有のゲストが生成されるかどうか、または衝突が発生するかどうかを判断することです。 

入力は複数のテスト ケースで構成され、それぞれが指定します$n$そして配列$a$。 シャッフルが一意性を保持する場合、出力は「YES」、そうでない場合は「NO」になります。 と$n$まで$2 \cdot 10^5$そして$t$まで$10^4$、すべてのテスト ケースにわたる配列要素の合計は (2 \cdot 10^5\) で制限されます。
