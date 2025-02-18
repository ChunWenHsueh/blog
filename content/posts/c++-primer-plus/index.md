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

When the compiler uses the template to generate a funciton for a particular type, we call it ***instantiation*** of the template. If we write this:

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

Then this is called ***implicit instantiation***. The compiler creates a function `Swap()` for `int` type because the program uses `Swap()` with `int` parameters.

There is also ***explicit instantiation***, which means that we can tell the compiler to generate a function for specific type for us.

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

We can write a nonmember function `Time operator*(double m, const Time &t)` to resolve this, but it raises a new problem: nonmember functions cannot access private data in a class. There is a special category of nonmenber functions, called ***friends***, that can access private members of a class.

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

[Vector class]({{< ref "/code/vector" >}}) 

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

* A special class member operator function called a ***conversion function*** serves as an instruction for converting a class object to some other type. This conversion function is invoked automatically when you assign a class object to a variable of that type or use the type cast operator to that type.

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

The `StringBad` class seems fine, however, there's actually some problems. Let's look at an example:

```c++
void callme(StringBad sb){
    cout << "String passed by value:\n";
    cout << " \"" << sb << "\"\n";
}

StringBad headline("Celery Stalks at Midnight");
callme(headline);
cout << headline << endl; // print out something strange
```

When we pass by value, the function will create a temporary `StringBad` object that points to the same address as the `headline` does. After the `callme` function, the destructor will be called, causing the pointer to be released, which messes up the original string.

### Special Member Functions

The problems with the `StringBad` class stem from ***special member functions***. C++ provides the following member functions:

* A default constructor if you define no constructors
* A default destructor if you don’t define one
* A copy constructor if you don’t define one
* An assignment operator if you don’t define one
* An address operator if you don’t define one

#### Default Constructor

If we omit any constructors, the compiler will call the default constructor. If we define a constructor with no arguments, or its arguments have default values, it will become the default constructor.

However, we can have only one default constructor; otherwise, there will be ambiguity.

```c++
Class_name(){}; // default constructor
Class_name(){ // default constructor with no arguments
    member = 0;
}
Class_name(){int n = 0}{ // default constructor with default argument value
    member = n;
}
```

#### Copy Constructor

A copy constructor for a class normally has this prototype: `Class_name(const Class_name &);`. The default copy constructor performs a member-by-member copy of the nonstatic members. Each member is copied by value.

A copy constructor is invoked whenever a new object is created and initialized to an existing object of the same kind.

```c++
// given that motto is a StringBad object
StringBad ditto(motto); // calls StringBad(const StringBad &)
StringBad metoo = motto; // calls StringBad(const StringBad &)
StringBad also = StringBad(motto); // calls StringBad(const StringBad &)
StringBad * pStringBad = new StringBad(motto); // calls StringBad(const StringBad &)
```

The middle two declarations may use a copy constructor directly to create `metoo` and `also`, or they may use a copy constructor to generate temporary objects whose contents are then assigned to `metoo` and `also`.

##### Back to Stringbad: Where the Copy Constructor Goes Wrong

The `callme` function creates a temporary variable that invokes the copy constructor, and this variable points to the same address as our original object. We should provide a copy constructor for the `StringBad` class.

```c++
StringBad::StringBad(const StringBad & st)
{
    num_strings++; // handle static member update
    len = st.len; // same length
    str = new char [len + 1]; // allot space
    std::strcpy(str, st.str); // copy string to new location
    cout << num_strings << ": \"" << str
    << "\" object created\n"; // For Your Information
}
```

#### Assignment Operator

Assignment Operator has the following prototype:`Class_name & Class_name::operator=(const Class_name &);`.


An overloaded assignment operator is used when you assign one object to another existing object:

```c++
StringBad headline("Celery Stalks at Midnight");
StringBad knot;
knot = headline; // assignment operator invoked
```

Like a copy constructor,an implicit implementation of an assignment operator performs a member-to-member copy.

##### Back to Stringbad: Where the Assignment Goes Wrong

We should also provide a assignment operator for the `StringBad` class. The implementation is similar to that of the copy constructor, but there are some differences:

* Use `delete []` to free the old string
* The function should protect against assigning an object to itself
* The function returns a reference to the invoking object

Here is the implementation:

```c++
StringBad & StringBad::operator=(const StringBad & st)
{
    if (this == &st) // object assigned to itself
        return *this; // all done
    delete [] str; // free old string
    len = st.len;
    str = new char [len + 1]; // get space for new string
    std::strcpy(str, st.str); // copy the string
    return *this; // return reference to invoking object
}
``` 

### The New, Improved `String` Class

We still need a few functions to improve the `String` class, such as comparison members or bracket notation.

Here is an improved `String` class:  
[String class]({{< ref "/code/string" >}}) 

For static class member functions, it can only use static data members, since it is not associated with a particular object.

We added `String & String::operator=(const char * s)` to improve efficiency. Consider the following code:

```c++
String name;
char temp[40];
cin.getline(temp, 40);
name = temp; // use constructor to convert type
```

The program will do the following steps:

* Use `String(const char *)` constructor to construct a temporary `String` object
* Use `String & String::operator=(const String &)` to copy the object to `name`
* call `~String()` destructor to delete the temporary object

Which is slower than copying the C string directly to the `String` object.

### Looking Again at Placement `new`

Using placement `new` is different from using regular `new` to allocate memory for objects. If we want to destroy the object that was allocated by placement `new`, we must call the destructor explicitly to do so.

```c++
char * buffer = new char[512]; // get a block of memory
MyClass *ptr;
ptr = new (buffer) MyClass; // place object in buffer
ptr->~MyClass(); // call the destructor explicitly
delete [] buffer; // Free the raw memory allocated earlier
```

We have to call the destructor instead of delete. The reason is that `delete` does two things:

* It calls the destructor of the object
* It attempts to free the memory where the object was located

However, when using the placement `new`, the memory location is provided by us, and therefore `delete` might not know how to correctly deallocate it, leading to undefined behavior.

### Member Initializer List

Let's say we have a class that looks like this:

```c++
class Queue{
private:
    enum {Size = 10};
    Node * front; // pointer to front of Queue
    Node * rear; // pointer to rear of Queue
    int items; // current number of items in Queue
    const int qsize; // maximum number of items in Queue
public:
    Queue(int qs)
    {
        front = rear = NULL;
        items = 0;
        qsize = qs; // not acceptable!
    }
};
```

We want to assign `qs` to the `qsize` variable, however, `const` variable can only be initialized to a value, not assigned to a value.

Calling a constructor creates an object before the code within the brackets is executed. Thus, the constructor will first allocate space for the four member variables, then enter the brackets and assign values into allocated space.

We can use ***member initializer list*** syntax to initialize member variables.

```c++
Queue(int qs) : qsize(qs) // initialize qsize to qs, instead of assigning
{
    front = rear = NULL;
    items = 0;
}
// or
Queue(int qs) : qsize(qs), front(NULL), rear(NULL), items(0)
{
}
```
Only constructors can use this initializer-list syntax. We also have to use it for class members that are declared as references.

In-class initialization is equivalent to using a member initialization list in the constructors:

```c++
class Classy{
    int mem1 = 10; // in-class initialization
}
// is equivalent to
Classy(): mem1(10){
    ...
}
```

The items are initialized in the order which they are decalred, not in the order in which they appear in the initializer list.

## Chapter 13: Class Inheritance

### Beginning with a Simple Base Class

```c++
// tabtenn0.h -- a table-tennis base class
#ifndef TABTENN0_H_
#define TABTENN0_H_
#include <string>
using std::string;
// simple base class
class TableTennisPlayer
{
private:
    string firstname;
    string lastname;
    bool hasTable;
public:
    TableTennisPlayer (const string & fn = "none",
        const string & ln = "none", bool ht = false);
    void Name() const;
    bool HasTable() const { return hasTable; };
    void ResetTable(bool v) { hasTable = v; };
};
#endif

//tabtenn0.cpp -- simple base-class methods
#include "tabtenn0.h"
#include <iostream>
TableTennisPlayer::TableTennisPlayer (const string & fn,
    const string & ln, bool ht) : firstname(fn),
        lastname(ln), hasTable(ht) {}

void TableTennisPlayer::Name() const
{
    std::cout << lastname << ", " << firstname;
}
```

Now we can create `RatedPlayer` class that derives from the `TableTennesPlayer` base class:

```c++
// RatedPlayer derives from the TableTennisPlayer base class
class RatedPlayer : public TableTennisPlayer
{
private:
    unsigned int rating; // add a data member
public:
    RatedPlayer (unsigned int r = 0, const string & fn = "none",
        const string & ln = "none", bool ht = false);
    RatedPlayer(unsigned int r, const TableTennisPlayer & tp);
    unsigned int Rating() const { return rating; } // add a method
    void ResetRating (unsigned int r) {rating = r;} // add a method
};
```

With public derivation, the public members of the base class become public members of the derived class. The private portions of a base class become part of the derived class, but they can be accessed only through public and protected methods of the base class.

### Constructors

When a program constructs a derived-class object, it first constructs the base-class object. We must use member initailizer list syntax to construct base-class object before we enter the body of the derived-class constructor.

```c++
RatedPlayer::RatedPlayer(unsigned int r, const string & fn,
    const string & ln, bool ht) : TableTennisPlayer(fn, ln, ht)
{
    rating = r;
}
```

We we omit calling a base-class constructor, the program uses the default constructor.

```c++
RatedPlayer::RatedPlayer(unsigned int r, const string & fn,
    const string & ln, bool ht)
{
    rating = r;
}
// is same as
RatedPlayer::RatedPlayer(unsigned int r, const string & fn,
    const string & ln, bool ht) : TableTennisPlayer()
{
    rating = r;
}
```

### Special Relationships Between Derived and Base Classes

A base-class pointer can point to a derivedclass object without an explicit type cast and that a base-class reference can refer to a derived-class object without an explicit type cast.

```c++
RatedPlayer player1(1140, "Mallory", "Duck", true);
TableTennisPlayer & rt = player;
TableTennisPlayer * pt = &player;
rt.Name(); // invoke Name() with reference
pt->Name(); // invoke Name() with pointer

TableTennisPlayer player2(player1);
// The exact match should be TableTennisPlayer(const RatedPlayer &) that doesn't exist
// but there is the implicit copy constructor TableTennisPlayer(const TableTennisPlayer &)
```

However,a base-class pointer or reference can invoke just base-class methods, not derived-class methods.

### Polymorphic Public Inheritance

`virtual` determines which method is used if the method is invoked by a reference or a pointer instead of by an object. If you don’t use the keyword virtual, the program chooses a method based on the reference type or pointer type.

For example, if we have a base-class `Brass` and a derived-class `BrassPlus`, and a virtual function `ViewAcct()` for both classes.

```c++
Brass dom("Dominic Banker", 11224, 4183.45);
BrassPlus dot("Dorothy Banker", 12118, 2592.00);
dom.ViewAcct(); // use Brass::ViewAcct()
dot.ViewAcct(); // use BrassPlus::ViewAcct()

Brass &b1_ref = dom;
Brass &b2_ref = dot;
b1_ref.ViewAcct(); // use Brass::ViewAcct()
b2_ref.ViewAcct(); // use BrassPlus::ViewAcct()
```

If we don't have `virtual` keyword for the function, the reference will use `Brass::ViewAcct()` in both line of code.

When a method is declared virtual in a base
class, it is automatically virtual in the derived class, but it is a good idea to document which functions are virtual by using the keyword `virtual` in the derived class declarations. 

### The Need for Virtual Destructors

It's also the usual practice to declare a virtual destructor for the base class. It ensures that the correct sequence of destructors is called.

```c++
Brass *ptr = new BrassPlus();
delete ptr; // calls BrassPlus destructor
// then automatically calls the base-class destructor
```

### How Virtual Functions Work

![Example image](/images/primer-plus-13.5.png)

The usual way compilers handle virtual functions is to add a hidden member to each object.The hidden member holds a pointer to an array of function addresses. Such an array is usually termed a virtual function table (vtbl). When we call a virtual function, the program looks at the vtbl address stored in an object and goes to the corresponding table of function addresses.

In short, using virtual functions has the following modest costs in memory and execution speed:

* Each object has its size increased by the amount needed to hold an address
* For each class, the compiler creates a table (an array) of addresses of virtual functions
* For each function call, there's an extra step of going to a table to look up an address

### Redefinition Hides Methods

Suppose we create something like the following:

```c++
class Dwelling
{
public:
    virtual void showperks(int a) const;
...
};
class Hovel : public Dwelling
{
public:
    virtual void showperks() const;
...
};

Hovel trump;
trump.showperks(); // valid
trump.showperks(5); // invalid
```

The new `showperks()` function that takes no arguments will hide the base class version that takes an `int` argument (it actually hides all base-class methods of the same name), instead of overloading the function.

If the base class declaration is overloaded, we need to redefine all the base-class version in the derived class.

```c++
class Dwelling
{
public:
    // three overloaded showperks()
    virtual void showperks(int a) const;
    virtual void showperks(double x) const;
    virtual void showperks() const;
    ...
};
class Hovel : public Dwelling
{
public:
    // three redefined showperks()
    virtual void showperks(int a) const;
    virtual void showperks(double x) const;
    virtual void showperks() const;
    ...
};
```

If we redefine just one version, the other two become hidden and cannot be used by objects of the derived class.

### Access Control: `protected`

The `protected` keyword is like `private` in outside world and `public` for derived class. It's useful for derived class to access to internal functions that are not available publicly.

### Abstract Base Classes (ABC)

Suppose we want to create two classes, `Circle` and `Ellipse`. Of course we can first write the `Ellipse` class and then write the `Circle` class by inheriting the `Ellipse` class, since a circle ***is-a*** ellipse. However, this derivation is awkward, because circle is simpler than ellipse. It seems simpler to define a `Circle` class without using inheritance.

The useful thing about ABC is that we can extract the same part of circle and ellipse, and derive the `Circle` and `Ellipse` classes from the ABC. For the different part of circle and ellipse, we can create pure virtual functions in ABC and let derived class to override them.

When a class contains a pure virtual function, we can't create an object of that class. C++ allows even a pure virtual funciton to have a definition. We can make the prototype virtual but still provide a definition in the implementation file.

### Inheritance and Dynamic Memory Allocation

When both the base class and the derived class use dynamic memory allocation, the derived-class destructor, copy constructor,and assignment operator all must use their base-class counterparts to handle the base-class component.

For a destructor, it is done automatically.

```c++
baseDMA::~baseDMA() // takes care of baseDMA stuff
{
    delete [] label;
}
hasDMA::~hasDMA() // takes care of hasDMA stuff
{
    delete [] style;
}
```

For a copy constructor, it is accomplished by invoking the base-class copy constructor in the member initialization list, or else the default constructor is invoked automatically.

```c++
baseDMA::baseDMA(const baseDMA & rs)
{
    label = new char[std::strlen(rs.label) + 1];
    std::strcpy(label, rs.label);
    rating = rs.rating;
}

hasDMA::hasDMA(const hasDMA & hs): baseDMA(hs)
{
    style = new char[std::strlen(hs.style) + 1];
    std::strcpy(style, hs.style);
}
```

For the assignment operator, it is accomplished by using the scope-resolution operator in an explicit call of the base-class assignment operator.

```c++
baseDMA & baseDMA::operator=(const baseDMA & rs)
{
    if (this == &rs)
        return *this;
    delete [] label;
    label = new char[std::strlen(rs.label) + 1];
    std::strcpy(label, rs.label);
    rating = rs.rating;
    return *this;
}

hasDMA & hasDMA::operator=(const hasDMA & hs)
{
    if (this == &hs)
        return *this;
    baseDMA::operator=(hs); // copy base portion, same as *this = hs
    delete [] style; // prepare for new style
    style = new char[std::strlen(hs.style) + 1];
    std::strcpy(style, hs.style);
    return *this;
}
```

## Chapter 14: Reusing Code in C++

### Private Inheritance

We use public inheritance to create ***is-a*** relationship, however, when we use private inheritance, it creates ***has-a*** relationship.

It seems weird to use a private inheritance, since we have no right to access base class methods in the outside world, but actually, the derived class contains all the members and data of the base class, so it creates ***has-a*** relationship.

#### `Student` Class Example

Let's say we want to create a `Student` class, the class should contain a string member and a valarray member. We could use containment to do so, but try private inheritance in here.

```c++
class Student : private std::string, private std::valarray<double>
{
public:
    ...
};
```

We don't need to create private data in the `Student` class, it's because the two inherited base class already provide all the needed data members.

#### Initializing Base-Class Components

```c++
// containment
Student(const char * str, const double * pd, int n)
: name(str), scores(pd, n) {} // use object names for containment

// private inheritance
Student(const char * str, const double * pd, int n)
: std::string(str), std::valarray<double>(pd, n) {} // use class names for inheritance
```

#### Accessing Base-Class Methods

Containment adds an object to a class as a named member object, and we use the variable name as a interface to access the class. Private inheritance provides the same feature as containment, but only without the interface, however, inheritance lets you use the class name and the scope-resolution operator to invoke base-class methods:

```c++
// containment
double Student::Average() const
{
    if (scores.size() > 0)
        return scores.sum()/scores.size();
    else
        return 0;
}

// private inheritance
double Student::Average() const
{
    if (ArrayDb::size() > 0)
        return ArrayDb::sum()/ArrayDb::size();
    else 
        reutrn 0;
}
```

#### Accessing Base-Class Objects

The way to access base-class objects is to use a type cast. Because `Student` is derived from a `string`, it's possible to type cast a `Student` object to a `string` object.

```c++
const string & Student::Name() const
{
    return (const string &) *this;
}
```

#### Accessing Base-Class Friends

We use explicit type cast to invoke the correct functions. This is basically the same technique used to access a base-class object in a class method.

```c++
ostream & operator<<(ostream & os, const Student & stu)
{
    os << "Scores for " << (const String &) stu << ":\n";
...
}
```

`<< (const String &) stu` converts `stu` to a `string` object, then invokes the `operator<<(ostream &, const string &)` function.

#### The revised `Student` class

[Student]({{< ref "/code/c++_primer_plus_studenti" >}}) 

#### Containment or Private Inheritance?

Both containment and private inheritance can create ***has-a*** relationship, but which one should we use? In general, we should use containment to model a ***has-a*** relationship, and use private inheritance if the new class needs to access protected members in the original class or if it needs to redefine virtual functions.

#### Protected Inheritance

With protected inheritance, public and protected members of a base class become protected members of the derived class. The main difference between private and protected inheritance is when we derive another class from a derived class. 

#### Redefining Access with `using`

Suppose we want to make a particular base-class method available publicly in the derived class. A easy way is to define a derived-class method that uses the base-class method. For example:

```c++
double Student::sum() const // public Student method
{
    return std::valarray<double>::sum(); // use privately-inherited method
}
```

Another way is to use a `using` declaration. For example:

```c++
class Student : private std::string, private std::valarray<double>
{
    ...
public:
    using std::valarray<double>::min;
    using std::valarray<double>::max;
    ...
};
```

The using declaration makes the `valarray<double>::min()` and `valarray<double>::max()` methods available as if they were public Student methods.

### Multiple Inheritance

Let's say we have a base class called `Worker`, and two derived class, `Singer` and `Waiter`. We can derive a `SingingWaiter` class from `Singer` and `Waiter` class:

```c++
class SingingWaiter : public Waiter, public Singer {...};
```

This is called multiple inheritance (MI).

MI can cause new problems.

* inheriting different methods with the same name from two different base classes
* inheriting nultiple instances of a class via two or more related immediate base class (`SingingWaiter` class encounters)

Let's look at an example.

[Worker0]({{< ref "/code/c++_primer_plus_worker0" >}})

The main difference between `Singer` and `Worker` class is the `Show` function. What will happen if we call the `Show` function in `SingingWaiter` class?

#### How Many Workers?

`SingingWaiter` winds up two `Worker` components. 

![Example image](/images/primer-plus-14.4.png)

We can assign the address of a derived-class object to a base-class pointer, however, this does not work in MI, since it's ambiguous now. We should specify which `Worker` we are pointing at:

```c++
SingingWaiter ed;
Worker * pw = &ed; // ambiguous
Worker * pw1 = (Waiter *) &ed; // the Worker in Waiter
Worker * pw2 = (Singer *) &ed; // the Worker in Singer
```

#### Virtual Base Classes

Virtual base classes allow an object derived from multiple bases that themselves share a common base to inherit just one object of that shared base class.

We would make `Worker` a virtual base class to `Singer` and `Waiter` by using the keyword
`virtual` in the class declarations:

```c++
// both order is valid
class Singer : virtual public Worker {...};
class Waiter : public virtual Worker {...};
// define SingingWaiter as before
class SingingWaiter: public Singer, public Waiter {...};
```

#### New Constructor Rules

C++ disables the automatic passing of information through an intermediate class to a base class if the base class is virtual. We have to call the virtual base class constructor explicitly.

```c++
// wk will not be pass down to base class constructor, it will use default base class constructor
SingingWaiter(const Worker & wk, int p = 0, int v = Singer::other)
: Waiter(wk,p), Singer(wk,v) {} // flawed
// the correct way to initialize, this will work only if we have virtual base class
SingingWaiter(const Worker & wk, int p = 0, int v = Singer::other)
: Worker(wk), Waiter(wk,p), Singer(wk,v) {}
```

#### Which Method?

Use scope-resolution operator to clarify which funciton we want to invoke.

```c++
SingingWaiter newhire("Elise Hawks", 2005, 6, soprano);
newhire.Show(); // ambiguous
SingingWaiter newhire("Elise Hawks", 2005, 6, soprano);
newhire.Singer::Show(); // use Singer version
```

A better way is to redefine `Show()` for `SingingWaiter` and specify which `Show()` to use (or use both).

However, the `Show()` function is an increment approach.

```c++
void Worker::Show() const
{
    cout << "Name: " << fullname << "\n";
    cout << "Employee ID: " << id << "\n";
}
void Waiter::Show() const
{
    cout << "Category: waiter\n";
    Worker::Show();
    cout << "Panache rating: " << panache << "\n";
}
void Singer::Show() const
{
    cout << "Category: singer\n";
    Worker::Show();
    cout << "Vocal range: " << pv[voice] << endl;
}
```

We can't combine `Waiter::Show()` and `Singer::Show()` without calling `Worker::Show()` twice.

We can use a modular approach to solve this. That is, providing a method that displays only the new components.

```c++
void Worker::Data() const
{
    cout << "Name: " << fullname << "\n";
    cout << "Employee ID: " << id << "\n";
}
void Waiter::Data() const
{
    cout << "Panache rating: " << panache << "\n";
}
void Singer::Data() const
{
    cout << "Vocal range: " << pv[voice] << "\n";
}
void SingingWaiter::Data() const
{
    Singer::Data();
    Waiter::Data();
}
void SingingWaiter::Show() const
{
    cout << "Category: singing waiter\n";
    Worker::Data();
    Data();
}
```

#### Mixed Virtual and Nonvirtual Bases

Suppose, for example, that class `B` is a virtual base class to classes `C` and `D` and a nonvirtual base class to classes `X` and `Y`. Furthermore, suppose class `M` is derived from `C, D, X`, and `Y`. In this case, class `M` contains one class `B` subobject for all the virtually derived ancestors (that is, classes `C` and `D`) and a separate class `B` subobject for each nonvirtual ancestor (that is, classes `X` and `Y`). So, all told, it would contain three class `B` subobjects.

#### Virtual Base Classes and Dominance

With nonvirtual base classes, if a class inherits two or more members (data or methods) with the same name from different classes, using that name without qualifying it with a class name is ambiguous. If virtual base classes are involved, however, such a use may or may not be ambiguous. In this case, if one name dominates all others, it can be used unambiguously without a qualifier.

```c++
class B
{
public:
    short q();
}
class C : virtual public B
{
public:
    long q();
    int omg()
};
class D : public C {};
class E : virtual public B
{
private:
    int omg();
};
class F: public D, public E {};
```

The `q()` from class `C` dominates the definition in class `B`, thus, methods in `F` can use `q()` to denote `C::q()`. Neither definition of `omg()` dominates the other, therefore, we must add qualifier.

The virtual ambiguity rules pay no attention to access rules. That is, even if `E::omg()` is private, using `omg()` is ambiguous. Even if `C::q()` is private, `q()` would still refer to inaccessible `C::q()`.

### Class Templates

Let's just see a template example.

```c++
// stacktp.h -- a stack template
#ifndef STACKTP_H_
#define STACKTP_H_
template <typename Type>
class Stack
{
private:
    enum {MAX = 10}; // constant specific to class
    Type items[MAX]; // holds stack items
    int top; // index for top stack item
public:
    Stack();
    bool isempty();
    bool isfull();
    bool push(const Type & item); // add item to stack
    bool pop(Type & item); // pop top into item
};
template <typename Type>
Stack<Type>::Stack()
{
    top = 0;
}
template <typename Type>
bool Stack<Type>::isempty()
{
    return top == 0;
}
template <typename Type>
bool Stack<Type>::isfull()
{
    return top == MAX;
}
template <typename Type>
bool Stack<Type>::push(const Type & item)
{
    if (top < MAX)
    {
        items[top++] = item;
        return true;
    }
    else
        return false;
}
template <typename Type>
bool Stack<Type>::pop(Type & item)
{
    if (top > 0)
    {
        item = items[--top];
        return true;
    }
    else
        return false;
}
#endif
```

Notice that we have to put class declaration and definition in the same file, since class templates are not class, they are just instructions to the compiler about how to generate class (same as funciton templates).

#### An Array Template Example and Non-Type Arguments

Let's begin with a simple array template that lets you specify an array size. One is to use a dynamic array and a constructor argument to provide the number of elements.

```c++
template <class T>
Stack<T>::Stack(int ss): stacksize(ss), top(0){
    items = new Type [stacksize]
}
```

Another approach is to use a template argument to provide the number of elements. This is what `std::array` does.

```c++
template<class T, int n>
class ArrayTP{
private:
    T ar[n];
...
}
```

`T` is a type parameter, or type argument. `n` is called a non-type, or expression argument.

Expression arguments can be an integer type, an enumerration type, a reference, or a pointer. Also the template code can't alter the value of the arument or take its address. Also when instantiating a template, the expression argument should be a constant expression.

The constructor approach (the `Stack` example) uses heap memory, whereas the expressoin argument approach uses memory stack. This provides faster execution time, particularly if we have a lot of small arrays.

However, the drawback is that different array sizes generate different templates. Also, the constructor approach is more versatile, since you can resize the array.

#### Template Versatility

##### Using More Than One Type Parameter

We can also write this:

```c++
template <class T1, class T2>
class Pair{
private:
    T1 a;
    T2 b;
...
}
```

##### Default Type Template Parameters

```c++
template <class T1, class T2 = int> class Topo {...};
Topo<double, double> m1; // T1 is double, T2 is double
Topo<double> m2; // T1 is double, T2 is int
```

#### Template Specializations

It is similar to function templates.

##### Implicit Instantiations

The compiler doesn't generate an implicit instantiation of the class until it needs an object:

```c++
ArrayTP<double, 30> * pt; // a pointer, no object needed yet
pt = new ArrayTP<double, 30>; // now an object is needed
```

##### Explicit Instantiations

```c++
template class ArrayTP<string, 100>; // generate ArrayTP<string, 100> class
```

The compiler generates the class definition, including method definitions, even though no object of the class has yet been created or mentioned.

##### Explicit Specializations

Tells the compiler to generate the template in a particular way.

Let's say we have a template, and we want to specialize it when the type is `const char *`:

```c++
template <typename T>
class SortedArray
{
    ...// details omitted
};

// specialization
template <> class SortedArray<const char *>
{
...// details omitted
};
```

##### Partial Specializations

```c++
// general template
template <class T1, class T2> class Pair {...};
// specialization with T2 set to int
template <class T1> class Pair<T1, int> {...};
// specialization with T1 and T2 set to int
template <> class Pair<int, int> {...};
Pair<double, double> p1; // use general Pair template
Pair<double, int> p2; // use Pair<T1, int> partial specialization
Pair<int, int> p3; // use Pair<int, int> explicit specialization

template<class T> class Feeb { ... };// general version
template<class T*> class Feeb { ... };// pointer partial specialization
Feeb<char> fb1; // use general Feeb template, T is char
Feeb<char *> fb2; // use Feeb T* specialization, T is char

// general template
template <class T1, class T2, class T3> class Trio{...};
// specialization with T3 set to T2
template <class T1, class T2> class Trio<T1, T2, T2> {...};
// specialization with T3 and T2 set to T1*
template <class T1> class Trio<T1, T1*, T1*> {...};
Trio<int, short, char *> t1; // use general template
Trio<int, short> t2; // use Trio<T1, T2, T2>
Trio<char, char *, char *> t3; use Trio<T1, T1*, T1*>
```

#### Member Templates

A template can be a member of a structure, class, or template class.

```c++
template <typename T>
class beta
{
private:
    template <typename V> // nested template class member
    class hold
    {
    private:
        V val;
    public:
        hold(V v = 0) : val(v) {}
        void show() const { cout << val << endl; }
        V Value() const { return val; }
    };
    hold<T> q; // template object
    hold<int> n; // template object
public:
    beta( T t, int i) : q(t), n(i) {}
    template<typename U> // template method
    U blab(U u, T t) { return (n.Value() + q.Value()) * u / t; }
    void Show() const { q.show(); n.show();}
};
```

We can also write the definition outside the class template, but it depends on the compiler. Some compilers don't support.

```c++
template <typename T>
class beta
{
private:
    template <typename V> // declaration
    class hold;
    hold<T> q;
    hold<int> n;
public:
    beta( T t, int i) : q(t), n(i) {}
    template<typename U> // declaration
    U blab(U u, T t);
    void Show() const { q.show(); n.show();}
};
// member definition
template <typename T>
    template<typename V>
        class beta<T>::hold
        {
        private:
            V val;
        public:
            hold(V v = 0) : val(v) {}
            void show() const { std::cout << val << std::endl; }
            V Value() const { return val; }
        };
// member definition
template <typename T>
    template <typename U>
        U beta<T>::blab(U u, T t)
        {
            return (n.Value() + q.Value()) * u / t;
        }
```

#### Templates As Parameters

We can write:

```c++
template <template <typename T> class Thing>
class Crab
```

Here `template <typename T> class` is the type, and `Thing` is the parameter. Suppose we have this declaration `Crab<King> legs;`. Then the template argument `King` must be a template class whose declaration look like this：

```c++
template <typename T>
class King {...};
```

For example:

```c++
template <template <typename T> class Thing>
class Crab
{
private:
    Thing<int> s1;
    Thing<double> s2;
public:
    Crab() {};
    // assumes the thing class has push() and pop() members
    bool push(int a, double x) { return s1.push(a) && s2.push(x); }
    bool pop(int & a, double & x){ return s1.pop(a) && s2.pop(x); }
};

// create object
Crab<Stack> nebula;
```

We can also mix template parameters with regular parameters.

```c++
template <template <typename T> class Thing, typename U, typename V>
class Crab{
private:
    Thing<U> s1;
    Thing<V> s2;
...
}

Crab<Stack, int, double> nebula; // T=Stack, U=int, V=double
```

#### Template Classes and Friends

Template class declarations can have friends, too. There are three categories of templates' friend:

* Non-template friends
* Bound template friends, meaning the type of the friend is determined by the type of the class when a class is instantiated
* Unbound template friends, meaning that all specializations of the friend are friends to each specialization of the class

##### Non-Template Friend Functions to Template Classes

```c++
template <class T>
class HasFriend
{
public:
    friend void counts(); // friend to all HasFriend instantiations
    ...
};
```

This declaration makes the `counts()` function a friend to all instantiations of the template. But the problem is, how do this function access a `HasFriend` object without having any parameter?

It could access a global object; it could access nonglobal objects by using a global pointer; it could create its own objects; and it could access static data members of a template class, which exist separately from an object.

What if we want to pass an object as a parameter? Can we write:

```c++
friend void report(HasFriend &);
```

The answer is no. The reason is that `HasFriend` is not a object. We need to indicate a specialization. For example:

```c++
template <class T>
class HasFriend
{
public:
    friend void report(HasFriend<T> &);  // bound template friend
    ...
};
```

Notice that `report()` is not a template function; it just has a parameter that is a template. This means that you have to define explicit specializations for the friends you plan to use:

```c++
void report(HasFriend<short> &) {...}; // explicit specialization for short
void report(HasFriend<int> &) {...}; // explicit specialization for int
```

##### Bound Template Friend Functions to Template Classes

We can make friend functions a template and bound them with each specialization.

First, declare each template function before the class definition.

```c++
template <typename T> void counts();
template <typename T> void report(T &);
```

Then declare the templates as friends inside the function:

```c++
template <typename TT>
class HasFriendT
{
    ...
    friend void counts<TT>();
    friend void report<>(HasFriendT<TT> &); // same as report<HasFriendT<TT>>(HasFriendT<TT> &)
};
```

In the `report()` function, we omit the template specialization in `<>` , since the argument can be deduced from the function argument.

The last thing is that we must provide template definitions for the friends.

```c++
// template friend functions definitions
template <typename T>
void counts()
{
    cout << "template size: " << sizeof(HasFriendT<T>) << "; ";
    cout << "template counts(): " << HasFriendT<T>::ct << endl;
}
template <typename T>
void report(T & hf)
{
    cout << hf.item << endl;
}
```

##### Unbound Template Friend Functions to Template Classes

The bound template friend functions in the preceding section are template specializations of a template declared outside a class. An int class specialization gets an int function specialization, and so on.

By declaring a template inside a class, you can create unbound friend functions for which every function specialization is a friend to every class specialization.

```c++
template <typename T>
class ManyFriend
{
private:
    T item;
public:
    ManyFriend(const T & i) : item(i) {}
    template <typename C, typename D> friend void show2(C &, D &);
};
template <typename C, typename D> void show2(C & c, D & d)
{
    cout << c.item << ", " << d.item << endl;
}
int main()
{
    ManyFriend<int> hfi1(10);
    ManyFriend<int> hfi2(20);
    ManyFriend<double> hfdb(10.5);
    cout << "hfi1, hfi2: ";
    show2(hfi1, hfi2);
    cout << "hfdb, hfi2: ";
    show2(hfdb, hfi2);
    return 0;
}
```

#### Template Aliases (C++11)

If you write code like this:

```c++
// define three typedef aliases
typedef std::array<double, 12> arrd;
typedef std::array<int, 12> arri;
typedef std::array<std::string, 12> arrst;
arrd gallons; // gallons is type std::array<double, 12>
arri days; // days is type std::array<int, 12>
arrst months; // months is type std::array<std::string, 12>
```

You can create template aliases:

```c++
template<typename T>
using arrtype = std::array<T,12>; // template to create multiple aliases

arrtype<double> gallons; // gallons is type std::array<double, 12>
arrtype<int> days; // days is type std::array<int, 12>
arrtype<std::string> months; // months is type std::array<std::string, 12>
```