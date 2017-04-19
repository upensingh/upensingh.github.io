---
layout: post
title:  "What does the C Compiler do with your binaries"
date:   2017-04-16 03:54:40 -0400
categories: C
---
The compiler divides the memory into various sections

{% highlight c %}
int global1 = 100; //data
static int staticGlobal1 = 200; //data
const int globalconst = 300; //rodata
static const int staticGlobalConst = 400; //rodata


typedef struct {
	long f1[2];
} struct1_T;

static struct1_T stGlobalZeroInit = { 0L, 0L}; //bss
static const struct1_T stGlobalZeroInitConst = { 0L, 0L}; //rodata

const char * fun(int i) {
	const char *foo = "foofoo foo foo foo";
	const char *bar = "barbar bar bar bar";
	return i ? foo : bar;
}

int main() {
	int i=1; //stack
	const int j = 2; //stack
	static int si = 3; //data
	static const int sic = 4; //rodata
	struct1_T r2 = { 0L, 0L}; //stack
	static struct1_T stZeroInit = { 0L, 0L}; //bss
	static const struct1_T stZeroInitConst = { 0L, 0L}; //rodata
	static int zeroInitArr[10] = {0,0,0,0,0,0,0,0,0,0}; //bss
	return 0;
}
{% endhighlight %}


#### References
---
1. <https://en.wikipedia.org/wiki/.bss>
2. [Expert C Programming, Peter Van Der Linden](https://www.amazon.com/Expert-Programming-Peter-van-Linden/dp/0131774298)

[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
