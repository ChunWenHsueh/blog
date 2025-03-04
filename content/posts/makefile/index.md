---
title: "Introduction to Makefile"
date: 2025-02-04T23:58:55-07:00
draft: false
showToc: true
TocOpen: true
categories: [CI/CD]
---

## Why make?

Make is a build automation tool that automatically builds executable programs and libraries from source code by reading files called Makefiles, which specify how to derive the target program. It avoids the need to manually type everything out every time you want to compile your code.

## Syntax

Make has the following syntax:

```make
target: dependencies
    command
```

For example, if you have a file called `hello.c` and you want to compile it into an executable called `hello`, you can write a Makefile like this:

```make
hello: hello.c
    gcc hello.c -o hello
```

This Makefile has one target, `hello`, which depends on `hello.c`. The command to build the target is `gcc -o hello hello.c`. We could type `make hello` in the terminal to build the target.

## More than one file?

If `hello.c` depends on `foo.c` and `bar.c`, you can write a Makefile like this:

```make
hello: hello.c foo.o bar.o
    gcc hello.c foo.o bar.o -o hello

foo.o: foo.c
    gcc -c foo.c -o foo.o

bar.o: bar.c
    gcc -c bar.c -o bar.o
```

When we compile using `gcc -c`, it compiles the source file into an object file instead of an executable. The `.o` file is a intermediate file that contains machine code but is not yet linked into an executable.

When we run `make hello`, `make` will check if everything is up to date. If `hello.c` is newer than `hello`, `make` will run the command to build `hello`. If `foo.c` is newer than `foo.o` (or if `foo.o` doesn't exist), `make` will run the command to build `foo.o`, but you need to provide the command to build `foo.o` in the Makefile.

## Clean up

When we want to publish our code, we don't want to include the object files or the executable files. We can add a target called `clean` to remove the object files:

```make
hello: hello.c foo.o bar.o
    gcc hello.c foo.o bar.o -o hello

foo.o: foo.c
    gcc -c foo.c -o foo.o

bar.o: bar.c
    gcc -c bar.c -o bar.o

clean:
    rm *.o main
```

Now we can run `make clean` to remove the object files and executable files.

## Two main functions?

If we have two main functions in your code, `hello.c` and `world.c`, we can't compile them into one executable. We can compile them into two separate executables like this:

```make
all: hello world

hello: hello.c foo.o bar.o
    gcc hello.c foo.o -o hello

world: world.c foo.o bar.o
    gcc world.c bar.o -o world

foo.o: foo.c
    gcc -c foo.c -o foo.o

bar.o: bar.c
    gcc -c bar.c -o bar.o

clean:
    rm *.o hello world
```

Now we can run `make all` to build both `hello` and `world`.

## Constants

If we decide to change the compiler from `gcc` to `clang`, we would have to change every instance of `gcc` in the Makefile. We can use constants to avoid this:

```make
CC = gcc

all: hello world

hello: hello.c foo.o bar.o
    $(CC) hello.c foo.o -o hello

world: world.c foo.o bar.o
    $(CC) world.c bar.o -o world

foo.o: foo.c
    $(CC) -c foo.c -o foo.o

bar.o: bar.c
    $(CC) -c bar.c -o bar.o

clean:
    rm *.o hello world
```

This also works for flags:

```make
CC = gcc
DBGFLAGS = -g -D_DEBUG_ON_
OPTFLAGS = -O2

hello_opt: hello.c foo_opt.o
    $(CC) $(OPTFLAGS) hello.c foo_opt.o -o hello_opt

foo_opt.o: foo.c
    $(CC) $(OPTFLAGS) -c foo.c -o foo_opt.o

hello_dbg: hello.c foo_dbg.o
    $(CC) $(DBGFLAGS) hello.c foo_dbg.o -o hello_dbg

foo_dbg.o: foo.c
    $(CC) $(DBGFLAGS) -c foo.c -o foo_dbg.o
```
