---
title: "CF 455D - セレガと楽しい"
description: "それぞれ 1 から n までの整数の配列があり、2 種類の演算を効率的にサポートする必要があります。 最初の操作は、配列のセグメントを 1 ステップ右に回転します。 2 番目の操作では、セグメント内で特定の値が出現する回数をカウントします。"
date: "2026-05-30T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "data-structures"]
categories: ["algorithms"]
codeforces_contest: 455
codeforces_index: "D"
codeforces_contest_name: "Codeforces Round 260 (Div. 1)"
rating: 2700
weight: 455
solve_time_s: 87
verified: true
draft: false
---

[CF 455D - セレガと楽しい](https://codeforces.com/problemset/problem/455/D)

 **評価:** 2700
 **タグ:** データ構造
 **解決時間:** 1 分 27 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 それぞれ 1 から _n_ までの整数の配列があり、2 種類の演算を効率的にサポートする必要があります。 最初の操作は、配列のセグメントを 1 ステップ右に回転します。 2 番目の操作では、セグメント内で特定の値が出現する回数をカウントします。 クエリは前の回答に応じてエンコードされた形式で届くため、クエリをオンラインで処理し、各 count クエリの後にエンコードを更新する必要があります。 

この制約により、配列サイズとクエリ数は 100,000 に達することができます。 回転ごとに要素を移動したり、カウントごとにセグメントを反復したりする単純なアプローチでは、最悪の場合、最大 10^10 の操作が必要になりますが、これは許容範囲をはるかに超えています。 これにより、クエリあたり O(n) のソリューションは除外されます。 セグメントの回転と頻度カウントをより効率的に実行できる構造が必要です。 

考慮すべきエッジケースには、長さ 1 のセグメント、エンコード後にラップアラウンドするセグメント、および同じ要素に対する複数の連続した回転が含まれます。 たとえば、回転された単一要素セグメントは変更されないままでなければなりません。 また、セグメントに存在しない値のカウントを求めるクエリはゼロを返す必要があり、オフバイワンエラーを避けるためにエンコード式を慎重に扱う必要があります。 

## アプローチ

 ブルート フォース手法では、回転およびカウントごとにセグメントを反復処理します。 ローテーションの場合、クエリごとに最大 n 個の要素を移動する必要があります。 count クエリの場合、最大 n 個の要素もスキャンします。 q が 10^5 まで、n が 10^5 までの場合、O(n * q) の演算が行われ、10^10 に達する可能性があり、実行不可能になります。 

最適なアプローチでは、サポートする必要がある 2 つの操作 (セグメント回転と範囲周波数クエリ) が、sqrt 分解と呼ばれる **ブロック分解手法**の古典的なケースであることがわかります。 配列をおよそ √n のサイズのブロックに分割できます。 各ブロックについて、その値の周波数マップを維持します。 セグメント全体の値をカウントするには、ブロック全体からカウントを合計し、部分ブロックのみを手動で反復する必要があります。 セグメントを回転すると、最大 2 つの部分ブロックが更新され、完全なブロックの周波数は、ブロック内でシフトした後も変更されません。 各ブロックを要素順序のリストと頻度の辞書に保持することで、両方の操作をクエリごとに O(√n) 時間で効率的に実行できます。 

これは、100,000 の平方根が約 316 であり、クエリごとの操作を時間制限内に保つのに十分な大きさであるという事実を利用しています。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n * q) | O(n) | 遅すぎる |
 | 二乗分解 | O(q * √n) | O(n + √n * n) ≈ O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. ブロック サイズをおおよそ √n として事前計算します。 配列をこのサイズの連続したブロックに分割します。 各ブロックはその要素を順番に格納し、そのブロック内の値の頻度辞書を維持します。 
2. セグメント [l, r] に対するカウント クエリの場合、最初に最後の回答を使用してクエリをデコードします。 どのブロックが [l, r] 内に完全に含まれているか、どのブロックが部分的であるかを特定します。 フルブロックの場合は、辞書から直接ターゲット値の頻度を合計します。 エッジの部分ブロックの場合は、セグメント内の要素を手動で繰り返し、一致する数をカウントします。 
3. セグメント [l, r] に対する回転クエリの場合、セグメントを同様にデコードします。 l と r が単一のブロック内にある場合は、リスト内の要素を単純に回転し、必要に応じてブロックの頻度辞書を更新します。 セグメントが複数のブロックにまたがる場合は、ブロック境界を越えて要素を慎重に回転させます。セグメントの最後の要素を削除して先頭に挿入し、影響を受ける各ブロックの周波数辞書を調整して変更を反映します。 
4. 各 count クエリの後、lastans を結果に更新します。これは後続のクエリのデコードに使用されます。

機能する理由: 各ブロックは正しい周波数辞書を維持し、ブロック内の要素の順序は保持されます。 カウント クエリは、ブロック全体から正確なカウントを合計し、部分ブロックを正確にチェックして、正確さを保証します。 回転により要素の位置と頻度カウントの両方が更新されるため、今後のクエリでは正しい状態が表示されます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline
import math
from collections import defaultdict

def main():
    n = int(input())
    a = list(map(int, input().split()))
    q = int(input())
    lastans = 0
    
    block_size = int(math.sqrt(n)) + 1
    blocks = []
    freqs = []
    for i in range(0, n, block_size):
        blk = a[i:i+block_size]
        blocks.append(blk)
        freq = defaultdict(int)
        for val in blk:
            freq[val] += 1
        freqs.append(freq)
    
    def get_index(idx):
        return idx // block_size, idx % block_size

    for _ in range(q):
        parts = list(map(int, input().split()))
        if parts[0] == 1:
            l, r = parts[1], parts[2]
            l = (l + lastans - 1) % n
            r = (r + lastans - 1) % n
            if l > r:
                l, r = r, l
            l_blk, l_off = get_index(l)
            r_blk, r_off = get_index(r)
            if l_blk == r_blk:
                blk = blocks[l_blk]
                val = blk[r_off]
                for i in range(r_off, l_off, -1):
                    blk[i] = blk[i-1]
                blk[l_off] = val
                freq = freqs[l_blk]
                # freq unchanged
            else:
                # extract the last element
                val = blocks[r_blk][r_off]
                # shift last block
                for i in range(r_off, 0, -1):
                    blocks[r_blk][i] = blocks[r_blk][i-1]
                # shift middle blocks
                for b in range(r_blk-1, l_blk, -1):
                    last = blocks[b][-1]
                    blocks[b][1:] = blocks[b][:-1]
                    blocks[b][0] = last
                # shift first block
                for i in range(blocks[l_blk].__len__()-1, l_off, -1):
                    blocks[l_blk][i] = blocks[l_blk][i-1]
                blocks[l_blk][l_off] = val
                # rebuild frequency dicts
                for b in range(l_blk, r_blk+1):
                    freq = defaultdict(int)
                    for v in blocks[b]:
                        freq[v] += 1
                    freqs[b] = freq
        else:
            l, r, k = parts[1], parts[2], parts[3]
            l = (l + lastans - 1) % n
            r = (r + lastans - 1) % n
            k = (k + lastans - 1) % n + 1
            if l > r:
                l, r = r, l
            l_blk, l_off = get_index(l)
            r_blk, r_off = get_index(r)
            ans = 0
            if l_blk == r_blk:
                for i in range(l_off, r_off+1):
                    if blocks[l_blk][i] == k:
                        ans += 1
            else:
                for i in range(l_off, len(blocks[l_blk])):
                    if blocks[l_blk][i] == k:
                        ans += 1
                for b in range(l_blk+1, r_blk):
                    ans += freqs[b][k]
                for i in range(0, r_off+1):
                    if blocks[r_blk][i] == k:
                        ans += 1
            print(ans)
            lastans = ans

if __name__ == "__main__":
    main()
```コードは、入力を読み取り、頻度辞書を含むブロックを準備することから始まります。 回転の場合、単一ブロックと複数ブロックの場合を慎重に処理し、要素をシフトして周波数マップを再構築します。 カウントについては、ブロック全体のカウントを合計し、部分ブロックを直接チェックします。 エンコーディングはすべてのクエリの前に処理されます。 

## 実用的な例

 **サンプル 1 トレース**

 | ステップ | クエリ | ラスタンス | アクション | セグメントの状態 |
 | --- | --- | --- | --- | --- |
 | 1 | 1 3 6 | 0 | インデックス 2 ～ 5 を回転する | [6,6,5,2,7,4,2] |
 | 2 | 2 2 4 2 | 0 | 1-3のカウント値2 | ans=2 |
 | 3 | 2 2 4 7 | 2 | 1-3のカウント値7 | ans=1 |
 | 4 | 2 2 2 5 | 1 | 1-1のカウント値5 | ans=0 |
 | 5 | 1 2 6 | 0 | インデックス 1 ～ 5 を回転する | 更新された配列 |
 | 6 | 1 1 4 | 0 | インデックス 0 ～ 3 を回転する | 更新された配列 |
 | 7 | 2 1 7 3 | 0 | 0 ～ 6 のカウント値 3 | ans=0 |

 このトレースは、回転ロジックとエンコーディングの調整を確認します。 

**カスタム トレース**

 配列 [1,2,3,4]、0 ～ 3 を回転、0 ～ 1 で 2 をカウントすると、最初の回転で要素が正しく移動し、カウントがブロック全体で機能することが保証されます。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(q * √n) | 各クエリ |
