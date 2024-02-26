[[00_Course_Files]]
#Cpp_Course 
# Initialization Methodes 
#Cpp_Initialize
In C++, there are various initialization syntax. These initialization methods are:
1. Copy initialization
2. Direct initialization
3. Uniform/Brace/Direct-List Initialization
4. Default Initialization
5. Value Initialization
6. Zero Initialization
7. Aggregate Initialization

## Copy Initialization
Classical (C style) initialization is named <mark style="background: #BBFABBA6;">copy initialization</mark>.
```c
int x = 10; // Copy initialization
```

## Direct Initialization
Before modern C++ parentheses used to initialize. This named <mark style="background: #BBFABBA6;">direct initialization</mark>.
```cpp
int x(10); // Direct initialization
```

## Uniform/Brace/Direct-List Initialization
<mark style="background: #FFF3A3A6;">There is a new initialization method that has came with modern C++</mark>. This named <mark style="background: #BBFABBA6;">uniform initialization</mark> or <mark style="background: #BBFABBA6;">brace initialization</mark> or <mark style="background: #BBFABBA6;">direct list initialization</mark>. Before modern C++, different initialization method used. To make common the initializer syntax the uniform initialization method is added with modern C++. This can be used with every kind, such as built-in types, user defined types and arrays etc.
```cpp
int c{10}; // uniform or brace initialization
```

> <mark style="background: #FFF3A3A6;">There are several reasons to add uniform initializer to C++</mark>, These are:
> 1. Uniform. Use it for everything.
> 2. To prevent data loss that because type conversion at initialization.
> 3. most vexing parse (Advanced topic - Scott Meyers) problem

> [!note] Note: **Most Vexing Parse** 
> <mark style="background: #FF5582A6;">(! This is a possible interview question)</mark>A written code can be a function declaration or object creating definition both at same time. For example:

```cpp
// Example of most vexing parse
class A {

};

class B{
public:
	B(A);
}

int main()
{
	B bx(A()); // Most vexing parse
}
```
#Cpp_Interview_Question
#Cpp_MostVexingParse

There are two meaning of upper code:
1. Create B object and give A object to constructor
2. bx is function declaration that returns B type and its argument is a function pointer.

This is a example to most vexing parse. This complexity is managed with giving priority. <mark style="background: #FFF3A3A6;">The function declaration option (2nd one) is has higher priority</mark> in this situation.

<mark style="background: #FFF3A3A6;">How uniform initialization is a solution to most versing parse? </mark>Because the parentheses syntax become brace syntax. 

## Default Initialization
If there is no initialization syntax like following, this named <mark style="background: #BBFABBA6;">default initialization</mark>:
```cpp
int x; // default initialization
```

<mark style="background: #FFF3A3A6;">Default initialization has some restrictions</mark>. For example, <mark style="background: #FFF3A3A6;">const objects can't be default initialized</mark>. This would be <mark style="background: #FFF3A3A6;">syntax error</mark>.
```cpp
cont int x; // syntax error
```

<mark style="background: #FFF3A3A6;">References can't be default initialized</mark>.
```cpp
int &x;
```

## Value Initialization
There is one more initialization method that added with modern C++. This named <mark style="background: #BBFABBA6;">value initialization</mark>. Very similar to uniform initialization but inside the braces is empty.
```cpp
int x{}; // initialized to 0
int* ptr{}; // initialized to nullptr
bool flag{}; // initialired to false
```

Default initialization caused the <mark style="background: #BBFABBA6;">indetermined value</mark>. But value initialization differs. If a arithmetic type initialized with value initialization, the initial value of the object will be 0. With bool, it will be false and with a pointer it will be nullptr.

## Zero Initialization
<mark style="background: #BBFABBA6;">Zero initialization</mark> is not a actually initialization method. But this used as first step of other initialization methods. If an object is global or static, it first initialized with zero initialization regardless of which initialization method we used in syntax.
arithmetic types initialized to 0, bool type initialized to false, pointer types initialized to nullptr with zero initialization.

```cpp
int x; // first zero initialized
int y{}; // first zero initialized

int main()
{
	static int c; // first zero initialized
	static int c{}; // first zero initialized
}
```

## Aggregate Initialization
In C++, types has a categorization. 
1. aggregate types  

Arrays is a type of <mark style="background: #BBFABBA6;">aggregate types</mark>. Initializing arrays named <mark style="background: #BBFABBA6;">aggregate initialization</mark>. For example: <mark style="background: #D2B3FFA6;">int a[] = {1, 2, 3, 4};</mark>. With aggregate initialization the rules in C is valid in C++ but, C++ has some other forms: int <mark style="background: #D2B3FFA6;">a[]{1, 2, 3, 4};</mark>. 
<mark style="background: #FFF3A3A6;">In C empty curly braces is a syntax error with aggregate initialization. In the other hand, in C++ this not syntax error and all members initialized with value 0</mark>. For example: <mark style="background: #D2B3FFA6;">int a[]{};</mark> or <mark style="background: #D2B3FFA6;">int a = {};</mark> is valid in C++. (TODO: Make a research C standards for this, it is valid on online compiler)

# Type deduction
#Cpp_TypeDeduction
<mark style="background: #BBFABBA6;">Type deduction</mark> is a very important component of C++'s tools set. Type deduction means to the compiler deduct the type information itself in the situations type information is not mentioned. This is not a run time mechanism and not to confuse with <mark style="background: #BBFABBA6;">reflection mechanism</mark> that another programming languages has. In C++, this is completely compile-time mechanism.

> C++ deduct the type with using some tools. These are:
> 1. auto type deduction
> 2. decltype type deduction
> 3. decltype(auto) type deduction
> 4. lambda expression type deduction
> 5. auto return type type deduction
> 6. function templates type deduction
> 7. class templates type deduction

<mark style="background: #ADCCFFA6;">Auto type deduction</mark>: Auto is a keyword and differ in meaning in C++ over C. Modern C++ overload the meaning of auto in C. (With the C++11 standard)

In C++, keyword are overloaded in context of language. This means a keyword has different meaning depending on which context it used. Some of the example of overloaded keywords are "auto" and "using". This is because to make easy the compiler work. Because each keyword is a token to compiler. In the same time, this situation makes harder to learn the language.

Auto: means in basic way, the variable type is same as the initialize type. For example: <mark style="background: #D2B3FFA6;">auto x = 10;</mark> makes the type of x integer. Actually this is not simple as that but we will detailed it later.

> [!note] Note: AAA (Almost Always Auto)
<mark style="background: #ADCCFFA6;">AAA (Almost Always Auto)</mark>: This is an identifier to a coding style. Means use auto in every place that is possible.

> There are some benefits of auto:
> 1. It makes easier to coding
> 2. It forces us to initialize the variable
> 3. Prevent us from problems that may occur when a part of code changed (For example a changed function return type after editing the code)

```cpp
std::vector<std::pair<int, std::vector<int>::iterator>> x func();

auto x = func();

auto x; // sytax error, auto types must be initialized
```

# Function Take Default Argument
#Cpp_DefaultArgument
<mark style="background: #BBFABBA6;">Default argument</mark> is a tool that used frequently in C++ and C++ standard libraries. If an defaulted argument is not provided in the function call the default value of the argument is used. This is a compile-time process and not effect code efficiency.
Default argument is stated in function declaration or function definition. But in most cases it is stated at function declaration. If the function doesn't have the declaration, than it is mentioned in the definition. <mark style="background: #FFF3A3A6;">Stating the default argument both in a function declaration and function definition is a syntax error</mark>.
> Benefit of default argument:
> 1. It makes easier to writing code.
> 2. Prevent us from faults that may be made when writing code.

```cpp
void func(int, int, int=0);
```

There are some rules to default argument: <mark style="background: #FFF3A3A6;">If an function argument takes a default value that the right side arguments of that argument must take a default value</mark>.

> [!note] Note: Maximal Much
<mark style="background: #FF5582A6;">(! C interview topic)</mark> A C and C++ topic. A compiler tokenize the syntax to maximize token. How the following syntax evaluated by compiler?

```c
int x = 10;
int y = 30;

int z = x+++y; // Maximal munch example
```

Is upper example undefined behavior or syntax error? This expression evaluated like that <mark style="background: #ADCCFFA6;">int z = (x++) + y;</mark>. Because the compiler maximizes the token. And this is not an undefined behavior or syntax error.

Let's look another example of maximal munch in the situation of default argument. We write a function declaration with default nullptr pointer argument and without mentioning argument names in declaration.

```cpp
void func(int, int *= nullptr); // Syntax error due to maximal much
```
#Cpp_MaximalMuch
#Cpp_Interview_Topic

> [!warning]  Warning: Maximal Much on Function Default Argument
<mark style="background: #FFF3A3A6;">Upper example is a syntax error</mark>. Because the compiler tries to maximizes the token and <mark style="background: #D2B3FFA6;">*=</mark> has different meaning in this situation. 

Some different example of using default argument. This is not a syntax error:
```cpp
int func(int x = 10, int y = 20);

void foo(int = func());

int main()
{
	foo(); // foo(func(10, 20));
}
```

> [!note] Note: Function definition without argument name
<mark style="background: #FFB8EBA6;">Additional info</mark>: In C++, we don't have to give function argument names in the function definition. But we can't use this argument in the function. This is not syntax error and related to C++'s another functionalities that we will mention later. And there is a small benefit of it too, the compiler doesn't warn us as unused parameter. But in C, this is a syntax error (Except C23 standards). (Note: It is possible to supported by some C compilers, because there is no error in tested online C compilers) For example:

```cpp
void func(int)
{

}
```

> [!question] Question: 
<mark style="background: #FF5582A6;">Interview question</mark>: Another default argument example. How the output will bee?:

```cpp
int x = 10;

void foo(int a = x++)
{
	std::cout << "a = " << a << "\n";
}

int main()
{
	foo();
	foo();
	foo();
}
```
#Cpp_Interview_Question 

The output will be like:
```
10
11
12
```

Because each function call evaluated independently like this: <mark style="background: #D2B3FFA6;">foo(x++);</mark>

<mark style="background: #FFF3A3A6;">Another key point</mark>: Let's assume we are using a function from an external module (such as something.h). And that function doesn't have default arguments. Can we redefine it default argument and use it in that way? Yes we do. For example:
```cpp
# include "somethong.h" // Has decleration void foo(int, int, int);

// We can redeclare that function with default argument
void foo(int, int, int = 0);
```
And interesting point about that, if the coming declaration has last argument default (Example <mark style="background: #D2B3FFA6;">void func(int, int, int = 0);</mark>), but we want use the previous argument as default too, than we can redefine it like that: <mark style="background: #D2B3FFA6;">void func(int, int = 0, int);</mark>. <mark style="background: #FFF3A3A6;">Watch out for this</mark>, that was has been a syntax error normally, but not in this situation.
This is not related to function overloading. This is about redefining a function.

Let's take one step over, If we have a function from external module and we want to use middle argument of it as default argument. How we do that? In this situation we don't have much option. We can write a function for this:
```cpp
#include "something.h"
// incoming function: void foo(int x, int y, int z);

void call_foo(int x, int z, int y = 10)
{
	foo(x, y, z);
}
```

> [!note] Note: How a C library header included in a C++ file
<mark style="background: #FFB8EBA6;">Additional Information</mark>: In C++, C standard libraries are included without .h and with added 'c' in front of them. For example:

```c
#include <ctime> // not time.h
#include <cctype> // not ctype.h
```

<mark style="background: #FFF3A3A6;">And library elements are used with std scope. For example <mark style="background: #D2B3FFA6;">std::time_t timer;</mark>. Using C libraries without std:: is not wrong, but using them with std:: is preferred</mark>

# Namespace
#Cpp_NameSpace
Namespace is briefly mentioned here. Details will be given later. In C, we have one namespace, the global namespace. 

The namespace syntax: 
```
namespace Time_
{
	int x = 0;
}
```
Note that there is no ";" after curly brace. If we write it, this won't be syntax error, but it would be an empty statement in global scope 

<mark style="background: #FFF3A3A6;">Unary ::</mark> operator means to look for the name in global scope. In other sense, it is way to say don't look this name inside nested scope or local scope. In C, we don't an operator that is corresponds that meaning.
```cpp
int x = 10;

int main()
{
	int x = 5;
	::x += x; // means look for the ::x only in global scope
}
```

Mentioning of an object with :: operand is called <mark style="background: #BBFABBA6;">qualified name</mark>. If not, it is called <mark style="background: #BBFABBA6;">unqualified name</mark>.

> [!note] Note: ADL Rule
<mark style="background: #ADCCFFA6;">ADL (Argument Dependent Lookup)rule</mark>: Sometimes even we mention the namespace, the namespace of an object can be referenced depending on some rules. This called ADL rule and we will be mention it later. Example:

```cpp
 #incldue <iostream>
 #include <algorithm>
 #include <vector>

 int main()
 {
	std::vector<int> x:
	count(x,begin(), x.end(), 1); // count is inside std namesapce
 }
```

---
# Terms
#Cpp_Terms
- Copy Initialization
- Direct Initialization
- Brace Initialization
- Uniform Initialization
- Direct-List Initialization
- Default Initialization
- Value Initialization
- Zero Initialization
- Aggregate Initialization
- Aggregate Types
- Most Vexing Parse
- Undetermined Value
- Type Deduction
- Reflection Mechanism
- Almost Always Auto (AAA)
- Default Argument
- Qualified Name
- Unqualified Name
- Argument Depended Lookup (ADL)

---
Return: [[00_Course_Files]]