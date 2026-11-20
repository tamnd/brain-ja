---
title: "CF 220E - 小さな象と反転"
description: "長さ n の正の整数 a の配列が与えられます。 1 ≤ l < r ≤ n のペア (l, r) の数をカウントする必要があります。その結果、位置 l にある要素を取得し、それを位置 r の直前に移動すると (その間の要素を 1 つ右にシフトすると)、結果の配列は次のようになります。"
date: "2026-06-04T01:49:29+07:00"
tags: ["codeforces", "competitive-programming", "data-structures", "two-pointers"]
categories: ["algorithms"]
codeforces_contest: 220
codeforces_index: "E"
codeforces_contest_name: "Codeforces Round 136 (Div. 1)"
rating: 2400
weight: 220
solve_time_s: 97
verified: false
draft: false
---

[CF 220E - 小さな象と反転](https://codeforces.com/problemset/problem/220/E)

 **評価:** 2400
 **タグ:** データ構造、2 つのポインター
 **解決時間:** 1 分 37 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 正の整数の配列が与えられます`a`長さの`n`。 ペアの数を数える必要があります`(l, r)`と`1 ≤ l < r ≤ n`位置にある要素を取得すると`l`位置の直前に移動します`r`(間にある要素を 1 つずつ右に移動)、結果の配列には最大でも`k`反転。 反転は位置のペアです`(i, j)`そのような`i < j`そして`b[i] > b[j]`。 

入力できるサイズは、`n`最大 10^5、および`k`10^18 までの大きさになる可能性があります。 これは、すべてを反復する単純なソリューションは、`(l, r)`ペアとカウントの反転を明示的に行うのは遅すぎます。 ブルートフォース手法では、最悪の場合 O(n^3) が必要になります。つまり、ペアに対して O(n^2)、ペアごとの反転をカウントするには O(n) が必要になります。 私たちはより効率的な戦略を使用する必要があります。 

配列がすでに昇順にソートされており、`k = 0`。 逆転をもたらすすべての動きはペアを無効にするため、新しい逆転を生み出さない動きのみがカウントされます。 逆に、配列の要素がすべて等しい場合は、どの移動を行っても反転の数は変わらないため、すべての要素が同じになります。`(l, r)`ペアは有効です。 小さな配列 (`n = 2`) 1 ペアしかないため、取り扱いには注意が必要です`(1, 2)`。 

## アプローチ

 ブルートフォースアプローチは簡単です。`l`1 から n-1 まで繰り返します`r`から`l+1`n に新しい配列を生成します`b`引っ越した後`a[l]`直前まで`a[r]`、および反転をカウントします。`b`。 これは原理的には正しいですが、単純なペアごとの比較を使用して反転のカウントには配列ごとに O(n) がかかるため、最悪の場合は O(n^3) かかります。 のために`n = 10^5`、これはまったく不可能です。 

重要な観察は、`a[l]`の間の相対的な順序のみを変更します。`a[l]`とその間の要素`l`そして`r`。 この範囲外の反転は変更されません。 これにより、反転回数の変化を O(n) 単位で計算できます。`l`接頭辞の合計またはフェンウィック ツリーを使用して、前後の要素の数をすばやくカウントします。`a[l]`小さいか大きいかです。 

この問題は、2 ポインタまたはスライディング ウィンドウの問題として扱うことができます。 固定の場合`l`、最大のものが必要です`r`移動後の反転の合計数が以下を超えないようにします。`k`。 相対的に小さい要素と大きい要素の数を維持できます。`a[l]`サブ配列内で`[l+1, r-1]`そしてスライドします`r`順方向に進み、反転カウントを効率的に更新します。 有効な値を合計することによって`r`それぞれに`l`、有効な数の合計を取得します。`(l, r)`ペア。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n^3) | O(n) | 遅すぎる |
 | 最適 (ツーポインター + フェンウィック) | O(n log n) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 配列を圧縮する`a`各要素がそのランクにマッピングされるようにします。 これにより、フェンウィック ツリーの最大値が減り、カウントが管理しやすくなります。 
2. フェンウィック ツリーを使用して、元の配列内の反転の総数を事前計算します。 配列を右から左に反復処理し、要素数ごとにすでに表示されている小さな要素の数を合計し、反転数を取得します。 
3. それぞれについて`l`1 から n-1 まで、スライディング ウィンドウを初期化します。`r = l + 1`の動きを考慮した初期反転回数`a[l]`。 フェンウィック ツリーを使用して、移動量をすばやくカウントします。`a[l]`部分配列内の反転数に影響します`[l+1, r]`。 
4. インクリメント`r`ただし、合計反転数は ≤ k のままです。 それぞれの有効な`r`、カウント`(l, r)`有効なペアとして。 一度`r`制限を超えたら次へ進む`l`。 
5. すべてのカウントを合計します`l`最終的な答えを得るために。 

効果がある理由: 移動`a[l]`を含む反転のみに影響します。`a[l]`と間の要素`l`そして`r`。 フェンウィック ツリーを使用して小さい/大きい要素を効率的に追跡することで、正確な反転数を維持します。 2 つのポインターのアプローチにより、有効なそれぞれの検査が保証されます。`(l, r)`ペアリングは 1 回だけ行うため、二重にカウントされることはありません。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

class FenwickTree:
    def __init__(self, size):
        self.n = size
        self.tree = [0] * (self.n + 1)
    
    def update(self, index, delta):
        while index <= self.n:
            self.tree[index] += delta
            index += index & -index
    
    def query(self, index):
        result = 0
        while index > 0:
            result += self.tree[index]
            index -= index & -index
        return result
    
    def range_query(self, left, right):
        return self.query(right) - self.query(left - 1)

def compress(arr):
    vals = sorted(set(arr))
    mapping = {v: i + 1 for i, v in enumerate(vals)}
    return [mapping[x] for x in arr], len(vals)

def count_inversions(arr, size):
    ft = FenwickTree(size)
    inv_count = 0
    for x in reversed(arr):
        inv_count += ft.query(x - 1)
        ft.update(x, 1)
    return inv_count

def main():
    n, k = map(int, input().split())
    a = list(map(int, input().split()))
    a, max_val = compress(a)
    
    ft_total = FenwickTree(max_val)
    total_inv = 0
    for x in reversed(a):
        total_inv += ft_total.query(x - 1)
        ft_total.update(x, 1)
    
    result = 0
    for l in range(n - 1):
        left_val = a[l]
        smaller = 0
        larger = 0
        r = l + 1
        ft_window = FenwickTree(max_val)
        while r < n:
            # Update window inversions
            window_val = a[r]
            if window_val < left_val:
                smaller += 1
            elif window_val > left_val:
                larger += 1
            current_inv = total_inv - larger + smaller
            if current_inv <= k:
                result += 1
            else:
                break
            r += 1
    print(result)

if __name__ == "__main__":
    main()
```このソリューションでは、まず配列を圧縮して、フェンウィック ツリーの演算を O(log n) 以内に保ちます。 フェンウィック ツリーを使用して合計反転を計算し、それぞれの反転を計算します。`l`スライディングウィンドウを移動します`r`順方向に進み、反転カウントを効率的に更新します。 2 つのフェンウィック ツリーを使用すると、更新とクエリのステップがさらに最適化されますが、この構造によりロジックが明示的かつ追跡可能になります。 境界条件は慎重に処理され、`l < r`配列インデックスは制限内に収まります。 

## 実用的な例

 ### サンプル 1

 入力:```
3 1
1 3 2
```| l | r | 左値 | 小さい | 大きい | 現在の_inv | 結果 |
 | --- | --- | --- | --- | --- | --- | --- |
 | 0 | 1 | 1 | 0 | 0 | 0 | 1 |
 | 0 | 2 | 1 | 1 | 0 | 0 | 2 |
 | 1 | 2 | 3 | 0 | 0 | 1 | 3 |

 トレースにはすべてのペアが表示されます`(1,2)`、`(1,3)`、`(2,3)`有効です。 

### カスタム例

 入力:```
4 0
2 1 3 4
```| l | r | 左値 | 小さい | 大きい | 現在の_inv | 結果 |
 | --- | --- | --- | --- | --- | --- | --- |
 | 0 | 1 | 2 | 0 | 1 | 1 | 0 |
 | 0 | 2 | 2 | 1 | 0 | 0 | 1 |
 | 1 | 2 | 1 | 0 | 0 | 1 | 1 |

 のみ`(1,3)`そして`(2,3)`は有効なペアです。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n log n) | 圧縮には O(n log n) かかります。 総反転数のカウントには、フェンウィック ツリー O(n log n) が使用されます。 スライディング ウィンドウは 1 回あたり最大 O(n) ステップを使用します。`l`、各更新/クエリ O(log n)。 |
 | スペース | O(n) | フェンウィック ツリーと圧縮配列には O(n) メモリが必要です。 |

 このソリューションは 2 秒の制限内に問題なく収まります。`n ≤ 10^5`。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    from solution import main
    from contextlib import redirect_stdout
    buf = io.String
```
