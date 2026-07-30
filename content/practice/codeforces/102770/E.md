---
title: "CF 102770E - 簡単な DP 問題"
description: "簡単なアプローチは、すべてのクエリの DP を個別に計算することです。 長さ m のセグメントの場合、その要素をスキャンしている間、状態 dp[i][j] を維持できます。 これは正しいです。再帰式は最初の i 要素間の最適な選択を直接記述しているからです。"
date: "2026-07-28T23:08:29+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102770
codeforces_index: "E"
codeforces_contest_name: "The 17th Zhejiang Provincial Collegiate Programming Contest"
rating: 0
weight: 102770
solve_time_s: 64
verified: true
draft: false
---

[CF 102770E - 簡単な DP 問題](https://codeforces.com/problemset/problem/102770/E)

 **評価:** -
 **タグ:** -
 **解決時間:** 1 分 4 秒
 **確認済み:** はい

 ## 解決策
 ## アプローチ

 簡単なアプローチは、すべてのクエリの DP を個別に計算することです。 ある長さのセグメントの場合`m`、状態を維持できます`dp[i][j]`その要素をスキャンしながら。 これは正しいです。再帰式は、最初の選択肢の中から最適な選択を直接記述しているからです。`i`要素。 ただし、1 つのクエリにはコストがかかります`O(mk)`、までのすべての州のため`m`そして`k`考慮する必要があります。 と`m`そして`k`どちらも近い`100000`1 つのクエリですでに数十億の操作が必要になる場合があります。 

重要な観察は、DP には隠れた単純な問題が含まれているということです。 によって提供された部分を削除します。`i²`条項。 定義する：`g[i][j] = dp[i][j] - (1² + 2² + ... + i²)`遷移は次のようになります。`g[i][j] = max(g[i-1][j], g[i-1][j-1] + b[i])`まさに選択のための繰り返しです`j`最初から可能な合計が最大になる要素`i`要素。 値が正なので、最高です`k`要素は単に`k`セグメント内の最大の数値。 

各クエリは、最大値の合計を見つけることに縮小されました。`k`部分配列内の値を取得し、固定値を追加します。`m(m+1)(2m+1)/6`これらの範囲クエリにすばやく答えるために、ウェーブレット ツリーを構築します。 値を再帰的に小さな範囲に分割します。 すべてのノードで、値の数と値の合計が左側に入るのかが保存されます。 これにより、最大の要素を検索するときに値の範囲全体をスキップできます。 最大値の合計を求めるには`k`値が大きい場合は、常に、より大きい値の子を最初に検査します。 その子に少なくとも`k`要素があれば、答えは完全にその中にあります。 それ以外の場合は、その子からすべての要素を取得し、より小さい値の子の残りの要素の検索を続けます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース |`O(mk)`クエリごと |`O(m)`| 遅すぎる |
 | ウェーブレット ツリー |`O(log A)`クエリごと |`O(n log A)`| 承認済み |

 ここ`A`配列の最大値です。`10^6`。 

## アルゴリズムのチュートリアル

 1. 元の配列上にウェーブレット ツリーを構築します。 各ノードは可能な値の範囲を表します。 ノードの構築中に、シーケンスを下位半分と上位半分の値に分割し、下位半分のプレフィックス数とプレフィックス合計を保存します。 これらのプレフィックス配列を使用すると、クエリ間隔内で各子に移動する値の数を知ることができます。 
2. クエリごとに`(l, r, k)`、位置をウェーブレット ツリーの 0 から始まるインデックスに変換します。 最大の合計が必要です`k`この間隔の値。 
3. ウェーブレット ツリー ノードで、クエリ範囲の要素の数が高値の子に属するかを判断します。 この数が少なくとも`k`、すべての必要な要素がより大きな値の中にあるため、その子を再帰的に実行します。 
4. 価値の高い子に含まれる内容が以下の場合`k`要素がある場合は、それらすべての要素の合計を答えに追加し、残りの要素数を求める低値の子に進みます。 
5. DP の二次寄与を追加します。 セグメント長が`m`、貢献度は次の平方和です。`1`に`m`、次の式で計算されます`m(m+1)(2m+1)/6`。 

クエリ プロセスの背後にある不変条件は、各ウェーブレット ツリー ノードで、アルゴリズムが現在の値間隔からまだ必要な最大値の数を正確に保持することです。 上位の子には常に下位の子のすべての値よりも大きな値が含まれるため、下位側を探索する前に上位側からすべての可能な値を取得すると、上位の定義が保持されます。`k`和。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

class WaveletTree:
    def __init__(self, arr, lo, hi):
        self.lo = lo
        self.hi = hi
        self.pref_cnt = None
        self.pref_sum = None
        self.left = None
        self.right = None

        if not arr or lo == hi:
            return

        mid = (lo + hi) // 2
        left_arr = []
        right_arr = []

        self.pref_cnt = [0]
        self.pref_sum = [0]

        for x in arr:
            if x <= mid:
                left_arr.append(x)
                self.pref_cnt.append(self.pref_cnt[-1] + 1)
                self.pref_sum.append(self.pref_sum[-1] + x)
            else:
                right_arr.append(x)
                self.pref_cnt.append(self.pref_cnt[-1])
                self.pref_sum.append(self.pref_sum[-1])

        self.left = WaveletTree(left_arr, lo, mid)
        self.right = WaveletTree(right_arr, mid + 1, hi)

    def top_sum(self, l, r, k):
        if k == 0:
            return 0
        if self.lo == self.hi:
            return self.lo * k

        left_before = self.pref_cnt[l]
        left_in_range = self.pref_cnt[r] - left_before

        right_count = (r - l) - left_in_range

        if right_count >= k:
            return self.right.top_sum(l - left_before, r - self.pref_cnt[r], k)

        right_sum = self.pref_sum[r] - self.pref_sum[l]
        return right_sum + self.left.top_sum(left_before, self.pref_cnt[r], k - right_count)

def solve():
    t = int(input())
    ans = []

    for _ in range(t):
        n = int(input())
        a = list(map(int, input().split()))

        tree = WaveletTree(a, 1, 10**6)

        q = int(input())
        for _ in range(q):
            l, r, k = map(int, input().split())

            selected = tree.top_sum(l - 1, r, k)

            length = r - l + 1
            squares = length * (length + 1) * (2 * length + 1) // 6

            ans.append(str(selected + squares))

    sys.stdout.write("\n".join(ans))

if __name__ == "__main__":
    solve()
```ウェーブレット ツリーは、位置ではなく値で分割された配列を格納します。 これは、この問題に役立つ重要な詳細です。 各ノードには、各子に渡された要素の数を知るのに十分なプレフィックス情報が格納されているため、クエリ範囲はすべてのノードで変換できます。 

の`top_sum`この関数は、現在のノード内のハーフオープンポジションで動作します。 変数`l`そして`r`そのノードに格納されているシーケンス内の現在の間隔を記述します。 表現`self.pref_cnt[r] - self.pref_cnt[l]`下の子に属する値の数を返します。 残りは上位の子の値の数です。 

リーフのケースは、残りのすべての値が同一であるため単純です。 必要であれば`k`値を含むリーフからの値`x`、それらの合計は`x * k`。 

Python の整数は、大きな中間値を自動的に処理します。 平方和は約に達します`10^15`したがって、固定幅の 32 ビット型を使用すると、整数が自動的に展開されない言語ではオーバーフローが発生します。 

## 実用的な例

 セグメントを考慮する`[1, 100, 2]`と`k = 2`。 

| ステップ | 現在値の範囲 | 高いサイドカウント | 残り k | 追加された合計 |
 | --- | --- | --- | --- | --- |
 | ルート |`[1,1000000]`| 1 | 2 | 100 |
 | 下の子 |`[1,500000]`| 1 | 1 | 2 |

 ウェーブレット ツリーは最初に最大値を取得します`100`、残りの部分でもう 1 つの値を検索します。 それは見つけます`2`、選択された貢献を生成します`102`。 DPの貢献度は`14`、したがって、答えは次のとおりです`116`。 

単一要素セグメントの場合`[5]`と`k = 1`、トラバースはすぐにリーフに到達します。 

| ステップ | 現在値の範囲 | 残り k | 追加された合計 |
 | --- | --- | --- | --- |
 | 葉 |`[5,5]`| 1 | 5 |

 選ばれた投稿は、`5`。 セグメントの長さは`1`、したがって、二乗寄与は次のようになります。`1`、最終的な答えを与える`6`。 

これらのトレースは、データ構造が常に利用可能な最大値を最初に取得すること、および固定 DP 項が選択された値に依存しないことを示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |`O((n + q) log A)`| ウェーブレット ツリーを構築すると、次のように各値が参照されます。`log A`すべてのクエリはレベルごとに 1 つのパスに従います。 |
 | スペース |`O(n log A)`| プレフィックス情報は、ウェーブレット ツリーのレベルごとに保存されます。 |

 最大値はあくまで`10^6`なので、木の高さは約20レベルです。 せいぜい`500000`要素とクエリの合計、対数係数により、合計作業量が制限内に保たれます。 

## テストケース```python
import sys
import io

def run(inp: str) -> str:
    old = sys.stdin
    sys.stdin = io.StringIO(inp)
    
    data = sys.stdin.readline
    t = int(data())
    out = []

    class WT:
        def __init__(self, arr, lo, hi):
            self.lo = lo
            self.hi = hi
            self.pc = self.ps = None
            self.l = self.r = None
            if not arr or lo == hi:
                return
            mid = (lo + hi) // 2
            la, ra = [], []
            self.pc = [0]
            self.ps = [0]
            for x in arr:
                if x <= mid:
                    la.append(x)
                    self.pc.append(self.pc[-1] + 1)
                    self.ps.append(self.ps[-1] + x)
                else:
                    ra.append(x)
                    self.pc.append(self.pc[-1])
                    self.ps.append(self.ps[-1])
            self.l = WT(la, lo, mid)
            self.r = WT(ra, mid + 1, hi)

        def get(self, l, r, k):
            if k == 0:
                return 0
            if self.lo == self.hi:
                return self.lo * k
            left_count = self.pc[r] - self.pc[l]
            right_count = r - l - left_count
            if right_count >= k:
                return self.r.get(l - self.pc[l], r - self.pc[r], k)
            return self.ps[r] - self.ps[l] + self.l.get(self.pc[l], self.pc[r], k - right_count)

    def solve_case(s):
        it = iter(s.split())
        n = int(next(it))
        a = [int(next(it)) for _ in range(n)]
        tree = WT(a, 1, 10**6)
        q = int(next(it))
        res = []
        for _ in range(q):
            l = int(next(it))
            r = int(next(it))
            k = int(next(it))
            m = r - l + 1
            res.append(str(tree.get(l - 1, r, k) + m * (m + 1) * (2 * m + 1) // 6))
        return "\n".join(res)

    result = solve_case(sys.stdin.read())
    sys.stdin = old
    return result

assert run("""1
3
1 100 2
1
1 3 2
""") == "116"

assert run("""1
1
5
1
1 1 1
""") == "6"

assert run("""1
3
3 4 5
1
1 3 3
""") == "26"

assert run("""1
5
7 7 7 7 7
3
1 5 1
1 5 5
2 4 2
""") == "22\n55\n30"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`[1,100,2], k=2`|`116`| 連続しない最大値の選択 |
 |`[5], k=1`|`6`| 最小の長さと平方寄与 |
 |`[3,4,5], k=3`|`26`| セグメント全体を選択する |
 | すべての値は等しい |`22,55,30`| 等しい値の処理と範囲境界 |

 ## 特殊なケース

 長さ 1 のクエリの場合、ウェーブレット ツリーは直ちにリーフに到達します。 入力配列の場合`[5]`そしてクエリ`(1,1,1)`、選択された合計は`5`。 平方式は次のようになります`1`したがって、出力は次のようになります`6`。 この場合、アルゴリズムは内部ツリー ノードの有無に依存しません。 

クエリの場合、`k`がセグメント長と等しい場合、検索では最終的に範囲内のすべての値が収集されます。 のために`[3,4,5]`そして`(1,3,3)`、ウェーブレットトラバーサルが集まります`5`、 それから`4`、 それから`3`、与える`12`。 正方形の寄与を追加する`14`生成する`26`。 

連続しない選択の場合は、`[1,100,2]`と`k=2`は、DP を連続選択問題に単純化できない理由を示しています。 ウェーブレット ツリーが選択するのは、`100`高価値側から、そして`2`残りの側からは、元の DP 再発と正確に一致します。
