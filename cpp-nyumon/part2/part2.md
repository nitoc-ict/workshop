<!-- $theme: default -->

# C++講座(中級編)
#### nito(c|k)::ict::kurokoji
###### 2018/3/20

---
<!-- page_number: true -->

## 前回のおさらい

- Hello, World!!のプログラム
- `bool`, `auto`
- 変数宣言位置
- STL(Standard Template Library)
  - `vector`
  - `string`…


---

## 参照

- ポインタによる参照を思い出してほしい
``` cpp
int n = 3;
int *p = &n; // アドレスを代入
*p = 4; // pの指すnに代入
cout << *p << endl; // pの指すnを参照(表示)する
```

- `&`演算子でアドレス，`*`演算子でポインタ変数の参照
- C++では新しく「参照」というものが追加された

``` cpp
int n = 3;
int &p = n;
p = 4;
cout << p << endl;
```

---

- ポインタの場合`*`を使って参照していたが，
  C++の参照を使えば`*`を書くことなく変数を参照することが出来る
- 使い方
  - `型名 &変数名 = 参照先;` のように宣言(必ず宣言時に初期化)
  - 関数の
  - あとは普通の変数の使い方と同じ
    ``` cpp
    int n = 3;
    int &p = n;
    ```
    としたとき，`&n == &p`になる
  - 同じ変数だけど名前が違うだけ，という考え
  - あくまで**コピー**ではなく**参照**なので`p`を変更すると
    `n`も変更される
    
---

``` cpp
// 参照を使ったswap
void ref_swap(int &a, int &b) {
  int tmp = a;
  a = b;
  b = a;
}
// ポインタを使ったswap
void p_swap(int *a, int *b) {
  int tmp = *a;
  *a = *b;
  *b = *a;
}

int main() {
  int a = 2, b = 3;
  ref_swap(a, b);
  p_swap(&a, &b); 
}
```

---

## `const`修飾子

- 今まで`#define`などで定数を扱っていた人もいると思う
- `const`修飾子を使うことで定数を扱うことが出来る
``` cpp
const int MOD = 1000000007;
const string S = "SHINOBU";
```
- `const`修飾子は定数というよりも**変更が不可能である変数**，という意味合いが強い
- 使い方
  - `const 型名 変数名 = 値`(必ず宣言時に初期化)
  - `型名 const 変数名 = 値`でも良い

---

``` cpp
// 参照で受け取ることでコピーコストが掛からないようにしている
int sum_vector(const vector<int> &v) {
  int sum = 0;
  for (int i = 0; i < v.size(); ++i) {
    sum += v[i];
  }
  v.push_back(sum); // これはエラー vはconstなので書き換えられない
  return sum;
}
```

---

## オーバーロード

- Cでは同じ名前の関数を定義出来ないが，C++では同じ名前の関数を定義することが出来る
- 同じ名前で機能の違う関数を作ることをオーバーロードという
``` cpp
int my_abs(int a, int b) {
  if (a > b) return a - b;
  return b - a;
}

double my_abs(double a, double b) {
  if (a > b) return a - b;
  return b - a;
}
```

--- 

- オーバーロードの制限
  - 返り値の型，引数の型，引数の数，順番が同じ場合はオーバーロード出来ない
  - 返り値の型のみ違う場合でもオーバーロード
    出来ない(区別出来ないため)
    ``` cpp
    int hoge(int a) {
      return a;
    }
    
    double hoge(int a) {
      return a;
    }
    
    int main() {
      hoge(10); // どちらが呼び出されたか区別出来ない
    }
    ```

---

## デフォルト引数

- 関数において，仮引数に代入するように書くと関数呼び出しの際にその部分を省略することが出来る
- この省略した引数をデフォルト引数と呼ぶ
- 例えば，`vector<int>`の総和を返す関数`my_sum`を定義する
- `my_sum(v)`のように第2引数はデフォルトで0が代入されているので省略出来る
- また，`my_sum(v, 10)`と呼び出すとことも出来る
  この場合`v`の総和+10の結果が返ってくることになる
``` cpp
int my_sum(const vector<int> &v, int res = 0) {
  for (int i = 0; i < v.size(); ++i) {
    res += v[i];
  }
  return res;
}
```

---

- デフォルト引数の制約
  - 定数や関数などのすでに決まってるものしかデフォルト引数に出来ない
  - 引数の途中(つまり飛ばし飛ばしに)デフォルト引数が使えない
  `void func(int x = 1, int y, int z = 2)`はダメ
  `void func(int x, int y = 3, int z = 2)`はOK
  - オーバロードは出来るが，呼び出しに失敗することがある
  `void func(int x, int y)`と
  `void func(int x, int y = 2, int z = 3)`を定義したとき
  `func(1, 3)`のようには呼び出せない
  
---

## `inline`展開

- 関数を呼び出すときにかかる時間をオーバーヘッドという
- そのオーバーヘッドすら削減したい場合`inline`を関数の返り値の型に付けることで，オーバーヘッドを取り除くことが出来る
- これは，関数を呼び出したところに関数を直接埋め込むような動作をしている(`inline`展開)

``` cpp
inline void print_hello() {
  cout << "Hello" << endl;
}

int main() {
  print_hello();
}
```

---
- inline展開は必ずされるとは限らない
- コンパイラが「展開するとプログラムサイズがデカくなる(埋め込むから)」などと判断した場合展開されない

---

# `class`

---

## `class`

- 構造体と`class`は似ている
- 構造体で関数をメンバにすることが出来るようになったと
  思っていい
  
---

``` cpp
class Profile {
public:
  string name;
  int height, weight;
  void disp_profile(); // プロトタイプ宣言
  /* このように書いてしまっても良い
  void disp_profile() {
    cout << name << " " << height << " " << weight << endl;
  }
  */
};

void Profile::disp_profile() {
  cout << name << " " << height << " " << weight << endl;
}

int main() {
  Profile c;
  c.name = "shinobu";
  c.height = 154;
  c.weight = 41;
  c.disp_profile();
}
```

---

- `class`では`public`をつけないと外からアクセス出来ない
- この`public`のようなものをアクセス修飾子と呼ぶ

  |アクセス修飾子|働き|
  |:---|:---|
  |`public`|`class`内外からアクセス可|
  |`private`|`class`内からアクセス可|
  |`protected`|`class`内，派生`class`からアクセス可|

---

- 関数を`class`の中で宣言する方法は2つ
  - `class`内でプロトタイプ宣言をして外で定義を書く
    - 外で定義を書く場合は`関数の型 クラス名::関数名() {}`のように書く
  - `class`内でそのまま定義を書く(この場合`inline`展開される)
- `class`のメンバ関数はその`class`内で宣言された変数，関数を使用出来る
- アクセス修飾子を書かない場合は`private`

---

## コンストラクタ

- 前のコードでは`c.name = "shinobu"`のように代入して初期化していたが，`class`なりの初期化の方法がある
- コンストラクタは`class`のメンバを初期化するときに使う
- コンストラクタはそのオブジェクトを作るときに呼び出される

---

``` cpp
class Profile {
private:
  string name;
  int height, weight;
public:
  Profile();
  Profile(string _n, int _h, int _w);
  void disp_profile();
};
Profile::Profile() {
  name = ""; height = 0; weight = 0;
}
Profile::Profile(string _n, int _h, int _w) {
  name = _n; height = _h; weight = _w;
}
void Profile::disp_profile() {
  cout << name << " " << height << " " << weight << endl;
}
int main() {
  // Profile::Profile()の呼び出し
  Profile a;
  // Profile::Profile(string _n, int _h, int _w)の呼び出し
  Profile b("shinobu", 154, 41);
  // それぞれdisp_profile()を呼び出してみてください
}
```

---

- コンストラクタは`class`の名前の返り値のない関数
- コンストラクタはオーバーロードが出来る

---

## デストラクタ

- デストラクタはその変数の寿命が終わるときに呼び出される
- メモリを解放するときなどによく使われる

---

``` cpp
class Profile {
public:
  Profile();
  ~Profile(); // デストラクタ
};

Profile::Profile() {
  cout << "コンストラクタが呼び出されました" << endl;
}

Profile::~Profile() {
  cout << "デストラクタが呼び出されました" << endl;
}

int main() {
  Profile a;
  {
    Profile b;
  }
}
```

---

```
コンストラクタが呼び出されました
コンストラクタが呼び出されました
デストラクタが呼び出されました
デストラクタが呼び出されました
```
となるはず

- デストラクタは`class`の名前の返り値のない関数の先頭に
  `~`(チルダ)をつける

---

## `const`メンバ関数と`mutable`メンバ変数

``` cpp
class Hoge {
private:
  int x;
public:
  Hoge() { x = 0; }
  Hoge(int _x) { x = _x; }
  int poyo() const {
    x++;
    return x;
  }
};

int main() {
  Hoge a;
  cout << a.poyo() << endl;
}
```
これはエラーになる

---

- `const`メンバ関数はメンバ内の変数を変更しないことを保証する
- 当然，`const`メンバ関数はメンバ内の変数を変更するような関数も呼び出せない
- `型名 関数名() const {}`という風に使う

<br>
では，これを動かすにはどうするか

---

``` cpp
class Hoge {
private:
  mutable int x;
public:
  Hoge() { x = 0; }
  Hoge(int _x) { x = _x; }
  int poyo() const {
    x++;
    return x;
  }
};

int main() {
  Hoge a;
  cout << a.poyo() << endl;
}
```

---

- これは正常に動く
- `mutable`は変わりやすい，移り気ななどの意味
- `mutable 型名 変数名`のように使う
- `mutable`メンバ変数は`const`メンバ関数からでも書き換えることが出来る

あまり使わないが覚えてて損はない
ただ，`const`メンバ関数は積極的に使うべきである

---

## `class`継承

- 継承とは，ある`class`を元に新しい`class`を作ること
- ある`class`に機能を追加したりカスタマイズするときに使う

---

``` cpp
// ただのペン
class Pen {
private:
  int core; // 芯の長さ
public:
  Pen() { core = 0; }
  void write() { if (core > 0) core--; }
};

// 消しゴム付きペン
class ErasePen : public Pen {
private:
  int eraser; // 消しゴムの長さ
public:
  void erase() { if (eraser > 0) ereaser--; }
};

int main() {
  ErasePen p;
  p.write(); // 引き継いだメンバ関数呼び出し
  p.erase(); // 新たに定義したメンバ関数呼び出し
}
```

---

- 引き継ぎ元になる`class`を基底クラス(ここでは`Pen`)
- 引き継いで新しく作った`class`を派生クラス(ここでは`ErasePen`)
- 継承は2種類ある
  - `class 派生クラス名 : public 基底クラス名 {};`
    - 基底クラスのメンバ全てを引き継ぐ
  - `class 派生クラス名 : private 基底クラス名 {};`
    - `public`, `protected`メンバのみ引き継ぐ

---

```cpp
// 消すたびに芯が伸びるすごいペン
class SugoiPen : Pen {
private:
  int eraser; // 消しゴムの長さ
public:
  void erase() {
    if (eraser > 0) eraser--;
    core++;
  }
};

int main() {
  SugoiPen p;
  p.erase();
}
```

これはエラーになる 何故か?

---

- `core`は基底クラス`Pen`で`private`メンバ変数となっている
- `private`メンバはその`class`内からのみアクセス可

---

``` cpp
// ただのペン
class Pen {
// private:
protected:
  int core; // 芯の長さ
public:
  Pen() { core = 0; }
  void write() { if (core > 0) core--; }
};
```

---

- これで正常に動く
- `protected`メンバはその`class`内では`private`メンバのように作用するが派生クラスの中では`public`のような作用になる
  (当然，`protected`メンバは外部からは派生，基底クラスの外からはアクセスできない)
  
--- 

## オーバーライド

- 基底クラスの関数を派生クラスで変更すること
  再定義，乗っ取りするイメージ
  (**オーバーロード**と間違えないように)

---

``` cpp
class Parent {
public:
  virtual void func() {
    cout << "基底クラスの関数" << endl;
  }
};

class Child : public Parent {
public:
  void func() {
    cout << "派生クラスの関数" << endl;
  }
};

int main() {
  Child c;
  c.func();
}
```

---

- 基底クラスで`virtual 関数名(){}`のようにする
- 派生クラスで再定義する
- 実は`virtual`をつけなくてもオーバーライド出来てしまうが若干挙動が異なる
  これに関しては各自で調べみてほしい
  
---

# 関数テンプレート

---

## 関数テンプレート

- `template`は型が違うだけのたくさんの関数をひとまとまりにするもの
- 例えば，このように中身は一緒なのに型が違うだけでこんなに実装しなければならないとき，関数テンプレートが有効である
``` cpp
int my_max(int a, int b) {
  return a > b ? a : b;
}

long my_max(int a, int b) {
  return a > b ? a : b;
}

double my_max(double a, double b) {
  return a > b ? a : b;
}
```
---

``` cpp
template<typename T>
T my_max(T a, T b) {
  return a > b ? a : b;
}

int main() {
  cout << my_max(1, 3) << endl;
  cout << my_max(3.5, 10.2) << endl;
}
```
- `my_max(1, 3)`を呼び出したとき自動で
  `int my_max(int a, int b)`を作る
- 状況に応じた関数を自動で作ってくれる
---

- 関数テンプレートでは`template<テンプレート引数>`が
  定義の前につく
- `typename`は名前の通り型名を表している
  ここでは`template<class T>`のように書いても同じ動きをする
- `typename`もしくは`class`で指定した型は**自由に変わる型**である

---

``` cpp
template<typename T1, typename T2>
void disp(T1 a, T2 b) {
  cout << a << " " << b << " " << endl;
}
```

このようにテンプレート引数は2つ以上書くことも出来る

---

# クラステンプレート

---
## クラステンプレート

- 関数テンプレートのように`class`にもテンプレートを適用することが出来る
- `vector`を思い出してほしい
- `vector`はこのクラステンプレートが使われている

---

``` cpp
// 座標を表すclass
template<typename T>
class Point {
private:
  T x, y;
public:
  Point() {}
  Point(T _x, T _y) { x = _x; y = _y; }
  void disp() const {
    cout << x << " " << y << endl;
  }
};

int main() {
  Point<int> p1(1, 2); // 任意の型を<>の中に入れて使う
  Point<double> p2(3.4, 10.2);
  p1.disp();
  p2.disp();
}
```
---

- 関数テンプレートと同じように`class`の定義の前に
  `template<テンプレート引数>`をつける
- 実際にその`class`を使うときは`Point<int> p`のように`<>`の中に型名を入れて使う