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

# 3回目
別の方法を考える。
1. すでに探査したノードを可変長配列で保持、ステップ毎に走査してユニークならappend
2. (LLM) 探査したノードの`next`を破壊する

1.はC言語だと実装が辛そうなので、他の人の実装を見てみる
- Pythonでは`set`オブジェクトを用いた実装がなされていた
    - `set`の定義: `PySet_Type` in https://github.com/python/cpython/blob/main/Objects/setobject.c
        - `PySet_MINSIZE = 8` in https://github.com/python/cpython/blob/main/Include/cpython/setobject.h
    - `.add()`コール時に呼ばれる関数: `set_add()` in https://github.com/python/cpython/blob/main/Modules/_testlimitedcapi/set.c
    - 最終的な処理: `set_add_key()`でハッシュ値計算、`set_add_entry_takeref()`でハッシュテーブル処理
        - いずれも https://github.com/python/cpython/blob/main/Objects/setobject.c

```python
class Solution:
    def hasCycle(self, head: Optional[ListNode]) -> bool:
        visited_nodes = set()
        current = head
        while current is not None:
            visited_nodes.add(current)
            current = current.next
            if current in visited_nodes:
                return True
        return False
```

- C++では`std::unordered_set`が相当する(`std::set`はRed-black Tree)

## memo
- 時間計算量: O(N)
    - `set`はHashTableを使用しているため、`add()`も`if in`探査も基本的にO(1)
    - 最悪全ハッシュが衝突するとき、内部で平均N/2回走査するのでO(N^2)
- メモリ: O(N)
    - ノード数に比例してメモリを使う

2.破壊アプローチ
- 時間計算量と空間計算量はFloydのそれと同じ
    - ただし、再利用できなくなる上`free()`することが事実上不可能
- 実用性は皆無

```c
bool hasCycle(struct ListNode *head) {
    struct ListNode *current, *next;

    current = head;
    while (current != NULL) {
        next = current->next;
        if (next == head)
            return true;
        current->next = head;
        current = next;
    }
    return false;
}
```

## memo
- LeetCode上ではheapエラーが発生(おそらくListNodeを解放するときに`head`のDouble Freeが発生するため)
    - 手元で一切メモリ解放しないテスト関数を書いたところ、動いてしまった
