#include <algorithm>
#include <deque>
#include <iostream>
#include <set>
#include <stack>
#include <vector>

using namespace std;
class MyCustomException : public std::exception {
 public:
  static string What() { return "error"; }
};
struct Ship {
  int ixix;
  int iyiy;
  int iziz;
  int value;

  bool operator==(const Ship& rhs) const {
    return ixix == rhs.ixix && iyiy == rhs.iyiy && iziz == rhs.iziz;
  }

  Ship(int ixix, int iyiy, int iziz, int value)
       : ixix(ixix), iyiy(iyiy), iziz(iziz), value(value) {}

  Ship() {}
};
struct StackWithMin {
  stack<pair<Ship*, Ship*>> stack;
  ::stack<pair<Ship*, Ship*>> new_stack;

  void Clear() { stack = new_stack; }

  int Siize() const { return stack.size(); }

  void Push(Ship* ship) {
    if (stack.empty()) {
      stack.push({ship, ship});
      return;
    }
    Ship* back_min = ship;
    if (stack.top().second->value < ship->value) {
      back_min = stack.top().second;
    }
    stack.push({ship, back_min});
  }

  void Pop() {
    Check();
    stack.pop();
  }

  Ship* Top() {
    Check();
    return stack.top().first;
  }

  Ship* MinE() {
    Check();
    return stack.top().second;
  }

  void Check() const {
    if (stack.empty()) {
      throw MyCustomException();
    }
  }
};
struct QueueWithMin {
  StackWithMin front_stack;
  StackWithMin back_stack;

  void Clear() {
    front_stack.Clear();
    back_stack.Clear();
  }

  int Siize() const { return front_stack.Siize() + back_stack.Siize(); }

  void Push(Ship* ship) { back_stack.Push(ship); }

  Ship* Pop() {
    Ship* top = Top();
    front_stack.Pop();
    return top;
  }

  Ship* Top() {
    if (front_stack.Siize() == 0) {
      while (back_stack.Siize() != 0) {
        Ship* ship = back_stack.Top();
        back_stack.Pop();
        front_stack.Push(ship);
      }
    }
    return front_stack.Top();
  }

  Ship* MinE() {
    if (front_stack.Siize() == 0) {
      return back_stack.MinE();
    }
    if (back_stack.Siize() == 0) {
      return front_stack.MinE();
    }
    Ship* front = front_stack.MinE();
    Ship* back = back_stack.MinE();
    return (front->value < back->value ? front : back);
  }
};

int ixix;
int iyiy;
int iziz;
int ll;
int ww;
int hh;

void SubStringMinX(vector<vector<vector<Ship>>>& vec,
                   vector<vector<vector<Ship>>>& ans) {
  int siize = hh;
  int ixix1 = ixix;
  int iyiy1 = iyiy;
  int iziz1 = iziz;
  ans.resize(ixix, vector<vector<Ship>>(iyiy, vector<Ship>(iziz - siize + 1)));
  QueueWithMin queue;
  for (int ix = 0; ix < ixix1; ix++) {
    for (int iy = 0; iy < iyiy1; iy++) {
      for (int iz = 0; iz < siize; iz++) {
        queue.Push(&vec[ix][iy][iz]);
      }
      for (int iz = 0; iz < iziz1 - siize; iz++) {
        ans[ix][iy][iz] = (*queue.MinE());
        queue.Pop();
        queue.Push(&vec[ix][iy][siize + iz]);
      }
      ans[ix][iy][iziz - siize] = (*queue.MinE());
      queue.Clear();
    }
  }
}

void SubStringMinY(vector<vector<vector<Ship>>>& vec,
                   vector<vector<vector<Ship>>>& ans) {
  int siize = ww;
  int ixix1 = ixix;
  int iyiy1 = iyiy;
  int iziz1 = iziz - hh + 1;
  ans.resize(ixix,
             vector<vector<Ship>>(iyiy - ww + 1, vector<Ship>(iziz - hh + 1)));
  QueueWithMin queue;
  for (int ix = 0; ix < iziz1; ix++) {
    for (int iy = 0; iy < ixix1; iy++) {
      for (int iz = 0; iz < siize; iz++) {
        queue.Push(&vec[iy][iz][ix]);
      }
      for (int iz = 0; iz < iyiy1 - siize; iz++) {
        ans[iy][iz][ix] = (*queue.MinE());
        queue.Pop();
        queue.Push(&vec[iy][siize + iz][ix]);
      }
      ans[iy][iyiy1 - siize][ix] = (*queue.MinE());
      queue.Clear();
    }
  }
}

void SubStringMinZ(vector<vector<vector<Ship>>>& vec,
                   vector<vector<vector<Ship>>>& ans) {
  int siize = ll;
  int ixix1 = ixix;
  int iyiy1 = iyiy - ww + 1;
  int iziz1 = iziz - hh + 1;
  ans.resize(ixix1 - siize + 1,
             vector<vector<Ship>>(iyiy1, vector<Ship>(iziz1)));
  QueueWithMin queue;
  for (int ix = 0; ix < iyiy1; ix++) {
    for (int iy = 0; iy < iziz1; iy++) {
      for (int iz = 0; iz < siize; iz++) {
        queue.Push(&vec[iz][ix][iy]);
      }
      for (int iz = 0; iz < ixix1 - siize; iz++) {
        ans[iz][ix][iy] = (*queue.MinE());
        queue.Pop();
        queue.Push(&vec[siize + iz][ix][iy]);
      }
      ans[ixix1 - siize][ix][iy] = (*queue.MinE());
      queue.Clear();
    }
  }
}

int main() {
  cin >> ixix;
  cin >> iyiy;
  cin >> iziz;
  cin >> ll;
  cin >> ww;
  cin >> hh;
  int oldix = ixix;
  int oldiy = iyiy;
  int oldiz = iziz;
  ixix += ll;
  iyiy += ww;
  iziz += hh;
  vector<vector<vector<Ship>>> inp(
       ixix, vector<vector<Ship>>(iyiy, vector<Ship>(iziz)));
  for (int ix = 0; ix < ixix; ix++) {
    for (int iy = 0; iy < iyiy; iy++) {
      for (int iz = 0; iz < iziz; iz++) {
        if (ix < oldix && iy < oldiy && iz < oldiz) {
          int value;
          cin >> value;
          value *= -1;
          inp[ix][iy][iz] = Ship(ix, iy, iz, value);
        } else {
          inp[ix][iy][iz] = Ship(ix, iy, iz, 1);
        }
      }
    }
  }
  vector<vector<vector<Ship>>> ans1;
  vector<vector<vector<Ship>>> ans2;
  vector<vector<vector<Ship>>> ans3;
  SubStringMinX(inp, ans1);
  SubStringMinY(ans1, ans2);
  SubStringMinZ(ans2, ans3);
  vector<vector<vector<Ship>>> ans(
       ixix, vector<vector<Ship>>(iyiy, vector<Ship>(iziz)));
  for (int ix = ixix; ix >= 0; ix--) {
    for (int iy = iyiy; iy >= 0; iy--) {
      for (int iz = iziz; iz >= 0; iz--) {
        Ship ship(ix, iy, iz, 0);
        if (!(ship == ans3[ship.ixix][ship.iyiy][ship.iziz])) {
          ship = ans3[ship.ixix][ship.iyiy][ship.iziz];
        }
        ans[ship.ixix][ship.iyiy][ship.iziz] = ship;
      }
    }
  }
  int nn;
  cin >> nn;
  for (int i = 0; i < nn; i++) {
    int pix;
    int piy;
    int piz;
    cin >> pix;
    cin >> piy;
    cin >> piz;
    cout << ans[pix][piy][piz].ixix << " " << ans[pix][piy][piz].iyiy << " "
         << ans[pix][piy][piz].iziz << '\n';
  }
}
