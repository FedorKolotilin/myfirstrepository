#include <algorithm>
#include <iostream>
#include <cstring>
#include <cassert>

using std::cin;
using std::cout;
using std::memcpy;
using std::memset;
class String {
 private:
  size_t sz = 0;
  size_t cap;
  char* arr = nullptr;

  String(size_t size) :  cap(size + 1), arr(new char[size + 1]) {
    std::cerr << "size constr" << std::endl;
    arr[0] = '\0';
  }

  void resize(size_t new_size) {
    std::cerr << "resize" << std::endl;
    char* new_arr = new char[new_size];
    memcpy(new_arr, arr, cap);
    std::swap(arr, new_arr);
    cap = new_size;
    delete[] new_arr;
  }

 public:
  String() {
    std::cerr << "default constr" << std::endl;
    cap = 1;
    arr = new char[1];
    arr[0] = '\0';
  }

  String(size_t count, char c) : String(count) {
    std::cerr << "char cnostr" << std::endl;
    memset(arr, c, count);
    sz = count;
    arr[sz] = '\0';
  }

  String(const char* string) : String(strlen(string)) {
    std::cerr << "char* constr" << std::endl;
    sz = cap - 1;
    memcpy(arr, string, sz);
    arr[sz] = '\0';
  }

  String& operator=(String other) {
    std::cerr << "=" << std::endl;
    if (this != &other) {
      swap(other);
    }
    return *this;
  }

  char operator[](size_t pos) const {
    std::cerr << "const []" << std::endl;
    return arr[pos];
  }

  char& operator[](size_t pos) {
    std::cerr << "[]" << std::endl;
    return arr[pos];
  }

  bool equals(const String& rhs) const {
    std::cerr << "equals" << std::endl;
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

  String& operator+=(const String rhs) {
    std::cerr << "+= string" << std::endl;
    char* copy;
    if(arr == rhs.arr) {
      copy = new char[cap];
      memcpy(copy, arr, sz + 1);
      arr = copy;
    }
    if (cap <= sz + rhs.sz) {
      resize(sz + rhs.sz + 1);
    }
    memcpy(arr + sz, rhs.arr, rhs.sz);
    sz += rhs.sz;
    return *this;
  }

  String& operator+=(const char ch) {
    std::cerr << "+= char" << std::endl;
    push_back(ch);
    return *this;
  }

  void swap(String& other) {
    std::cerr << "swap " << std::endl;
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
    return sz;
  }

  char front() const {
    std::cerr << "front const " << std::endl;
    return arr[0];
  }

  char back() const {
    std::cerr << "back const " << std::endl;
    return arr[sz - 1];
  }

  char& front() {
    std::cerr << "front " << std::endl;
    return arr[0];
  }

  char& back() {
    std::cerr << "back " << std::endl;
    return arr[sz - 1];
  }

  void push_back(char ch) {
    std::cerr << "push_back" << std::endl;
    if (cap == sz + 1) {
      resize(2 * sz + 2);
      push_back(ch);
      return;
    }
    arr[sz] = ch;
    arr[sz + 1] = ch;
    sz += 1;
  }

  void pop_back() {
    std::cerr << "pop_back " << std::endl;
    arr[sz - 1] = '\0';
    sz--;
  }

  String substr(size_t start, size_t count) const {
    std::cerr << "substr " << std::endl;
    String substring(count);
    memcpy(substring.arr, arr + start, count);
    substring[count] = '\0';
    return substring;
  }

  size_t find(const String& sub) const {
    std::cerr << "find " << std::endl;
    for (size_t i = 0; i <= (sz - sub.size() + 1); i++) {
      if (sub.equals(substr(i, sub.size()))) {
        return i;
      }
    }
    return length();
  }

  size_t rfind(const String& sub) const {
    std::cerr << "rfind " << std::endl;
    for (size_t i = (sz - sub.size() + 1) + 1; i > 0; i--) {
      if (sub.equals(substr(i + 1, sub.size()))) {
        return i + 1;
      }
    }
    return length();
  }

  bool empty() {
    return sz == 0;
  }

  void clear() {
    sz = 0;
  }

  void shrink_to_fit() {
    std::cerr << "shrink_t_f " << std::endl;
    resize(size() + 1);
  }

  ~String() {
    std::cerr << "destuct " << std::endl;
    delete[] arr;
  }

  char* data() {
    std::cerr << "data const " << std::endl;
    return arr;
  }

  char* data() const {
    std::cerr << "data" << std::endl;
    return arr;
  }
  char Get(int idx) const {
    return arr[idx];
  }
};

String operator+(String str1, String str2) {
  std::cerr << "+1" << std::endl;
  String sum_string;
  sum_string += str1;
  sum_string += str2;
  return sum_string;
}

String operator+(char str1, String str2) {
  std::cerr << "+2" << std::endl;
  String sum_string;
  sum_string += str1;
  sum_string += str2;
  return sum_string;
}

String operator+(String str1, char str2) {
  std::cerr << "+3 " << std::endl;
  String sum_string;
  sum_string += str1;
  sum_string += str2;
  return sum_string;
}

std::ostream& operator<<(std::ostream& os, const String& string) {
  std::cerr << "+4" << std::endl;
  for (size_t i = 0; i < string.size(); i++) {
    cout << string[i];
  }
  return os;
}

bool is_end(char ch) {
  switch (ch) {
    case ' ':
      return true;
    case '\n':
      return true;
    case '\r':
      return true;
    case '\t':
      return true;
    default:
      return false;
  }
}
bool operator<(const String& lhs, const String& rhs) {
  std::cerr << "<" << std::endl;
  for (size_t i = 0; i < std::min(lhs.size(), rhs.size()) + 1; i++) {
    if (rhs.Get(i) < lhs.Get(i)) {
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

std::istream& operator>>(std::istream& is, String& string) {
  std::cerr << ">>" << std::endl;
  char ch = is.get();
  while(std::isspace(ch)) {
    ch = is.get();
  }
  while (!std::isspace(ch)) {
    string.push_back(ch);
    ch = is.get();
  }
  return is;
}

bool operator==(const String& lhs, const String& rhs) {
  std::cerr << "==" << std::endl;
  if (lhs.size() == rhs.size()) {
    for (size_t i = 0; i <= lhs.size(); i++) {
      if (lhs.Get(i) != lhs.Get(i)) {
        return false;
      }
    }
    return true;
  }
  return false;
}

bool operator!=(const String& lhs, const String& rhs) {
  std::cerr << "!=" << std::endl;
  return !(rhs == lhs);
}
