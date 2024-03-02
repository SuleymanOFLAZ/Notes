[[00_Course_Files]]
#Cpp_Course
# Reference Semantics
#Cpp_ReferenceSemantic
There is an alternative semantic to pointer semantics named **reference semantic** in C++. This is a language layer tool. The compiler generates the same assembly code as we use pointer semantics most of the time. Small differences can depend on the optimization level.

The reason to add reference semantics to the language is that pointer semantics are not compatible with other language features like **operator overloading**.

References were has existed before modern C++. This was only called "reference". When **move semantics** and **perfect forwarding** tools were moved in with modern C++, the "reference" concept was expanded and divided into two categories, Old "reference" is called "**L value reference**" and  "**R value reference**" is added. #Cpp11

Why are R-value references added to the language? Modern C++ (C++11 and later) added various tools to the language. Some of these tools are very affected the language. These are move semantics, perfect forwarding, lambda expressions, etc. R-value references are added to support these features.

> In conclusion there are two reference concepts in C++:
> 1. L-value reference
> 2. R-value reference

Now L value references are mentioned. We will look at the R-value reference later.

> [!note] Note: Compiler Explorer
**godbolt.org** is a website that shows the assembly code of a given code.

# Value Category
#Cpp_ValueCategory
Value category is the qualification of an expression. That means, "int x;" does not have a value category. Value category qualifies expressions like: "x, 10, x+5, a\[3], \*ptr". There is no concept like the value category of a declared variable. Data type and value category are different concepts. Value category describes which contexts an expression can be used as legal and which situation is that expression be subjected to certain conversion operations. When we describe an expression's value category, we can determine how the other tools of the language react to that expression and whether is there a syntax error or how to make inferences in similar situations.

Value categorization differs between C and C++ and this makes it more complex. In C, an expression can be an L-value expression or can be an R-value expression. L value means that the expression corresponds to an object and has an area on memory and we can access that area. We can test that with the address-of-operator. If we can use the address operator on the expression then it is an L-value reference, and in the other way, it is an R-value reference.

But in C++, this is not that simple. The value category of a C expression may not be the same as the value category of C++. In modern C++, there are three value categories, and they are called **primary value categories**.

> Primary value categories are:
> 1. **L value**
> 2. **PR value**
> 3. **X value**

An expression can belong to only one of the primary categories at the same time.
For example:
```cpp
x // L value
x + 10 // PR value

int&& foo();

foo() // X value
```

- L value: It has come from the "left value". But after, it has been evaluated to "locators value".
- PR value: pure R-value.
- X value: expiring value

There are composite value categories too. There are:
- **GL value**: L value and X value combination set is GL value. Generalized L value.
- **R value**: PR value and X value combination set is R value. 
![[CppValueCategories.png|700]]

![[Value_Category|700]]

# L value references
#Cpp_LvalueReference
A reference is a name that replaces an object. L value reference:
```cpp
int x = 20;
int &r = x; // L value reverence

++r;
```

We bind r to x.
The example corresponds to the following pointer syntax:
```cpp
int x = 20;
int* const ptr = &x;

++(*ptr);
```

That means r is a reference to x. r means x. That means there are no two objects. There is only one object and, x and r means x. & is not an operator here, & is a **declarator**. For example:
```cpp
 r = 90; // we used x
 &r // we take the reference
 
///

int x = 10;
int& r =x; // Here, & is a declerator

int* p = &r; // Here, * is a declerator but & is a operator (address of) 
```

That makes it easier to write and ensures compliance with C++ tools like operator overloading.

L value references can't point to another object after the initial value. That means L value references correspond to const top-level pointers and means the address that  L value references hold can't be changed.

All initialize methods can be used with  L value reference except default initialization. For example:
```cpp
#include ‹iostream>

int main()
{
	int x = 10;
	int& r1 = x;
	int& r2｛ x ｝；
	int& r3(x);
}
```

> [!note] Note: There is another name for the heap in C++, which is **free store**.

Let's look at some legal and illegal (syntax error) situations:
1. References must be initialized. That means references cannot be default initialized. For example:
```cpp
int main()  
{  
	int& x; // Syntax error, a reference must be initialized
}
```

2. We must initialize an r-value reference with the same type of object. If we violate that rule, that would be a syntax error. Note that, this is the same with the pointer semantic in C++, but in C that differs. For example:
```cpp
int main()
{
	unsigned int x = 10;
	int& r = x; // Syntac error, not same type
}
```

3. We cannot initialize L-value references with R-value references. For example:
```cpp
int main()  
{  
	unsigned int x = 10;  
	int& r = 10;  // Syntax error, we cannot initialize with a R-value
}
```

4. The following syntaxes are legal to use:
```cpp
int main()
{
	int a[]{ 1, 2, 3, 4 };
	int* p= a;
	int& r1 = a[2];
	int& r2 = *p;
}
```

5. And also following is legal:
```cpp
int main()
{
	int x = 10;
	int* p{ &x };
	int* &r = p;

	++ *r; // increment of x
｝
```

Look for the following:
```cpp
int main()
{
  int a[20] {10, 12, 13};
  int* &r = &(a[1]); // Syntax error: non-const lvalue reference to type 'int *' cannot bind to a temporary of type 'int *'

  // But the following is legal
  int a[20] {10, 12, 13};
  int* ptr = &(a[1]);
  int* &rr = ptr; // OK
}
```

There is no reference to reference as similar to pointer to pointer. For example:
```cpp
int main()
{
	// Classical pointer to pointer syntax
	int x = 10;
	int* p = &x;
	int** ptr = &p;

	// Followings syntax doesn't correspond to pointer to pointer
	int x = 10;
	int& r1 = x;
	int& r2 = r1; // r2 is same as r1

}
```

Question: Can we take a reference to an array? The answer is yes, but first, let's look at how could we take a pointer to an array. A pointer to an array is different from a pointer to an array member. For example:
```cpp
int main()
{
	int ar[] = {2, 5, 6, 9 };
	int(*p)[4] = &ar; // Pointer to an array
}
```

Same as a pointer to an array, reference to an array is possible:
```Cpp
int main()
{
	int ar[] = {2, 5, 6, 9 };
	int(&r)[4] = ar; // Reference to an array
	r[2] = 34; // OK
	int *ptr = r; // OK, array decay
}
```
However, there is less complex syntax that corresponds to upper syntax. We can benefit from type deduction and we can write it like the following:
```cpp
int main()
{
	int ar[] = {2, 5, 6, 9 };
	//int (&r)[4] = ar;
	
	auto& r = ar; // auto type deduction
｝
```

## The Function That Has Reference Argument

 ```cpp
 #include <iostream>

void func(int& r)
{
	r = 999;
}

int main()
{
	int ival{ 3 };
	
	sta:: cout << "ival= " « ival « "\n"; // It prints 3
	
	func(ival);
	std:: cout << "ival = " « ival « "\n"; // It prints 999
｝
```

Keep caution on that; we cannot determine whether the "func(ival);" can change the arguments or not without showing func's definition (which means only looking at the function call) in C++. In C, we can say if the function is call-by-value or call-by-reference by seeing the function call. But in C++, because of the reference semantic we cannot say that and we must see the function implementation to say if the function can change the passed argument or not.

Important: Function call expression that the function return type is an L-value reference is an L-value expression in C++ language.  That means the function call expression to "int& foo();" is an L-value expression but, the function call expression to "int foo();" is a PR value expression. For example, the function call "foo();" is an L-value expression in the below code, because the function returns an L-value reference:
```cpp
#include <iostream>

int g = 10;

int& foo()
{
	return g; // returns global g
}

int main()
{
	int x = foo(); // Here, x not meas g, because x isn't a reference
	int& r = foo(); // Here, r means g, because r is a reference

	++r; // global g is increased

	foo() = 999; // Assign 999 to g
}
```

Since "int& foo()" is an L-value expression we can assign it to an L-value reference.
And we can use the "foo();" call at the left side of the assignment operator: "foo() = 999;". 

Keep caution on that, In the expression "int x = foo();", the "x" does not mean the "g". So the "x" does not change the "g" and it is a different object. But with the syntax "int& r = foo();", the "r" means the "g".

Pointer semantic example is in below:
```cpp
int g = 10;

int* foo(void)
{
	//code
	return &g;
}

int main()
{
	int* p = foo();
	
	std::cout << "g = " << g << "\n";
	
	*foo() = 999;
	
	std::cout << "g = " << g << "\n";
	
	++*foo();
	
	std::cout << "g = " << g << "\n";
}
```

> [!note] Note: Array Decay
> The below code is an array decay example.

```c
int main()  
{
	int a[] = { 1,2,3 }; //
	int (*p)[3] = &a;
	
	(*p)[2] = 99;
	
	int* ptr = *p; //array decay
}
```

## Const L Value Reference
If we want to use an L reference to only access, we use a const L-value reference. We use the const keyword to do that.

Remember, L-value references are not re-bindable. That means L-value references must be initialized and they are the same as a const top-level pointer. But if we want to make them as low-level const pointers we use "const reference". By making an L-value reference const, we cannot change the value that the reference refers to. That is called "const L-value reference".

**Const correctness** determines the code quality and it is very important.
```cpp
#include <iostream>

// accessor
void foo(const T&);
//mutator
void bar(T &r);

int main()
{
	int x = 10;
	const int& r = x;
	
	int ival = r;
	ival = r + 3;
	
	r = 9; // Syntax error
}
```

It is optional to determine the "const" keyword location:
```cpp
const int& r = x; // OK
int const &rr = x: // OK
```

The top-level "const" keyword can be used with references, but there is no meaning in using it because of already a reference is a top-level const. That means a reference is not re-bindable. For example:
```cpp
int & const ref = x; // OK, but there is no meaning to it. It is the same as int &ref = x;
```

> [!note] Note: Strict Aliasing Rule
> Rules of address type conversions. There are some legal rules. For example, conversion to char* is legal both in C and C++ (with type conversion operator).
> #Cpp_StrictAliasingRule

```c
int main()
{
	double x = 10.5;
	int* ptr = (int*)&x; //strict aliasing rules
}
```

Normally reference type conversion isn't legal in C++. But if the reference type is a const type, type conversion becomes legal. For example:
```cpp
int main()
{
	int x = 10;
	
	double& r = x; // This is not legal
	const double& r = x; // This is legal
}
```
But how does the "r" replace another type in the upper example? The answer is it doesn't, the "r" replaces a double object that is generated by the compiler. The compiler generates a temporary object with the same type of the reference, then assigns the value to it and links our reference to that temporary object. The lifespan of this temporary object lasts in the scope of the reference's scope. This can be done because it is possible to implicit (automatic) type conversion from int to double. If it is not possible, then that doesn't work and that means syntax error.

The objects that do not exist in the source code but are compiler-generated are named **temporary objects**. This is an important topic of C++. The compiler-generated code is like the following: #Cpp_TemporaryObject 
```cpp
int main()
{
	int x = 10;
	
	// double& r = x; // This is not legal
	const double& r = x; // This is legal
	/******  The following syntax represents the compiler-generated code *******/
	{
		const double temp = x;
		const The f& r = temp;
	}
	/******  End of representation *******/
}
```

what would be if we assign an R-value to a const L value reference? This wouldn't be a syntax error. Because like the upper description, the compiler creates a temporary object for that R-value and assigns out const L value reference to it. For example:
```cpp
int main()
{
	const int& r = 5;
	/****** The following syntax represents the compiler-generated code *******/
	{
		const int temp = 5;
		const int& r = temp;
	}
	/******  End of representation *******/
}
```

What does that mean practically? This means we can call the function "void foo(T &);" with only L value category expression arguments. But, we can call the function "void foo(const T &);" with the L-value category expression arguments and also R-value category expression arguments. #Cpp_ConstOverloading #Cpp_TemporaryObject 
## Pointer semantics vs reference semantics
Most of the time pointer semantics and reference semantics are alternative to each other. But sometimes we cannot use one of them instead of the other.

> 1. There is pointer-to-pointer but there is no reference-to-reference.
> 2. A pointer array is possible but a reference array is not possible. There is no such an array that its members are references. But this can be solved with the **std::reference_wrapper** class of the C++ standard library. We will loot it later. #Cpp_std_wrapper
> 3. There is nullptr, but there is no null reference. This means if we use nullptr with pointers there is no corresponding here to references.
> 4. We can assign another address to a pointer if the pointer is not a top-level const pointer. But we cannot change a reference. That means references are not re-bindable. But, we can use std::reference_wrapper object instead of that. #Cpp_std_wrapper
> 5. A reference corresponds to the top-level const pointer. Because the references must be initialized and we cannot change where they point later on.
> 6. Pointers can be default initialized but references are not.

Let's expand the upper bullets a little bit:
1. Let's look pointer to pointer example:
```cpp
// Pointer swap example
void pswap(int** ptrl, int** ptr2)
{
	int* ptemp = *ptr1;
	
	*ptr1 = *ptr2;
	*ptr2 = ptemp;
}

int main
{
	int x = 10, y = 34;
	int* p1 = &x, * p2 = &y;
	
	pswap (&p1, &p2);
}
```

And let's look at the reference semantics of the same code. There is no reference to reference here:
```cpp
void pswap(int* &r1, int* &r2)
{
	int* ptemp = r1;
	
	r1 = r2;
	r2 = ptemp;
}

int main
{
	int x = 10, y = 34;
	int* p1 = &x, * p2 = &y;
	
	pswap (p1, p2);
}
```

2. The Below code is for the pointer array example:
```cpp
int main()
{
	int* p[10]; // An pointer array

	//There is no alternative to upper code with references
	int &r[3]; // Syntax error
}
```

3. Where nullptr or NULL is used?
1. address returning function like "FILE* fopen();". returns NULL in case of failure. Or malloc() is the same. This kind returns an address of the object in case of success, the other way it returns NULL. Also, the same system functions or 3rd party codes use this approach. There is no corresponding approach to that method with reference semantics because there is no null reference.
2. NULL is used frequently in search functions. They return the address of the objects if it is founds and NULL if it is not found.
3. A NULL function argument can be used to give an option to the caller. For example, a function takes a pointer argument and if the value of the pointer is NULL, the function makes a different operation. For example, for "fflush()", if we give it a file object address, the function flashes the file's buffer; but if we give it NULL, the function flashes the all open files buffer. 

In upper cases or similar cases where NULL is used, we cannot use references. In this case, we use pointers. Or we can use alternative tools of C++ such as **std::optional**. #Cpp_std_optional

We can declare function references like function pointers. For example:
```cpp
int foo(int);

int main()  
{  
	int (*fp)(int) = foo; // Function pointer
	int (&fpr)(int) = foo; // Function reference
}
```

# R-Value Reference
Now we will briefly mention R-value references. We will mention it later in detail with move semantics and perfect forwarding. R-value references came with modern C++. #Cpp11
- We can initialize L value references with L value expressions.
- We can initialize R value references with R value expressions.
- We can initialize const  L value references with both L value expressions and R value expressions.
- An R value reference must be initialized. (Same as L value reference)
R value references can be created with **&& declarator**.
```cpp
int&& r = 10; // r is a R value reference
```
R value references must be initialized with an R value expression. If we try to initialize it with an L value expression, it would be a syntax error.

# Expression's Value Category
#Cpp_ValueCategory 
Which expression is the L value and which expression is the R value:
1. If an expression exists of a variable name then it is an L value (only name).
2. Literal expressions are PR value.
3. If an expression consists of an arithmetic operator, then it is a PR value.
4. Some expressions that are created with the some operators are L value. The front ++ operator is one of them. For example, "++x" is a L value. But "x++" is a PR value. It is the same with "--" operator.
5. Expression that is created with a comma if the right expression of the coma is L value that the expression is an L value
6. Expression created with assignment operator is an L value
7. If a function return type is not a reference a call to that function is a PR value
8. If a function return type is an L value reference a call to that function is an L value
9. If a function return type is an R value reference a call to that function is an X value
```cpp
int x{}; // x is a L value

int foo();
int& func();
int&& bar();

int&& r = 10;

int main 
{
	int x = 10;
	int y = 10;
	int a[120];
	int&& r = 10;
	
	pvcat(x); // L value because it is a name
	pvcat((x)); // L value
	pvcat(10); // PR value because it is a literal
	pvcat(x + 4); // PR value because it consists of an arithmetic operator
	pvcat(a); // L value because it is a name
	pvcat(a[3]); // L value
	pvcat(++x); // L value because of pre-increment operator
	pvcat(x++); // PR value because of post increment operator 
	pvcat(--x); // L value because of pre decrement operator
	pvcat(X--); // PR value because of pre decremetn operator
	pvcat((x, y)); // L value because the right side of the coma is an L value
	pvcat(x = 5); // L value because of assignment operator
	pvcat (x += 5); // L value because of assignment operator
	pvcat(foo()); // PR value because the function doesn't return a reference
	pvcat(func()); // L value because the function returns an L value reference
	pvcat(bar()); // X value because the function returns an R-value reference
	pvcat(r); // L value because r is a name
	pvcat(foo); // L value because foo is a function name
	pvcat(nullptr); // PR value because nullptr is a literal
	pcvat( x > 10 ? a : b); // L value expression
}
```

Some exercises:
```cpp
int main()
{
	int x = 10;
	
	int &r1 = ++x; // OK. ++x is a L value
	int &r2 = x++; // syntax ERROR. x++ is not a L value (PR value)
	int &&r2 = --x; // syntax ERROR. --x is not a R value (L value)

	const int &r2 = x++; // OK. we can assign a PR value to const L value reference
}
```

**Value category comparison with C**:
1. ++x is R value expression in C
2. x, y (comma operator) is R value expression in C
3. x > 10 ? a : b is R value expression in C. (L value expression in C++)

> [!note] Note: Pointer or reference function parameters can take a default value.

```cpp
int g = 10;

void func(int& r = g); // OK

int main()
{
	func();
}
```

> [!note] Note:  Code to print value category

```cpp
#pragma once

template<typename T>
struct ValCat {
	static constexpr const char* p = "PR value";
};

template<typename T>
struct ValCat <T&> {
	static constexpr const char* p = "L value";
};

template<typename T>
struct ValCat <T&&> {
	static constexpr const char* p = "X value";
};

#define pvcat(expr) std::cout << "Value category of expr '" #expr "' is : " << ValCat<decltype((expr))>::p << '\n'
```
# Auto Type Deduction
Type deduction is a compile-time tool. Now we look only to auto type deduction. With the auto keyword, we must initialize the variable. We can use the following initialize methods: #Cpp_keyword_auto
```cpp
int main()
{
	auto x = 12;
	auto y{ 12 };
	auto z(12);
｝
```
There are differences between these initialization methods, but we will mention them later.

There are some rules to deducting a type. These rules are complicated. We have three separate rule sets and we must learn them separately.

The following syntaxes have different type deduction rules:
```cpp
auto x = expr;
auto &x = expr;
auto &&x = expr; // This is not a R value referance. This is a forwarding reference (universal reference)
```

If the auto keyword is used with "&&" like "auto &&x = expr;" this means **forwarding reference**, not the R-value reference. Or it is named **universal reference**. We will learn it later. #Cpp_ForwardingReference #Cpp_UniversalReference
For now, we will be focusing first two.
## auto x = expression; 
The auto works as a placeholder. That means the compiler puts a type in place of auto.

What rules are implemented to determine which type has replaced the auto keyword?
Here are some examples:
```cpp
 int main()
{
	char c = 'A'; 
	auto x1 = 12; // The type is int
	auto x2 = 3.4; // The type is double
	auto x3 = 3.4L; // The type is long double
	auto ×4 = 'A'; // The type is char
	auto x5 = nullptr; // The type is nullptr_t
	auto x6 = 10 > 5; // The type is bool
}
```

```cpp
int main()
{
	char c ='A';
	
	auto x1 = c; // Type of x1 is char 
	auto x2 = +c; // Type of x2 is integer, beceuse there are integral promotion rule 
}
```

> [!note] Note: Integral Promotion Rule
If an operand has an operator, under integer types are promoted to integer.

```cpp
 int main()
{
	int x = 10;
	double d = 3.4;
	auto x = × > 5? 3 : d; // type of x is double, type conversion rules is valid here
}
```

```cpp
int main()
{
	int x = 10;
	int& r = x;
	
	auto a = r; // Type of a is int, references are ignored in this situation
}
```

```cpp
int main()
{
	const int x = 10;
	auto a = x; // type of a is int, const is ignored
}
```
const and reference are ignored when auto type deduction.

```cpp
int main()
{
	int x = 10;
	const int& cr = x;
	
	auto a = cr; // Type of a is int, const and reference are ignored
}
```

### Const differs with address types

```cpp
int main()
{
	int x = 10;
	const int y = 20;

	&x; // Type of &x is "int*"
	&y; // Type of &y is "const int*"

	int a[10]{};
	const int b[10]{};

	&a[0]; // Type of a is "int*" (array decay is the same)
	&b[0]; // Type of b is "const int*" (array decay is the same)
}
```

Look at the below code. What is the deducted type with a second auto?  
```cpp
int main()
{
	int a[] = {1, 2, 3 };
	auto b = a; // type of b is "int*" , array decay

//---------

	const int a[] = {1, 2, 3 };
	auto b = a; // type of b is "const int*" , array decay
}
```

We expect the deducted type "int \*" because "const" is ignored. But the deducted type with the second auto is "const int \*". "Const" makes the difference between variable types and address types. The address type of the "const int a[];" is "const int \*".

```cpp
 int main()  
{
	auto x = "mesut" ; // type of x is "const char *"
}
```

#### This Const Type Difference is also related to the Function Overloading
#Cpp_FunctionOverloading 
Q: Is the following a function redeclaration?
A: Yes, this is a function re-declaration. If the parameter is not a pointer, there is no difference if the argument is const or not.
```cpp
void func(int a);
void func(const int a);
```

Q: Is the following a function redeclaration?
A: No, this is not a function redeclaration. These functions are separate.
```cpp
void func(int* p);
void func(const int* p);
```

Q: Is the following a function redeclaration?
A: Yes, this is a function redeclaration.
```cpp
void func(int* p);
void func(int* const p);
```

> [!note] Note: Function signature difference with const arguments
> The constness of the parameter itself does not make a signature difference.

> [!note] Note: Differences between address types and variable types
> There is no type difference between a variable type and a const type of the same variable. That means "int" and "const int" are the same type. But, const makes a difference in pointers, "int \*" and "const int \*" are not the same type. But, "int \* p" and "int \*const p" are the same type. For example:

```cpp
// The following is not a function redeclaration. They are the declaration of different functions
void func(int* p);
void func(const int* p);

//The following code is a function redeclaration. They are redeclaration of the same functions
void func(int* p);
void func(int* const p);
```

```cpp
int foo(int);

int main()
{
	auto x = foo; // type of x is "int (*)(int)", it is a decay
	auto y = &foo; // type of x is "int (*)(int)"
	// Type of x and y are same
}
```

```cpp
int main()  
{  
	int a[10][20];  
	auto x = a; // Type of x "int (*)[20]", array decay
	// Type of the first member of the a is "int[20]", so array decay of a is "int (*)[20]"
}
```

## auto & = expression;

### Const difference
Const is not ignored with "auto&", and this is different from "auto".
Const difference between auto and auto&:
```cpp
int main()
{
	const int ival{};
	auto x = ival; // Type of x is "int", const is ignored

//--------

	int ival{};
	auto &x = ival; // Type of x is "int&" and deducted type with auto is "int"

//--------

	const int ival{};
	auto &x = ival; // Type of x is "const int&", and deducted type with auto is "const int". The expression is the same as "const int &x = ival;"
}
```

In the upper example,
At first example, const was being ignored. The type of "x" is "int".
In the second example, the type of auto and type of x differ. type of auto is "int" and type of "x" is "int&".  
In the third example, the type of auto is "const int" and the type of "x" is "const int&". 
So, with auto& const is not ignored.
### Array decay difference
array decay difference between "auto" and "auto&":
```cpp
int main()
{
	int a[10]{};
	auto &x = a; // int (&x)[10], auto -> int[10], x -> int (&)[10]
	// int (&x)[10] = a;
｝
```
In the upper example, the deduced type that corresponds to the auto is "int\[10\]".  x is a reference to that type: "int (&)\[10\]"

```cpp
int main()
{
	auto& r = "hello"; // "const char[6]" is corresponding the auto, the type of r is "const char (&)[6]"
	// cosnt char (&r)[6] = "hello";
}
```
So with "auto&" there is no array decay, but with "auto" there is.

### pointer reference difference
pointer reference difference between "auto" and "auto&".
```cpp
void foo(int);

int main)
{
	auto f1 = foo; // The deducted type for auto is:  "void (*)(int)"
	// void (*f1)(int) = foo;
	
	auto &f2 = foo; // The deduced type for auto is: "void (int)", type of f2 is: "void (&)(int)"
	// void (&f2)(int) = foo;
}
```

## We can combine the auto keyword with the other keywords
Additional Information: We can use auto keywords with other keywords. For example:
```cpp
int main()
{
	int x = 120;
	
	const auto y = x; // Type of y is "const int"
}
```

In the below example, the type of p1 and p2 is the same
```cpp
int main()
{
	int x = 10;
	auto p1 = &x; // auto = "int*"
	auto *p2 = &x; // auto = "int", p1 = "int*"
}
```

In the below example, const qualifies the name (p2), and that means it is a top-level const pointer.
```cpp
int main()
{
	int x = 10;
	const auto p2 = &x; // p2 = "int * const" , top level const pointer
}
```

In the below example, the error is not related to auto-type deduction. It is related to reference semantics.
```cpp
int main()
{
	auto &x = 10; // syntax error, not related to deduction, related to R-value, "int &x = 10;" (assigning an R-value expression to L value reference is a syntax error)
}
```

---
# Terms
#Cpp_Terms 
- Reference Semantic
- Operator Overloading
- Move Semantic
- Perfect Forwarding
- L-value reference
- R-value reference
- Value Category
- Primary value category
- Composite value category
- free store
- const correctness
- forwarding reference
- universal reference
---
Return: [[00_Course_Files]]