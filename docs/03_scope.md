<!--

This document is written in Markdown.
You can preview on such as VisualStudio Code.
If you want to know more, search with "vscode markdown" or refer to official document https://code.visualstudio.com/Docs/languages/markdown .

-->

# Scope and declare

変数は他の変数などからアクセスできる範囲が決められている。
この制限をうまく使い、
コンパイル時やSyntax Highright(コードに色付けしたり、構文解析してくれる機能)でエラーを検出してバグを生みにくいコードを書くことが重要である。

この変数がアクセスできる範囲のことをスコープ(Scope)という。

## 宣言

C言語では変数は宣言した後から使うことができる。

以下の例では"alpha"を宣言するときに"beta"で初期化しようとしているが、この段階では"beta"が宣言されていないためコンパイル時にエラーが出る。

```C
int main()
{
	int alpha = beta; // Error (Not declare)
	int beta = 1;
	// ...
}
```

## 宣言の場所

太古のC言語では「変数を宣言する場所はmain文や関数の中の一番最初」という制限があったが現在は基本どこで定義してもかまわない。今まで以下のコードのような書き方で学んできたかもしれないが`スコープ`の説明をすることが面倒だったからである。
最初から使わない変数をわざわざ最初に並べる必要はない。

```C
int main()
{
	int i, j, k, N;
	
	// ...
}
```

変数は使いたいときに宣言して

```C
int main()
{
	int a = 10;
	printf("a : %d\r\n", a); // "a" is available, but not "b" is not

	int b = 20;
	printf("b : %d\r\n", b); // "a" and "b" are available

	// ...
}
```

とすることでその変数が実際どこに必要なのかわかりやすくなることがある。


## 宣言と同時に初期化

C言語では変数を宣言してもデータが初期化されていない。つまり"メモリの場所は確保したが、中身に最初から数字が入っている"ということである。データを入れることにも時間はかかるので、慣れてくると"あえて"初期化しない場合はあるが基本的には宣言と同時に初期化することが一般的である。

```C
int main()
{
	int a = 0; // Declare and initialize with 0
	//...
}
```

## 一つづつ宣言

先ほどの

```C
int main()
{
	int i, j, k, N;
	
	// ...
}
```
のように
複数の変数を同時に宣言することがあったが読みにくいので避けたほうがいい。

品がない。

メモリが貧しい時代にソースコードだけでメモリがいっぱいにならないよう短い変数名や関数名など、以上のような記述が行われてきたか知らないが、今のメモリの容量や実行速度で前述のメリットは皆無に近い。

以下のように初期化しつつ宣言していくのがいいだろう。
```C
int main()
{
	int width = 120;
	int height = 320;
	int depth = 500;
	// ...
}
```


## for文

ここまでで既に
```C
int main()
{
	int i, j, k, N;
	
	// ...
}
```
という書き方がナンセンスなのことは説明した。ここでのどうせ"i"や"j"はfor文のための変数であろう。

for文のループ回数などというものは実際にそのループの中でしか必要が無かったりするものである。

スコープの観点から以下のような書き方ができる。

```C
int main()
{
	for(int i = 0; i < 10; i++) // Declare "i"
	{
		printf("%d\r\n", i); // "i" is available
	}

	printf("%d\r\n", i); // Error (Not declare)

	// ...
}
```

以上のようにfor文の"()"の中で`宣言`された"i"はfor文の中("()"と"{}")でしかアクセスすることができない。

そのため以下のように安全に変数を使いまわすことができる。

```C
int main()
{
	for(int i = 0; i < 10; i++) // Declare "i"
	{
		for(int j = 0; j < 10; j++) // Declare "j"
		{
			printf("%4d", i * 10 + j);
		}
		printf("\r\n");
	}

	for(int i = 0; i < 10; i++) // Declare "i"
	{
		printf("%d\r\n", i);
	}

	// ...
}
```

## 一般に言うスコープ

`for文`や`if文`や`switch文`も"{}"の中で`宣言`された変数は外部からアクセスできない。

```C
int main()
{
	int a = 1;
	if(a > 0)
	{
		int output = 0; // Declare "output" (only this scope)
		printf("%d\r\n", a); // "a" is available
		printf("%d\r\n", output); // "output" is available
	}

	printf("%d\r\n", output); // Error (Not Declare, Out of scope)
	
	// ...
}
```

### ちょっとしたテクニック

スコープを作るのは"{}"なので

以下の場合"a"のスコープが違うので"aが再宣言されている"ようなエラーが起こらない。デバッグ時などに少し便利。

```C
int main()
{
	{
		int a = 0;
		printf("%d\r\n", a);
	}

	{
		int a = 0;
		printf("%d\r\n", a + 1);
	}

	// copy and paste...
}
```

---
[Back to Home](../readme.md)

<!-- Written by Croyfet in 2022-->
