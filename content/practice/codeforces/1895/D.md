---
title: "CF 1895D - XOR 構造"
description: "未知の順列の連続する要素間の XOR 値が与えられます。 必要な順列を $b$ とします。 すべての隣接するペアについて、$$bi oplus b{i+1} = ai.$$ がわかります。配列 $b$ には、$0$ から $n-1$ までのすべての整数が 1 回だけ含まれていなければなりません。"
date: "2026-06-08T21:46:38+07:00"
tags: ["codeforces", "competitive-programming", "bitmasks", "constructive-algorithms", "data-structures", "math", "string-suffix-structures", "trees"]
categories: ["algorithms"]
codeforces_contest: 1895
codeforces_index: "D"
codeforces_contest_name: "Educational Codeforces Round 157 (Rated for Div. 2)"
rating: 1900
weight: 1895
solve_time_s: 169
verified: true
draft: false
---

[CF 1895D - XOR 構築](https://codeforces.com/problemset/problem/1895/D)

 **評価:** 1900
 **タグ:** ビットマスク、構成的アルゴリズム、データ構造、数学、文字列サフィックス構造、ツリー
 **解決時間:** 2 分 49 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 未知の順列の連続する要素間の XOR 値が与えられます。 

必要な順列を次のようにします$b$。 隣接するすべてのペアについて、次のことがわかります。$$b_i \oplus b_{i+1} = a_i.$$配列$b$以下のすべての整数を含める必要があります$0$に$n-1$まさに一度。 入力により、そのような順列が少なくとも 1 つ存在することが保証されます。 

最初の観察は、かつて$b_1$が固定されると、他のすべての要素が決定されます。 から$$b_2 = b_1 \oplus a_1,$$その後$$b_3 = b_2 \oplus a_2,$$等々。 配列全体は、単一の未知の開始値の関数です。 

制約$n \le 2 \cdot 10^5$二次関数はすぐに除外されます。 アン$O(n^2)$検証プロセスには約$4 \cdot 10^{10}$制限時間を大幅に超えた最悪の場合の作戦。 期待すべきことは、$O(n)$または$O(n \log n)$解決。 

難しいのは、次の正しい値を見つけることです。$b_1$。 単純な実装では、考えられるすべての開始値を試し、結果のシーケンスが順列であるかどうかをチェックする可能性があります。 これは概念的には機能しますが、非常に遅くなります。 

いくつかの特殊なケースは、誤って処理されやすいものです。 

考慮する：```
n = 2
a = [1]
```有効な置換は次のいずれかです$[0,1]$または$[1,0]$。 を想定した解決策$b_1=0$検証を行わないと、実際の開始値が異なる場合は常に失敗します。 

考慮する：```
n = 4
a = [2,1,2]
```私たちが選ぶなら$b_1=0$を取得します。```
0 2 3 1
```それは有効です。 しかし、多くの入力において、$0$重複した値が作成されます。 生成された数値が狭い範囲内に留まるという事実は、それらが順列を形成することを意味するものではありません。 

XOR プレフィックスが繰り返される場合、別の微妙なケースが発生します。```
prefixes = [0, 5, 2, 5]
```不用意に議論すると、重複は避けられないと結論付けられる可能性があります。 実際には、適切な XOR 値によってすべてのプレフィックスをシフトすると、すべての数値が同時に変更され、重複は選択した開始値に依存します。 正しい解決策は、個々の位置ではなく、値のセット全体について推論する必要があります。 

## アプローチ

 ブルートフォースのアイデアは、修正が行われるという観察から直接得られます。$b_1$順列全体を決定します。 

プレフィックス XOR を定義します。$$p_1 = 0,$$そして$$p_i = a_1 \oplus a_2 \oplus \cdots \oplus a_{i-1}.$$すると、すべての要素が満たされます$$b_i = b_1 \oplus p_i.$$考えられるすべての値を試してみると、$b_1$から$0$に$n-1$、すべてを生成できます$n$値を調べ、それらが正確にセットを形成しているかどうかを確認します$\{0,1,\dots,n-1\}$。 

すべての有効な順列はこれらの開始値の 1 つから生じる必要があるため、この推論は正しいです。 問題は複雑さです。 がある$n$の候補者$b_1$、それぞれの検証にかかる費用$O(n)$、につながる$O(n^2)$。 

重要な観察は、入力には解決策があることが保証されていることです。 考えられるすべての開始をテストする代わりに、正しい開始を直接導き出すことができます。 

させて$$p_1,p_2,\dots,p_n$$プレフィックス XOR 値になる$p_1=0$。 

以来$$b_i=b_1\oplus p_i,$$セット$$\{b_1\oplus p_i\}$$正確に等しくなければなりません$$\{0,1,\dots,n-1\}.$$次に、バイナリ トライの解釈に焦点を当てます。 

候補値としては$x$の中で最大の数です。$$x \oplus p_i$$トライすることで効率的に見つけることができます。 結果のセットが正確に$[0,n-1]$の場合、すべての値は以下でなければなりません$n$。 したがって、$$\max_i (x \oplus p_i) < n.$$解が存在することが保証されているため、正しい開始値は正確にその値です。$x$任意のプレフィックスとの最大 XOR は次のようになります。$n-1$。 

元の Codeforces ソリューションは、すべてのプレフィックス XOR をバイナリ トライに挿入し、値を見つけます。$x$セットに対する最大 XOR が等しい$n-1$。 その値は次のようになります$b_1$。 一度$b_1$が既知であれば、順列全体の再構築は即座に行われます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース |$O(n^2)$|$O(n)$| 遅すぎる |
 | 最適 |$O(n \log V)$|$O(n \log V)$| 承認済み |

 ここ$V$可能な最大の XOR 値であり、以下の値です。$2^{18}$この問題のために。 

## アルゴリズムのチュートリアル

 1. プレフィックス XOR を計算します。 

させて$p_1=0$。 ポジションごとに、以前のすべての累積 XOR を追加します。$a$-価値観。 
2. すべてのプレフィックス XOR をバイナリ トライに挿入します。 

このトライにより、最大 XOR クエリに答えることができます。$O(\log V)$。 
3. あらゆる値について$x$から$0$に$n-1$、トライをクエリします$$\max_i (x \oplus p_i).$$これは標準の最大 XOR クエリです。 
4. 固有の値を見つける$x$最大 XOR が等しい$n-1$。 

最終的な順列には次のすべての数値が含まれている必要があるため、$0$に$n-1$、生成される最大の値は正確に$n-1$。 解の存在が保証されているということは、正しい開始値がこの条件を満たしていることを意味します。 
5.セット$b_1=x$。 
6. 次を使用して順列を再構築します。$$b_i = x \oplus p_i.$$7. 結果のシーケンスを出力します。 

### なぜ効果があるのか

 プレフィックス XOR 表現は次のようになります。$$b_i=b_1\oplus p_i.$$したがって、有効な順列はすべて、プレフィックス セット全体を 1 つの値で XOR シフトすることによって取得されます。 

仮定する$x=b_1$。 結果の数値は正確なので、$\{0,\dots,n-1\}$、それらの最大値は次の値に等しい$n-1$。 したがって、$$\max_i(x\oplus p_i)=n-1.$$他の候補の場合、シフトされたセットは目的の順列と等しくなることができないため、最大 XOR 値は異なります。 トライにより、正しいシフトを効率的に特定することができます。 回復後$b_1$、残りのすべての要素は定義された XOR 関係に従うため、再構築された配列はまさに必要な順列になります。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

class TrieNode:
    __slots__ = ("ch",)

    def __init__(self):
        self.ch = [-1, -1]

trie = [TrieNode()]

def add(x):
    node = 0
    for bit in range(20, -1, -1):
        b = (x >> bit) & 1
        if trie[node].ch[b] == -1:
            trie[node].ch[b] = len(trie)
            trie.append(TrieNode())
        node = trie[node].ch[b]

def max_xor(x):
    node = 0
    ans = 0
    for bit in range(20, -1, -1):
        b = (x >> bit) & 1
        want = b ^ 1
        if trie[node].ch[want] != -1:
            ans |= 1 << bit
            node = trie[node].ch[want]
        else:
            node = trie[node].ch[b]
    return ans

def solve():
    n = int(input())
    a = list(map(int, input().split()))

    pref = [0]
    cur = 0
    for x in a:
        cur ^= x
        pref.append(cur)

    for x in pref:
        add(x)

    start = 0
    for x in range(n):
        if max_xor(x) == n - 1:
            start = x
            break

    ans = [start ^ p for p in pref]
    print(*ans)

solve()
```最初の部分では、プレフィックス XOR 配列を構築します。 関係$$b_i=b_1\oplus p_i$$これは問題の中心的な構造的事実であるため、その後のすべてはプレフィックス値に基づいて動作します。 

トライにはすべてのプレフィックスが保存されます。 各レベルは 1 ビットに対応します。 最大 XOR クエリは、可能な限り反対ビットを貪欲に追跡します。これは、バイナリ トライにおける最大 XOR の標準的な証明です。 

すべての候補者に対するループ$x \in [0,n-1]$プレフィックス セットに対する最大 XOR が等しい開始値を検索します。$n-1$。 問題保証により、そのような値が存在することが保証されます。 

最後に、答えは次のように直接再構築されます。$x \oplus p_i$。 追加の検証は必要ありません。 

この実装では 21 ビットを使用し、問題内のすべての値を快適にカバーします。 Python では XOR 演算がオーバーフローすることはないため、整数サイズに関する懸念はありません。 

## 実用的な例

 ### 例 1

 入力:```
4
2 1 2
```プレフィックス XOR:

 | ステップ | 現在の | プレフィックス XOR |
 | --- | --- | --- |
 | 開始 | - | 0 |
 | 1 | 2 | 2 |
 | 2 | 1 | 3 |
 | 3 | 2 | 1 |

 それで：```
pref = [0, 2, 3, 1]
```候補の検索:

 | × | max(x XOR pref[i]) |
 | --- | --- |
 | 0 | 3 |
 | 1 | 3 |
 | 2 | 2 |
 | 3 | 3 |

 最初の有効な選択肢は、$x=0$。 

再構築:

 | 私 | プレフィ[i] | b[i] = 0 XOR プレフィ [i] |
 | --- | --- | --- |
 | 1 | 0 | 0 |
 | 2 | 2 | 2 |
 | 3 | 3 | 3 |
 | 4 | 1 | 1 |

 出力：```
0 2 3 1
```このトレースは、順列全体が単にプレフィックス XOR セットのシフトされたバージョンであるという中心的な考え方を示しています。 

### 例 2

 入力:```
2
1
```プレフィックス XOR:

 | ステップ | プレフィックス XOR |
 | --- | --- |
 | 開始 | 0 |
 | [1] の後 | 1 |

 それで：```
pref = [0, 1]
```候補の検索:

 | × | max(x XOR pref[i]) |
 | --- | --- |
 | 0 | 1 |
 | 1 | 1 |

 選ぶ$x=0$。 

再構築:

 | 私 | プレフィ[i] | 結果 |
 | --- | --- | --- |
 | 1 | 0 | 0 |
 | 2 | 1 | 1 |

 出力：```
0 1
```この例では、可能な限り最小の入力を示し、XOR 制約が 1 つだけ存在する場合でも再構成式が機能することを確認します。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |$O(n \log V)$| 各プレフィックス挿入と各クエリはすべてのトライ レベルを横断します。 
| スペース |$O(n \log V)$| すべてのプレフィックスを保存中に作成されたトライノード |

 この問題については、$V$以下です$2^{21}$、 それで$\log V$は事実上小さな定数です。 と$n \le 2 \cdot 10^5$、ソリューションは時間とメモリの制限内に簡単に収まります。 

## テストケース```python
# helper: run solution on input string, return output string
import sys, io

def solve():
    class TrieNode:
        __slots__ = ("ch",)

        def __init__(self):
            self.ch = [-1, -1]

    trie = [TrieNode()]

    def add(x):
        node = 0
        for bit in range(20, -1, -1):
            b = (x >> bit) & 1
            if trie[node].ch[b] == -1:
                trie[node].ch[b] = len(trie)
                trie.append(TrieNode())
            node = trie[node].ch[b]

    def max_xor(x):
        node = 0
        ans = 0
        for bit in range(20, -1, -1):
            b = (x >> bit) & 1
            want = b ^ 1
            if trie[node].ch[want] != -1:
                ans |= 1 << bit
                node = trie[node].ch[want]
            else:
                node = trie[node].ch[b]
        return ans

    n = int(input())
    a = list(map(int, input().split()))

    pref = [0]
    cur = 0
    for x in a:
        cur ^= x
        pref.append(cur)

    for x in pref:
        add(x)

    start = 0
    for x in range(n):
        if max_xor(x) == n - 1:
            start = x
            break

    ans = [start ^ p for p in pref]
    print(*ans)

def run(inp: str) -> str:
    backup_stdin = sys.stdin
    backup_stdout = sys.stdout

    sys.stdin = io.StringIO(inp)
    out = io.StringIO()
    sys.stdout = out

    global input
    input = sys.stdin.readline

    solve()

    sys.stdin = backup_stdin
    sys.stdout = backup_stdout

    return out.getvalue().strip()

# provided sample
assert run("4\n2 1 2\n") == "0 2 3 1"

# minimum size
assert run
```
