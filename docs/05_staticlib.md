<!--

This document is written in Markdown.
You can preview on such as VisualStudio Code.
If you want to know more, search with "vscode markdown" or refer to official document https://code.visualstudio.com/Docs/languages/markdown .

-->

# Static Library

コーディングをしていてよく聞くであろうライブラリについてである。ライブラリはプログラムを配布用にコンパイル、パッケージングしたものである。

ライブラリにはstatic library(静的ライブラリ)とdynamic library(動的ライブラリ)がある。

- Static library

	拡張子(ファイル名)はxxx.lib(Windows) / libxxx.a(Linux / Mac)としなければならない。

	static libraryの関数などを使った場合、呼び出し元のコンパイル時にその関数などだけ取りこみコンパイルする。

- Dynamic library

	拡張子は.dll(Windows) / .dylib(Mac) / .so(Linux)となる。

	dynamic libraryは実行ファイルを実行したときに動的に読み込まれる。そのため実行ファイルの実行時、dynamic libraryが指定のディレクトリのどこにも無い場合は実行ができないのでエラーを書いたりする必要がある。

特にWindowsのdllはDLLプロシージャなど作法が異なるのでここではstatic libraryの話のみする。

Sample
[GitHub : LabFurukawa/CStaticLibrarySample](https://github.com/LabFurukawa/CStaticLibrarySample)

## static libraryの作成

1. 通常通り、.hファイルと.cファイルを作成する。

	ここではschrocat1.hとschrocat1.cを作成する。

	```C
	// schrocat1.h
	#ifndef _INCLUDE_GUARD_SCHROCAT1_H_
	#define _INCLUDE_GUARD_SCHROCAT1_H_
	int Nyan(int chat);
	int NyanNyan(int chat);
	#endif
	```
	```C
	// schrocat1.c
	#include <schrocat1.h>
	int Nyan(int chat)
	{
		int ret = chat;
		ret++;
		return ret;
	}
	int NyanNyan(int chat)
	{
		int ret = chat;
		ret += 2;
		return ret;
	}
	```

2. オブジェクトファイルの作成

	`-c`オプションでオブジェクトファイルを作成する。

	```
	gcc -c -o schrocat1.o schrocat1.c
	```

3. static libraryの作成

	static libraryのファイル名は`libxxx.a`(xxxは任意)が絶対である。

	1つ以上のオブジェクトファイルをアーカイブしてstatic libraryにする。

	ここでは"schrocat1.o"、"schrocat2.o"から`ar`コマンドを使って "libschrocat.a"を作成する。

	```
	ar rcvs libschrocat.a schrocat1.o schrocat2.o
	```

## static libraryの中身を確認

中身を確認するには`nm`コマンドを使用する。

先ほど作成した"libschrocat.a"の中身を確認する。

```
nm libschrocat.a
```

## static libraryの呼び出し(gcc)

gccでは`-Lオプション`を用いてlibxxx.aファイル(xxxは任意)のディレクトリを指定、`-lオプション`を用いて呼び出す。当然そのライブラリを使うときにheaderファイルも必要であるから`-Iオプション`で追加する。

つまりgccでstatic libraryのライブラリを呼び出すには

- そのライブラリのlibxxx.a
- そのライブラリのheader

が必要である。
たとえば"source.c"から"libschrocat.a"を呼び出すとき、

```
gcc source.c -I_INCLUDE_DIR -L_LINK_DIR_ -lschrocat
```

とする。libファイルの名前は"xxx"の部分のみである事に気を付ける。

---
[Back to Home](../readme.md)

<!-- Written by Croyfet in 2022-->
