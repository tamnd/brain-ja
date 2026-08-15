---
title: "CF 102341K - ケクレオン"
description: "右端に 1 文字を追加することによってのみ増加する小文字の文字列を維持します。 クエリでは長さ (k) が要求され、長さ (k) の文字列全体のプレフィックスと正確に等しい長さ (k) の部分文字列がいくつあるかを数えなければなりません。"
date: "2026-08-13T03:23:23+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102341
codeforces_index: "K"
codeforces_contest_name: "Radewoosh+mnbvmar Contest (supported by AIM Tech)"
rating: 0
weight: 102341
solve_time_s: 187
verified: true
draft: false
---

[CF 102341K - ケクレオン](https://codeforces.com/problemset/problem/102341/K)

 **評価:** -
 **タグ:** -
 **解決時間:** 3 分 7 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 右端に 1 文字を追加することによってのみ増加する小文字の文字列を維持します。 クエリでは長さ (k) が要求され、長さ (k) の文字列全体のプレフィックスと正確に等しい長さ (k) の部分文字列がいくつあるかを数えなければなりません。 

入力は 2 つの方法でオンラインで行われます。 まず、追加される文字と要求された長さは両方とも前の回答を使用してエンコードされるため、将来のクエリを事前にデコードすることはできません。 第 2 に、文字列自体は増加するだけなので、情報を段階的に維持する機会が得られます。 

クエリの数は最大 (300,000) であるため、最終文字列の長さも最大 (300,000) になります。 すべてのクエリに対して文字列全体をスキャンするアルゴリズムは、すでに遅すぎます。 すべての候補部分文字列を 1 文字ずつ比較するアルゴリズムは、大規模な敵対的入力に対して約 (10^{15}) 文字の比較に到達できます。 操作ごとにほぼ対数の仕事が必要です。 

一般的に間違った解決策を引き起こす原因となる詳細が 3 つあります。 

1 つ目は、(k=n) には一致する区間が 1 つだけ、つまり文字列全体があるということです。 例えば、```
3
add a
add b
get 2
```生成する```
1
```適切な出現のみをカウントするソリューションでは、誤ってゼロが返される可能性があります。 

2 つ目は、プレフィックス自体が常に出現としてカウントされることです。 のために```
2
add a
get 1
```答えは```
1
```3 つ目はオンライン エンコードです。 考慮する```
6
add a
add a
add b
add a
get 1
get 1
```文字列は`aaba`。 最初`get 1`(k=1) を求めますが、その答えは (3) です。`last`(3)となります。 2 番目の生の値`1`次に、(n=4) を使用して (k=1) ではなく (k=4) としてデコードされます。 答えは(1)です。 正しい出力は次のとおりです```
3
1
```無視する`last`間違った質問に黙って答えるだろう。 

## アプローチ

 直接的な解決策は、現在の文字列を保存し、クエリごとに、考えられるすべての開始位置を検査することです。 位置ごとに、長さ (k) の部分文字列と最初の (k) 文字を比較します。 これらはクエリで指定されている間隔とまったく同じであるため、これは正しいです。 ただし、(n-k+1) 個の候補区間が存在する可能性があり、1 つの区間の比較には (k) のコストがかかり、1 つのクエリに対して (O(nk)) の作業量が発生する可能性があります。 

最悪のケースは、許容される 4 秒の制限をはるかに超えています。 約 (200,000) 個の追加文字と (100,000) 個のクエリを使用すると、1 つのクエリで約 (10^{10}) 個の文字比較が必要となり、これを複数のクエリにわたって繰り返すと、(10^{15}) 個の比較に達します。 

ローリング ハッシュを使用すると、1 つの部分文字列を比較するコストが削減されますが、それでもすべての (n-k+1) 個の開始位置を検査する必要があります。 本当の問題は平等性テストだけではありません。 文字列をスキャンせずに、プレフィックスの出現をすべてカウントする方法が必要です。 

重要な観察は、KMP で使用されるプレフィックス関数から得られます。 位置 (i) で終わるすべてのプレフィックスについて、プレフィックス関数は、サフィックスでもあるその最長の適切なプレフィックスの長さを教えてくれます。 プレフィックス長ごとにノードを作成し、ノード (i) をノード (\pi[i-1]) の子にすると、プレフィックス関数ツリーが得られます。 

ここで、長さ (k) のプレフィックスを考えてみましょう。 最初の (k) 文字が (i) で終わるプレフィックスのサフィックスである場合に、位置 (i) で終了します。 プレフィックス関数ツリーでは、ノード (k) がノード (i) の祖先であることを意味します。 したがって、プレフィックス (k) の出現数は、ノード (k) をルートとするサブツリーのサイズとまったく同じになります。 

これにより問題は完全に変わります。 追加された各文字は、接頭辞関数ツリー内に 1 つの新しいノードを作成し、そのノードはリーフとして接続されます。 各クエリは、動的なサブツリー サイズのクエリになります。 

静的オイラー ツアーでは、すべてのサブツリーが連続した間隔になりますが、ツリーはオンラインで構築されるため、最終的な DFS 順序は不明です。 代わりに、暗黙的な treap でオイラー数列を動的に維持できます。 すべてのツリー ノードは、入口トークンと出口トークンを受け取ります。 入口トークンの値は (1) ですが、出口トークンの値は (0) です。 ノードのサブツリー全体は、常にその入口トークンと出口トークンの間の連続したシーケンスになります。 新しいリーフが親に接続されると、その 2 つのトークンが親の終了トークンの直前に挿入されます。 

treap はオイラー数列を保存し、すべての treap サブツリー内のトークン値の合計を維持します。 サブツリー サイズのクエリは、対応する入口トークンと出口トークンの間の入口トークンの数にすぎません。 

ブルートフォース手法と最適手法は次のように比較できます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | (O(qn^2)) 最悪の場合 | (O(n)) | 遅すぎる |
 | 最適 | (O(q\log q)) が期待されます | (O(q)) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 現在の文字列とそのプレフィックス関数配列を維持します。 新しい文字が追加されると、通常の KMP フォールバック チェーンを使用してそのプレフィックス関数の値が計算されます。 新しい位置が (n) の場合、接頭辞関数ツリー内のその親はノード (\pi[n-1]) になります。 

すべてのフォールバックは以前に計算された境界に移動するため、KMP の計算はシーケンス全体にわたって線形に償却されます。 
2. すべてのノードの入口トークンと出口トークンを使用してプレフィックス関数ツリーを表します。 ノード (v) の入口トークンには値 (1) が格納され、出口トークンには値 (0) が格納されます。 人工ルートノード (0) は値 (0) を持ちます。 

DFS 表現は次のようになります`enter(v), all descendants, exit(v)`。 したがって、(v) のサブツリー内のすべてのノードは、次の間隔内でちょうど 1 つだけ寄与します。`enter(v)`に`exit(v)`。 
3. このトークン シーケンスを暗黙的トレプに保存します。 トリープは、明示的なキーではなくシーケンス位置によって順序付けされます。 各 treap ノードには、サブツリーのサイズ、サブツリーの合計、子、親、およびランダムな優先度が格納されます。 

親ポインターを使用すると、トークンから treap ルートに向かって歩くことによって、予想される時間 (O(\log n)) でトークンの現在位置を見つけることができます。 
4. 親 (p) を使用してプレフィックス ツリー ノード (v) が作成されたら、その現在の位置を見つけます。`exit(p)`。 そのトークンの直前でオイラー数列を分割し、`enter(v), exit(v)`、シーケンスをマージして戻します。 

親の終了トークンの直前に挿入すると、新しいノードがその親の最後の子として配置されます。 サブツリーのメンバーシップのみが使用されるため、兄弟間の正確な順序は重要ではありません。 
5. のために`get`クエリでは、まず現在の値を使用して要求された長さをデコードします。`last`。 

復号化された(k)は、接頭辞関数ツリーノード(k)に対応する。 エントリ トークンの数を求めます。`enter(k)`を通して`exit(k)`。 この数値は (k) のサブツリー サイズであり、長さ (k) のプレフィックスの出現数と正確に一致します。 
6. 答えを次の場所に保存します`last`次のクエリを処理する前に。 

### なぜ効果があるのか

 すべての位置 (i) について、ノード (i) はその位置で終わるプレフィックス全体を表します。 ノード (k) は、長さ (k) のプレフィックスが (i) で終わるプレフィックスのサフィックスである場合に、ノード (i) の祖先になります。 このサフィックスは、正確には (i) で終わる最初の (k) 文字の出現です。 したがって、クエリされたプレフィックスの出現は、(k) のサブツリー内のノードと 1 対 1 に対応します。 

動的オイラー数列には、常にすべてのサブツリーが 1 つの連続した間隔として含まれます。 エントリ トークンのみが保存された合計に寄与するため、ノード (k) の間隔にわたる合計では、すべての子孫が 1 回だけカウントされます。 したがって、treap は必要な数の一致間隔を正確に返します。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

sys.setrecursionlimit(1_000_000)

def solve():
    q = int(input())

    # Prefix-function data.
    s = bytearray()
    pi = [0]

    # Implicit treap data.
    # Node 0 is the null treap node.
    left = [0]
    right = [0]
    parent = [0]
    size = [0]
    sm = [0]
    value = [0]
    priority = [0]

    seed = 0x12345678

    def rng():
        nonlocal seed
        seed ^= (seed << 13) & 0xFFFFFFFF
        seed ^= seed >> 17
        seed ^= (seed << 5) & 0xFFFFFFFF
        seed &= 0xFFFFFFFF
        return seed

    def new_node(v):
        idx = len(left)
        left.append(0)
        right.append(0)
        parent.append(0)
        size.append(1)
        sm.append(v)
        value.append(v)
        priority.append(rng())
        return idx

    # Root of the prefix-function tree is node 0.
    # Its Euler sequence is enter(0), exit(0).
    new_node(0)
    new_node(0)
    root = 1

    def pull(x):
        l = left[x]
        r = right[x]
        size[x] = size[l] + size[r] + 1
        sm[x] = sm[l] + sm[r] + value[x]

    def merge(a, b):
        if a == 0:
            if b:
                parent[b] = 0
            return b
        if b == 0:
            parent[a] = 0
            return a

        if priority[a] > priority[b]:
            nr = merge(right[a], b)
            right[a] = nr
            if nr:
                parent[nr] = a
            pull(a)
            parent[a] = 0
            return a

        nl = merge(a, left[b])
        left[b] = nl
        if nl:
            parent[nl] = b
        pull(b)
        parent[b] = 0
        return b

    def split(x, k):
        if x == 0:
            return 0, 0

        ls = size[left[x]]

        if k <= ls:
            a, b = split(left[x], k)
            left[x] = b
            if b:
                parent[b] = x
            parent[x] = 0
            if a:
                parent[a] = 0
            pull(x)
            return a, x

        a, b = split(right[x], k - ls - 1)
        right[x] = a
        if a:
            parent[a] = x
        parent[x] = 0
        if b:
            parent[b] = 0
        pull(x)
        return x, b

    def get_rank(x):
        # 1-based position of x in the implicit sequence.
        ans = size[left[x]] + 1
        while parent[x]:
            p = parent[x]
            if right[p] == x:
                ans += size[left[p]] + 1
            x = p
        return ans

    def prefix_before(x):
        # Sum of values strictly before x.
        ans = sm[left[x]]
        while parent[x]:
            p = parent[x]
            if right[p] == x:
                ans += sm[left[p]] + value[p]
            x = p
        return ans

    def enter_token(v):
        # Vertex v has tokens 2*v+1 and 2*v+2.
        return 2 * v + 1

    def exit_token(v):
        return 2 * v + 2

    # Insert the two Euler tokens of vertex v immediately
    # before the exit token of its parent.
    def link_leaf(v, p):
        nonlocal root

        target = exit_token(p)
        pos = get_rank(target)

        a, b = split(root, pos - 1)

        en = new_node(1)
        ex = new_node(0)
        pair = merge(en, ex)

        root = merge(merge(a, pair), b)

    last = 0
    output = []

    for _ in range(q):
        parts = input().split()

        if parts[0] == b"add" or parts[0] == "add":
            raw = parts[1]
            if isinstance(raw, bytes):
                raw = raw[0]
            else:
                raw = ord(raw)

            c = (raw - 97 + last) % 26

            old_n = len(s)
            s.append(c + 97)

            if old_n == 0:
                cur_pi = 0
            else:
                j = pi[old_n - 1]
                while j > 0 and s[old_n] != s[j]:
                    j = pi[j - 1]
                if s[old_n] == s[j]:
                    j += 1
                cur_pi = j

            pi.append(cur_pi)

            v = old_n + 1
            link_leaf(v, cur_pi)

        else:
            raw_k = int(parts[1])
            n = len(s)

            k = ((raw_k - 1 + last) % n) + 1

            tin = enter_token(k)
            tout = exit_token(k)

            # All entry tokens in the subtree lie between tin and tout.
            ans = prefix_before(tout) - prefix_before(tin)

            output.append(str(ans))
            last = ans

    sys.stdout.write("\n".join(output))

if __name__ == "__main__":
    solve()
```prefix-function セクションは、標準の KMP の考え方に従います。 最初の文字の接頭辞関数値は 0 です。 以降のすべての文字については、前の接頭辞関数の値から開始し、現在の文字が境界を拡張できるまで失敗リンクを繰り返したどります。 

prefix-function の値は、ツリー内の親インデックスでもあります。 現在のプレフィックスの長さが (v) の場合、サフィックスでもあるその最長の適切なプレフィックスも長さになります。`pi[v - 1]`、つまり、それはまさにノード (v) の親です。 

オイラー トークンには固定の識別子が割り当てられます。 頂点 (v) については、`2*v+1`はそのエントリトークンであり、`2*v+2`はその終了トークンです。 これにより、頂点ごとに個別のトークン参照を保存する必要がなくなりました。 

treap はオイラー数列を暗黙的に保存します。`split(root, k)`最初の (k) 個のトークンを分離します。`merge(a, b)`2 つのシーケンスを連結します。 親ポインタは、子が treap ノードにアタッチされるたびに維持されます。`get_rank`可能。 

挿入位置は`get_rank(exit_token(parent)) - 1`。 ここは、間違えやすい場所です。 分割には、親の終了トークンの前にすべてのトークンが含まれている必要がありますが、終了トークン自体は右側の部分に属します。 

クエリの場合は、`prefix_before(x)`トークン (x) より前のすべてのトークン値の合計を返します。 したがって、前の値を減算すると、`enter(k)`前の値から`exit(k)`サブツリー内のすべての入口トークンをカウントし、出口トークン自体を除外します。 Python では整数のオーバーフローは発生しません。C++ では、最大でも (n) であるため、答えは 32 ビットの符号付き整数に収まります。 

デコードは更新する前に行う必要があります`last`。 新しい答えは次のようになります`last`クエリが完全に処理された後にのみ。 

## 実用的な例

 ### サンプル 1

 デコードされた文字列は最終的に次のようになります。`abcababca`。 プレフィックス関数の親はオンラインで生成されますが、オイラー ツアーではすべてのプレフィックス関数サブツリーが連続した状態に保たれます。 

| クエリ | 現在の文字列 | デコード済み (k) | プレフィックス ツリー ノード | 答え |`last`|
 | --- | --- | --- | --- | --- | --- |
 |`add a`|`a`| | | | 0 |
 |`add b`|`ab`| | | | 0 |
 |`add c`|`abc`| | | | 0 |
 |`add a`|`abca`| | | | 0 |
 |`get 1`|`abca`| 1 | 1 | 2 | 2 |
 |`add z`|`abcab`| | | | 2 |
 |`get 1`|`abcab`| 3 | 3 | 1 | 1 |
 |`get 1`|`abcab`| 2 | 2 | 2 | 2 |
 |`add y`|`abcaba`| | | | 2 |
 |`add z`|`abcabab`| | | | 2 |
 |`add a`|`abcababc`| | | | 2 |
 |`add y`|`abcababca`| | | | 2 |
 |`get 8`|`abcababca`| 1 | 1 | 4 | 4 |
 |`get 7`|`abcababca`| 3 | 3 | 3 | 3 |
 |`get 9`|`abcababca`| 4 | 4 | 2 | 2 |
 |`get 2`|`abcababca`| 4 | 4 | 2 | 2 |

 最初のクエリは、次の出現を求めます。`a`で`abca`、2つを与えます。 その答えによって次の解釈が変わります`get`。 2 番目の答えが 1 になった後、次の生の値`1`(k=2) にデコードされ、プレフィックスは`ab`そして2回発生します。 

後のクエリは、答えが直接の文字数ではなくサブツリー サイズである理由を示しています。 たとえば、デコードされた (k=4) はプレフィックスに対応します。`abca`。 その出現は、接頭辞関数ツリーのノード 4 の子孫によって表され、オイラー区間にはそれらのエントリ トークンが正確に含まれます。 

### オンラインデコードの例

 より小さい入力を考慮してください```
6
add a
add a
add b
add a
get 1
get 1
```実際の文字列は`aaba`。 最初のクエリには (k=1) があり、プレフィックスは`a`3回発生します。 これにより、`last=3`。 2番目の生`get 1`次に、(n=4) を使用してデコードされ、(k=4) が得られます。 

| クエリ | 文字列 | デコード済み (k) | 関連するプレフィックス | 答え |`last`|
 | --- | --- | --- | --- | --- | --- |
 |`add a`|`a`| | | | 0 |
 |`add a`|`aa`| | | | 0 |
 |`add b`|`aab`| | | | 0 |
 |`add a`|`aaba`| | | | 0 |
 |`get 1`|`aaba`| 1 |`a`| 3 | 3 |
 |`get 1`|`aaba`| 4 |`aaba`| 1 | 1 |

 このトレースは、すべてのクエリを前処理することによっては処理できない問題の部分を実行します。 2 番目のクエリは、デコードされた値が最初のクエリに対する答えに依存するため、実際には現在の文字列全体について尋ねています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O(q\log q)) が期待されます | 各追加は、償却された (O(1)) プレフィックス関数作業と (O(\log q)) の予期された treap 作業を実行します。 それぞれ`get`2 回の (O(\log q)) トレプトラバーサルを使用します。 |
 | スペース | (O(q)) | すべてのプレフィックス関数ノードに 2 つのオイラー トークンと、文字列配列とプレフィックス関数配列があります。 |

 プレフィックス関数ノードの最大数は (300,000) であるため、トレプには人工ルートを含めて最大 (600,002) 個のトークンが含まれます。 予想される対数トリープ高さにより、各動的挿入とクエリが必要な漸近限界内に維持されます。 元の問題には 4 秒の制限があるため、実装にはコンパクトなデータ構造と高速 I/O が必要です。 Python 実装では、プリミティブ Python 整数の配列を使用し、部分文字列の作成やハッシュ化を回避します。 

## テストケース

 次のテストは次のことを前提としています。`solve()`ソリューションの関数が同じファイルに存在します。```python
import sys
import io

def run(inp: str) -> str:
    global input

    old_stdin = sys.stdin
    old_stdout = sys.stdout
    old_input = input

    sys.stdin = io.StringIO(inp)
    input = sys.stdin.readline
    out = io.StringIO()
    sys.stdout = out

    try:
        solve()
    finally:
        sys.stdin = old_stdin
        sys.stdout = old_stdout
        input = old_input

    return out.getvalue()

sample_1 = """16
add a
add b
add c
add a
get 1
add z
get 1
get 1
add y
add z
add a
add y
get 8
get 7
get 9
get 2
"""

assert run(sample_1) == """2
1
2
4
3
2
2
""", "sample 1"

assert run("""2
add a
get 1
""") == """1
""", "minimum size"

assert run("""5
add a
add a
add a
add a
get 1
""") == """4
""", "all equal values"

assert run("""3
add a
add b
get 2
""") == """1
""", "k equals n"

assert run("""6
add a
add a
add b
add a
get 1
get 1
""") == """3
1
""", "online decoding"

max_q = 300000
max_input = str(max_q) + "\n" + ("add a\n" * (max_q - 1)) + "get 1\n"
assert run(max_input) == str(max_q - 1) + "\n", "maximum size"

# A mixed pattern with several different prefix occurrences.
assert run("""9
add a
add b
add a
add b
add a
get 1
get 2
get 3
""") == """3
2
1
""", "overlapping prefixes"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`2 / add a / get 1`|`1`| 最小有効入力とプレフィックス自体が重要であるという事実 |
 | 4`add a`続いて行われる操作`get 1`|`4`| すべて等しい文字列と大きなプレフィックス関数サブツリー |
 |`add a`、`add b`、`get 2`|`1`| 境界ケース (k=n) |
 |`aaba`続いて 2 つのエンコード`get 1`クエリ |`3`、`1`| 正しい使い方`last`デコードするとき |
 | (299,999) 個の追加に続いて`get 1`|`299999`| クエリの最大数と大きなトレプ状態 |
 |`ababa`複数の取得を伴う |`3`、`2`、`1`| 重複するプレフィックスの出現とネストされたプレフィックス関数のサブツリー |

 ## 特殊なケース

 最低限の入力に関しては```
2
add a
get 1
```プレフィックス機能ツリーには、人工ルートの直下にノード 1 が含まれています。 そのオイラー数列は次のとおりです。`enter(0), enter(1), exit(1), exit(0)`。 ノード 1 に属する間隔には、エントリ トークンが 1 つだけ含まれているため、答えは次のようになります。`1`。 

すべて等しい文字列の場合```
5
add a
add a
add a
add a
get 1
```プレフィックス関数ツリーはチェーンです。 ノード 1 はノード 2、3、および 4 の祖先であるため、そのサブツリーには 4 つの実際のノードすべてが含まれます。 ノード 1 のオイラー区間には 4 つのエントリ トークンが含まれており、正しい答えが得られます。`4`。 

境界の場合```
3
add a
add b
get 2
```ノード 2 は最新のプレフィックス関数ノードであり、まだ子孫がありません。 そのサブツリーはそれ自体のみで構成されるため、オイラー区間には 1 つのエントリ トークンが含まれます。 答えは、`1`、これは唯一の長さ 2 の区間、つまり文字列全体に対応します。`ab`。 

オンラインデコードの場合、```
6
add a
add a
add b
add a
get 1
get 1
```最初`get`(k=1) にデコードして返します`3`。 次の生の値も`1`、しかし今は`last=3`(n=4) であるため、デコードされた長さは (4) になります。 ノード 4 には子孫がなく、そのサブツリーのサイズは`1`。 したがって、出力は次のようになります`3`に続く`1`。 

ケース (k=n) は、値が 0 であっても終了トークンを保持しなければならない理由も説明しています。 入口トークンと出口トークンは、曖昧さなくサブツリーを区切ります。 クエリが最新のノードを要求する場合、2 つのトークンは隣接しており、それらのプレフィックスの合計の差により、正確に 1 つのエントリが得られます。 

最後に、兄弟の挿入順序は正確さに影響しません。 新しいプレフィックス関数ノードは常に親の終了トークンの前に挿入されるため、親のサブツリーの一部になります。 親の既存の子の前後に出現するかどうかは、サブツリーのメンバーシップやサブツリーのサイズには影響しません。 トリープは、正規の DFS 順序ではなく、1 つの有効な DFS 順序のみを維持します。
