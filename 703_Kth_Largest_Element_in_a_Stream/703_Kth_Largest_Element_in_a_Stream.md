# 1回目
`std::priority_queue`を使って小さい順に並べ、トップk個保持すればいける

```c++
class KthLargest {
private:
    int k_ = 0;
    std::priority_queue<int, std::vector<int>, std::greater<int>> scores;
public:
    KthLargest(int k, vector<int>& nums) {
        k_ = k;
        for (int num : nums)
            scores.push(num);
        while (scores.size() > k_)
            scores.pop();
    }
    
    int add(int val) {
        scores.push(val);
        while (scores.size() > k_)
            scores.pop();
        return scores.top();
    }
};
```

- Priority Queueについて
	- push/pop時計算量はO(log N)
		- 2分木なら木の高さはlog_2 N、最大でも入れ替えにかかるステップ数は木の高さ分だけ
	- 最大値取得はrootを取ればいいのでO(1)
	- メモリはO(N)
		- 木と単純な配列を関連付けて保管するため

- データ量が多くてメモリに配置しきれないときはB木/B+/B*が良い?
	- ディスク読み出し回数は木の高さだけ発生する
- `val`の範囲がもっと狭いならbucket queueも使えそう
- 理論上更に優れたものにフィボナッチヒープなるものがある
	- よく分からなかったので詳細は後で調べる
