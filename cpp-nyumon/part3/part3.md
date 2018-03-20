<!-- $theme: default -->


# C++講座(ゆるふわ上級編)
#### nito(c|k)::ict::kurokoji
###### 2018/3/21

---
<!-- page_number: true -->

## 前回のおさらい

- 参照
- `const`修飾子
- オーバーロード
- デフォルト引数
- `inline`展開
- `class`
- クラス・関数テンプレート
- range based for
---
## 注意

- 今日の内容は結構難しいので，なんとなく分かる程度でOK
- 開発するときにリファレンスを見ながら出来ればよい

---
## 演算子オーバーロード

- 演算子オーバーロードとは`+`, `-`, `*`, `/`や`<<`, `>>`などの演算子を再実装すること
- 例えば座標を表す`class`,`P`があったとする
  `a+b`をやりたい場合`P add(const P& a, const P& b)`関数を定義れば良いが，何度もこの計算を行う場合，見た目がよくない

---
## 算術演算子のオーバーロード
``` cpp
class P {
private:
  int x, y;
public:
  P(int _x, int _y) {
    x = _x;
    y = _y;
  }
  void show() {
    cout << x << " " << y << endl;
  }
};

int main() {
  P a(4, 5), b(1, 2);
  P res = a + b; // add(a, b)のようにすればいいが…
  // (5, 7)になることを期待する
  res.show();
}
```
これはエラーになる

---

`+`, `-`演算子を再定義する
``` cpp
#include <iostream>
using namespace std;
class P {
private:
  int x, y;
public:
  P(int _x, int _y) { x = _x; y = _y; }
  void show() {
    cout << x << " " << y << endl;
  }
  P operator+(const P& rhs) const {
    return P(x + rhs.x, y + rhs.y);
  }
  P operator-(const P& rhs) const {
    return P(x - rhs.x, y - rhs.y);
  }
};

int main() {
  P a(4, 5), b(1, 2), c(1, 1);
  P res = a + b - c; // -も定義したので使える
  res.show();
}
```


---


``` bash
4 6
```
が出力されるはず

- `+`, `-`, `*`, `/`, `%`などの算術演算子はこのように`class`内で演算子を定義することが出来る
- `クラス名 operator 演算子 (const クラス名& rhs) const {}`のように定義
  (必ず`rhs`にしろというわけではないが分かりやすいので)
- `rhs`は`right hand side`の意味で演算子を中心とすると右手側を表している
- このとき自分自身は演算子を中心とすると左手側になる
---

## 比較演算子のオーバーロード
``` cpp
class P {
// コンストラクタなど省略
public:
  bool operator==(const P& rhs) const {
    return x == rhs.x && y == rhs.y;
  }
  bool operator!=(const P& rhs) const {
    return x != rhs.x || y != rhs.y;
  }
  bool operator<(const P& rhs) const {
    return x == rhs.x ? y < rhs.y : x < rhs.x;
  }
  bool operator>=(const P& rhs) const {
    return x >= rhs.x;
  }
  bool operator>(const P& rhs) const {
    return x == rhs.x ? y > rhs.y : x > rhs.x;
  }
  bool operator<=(const P& rhs) const {
    return x <= rhs.x;
  }
};
```

---

``` cpp
int main() {
  P a(1, 2), b(1, 2), c(1, 3);
  bool res1 = (a == b);
  bool res2 = (a < c);
  cout << res1 << " " << res2 << endl;
}
```
``` bash
1 1
```
と表示されるはず

- 比較演算子も算術演算子と同様に定義するが，`==`を定義した場合は`!=`も定義しなければならない
- 同じように`<`を定義したとき`>=`，`>`を定義したとき`<=`をそれぞれ定義しなければならないことに注意
---
## 複合代入演算子のオーバーロード

``` cpp
class P {
// コンストラクタなど省略
public:
  P& operator+=(const P& rhs) {
    x += rhs.x;
    y += rhs.y;
    return *this;
  }
}
```
---
``` cpp
int main() {
  P a(1, 2), b(2, 3);
  a += b;
  a.show();
}
```
``` bash
3 5
```
と出力されるはず

---

- `クラス名& operator 演算子(const クラス名& rhs) {}`
- `-=`, `*=`, `/=`, `%=`なども同じように定義する
- `this`は自分自身のポインタ(`this->x`のようにアクセス出来る)
- 返り値は`*this`の参照を返す
---

- これらに留まらず色んな演算子をオーバーロードすることが出来る
- `[]`, `->`, `<<`など
  - `vector`は`[]`を定義して配列のようにアクセスできる
  - `cout`, `cin`は`<<`や`>>`を定義している
- ここでは紹介しきれないので調べてみてください
---

# イテレータ(反復子)

---

## イテレータ(反復子)
- イテレータ(反復子)とはコンテナ(`vector`, `list`など)に含まれる要素に対する反復処理を行うオブジェクト
- 使い方はポインタに似ている

---

``` cpp
#include <iostream>
#include <list>
using namespace std;

int main() {
  list<int> a = {1, 2, 3};
  for (list<int>::iterator i = a.begin(); i != a.end(); ++i) {
    cout << *i << endl;
  }
}
```

---

``` bash
1
2
3
```
と出力されるはず

- イテレータはコンテナが持つ`begin()`, `end()`メンバ関数から取得できる
  -  `begin()`は先頭要素を，`end()`は末尾の1つ先の要素を指す 
- イテレータ変数は`コンテナ::iterator 変数名`のように宣言する
- `*`で要素を参照出来る(これがポインタに似てると言われる所以)
- しかしこのように長くなってしまうため初期化出来る場合は`auto`を使うべき(>= C++11)
``` cpp
for (auto i = a.begin(); i != a.end(); ++i) {
  cout << *i << endl;
}
```

---

- `vector`や`map`，`set`, `deque`などのSTLコンテナでも同じように使える
  - `vector<int>::iterator`のように
- イテレータの指し示す要素が変更できない`const_iterator`も用意されている
  - `list<int>::const_iterator`
  - `const`なイテレータを取得する場合，`cbegin()`, `cend()`で取得できる
- 非メンバ関数`begin()`, `end()`(>= C++11)
  `cbegin()`, `cend()`(>= C++14)もある
  - `#include <iterator>`をすると使える
  - `begin(a)`と`a.begin()`は同じ

---

- イテレータは`++`演算子で次の要素に，`--`演算子で前の要素に移動出来る
  ``` cpp
  list<int> a = {1, 2, 3, 4};
  auto a = a.begin();
  cout << *a << endl; // 1
  ++a;
  cout << *a << endl; // 2
  --a;
  cout << *a << endl; // 1
  ```
---

- `prev()`, `next()`でイテレータを戻したり進めたり出来る
  (>= C++11)
  第2引数はどれだけ進ませたり戻したりするかを指定でき，
  デフォルト引数で1を指定している
  ``` cpp
  list<int> a = {1, 2, 3, 4, 5};
  auto it = a.begin();
  it = next(it); // itを1つ先に進ませる
  it = next(it, 3); // itを3つ先に進ませる
  it = prev(it); // itを1つ前に戻す
  it = prev(it, 3); // itを3つ前に戻す
  ```
  
---

# 名前空間
---

## 名前空間
- 名前空間とはC++において名前の衝突を防ぐために必要なもの
- `namespace 名前 {}`のように使う
- 名前空間されたものを呼び出すときは`::`(スコープ解決演算子)
  を用いる
  - `名前::func()`
- C++の標準ライブラリは`std`名前空間に定義されている
  - 本来は`std::cout`, `std::min()`のように
使わなければならない
- また名前空間はネスト可能でそれを呼び出す場合`A::B::func()`のように呼び出す
---

``` cpp
#include <iostream>

namspace aomori {
  void kawaii() {
    std::cout << "めんこい" << std::endl;
  }
}

namespace okinawa {
  void kawaii() {
    std::cout << "ちゅらかーぎー" << std::endl;
  }
}

int main() {
  aomori::kawaii();
  okinawa::kawaii();
}
```

---
``` bash
めんこい
ちゅらかーぎー
```
と出力されるはず

---

## `using namespace`の弊害

- 毎回のように`using namespace std;`を
  おまじないだと言っていたが，`std`を省略して書くためである
- しかし，本来名前の衝突を防ぐために使っていた名前空間をこのように扱ってしまうと問題が起きる

---

``` cpp
#include <iostream>
#include <algorithm>
using namespace std;

template<typename T>
T max(const T& a, const T& b) {
  return a > b ? a : b;
}

int main() {
  cout << max(1, 2) << endl;
}
```
これはエラーになる

---

- `std`名前空間で定義された`max()`と自分で定義した`max()`が衝突しどちらを呼び出すのかが分からなくなる

これを回避するためには
1. `using namespace std;`を消す
2. `std`名前空間の`max()`を呼び出したければ`std::max()`
3. 自分で定義した`max()`を呼び出したければ`max()`

とすれば良い

---

- `std`空間の関数などを全て把握しているなら
   `using namespace std;` をしても良い
   つまり，全て把握出来るわけが無いので使うべきではない
   
---

## 名前空間のエイリアス(別名)

- `namespace 別名 = 元の名前;`というように使う
  ``` cpp
  #include <iostream>
  
  int main() {
    namespace s = std;
    s::cout << "hoge" << s::endl;
  }
  ```
---

## `using`宣言

- `using namespace std;`を使ってしまうと名前空間にある名前が衝突するおそれがある
- `using`宣言を使えば被害を最小限に留めることが出来る
  ``` cpp
  #include <iostream>
  
  int main() {
    using std::cout;
    using std::endl;
    using std::cin;
    
    int n;
    cin >> n;
    cout << n << endl;
  }
  ```

---

# おまけ

---

## `cstdint`で定義されている型(>= C++11)

|型|内容|
|:---|:---|
|`std::int32_t`|32bitの符号付き整数|
|`std::int64_t`|64bitの符号付き整数|
|`std::uint32_t`|32bitの符号無し整数|
|`std::uint64_t`|64bitの符号無し整数|
|`std::int_fast32_t`|32bit以上の最も高速に処理する符号付き整数|
|`std::int_fast64_t`|64bit以上の最も高速に処理する符号付き整数|
|`std::uint_fast32_t`|32bit以上の最も高速に処理する符号無し整数|
|`std::uint_fast64_t`|64bit以上の最も高速に処理する符号なし整数|

---

- 細かいが高速な処理が必要なとき`std::int_fast64_t`らを使うと良いだろう
- このままでは長いため`using int64 = std::int_fast64_t;`として使う方がよい