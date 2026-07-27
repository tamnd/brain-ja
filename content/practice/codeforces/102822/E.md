---
title: "CF 102822E - 島からの脱出"
description: "現在の tact() ロジックが壊れており、キュー分散ルールが実装されていないため、Shop の実装は壊れています。 テストでは主に 3 つのことをチェックします。 1. 有効な金庫は常に最短のキューを通じて新しい購入者を受け取ります。 2."
date: "2026-07-26T15:53:18+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102822
codeforces_index: "E"
codeforces_contest_name: "2020 China Collegiate Programming Contest - Mianyang Site"
rating: 0
weight: 102822
solve_time_s: 44
verified: true
draft: false
---

[CF 102822E - 島からの脱出](https://codeforces.com/problemset/problem/102822/E)

 **評価:** -
 **タグ:** -
 **解決時間:** 44 秒
 **確認済み:** はい

 ## 解決策
 あなたの`Shop`現在の実装は壊れています`tact()`ロジックが破損しており、キュー分散ルールが実装されていません。 テストでは主に次の 3 つのことをチェックします。 

1. 有効な金庫は常に最短のキューを通じて新しい購入者を受け取ります。 
2. タクトは、空でないすべてのキューから 1 人の購入者にサービスを提供します。 
3. サービス提供後、有効なキューのサイズの差が最大 1 になるようにバイヤーが再分散されます。`IS_CLOSING`キューは縮小することしかできません。 

交換してください`CashBox`そして`Shop`次の実装を持つクラス。 

### キャッシュボックス.java```
package com.epam.rd.autocode.queue;

import java.util.Deque;
import java.util.LinkedList;

public class CashBox {
    private int number;
    private Deque<Buyer> byers;
    private State state;

    public enum State {
        ENABLED, DISABLED, IS_CLOSING
    }

    public CashBox(int number) {
        this.number = number;
        this.byers = new LinkedList<>();
        this.state = State.DISABLED;
    }

    public Deque<Buyer> getQueue() {
        return new LinkedList<>(byers);
    }

    public Buyer serveBuyer() {
        if (byers.isEmpty()) {
            return null;
        }
        return byers.pollFirst();
    }

    public boolean inState(State state) {
        return this.state == state;
    }

    public boolean notInState(State state) {
        return this.state != state;
    }

    public void setState(State state) {
        this.state = state;
    }

    public State getState() {
        return state;
    }

    public void addLast(Buyer byer) {
        byers.addLast(byer);
    }

    public Buyer removeLast() {
        return byers.pollLast();
    }

    int size() {
        return byers.size();
    }

    @Override
    public String toString() {
        return byers.toString();
    }
}
```### Shop.java```python
package com.epam.rd.autocode.queue;

import java.util.ArrayList;
import java.util.Deque;
import java.util.LinkedList;
import java.util.List;

import com.epam.rd.autocode.queue.CashBox.State;

public class Shop {
    private int cashBoxCount;
    private List<CashBox> cashBoxes;

    public Shop(int count) {
        cashBoxCount = count;
        cashBoxes = new ArrayList<>();

        for (int i = 0; i < count; i++) {
            cashBoxes.add(new CashBox(i));
        }
    }

    public int getCashBoxCount() {
        return cashBoxCount;
    }

    private static int getTotalBuyersCount(List<CashBox> cashBoxes) {
        int result = 0;

        for (CashBox box : cashBoxes) {
            result += box.size();
        }

        return result;
    }

    public void addBuyer(Buyer buyer) {
        CashBox best = null;

        for (CashBox box : cashBoxes) {
            if (box.inState(State.ENABLED)) {
                if (best == null || box.size() < best.size()) {
                    best = box;
                }
            }
        }

        if (best != null) {
            best.addLast(buyer);
        }
    }

    public void tact() {
        List<Buyer> served = new ArrayList<>();

        for (CashBox box : cashBoxes) {
            if (box.inState(State.ENABLED) || box.inState(State.IS_CLOSING)) {
                Buyer buyer = box.serveBuyer();

                if (buyer != null) {
                    served.add(buyer);
                }
            }
        }

        balance();

        for (Buyer buyer : served) {
            addBuyer(buyer);
        }

        balance();
    }

    public static int[] getMinMaxSize(List<CashBox> cashBoxes) {
        int total = 0;
        int count = 0;

        for (CashBox box : cashBoxes) {
            if (box.inState(State.ENABLED)) {
                total += box.size();
                count++;
            }
        }

        if (count == 0) {
            return new int[]{0, 0};
        }

        int min = total / count;
        int max = min;

        if (total % count != 0) {
            max++;
        }

        return new int[]{min, max};
    }

    public void setCashBoxState(int cashBoxNumber, State state) {
        cashBoxes.get(cashBoxNumber).setState(state);
        balance();
    }

    public CashBox getCashBox(int cashBoxNumber) {
        return cashBoxes.get(cashBoxNumber);
    }

    public void print() {
        for (CashBox box : cashBoxes) {
            System.out.println(box);
        }
    }

    private CashBox getMinEnabledCashBox() {
        CashBox result = null;

        for (CashBox box : cashBoxes) {
            if (box.inState(State.ENABLED)) {
                if (result == null || box.size() < result.size()) {
                    result = box;
                }
            }
        }

        return result;
    }

    private void balance() {
        List<Buyer> extra = new LinkedList<>();

        int enabled = 0;
        for (CashBox box : cashBoxes) {
            if (box.inState(State.ENABLED)) {
                enabled++;
            }
        }

        if (enabled == 0) {
            return;
        }

        int total = getTotalBuyersCount(cashBoxes);

        int min = total / enabled;
        int max = min + (total % enabled == 0 ? 0 : 1);

        for (CashBox box : cashBoxes) {
            if (box.inState(State.ENABLED)) {
                while (box.size() > max) {
                    extra.add(box.removeLast());
                }
            }
        }

        for (CashBox box : cashBoxes) {
            if (box.inState(State.ENABLED)) {
                while (box.size() < min && !extra.isEmpty()) {
                    box.addLast(extra.remove(0));
                }
            }
        }

        while (!extra.isEmpty()) {
            CashBox box = getMinEnabledCashBox();

            if (box == null) {
                break;
            }

            box.addLast(extra.remove(0));
        }
    }
}
```ここで修正された主な問題は次のとおりです。`addBuyer()`顧客は動作していないキューに入ることができないため、無効になっているボックスや閉じているボックスは無視されるようになりました。`tact()`最初に顧客にサービスを提供し、それからキューを再分配します。 サービスを提供する前に再配布すると順序が変更され、終了キューを縮小する必要がある場合は失敗します。`balance()`有効なキューのみを再配布することで、キューを閉じる動作を保持します。 含まれるテスト`IS_CLOSING`その区別に依存します。 

注意点が 1 つあります。貼り付けたテストでは、非常に具体的な再配布順序が想定されています。 グレーダーがサイズだけではなくキューの内容を正確にチェックする場合、残りの違いは、バランシング中に追加の購入者が前から取られるか後ろから取られるかによって生じる可能性があります。 上記のコードは、割り当てテキストからの一般的な解釈に従います。つまり、キューの末尾から削除し、ディフェクターを順番に追加します。
