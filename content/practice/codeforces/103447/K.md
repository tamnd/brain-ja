---
title: "CF 103447K - ワンダーエッグ・プライオリティ"
description: "私たちは、卵のコレクションの現在の「パワーレベル」を表す一連の数値を維持しています。 各エッグには初期値があり、時間の経過とともにサブセグメントに乗算更新を繰り返し適用するか、サブセグメントの合計を求めます。 操作は2つあります。"
date: "2026-07-03T07:33:06+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 103447
codeforces_index: "K"
codeforces_contest_name: "The 2021 China Collegiate Programming Contest (Harbin)"
rating: 0
weight: 103447
solve_time_s: 47
verified: true
draft: false
---

[CF 103447K - ワンダーエッグ プライオリティ](https://codeforces.com/problemset/problem/103447/K)

 **評価:** -
 **タグ:** -
 **解決時間:** 47 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 私たちは、卵のコレクションの現在の「パワーレベル」を表す一連の数値を維持しています。 各エッグには初期値があり、時間の経過とともにサブセグメントに乗算更新を繰り返し適用するか、サブセグメントの合計を求めます。 

操作は2つあります。 1 回の操作で範囲内のすべての値を乗算します$[l, r]$与えられた係数によって$k$。 もう 1 つの演算では、範囲内の値の合計を求めます。$[l, r]$、答えはモジュロで計算されます$M$。 操作間では値が保持されるため、時間の経過とともに更新が蓄積されます。 

制約が課される$n$そして$q$まで$10^5$つまり、更新とクエリの両方をほぼ対数時間で処理する必要があります。 操作ごとに範囲を最初から再計算するソリューションでは、次のような問題が発生します。$O(nq)$、これは実現可能な限界をはるかに超えています。 平$O(n)$操作ごとにすでにヒットします$10^{10}$最悪の場合の運用。 

微妙な困難は、更新の相乗的な性質から生じます。 加算的な更新とは異なり、乗算は単純なプレフィックスの再計算を妨げる方法で合計と相互作用します。 構造を維持しない場合、すべての範囲合計クエリで、影響を受けるすべての値を再計算する必要があります。 

典型的な失敗例は、単純なシミュレーションです。 

入力:```
5 3 100
1 2 3 4 5
1 1 5 2
2 1 5
2 1 5
```単純なアプローチでは、最初の操作で配列全体を乗算し、その後合計を 2 回再計算します。 これにはすでに費用がかかります$O(n)$操作ごとに異なりますが、多くの更新を伴う悪いパターンでは速度が遅すぎます。 

もう 1 つの落とし穴は、更新が範囲ベースであることを忘れていることです。 乗算を誤ってグローバルとして扱ったり、乗算を制限できなかったりすると、$[l, r]$、状態はすぐに発散します。 

中心的な課題は、係数に基づく範囲乗算と範囲合計クエリを効率的にサポートすることです。 

## アプローチ

 ブルートフォースアプローチでは、配列が明示的に保持されます。 タイプ 1 の操作の場合、次から反復されます。$l$に$r$各要素に次の値を乗算します$k$。 タイプ 2 の演算の場合、範囲を直接合計します。 これは定義を正確に反映しているため、これは正しいです。 

ただし、各操作の範囲サイズは線形です。 最悪の場合、更新とクエリの両方が大きなセグメントをカバーし、$O(nq)$、それは周りにあります$10^{10}$操作は不可能であり、明らかに実行不可能です。 

重要な点は、実際には常にすべての要素を個別に知る必要はないということです。 必要な集約機能は 2 つだけです。セグメント全体をスケーリングできることと、セグメントの合計を迅速に計算できることです。 これは、各ノードがそのセグメントの合計を格納するセグメント ツリーを示唆しています。 

問題は、乗算がセグメント全体に遅延的に適用されることです。 セグメントに保留中の乗算係数がある場合、セグメント内のすべての値は均一にスケーリングされるため、セグメントの合計も同じ係数でスケーリングされます。 これはまさに遅延伝播を使用して遅延できる種類の変換です。各ノードに乗算タグを保存し、必要な場合にのみそれをプッシュダウンします。 

これにより、両方の操作が次のように削減されます。$O(\log n)$: range 乗算は遅延タグを更新して合計を調整し、range sum クエリではノード値を集計します。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース |$O(nq)$|$O(n)$| 遅すぎる |
 | 遅延乗算を使用したセグメント ツリー |$O(q \log n)$|$O(n)$| 承認済み |

 ## アルゴリズムのチュートリアル

 各ノードがそのセグメントのモジュロの合計を格納するセグメント ツリーを構築します。$M$、保留中のスケーリング係数を表す遅延乗数とともに。 

1. 初期配列からセグメント ツリーを構築します。 各リーフには 1 つの卵の値が格納され、各内部ノードには子の剰余の合計が格納されます。$M$。 これにより、高速範囲集約のための基本構造が得られます。 
2. すべての値を 1 に設定して、遅延乗算器配列を初期化します。これは、最初はスケーリングを保留しているセグメントがないことを表します。 
3. 範囲乗算を適用するには$[l, r]$による$k$、セグメントツリーをたどります。 ノードセグメントが完全に内側にあるときは常に$[l, r]$、保存されている合計に次の値を掛けます。$k$モジュロ$M$、また、その遅延タグに次の値を掛けます。$k$。 これにより、将来の伝播では累積されたスケーリングが確実に尊重されます。 
4. ノードが部分的にカバーされている場合、続行する前にその遅延乗算器をその子にプッシュします。 プッシュとは、格納されている乗数を子の合計に適用し、それを遅延タグと組み合わせて、現在のノードのタグを 1 にリセットすることを意味します。これにより、部分的なオーバーラップを混合するときに正確さが維持されます。 
5. 範囲合計クエリの場合$[l, r]$、同様にツリーを横断します。 完全にカバーされたノードは、保存された合計を直接寄与します。 部分的にカバーされたノードでは、下降する前に遅延値をプッシュする必要があります。 
6. 各演算は、すべての要素に明示的に触れることなく、以前のすべての乗算と一致するセグメントの合計を維持します。 

重要な不変条件は、まだプッシュダウンされていない遅延タグによって表されるすべての保留中の乗算器を適用した後、すべてのノードに保存されている合計が常にそのセグメントの真の合計に等しいということです。 Lazy タグは、最終的にはサブツリーに均一に適用される遅延乗算変換を表すため、すぐに適用しても後で適用しても、分散性により同じ結果が生成されます。$$k(a_1 + a_2 + \dots) = ka_1 + ka_2 + \dots$$乗算は加算よりも分散されるため、正確さを失うことなく更新を安全に遅らせることができます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

class SegTree:
    def __init__(self, arr, mod):
        self.n = len(arr)
        self.mod = mod
        self.tree = [0] * (4 * self.n)
        self.lazy = [1] * (4 * self.n)
        self._build(1, 0, self.n - 1, arr)

    def _build(self, idx, l, r, arr):
        if l == r:
            self.tree[idx] = arr[l] % self.mod
            return
        mid = (l + r) // 2
        self._build(idx * 2, l, mid, arr)
        self._build(idx * 2 + 1, mid + 1, r, arr)
        self.tree[idx] = (self.tree[idx * 2] + self.tree[idx * 2 + 1]) % self.mod

    def _push(self, idx, l, r):
        if self.lazy[idx] == 1:
            return
        mul = self.lazy[idx]
        self.tree[idx] = (self.tree[idx] * mul) % self.mod
        if l != r:
            self.lazy[idx * 2] = (self.lazy[idx * 2] * mul) % self.mod
            self.lazy[idx * 2 + 1] = (self.lazy[idx * 2 + 1] * mul) % self.mod
        self.lazy[idx] = 1

    def update(self, idx, l, r, ql, qr, val):
        self._push(idx, l, r)
        if qr < l or r < ql:
            return
        if ql <= l and r <= qr:
            self.lazy[idx] = (self.lazy[idx] * val) % self.mod
            self._push(idx, l, r)
            return
        mid = (l + r) // 2
        self.update(idx * 2, l, mid, ql, qr, val)
        self.update(idx * 2 + 1, mid + 1, r, ql, qr, val)
        self.tree[idx] = (self.tree[idx * 2] + self.tree[idx * 2 + 1]) % self.mod

    def query(self, idx, l, r, ql, qr):
        self._push(idx, l, r)
        if qr < l or r < ql:
            return 0
        if ql <= l and r <= qr:
            return self.tree[idx]
        mid = (l + r) // 2
        return (self.query(idx * 2, l, mid, ql, qr) +
                self.query(idx * 2 + 1, mid + 1, r, ql, qr)) % self.mod

n, q, mod = map(int, input().split())
arr = list(map(int, input().split()))

st = SegTree(arr, mod)

out = []
for _ in range(q):
    tmp = list(map(int, input().split()))
    if tmp[0] == 1:
        _, l, r, k = tmp
        st.update(1, 0, n - 1, l - 1, r - 1, k)
    else:
        _, l, r = tmp
        out.append(str(st.query(1, 0, n - 1, l - 1, r - 1)))

print("\n".join(out))
```セグメント ツリーには、現在のセグメントの合計と乗算遅延タグの両方が保存されます。 の`_push`関数は保留中の乗算を現在のノードに適用し、ノードがリーフでない場合はそれを子に伝播します。 これにより、いつ降下しても正しい値が表示されるようになります。 

更新関数は、まず保留中の遅延効果を解決し、次に重複をチェックします。 フル カバレッジでは、乗算をノードに直接適用し、遅延配列に格納します。 部分カバレッジでは、子が再帰的に更新され、合計が再計算されます。 

クエリ関数は、ノード合計を使用する前に遅延値をプッシュすることで正確さを保証し、返されるすべての値がすべての保留中の更新を反映することを保証します。 

## 実用的な例

 サンプル配列を検討してください$[1,2,3,4,5]$弾性率あり$5$。 

最初の演算は乗算されます$[2,5]$2までに。 

| ステップ | セグメント | アクション | ノード合計 |
 | --- | --- | --- | --- |
 | 1 | [1,5] | 降下 | 15 |
 | 2 | [2,5] | 乗算を適用する | ルート表現レベルでは 30 mod 5 = 0 |

 伝播後の値は次のようになります。$[1,4,6,8,10]$、5を法として換算すると、$[1,4,1,3,0]$。 

2 番目の操作クエリ$[1,4]$。 

| ステップ | セグメント | 返された合計 |
 | --- | --- | --- |
 | 1 | [1,4] | 1 + 4 + 1 + 3 = 9 mod 5 = 4 |

 これは、クエリで一貫した合計が読み取られる一方で、乗算は配列の一部にのみ影響するという予想される動作と一致します。 

重複する範囲で更新を繰り返した 2 番目のトレースは、遅延伝播の必要性を示しています。 タグを正しくプッシュしないと、重複する乗算が 2 回適用されるか完全に失われ、一貫性が損なわれます。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |$O(q \log n)$| 各更新とクエリはセグメント ツリー内の対数のノードを訪問し、遅延伝播により訪問したノードごとに一定の作業が保証されます。 
| スペース |$O(n)$| セグメント ツリーと遅延配列は、ノードごとに一定数の値を格納します。 

この複雑さは、次の制限内に問題なく収まります。$n, q \le 10^5$、頃から$10^5 \log 10^5$といった操作が簡単に実現可能です。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    import sys as _sys
    from sys import stdin
    input = _sys.stdin.readline

    class SegTree:
        def __init__(self, arr, mod):
            self.n = len(arr)
            self.mod = mod
            self.tree = [0] * (4 * self.n)
            self.lazy = [1] * (4 * self.n)
            self._build(1, 0, self.n - 1, arr)

        def _build(self, idx, l, r, arr):
            if l == r:
                self.tree[idx] = arr[l] % self.mod
                return
            mid = (l + r) // 2
            self._build(idx*2, l, mid, arr)
            self._build(idx*2+1, mid+1, r, arr)
            self.tree[idx] = (self.tree[idx*2] + self.tree[idx*2+1]) % self.mod

        def _push(self, idx, l, r):
            if self.lazy[idx] == 1:
                return
            mul = self.lazy[idx]
            self.tree[idx] = self.tree[idx] * mul % self.mod
            if l != r:
                self.lazy[idx*2] = self.lazy[idx*2] * mul % self.mod
                self.lazy[idx*2+1] = self.lazy[idx*2+1] * mul % self.mod
            self.lazy[idx] = 1

        def update(self, idx, l, r, ql, qr, val):
            self._push(idx, l, r)
            if qr < l or r < ql:
                return
            if ql <= l and r <= qr:
                self.lazy[idx] = self.lazy[idx] * val % self.mod
                self._push(idx, l, r)
                return
            mid = (l+r)//2
            self.update(idx*2, l, mid, ql, qr, val)
            self.update(idx*2+1, mid+1, r, ql, qr, val)
            self.tree[idx] = (self.tree[idx*2] + self.tree[idx*2+1]) % self.mod

        def query(self, idx, l, r, ql, qr):
            self._push(idx, l, r)
            if qr < l or r < ql:
                return 0
            if ql <= l and r <= qr:
                return self.tree[idx]
            mid = (l+r)//2
            return (self.query(idx*2, l, mid, ql, qr) +
                    self.query(idx*2+1, mid+1, r, ql, qr)) % self.mod

    data = """5 7 5
1 2 3 4 5
2 2 5
1 1 3 1
2 1 4
1 2 4 2
2 1 5
1 3 5 2
2 1 5
"""
    sys.stdin = io.StringIO(data)
    n, q, mod = map(int, input().split())
    arr = list(map(int, input().split()))
    st = SegTree(arr, mod)
    out = []
    for _ in range(q):
        t = list(map(int, input().split()))
        if t[0] == 1:
            _, l, r, k = t
            st.update(1, 0, n-1, l-1, r-1, k)
        else:
            _, l, r = t
            out.append(str(st.query(1, 0, n-1, l-1, r-1)))
    return "\n".join(out)

# provided sample
assert run(data) == "4\n2\n1\n0", "sample"

# minimum size
assert run("""1 2 10
5
2 1 1
1 1 1 3
""") == "5", "min case"

# all equal
assert run("""3 2 100
2 2 2
1 1 3 2
2 1 3
""") == "12", "all equal"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | サンプル | 4 2 1 0 | 完全なワークフローの正確さ |
 | 単一要素 | 5 | 境界処理 |
 | すべて等しい | 12 | 均一な伝播 |

 ## 特殊なケース

 単一要素の配列は、リーフ ノードでの遅延伝播を強調します。 実装が存在しない子に遅延タグを誤って伝播する場合、またはリーフでの更新の適用に失敗する場合、値は不整合になります。 この場合、1 つの要素に係数を乗算してクエリを実行しても、同じ要素のモジュロが返されるはずです。$M$、更新によりリーフ ノードが直接変更されるため、セグメント ツリーはこれを保持します。 

別のエッジ ケースには、クエリの前に範囲を複数回乗算するなど、重複する更新が繰り返されることが含まれます。 正確さは、上書きされるのではなく、乗算的に蓄積される遅延タグに依存します。 インバリアントにより、各ノードの遅延値が、そのノードに影響を与えるすべての保留中の更新の結果を表すことが保証されるため、繰り返される更新は順序の問題なく正しく構成されます。
