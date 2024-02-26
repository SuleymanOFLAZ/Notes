[[00_Course_Files]]
#Cpp_Course
# Reference Semantics
#Cpp_ReferenceSemantic
There is an alternative semantic to pointer semantics named <mark style="background: #BBFABBA6;">reference semantic</mark> in C++. This is a language layer tool. Compiler generates same assembly code as like as we use pointer semantic most of the time. Small differences can be depending on optimization level.

Reason to add reference semantic to the language is that pointer semantic not compatible with other language features like <mark style="background: #BBFABBA6;">operator overloading</mark>.

References was has been existed before modern C++. This was only called "reference". When <mark style="background: #BBFABBA6;">move semantics</mark> and <mark style="background: #BBFABBA6;">perfect forwarding</mark> tools were move in with modern C++, the "reference" concept is expanded and divided into two category, Old "reference" is called "<mark style="background: #BBFABBA6;">L value reference</mark>" and  "<mark style="background: #BBFABBA6;">R value reference</mark>" is added.

Why R value references added to the language? Modern C++ (C++11 and later) added various tool to the language. Some of these tools are very effected the language. These are move semantics, perfect forwarding, lambda expressions etc. R value references added to support these features.

> In conclusion there are two reference concept in C++:
> 1. L value reference
> 2. R value reference

Now L value references are mentioned. We will look to R value reference later.

> [!note] Note: Compiler Explorer
<mark style="background: #FFB86CA6;">godbolt.org</mark> is a web site that shows assembly code of given code.

# Value Category
#Cpp_ValueCategory
Value category: Qualification of an expression. That means <mark style="background: #ADCCFFA6;">int x;</mark> not has a value category. Value category qualify an expression like: <mark style="background: #ADCCFFA6;">x, 10, x+5, a[3], *ptr</mark>. There is no concept like value category of an declared variable. Date type and value category are different concepts. <mark style="background: #FFF3A3A6;">Value category describes which contexts is an expression can be used as legal and which situation is that expression be subjected to certain conversion operations</mark>. When we describe an expression's value category, we can determine the other tools of the language how reacts that expression and is there a syntax error or how to make inferences like similar situations.

Value categorization is differs between C and C++ and this makes it more complex. In C, an expression can be a L value expression or can be an R value expression. L value means that the expression corresponds an object and has a area on memory and we can access that area. We can test that with address of operator. If we can use address operator on the expression than it is a L value reference, and in the other way it is a R value reference.

But in C++, this is not that simple. Value category of an C expression may not be same as value category of C++. In modern C++, there are three value categories, and there are called <mark style="background: #BBFABBA6;">primary value categories</mark>.

> Primary value categories are:
> 1. <mark style="background: #BBFABBA6;">L value</mark>
> 2. <mark style="background: #BBFABBA6;">PR value</mark>
> 3. <mark style="background: #BBFABBA6;">X value</mark>

<mark style="background: #FFF3A3A6;">An expression can be belong to only one of the primary category in same time.</mark>. 
For example:
```cpp
x // L value
x + 10 // PR value

int&& foo();

foo() // X value
```

- L value: It was has been came from "left value". But after, it has been evaluated to "locators value".
- PR value: pure R value.
- X value: expiring value

There are <mark style="background: #BBFABBA6;">composite value categories</mark> too. There are:
- <mark style="background: #BBFABBA6;">GL value</mark>: L value and X value combination set is GL value. Generalized L value.
- <mark style="background: #BBFABBA6;">R value</mark>: PR value and X value combination set is R value. 
![[CppValueCategories.png]]

![[Value_Category]]

# L value references
#Cpp_LvalueReference
An reference is a name that replaces an object. L value reference:
```cpp
int x = 20;
int &r = x; // L value reverence

++r;
```
That means r is a reference to x. r means x. That means there are no two abject names x and r. There only one object that is x and r means x. & is not an operator here, & is a <mark style="background: #BBFABBA6;">declarator</mark>. For example:
```cpp
 r = 90; // we used x
 &r // we take the reference
 
///

int x = 10;
int& r =x; // Here, & is a declerator

int* p = &r; // Here, * is a declerator but & is a operator (address of) 
```

We bind r to x.
Example corresponds following syntax:
```cpp
int x = 20;
int* const ptr = &x;

++(*ptr);
```

That make easier to writing and ensures the compliance with C++ tools like operator overloading.

L value references can't point to another object after initial value. That means L value references corresponds to const top level pointers and means address that  L value references pointed can't be changed.

All initialize methods can be used with  L value reference syntax. (<mark style="background: #FFF3A3A6;">Except default initialization</mark>) For example:
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

> [!note] Note
<mark style="background: #FFB8EBA6;">Additional information</mark>: There is another name of the heap in C++, that is <mark style="background: #BBFABBA6;">free store</mark>.

Let's look at some legal and illegal (syntax error) situations:
1. <mark style="background: #FFF3A3A6;">References must be initialized</mark>. That means references cannot be default initialized. For example:
```cpp
int main()  
{  
	int& x; // Syntax error, a reference must be initialied
}
```

2. <mark style="background: #FFF3A3A6;">We must initialize a r value reference with same type of the object.</mark>. If we violate that rule, that would be a syntax error. Note that, this is same with the pointer semantic in C++, but in C that differs. For example:
```cpp
int main()
{
	unsigned int x = 10;
	int& r = x; // Syntac error, not same type
}
```

3. <mark style="background: #FFF3A3A6;">We cannot initialize L value references with R value references</mark>. For example:
```cpp
int main()  
{  
	unsigned int x = 10;  
	int& r = 10;  // Syntax error, we cannot initialize with a R value
}
```

4. Following syntaxes are legal to use:
```cpp
int main()
{
	int a[]{ 1, 2, 3, 4 };
	int* p= a;
	int& r1 = a[2];
	int& r2 = *p;
}
```

5. And also followings is legal:
```cpp
int main()
{
	int x = 10;
	int* p{ &x };
	int* &r = p;

	++ *r; // increment of x
｝
```

Look for following:
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

<mark style="background: #FFF3A3A6;">There is no reference to reference as similar to pointer to pointer</mark>. For example:
```cpp
int main()
{
	// Classical pointer to ponter syntax
	int x = 10;
	int* p = &x;
	int** ptr = &p;

	// Followings syntax doesn't corresponds to pointer to pointer
	int x = 10;
	int& r1 = x;
	int& r2 = r1; // r2 is same as r1

}
```

<mark style="background: #FFF3A3A6;">Question: Can we take a reference to an array?</mark> The answer is yes, but first let's look how would we take a pointer to an array. Pointer to an array is different from pointer to an array member. For example:
```cpp
int main()
{
	// Pointer to an array
	int ar[] = {2, 5, 6, 9 };
	int(*p)[4] = &ar;
}
```

Same as pointer to an array, reference to an array is possible:
```Cpp
int main()
{
	// Reference to an array
	int(&r)[4] = ar;
	r[2] = 34; // OK
	int *ptr = r; // OK, array decay
}
```
But upper there is less complex syntax that corresponds upper syntax. We can benefit of type deduction and we can write it like following:
```cpp
int main()
{
	int ar[] = {2, 5, 6, 9 };
	//int (&r)[4] = ar;
	
	auto& r = ar; // auto type deduction
｝
```

## Function that has reference argument

 ```cpp
 #include <iostream>

void func(int& r)
{
	r = 999;
}

int main()
{
	int ival{ 3 };
	
	sta:: cout << "ival= " « ival « "\n";
	
	func(ival);
	std:: cout << "ival = " « ival « "\n";
｝
```

<mark style="background: #FFF3A3A6;">Keep caution</mark> on that; we cannot determine that the <mark style="background: #ADCCFFA6;">func(ival);</mark> can be changed the arguments or not without showing func's definition in C++. In C, we can say it if the function is call-by-value or call-by-reference by seeings the function call. But in C++, because of the reference semantic we cannot say that and we must see the function implementation to say if the function can change the passed argument or not.

<mark style="background: #FFF3A3A6;">Important: Function call expression that the function return type is a L value reference is a L value expression in C++ language.</mark>  That means the function call expression to <mark style="background: #D2B3FFA6;">int& foo();</mark> is a L value expression but, the function call expression to <mark style="background: #D2B3FFA6;">int foo();</mark> is a PR value expression. For example the function call <mark style="background: #D2B3FFA6;">foo();</mark> is a L value expression in below code, because the function returns an L value reference:
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

Since <mark style="background: #D2B3FFA6;">int& foo()</mark> is a L value expression ve can assign it to a L value reference.
And we can use function <mark style="background: #D2B3FFA6;">foo();</mark> call at a left side of the assignment operator: <mark style="background: #D2B3FFA6;">foo() = 999;</mark>. 

<mark style="background: #FFF3A3A6;">Keep caution</mark> on that: In expression <mark style="background: #ADCCFFA6;">int x = foo();</mark> , x not means g. So x not changes the g and it is a different object. But with syntax <mark style="background: #ADCCFFA6;">int& r = foo();</mark> , r means g.

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
<mark style="background: #FFB8EBA6;">Additional Information</mark>: <mark style="background: #BBFABBA6;">Array decay</mark>.

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
If we want to use an L reference to only access, we use const L value reference. we use const keyword to do that.

Remember, L value references are not re-bindable. That means L value references must be initialize a value and they are same as const top level pointer. But if we want to make them as low-level const pointer ve use "const reference". By making a L value reference const, we cannot change the value that the reference is refers to. That is called "const L value reference".

<mark style="background: #BBFABBA6;">Const correctness</mark> is determines the code quality and it is very important.
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

Top level "const" keyword can be used with references, but there is no meaning of using it because of already a reference is top level const. That means a reference is not re-bindable. For example:
```cpp
int & const ref = x; // OK, but there is no meaning of it. It is same as int &ref = x;
```

> [!note] Note: Strict Aliasing Rule
> Rules of address types conversions. There are some legal rules. For example conversion to char* is legal both in C and C++ (with type conversion operator).
> #Cpp_StrictAliasingRule

```c
int main()
{
	double x = 10.5;
	int* ptr = (int*)&x; //strict aliasing rules
}
```

<mark style="background: #FFF3A3A6;">Normally reference type conversion isn't legal in C++. But if the reference type is a const type, type conversion becomes legal</mark>. For example:
```cpp
int main()
{
	int x = 10;
	
	double& r = x; // This is not legal
	const double& r = x; // This is legal
}
```
But how r replaces an another type in upper example? The answer is it doesn't, <mark style="background: #FFF3A3A6;">r replaces a double objects that generated by compiler</mark>. The compiler generates an temporary object with same type of the reference, than assign the value to it and links our reference to that temporary object. And the lifespan of this temporary objects last in scope of the reference's scope. This can be done because it is possible to implicit (automatic) type conversion from int to double. If it is not possible, than that doesn't work and that means syntax error.

The objects that not existed in source code but compiler generated is named <mark style="background: #BBFABBA6;">temporary object</mark>. This is a important topic of the C++. The compiler generated code is like following:
```cpp
int main()
{
	int x = 10;
	
	// double& r = x; // This is not legal
	const double& r = x; // This is legal
	/******  Following syntax represents the compiler generated code *******/
	{
		const double temp = x;
		const double& r = temp;
	}
	/******  End of representation *******/
}
```

<mark style="background: #FFF3A3A6;">what would be if we assign an R value to a const L value reference? This wouldn't be a syntax error</mark>. Because like the upper description the compiler creates a temporary object for that R value and assign out cont L value reference to it. For example:
```cpp
int main()
{
	const int& r = 5;
	/******  Following syntax represents the compiler generated code *******/
	{
		const int temp = 5;
		const int& r = temp;
	}
	/******  End of representation *******/
}
```

What that mean in practically? This means we can call the function <mark style="background: #D2B3FFA6;">void foo(T &);</mark> with only L value category expression arguments. <mark style="background: #FFF3A3A6;">Bu we can call the function <mark style="background: #D2B3FFA6;">void foo(const T &);</mark> with the L value category expression arguments and also R value category expression arguments.</mark> 
## Pointer semantics vs reference semantics
Most of the time pointer semantics and reference semantics are alternative each other. But sometimes we cannot use one of them instead of other.

> 1. There is pointer to pointer but there is no reference to reference.
> 2. pointer array is possible but reference array is not possible. There is no such an  array that its members are references. But this need solved with <mark style="background: #BBFABBA6;">std::reference_wrapper</mark> class of C++ standard library. We will loot it later.
> 3. There is nullptr, but there is no null reference. This means if we use nullptr with pointers there is no corresponding here to references.
> 4. We can assign another address to a pointer, if the pointer is not top level const pointer. But we cannot change a reference. That means references is not <mark style="background: #BBFABBA6;">rebindable</mark>. Bu we can use std::reference_wrapper object instead of that.
> 5. A reference is corresponds the top level const pointer. Because the references must be initialized and we cannot change where it points later on.
> 6. Pointers can be default initialized but references not.

Let expanse upper bullets little bit:
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

And let look the reference semantic of same code. There is no reference to reference here:
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

2. Below code is for pointer array example:
```cpp
int main()
{
	int* p[10]; // An pointer array

	// there is no alternative of upper code with references
	int &r[3]; // Syntax error
}
```

3. Where nullptr or NULL is used?
1. address returning function like <mark style="background: #D2B3FFA6;">FILE* fopen():</mark>. returns NULL in case of failure. Or malloc() is same. This kind return a address of the object in case of success, other way it return NULL. Also same system functions or 3rd party codes uses this approach. There is no corresponding approach to that method with reference semantic, because of there is no null reference.
2. NULL is used frequently in search functions. They return the address of the objects if it founds and NULL if it is not found.
3. NULL function argument can be used to give an option to the caller. For example a function takes an pointer argument and if the value of the pointer is NULL, the function makes different operation. For example <mark style="background: #D2B3FFA6;">fflush()</mark>, if we give it a file object address, the function flashes the file's buffer; but if we give it NULL, the function flashes the all open files buffer. 

In upper cases or similar cases that NULL is used, we cannot use references. In this cases we use pointers. Or we can use alternative tools of C++ such as <mark style="background: #BBFABBA6;">std::optional</mark>.

<mark style="background: #FFF3A3A6;">We can declare function reference like function pointers</mark>. For example:
```cpp
int foo(int);

int main()  
{  
	int (*fp)(int) = foo;  
	int (&fpr)(int) = foo;
}
```

# R Value Reference
Now we will briefly mention about R value references. We will mention it later in detail with move semantics and perfect forwarding. R value references came with modern C++.
- We can initialize L value references with L value expressions.
- We can initialize R value references with R value expressions.
- We can initialize const  L value references with both L value expressions and R value expressions.
- An R value reference must be initialized. (Same as L value reference)
R value references can be created with <mark style="background: #FFF3A3A6;">&& declarator</mark>.
```cpp
int&& r = 10; // r is a R value reference
```
<mark style="background: #FFF3A3A6;">R value references must be initialized with an R value expression. If we try to initialize it with an L value expression, it would be a syntax error.</mark>

# Expression's Value Category
#Cpp_ValueCategory 
Which expression is L value and which expression is R value:
1. If an expression is exist of an variable name than it is a L value (only name).
2. Literal expressions is PR value.
3. If an expression consist of arithmetic operator, than it is a PR value.
4. Some expression that created with same operators are L value. Front ++ operator is one of them. For example ++x is a L value. But X++ is a PR value. It is same with -- operator.
5. Expression that created with comma if right expression of the coma is L value that the expression is a L value
6. Expression created with assignment operator is an L value
7. If an function return type is not a reference that a call to that function is an PR value
8. If an function return type is a L value reference that a call to that function is an L value
9. If an function return type is a R value reference that a call to that function is an X value
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
	pvcat(x + 4); // PR value because it consist of an arithmetic operator
	pvcat(a); // L value because it is a name
	pvcat(a[3]); // L value
	pvcat(++x); // L value because of pre increment operator
	pvcat(x++); // PR value because of post incemetn operator 
	pvcat(--x); // L value because of pre decrement operator
	pvcat(X--); // PR value because of pre decremetn operator
	pvcat((x, y)); // L value because right handside of coma is a L value
	pvcat(x = 5); // L value because of assignment operator
	pvcat (x += 5); // L value because of assignment operator
	pvcat(foo()); // PR value because function doesn't return a reference
	pvcat(func()); // L value because function returns an L value referenca
	pvcat(bar()); // X value because function returns an R value reference
	pvcat(r); // L value because r is a name
	pvcat(foo); // L value because foo is a function name
	pvcat(nullptr); // PR value because nullptr is a literal
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

	const int &r2 = x++; // OK. we can assing a PR value to const L value reference
}
```

**Value category comparison with C**:
1. ++x is R value expression in C
2. x, y (comma operator) is R value expression in C
3. x > 10 ? a : b is R value expression in C. (L value expression in C++)

> [!note] Note:
> Pointer or reference function parameters can be take default value.

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
Type deduction is a compile time tool. Now we look only auto type deduction. With auto keyword we must initialize the variable. We can use following initialize methods:
```cpp
int main()
{
	auto x = 12;
	auto y{ 12 };
	auto z(12);
｝
```

There are differences between these initialization methods, but we will mention it later.

There are some rules to deducting a type. This rules are complicated. We have three separated rule set and we must learn them separately.

Following syntaxes have different type deduction rules:
```cpp
auto x = expr;
auto &x = expr;
auto &&x = expr; // This is not a R value referance. This is forwarding reference (universal reference)
```

If auto keyword is used with && like <mark style="background: #D2B3FFA6;">auto &&x = expr;</mark> this means <mark style="background: #BBFABBA6;">forwarding reference</mark>, not the R value reference. Or it named <mark style="background: #BBFABBA6;">universal reference</mark>. We will learn it later.
For now, we will be focusing first two one.
## auto x = expression; 
auto works an placeholder. That means the compiler puts a type in place of auto.

<mark style="background: #FFF3A3A6;">What rules are implemented to determine which type is replaced the auto keyword?</mark>
Here some examples:
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
	auto a = x; // type of a is int, cosnt is ignored
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

	&a[0]; // Type of a is "int*" (array decay is same)
	&b[0]; // Type of b is "const int*" (array decay is same)
}
```

Look to the below code. What is the deducted type with second auto?  
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

We expect the deducted type "int \*" because "const" is ignored. But the deducted type with second auto is "const int \*". "Const" makes difference between variable types and address types. Address type of the <mark style="background: #ADCCFFA6;">const int a[];</mark> is <mark style="background: #ADCCFFA6;">const int *</mark>.

```cpp
 int main()  
{
	auto x = "mesut" ; // type of x is "const char *"
}
```

Q: Is the following a function redeclaration?
A: Yes, this is a function re-declaration. If the parameter is not a pointer, there is no difference if the argument is const or not.
```cpp
void func(int a);
void func(const int a);
```

Q: Is the following a function redeclaration?
A: No, this is not a function redeclaration. These functions are separate functions.
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
> constness of the parameter itself is not makes signature difference.

> [!note] Note: Differences between address types and variable types
<mark style="background: #FFB8EBA6;">Important Note: Additional info: </mark>There is no type difference between a variable type and const type of the same variable. That means <mark style="background: #D2B3FFA6;">int</mark> and <mark style="background: #D2B3FFA6;">const int</mark> is same type. But const makes difference in pointers; <mark style="background: #D2B3FFA6;">int \*</mark> and <mark style="background: #D2B3FFA6;">const int \*</mark> is not same type. But <mark style="background: #ADCCFFA6;">int \* p</mark> and <mark style="background: #ADCCFFA6;">int \*const p</mark> is same type. For example:

```cpp
// Following is not a function redecleration. They are the decleration of different functions
void func(int* p);
void func(const int* p);

// Following code is a function redecleration. They are redecleration of same functions
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
Const is not ignored with "auto&", and this is different form "auto".
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
	auto &x = ival; // Type of x is "const int&", and deducted type with auto is "const int". The expression is same as "const int &x = ival;"
}
```

In upper example,
At first example, const was being ignored. Type of x is int.
At second example, type of auto and type of x are differ. type of auto is int and type of x is int&.  
At third example, type of auto is const int and type of x is const int&. 
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
In upper example, the deducted type that corresponds to the auto is "int\[10\]".  x is reference to that type: "int (&)\[10\]"

```cpp
int main()
{
	auto& r = "hello"; // const char[6] is corresponds the auto
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
	//auto f1 = foo;
	void (*f1)(int) = foo; // The deducted type for auto is:  "void (*)(int)"
	
	//auto &f2 = foo;
	void (&f2)(int) = foo; // The decucted tyep for auto is: "void (int)", type of f2 is: "void (&)(int)"
}
```

Additional Information: We can use auto keyword with other keywords. For example:
```cpp
int main()
{
	int x = 120;
	
	const auto y =X;
}
```

In below example, type of p1 and p2 is same
```cpp
int main()
{
	int x = 10;
	auto p1 = &x; // auto = "int*"
	auto *p2 = &x; // auto = "int", p1 = "int*"
}
```

In below example, const qualifies the name (p2), and that means it is a top level const pointer.
```cpp
int main()
{
	int x = 10;
	const auto p2 = &x; // p2 = "int * const" , top level const pointer
}
```

In below example, the error is not related to auto type deduction. It is related to reference semantics.
```cpp
int main()
{
	auto &x = 10; // syntax error, not related to deduction, related to R value: int &x = 10; (R value expression to L value reference syntax error)
}
```

---
# Terms
#Cpp_Terms 
- Reference Semantic
- Operator Overloading
- Move Semantic
- Perfect Forwarding
- L value reference
- R value reference
- Value Category
- Primary value category
- Composite value category
- free store
- const correctness
- forwarding reference
- universal reference
---
Return: [[00_Course_Files]]