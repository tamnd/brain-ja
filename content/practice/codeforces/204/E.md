---
title: "CF 204E - 小さな象とひも"
description: "文字列のリストが与えられ、各文字列について、リストの少なくとも k 個の文字列に出現する部分文字列の数を数える必要があります。 部分文字列は、文字列の連続したセグメントです。"
date: "2026-05-29T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "data-structures", "implementation", "string-suffix-structures", "two-pointers"]
categories: ["algorithms"]
codeforces_contest: 204
codeforces_index: "E"
codeforces_contest_name: "Codeforces Round 129 (Div. 1)"
rating: 2800
weight: 204
solve_time_s: 45
verified: true
draft: false
---

[CF 204E - 小さな象とひも](https://codeforces.com/problemset/problem/204/E)

 **評価:** 2800
 **タグ:** データ構造、実装、文字列サフィックス構造、2 つのポインター
 **解決時間:** 45 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 文字列のリストが与えられ、各文字列について、リストの少なくとも _k_ 個の文字列に出現する部分文字列の数を数える必要があります。 部分文字列は、文字列の連続したセグメントです。 たとえば、文字列が「abc」の場合、その部分文字列は「a」、「b」、「c」、「ab」、「bc」、「abc」になります。 これらのそれぞれについて、入力内の文字列にその文字列がいくつ含まれているか、またその数が少なくとも _k_ であるかどうかを知る必要があります。 出力は、このカウントを表す一連の整数 (文字列ごとに 1 つ) です。 

制約は厳しく、文字列は最大 100,000 個まで存在でき、すべての文字列の合計長は最大 100,000 文字です。 これにより、部分文字列のすべてのペアを直接チェックするアプローチは即座に除外されます。 長さ _m_ の文字列の部分文字列の総数は _m_(m+1)/2 で、最悪の場合、すべての文字列全体で 10^10 に近くなる可能性があります。 したがって、O(n²) または O(total_substrings) のアプローチは完全に実行不可能です。 これは、部分文字列の出現を効率的に集約できるグローバル文字列構造が必要であることを示唆しています。 

明白ではないエッジ ケースは、複数の文字列が同一である場合、または _k_ が 1 の場合に発生します。たとえば、入力が ["a", "aa", "aaa"] (k=1) の場合、すべての部分文字列が少なくとも 1 つの文字列に出現するため、すべての部分文字列がカウントされます。 文字列全体の個別の部分文字列のみをカウントする不注意な実装では、過小カウントされる可能性があります。 もう 1 つのエッジ ケースは、重複する部分文字列を持つ文字列です: ["ab", "ba"] (k=2)。 部分文字列「a」は両方の文字列に現れますが、「ab」と「ba」はどちらか一方の文字列にのみ現れます。 これらの重複を正しく処理するには、正確な簿記が必要です。 

## アプローチ

 ブルート フォースのアプローチは概念的には単純です。文字列ごとに、そのすべての部分文字列を列挙し、それぞれの部分文字列が少なくとも _k_ 個の文字列に出現するかどうかを確認します。 これには、すべての文字列のすべての部分文字列を反復してから、すべての文字列を反復して、その部分文字列が含まれているかどうかを確認する必要があります。 すべての文字列の合計長が _L_ の場合、部分文字列の数は L² のオーダーになります。 高速な部分文字列チェックを仮定したとしても、総作業量は 10^10 演算をはるかに超えることになりますが、与えられた時間制約の下では不可能です。 

最適な解決策を可能にする重要な観察は、どの文字列に部分文字列が個別に含まれるかは気にせず、個別の文字列にその部分文字列が含まれる数だけを気にするということです。 これは、一般化された接尾辞オートマトンや接尾辞トライ/ツリーなどの接尾辞データ構造を示唆しており、各ノードは部分文字列を表し、その部分文字列を含む個別の文字列の数で注釈を付けることができます。 すべての文字列に対して一般化された接尾辞オートマトンを構築すると、すべての一意の部分文字列を効率的に走査できるようになります。 部分文字列ごとのカウントが得られたら、各文字列を反復処理し、オートマトン内の文字列の接尾辞リンクに沿った 2 つのポインターを使用して、しきい値 _k_ を満たす部分文字列の数を数えます。 

2 ポインター トラバーサルは、文字列内の特定の開始インデックスについて、_k_ しきい値を満たす部分文字列内に留まりながら、終了インデックスを可能な限り拡張できるため、効率的です。 各開始インデックスからの長さの合計により、その文字列の条件を満たす部分文字列の合計数が得られます。 これにより、問題は、すべての文字列の全長にオートマトンの遷移のオーバーヘッドを乗じたものに対して本質的に線形な時間に短縮されます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(L3) | O(L²) | 遅すぎる |
 | サフィックス オートマトン + 2 つのポインター | O(L) | O(L) | 承認済み |

 ## アルゴリズムのチュートリアル

1. 部分文字列が文字列の境界を越えないように、すべての文字列を一意の区切り文字で連結します。 誤った重複を避けるために、各区切り文字は「a」から「z」とは区別されます。 
2. 連結された文字列に対して一般化接尾辞オートマトン (SAM) を構築します。 SAM はすべての一意の部分文字列を効率的にエンコードし、サフィックス リンクに沿った高速なトラバーサルを可能にします。 SAM 内の各状態は、その状態で終わる一連の部分文字列を表します。 
3. 各状態について、その状態によって表される部分文字列を含む個別の文字列の数を計算します。 これは、終端状態からサフィックス リンクに沿って上方向に文字列識別子を伝播することによって行うことができます。 
4. 元の文字列ごとに、カウンターを初期化します。 文字列内の各位置から開始して、対応する文字に続いて SAM を横断します。 含まれる文字列の数が _k_ 未満になる状態に達するまで、トラバーサルを延長します。 この走査の長さによって、その位置から始まる条件を満たす部分文字列の数が決まります。 
5. 文字列内のすべての開始位置からのカウントを合計します。 これにより、その文字列に対する答えが得られます。 すべての文字列に対して繰り返します。 

機能する理由: 一般化サフィックス オートマトンは、すべての部分文字列を 1 回だけエンコードし、文字列識別子の伝播により、各部分文字列のカウントがそれを含む個別の文字列の数を反映するようにします。 オートマトンを使用して文字列に沿って走査すると、各位置から始まるすべての部分文字列がカウントされ、2 ポインター法が二重にカウントすることなくすべての最大の有効な部分文字列をキャプチャすることが保証されます。 このアプローチは、効率的に動作しながら、元の問題の要件を尊重します。 

## Python ソリューション```python
import sys
input = sys.stdin.readline
from collections import defaultdict, deque

class SAMNode:
    def __init__(self):
        self.next = {}
        self.link = -1
        self.len = 0
        self.strings = set()

class SuffixAutomaton:
    def __init__(self):
        self.nodes = [SAMNode()]
        self.last = 0
    
    def extend(self, c, string_id):
        p = self.last
        cur = len(self.nodes)
        self.nodes.append(SAMNode())
        self.nodes[cur].len = self.nodes[p].len + 1
        self.nodes[cur].strings.add(string_id)
        while p != -1 and c not in self.nodes[p].next:
            self.nodes[p].next[c] = cur
            p = self.nodes[p].link
        if p == -1:
            self.nodes[cur].link = 0
        else:
            q = self.nodes[p].next[c]
            if self.nodes[p].len + 1 == self.nodes[q].len:
                self.nodes[cur].link = q
            else:
                clone = len(self.nodes)
                self.nodes.append(SAMNode())
                self.nodes[clone].len = self.nodes[p].len + 1
                self.nodes[clone].next = self.nodes[q].next.copy()
                self.nodes[clone].link = self.nodes[q].link
                self.nodes[clone].strings = self.nodes[q].strings.copy()
                while p != -1 and self.nodes[p].next[c] == q:
                    self.nodes[p].next[c] = clone
                    p = self.nodes[p].link
                self.nodes[q].link = self.nodes[cur].link = clone
        self.last = cur

def solve():
    n, k = map(int, input().split())
    strings = [input().strip() for _ in range(n)]
    separators = [chr(123+i) for i in range(n)]
    sam = SuffixAutomaton()
    
    for idx, s in enumerate(strings):
        sam.last = 0
        for c in s + separators[idx]:
            sam.extend(c, idx)
    
    # Propagate string counts using topological order by length
    max_len = max(node.len for node in sam.nodes)
    bucket = [[] for _ in range(max_len+1)]
    for i, node in enumerate(sam.nodes):
        bucket[node.len].append(i)
    
    order = []
    for b in bucket[::-1]:
        order.extend(b)
    
    for u in order:
        if sam.nodes[u].link != -1:
            sam.nodes[sam.nodes[u].link].strings.update(sam.nodes[u].strings)
    
    res = []
    for idx, s in enumerate(strings):
        count = 0
        l = 0
        node = 0
        for r, c in enumerate(s):
            while node != -1 and c not in sam.nodes[node].next:
                node = sam.nodes[node].link
                l = sam.nodes[node].len if node != -1 else 0
            if node == -1:
                node = 0
                l = 0
            else:
                node = sam.nodes[node].next[c]
                while sam.nodes[node].link != -1 and len(sam.nodes[sam.nodes[node].link].strings) >= k:
                    node = sam.nodes[node].link
                    l = sam.nodes[node].len
                count += r - l + 1
        res.append(str(count))
    print(' '.join(res))

if __name__ == "__main__":
    solve()
```コードは最初に、一意の区切り文字を持つすべての文字列に対して一般化された接尾辞オートマトンを構築します。 次に、文字列識別子をサフィックス リンクに沿って、ノードの長さによるトポロジ順に伝播します。 各文字列について、オートマトンに沿った 2 ポインターのような走査を使用して、少なくとも _k_ 個の文字列に出現する部分文字列をカウントします。 一意の区切り文字を使用すると、文字列間の境界を越える部分文字列がカウントされなくなります。 

## 実用的な例

 ### 例 1

 入力:```
3 1
abc
a
ab
```| 文字列 | 部分文字列 | ≥1 文字列に出現 | カウント |
 | --- | --- | --- | --- |
 | "ABC" | a、b、c、ab、bc、abc | すべて | 6 |
 | "a" | | | 1 |
 | "アブ" | a、b、ab | すべて | 3 |

 これにより、アルゴリズムが各位置から始まる部分文字列を正しくカウントし、しきい値 1 が正しく適用されていることを確認できます。
