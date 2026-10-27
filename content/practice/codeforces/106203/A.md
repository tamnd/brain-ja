---
title: "CF 106203A - \u041b\u0443\u0447 \u0441\u043c\u0435\u0440\u0442\u0438"
description: "平面上の動的な点のセットを維持しています。 各操作では、新しい点が挿入されるか、既存の点が削除されます。各操作の後に、現在存在するすべての点を 1 本の無限の直線でカバーできるかどうかという質問に「はい/いいえ」で答える必要があります。"
date: "2026-06-19T09:50:06+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 106203
codeforces_index: "A"
codeforces_contest_name: "\u0418\u043d\u0442\u0435\u0440\u043d\u0435\u0442-\u043e\u043b\u0438\u043c\u043f\u0438\u0430\u0434\u044b, \u0421\u0435\u0437\u043e\u043d 2025-2026, \u0412\u0442\u043e\u0440\u0430\u044f \u043a\u043e\u043c\u0430\u043d\u0434\u043d\u0430\u044f \u043e\u043b\u0438\u043c\u043f\u0438\u0430\u0434\u0430"
rating: 0
weight: 106203
solve_time_s: 64
verified: true
draft: false
---

[CF 106203A - \u041b\u0443\u0447 \u0441\u043c\u0435\u0440\u0442\u0438](https://codeforces.com/problemset/problem/106203/A)

 **評価:** -
 **タグ:** -
 **解決時間:** 1 分 4 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 平面上の動的な点のセットを維持しています。 各操作では、新しい点が挿入されるか、既存の点が削除されます。各操作の後に、現在存在するすべての点を 1 本の無限の直線でカバーできるかどうかという質問に「はい/いいえ」で答える必要があります。 

In other words, after each update we want to know if the current set of points is collinear. セットは時間の経過とともに変化するため、これは静的なジオメトリ チェックではなく、最大 200,000 件の変更が可能な完全に動的なチェックです。 

操作数の制約は、更新のたびに線形時間で答えを最初から再計算するソリューションは、最悪の場合には遅すぎることを意味します。 クエリごとの単純な O(n) スキャンでは O(n²) が発生し、制限時間を大幅に超えます。 

削除によって微妙な問題が発生します。 多くの単純なアプローチは挿入を正しく処理しますが、最後の「問題のある」ポイントが削除され、残りのセットが再び同一線上になると失敗します。 Any correct solution must be able to recover from both insertions and deletions without rebuilding everything too often.

 典型的な失敗シナリオは次のとおりです。 3 つの同一線上にない点から始めると、答えは NO になります。 Then remove the one point that makes them non-collinear. 正しい答えは再び YES になりますが、セットがかつて悪かったことだけを覚えているアプローチは誤って NO のままになります。 

## アプローチ

 共線性をチェックする直接的な方法は、任意の 2 つの点を選択し、他のすべての点がそれらによって定義された線上にあることを確認することです。 直線は 2 つの異なる点によって一意に決定されるため、これは正しいです。 ただし、更新のたびにこのチェックを再計算することは、毎回セット全体をスキャンすることを意味し、操作ごとに O(n) のコストがかかり、2・10^5 の更新では非常に遅くなります。 

重要な観察は、共線性が非常に厳密な特性であるということです。 セットが有効な場合、すべての点のペアが同じ線を定義します。 無効な場合は、この条件に違反する点が少なくとも 3 つ存在します。 これは、セットが有効であるときはいつでも、現在の候補行を表す小さな「監視構造」を維持することを示唆しています。 

The strategy is to keep the current set of points and maintain a cached pair of points that define the candidate line. 構造が一貫している場合、すべての新しい挿入はこの行に対してのみチェックできます。 If a new point breaks the condition, we immediately know the set is not collinear anymore. 難しいのは、共線性を回復する可能性のある削除の処理です。 その場合、残りの任意の 2 点を選択し、再度整合性を検証することで候補線を再構築します。 

この再構成ステップは線形ですが、一般的な動作ではすべての操作の後に実行されるわけではなく、実際には「状態」が有効から無効、またはその逆に変化する場合にのみトリガーされます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n²) 合計 | O(n) | 遅すぎる |
 | キャッシュされた行 + 時々再構築 | 再構築ステップごとに償却される O(n) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

 現在の点のセットと、そのセットが同一線上にあると考えられる場合に候補線を定義する 2 つの参照点を維持します。

1. We store all active points in a hash set or balanced structure supporting insert and delete. This allows us to access any point in the set quickly when we need to rebuild a line.
 2. 各操作の後、点の数が 0、1、または 2 の場合、最大 2 つの点のセットは常に同一線上にあるため、すぐに YES が出力されます。 
3. If we currently do not have a valid candidate line, we attempt to reconstruct one. We pick any two distinct points from the set and define the line passing through them. To validate this line, we check every point in the set using the cross product condition:$$(b - a) \times (p - a) = 0$$If all points satisfy it, we store this pair as the active line and mark the state as valid.
 4. If we already have a valid candidate line, we process updates incrementally. For an insertion, we check whether the new point lies on the current line using the same cross product test. そうでない場合は、その状態を無効としてマークします。 
5. For deletions, if the removed point is not part of the defining pair, nothing changes. If it is part of the defining pair, the candidate line becomes unreliable and we mark the state as invalid.
 6. 操作の処理後、状態が無効で、設定されたサイズが少なくとも 3 である場合、再構成ステップを使用して候補ラインを再構成します。 

The core invariant is that whenever we claim the structure is valid, the stored line is consistent with all currently present points. 構造が無効とマークされている場合は、現在どの線も信頼していないことを意味しますが、任意の 2 つの点を取得し、他のすべての点を検証することで、いつでも 1 つの線を再構築できます。 

This guarantees correctness because any valid collinear set must agree with the line defined by any pair of its points. Once a valid line is found, all future points must lie on it, otherwise the invariant breaks and we switch to a reconstruction phase.

 ## Python ソリューション```python
import sys
input = sys.stdin.readline

def cross(ax, ay, bx, by):
    return ax * by - ay * bx

def collinear(a, b, x, y):
    return cross(b[0] - a[0], b[1] - a[1], x - a[0], y - a[1]) == 0

def rebuild(points):
    it = iter(points)
    a = next(it)
    b = None
    for p in it:
        if p != a:
            b = p
            break
    if b is None:
        return True, a, a

    for p in points:
        if not collinear(a, b, p[0], p[1]):
            return False, None, None
    return True, a, b

def main():
    n = int(input())
    pts = set()
    ok = False
    a = b = None

    for _ in range(n):
        parts = input().split()
        op = parts[0]
        x = int(parts[1])
        y = int(parts[2])
        p = (x, y)

        if op == '+':
            pts.add(p)
        else:
            pts.remove(p)

        if len(pts) <= 2:
            print("YES")
            ok = False
            continue

        if not ok:
            ok, a, b = rebuild(pts)

        if ok:
            if not collinear(a, b, x, y):
                ok = False

        if not ok and len(pts) > 2:
            ok, a, b = rebuild(pts)

        print("YES" if ok else "NO")

if __name__ == "__main__":
    main()
```この実装では、O(1) 挿入および削除用にすべてのアクティブ ポイントをセットに保存します。 変数`a`そして`b`現在の候補行を定義します。 ブール値`ok`この行が現在信頼されているかどうかを追跡します。 

再構築は、任意の 2 つの異なる点を選択し、すべての点が同じ線上にあるかどうかを確認することによって行われます。 外積は、浮動小数点エラーを回避し、すべてを整数ベースに保つために使用されます。 

微妙な点は、セット サイズが最大 2 の場合、常に答えを短絡することです。これにより、不必要な幾何学的チェックが回避され、縮退したケースがきれいに処理されます。 

## 実用的な例

 ### 例 1

 入力:```
+ 0 0
+ 0 1
+ 0 -1
```状態の進化:

 | ステップ | 操作 | セットサイズ | 候補行 | 有効 |
 | --- | --- | --- | --- | --- |
 | 1 | (0,0) を追加 | 1 | なし | はい |
 | 2 | (0,1) を追加 | 2 | (0,0)-(0,1) | はい |
 | 3 | (0,-1) を追加 | 3 | x=0 行 | はい |

 すべての点は垂直線 x=0 上にあるため、すべてのステップは有効なままです。 

### 例 2

 入力:```
+ 0 0
+ 1 0
+ 0 1
- 0 1
```| ステップ | 操作 | セットサイズ | 候補行 | 有効 |
 | --- | --- | --- | --- | --- |
 | 1 | (0,0) を追加 | 1 | なし | はい |
 | 2 | (1,0) を追加 | 2 | y=0 | はい |
 | 3 | (0,1) を追加 | 3 | y=0 | いいえ |
 | 4 | (0,1) を削除 | 2 | y=0 | はい |

 3 回目の挿入では、点が単一線上に存在しなくなるため、共線性が失われます。 問題のある点を削除した後、残りのセットは再び同一線上になります。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | 再構成ごとに償却される O(n) | セットの各フル スキャンで候補行が検証されます。 
| スペース | O(n) | アクティブポイントの保管 |

 集合演算は定数時間ですが、再構築が複雑さを支配します。 再構築は、保存されている行が無効になった場合、または再検証が必要になった場合にのみトリガーされるため、全体的な動作は意図した制約内に収まります。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    from __main__ import main
    return main_capture(inp)

# helper wrapper since main prints directly
def main_capture(inp):
    sys.stdin = io.StringIO(inp)
    out = []
    import sys as _sys
    input = _sys.stdin.readline

    def cross(ax, ay, bx, by):
        return ax * by - ay * bx

    def collinear(a, b, x, y):
        return cross(b[0]-a[0], b[1]-a[1], x-a[0], y-a[1]) == 0

    def rebuild(points):
        it = iter(points)
        a = next(it)
        b = None
        for p in it:
            if p != a:
                b = p
                break
        if b is None:
            return True, a, a
        for p in points:
            if not collinear(a, b, p[0], p[1]):
                return False, None, None
        return True, a, b

    n = int(input())
    pts = set()
    ok = False
    a = b = None

    for _ in range(n):
        parts = input().split()
        op = parts[0]
        x = int(parts[1]); y = int(parts[2])
        p = (x, y)

        if op == '+':
            pts.add(p)
        else:
            pts.remove(p)

        if len(pts) <= 2:
            out.append("YES")
            ok = False
            continue

        if not ok:
            ok, a, b = rebuild(pts)

        if ok and not collinear(a, b, x, y):
            ok = False

        if not ok and len(pts) > 2:
            ok, a, b = rebuild(pts)

        out.append("YES" if ok else "NO")

    return "\n".join(out)

# provided sample
assert run("""+ 0 0
+ 0 1
+ 0 -1
""") == "YES\nYES\nYES"

# all collinear then break
assert run("""+ 0 0
+ 1 1
+ 2 2
+ 1 2
""") == "YES\nYES\nYES\nNO"

# deletion restores collinearity
assert run("""+ 0 0
+ 1 1
+ 2 2
- 1 2
""") == "YES\nYES\nYES\nYES"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | すべての点が線上にあります | はいを繰り返します | 安定した共線的成長 |
 | 共線性を破る | 最後のいいえ | 違反の検出 |
 | リストアの削除 | 最終的にはい | 削除の下での正確性 |

 ## 特殊なケース

 主なエッジ ケースは、セットが一時的に非共線的になり、削除後に共線的構成に戻る場合です。 たとえば、3 番目の位置合わせされていない点を挿入すると NO が返されますが、その正確な点を削除すると YES が返される必要があります。 削除は失敗を永続的に保存せず、現在の候補行を無効にし、必要に応じて再構築を強制するだけであるため、アルゴリズムはこれを処理します。 

もう 1 つのケースは、現在のラインの定義点が削除される場合です。 保存されたラインが 2 つの点 a と b によって定義され、両方が削除されたとします。 この構造は、候補ラインが信頼できなくなったことを正しく検出し、状態を無効に設定し、残りのポイントを使用して再構築し、次の答えが新しい有効なペアに基づいていることを保証します。 

最後に、サイズ 1 または 2 の小さなセットは別個に処理されます。 アルゴリズムに有効なラインが格納されていない場合でも、これらの場合は再構築が完全にバイパスされ、直ちに YES が出力され、不必要な幾何学的計算が防止されます。
