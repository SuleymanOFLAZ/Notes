[[00_Course_Files]]
#Cpp_Course 
# Initialization Methods 
#Cpp_InitializeMethods
In C++, there are various initialization syntaxes. These initialization methods are:
1. Copy initialization
2. Direct initialization
3. Uniform/Brace/Direct-List Initialization
4. Default Initialization
5. Value Initialization
6. Zero Initialization
7. Aggregate Initialization
## Copy Initialization
#Cpp_CopyInitialization
Classical (C style) initialization is named **copy initialization**.
```c
int x = 10; // Copy initialization
```
Look to #Cpp_keyword_explicit  for further understanding of the copy initialization.
## Direct Initialization
#Cpp_DirectInitialization
Before modern C++ parentheses were used to initialize. This is named **direct initialization**.
```cpp
int x(10); // Direct initialization
```
## Uniform/Brace/Direct-List Initialization
#Cpp_UniformInitialization #Cpp11
There is a new initialization method that has come with modern C++. This is named **uniform initialization**, or **brace initialization**, or **direct list initialization**. Before modern C++, different initialization methods were used. To make common the initializer syntax the uniform initialization method is added with modern C++. This can be used with every kind, such as built-in types, user-defined types, and arrays, etc.
```cpp
int c{10}; // uniform or brace initialization
```

> There are several reasons to add a uniform initializer to C++, These are:
> 1. Uniform. Use it for everything.
> 2. To prevent data loss because of type conversion at initialization.
> 3. Most vexing parse (Advanced topic - Scott Meyers) problem

> [!note] Note: **Most Vexing Parse** #Cpp_Interview_Topic #Cpp_MostVexingParse
> A written code can be a function declaration or object-creating definition both at the same time. For example:

```cpp
// Example of the most vexing parse
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

There are two meanings of upper code:
1. Create B object and give A object to the constructor
2. bx is a function declaration that returns B type and its argument is a function pointer.

This is an example of the most vexing parse. This complexity is managed by giving priority. The function declaration option (2nd one) has higher priority in this situation.

How uniform initialization is a solution to the most versing parse? Because the parentheses syntax becomes brace syntax. 
## Default Initialization
#Cpp_DefaultInitialization
If there is no initialization syntax like the following, this is named **default initialization**:
```cpp
int x; // default initialization
```

Default initialization has some restrictions. For example, const objects can't be default initialized. This would be a syntax error.
```cpp
cont int x; // syntax error
```

References can't be default initialized.
```cpp
int &x;
```

## Value Initialization
#Cpp_ValueInitialization #Cpp11
There is one more initialization method that is added with modern C++. This is named **value initialization**. Very similar to uniform initialization but inside the braces is empty.
```cpp
int x{}; // initialized to 0
int* ptr{}; // initialized to nullptr
bool flag{}; // initialized to false
```

Default initialization caused the **undetermined value**. But value initialization differs. If an arithmetic type is initialized with value initialization, the initial value of the object will be 0. With a bool, it will be false, and with a pointer, it will be nullptr.

## Zero Initialization
#Cpp_ZeroInitialization
Zero initialization is not an actual initialization method. But this is used as the first step of other initialization methods. If an object is global or static, it is first initialized with zero initialization regardless of which initialization method we used in syntax.
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
#Cpp_AggregateInitialization
In C++, types have a categorization. 
1. aggregate types  

Arrays are a type of aggregate types. Initializing arrays named aggregate initialization. For example: "int a[] = {1, 2, 3, 4};". With aggregate initialization the rules in C are valid in C++ but, C++ has some other forms: "int a[]{1, 2, 3, 4};". 
In C empty curly braces is a syntax error with aggregate initialization. On the other hand, in C++ this is not a syntax error and all members initialized with value 0. For example: "int a[]{};" or "int a = {};" is valid in C++. (TODO: Make a research C standards for this, it is valid on online compiler)

# Type deduction
#Cpp_TypeDeduction
**Type deduction** is a very important component of C++'s tools set. Type deduction means the compiler deducts the type information itself in situations where type information is not mentioned. This is not a run-time mechanism and is not to be confused with the **reflection mechanism** that other programming languages have. In C++, this is completely compile-time mechanism.

> C++ deducts the type by using some tools. These are:
> 1. auto type deduction
> 2. decltype type deduction
> 3. decltype(auto) type deduction
> 4. lambda expression type deduction
> 5. auto return type type deduction
> 6. function templates type deduction
> 7. class templates type deduction

**Auto type deduction**: Auto is a keyword and differs in meaning in C++ over C. Modern C++ overloads the meaning of auto in C. (With the C++11 standard)

In C++, keywords are overloaded in the context of language. This means a keyword has different meanings depending on which context it is used. Some of the examples of overloaded keywords are "auto" and "using". It was done like this to make easy the compiler's job. Because each keyword is a token to the compiler. At the same time, this situation makes it harder to learn the language.

Auto means in a basic way, the variable type is the same as the initialized type. For example: "auto x = 10;" makes the type of x integer. This is not as simple as that but we will detail it later.

> [!note] Note: AAA (Almost Always Auto) #Cpp_AAA
AAA (Almost Always Auto) is an identifier of a coding style. This means using auto in every place that is possible.

> There are some benefits of auto:
> 1. It makes it easier to coding
> 2. It forces us to initialize the variable
> 3. Prevent us from problems that may occur when a part of code is changed (For example a changed function return type after editing the code)

```cpp
std::vector<std::pair<int, std::vector<int>::iterator>> x func();

auto x = func();

auto x; // syntax error, auto types must be initialized
```

# Function Take Default Argument
#Cpp_DefaultArgument
**Default argument** is a tool that is used frequently in C++ and C++ standard libraries. If a defaulted argument is not provided in the function call the default value of the argument is used. This is a compile-time process and does not affect code efficiency.
The default argument is stated in the function declaration or function definition. But in most cases, it is stated in the function declaration. If the function doesn't have the declaration, then it is mentioned in the definition. Stating the default argument both in a function declaration and function definition is a syntax error.
> Benefit of default argument:
> 1. It makes it easier to write code.
> 2. Prevent us from faults that may be made when writing code.

```cpp
void func(int, int, int=0);
```

There are some rules to default argument: If a function argument takes a default value the right side arguments of that argument have to take a default value.

> [!note] Note: Maximal Much  #Cpp_MaximalMuch  #Cpp_Interview_Topic 
A C and C++ topic. A compiler tokenizes the syntax to maximize the token. How is the following syntax evaluated by the compiler?

```c
int x = 10;
int y = 30;

int z = x+++y; // Maximal munch example
```

Is the upper example undefined behavior or syntax error? This expression is evaluated like that "int z = (x++) + y;". Because the compiler maximizes the token. This is not an undefined behavior or syntax error.

Let's look at another example of maximal munch in the situation of default argument. We write a function declaration with a default nullptr pointer argument without mentioning argument names in the declaration.

```cpp
void func(int, int *= nullptr); // Syntax error due to maximal much
```
#Cpp_Interview_Question 

> [!warning]  Warning: Maximal Much on Function Default Argument
The upper example is a syntax error. Because the compiler tries to maximize the token and \*= has a different meaning (multiple and assign) in this situation. 

Some different examples of using default argument. This is not a syntax error:
```cpp
int func(int x = 10, int y = 20);

void foo(int = func());

int main()
{
	foo(); // foo(func(10, 20));
}
```

> [!note] Note: Function definition without argument name
In C++, we don't have to give function argument names in the function definition. But we can't use this argument in the function. This is not a syntax error and is related to C++'s other functionalities which we will mention later. And there is a small benefit of it too, the compiler doesn't warn us of unused parameters. But in C, this is a syntax error (Except C23 standards). (Note: It is possible to be supported by some C compilers because there is no error in tested online C compilers) For example:

```cpp
void func(int)
{

}
```

> [!question] Question: How the output will be? #Cpp_Interview_Question 

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

The output will be like:
```
10
11
12
```

Because each function call is evaluated independently like this: "foo(x++);"

Let's assume we are using a function from an external module (such as something.h). And that function doesn't have default arguments. Can we redefine its default argument and use it in that way? Yes, we do. For example:
```cpp
# include "something.h" // Has declaration void foo(int, int, int);

// We can redeclare that function with the default argument
void foo(int, int, int = 0);
```
An interesting point about that, if the coming declaration has the last argument default (For example "void func(int, int, int = 0);"), but we want to use the previous argument as default too, then we can redefine it like that: "void func(int, int = 0, int);". Watch out for this, that was a syntax error normally, but not in this situation.
This is not related to function overloading. This is about redefining a function.

Let's take one step over, If we have a function from an external module and we want to use the middle argument of it as a default argument. How do we do that? In this situation, we don't have many options. We can write a function for this:
```cpp
#include "something.h"
// incoming function: void foo(int x, int y, int z);

void call_foo(int x, int z, int y = 10)
{
	foo(x, y, z);
}
```

> [!note] Note: How is a C library header included in a C++ file?
In C++, C standard libraries are included without .h and with added 'c' in front of them. For example:

```c
#include <ctime> // not time.h
#include <cctype> // not ctype.h
```

C library elements are used with std scope. For example std::time_t timer;. Using C libraries without std:: is not wrong, but using them with std:: is preferred

# Namespace
#Cpp_NameSpace
The namespace is briefly mentioned here. Details will be given later. In C, we have one namespace, the global namespace. 

The namespace syntax: 
```
namespace Time_
{
	int x = 0;
}
```
Note that there is no ";" after curly brace. If we write it, this won't be a syntax error, but it would be an empty statement in the global scope 

## Unary "::" operator
Unary "::" operator means to look for the name in the global scope. In another sense, it is a way to say don't look at this name inside nested or local scope. In C, we don't have an operator corresponding to that meaning.
```cpp
int x = 10;

int main()
{
	int x = 5;
	::x += x; // means look for the ::x only in global scope
}
```

Mentioning of an object with "::" operand is called a "**qualified name**". If not, it is called an "**unqualified name**".

> [!note] Note: Argument Dependent Lookup - ADL Rule #Cpp_ADL
ADL (Argument Dependent Lookup)rule: Sometimes even if we didn't mention the namespace, the namespace of an object can be referenced depending on some rules. This is called the ADL rule and we will mention it later. Example:

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
- Maximal Munch
- Qualified Name
- Unqualified Name
- Argument Depended Lookup (ADL)

---
Return: [[00_Course_Files]]