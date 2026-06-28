# 1回目
各メアドごとに実際の送信先を取り出して`unordered_set`で持てば良さそう

```c++
class Solution {
public:
    int numUniqueEmails(vector<string>& emails) {
        unordered_set<string> unique_email_set;
        for (auto& email : emails) {
            optional<string> actual_email = getActualEmailAddr(email);
            if (!actual_email.has_value()) {
                continue;
            }
            unique_email_set.insert(actual_email.value());
        }

        return unique_email_set.size();
    }
private:
    optional<string> getActualEmailAddr(string& email) {
        size_t at_pos = email.find('@');
        if (at_pos == string::npos) {
            return nullopt;
        }

        string at_domain = email.substr(at_pos);
        if (at_domain.find("@.") != string::npos) {
            return nullopt;
        }

        string local = email.substr(0, at_pos);
        
        size_t plus_pos = local.find_first_of('+');
        string actual_local = (plus_pos != string::npos) ? local.substr(0, plus_pos) : local;
        erase(actual_local, '.');

        return actual_local + at_domain;
    }
};
```

## memo
- `N = emails.length, L = max(emails[].length)`として計算量O(NL)、メモリO(NL)
	- どっちも<=100なので愚直にやっても処理はすぐ済む

# 2回目
`string_view`を使って余分なコピーをなくそう

```c++
class Solution {
public:
    int numUniqueEmails(vector<string>& emails) {
        unordered_set<string> unique_email_set;
        for (auto& email : emails) {
            if (const auto actual_email = getActualEmailAddr(email); actual_email.has_value()) {
                unique_email_set.insert(actual_email.value());
            }
        }

        return unique_email_set.size();
    }
private:
    optional<string> getActualEmailAddr(string_view email) const {
        size_t at_pos = email.find('@');
        if (at_pos == string::npos) {
            return nullopt;
        }

        string_view at_domain = email.substr(at_pos);
        if (at_domain.find("@.") != string_view::npos) {
            return nullopt;
        }

        string_view local = email.substr(0, at_pos);
        
        size_t plus_pos = local.find_first_of('+');
        string actual_local = (plus_pos != string::npos) ? string{local.substr(0, plus_pos)} : string{local};
        erase(actual_local, '.');

        return actual_local + string{at_domain};
    }
};
```

## memo
- `string_view`: 文字列開始位置のポインタと列長だけを持つ参照。実体のコピーが発生しないため高速。

# 3回目

```c++
class Solution {
public:
    int numUniqueEmails(vector<string>& emails) {
        unordered_set<string> unique_email_set;
        for (const auto& email : emails) {
            if (auto actual_email = getActualEmailAddr(email); actual_email.has_value()) {
                unique_email_set.insert(move(*actual_email));
            }
        }
        return unique_email_set.size();
    }
private:
    optional<string> getActualEmailAddr(string_view email) {
        if (email.find("@.") != string_view::npos) {
            return nullopt;
        }
        
        size_t at_pos = email.find('@');
        if (at_pos == string_view::npos) {
            return nullopt;
        }
        string_view at_domain = email.substr(at_pos);
        
        string_view local = email.substr(0, at_pos);
        size_t plus_pos = local.find_first_of('+');
        string actual_local = (plus_pos != string_view::npos) ? string{local.substr(0, plus_pos)} : string{local};
        erase(actual_local, '.');

        return move(actual_local.append(at_domain));
    }
};
```
