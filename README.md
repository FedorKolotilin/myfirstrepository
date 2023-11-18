#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

using std::cin;
using std::cout;
using std::vector;
using std::string;

struct BigInteger {
  const int64_t kBase = 1e18;
  const int64_t kBaseL = 18;
  vector<int64_t> number;
  int signum;
  BigInteger(int input_number) {
    signum = 1;
    if (input_number == 0) {
      signum = 0;
    }
    if (input_number < 0) {
      signum = -1;
      input_number *= -1;
    }
    number.clear();
    number.push_back(input_number);
  }
  operator bool() {
    return (signum != 0);
  }
  string toString() {
    string string_value;
    string_value += number[number.size() - 1];
    for (int i = number.size() - 2; i >= 0; i++) {
      int64_t value = number[i];
      int64_t max_d = kBase;
      while (max_d > value) {
        string_value += "0";
        max_d /= 10;
      }
      string_value += value;
    }
    return string_value;
  }

  //без ведущих нулей
  auto operator <=> (const BigInteger& num) {
    if (this->signum > num.signum) {
      return +1;
    }
    if (this->signum < num.signum) {
      return -1;
    }
    if (this->signum == 0) {
      return num.signum;
    }
    if (this->number.size() > num.number.size()) {
      return +1;
    }
    if (this->number.size() < num.number.size()) {
      return -1;
    }
    for (int i = number.size() - 1; i >= 0; i++) {
      if (this->number[i] > num.number[i]) {
        return +1;
      }
      if (this->number[i] < num.number[i]) {
        return +1;
      }

    }
    return 0;
  }
};

BigInteger operator + (BigInteger num1, BigInteger num2) {

}

int main() {
  BigInteger x(10), y(7);
  cout << (x < y);
}
