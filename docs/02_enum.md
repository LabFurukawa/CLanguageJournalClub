<!--

This document is written in Markdown.
You can preview on such as VisualStudio Code.
If you want to know more, search with "vscode markdown" or refer to official document https://code.visualstudio.com/Docs/languages/markdown .

-->

# enum

## Difinition

```C
typedef enum _tagWEEK
{
	WEEK_SUN = 1,
	WEEK_MON,
	WEEK_TUE,
	WEEK_WED = 2,
	WEEK_THU,
	WEEK_FRI,
	WEEK_SAT
}WEEK;
```

`enum`型は内部的には`int`型に変換されるため`列挙子`(`WEEK_SUN`, `WEEK_MON`など)の名前の情報などは残らない。

あくまでコーディングの可読性を上げるためのもの。

任意の値が割り振られるが明示的に値を設定することができる。同じ値は設定できない(方法はある)。


## Usage

```C
int GetWeek(WEEK w);

int main()
{
	WEEK today = WEEK_SUN;
	GetWeek(today);

	return 0;
}

int GetWeek(WEEK w)
{
	switch(w)
	{
	case WEEK_SUN:
		printf("Sunday\n");
		break;
		//....
	}

	return 0;
}
```

## Remarks

### Notation

`enum`型はただの`int`型なので`#define`を使って

```C
#define WEEK_SUN (1)
#define WEEK_MON (2)
#define WEEK_TUE (1)
//...
```
としても構わない。しかしコピペのミスなどで同じ値を間違って振ってしまうなどのミスを防ぐことができる。`enum`型で同じ値を明示的に振ったとしてもコンパイルエラーとなるため変なバグを生まない対策になる。

バグを生みにくいコーディングをすることも重要である。

### Readable

上のバグを生みにくいコードの続きで

```C
int GetQuantumState_int(int particle, ....);
```
というような関数の場合
- 0の場合boson
- 1の場合fermion

という設計だったとして実際にコードを書くと

```C
GetQuantumState_int(0, ....);
```

という使い方になる。この関数がbosonを見ているのかfermionを見ているのかパッと見わからない。

またコーディングしていても「1はどっちだっけ」となることもあり得るだろう。

```C
typedef enum _tagPARTICLE
{
	PARTICLE_BOSON,
	PARTICLE_FERMION
}PARTICLE;

int GetQuantumState_enum(PARTICLE particle, ....);
```
という設計にしておけば
```C
GetQuantumState_enum(PARTICLE_BOSON, ....);
```
というように可読性が高く、バグも生みにくいコードを書くことができる。

最初、`WEEK`のような多くの列挙子を持つ例を挙げていたが、上記のように`PARTICLE_BOSON`と`PARTICLE_FERMION`だけという少ない列挙子だろうと見やすいのならば積極的に`enum`型を利用すべきである。


### Namespace and Naming

「`enum`型や定数は大文字で命名する」ことが多い傾向にある。

というのも大事なのだがC言語にはnamespace(名前空間 C++で出てくる概念)がない。

簡単に言えば

```C
typedef enum _tagHAND
{
	RIGHT,
	LEFT
}HAND;

typedef enum _tagANSWER
{
	RIGHT,
	WRONG
}ANSWER;
```

とした場合`RIGHT`が2回定義されたことになり、コンパイルできない。

また関数を書いているときも

```C
int Question(ANSWER h);
```
としたとき

```C
int Question(
```
と打っていて予測変換で`enum ANSWER`型だとわかっても、列挙子はなんだったか`RIGHT`だったか`CORRECT`だったかといった思い出せないことも起こりうる。

列挙子名の頭にも`enum`型と同じプレフィックスを付けて
```C
typedef enum _tagANSWER
{
	ANSWER_RIGHT,
	ANSWER_WRONG
}ANSWER;
```
とすれば
```C
int Question(ANSWER_
```
まで打って予測変換も可能だろう。


### Modern Style [Advanced]

近年の関数設計はenumが引数に用いられるようになったりしている。

何より3DグラフィックスのためのVulkanやGPGPUのためのCUDAの関数の戻り値は
`enum VkResult`や`enum cudaError_t`などenum型の戻り値を用いるようになってきている。

`ポインター`の説明までは踏み込まないため言及はしないが

`大きな値や計算結果は引数にポインター渡し、戻り値にはenumでエラーコード`

という設計がモダンだと考えている。(諸説あり)

---
[Back to Home](../readme.md)

<!-- Written by Croyfet in 2022-->
