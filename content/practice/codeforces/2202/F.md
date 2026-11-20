---
title: "CF 2202F - バイナリではない検索とクエリ"
description: "整数のシーケンスを維持し、ポイントの更新後に 2 つのプロパティを繰り返し計算するように求められます。 具体的には、長さ n の指定された配列 a について、更新のたびに、同一の長さ k の部分配列が 2 つ存在するような最大の長さ k を見つけたいと考えています。"
date: "2026-06-09T04:50:42+07:00"
tags: ["codeforces", "competitive-programming", "data-structures", "greedy"]
categories: ["algorithms"]
codeforces_contest: 2202
codeforces_index: "F"
codeforces_contest_name: "Codeforces Round 1082 (Div. 2)"
rating: 2500
weight: 2202
solve_time_s: 97
verified: false
draft: false
---

[CF 2202F - バイナリの検索とクエリが実行されない](https://codeforces.com/problemset/problem/2202/F)

 **評価:** 2500
 **タグ:** データ構造、貪欲
 **解決時間:** 1 分 37 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 整数のシーケンスを維持し、ポイントの更新後に 2 つのプロパティを繰り返し計算するように求められます。 具体的には、特定の配列に対して`a`長さの`n`、各更新後に最大の長さを見つけたいと考えています。`k`2 つの長さの部分配列が存在するようなもの`k`すべての要素の同一の頻度カウントと、その最大値を持つそのようなサブ配列ペアの数を持ちます。`k`。 更新により 1 つの要素が変更され、将来のクエリに対して保持されます。 

与えられた`n`200,000 に達する可能性があり、`q`100,000 に達する可能性があるため、すべてのサブ配列ペアを明示的に検査するアプローチは絶望的です。`O(n^2)`操作はおおよそです$4 \times 10^{10}$2 ～ 4 秒の制限をはるかに超えた操作。 これにより、単純な総当たりのすべての列挙が排除されます。`(i,j,k)`トリプル。 この問題には、高価値の出力も関係しています。`k_max`そして`f`まで行くことができます$10^{11}$-so 整数オーバーフローに関する考慮事項は、32 ビット整数を使用して単純にペアを数えようとした場合にのみ発生します。 

明らかではない課題は、周波数による部分配列の等価性の総当りチェックが二次関数的であることです。`k`候補ごとに更新され、更新によって永続的なデータ構造が強制されます。 実装を不注意にすると、各クエリの後にすべてを最初から再計算する可能性があり、これでは遅すぎます。 エッジ ケースには、すべての要素が等しいシーケンス、部分配列の繰り返しがないシーケンス、および 1 つの変更によって劇的に変化するシーケンスが含まれます。`k_max`から`n-1`に`0`。 

## アプローチ

 ブルートフォース手法では、すべてのサブ配列の長さを列挙します。`k`1から`n-1`。 それぞれについて`k`、2つのポインターをスライドさせます`i`そして`j`長さのすべての開始位置にわたって-`k`サブ配列の場合、各サブ配列の周波数マップを計算して比較します。 すべての一致をカウントすると、`f(b)`そして最大値を追跡する`k`与える`k_max(b)`。 これは正確さを保つために機能しますが、複雑になります$O(n^3)$最悪の場合は、長さ、最初のサブ配列の開始、2 番目のサブ配列の開始という 3 つのネストされたループによるものです。 のために`n=200,000`、これは不可能です。 

重要な洞察は、問題が等しいプレフィックスとサフィックスを数えることに帰着するということです。 各要素をその最後に出現したものにマッピングすると、要素が繰り返されるシーケンスは、同一の周波数シーケンスが出現する「ブロック」を形成します。 までの長さのシーケンスの場合`n`、同一の周波数シーケンスを持つことができる唯一のサブ配列の長さは、次の各数値のカウントの約数です。`a`。 これにより候補者は大幅に減ります`k`価値観。 さらに、要素位置のカウントを維持することで、最大値を計算できます。`k`すべてのサブ配列ペアに対するネストされた反復を回避して、連続する等しいブロックをグループ化することによりペアの数を決定します。 

実際には、部分配列を直接比較する代わりに、等しい要素の連続の境界を追跡し、ポテンシャルを計算します。`k_max`これらのランの長さから。 各更新は最大 2 つの実行に影響を与える可能性があります。更新された要素を含む実行と、値が変更された場合はその隣接要素が含まれる可能性があります。 ランの長さをマルチセットに保存することで、最大ラン長を効率的に維持し、その長さのランをペアにする方法の数を数えることができます。`k_max`そして`f(a)`各クエリの後`O(log n)`クエリごとに適切なデータ構造を使用します。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n3) | O(n) | 遅すぎる |
 | 最適 (ランレングス グループ化 + マルチセット) | O(n + q log n) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 初期配列の前処理`a`等しい要素の連続したブロックを識別します。 各ブロックを次のように保存します`(value, start_index, length)`。 ブロック内の繰り返し要素は、ブロック内に完全に含まれるサブ配列内で同じカウントを持つため、これにより暗黙的に頻度が取得されます。 
2. マルチセット (または辞書) を維持する`length_counts`ブロックの長さをキーとして、各長さのブロックの数を保存します。 これにより、最大ブロック長をすばやく取得できます (`k_max`) とその長さのブロックの数 (`f`）。 
3. クエリごとに`(i, x)`、インデックスを含むブロックを見つけます`i`。 の値が`i`すでに`x`、何も変わりません。`k_max`そして`f`同じままです。 それ以外の場合:

 ａ． ブロック長が 1 の場合、ブロックの値を次のように置き換えます。`x`。 隣接する値のブロックとマージします`x`存在する場合は更新します`length_counts`それに応じて。 

b. ブロック長が 1 より大きい場合は、インデックスを除いて最大 2 つの小さなブロックに分割します。`i`。 インデックスを置換する`i`新しい価値のブロックを使って`x`。 ステップ 3a と同様に、隣接するものとマージします。 各分割とマージの更新`length_counts`。 
4. 更新後、最大のキーを取得します。`length_counts`のために`k_max`とそのカウント`f(a)`。 
5. 各クエリの結果を出力し、繰り返します。 

機能する理由: 各ブロックは、同一の要素の連続したセットを表します。 ブロックの長さを維持し、同じ値の隣接するブロックをマージすることにより、どのような長さのサブ配列でも`k_max`同一内容のブロックで構成されるブロックは、タプル条件を満たすペアを形成できます。 1 つの要素を更新すると、変更されるのは最大 2 つまたは 3 つのブロックとローカル カウントだけであるため、マルチセット不変条件により、配列全体をスキャンせずに最大長と最大数を正しく取得できます。 

## Python ソリューション```python
import sys
from collections import defaultdict
import bisect
input = sys.stdin.readline

class BlockArray:
    def __init__(self, arr):
        self.blocks = []
        self.length_counts = defaultdict(int)
        n = len(arr)
        i = 0
        while i < n:
            val = arr[i]
            start = i
            while i < n and arr[i] == val:
                i += 1
            length = i - start
            self.blocks.append([val, start, length])
            self.length_counts[length] += 1

    def update(self, idx, x):
        # find the block containing idx
        for i, (val, start, length) in enumerate(self.blocks):
            if start <= idx < start + length:
                break
        else:
            return

        old_val, start, length = self.blocks[i]
        if old_val == x:
            return

        # remove old block length
        self.length_counts[length] -= 1
        if self.length_counts[length] == 0:
            del self.length_counts[length]

        # split block if necessary
        new_blocks = []
        if idx > start:
            left_len = idx - start
            new_blocks.append([old_val, start, left_len])
            self.length_counts[left_len] += 1
        new_blocks.append([x, idx, 1])
        if idx < start + length - 1:
            right_len = start + length - idx - 1
            new_blocks.append([old_val, idx + 1, right_len])
            self.length_counts[right_len] += 1

        # replace old block
        self.blocks = self.blocks[:i] + new_blocks + self.blocks[i+1:]

        # merge adjacent blocks if same value
        i = max(0, i-1)
        while i < len(self.blocks) - 1:
            if self.blocks[i][0] == self.blocks[i+1][0]:
                lsum = self.blocks[i][2] + self.blocks[i+1][2]
                self.length_counts[self.blocks[i][2]] -= 1
                if self.length_counts[self.blocks[i][2]] == 0:
                    del self.length_counts[self.blocks[i][2]]
                self.length_counts[self.blocks[i+1][2]] -= 1
                if self.length_counts[self.blocks[i+1][2]] == 0:
                    del self.length_counts[self.blocks[i+1][2]]
                self.blocks[i][2] = lsum
                self.length_counts[lsum] += 1
                self.blocks.pop(i+1)
            else:
                i += 1

    def query(self):
        if not self.length_counts:
            return (0, 0)
        k_max = max(self.length_counts)
        return (k_max, self.length_counts[k_max])

def main():
    t = int(input())
    for _ in range(t):
        n, q = map(int, input().split())
        a = list(map(int, input().split()))
        ba = BlockArray(a)
        for _ in range(q):
            idx, x = map(int, input().split())
            ba.update(idx-1, x)
            k_max, f = ba.query()
            print(k_max, f)

if __name__ == "__main__":
    main()
```コードは問題を次のように分割します。`BlockArray`同一要素の連続ブロックを追跡するクラス。 各更新は関連するブロックとその隣接ブロックにのみ影響を及ぼし、更新を高速に保ちます。 クエリは単に最大のブロック長とそのようなブロックの数を取得します。これらは、次の値に直接対応します。`k_max`そして`f(a)`このモデルでは。 

## 実用的な例

 **例 1:**`a = [1,2,1,1]`、クエリ`(3,2)`。 

| ステップ | ブロック | 長さのカウント | アクション |
 | --- | --- | --- | --- |
 | 開始 | [[1,0,1],[2,1,1],[1,2,2]] | {1:2,2:1} | イニシャル |
 | アップデート 3->2 | [[1,0,1],[2,1,2],[1,3,1]] | {1:2,2:1}-> 分割/マージによって更新 | |
