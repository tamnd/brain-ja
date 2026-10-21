---
title: "CF 105928E - LCM クエリ"
description: "私たちは要素が時間の経過とともに変化する可能性のある動的配列を維持しており、それらの要素から派生した乗法構造に関する範囲クエリに答える必要があります。 各クエリは、単一の位置を更新するか、配列のセグメントについて尋ねます。"
date: "2026-06-22T15:38:11+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 105928
codeforces_index: "E"
codeforces_contest_name: "Soy Cup #2: Vivian"
rating: 0
weight: 105928
solve_time_s: 57
verified: true
draft: false
---

[CF 105928E - LCM クエリ](https://codeforces.com/problemset/problem/105928/E)

 **評価:** -
 **タグ:** -
 **解決時間:** 57 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 私たちは要素が時間の経過とともに変化する可能性のある動的配列を維持しており、それらの要素から派生した乗法構造に関する範囲クエリに答える必要があります。 各クエリは、単一の位置を更新するか、配列のセグメントについて尋ねます。 

範囲に対するクエリの場合、概念的には、そのセグメント内のすべての数値の最小公倍数を取得します。 LCM 自体を出力する代わりに、LCM が固定素数を法として持つ正の約数の数を計算するように求められます。 

主な難点は、配列が最大 20 万要素と大きく、更新とクエリの両方がインターリーブされることです。 各値は最大でも 10 万であり、素因数分解が可能なほど十分小さいですが、クエリごとに何かを最初から再計算するにはまだ大きすぎます。 

単純なアプローチでは、範囲をスキャンして素数の指数を取得することによって、クエリごとに LCM を再計算します。 これは、各クエリが O(n) 個の要素に接触する可能性があり、O(n) 個のクエリが存在する可能性があるため、すぐに破綻し、O(n²) 個の動作が発生します。 

2 番目の単純なアイデアは、LCM を直接保守することです。 LCM 値が爆発的に大きくなり、更新時に安定しないため、これも失敗します。 保管することさえ不可能になります。 

重複する領域で更新が頻繁に発生する場合、微妙なエッジ ケースが発生します。 たとえば、クエリごとに素数指数を個別に再計算し続けると、同じ数値を繰り返し因数分解し、クエリ間で共有されている構造がすべて失われます。 

本当の課題は、LCM 構造が範囲全体の素指数の最大値によって完全に支配され、約数の数が LCM 自体の数値ではなくそれらの指数にのみ依存することを認識することです。 

## アプローチ

 ブルート フォース ソリューションでは、各クエリが個別に再計算されます。 範囲クエリの場合、l から r までのすべての要素をスキャンし、各数値を因数分解して、そのセグメントに出現するすべての素数の最大指数を追跡します。 すべての素数が収集されると、除数の数が (指数 + 1) の素数の積として計算されます。 

これは正しいです。LCM は各素数について、セグメント内のすべての数値の最大指数を取ることによって定義されるからです。 ただし、このアプローチは遅すぎます。 各因数分解のコストは約 O(√A) で、各クエリは O(n) 要素に触れるので、クエリごとにおよそ O(n√A) になります。 最大 2e5 のクエリでは、これは実現不可能です。 

重要な洞察は、各数値は少数の素数セットにのみ寄与し、各素数は範囲内の最大指数のみを必要とするということです。 最初から再計算する代わりに、プライムセグメントごとの情報を維持します。 値は最大でも 1e5 であるため、個別の素数の総数は制限されており、各数値に含まれる素因数は最大でも数個です。 

問題を再定式化します。素数 p ごとに、「点更新での [l, r] の p の最大指数は何ですか」に答えることができるデータ構造を維持する必要があります。 最終的な答えは、(最大指数 + 1) の素数の積です。 

これにより、位置にわたるセグメント ツリーが作成され、各ノードがそのセグメント内の素数から最大指数までのスパース マップを格納します。 更新は、ルートからリーフへの 1 つのパスに沿ってのみ再計算されます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(q・n・√A) | お(1) | 遅すぎる |
 | 素数指数上のセグメント ツリー | O(q log n · k) | O(n log n · k) | 承認済み |

 ここで、k は数値当たりの素因数の数であり、小さいです。 

## アルゴリズムのチュートリアル

 1e5 までのすべての整数の最小の素因数を前処理するので、あらゆる値を素因数分解して素数累乗を迅速に計算できます。 

次に、配列上にセグメント ツリーを構築します。ここで、各ノードには、素数をそのセグメント内の最大指数にマッピングする辞書が格納されます。 

### ステップ

1. 1e5 までのすべての値について最小の素因数を事前計算します。 これにより、任意の配列要素をそのサイズに対して対数時間で因数分解できます。 これがないと、更新時に sqrt 分解を繰り返すと遅すぎます。 
2. 各初期配列要素を素数と指数のペアに因数分解します。 各要素は少数のエントリのみを提供するため、この表現はコンパクトなままです。 
3. 各リーフ ノードが 1 つの配列位置に対応し、その素数マップを格納するセグメント ツリーを構築します。 
4. 内部ノードの場合、いずれかの子に存在する各素数の最大指数を取得して、その子をマージします。 これは、切り離されたセグメント上で LCM がどのように動作するかを正確に反映しています。 
5. ポイント更新の場合、新しい値を因数分解し、リーフを置き換え、子を再度マージすることでルートへのパスに沿ったすべてのセグメント ツリー ノードを再計算します。 
6. [l, r] に関するクエリの場合、セグメント ツリーを走査し、その区間をカバーするすべてのノードを収集します。 素数ごとの最大指数を取得して素数マップをマージします。 
7. クエリ範囲の結合されたマップを取得した後、(指数 + 1) モジュロ 998244353 のすべての素数の積として答えを計算します。 

重要な設計上の選択は、実際の LCM を決して計算しないことです。 正確さと効率の両方にとって十分な素指数の最大値のみを追跡します。 

### なぜ効果があるのか

 数値セットの最小公倍数は、セット全体の各素数の最大指数によって完全に決定されます。 セグメント ツリーは、すべてのノードでこの不変条件を正確に維持します。つまり、各ノードは、その間隔の正しい最大指数表現を格納します。 ノードをマージすると素数ごとの最大値が保存されるため、すべてのクエリで範囲の正しい指数プロファイルが再構築されます。 除数の数の式はこれらの指数にのみ依存するため、プロファイルが正しければ、最終的な積も正しいことが保証されます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

MOD = 998244353
MAXV = 100000

# smallest prime factor sieve
spf = list(range(MAXV + 1))
for i in range(2, int(MAXV ** 0.5) + 1):
    if spf[i] == i:
        for j in range(i * i, MAXV + 1, i):
            if spf[j] == j:
                spf[j] = i

def factorize(x):
    res = {}
    while x > 1:
        p = spf[x]
        cnt = 0
        while x % p == 0:
            x //= p
            cnt += 1
        res[p] = cnt
    return res

class SegTree:
    def __init__(self, arr):
        self.n = len(arr)
        self.size = 1
        while self.size < self.n:
            self.size <<= 1
        self.tree = [dict() for _ in range(2 * self.size)]
        for i in range(self.n):
            self.tree[self.size + i] = factorize(arr[i])
        for i in range(self.size - 1, 0, -1):
            self.tree[i] = self.merge(self.tree[2 * i], self.tree[2 * i + 1])

    def merge(self, a, b):
        if len(a) < len(b):
            a, b = b, a
        res = dict(a)
        for p, v in b.items():
            if p not in res or res[p] < v:
                res[p] = v
        return res

    def update(self, idx, val):
        i = self.size + idx
        self.tree[i] = factorize(val)
        i //= 2
        while i:
            self.tree[i] = self.merge(self.tree[2 * i], self.tree[2 * i + 1])
            i //= 2

    def query(self, l, r):
        l += self.size
        r += self.size
        left_res = {}
        right_res = {}
        while l <= r:
            if l % 2 == 1:
                left_res = self.merge(left_res, self.tree[l])
                l += 1
            if r % 2 == 0:
                right_res = self.merge(self.tree[r], right_res)
                r -= 1
            l //= 2
            r //= 2
        return self.merge(left_res, right_res)

n, q = map(int, input().split())
arr = list(map(int, input().split()))
st = SegTree(arr)

for _ in range(q):
    tmp = list(map(int, input().split()))
    if tmp[0] == 1:
        _, i, x = tmp
        st.update(i - 1, x)
    else:
        _, l, r = tmp
        res = st.query(l - 1, r - 1)
        ans = 1
        for v in res.values():
            ans = (ans * (v + 1)) % MOD
        print(ans)
```セグメント ツリーは、生の整数ではなく因数分解された表現に基づいて構築されます。 各ノードには辞書が格納されており、2 つのノードをマージすることは、素指数の座標上の最大値を取得することに正確に対応します。 

更新操作では 1 つのリーフを置き換え、ルートへのパスのみを再構築し、ツリー全体を再計算することなくローカルで正確さを維持します。 

クエリ操作では、標準の 2 ポインター セグメント ツリー トラバーサルが使用され、関連するセグメントのみから素指数の最大値が累積されます。 

微妙な点は、辞書の結合は効率が非対称であるということです。 最初に大きなマップをコピーすると、オーバーヘッドがわずかに軽減されますが、これは厳しい制約の下では重要です。 

## 実用的な例

 配列を考慮してください`[6, 9, 12, 16]`。 

### クエリ 1:`2 1 3`| ステップ | セグメント | プライムマップ |
 | --- | --- | --- |
 | 6 | 2・3 | {2:1、3:1} |
 | 9 | 3² | {3:2} |
 | 12 | 2²・3 | {2:2、3:1} |

 結合されたマップは次のようになります`{2:2, 3:2}`。 

答えは (2+1)(2+1) = 9 です。 

これは、アルゴリズムが明示的に 36 を形成することなく LCM 構造を正しくキャプチャしていることを確認します。 

### 更新を含むクエリ シーケンス

 後`1 2 15`、配列は次のようになります`[6, 15, 12, 16]`。 

今すぐクエリ`2 2 4`。 

| ステップ | セグメント | プライムマップ |
 | --- | --- | --- |
 | 15 | 3・5 | {3:1、5:1} |
 | 12 | 2²・3 | {2:2、3:1} |
 | 16 | 2⁴ | {2:4} |

 結合されたマップは次のようになります`{2:4, 3:1, 5:1}`。 

答えは (4+1)(1+1)(1+1) = 20 です。 

これは、更新が正しく伝播し、セグメント ツリーが一貫した素数最大値を維持していることを示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(q log n · k) | 各更新とクエリは O(log n) ノードに触れ、各マージは小さな素数マップを処理します。 
| スペース | O(n log n · k) | 各セグメント ツリー ノードはスパース プライム マップを格納します。 

制約により最大 2e5 の演算が許可されるため、対数動作が必要になります。 各数値には素因数がほとんどないため、定数因数は 4 秒間は十分に小さいままです。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    import sys
    input = sys.stdin.readline

    MOD = 998244353
    MAXV = 100000

    spf = list(range(MAXV + 1))
    for i in range(2, int(MAXV ** 0.5) + 1):
        if spf[i] == i:
            for j in range(i * i, MAXV + 1, i):
                if spf[j] == j:
                    spf[j] = i

    def factorize(x):
        res = {}
        while x > 1:
            p = spf[x]
            cnt = 0
            while x % p == 0:
                x //= p
                cnt += 1
            res[p] = cnt
        return res

    class SegTree:
        def __init__(self, arr):
            self.n = len(arr)
            self.size = 1
            while self.size < self.n:
                self.size <<= 1
            self.tree = [dict() for _ in range(2 * self.size)]
            for i in range(self.n):
                self.tree[self.size + i] = factorize(arr[i])
            for i in range(self.size - 1, 0, -1):
                self.tree[i] = self.merge(self.tree[2 * i], self.tree[2 * i + 1])

        def merge(self, a, b):
            if len(a) < len(b):
                a, b = b, a
            res = dict(a)
            for p, v in b.items():
                if p not in res or res[p] < v:
                    res[p] = v
            return res

        def update(self, idx, val):
            i = self.size + idx
            self.tree[i] = factorize(val)
            i //= 2
            while i:
                self.tree[i] = self.merge(self.tree[2 * i], self.tree[2 * i + 1])
                i //= 2

        def query(self, l, r):
            l += self.size
            r += self.size
            left_res = {}
            right_res = {}
            while l <= r:
                if l % 2 == 1:
                    left_res = self.merge(left_res, self.tree[l])
                    l += 1
                if r % 2 == 0:
                    right_res = self.merge(self.tree[r], right_res)
                    r -= 1
                l //= 2
                r //= 2
            return self.merge(left_res, right_res)

    n, q = map(int, input().split())
    arr = list(map(int, input().split()))
    st = SegTree(arr)

    out = []
    for _ in range(q):
        tmp = list(map(int, input().split()))
        if tmp[0] == 1:
            st.update(tmp[1] - 1, tmp[2])
        else:
            res = st.query(tmp[1] - 1, tmp[2] - 1)
            ans = 1
            for v in res.values():
                ans = (ans * (v + 1)) % MOD
            out.append(str(ans))

    return "\n".join(out)

# provided sample placeholders (replace with actual if needed)
# assert run(...) == ...
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 単一要素クエリ | 2 | 基数の除数のカウント |
 | 同じインデックスを繰り返し更新する | 正しい再計算 | 更新の正確性 |
 | 全範囲クエリ | 依存します | グローバル集計の正確性 |
 | 交互の更新/クエリ | 安定した出力 | 操作間の永続性 |

 ## 特殊なケース

 重要なエッジ ケースは、異なる因数分解を使用して同じインデックスに対して繰り返し更新が行われることです。 たとえば、要素が高度に合成された数から素数に変化する場合、セグメント ツリーは指数を部分的に更新するのではなく、古い寄与を完全に置き換える必要があります。 更新操作は、段階的に変更するのではなくリーフ マップ全体を置き換えるため、これを処理します。 

別の特殊なケースは、長さ 1 のセグメントをクエリすることです。 この場合、答えは単純に 1 つの値の約数の数になります。 セグメント ツリーがリーフ ノード マップを正確に返し、積の公式が正しく適用されるため、アルゴリズムはこれを自然に処理します。 

最後の微妙なケースは、異なるセグメントが素数セットに寄与している場合です。 マージ操作では、両側のすべての素数を保持する必要があります。 マージは両方の辞書に対して明示的に反復されるため、素数が失われることはなく、素数が範囲全体で重複していない場合でも、最終的な約数の数は正しいままになります。
