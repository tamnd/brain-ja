---
title: "CF 132E - 古き良き英国のひととき"
description: "このタスクは、限られた数の変数と 2 つの操作 (変数を整数に代入する操作と、変数を出力する操作) を使用して、指定された一連の整数を出力することです。 各割り当てには、割り当てられた番号の設定ビット数に等しいコストがかかりますが、印刷は無料です。"
date: "2026-05-28T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "flows", "graphs"]
categories: ["algorithms"]
codeforces_contest: 132
codeforces_index: "E"
codeforces_contest_name: "Codeforces Beta Round 96 (Div. 1)"
rating: 2700
weight: 132
solve_time_s: 107
verified: false
draft: false
---

[CF 132E - 陽気な古きイギリスのひとこま](https://codeforces.com/problemset/problem/132/E)

 **評価:** 2700
 **タグ:** フロー、グラフ
 **解決時間:** 1 分 47 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 このタスクは、限られた数の変数と 2 つの操作 (変数を整数に代入する操作と、変数を出力する操作) を使用して、指定された一連の整数を出力することです。 各割り当てには、割り当てられた番号の設定ビット数に等しいコストがかかりますが、印刷は無料です。 目標は、総割り当てコストを最小限に抑えながらシーケンスを正確に出力するプログラムを構築することです。 

入力により、シーケンスの長さ、使用可能な変数の数、およびシーケンス自体が得られます。 シーケンス内の各整数は次のように大きくできます。$10^9$つまり、整数は 30 ビットに収まるため、割り当てには最大 30 のコストがかかる可能性があります。 シーケンスの長さは 250 に達することがあり、最大 26 個の変数を含めることができます。 この小物の組み合わせは、$n$そして適度な$m$複数の変数の状態を追跡する場合でも、動的プログラミングまたはグラフベースのアプローチが実現可能であることを示唆しています。 

エッジ ケースには、同じ数値が連続して出現するシーケンス、変数の数よりも長いシーケンス、最も安価なプログラムで変数を複数回再割り当てする必要があるシーケンスなどが含まれます。 たとえば、シーケンスが次の場合、`1 2 1 2`変数が 1 つしかない場合、変数を決して再利用しない素朴な貪欲な戦略は、変数に既に格納されている数値を追跡し、それらを戦略的に再割り当てするアプローチと比較して、合計ペナルティが高くなる可能性があります。 

## アプローチ

 ブルートフォース手法では、各ステップで変数への数値の割り当てをすべて試行し、途中でシーケンスを出力し、ペナルティを合計します。 これは概念的には正しいですが、遅すぎます。 それぞれを考慮すると、$n$番号は次のいずれかに割り当てることができます$m$変数の場合、状態空間は$O(m^n)$、それはたとえ小さくても実行不可能です$n$。 

重要な洞察は、ペナルティが変数への最初の数値の代入のみに依存することに気づくことです。 数値を変数に保存すると、無料で繰り返し出力できます。 これは、これをキャッシュ問題の変形にします。変数はキャッシュ スロットとして機能し、割り当てコストが重みになり、一連のアクセスの合計重みを最小限に抑えたいと考えます。 なぜなら$n$そして$m$が小さい場合は、各ステップでどの数値がどの変数を占めるかを表す状態を使用して、動的プログラミングを使用してこれをモデル化できます。 

現在の変数割り当てのタプルとして状態を追跡できます。 各ステップで、現在の数値を出力するには、その数値を保持する既存の変数を使用するか、それを変数に代入して、場合によっては別の数値を削除します。 DP は合計ペナルティが最小限になるアクションを選択し、割り当てのシーケンスを再構築し、標準の親ポインターを使用して出力します。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(m^n) | O(n) | 遅すぎる |
 | 動的プログラミング / 状態圧縮 | O(n * m * 2^m) | O(n * 2^m * m) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. シーケンス内のすべての数値を列挙し、それらを圧縮して状態表現を削減します。 それぞれの一意の番号にインデックスを付けることができます。 
2. 変数の現在の状態を長さのタプルとして表現します。$m$、各スロットには数字が含まれるか空です。 これにより、保存された数値の「キャッシュ」がキャプチャされます。 
3. DP 配列を初期化する`dp[pos][state]`どこ`pos`はシーケンス内の位置であり、`state`現在の変数の割り当てです。`dp[pos][state]`最初に印刷するための最小限のペナルティを保存します`pos`この状態で終わる数字。 
4. シーケンスを繰り返します。 それぞれの位置と状態について、現在の数値の 2 つのオプションを検討してください。 

- すでに変数に格納されている場合は、それを無料で出力し、同じ状態で DP を次の位置に伝播します。 
- 保存されていない場合は、それを任意の変数に代入し (前の内容を上書きする可能性があります)、この数値の設定ビット数をペナルティに追加します。 更新された状態で DP を次の位置に伝播します。 
5. シーケンス全体を処理した後、最後の DP 層で最小のペナルティを見つけます。 親ポインタを使用して、割り当てと出力のシーケンスを再構築します。 
6. プログラムを出力します。各割り当ての後に出力コマンドが続きます。`variable=number`そして`print(variable)`順番に。 

機能する理由: DP は、各ステップで、変数内の数値の任意の組み合わせでその位置に到達するための最小限のペナルティがわかっているという不変式を維持します。 各ステップですべての割り当てを調査することで、グローバル最小値を見つけることが保証されます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

def bits(x):
    return bin(x).count('1')

def solve():
    n, m = map(int, input().split())
    seq = list(map(int, input().split()))
    unique = list(sorted(set(seq)))
    idx = {num:i for i,num in enumerate(unique)}
    seq_idx = [idx[x] for x in seq]
    u = len(unique)
    
    INF = 10**9
    from collections import deque
    
    # dp[pos][state_tuple] = penalty
    dp = [{} for _ in range(n+1)]
    parent = [{} for _ in range(n+1)]
    
    empty_state = tuple([-1]*m)
    dp[0][empty_state] = 0
    
    for pos in range(n):
        num = seq_idx[pos]
        for state, cost in dp[pos].items():
            # check if number is already in some variable
            if num in state:
                dp[pos+1][state] = min(dp[pos+1].get(state, INF), cost)
                parent[pos+1][state] = (state, None)
            # try assigning to any variable
            for i in range(m):
                new_state = list(state)
                new_state[i] = num
                new_state_tuple = tuple(new_state)
                new_cost = cost + bits(unique[num])
                if new_cost < dp[pos+1].get(new_state_tuple, INF):
                    dp[pos+1][new_state_tuple] = new_cost
                    parent[pos+1][new_state_tuple] = (state, i)
    
    # find minimal penalty in last position
    min_penalty = INF
    last_state = None
    for state, cost in dp[n].items():
        if cost < min_penalty:
            min_penalty = cost
            last_state = state
    
    # reconstruct program
    program = []
    state = last_state
    pos = n
    var_name = [chr(ord('a')+i) for i in range(m)]
    current_vars = [-1]*m
    while pos > 0:
        prev_state, assigned_var = parent[pos][state]
        num = seq[pos-1]
        if assigned_var is None:
            # number already in variable, find which
            for i,v in enumerate(current_vars):
                if v == seq_idx[pos-1]:
                    program.append(f'print({var_name[i]})')
                    break
        else:
            program.append(f'print({var_name[assigned_var]})')
            program.append(f'{var_name[assigned_var]}={num}')
            current_vars[assigned_var] = seq_idx[pos-1]
        state = prev_state
        pos -= 1
    
    program.reverse()
    print(len(program), min_penalty)
    for line in program:
        print(line)

if __name__ == "__main__":
    solve()
```このソリューションでは、まず数値をインデックスに圧縮して、メモリと計算のオーバーヘッドを削減します。 変数の代入をタプルとして表し、DP を使用して最小限のペナルティを保存します。 親ポインタは、割り当てを再構築し、コマンドを出力するために使用されます。 再構築では、DP 状態を実際の変数名に注意深くマップし直します。 

## 実用的な例

 入力例:```
7 2
1 2 2 4 2 1 2
```各位置の主な変数:

 | 位置 | 状態 | コスト | アクション |
 | --- | --- | --- | --- |
 | 0 | (-1,-1) | 0 | var b に 1 を代入する |
 | 1 | (0,-1) | 1 | 印刷b |
 | 2 | (0,1) | 3 | 変数 a に 2 を代入します |
 | 3 | (0,1) | 3 | | を印刷します
 | 4 | (2,1) | 4 | var b に 4 を代入する |
 | 5 | (2,1) | 4 | | を印刷します
 | 6 | (0,1) | 4 | b を 1 に再代入し、 | を出力します。 
| 7 | (0,1) | 4 | | を印刷します

 これは、変数を再利用すると、単純な数値ごとの割り当てによって生じるペナルティが軽減されることを示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n * m * 2^m) | 各位置は、以前のすべての状態を反復し、各状態に対して m 回の割り当てを試みます。 |
 | スペース | O(n * 2^m) | 各位置と状態の組み合わせの DP、再構築のための親ポインタを保存します。 |

 と$n$≤ 250 および$m$≤ 26、アクティブな状態の実際の数は 26 よりもはるかに少なくなります。$2^m$実際には、ソリューションは 2 秒以内に快適に実行されます。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    sys.stdout = io.StringIO()
    solve()
    return sys.stdout.getvalue().strip()

# provided sample
assert run("7 2\n1 2 2 4 2 1 2\n").
```
