<!--

This document is written in Markdown.
You can preview on such as VisualStudio Code.
If you want to know more, search with "vscode markdown" or refer to official document https://code.visualstudio.com/Docs/languages/markdown .

-->

# Preprocessor

C言語やC++のコードをコンパイルする前には`プリプロセッサー`の処理が走る。

`プリプロセッサー`の処理は簡単に言ってしまえば `#` から始まる文のことである。

例を挙げるなら`#include`、`#define`のようなものである。

この処理はコンパイラーがコンパイルする前に行われる。つまりコンパイラーが見るのは`プリプロセッサーに処理された後のコード`である。

実際に例を見ていったほうがいいだろう。

## `#define`

#defineは値を定義する場合に用いられる。

先述の通り、`#define`、`defineマクロ`はコンパイルされる前に`プリプロセッサー`により処理される。

```C
#define HUNDRED 100

printf("value : %d", HUNDRED + 1); // value : 101
```
というようなコードがあった場合、コンパイラーでは

```C
printf("value : %d", 100 + 1); // value : 101
```

というコードが書かれているように見えるわけである。

`defineマクロ`で引き起こされる有名なバグがある。

```C
#define HUNDRED 40 + 60

printf("value : %d", HUNDRED * 2);
```

上記のコードがコンパイルされた場合、実は答えは $160$ が出てくる。先までの説明に沿えば難しいことはない。

上記のコードはプリプロセッサーにより展開され

```C
printf("value : %d", 40 + 60 * 2);
```

とコンパイラーから見えるわけである。当然、演算子の優先度から $60 \times 2$ が先に評価され $160$ の答えが出てくる。

この対策としては愚直に

```C
#define HUNDRED (40 + 60)
```
とするか
```C
const int HUNDRED = 100;
```
としたりするほうがいいだろう。ただしC言語では配列が要素数がコンパイル時定数である必要があるため`defineマクロ`の使用を避けることはできない。(C++だと話が変わってくる)

## `#include`

おまじないと言われているアレ。

includeしたヘッダーファイルが`#include`した場所に展開される。

cファイルをインクルードすることは普通しない。

また`#include`の書き方に
- #include <>
- #include ""

があるが
- #include <>

	環境変数から探索される。

- #include ""

	カレントディレクトリ、環境変数の順で探索される。

となっている。つまり、
```C
#include "stdio.h"
````
としてもコンパイルはできる。

またコンパイラーのインクルードパスは
```
gcc -x c++ -v -E /dev/null
```
で確認できる。

またこの環境変数はgccのオプションで

```
gcc -I../include source.c
```

のように追加することができる。
すなわちこのオプションを追加することで自分の作ったヘッダーファイルも`#include <>`で参照することができる。


## `#ifdef`/`#ifndef` ~ `#endif`

その名の通り`#define`されている場合その範囲のコードが有効化されたり無効化されたりする。

先述通り、無効化されているときは`そのコードが書かれていないものとして`扱われて、コンパイラーに渡される。

たとえば`DEBUG`という値を宣言した場合

```C
#define DEBUG

#ifdef DEBUG
// Active
#endif
```

この値の`#define`を無効化すると

```C
//#define DEBUG

#ifdef DEBUG
// Inactive
#endif
```

## `else` / `elif <condition>`

条件分を使えるのでバージョンごとにincludeするヘッダーファイルを変更したり、定義する値を変更したりすることができる。

```C
#define VERSION (40)

#ifdef (VERSION < 10)
printf("Version : 0.x");
#elif (VERSION < 20)
printf("Version : 1.x");
#else
printf("Version : TOO NEW");
#endif
```


## インクルードガード(defineマクロの応用)

以下のような記述をすることでその内容を一度だけ読み込むことが可能となる。

ヘッダーファイルに関数の定義などを書いた場合、その関数がそこに実体を持ってしまう。つまり、その関数が10買い呼び出されたらプログラムに10個同じ関数が作成されてしまう。

ヘッダーファイルは基本は定義だけ、宣言は別のファイルで行うことで一回の宣言と定義だけされて1個の関数を10回呼び出すプログラムになる。

```C
// foo.h

#ifndef _INCLUDE_GUARD_FOO_H_
#define _INCLUDE_GUARD_FOO_H_

// Declare
int add(int lhs, int rhs);

#endif
```

```C
// foo.cpp

#include <foo.h>
int add(int lhs, int rhs)
{
	return lhs + rhs;
}
```

```C
// main.cpp
#include <foo.h>
#include <stdio.h>

int main()
{
	int ans = add(1, 2); // 3
	printf("ans : %d", ans);
	return 0;
}
```


## プリプロセッサー定義済みマクロ

`#define`したものだけが定義されているわけではない。

何を言っているかというとあらかじめdefineされている値もあるということである。

たとえばgccでは`__GNUC__`があらかじめ定義されている。それに対し、WindowsのVisual C++環境では`_WIN32`のマクロが定義されている。

これがマルチプラットフォームでコンパイルすることができるコードを実現している。

例えばLinuxなどで低レベルでファイルを扱うには`fcntl.h`が必要だがwindows環境では`fileapi.h (Windows.h)`が必要である。OSによって設計が違うためシステムコールなどの関数が異なり、違うヘッダーファイルが必要になってくる。

そのため

```C
#ifdef _WIN32
#include <Windows.h> // for Win
#else
#include <fcntl.h> // for Linux
#endif
```

のように書くことがある。このようにdefineマクロなどでコンパイル対象を変更したりする手法を俗に`コンパイルスイッチ`などと言われる。


## gccオプションからの定義

gccの`-D`オプションを使うことで、定義済みマクロのように外部からマクロを流し込むことができる。

```
gcc -DDEBUG source.cpp
```

とすればコード内で`#define DEBUG`としたと同様の振る舞いをする。

---
[Back to Home](../readme.md)

<!-- Written by Croyfet in 2022-->
