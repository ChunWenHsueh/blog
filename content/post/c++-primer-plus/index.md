---
title: "C++ Primer Plus notes"
date: 2024-01-05T12:00:00+08:00
draft: false
math: true
---

## Chapter 4

### Union

union is a data structure that can store different types of data, but it can only store one type of data at a time. The size of union is the size of its largest data.

```c++
union one4all
{
    int int_val;
    long long_val;
    double double_val;
};

one4all pail;
pail.int_val = 15; // store an int
cout << pail.int_val;
pail.double_val = 1.38; // store a double, int value is lost
cout << pail.double_val;
```

### Enumerations

```c++
enum spectrum {red, orange, yellow, green, blue, violet, indigo, ultraviolet};

spectrum band; // band a variable of type spectrum
band = blue; // valid, blue is an enumerator
band = 2000; // invalid, 2000 not an enumerator
```

Enumarators are integer type, and can be converted to `int`, but not reversely.

```c++
int color = blue; // valid, spectrum type promoted to int
band = 3; // invalid, int not converted to spectrum
band = orange + red; // not valid, but a little tricky, int not converted to spectrum
band = orange; // valid
color = 3 + red; // valid, red converted to int
band = spectrum(3); // typecast 3 to type spectrum
```

In practice, enumerations are used more often as a way of defining related symbolic constants than as a means of defining new types.

```c++
enum bits{one = 1, two = 2, four = 4, eight = 8};
enum bigstep{first, second = 100, third}; // first = 0, third = 101
enum {zero, null = 0, one, numero_uno = 1}; // zero = 0, one = 1
```

### Pointers

```c++
int array[3] = {1, 2, 3};
int *ptr = array;
// array = &array[0] = address of first element of array
// &array = address of whole array = int (*ptr) [3] = pointer that points to array-of-3-ints
```

`array` is the address of a 4-byte block of memory, `&array` is the address of a 12-byte block of memory.  
`array + 1` adds 4 to the address value, `&array + 1` adds 12 to the address value  

A way to describe the type of variable is to remove the variable name, for example, `short (*pas)[20]`, `pas` points to array of 20 shorts, the type of `pas` is `short (*)[20]`


`sizeof(array_name)` = size of the array  
`sizeof(pointer_name)` = size of the pointer

```c++
int *pt = new int;
delete pt;
delete [] pt; // effect is undefined, don't do it

int *ps = new int [10];
delete [] ps;
delete ps; // effect is undefined, don't do it
```

1. Use `delete []` if you used `new []` to allocate an array.
2. Use `delete` (no brackets) if you used `new` to allocate a single entity.

## Chapter 5: Loops and Relational Expressions

### Prefix and Postfix increment

In prefix increment `++i`, it increments the value and return the value. However, in postfix increment `i++`, it copys the value, increments the value, then returns the copy of the value.  
For classes, the prefix version is more efficient than the postfix version.

### Type Aliases
```c++
#define BYTE char // preprocessor replaces BYTE with char
typedef char btye // makes byte an alias for char
// typedef typeName aliasName
typedef char * byte_pointer; // pointer to char type
```

It's better to use `typedef` instead of `#define`. Consider the following:

```c++
#define FLOAT_POINTER float *
FLOAT_POINTER pa, pb;

// preprocessor substitution converts to
float * pa, pb; // pa a pointer to float, pb just a float
```

`typedef` approach doesn't have that problem.

## Chapter 7: Functions: C++’s Programming Modules

### Pointers and `const`

```c++
int age = 39;
const int * pt = &age;
*pt += 1; // INVALID because pt points to a const int
cin >> *pt; // INVALID for the same reason
age = 20; // VALID because age is not declared to be const
```

Assigning the address of a `const` variable to a pointer-to-const is valid, however, assigning to regular pointer is not.

```c++
const float g_earth = 9.80;
const float * pe = &g_earth; // VALID

const float g_moon = 1.63;
float * pm = &g_moon; // INVALID
```

`const` doesn't prevent changing the value of `pt`, it prevents changing the value which `pt` points to.

```c++
int age = 39;
const int * pt = &age;
int sage = 80;
pt = &sage; // okay to point to another location
```

If you want to prevent changing the value of `pt`, write `DataType * const VariableName = &Data`.

```c++
int sloth = 3;
const int * ps = &sloth; // a pointer to const int
int * const finger = &sloth; // a const pointer to int
*finger = 4; // VALID
```

`finger` and `*ps` are both const, and `*finger` and `ps` are not const.

### Functions and Two-Dimensional Arrays

```c++
int data[3][4] = {{1,2,3,4}, {9,8,7,6}, {2,4,6,8}};
```

`data` is an array with three elements. The first element is an array of four `int` values. Hence, the type of `data` is pointer-to-array-of-four-int, so an appropriate prototype would be:

```c++
int sum(int (*ar2)[4], int size);
int sum(int ar2[][4], int size); // VALID, more readable
int sum(int *ar2[4], int size); // INVALID
```

Now the `sum` function works with arrays with four columns.

```c++
int a[100][4];
int b[6][4];
...
int total1 = sum(a, 100); // sum all of a
int total2 = sum(b, 6); // sum all of b
int total3 = sum(a, 10); // sum first 10 rows of a
int total4 = sum(a+10, 20); // sum next 20 rows of a
```

### Pointers to Functions

Funcitons also have addresses. Pointers to functions are useful because we can pass in different functions in different times.

Here’s what a declaration of an appropriate pointer type looks like:

```c++
double pam(int); // prototype
double (*pf)(int); // pf points to a function that takes
// one int argument and that
// returns type double

double *pf(int); // pf() a function that returns a pointer-to-double
```

We can use two ways to invoke a function with function pointers.

```c++
double pam(int);
double (*pf)(int);
pf = pam; // pf now points to the pam() function
double x = pam(4); // call pam() using the function name
double y = (*pf)(5); // call pam() using the pointer pf

double y = pf(5); // also call pam() using the pointer pf
// however, the previous one provides a visual reminder that the code is using a function pointer
```

## Chapter 8: Adventures in Functions

### Temporary Variables, Reference Arguments, and `const`

When the reference parameter is a `const`, the compiler generates a temporary variable when:

1. The actual argument is the correct type but isn’t an lvalue
2. The actual argument is of the wrong type, but can be conoverted to the corret type

lvalue is a data object that can be referenced by address, such as variables, array elements, stucture members. Non-lvalue include literal constants and expressions with multiple terms.

```c++
double refcube(const double &ra){
    return ra * ra * ra;
}
double side = 3.0;
double * pd = &side;
double & rd = side;
long edge = 5L;
double lens[4] = { 2.0, 5.0, 10.0, 12.0};
double c1 = refcube(side); // ra is side
double c2 = refcube(lens[2]); // ra is lens[2]
double c3 = refcube(rd); // ra is rd is side
double c4 = refcube(*pd); // ra is *pd is side
double c5 = refcube(edge); // ra is temporary variable, long coverted to double
double c6 = refcube(7.0); // ra is temporary variable, literal constants
double c7 = refcube(side + 10.0); // ra is temporary variable, multiple terms
```

Note that if `refcude` does not use constant references, c5, c6, c7 will show error. If there is no such rule, this will happen:

```c++
void swapr(int & a, int & b) // use references
{
    int temp;
    temp = a; // use a, b for values of variables
    a = b;
    b = temp;
}
long a = 3, b = 5;
swapr(a, b); // swapping temporary variable instead of swapping a and b
```

In short, if the intent of a function with reference arguments is to modify variables passed as arguments, situations that create temporary variables thwart that purpose. Adding `const` means that we are not modifying them, so temporary variable cause no harm. 

In C++11, there is a second kind of reference, called an rvalue reference. It's declared using `&&`:

```c++
double && rref = std::sqrt(36.00); // not allowed for double &
double j = 15.0;
double && jref = 2.0* j + 18.5; // not allowed for double &
std::cout << rref << '\n'; // display 6.0
std::cout << jref << '\n'; // display 48.5;
```

The original reference type (`&`) is now called an lvalue reference.

### Why Return a Reference?

```c++
int & square(int &i){
    i *= i;
    return i;
}
int x = 2;
int y = square(x); // note that x and y have different address
int &z = square(x); // x and z have same address
```

If `square()` returned `int` instead of `int &`, this could involve copying the entire structure to a temporary location and then copying that copy to `y`. But with a reference return value, `x` is copied directly to `y`, a more efficient approach.

### Being Careful About What a Return Reference Refers To

Avoid these lines:

```c++
const free_throws & clone2(free_throws & ft)
{
    free_throws newguy; // first step to big error
    newguy = ft; // copy info
    return newguy; // return reference to copy
}
```

It returns a reference to a temporary variable. The simplest way to avoid is to return a reference that was passed as an argument, like the `square()` function above. 

Another way is to use `new` to create new storage.

```c++
const free_throws & clone(free_throws & ft)
{
    free_throws * pt;
    *pt = ft; // copy info
    return *pt; // return reference to copy
}
```

However, it is easy to forget to use `delete` later.

### Function Overloading

```c++
void print(const char * str, int width); // #1
void print(double d, int width); // #2
void print(long l, int width); // #3
void print(int i, int width); // #4
void print(const char *str); // #5

print("Pancakes", 15); // use #1
print("Syrup"); // use #5
print(1999.0, 10); // use #2
print(1999, 12); // use #4
print(1999L, 15); // use #3

unsigned int year = 3210;
print(year, 6); // ambiguous call, error
// If the only print() prototype were #2, then print(year, 6) will convert to type double
```

The function-matching process does discriminate between const and non-const variables.

```c++
void dribble(char * bits); // overloaded
void dribble (const char *cbits); // overloaded
void dabble(char * bits); // not overloaded
void drivel(const char * bits); // not overloaded

const char p1[20] = "How's the weather?";
char p2[20] = "How's business?";
dribble(p1); // dribble(const char *);
dribble(p2); // dribble(char *);
dabble(p1); // no match
dabble(p2); // dabble(char *);
drivel(p1); // drivel(const char *);
drivel(p2); // drivel(const char *); since it's valid to assign non-const value to a const variable, but not vice versa

void stove(double & r1); // matches modifiable lvalue
void stove(const double & r2); // matches const lvalue
void stove(double && r3); // matches rvalue

double x = 55.5;
const double y = 32.0;
stove(x); // calls stove(double &)
stove(y); // calls stove(const double &)
stove(x+y); // calls stove(double &&)
// If we omit the stove(double &&) function, then stove(x + y) will call the stove(const double &) funciton instead
```

### Function Templates

Let's say we want to create `swap()` function that can swap two same type of data. One approach is to duplicate the function and overload it, however, it's a waste of time and if we make changes to one function, we have to change every functions.

We can set up a swapping template like this:
```c++
template <typename AnyType>
void Swap(AnyType &a, AnyType &b)
{
    AnyType temp;
    temp = a;
    a = b;
    b = temp;
}
```

The template does not create any functions.Instead, it provides the compiler with directions about how to define a function. If you want a function to swap `int`s, then the compiler creates a function following the template pattern, substituting `int` for `AnyType`.

### Overloaded Templates

If we want to create `swap()` function for C arrays, we can overload templates.

```c++
template <typename T>
void Swap(T &a, T &b)
{
    T temp;
    temp = a;
    a = b;
    b = temp;
}

template <typename T>
void Swap(T a[], T b[], int n)
{
    T temp;
    for (int i = 0; i < n; i++)
    {
        temp = a[i];
        a[i] = b[i];
        b[i] = temp;
    }
}
```

### Template Limitations

Let's say we want to compare two numbers in a template, we can simply write things like `a == b`, however, the `==` equal sign (and `>, <`) is not defined under C arrays or other data structures. We must provide specialized template funcitons for particular types.

### Explicit Specializations

Suppose we have a structure that looks like this:

```c++
struct job
{
    char name[40];
    double salary;
    int floor;
};
```

and we want to define a swap funciton for it, but only swapping `salary` and `floor`, then we can use explicit specializations. The prototype and definition for an explicit specialization should be preceded by `template <>` and should mention the specialized type by name.

```c++
// non template function prototype
void Swap(job &, job &);

// template prototype
template <typename T>
void Swap(T &, T &);

// explicit specialization for the job type
template <> void Swap<job>(job &, job &);

// swaps just the salary and floor fields of a job structure
template <> void Swap<job>(job &j1, job &j2) // specialization
{
    double t1;
    int t2;
    t1 = j1.salary;
    j1.salary = j2.salary;
    j2.salary = t1;
    t2 = j1.floor;
    j1.floor = j2.floor;
    j2.floor = t2;
}
```

For a given function name, you can have a non template function,a template function,and an explicit specialization template function,along with overloaded versions of all of these. A specialization overrides the regular template,and a non template function overrides both.

### Instantiations and Specializations

When the compiler uses the template to generate a funciton for a particular type, we call it $instantiation$ of the template. If we write this:

```c++
template <typename T>
void Swap(T &a, T &b)
{
    T temp;
    temp = a;
    a = b;
    b = temp;
}
...
Swap(i, j); // i, j are int type
...
```

Then this is called $implicit instantiation$. The compiler creates a function `Swap()` for `int` type because the program uses `Swap()` with `int` parameters.

There is also $ explicit instantiation$, which means that we can tell the compiler to generate a function for specific type for us.

```c++
template void Swap<int>(int, int); // explicit instantiation
```

It basically tells the compiler "Use the `Swap()` template to generate a function definition for the `int` type."

Note the difference between explicit instantiation and explicit specialization. The explicit specialization declaration has `<>` after the keyword `template`, whereas the explicit instantiation omits the `<>`.

```c++
template void Swap<int>(int &, int &); // explicit instantiation
template <> void Swap<int>(int &, int &); // explicit specialization
template <> void Swap(int &, int &); // explicit specialization
```

Explicit specialization tells the compiler "Don’t use the `Swap()` template to generate a function definition. Instead, use a separate, specialized function definition explicitly defined for the `int` type."

### Which Function Version Does the Compiler Pick?

The compiler will pick the function by such ranking:

1. Exact match, with regular functions outranking templates
2. Conversion by promotion (for example, the automatic conversions of char and
short to int and of float to double)
3. Conversion by standard conversion (for example, converting int to char or long
to double)
4. User-defined conversions, such as those defined in class declarations

For example,

```c++
may('B'); // actual argument is type char

void may(int); // #1
float may(float, float = 3); // #2
void may(char); // #3
char * may(const char *); // #4
char may(const char &); // #5
template<class T> void may(const T &); // #6
template<class T> void may(T *); // #7
```

#4 and #7 are not viable. #1 is better than #2, since `char`-to-`int` is a promotion, whereas `char`-to-`float` is a standard conversion. #3, #5 and #6 are better than #1 since they are exact matches. #3 and #5 are better than #6 because #6 is a template. However, what happens if we have #3 and #5 at the same time? Most of the time, two exact matches are an error, however, special cases are exceptions to this rule.

### Exact Matches and Best Matches

Suppose you have the following code:

```c++
struct blot {int a; char b[10];};
blot ink = {25, "spots"};
...
recycle(ink);

// the following prototypes are exact matches
void recycle(blot); // #1 blot-to-blot
void recycle(const blot); // #2 blot-to-(const blot)
void recycle(blot &); // #3 blot-to-(blot &)
void recycle(const blot &); // #4 blot-to-(const blot &)
```

Pointers and references to non-`const` data are preferentially matched to non-`const` pointer and reference parameters. If only Functions #3 and #4 were available in the `recycle()` example, #3 would be chosen because `ink` wasn’t declared as `const`.

However, this discrimination between `const` and non-`const` applies just to data referred to by pointers and references. That is, if only #1 and #2 were available, you would get an ambiguity error.

If we have two template functions with exact matches, the more specialized is the better one. For example:

```c++
template <class Type> void recycle (Type t); // #1
template <class Type> void recycle (Type * t); // #2

struct blot {int a; char b[10];};
blot ink = {25, "spots"};
...
recycle(&ink); // address of a structure
```

The `recycle(&ink)` call matches Template #1, with `Type` interpreted as `blot *`.The `recycle(&ink)` function call also matches Template #2, this time with `Type` being `ink`. In Template #2, `Type` was already specialized as a pointer, hence it is "more specialized."   

### Making Your Own Choices

```c++
template<class T>
T lesser(T a, T b) // #1
{
    ...
}
int lesser (int a, int b) // #2
{
    ...
}

int m = 20;
int n = -30;
double x = 15.5;
double y = 25.9;
cout << lesser(m, n) << endl; // use #2
cout << lesser(x, y) << endl; // use #1 with double
cout << lesser<>(m, n) << endl; // use #1 with int, tells compiler to choose a template function
cout << lesser<int>(x, y) << endl; // use #1 with int,  explicit instantiation
```

### What’s That Type?

```c++
template<class T1, class T2>
void ft(T1 x, T2 y)
{
    ...
    ?type? xpy = x + y; // what is the type of xpy
    ...
}
```

What should the type for `xpy` be? We can use `decltype` keyworld as a solution.

```c++
// decltype(expression) var;
decltype(x + y) xpy = x + y; // make xpy the same type as x + y
```
`decltype` should follow these rules:

1. If `expression` has no additional parentheses, then `var` is the same type as the identifier

2. If `expression` is a funciton call, then `var` is the same type as the function return type. Note that there's no need to actually call the funciton.

3.  `decltype` with extra parentheses preserves references from the original expression

4. If none of the preceding cases apply, `var` is the same type as `expression`

```c++
double x = 5.5;
double y = 7.9;
double &rx = x;
const double * pd;
decltype(x) w; // w is type double
decltype(rx) u = y; // u is type double &
decltype(pd) v; // v is type const double *

long indeed(int);
decltype (indeed(3)) m; // m is type long

double xx = 4.4;
decltype ((xx)) r2 = xx; // r2 is double &
decltype(xx) w = xx; // w is double (Stage 1 match)

int j = 3;
int &k = j
int &n = j;
decltype(j+6) i1; // i1 type int
decltype(100L) i2; // i2 type long
decltype(k+n) i3; // i3 type int;
```

Note that we can use `typedef` and `decltype` at the same time.

```c++
typedef decltype(x + y) xytype;
xytype xpy = x + y;
```

### Alternative Function Syntax

```c++
template<class T1, class T2>
?type? gt(T1 x, T2 y)
{
    ...
    return x + y;
}
```

What if the problem appears in return type? In this case, we cannot use ` decltype(x + y)` for the return type, since parameters `x` and `y` have not been declared. The `decltype` has to come after the parameters are declared. Instead, we can write:

```c++
template<class T1, class T2>
auto gt(T1 x, T2 y) -> decltype(x + y)
{
    ...
    return x + y;
}
```

## Chapter 9: Memory Models and Namespaces

### Separate Compilation

We can divide the program into three parts:

1. A header file that contains the structure declarations and prototypes for functions that use those structures
2. A source code file that contains the code for the structure-related functions
3. A source code file that contains the code that calls the structure-related functions

However, this creates new problems. For example, if you had a function definition in a header file and two other files that are part of a single program included it, you would wind up with two definitions of the same function, which is an error.

Here are some things commonly found in header files:

* Function prototypes
* Symbolic constants defined using `#define` or `const`
* Structure declarations
* Class declarations
* Template declarations
* Inline functions


Here is an example of seperating a program into three files:

```c++
// coordin.h -- structure templates and function prototypes
// structure templates
#ifndef COORDIN_H_
#define COORDIN_H_
struct polar
{
    double distance; // distance from origin
    double angle; // direction from origin
};
struct rect
{
    double x; // horizontal distance from origin
    double y; // vertical distance from origin
};

// prototypes
polar rect_to_polar(rect xypos);
void show_polar(polar dapos);
#endif

// file1.cpp -- example of a three-file program
#include <iostream>
#include "coordin.h" // structure templates, function prototypes
using namespace std;
int main()
{
    rect rplace;
    polar pplace;
    cout << "Enter the x and y values: ";
    while (cin >> rplace.x >> rplace.y) // slick use of cin
    {
        pplace = rect_to_polar(rplace);
        show_polar(pplace);
        cout << "Next two numbers (q to quit): ";
    }
    cout << "Bye!\n";
    return 0;
}

// file2.cpp -- contains functions called in file1.cpp
#include <iostream>
#include <cmath>
#include "coordin.h" // structure templates, function prototypes
// convert rectangular to polar coordinates
polar rect_to_polar(rect xypos)
{
    using namespace std;
    polar answer;
    answer.distance =
    sqrt( xypos.x * xypos.x + xypos.y * xypos.y);
    answer.angle = atan2(xypos.y, xypos.x);
    return answer; // returns a polar structure
}
// show polar coordinates, converting angle to degrees
void show_polar (polar dapos)
{
    using namespace std;
    const double Rad_to_deg = 57.29577951;
    cout << "distance = " << dapos.distance;
    cout << ", angle = " << dapos.angle * Rad_to_deg;
    cout << " degrees\n";
}
```

### Header File Management

You should include a header file just once in a file. It's easy to include a header file multiple times accidentally. For example, you might use a header file that includes another header file.

We can use preprocessor `#ifndef` (if not defined) directive.

```c++
#ifndef COORDIN_H_
#define COORDIN_H_
// place include file contents here
#endif
```

The code above means "process the statements between the `#ifndef` and `#endif` only if the name `COORDIN_H_` has not been defined previously by the preprocessor #define directive."

### Storage Duration, Scope, and Linkage

C++ uses four different schemes for storing data,

* Automatic storage duration: Variables declared inside a function definition (including function parameters) will be created when program enters a function or a block and freed when executuion leaves the function or block.
* Static storage duration: Varialbes defined outside function definition or with the keyword `static` have static storage duration. They persist for the entire time  a program is running. 
* Thread storage duration: Variables declared with the `thread_local` keyword have storage that persists for as long as the containing thread lasts.
* Dynamic storage duration: Memory allocated by the `new` keyword persists until it is freed with the `delete` operator or until the program ends.

### Scope and Linkage

Scope describes how widely visible a name is in a file. Linkage describes how a name can be shared in different units (files).

### Static Duration Variables

```c++
int global = 1000; // static duration, external linkage (other files can see the variable)
static int one_file = 50; // static duration, internal linkage (only this file can see the varialbe)
int main()
{
    ...
}
void funct1(int n)
{
    static int count = 0; // static duration, no linkage (only funct1 can see the variable)
    int llama = 0;
    ...
}
void funct2(int q)
{
    ...
}
```

The static duration variables (`global, one_file, count`) persist when the program begins and execute until the program terminates.

### Initializing Static Variables

All static duration variables have the following initialization feature: all its bits set to 0. It is also called zero-initialization.

```c++
#include <cmath>
int x; // zero-initialization
int y = 5; // constant-expression initialization
long z = 13 * 13; // constant-expression initialization
const double pi = 4.0 * atan(1.0); // dynamic initialization
```

First, `x, y, z` and `pi` are zero-initialized. Then the compiler initialize `y` and `z` to `5` and `169`. `pi` will be initialized until the `atan()` function is linked and the program executes.

### Static Duration, External Linkage

```c++
// file01.cpp
extern int cats = 20; // definition because of initialization, the extern can be omitted
int dogs = 22; // definition
...

// file02.cpp
// use cats and dogs from file01.cpp
extern int cats; // referencing declaration
extern int dogs; // does not cause storage to be allocated
```

### Static Duration, Internal Linkagek

```c++
// file01
int errors = 20;

// file02
int errors = 5; // this is an error, since violates the one definition rule
```

If we want to use the same variable name, we should addd `static` keyword.

```c++
// file01
int errors = 20;

// file02
static int errors = 5; // this is an error, since violates the one definition rule
```

### Static Storage Duration, No Linkage

```c++
// reference: https://stackoverflow.com/questions/12186857/what-is-the-difference-between-static-local-variables-and-static-global-variable
void foo () {   
    static int x = 0;
    ++x;
    cout << x << endl;
}

int main (int argc, char const *argv[]) {
    foo();  // 1
    foo();  // 2
    foo();  // 3
    return 0;
}
```

The difference between static internal linkage and static local variable is 

* The name is only accessible within the function, and has no linkage
* It is initialised the first time execution reaches the definition, not necessarily during the program's initialisation phases

### More About `const`

Whereas a global variable has external linkage by default,a `const` global variable has internal linkage by default.

If, for some reason, you want to make a constant have external linkage, you can use the `extern` keyword to override the default internal linkage.

## Chapter 10: Objects and Classes

### `const` Member Functions

Consider the following code:

```c++
const Stock land = Stock("Kludgeforn Properties");
land.show(); // error
```

In `Stock::show()` function, we cannot guarantee that it will not modify the object. To solve the issue, we can add `const` at the end of the function when we declare or write definition for it.

```c++
void Stock::show() const{ // promises not to change the object
}
```

## Chapter 11: Working with Classes

### Operator Overloading

A common computing task is adding two arrays. We might write something like this:

```c++
for (int i = 0; i < 20; i++){
    evening[i] = sam[i] + janet[i];
}
```

We can overload the `+` operator so that you can do this:

```c++
evening = sam + janet;
```

For example, `operator+()` overloads the `+` operator and `operator*()` overloads the `*` operator. We can also write `operator[]()` to overload the `[]` operator, which is the array-indexing operator.

Let's say we have a `Time` class, and we want to use `+` operator to add two `Time` objects. We can write the following code:

```c++
class Time {
private:
    int hours;
    int minutes;
public:
    Time operator+(const Time &t) const{
        Time sum;
        sum.minutes = minutes + t.minutes;
        sum.hours = hours + t.hours + sum.minutes / 60;
        sum.minutes %= 60;
        return sum;
    };
    ...
    // other funcions in the class
    ...
};
```

Now we can simply write `time1 + time2` instead of `time1.add(time2)`, and it will be translated to `time1.operator+(time2)`.

### Introducing Friends

There are still some restrictions when overloading operators. For example, if we overload the `*` operator and write `time1 * 2.5`, it translates to `time1.operator*(2)`. However, `2.5 * time1` does not correspond to a member function since `2.5` is not a `Time` object.

We can write a nonmember function `Time operator*(double m, const Time &t)` to resolve this, but it raises a new problem: nonmember functions cannot access private data in a class. There is a special category of nonmenber functions, called $friends$, that can access private members of a class.

### Creating Friends

To create a friend function, we need to add `friend` keyword in the prefix of the declaration.

```c++
friend Time operator*(double m, const Time &t); // goes in class declaration
```

The friend function is not a member function although it is in class declaration, but it has the same access rights as a member function.

The definition should like this:

```c++
// no need to add friend keyword
// no need to write Time Time::operator*(double m, const Time &t) since it is not a member function
Time operator*(double m, const Time &t){
    Time result;
    long totalminutes = t.hours * mult * 60 +t. minutes * mult; // can access private members
    result.hours = totalminutes / 60;
    result.minutes = totalminutes % 60;
    return result;
}
```

Now, the statement `time2 = 2.5 * time1` translates to `time2 = operator*(2.5, time1)`

Actually, we can write it as a non-friend function. However, it is better to write it as a friend function, since it ties the function and the class interface together, and allows potential access to private data in the future.

```c++
Time operator*(double m, const Time &t){
    return t * m; // use t.operator*(m)
    // no need to access private members
}
```

### Overloading the `<<` Operator

Suppose `trip` is a `Time` object. To display `Time` values, we need to write `trip.show()` to print out the values. However, we could overload the `<<` operator and make `cout << trip` print out the values.

We can overload the operator this way:

```c++
void operator<<(ostream &os, const Time &t){
    os << t.hours << "hours, " << t.minutes << " minutes";
}
```

How about a more complex one, something like this: `cout << "Trip time: " << trip << "\n";`

Actually, `cout` is an `ostream` object, and the `ostream` class returns a reference to an `ostream` object when implementing the `<<` operator.

We just need to make `operator<<()` return a `ostream` object.

```c++
ostream& operator<<(ostrream &os, const Time &t){
    os << t.hours << "hours, " << t.minutes << " minutes";
    return os;
}
```

### A Vector Class

```c++
// vect.h -- Vector class with <<, mode state
#ifndef VECTOR_H_
#define VECTOR_H_
#include <iostream>
namespace VECTOR
{
    class Vector
    {
    public:
        enum Mode {RECT, POL};
    // RECT for rectangular, POL for Polar modes
    private:
        double x; // horizontal value
        double y; // vertical value
        double mag; // length of vector
        double ang; // direction of vector in degrees
        Mode mode; // RECT or POL
        // private methods for setting values
        void set_mag();
        void set_ang();
        void set_x();
        void set_y();
    public:
        Vector();
        Vector(double n1, double n2, Mode form = RECT);
        void reset(double n1, double n2, Mode form = RECT);
        ~Vector();
        double xval() const {return x;} // report x value
        double yval() const {return y;} // report y value
        double magval() const {return mag;} // report magnitude
        double angval() const {return ang;} // report angle
        void polar_mode(); // set mode to POL
        void rect_mode(); // set mode to RECT
        // operator overloading
        Vector operator+(const Vector & b) const;
        Vector operator-(const Vector & b) const;
        Vector operator-() const;
        Vector operator*(double n) const;
        // friends
        friend Vector operator*(double n, const Vector & a);
        friend std::ostream &
        operator<<(std::ostream & os, const Vector & v);
    };
} // end namespace VECTOR
#endif

// vect.cpp -- methods for the Vector class
#include <cmath>
#include "vect.h" // includes <iostream>
using std::sqrt;
using std::sin;
using std::cos;
using std::atan;
using std::atan2;
using std::cout;
namespace VECTOR
{
    // compute degrees in one radian
    const double Rad_to_deg = 45.0 / atan(1.0);
    // should be about 57.2957795130823
    // private methods
    // calculates magnitude from x and y
    void Vector::set_mag()
    {
        mag = sqrt(x * x + y * y);
    }
    void Vector::set_ang()
    {
        if (x == 0.0 && y == 0.0)
            ang = 0.0;
        else
            ang = atan2(y, x);
    }
    // set x from polar coordinate
    void Vector::set_x()
    {
        x = mag * cos(ang);
    }
    // set y from polar coordinate
    void Vector::set_y()
    {
        y = mag * sin(ang);
    }
    // public methods
    Vector::Vector() // default constructor
    {
        x = y = mag = ang = 0.0;
        mode = RECT;
    }
    // construct vector from rectangular coordinates if form is r
    // (the default) or else from polar coordinates if form is p
    Vector::Vector(double n1, double n2, Mode form)
    {
        mode = form;
        if (form == RECT)
        {
            x = n1;
            y = n2;
            set_mag();
            set_ang();
        }
        else if (form == POL)
        {
            mag = n1;
            ang = n2 / Rad_to_deg;
            set_x();
            set_y();
        }
        else
        {
            cout << "Incorrect 3rd argument to Vector() -- ";
            cout << "vector set to 0\n";
            x = y = mag = ang = 0.0;
            mode = RECT;
        }
    }
    // reset vector from rectangular coordinates if form is
    // RECT (the default) or else from polar coordinates if
    // form is POL
    void Vector:: reset(double n1, double n2, Mode form)
    {
        mode = form;
        if (form == RECT)
        {
            x = n1;
            y = n2;
            set_mag();
            set_ang();
        }
        else if (form == POL)
        {
            mag = n1;
            ang = n2 / Rad_to_deg;
            set_x();
            set_y();
        }
        else
        {
            cout << "Incorrect 3rd argument to Vector() -- ";
            cout << "vector set to 0\n";
            x = y = mag = ang = 0.0;
            mode = RECT;
        }
    }
    Vector::~Vector() // destructor
    {
    }
    void Vector::polar_mode() // set to polar mode
    {
        mode = POL;
    }
    void Vector::rect_mode() // set to rectangular mode
    {
        mode = RECT;
    }
    // operator overloading
    // add two Vectors
    Vector Vector::operator+(const Vector & b) const
    {
        return Vector(x + b.x, y + b.y);
    }
    // subtract Vector b from a
    Vector Vector::operator-(const Vector & b) const
    {
        return Vector(x - b.x, y - b.y);
    }
    // reverse sign of Vector
    Vector Vector::operator-() const
    {
        return Vector(-x, -y);
    }
    // multiply vector by n
    Vector Vector::operator*(double n) const
    {
        return Vector(n * x, n * y);
    }
    // friend methods
    // multiply n by Vector a
    Vector operator*(double n, const Vector & a)
    {
        return a * n;
    }
    // display rectangular coordinates if mode is RECT,
    // else display polar coordinates if mode is POL
    std::ostream & operator<<(std::ostream & os, const Vector & v)
    {
    if (v.mode == Vector::RECT)
        os << "(x,y) = (" << v.x << ", " << v.y << ")";
    else if (v.mode == Vector::POL)
    {
        os << "(m,a) = (" << v.mag << ", "
        << v.ang * Rad_to_deg << ")";
    }
    else
        os << "Vector object mode is invalid";
        return os;
    }
} // end namespace VECTOR
```

### Automatic Conversions and Type Casts for Classes

C++ provides the following type conversions for classes:

* A class constructor that has but a single argument serves as an instruction for converting a value of the argument type to the class type.

For example, if we have a constructor `Stonewt(double lbs)` for `Stonewt` class. Then `Stonewt myCat = 19.6` will convert `19.6` to a `Stonewt` object.

However, using `explicit` in the constructor declaration eliminates implicit conversions and allows only explicit conversions.

```c++
explicit Stonewt(double lbs); // no implicit conversions allowed

Stonewt myCat; // create a Stonewt object
myCat = 19.6; // not valid if Stonewt(double) is declared as explicit
mycat = Stonewt(19.6); // ok, an explicit conversion
mycat = (Stonewt) 19.6; // ok, old form for explicit typecast
```

* A special class member operator function called a $conversion function$ serves as an instruction for converting a class object to some other type. This conversion function is invoked automatically when you assign a class object to a variable of that type or use the type cast operator to that type.

```c++
class Stonewt
{
    ...
    // conversion functions
    explicit operator int() const;
};

Stonewt::operator int() { return int (pounds + 0.5); }

Stonewt wolfe(285.7);
int host = int (wolfe); // an explicit conversion
```

### Conversions and Friends

```c++
Stonewt operator+(const Stonewt & st1, const Stonewt & st2) // friend function
{
    double pds = st1.pounds + st2.pounds;
    Stonewt sum(pds);
    return sum;
}

Stonewt jennySt(9, 12);
double pennyD = 146.0;
Stonewt total;
total = pennyD + jennySt; // ok with friend function, but not member function
// convert pennyD to a Stonewt object
```

## Chapter 12: Classes and Dynamic Memory Allocation

### A Review Example and Static Class Members

Let's take a look at a class example:

```c++
// strngbad.h -- flawed string class definition
#include <iostream>
#ifndef STRNGBAD_H_
#define STRNGBAD_H_
class StringBad
{
    private:
        char * str; // pointer to string
        int len; // length of string
        static int num_strings; // number of objects
    public:
        StringBad(const char * s); // constructor
        StringBad(); // default constructor
        ~StringBad(); // destructor
        // friend function
        friend std::ostream & operator<<(std::ostream & os,
        const StringBad & st);
};
#endif

// strngbad.cpp -- StringBad class methods
#include <cstring> // string.h for some
#include "strngbad.h"
using std::cout;
// initializing static class member
int StringBad::num_strings = 0;
// class methods
// construct StringBad from C string
StringBad::StringBad(const char * s)
{
    len = std::strlen(s); // set size
    str = new char[len + 1]; // allot storage
    std::strcpy(str, s); // initialize pointer
    num_strings++; // set object count
    cout << num_strings << ": \"" << str
    << "\" object created\n"; // For Your Information
}
StringBad::StringBad() // default constructor
{
    len = 4;
    str = new char[4];
    std::strcpy(str, "C++"); // default string
    num_strings++;
    cout << num_strings << ": \"" << str
    << "\" default object created\n"; // FYI
}
StringBad::~StringBad() // necessary destructor
{
    cout << "\"" << str << "\" object deleted, "; // FYI
    --num_strings; // required
    cout << num_strings << " left\n"; // FYI
    delete [] str; // required
}
std::ostream & operator<<(std::ostream & os, const StringBad & st)
{
    os << st.str;
    return os;
}
```

Notice that we initializes the static `num_strings` member to `0` in the `.cpp` file instead of the header file. That's because we cannot initialize a static member variable inside the class declaration. Declaration only tells how to allocate the memory, but it doesn't allocate memory. For static members, we need to initialize outside the class declaration (but not `const static`).

### Special Member Functions

