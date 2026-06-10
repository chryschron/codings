# 1回目
2つのポインタで実現出来そうとの直感のもと、素直に実装した。

2ポインタ法という名前があることを後から知った。

```c
struct ListNode* deleteDuplicates(struct ListNode* head) {
    struct ListNode *base, *pioneer;

    base = pioneer = head;
    while (base) {
        while (pioneer && base->val == pioneer->val)
            pioneer = pioneer->next;
        base = base->next = pioneer;
    }
    return head;
}
```

## memo
- 計算量: O(N)
	- frontierが要素Nすべてを探索する
	- ワーストは重複が無い場合、同じ値をひたすら代入し直す羽目になるから
- メモリ: O(1)

# 2回目
メモリ管理を怠っていたので追加する。

```c
struct ListNode* deleteDuplicates(struct ListNode* head) {
    struct ListNode *base, *pioneer;

    base = pioneer = head;
    while (base) {
        pioneer = base->next;
        while (pioneer && base->val == pioneer->val) {
            struct ListNode *dup = pioneer;
            pioneer = pioneer->next;
            free(dup);
        }
        base = base->next = pioneer;
    }
    return head;
}
```

## memo
- `while (pioneer && base->val == pioneer->val)`
	- 2つ目は値の比較、1つ目は内側で`pioneer`を後ろに進める分のNULLチェック
