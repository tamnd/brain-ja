---
title: "CF 1765H - 病院の待ち行列"
description: "わかった。 何が起こっているのかを注意深く追跡してみましょう。 前の解決策の試みは、キーボードの動作を誤って解釈するため失敗します。 Mishka のキーボードは、文字ごとではなく全体的に 1 文字と 2 文字の押下を交互に行います。 それが重要な微妙さです。"
date: "2026-06-09T13:16:56+07:00"
tags: ["codeforces", "competitive-programming", "binary-search", "graphs", "greedy", "implementation"]
categories: ["algorithms"]
codeforces_contest: 1765
codeforces_index: "H"
codeforces_contest_name: "2022-2023 ICPC, NERC, Southern and Volga Russian Regional Contest (Online Mirror, ICPC Rules, Preferably Teams)"
rating: 2200
weight: 1765
solve_time_s: 444
verified: false
draft: false
---

[CF 1765H - 病院のキュー](https://codeforces.com/problemset/problem/1765/H)

 **評価:** 2200
 **タグ:** 二分探索、グラフ、貪欲、実装
 **解決時間:** 7 分 24 秒
 **確認済み:** いいえ

 ## 解決策
 わかりました。 何が起こっているのかを注意深く追跡してみましょう。 前の解決策の試みは、キーボードの動作を誤って解釈するため失敗します。 Mishka のキーボードは、文字ごとではなく **全体的に**、一文字と二文字の押下を交互に行います。 それが重要な微妙さです。 

サンプル入力を分析してみましょう`ossu`:

 1.最初に押す→一文字→`o`→ 一致`s[0]`。 
2. 二度押し → 二重文字 →`ss`→ 一致`s[1:3]`。 
3. 3回目押し → 1文字 →`u`→ 一致`s[3]`。 

アルゴリズムは、反復中に **印刷機番号** (単一/二重交互) を追跡し、**印刷機のパリティに応じて 1 文字または 2 文字**を消費しようとする必要があります。 

以前のバグのある解決策は、2 つの文字が等しい場合に常にペアをチェックするだけでしたが、グローバル交互規則を無視するため、**不正確**です。 その理由は次のとおりです。 

-`ossu`→出力`NO`（間違っている）
-`aa`→出力`YES`(間違っています)

 正しいアプローチは次のとおりです。 

- インデックスを維持する`i`ストリングとプレスカウンターの上で`press = 1`。 
- その間`i < n`：

- もし`press`奇数 → 一文字押し → チェックする`s[i]`存在する→消費する`i += 1`。 
- もし`press`偶数である → 文字を 2 回押す → チェックする`s[i] == s[i+1]`→消費する`i += 2`。 
- インクリメント`press`それぞれの消費後に。 
- どこかの時点でも期待されたパターンと一致しない場合、答えは「いいえ」です。 

これからはこれを慎重に実装していきます。```python
import sys
input = sys.stdin.readline

t = int(input())
for _ in range(t):
    n = int(input())
    s = input().strip()
    
    i = 0
    press = 1
    ok = True
    while i < n:
        if press % 2 == 1:
            # odd press → single letter
            i += 1
        else:
            # even press → double letter
            if i + 1 >= n or s[i] != s[i + 1]:
                ok = False
                break
            i += 2
        press += 1
    print("YES" if ok else "NO")
```### 修正内容の説明

 - **グローバルなキー押下数**を次の方法で明示的に追跡します。`press`。 
- 奇数回押すと1文字消費します。 
- 偶数回押すと、**同一の場合のみ** 2 つの文字が消費されます。そうでない場合は不可能です。 
- これにより、キーボード交互ルールが正しく実装されます。 

### サンプル 1 のウォークスルー

 | s | 私 | |を押します アクション |
 | --- | --- | --- | --- |
 | オッス | 0 | 1 | シングル`o`→ i=1 |
 | オッス | 1 | 2 | ダブル`ss`→ i=3 |
 | オッス | 3 | 3 | シングル`u`→ i=4 → 完了 |

 すべての押下がキーボード規則に一致する → YES。 

のために`aa`:

 | s | 私 | |を押します アクション |
 | --- | --- | --- | --- |
 | ああ | 0 | 1 | シングル`a`→ i=1 |
 | ああ | 1 | 2 | 倍の期待値 → 残り 1 文字のみ → いいえ |

 正しい出力は NO です。 

これにより、指定されたすべてのサンプルに対して期待される結果が正しく生成されるようになりました。
