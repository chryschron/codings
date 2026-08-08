# 1回目
率直に解いた(TLE)

```c++
class Solution {
public:
    double myPow(double x, int n) {
        double result = 1.0;

        if (n < 0) {
            return myPow(1 / x, -n);
        }
        
        for (int i = 0; i < n; i++) {
            result *= x;
        }
        return result;
    }
};
```

## memo
- 時間計算量O(N)
	- `N <= 2^31(≈2*10^9)`ゆえ、大体1sオーダー
- 書いてから気づいたこと: `n = -2^31`のとき`-n`がoverflowする
	- 一応改善したもの(もちろんTLE)

```c++
class Solution {
public:
    double myPow(double x, int n) {
        double result = 1.0;
        int64_t n64 = n;
        if (n64 < 0) {
            x = 1 / x;
            n64 = -n64;
        }

        for (int64_t i = 0; i < n64; i++) {
            result *= x;
        }
        return result;
    }
};
```

# 2回目
高速冪乗法を使う

```c++
class Solution {
public:
    double myPow(double x, int n) {
        double result = 1.0;
        int64_t n64 = n;
        if (n64 < 0) {
            x = 1 / x;
            n64 = -n64;
        }

        double current_product = x;
        while (n64 > 0) {
            if (n64 & 1) {
                result *= current_product;
            }
            current_product *= current_product;
            n64 >>= 1;
        }
        return result;
    }
};
```

## memo
- 時間計算量O(logN)
	- 高々10nsオーダー
- 2で割った(`>>1`)余りで繰り返す
