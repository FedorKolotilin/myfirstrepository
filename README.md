#include <iostream>

using std::cin;
using std::cout;
char** storage;
int rl_size = 4;
int im_size = 0;

char* Copy(const char* string, int size) {
  char* s_copy = new char[size];
  for (int i = 0; i < size; i++) {
    s_copy[i] = string[i];
  }
  return s_copy;
}

void Resize() {
  rl_size *= 2;
  char** storage_cpy = new char*[rl_size];
  for (int i = 0; i < im_size; i++) {
    storage_cpy[i] = storage[i];
  }
  delete[] storage;
  storage = storage_cpy;
}

void Push(char* element) {
  if (im_size == rl_size) {
    Resize();
  }
  storage[im_size++] = Copy(element, 256);
}

void Initialize() {
  im_size = 0;
  rl_size = 4;
  char* e = new char[256];
  e[0] = 'x';
  e[1] = 'y';
  e[2] = 'z';
  e[3] = ' ';
  storage = new char*[4];
  storage[0] = Copy(e, 256);
  storage[1] = Copy(e, 256);
  storage[2] = Copy(e, 256);
  storage[3] = Copy(e, 256);
  delete[] e;
}

void Clear() {
  delete[] storage;
  Initialize();
  cout << "ok" << '\n';
}

void Pop() {
  if (im_size == 0) {
    cout << "error" << '\n';
  } else {
    cout << storage[--im_size] << '\n';
  }
}

int main() {
  Initialize();
  char inp[10];
  cin >> inp;
  while (inp[0] != 'e') {
    switch (inp[0]) {
      case 'p':
        if (inp[1] == 'u') {
          char value[256];
          cin >> value;
          Push(value);
          cout << "ok" << '\n';
        } else {
          Pop();
        }
        break;
      case 'b':
        cout << storage[im_size - 1] << '\n';
        break;
      case 's':
        cout << im_size << '\n';
        break;
      case 'c':
        Clear();
        break;
    }
    cin >> inp;
  }
  Clear();
  cout << "bye";
}
