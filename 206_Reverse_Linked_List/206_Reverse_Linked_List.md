# 1回目
iterativeな方法を思いついたので実装する

```c++
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        std::stack<ListNode*> nodes;
        
        if (!head)
            return NULL;
        
        for (ListNode* runner = head; runner != nullptr; runner = runner->next)
            nodes.push(runner);
        
        ListNode* tail = nodes.top();
        while (nodes.size() > 1) {
            ListNode* runner = nodes.top();
            nodes.pop();
            runner->next = nodes.top();
        }
        nodes.top()->next = NULL;
        nodes.pop();
        return tail;
    }
};
```

## memo
- ノード数Nに対して時間計算量O(N)、メモリO(N)
	- ノード数が最大5000なのでメモリが十分あるなら`std::vector`で適当な大きさの領域(100‐1000?)reserveしておくのも手

# 2回目
recursiveな方法も思いついたので実装

```c++
class Solution {
private:
    ListNode* reverseList_(ListNode* prev, ListNode* cur) {
        if (cur == nullptr)
            return prev;

        ListNode* next = cur->next;
        cur->next = prev;
        return reverseList_(cur, next);
    }
public:
    ListNode* reverseList(ListNode* head) {
        return reverseList_(NULL, head);
    }
};
```

## memo
- 理論的な計算量は同じ
	- 関数呼び出しの分メモリが多く必要になる
