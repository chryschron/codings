# 1回目
再帰で解く

```c++
class Solution {
public:
    int kthGrammar(int n, int k) {
        if (n == 1) {
            return 0;
        }

        int mid = 1 << (n - 2);
        if (k <= mid) {
            return kthGrammar(n - 1, k);
        } else {
            return kthGrammar(n - 1, k - mid) ^ 1;
        }
    }
};
```

## memo
- 時間計算量O(N)、空間計算量O(N)
	- `N <= 30`なので高々10nsオーダー、stack overflowも大丈夫
- なぜうまく行くのか、実際に列を作って確認する
	- 0
	- 01
	- 0110
	- 01101001
	- 0110100110010110
- 前半部分は一個前の列と同じ、後半はそのビット反転とわかる

# 2回目
再帰を使わないで解く

```c++
class Solution {
public:
    int kthGrammar(int n, int k) {
        int retval = 0;
        while (1 < n) {
            int mid = 1 << (n - 2);
            if (k > mid) {
                k -= mid;
                retval ^= 1;
            }
            n--;
        }
        return retval;
    }
};
```

## memo
- 空間計算量をO(1)にできる
	- スタックはいらない
- これ以上減らすこともできるが可読性が一気に落ちる

```c++
class Solution {
public:
    int kthGrammar(int n, int k) {
        int retval = 0;
        while (1 < n--) {
            int mid = 1 << (n - 1);
            if (k > mid) {
                k -= mid;
                retval ^= 1;
            }
        }
        return retval;
    }
};
```
