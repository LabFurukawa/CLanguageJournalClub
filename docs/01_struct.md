<!--

This document is written in Markdown.
You can preview on such as VisualStudio Code.
If you want to know more, search with "vscode markdown" or refer to official document https://code.visualstudio.com/Docs/languages/markdown .

-->

# Struct

## Declear and difinition

```C
struct Vec3i; // Declare
struct Vec3i
{
	int x;
	int y;
	int z;
}; // Definition
```
宣言は必須ではないしあまり書かれることはない。

大抵、構造体はヘッダーファイルに書かれる。

## Usage

```C
struct Vec3i foo;
foo.x = 1;
foo.y = 2;
doo.z = 3;

struct Vec3i bar = { 1, 2, 3 };
```

## typedef

新しい型名を定義するキーワード

型や構造体に別名を付けることができる。

```C
typedef int MY_INT;

MY_INT main()
{
	return 0;
}
```

## struct + typedef

```C
typedef struct _tagVec3d
{
	double x;
	double y;
	double z;
} Vec3d;

Vec3d baz = { 0.0, 0.1, 0.2 };
```

いちいち`struct _tagVec3d`と書くこともできるが面倒なのでtypedefで`Vec3d`に書き換えてしまう。

もともとの構造体名に`_tag`というプレフィックスを付けるのはWindowsのSDKのコードに倣ったもの。

typedefしたものと同じ名前は付けられないので予測変換の邪魔にならないよう頭に少なくとも`_`をつけることが一般的である。


---
[Back to Home](../readme.md)

<!-- Written by Croyfet in 2022-->
