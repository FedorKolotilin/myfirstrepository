#include <algorithm>
#include <deque>
#include <iostream>
#include <list>
#include <map>
#include <set>
#include <stack>
#include <vector>

using namespace std;
const int kINF = (1e9) + 1;
const int kLEN = (1e6) + 10;

int Log(int value) {
  int ans = 0;
  while (value != 1) {
    value /= 2;
    ans += 1;
  }
  return ans;
}

struct Tree {
  Tree* parent = nullptr;
  set<Tree*> child;
  int size = 0;
  int root_value = kINF;
  int root_idx = 0;
  int heap_idx = 0;
  int heap_root_idx = 0;

  static void Swap(Tree* tree1, Tree* tree2) {
    swap(tree1->size, tree2->size);
    tree1->heap_idx = tree2->heap_idx;
    tree1->heap_root_idx = tree2->heap_root_idx;
    set<Tree*> child_copy = tree1->child;
    tree1->child = tree2->child;
    tree1->child.insert(tree2);
    tree1->child.erase(tree1);
    tree2->child = child_copy;
    tree1->parent = tree2->parent;
    tree2->parent = tree1;
  }

  Tree(int root_value, int root_idx, int heap_idx)
      : root_value(root_value), root_idx(root_idx), heap_idx(heap_idx) {
    size = 1;
  }

  Tree() { size = 0; }

  static bool Compare(Tree* t1, Tree* t2) {
    return ((t1->root_value < t2->root_value) ||
            (t1->root_value == t2->root_value && t1->root_idx < t2->root_idx));
  }

  static Tree* Merge(Tree* tree1, Tree* tree2) {
    if (Compare(tree1, tree2)) {
      tree1->child.insert(tree2);
      tree2->parent = tree1;
      tree2->heap_idx = tree1->heap_idx;
      return tree1;
    }
    tree2->child.insert(tree1);
    tree1->parent = tree2;
    tree1->heap_idx = tree2->heap_idx;
    return tree2;
  }

  static void SiftUp(Tree* tree) {
    if (tree->parent == nullptr) {
      return;
    }
    if (Compare(tree, tree->parent)) {
      Swap(tree, tree->parent);
      SiftUp(tree);
    }
  }

  bool Empty() const { return size == 0; }

  void Print() {
    if (Empty()) {
      cout << "empty";
      return;
    }
    cout << root_value << " ";
    for (Tree* tree : child) {
      tree->Print();
    }
  }
};
vector<Tree*> elements(kLEN);

struct BinHeap {
  int heap_idx;
  vector<int> roots;

  BinHeap() { heap_idx = kINF; }

  void Merge(BinHeap& heap) {
    Tree* previous_level_tree = elements[0];
    unsigned idx = 0U;
    while (idx < min(roots.size(), heap.roots.size())) {
      if (previous_level_tree->Empty()) {
        if (elements[roots[idx]]->Empty()) {
          roots[idx] = heap.roots[idx];
        } else if (!elements[heap.roots[idx]]->Empty()) {
          previous_level_tree =
              Tree::Merge(elements[roots[idx]], elements[heap.roots[idx]]);
          roots[idx] = 0;
        }
      } else {
        if (elements[roots[idx]]->Empty() &&
            elements[heap.roots[idx]]->Empty()) {
          elements[roots[idx]] = previous_level_tree;
        } else {
          previous_level_tree =
              Tree::Merge(elements[heap.roots[idx]], previous_level_tree);
        }
      }
      idx++;
    }
    while (idx < heap.roots.size()) {
      if (previous_level_tree->Empty()) {
        roots.push_back(heap.roots[idx]);
      } else if (heap.roots[idx] != 0) {
        previous_level_tree =
            Tree::Merge(elements[heap.roots[idx]], previous_level_tree);
        roots.push_back(0);
      } else {
        roots.push_back(previous_level_tree->root_idx);
      }
      idx++;
    }
    if (!previous_level_tree->Empty()) {
      if (idx < roots.size()) {
        roots[idx] = previous_level_tree->root_idx;
      } else {
        roots.push_back(previous_level_tree->root_idx);
      }
    }
    for (unsigned i = 0U; i < roots.size(); i++) {
      elements[roots[i]]->heap_idx = heap_idx;
      elements[roots[i]]->heap_root_idx = i;
    }
    heap.Cleer();
  }

  void Insert(int val, int idx) {
    BinHeap bin_heap;
    Tree* tree = new Tree(val, idx, kINF);
    elements[idx] = tree;
    bin_heap.roots.push_back(idx);
    Merge(bin_heap);
  }

  int GetI() {
    int idx = 0;
    unsigned num = 0U;
    while (num < roots.size()) {
      if (Tree::Compare(elements[roots[num]], elements[roots[idx]])) {
        idx = num;
      }
      num++;
    }
    return idx;
  }

  int GetV() { return elements[roots[GetI()]]->root_value; }

  void Cleer() { roots.clear(); }

  void Extract() {
    BinHeap bin_heap;
    int idx = GetI();
    Tree extracting_tree = *elements[roots[idx]];
    for (Tree* tree : extracting_tree.child) {
      bin_heap.roots.push_back(tree->root_idx);
    }
    roots[idx] = 0;
    Merge(bin_heap);
  }

  void Print() {
    int ii = 0;
    for (auto tree : roots) {
      cout << "tree " << ii++ << "(" << elements[tree]->size << ")"
           << ": ";
      elements[tree]->Print();
      cout << '\n';
    }
  }
};

vector<BinHeap*> heaps;

void Delete(int element_idx) {
  elements[element_idx]->root_value = 0;
  Tree::SiftUp(elements[element_idx]);
  heaps[elements[element_idx]->heap_idx]
      ->roots[elements[element_idx]->heap_root_idx] = element_idx;
  heaps[elements[element_idx]->heap_idx]->Extract();
}

int main() {
  int nn;
  cin >> nn;
  nn++;
  int mm;
  cin >> mm;
  Tree empty_tree;
  elements[0] = &empty_tree;
  for (int i = 0; i < nn; i++) {
    BinHeap* bin_heap = new BinHeap();
    heaps.push_back(bin_heap);
  }
  int add_operation_cnt = 0;
  for (int i = 0; i < mm; i++) {
    int operation;
    cin >> operation;
    int heap_idx;
    int element_idx;
    int heap2_idx;
    int value;
    switch (operation) {
      case 0:
        add_operation_cnt++;
        cin >> heap_idx;
        cin >> value;
        heaps[heap_idx]->Insert(value, add_operation_cnt);
        break;
      case 1:
        cin >> heap_idx;
        cin >> heap2_idx;
        heaps[heap2_idx]->Merge(*heaps[heap_idx]);
        break;
      case 2:
        cin >> element_idx;
        Delete(element_idx);
        break;
      case 3:
        cin >> element_idx;
        cin >> value;
        Delete(element_idx);
        heaps[elements[element_idx]->heap_idx]->Insert(value, element_idx);
        break;
      case 4:
        cin >> heap_idx;
        cout << heaps[heap_idx]->GetV() << '\n';
        break;
      case 5:
        cin >> heap_idx;
        heaps[heap_idx]->Extract();
        break;
    }
  }
  for (int i = 1; i < kLEN; i++) {
    delete elements[i];
  }
  for (int i = 0; i < nn; i++) {
    delete heaps[i];
  }
}
