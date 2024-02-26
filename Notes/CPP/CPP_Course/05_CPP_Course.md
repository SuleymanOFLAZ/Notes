[[00_Course_Files]]
#Cpp_Course 
# Function Binding Methods
#Cpp_FunctionBinding
**Static Binding**
If which function called is determined on compile-time, If there is no program run-time cost, for example function overloading, this is called <mark style="background: #BBFABBA6;">early binding</mark> or <mark style="background: #BBFABBA6;">static binding</mark>. 

**Dynamic Binding**
If which function is called is determined on run-time, this called <mark style="background: #BBFABBA6;">late binding</mark> or <mark style="background: #BBFABBA6;">dynamic binding</mark>. In this case another code determines which function is called on run-time. This method is frequently used in C++, Java and C#. Even it is the only method in some languages.

we will see in detail the late binding or dynamic binding, when we look at <mark style="background: #BBFABBA6;">inheritances</mark> in C++.

C++ supports both methods.

# Type cast operators
#Cpp_TypeCastOperators
There is only one type conversion operator in C. That is type-cast operator. The expression that generated with type-cast operator is not a L value expression in C.

The meaning of type cast and type conversion is not same.

Type conversion can be implicit or explicit. Implicit type conversion means the compiler determines the type depending on code. Explicit type conversion means the type conversion is done with an directive.

Type cast is a type conversion with usage of an operator.

Why we have new type-cast operators in C++? Type conversion is done with different purpose and there is different risk levels. This situation is valid both C and C++ and C has only one operator for this purpose.

This purposes are:
1. The compiler would do the same implicit conversion, if we didn't use the type cast. But we use the type cast operator in order to show out intend. Think a case that, there is a data loss with implicit type conversion but that is okay for us. How would he reader know that? For that reason we use type conversion. The second benefit of this is the compiler doesn't warn us. 
2. Sometimes we do type conversion between address types. <mark style="background: #BBFABBA6;">Strict aliasing rule</mark>.
3. The type conversion from a const object's address to non-const object's address.

> [!note] Note: With brace (uniform) initialization the compiler throw an error, if a data loss occurs.

>We have four new type-cast operators in C++. These are keywords also.
> - <mark style="background: #BBFABBA6;">static_cast</mark>
> - <mark style="background: #BBFABBA6;">const_cast</mark>
> - <mark style="background: #BBFABBA6;">reinterpret_cast</mark>
> - <mark style="background: #BBFABBA6;">dynamic_cast</mark>

> [!note] Note: These are standard type-cast operators. Libraries can have more type-cast operators.

This type-cast operators are from before modern C++.

## Static Cast
Static cast is used to type conversations between integer and real numbers. Actually this kind of conversations are conversation that the compiler can. For example, It is used in integer divide by integer situation. Type cast to double is used to prevent data loss in this example.
```cpp
int sum = 354;
int cnt = 17;
double d = (double)sum/cnt;
```

## Const Cast
The type conversion from a const object's address to non-const object's address. Keep caution on this, most of the time const conversion lead undefined behavior. For example:
```cpp
const int x = 10;

int main()
{
	int* p = &x; // Syntax error in C++, but warning in C (undefined behavior).
}
```
Upper code is syntax error in C++. There is no type conversion from const int* to int*. But the compiler warn us in C, not a syntax error. 
We can prevent the error with type cast. But that doesn't mean this is true. Actually this is very dangerous, because it can lead to undefined behavior in case of we try to change the object.
```cpp
const int x = 10;

int main()
{
	int* p = (int*)&x; // Not syntax error because of the type cast
	*p = 55; // Undefined behavior
}
```

We need const cast rarely, but when we use it, we need to be careful about it. 

But why we use const cast, it is dangerous? Let's look to the "strchr()" in C. Function return type is "char\*", but the function can return the address of a member of a "const char\*" array (string literal). This is completely legal in syntax of C. But if we try to deference the returned address to change it, it leads to undefined behavior. C++ overloaded that function to prevent the undefined behavior.
```cpp
// Related part of the strchr() in C
char* Strchr(const char* p, int c)
{
	while(*p) {
		if(*p == c)
			return (char*)p;
		++p;
	}
	// ...
}
```
This is overload in C++. In this way, if we want to search in a const array the const function is called.
```cpp
const char* strchr(const char* p, int c):
char* strchr(char* p, int c):
```

It can be used with reference samantic too. This means we can use it to make type conversion form "const T&" to "T&"

## Reinterpret Cast
Reinterpret is the type conversion between different address types. This is the most dangerous type conversion.
The most dangerous type cast is using one object's address as another object's address. For example a type conversion from a struct's address to char* for byte search. This type of operation can be an obligation is some cases, so we must be careful when we use it.
```cpp
struct Data {
	int a, b, c;
};

int main()
{
	struct Data mydata = {1, 54, 6};

	char* p = &mydata; // Legal in C (compiler warning). Illegal in C++, but we can make it legal with type cast operator.
}
```
Upper code is legal in C (compiler warning). Illegal in C++, but we can make it legal with type cast operator.

There are three kind of type conversion but there is only one interface to do that, in C. So what is the problem here?
- It does't show the intend of the type conversion.
- We can have difficulty find the type casts when we search for it in the code later on.
- It is open to make mistakes to make a out of intent type conversion. For example a reinterpret cast instead of static cast.

With the new type cast operators, these kind of situations are prevented. If we use the "reinterpret_cast" instead of "static_cast" in case static cast needed the compiler gives syntax error.

## Dynamic Cast
This conversion cast don't exist in C. It is related to inheritance. This actualizes <mark style="background: #BBFABBA6;">down cast</mark>. The conversion from parent class to child class in inheritance. This is actualized with a code that runs on run-time (The other type casts are done in compile-time). We will look that later.

## Syntax of the type-cast operators
The syntax of these cast operators is following.  Actually syntax of it is related to templates, but we will mention it later. The parentheses is not related to priority, it is related to the syntax. So, It would be the syntax error, if we write it without parentheses.
```cpp
static_cast<target type>(expression)
```

```cpp
struct Data {
	int a, b, c;
};

int main()
{
	struct Data mydata = {1, 54, 6};

	char* p = reinterpret_cast<char *>(&mydata); // OK.
	char* p = static_cast<char *>(&mydata); // Syntax ERROR. "reinterpret_cast" is needed in this situation

	int x = 10;
	int y = 56;
	if(y) {
		double dval = static_cast<double>(x)/y; // OK.
	}

	const int* p = &x;
	int* ptr = const_cast<int*>(p); // OK.
}
```

> [!note]  Note: An exception
> We cannot use the type cast operators instead of each other. But there is an exception case about it. We can use both static cast and reinterpret cast in conversion "void\*" to "T\*"

```cpp
int main()
{
	std::size_t n = 100;

	int* p = static_cast<int*>(std::malloc(n*sizeof(int))); // OK.
	int* p = reinterpret_cast<int*>(std::malloc(n*sizeof(int))); // OK. The exception case 

}
```

We use static_cast between type conversion between enum types and integer types, because enum types are integer types.
```cpp
enum Color {blue, red, magenta}

int main()
{
	Color mycolor{red};
	int ival = 1;

	mycolor = static_cast<Color>(ival); // OK.
}
```

We cannot do the conversion with one operator, if two conversion is needed in a situation.
There is no two conversion at same time. The following code is syntax error. There are two conversion here, int is a const object. For example:
```cpp
int main()
{
	const int x = 10;
	char* p = reinterpret cast<char*> (&x); // Syntax error, there is two type converion here
	const_cast<char *>(reinterpret_cast<const char*>(&×))); // OK
}
```

# Implicit type conversion in C++
#Cpp_ImplicitTypeConversions

1. There is bidirectional implicit type conversion between integer number types and real number types. And also there is bidirectional implicit type conversion between integer types. And also there is bidirectional implicit type conversion between real types. That means there is bidirectional conversion between int, long int, long long int, short, char, float, double, long double 
4. There is NOT implicit type conversion between different address types.
5. There is NOT implicit type conversion between const object address to non-const object address. (const T\* -> T\*).
6. There is implicit type conversion between const object address to non-const object address. (T\* -> const T\*).
7. There is implicit type conversion from "T*" -> "void*"
8. There is NOT implicit type conversion form "void*" to "T*"
9. There is implicit type conversion from enum types to arithmetic types.
10. There is NOT implicit type conversion from arithmetic types to enum types.
11. There is NOT implicit type conversion between enum types.
12. There is NOT implicit type conversion from enum class types to arithmetic types.

# Scoped Enums
#Cpp_enumClass
Scoped enum (enum class) added to the language with modern C++. The "class" keyword can make some confusion but, "enum class" is not related to classes. 

Why enum class exist while enum exist?
Let's look at problems with enum:
1. The good thing with enum, there is not implicit type conversion from arithmetic types to enum and there is not implicit type conversion between enums. But the bad things is there is implicit type conversion from enum types to arithmetic types.
2. Each enum type has a <mark style="background: #BBFABBA6;">underlying type</mark>. Enum types are actually integer types. The underlying type of enum is int in C and, can't be another type. But in C++, the compiler determines enum's underlying type depending on values of the enumerators. This comes from before modern C++, so the underlying type doesn't have to be int in C++. Underlying type can be an integer and cannot be a smaller integer type (For example, the underlying type cannot be short or char). But, it can be int, unsigned int, long int, long long int etc. The bad thing is we must use 4 bytes(in assumption that int is 4 byte) at least for an enum and this is a memory problem. And the second problem is that brings portability issues too. The portability of program is effected by this design because the underlying type can be differ between compilers.
3. Enum is a incomplete type in C++. This is not an issue in C, because the underlying type of enum is int, that means enum is a complete type in C.
4. The enumerators of all enums that declared in same scope considered in same scope. So we cannot define same enumerator name in different enums. Think about, same enumerators are included with different headers, this would be a big problem.

> [!note] Note: Complete Type and Incomplete Type
> A C topic. If size of a type is not known the type is a incomplete type.

```cpp
enum Color; // Just decleration, not definition

int main()
{
	Color mycolor; // Syntax error in C++, but OK in C.
}
```
Upper code is a syntax error in C++ because, the compiler doesn't know the size of the enum. This is "<mark style="background: #BBFABBA6;">incomplete type</mark>" in C++. But, the syntax is not  error in C because, the compiler knows the size, it must be int. So, this is a "<mark style="background: #BBFABBA6;">complete type</mark>" in C.

Let's think about a situation. Our enum definition is inside a header file. We don't want to include that header instead, we want to make forward declaration of the enum
```cpp
// file.h // We don't include the header
enum Color; // Forward declaration

struct Data {
	Color ncolor;
};

```
But this causes syntax error because, because elements of the "struct Data" must be complete type (size of them must be known) to calculate the struct's size. 
This is a very bad situation. Because we must include the header file that contains the definition of "enum Color" to know it's size. Dependency decreases that much how much less header file included.
If the underlying type of the enum is known, the need to add the header file would be eliminated.

So the "enum class" added to the language with modern C++ to cover these problems. And also added some syntax features to enums, too.
## enum class
Differences of enum class:
1. Underlying type can be stated directly with syntax. If we didn't state the underlying type, it is be int by default. That means underlying type of the enum classes is not determined by the compiler anymore. That solves the problems that we mentioned about forward declaration. And we can determine underlying type of the enum class types as smaller integer types such as char, short, unsigned short etc. Underlying determine feature is also added to the classical enums too, not only to enum classes. This solves forward declaration for classical enums, too. This also solves the portability issues.
```cpp
enum class Color {red, blue, green}; // underlying type: default int
----
enum class Color : char{red, blue, green}; // We can state the underlying type
----
enum class Color : unsigned{red, blue, green}; // We can state the underlying type
----
enum class Color: int; // Forward decleration example. The rules is existed on classical enums too
----
enum Color : unsigned{red, blue, green}; // We can state the underlying type
```
2. enum classes has their own scopes. This solves the scope problem of classical enums.
```cpp
enum class Color {red, blue, green};

Color mycolor = red; // Syntax error.
Color mycolor = Color::red; // We used binary resolution operator
```
Writing that resolution operator makes the code hard to read. In C++20, a new feature is added to the language. We can use the enum class without resolution operator, if we declare a special using declaration. The enum can be used without resolution operator in the scope of the using declaration: <mark style="background: #D2B3FFA6;">using enum Enum_Name;</mark>. Or we can use only one enumerator without specifying the resolution operator in the scope of the using declaration: <mark style="background: #D2B3FFA6;">using Color::blue;</mark>.
```cpp
enum class Color {red, blue, green};

int main()
{
	using enum Color;
	Color c = red; // this is valid in the scope of upper decleration
---
	// Or we can write like following to use blue without resolution operator
	using Color::blue; // the using decleration for only one u enumeration. Note that there is no enum keyword in decleration
	Color x = bleu;
}
```

> [!note] Note: About "using" keyword
There are using keywords that corresponds to different meanings. The keyword is one of the most overloaded keyword. We look through them later. 

3. There is NOT implicit type conversion from enum classes to arithmetic types.

**The added features to the classical enums with modern C++**:
1. Specifying the underlying type of the classical enums, too.
2. We can use classical enums with the resolution operator just like enum classes. This is not has a effect, it is added for just to make the syntax same. (For example: "auto x = Color::red", Color is a classical enum)

<mark style="background: #FFF3A3A6;">Use the enum classes if there is no special situation to use the classical enums.</mark>

<mark style="background: #FFF3A3A6;">Enum classes are not a class type.</mark> The can test that with following syntax, and note that "is_class_v<>" is a <mark style="background: #BBFABBA6;">compile-time library</mark>, included with <mark style="background: #BBFABBA6;">type_traits</mark>, and we will look them in detail later:

```cpp
#include < type_traits>
#include ‹stdexcept>

using namespace std;

enum class Color {Blue};

int main()
{
	constexpr boo1 b = is_class_v<Color>; // This is false
}
```

# decltype Specifier
#Cpp_decltype
Type deduction was exist before modern C++. But, type deduction is only exist on usage of templates. Modern C++ spread the type deduction on most of the language and add new type deduction tools. One of them is auto keyword, and we look it a little bit. The another one is the <mark style="background: #BBFABBA6;">decltype specifier</mark>. Auto and decltype are completely different tools.

**A quick reminder about "Auto" type deduction**: There are variety of "auto" keyword usages to deduct the type. For example auto return type, auto usage on lambda expressions (for example generalized lambda expressions),  auto usage as template argument and many more. We have been looked the one kind of usage of the auto keyword that is to deduct a variable type depending on the type of the object that we used the initialize the deducted variable. We mention others later. 

Another tool to make the type deduction is decltype specifier. But usage of decltype is different and not related to initialization like as "auto". "decltype" is a keyword and we write a expression between parentheses after the keyword and that syntax corresponds to a type and we can use that syntax everywhere that we use a type. The type deduction is done at compile time. There is no obligation to initialize or declare a variable with decltype.

We can use the "decltype" everywhere where we use a type. For example we can use it as function parameter types, to declare local or global variable, as function return type, as a type of the member of a class or struct. Actually, "decltype" is converted into a type by compiler.

Soma usage examples of "decltype":

```cpp
decltype(expr) x, y; // Compiler deducts the type depending on given expression.

decltype(expr) foo(); // decltype(expr) corrensponds to the function return type
```

```cpp
int main()
{
	int x = 5;
	decltype(x) y; // decltype(x) means int in this situation
}
```

```cpp
int x = 5;

decltype(x) foo(decltype(x)); // A function declaration that return type is type of decltype(x), int in this case. Also parameter of the function is int, too.

int main()
{

}
```

```cpp
int x = 5;

int main()
{
	std::vector<decltype(x)> z; // "decltype" used as template argument
}
```

How the type deduction is done with decltype? The type deduction is done in two forms with the "decltype".  These forms are:
- The expression of the decltype is a name expression.
- The expression of the decltype is NOT a name expression.
Let's look these forms.

## First form of "decltype" type deduction - The operand of the decltype is a name
The operand of the decltype specifier is in a name form. The compiler looks to the type of the name (which type the name declared with) to deduct the type in this situation.

The key point is here to understand that, the type that in declaration to be taken into consideration. That means constness and referenceness are considered too.

> [!note] Note: The dot operand of a struct or class (For example, "decltype(a.b)") and arrow operand of a pointer (For example, "decltype(ptr->y)") is included in the name form.

```cpp
int x = 10;
const int cx = 20;
int* ptr = &x;
int& r = x;

int main()
{
	decltype(c) a = 5; // decltype is "int"
	decltype(cx) a = 5; // decltype is "const int"
	decltype(ptr) a = &x; // decltype is "int*"
	decltype(r) a = x; // decltype is "int&"

	int a[] = {1, 2, 3};
	decltype(a) b; // decltype is "int[3]". There is not array decay with decltype
	
}
```

> [!question] Question: Interview questions about decltype usage
> Q: Is there a syntax error?
> A: First decltype usage is a syntax error because, deducted type with decltype is "const int" and, a const object must be initialized. The second decltype usage is syntax error because, deducted type with decltype is "int&" and a reference must be initialized.
```cpp
const int cx = 20;
int& r = x;

int main()
{
	decltype(cx) a; // Syntax error. const int must be initialized
	
	decltype(r) a; // syntax error. int& must be initialized
}
```
#Cpp_Interview_Question 

> [!warning]  Warning: There is no array decay with decltype in the name usage. Because the declared type is an array. Don't forget type deduction is done with declared type with decltype (In case of operand of the decltype is a name).

```cpp
int main()
{
	int a[] = {1, 2, 3};
	decltype(a) b; // decltype is "int[3]". There is not array decay with decltype
	
}
```

## Second form of "decltype" type deduction - The operand of the decltype is an expression

The operand of the decltype is a expression, but not a name expression. The set of rules for this notation is different form previous (The operand was a name expression in previous form). 

> [!warning]  Warning: We must know which expressions are considered as name, and which are not. For example, there are expression example that looks like name expressions but not in below.
```cpp
int x;
int* ptr;

decltype(*ptr); // "*ptr" is NOT a name expression.
decltype((x)); // "(x)" is NOT a name expression.
```

Now, let's look the set of rules:
The type that deducted with decltype is depends on the decltype's operand's value category. Remember, an expression's value category can be in PR value category or L value category or X value category.

The deducted type can be like followings depending on the the value category of the expression of the operand of the decltype, in assumption the type of the expression is T type.
1. The type is deducted as "T" in the situation the expression value category is a PR value.
2. The type is deducted as "T&" (L value reference) in the situation the expression value category is a L value.
3. The type is deducted as "T&&" in the situation the expression value category is a X value.

> [!note] Note: An expression has a type and that type can't be a reference type, but can be a int, double or int* etc

**Case the expression is PR value:**
```cpp
int main()
{
	int x = 10;
	double dval = .5;
	decltype(× + 5) ival; // decltype is "int". Because the "x+5" is PR value and type of it is int.
	decltype(× + dval) ival; // decltype is "double". Because the "x+dval" is PR value and type of it is dobule.
}
```

**Case the expression is L value:**
```cpp
int main()
{
	int* ptr = &x;
	decltype(*ptr) y = x; // decltype is "int&". Because the type of expression "*ptr" is "int" and value category of it is "L value". 
	decltype(*ptr) y; // Syntax ERROR. Because, decltype is "int&" and not initialized.

	int a[5]{};
	int y{};
	decltype(a[2]) x = y; // decltype is "int&". Because, "a[2]" is a L value expression and type of it is "int".
}
```
#Cpp_Interview_Question 

**Case the expression is X value:**
```cpp
int&& foo();
int main()
{
	int a = 5;
	int b = 6;

	decltype(a) x = b; // decltype is "int". Because the "a" is a name
	decltype((a)) y = b: // decltype is a "int&" because, "(x)" is a L value expression (Not a name because of the parentheses). Parentheses are priority operator here.

	decltype(foo()) x = 10; // decltype is a "int&&". Because, the "foo()" is a X value expression (returns a R value reference). This is same as writing "int&& x = 10;"
}
```
#Cpp_Interview_Question

> [!question] Question: 
> Q: How would the following program print ? 
> A: The "y" prints as 450 because "++a" is a L value expression and deducted is "int&". The "a" prints "10" because the expression inside decltype specifier parentheses considered as "unevaluated context"  just like "sizeof" operator.
```cpp
 #include <iostream>

using namespace std;

int main()
{
	int a = 10;
	int y = 45;
	
	decltype(++a) x = y; // ++a is a L value expression. decltype is int&
	× *= 10;
	
	sta::cout << "у = " << y < "\n";
	std::cout << "a = " << a << "\n";
}
```
#Cpp_Interview_Question 

> [!warning]  Warning: Expression that is operand of the decltype is considered as "unevaluated context".

> [!note] Note: Unevaluated Context
> There are certain places that the compiler doesn't evaluate the expression inside them. We call that situation "Unevaluated Context". The not execution of the expression that considered as unevaluated context is guaranteed by the language rules. There is one example of it in C, that is expression of "sizeof()"operator. But, there are several examples of it in C++ and, one of them is the expression of the decltype specifier.

> [!example] Example: Unevaluated context example
```cpp
// Unevaluated Context exmaple.
using namespace std;

int main()
{
	int* p = nullptr;
	int a[3]{};
	auto n1 = sizeof(*p); // OK, there isn't undefined behavior. The compiler does't dereferance the null pointer because it is unevaluated context
	auto n2 = sizeof(a[5]); // OK, there isn't undefined behavior. The cmopiler doesn't use the a[5] because it is unevaluated context
}
```

> [!example] Example: Vale category of an expression and type of an expression is different things.
> Q1: What is the type of variable r?
> A1: The type of "r" is "int&&"
> O2: What is the value category of expression "r"?
> A2: The value category of "r" is "L value "
```cpp
int main()
{
	int x = 10;
	int&& r = 5 + x;
}
```

> [!question] Question: The upper example is asked in exams in function overload form
> Q: Which function is called?
> A: The function that takes "int&" is called because value category of "r" is L value even though type of r is "int&&".
```cpp
void func(int&&)
{
	std::cout << "1";
}

void func(int&)
{
	std::cout << "2";
}

int main()
{
	int&& r = 10;
	func(r);
}
```
#Cpp_Interview_Question 

---
# Terms
- early/static binding
- late/dynamic binding
- inheritance
- type-cast operator
- type-conversion
- static_cast
- const_cast
- reinterpret_cast
- dynamic_cast
- down-cast
- underlying type
- complete type
- incomplete type
- enum class
- forward declaration
- decltype specifier
- unevaluated context
---
Return: [[00_Course_Files]]