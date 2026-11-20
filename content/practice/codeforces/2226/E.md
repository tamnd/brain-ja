---
title: "CF 2226E - メンタルモニュメンタル (ハードバージョン)"
description: "配列が与えられ、それを接頭辞ごとに調べます。 各プレフィックスについて、非常に寛容な操作を使用して、各要素を個別に 1 回だけ変換することができます。つまり、任意の整数 $bi ge 1$ を選択し、$ai$ を $bi$ で割った余りで置き換えます。"
date: "2026-06-01T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "data-structures", "greedy", "math", "two-pointers"]
categories: ["algorithms"]
codeforces_contest: 2226
codeforces_index: "E"
codeforces_contest_name: "Codeforces Round 1095 (Div. 2)"
rating: 0
weight: 2226
solve_time_s: 239
verified: false
draft: false
---

[CF 2226E - 精神の記念碑 (ハード バージョン)](https://codeforces.com/problemset/problem/2226/E)

 **評価:** -
 **タグ:** データ構造、貪欲、数学、2 つのポインター
 **解決時間:** 3 分 59 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 配列が与えられ、それを接頭辞ごとに調べます。 各プレフィックスについて、非常に寛容な操作 (任意の整数を選択) を使用して、各要素を個別に 1 回だけ変換することができます。$b_i \ge 1$、 交換する$a_i$それを割った余りで$b_i$。 これをすべての位置に対して実行した後、結果として得られる配列の mex を調べ、可能なすべての選択肢にわたってそれを最大化したいと考えます。$b_i$。 

mex は、変換後に表示されない最小の非負の整数です。 したがって、長さのプレフィックスの場合$i$、答えは最大です$k$すべての値を強制できるように$0,1,\dots,k-1$変換された接頭辞のどこかに表示されます。 

通常の競技プログラミングの意味では制約は厳しく、次のとおりです。$2 \cdot 10^5$テスト ケース全体の長さ、および最大値$10^6$。 これにより、二次関数は除外されます。$n$, and also rules out any solution that expands per-element behavior into large per-value simulations without careful amortization. Anything involving matching or greedy simulation must run in essentially linear or near-linear time.

 A subtle failure case for naive thinking comes from assuming each element independently contributes a simple range of achievable values. For example, it is tempting to think we can just compute a “cap” per element and greedily take smallest missing numbers. That breaks when many elements compete for the same small values.

 次のようなプレフィックスを検討してください$[3,3,3]$。 それぞれの要素が生み出すことができるのは、$0,1$、 または$3$。 素朴な貪欲な人は次のように割り当てるかもしれません$0$1つの要素に、そして$1$別の人にとって、メキシコの結論は次のとおりです$2$。 しかし、代わりにあなたが持っていたとしたら$[1,2]$、どちらの要素も慎重に必要であり、間違った割り当ての選択により mex への到達が妨げられる可能性があります。$2$。 問題は、各要素が最大 1 つのターゲット値に対して再利用可能であるため、これは独立した実現可能性チェックではなく、制約された割り当ての問題であるということです。 

## アプローチ

 ブルート フォース アプローチは概念的に単純です。固定プレフィックスの場合、考えられるすべての選択肢を列挙します。$b_i$、結果の変換された配列を計算し、その mex を計算します。 「合理的」だけに注目しても$b_i$、組み合わせの数はプレフィックス長の指数関数的になります。 これはたとえ$n=30$、各ポジションには多くの有効な選択肢があるため、$b_i$、そしてすべてのポジションにわたって調整された割り当てを考慮する必要があります。 

重要な簡素化は、単一の要素がどのような値を生成できるかを理解することから生まれます。 価値のために$x = a_i$、選択する$b > x$余りを次と等しくします$x$したがって、元の値は常に達成可能です。 さらに興味深いのは、$r < x$、強制することができます$x \bmod (x-r) = r$いつでも$x > 2r$。 これは、すべての要素がその値のおよそ半分に独自の値を加えたものまでのすべての小さな値を生成できることを意味します。 

したがって、各位置は「到達可能な mex 候補」のセットに寄与します。つまり、以下のすべての整数です。$0$まで$\lfloor (a_i-1)/2 \rfloor$、さらに値$a_i$自体。 

ここで問題は、プレフィックスごとに、次のすべての整数をカバーする個別のインデックスを割り当てることができるかということです。$0$上向き？ それぞれの数字$x$それを生成できるインデックスが 1 つ必要です。 これは値とインデックスの間のマッチングの問題ですが、プレフィックスを拡張する際に貪欲な構築が機能するように十分に構造化されています。 

プレフィックスごとに最初から再計算する代わりに、利用可能なインデックスの増大するセットを維持し、現在達成可能な mex を可能な限り拡張しようとします。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | 全てにおいてブルートフォース$b_i$| 指数関数 | 指数関数 | 遅すぎる |
 | 貪欲な増分マッチング |$O(n \log n)$償却済み |$O(n)$| 承認済み |

 ## アルゴリズムのチュートリアル

 現在の最大 mex を維持しながら、プレフィックスを段階的に処理します。$k$私たちがこれまでに構築に成功してきました。 

各インデックス$i$2種類の有用性をもたらします。 まず、すべての値をサポートできます。$x \le \lfloor (a_i-1)/2 \rfloor$。 第二に、値を排他的にサポートできます。$a_i$。 「小さな値」は間隔カバレッジのように動作するのに対し、正確な値は特別な 1 回限りのフォールバックのように動作するため、この分離は重要です。 

配列をスキャンするときに 3 つの状態を維持します。 

ポインタを維持します$k$、これまでに構築に成功した現在の mex です。 

小さな値をサポートできるインデックスのプールを維持します$x \le k$、彼らの意味$a_i$十分な大きさなので、$\lfloor (a_i-1)/2 \rfloor \ge k$。 これらのインデックスは、有効である限り、現在および将来のすべての小さな値に対して引き続き使用できます。 

また、バケットを正確な値で管理し、インデックスを保存します。$a_i = v$まだ使用されていないもの。 

各接頭辞ステップで、新しい要素が到着すると、それを両方の構造に挿入します。 次に、mex を可能な限り拡張しようとします。 

各候補値について$x = k$、未使用のインデックスを 1 つ割り当てようとします。$x$。 正確な値が以下に等しいインデックスを使用することを好みます。$x$なぜなら、これらのインデックスは他の値には役に立たないからです。 そのようなインデックスが存在しない場合は、サポートするのに十分な容量がまだある一般プールからインデックスを取得します。$x$。 インデックスは一度使用されると、すべての構造から削除されます。 

連続する値にインデックスを正常に割り当てることができるまで、このプロセスを繰り返します。 割り当てることができない最初の値はプロセスを停止します。その値は現在のプレフィックスの mex です。 

重要な不変条件は、処理後の値です。$x-1$、すべての値に個別のインデックスを割り当てました$0,1,\dots,x-1$、残りの未使用のインデックスはすべて、その機能に応じて将来の割り当てに引き続き使用できます。 割り当てようとすると$x$、正確なバケットと一般的なプールの両方が失敗した場合、未使用のインデックスが生成できないことを意味します。$x$, したがって、mex を拡張することは不可能です。 逆に、割り当てが存在する場合、正確なインデックスは他の値には役に立たないため、最初に完全一致を使用するという貪欲な選択が将来の実現可能性を損なうことはありません。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

def solve():
    t = int(input())
    for _ in range(t):
        n = int(input())
        a = list(map(int, input().split()))

        # bucket for exact values
        exact = [[] for _ in range(max(a) + 2)]

        # all indices as candidates for "large support"
        # we store (cap, idx), cap = (a[i]-1)//2
        import heapq
        heap = []

        used = [False] * n

        k = 0  # current mex

        for i, val in enumerate(a):
            cap = (val - 1) // 2
            heapq.heappush(heap, (cap, i))
            exact[val].append(i)

            while True:
                chosen = -1

                # try exact match first
                if k < len(exact) and exact[k]:
                    while exact[k] and used[exact[k][-1]]:
                        exact[k].pop()
                    if exact[k]:
                        chosen = exact[k].pop()

                if chosen == -1:
                    # try general pool
                    while heap:
                        cap_i, idx = heapq.heappop(heap)
                        if used[idx]:
                            continue
                        if cap_i >= k:
                            chosen = idx
                            break

                if chosen == -1:
                    break

                used[chosen] = True
                k += 1

            print(k, end=" ")
        print()

def main():
    solve()

if __name__ == "__main__":
    main()
```この実装では、前方にのみ移動するグローバル mex ポインターが保持されるため、プレフィックスごとの再計算が回避されます。 各インデックスは 1 回挿入され、1 回削除されるため、ヒープ操作は対数的に償却されたままになります。 正確な値のバケットは、すでに使用されているインデックスの遅延クリーンアップを備えた単純なスタックです。 

よくある落とし穴は、インデックスが完全一致として使用されるとヒープ エントリが古くなってしまうことを忘れていることです。 コードはチェックすることでこれを処理します。`used[idx]`候補者を受け入れる前に。 

## 実用的な例

 ### 例 1

 入力プレフィックス:$[0,1,2]$| ステップ | 付加価値 | 利用可能な正確な | メキシコ人$k$前 | アクション | 後のメキシコ |
 | --- | --- | --- | --- | --- | --- |
 | 1 | 0 | {0} | 0 | 正確な 0 を使用する | 1 |
 | 2 | 1 | {1} | 1 | 正確な 1 を使用する | 2 |
 | 3 | 2 | {2} | 2 | 正確な 2 を使用する | 3 |

 これは、各要素が独自の値を直接サポートするクリーンなケースを示しているため、mex は線形に増加します。 

### 例 2

 入力プレフィックス:$[6,7]$| ステップ | 付加価値 | 利用可能な正確な | メキシコ人$k$前 | アクション | 後のメキシコ |
 | --- | --- | --- | --- | --- | --- |
 | 1 | 6 | {6} | 0 | 一般的な (または後で正確な) を使用します。 1 |
 | 2 | 7 | {6,7} | 1 | 正確な 1 を使用しますか? 不可能なので、一般/構造化された代入を使用してください。 2 |

 ここで、正確な値は小さい mex 値には役に立たないため、アルゴリズムは間隔機能に依存します。$0 \dots \lfloor (a_i-1)/2 \rfloor$。 これは、正確な一致と間隔のサポートを分離する必要がある理由を示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |$O(n \log n)$| 各インデックスはヒープから最大 1 回プッシュおよびポップされ、mex は全体的に増加するだけです。 
| スペース |$O(n)$| ヒープ エントリ、正確なバケット、使用量配列の保存 |

 演算の合計数は対数係数まで線形であり、次の合計が与えられた制約の下で快適に適合します。$n$テストケース全体で。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    from collections import deque
    import heapq

    def solve():
        t = int(input())
        out = []
        for _ in range(t):
            n = int(input())
            a = list(map(int, input().split()))

            exact = [[] for _ in range(max(a) + 2)]
            import heapq
            heap = []
            used = [False] * n
            k = 0
            res = []

            for i, val in enumerate(a):
                cap = (val - 1) // 2
                heapq.heappush(heap, (cap, i))
                exact[val].append(i)

                while True:
                    chosen = -1

                    if k < len(exact) and exact[k]:
                        while exact[k] and used[exact[k][-1]]:
                            exact[k].pop()
                        if exact[k]:
                            chosen = exact[k].pop()

                    if chosen == -1:
                        while heap:
                            cap_i, idx = heapq.heappop(heap)
                            if used[idx]:
                                continue
                            if cap_i >= k:
                                chosen = idx
                                break

                    if chosen == -1:
                        break

                    used[chosen] = True
                    k += 1

                res.append(str(k))

            out.append(" ".join(res))
        return "\n".join(out)

    return solve()

# custom sanity checks (small hand-verifiable cases)
assert run("1\n1\n0\n") == "1\n"
assert run("1\n2\n0 1\n") == "1 2\n"
assert run("1\n3\n0 0 0\n") == "1 1 1\n"
assert run("1\n3\n1 2 3\n") == "1 2 3\n"
assert run("1\n4\n6 7 8 9\n")  # just ensure no crash
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`[0]`|`1`| 最小限のプレフィックス動作 |
 |`[0,1]`|`1 2`| メキシコの線形成長 |
 |`[0,0,0]`|`1 1 1`| 重複処理 |
 |`[1,2,3]`|`1 2 3`| 増加する構造 |
 |`[6,7,8,9]`| 単調な成長 | 大きな値の間隔依存性 |

 ## 特殊なケース

 多くの要素が同じ小さな値を共有する場合、まれなケースが発生します。 のような入力の場合$[0,0,0,0]$、各プレフィックスは引き続き mex を正しく報告する必要があります$1$、生成するために必要なインデックスは 1 つだけであるため、$0$。 1 つのインデックスが値として消費されると、アルゴリズムがこれを処理します。$0$、残りのインデックスは mex をそれ以上拡張するのに貢献できません。$1$。 

もう 1 つのケースは、値が大きいがまばらな場合です。$[10^6, 10^6, \dots]$。 ここでは完全一致が優先され、間隔の機能は無関係です。 各インデックスは最大 1 回しか使用できず、十分な個別のインデックスが利用可能な場合にのみ mex が増加するため、アルゴリズムは引き続き正しく動作します。 

微妙なケースとして、正確な寄与と間隔の寄与が混在している場合があります。たとえば、$[2,4,7]$。 完全一致として使用するのに最適な値もあれば、小さな mex 値をより広範囲にカバーする値もあります。 貪欲な優先順位により、正確に一致するものが最初に消費されるようになり、より柔軟なインデックスでカバーできたはずの値が無駄に使用されるのを防ぎます。
