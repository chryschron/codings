# 1回目
以前Youtubeで見た動画の中でFloyd's Cycle-Finding Algorithmが取り上げられていたのを覚えていたので、それを用いた。

```c
bool hasCycle(struct ListNode *head) {
    /* Floyd's Cycle-Finding Algorithm */
    struct ListNode *tortoise, *hare;

    if (head == NULL || head->next == NULL)
        return false;

    tortoise = hare = head;
    while (hare != NULL && hare->next != NULL) {
        tortoise = tortoise->next;
        hare = hare->next->next;
        if (tortoise == hare)
            return true;
    }
    return false;
}
```

## memo
- ループに突入した時hareはtortoiseよりも周回遅れとみなす
    - 各ステップ毎に両者の距離は1縮まる
    - 故にループが存在するならいずれ一致する
- 時間計算量: 要素数NとしてワーストO(N)
    - N個のループがあった場合がNステップで最悪?
        - 多分合ってる
    - (LLMより): m個直進+n個ループの場合はtortoiseがループ突入までmステップかかり、その時の両者間距離は最大n-1なので合流までのステップ数はN-1
- メモリ: O(1)
    - 定数個のメモリしか使っていない

# 2回目
はじめのNULLチェックはwhile文の中身と本質的に同じなので削って良い

```c
bool hasCycle(struct ListNode *head) {
    /* Floyd's Cycle-Finding Algorithm */
    struct ListNode *tortoise, *hare;

    tortoise = hare = head;
    while (hare != NULL && hare->next != NULL) {
        tortoise = tortoise->next;
        hare = hare->next->next;
        if (tortoise == hare)
            return true;
    }
    return false;
}
```

## memo
- Assemblyを見てみる
    - メモリは64bit * 3使用。うち1つは引数用
- NULLは`stddef.h`で定義されている
    - `#define NULL  ((void *) 0)`
