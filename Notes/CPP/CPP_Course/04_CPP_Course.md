[[00_Course_Files]]
#Cpp_Course 
# constexpr
#Cpp_constexpr
constexpr is added to the language after modern C++. Constexpr says to the compiler and the reader that the object created as constexpr is can be used where literal expression is needed.

const is a part of type, but constexpr is not. That means there is a type like "const int" but there is no type like "constexpr int". Type of an object declared with constexpr is const. For example:
```cpp
constexpr int x = 10; // Type of x is "const int"
```

A const expression must be initialized with a constant expression. constexpr comprehends the const but also <mark style="background: #FFF3A3A6;">can be used in places where constant expression is needed.</mark>
   
Let's look at const first. We can use a const in places where a constant expression needed, if only the const is initialized with a const expression. For example:
```cpp
const int x = y; // Not used in places that needs constant expression
const int c = 10; // Can be used as constant expression
```

If we use "constexpr" keyword instead of "const", we assign a responsibility to the compiler about the qualified object is must be able to used at places that constant expression is needed. This responsibility is for compile time. If the object is not be able to used at places that requires constant expression, the compiler throws syntax error. The compiler checks, if the object qualified with constexpr is initialized with a constant expression or not.

If we process a constexpr object with a constant expression, the result of it is also constexpr.
```cpp
constexpr int x = 10;
x + 20; // That expression is "constexpr" too
```

We can write following but, there is no need for this because constexpr comprehends the const:
```cpp
constxpr const int x = 10; // Actually there is no need for const here
constxpr int x = 10;
```
The type of x is const int in upper two lines. And there is no difference between them.

But there is difference between two expression in below code, "constexpr" qualifies the object, so it makes the object const (top level const pointer):
```cpp
const int* p = &g; // This is low level const means p is not const itself
constexpr const int* q = &g; // Here q is const too
q = &x; // Syntax Error
//---
constexpr int* q = &g; // q is const but what it ponits to is not const
q = &x; // Syntax error, q is const
*q = 12; // OK

```

Following syntax is OK. "constexpr" qualifies the top level.
```cpp
int g = 10;

int main()
{
	constexpr int *p = &g;
	*p = 99;
}
```

Constexpr array. It makes the effect that the array is const. So we can use the array members in places that requires constexpr:
```cpp
constexpr int a[] = { 1, 3, 5, 7}; // all of members are const
// Type of a is const int[4]
```

We can use constexpr with auto:
```cpp
constexpr auto b = 10 > 5; // OK

const int y = 40;
constexpr int z = 909;
constexpr auto x = y + z; // OK
```
## Constexpr Functions
#Cpp_constexpr_functions
We can qualify a function with "constexpr" keyword too.
constexpr function syntax:
```cpp
constexpr int func(int x, int y)
{

}
```

<mark style="background: #FFF3A3A6;">There are conditions to ensure a constexpr function.</mark> The compiler checks these conditions. We inspire these rules later but let's look some of them. 
1. static local variable can't be used inside the function
2. parameters, return type and all variables that defined inside the constexpr function must be <mark style="background: #BBFABBA6;">literal type</mark>.
3. constexpr functions definition must be inside header file. Because compiler must know the function definition to calculate at compile-time.

> [!note] Note: Literal types
> Types that can create objects that can be used as constants (it can be class) called as literal type.
> For example std::string is not a literal type. So if we use it as function parameter, We break the rule of the constexpr function.

<mark style="background: #FFF3A3A6;">If all the arguments that passed to the constexpr function is constexpr variables, the function is calculated in compile time</mark>. 

```cpp
constexpr int func(int x, int y)
{
	return x * x + y * y;
}

int main()
{
	constexpr int a = 7;
	const int b = 5;
	
	int ar[func(10, 20)] {}; // OK
	constexpr auto val = func(a * a, b + 13); // OK
}
```

That means a call to a constexpr function with constexpr parameters is evaluated as constant expression.

<mark style="background: #FFF3A3A6;">If we don't pass a constexpr argument to the constexpr function that it is calculated at run time.</mark>
```cpp
constexpr int func(int x, int y)
{
	return x * x + y * y;
}

int main()
{
	int a = 7;
	int b = 5;

	func(a, b); // This is calculated at runtime
	int ar[func(a, b)] {}; // This is syntax ERROR because the function not called with constxpr
	constexpr int c = func(a, a + 21); // syntax ERROR because function return is not constexpr, the error not related to the function call
	int e = func(a, a + 21); // OK, calculated at runtime
}
```

> [!note] Note: Constexpr functions are implicitly inline functions
<mark style="background: #FFF3A3A6;">Constexpr functions are implicitly inline functions</mark>. That means following the syntax is same:

```cpp
constexpr int foo(int x)
{
	return x * x;
}
//-----
inline constexpr int foo(int x)
{
	return x * x;
}
```

> [!example] Example: isprime

```cpp
constexpr bool isprime(int x)
{
	if (× == 0 || x == 1）
		return false;

	if (× % 2 == 0)
		return x == 2;
	
	if (× % 3 == 0)
		return x == 3;
	
	if (× % 5 == 0)
		return x == 5;
		
	for (int i = 7; i * i <= x; i += 2)
		if (× % i == 0)
			return false;

	return true;
} 

int main()
{
	constexpr int x = 123;
	constexpr int y = 434;

	constexpr bool b = isprime(x-y+1); // Calculated at compile time

	if(isprime(a)) // Run-time
		std::cout << "...";

	constexpr bool bb = isprime(a); // Syntax error
}
```

> [!note] Note: "for" Difference between C and C++
Let's look a code piece:
```cpp
int main()
{
	for (int i = 0; i < 10; ++i)
		int i = 99; // syntac error in C++ but not an error in C
}
```
This is a syntax error in C++ but not a syntax error in C. We define two same named variable in same scope and that caused syntax error. But why this isn't a syntax error in C? Because in C there is a seamless curly braces at start of the for and end of the for. That is because it was designed like that. So the corresponded code would be look like following in C:
```c
// Corresponded C code
int main()
{
	for (int i = 0; i < 10; ++i)
	{
	{
		int i = 99;
	}
	}
}
```
#Cpp_C_difference _

# Function Overloading
#Cpp_FunctionOverloading
Function overloading is one of the most useful feature of the language. We can give same name to various functions that in same scope with using function overloading. This is a compile time feature.
<mark style="background: #BBFABBA6;">Function overload resolution</mark> states how compiler choose convenient function between overloaded functions. This increased the size of the compiler and for that reason C doesn't support that tool in order to keep small the compiler size.

<mark style="background: #FFF3A3A6;">If we encounter first time with a C++ tool, we must ask that question first is it has a effect in runtime of the program. This question is also frequently asked in interviews.</mark> So, <mark style="background: #FFF3A3A6;">is there a cost of function overloading at run time of program? The answer is no.</mark> Because this is completely compile time operation. 

> [!note] Note: Two types of function overload question
> There are two question that is confused each other in interview questions:
>1. Is there function overload in the example?
> 2. Which function will be called in case of function overloading? (Related to function overload resolution)

**What is the conditions that required for function overloading?**
There are three needed condition for function overloading:
1. Functions must have same name.
2. Functions must be inside the same scope. Different scopes hides names each other. That named <mark style="background: #BBFABBA6;">name hiding</mark> or <mark style="background: #BBFABBA6;">name masking</mark> or <mark style="background: #BBFABBA6;">name shadowing</mark>.
3. Signature of overloaded functions must be different.

**Function Signature**
<mark style="background: #BBFABBA6;">Function signature</mark> is parametric structure of the function except return type. For example, signature of the function <mark style="background: #D2B3FFA6;">int foo(int, int);</mark> is that the function has two int argument.

<mark style="background: #FFF3A3A6;">If signatures of two functions are same but return types are different, this is a syntax error.</mark> The compiler evaluates that situation there is function redeclaration (because the signatures of the functions are same) and return type of the redeclared function is different. That causes the syntax error. But if the returns values are same too, this evaluated like redeclaration of the function by the compiler without syntax error.

Functions must be in same scope for mentioning a function overloading. For example below example is not a function overloading:
```cpp
int foo(int);

int main()
{
	int foo(int, int); // Not a overload, they are not in same scope
}
```

Functions that have same signature but have different return type is syntax error. Bu if the signatures different, the return type difference do not cause a syntax error.
```cpp
// Syntax ERROR example
int foo(int);
double foo(int); // Syntax ERROR.
--------
// Redecleration example
int foo(int);
int foo(int); // OK. This is a redecleration, not a overload
--------
// Different signature and return type example
int foo(int);
double foo(int, int); // OK. Because the signatures are different
```

<mark style="background: #FFF3A3A6;">Changing the type name (but just type name, the type is not changed) is not a function overloading. This is function redeclaration. </mark>For example:
```cpp
typedef int Word;

void foo(int);
void foo(Word); // This is redecleration, not a overloading Because the Word is int too
```

## Is there a function overloading?
Let's look some is it function overloading or not question that asked in interviews.

```cpp
void func(char);
void func(unsigned char);
void func(signed char);
```
Q: How many function overload are there in upper code?
A: The answer is three. char, unsigned char and signed char types are distinct from each other in C++.

> [!warning]  Warning: 
> Only the char and signed char is different in C++. Int and signed int are same, so in case of int that wouldn't be a function overload.

> [!note] Note: About "char" Type
> A variable created with only "char" keyword can be signed or unsigned depending on compiler. This means if the char signed or unsigned is "unspecified", but "implementation defined". But this makes the "char" different types. So "char", "signed char", and "unsigned char" are three different type in C++. They makes the function signature different. This rules are same in C too.
#Cpp_C_difference 

```cpp
void func(int x, int y = 5);
void func(int x);
```
Q: Is there a function overloading in upper code?
A: Yes, there is. Default argument is not effect the function signature.

```cpp
void func(int);
void func(const int);
```
Q: Is there a function overloading in upper code?
A: No. This is a function redeclaration. Top level constness (constness of the parameter itself) doesn't change the function signature. 

```cpp
void func(int* p);
void func(const int* p);
```
Q: Is there a function overloading in upper code?
A: Yes, there is. This overloading is used frequently and named as "const overloading".

```cpp
void func(int* p);
void func(int* const p); // This is redeclaration of first one
void func(const int* p); // This is function overload
```
Q: How many function overload are there in upper code?
A: The answer is two. Because first two is redeclaration. In 2nd declaration the const is for object itself and that not change the function signature.

```cpp
void func(int&);
void func(const int&);
```
Q: Is there a function overloading in upper code?
A: Yes, there is. This is used frequently to. Actually this is same as that we done with pointers (const overloading).

```cpp
#include <cstdint>

void f(int32_t);
void f(int);
```
Q: Is there a function overloading in upper code?
A: It depends on the situation. uint_32 is a typedef declaration and a homonym. It can be differ depending in compiler. So that can be a function overload or can't be too depending on the compiler.

```cpp
void f(int32_t);
void f(int64_t);
```
Q: Is there a function overloading in upper code?
A: Yes, there is.

```cpp
void func(...); // variadic function
void func(int);
```
Q: Is there a function overloading in upper code?
A: Yes, there is. The function signature is different

```cpp
void func(int&);
void func(int&&);
```
Q: Is there a function overloading in upper code?
A: Yes, there is. L value reference and R value reference makes the function signature different.

```cpp
void func(bool);
void func(int);
```
Q: Is there a function overloading in upper code?
A: Yes, there is. Bool and int are different types in C++.

> [!note] Note: Variadic function difference between C and C++
> There must be at least one parameter before the variadic parameter in C. But in C++, there is no need for this. A variadic function can be declared with only variadic parameter.
#Cpp_C_difference
# Function Overload Resolution
#Cpp_FunctionOverloadResolution
Existence of function overloading is not related to function overload resolution. Function overload resolution is about which overloaded function will be called after a overloaded function call.
If there is function overload, the compiler determines which function will be called depending on language's complex rules. We must understand this rules very well.

**How a function overload resolution can be concluded?**
Function overload resolution can be concluded in two different way:
1. The compiler determines the which function is called depending on language rule and bind the function call to that function.
2. An error occurs during the function overload resolution. The compiler throws syntax error in case of function overload resolution error. This can be happening in two different ways:
	1. There is an overload in the case, but the function call is not compliance with one of them. Even if the each function is existed only itself, the function call is would be a syntax error.  In this case, the compiler says there is no convenient function to the function call.
	2. There are two or more options to choose but the compiler rules doesn't let to choose one of them. That means two of the function is valid for the function call but it is impossible to determine which one is the right one to chose. This situation named <mark style="background: #BBFABBA6;">ambiguity</mark>. If one of the function is not exist, the ambiguity doesn't exist and compiler will choose the other one.

> [!question] Question: An example that shows the importance of the topic
> Q: Is there overload? Which one is chosen in case of overload?
> A: Syntax error due to ambiguity.
```cpp
void func(long double);
void func(char);

int main()
{
	func (3.4);
}
```
Upper code is a syntax error due to ambiguity. We would expect that the 3.4 is double and depending on type conversion there is no loss to type conversion from double to long double, so the function overload resolution is conclude with long double function call. But this assumption would be wrong. <mark style="background: #FFF3A3A6;">In conclusion we must learn the rules, assumptions are doesn't work here</mark>.

## How function overload resolution works?
Now, let's look at how the function overload resolution works. The compiler determines which function will be called with 4 step.
### Function overload resolution: Phase 1
The compiler makes the list of functions that has same name and in the same scope when a function call is made. That means the compiler makes the list of functions that can be used in function overload resolution. The only things that the compiler cares at that step are is the functions have the same name and is the function in the same scope. This list is named <mark style="background: #BBFABBA6;">function overload set</mark>. Of course, the function signatures must be different if it doesn't that would be syntax error. The arguments that used in function calls isn't considered at this step. The functions that assumed into the list is named <mark style="background: #BBFABBA6;">candidate functions</mark>.  
For example, there are six candidate function in below code:
```cpp
void func(long double);
void func(char);
void func(int *p);
void func(Color);
void func(nullptr_t);
void func(nullptr_t, int);
```

### Function overload resolution: Phase 2
The compiler checks the function call is legal or not with the arguments that used in function call for each candidate function. The compiler ask that question for each candidate function: Is that function call with those arguments
is legal or a syntax error.

Following functions can't pass the check:
- If the number of arguments doesn't match, the function is eliminated.
- If the there is no automatic type conversion between types, the function is eliminated.

Following functions pass the check:
- If there is automatic type conversion between the function's argument and the caller's argument, the function pass the check and the compiler doesn't care is there a value loss or not with that type conversion.
- If there function has more argument but the excess arguments has default value, the function pass the check.

The functions that pass that check is called <mark style="background: #BBFABBA6;">viable functions</mark>. The function call would be legal if there was only one of the viable function.

With another expression, In order for being a viable function, the function has to establish followings:
1. The number of argument that in function call must match the number of the function's argument. (default argument and variadic parameter are included)
2. There must be a legal conversion for each argument to the function's relevant parameter variable
 
```cpp
enum Color {black ,red, green};

void func (long double); // This is legal. There is type converion double to long double
void func (char); // This is legal. There is type conversion double to char
void func(int *p); // This is NOT legal. There is not type conversion double to int*
void func(Color); // This is not legal. There is not type conversion double to Color
void func (nullptr_t); // This is not legal. There is not type conversion double to nullprt_t
void func(nullptr_t, int); // This is not legal. Number of arguments doesn't match
void func(int, int = 10, int = 20); // This is legal. The excess arguments has default value

int main()
{
	func(3.4);
}
```

If there is no viable function left in the second phase, that is a syntax error and named <mark style="background: #BBFABBA6;">no match</mark>. The compiler throws following error message: "no instance of overloaded function "func" matches the argument list". 

If there was only one viable function left in that phase, that function would be chosen. That means function overload resolution ends here.

If there are two or more viable functions left the function overload resolution is continue with 3rd phase.
### Function overload resolution: Phase 3
In this phase the viable functions are separated into <mark style="background: #BBFABBA6;">electability categories</mark>.  Lets look:

1. The least electable functions are variadic functions.
```cpp
void f(int);
void f(...); // Least electable 
```

2. There is a special conversion category that we not met yet. We met them when we will look classes. This conversion is <mark style="background: #BBFABBA6;">user-defined conversion</mark>. 
> [!note] Note: User defined conversion
We can define functions for type conversion according to the rules of the language at places where there is no automatic type conversion. The compiler uses the function for the conversion. The conversion functions called <mark style="background: #BBFABBA6;">conversion constructor</mark>. An example of user-defined conversion:
```cpp
// User-defined conversion
class Myclass {
public:
	Myclass();
	Myclass(int); // This is a converion constructor
};

void func(Myclass); // Win over variadic conversion
void func(...);

int main()
{
	func(12);
}
```
User defined conversions are chosen over variadic conversions, but not chosen over all other conversions.

3. Any other conversions that legal according to the rules of the language. This conversions are dominant to the variadic functions and user defined conversions. This kind of conversions are called <mark style="background: #BBFABBA6;">standard conversion</mark>.

The mentioned conversions are easily electable between each other with following order (first is least electable):
- variadic conversion
- user-defined conversion
- standard conversion
### Function overload resolution: Phase 4
What if there is more than one standard conversions? Standard conversion are divided into own categories.

There are three standard conversion (first is most electable):
- exact match
- promotion
- other conversion
#### The exact match
The best situation is <mark style="background: #BBFABBA6;">exact match</mark>. That means the parameter types and the argument types are exactly same.
```cpp
void func (double);
void func (float);
void func (char);
void func(long); // This is exact match

int main()
{
	func(12L);
}
```

The const conversion is included in exact match. For example the conversion from int\* to const int\* is accepted exact match.
```cpp
void func(const int *);

int main()
{
	int x = 10;
	func(&X); // The converion from "int*" to "const int*" is accepted exact match
}
```
> [!warning]  Warning: Reverse situation is not viable
> We cannot call a "int\*" parameter function with "const int\*" type. Because there is no type conversion form "const T\*" to "T\*" in C++. There is only type conversion from "T\*" to "const T\*".

Array decay is accepted as exact match:
```cpp
void func(const int *);

int main()
{
	int a[] = {1, 2, 3};
	func(a); // The type of the argument is "int[3]" the type is converted to "int*" type with array decay. That means exact match
}
```

Decay of an function is accepted as exact match:
```cpp
int foo(int);

void func(int (*)(int));

int main()  
{  
	func(foo); // This is exact match because of decay.
}
```

#### The promotion
There are two type of promotion in C++. These are:
1. Integral promotion (comes with C)
2. float to double promotion

**Integral Promotion**
Integer subspecies are converted to int. 
Int subspecies are:
- short, signed short and unsigned short
- char, unsigned char, signed char
- bool

**Float to Double Promotion**
There is a special promotion that is float to double. But only float to double. There is no promotion from float to long double or double to long double.

#### The other conversions
If the conversion is not a exact match or promotion, this conversion is least electable between others.

## Let's look some function resolution examples

```cpp
void func(int);
void func(double);
void func(char);

int main()
{
	func(12); // First function is called because of exact match
	func(3.4f); // Second function is called because of float to double promotion
	funct(12u); // This is ambiguity
}
```

> [!note] Note: 
> "unsigned int" and "int/signed int" are not same type. So if we call a function that has an "int" argument with an "unsigned int" type, that wouldn't be exact match. It would be standard conversion.

```cpp
void func(long double);
void func(char);

int main()
{
	func(2.3); // ambiguity. There is no promotion from double to long double
}
```

```cpp
void func(bool);
void func (int);
void func(double);

int main()
{
	func(10 > 5); // First function is exact match
}
```

```cpp
void func (int);
void func(double);

int main()
{
	func(10 > 5); // First function is integral promotion
}
```

```cpp
enum color {black, green};
void func(color);
void func(int);
void func (double);

int main()
{
	func(green); // First function is exact match
}
```

```cpp
enum color {black, green};
void func(int);
void func(double);

int main()
{
	func(green); // Int is called, because there is implicit type conversion from enum to int
}
```

> [!note] Note: 
> In case of "enum class", upper example is not valid. Because there isn't a type conversion from an "enum class" type to int

```cpp
enum color {black, green};
void func(color);
void func(int);
void func (double);

int main()
{
	func(10); // Second function is exact match and first isn't viable, there is no implicit type conversion from int to an enum type,this is not C.
}
```

```cpp
enum color {black, green};  
void func(color);
void func(int);
void func (double);

int main()
{
	func(10.f); // Third is promotion
}
```

```cpp
enum color {black, green};
void func(color);
void func(int);
void func (double);

int main()
{
	func(10u); // Ambiguity, "10L" would be ambiguity too
}
``` 

> [!note] Note: Null pointer conversion
> 0 was used as null pointer before modern C++. We make a call like that "func(0);" if we want to call "void func(int* p)". This is named "null pointer conversion". We not prefer to use it after modern C++.

> [!warning]  Warning: 
> Let's think a situation that we made a function call like that "func(0);" to call to "void func(int* p);". But after that we include a header file that has the function "void func(int);". This is not a syntax error. Because the function that excepts int argument is chosen because of exact match. But we want to call the other function. We can't even figure out that our function isn't called. This is a very big problem.
> This situation is not an issue now with modern C++'s "nullptr" approach. nullptr_t has automatic type conversion to all pointer types, but has not to other types.

```cpp
void func(int * p);
void func(int);

int main()
{
	func(0); // Secont is called due to exact match
	func(nullptr); // First is called
}
```

> [!question] Question: Interview question
> There is a special situation. Keep caution on second example. This is related to "null pointer conversion"

#Cpp_Interview_Question

```cpp
void func(double);
void func(int *);
void func(int);

int main()
{
	func(0); // Third function is exact match 
}
```

```cpp
void func(double);
void func(int *);

int main()
{
	func(0); // Ambiguity. Sytax error. Two of them is standard converion. This is special situatuin. 0 is a nullptr literal
}
```

## Special cases of function overload resolution
#Cpp_ConstOverloading
1. We mentioned that calling "void func(const int*)" with "int*" is an exact match:
```cpp
void func(int*);
void func (const int*);

int main()
{
	int x = 10;
	funt(&x); // Exact Match, but which one.
```

But below code is function overloading.  This named <mark style="background: #BBFABBA6;">const overloading</mark>, and there is a special rule for this situation.
```cpp
void func(int*);
void func(const int*);
```

Which one is exact match? There must be a rule here that important for language.
Let's look in detail:
```cpp
//const overloading

void func(int*)
{
	std:: cout « "func(int *)In";
}

void func(const int*)
{
	std:: cout « "func(const int *)In";
}

int main()
{
	const int cx = 10;
	func (&cx); // Exact match to the second function. Already the first function is not viable.
}
```
Upper code is exact match to second function. <mark style="background: #FFF3A3A6;">The first one is not viable in this situation, because there is no type conversion from <mark style="background: #D2B3FFA6;">const T*</mark> to <mark style="background: #D2B3FFA6;">T*</mark> in C++</mark>. This is not a special case. 

But look at below code:
```cpp
 //const overloading

void func(int*)
{
	std:: cout « "func(int *)In";
}

void func(const int*)
{
	std:: cout « "func(const int *)In";
}

int main()
{
	const int cx = 10;
	int x = 20;
	
	func(&cx); // second functiion is called
	func(&x); // first function is called
}
```
<mark style="background: #FFF3A3A6;">Two functions are viable in upper code.  This is a language rule.</mark> <mark style="background: #FFF3A3A6;">Don't forger two of them are exact match.</mark>. 

<mark style="background: #FFF3A3A6;">The Rule is that</mark>: 
- For const object, function that takes const pointer is called.
- For non-const object, function that takes non-const argument is called.
This is a special case and language rule.

Const overloading is frequently used and there are a lot of examples in the standard library.
Corresponding reference semantic is valid too:
```cpp
void func(int&)
{
	std:: cout « "func(int *)In";
}

void func(const int&)
{
	std:: cout « "func(const int *)In";
}

int main()
{
	int cx = 10;
	func (cx);
}
```

> [!question] Question: A real example of const overloading
Strchr function has a problem in C. Because there is no function overloading in C.
Let's look at strchr (a C function)
```cpp
char* strchr (const char*, int c);
int main(
{
	const char str[100] = "bugun hava cok guzel";
	char c = 'u';
	auto p = strchr(str, c);

	*p = 'k'; // Unfedined behavior, but not a syntax error, we assign a value to a strign literal character
}
```
The return type of "strchr" is not "const char*". So we can assign a value to the returned pointer object. This can cause undefined behavior if we gave a string literal to the function for search.

This problem can be overcame in C++. If we look that function in C++'s standard library, we can see the function is overloaded:
```cpp
const char* Strchr (const char*, int c);
char* Strchr (char*, int c);

int main(
{
	const char str[100] = "bugun hava cok guzel";
	char s[100] = "bugun hava cok guzel";
	char c = 'u';
	
	auto p = Strchr(str, c);
	auto ptr = Strchr(s, c);

	*p = 'k'; // Not an undefined behavior, this syntax error, because p is a const
	*ptr = 'k'; // OK, ptr is not const
}
```
With that syntax, the called function will be the function that has "const char\*" according to the language rule (string literals must be const char\* in C++). And that returns a "const char\*" type. That solves the problem. We see how important is the const overloading with this example.

> [!question] Question: Interview Question (C and C++)
> Q: What is the difference between below two lines?
> A: First line is an array that has static lifespan. Actually we create two object with first line. First object is the array literal that has static lifespan. Second one a pointer variable that holds the address of the array. In C++ this pointer must be "const char \*".
> There is no char array that has static lifespan in second line. This is just a local variable.
```c
int main()
{
	char* p = "bilge"; // array has static lifespan, but p is not. (must be "cosnt char*" in C++)
	char str[] = "fatih"; // This has not static lifespan
}
```

2. There is a function overloading that we don't want
```cpp
void func(int);
void func(int &);

int main()
{
	func(10); // This calls first function, because the second is not viable, because we cannot assign a R valeu to an L value

	int x = 10;
	func(x); // This is ambiguity. There is no election criteria between call-by value or call-by reference
}
```
Is this is a function overloading? Yes. If we call the function with a R value expression, the second function is no viable. Because R value expression cannot bind to the L value reference. 
But if we call the function with an object, there is no criteria of how the compiler chose between these functions. So this would be a ambiguity situation.

But following syntax is even an ambiguity, if we call the function with R value expression. Keep caution on const.
```cpp
void func(int);
void func(const int &);

int main()
{
	func(12); // Ambiguity
}
```

3. Default arguments can make confuse
Following is a function overloading, because the signatures of the functions are different. But if we make a call like in the example, it would be ambiguity.
```cpp
void func(int, int = 10);
void func(int);

int main()
{
	func(12); // Ambiguity
}
```

4. What if overloaded functions have many arguments. This situation is looks complex at first glance. But there is a simple rule for that.
```cpp
void f(int, double, long);
void f(float, int, double); 
void f(float, char, bool);
```
<mark style="background: #FFF3A3A6;">The rule is : The function that is chosen must gain the upper hand over others at least one parameter. But it must not be worse at other parameters (can be equal). That means if a function has advantage in one argument and an another function has advantage on different argument, that means ambiguity</mark>
<mark style="background: #FFF3A3A6;">There is a ambiguity if no one ensure the rule. But if one of the function ensure the rule, it is chosen.</mark>

For example: 
First function call: Second function has advantage on 2nd argument and not worst at others. Means the second function is chosen.
Second Function call: Ambiguity. First function has advantage on 3rd argument and second function has advantage on 2nd argument.
```cpp
void f(int, double, long); 
void f(bool, int, double);

int main()
{
	f(7u, 12, 'A'); // Second function has advantage on 2nd argument.
	f(7u, 12, 6L); // Ambiguity, second function has advantage in 2nd argument and first function has advantage on 3rd argument
}
```

5. Another special case:
There was no election criteria between two standard conversion. But there is an exception.
There is implicit type conversion from "int*" to "bool" and "void*". 
The void* is chosen.
```cpp
void foo(bool);
void foo(void *);

int x{};

int main)  
{  
	foo(&x); // Second one (void*) is choosen. This is a special rule
}
```

**Type Casting with Overload**
In below example:
First call is ambiguity. So, do we make call to one of the overloaded function with help of type casting? Yes, we do. We can use type cast operator on the parameter of function call in order to obstruct the ambiguity. 
Second call is exact match to first overload.
Is this technique is bad? No, if we need to call a overloaded function in cases like that, we don't have another option other that using type casting. For example, that overloaded function can be a library function.
```cpp
void func(long);
void func(double);
void func(char);

int main()
{
	int ival{};

	func(ival); // This is ambiguity
	func((long)ival); // Fist one is exact match
}
```
But don't use type-cast operator that comes from C except some rare situations. C++ has own type cast operators. These are:
- static_cast
- const_cast
- reinterpret_cast
- dynamic_cast
## R value references and function overloading 
#Cpp_RvalueReference
R value references added to the language in order to use move semantics and perfect forwarding in transition to Modern C++. 

in order to add support of move semantics to the function overload resolution, Some additions are made to the language's function overload rules with modern C++.

The content related to the function overload that mentioned until here is before modern C++ features (except nullptr difference). But, after this point the content that will mentioned is added with modern C++.

Below three functions are overloads.
```cpp
void func(int&);
void func(const int&);
void func(int&&);
```

If we make a call with a L value expression, third one is not viable. The situation turns into const overloading.
If we call the function with R value expression, the first one is not viable, because we cannot bind an R value expression to the L value reference. But last two are viable. A special rule that added with C++11 is comes into the place. The rule is that the function that takes R value reference is called. 
```cpp
void func(int&);
void func(const int&);
void func(int&&);

int main()
{
	int x = 10;
	func(x); // Thid is not viable, so the situation is const overloading, the first one is called
	func(10); // The third one is callsed. This is a special case
}
```

In below situation, first one is not viable so the second one is called.
```cpp
void func(const int&);
void func(int&&);

int main()
{
	func(10); // Both function is viable, but the second one is called due to special rule

	int ival = 10;
	func(ival); // First one is called, second one is not viable
}
```

---
# Terms
#Cpp_Terms 
- constexpr
- literal type
- function overloading
- function overload resolution
- name hiding/masking/shadowing
- const overloading
- ambiguity
- function overload set
- Candidate functions
- viable functions
- no match
- electability categories
- user defined conversion
- conversion constructor
- standard conversions
- exact match
- integral promotion
- float to double promotion
- null pointer conversion
- const overloading
---
Return: [[00_Course_Files]]