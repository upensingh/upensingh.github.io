---
layout: post
title:  "Utilities available to look at a binary"
date:   2017-04-23 03:54:40 -0400
categories: C
---
In my previous post we saw what are the various sections in a binary file generated for a C program. We talked about some utilities such as objdump. In this post I am going to talk about some of the utilities available for poking around binary files. Here is a small list of utilities available
1. [objdump] [man-objdump]
2. [size] [man-size]
3. [readelf] [man-readelf]
4. [nm] [man-nm]
5. [dumpbin.exe] [man-dumpbin] (Windows only, part of Visual Studio build tools)

All the tools above are available on most Linux distros, except dumpbin which is part of the Visual Studio build tools. Some of these may not be available if you are using a mac but can be installed (see this [post] [so-readelf] on stack overflow). Continuing to use the example C program from the previous post

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

Compiling the above program using gcc produces an object file (main.o).
{% highlight shell %}
gcc -c main.c
{% endhighlight %}
Now Lets try and use objdump on the generated object file "main.o". The -h is used to display summary information for each section of the object file. (Look at the [objdump man page][man-objdump] for other options and more details)
{% highlight shell %}
objdump -h main.o
{% endhighlight %}
This produces the following output
{% highlight shell %}
main.o:     file format elf64-x86-64

Sections:
Idx Name          Size      VMA               LMA               File off  Algn
  0 .text         00000029  0000000000000000  0000000000000000  00000040  2**0
                  CONTENTS, ALLOC, LOAD, READONLY, CODE
  1 .data         0000000c  0000000000000000  0000000000000000  0000006c  2**2
                  CONTENTS, ALLOC, LOAD, DATA
  2 .bss          00000060  0000000000000000  0000000000000000  00000080  2**5
                  ALLOC
  3 .rodata       00000034  0000000000000000  0000000000000000  00000080  2**4
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
  4 .debug_info   000001b9  0000000000000000  0000000000000000  000000b4  2**0
                  CONTENTS, RELOC, READONLY, DEBUGGING
  5 .debug_abbrev 000000b5  0000000000000000  0000000000000000  0000026d  2**0
                  CONTENTS, READONLY, DEBUGGING
  6 .debug_aranges 00000030  0000000000000000  0000000000000000  00000322  2**0
                  CONTENTS, RELOC, READONLY, DEBUGGING
  7 .debug_line   0000003f  0000000000000000  0000000000000000  00000352  2**0
                  CONTENTS, RELOC, READONLY, DEBUGGING
  8 .debug_str    000000df  0000000000000000  0000000000000000  00000391  2**0
                  CONTENTS, READONLY, DEBUGGING
  9 .comment      0000001e  0000000000000000  0000000000000000  00000470  2**0
                  CONTENTS, READONLY
 10 .note.GNU-stack 00000000  0000000000000000  0000000000000000  0000048e  2**0
                  CONTENTS, READONLY
 11 .eh_frame     00000038  0000000000000000  0000000000000000  00000490  2**3
                  CONTENTS, ALLOC, LOAD, RELOC, READONLY, DATA
{% endhighlight %}
The objdump output shows the size of various sections e.g. size of the .data section is 12 bytes (sizes are in Hex) that is consistent with the annotations in main.c above on Debian x86-64 system with gcc compiler. Also, note the file format of the object file is elf64-x86-64.

[man-objdump]: https://linux.die.net/man/1/objdump
[man-size]: https://linux.die.net/man/1/size
[man-dumpbin]: https://msdn.microsoft.com/en-us/library/c1h23y6c.aspx
[man-readelf]: https://linux.die.net/man/1/readelf
[man-nm]: https://linux.die.net/man/1/nm
[so-readelf]: http://stackoverflow.com/questions/3286675/readelf-like-tool-for-mac-os-x