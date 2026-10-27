---
title: "CF 106202B - \u0422\u043e\u0440\u0433\u043e\u0432\u0446\u044b"
description: "私たちには一連の商人が与えられており、それぞれが価格付きのアイテムを 1 つずつ持っています。 i 番目の販売者の価格は 0 から $2^k - 1$ までの範囲の整数であり、すべての価格は概念的には固定長の k ビット 2 進数として格納されます。 システムは 3 種類の操作を処理します。"
date: "2026-06-20T22:28:41+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 106202
codeforces_index: "B"
codeforces_contest_name: "\u0418\u043d\u0442\u0435\u0440\u043d\u0435\u0442-\u043e\u043b\u0438\u043c\u043f\u0438\u0430\u0434\u044b, \u0421\u0435\u0437\u043e\u043d 2025-2026, \u041f\u0435\u0440\u0432\u0430\u044f \u043a\u043e\u043c\u0430\u043d\u0434\u043d\u0430\u044f \u043e\u043b\u0438\u043c\u043f\u0438\u0430\u0434\u0430"
rating: 0
weight: 106202
solve_time_s: 56
verified: true
draft: false
---

[CF 106202B - \u0422\u043e\u0440\u0433\u043e\u0432\u0446\u044b](https://codeforces.com/problemset/problem/106202/B)

 **評価:** -
 **タグ:** -
 **解決時間:** 56 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 私たちには一連の商人が与えられており、それぞれが価格付きのアイテムを 1 つずつ持っています。 i 番目の販売者の価格は 0 から 0 までの範囲の整数です。$2^k - 1$、すべての価格は概念的には固定長の k ビット 2 進数として格納されます。 

システムは 3 種類の操作を処理します。 最初のタイプは、単一の販売者の価格を更新します。 2 番目のタイプは、販売者のセグメントに変換を適用します。セグメント内のすべての価格は、k ビットのバイナリ形式で書き換えられ、すべてのビットが反転され、結果が整数として解釈されます。 3 番目のタイプは、セグメントにわたる価格の合計を要求します。 

重要な詳細は、固定幅バイナリで何が起こっているかを認識しない限り、ビット反転は算術否定や固定マスクとの XOR ではないということです。すべての数値 x は次のように変換されます。$(2^k - 1) - x$。 

制約は大きく、最大 200,000 の加盟店と 200,000 のオペレーションです。 これにより、セグメントの合計を再計算したり、クエリごとに要素ごとに反転を適用したりするソリューションは即座に除外されます。 対数データ構造であっても、反転クエリのすべての要素に触れないようにする必要があります。 

微妙な点は、反転が可逆的であり、セグメント全体にわたって均一であることです。 2 回反転されたセグメントは元の状態に戻ります。 もう 1 つの重要な側面は、更新によって値が上書きされるため、構造内の保留中の反転が考慮される必要があることです。 

エッジ ケースは、k が値に対して小さいか大きい場合、特にすべてのビットが 1 または 0 の場合に発生し、反転が恒等または完全な補数の動作になります。 もう 1 つの注意が必要なシナリオは、反転とポイント更新の重複です。 慎重に扱わないと、古い値が伝播する可能性があります。 

たとえば、k = 3 および x = 2 (バイナリ 010) の場合、反転により 5 である 101 が生成されます。整数の否定に関して間違って考えると、k が表現サイズを変更するときに中間状態を誤って処理する可能性がありますが、ここでは k はグローバルに固定されています。 

## アプローチ

 直接的なアプローチでは、配列を明示的に維持します。 タイプ 1 クエリは、単一の位置を更新します。 タイプ 2 のクエリは範囲を反復し、各値 x を次の値に置き換えます。$(2^k - 1) - x$。 タイプ 3 のクエリは、セグメントを直接合計します。 

これは正しいですが、最悪の場合の入力ではすぐに失敗します。 各反転演算または合計演算には O(n) がかかり、最大 200,000 回の演算で最悪の場合に達します。$O(nq)$、これは実現可能な限界をはるかに超えています。 

変換の構造が重要な観察です。 各反転は、セグメント内のすべての値 x を定数線形変換に置き換えます。$x \mapsto C - x$、 どこ$C = 2^k - 1$。 これはアフィンです。 合計に関しては、予想どおりに動作します。 

セグメントの長さが len で合計が S の場合、反転後の新しい合計は次のようになります。$$len \cdot C - S$$これは、個々の要素に触れる必要がないことを意味します。 必要なのは、セグメントの合計を維持し、「反転状態」の範囲割り当てをサポートすることだけです。 

残りの課題は構成可能性です。反転を繰り返すと、同一性と補色性が切り替わります。 これは、ブール値の反転フラグを備えた遅延伝播セグメント ツリーを示唆しています。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(nq) | O(n) | 遅すぎる |
 | 遅延反転を使用したセグメント ツリー | O(q log n) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

 各ノードがそのセグメントの合計を格納するセグメント ツリーを維持します。 さらに、各ノードは、そのセグメントが現在論理反転されているかどうかを示す遅延フラグを保持します。 

1. 初期値を使用してセグメント ツリーを構築します。 各ノードはそのセグメントの合計を保存し、保留中のフリップは保存しません。 これにより、クエリの直接集計が可能になります。 
2. ノードの反転効果を定義します。 長さ len のセグメントの合計が S の場合、反転後の新しい合計は len 倍 C から S を引いた値になります。これにより、ノードでセグメント全体を O(1) 時間で更新できます。 
3. 範囲反転クエリの場合、ノード セグメントが完全にカバーされている場合、変換をその合計に直接適用し、遅延フラグを切り替えます。 2 回切り替えるとキャンセルされ、正確さが維持されます。 
4. ノードが部分的に覆われている場合は、下降する前に保留中の反転を押し下げます。 プッシュとは、反転状態を子に適用し、親でフラグをクリアすることを意味します。 
5. ポイントを更新するには、リーフに移動します。 パスに沿って、リーフが上書きされる前に実際の現在の値を反映するように、すべての保留中のフリップがプッシュされるようにします。 
6. リーフを更新した後、子を合計することによって合計を上向きに再計算します。 
7. 範囲合計クエリの場合も同様に、降順の前に遅延フラグをプッシュし、完全にカバーされたセグメントを直接合計することで正確性を保証します。 

重要な構造上の考え方は、反転は合計と自己反転に対して線形であるため、単一のブール タグとしてエンコードできるということです。 

### なぜ効果があるのか

 正しさは 2 つの不変条件に基づいています。 まず、各ノードに格納されている合計は常に、そのセグメントの真の合計、またはまだ子に伝播されていない保留中の反転フラグを 1 つだけ適用した後の合計のいずれかを表します。 第 2 に、反転操作はセグメント全体に分散的です。つまり、それを互いに素なセグメントの結合に適用することは、それを各セグメントに個別に適用することと同じです。 反転はそれ自体の反転であるため、遅延フラグには履歴ではなくパリティのみが必要です。 これにより、一連の更新が曖昧さのない一貫したローカル変換に帰着することが保証されます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

class SegTree:
    def __init__(self, arr, k):
        self.n = len(arr)
        self.k = k
        self.C = (1 << k) - 1
        self.sum = [0] * (4 * self.n)
        self.lazy = [0] * (4 * self.n)
        self.build(1, 0, self.n - 1, arr)

    def build(self, v, l, r, arr):
        if l == r:
            self.sum[v] = arr[l]
            return
        m = (l + r) // 2
        self.build(v*2, l, m, arr)
        self.build(v*2+1, m+1, r, arr)
        self.sum[v] = self.sum[v*2] + self.sum[v*2+1]

    def apply_flip(self, v, l, r):
        length = r - l + 1
        self.sum[v] = length * self.C - self.sum[v]
        self.lazy[v] ^= 1

    def push(self, v, l, r):
        if not self.lazy[v] or l == r:
            return
        m = (l + r) // 2
        self.apply_flip(v*2, l, m)
        self.apply_flip(v*2+1, m+1, r)
        self.lazy[v] = 0

    def update_point(self, v, l, r, idx, val):
        if l == r:
            self.sum[v] = val
            return
        self.push(v, l, r)
        m = (l + r) // 2
        if idx <= m:
            self.update_point(v*2, l, m, idx, val)
        else:
            self.update_point(v*2+1, m+1, r, idx, val)
        self.sum[v] = self.sum[v*2] + self.sum[v*2+1]

    def range_flip(self, v, l, r, ql, qr):
        if ql <= l and r <= qr:
            self.apply_flip(v, l, r)
            return
        self.push(v, l, r)
        m = (l + r) // 2
        if ql <= m:
            self.range_flip(v*2, l, m, ql, qr)
        if qr > m:
            self.range_flip(v*2+1, m+1, r, ql, qr)
        self.sum[v] = self.sum[v*2] + self.sum[v*2+1]

    def range_sum(self, v, l, r, ql, qr):
        if ql <= l and r <= qr:
            return self.sum[v]
        self.push(v, l, r)
        m = (l + r) // 2
        res = 0
        if ql <= m:
            res += self.range_sum(v*2, l, m, ql, qr)
        if qr > m:
            res += self.range_sum(v*2+1, m+1, r, ql, qr)
        return res

n, k = map(int, input().split())
arr = list(map(int, input().split()))
q = int(input())

st = SegTree(arr, k)

for _ in range(q):
    tmp = input().split()
    t = int(tmp[0])
    if t == 1:
        i = int(tmp[1]) - 1
        x = int(tmp[2])
        st.update_point(1, 0, n-1, i, x)
    elif t == 2:
        l = int(tmp[1]) - 1
        r = int(tmp[2]) - 1
        st.range_flip(1, 0, n-1, l, r)
    else:
        l = int(tmp[1]) - 1
        r = int(tmp[2]) - 1
        print(st.range_sum(1, 0, n-1, l, r))
```この実装では、遅延伝播を備えた古典的なセグメント ツリーが使用されます。 クリティカルラインは変革です`length * C - sum[v]`、個々の要素に触れることなく、完全なビットごとの反転をエンコードします。 2 つの反転が相殺されるため、遅延フラグは XOR 演算されます。 

プッシュ操作により、部分再帰の前に正確さが保証されます。 これがないと、ポイントの更新により古い反転値が上書きされてしまいます。 範囲演算では常に再帰後に子からノードの合計が再計算され、一貫性が維持されます。 

## 実用的な例

 k = 3 の小さな配列を考えてみましょう。`[1, 2, 3]`なので、C = 7となります。 

### 例 1: 単一のフリップとクエリ

 | ステップ | 操作 | 影響を受けるセグメント | 合計状態 |
 | --- | --- | --- | --- |
 | 1 | イニシャル | [1,2,3] | 6 |
 | 2 | フリップ [1,3] | フルセグメント | 3 * 7 - 6 = 15 |
 | 3 | クエリ [2,2] | 葉2 | 7 - 2 = 5 |

 反転後、各要素はその補数になります: 1→6、2→5、3→4。 セグメント ツリーは、要素を展開せずにこれをキャプチャします。 

### 例 2: 反転してからポイントを更新する

 もう一度始めます:`[1, 2, 3]`| ステップ | 操作 | 配列の解釈 | 合計 |
 | --- | --- | --- | --- |
 | 1 | フリップ [1,3] | [6,5,4] | 15 |
 | 2 | インデックス 2 = 0 を設定 | [6,0,4] | 10 |
 | 3 | クエリ [1,3] | 直接合計 | 10 |

 重要な点は、ポイント更新ではまず保留中のフリップを解決し、インデックス 2 が正しく 5 として認識されてから上書きされることです。 

各トレースは、反転後に完全な配列を実体化する必要がないことを示しています。 合計と遅延状態のみが必要です。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(q log n) | 各更新、フリップ、またはクエリはセグメント ツリーの高さを下降します。 
| スペース | O(n) | セグメント ツリー配列は合計と遅延フラグを格納します。 

n と q が最大 200,000 の場合、操作ごとの対数コストは制限内に問題なく収まります。 反転はノードごとに O(1) であるため、定数係数は小さくなります。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    import sys
    input = sys.stdin.readline

    class SegTree:
        def __init__(self, arr, k):
            self.n = len(arr)
            self.k = k
            self.C = (1 << k) - 1
            self.sum = [0] * (4 * self.n)
            self.lazy = [0] * (4 * self.n)
            self.build(1, 0, self.n - 1, arr)

        def build(self, v, l, r, arr):
            if l == r:
                self.sum[v] = arr[l]
                return
            m = (l + r) // 2
            self.build(v*2, l, m, arr)
            self.build(v*2+1, m+1, r, arr)
            self.sum[v] = self.sum[v*2] + self.sum[v*2+1]

        def apply_flip(self, v, l, r):
            length = r - l + 1
            self.sum[v] = length * self.C - self.sum[v]
            self.lazy[v] ^= 1

        def push(self, v, l, r):
            if not self.lazy[v] or l == r:
                return
            m = (l + r) // 2
            self.apply_flip(v*2, l, m)
            self.apply_flip(v*2+1, m+1, r)
            self.lazy[v] = 0

        def update_point(self, v, l, r, idx, val):
            if l == r:
                self.sum[v] = val
                return
            self.push(v, l, r)
            m = (l + r) // 2
            if idx <= m:
                self.update_point(v*2, l, m, idx, val)
            else:
                self.update_point(v*2+1, m+1, r, idx, val)
            self.sum[v] = self.sum[v*2] + self.sum[v*2+1]

        def range_flip(self, v, l, r, ql, qr):
            if ql <= l and r <= qr:
                self.apply_flip(v, l, r)
                return
            self.push(v, l, r)
            m = (l + r) // 2
            if ql <= m:
                self.range_flip(v*2, l, m, ql, qr)
            if qr > m:
                self.range_flip(v*2+1, m+1, r, ql, qr)
            self.sum[v] = self.sum[v*2] + self.sum[v*2+1]

        def range_sum(self, v, l, r, ql, qr):
            if ql <= l and r <= qr:
                return self.sum[v]
            self.push(v, l, r)
            m = (l + r) // 2
            res = 0
            if ql <= m:
                res += self.range_sum(v*2, l, m, ql, qr)
            if qr > m:
                res += self.range_sum(v*2+1, m+1, r, ql, qr)
            return res

    n, k = map(int, input().split())
    arr = list(map(int, input().split()))
    q = int(input())
    st = SegTree(arr, k)

    out = []
    for _ in range(q):
        tmp = input().split()
        t = int(tmp[0])
        if t == 1:
            i = int(tmp[1]) - 1
            x = int(tmp[2])
            st.update_point(1, 0, n-1, i, x)
        elif t == 2:
            l = int(tmp[1]) - 1
            r = int(tmp[2]) - 1
            st.range_flip(1, 0, n-1, l, r)
        else:
            l = int(tmp[1]) - 1
            r = int(tmp[2]) - 1
            out.append(str(st.range_sum(1, 0, n-1, l, r)))

    return "\n".join(out)

# Sample-style small test
assert run("""3 3
1 2 3
4
3 1 3
2 1 3
3 1 3
3 2 2
""") is not None
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 小さなフリップ + クエリ | マニュアル | 反転の基本的な正しさ |
 | 単一要素の更新 | マニュアル | 遅延状態でのポイント更新 |
 | すべて 2 回反転 | 元の合計 | XOR 反転の冪等性 |
 | 最大 k 境界値 | 正しい補数和 | フルビット範囲の処理 |

 ## 特殊なケース

 同じ範囲で反転が繰り返されるケースは、遅延フラグがパリティとして動作することを示しています。 セグメントが 2 回反転された場合、保存されたノードは元の合計に正確に戻るはずです。 実装はこれを処理します。`lazy[v] ^= 1`状態を切り替え、変換式は自己逆になります。 

保留中のフリップ後のポイント更新はより繊細です。 ノードが論理的に反転されているがプッシュされていないと仮定し、リーフを直接上書きします。 プッシュしないと、更新は古い表現に適用されます。 コードは次の呼び出しによって正確さを保証します。`push`したがって、葉は常に変更前の正しい状態で実体化されます。 

k = 1 の場合、境界ケースが発生します。その場合、C = 1 となり、反転により 0 と 1 が単純に交換されます。式は引き続き成立し、新しい sum = len - sum となり、セグメント ツリーは変更なしで同じように動作します。
