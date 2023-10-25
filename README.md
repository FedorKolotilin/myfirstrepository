#include <algorithm>
#include <deque>
#include <iostream>
#include <set>
#include <stack>
#include <vector>

using namespace std;
struct MyHeap {
  const long long kINF = 1e18 + 1;
  const long long kLEN = 5 * 1e5 + 10;
  vector<pair<long long, long long>> heap;
  vector<long long> q_info;
  long long size = 0;

  bool (*compare)(long long p1, long long p2);

  MyHeap(bool (*compare)(long long, long long)) : compare(compare) {
    heap.assign(kLEN + 1, {(compare(kINF, -kINF) ? -kINF : kINF), 0});
    q_info.assign(2 * kLEN + 1, 0);
  }

  void HeapSwap(long long idx1, long long idx2) {
    q_info[heap[idx1].second] = idx2;
    q_info[heap[idx2].second] = idx1;
    swap(heap[idx1], heap[idx2]);
  }

  void SiftUp(long long idx) {
    if (idx != 1 && compare(heap[idx].first, heap[idx / 2].first)) {
      HeapSwap(idx, idx / 2);
      SiftUp(idx / 2);
    }
  }

  void SiftDown(long long idx) {
    long long ll = idx * 2;
    long long rr = idx * 2 + 1;
    if (ll == size) {
      if (!compare(heap[idx].first, heap[ll].first)) {
        HeapSwap(idx, ll);
        SiftDown(ll);
      }
      return;
    }
    if (ll < size) {
      if (compare(heap[rr].first, heap[ll].first)) {
        swap(ll, rr);
      }
      if (!compare(heap[idx].first, heap[ll].first)) {
        HeapSwap(idx, ll);
        SiftDown(ll);
      }
    }
  }

  void Insert(long long value, long long op_idx) {
    ++size;
    q_info[op_idx] = size;
    heap[size] = {value, op_idx};
    SiftUp(size);
  }

  void Extract() {
    HeapSwap(1, size);
    size--;
    SiftDown(1);
  }

  long long GetMinValue() { return heap[1].first; }

  long long GetMinIdx() { return heap[1].second; }

  void DecreaseKey(long long key, long long value) {
    heap[q_info[key]].first -= value;
    SiftUp(q_info[key]);
  }

  void IncreaseKey(long long key, long long value) {
    heap[q_info[key]].first += value;
    SiftDown(q_info[key]);
  }
};
struct MinMaxHeap {
  static bool (MoreComp)(long long p1, long long p2) { return p1 > p2; }

  static bool (LessComp)(long long p1, long long p2) { return p1 < p2; }

  MyHeap min_heap = MyHeap(LessComp);
  MyHeap max_heap = MyHeap(MoreComp);

  void Insert(long long element, long long op_idx) {
    min_heap.Insert(element, op_idx);
    max_heap.Insert(element, op_idx);
  }

  void ExtractMax() {
    long long idx = max_heap.GetMinIdx();
    max_heap.Extract();
    min_heap.DecreaseKey(idx, max_heap.kINF);
    min_heap.Extract();
  }

  void ExtractMin() {
    long long idx = min_heap.GetMinIdx();
    min_heap.Extract();
    max_heap.DecreaseKey(idx, -max_heap.kINF);
    max_heap.Extract();
  }

  long long GetSize() { return min_heap.size; }

  long long GetMin() { return min_heap.GetMinValue(); }

  long long GetMax() { return max_heap.GetMinValue(); }

  void Clear() {
    max_heap.size = 0;
    min_heap.size = 0;
  }

  void DecreaseKey(long long key, long long value) {
    min_heap.heap[min_heap.q_info[key]].first -= value;
    min_heap.SiftUp(min_heap.q_info[key]);
    max_heap.heap[max_heap.q_info[key]].first -= value;
    max_heap.SiftUp(max_heap.q_info[key]);
  }

  void IncreaseKey(long long key, long long value) {
    min_heap.heap[min_heap.q_info[key]].first += value;
    min_heap.SiftDown(min_heap.q_info[key]);
    max_heap.heap[max_heap.q_info[key]].first += value;
    max_heap.SiftDown(max_heap.q_info[key]);
  }
};

int main() {
  long long ll;
  int nn;
  int kk;
  cin >> ll;
  cin >> nn;
  cin >> kk;
  vector<int> inp(nn);
  vector<long long> segm(nn);
  vector<vector<int>> steps(kk);
  vector<int> ans(kk);
  vector<int> key(nn);
  for (int i = 0; i < nn; i++) {
    cin >> inp[i];
    -- inp[i];
  }
  sort(inp.begin(), inp.end());
  for (int i = 0; i < nn; i++) {
    int ost = inp[i] % kk;
    steps[ost].push_back(i);
    segm[i] = inp[i] / kk;
  }
  MinMaxHeap minMaxHeap;
  minMaxHeap.Insert(segm[0], 0);
  key[0] = 0;
  for (int i = 1; i < nn; i++) {
    int last_key = 0;
    if (segm[i] != segm[i - 1]) {
      last_key = i * 2;
      minMaxHeap.Insert(segm[i], last_key);
    } else {
      minMaxHeap.IncreaseKey(last_key, 1);
    }
    key[i] = last_key;
  }
  for (int i = 0; i < kk; i++) {
    ans[i] = minMaxHeap.GetMax() - minMaxHeap.GetMin();
    for (auto j = 0u; i < steps[i].size(); j++) {
      int idx = steps[i][j];
      minMaxHeap.DecreaseKey(key[idx], 1);
      segm[idx] = (ll / kk + segm[idx] - 1) % (ll / kk);
      if (idx == 0) {
        if (segm[nn - 1] == segm[0]){
          key[0] = key[nn-1];
        } else {
          key[idx] -= 1;
        }
      }
      minMaxHeap.IncreaseKey(key[idx], 1);
    }
  }
  int cnt = 0;
  int mv = 0;
  for(int i = 0; i < kk; i++){
    if(ans[i] < ans[mv]){
      cnt = 1;
      mv = ans[i];
      continue;
    }
    if(ans[i] == mv){
      cnt += 1;
    }
  }
  cout << ans[mv] << " " << cnt * (ll/kk) << '\n';
  cout << mv;
}
