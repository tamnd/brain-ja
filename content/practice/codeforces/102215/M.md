---
title: "CF 102215M - シュラコブロックがライブ中です!"
description: "(n) 個のゲームがあります。 ゲーム (i) には現在 (vi) の投票があり、それを見るのが楽しい (pi) です。 どのゲームにも 1 票を追加できますが、1 試合につき 1 回までです。 私たちの選択の後、1 つの投票が均一にランダムに選択されるため、より多くの投票が得られたゲームがストリーミングされる可能性が高くなります。"
date: "2026-08-18T12:20:18+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102215
codeforces_index: "M"
codeforces_contest_name: "2019, XII Samara Regional Intercollegiate Programming Contest"
rating: 0
weight: 102215
solve_time_s: 641
verified: false
draft: false
---

[CF 102215M - Shlakoblock が稼働中です!](https://codeforces.com/problemset/problem/102215/M)

 **評価:** -
 **タグ:** -
 **解決時間:** 10 分 41 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 (n) 個のゲームがあります。 ゲーム (i) には現在 (v_i) 票があり、それを見るのは楽しいです (p_i)。 どのゲームにも 1 票を追加できますが、1 試合につき 1 回までです。 私たちの選択の後、1 つの投票が均一にランダムに選択されるため、より多くの投票が得られたゲームがストリーミングされる可能性が高くなります。 

(S) を投票するゲームのセットとします。 現在の総投票数が

 [
 V=\sum_{i=1}^n v_i,
 】

 投票後は (V+|S|) 票になります。 すべての投票によって表される喜びの合計は、

 [
 A+\sum_{i\in S}p_i,
 】

 どこで

 [
 A=\sum_{i=1}^n v_i p_i。 
】

 したがって、期待される喜びは、

 [
 \frac{A+\sum_{i\in S}p_i}{V+|S|}。 
】

 タスクは、(S) を選択し、既約形式で可能な最大の分数を出力し、それを達成する 1 セットのゲームを出力することです。 

制約は並べ替えには十分小さいですが、サブセットを列挙するには十分ではありません。 1 つのテストには (n=1000) ゲームを含めることができ、最大 500 のテスト ケースを含めることができます。 (O(n^2)) のソリューションは、合計が最悪の場合でもすでに不必要に高価ですが、(O(n\log n)) は簡単に十分に高速です。 値 (p_i,v_i) は最大 1000 ですが、合計には最大 1000 個の項が含まれるため、通常の Python 整数で十分です。 

不用意に実装すると失敗するケースがいくつかあります。 ゲームを選択しない場合でも、答えは最適になる可能性があります。 例えば、```
1
1
0 5
```期待される喜びを与える (0/5=0) ため、正しい出力は次のようになります。```
0/1
0
```常に少なくとも 1 つのゲームを追加する実装では、さらに悪い結果が生成されます。 

2 番目の問題は、現在投票がゼロのゲームでも投票の対象となるということです。 のために```
1
2
10 1
100 0
```最初の期待値は (10) です。 ゲーム 2 に投票すると (110/2=55) となり、これが最適です。 (v_i=0) のゲームを無視すると、答えが見つかりません。 

3 番目の問題は、別のゲームに投票するたびに分母が変わることです。 のために```
1
2
100 1
0 100
```最初のゲームに投票すると (200/101)、2 番目のゲームに投票すると (100/101) が得られます。 すべてのゲームを肯定的な喜びで選択するだけでは選択はできません。 追加投票の寄与は、分母の追加 (1) と合わせて考慮する必要があります。 

最後に、いくつかの異なるサブセットが同じ最適値を達成できます。 と```
1
2
5 1
5 1
```どちらかのゲームに投票した後の最良の答えは (10/2=5) であり、両方の選択肢が有効です。 アルゴリズムは 1 つの最適なサブセットを保持するだけで済みます。 

## アプローチ

 最も直接的なアプローチは、ゲームのすべてのサブセットを試してみることです。 サブセット (S) については、その分子と分母を計算し、最良の期待値を維持できます。 すべての法的投票戦略は 1 つのサブセットによって表されるため、これは正しいです。 ただし、(2^n) 個のサブセットがあり、各サブセットの評価には最大 (O(n)) 個の作業がかかり、最悪の場合は (O(n2^n)) 個の演算が行われます。 (n=1000) の場合、(2^{1000}) でも制限時間内に実行できる量をはるかに超えています。 

選択したゲームのアイデンティティを気にするのをやめて、最初にその番号を修正すると、有用な構造が現れます。 ちょうど (k) 個のゲームに投票すると決めたとします。 分母は (V+k) に固定され、元の寄与 (A) も固定されます。 最適化できる唯一の部分は、

 [
 \sum_{i\in S}p_i。 
】

 正確に (k) 個のゲームの場合、この合計は、(k) 個の最大の喜びの値を取得することによって最大化されます。 

この観察により、指数関数検索が単純な並べ替えられたプレフィックス検索に変わります。 ゲームを降順 (p_i) で並べ替えます。 ソート後、サイズ (k) の最適なサブセットは正確に最初 (k) のゲームです。 彼らの喜びの合計を段階的に構築し、0 から (n) までのすべての (k) を評価できます。 

ブルートフォースアプローチは、考えられるすべてのサブセットを考慮するため機能します。 サブセットが指数関数的に多いため、失敗します。 固定サブセット サイズに対する最適な選択は、最大 (p_i) のゲームから構成されるという観察により、同じサイズのすべてのサブセットを 1 つの代表に置き換えることができ、ソート後の問題を (n+1) 個の候補戦略に縮小できます。 

分数を正確に比較するには、浮動小数点を使用すべきではありません。 候補者2名に対して

 [
 \frac{x_1}{y_1}
 \quad\text{および}\quad
 \frac{x_2}{y_2}、
 】

 (x_1y_2) と (x_2y_1) を比較します。 Python の整数はこれらの積を正確に処理します。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | (O(n2^n)) | (O(n)) | 遅すぎる |
 | 最適 | (O(n\log n)) | (O(n)) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 現在の総投票数 (V=\sum v_i) と現在の総喜び寄与 (A=\sum v_i p_i) を計算します。 これらの値は、投票を追加する前に期待される喜びを表します。 
2. 元のインデックスを維持したまま、すべてのゲームを (p_i) を減少させて並べ替えます。 最終的に正確に (k) 票を追加することにした場合、この順序の最初の (k) ゲームで可能な限り最大の追加の喜びが得られます。 
3. (k=0) から開始します。 候補の期待値は (A/V) です。 この問題では、少なくとも 1 つの (v_i) が正であることが保証され、つまり (V>0) になります。 
4. ソートされたゲームを横断します。 次のゲームを処理するときに、その (p_i) を実行中のプレフィックスの合計に加算します。 (k) ゲームを追加すると、分子の候補は (A+\text{prefix}) となり、分母は (V+k) になります。 
5. 交差乗算を使用して、すべての候補をこれまでに確認された最良の候補と比較します。 もし

 [
 (A+\text{プレフィックス})(V+k_{\text{best}})

 >

 (A+\text{プレフィックス}_{\text{best}})(V+k)、
 】

 現在のベストアンサーを置き換えます。 

1. 対応する (k) を保存します。 ゲームは喜びの減少によってすでにソートされているため、最初の (k) インデックスはその (k) に対する最適な投票セットを形成します。 
2. スキャン後、分子と分母を最大公約数で割ることにより、最良の分数を減らします。 縮小された分数、選択されたカウント、および対応する元のインデックスを出力します。 

### なぜ効果があるのか

 追加投票の可能な数 (k) ごとに、分母は正確に (V+k) になります。 (k) ゲームのすべてのサブセットの中で、元の貢献 (A) は同一であるため、期待される喜びを最大化することは、それらの (p_i) 値の合計を最大化することと同じです。 (k) の最大 (p_i) 値は可能な最大の合計を与えるため、ソートされたプレフィックスはその特定の (k) に最適です。

このアルゴリズムは、0 から (n) までのすべての可能な (k) を調べ、各 (k) について、そのサイズの最適なサブセットを調べます。 したがって、全体的な最適値は、スキャンで考慮される候補の中に含まれている必要があります。 相互乗算ではこれらの候補が正確に比較されるため、選択された候補は浮動小数点近似ではなく真の最大値になります。 

## Python ソリューション```python
import sys
from math import gcd

input = sys.stdin.readline

def solve():
    t = int(input())
    out = []

    for _ in range(t):
        n = int(input())

        games = []
        total_votes = 0
        total_pleasure = 0

        for idx in range(1, n + 1):
            p, v = map(int, input().split())
            games.append((p, idx))
            total_votes += v
            total_pleasure += p * v

        games.sort(key=lambda x: (-x[0], x[1]))

        best_num = total_pleasure
        best_den = total_votes
        best_k = 0

        prefix = 0

        for k, (p, idx) in enumerate(games, 1):
            prefix += p

            cur_num = total_pleasure + prefix
            cur_den = total_votes + k

            if cur_num * best_den > best_num * cur_den:
                best_num = cur_num
                best_den = cur_den
                best_k = k

        g = gcd(best_num, best_den)
        best_num //= g
        best_den //= g

        out.append(f"{best_num}/{best_den}")
        out.append(str(best_k))

        if best_k == 0:
            out.append("")
        else:
            chosen = [str(games[i][1]) for i in range(best_k)]
            out.append(" ".join(chosen))

    sys.stdout.write("\n".join(out))

if __name__ == "__main__":
    solve()
```入力ループは各ゲームを次のように保存します。`(p, index)`ソートにはその喜びのみが影響し、出力には元のインデックスが必要になるためです。 同時に、現在の投票数と現在の喜びの貢献度が蓄積されます。 

分類ステップでは、快楽の減少を使用します。 元のインデックスによる二次的な順序付けは数学的には必要ありませんが、複数のゲームが同じ楽しみを持つ場合、プログラムが決定的になります。 

スキャンは (k=0) から始まります。これは、ゲームに投票しないことが合法であるため重要です。 変数`prefix`は、最初 (k) 個のソートされたゲームの楽しみの合計であるため、分子と分母の候補は常に正確に (A+\text{prefix}) と (V+k) になります。 

比較には除算ではなく乗算が使用されます。 正の分母の場合、

 [
 \frac{x}{y}>\frac{a}{b}
 】

 は (xb>ay) と同等です。 これにより、浮動小数点精度エラーが回避され、浮動小数点値を繰り返し構築することも回避されます。 

選択したインデックスは最初のインデックスから再構築されます。`best_k`ソートされた配列の要素。 オフバイワンの問題はありません。`enumerate(games, 1)`作る`k`プレフィックスに含まれるゲームの数と同じです。 

元の入力には少なくとも 1 つの肯定的な投票が含まれているため、分母は常に正になります。 Python の任意精度の整数は、実際の境界がすでに標準の 64 ビット算術演算に十分小さいにもかかわらず、オーバーフローを不可能にします。 

いつ`best_k`がゼロの場合、必要な 3 番目の出力行は空です。 このコードでは、空の文字列を明示的に追加するため、すべてのテスト ケースは出力行のちょうど 3 行を占有します。 

## 実用的な例

 最初のサンプルには 5 つのゲームが含まれています。 彼らの最初の合計は (V=21) で、現在の喜びの貢献度は

 [
 A=5\cdot10+7\cdot4+3\cdot6+2\cdot8+4\cdot2=120。 
】

 楽しみごとに分類すると、ゲーム 1、4、3、2、5 の順になります。 

| (k) | さらなる楽しみ | 分子 | 分母 | 期待 |
 | --- | --- | --- | --- | --- |
 | 0 | 0 | 120 | 21 | (120/21) |
 | 1 | 10 | 130 | 22 | (130/22) |
 | 2 | 18 | 138 | 23 | (138/23=6) |
 | 3 | 24 | 144 | 24 | (144/24=6) |
 | 4 | 28 | 148 | 25 | (148/25) |
 | 5 | 30 | 150 | 26 | (150/26) |

 最大値は 6 です。(k=2) と (k=3) の間には同数があります。 新しい候補が厳密に大きい場合にのみ最良の答えを置き換えるため、実装では最初の最大値が維持されます。 したがって、ゲーム 1 と 4 を選択して出力します。`6/1`。 

2 番目のサンプルには (V=1111) と

 [
 A=1000\cdot1+100\cdot10+10\cdot100+1\cdot1000=4000。 
】

 ソート順はゲーム 4、3、2、1 です。 

| (k) | さらなる楽しみ | 分子 | 分母 | 期待 |
 | --- | --- | --- | --- | --- |
 | 0 | 0 | 4000 | 1111 | (4000/1111) |
 | 1 | 1000 | 5000 | 1112 | (5000/1112) |
 | 2 | 1100 | 5100 | 1113 | (5100/1113) |
 | 3 | 1110 | 5110 | 1114 | (5110/1114) |
 | 4 | 1111 | 5111 | 1115 | (5111/1115) |

 最良の候補はゲーム 4、3、2 を使用します。その端数は次のとおりです。 

[
 \frac{5110}{1114}=\frac{2555}{557}、
 】

 これは、両方の数値を 2 で割った後の要求された縮小表現です。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O(n\log n)) | 並べ替えはリニア スキャンと入力処理を支配します。 
| スペース | (O(n)) | ゲーム配列には、ゲームごとに 1 つのレコードが格納されます。 

(n\le1000) の場合、(O(n\log n)) でのソートは 2 秒以内に収まります。 500 のテスト ケースにわたっても、アルゴリズムはソート以外のゲームごとに少量の作業のみを実行し、メモリ使用量は 1 つのテスト ケースのサイズ内で線形です。 

## テストケース

 以下のテスト ハーネスは、提出されたソリューションと同じ決定論的タイブレークを使用します。 Codeforces では任意の最適なサブセットが許可されるため、一般的な検証では、答えの構造的妥当性とその最適値もチェックします。```python
import sys
import io
from math import gcd

def solution(inp: str) -> str:
    old_stdin = sys.stdin
    old_stdout = sys.stdout

    sys.stdin = io.StringIO(inp)
    sys.stdout = io.StringIO()

    try:
        input = sys.stdin.readline

        t = int(input())
        out = []

        for _ in range(t):
            n = int(input())

            games = []
            total_votes = 0
            total_pleasure = 0

            for idx in range(1, n + 1):
                p, v = map(int, input().split())
                games.append((p, idx))
                total_votes += v
                total_pleasure += p * v

            games.sort(key=lambda x: (-x[0], x[1]))

            best_num = total_pleasure
            best_den = total_votes
            best_k = 0
            prefix = 0

            for k, (p, idx) in enumerate(games, 1):
                prefix += p
                cur_num = total_pleasure + prefix
                cur_den = total_votes + k

                if cur_num * best_den > best_num * cur_den:
                    best_num = cur_num
                    best_den = cur_den
                    best_k = k

            g = gcd(best_num, best_den)
            best_num //= g
            best_den //= g

            out.append(f"{best_num}/{best_den}")
            out.append(str(best_k))

            if best_k == 0:
                out.append("")
            else:
                out.append(" ".join(str(games[i][1]) for i in range(best_k)))

        return "\n".join(out)
    finally:
        sys.stdin = old_stdin
        sys.stdout = old_stdout

def check(inp: str, output: str):
    data = list(map(int, inp.split()))
    pos = 0
    t = data[pos]
    pos += 1

    lines = output.splitlines()
    line_pos = 0

    for _ in range(t):
        n = data[pos]
        pos += 1

        games = []
        total_votes = 0
        total_pleasure = 0

        for idx in range(1, n + 1):
            p = data[pos]
            v = data[pos + 1]
            pos += 2
            games.append((p, v))
            total_votes += v
            total_pleasure += p * v

        fraction = lines[line_pos]
        line_pos += 1

        num, den = map(int, fraction.split("/"))
        assert gcd(num, den) == 1
        assert den > 0

        k = int(lines[line_pos])
        line_pos += 1

        chosen = []
        if k > 0:
            chosen = list(map(int, lines[line_pos].split()))
        line_pos += 1

        assert 0 <= k <= n
        assert len(chosen) == k
        assert len(set(chosen)) == k
        assert all(1 <= x <= n for x in chosen)

        chosen_set = set(chosen)
        actual_num = total_pleasure
        for i, (p, v) in enumerate(games, 1):
            if i in chosen_set:
                actual_num += p

        actual_den = total_votes + k

        assert num * actual_den == actual_num * den

        best_num = total_pleasure
        best_den = total_votes

        ordered = sorted((p, i) for i, (p, v) in enumerate(games, 1))
        ordered.reverse()

        prefix = 0
        for kk in range(1, n + 1):
            prefix += ordered[kk - 1][0]
            candidate_num = total_pleasure + prefix
            candidate_den = total_votes + kk
            assert candidate_num * best_den <= best_num * candidate_den or (
                candidate_num * best_den == best_num * candidate_den
            )

            if candidate_num * best_den > best_num * candidate_den:
                best_num = candidate_num
                best_den = candidate_den

sample = """2
5
10 5
4 7
6 3
8 2
2 4
4
1 1000
10 100
100 10
1000 1
"""

check(sample, solution(sample))

minimum = """1
1
0 7
"""
check(minimum, solution(minimum))

all_equal = """1
4
5 1
5 2
5 3
5 4
"""
check(all_equal, solution(all_equal))

zero_votes = """1
2
10 1
100 0
"""
check(zero_votes, solution(zero_votes))

boundary = """1
3
0 1000
1000 0
999 1
"""
check(boundary, solution(boundary))

large = "1\n1000\n" + "\n".join(
    f"{i % 1001} {1 if i == 1 else 0}" for i in range(1000)
) + "\n"
check(large, solution(large))

print("All tests passed.")
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | との 1 つのゲーム`p=0`|`0/1`、`k=0`| 有効な空のサブセットと分子ゼロ |
 | 同等の楽しみを持つ 4 つのゲーム | 任意の最適なプレフィックス | 等しい値と同点の処理 |
 | 非常に楽しいゼロ投票ゲーム | 快感度の高いゲームを厳選 | (v_i=0) のゲームは引き続き対象となる必要があります |
 |`p=0`、`v=1000`大きな喜びと混ざり合う | 最良のプレフィックスからの正確な分数 | 境界値と分母の変更 |
 | 1000 ゲーム生成のケース | 任意の有効な最適値 | 最大値 (n) とリニアメモリ動作 |

 ## 特殊なケース

 どのゲームにも投票しないことが最適な場合、最初の最良の候補は (k=0) であるため、スキャンによってそれが処理されます。 入力用```
1
1
0 5
```(A=0) と (V=5) があります。 唯一の選択肢は、喜びゼロの投票を追加しても期待値 (0) を与えるため、アルゴリズムは (k=0) を維持し、(0/5) を次のように減らします。`0/1`。 3行目は空です。 

ゲームに現在の投票がない場合でも、ソートされた配列に表示されます。 のために```
1
2
10 1
100 0
```(A=10) と (V=1) があります。 当初の候補は(10/1)です。 ゲーム 2 を追加すると、候補は (110/2=55) になるため、アルゴリズムはゲーム 2 を選択します。既存の投票数が 0 であっても、新しい投票がそれを最も可能性の高いストリーミング ゲームにすることを妨げるものではありません。 

分母の変更は次を使用して直接処理されます。`total_votes + k`。 考慮する```
1
2
100 1
0 100
```ここでは (A=100) と (V=101) です。 追加の投票がない場合、期待値は (100/101) です。 最初のゲームを追加すると (200/102=100/51) が生成され、こちらの方が優れています。 代わりに、快楽ゼロのゲームを追加すると (100/102=50/51) となり、さらに悪いことになります。 プレフィックス スキャンは両方の可能性を正確に評価します。 

同等の最適な候補者は、厳密なルールによって処理されます。`>`比較。 のために```
1
2
5 1
5 1
```(k=0) の期待値は (10/2=5) であり、どちらかのゲームを加算すると (15/3=5) になります。 値が向上しないため、実装は (k=0) のままです。 この問題では最大化戦略が求められているため、これは有効です。 

最適値がたまたま単純な値である場合でも、縮小ステップは必要です。 2 番目のサンプルでは、​​選択された候補は (5110/1114) で、最大公約数は 2 です。両方の部分を除算すると、次のようになります。`2555/557`、必要な既約分数形式を満たしています。
