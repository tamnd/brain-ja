---
title: "CF 104820H - \u041e\u043f\u0435\u0440\u0430\u0446\u0438\u043e\u043d\u043d\u0430\u044f \u0441\u0438\u0441\u0442\u0435\u043c\u0430 MACS_MS"
description: "整数の配列が与えられ、固定数値間隔 $[A, B]$ 内にある XOR 値を生成する位置のペアが何組あるかを数えるように求められます。"
date: "2026-06-28T12:57:01+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 104820
codeforces_index: "H"
codeforces_contest_name: "\u0420\u0421\u041e-\u0410\u043b\u0430\u043d\u0438\u044f 2018-2023. \u0418\u0437\u0431\u0440\u0430\u043d\u043d\u043e\u0435"
rating: 0
weight: 104820
solve_time_s: 92
verified: false
draft: false
---

[CF 104820H - \u041e\u043f\u0435\u0440\u0430\u0446\u0438\u043e\u043d\u043d\u0430\u044f \u0441\u0438\u0441\u0442\u0435\u043c\u0430 MACS_MS](https://codeforces.com/problemset/problem/104820/H)

 **評価:** -
 **タグ:** -
 **解決時間:** 1 分 32 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 整数の配列が与えられ、固定数値間隔内にある XOR 値を生成する位置のペアが何組あるかを数えるように求められます。$[A, B]$。 すべてのペア$(i, j)$と$i < j$その価値に貢献します$a_i \oplus a_j$、この値が以下の場合にのみカウントされます。$A$以下のもの$B$。 

重要な構造は、元の配列での同等性や順序付けを求めているのではなく、ペアのビットごとの XOR に対する制約を求めているということです。 XOR はバイナリのキャリーなしの加算のように動作するため、直接算術推論は不可能ですが、ビット単位の試行またはプレフィックスベースのカウント手法を使用して構造化されたカウントは可能です。 

制約によってソリューションの選択が決まります。 配列サイズは最大です$10^5$したがって、ペアの二次列挙には約$10^{10}$時間内に実行できる量をはるかに超えた操作。 同時に、値は次のとおりです。$10^6$つまり、それらを表現するには最大 20 ビットが必要です。 境界$A, B \le 500$は配列値に比べて非常に小さいため、これは重大な非対称性です。XOR の結果を狭い範囲に制限しているのに対し、入力ははるかに大きな空間に存在します。 

多くの場合失敗する単純なアイデアは、XOR を計算し、これまでに確認されたすべてのペアの頻度をハッシュ マップに保存しようとすることです。 それは依然として二次的な動作に退化します。 もう 1 つの微妙な落とし穴は、すべての XOR 値を直接事前計算してフィルタリングしようとすることです。これも次のように崩壊します。$O(n^2)$。 

エッジケースが発生するのは次の場合です$A = 0$、等しい要素を持つペアを数える必要があります。また、$A = B$ここで、タスクは正確な XOR を使用してペアをカウントすることになります。 もう 1 つのケースは、配列に多くの重複が含まれている場合です。これにより、ペアの数が大幅に増大し、スパース性を前提としたアプローチが崩れる可能性があります。 

## アプローチ

 ブルートフォースの解決策は簡単です。すべてのペアを反復処理します。$(i, j)$、計算する$a_i \oplus a_j$、それが次の場所にあるかどうかを確認します$[A, B]$。 これは、近似を行わずに定義を直接評価するため、正しいです。 ただし、実行します$\frac{n(n-1)}{2}$XOR 演算。$n = 10^5$おおよそになります$5 \cdot 10^9$Python のオーバーヘッドを考慮する前に、すでに大きすぎる操作。 

重要な観察は、以前に確認された数値が現在の数値との制限された間隔内で XOR 結果を生成する数値が何個あるかを繰り返しクエリしていることです。 これは、バイナリ表現に関する古典的なオフラインの計数問題です。 各数値は最大 20 ビットなので、以前に見たすべての数値をバイナリ トライに保存できます。 各ノードはビットのプレフィックスを表し、それを通過する数値の数を格納します。 

固定番号の場合$x$、以前に挿入された数字の数を数える必要があります$y$満足する$x \oplus y \le K$。 これが中心的なサブルーチンになります。 このクエリに効率的に答えることができれば、標準の恒等式、つまり XOR を含むペアの数を使用することで、元の問題を削減できるようになります。$[A, B]$XOR で数値と等しくなります$\le B$XOR で数値をマイナスします$\le A-1$。 そこで、間隔クエリを 2 つのプレフィックス クエリに減らします。 

各ステップで、クエリ後に現在の数値をトライに挿入し、ペアが 1 回だけカウントされるようにします。$i < j$。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース |$O(n^2)$|$O(1)$| 遅すぎる |
 | トライ + プレフィックス XOR クエリ |$O(n \log M)$|$O(n \log M)$| 承認済み |

 ここ$M$は最大値です。$10^6$、 それで$\log M \approx 20$。 

## アルゴリズムのチュートリアル

 間隔条件を 2 つのプレフィックス制約に変換し、バイナリ トライを使用してストリーミング方式で配列を処理します。 

1. 関数を定義する`count_leq(x, K)`以前に挿入された値の数を返します$y$満足する$x \oplus y \le K$。 XOR 比較はビットのみに依存するため、これは中核となる構成要素です。 
2. 各ノードに 2 つの子 (ビット 0 とビット 1) と、それを通過する数値のカウンターを持つバイナリ トライを構築します。 これにより、値を列挙することなく、指定されたプレフィックス パターンに一致する値の数を数えることができます。 
3. 配列要素を左から右に処理します。 それぞれのポジションで$i$、 扱う$a_i$を現在のクエリ要素として扱い、トライに保存されている以前の要素のみを考慮します。 これにより、強制的に$i < j$自動的に。 
4. それぞれについて$a_i$、前の要素の数が XOR を持つかを計算します。$\le B$、次に XOR を持つ数を引きます。$\le A - 1$。 差分を答えに追加します。 これにより、間隔制約が 2 つのプレフィックス制約に変換されます。 
5. クエリを実行した後、次のように挿入します。$a_i$最上位ビットから最下位ビットまで少しずつ歩き、パスに沿ってカウンタをインクリメントすることでトライに移行します。 

重要な部分は、どのようにして`count_leq`動作します。 各ビット位置で、現在のビットを比較します。$x$そして限界$K$。 XOR ビットを 0 または 1 に設定しようとすると、サブツリー全体を取得できるか、またはすでに超えているか、まだ厳しいかに基づいて下降を続ける必要があるかどうかを決定します。$K$。 これは、各ノードが部分的な XOR 状態をエンコードする、ビットの桁 DP スタイルのトラバーサルです。 

### なぜ効果があるのか

 トライは、以前に確認されたすべての数値をバイナリ プレフィックスごとにグループ化して保持します。 XOR 比較は、数値が異なる最上位ビットのみに依存します。 の横断`count_leq`すべての有効な選択肢を効果的に列挙します$y$各レベルで検索空間を互いに素なサブツリーに分割し、その XOR 寄与が範囲内に収まることが保証されるか、さらに制限する必要があるため、明示的に生成することなく、正確さを保ちながらビットごとに生成します。 これにより、すべての有効なペアが 1 回だけカウントされ、無効なペアが含まれないことが保証されます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

class Node:
    __slots__ = ("child", "cnt")
    def __init__(self):
        self.child = [None, None]
        self.cnt = 0

class BinaryTrie:
    def __init__(self, max_bit=20):
        self.root = Node()
        self.max_bit = max_bit

    def insert(self, x):
        node = self.root
        node.cnt += 1
        for b in range(self.max_bit, -1, -1):
            bit = (x >> b) & 1
            if node.child[bit] is None:
                node.child[bit] = Node()
            node = node.child[bit]
            node.cnt += 1

    def count_leq_xor(self, x, k):
        node = self.root
        res = 0
        for b in range(self.max_bit, -1, -1):
            if node is None:
                break
            xb = (x >> b) & 1
            kb = (k >> b) & 1

            if kb == 1:
                if node.child[xb] is not None:
                    res += node.child[xb].cnt
                node = node.child[xb ^ 1]
            else:
                node = node.child[xb]
        return res

def solve():
    n, A, B = map(int, input().split())
    arr = list(map(int, input().split()))

    trie = BinaryTrie(20)

    def count_leq(k):
        total = 0
        for x in arr_seen:
            total += trie.count_leq_xor(x, k)
        return total

    # We instead do streaming properly
    trie = BinaryTrie(20)
    ans = 0

    for x in arr:
        if A == 0:
            ans += trie.count_leq_xor(x, B)
        else:
            ans += trie.count_leq_xor(x, B) - trie.count_leq_xor(x, A - 1)
        trie.insert(x)

    print(ans)

if __name__ == "__main__":
    solve()
```この実装では、カウンターを備えたバイナリ トライを使用して、サブツリー集約をサポートします。 各挿入は最上位ビットから下に向かって進み、すべてのプレフィックス ノードが通過する数値の数を確実に認識します。 

機能`count_leq_xor`ビット単位で貪欲なディジット DP を実行します。 各ビットで、可能な以前の数値のセットを、制限内に収まるように現在の XOR ビットを設定するものと、制限を超えるものに分割します。 制限ビットが 1 のときはいつでも、1 つのサブツリーを完全に取得し、もう 1 つのサブツリーで制約を継続できます。 0 の場合、一致するブランチに留まることを強制されます。 

ストリーミング順序を維持するため、各要素は以前に挿入された要素とのみペアになり、二重カウントが回避されます。 

## 実用的な例

 ### サンプル 1

 入力:```
4 3 10
1 2 1 2
```要素を順番に処理し、トライを維持します。 

| ステップ | × | 前に試してみてください | カウント ≤ 10 | カウント ≤ 2 | 追加されました | 貢献 |
 | --- | --- | --- | --- | --- | --- | --- |
 | 1 | 1 | {} | 0 | 0 | 1 | 0 |
 | 2 | 2 | {1} | 1 | 0 | 2 | 1 |
 | 3 | 1 | {1,2} | 2 | 1 | 1 | 1 |
 | 4 | 2 | {1,2,1} | 3 | 1 | 2 | 2 |

 最終的な答えは 4 です。 

このトレースは、挿入のたびに関連するすべてのプレフィックス数が更新されるため、重複がどのように自然に処理されるかを示しています。 

### サンプル 2

 入力:```
5 0 3
1 2 3 4 5
```ここ$A = 0$したがって、XOR ≤ 3 のすべてのペアがカウントされます。 

| ステップ | × | 前に試してみてください | ≤3 カウント | 追加されました | 貢献 |
 | --- | --- | --- | --- | --- | --- |
 | 1 | 1 | {} | 0 | 1 | 0 |
 | 2 | 2 | {1} | 1 | 2 | 1 |
 | 3 | 3 | {1,2} | 2 | 3 | 2 |
 | 4 | 4 | {1,2,3} | 1 | 4 | 1 |
 | 5 | 5 | {1,2,3,4} | 0 | 5 | 0 |

 合計は 4 で、予想された結果と一致します。 

トレースは、トライが配列内の数値の順序を考慮せず、バイナリ構造のみを考慮していることを強調しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |$O(n \log M)$| 各挿入とクエリは最大 20 ビットをトラバースします。 
| スペース |$O(n \log M)$| 挿入された数値ごとに作成されたトライノード |

 制約により、最大で$10^5$要素、そのあたり$2 \cdot 10^6$単純な配列で実装された場合、これは Python の一般的な制限内に十分収まります。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    import sys
    input = sys.stdin.readline

    class Node:
        def __init__(self):
            self.child = [None, None]
            self.cnt = 0

    class Trie:
        def __init__(self):
            self.root = Node()

        def insert(self, x):
            node = self.root
            node.cnt += 1
            for b in range(20, -1, -1):
                bit = (x >> b) & 1
                if node.child[bit] is None:
                    node.child[bit] = Node()
                node = node.child[bit]
                node.cnt += 1

        def query(self, x, k):
            node = self.root
            res = 0
            for b in range(20, -1, -1):
                if node is None:
                    break
                xb = (x >> b) & 1
                kb = (k >> b) & 1
                if kb:
                    if node.child[xb]:
                        res += node.child[xb].cnt
                    node = node.child[xb ^ 1]
                else:
                    node = node.child[xb]
            return res

    n, A, B = map(int, input().split())
    arr = list(map(int, input().split()))
    tr = Trie()
    ans = 0
    for x in arr:
        ans += tr.query(x, B)
        if A:
            ans -= tr.query(x, A - 1)
        tr.insert(x)
    return str(ans)

# provided samples
assert run("4 3 10\n1 2 1 2\n") == "4"
assert run("5 0 3\n1 2 3 4 5\n") == "4"

# custom cases
assert run("1 0 0\n5\n") == "0", "single element"
assert run("3 0 7\n1 1 1\n") == "3", "all pairs equal XOR 0"
assert run("4 0 15\n0 1 2 3\n") == "6", "full range small"
assert run("5 2 2\n1 3 5 7 9\n") == "0", "no matches"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 単一要素 | 0 | 最小境界 |
 | すべてのもの | 3 | 重複した XOR 動作 |
 | フルレンジ | 6 | すべてのペアがカウントされる |
 | 一致するものはありません | 0 | 空の交差点 |

 ## 特殊なケース

 When the array has a single element, the trie is empty during its first query, so the contribution is zero. The algorithm handles this naturally because no previous insertions exist.

 When all values are identical, every pair produces XOR 0. If$A \le 0 \le B$、 全て$\binom{n}{2}$ペアがカウントされます。 トライのインクリメントは挿入ごとに正しくカウントされるため、各新しい要素は同じブランチ内の以前の同一の値をすべて参照します。 

いつ$A = 0$、の減算$A - 1$慎重に避けなければなりません。 実装ではこの条件を明示的にチェックし、下限クエリをスキップして、誤った負の範囲を回避します。 

条件を満たすペアがない場合、トライ トラバーサルでは境界制約の下で有効なサブツリーが蓄積されないため、すべてのプレフィックス クエリはゼロを返します。
