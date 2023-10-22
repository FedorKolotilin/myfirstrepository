#include <algorithm>
#include <deque>
#include <iostream>
#include <stack>
#include <vector>

using namespace std;
const int kINF = 1e9 + 1;
const int kMod = 1 << 30;
vector<int> heap;
int heap_size = 0;

void HeapSwap(int idx1, int idx2) { swap(heap[idx1], heap[idx2]); }

void SiftUp(int idx) {
  if (idx != 1 && heap[idx] > heap[idx / 2]) {
    HeapSwap(idx, idx / 2);
    SiftUp(idx / 2);
  }
}

void SiftDown(int idx) {
  int ll = idx * 2;
  int rr = idx * 2 + 1;
  if (ll == heap_size) {
    if (heap[idx] < heap[ll]) {
      HeapSwap(idx, ll);
      SiftDown(ll);
    }
    return;
  }
  if (ll < heap_size) {
    if (heap[ll] < heap[rr]) {
      swap(ll, rr);
    }
    if (heap[idx] < heap[ll]) {
      HeapSwap(idx, ll);
      SiftDown(ll);
    }
  }
}

void Insert(int element) {
  ++heap_size;
  heap[heap_size] = element;
  SiftUp(heap_size);
}

void ExtractMax() {
  HeapSwap(1, heap_size);
  heap_size--;
  SiftDown(1);
}

int main() {
  cin.tie(nullptr);
  std::ios_base::sync_with_stdio(false);
  int nn;
  int kk;
  cin >> nn;
  cin >> kk;
  int ai;
  long long xx;
  long long yy;
  cin >> ai;
  cin >> xx;
  cin >> yy;
  heap.resize(kk + 2, kINF);
  for (int i = 0; i < nn; i++) {
    ai = (xx * ai + yy) % (kMod);
    Insert(ai);
    if (heap_size == kk + 1) {
      ExtractMax();
    }
  }
  vector<int> ans;
  for (int i = 0; i < kk; i++) {
    ans.push_back(heap[1]);
    ExtractMax();
  }
  for (int i = kk - 1; i >= 0; i--) {
    cout << ans[i] << " ";
  }
  cout << '\n';
}
