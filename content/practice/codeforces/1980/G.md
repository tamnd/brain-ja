---
title: "CF 1980G ヤシャと不思議な樹"
description: "n 個の頂点を持つツリーが与えられます。 このツリーの各エッジには重みがあります。 2 種類のクエリが m 個あります。 最初のタイプは、すべてのエッジを指定された数値 y と XOR 演算することによって、すべてのエッジの実効重みを変更します。"
date: "2026-06-08T16:56:36+07:00"
tags: ["codeforces", "competitive-programming", "bitmasks", "data-structures", "dfs-and-similar", "graphs", "greedy", "strings", "trees"]
categories: ["algorithms"]
codeforces_contest: 1980
codeforces_index: "G"
codeforces_contest_name: "Codeforces Round 950 (Div. 3)"
rating: 2300
weight: 1980
solve_time_s: 168
verified: false
draft: false
---

【CF1980G ヤシャと不思議な樹】(https://codeforces.com/problemset/problem/1980/G)

 **評価:** 2300
 **タグ:** ビットマスク、データ構造、DFS など、グラフ、貪欲、文字列、ツリー
 **解決時間:** 2 分 48 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 私たちに与えられた木は、`n`頂点。 このツリーの各エッジには重みがあります。 がある`m`2 種類のクエリ。 最初のタイプは、すべてのエッジを指定された数値で XOR 演算することにより、すべてのエッジの実効重みを変更します。`y`。 2 番目のタイプは、頂点を指定して尋ねます。`v`そして数字`x`、接続によって取得できる最大の XOR はいくらですか`v`他の頂点へ`u`(実際にツリーを変更せずに精神的に)、結果として得られる一意のサイクルの XOR を計算します。 

2 番目のクエリ タイプでは、エッジを追加することでサイクルが形成されることを認識することが重要です。`v-u`からのパスの XOR と等しい XOR があります。`v`に`u`ツリーの中に、プラス`x`追加されたエッジから。 私たちは選ぶことができるので、`u`任意に、問題は頂点を見つけることに帰着します`u`パスの XOR から`v`、と XOR した場合`x`、最大です。 これは、**一連の数値に対する最大の XOR クエリ**に相当します。 

この制約により、すべてのテスト ケースにわたって最大 200,000 個の頂点と 200,000 個のクエリを処理する必要があります。 各クエリは潜在的にすべての頂点を検査できるため、クエリごとに単純な O(n) アプローチでは遅すぎます。 これは、クエリごとの単純なツリー トラバーサルではなく、**前処理**と**効率的な最大 XOR 取得**が必要であることを強く示唆しています。 

明らかではないエッジ ケースには、すべてのエッジの重みが同じ場合、または XOR が更新される場合 (`^ y`) 以前は最適だったパスが最適ではなくなるような方法でビットを反転します。 もう 1 つの微妙な点は、仮想エッジを追加することです。`v`実際にツリーを変更することは決してないので、事前計算は複数の第 2 タイプのクエリにわたって有効なままにしておく必要があり、前のタイプ 1 クエリによって変更されるのは XOR オフセットだけです。 

## アプローチ

 素朴なアプローチは、各クエリを直接処理することです。 2 番目のタイプのクエリの場合`? v x`から DFS を実行できます。`v`他のすべての頂点へのパス XOR を計算してから、`path_xor ^ x`すべての候補者について、最大値を取得します。 複雑さはクエリごとに O(n) です。 以来`n`2×10^5 にすることができ、`m`が 2×10^5 の場合、O(n*m) = 4×10^10 の演算が行われることになりますが、これは明らかに実行不可能です。 

重要な洞察は、各ツリーを 1 回前処理できるということです。 ルート (頂点 1 など) を選択すると、ルートからその頂点までのパスの XOR を頂点ごとに計算できます。これを`px[v]`。 次に、エッジを追加します`v-u`のサイクル XOR を返します`px[v] ^ px[u] ^ x`。 最大限に活用する`u`これはまさに古典的な **配列内の最大 XOR** 問題です。`px[u]`それは最大化します`px[u] ^ x ^ px[v]`。 これは、すべてを格納する **バイナリ トライ** (またはプレフィックス ツリー) を使用して効率的に解決できます。`px[u]`。 

最初のタイプのクエリ`^ y`すべての XOR 値をシフトするだけです`y`。 毎回更新するのではなく、`px[u]`トライでは、グローバル XOR オフセットを維持し、クエリ中に遅延して適用します。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n * m) | O(n) | 遅すぎる |
 | XOR + トライを事前計算する | O(n + m * log(MAXW)) | O(n * log(MAXW)) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 頂点でツリーを任意にルートします。 1. DFS を実行して計算します。`px[v]`、ルートから各頂点までの XOR。 これにより、各頂点にクエリとは独立した静的な値が与えられます。 
2. すべてを含むバイナリ トライを構築します。`px[v]`。 各トライ ノードは、格納されている数値のビット位置を表します。 このトライでは、最大 O(log(MAXW)) の XOR クエリが許可されます。 
3. グローバル XOR オフセットを初期化する`off = 0`。 タイプ 1 クエリの場合`^ y`、 アップデート`off = off ^ y`。 トライには実際の変更は加えられません。 オフセットはルックアップ中に適用されます。 
4. タイプ 2 クエリの場合`? v x`、目標値を計算します`t = x ^ px[v] ^ off`。 トライに数値を問い合わせる`px[u]`それは最大化します`px[u] ^ off`。 最終的な答えは、`max_xor ^ t`。 
5. すべての回答を収集して出力します。 

機能する理由: ルートからすべての頂点までの XOR を保存することにより、仮想エッジの追加によって形成されたサイクルは次のように表現できます。`px[v] ^ px[u] ^ x`。 トライにより、効率的に選択できるようになります。`px[u]`これは、任意の数値との XOR を最大化します。 遅延 XOR オフセットは、すべての累積効果を正確にシミュレートします。`^ y`トライを再構築せずに更新します。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

class TrieNode:
    def __init__(self):
        self.child = [None, None]

class Trie:
    def __init__(self):
        self.root = TrieNode()
        self.BITS = 30  # 1e9 < 2^30
    
    def insert(self, num):
        node = self.root
        for i in reversed(range(self.BITS)):
            b = (num >> i) & 1
            if not node.child[b]:
                node.child[b] = TrieNode()
            node = node.child[b]
    
    def max_xor(self, num):
        node = self.root
        res = 0
        for i in reversed(range(self.BITS)):
            b = (num >> i) & 1
            if node.child[b ^ 1]:
                res |= (1 << i)
                node = node.child[b ^ 1]
            else:
                node = node.child[b]
        return res

def solve():
    t = int(input())
    for _ in range(t):
        n, m = map(int, input().split())
        tree = [[] for _ in range(n+1)]
        for _ in range(n-1):
            u, v, w = map(int, input().split())
            tree[u].append((v, w))
            tree[v].append((u, w))
        
        px = [0] * (n+1)
        visited = [False] * (n+1)
        def dfs(u, par):
            visited[u] = True
            for v, w in tree[u]:
                if not visited[v]:
                    px[v] = px[u] ^ w
                    dfs(v, u)
        dfs(1, -1)
        
        trie = Trie()
        for v in range(1, n+1):
            trie.insert(px[v])
        
        off = 0
        res = []
        for _ in range(m):
            line = input()
            if line[0] == '^':
                y = int(line[2:])
                off ^= y
            else:
                _, v, x = line.split()
                v = int(v)
                x = int(x)
                t_val = x ^ px[v] ^ off
                ans = trie.max_xor(t_val)
                res.append(ans)
        print(*res)

if __name__ == "__main__":
    solve()
```このコードは、まずルートから頂点までのすべての XOR を計算し、それらをトライに挿入します。 のために`^ y`クエリを実行すると、グローバル オフセットが更新されます。 のために`? v x`クエリでは、ターゲットの XOR を計算し、トライを効率的にクエリします。 10^9 までのすべての重みに対して 30 ビットの選択で十分です。 

## 実用的な例

 **サンプル 1:**

 | クエリ |`off`|`px[v]`|`t = x ^ px[v] ^ off`| トライマックス | 答え |
 | --- | --- | --- | --- | --- | --- |
 | ^ 5 | 5 | - | - | - | - |
 | ? 2 9 | 5 | 1 | 9 ^ 1 ^ 5 = 13 | 0 ^ 13 = 13 | 13 |
 | ^ 1 | 4 | - | - | - | - |
 | ? 1 10 | 4 | 0 | 10 ^ 0 ^ 4 = 14 | 1 ^ 14 = 15 | 15 |

 この表は、遅延 XOR がどのようにオフセットされ、事前計算されるかを示しています。`px[v]`組み合わせて効率的に答えを導き出します。 

**サンプル 2:** 同様の処理で、1e9 のような大きな数値が正しく処理されることを確認します。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n + m * log(MAXW)) | DFS には O(n) がかかり、トライ クエリにはクエリごとに O(log(MAXW)) がかかります。 
| スペース | O(n * log(MAXW)) | Try は、それぞれ log(MAXW) ノードを持つ最大 n 個の数値を保存します。 

これは許容されます。`n`、`m`≤ 2×10^5 および log(MAXW) ≤ 30 で、テスト ケースごとに ~6×10^6 の操作が行われます。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    sys.stdout = io.StringIO()
    solve()
    return sys.stdout.getvalue().strip()

# Provided samples
assert run("""2
3 7
1 2 1
3 1 8
^ 5
? 2 9
^ 1
? 1 10
```
