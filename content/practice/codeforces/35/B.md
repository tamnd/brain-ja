---
title: "CF 35B - 倉庫"
description: "格子状の棚システムを備えた倉庫をシミュレートするように求められました。 各棚には m 個のセクションがあり、上から下に n 枚の棚が積み重ねられています。 各セクションには最大 1 つのボックスを収納できます。"
date: "2026-05-28T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "implementation"]
categories: ["algorithms"]
codeforces_contest: 35
codeforces_index: "B"
codeforces_contest_name: "Codeforces Beta Round 35 (Div. 2)"
rating: 1700
weight: 35
solve_time_s: 81
verified: true
draft: false
---
[CF 35B - ウェアハウス](https://codeforces.com/problemset/problem/35/B)

 **評価:** 1700
 **タグ:** 実装
 **解決時間:** 1 分 21 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 格子状の棚システムを備えた倉庫をシミュレートするように求められました。 各棚には、`m`セクションがあり、`n`棚は上から下に積み上げられています。 各セクションには最大 1 つのボックスを収納できます。 一連の操作を受け取ります。特定の棚とセクションにボックスを追加するものと、識別子に基づいてボックスを削除するものがあります。 ボックスを重ねることはできません。 意図したセクションがいっぱいの場合、ボックスは同じ棚の右にある次に利用可能なセクションに配置されます。 セクションが残っていない場合は、左端のセクションから下の次の棚に進みます。 すべての棚に空きセクションがない場合、そのボックスは破棄されます。 

取り外し操作ごとに、ボックスが占有されている正確な棚とセクションを出力する必要があります。`-1 -1`倉庫になかったら。 

制約は小さいです。`n`そして`m`最大 30、および操作の数`k`これにより、直接シミュレーションが可能になります。 各配置では、最悪の場合、すべての配置をスキャンする必要がある場合があります。`n * m`ポジション。 それにより得られるのは`2000 * 900 = 1.8 * 10^6`操作は 2 秒の制限内で許容されます。 明らかではないエッジケースには、棚いっぱいにボックスを配置しようとしたり、一度も挿入されなかったボックスを削除しようとしたりすることが含まれます。 

たとえば、セクションにボックスを配置しようとすると、`(1, 2)`しかし、それはいっぱいで、棚 1 全体がいっぱいです。箱はセクションに移動する必要があります`(2, 1)`。 すべての棚がいっぱいの場合、そのボックスは無視されます。 次の棚に続けることを忘れる単純なアプローチでは、間違った配置が発生します。 

## アプローチ

 ブルートフォースアプローチは簡単です。倉庫を表す 2D グリッドを維持します。 各セルにはボックス ID が格納されるか、空になります。 挿入用`+1 x y id`、ボックスを次の場所に置いてみます。`(x, y)`。 占有されている場合は、空きセルが見つかるかすべてのシェルフが使い果たされるまで、現在の行の残りをスキャンし、その後の行を順番にスキャンします。 除去のため`-1 id`、グリッド全体を検索してボックスを見つけ、そのセルをクリアします。 

これは正しいですが、非効率的です`-1`各検索には時間がかかる可能性があるため、クエリ`O(n*m)`時間がかかり、その結果、`O(k * n * m)`全体。 Given the constraints, this naive search would still work but is avoidable.

 The key insight for an efficient solution is that every box has a unique identifier and we only need to answer removals efficiently. By maintaining a dictionary mapping box identifiers to their positions, we can retrieve positions in constant time. The placement logic still uses the grid but the removal operation no longer requires scanning. これにより、削除にかかる時間が短縮されます。`O(n*m)`に`O(1)`クエリごとに。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(k * n * m) | O(n*m) | 動作しますが、上限では遅い |
 | 最適 | O(k * n * m) | O(n*m + k) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 2D 配列を初期化する`warehouse`サイズの`n x m`ボックス識別子を保存するため、または`None`空のセクション用。 辞書を作成する`positions`ボックス識別子を現在のものにマッピングする`(shelf, section)`コーディネート。 
2. クエリごとに、操作を解析します。 そうであれば`+1 x y id`、棚からボックスを配置してみます`x-1`とセクション`y-1`。 セルが空かどうかを確認します。 その場合は、ボックスを配置し、その座標を保存します。`positions`、次のクエリに進みます。 占有されている場合は、同じ棚に沿って右に移動します。 棚の端に到達したら、左端のセクションからスキャンしながら下の次の棚に移動します。 空のセクションが見つかるか、すべての棚がなくなるまで繰り返します。 スペースが見つからない場合は、ボックスを無視してください。 
3. のために`-1 id`、どうかを確認してください`id`に存在します`positions`。 その場合は取得してください`(shelf, section)`、1 から始まるインデックスを出力し、からエントリを削除します。`positions`でセルをマークします。`warehouse`空のように。 存在しない場合は出力します`-1 -1`。 
4. すべてのクエリに対して繰り返します。 

これが機能する理由は、倉庫グリッドがリアルタイムの占有状況を正確に表しており、辞書によって移動の定時取得が保証されているためです。 スキャン順序は問題の配置ルールに厳密に従っており、正しい位置が保証されます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

n, m, k = map(int, input().split())
warehouse = [[None for _ in range(m)] for _ in range(n)]
positions = {}

for _ in range(k):
    parts = input().split()
    if parts[0] == '+1':
        x, y, box_id = int(parts[1]) - 1, int(parts[2]) - 1, parts[3]
        placed = False
        for i in range(x, n):
            start_j = y if i == x else 0
            for j in range(start_j, m):
                if warehouse[i][j] is None:
                    warehouse[i][j] = box_id
                    positions[box_id] = (i, j)
                    placed = True
                    break
            if placed:
                break
    else:
        box_id = parts[1]
        if box_id in positions:
            i, j = positions.pop(box_id)
            print(i + 1, j + 1)
            warehouse[i][j] = None
        else:
            print(-1, -1)
```スキャンは簡単かつ安全であるため、配置用のグリッドを維持します。 ディクショナリでは、削除が O(1) であることが保証されます。 出力の期待に一致するように、0 ベースのインデックスと 1 ベースのインデックスを慎重に処理します。 新しい棚に移動するときに列スキャンもリセットします。 

## 実用的な例

 サンプル 1 の入力:```
2 2 9
+1 1 1 cola
+1 1 1 fanta
+1 1 1 sevenup
+1 1 1 whitekey
-1 cola
-1 fanta
-1 sevenup
-1 whitekey
-1 cola
```| ステップ | 操作 | 倉庫の状態 | ポジション | 出力 |
 | --- | --- | --- | --- | --- |
 | 1 | +1 1 1 コーラ | [[コーラ, なし],[なし,なし]] | {'コーラ':(0,0)} | |
 | 2 | +1 1 1 ファンタ | [[コーラ、ファンタ]、[なし,なし]] | {'コーラ':(0,0),'ファンタ':(0,1)} | |
 | 3 | +1 1 1 セブンアップ | [[コーラ,ファンタ],[セブンナップ,なし]] | ... | |
 | 4 | +1 1 1 ホワイトキー | [[コーラ、ファンタ]、[セブンナップ、ホワイトキー]] | ... | |
 | 5 | -1コーラ | [[なし,ファンタ],[セブンアップ,ホワイトキー]] | {'ファンタ':(0,1),...} | 1 1 |
 | 6 | -1 ファンタ | [[なし,なし],[セブンアップ,ホワイトキー]] | {'セブンアップ':(1,0),'ホワイトキー':(1,1)} | 1 2 |
 | 7 | -1 セブンアップ | [[なし,なし],[なし,ホワイトキー]] | {'ホワイトキー':(1,1)} | 2 1 |
 | 8 | -1 ホワイトキー | [[なし,なし],[なし,なし]] | {} | 2 2 |
 | 9 | -1コーラ | [[なし,なし],[なし,なし]] | {} | -1 -1 |

 このトレースにより、配置順序と辞書検索が確認されます。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(k * n * m) | 各配置は最悪の場合、すべてのセクションをスキャンする可能性があり、削除は O(1) です。 |
 | スペース | O(n*m + k) | 倉庫のグリッド + 最大 k 個のボックス ID の辞書。 |

 と`n, m <= 30`そして`k <= 2000`、これは 2 秒の制限内に余裕で収まります。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    output = io.StringIO()
    sys.stdout = output
    # call solution
    n, m, k = map(int, input().split())
    warehouse = [[None]*m for _ in range(n)]
    positions = {}
    for _ in range(k):
        parts = input().split()
        if parts[0] == '+1':
            x, y, box_id = int(parts[1]) - 1, int(parts[2]) - 1, parts[3]
            placed = False
            for i in range(x, n):
                start_j = y if i == x else 0
                for j in range(start_j, m):
                    if warehouse[i][j] is None:
                        warehouse[i][j] = box_id
                        positions[box_id] = (i,j)
                        placed = True
                        break
                if placed:
                    break
        else:
            box_id = parts[1]
            if box_id in positions:
                i,j = positions.pop(box_id)
                print(i+1,j+1)
                warehouse[i][j] = None
            else:
                print(-1,-1)
    return output.getvalue().strip()
```
