# 1回目
Floyd's Cycle Finding Algorithmを使えば解ける。

```c
struct ListNode *detectCycle(struct ListNode *head) {
    struct ListNode *tortoise, *hare;

    tortoise = hare = head;
    while (hare != NULL && hare->next != NULL) {
        tortoise = tortoise->next;
        hare = hare->next->next;
        if (tortoise == hare)
            goto loop_found;
    }
    return NULL;

loop_found:
    hare = head;
    while (tortoise != hare) {
        tortoise = tortoise->next;
        hare = hare->next;
    }
    return tortoise;
}
```

## memo
- スタート地点からループ開始地点までの距離を`L`、ループ開始地点からtortoise、hareの一致地点までの距離を`M`、`N=(ループの大きさ-M)`と置く。
	- 両者が一致したとき、tortoiseは`L+M`動いている
	- hareは`2(L+M)=L+k(M+N) (k=1,2,3...)`動いている
	- hareの式を変形すると`L=(k-1)(M+N)+N`
	- 故に、両者にNを足すとそれぞれ`2(L+M)+N=L+(k+1)(M+N)`、`L+M+N`
	- 両者は`mod M+N`について`L`で合同=上の関数は開始地点を返す

# 2回目
`goto`を嫌う人がいそうなので無いバージョンを作った

```c
static struct ListNode *locateCycleBeginning(struct ListNode *p1, struct ListNode *p2) {
    while (p1 != p2) {
        p1 = p1->next;
        p2 = p2->next;
    }
    return p1;
}

struct ListNode *detectCycle(struct ListNode *head) {
    struct ListNode *tortoise, *hare;

    tortoise = hare = head;
    while (hare != NULL && hare->next != NULL) {
        tortoise = tortoise->next;
        hare = hare->next->next;
        if (tortoise == hare)
            return locateLoopBeginning(head, tortoise);
    }
    return NULL;
}
```

あとは141でやった素朴な実装

```c++
class Solution {
public:
    ListNode *detectCycle(ListNode *head) {
       std::unordered_set<ListNode *> visited_nodes;
       ListNode *current = head;
       while (current != nullptr) {
        visited_nodes.insert(current);
        current = current->next;
        if (visited_nodes.contains(current))
            return current;
       }
       return nullptr; 
    }
};
```

## memo
- `visited_nodes.contains()`に`nullptr`が入るリスクがあるが大丈夫なのか
	- `std::hash` https://ja.cppreference.com/cpp/utility/hash
		> テンプレート std::hash を宣言する標準ライブラリの各々のヘッダは std::nullptr_t、すべての cv 修飾された算術型 (あらゆる拡張整数型を含む)、すべての列挙型およびすべてのポインタ型に対する std::hash の有効な特殊化を提供します。 (C++17以上) 
	- とのことなので大丈夫そう (`contains()`はC++20なので)
