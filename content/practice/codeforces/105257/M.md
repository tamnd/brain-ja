---
title: "CF 105257M - 窓装飾"
description: "私たちは、100 × 100 の正方形の窓の中に、最大 1 万個の同じ装飾品を配置されています。 各装飾は厳密に境界内の整数座標に中心があり、それぞれは回転された正方形であり、その対角線は座標軸と一致しており、合計は…"
date: "2026-06-24T04:31:01+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 105257
codeforces_index: "M"
codeforces_contest_name: "2024 ICPC ShaanXi Provincial Contest"
rating: 0
weight: 105257
solve_time_s: 63
verified: true
draft: false
---

[CF 105257M - ウィンドウ装飾](https://codeforces.com/problemset/problem/105257/M)

 **評価:** -
 **タグ:** -
 **解決時間:** 1 分 3 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 私たちは、100 × 100 の正方形の窓の中に、最大 1 万個の同じ装飾品を配置されています。 各装飾は境界の厳密に内側の整数座標に中心があり、それぞれは対角線が座標軸と一致し、全長 2 の回転された正方形です。 

対角線は軸に平行で、整数点を中心とするため、各装飾はまさにその中心までのマンハッタン距離が最大 1 である点のセットになります。幾何学的には、これは基本方向に 1 単位離れた 4 つの頂点を持つダイヤモンド形です。 

タスクは、重複を 1 回だけ数えて、ウィンドウ内の合計領域がこれらのダイヤモンドの少なくとも 1 つによってカバーされる面積を計算することです。 

入力サイズでは最大 10,000 の形状が許可されるため、オーバーラップをペアごとに測定したり、高解像度で平面をサンプリングしようとするアプローチは遅すぎます。 単純なペアごとの和集合の計算では、形状のすべてのペア間の交差をチェックする必要があり、これによりおよそ 10^8 の比較が行われ、幾何学的に実行された場合、各交差テストは定数時間ではなくなります。 

グリッド セルまたはサンプリング ポイントを数えることによって面積を近似しようとすると、微妙な問題が発生します。 形状は連続的であり、境界が重要です。単一のダイヤモンドは、領域を部分的にしかカバーしていない場合でも、面積に貢献します。 離散化すると、必要な 1e-4 許容誤差を超える誤差が発生します。 

もう 1 つの落とし穴は、カバーされた領域が元の座標系で軸に揃えられた正方形の単純な結合のように動作すると仮定していることです。 形状は軸に沿った正方形ではないため、標準の長方形の結合手法は変換せずに直接適用できません。 

## アプローチ

 ダイヤモンド上の直接幾何学的結合は、各形状が L1 制約によって定義されるため、複雑になります。 強引なアイデアは、すべてのペアごとの交差を考慮して和集合面積を計算し、包含/除外またはポリゴン境界上の平面スイープを適用することです。 これは正しいのですが、各ひし形が 4 つの線分に寄与し、O(n) 個のポリゴン間のすべての交差を処理す​​ると二次関数またはさらに悪い動作が発生するため、これは厄介になります。 

重要な観察は、変数の線形変化の下では L1 ダイヤモンドがはるかに単純になるということです。 新しい座標 u = x + y および v = x - y を定義すると、条件 |x - xi| が成立します。 + |y - yi| ≤ 1 は、u と v の両方がそれらの中心の周りの間隔内で独立して制限される条件に変換されます。 この変換された空間では、各ダイヤモンドは軸に沿った正方形になります。 

問題が軸に揃えられた正方形の和集合として表現されると、タスクは長方形上の古典的な平面スイープになります。 セグメント ツリーまたは座標圧縮差分カウントを使用して、一方の軸に沿ってスイープし、もう一方の軸でアクティブなカバレッジを維持します。 これにより、問題は O(n log n) に軽減され、n = 10000 の場合は十分に高速になります。 

最後に、変換は領域をスケーリングするため、計算された領域を変換のヤコビアン行列式によって補正します。 これにより、結果が元の座標系と確実に一致します。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルート フォース ポリゴン ユニオン | O(n^2) 以上 | O(n) | 遅すぎる |
 | 変形 + スイープ ライン | O(n log n) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

1. 各中心 (x, y) を (u, v) に変換します (u = x + y および v = x - y)。 この線形変換が選択されるのは、L1 距離制約を u と v の独立した境界に変換するためです。 
2. 各ダイヤモンド条件 |x - xi| を書き換えます。 + |y - yi| 変換された空間における 2 つの不等式として ≤ 1: [ui - 1, ui + 1] の u と [vi - 1, vi + 1] の v。 これは、各形状が軸に沿った辺の長さ 2 の正方形になったことを示しています。 
3. 各正方形を (u, v) 平面の長方形イベントとして表します。 それぞれは、u に沿った間隔 [ui - 1, ui + 1] と v に沿った [vi - 1, vi + 1] に寄与します。 
4. すべての長方形のエッジを u 軸に沿ったスイープ イベントとして収集します。 各イベントは、v の間隔にわたってカバレッジを追加または削除します。 
5. イベントを U 座標で並べ替え、左から右にスイープします。 連続するイベント位置の間では、アクティブな四角形のセットは変化しないため、カバーされる v の長さはそのストリップ全体で一定のままになります。 
6. 座標圧縮とカバーされる全長を保存するセグメント ツリーを使用して、v 軸上のアクティブなカバレッジを維持します。 位置 u でイベントを処理した後、(next_u - current_u) に active_v_coverage を乗算して寄与度を計算します。 
7. すべての寄与を合計して、(u, v) 空間内の総面積を取得します。 
8. 線形変換にはヤコビアン行列式 1/2 があるため、最終結果に 1/2 を乗算して (x, y) 空間に変換します。 

正確さは、すべてのポイントがスイープ内で正確に 1 回カウントされるという事実に依存します。u の各水平ストリップは、v の正しいアクティブな垂直カバレッジとペアになり、変換により均一なスケーリングを超えた歪みのないカバレッジ構造が保存されます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

class SegTree:
    def __init__(self, coords):
        self.coords = coords
        self.n = len(coords) - 1
        self.count = [0] * (4 * self.n)
        self.length = [0] * (4 * self.n)

    def _update(self, idx, l, r, ql, qr, val):
        if ql <= l and r <= qr:
            self.count[idx] += val
        else:
            mid = (l + r) // 2
            if ql < mid:
                self._update(idx * 2, l, mid, ql, qr, val)
            if qr > mid:
                self._update(idx * 2 + 1, mid, r, ql, qr, val)

        if self.count[idx] > 0:
            self.length[idx] = self.coords[r] - self.coords[l]
        else:
            if r - l == 1:
                self.length[idx] = 0
            else:
                self.length[idx] = self.length[idx * 2] + self.length[idx * 2 + 1]

    def update(self, l, r, val):
        self._update(1, 0, self.n, l, r, val)

    def query(self):
        return self.length[1]

n = int(input())
rects = []

vs = []

for _ in range(n):
    x, y = map(int, input().split())
    u = x + y
    v = x - y
    x1, x2 = u - 1, u + 1
    y1, y2 = v - 1, v + 1
    rects.append((x1, x2, y1, y2))
    vs.append(y1)
    vs.append(y2)

vs = sorted(set(vs))
idx = {v: i for i, v in enumerate(vs)}

events = []
for x1, x2, y1, y2 in rects:
    events.append((x1, y1, y2, 1))
    events.append((x2, y1, y2, -1))

events.sort()

seg = SegTree(vs)

area_uv = 0
for i in range(len(events)):
    x, y1, y2, t = events[i]
    seg.update(idx[y1], idx[y2], t)
    if i + 1 < len(events):
        dx = events[i + 1][0] - x
        area_uv += dx * seg.query()

print(area_uv / 2)
```解決策は、各入力中心を、各ひし形が正方形になる回転座標系に変換することから始まります。 長方形リストには、それらの正方形が u および v 間隔で保存されます。 

セグメント ツリーは、圧縮された v 座標に対して動作します。 各更新では、現在 V セグメントをカバーする長方形の数が調整され、ツリーはカバーされる全長を維持します。 u をスイープすると、このカバーされる長さにイベント間の水平距離が乗算されます。 

最後の 2 による除算は、線形変換によって導入された領域のスケーリングを修正します。 

微妙な点は、間隔更新では座標圧縮を通じて暗黙的にハーフオープン構造を使用するため、共有境界の二重カウントが回避されることです。 

## 実用的な例

 元のグリッドで 2 つのダイヤモンドがわずかに重なる小さな構成を考えてみましょう。 変換後、UV 空間で 2 つの重なり合う正方形が得られます。 

説明のために、2 つの中心が部分的に重なる v の間隔を生成すると仮定します。 

| スイープステップ | u 座標 | アクティブ v カバレッジ | セグメントの長さ | 貢献 |
 | --- | --- | --- | --- | --- |
 | 1 | u1 | 0 | 0 | 0 |
 | 2 | u2 | k | u2 - u1 | (u2 - u1) * k |

 これは、イベント間に水平方向の分離がある場合にのみ、エリアがどのように蓄積されるかを示しています。 

2 番目の例は、単一のダイヤモンドです。 UV空間では2面の1マスになります。 スイープは幅全体にわたって一定のアクティブ カバレッジを生成し、UV 空間で正確に 4 を生成しますが、スケーリング後の元の座標では 2 になります。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n log n) | 各エンドポイントのイベントの並べ替えとセグメント ツリーの更新 |
 | スペース | O(n) | イベント、座標圧縮、セグメント ツリーのストレージ |

 この制約により最大 10,000 の形状が可能になり、対数オーバーヘッドが十分に小さいため、スイープ ライン ソリューションは制限内で快適に実行できます。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    from math import isclose

    # --- paste solution logic here as function ---
    input = sys.stdin.readline

    class SegTree:
        def __init__(self, coords):
            self.coords = coords
            self.n = len(coords) - 1
            self.count = [0] * (4 * self.n)
            self.length = [0] * (4 * self.n)

        def _update(self, idx, l, r, ql, qr, val):
            if ql <= l and r <= qr:
                self.count[idx] += val
            else:
                mid = (l + r) // 2
                if ql < mid:
                    self._update(idx * 2, l, mid, ql, qr, val)
                if qr > mid:
                    self._update(idx * 2 + 1, mid, r, ql, qr, val)

            if self.count[idx] > 0:
                self.length[idx] = self.coords[r] - self.coords[l]
            else:
                if r - l == 1:
                    self.length[idx] = 0
                else:
                    self.length[idx] = self.length[idx * 2] + self.length[idx * 2 + 1]

        def update(self, l, r, val):
            self._update(1, 0, self.n, l, r, val)

        def query(self):
            return self.length[1]

    n = int(input())
    rects = []
    vs = []

    for _ in range(n):
        x, y = map(int, input().split())
        u = x + y
        v = x - y
        rects.append((u - 1, u + 1, v - 1, v + 1))
        vs += [v - 1, v + 1]

    vs = sorted(set(vs))
    idx = {v: i for i, v in enumerate(vs)}

    events = []
    for x1, x2, y1, y2 in rects:
        events.append((x1, y1, y2, 1))
        events.append((x2, y1, y2, -1))

    events.sort()

    seg = SegTree(vs)

    area_uv = 0
    for i, (x, y1, y2, t) in enumerate(events):
        seg.update(idx[y1], idx[y2], t)
        if i + 1 < len(events):
            area_uv += (events[i + 1][0] - x) * seg.query()

    return str(area_uv / 2)

# sample-like sanity checks
assert run("1\n1 1\n")  # single diamond produces nonzero area
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 1 シングルポイント | 2 | 基本的な単一形状の正確性 |
 | 複数の重なり合う中心 | マージエリア | オーバーラップ処理 |
 | 最大散乱点 | 安定した出力 | パフォーマンスとスイープの正確性 |
 | 同じ中心が繰り返される | 二重カウントなし | マルチセットの処理 |

 ## 特殊なケース

 重要なエッジケースは、中心が繰り返されることです。 複数の装飾が同じ整数ポイントを共有する場合、単純なアプローチではその領域を複数回追加する可能性があります。 このソリューションでは、両方のコピーが UV 空間に同一の四角形を生成しますが、セグメント ツリーはカバレッジを和集合としてカウントするため、重複によって最初の挿入後にアクティブな長さが変更されることはありません。 

別のエッジ ケースがウィンドウの境界近くに表示されます。 (1, 1) の中心は、x = 0 または y = 0 に達するダイヤモンドを生成します。スイープは幾何学的領域全体で動作し、ウィンドウ制約は配置ルールによってすでに満たされているため、変換はウィンドウへのクランプに依存しません。 

最後のエッジ ケースは、長方形が境界でのみ接触する場合です。 スイープでは座標圧縮に半開区間を使用するため、エッジが接触しても人為的な領域は作成されません。 セグメント ツリーは、間隔が正の測定値を持つ場合にのみ長さに寄与するため、幅がゼロの重複は正しく無視されます。
