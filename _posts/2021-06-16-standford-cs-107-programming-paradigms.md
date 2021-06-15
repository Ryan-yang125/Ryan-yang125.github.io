---
layout: post
title: "Stanford CS 107 Programming Paradigms"
subtitle: "Lecture 1-14"
date: 2021-06-16
author: "RyanYang"
header-img: "img/post-bg-default.jpg"
tags: [Notion,CS]
---

编程范式这门课分为四部分：C and Cpp (OO), Concurrency, Lisp(FP), Python

这是第一部分的，差不多算复习了一下 C + Cpp + Assembly language。

讲师真的🐂🍺，无ppt，无稿子，全程黑板上手写代码，二倍速的节奏非常舒服。

课程官网assignment, handout, video 都很全。看了下习题，前几章有趣但难度不大，concurrency和lisp做做差不多了，python懒得看了。

> Review of Stanford CS 107 Programming Paradigms Lecture 1-14

### Lec 1 - 4:

1. char ⇒ short int, just copy bits

```c
// normally fill 0
char ch = 'A'; // char: 1bytes, short: 2bytes
short n = ch; // 65

short n = 67;
char ch = n; // 'C'

// not all filled 0,look
short a = -1; // 11111111,11111111
int b = a; // 11111111,11111111,11111111,11111111

// but only one choice, as it's done all by digital cell
```

2. float / double  ⇒ int

```c
// implicit: bits:101 => int:5 => float:1.25 * 2 ^ 2 => bits
int a = 5;
float b = a; // 5.0

// explicit: bits: 101 => bits: 101
// &x means the beginning memory address of 5 => int *
// float * means we regards the (int *) as (float *)
// * means we just pick the float out, no matter what the bit pattern is
int x = 5;
float y = *(float *) (&x);

// notice the beginning memory, especially when bytes not the same
// see:
float f = 7.0;
short s = * (short *)(&f);
// means we not only pick the start 2 bytes but not the end
// versus means we will pick more two bytes what we have no idea of 

// implicit transfrom happens anywhere such as :
char a = 65; // bits => int => char => bits
// as the char / short / int have the same bit pattern so just copy bytes
```

3. struct

```c
struct fraction {
	int num;
	int denum;
};
fraction a;
a.num = 12;
a.denum = 13;
((fraction *)&(a.denum))->num = 45;
a.denum; // 45
// &a == (fraction *)&(a.num), just like the array below, or it's a diffrent types' array
```

4. array

```c
int a = 12;
int ar[10];
int b = 13;
print("%d", ar[-1]); // 13
print("%d", ar[-2]); // 12
print("%d", ar[10]); // 0
```

5. back to bit patterns

```c
// a generic swap function
void swap(void *vp1, void *vp2, int size) {
	char buf[size];
	memcpy(buf, vp1, size);
	memcpy(vp1, vp2, size);
	memcpy(vp2, buf, size);
}
```

The key point is, anything we do or compiler does itself, is to tell **the rule of how we comprehend the bits pattern**,  char / int / double with or not * is the rule.

look at this, a more generic search function for base type (expect pointer or struct with pointer)

```c
void *search(void *key, void *base, int size, int n) {
	for (int i = 0; i < n; i++) {
		void *elem = (char *)base + i * size; // a hack to compute with void *.
		if (memcmp(key, elem, size) == 0) {
			return elem; 
		}	
	}
	return NULL;
}
// a more generic
void *search(void *key, void *base, int size, int n, int (*cmp)(void *, void *)) {
		void *elem = (char *)base + i * size; // a hack to compute with void *.
		if (cmp(key, elem) == 0) {
			return elem; 
		}	
	}
	return NULL;
}
// so the point is how to compare two element
int cmp(void *vp1, void *vp2) {
	int size = 4
	return memcmp(vp1, vp2, size) // 1 / 2 / 4 / 8 for basic types, what about others?
}
```

Lec 5 - 7:

1. implement a stack in c with struct like c++ with class.

```c
// stack.h
typeof struct {
	int *elems;
	int logiclLen;
	int allocLen;
} stack;

void StackNew(stack *s, int len); // it's 'this' in c++ class
void StackDipose(stack *s);
void StackPush(stack *s, int elem);
int StackPop(stack *s);

```

```c
// stack.c

void StackNew(stack *s, int len) {
	s -> logicLen = 0;
	s -> allocLen = len;
	s -> elems = malloc(len * sizeof(int));
	// test
	assert(s -> elems != NULL);
}

void StackDispose(stack *s) {
	free(s -> elems);
}

void StackPush(stack *s, int elem) {
	if (s -> logicLen == s -> allocLen) {
		// static void reGrow(stack *s);
		// static means only this func can be used in this .o file,
		// a little bit like private, but in case many files implement it's owe reGrow
		// and compiler not know to use which.
		s -> allocLen *= 2;
		// simply expand or 
		// 1. find a new space in heap
		// 2. memcpy(new, old, oldSize)
		// 3. free old
		// 4. return new
		// it's O(M), M is the heap space, as we do search in firtst step
		s -> elems = realloc(s -> elems, s -> allocLen * sizeof(int));
		assert(s -> elems != NULL);
	}
	// void *target = (char *)(s -> elems) + s -> logicLen * s -> elemSize
	s -> elems[s -> logicLen] = elem;
	s -> logicLen += 1;
}

int StackPop() {
	assert(s -> logicLen > 0);
	s -> logicLen -= 1;
	return s -> elems[s -> logicLen];
}
```

```c
// main.c
#include<stack.h>
stack s;
StackNew(&s, 4);
```

a more generic type just means we need to do address compute(char*) and memcpy by ourselves.

Lec 8:

stack is managed by hardware (just push and pop in assembly), as heap is  by software, which means we pick a memory space as `heap`, implement some algorithms to manage it efficiently and expose functions  `malloc, realloc, free` written by c. So `heap` is just a byte sandbox.

head:

The interesting part is how we manage memory when the space is not enough.

One method is to use a link to connect the end of used block (the start of unused block).

**memory shrink is to use **(handle) and lock.**

stack: just  for variables, push and pop with function call, nothing special.

Lec 9 - 11:

Assembly: load ⇒ alu ⇒ store

function call: caller(调用者传入参数) ⇒ PC（函数调用的下一条指令地址） ⇒ callee （函数内部变量）

PC: always instructions address to execute

SP: always stack top

Lec 12 -14:

preprocessor: a.c ⇒ a.c with replace all `#define` by a hash-set , which means just a text to another.

**macro:**

```c
#define PI 3.14 // param
#define MAX(a,b) a > b ? a : b  // expression
// assert.h
#ifdef NODEBUG
	#define assert(cond) (void) 0
#else
	#define assert(cond) (cond) ? (void 0) : fprintf(...); exit(0)
#endif
```

for header:

```c
#ifndef 
#define
#endif
```

```bash
clang -E
```

`#include<header.h>` is replace in **preprocessor**, and in compile stage (⇒ .o)，just tell the compiler the rule of some functions, params and doesn't generate any assembly code.

what's more, when `link`, the compiler just try to find the function in library, so we can write code like this.

```c
unsigned long strlen(const char *str, int a, int b, int c);
int printf(const char *str, ...); // push param from right - left
int main()
{
    int x = strlen("hahah", 10, 10, 10);
    printf("%d\n", x, x, x, x);
}
```

```bash
clang main.c -o main && ./main
// output
hello.c:3:15: warning: incompatible redeclaration of library function 'strlen' [-Wincompatible-library-redeclaration]
unsigned long strlen(const char *str, int a, int b, int c);
              ^
hello.c:3:15: note: 'strlen' is a builtin with type 'unsigned long (const char *)'
hello.c:8:23: warning: data argument not used by format string [-Wformat-extra-args]
    printf("%d\n", x, x, x, x);
           ~~~~~~     ^
2 warnings generated.
5
```

`incompatible redeclaration of library function 'strlen'`

but still running !

let's get deeper in it:

```c
int memcmp(void *vp);
// actuall it's int memcmp(const void *vp1, const void *vp2, unsigned long size);
int a = 15;
int m = memcmp(&a);
// in compile => sp = sp+4 for param
// but after linking and run
// we need 3 params, so just sp = sp - 12.
// which means we wrongly use the last function call params.
```

C在编译后的汇编调用函数签名:`CALL memcmp` , 因此只有一个同名函数

C++会根据参数生产不同签名: `CALL memcmp_int_void_p`, 因此重载

About Errors:

Segment Fault: *(NULL) or some address can't be *

Baseline Fault: 

```c
void *a = __;
*(int *)a = 12;
// if a is in stack or heap, but not 4^
```

Lec 15-18 Concurrency 

[Lec 15 - 18 Concurrency](https://www.notion.so/Lec-15-18-Concurrency-4050cfa35a6c4773a8d64d2ae54d2bd7)

Lec 19 - 23 Schema

[Lec 19-23 Schema](https://www.notion.so/Lec-19-23-Schema-09b3d10d4e244436bed555d564f6b502)