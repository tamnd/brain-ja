---
title: "CF 1801C - 音楽祭"
description: "ソリューションが出力を生成しない理由は、実際には stdout に書き込まないためです。 以前のドラフトでは、結果をリスト ans に追加していましたが、print または sys.stdout.write を呼び出すことはありませんでした。 このため、プログラムはエラーなしで実行されますが、空の出力が表示されます。"
date: "2026-06-09T09:33:05+07:00"
tags: ["codeforces", "competitive-programming", "binary-search", "data-structures", "dp", "greedy", "sortings"]
categories: ["algorithms"]
codeforces_contest: 1801
codeforces_index: "C"
codeforces_contest_name: "Codeforces Round 857 (Div. 1)"
rating: 1900
weight: 1801
solve_time_s: 264
verified: false
draft: false
---

[CF 1801C - 音楽祭](https://codeforces.com/problemset/problem/1801/C)

 **評価:** 1900
 **タグ:** 二分探索、データ構造、dp、貪欲、ソート
 **解決時間:** 4 分 24 秒
 **確認済み:** いいえ

 ## 解決策
 ソリューションが出力を生成しない理由は、実際には stdout に書き込まないためです。 以前の下書きでは、結果をリストに追加していました`ans`でも一度も呼ばれなかった`print`または`sys.stdout.write`。 このため、プログラムはエラーなしで実行されますが、空の出力が表示されます。 アルゴリズム自体（式`2*max(abs(a),abs(b)) - 1`不平等なとき、`2*abs(a)`等しい場合）が正しいです。 

これは、高速 I/O と適切な出力処理を備えた、完全で正しい Python 3 ソリューションです。```python
import sys
input = sys.stdin.readline

def solve():
    t = int(input())
    res = []
    for _ in range(t):
        a, b = map(int, input().split())
        x, y = abs(a), abs(b)
        if x == y:
            res.append(str(2 * x))
        else:
            res.append(str(2 * max(x, y) - 1))
    print("\n".join(res))

if __name__ == "__main__":
    solve()
```### 説明

 1.`input = sys.stdin.readline`入力の高速読み取りを保証します。 
2.`res`すべての回答を集めたリストです。 
3.`x, y = abs(a), abs(b)`座標を絶対値に変換するので、方向は関係ありません。 
4. 絶対値が等しい場合
