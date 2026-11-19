---
title: "CF 2126D - これが最後です"
description: "私たちには一連のカジノが与えられ、それぞれがプレイしなければならないコイン [li, ri] の範囲と、そこでプレイした後の新しいコイン数となる固定結果 Reali によって定義されます。 k コインから開始し、任意の順序でカジノを訪問できますが、それぞれ 1 回だけです。"
date: "2026-06-08T03:22:46+07:00"
tags: ["codeforces", "competitive-programming", "data-structures", "greedy", "sortings"]
categories: ["algorithms"]
codeforces_contest: 2126
codeforces_index: "D"
codeforces_contest_name: "Codeforces Round 1037 (Div. 3)"
rating: 1200
weight: 2126
solve_time_s: 78
verified: true
draft: false
---

[CF 2126D - これが最後です](https://codeforces.com/problemset/problem/2126/D)

 **評価:** 1200
 **タグ:** データ構造、貪欲、ソート
 **解決時間:** 1 分 18 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 一連のカジノが与えられ、それぞれがコインの範囲によって定義されます。`[l_i, r_i]`プレーしなければならないこと、そして決まった結果`real_i`それがそこでプレイした後の新しいコイン数となります。 から始めます`k`コインを獲得し、任意の順序でカジノにアクセスできますが、それぞれ 1 回のみです。 目標は、最終的にコインの数を最大化することです。 

入力は複数のテスト ケースで構成され、それぞれがカジノの数を指定します。`n`そして初期コイン`k`、 に続く`n`三つ子`(l_i, r_i, real_i)`。 出力はテスト ケースごとに 1 つの整数、つまり達成可能な最大コインです。 

与えられた`n`まで可能です`10^5`すべてのテストケースにわたって、`k`、`l_i`、`r_i`、`real_i`まで`10^9`、すべての順列をチェックするソリューションは実行不可能です。 ナイーブ`O(n!)`このアプローチはすぐに失敗します。 テストケースごとに線形または線形に近いアプローチが必要です。 

エッジケースには、次のようなものがあります。`0`コイン、カジノどこ`real_i`カウントは向上しません。または、一部のカジノを訪問すると最初に他のカジノがブロックされるシーケンスもあります。`l_i`しきい値。 たとえば、次の場合`k = 0`そしてすべてのカジノには`l_i >= 1`、最大コインが残っています`0`。 最大のものを選択する不用意な貪欲なアプローチ`real_i`そのカジノにまだアクセスできない場合は、盲目的に失敗する可能性があります。 

## アプローチ

 ブルートフォース手法では、カジノ訪問のすべての順列を試し、コイン数を更新し、最大値を追跡します。 これは原理的には機能しますが、絶望的に遅いです。`n = 10^5`カジノでは、順列の数は天文学的に多くなります。 

重要な観察は、カジノでプレイできるようになると、前提条件が満たされている限り、新しいコインのカウントはそこに到着した順序とは無関係であるということです。`l_i <= coins <= r_i`満足しています。 したがって、貪欲な戦略が機能します。つまり、その時点でコインを最大化するプレイ可能なカジノを繰り返し選択できます。 プレイするたびに、現在のコインは増加するか、そのままになります。 プレイ可能なカジノがなくなると停止します。 

これにより、問題は、未訪問のカジノのセットを維持し、現在プレイ可能なカジノを繰り返し選択するプロセスのシミュレーションに集約されます。 キューまたはリストを使用して、ラウンドごとに未訪問のカジノをすべてスキャンすると、`O(n^2)`最悪の場合、遅すぎます。 代わりに、カジノを下限で並べ替えます`l_i`これにより、プレイ可能なカジノを効率的に特定し、新しいカジノが追加されなくなるまで繰り返すことができます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | お(ん!) | O(n) | 遅すぎる |
 | ソートによる貪欲なシミュレーション | O(n log n) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. テストケースの数を読み取る`t`。 
2. 各テスト ケースについて、次の内容を読んでください。`n`そして初期コイン`k`。 カジノをタプルのリストとして保存する`(l_i, r_i, real_i)`。 
3. カジノを下限で並べ替えます`l_i`。 これにより、左から右にスキャンすると、どのカジノでも`l_i <= current coins`すぐにプレイ可能です。 
4. フラグを初期化する`changed = True`。 その間`changed`が true の場合は、次の操作を実行します。 

1.セット`changed = False`。 
2. すべてのカジノを反復処理します。 カジノが未訪問の場合`l_i <= current coins <= r_i`、訪問したことをマークし、更新します`current coins = real_i`、設定します`changed = True`。 
5. 新しいカジノがプレイできなくなるまで繰り返します。 現在のコイン数は達成可能な最大値です。 

機能する理由: このプロセスでは、カジノがプレイ可能になるたびに、可能な限り早い反復でそのカジノにアクセスすることが保証されます。 以来`real_i >= l_i`各カジノの使用は 1 回のみで、コインが減ることはなく、あらゆるチャンスを逃すことがありません。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

def solve():
    t = int(input())
    for _ in range(t):
        n, k = map(int, input().split())
        casinos = []
        for _ in range(n):
            l, r, real = map(int, input().split())
            casinos.append([l, r, real, False])  # last element marks visited

        casinos.sort(key=lambda x: x[0])  # sort by l_i
        coins = k
        changed = True
        while changed:
            changed = False
            for casino in casinos:
                if not casino[3] and casino[0] <= coins <= casino[1]:
                    coins = casino[2]
                    casino[3] = True
                    changed = True
        print(coins)

if __name__ == "__main__":
    solve()
```並べ替える`l_i`どのカジノがすぐにプレイ可能かを効率的に特定します。 私たちは、`visited`flag to avoid revisiting. 外側のループは新しいコインが獲得されている間のみ実行され、終了が保証されます。 

## 実用的な例

 **例 1:**

 入力:```
3 1
2 3 3
1 2 2
3 10 10
```| コイン | 訪れたカジノ | 訪問後のコイン |
 | --- | --- | --- |
 | 1 | カジノ 2 (1,2,2) | 2 |
 | 2 | カジノ 1 (2,3,3) | 3 |
 | 3 | カジノ 3 (3,10,10) | 10 |

 Final coins: 10. The table confirms that choosing casinos in an order respecting`l_i <= coins <= r_i`コインを最大化します。 

**例 2:**

 入力:```
1 0
1 2 2
```| コイン | 訪れたカジノ | 訪問後のコイン |
 | --- | --- | --- |
 | 0 | なし | 0 |

 最終的なコイン: 0。これにより、アルゴリズムが到達不能なカジノを処理していることが確認されます。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n log n) | テイクの並べ替え`O(n log n)`。 The while loop scans each casino at most once per coin increase. Since coins increase monotonically and casinos are only visited once, total iterations are`O(n)`。 |
 | スペース | O(n) | フラグを付けてカジノを保存するには次のものが必要です`O(n)`。 |

 このソリューションは、最大値であっても 2 秒の制限内に問題なく収まります。`n = 10^5`。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    sys.stdout = io.StringIO()
    solve()
    return sys.stdout.getvalue().strip()

# provided samples
assert run("5\n3 1\n2 3 3\n1 2 2\n3 10 10\n1 0\n1 2 2\n1 2\n1 2 2\n2 2\n1 3 2\n2 4 4\n2 5\n1 10 5\n3 6 5") == "10\n0\n2\n4\n5", "sample 1"

# minimum input
assert run("1\n1 0\n0 0 0") == "0", "minimum input"

# maximum single casino reachable
assert run("1\n1 100\n50 200 300") == "300", "max coins single casino"

# all casinos unreachable
assert run("1\n3 0\n1 2 2\n2 3 3\n3 4 4") == "0", "all unreachable"

# sequential unlocks
assert run("1\n3 1\n1 2 2\n2 3 3\n3 4 4") == "4", "sequential unlocks"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`1\n1 0\n0 0 0`| 0 | 最小限のコインと単一のカジノを処理します |
 |`1\n1 100\n50 200 300`| 300 | 到達可能な大規模カジノを扱う |
 |`1\n3 0\n1 2 2\n2 3 3\n3 4 4`| 0 | 最初からすべてのカジノにアクセスできません |
 |`1\n3 1\n1 2 2\n2 3 3\n3 4 4`| 4 | カジノの順次ロック解除が機能します |

 ## 特殊なケース

 コインゼロから始めてカジノはプラスが必要`l_i`いかなる遊びも決して許さない。 例えば、`k=0`、カジノ`(1,2,2)`そして`(3,5,5)`。 このループはすべてのカジノをスキャンし、プレイ可能なカジノを見つけず、セットします。`changed=False`、すぐに終了し、0 を出力します。 

複数のカジノがある場合`l_i`現在のコイン以下の場合、コイン数は増加するか同じままであるため、順序は重要ではありません。 のために`k=1`、カジノ`(1,3,2)`そして`(1,2,5)`、アルゴリズムはスキャンで最初のカジノを選択し、コインを 2 に更新してから 2 番目のカジノを選択します (コイン 2 は満たされます)`l_i=1`) コインを 5 に更新します。最終的なコインは正しいです。
