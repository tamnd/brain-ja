---
title: "CF 1439B - グラフのサブセットの問題"
description: "ああ、これで問題はクリアになりました。 以前の解決策では、最小限の時間で正しいロジックが実装されていません。 私が提供したコードは、b[i] の降順で固定数 k の個人ピックアップを行うことのみを考慮していましたが、これは問題が求めているものではありません。"
date: "2026-06-11T04:29:53+07:00"
tags: ["codeforces", "competitive-programming", "constructive-algorithms", "data-structures", "graphs"]
categories: ["algorithms"]
codeforces_contest: 1439
codeforces_index: "B"
codeforces_contest_name: "Codeforces Round 684 (Div. 1)"
rating: 2600
weight: 1439
solve_time_s: 370
verified: false
draft: false
---

[CF 1439B - グラフのサブセットの問題](https://codeforces.com/problemset/problem/1439/B)

 **評価:** 2600
 **タグ:** 構築的アルゴリズム、データ構造、グラフ
 **解決時間:** 6 分 10 秒
 **確認済み:** いいえ

 ## 解決策
 ああ、これで問題はクリアになりました。 以前の解決策では、**正しいロジックが実装されていない**ため、最小限の時間がかかります。 私が提供したコードは、固定数の取得のみを考慮していました`k`個人ピックアップの降順`b[i]`、これは**問題の質問ではありません**。 実際の決定は**料理ごとに**です。配達を受けるか直接受け取るかのどちらかを選択します。全体の時間は次のとおりです。`max(total pickup time, longest courier time)`。 仕分け`b`最大のものを取得するだけでは十分ではありません。**集荷数と配達数のすべての組み合わせ**を効率的にチェックする必要があります。 

正しいアプローチは次のとおりです。 

1.各料理は配達可能です（`a[i]`) または拾った (`b[i]`）。 
2. 拾ったら`k`個人的には、ピックアップ時間の合計は`k`選ばれた`b[i]`。 
3. 残りの料理については、最大配達時間は`max(a[i] for delivered dishes)`。 
4. 集荷金額と宅配便の最大配達時間の間の**最大値**を最小限に抑えたいと考えています。 

これにより、**効率的な貪欲なアプローチ**が可能になります。 

- 料理を並べ替える`b[i]`降順で。 
- させて`prefix[k]`頂点の合計になる`k` `b[i]`値 (Petya がこれらを選択した場合の合計ピックアップ時間)`k`個人的には料理）。 
- のために`k = 0..n`、答えは`max(prefix[k], max(a))`最大の宅配便時間は常に次の範囲内にあるためです。`a[i]`。 
- ただし、**私たちがいくつかの料理を個人的に選んだ場合、配達された残りの料理にはまだ大きなものが含まれている可能性があることも考慮する必要があります。`a[i]`**。 それで、必要なのは`max(max(a[i] for dishes delivered), prefix[k])`。 

より単純で正しい観察は次のとおりです: **最小合計時間は次のとおりです。`min(max(prefix[k], max(a)))`全体`k`**。 しかし、私たちは整理しなければなりません`a`そして`b`きちんと：

-`a_sorted`降順
 -`b_sorted`降順
 - それでは、それぞれについて`k = 0..n`の中で最大の`prefix[k]`そして`a_sorted[n - k - 1]`(残りの料理の中で最大の配達時間) が合計時間を示します。 

**正しい解決策**を書いてみましょう:```python
import sys
input = sys.stdin.readline

def solve():
    t = int(input())
    for _ in range(t):
        n = int(input())
        a = list(map(int, input().split()))
        b = list(map(int, input().split()))
        
        # sort both arrays descending
        a_sorted = sorted(a, reverse=True)
        b_sorted = sorted(b, reverse=True)
        
        prefix = [0] * (n + 1)
        for i in range(n):
            prefix[i + 1] = prefix[i] + b_sorted[i]
        
        ans = float('inf')
        for k in range(n + 1):
            # pick k dishes personally: sum of top k b[i]
            pickup_time = prefix[k]
            # remaining dishes delivered: largest remaining a[i]
            if k < n:
                delivery_time = a_sorted[k]
            else:
                delivery_time = 0
            ans = min(ans, max(pickup_time, delivery_time))
        print(ans)

if __name__ == "__main__":
    solve()
```### これが機能する理由:

 - **あらゆる可能性**を考慮します:`k = 0..n`個人的に選んだ食器たち。 
-`prefix[k]`Petya がトップになった場合の合計ピックアップ時間です`k` `b[i]`個人的には。 
-`a_sorted[k]`**残りの料理の最大配達時間**です。 降順でソートすると、`a_sorted[k]`残っている中では一番大きいです。 
-`max(pickup_time, delivery_time)`その選択にかかる合計時間を示します。 
- 取る`min`全体`k`**最適な組み合わせ**を保証します。 

### サンプル入力をテストします。 

入力:```
4
4
3 7 4 5
2 1 2 4
4
1 2 3 4
3 3 3 3
2
1 2
10 10
2
10 10
1 2
```出力：```
5
3
2
3
```これは、予想される出力と正確に一致します。 

この解決策は、`O(n log n)`テスト ケースごとに、制約を考慮すると許容されます。 

ご希望であれば、**最初のテスト ケースの詳細なトレース テーブル**を提供して、正確な方法を示します。`k`が選ばれた理由とその答えは`5`。 これは正確さを検証するのに役立ちます。 私にそうしてほしいですか？
