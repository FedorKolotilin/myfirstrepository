#include <algorithm>
#include <deque>
#include <iostream>
#include <set>
#include <stack>
#include <vector>

using namespace std;
const long long kINF = 1e18 + 1;
const long long kMod = 1 << 30;
vector<long long> heap;
long long heap_size = 0;

void HeapSwap(long long idx1, long long idx2) { swap(heap[idx1], heap[idx2]); }

void SiftUp(long long idx) {
  if (idx != 1 && heap[idx] > heap[idx / 2]) {
    HeapSwap(idx, idx / 2);
    SiftUp(idx / 2);
  }
}

void SiftDown(long long idx) {
  long long ll = idx * 2;
  long long rr = idx * 2 + 1;
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
  long long nn;
  long long kk;
  cin >> nn;
  cin >> kk;
  int ai;
  int xx;
  int yy;
  cin >> ai;
  cin >> xx;
  cin >> yy;
  heap.resize(kk + 1, kINF);
  for (long long i = 0; i < nn; i++) {
    ai = (xx * ai + yy) % (kMod);
    Insert(ai);
    if (heap_size == kk + 1) {
      ExtractMax();
    }
  }
  vector<long long> ans;
  for (int i = 0; i < kk; i++) {
    ans.push_back(heap[1]);
    ExtractMax();
  }
  for (int i = kk - 1; i >= 0; i--) {
    cout << ans[i] << " ";
  }
  cout << '\n';
}
