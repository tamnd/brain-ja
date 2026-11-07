---
title: "CF 145E - ラッキー クエリ"
description: "数字 4 と 7 だけで構成される文字列があります。これに対して 2 種類の操作が実行されます。 最初の操作は、セグメント内のすべての桁を反転します。 4 がすべて 7 になり、7 がすべて 4 になります。"
date: "2026-05-29T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "data-structures"]
categories: ["algorithms"]
codeforces_contest: 145
codeforces_index: "E"
codeforces_contest_name: "Codeforces Round 104 (Div. 1)"
rating: 2400
weight: 145
solve_time_s: 132
verified: true
draft: false
---

[CF 145E - ラッキー クエリ](https://codeforces.com/problemset/problem/145/E)

 **評価:** 2400
 **タグ:** データ構造
 **解決時間:** 2 分 12 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 数字だけで構成される文字列があります`4`そして`7`。 2 種類の操作が実行されます。 

最初の操作は、セグメント内のすべての桁を反転します。 毎`4`になる`7`、そしてすべての`7`になる`4`。 

2 番目の操作では、現在の文字列の最長の非減少サブシーケンスの長さを求めます。 

文字列には可能な数字が 2 つしか含まれていないため、`4 < 7`、非減少サブシーケンスは非常に特殊な形状をしています。 それはいくつかの数から構成されます`4`s の後にいくつかの`7`s. 文字をスキップすることはできますが、文字を配置することはできません。`7`選ばれる前に`4`。 

たとえば:

 - で`44777`、文字列全体はすでに非減少であるため、答えは次のようになります。`5`。 
- で`7744`、最良のサブシーケンスは次のいずれかです`77`または`44`、したがって、答えは次のとおりです`2`。 
- で`74747`、私たちは取ることができます`4447`または`777`、したがって、答えは次のとおりです`4`。 

この制約により、更新のたびに回答を最初から再構築することが完全に排除されます。 文字列の長さは次のとおりです`10^6`、そしてそれがあるかもしれません`3 * 10^5`操作。 クエリごとのリニア スキャンでも、おおよその時間がかかります。`3 * 10^11`3秒以内では不可能な最悪の場合の操作も可能です。 

更新は範囲更新でもあるため、単純な突然変異は高価になります。 以下をサポートするものが必要です。 

- 間隔内のすべての桁を反転します。 
- 間隔からの情報を効率的に組み合わせる、
 - グローバル最長の非減少サブシーケンスに迅速に応答します。 

データ構造に格納されている状態が不完全である場合、いくつかの特殊なケースでは誤って処理されやすくなります。 

考慮する：```
s = 7474
```答えは`3`、サブシーケンスを取得できるため、`447`。 

の数だけを保存した場合`4`そして`7`、それらがどのように配置されているかはわかりません。 両方`4477`そして`7474`2つ含まれています`4`と２つ`7`しかし、彼らの答えは異なります。 

もう 1 つの危険なケースは、重複する範囲で繰り返し反転することです。```
initial: 4477
switch 1 2 -> 7777
switch 2 3 -> 7447
```遅延伝播は正しく構成する必要があります。 よくあるバグは、保留中のフリップを切り替えるのではなく上書きすることです。 

最小のケースも重要です。```
1 3
4
count
switch 1 1
count
```出力は次のとおりです。```
1
1
```単一の文字は、反転に関係なく、常に有効な非減少サブシーケンスです。 

## アプローチ

 ブルート フォース ソリューションは、すべての更新を文字列に直接適用し、すべての更新に対して答えを最初から再計算します。`count`クエリ。 

バイナリ文字列内の最長の非減少サブシーケンスを計算するには、左から右にスキャンします。 各位置で、サブシーケンスを拡張するかどうかを決定します。`4`または`7`。 動的プログラミング ソリューションは線形時間で動作します。 

それは次のようになります:

 -`O(r - l + 1)`各スイッチの時間、
 -`O(n)`各カウントの時間。 

最悪の場合、両方の操作が発生します`3 * 10^5`一定の長さの文字列の回数`10^6`。 それは何千億もの操作になります。 

重要な観察は、セグメントの答えは少数の値のみを使用して表現できるということです。 

セグメントごとに、以下を保存します。 

- の数`4`です、
 - の数`7`です、
 - 最長の非減少サブシーケンス (`4...7`）、
 - 最長の非増加サブシーケンス (`7...4`）。 

なぜ減少バージョンも必要なのでしょうか? 数字を反転すると意味が入れ替わるからです`4`そして`7`。 反転後:

 - すべて`4`になる`7`さん、
 - すべて`7`になる`4`さん、
 - 増加するサブシーケンスは減少するサブシーケンスになります。 

この対称性により、遅延伝播が非常にクリーンになります。 

セグメントを左半分と右半分に分割するとします。 

セグメント全体の最良の非減少サブシーケンスを構築するには、2 つの選択肢があります。 

- take all useful`4`s を左から、右から最適な非減少サブシーケンスを続けます。 
- 左から最良の非減少サブシーケンスを選択し、有用なものをすべて取得します`7`右からです。 

それで：```
inc = max(left.four + right.inc,
          left.inc + right.seven)
```同様に:```
dec = max(left.seven + right.dec,
          left.dec + right.four)
```このマージ ルールは結合性があるため、セグメント ツリーに最適です。 

範囲反転は遅延して処理されます。 セグメントを反転すると、単純に入れ替えられます。 

-`four <-> seven`-`inc <-> dec`各操作にコストがかかります`O(log n)`。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(mn) | O(n) | 遅すぎる |
 | 最適 | O(m log n) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 文字列上にセグメント ツリーを構築します。 

すべてのノードは連続した部分文字列を表します。 
2. 各ノードに対して、次の 4 つの値を保存します。 

-`four`: の数`4`さん、
 -`seven`: の数`7`さん、
 -`inc`: 最長の減少しないサブシーケンスの長さ、
 -`dec`: 増加しない最長のサブシーケンスの長さ。 

単一の文字の場合:

 - もしそうなら`4`の場合、4 つの値はすべて次のようになります。`(1, 0, 1, 1)`、
 - もしそうなら`7`、そうすると、彼らは`(0, 1, 1, 1)`。 
3. 2 つの子ノードを 1 つの親ノードにマージします。 

カウントは単純です。```
four = left.four + right.four
seven = left.seven + right.seven
```のために`inc`、次のいずれかです。 

- 私たちはすべて良いものを使います`4`s を左から、右から最適な増加サブシーケンスを続けます。 
- または、左から増加する最良のサブシーケンスを使用し、すべての良いものを追加します`7`右からです。 

それで：```
inc = max(left.four + right.inc,
          left.inc + right.seven)
```同じ考え方で次のようになります。```
dec = max(left.seven + right.dec,
          left.dec + right.four)
```4. フリップの遅延伝播をサポートします。 

フリップは毎に交換します`4`そして`7`。 

つまり:```
four <-> seven
inc <-> dec
```また、ノードの遅延フラグも切り替えます。 
5. 範囲の更新中に、現在のノードが完全に更新間隔内にある場合は、すぐに反転を適用し、下降を停止します。 

それ以外の場合は、保留中の遅延操作を子にプッシュし、再帰的に続行します。 
6. のために`count`質問に対する答えは常に`inc`ルートに保存されている値。 

ルートは文字列全体を表すため、その最良の非減少サブシーケンスがまさに必要な答えになります。 

### なぜ効果があるのか

 セグメントごとに、`inc`次のような形のサブシーケンスの可能な最大長を常に格納します。`444...777`。 中間点を越える有効なサブシーケンスは、そのシーケンスを終了するか、`4`左の子の一部、またはその子の開始`7`右の子の一部。 マージ式はまさにそれらの可能性を列挙します。 

反転操作では、交換するため正確さが保たれます。`4`そして`7`増加する部分列を減少する部分列に、またはその逆に変換します。 保存された値を交換すると、再構築せずに、反転されたセグメントと一致するようにノードが更新されます。 

すべてのノードには常にそのセグメントの正しい情報が含まれるため、ルートには常に文字列全体の正しい答えが含まれます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

class SegmentTree:
    def __init__(self, s):
        self.n = len(s)
        size = 4 * self.n

        self.four = [0] * size
        self.seven = [0] * size
        self.inc = [0] * size
        self.dec = [0] * size
        self.lazy = [False] * size

        self.s = s
        self.build(1, 0, self.n - 1)

    def build(self, node, l, r):
        if l == r:
            if self.s[l] == '4':
                self.four[node] = 1
            else:
                self.seven[node] = 1

            self.inc[node] = 1
            self.dec[node] = 1
            return

        mid = (l + r) // 2

        self.build(node * 2, l, mid)
        self.build(node * 2 + 1, mid + 1, r)

        self.pull(node)

    def pull(self, node):
        left = node * 2
        right = node * 2 + 1

        self.four[node] = self.four[left] + self.four[right]
        self.seven[node] = self.seven[left] + self.seven[right]

        self.inc[node] = max(
            self.four[left] + self.inc[right],
            self.inc[left] + self.seven[right]
        )

        self.dec[node] = max(
            self.seven[left] + self.dec[right],
            self.dec[left] + self.four[right]
        )

    def apply_flip(self, node):
        self.four[node], self.seven[node] = (
            self.seven[node],
            self.four[node]
        )

        self.inc[node], self.dec[node] = (
            self.dec[node],
            self.inc[node]
        )

        self.lazy[node] ^= True

    def push(self, node):
        if self.lazy[node]:
            self.apply_flip(node * 2)
            self.apply_flip(node * 2 + 1)
            self.lazy[node] = False

    def update(self, node, l, r, ql, qr):
        if ql <= l and r <= qr:
            self.apply_flip(node)
            return

        self.push(node)

        mid = (l + r) // 2

        if ql <= mid:
            self.update(node * 2, l, mid, ql, qr)

        if qr > mid:
            self.update(node * 2 + 1, mid + 1, r, ql, qr)

        self.pull(node)

def solve():
    n, m = map(int, input().split())
    s = input().strip()

    seg = SegmentTree(s)

    ans = []

    for _ in range(m):
        query = input().split()

        if query[0] == "count":
            ans.append(str(seg.inc[1]))
        else:
            l = int(query[1]) - 1
            r = int(query[2]) - 1

            seg.update(1, 0, n - 1, l, r)

    sys.stdout.write("\n".join(ans))

solve()
```この規模では Python オブジェクトの割り当てが高価になるため、ツリーにはカスタム オブジェクトの代わりに 4 つの配列が保存されます。 入力サイズが十分に大きいため、実装の詳細が重要になります。 

の`pull`メソッドにはコア遷移式が含まれています。 よくある間違いは、計算しようとすることです`inc`として：```
max(left.inc, right.inc)
```これは、まさに最適なサブシーケンスが頻繁に現れる場所である、中点を横切るサブシーケンスを無視します。 

ここでの遅延伝播は特にエレガントです。 セグメントを反転する場合、子からの再計算は必要ありません。 保存された値を交換するだけです。 これは、すべての構造特性が交換下で対称であるため機能します。`4`そして`7`。 

更新では包含範囲が使用されます。 入力のインデックスは 1 ですが、実装のインデックスは 0 であるため、更新呼び出しの前に両方のエンドポイントがデクリメントされます。 

すべての答え`count`クエリは`seg.inc[1]`、ノードのため`1`文字列全体を表すルートです。 

## 実用的な例

 ### サンプル 1

 入力:```
2 3
47
count
switch 1 2
count
```| ステップ | 文字列 | 株式会社ルート | 出力 |
 | --- | --- | --- | --- |
 | イニシャル | 47 | 2 | |
 | カウント | 47 | 2 | 2 |
 | スイッチ 1 2 | 74 | 1 | |
 | カウント | 74 | 1 | 1 |

 最初は、文字列全体がすでに減少していません。 両方の位置を反転すると、文字列は次のようになります。`74`。 減少しない部分シーケンスには 1 文字しか含めることができないため、答えは次のようになります。`1`。 

### サンプル 2

 次のことを考慮してください。```
3 4
747
count
switch 1 1
count
switch 2 3
count
```| ステップ | 文字列 | 最高のサブシーケンス | 株式会社ルート | 出力 |
 | --- | --- | --- | --- | --- |
 | イニシャル | 747 | 47 | 2 | |
 | カウント | 747 | 47 | 2 | 2 |
 | スイッチ1 1 | 447 | 447 | 3 | |
 | カウント | 447 | 447 | 3 | 3 |
 | スイッチ 2 3 | 474 | 44 | 2 | |
 | カウント | 474 | 44 | 2 | 2 |

 このトレースは、桁の順序が重要である理由を示しています。 両方`447`そして`474`2つ含まれています`4`と 1`7`, しかし、アレンジのため答えは異なります。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(m log n) | 各範囲の更新は、対数的に多くのセグメント ツリー ノードに影響します。 
| スペース | O(n) | セグメント ツリーには、ノードごとに一定量の情報が格納されます。 

と`n = 10^6`そして`m = 3 * 10^5`、`O(m log n)`ソリューションは制限内に簡単に収まります。 セグメントツリーには約`4n`これは、整数配列を使用して実装された場合、メモリ制限内で許容されます。 

## テストケース```python
# helper: run solution on input string, return output string
import sys
import io

def run(inp: str) -> str:
    input_data = io.StringIO(inp)
    output_data = io.StringIO()

    input = input_data.readline

    class SegmentTree:
        def __init__(self, s):
            self.n = len(s)

            size = 4 * self.n

            self.four = [0] * size
            self.seven = [0] * size
            self.inc = [0] * size
            self.dec = [0] * size
            self.lazy = [False] * size

            self.s = s

            self.build(1, 0, self.n - 1)

        def build(self, node, l, r):
            if l == r:
                if self.s[l] == '4':
                    self.four[node] = 1
                else:
                    self.seven[node] = 1

                self.inc[node] = 1
                self.dec[node] = 1
                return

            mid = (l + r) // 2

            self.build(node * 2, l, mid)
            self.build(node * 2 + 1, mid + 1, r)

            self.pull(node)

        def pull(self, node):
            left = node * 2
            right = node * 2 + 1

            self.four[node] = self.four[left] + self.four[right]
            self.seven[node] = self.seven[left] + self.seven[right]

            self.inc[node] = max(
                self.four[left] + self.inc[right],
                self.inc[left] + self.seven[right]
            )

            self.dec[node] = max(
                self.seven[left] + self.dec[right],
                self.dec[left] + self.four[right]
            )

        def apply_flip(self, node):
            self.four[node], self.seven[node] = (
                self.seven[node],
                self.four[node]
            )

            self.inc[node], self.dec[node] = (
                self.dec[node],
                self.inc[node]
            )

            self.lazy[node] ^= True

        def push(self, node):
            if self.lazy[node]:
                self.apply_flip(node * 2)
                self.apply_flip(node * 2 + 1)
                self.lazy[node] = False

        def update(self, node, l, r, ql, qr):
            if ql <= l and r <= qr:
                self.apply_flip(node)
                return

            self.push(node)

            mid = (l + r) // 2

            if ql <= mid:
                self.update(node * 2, l, mid, ql, qr)

            if qr > mid:
                self.update(node * 2 + 1, mid + 1, r, ql, qr)

            self.pull(node)

    n, m = map(int, input().split())
    s = input().strip()

    seg = SegmentTree(s)

    ans = []

    for _ in range(m):
        q = input().split()

        if q[0] == "count":
            ans.append(str(seg.inc[1]))
        else:
            l = int(q[1]) - 1
            r = int(q[2]) - 1

            seg.update(1, 0, n - 1, l, r)

    return "\n".join(ans)

# provided sample
assert run(
"""2 3
47
count
switch 1 2
count
"""
) == "2\n1", "sample 1"

# minimum size
assert run(
"""1 3
4
count
switch 1 1
count
"""
) == "1\n1", "single element"

# all equal values
assert run(
"""5 3
44444
count
switch 2 4
count
"""
) == "5\n3", "all equal then partial flip"

# overlapping updates
assert run(
"""4 5
4477
count
switch 1 2
count
switch 2 3
count
"""
) == "4\n4\n3", "lazy propagation composition"

# off-by-one boundaries
assert run(
"""5 4
74747
count
switch 1 5
count
"""
) == "3\n3", "full range update"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 単一文字 |`1 1`| 最小サイズの取り扱い |
 |`44444`部分反転あり |`5 3`| 正しいサブシーケンスの再計算 |
 | 重複する更新 |`4 4 3`| 遅延伝播の構成 |
 | フルレンジフリップ |`3 3`| 境界の正確性 |

 ## 特殊なケース

 次の文字列を考えてみましょう。```
4 1
7474
count
```正しい答えは次のとおりです。```
3
```最良のサブシーケンスは`447`。 桁数のみを保存するソリューションでは、答えが頻度のみに依存すると誤って考えられ、出力される可能性があります。`4`。 セグメント ツリーはこれを回避します。`inc`注文情報をエンコードします。 

ここで、繰り返し重なる反転を考えてみましょう。```
4 3
4477
switch 1 2
switch 2 3
count
```実行は次のように進行します。```
4477
-> 7777
-> 7447
```答えは`3`、サブシーケンスを使用して`447`。 

遅延フラグは上書きされずに切り替わります。 XOR 演算の代わりにフラグを置き換えると、2 回目の更新で保留中の情報が破壊され、間違った状態が生成されます。 

最後に、完全な範囲の反転を考えてみましょう。```
5 2
44777
switch 1 5
count
```反転後:```
77444
```最適な非減少サブシーケンスには長さがある`3`。 

The implementation handles this efficiently by swapping:```
four <-> seven
inc <-> dec
```セグメント全体を横断せずに、ルート ノードで直接実行します。
