# 1回目
素朴に実装する

```c
struct ListNode* addTwoNumbers(struct ListNode* l1, struct ListNode* l2) {
    struct ListNode *dummy_head, *sum_runner;
    int carry;

    dummy_head = calloc(1, sizeof(struct ListNode));
    sum_runner = dummy_head;
    carry = 0;
    while (l1 || l2 || carry) {
        int partial_sum = carry;
        if (l1) {
            partial_sum += l1->val;
            l1 = l1->next;
        }
        if (l2) {
            partial_sum += l2->val;
            l2 = l2->next;
        }
        carry = partial_sum / 10;
        partial_sum %= 10;

        sum_runner = sum_runner->next = calloc(1, sizeof(struct ListNode));
        sum_runner->val = partial_sum;
    }
    
    sum_runner = dummy_head->next;
    free(dummy_head);
    return sum_runner;
}
```

## memo
- 計算量O(N)、メモリO(N)
	- Nはリスト長
- リスト長が最大100(=100桁)なので、リストを整数に変換する手法は良くない
	- 64bitの場合、最大でもlog2^64=19.2659、高々20桁までしか表せられない為
- while内の処理を楽にするため、dummyを利用した
- `l1` `l2`どっちかを破壊すればメモリO(1)を達成できそうだが、辞めた(最後に掲載)
	- どちらのリストが長いかを事前に計測する必要がある
		- さもなくば最悪O(N) (`l1`がNULL・`l2`がN個、`l1`を選んだとき)
# 2回目
LLMにメモリ確保ミス時の処理がないと怒られたので一応追加する

```c
struct ListNode* addTwoNumbers(struct ListNode* l1, struct ListNode* l2) {
    struct ListNode *dummy_head, *sum_runner;
    int carry;

    dummy_head = calloc(1, sizeof(struct ListNode));
    if (!dummy_head) return NULL;
    
    sum_runner = dummy_head;
    carry = 0;
    while (l1 || l2 || carry) {
        int partial_sum = carry;
        if (l1) {
            partial_sum += l1->val;
            l1 = l1->next;
        }
        if (l2) {
            partial_sum += l2->val;
            l2 = l2->next;
        }
        carry = partial_sum / 10;
        partial_sum %= 10;

        sum_runner = sum_runner->next = calloc(1, sizeof(struct ListNode));
        if (!sum_runner) return NULL;
        sum_runner->val = partial_sum;
    }
    
    sum_runner = dummy_head->next;
    free(dummy_head);
    return sum_runner;
}
```

破壊アプローチ
```c
struct ListNode* addTwoNumbers(struct ListNode* l1, struct ListNode* l2) {
    struct ListNode *sum_runner, *runner_prev, *ret_head;
    int carry, l1_len, l2_len;

    l1_len = l2_len = 0;
    for (struct ListNode *tmp = l1; tmp; tmp = tmp->next)
        l1_len++;
    for (struct ListNode *tmp = l2; tmp; tmp = tmp->next)
        l2_len++;
    
    sum_runner = ret_head = l1_len > l2_len ? l1 : l2;
    carry = 0;
    while (sum_runner) {
        int partial_sum = carry;
        if (l1) {
            partial_sum += l1->val;
            l1 = l1->next;
        }
        if (l2) {
            partial_sum += l2->val;
            l2 = l2->next;
        }
        carry = partial_sum / 10;
        partial_sum %= 10;

        sum_runner->val = partial_sum;
        runner_prev = sum_runner;
        sum_runner = sum_runner->next;
    }
    if (carry) {
        sum_runner = runner_prev->next = calloc(1, sizeof(struct ListNode));
        if (!sum_runner) return NULL;
        
        sum_runner->val = carry;
    }

    return ret_head;
}
```

## memo
- 破壊アプローチの時間計算量はO(N)、メモリO(1)。
