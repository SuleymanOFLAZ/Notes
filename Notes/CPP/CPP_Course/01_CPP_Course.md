[[00_Course_Files]]
#Cpp_Course
# About C++ and C++ Standards
C++ is a multi-paradigm programming language. The paradigms that C++ support are:
1. Procedural paradigm
2. Object oriented paradigm
3. Generic programming (Polymorphism)
4. Genre independent programming paradigm

Legacy C++ codes can be compiled with setting up compiler switches.

First C++ standards are C++98 and C++03. Actually these standards are same. A guarantee has been given on C++98 standard to not the change the language features until existing problems are discovered and reported in order to fix them.

The new rule has been established that new C++ standards are published apart over three years. And 11, 14, 17, 20 standards are published.

C++11 and later standards are mentioned Modern C++ informally.

# C++ C core and C Differences
#Cpp_C_core
C++ has core that based on C. But this C core not exactly same as C language.

Main reason to this differences is compile time checks are more restricted in C++.

In programming languages there are two basic type concepts. These are:
1. <mark style="background: #BBFABBA6;">static typing</mark>
2. <mark style="background: #BBFABBA6;">dynamic typing</mark>

Compiler detect data's type depends on source code and can make checks at compile time. For example if we try to assign a real number on a integer type, compiler can detect that and warn us. This is static typing concept. Dynamic typing concept is to detect datas type on run time. For example x = "string", than x = 12.

<mark style="background: #FFF3A3A6;">C++, C#, and Java like programming languages are actually has the static typing concept. But also they have support to dynamic typing concepts.</mark>

Though C has static typing concept. Dynamic typing concept can be ensured by writing code.

Although both C and C++ has static typing concept, this not mean their concepts are identical. C designed as a small and generic language. For this reason the type checking responsibility of C on compile time is not exact as C++. C has weak type control. C++ provides strict type controls.

<mark style="background: #FFF3A3A6;">For example type conversion form void* to any other type is allowed and okay in C. But in C++ this kind of conversion is a syntax error. In C++ the type conversion operator is a must.</mark>

```cpp
int *p = malloc(n); // malloc return type is void*
// This is a syntax error in C++ (void* - > T*)
```

Type of the string literals in C is <mark style="background: #ADCCFFA6;">char*</mark>, but in C++ it is <mark style="background: #ADCCFFA6;">const char*</mark>.
Because type of string literals of string in C is char*, following syntax is legal in C, but it is also undefined behavior in C:
```c
char *p = "Hello";
```

But in C++, this literal type is <mark style="background: #ADCCFFA6;">const char*</mark>. In the other mean, upper code piece is illegal in C++ and not allowed in ISO C++11 standard. Compiler throws warning in this situation.
<mark style="background: #FFF3A3A6;">Type conversion of an const address to non-const address is syntax error in C++.</mark> Except the string literal example, compiler throws error with this kind of conversation.

This kind of incompatible situations are increased at every new C++ standard.

For example auto keyword is in both C and C++. But meaning of it are differs between them. The register keyword is also has different meanings between C and C++. The register keyword is reserved for different meaning in C++.

## Three important term to know in both C and C++
There are three important term to know both in C and C++. These are:
1. Undefined behavior
2. Unspecified behavior
3. Implementation defined behavior

Small part of undefined behavior that in C is a syntax error in C++. And there are some situations that undefined behavior in C but defined in C++.

## Differences of functions
Function differences between C++ C core and C:
1. Implicit int rule
2. Void function paramater
3. C old style function definition
4. Implicit function definition
### Implicit int rule
C was had implicit int rule until 99. Well, implicit int rule allowed in C89 and not allowed C99 and after that. With C99 standard this rule has been removed. But some compiler has been keep the support for this rule in order to keep support to legacy C code. 
Implicit int rule means the language assumes the type of data as int in some cased when we not mention the int explicitly. For example function returns:
```c
func(void) // There is no return type. The compiler assumes that as int (C)
{

}
```

Don't use a implicit int in C. This is only for legacy code. In the other hand this syntax is an error in C++.

### Void function parameter
In C, there is a difference between empty function argument list and void function argument.
```c
int func();
int foo(void);
```
If we call the function func() with an argument, the compiler doesn't throw an error. But if we call function foo(void) with an argument the compiler does it.

However, calling both function with an argument is a syntax error in C++.

Note: "..." is not an operator. This is a token named eclipses. This token is used in various area in C and C++. But usage of it is much more in C++. Variadic function in C: <mark style="background: #ADCCFFA6;">void f(int, ...);</mark>

### C old style function definition
This rule is not used in C anymore though, it is supported from compilers to give support to legacy C code. For example:
```c
int foo(a, b, c)
double b, c;
{
	///
	return a;
}
```
In this legacy syntax, the function argument types not mentioned in function parentheses but given after that before curly braces. Not given argument types are assumes as int by default.

Old style function definition not supported in C++.

### Implicit function declaration
In C, the compiler look for identifiers (making name lookup) and throws an warning if it not found a declaration of it. 
But in C++, the compiler throws an error instead of a warning. This is a name look error.
For example:
```c
int main()
{
	func(1, 2);
}
```
The C compiler looks for the function name and assumes it as a function name that defined in an external module if the compiler not find the identifier of function. And also assumes its return value type as int.
This implicit function declaration is also for supporting legacy code.

## Type and type conversion related differences
Type and type conversion related differences between C++ C core and C:
1. The conversion between arithmetic types and address types
2. Type conversion between different address types
3. Implicit type conversion from void \* to any other address type
4. Character literal types (keep caution on function overload)
5. String literal types

1. The conversation between arithmetic types and address types. This is legal in C but illegal in C++. For example:
```c
int main()
{
	int x = 10;
	int* p = x; // Syntax error in C++, there is no type conversion from T -> T*
	//
	int* p2 = &x;
	int y;
	y = p2; // Syntax error in C++, there is no type conversion from T* -> T
}
```

> [!note] Note: There is an exception to that rule, look to T* -> bool conversion.

2. There is not type conversation between different kind of address types in C++. This is a syntax error. But this is legal in C. For example:
```c
int main()
{
	int x = 34;
	int* p = &x;
	char* ptr = p; // Syntax error in C++, there is no implicit type conversion between address types
}
```

> [!note] Note: There is an exception to that rule, T* -> void\*

3. There is implicit type conversion from void* to other address types in C. This is completely legal in C. But this is an syntax error in C++. For example:
```c
int main()
{
	int x = 10;
	void* p = &x;
	int* iptr = p; // Syntax error in C++, there is NO implicit type conversion form void* to T*
}
```

> [!note] Note: But the reverse situation is legal in C++, type conversion from T* to void\* is legal.

4. Character literals type in C is int type. But character literal type in C++ is char type.
```cpp
int main()
{
	printf("%zu\n", sizeof('A'));
}
```
This is important to figure out undefined behaviors. For example, C++ has feature named function overload.  Which type of argument the overloaded function is using determined which function is called. For example:
```cpp
void func(int);
void func(char);

int main()
{
	func(10);
	func('A'); // Function with char argument is called, because character literal type is char in C++.
}
```

5. String literals are actually an array. The string literals are const in C++, however not const in C. This not mens changing string literals is true in C. This is undefined behavior in C but, error in C++. For example:
```
"hello" -> char[6] in C
"hello" -> const char[6] in C++
```

```cpp
int main()
{
	// Following is legal in C but syntax error in C++
	char * p = "hello";
	// Following is undefined behavior in C but error in C++
	*p = 'A';
}
```

> [!note] Note: There is C++ compilers that the string literal example is not an error but a warning.

## Const keyword differences
Const keyword differences between C++ C core and C:
1. Obligation to initialize a top-level const pointer.
2. Const literal and constexpr usage
3. Linkage differences of global const objects
4. Implicit conversion difference between const address to address.

1. Initializing const objects is a obligation in C++.  This including the top-level const pointers, too. They must be initialized in C++, other way it will be syntax error.
```c
// constant pointer to int
// Top-level pointer
// right-const
int* const ptr; // ! Syntax ERROR in C++. Not initialized const

// pointer to const int
// Low-level ponter
// left-pointer
const int* ptr; // ! Not syntax error on C++
```

2. An array size must be const in C and C++.  In C++ language, If a const variable initialized with a const literal (constant expression), the compiler let us to use this variable in areas that needs const literal such as array size and case of a switch and bit field. This is not allowed in C. For example:
```cpp
int main()
{
	const int size = 10;
	int a[size] = { 0 }; // OK in C++, but syntax error in C
}
```

3. Global const objects has internal linkage in C++. But in C, global const objects have external linkage. In the other expression, global const objects in C++ has a implicit static keyword in front of them.  For example:
```cpp
const int y = 20;
// Means below in global scope:
static const int y = 20;
```

> [!note] Note: External linkage global const object in C++
> If we want a external linkage global const in C++, we must define it with extern keyword: <mark style="background: #D2B3FFA6;">extern const int x = 10;</mark>

4. In C, we have implicit type conversion from <mark style="background: #ADCCFFA6;">const T*</mark> to <mark style="background: #ADCCFFA6;">T*</mark>. But this is syntax error in C++. For example:
```cpp
int main()
{
	const int x = 10;
	int * p = &x; // Valid in C but syntax error in C++
}
```

> [!note] Note: Reverse situation is legal. So, T\* to const T\* is legal.

Because of this the following is a syntax error in C++.
```cpp
int main()
{
	char* p = "Hello"; // const char* to char* is syntax error in C++
}
```

> [!note] Note: An exception case in string literals
> Not always. Some compiler only throws a warning for string literal example. But the conversion always syntax error.
## Bool Type conversion differences
1. C has \_Bool keyword that added with C99 standard. This is a unsigned integer type and we can assign an integer value to that type. stdbool lib provides macros inside it to write "bool  flag = true;" but this is actually same as \_Bool. That leads the logic operators and the comparison operators are also results integer value in C, not the exact bool value. But in C++, bool is real. There are keyword bool, true and false. Therefore in C++, we shouldn't use int instead of bool.
2. In C++ an another type has a automatic type conversion to bool. The rule is that if the value zero than the bool value is false and other ways it is always true.
3. In C++ bool type has automatic type conversion to other types. true bool value is converted to integer 1 or real 1. false bool value is converted to 0.
4. In C++, there are automatic type conversion from pointer types to bool type. Except nullptr, all pointers are converted to bool as true.
5. In C++, that is guarantee to bool variables are 1 byte. But if we want to bool value as 1 bit, C++ offers different tools to do it.
6. In C++, increment operator (++) can't be used with bool object. This was has been allowed older standards of C++, but in modern C++ this is not allowed. <mark style="background: #FFF3A3A6;">(With C++17 and later, that is not allowed)</mark>

## Pointer differences
In C++, we have other options that alternative to pointer. One of them is reference semantics. But of course we can use pointer semantics in C++ too. Some alternatives to pointer in C++ is followings:
- <mark style="background: #BBFABBA6;">reference semantics</mark>
- <mark style="background: #BBFABBA6;">smart pointers</mark>
- <mark style="background: #BBFABBA6;">std::reference_wrapper</mark>

## Array differences
In C++, classical (C style) arrays is not widely used. Can be used at low level codes, but in general std::vector, std::array are preferred.

## Null pointer differences
In C, NULL is a macro not a keyword. And NULL is actually <mark style="background: #ADCCFFA6;">(void*)0</mark>. Also assigning 0 as a integer literal to a pointer means same thing. But in C++, this is differs. <mark style="background: #ADCCFFA6;">nullptr</mark> is a keyword that added to Modern C++. Don't use NULL macro instead of nullptr in C++, if you are not dealing with legacy code. Not use 0 either.

<mark style="background: #FFF3A3A6;">nullptr is a keyword in C++. This means we are not needed to include a header file to use it. nullptr is a literal that type <mark style="background: #ADCCFFA6;">nullptr_t</mark>. nullptr_t not an address type but nullptr can be assigned to addresses. It can transform to a pointer type. The advantage of nullptr, it can't be transform into not pointer types</mark>. For example:
```cpp

int* ptr = nullptr; // OK
int ptr2 = nullptr; // ! NOT OK. Syntax ERROR.
```
<mark style="background: #FFF3A3A6;">nullptr also important to function overloading concept</mark>. But we will mention in later.

## User defined type differences
User defined type differences between C++ C core and C:
1. Underlying type difference of enum
2. Define style difference of enum object
3. There is no implicit type conversion from other types to enums and between enum types in C++
4. There is implicit type conversion from classical enums to other types in C++ like C.
5. Enum underlying type can be defined in C++
6. There is scoped enum (enum class) approach in C++

 1. In C, default enumerator underlying type is integer. But in C++, enumerator underlying type determined during compile time.
 ```c
enum Color {Blue, Red, Black};
// underlying type
int main()
{
	sizeof(enum Color) == sizeof(int); // True in C, but nor always true in C++
}
```

2. In C we define an enum object with enum keyword: <mark style="background: #ADCCFFA6;">enum Color mycolor = Red;</mark> . But in C++ there is no need to enum keyword (using it is optional): Color <mark style="background: #ADCCFFA6;">mycolor = Red;</mark>. 
<mark style="background: #FFF3A3A6;">int underlying type enum codes written in C is can not be compatible with C++</mark>.

3. In C, there is implicit type conversion between enums and arithmetic types. But in C++, there is no implicit (automatic) type conversion from other types to enum types. And also there is no type conversion between enum types.
```cpp
enum Color {Blue, Red, Black};
int main(void)
{
	Color c = Blue;
	c = 3; // This is NOT allowed in C++
}
```

4. But in C++, there is implicit (automatic) type conversion from classical enums to arithmetic types (Reverse not allowed) only with classical enum types. One of the reason of the existing of enum class is this. <mark style="background: #FFF3A3A6;">This conversion not allowed with enum classes</mark>.
```cpp
enum Color {Blue, Red, Black};
int main(void)
{
	Color c = Blue;
	int x = c; // This is allowed in C++
}
```

5. We can define an enums underlying type in C++. This must be integer type and can't be real number type. This feature included with modern C++. We can chose underlying type for classical enum types and scoped enum (enum class) types.
```cpp
enum Color : unsigned char {Blue, Red, Black};
```

6. In C++, there is an another approach to enums that is scoped enum. Both C and C++ classical enum is in scope that it is defined. For that reason we can't use same enum member name in two different enums in same scope. For overcome of this problem, scoped enums (enum class) are added in C++. For example:
```cpp
enum class Color {Blue, Red, Black};
enum class TrafficLight {Yellow, Red, Green};
// it is possible to define red in two enum class

int main()
{
	Color c = Color::Blue; // Scope resolution operator must bu used
	TrafficLight tl = TrafficLight::Red;
}
```

## Another difference
Array size can't be less than necessary when a string literal assigned to a char array in C++, and this is a syntax error. But in C this is not a syntax error, it is undefined behavior in C.
```c
# include <stdio.h>

int main()
{
	char str[5] = "Hello"; // Alloved in C, but sytax ERROR in C++
	// Error because array size must be one more for \0
	// Not related to type conversion
	// Error message: error: initializer-string for char array is too long

	puts(str); // Undefined bevior in C
}
```

---
# Terms
#Cpp_Terms 
- Static typing
- Dynamic typing
- Undefined behavior
- Unspecified behavior
- Implementation defined behavior 
- Implicit Integer Rule
- Implicit function declaration
- Reference semantic
- Underlying Type
- Implicit Type conversation
---
Return: [[00_Course_Files]] 