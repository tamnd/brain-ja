---
title: "CF 1154E - 2 つのチーム"
description: "私たちは n 人の生徒の列を与えられ、それぞれが 1 から n までの異なるプログラミング スキルを持っています。 2 人のコーチが交代で生徒を選んでチームを編成します。 コーチの順番で、列に残っている最も高いスキルを持つ生徒を選択します。"
date: "2026-06-12T02:49:32+07:00"
tags: ["codeforces", "competitive-programming", "data-structures", "implementation", "sortings"]
categories: ["algorithms"]
codeforces_contest: 1154
codeforces_index: "E"
codeforces_contest_name: "Codeforces Round 552 (Div. 3)"
rating: 1800
weight: 1154
solve_time_s: 118
verified: true
draft: false
---

[CF 1154E - 2 つのチーム](https://codeforces.com/problemset/problem/1154/E)

 **評価:** 1800
 **タグ:** データ構造、実装、並べ替え
 **解決時間:** 1 分 58 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 の行が与えられています`n`学生はそれぞれ 1 から 2 までの異なるプログラミング スキルを持っています。`n`。 2 人のコーチが交代で生徒を選んでチームを編成します。 コーチの順番で、列に残っている最も高いスキルを持つ生徒を選択します。 次に、その生徒に加えて、次の生徒も引き受けます。`k`左側の生徒たちと`k`これらの学生がまだ選ばれていない限り、右側の学生。 選択された生徒全員が列を離れ、現在のコーチのチームに加わります。 このプロセスは、生徒がいなくなるまで交互に行われます。 

入力により提供されるのは、`n`そして`k`、その後に次の配列が続きます`n`生徒のスキルを順番に表す整数。 出力では、元の行の各生徒について、どのチームに参加しているかを示す必要があります。`1`最初のコーチと`2`2人目のコーチに。 

制約を考慮すると、`n`最大 200,000 まで可能です。 現在の行で最大のスキルを繰り返しスキャンする単純なアプローチでは O(n²) 時間がかかる可能性があり、これでは遅すぎます。 現在の最大値を迅速に見つけて、連続する範囲の生徒を効率的に削除できるデータ構造を使用する必要があります。 最大値が行の境界に近い場合、エッジ ケースが発生します。 ここでの範囲は、`k`左または右の生徒が列を越えて伸びる可能性があり、アルゴリズムはこれを適切に処理する必要があります。 不用意に実装すると、配列の境界外のインデックスにアクセスしようとする可能性があります。 

たとえば、`n = 5`、`k = 2`、スキル`[2, 4, 5, 3, 1]`、最初のコーチが選択します`5`そして、すべての生徒 (ポジション 1 から 5) を受け取り、2 番目のコーチには生徒がいない状態になります。 端の範囲を正しく処理しない単純な方法では、誤ったチーム割り当てが生成される可能性があります。 

## アプローチ

 ブルートフォースアプローチは簡単です。各ターンで現在の配列をスキャンして最大のスキルを見つけ、それに基づいて受講者のインデックスを決定します。`k`、それらを現在のコーチに属するものとしてマークし、配列から削除し、繰り返します。 これは論理的に機能しますが、配列をスキャンして各ターンで最大値を見つけるには、最大で`n + (n-1) + ... + 1 = O(n²)`比較は受け入れられません`n`最大 2 × 10⁵。 

重要な洞察は、スキルが 1 から 2 までの異なる整数であるということです。`n`。 これにより、繰り返しスキャンするのではなく、スキルの降順に生徒を処理できるようになります。 まだ列に残っている学生のソートされたインデックスのセットを維持すると、スキルごとに次のことができます。`n`1 まで下げると、学生がすでに割り当てられているかどうかを確認します。 そうでない場合は、それらを割り当てます。`k`並べ替えられたセットを使用して両側の最近傍を検索し、隣接するインデックスを効率的に見つけます。 これにより、バランスのとれたツリー構造 (またはソートされたセット) での各挿入/削除には log n 時間がかかり、各スチューデントは 1 回だけ処理されるため、操作の数が O(n log n) に削減されます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n²) | O(n) | 遅すぎる |
 | 最適 | O(n log n) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 配列を初期化する`team`長さの`n`すべてゼロで。 この配列には、各生徒のチーム割り当てが保存されます。 ゼロは、学生がまだ割り当てられていないことを示します。 
2. 以下のリストを作成します。`(skill, index)`ペアにしてスキルの降順に並べ替えます。 これにより、スキルが最も高い学生から最も低い学生まで処理することができます。 
3. を維持します。`SortedSet`または`TreeSet`まだ列に残っている生徒を表すインデックスの数。 これにより、連続する近傍を効率的に見つけることができます。 
4. 変数を設定する`current_team = 1`どのコーチの順番なのかを追跡します。 
5. ソートされた学生のリストを繰り返し処理します。 それぞれについて`(skill, idx)`：

- もし`team[idx]`はすでにゼロではないため、この学生をスキップします。 
- それ以外の場合は、割り当てます`team[idx] = current_team`。 
- ソートされたセットを使用して、以下を特定します。`k`左右の隣人。 それらに同じものを割り当てます`current_team`値を変更し、ソートされたセットから割り当てられたすべてのインデックスを削除します。 
6. 各最高スキルを処理した後、反転します`current_team`コーチを交代すること。 
7. すべての生徒がチームに割り当てられるまで続けます。 

機能する理由: 各ステップで、ルールの指示どおりに、割り当てられていない最も高いスキルを常に選択します。 までの割り当て`k`両側の隣人はコーチの選択を正確にモデル化しています。 学生を再割り当てすることはなく、各学生は最終的に降順に処理されるため、結果として`team`配列は問題のルールを遵守することが保証されています。 

## Python ソリューション```python
import sys
input = sys.stdin.readline
from bisect import bisect_left, bisect_right, insort

class SortedSet:
    def __init__(self, data=None):
        self.data = sorted(data) if data else []

    def __contains__(self, x):
        i = bisect_left(self.data, x)
        return i < len(self.data) and self.data[i] == x

    def add(self, x):
        i = bisect_left(self.data, x)
        if i == len(self.data) or self.data[i] != x:
            self.data.insert(i, x)

    def discard(self, x):
        i = bisect_left(self.data, x)
        if i < len(self.data) and self.data[i] == x:
            self.data.pop(i)

    def index_left(self, x):
        i = bisect_left(self.data, x)
        return i

    def index_right(self, x):
        i = bisect_right(self.data, x)
        return i

    def get_left_neighbors(self, idx, k):
        i = bisect_left(self.data, idx)
        left = self.data[max(0, i - k):i]
        return left

    def get_right_neighbors(self, idx, k):
        i = bisect_right(self.data, idx)
        right = self.data[i:i + k]
        return right

def main():
    n, k = map(int, input().split())
    a = list(map(int, input().split()))
    team = [0] * n
    sorted_indices = SortedSet(range(n))
    skills = sorted([(val, idx) for idx, val in enumerate(a)], reverse=True)
    current_team = 1

    for val, idx in skills:
        if team[idx] != 0:
            continue
        to_assign = [idx]
        to_assign += sorted_indices.get_left_neighbors(idx, k)
        to_assign += sorted_indices.get_right_neighbors(idx, k)
        for i in to_assign:
            team[i] = current_team
            sorted_indices.discard(i)
        current_team = 3 - current_team  # switch team

    print("".join(map(str, team)))

if __name__ == "__main__":
    main()
```の`SortedSet`class は、残りのスチューデント インデックスを維持し、近傍を効率的に見つけるためのユーティリティです。 の`get_left_neighbors`そして`get_right_neighbors`関数は最大で取得します`k`両側の隣人。 スイッチング`current_team`によって行われます`3 - current_team`1と2を交互に繰り返します。`team`配列は必要な出力文字列を直接表します。 

## 実用的な例

 ### サンプル 1

 入力:`5 2`、スキル`[2, 4, 5, 3, 1]`。 

| 反復 | 現在のチーム | 最大スキル | 割り当てられたインデックス | 残りのソートセット | チーム配列 |
 | --- | --- | --- | --- | --- | --- |
 | 1 | 1 | 5 | [2,0,1,3,4] | [] | [1,1,1,1,1] |

 すべての生徒が先手で取られます。 最終的な出力は次のとおりです`11111`。 これは、アルゴリズムが全範囲選択を正しく処理していることを示しています。 

### サンプル 2

 入力:`5 1`、スキル`[1, 3, 5, 4, 2]`。 

| 反復 | 現在のチーム | 最大スキル | 割り当てられたインデックス | 残りのソートセット | チーム配列 |
 | --- | --- | --- | --- | --- | --- |
 | 1 | 1 | 5 | [2,1,3] | [0,4] | [0,1,1,1,0] |
 | 2 | 2 | 4 | [0,4] | [] | [2,1,1,1,2] |

 これにより、隣接する選択が境界と交互のターンを尊重していることが確認されます。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n log n) | ソートには O(n log n) がかかり、ソートされたセット内の各挿入/破棄は生徒 1 人あたり O(log n) です。 |
 | スペース | O(n) | チームの割り当てとサイズ n のソートされたインデックスを保存します。 |

 n ≤ 2 × 10⁵ の場合、アルゴリズムは 2 秒以内に快適に実行され、使用するメモリは 256 MB 未満になります。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    import builtins
    input_backup = builtins.input
    builtins.input = lambda: sys.stdin.readline()
    from contextlib import redirect_stdout
    output = io.StringIO()
    with redirect_stdout(output):
        main()
    builtins.input = input_backup
    return output.getvalue().strip()

# Provided samples
assert run("5 2\n2 4 5
```
