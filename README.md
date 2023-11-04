#include <algorithm>
#include <iostream>
#include <cstring>

using std::cin;
using std::cout;
using std::memcpy;
using std::memset;
class String {
 private:
  size_t sz = 0;
  size_t cap;
  char* arr;

  String(size_t size) : sz(size), cap(size + 1), arr(new char[size + 1]) {
    arr[sz] = '\0';
  }

  void resize(size_t new_size) {
    char* new_arr = new char[new_size];
    memcpy(new_arr, arr, cap);
    cap = new_size;
    delete[] arr;
    arr = new_arr;
  }

 public:
  String() : String(static_cast<size_t>(0)) {
  }

  String(size_t count, char c) : String(count) {
    memset(arr, c, count);
  }

  String(const char* string) : String(strlen(string)) {
    memcpy(arr, string, sz);
  }

  String(const String& string) : String(string.sz) {
    memcpy(arr, string.arr, sz);
  }

  String& operator=(String other) {
    swap(other);
    return *this;
  }

  char operator[](size_t pos) const {
    return arr[pos];
  }

  char& operator[](size_t pos) {
    return arr[pos];
  }


  bool equals(const String& rhs) const {
    if (sz == rhs.sz) {
      for (size_t i = 0; i < sz; i++) {
        if (arr[i] != rhs.arr[i]) {
          return false;
        }
      }
      return true;
    }
    return false;
  }


  String& operator+=(const String& rhs) {
    if (cap <= sz + rhs.sz) {
      resize(2 * (sz + rhs.sz + 1));
    }
    memcpy(arr + sz, rhs.arr, rhs.sz);
    sz += rhs.sz;
    return *this;
  }


  String& operator+=(const char ch) {
    push_back(ch);
    return *this;
  }

  void swap(String& other) {
    std::swap(arr, other.arr);
    std::swap(sz, other.sz);
    std::swap(cap, other.cap);
  }

  size_t length() const {
    return sz;
  }

  size_t size() const {
    return sz;
  }

  size_t capacity() const {
    return cap;
  }

  char front() const {
    return arr[0];
  }

  char back() const {
    return arr[sz - 1];
  }

  char& front() {
    return arr[0];
  }

  char& back() {
    return arr[sz - 1];
  }

  void push_back(char ch) {
    if (cap <= sz + 1) {
      resize(2 * sz + 2);
      push_back(ch);
      return;
    }
    if (cap <= sz + 1) {
    }
    arr[sz] = ch;
    arr[sz + 1] = '\0';
    sz += 1;
  }

  void pop_back() {
    arr[sz - 1] = '\0';
    sz--;
  }

  String substr(size_t start, size_t count) const {
    String substring(count, ' ');
    memcpy(substring.arr, arr + start, count);
    substring[count] = '\0';
    return substring;
  }


  size_t find(const String& sub) const {
    for (size_t i = 0; i <= (sz - sub.size() + 1); i++) {
      bool eq = true;
      for (size_t j = 0; j < sub.size(); j++) {
        if (arr[i + j] != sub[j]) {
          eq = false;
        }
      }
      if (eq) {
        return i;
      }
    }
    return length();
  }


  size_t rfind(const String& sub) const {
    for (size_t i = (sz - sub.size() + 1) + 1; i > 0; i--) {
      bool eq = true;
      for (size_t j = 0; j < sub.size(); j++) {
        if (arr[i - 1 + j] != sub[j]) {
          eq = false;
        }
      }
      if (eq) {
        return (i - 1);
      }
    }
    return length();
  }

  bool empty() {
    return sz == 0;
  }

  void clear() {
    sz = 0;
    arr[0] = '\0';
  }


  void shrink_to_fit() {
    resize(size());
  }

  ~String() {
    sz = 0;
    cap = 0;
    delete[] arr;
  }

  char* data() {
    return arr;
  }

  char* data() const {
    return arr;
  }
};

bool is_end(char ch) {
  switch (ch) {
    case ' ':
      return true;
    case '\n':
      return true;
    case '\t':
      return true;
    default:
      return false;
  }
}

std::istream& operator>>(std::istream& is, String& string) {
  string.clear();
  char ch = is.get();
  while (is && is_end(ch)) {
    ch = is.get();
  }
  while (is && !is_end(ch)) {
    string.push_back(ch);
    ch = is.get();
  }
  return is;
}

std::ostream& operator<<(std::ostream& os, const String& string) {
  for (size_t i = 0; i < string.size(); i++) {
    os << string[i];
  }
  return os;
}

String operator+(String& str1, String& str2) {
  String sum_string;
  sum_string += str1;
  sum_string += str2;
  return sum_string;
}


String operator+(char str1, String& str2) {
  String sum_string;
  sum_string += str1;
  sum_string += str2;
  return sum_string;
}


String operator+(String str1, char str2) {
  String sum_string;
  sum_string += str1;
  sum_string += str2;
  return sum_string;
}

bool operator<(const String& lhs, const String& rhs) {
  for (size_t i = 0; i < std::min(lhs.size(), rhs.size()) + 1; i++) {
    if (rhs[i] < lhs[i]) {
      return true;
    }
  }
  return false;
}

bool operator>(const String& lhs, const String& rhs) {
  return rhs < lhs;
}

bool operator<=(const String& lhs, const String& rhs) {
  return !(rhs < lhs);
}

bool operator>=(const String& lhs, const String& rhs) {
  return !(lhs < rhs);
}

bool operator==(const String& lhs, const String& rhs) {
  if (lhs.size() == rhs.size()) {
    for (size_t i = 0; i <= lhs.size(); i++) {
      if (lhs[i] != lhs[i]) {
        return false;
      }
    }
    return true;
  }
  return false;
}

bool operator!=(const String& lhs, const String& rhs) {
  return !(rhs == lhs);
}
