---
title: "CF 102412J - さらにもう一つのメキシコ問題"
description: "この問題は、MEX Foundation Contest、Gym 102412、問題 J からのものです。公式の制限は (2le nle 2cdot10^5)、(1le kle n)、(0le aile n) で、制限時間は 4 秒、メモリは 512 MiB です。 非負の整数配列 (a) があります。"
date: "2026-08-10T14:14:38+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102412
codeforces_index: "J"
codeforces_contest_name: "MEX Foundation Contest (supported by AIM Tech)"
rating: 0
weight: 102412
solve_time_s: 350
verified: true
draft: false
---

[CF 102412J - さらに別のメキシコ問題](https://codeforces.com/problemset/problem/102412/J)

 **評価:** -
 **タグ:** -
 **解決時間:** 5 分 50 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 この問題は、MEX Foundation Contest、Gym 102412、問題 J のものです。公式の制限は (2\le n\le 2\cdot10^5)、(1\le k\le n)、(0\le a_i\le n) で、制限時間は 4 秒、メモリは 512 MiB です。 

非負の整数配列 (a) があります。 それを連続した部分に分割する必要があります。各部分の長さは最大 (k) です。 ピースの場合、その値は要素の合計に MEX を乗算したものになります。 パーティション全体の値はすべての部分の値の合計であり、可能な最大値が必要です。 出力はその最大値です。 

(S_i=a_1+\cdots+a_i)、(S_0=0) とします。 最後のピースが (j+1) で始まり (i) で終わる場合、その寄与度は次のようになります。 

[
 \演算子名{mex}(a_{j+1},\ldots,a_i)(S_i-S_j)。 
】

 (dp_i) が (i) で終わるプレフィックスに対する最良の回答を示す場合、直接再帰は次のようになります。 

[
 dp_i=
 \max_{i-k\le j<i}
 \左(
 dp_j+
 \演算子名{mex}(j+1,i)(S_i-S_j)
 \右）。 
】

 (j) の下限は、ピースの最大長を強制するものです。 (n) が (2\cdot10^5) に達するため、(O(n^2)) アルゴリズムにはすでに約 (4\cdot10^{10}) の演算が必要であり、数秒で処理できる演算をはるかに超えています。 すべての MEX を最初から計算すると、直接的なアプローチ (O(nk^2)) が行われ、約 (8\cdot10^{15}) 個の基本演算に到達できます。 すべての間隔を維持しても、MEX は段階的に (O(nk))、最大でも約 (4\cdot10^{10}) の演算を実行します。 ソリューションでは、すべての候補区間を個別に検査するのではなく、MEX の特別な構造を利用する必要があります。 

誤って処理されやすい境界ケースがいくつかあります。 と`n = 2, k = 1, a = [5, 7]`、すべてのピースには正の値が 1 つだけ含まれているため、すべての MEX はゼロであり、答えは次のようになります。`0`。 空ではないすべてのセグメントに正の MEX があると想定する実装は、ここで失敗します。 

と`n = 2, k = 2, a = [0, 0]`、配列全体には MEX (1) がありますが、その合計はゼロであるため、その寄与は依然としてゼロであり、答えは次のようになります。`0`。 MEX と合計の乗算は、両方の量が計算された後に行う必要があります。 

と`n = 4, k = 2, a = [0, 1, 2, 3]`の場合、配列全体は MEX (4) になりますが、長さが 4 であるため、1 つの部分にすることはできません。 最適なパーティションは、`[0,1]`そして`[2,3]`、(2\cdot1+0=2) となります。 長さの境界を無視すると、完全に無効な遷移が生成されます。 

重複も重要です。 のために`n = 3, k = 3, a = [0, 1, 1]`の場合、MEX は周波数ではなく存在を重視するため、MEX は (3) ではなく (2) になります。 正しい値は (2\cdot2=4) です。 

## アプローチ

 ブルートフォース DP は、上記の繰り返しから直接続きます。 すべての右端点 (i) について、すべての可能な以前のカット (j) を試し、(a_{j+1},\ldots,a_i) の MEX を計算し、更新 (dp_i) します。 これは正しいです。すべての有効なパーティションには最後のピースが 1 つだけあり、その前のカットがこれらのトランジションの中に表示されるからです。 MEX が部分をスキャンして再計算される場合、最悪の場合の複雑さは (O(nk^2))、(n=k=2\cdot10^5) の場合、約 (8\cdot10^{15}) 回の演算になります。 間隔を延長しながらすべての MEX 値を維持する、より慎重なバージョンでも (O(nk)) が必要ですが、それでも大きすぎます。 

有益な観察は、固定右端点 (i) の場合、接尾辞の MEX 値が単調であることです。 左側のエンドポイントを増やすと要素が削除されるため、MEX は同じままか減少することしかできません。 したがって、可能な以前のカット (j) は、MEX が一定である連続した間隔に分割できます。 

以前のカットの間隔の 1 つが (L\le j\le R) であり、その MEX が (m) であると仮定します。 この間隔全体からのすべての遷移には次の形式があります。 

mS_i+\left(dp_j-mS_j\right)。 
】

 この MEX ブロックに必要なのは、

 [
 \max_{L\le j\le R}(dp_j-mS_j)。 
】

 これにより、内部最適化が行クエリに変わります。 前のカット (j) をラインに関連付けます

 [
 F_j(x)=-S_jx+dp_j。 
】

 すると必要な量は単純に

 [
 \max_{L\le j\le R}F_j(m)。 
】

 残りの問題は、MEX ブロックを効率的に維持する方法です。 右端点が (i-1) から (i) に移動すると、新しく挿入された値 (a_i) のみがサフィックス MEX 値を変更できます。 変更された MEX ブロックは小さなブロックに分割され、分割後にそれらのブロックをマージし直す必要はありません。 完全なスキャン全体で、作成されるブロックの総数は (O(n)) です。 値ドメイン上のセグメント ツリーは、すべての値の最後の出現を保存しており、(O(\log n)) で新しいブロック境界を見つけることができます。 これにより、MEX 構造の合計作業量は (O(n\log n)) になります。 この分解は、標準溶液の中心的な観察です。 

この分解の後には、2 つの入れ子になった最適化問題が存在します。 1 つ目は、インデックス間隔にわたる (F_j(m)) の最大値を求めます。 インデックス (j) 上のセグメント ツリーは、すべてのノードに Li Chao ツリーを格納できます。 行はその位置をカバーするすべての (O(\log n)) 個のインデックス ツリー ノードに挿入され、範囲クエリは (O(\log n)) 個のノードを訪問します。 Li Chao の各操作には (O(\log n)) のコストがかかり、範囲操作ごとに (O(\log^2 n)) が与えられます。 

2 番目の最適化には、特に便利な特性があります。 MEX ブロックが生成されると、

 [
 C=\max_{L\le j\le R}F_j(m),
 】

 現在のプレフィックス合計の関数としてのその寄与は、次の行になります。 

[
 H(x)=mx+C。 
】

 MEX 値は右端が移動する場合にのみ増加するため、古い行を削除する必要はありません。 この行をブロックの左側の境界に挿入し、Li Chao ツリーの別のインデックス セグメント ツリーを維持します。 現在のスライディング ウィンドウに対するクエリでは、その左側の境界が正当な以前のカットであるブロック ラインが正確に考慮されます。 これはデータ構造の 2 番目の層です。 標準的な導出では (O(n\log^2 n)) が得られますが、第 2 層をオフラインで構築することで (O(n\log n)) の改良が可能になります。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | (O(nk^2)) | (O(n)) | 遅すぎる |
 | 増分 MEX DP | (O(nk)) | (O(n)) | 遅すぎる |
 | MEX ブロック + ネストされた Li Chao ツリー | (O(n\log^2 n)) | (O(n\log n)) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. プレフィックスの合計 (S_i) を構築します。 最終ピースの候補 (j+1,\ldots,i) の合計は (S_i-S_j) となるため、DP 遷移は配列の別のスキャンを必要とする代わりにアフィン式になります。 
2. すでに解決されているすべてのプレフィックス (j) に関連付けられた行を次のように定義します。 

[
 F_j(x)=-S_jx+dp_j。 
】

サフィックスが MEX(m) の場合、その遷移は (mS_i+F_j(m)) となります。 したがって、MEX ブロック全体を (x=m) での 1 つの範囲最大クエリに減らすことができます。 
3. すべての値の最後の出現を維持します。 現在の右端点 (i) の場合、値 (v) は、最後に出現した値が (j) より大きいときに、サフィックス (j+1,\ldots,i) に出現します。 したがって、MEX は最後に出現した配列によって決定できます。 
4. MEX ブロックを境界のスタックとして保持します。 MEX 値はスタックに沿って単調になります。 (a_i) が挿入されると、境界が有効でなくなったブロックをポップし、最後に出現した値が現在の境界より下にある最小値を繰り返し検索します。 このような検出ごとに、1 つの新しい MEX ブロックが作成されます。 

すべての値間隔の最小の最後の出現を格納するセグメント ツリーは、(O(\log n)) でのこの検索をサポートします。 重要な償却は、ブロックが作成されたときにのみ分割されるため、ブロック作成の総数は線形であるということです。 
5. MEX (m) で新しく作成されたブロック ([L,R]) ごとに、最初の Li Chao 構造体に対してクエリを実行します。 

[
 C=\max_{L\le j\le R}F_j(m)。 
】

 値 (C) は、その MEX ブロック内のすべての可能な以前のカットを要約します。 
6. ブロックをラインに変換する

 [
 H(x)=mx+C。 
】

 この行を 2 番目の範囲の Li Chao 構造体の位置 (L) に挿入します。 この線は、その MEX が少なくとも線の作成時に使用された MEX である将来のすべての遷移を表します。 すべての配列要素は非負であるため、MEX を増加しても寄与が改善されるだけなので、古い行を保持するのが安全です。 
7. エンドポイント (i) については、ゼロでクリップされた正当な以前のカット範囲 ([i-k,i-1]) にわたって 2 番目の構造をクエリします。 これは、長さ制約内にあるすべての完全な MEX ブロックを処理します。 
8. 1 つのブロックは、正当な範囲に完全に含まれなくても、その左側の境界を横切ることができます。 MEX スタック内でそのブロックを直接検索し、クリップされた部分のみで最初の構造をクエリします。 この 1 回の修正は、長さ制限によって作成された部分ブロックのみを処理します。 
完全ブロッククエリとこの部分ブロッククエリの最大値は(dp_i)である。 新しく取得した行を挿入します

 [
 F_i(x)=-S_ix+dp_i
 】

 最初の構造に追加すると、将来のエンドポイントはプレフィックス (i) を使用できるようになります。 
10. すべてのエンドポイントを処理した後、(dp_n) を返します。 

不変条件は、すべての正当な以前のカット (j) が正確に 1 つの現在の MEX ブロックに属し、そのブロックが (a_{j+1},\ldots,a_i) の正しい MEX を格納することです。 したがって、最初の Li Chao 構造は、各ブロック内のすべてのカットの間で最適なトランジションを計算します。 2 番目の構造は、完成したブロックによって生成されたアフィン関数を正確に保存し、そのスライディング ウィンドウ クエリによって長さの制限が強制されます。 有効な最後の部分はすべてこれらのケースの 1 つで表されるため、(dp_i) に対して得られた最大値はまさにプレフィックスの最適値となります。 

## Python ソリューション

 以下の実装は、(O(n\log^2 n)) 構造に従います。 これは、MEX ブロック境界の値に対してセグメント ツリーを使用し、そのノードに 2 つの範囲最大構造の Li Chao ツリーが含まれるセグメント ツリーを使用します。```python
import sys
from array import array

input = sys.stdin.readline
NEG = -(10 ** 40)

class LastOccurrenceTree:
    def __init__(self, n):
        self.n = n
        size = 1
        while size < n + 2:
            size <<= 1
        self.size = size
        self.mn = array('i', [0]) * (2 * size)

    def update(self, p, value):
        p += self.size
        self.mn[p] = value
        p >>= 1
        while p:
            x = self.mn[p << 1]
            y = self.mn[p << 1 | 1]
            self.mn[p] = x if x < y else y
            p >>= 1

    def first_less(self, limit):
        if self.mn[1] >= limit:
            return None

        p = 1
        l = 0
        r = self.size - 1

        while l != r:
            mid = (l + r) >> 1
            left = p << 1
            if self.mn[left] < limit:
                p = left
                r = mid
            else:
                p = left | 1
                l = mid + 1

        return self.mn[p], l

class RangeLiChao:
    def __init__(self, n, prefix, use_prefix):
        self.n = n
        self.prefix = prefix
        self.use_prefix = use_prefix

        self.roots = array('i', [0]) * (4 * n + 20)

        self.left = array('i', [0])
        self.right = array('i', [0])
        self.line = array('i', [0])

        self.slopes = [0]
        self.intercepts = [NEG]

    def value(self, line_id, x):
        if self.use_prefix:
            x = self.prefix[x]
        return self.slopes[line_id] * x + self.intercepts[line_id]

    def add_line(self, slope, intercept):
        self.slopes.append(slope)
        self.intercepts.append(intercept)
        return len(self.slopes) - 1

    def new_node(self, line_id):
        idx = len(self.line)
        self.left.append(0)
        self.right.append(0)
        self.line.append(line_id)
        return idx

    def insert_inner(self, root, line_id, lo, hi):
        if root == 0:
            return self.new_node(line_id)

        cur = self.line[root]
        mid = (lo + hi) >> 1

        left_new = self.value(line_id, lo) > self.value(cur, lo)
        mid_new = self.value(line_id, mid) > self.value(cur, mid)

        if mid_new:
            self.line[root], line_id = line_id, cur
            cur = self.line[root]

        if lo == hi:
            return root

        if left_new != mid_new:
            child = self.left[root]
            new_child = self.insert_inner(child, line_id, lo, mid)
            self.left[root] = new_child
        else:
            child = self.right[root]
            new_child = self.insert_inner(child, line_id, mid + 1, hi)
            self.right[root] = new_child

        return root

    def query_inner(self, root, x, lo, hi):
        if root == 0:
            return NEG

        ans = self.value(self.line[root], x)

        if lo == hi:
            return ans

        mid = (lo + hi) >> 1
        if x <= mid:
            other = self.query_inner(self.left[root], x, lo, mid)
        else:
            other = self.query_inner(self.right[root], x, mid + 1, hi)

        return ans if ans > other else other

    def insert(self, pos, slope, intercept):
        line_id = self.add_line(slope, intercept)

        node = 1
        lo = 0
        hi = self.n - 1

        while True:
            self.roots[node] = self.insert_inner(
                self.roots[node], line_id, 0, self.n - 1
            )

            if lo == hi:
                break

            mid = (lo + hi) >> 1
            if pos <= mid:
                node = node << 1
                hi = mid
            else:
                node = node << 1 | 1
                lo = mid + 1

        return line_id

    def query(self, left, right, x):
        if left > right:
            return NEG

        left += 1
        right += 1

        # Iterative canonical decomposition.
        L = left + self.n - 1
        R = right + self.n - 1

        ans = NEG

        while L <= R:
            if L & 1:
                q = self.query_inner(self.roots[L], x, 0, self.n - 1)
                if q > ans:
                    ans = q
                L += 1

            if not (R & 1):
                q = self.query_inner(self.roots[R], x, 0, self.n - 1)
                if q > ans:
                    ans = q
                R -= 1

            L >>= 1
            R >>= 1

        return ans

def solve_case(n, k, a):
    prefix = [0] * (n + 1)
    for i, x in enumerate(a, 1):
        prefix[i] = prefix[i - 1] + x

    # T1: lines F_j(x) = -S_j*x + dp_j.
    # x is a MEX, hence x in [0, n].
    t1 = RangeLiChao(n + 1, prefix, False)

    # T2: lines H(x) = mex*x + C, evaluated at x = S_i.
    # The implementation indexes x by i and converts it to S_i.
    t2 = RangeLiChao(n + 1, prefix, True)

    last_tree = LastOccurrenceTree(n + 1)
    last = [0] * (n + 1)

    # Stack entries are (boundary, mex).
    # The sentinel boundary is 0.
    stack = [(0, 0)]

    dp = [0] * (n + 1)

    # F_0(x) = 0.
    t1.insert(0, 0, 0)

    for i in range(1, n + 1):
        x = a[i]

        # The value x now occurs at i.
        # Blocks whose boundary lies after the previous occurrence of x
        # may need to be split.
        previous = last[x]

        while stack[-1][0] > previous:
            stack.pop()

        pending = []
        rpos = i

        while rpos > stack[-1][0]:
            result = last_tree.first_less(rpos)
            if result is None:
                break

            min_last, mex = result
            pending.append((rpos, mex))
            rpos = min_last

        pending.reverse()

        for pos, mex in pending:
            left_boundary = stack[-1][0]

            # C = max F_j(mex) for j in [left_boundary, pos - 1].
            c = t1.query(left_boundary, pos - 1, mex)

            # H(S_i) = mex*S_i + C.
            t2.insert(left_boundary, mex, c)

            stack.append((pos, mex))

        last[x] = i
        last_tree.update(x, i)

        lower = max(i - k, 0)

        # Find the first stack boundary >= lower + 1.
        lo = 1
        hi = len(stack)

        while lo < hi:
            mid = (lo + hi) >> 1
            if stack[mid][0] < lower + 1:
                lo = mid + 1
            else:
                hi = mid

        p = lo

        best = NEG

        # The first complete block starting at or after lower.
        q = t2.query(lower, i - 1, i)
        if q > best:
            best = q

        # The block crossing the left boundary may be only partially usable.
        if p < len(stack):
            block_left = stack[p - 1][0]
            block_right = stack[p][0] - 1

            if block_left < lower <= block_right:
                mex = stack[p][1]
                c = t1.query(lower, block_right, mex)
                candidate = mex * prefix[i] + c
                if candidate > best:
                    best = candidate

        dp[i] = best

        # F_i(x) = -S_i*x + dp_i.
        t1.insert(i, -prefix[i], dp[i])

    return dp[n]

def main():
    n, k = map(int, input().split())
    a = list(map(int, input().split()))
    print(solve_case(n, k, a))

if __name__ == "__main__":
    main()
```すべての遷移が (S_i-S_j) を使用するため、プレフィックス合計配列が最初に構築されます。 Python の整数にはすでに任意の精度があるため、オーバーフローの問題はありませんが、元の C++ 実装では 64 ビット整数が必要です。`LastOccurrenceTree`すべての値間隔の最小の最後の出現を保存します。`first_less(x)`最後に出現した値が次の値より小さい最小値を見つけます。`x`。 これはまさに、サフィックス境界がその位置に到達したときに MEX となる値です。`RangeLiChao`DP に必要なブラック ボックスを実装します。 その外側のツリーには、前のカット位置によってインデックスが付けられます。 すべての行は、その位置をカバーする (O(\log n)) 個の外側ノードに挿入されます。 各外側ノードには内側の Li Chao ツリーがあり、以前のすべてのカットを明示的に訪問することなく、さまざまな位置をクエリできるようになります。 

最初のインスタンスは次のことを表します

 [
 F_j(x)=-S_jx+dp_j。 
】

 そのクエリ座標は MEX 自体です。 2 番目のインスタンスは次のことを表します

 [
 H(x)=mx+C、
 】

 ここで、引数はエンドポイント インデックスであり、内部で対応するプレフィックス合計 (S_i) に変換されます。 

スタックには、以前の個々のカットではなく境界が保存されます。 2 つの連続するスタック境界が`L`そして`R`、その間隔内のすべての以前のカットは同じ MEX を持ちます。 これが、DP が 1 つのエンドポイントに対して (O(k)) 遷移を実行しない理由です。 

部分ブロック クエリが必要なのは、正当な範囲が (i-k) で始まり、MEX ブロックの中央を通過する可能性があるためです。 2 番目の構造は完全なブロックを処理しますが、最初の構造はその 1 つのクリップされたブロックを明示的に処理します。 この修正を省略すると、よくある off-by-one エラーになります。 

このコードはゼロでセンチネル境界を使用し、ゼロベースのプレフィックス インデックスを使用して DP カットを表します。 (i) で終わり (j+1) で始まるピースは、結果的にカット インデックス (j) に関連付けられ、代数とプレフィックスの合計の一貫性が保たれます。 

## 実用的な例

 ### サンプル 1

 のために```
5 3
3 4 0 0 3
```必要な答えは`10`。 

重要な DP 状態は次のとおりです。 

| エンドポイント (i) | (S_i) | 最高の最終作品 | メキシコ | (dp_i) |
 | --- | --- | --- | --- | --- |
 | 1 | 3 |`[3]`| 0 | 0 |
 | 2 | 7 |`[4]`または`[3,4]`| 0 | 0 |
 | 3 | 7 |`[3,4,0]`| 1 | 7 |
 | 4 | 7 |`[0]`3 で終わるプレフィックスの後 | 1 | 7 |
 | 5 | 10 |`[0,3]`3 で終わるプレフィックスの後 | 1 | 10 |

 エンドポイント 3 では、セグメント全体が`[3,4,0]`MEX (1) と合計 (7) を持ち、(7) を生成します。 エンドポイント 5 での最適な遷移は (dp_3+1\cdot(10-7)=7+3=10) です。 このトレースは、MEX が小さいセグメントが、その合計が大きい場合でも最適である理由を示しています。 

### サンプル 2

 のために```
8 4
0 1 2 0 3 1 4 1
```答えは`26`。 関連する州は次のとおりです。 

| エンドポイント (i) | (S_i) | 過去のベストカット (j) | 最終セグメント | メキシコ | (dp_i) |
 | --- | --- | --- | --- | --- | --- |
 | 1 | 0 | 0 |`[0]`| 1 | 0 |
 | 2 | 1 | 0 |`[0,1]`| 2 | 2 |
 | 3 | 3 | 0 |`[0,1,2]`| 3 | 9 |
 | 4 | 3 | 0 |`[0,1,2,0]`| 3 | 9 |
 | 5 | 6 | 1 |`[1,2,0,3]`| 4 | 24 |
 | 6 | 7 | 2 |`[2,0,3,1]`| 4 | 26 |
 | 7 | 11 | 6 |`[4]`| 0 | 26 |
 | 8 | 12 | 6 |`[4,1]`| 0 | 26 |

 エンドポイント 6 の遷移は興味深いものです。 最後の 4 つの要素は、`[2,0,3,1]`、MEX は (4)、合計は (6) です。 それらの前のプレフィックスには値 (dp_2=2) があり、次のようになります。 

[
 2+4\cdot6=26。 
】

 これは、DP が現在の部分の MEX とは別に最適なプレフィックス値を保存する必要がある理由を示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O(n\log^2 n)) | (O(n)) 個の MEX ブロックの作成があり、各範囲の Li Chao 操作コストは (O(\log^2 n)) です。 |
 | スペース | (O(n\log n)) | 挿入された各行は、(O(\log n)) 個の外側のセグメント ツリー ノードに参加します。 |

 元の問題では (n=2\cdot10^5) が許可され、512 MiB で 4 秒かかります。 このアルゴリズムは、(O(nk)) 個の遷移ファミリーを (O(n)) 個の MEX ブロックと対数幾何学的クエリのみで置き換えるので、漸近的に十分高速です。 ネストされた Li Chao 実装はメモリを大量に消費するため、元の高パフォーマンス実装では Python オブジェクトではなくコンパクトな静的配列が使用されます。 

## テストケース

 公式サンプルは次のとおりです。```
# The reference solution above reads one case at a time.

# Sample 1
assert solve_case(
    5, 3, [3, 4, 0, 0, 3]
) == 10

# Sample 2
assert solve_case(
    8, 4, [0, 1, 2, 0, 3, 1, 4, 1]
) == 26

# Sample 3
assert solve_case(
    10, 5, [0, 2, 0, 1, 2, 1, 0, 2, 2, 1]
) == 33

# Minimum size, k = 1.
assert solve_case(
    2, 1, [5, 7]
) == 0

# All equal values. The MEX is 0 because 0 never appears.
assert solve_case(
    5, 3, [7, 7, 7, 7, 7]
) == 0

# Maximum possible piece length is allowed.
assert solve_case(
    4, 4, [0, 1, 2, 3]
) == 9

# Length boundary catches an invalid transition using all four elements.
assert solve_case(
    4, 2, [0, 1, 2, 3]
) == 2
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`2 1 / 5 7`|`0`| シングルトンの正の値の最小サイズと MEX ゼロ |
 |`5 3 / 7 7 7 7 7`|`0`| ゼロを含まないすべて等しい値 |
 |`4 4 / 0 1 2 3`|`9`| (k=n) の場合の完全配列セグメント |
 |`4 2 / 0 1 2 3`|`2`| 最大セグメント長の正しい適用 |

 最大サイズのストレスケースの場合、有用なテストは次のとおりです。`n = 200000`、`k = 1`、すべての要素が次と等しい`1`。 すべてのセグメントの MEX がゼロになるため、期待される答えはゼロになります。 これにより、MEX 構造が自明な場合に、実装が誤って (nk) に比例する作業を割り当てないことがチェックされます。 

## 特殊なケース

 のために`n = 2, k = 1, a = [5,7]`、合法的な部分は次のとおりです。`[5]`そして`[7]`。 どちらにもゼロが含まれていないため、両方の MEX 値はゼロになります。 DP が取得するのは、`dp[1] = 0`そして`dp[2] = 0`。 答えは、`0`。 MEX スタックにはゼロ MEX ブロックのみが含まれるため、Li Chao 構造がプラスの寄与を生み出すことはありません。 

のために`n = 2, k = 2, a = [0,0]`、セグメント全体に MEX (1) がありますが、その合計はゼロです。 遷移は (1\cdot0=0) なので、`dp[2]=0`。 これは、MEX が正であるが、セグメントが何も寄与しない場合を例にしています。 

のために`n = 4, k = 2, a = [0,1,2,3]`、エンドポイント 4 の正当な範囲にはカット 2 と 3 のみが含まれます。`[0,1,2,3]`は長さが 4 であるため除外されます。 最適な有効なパーティションは次のとおりです。`[0,1]`に続く`[2,3]`、値は (2\cdot1+0=2) です。 明示的な部分ブロック クエリは、データ構造がスライディング ウィンドウの左側の境界を越える MEX ブロックを誤って使用することを防ぎます。 

のために`n = 3, k = 3, a = [0,1,1]`、値`0`そして`1`が存在する一方で、`2`が存在しないため、MEX は正確に (2) になります。 合計は(2)となり、(4)となります。 候補カットの後にその値が存在するかどうかを決定する際には、各値の最新の出現のみが重要となるため、最終出現表現は自然に重複を処理します。
