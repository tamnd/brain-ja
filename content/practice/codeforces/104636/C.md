---
title: "CF 104636C - ランク"
description: "学生のリストが与えられ、各学生は 1 から n までの整数 ID で識別されます。 生徒 1 はトーマスです。 すべての学生には 4 つの試験スコアがあり、全体的な成績はこれら 4 つのスコアの合計によって測定されます。"
date: "2026-06-29T17:05:03+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 104636
codeforces_index: "C"
codeforces_contest_name: "\u041c\u0438\u0441\u0438\u0441 2023 \u043e\u0441\u0435\u043d\u044c - \u043c\u0430\u0441\u0441\u0438\u0432\u044b, \u0441\u0442\u0440\u043e\u043a\u0438"
rating: 0
weight: 104636
solve_time_s: 83
verified: false
draft: false
---

[CF 104636C - ランク](https://codeforces.com/problemset/problem/104636/C)

 **評価:** -
 **タグ:** -
 **解決時間:** 1 分 23 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 学生のリストが与えられ、各学生は 1 から n までの整数 ID で識別されます。 生徒 1 はトーマスです。 すべての学生には 4 つの試験スコアがあり、全体的な成績はこれら 4 つのスコアの合計によって測定されます。 

すべての生徒は、この合計スコアの降順に並べ替えることによってランク付けされます。 2 人の生徒の合計スコアが同じ場合、ID が小さい生徒を最初に選択することで同点が解消されます。 課題は、この順序におけるトーマスの最終的な位置を決定することです。 

The constraints n ≤ 1000 and score values up to 100 mean each total score is bounded by 400. This immediately suggests that any O(n^2) or O(n log n) solution is comfortably fast, since even 10^6 comparisons is trivial in 1 second.

 The main subtlety is the tie-breaking rule. よくある間違いは、厳密に高いスコアのみをカウントし、同じスコアでも ID の比較が必要であることを忘れることです。 Another failure case is sorting only by score without encoding the id ordering correctly, which leads to incorrect placement when duplicates exist.

 具体的なエッジケースは、複数の生徒がトーマスと同じ合計点を共有する場合です。 For example, if Thomas has total 390 and two other students also have 390, only students with strictly greater scores should be ahead of him, and among equal scores Thomas should come first due to id = 1. A careless implementation that counts “score ≥ Thomas score” as higher rank would incorrectly push Thomas down.

 ## アプローチ

 A direct approach is to compute each student’s total score, then sort the students using the required ordering rule, and finally locate the position of student 1 in the sorted list.

 This works because the ranking definition is exactly a global ordering problem: once all totals are computed, the task reduces to sorting pairs of (score, id) with a lexicographic order where score is descending and id is ascending.

 強引な代替案は、トーマスを他のすべての生徒と比較し、何人が彼よりも上位にランクされているかを数えることです。 自分のスコアがトーマスのスコアよりも厳密に大きい場合、またはスコアが同じで ID が小さい場合、生徒が有利です。 Thomas の ID が最小であるため、同じスコアの生徒は彼を上回ることはできないため、厳密に大きいスコアのみが問題となります。 この観察により、ロジックが大幅に削減されます。 

この強引なアイデアはすでに O(n) で実行されますが、これをあらゆる生徒に一般化するには O(n^2) 回の比較が必要になります。 並べ替えベースのアプローチはより均一であり、ランキング システムの一般的な実装方法に近いものです。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n^2) | お(1) | 受け入れられるが不要 |
 | 並べ替え | O(n log n) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

 ### 最適なアプローチ

 1. すべての生徒を読み、4 つの科目を合計して合計得点を計算します。 

これにより、問題は生徒ごとに単一の比較可能な値を扱うように変換されます。 
2. 各生徒を、合計スコアと ID を含むペアとして保存します。 

We keep id because tie-breaking depends on it.
 3. 学生のリストを 2 つのキーで並べ替えます。最初は合計スコアを減らし、次に ID を増やします。 

これにより、ランキング ルールがソート コンパレータに直接エンコードされます。 
4. ソートされたリストをスキャンして、id が 1 に等しい位置を見つけます。 

このソート順のインデックスが、1 から始まるインデックスに調整された答えになります。 

### なぜ効果があるのか

 合計を計算した後、学生間のすべての比較は、スコアと ID の 2 つの値のみに依存します。 並べ替え順序は問題のランキング ルールと正確に一致します。つまり、並べ替えられた配列が有効な最終的なランキングになります。 Since sorting produces a total order consistent with the required comparison function, the position of student 1 is guaranteed to match their rank. すべてのタイブレークはソート中にすでに解決されているため、後で調整する必要はありません。 

## Python ソリューション

```python
import sys
input = sys.stdin.readline

def main():
    n = int(input())
    students = []
    
    for i in range(1, n + 1):
        a, b, c, d = map(int, input().split())
        total = a + b + c + d
        students.append(( -total, i ))  # negative for descending sort
    
    students.sort()
    
    for idx, (_, sid) in enumerate(students, start=1):
        if sid == 1:
            print(idx)
            return

if __name__ == "__main__":
    main()
```実装では、カスタム コンパレーターの作成を避けるために否定スコアを使用します。Python はデフォルトで昇順にソートするため、符号を反転すると降順になります。 ID はそのまま残されるため、最初に小さい ID に向かってタイが自然に解決されます。 

n ≤ 1000 であるため、ソートされた生徒のループは安全であり、線形スキャンは無視できます。 

## 実用的な例

 ### サンプル 1

 入力した生徒の合計は 398、400、398、379、357 になります。(-score, id) による並べ替えを追跡します。 

| ステップ | 学生 | 合計 | キー (-合計、ID) | これまでの並べ替え順序 |
 | --- | --- | --- | --- | --- |
 | 1 | 1 | 398 | (-398, 1) | |
 | 2 | 2 | 400 | (-400、2) | |
 | 3 | 3 | 398 | (-398, 3) | |
 | 4 | 4 | 379 | (-379, 4) | |
 | 5 | 5 | 357 | (-357, 5) | |

 並べ替えると、順序は生徒 2、次に生徒 1 と 3、次に 4、次に 5 になります。生徒 1 は 2 番目の位置にあるため、出力は 2 になります。 

このトレースは、ソートされた構造内の ID 順序のみによって等しい合計がどのように解決されるかを示しています。 

### サンプル 2

 Totals are 369, 240, 310, 300, 300, 0.

 | ステップ | 学生 | 合計 | キー (-合計、ID) | これまでの並べ替え順序 |
 | --- | --- | --- | --- | --- |
 | 1 | 1 | 369 | (-369, 1) | |
 | 2 | 2 | 240 | (-240, 2) | |
 | 3 | 3 | 310 | (-310, 3) | |
 | 4 | 4 | 300 | (-300、4) | |
 | 5 | 5 | 300 | (-300、5) | |
 | 6 | 6 | 0 | (0, 6) | |

 厳密には 369 が最大であるため、ソート順序は生徒 1 から始まります。 したがって、トーマスが最初です。 

これにより、誰もトーマスのスコアを超えない場合、他の分布に関係なく彼のランクは 1 であることが確認されます。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n log n) | sorting n students dominates all work |
 | スペース | O(n) | storing list of student tuples |

 n ≤ 1000 という制約により、これは快適に高速になります。 最悪の場合でも、1000 個の要素を並べ替えるのは簡単で、メモリ使用量も無視できます。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    from __main__ import main
    try:
        main()
    except SystemExit:
        pass
    return sys.stdout.getvalue().strip()

# provided samples
assert run("5\n100 98 100 100\n100 100 100 100\n100 100 99 99\n90 99 90 100\n100 98 60 99\n") == "2"
assert run("6\n100 80 90 99\n60 60 60 60\n90 60 100 60\n60 100 60 80\n100 100 0 100\n0 0 0 0\n") == "1"

# all equal
assert run("3\n10 10 10 10\n10 10 10 10\n10 10 10 10\n") == "1"

# Thomas already lowest
assert run("3\n0 0 0 0\n100 100 100 100\n50 50 50 50\n") == "3"

# maximum tie around Thomas
assert run("4\n10 10 10 10\n10 10 10 10\n10 10 10 10\n10 10 10 10\n") == "1"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | すべて等しいスコア | 1 | tie-breaking by id places Thomas first |
 | トーマス最低 | 3 | correct rank when others dominate |
 | ユニフォームマックスネクタイ | 1 | stable handling of full equality |

 ## 特殊なケース

 重要なケースの 1 つは、すべての生徒の合計スコアが同じである場合です。 この状況では、並べ替えは完全に ID の順序に依存します。 学生 1 の ID が最小であるため、ソート順は Thomas から始まり、アルゴリズムはランク 1 を正しく返します。 

もう 1 つのケースは、トーマスが可能な限り低いスコア (すべてゼロなど) を持ち、他の人が最大スコアを持っている場合です。 The sorting places him at the end because every other student has a higher key (-score is more negative), so his rank becomes n, which is consistent with expectations.

 最後の微妙なケースは、複数の生徒がトーマスのスコアを共有しているが、誰もそれを超えていない場合です。 ソートすると、ID の順序によりトーマスがすべての前に表示されるため、多くの同点が存在する場合でも、トーマスのランクは 1 のままです。
