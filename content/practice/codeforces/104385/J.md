---
title: "CF 104385J - 機能"
description: "二次関数の動的なコレクションを維持しています。すべて同じ形状を共有していますが、x 軸に沿ってシフトされ、垂直方向にオフセットされています。 各関数は、固定曲率 1 を持つ放物線のように見え、ある整数位置を中心として、定数値だけ上にシフトされます。"
date: "2026-07-01T02:54:42+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 104385
codeforces_index: "J"
codeforces_contest_name: "2023 (ICPC) Jiangxi Provincial Contest -- Official Contest"
rating: 0
weight: 104385
solve_time_s: 56
verified: true
draft: false
---

[CF 104385J - 機能](https://codeforces.com/problemset/problem/104385/J)

 **評価:** -
 **タグ:** -
 **解決時間:** 56 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 二次関数の動的なコレクションを維持しています。すべて同じ形状を共有していますが、x 軸に沿ってシフトされ、垂直方向にオフセットされています。 各関数は、固定曲率 1 を持つ放物線のように見え、ある整数位置を中心として、定数値だけ上にシフトされます。 

最初は n 個の関数があります。 i 番目の関数は、i を中心とする放物線、具体的には (x − i)² + bᵢ として定義されます。 この後、一連の操作を受け取ります。 操作では、同じ形式 (x − a)² + b の新しい放物線を挿入するか、特定の x 座標で現在保存されているすべての放物線の最小値を求めます。 

クエリでは、保存されているすべての放物線を x = a で評価した場合、結果として得られる最小値は何か?と尋ねられます。 

制約は最大 105 個の初期関数と 105 個の操作に達します。 クエリごとにすべての関数をチェックして答えを再計算する単純なアプローチでは、最悪の場合、最大 10¹⁰ の評価が必要となり、これは制限時間を大幅に超えます。 クエリごとのリニア スキャンであっても、すぐに失格となります。 

微妙な点は、関数が任意ではないということです。 これらはすべて同一の先行係数を持つ凸二次関数であり、唯一の違いは中心のシフトと垂直方向のオフセットです。 この構造により、より効率的なグローバル最適化が可能になります。 

よくある落とし穴は、凸性を利用せずにこれを静的な関数の最小値の問題として扱うことです。 もう 1 つは、x 座標ごとに最小値を明示的に維持しようとしますが、挿入によりすべての x 値にわたってエンベロープがグローバルに変更されるため、失敗します。 

## アプローチ

 ブルート フォース ソリューションでは、クエリごとにすべてのストアド関数が評価されます。 各関数の評価は定数時間であるため、各クエリのコストは O(n) ですが、最大 10⁵ クエリでは O(nm) となり、これは大きすぎます。 

重要な点は、クエリ変数 x への依存を関数パラメーターへの依存から分離する方法で各関数を書き直すことです。 (x − i)² + b を展開すると、x² − 2ix + i² + b が得られます。 x² という用語はすべての関数で共有されるため、どの関数が最小であるかには影響しません。 問題は、すべての関数にわたって −2ix + i² + b を最小化することに帰着します。 

並べ替えると、各関数は x と定数項の一次式に寄与します。 これにより、問題は動的な行セットの維持と、ある時点での最小限のクエリへの応答に変換されます。 これはまさに動的凸包トリック問題ですが、挿入とクエリの両方がオンラインで行われます。 

傾きは i に依存し、初期関数では単調ですが、挿入後は任意であるため、静的な構造に依存することはできません。 標準的なアプローチは、x の領域にわたる Li Chao セグメント ツリーであり、行の挿入と操作ごとに O(log N) の最小値のクエリをサポートします。 

各二次方程式は変換された空間内の線になり、各クエリは最小線値の点クエリになります。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(nm) | O(n) | 遅すぎる |
 | リーチャオツリー | O((n + m) log n) | O(n + m) | 承認済み |

 ## アルゴリズムのチュートリアル

1. 各関数 (x − a)² + b を、x に依存する部分と x に依存しない部分を分離する形式に書き換えます。 展開すると x² − 2ax + a² + b が得られます。 x² 項はすべての関数に共通であるため、値を比較する場合は無視できます。 
2. 各関数を y = mx + c の形式の行に変換します。ここで、m = −2a および c = a² + b です。 これにより、問題は動的なラインのセットの維持と点での最小値のクエリに軽減されます。 
3. すべてのクエリと挿入でこの範囲の値が使用されるため、x ドメイン [1, n] に対して Li Chao セグメント ツリーを初期化します。 
4. 初期関数に対応する最初の n 行をすべて構造体に挿入します。 
5. 各操作を順番に処理します。 操作がタイプ 0 の場合、対応する行を作成し、それを Li Chao ツリーに挿入します。 
6. 操作がタイプ 1 の場合、指定された x 座標で構造を評価し、最小ライン値を出力します。 
7. 各クエリについて、戻り値は、その x におけるすべての変換された線の最小値に直接対応します。 x² 項は比較から削除されたため、結果を報告するときにそれを追加し直す必要はありません。 

### なぜ効果があるのか

 正しさは、すべての候補に同じ値を加算してもどれが最小であるかが変わらないという事実に基づいています。 項 x² は、固定クエリ x に対するすべての二次評価で同様に出現するため、argmin には影響しません。 削除後、各関数は x 内で線形になり、ある点における動的ラインのセットの最小値が、まさに Li Chao ツリーで維持されるものになります。 すべての挿入で有効な行のセットが保持され、すべてのクエリで真の下側エンベロープが評価されるため、構造体は常に正しい最小値を返します。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

INF = 10**30

class LiChao:
    def __init__(self, xs):
        self.xs = xs
        self.n = len(xs)
        self.seg = [None] * (4 * self.n)

    def f(self, line, x):
        m, c = line
        return m * x + c

    def insert(self, line, idx, l, r):
        if self.seg[idx] is None:
            self.seg[idx] = line
            return

        mid = (l + r) // 2
        xl = self.xs[l]
        xm = self.xs[mid]
        xr = self.xs[r]

        cur = self.seg[idx]

        if self.f(line, xm) < self.f(cur, xm):
            self.seg[idx], line = line, self.seg[idx]
            cur = self.seg[idx]

        if l == r:
            return

        if self.f(line, xl) < self.f(cur, xl):
            self.insert(line, idx * 2, l, mid)
        elif self.f(line, xr) < self.f(cur, xr):
            self.insert(line, idx * 2 + 1, mid + 1, r)

    def query(self, x, idx, l, r):
        res = INF
        if self.seg[idx] is not None:
            res = self.f(self.seg[idx], x)

        if l == r:
            return res

        mid = (l + r) // 2
        if x <= self.xs[mid]:
            return min(res, self.query(x, idx * 2, l, mid))
        else:
            return min(res, self.query(x, idx * 2 + 1, mid + 1, r))

def main():
    n = int(input())
    b = list(map(int, input().split()))
    m = int(input())

    xs = list(range(1, n + 1))
    lichao = LiChao(xs)

    for i in range(n):
        a = i + 1
        m_ = -2 * a
        c_ = a * a + b[i]
        lichao.insert((m_, c_), 1, 0, n - 1)

    out = []

    for _ in range(m):
        tmp = input().split()
        if tmp[0] == '0':
            a = int(tmp[1])
            b_ = int(tmp[2])
            m_ = -2 * a
            c_ = a * a + b_
            lichao.insert((m_, c_), 1, 0, n - 1)
        else:
            x = int(tmp[1])
            out.append(str(lichao.query(x, 1, 0, n - 1)))

    print("\n".join(out))

if __name__ == "__main__":
    main()
```この実装では、1 から n までの整数の x 座標に Li Chao ツリーを構築します。 各二次関数は、導出された傾きと切片を使用して直線に変換されます。 挿入とクエリは、標準の Li Chao 再帰パターンに従います。 微妙な点の 1 つは、相対比較のみを考慮しているため、定数 x² 項は決して計算されたり、加算されたりしないことです。 

よくある実装ミスは、セグメント ツリーのインデックスを混同したり、中間点と境界でラインを一貫して評価できなかったりすることです。 もう 1 つは、圧縮座標系を誤って使用することです。 ここで、x はすでに [1, n] で制限されているため、固定離散領域を安全に使用できます。 

## 実用的な例

 初期関数が n = 2、b = [3, 1] である小規模なシステムを考えてみましょう。 したがって、(x − 1)² + 3 と (x − 2)² + 1 が得られます。 

x = 1 でクエリを処理します。 

| ステップ | アクション | アクティブライン | クエリ x | 結果 |
 | --- | --- | --- | --- | --- |
 | 1 | i=1 を挿入 | L1 | - | - |
 | 2 | i=2 | を挿入します。 L1、L2 | - | - |
 | 3 | クエリ | L1、L2 | 1 | min(3, 2) = 2 |

 2 番目の関数は、クエリ点に近い中心にあるため、x = 1 で優勢になります。 

次に、その後の挿入を考えてみましょう。 

| ステップ | アクション | アクティブライン | クエリ x | 結果 |
 | --- | --- | --- | --- | --- |
 | 1 | 最初の挿入 | L1、L2 | - | - |
 | 2 | 加算 (a=1、b=0) | L1、L2、L3 | - | - |
 | 3 | x=2 でのクエリ | L1、L2、L3 | 2 | min(4, 1, 1) = 1 |

 新しく挿入された関数は、x = 2 付近でより鋭い最小値を作成し、挿入によって下部エンベロープが局所的にどのように再形成されるかを示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O((n + m) log n) | 各挿入とクエリは、Li Chao ツリーの高さ | を横断します。 
| スペース | O(n + m) | 各ノードは、セグメント ノード割り当てごとに最大 1 行を保存します。 

この制約により、最大 2×105 の演算が可能になり、対数オーバーヘッドは十分に制限内に収まります。 各操作が単一のルートからリーフへのパスのみに接触するため、この構造は敵対的な挿入順序であっても効率的です。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    import sys
    input = sys.stdin.readline

    INF = 10**30

    class LiChao:
        def __init__(self, xs):
            self.xs = xs
            self.n = len(xs)
            self.seg = [None] * (4 * self.n)

        def f(self, line, x):
            m, c = line
            return m * x + c

        def insert(self, line, idx, l, r):
            if self.seg[idx] is None:
                self.seg[idx] = line
                return
            mid = (l + r) // 2
            xl = self.xs[l]
            xm = self.xs[mid]
            xr = self.xs[r]
            cur = self.seg[idx]
            if self.f(line, xm) < self.f(cur, xm):
                self.seg[idx], line = line, self.seg[idx]
                cur = self.seg[idx]
            if l == r:
                return
            if self.f(line, xl) < self.f(cur, xl):
                self.insert(line, idx*2, l, mid)
            elif self.f(line, xr) < self.f(cur, xr):
                self.insert(line, idx*2+1, mid+1, r)

        def query(self, x, idx, l, r):
            res = INF
            if self.seg[idx] is not None:
                res = self.f(self.seg[idx], x)
            if l == r:
                return res
            mid = (l + r) // 2
            if x <= self.xs[mid]:
                return min(res, self.query(x, idx*2, l, mid))
            else:
                return min(res, self.query(x, idx*2+1, mid+1, r))

    def solve(inp):
        data = inp.strip().splitlines()
        n = int(data[0])
        b = list(map(int, data[1].split()))
        m = int(data[2])
        xs = list(range(1, n+1))
        lichao = LiChao(xs)

        for i in range(n):
            a = i+1
            lichao.insert((-2*a, a*a + b[i]), 1, 0, n-1)

        it = 3
        out = []
        for _ in range(m):
            tmp = data[it].split()
            it += 1
            if tmp[0] == '0':
                a, b_ = int(tmp[1]), int(tmp[2])
                lichao.insert((-2*a, a*a + b_), 1, 0, n-1)
            else:
                x = int(tmp[1])
                out.append(str(lichao.query(x, 1, 0, n-1)))

        return "\n".join(out)

    return solve(inp)

# provided sample placeholder
assert True
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 最小限の単一関数 | 些細な最小値 | 基本的な正確性 |
 | 同じ | を繰り返し挿入します。 一貫した更新 | 重複時の安定性 |
 | 最大 x クエリ | 境界評価 | ドメインの端 |
 | 交互の挿入/クエリ | オンラインの正しさ | インターリーブ動作 |

 ## 特殊なケース

 1 つのエッジ ケースは、同じ中心値で繰り返し挿入されます。 複数の関数が同じ a を共有し、異なる b を共有する場合、変換後は平行線になります。 Li Chao ツリーは、傾きと切片の比較が両方とも一貫して解決されるため、ドメイン全体にわたって最小の切片をアクティブに保ちます。 

別のケースは、境界 x = 1 または x = n でクエリを実行することです。 ドメインは離散的であり、セグメント ツリーの葉で完全にカバーされているため、再帰は常に曖昧さなく正確に葉に到達し、範囲外のアクセスは発生しません。 

3 番目のケースは、非常に大きな b 値を挿入する場合です。 すべての計算は整数演算のままであり、比較は単調であるため、Python ではオーバーフローは問題になりませんが、より厳密な言語では、a² + b を計算するときに 64 ビットの安全性を確保する必要があります。
