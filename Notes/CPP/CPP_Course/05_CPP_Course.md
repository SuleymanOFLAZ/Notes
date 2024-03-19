[[00_Course_Files]]
#Cpp_Course 
# Function Binding Methods
#Cpp_FunctionBinding
**Static Binding**
If which function is called is determined on compile-time, If there is no program run-time cost, for example, function overloading, this is called **early binding** or **static binding**. 

**Dynamic Binding**
If which function is called is determined on the run-time, this is called **late binding** or **dynamic binding**. In this case, another code determines which function is called on run-time. This method is frequently used in C++, Java, and C#. Even it is the only method in some languages.

C++ supports both methods. We will see in detail the late binding or dynamic binding when we look at **inheritances** in C++.
# Typecast operators
#Cpp_TypeCastOperators
There is only one type-conversion operator in C. That is the type-cast operator. The expression that generated with the type-cast operator is not an L-value expression in C.

The meaning of type cast and type conversion is not the same. Type conversion can be implicit or explicit. Implicit type conversion means the compiler determines the type depending on the code. Explicit type conversion means the type conversion is done with a directive. Typecast is a type conversion with the usage of an operator.

Why do we have new type-cast operators in C++? Type conversion is done with different purposes and there are different risk levels. This situation is valid in both C and C++ and C has only one operator for this purpose.

These purposes are:
1. The compiler would do the same implicit conversion if we didn't use the typecast. But we use the typecast operator to show our intent. Think of a case where there is a data loss with implicit type conversion but that is okay for us. How would the reader know that? For that reason, we use type conversion. The second benefit of this is the compiler doesn't warn us. 
2. Sometimes we do type conversion between address types. **Strict aliasing rule**. #Cpp_StrictAliasingRule 
3. The type conversion from a const object's address to a non-const object's address.

> [!note] Note: With brace (uniform) initialization the compiler throws an error, if a data loss occurs.

>We have four new type-cast operators in C++. These are keywords also. #Cpp_keyword_typecast
> - **static_cast**
> - **const_cast**
> - **reinterpret_cast**
> - **dynamic_cast**

> [!note] Note: These are standard type-cast operators. Libraries can have more type-cast operators.

These type-cast operators are from before modern C++. #Cpp98-03 
## Static Cast
The static cast is used in integer-integer, integer-real, and real-real conversions. The compiler can do these types of conversions as implicitly. An example of the static cast is an integer divided by an integer situation. Typecast to double is used to prevent data loss in this example.

```cpp
int sum = 354;
int cnt = 17;
double d = (double)sum/cnt;
```

## Const Cast
The const cast is used in the type conversion from a const object's address to a non-const object's address. Keep caution on this, most of the time const conversion leads to undefined behavior. For example:

```cpp
const int x = 10;

int main()
{
	int* p = &x; // Syntax error in C++, but warning in C (undefined behavior).
}
```

Upper code is a syntax error in C++. There is no type conversion from "const int*" to "int*". But the compiler warns us in C, not a syntax error. 
We can prevent the error with typecast. But that doesn't mean this is true. This is very dangerous because it can lead to undefined behavior in case we try to change the object.

```cpp
const int x = 10;

int main()
{
	int* p = (int*)&x; // Not syntax error because of the type cast
	*p = 55; // Undefined behavior
}
```

We need const cast rarely, but when we use it, we need to be careful about it. 

Why do we use const cast if it is dangerous? Let's look at the "strchr()" in C. The "strchr()"'s return type is "char\*", but the function can return the address of a member of a "const char\*" array (string literal). This is completely legal in terms of the syntax in C. But if we try to deference the returned address to change it, it leads to undefined behavior. C++ overloaded that function to prevent the undefined behavior.

```c
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

This is the overload in C++. In this way, if we give a const array to the function the const function is called. The const function returns "const char*". Therefore if we try to change the address that the const overloaded function returned, it will be a syntax error.

```cpp
const char* strchr(const char* p, int c):
char* strchr(char* p, int c):
```

The const cast can be used with reference semantics too. This means we can use the const cast to make a type conversion form "const T&" to "T&"
## Reinterpret Cast
Reinterpret is the type conversion between different address types. This is the most dangerous type conversion.
The most dangerous type cast is using one object's address as another object's address. For example a type conversion from a struct's address to char* for byte search. This type of operation can be an obligation in some cases, so we must be careful when we use it.

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

The upper code is legal in C (compiler warning). Illegal in C++, but we can make it legal with type cast operator.

There are three kinds of type conversion but there is only one interface to do that, in C. So what is the problem here?
- It doesn't show the intent of the type conversion.
- We can have difficulty finding the type casts when we search for them in the code later on.
- It is open to making mistakes to make an out-of-intent type conversion. For example, a reinterpret cast instead of a static cast.

With the new type cast operators, these kinds of situations are prevented. If we use the "reinterpret_cast" instead of "static_cast" in case a static cast is needed the compiler gives a syntax error.
## Dynamic Cast
#Cpp_DynamicCast
This conversion cast doesn't exist in C. It is related to inheritance. This actualizes **downcast**. The conversion from parent class to child class in inheritance. This is actualized with a code that runs on run-time (The other type casts are done in compile-time). We will look at that later.

## Syntax of the type-cast operators
The syntax of these cast operators is the following.  Actually syntax of it is related to templates, but we will mention it later. The parentheses are not related to priority, it is related to the syntax. So, It would be a syntax error, if we write it without parentheses.

```cpp
static_cast<target type>(expression)
```

```cpp valid:9,15,19 error:10
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
> We cannot use the typecast operators instead of each other. But there is an exception case about it. We can use both static cast and reinterpret cast in conversion "void\*" to "T\*"

```cpp valid:cast
int main()
{
	std::size_t n = 100;

	int* p = static_cast<int*>(std::malloc(n*sizeof(int))); // OK.
	int* p = reinterpret_cast<int*>(std::malloc(n*sizeof(int))); // OK. The exception case 

}
```

We use static_cast between type conversion between enum types and integer types because enum types are integer types.

```cpp valid:8
enum Color {blue, red, magenta}

int main()
{
	Color mycolor{red};
	int ival = 1;

	mycolor = static_cast<Color>(ival); // OK.
}
```

We cannot do the conversion with one operator if two conversions are needed in a situation.
There are no two conversions at the same time. The following code is a syntax error. There are two conversions here, "x" is a const object. For example:

```cpp error:4
int main()
{
	const int x = 10;
	char* p = reinterpret cast<char*> (&x); // Syntax error, there is two type conversion here
	const_cast<char *>(reinterpret_cast<const char*>(&×))); // OK
}
```
# Implicit type conversion in C++
#Cpp_ImplicitTypeConversions

1. There is bidirectional implicit type conversion between integer number types and real number types. And also there is bidirectional implicit type conversion between integer types. And also there is bidirectional implicit type conversion between real number types. That means there is bidirectional conversion between int, long int, long long int, short, char, float, double, and long double.
4. There is NO implicit type conversion between different address types.
5. There is NO implicit type conversion from the const object address to the non-const object address. (const T\* -> T\*).
6. There is implicit type conversion from a const object address to a non-const object address. (T\* -> const T\*).
7. There is implicit type conversion from "T*" -> "void*"
8. There is NO implicit type conversion from "void*" to "T*"
9. There is implicit type conversion from enum types to arithmetic types.
10. There is NO implicit type conversion from arithmetic types to enum types.
11. There is NO implicit type conversion between enum types.
12. There is NO implicit type conversion from enum class types to arithmetic types.
# Scoped Enums
#Cpp_enumClass
Scoped enum (enum class) added to the language with modern C++. The "class" keyword can cause some confusion but, "enum class" is not related to classes. #Cpp11  

Why enum class exist while the classical enums exist?
Let's look at problems with enum:
1. The good thing with enums, there is no implicit type conversion from arithmetic types to enum types and there is no implicit type conversion between enums. But the bad thing is there is implicit type conversion from enum types to arithmetic types.
2. Each enum type has an **underlying type**. Enum types are integer types. The underlying type of enum is "int" in C and, can't be another type. But in C++, the compiler determines the enum's underlying type depending on the values of the enumerators. This comes from before modern C++, so the underlying type doesn't have to be "int" in C++. The underlying type can be an integer and cannot be a smaller integer type (For example, the underlying type cannot be short or char). But, it can be int, unsigned int, long, long long, etc. The bad thing is we must use 4 bytes(in the assumption that "int" is 4 bytes) at least for enum and this is a memory problem. The second problem is that it brings portability issues too. The portability of the program is affected by this design because the underlying type can differ between compilers. #Cpp11 
3. Enum is an **incomplete type** in C++. This is not an issue in C, because the underlying type of the enum is "int", which means the enum is a complete type in C. #Cpp_C_difference 
4. The enumerators of all enums that are declared in the same scope are considered in the same scope. So we cannot define the same enumerator name in different enums. Think about it, the same enumerators are included with different headers, this will be a big problem.

> [!note] Note: Complete Type and Incomplete Type
> A C topic. If the size of a type is not known the type is an incomplete type.

```cpp
enum Color; // Just declaration, not definition

int main()
{
	Color mycolor; // Syntax error in C++, but OK in C.
}
```

The upper code is a syntax error in C++ because the compiler doesn't know the size of the enum. This is "**incomplete type**" in C++. But, the syntax is not an error in C because, the compiler knows the size, it must be int. So, this is a "**complete type**" in C. #Cpp_C_difference 

Let's think about a situation. Our enum definition is inside a header file. We don't want to include that header instead, we want to make a forward declaration of the enum.

```cpp
// file.h // We don't include the header
enum Color; // Forward declaration

struct Data {
	Color ncolor;
};
```

But this causes a syntax error because elements of the "struct Data" must be complete type (the size of them must be known) to calculate the struct's size. 
This is a very bad situation. Because we must include the header file that contains the definition of "enum Color" to know its size. Dependency decreases as much as fewer header files are included.
If the underlying type of the enum is known, the need to add the header file would be eliminated.

So the "enum class" was added to the language with modern C++ to cover these problems. And also added some syntax features to enums, too. #Cpp11 
## enum class
Differences of enum class:
1. Underlying type can be stated directly with syntax. If we didn't state the underlying type, it would be "int" by default. That means underlying type of the enum classes is not determined by the compiler anymore. That solves the problems that we mentioned about forward declaration. And we can determine underlying type of the enum class types as smaller integer types such as char, short, unsigned short etc. Underlying determine feature is also added to the classical enums, not only to enum classes. This solves forward declaration for classical enums, too. This also solves the portability issues. #Cpp11 

```cpp
enum class Color {red, blue, green}; // underlying type: default int
----
enum class Color : char{red, blue, green}; // We can state the underlying type
----
enum class Color : unsigned{red, blue, green}; // We can state the underlying type
----
enum class Color: int; // Forward decleration example. The rules existed for classical enums too
----
enum Color : unsigned{red, blue, green}; // We can state the underlying type
```

2. enum classes have their own scopes. This solves the scope problem of classical enums.

```cpp error:3
enum class Color {red, blue, green};

Color mycolor = red; // Syntax error.
Color mycolor = Color::red; // We used binary resolution operator
```

Writing that resolution operator makes the code hard to read. In C++20, a new feature is added to the language. We can use the enum class without the resolution operator if we declare a special using declaration. The enum can be used without the resolution operator in the scope of the using-declaration: "using enum Enum_Name;". Or we can use only one enumerator without specifying the resolution operator in the scope of the using-declaration: "using Color::blue;". #Cpp20 #Cpp_keyword_using 

```cpp hl:5,9
enum class Color {red, blue, green};

int main()
{
	using enum Color;
	Color c = red; // this is valid in the scope of the upper declaration
---
	// Or we can write like the following to use blue without resolution operator
	using Color::blue; // the using-declaration for only one enumeration. Note that there is no enum keyword in the declaration
	Color x = bleu;
}
```

> [!note] Note: About "using" keyword
Some using-keywords correspond to different meanings. The keyword is one of the most overloaded keywords. We look through them later. #Cpp_keyword_using 

3. There is NO implicit type conversion from enum classes to arithmetic types.

**The added features to the classical enums with modern C++**: #Cpp11 
1. Specifying the underlying type of the classical enums. (The default underlying type has become "int" for classical enums too with the modern C++, which solves the forward declaration problem)
2. We can use classical enums with the resolution operator just like enum classes. This does not have an effect, it is added just to make the syntax the same. (For example: "auto x = Color::red", Color is a classical enum)

Use the enum classes if there is no special situation to use the classical enums.

Enum classes are not a class type. We can test that with the following syntax, and note that "is_class_v<>" is a **compile-time library**, included with **type_traits**, and we will look at them in detail later:

```cpp
#include <type_traits>
#include <stdexcept>

using namespace std;

enum class Color {Blue};

int main()
{
	constexpr boo1 b = is_class_v<Color>; // This is false
}
```
# decltype Specifier
#Cpp_decltype
Type deduction existed before modern C++. However, type deduction only exists on usage of templates. Modern C++ spreads the type deduction on most of the language and adds new type deduction tools. One of them is the auto keyword, and we look at it a little bit. The other one is the **decltype specifier**. Auto and decltype are completely different tools. #Cpp11 

**A quick reminder about "Auto" type deduction**: There are a variety of "auto" keyword usages to deduct the type. For example auto return type, auto usage on lambda expressions (for example generalized lambda expressions),  auto usage as a template argument, and many more. We have looked at the one kind of usage of the auto keyword which is to deduct a variable type depending on the type of the object that we used the initialize the deducted variable. We mention others later. 

Another tool to make the type deduction is the **decltype specifier**. But usage of decltype is different and not related to initialization like as "auto". "decltype" is a keyword and we write an expression between parentheses after the keyword and that syntax corresponds to a type and we can use that syntax everywhere that we use a type. The type deduction is done at compile time. There is no obligation to initialize or declare a variable with decltype. #Cpp_keyword_decltype

We can use the "decltype" everywhere where we use a type. For example, we can use it as function parameter types, to declare local or global variables, as a function return type, as a type of the member of a class or struct. Actually, "decltype" is converted into a type by the compiler.

Some usage examples of "decltype":

```cpp
decltype(expr) x, y; // Compiler deducts the type depending on given expression.

decltype(expr) foo(); // decltype(expr) corresponds to the function return type
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

decltype(x) foo(decltype(x)); // A function declaration that return type is type of decltype(x), "int" in this case. Also parameter of the function is "int", too.

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
Let's look at these forms.

## The first form of "decltype" type deduction - The operand of the decltype is a name
The operand of the decltype specifier is in a name form. The compiler looks to the type of the name (which type the name declared with) to deduct the type in this situation.

The key point is here to understand, the type of declaration to be taken into consideration. That means constness and references are considered too.

> [!note] Note: The dot operand of a struct or class (For example, "decltype(a.b)") and arrow operand of a pointer (For example, "decltype(ptr->y)") are included in the name form.

```cpp
int x = 10;
const int cx = 20;
int* ptr = &x;
int& r = x;

int main()
{
	decltype(x) a = 5; // decltype is "int"
	decltype(cx) a = 5; // decltype is "const int"
	decltype(ptr) a = &x; // decltype is "int*"
	decltype(r) a = x; // decltype is "int&"

	int a[] = {1, 2, 3};
	decltype(a) b; // decltype is "int[3]". There is no array decay with decltype
	
}
```

> [!question] Question: Interview questions about decltype usage
> Q: Is there a syntax error? #Cpp_Interview_Question 
> A: First decltype usage is a syntax error because, the deducted type with decltype is "const int" and, a const object must be initialized. The second decltype usage is a syntax error because the deducted type with decltype is "int&" and a reference must be initialized.

```cpp error:decltype
const int cx = 20;
int& r = x;

int main()
{
	decltype(cx) a; // Syntax error. const int must be initialized
	
	decltype(r) a; // syntax error. int& must be initialized
}
```

> [!warning]  Warning: There is no array decay with decltype in the name usage. Because the declared type is an array. Don't forget type deduction is done with declared type with decltype (In the case of the operand of the decltype is a name).

```cpp hl:decltype
int main()
{
	int a[] = {1, 2, 3};
	decltype(a) b; // decltype is "int[3]". There is no array decay with decltype
	
}
```

## The second form of "decltype" type deduction - The operand of the decltype is an expression

The operand of the decltype is an expression, but not a name expression. The set of rules for this notation is different from the previous one (The operand was a name expression in the previous form). 

> [!warning]  Warning: We must know which expressions are considered as names, and which are not. For example, there are expression example that looks like name expressions but not in below.

```cpp
int x;
int* ptr;

decltype(*ptr); // "*ptr" is NOT a name expression.
decltype((x)); // "(x)" is NOT a name expression.
```

Now, let's look at the set of rules:
The type that deducted with decltype is depends on the decltype's operand's value category. Remember, an expression's value category can be in the PR value category, the L value category, or the X value category.

The deduced type can be like the following depending on the value category of the expression of the operand of the decltype, in assumption the type of the expression is T type. #Cpp_ValueCategory 
1. The type is deducted as "T" in the situation where the expression value category is a PR value.
2. The type is deducted as "T&" (L value reference) in the situation where the expression value category is an L value.
3. The type is deducted as "T&&" in the situation where the expression value category is an X value.

> [!note] Note: An expression has a type and that type can't be a reference type but can be an int, double, or int*, etc

**Case the expression is PR value:**
```cpp
int main()
{
	int x = 10;
	double dval = .5;
	decltype(× + 5) ival; // decltype is "int". Because the "x+5" is PR value and the type of it is int.
	decltype(× + dval) ival; // decltype is "double". Because the "x+dval" is PR value and the type of it is double.
}
```

**Case the expression is L value:**
```cpp error:5
int main()
{
	int* ptr = &x;
	decltype(*ptr) y = x; // decltype is "int&". Because the type of expression "*ptr" is "int" and the value category of it is "L value". 
	decltype(*ptr) y; // Syntax ERROR. Because decltype is "int&" and not initialized.

	int a[5]{};
	int y{};
	decltype(a[2]) x = y; // decltype is "int&". Because "a[2]" is an L value expression and its type of it is "int".
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
	decltype((a)) y = b: // decltype is an "int&" because "(x)" is an L value expression (Not a name because of the parentheses). Parentheses are the priority operator here.

	decltype(foo()) x = 10; // decltype is a "int&&". Because the "foo()" is an X value expression (returns an R value reference). This is the same as writing "int&& x = 10;"
}
```
#Cpp_Interview_Question

> [!question] Question: 
> Q: How would the following program print? #Cpp_Interview_Question 
> A: The "y" prints as 450 because "++a" is an L value expression and deducted is "int&". The "a" prints "10" because the expression inside decltype specifier parentheses is considered as "unevaluated context"  just like "sizeof" operator.

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

> [!warning]  Warning: Expression that is the operand of the decltype is considered as "unevaluated context".

> [!note] Note: Unevaluated Context
> There are certain places where the compiler doesn't evaluate the expression inside them. We call that situation "Unevaluated Context". The not execution of the expression that is considered an unevaluated context is guaranteed by the language rules. There is one example of it in C, which is an expression of "sizeof()"operator. But, there are several examples of it in C++, and one of them is the expression of the decltype specifier.

> [!example] Example: Unevaluated context example

```cpp
// Unevaluated Context example.
using namespace std;

int main()
{
	int* p = nullptr;
	int a[3]{};
	auto n1 = sizeof(*p); // OK, there isn't undefined behavior. The compiler doesn't dereference the null pointer because it is an unevaluated context
	auto n2 = sizeof(a[5]); // OK, there isn't undefined behavior. The compiler doesn't use the a[5] because it is an unevaluated context
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
> Q: Which function is called? #Cpp_Interview_Question 
> A: The function that takes "int&" is called because the value category of "r" is L value even though the type of r is "int&&".

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