<!-- $theme: default -->

# C++講座(入門編)
#### nito(c|k)::ict::kurokoji
###### 2018/3/19

---
<!-- page_number: true -->

## C++とは

- Cにclassとかが生えたやつ
- STL(Standard Template Library)

---

# C++とCを比べる

--- 
## C++とCを比べる
##### Hello, World!!のプログラム
``` cpp
// Main.cpp
#include <iostream>
#include <cstdio>
using namespace std; // 今はおまじないとしておきます

int main() {
  cout << "Hello, World!!" << endl;
  printf("Hello, World!!"); // こっちでも良い
}
```
``` c
// Main.c
#include <stdio.h>

int main(void) {
  printf("Hello, World!!\n");
  return 0;
}
```

---

## C++とCを比べる
##### Hello, World!!のプログラム

- 拡張子が違う
  - Cでは`.c`
  - C++では`.cpp`

- C++でもCのlibraryは使える
  - Cのlibraryを扱うときは`.h`を抜いて先頭に`c`を付ける
    - Cでは`#include <stdio.h>`
    - C++では`#include <cstdio>`
     (`#include <stdio.h>`でも動くのでどっちでも良い)
- 関数の`void`は省略出来る
- `main`の`return 0;`は省略出来る
  - C++では仕様で`0`が返る

--- 

## C++とCを比べる
##### 構造体

``` 
// C++
struct Shinobu {
  int height;
  double weight;
};

int main() {
  Shinobu st;
  st.height = 154;
  st.weight = 41;
}
```

``` c
// C
typedef struct {
  int height;
  double weight;
} Shinobu;
// mainは同じ
```

---

# CにないC++の機能

---
## CにないC++の機能
##### `bool`型

``` cpp
bool is_leep(int y) {
  bool res = (y % 400 == 0) || (y % 4 == 0 && y % 100 != 0);
  return res;
}
```


---

## CにないC++の機能
##### `bool`型

``` cpp
bool res;
int a = 1, b = 2;
res = true; // 数値的には1
res = false; // 数値的には0

if (res) {
  cout << "true" << endl;
} else {
  cout << "false" << endl;
}

res = a < b; // true
res = a > b; // false
// 否定演算子(!) !trueはfalse, !falseはtrue
res = !res;
```
---

## CにないC++の機能
##### `bool`型

- フラグ管理に使う
  - フラグであることが分かりやすい	
  ``` cpp
  int flg;
  bool flg;
  ```
  ```cpp
  flg = 1;
  flg = true;
  ```
- 真か偽を判定するには1と0で十分
  - `int`型は4byte(もったいない)
  - `bool`型は1byteなのでメモリを節約できる

---
