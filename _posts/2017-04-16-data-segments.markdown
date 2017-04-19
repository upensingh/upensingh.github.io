---
layout: post
title:  "What does the C Compiler do with data in your C program"
date:   2017-04-16 03:54:40 -0400
categories: C
---
The compiler divides the memory into various sections in the binary files (object files, shared/static libraries). On UNIX and various UNIX like systems the file format is called [Executable and Linkable Format][ELF-wiki] (or ELF). All the data (variables/constants) defined in your program lives in one of the following sections of the binary
1. .data section
2. .rodata section
3. .bss section
4. Stack
5. Heap

The next question is what goes where, Here is a small diagram that explains it
![Data_Layout]({{ site.url }}/assets/data_layout.jpeg)

The above diagram shows where the compiler puts the initialized data. Apart from this
* Static 0 initilized or uninitialized data goes into the [.bss][BSS-wiki] section.
* All dynamically assigned data goes on to the heap.

The following C program exemplifies all the different types discussed above followed by a comment indicating which part of the binary it is stored in.

{% highlight c %}
int global1 = 100; //.data
static int staticGlobal1 = 200; //.data
const int globalconst = 300; //.rodata
static const int staticGlobalConst = 400; //.rodata


typedef struct {
	long f1[2];
} struct1_T;

static struct1_T stGlobalZeroInit = { 0L, 0L}; //.bss
static const struct1_T stGlobalZeroInitConst = { 0L, 0L}; //.rodata

int main() {
	int i=1; //stack
	const int j = 2; //stack
	static int si = 3; //.data
	static const int sic = 4; //.rodata
	struct1_T r2 = { 0L, 0L}; //stack
	static struct1_T stZeroInit = { 0L, 0L}; //.bss
	static const struct1_T stZeroInitConst = { 0L, 0L}; //.rodata
	static int zeroInitArr[10] = {0,0,0,0,0,0,0,0,0,0}; //.bss
	return 0;
}
{% endhighlight %}

An important thing to note here is this layout can differ between different compilers. The above information is as obtained from gcc 6 on a Debian 8 machine. Stay tuned for the next post to learn how to obtain this information.
 
#### References
---
1. <https://en.wikipedia.org/wiki/.bss>
2. <https://en.wikipedia.org/wiki/Executable_and_Linkable_Format>
3. [Expert C Programming, Peter Van Der Linden](https://www.amazon.com/Expert-Programming-Peter-van-Linden/dp/0131774298)

[BSS-wiki]: https://en.wikipedia.org/wiki/.bss
[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
[ELF-wiki]: https://en.wikipedia.org/wiki/Executable_and_Linkable_Format
