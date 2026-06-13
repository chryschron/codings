# 1回目
今回は重複を全て消す。NULLチェック等の例外想定が甘く何度も弾かれた。

```c
struct ListNode* deleteDuplicates(struct ListNode* head) {
    struct ListNode *new_head, *base, *pioneer;

    if (!head)
        return head;

    new_head = head;
    while (new_head && new_head->next && new_head->val == new_head->next->val) {
        while (new_head->next && new_head->val == new_head->next->val)
            new_head = new_head->next;
        new_head = new_head->next;
    }

    base = new_head;
    pioneer = base ? base->next : NULL;
    while (pioneer) {
        if (!pioneer->next) {
            base = base->next = pioneer;
            break;
        } else if (pioneer->val != pioneer->next->val) {
            base = base->next = pioneer;
            pioneer = pioneer->next;
            continue;
        }
        while (pioneer->next && pioneer->val == pioneer->next->val)
            pioneer = pioneer->next;
        pioneer = pioneer->next;
    }
    if (base)
        base->next = NULL;
    return new_head;
}
```

## memo
- 時間計算量はO(N)、メモリはO(1)

# 2回目
LLMからdummyノードを先頭に挟めば見通しが良くなるとのヒントを貰った。
他の方の回答を拝見したところ皆dummyを使っていたので、実装してみる。

```c
struct ListNode* deleteDuplicates(struct ListNode* head) {
    struct ListNode dummy_head;
    struct ListNode *runner;

    dummy_head.next = head;
    runner = &dummy_head;
    while (runner) {
        if (runner->next && runner->next->next && runner->next->val == runner->next->next->val) {
            int dup_val = runner->next->val;
            while (runner->next && runner->next->val == dup_val)
                runner->next = runner->next->next;
        } else {
            runner = runner->next;
        }
    }
    return dummy_head.next;
}
```

## memo
- なぜdummyが必要なのか
	- `head`が動く可能性がある。
		- 1回目では`head`と`base`の移動で場合分けしている
	- `dummy_head`を挟むことでこれを一つに集約できる
- `base` `pioneer`形式が良くないのは`dummy_head`が値を持っていないのと、`base`判定が煩雑になるから
	- 83は単に新しい値なら問答無用で`base`になる
	- 今回は新しい値が連続していないかの判定もしないといけない
		- 加えてNULLチェック
- このコードを書いているとき、83の別解を思いついた

問題: [83. Remove Duplicates from Sorted List](https://leetcode.com/problems/remove-duplicates-from-sorted-list/description/)
```c
struct ListNode* deleteDuplicates(struct ListNode* head) {
    struct ListNode *runner;

    runner = head;
    while (runner) {
        if (runner->next && runner->val == runner->next->val)
            runner->next = runner->next->next;
        else
            runner = runner->next;
    
    }
    return head;
}
```

- こっちもきれいだけれど、(83については)`base` `pioneer`の方が直感的と感じる
	- とはいえ同じ発想に纏められるのは都合が良い
