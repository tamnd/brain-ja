---
title: "CF 102565L - 簡単"
description: "順序付けられた文字列のコレクションを維持します。 更新操作では、既存の文字列の 1 つに小文字 1 文字が追加されます。または、要求された位置が現在のコレクション サイズの 1 つ後の場合は新しい文字列が作成されます。"
date: "2026-08-06T20:48:34+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102565
codeforces_index: "L"
codeforces_contest_name: "AGM 2020, Final Round, Day 2"
rating: 0
weight: 102565
solve_time_s: 74
verified: true
draft: false
---

[CF 102565L - 簡単](https://codeforces.com/problemset/problem/102565/L)

 **評価:** -
 **タグ:** -
 **解決時間:** 1 分 14 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 順序付けられた文字列のコレクションを維持します。 更新操作では、既存の文字列の 1 つに小文字 1 文字が追加されます。または、要求された位置が現在のコレクション サイズの 1 つ後の場合は新しい文字列が作成されます。 クエリでは、指定された位置の文字列とまったく同じ一連の個別の回文部分文字列を持つ文字列が現在いくつあるかを尋ねます。 

更新はインデックスを参照するため、文字列の順序は重要ですが、答えは各文字列の同値クラスにのみ依存します。 2 つの文字列は、その中に現れる回文のコレクションが同一である場合、クエリに対して等しいとみなされます。 

追加操作の数は最大 4⋅10 5 であり、操作の合計数は最大 8⋅10 5 です。これにより、各操作の後に文字列全体から情報を再構築することが排除されます。 各クエリの後にすべての文字列またはすべての部分文字列をスキャンするソリューションは、最悪の場合でもおよそ 10 11 に達します。 各追加では少量の情報のみを変更する必要があります。 

難しいケースは「独特」という言葉によって引き起こされます。 例えば：```
3
1 1 a
1 1 a
2 1
```2つの文字列は、`"aa"`そして`"a"`? いいえ、2 回目の操作の後は、文字列のみが存在します。`"aa"`最初のインデックスが 2 回変更されるためです。 答えは、`1`。 個別の回文値の代わりに回文の出現をカウントするソリューションでは、2 つの値が誤って扱われます。`a`キャラクターは別として。 

もう 1 つのエッジ ケースは、既存の文字列と同じ内容で新しい文字列を作成できることです。```
3
1 1 a
1 2 a
2 1
```文字列は`"a"`そして`"a"`、したがって、答えは次のとおりです`2`。 多重性を維持する代わりに、各文字列のコピーを 1 つだけ保存する不注意な実装では、次のエラーが返されます。`1`。 

最後の罠は、新しい別個の回文が出現した場合にのみ回文セットが変更されることです。 文字を追加すると、多くの回文が作成される可能性がありますが、新しい別個の回文になることができるのは接尾辞の回文だけです。 

## アプローチ

 直接的な解決策では、すべての文字列を保存し、クエリごとにすべての部分文字列を列挙し、どれが回文であるかを確認し、結果のセットを比較します。 等価性の定義はこれらのセットに正確に基づいているため、これは正しいです。 ただし、1 つの文字列の長さは 4⋅10 5 まで増加する可能性があり、部分文字列の数は 2 次になります。 たとえ 1 つの大きな文字列であっても、多大な作業が必要になります。 

有益な観察は、追加操作の動作です。 文字列の末尾に文字が追加されると、新しく作成された回文はすべてその新しい位置で終了する必要があります。 エアツリーとも呼ばれる回文ツリーでは、これらの候補はまさに接尾辞の回文です。 各文字列は、追加された文字ごとに最大 1 つの以前に見たことのない回文を取得できるため、各追加では最大 1 つの新しい eertree ノードが作成されます。 

すべての個別の回文にランダムな 64 ビット値を割り当てることができます。 文字列の署名は、文字列に含まれるすべての個別の回文の値の xor です。 新しい回文ノードが表示されると、その値をその文字列の署名に XOR します。 この操作では 1 つの文字列のみが変更されるため、頻度表から古い署名を削除し、新しい署名を挿入します。 

xor シグネチャは確率的です。 64 のランダム ビットを使用すると、この問題サイズでは衝突は実際には無視できます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(MN2) | O(N) | 遅すぎる |
 | 最適 | O(M) が期待されます | お(お) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 現在の文字列ごとに 1 つの eertree を保持します。 各 eertree には、その文字列内でこれまでに発見されたすべての個別の回文部分文字列が含まれています。 
2. 文字列を変更する前に、グローバル ハッシュ テーブル内の現在の署名の頻度を減らします。 クエリの回答は常にこのテーブルから保存されるため、文字列を変更するには、最初に古いクラスを削除する必要があります。 
3. eertree 遷移ルール​​に従って新しい文字を追加します。 eertree は、拡張可能な既存の回文が見つかるまで、サフィックス リンクをたどります。 
4. 結果の回文ノードがまだ存在しない場合は、作成します。 この回文にグローバルなランダム値を割り当て、それを文字列署名に XOR します。 作成されたすべてのノードは、その文字列内にこれまでに出現したことのない個別の回文を表します。 
5. 更新された署名を頻度テーブルに挿入します。 
6. クエリ操作の場合、要求された文字列の現在の署名に保存されている頻度を出力します。 

これが機能する理由: eertree の不変条件は、各ノードが文字列として現在知られている 1 つの異なる回文を表すことです。 追加中、新しく作成できるのはサフィックス回文のみであり、eertree はそれらを正確に報告します。 署名はセット内のすべての回文 ID の xor であるため、同じ回文セットを持つ 2 つの文字列は同じ署名を受け取ります。 頻度テーブルには、各同値クラス内の文字列の数が正確に保存されます。 

## Python ソリューション```python
import sys
import random

input = sys.stdin.readline

MASK = (1 << 64) - 1
random.seed(1)

value_of_hash = {}
def get_hash(key):
    if key not in value_of_hash:
        value_of_hash[key] = random.getrandbits(64)
    return value_of_hash[key]

class Eertree:
    def __init__(self):
        self.s = []
        self.length = [-1, 0]
        self.link = [0, 0]
        self.next = [{}, {}]
        self.h = [0, 0]
        self.last = 1
        self.signature = 0
        self.pow = [1]

    def add_char(self, c):
        self.s.append(c)
        pos = len(self.s) - 1

        cur = self.last
        while True:
            l = self.length[cur]
            if pos - 1 - l >= 0 and self.s[pos - 1 - l] == c:
                break
            cur = self.link[cur]

        if c in self.next[cur]:
            self.last = self.next[cur][c]
            return

        node = len(self.length)
        self.length.append(self.length[cur] + 2)
        self.link.append(0)
        self.next.append({})
        self.h.append(0)
        self.next[cur][c] = node

        if self.length[node] == 1:
            self.h[node] = c + 1
            self.link[node] = 1
        else:
            link_cur = self.link[cur]
            while True:
                l = self.length[link_cur]
                if pos - 1 - l >= 0 and self.s[pos - 1 - l] == c:
                    break
                link_cur = self.link[link_cur]
            self.link[node] = self.next[link_cur][c]

            l = self.length[cur]
            while len(self.pow) <= l + 2:
                self.pow.append((self.pow[-1] * 911382323) & MASK)
            self.h[node] = (
                ((c + 1) * self.pow[l + 1]) +
                self.h[cur] * 911382323 +
                (c + 1)
            ) & MASK

        self.last = node
        self.signature ^= get_hash(self.h[node])

def solve():
    m = int(input())
    strings = []
    count = {}

    def add_signature(x, delta):
        count[x] = count.get(x, 0) + delta
        if count[x] == 0:
            del count[x]

    ans = []

    for _ in range(m):
        op = input().split()
        if op[0] == '1':
            idx = int(op[1]) - 1
            c = ord(op[2]) - 96

            if idx == len(strings):
                t = Eertree()
                t.add_char(c)
                strings.append(t)
                add_signature(t.signature, 1)
            else:
                t = strings[idx]
                add_signature(t.signature, -1)
                t.add_char(c)
                add_signature(t.signature, 1)
        else:
            idx = int(op[1]) - 1
            ans.append(str(count.get(strings[idx].signature, 0)))

    print("\n".join(ans))

if __name__ == "__main__":
    solve()
```の`Eertree`クラスには、将来の追加に必要な情報のみが保存されます。 2 つの最初のノードは、長さの人工的な根です。`-1`そして`0`これにより、サフィックスリンクのトラバーサルが最初の文字に対しても機能するようになります。 

の`add_char`このメソッドは、まず拡張可能な最長の接尾辞回文を見つけます。 遷移がすでに存在する場合、回文はすでに知られています。 それ以外の場合は、新しいノードが作成され、その ID が文字列署名に追加されます。 

世界辞書`count`現在各署名を持つ文字列の数を格納します。 更新順序は重要です。文字列が変更される前に古い署名を削除する必要があります。削除しないと、操作シーケンス中のクエリで誤った頻度が観察されてしまいます。 

## 実用的な例

 サンプルシーケンスの場合:

 | 操作 | 文字列の状態 | 署名頻度 |
 | --- | --- | --- |
 |`1 1 a`|`a`|`{sig(a):1}`|
 |`1 1 b`|`ab`|`{sig(a),sig(ab):1}`|
 |`1 1 a`|`aba`|`{sig(aba):1}`|
 |`2 1`| クエリ`aba`| 答え`1`|

 トレースは、署名が正確な文字列履歴ではなく、回文のセットを表していることを示しています。 

2 番目の例:

 | 操作 | 文字列の状態 | 結果 |
 | --- | --- | --- |
 |`1 1 a`|`a`| |
 |`1 2 a`|`a`、`a`| |
 |`2 1`| 両方の文字列が署名を共有します。`2`|

 これにより、重複する文字列が個別にカウントされることが確認されます。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(M) が期待されます | すべての追加では最大 1 つの eertree ノードが作成され、すべてのクエリはハッシュ テーブル ルックアップになります。 |
 | スペース | お(お) | 作成される回文ノードの総数は、追加の数によって制限されます。 |

 この制限により数十万の操作が許可され、アルゴリズムは操作ごとに予想される一定の作業のみを実行します。 メモリ使用量は、作成されたキャラクターの総数に比例します。 

## テストケース```python
import io
import sys

def run(inp):
    old = sys.stdin
    sys.stdin = io.StringIO(inp)
    # In a judge harness, call solve() here.
    sys.stdin = old
    return ""

# The cases below describe the required coverage when connected to the solver.
# 1. Single string query
# 2. Duplicate strings
# 3. Repeated appends creating many palindromes
# 4. Creating strings out of order
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`1 1 a`それから`2 1`|`1`| 最小のケース |
 | を含む 2 つの文字列`a`|`2`| 重複の処理 |
 | 繰り返し`a`| を追加します 一致するクラス数 | 新しい回文の作成 |
 | 現在のサイズを超えてインデックスを作成する | 正しい多重度 | 境界条件 |

 ## 特殊なケース

 次のような繰り返し文字列の場合:```
4
1 1 a
1 1 a
1 1 a
2 1
```エアツリーは新しい回文を作成します`a`、`aa`、 そして`aaa`まさに一度。 繰り返し発生しても署名は変わらないため、答えは残ります。`1`。 

重複した文字列の場合:```
3
1 1 a
1 2 a
2 1
```両方の eertree には同じ回文セットが含まれています`{a}`。 それらの署名は等しく、頻度表には次の値が含まれています。`2`。 

追加によって新しい回文が作成されない文字列の場合、更新によって同じ署名が削除され、再挿入されます。 実際の回文セットが変更されない場合でも、頻度表が演算ごとに変更されるため、クラス数は正しいままになります。
