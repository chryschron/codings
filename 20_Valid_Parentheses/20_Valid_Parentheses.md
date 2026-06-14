# 1回目
文脈自由言語なのでPDAで解ける。CにはstackがないのでC++の`std::stack`で実装した。

```c++
class Solution {
public:
    bool isValid(string s) {
        std::stack<char> open_brackets;
        open_brackets.push('$');

        for (const char c : s) {
            switch (c) {
                case '(':
                case '{':
                case '[':
                    open_brackets.push(c);
                    continue;
                case ')':
                    if (c == ')' && open_brackets.top() != '(')
                        return false;
                case '}':
                    if (c == '}' && open_brackets.top() != '{')
                        return false;
                case ']':
                    if (c == ']' && open_brackets.top() != '[')
                        return false;
                    open_brackets.pop();
                    continue;
                default:
                    return false;
            }
        }
        return open_brackets.top() == '$';
    }
};
```

## memo
- `std::stack`にて、`empty() == true`の時の`.top()`はUB
	- 今回は`$`を末尾に入れて対策した
	- 一々比較時に空か確認するのは辛い
- `s.length() == N`として計算量O(N)
- `std::stack`について
	- デフォルトの実装は`std::deque` (double-ended queue)で、`std::vector`(動的配列) `std::list`(双方向連結リスト)に変更可能
		- https://en.cppreference.com/cpp/container/deque
		- https://en.cppreference.com/cpp/container/vector
		- https://en.cppreference.com/cpp/container/list
	- アクセス・末端追加がO(1)で済む一方、メモリが多分最低4096 byte必要
		- >  (e.g. 8 times the object size on 64-bit libstdc++; 16 times the object size or 4096 bytes, whichever is larger, on 64-bit libc++).
	- また、連続的に要素が配置されない
		- `std::list`も同様。`std::vector`は連続配置
	- とはいえmaxの文字数が10^4なので、メモリ移動が起きないのは強み
	- 今回はdequeを採用するのが良さそう？(わからない)

# 2回目
マジックナンバーを消し、拡張性を高めた

```c++
class Solution {
public:
    bool isValid(string s) {
        const string open  = "({[";
        const string close = ")}]";
        const char stack_bottom = '$';
        std::stack<char> open_brackets;
        open_brackets.push(stack_bottom);

        for (const char c : s) {
            if (open.find_first_of(c) != string::npos) {
                open_brackets.push(c);
            } else {
                auto opos = open.find_first_of(open_brackets.top());
                auto cpos = close.find_first_of(c);
                if (opos == string::npos || cpos == string::npos)
                    return false;
                else if (opos != cpos)
                    return false;
                else
                    open_brackets.pop();
            }
        }
        return open_brackets.top() == stack_bottom;
    }
};
```

- `std::string`
	- https://en.cppreference.com/cpp/string/basic_string
- (余談)マルチバイト文字の場合は`wstring`と`wchar_t`を使うと良さそう?
