[[00_Course_Files]]
#Cpp_Course
# About C++ and C++ Standards
C++ is a multi-paradigm programming language. The paradigms that C++ supports are:
1. Procedural paradigm
2. Object-oriented paradigm
3. Generic programming (Polymorphism)
4. Genre-independent programming paradigm

Legacy C++ codes can be compiled by setting up compiler switches.

The first C++ standards are C++98 and C++03. Actually, these standards are the same. A guarantee has been given on the C++98 standard to not change the language features until existing problems are discovered and reported in order to fix them.

The new rule has been established that new C++ standards are published apart over three years. And 11, 14, 17, and 20 standards are published.

C++11 and later standards are mentioned Modern C++ informally.

# C++ C core and C Differences
#Cpp_C_core
C++ has a core that is based on C. But this C core is not exactly the same as the C language.

The main reason for these differences is that compile-time checks are more restricted in C++.

In programming languages, there are two basic type concepts. These are:
1. static typing
2. dynamic typing

Compiler detect data's type depending on the source code and can make checks at compile time. For example, if we try to assign a real number to an integer type, the compiler can detect that and warn us. This is a static typing concept. The dynamic typing concept is to detect data type on run time. For example x = "string", then x = 12.

C++, C#, and Java like programming languages actually have the static typing concept. But also they have support to dynamic typing concepts.

Though C has a static typing concept. The dynamic typing concept can be ensured by writing code.

Although both C and C++ have static typing concepts, this does not mean their concepts are identical. C is designed as a small and generic language. For this reason, the type-checking responsibility of C on compile time is not as exact as C++. C has weak type control. C++ provides strict type controls.

For example, type conversion from "void*" to any other type is allowed and okay in C. But in C++ this kind of conversion is a syntax error if we don't use a type conversion operator.

```cpp
int *p = malloc(n); // malloc return type is void*
// This is a syntax error in C++ (void* - > T*)
```

The type of the string literals in C is "char*", but in C++ it is "const char*".
Because the type of string literals of string in C is "char*", the following syntax is legal in C, but it is also undefined behavior in C:
```c
char *p = "Hello";
```

But in C++, this literal type is "const char*". On the other mean, upper code piece is illegal in C++ and not allowed in ISO C++11 standard. The compiler throws a warning in this situation. #Cpp11
Type conversion of a const address to a non-const address is a syntax error in C++. (Except for the string literal example, some compilers throw an error instead of a syntax error with this kind of conversation.)

This kind of incompatible situation is increased with every new C++ standard.

For example, the auto keyword is in both C and C++. But the meaning of it differs between them. The register keyword also has different meanings between C and C++. The register keyword is reserved for different meanings in C++.

## Three important terms to know in both C and C++
There are three important terms to know both in C and C++. These are:
1. Undefined behavior
2. Unspecified behavior
3. Implementation-defined behavior

A small part of undefined behavior in C is a syntax error in C++. There are some situations that undefined behavior in C but are defined in C++.

## Differences of functions 
#Cpp_C_difference 
Function differences between C++ C core and C:
1. Implicit int rule
2. Void function parameter
3. C old-style function definition
4. Implicit function definition
### Implicit int rule
C had implicit int rule until 99. Well, the implicit int rule is allowed in C89 and not allowed in C99 and after that. With the C99 standard, this rule has been removed. However, some compilers have been keeping the support for this rule in order to keep support to legacy C code. 
Implicit int rule means the language assumes the type of data as an int in some cases when we do not mention the int explicitly. For example function returns:
```c
func(void) // There is no return type. The compiler assumes that as int (C)
{

}
```

Don't use an implicit int in C. This is only for legacy code. On the other hand, this syntax is an error in C++.

### Void function parameter
In C, there is a difference between the empty function argument list and the void function argument.
```c
int func();
int foo(void);
```
If we call the function func() with an argument, the compiler doesn't throw an error. But if we call function foo(void) with an argument the compiler does it. (in C)

However, calling both functions with an argument is a syntax error in C++.

> [!note] Note:  "..." is not an operator. This is a token named **eclipses**. This token is used in various areas in C and C++. But usage of it is much more in C++. Variadic function in C: "void f(int, ...);"

### C old-style function definition
This rule is not used in C anymore though, it is supported by compilers to give support to legacy C code. For example:
```c
int foo(a, b, c)
double b, c;
{
	///
	return a;
}
```
In this legacy syntax, the function argument types are not mentioned in function parentheses but given after that before curly braces. Not given argument types are assumed as int by default.

Old style function definition not supported in C++.

### Implicit function declaration
In C, the compiler looks for identifiers (making name lookup) and throws a warning if it does not find a declaration of it. 
But in C++, the compiler throws an error instead of a warning. This is a name-look error.
For example:
```c
int main()
{
	func(1, 2);
}
```
The C compiler looks for the function name and assumes it is a function name that is defined in an external module if the compiler does not find the identifier of the function. And also assumes its return value type as int.
This implicit function declaration is also for supporting legacy code.

## Type and type conversion-related differences
Type and type conversion-related differences between C++ C core and C:
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

2. There is no type of conversation between different kind of address types in C++. This is a syntax error. But this is legal in C. For example:
```c
int main()
{
	int x = 34;
	int* p = &x;
	char* ptr = p; // Syntax error in C++, there is no implicit type conversion between address types
}
```

> [!note] Note: There is an exception to that rule, T* -> void\*

3. There is an implicit type conversion from "void*" to other address types in C. This is completely legal in C. But this is a syntax error in C++. For example:
```c
int main()
{
	int x = 10;
	void* p = &x;
	int* iptr = p; // Syntax error in C++, there is NO implicit type conversion form void* to T*
}
```

> [!note] Note: But the reverse situation is legal in C++, type conversion from T* to void\* is legal.

4. Character literals type in C is int type. But the character literal type in C++ is char type.
```cpp
int main()
{
	printf("%zu\n", sizeof('A'));
}
```
This is important to figure out undefined behaviors. For example, C++ has a feature named function overload.  Which type of argument the overloaded function is using determines which function is called. For example:
```cpp
void func(int);
void func(char);

int main()
{
	func(10);
	func('A'); // Function with char argument is called because character literal type is char in C++.
}
```

5. String literals are actually an array. The string literals are const in C++, however not const in C. This does not mean changing string literals is true in C. This is an undefined behavior in C but, an error in C++. For example:
```
"hello" -> char[6] in C
"hello" -> const char[6] in C++
```

```cpp
int main()
{
	// Following is legal in C but syntax error in C++
	char * p = "hello";
	// Following is undefined behavior in C but an error in C++
	*p = 'A';
}
```

> [!note] Note: There are C++ compilers that the string literal example is not an error but a warning.

## Const keyword differences
Const keyword differences between C++ C core and C:
1. Obligation to initialize a top-level const pointer.
2. Const literal and constexpr usage
3. Linkage differences of global const objects
4. Implicit conversion difference between const address to address.

1. Initializing const objects is an obligation in C++.  This includes the top-level const pointers, too. They must be initialized in C++, another way will be syntax error.
```c
// constant pointer to int
// Top-level pointer
// right-const
int* const ptr; // ! Syntax ERROR in C++. Not initialized const

// pointer to const int
// Low-level ponter
// left-pointer
const int* ptr; // ! No syntax error on C++
```

2. An array size must be const in C and C++.  In C++ language, If a const variable is initialized with a const literal (constant expression), the compiler lets us use this variable in areas that need a const literal such as array size and case of a switch and bit field. This is not allowed in C. For example:
```cpp
int main()
{
	const int size = 10;
	int a[size] = { 0 }; // OK in C++, but syntax error in C
}
```

3. Global const objects have internal linkage in C++. But in C, global const objects have external linkage. In the other expression, global const objects in C++ have an implicit static keyword in front of them.  For example:
```cpp
const int y = 20;
// Means below in global scope:
static const int y = 20;
```

> [!note] Note: External linkage global const object in C++
> If we want an external linkage global const in C++, we must define it with extern keyword: "extern const int x = 10;"

4. In C, we have an implicit type conversion from "const T*" to "T*". But this is a syntax error in C++. For example:
```cpp
int main()
{
	const int x = 10;
	int * p = &x; // Valid in C but syntax error in C++
}
```

> [!note] Note: The reverse situation is legal. So, T\* to const T\* is legal.

Because of this, the following is a syntax error in C++.
```cpp
int main()
{
	char* p = "Hello"; // const char* to char* is syntax error in C++
}
```

> [!note] Note: An exception case in string literals
> Not always. Some compiler only throws a warning for string literal examples. But the conversion is always a syntax error.
## Bool-type conversion differences
#Cpp_bool
1. C has \_Bool keyword that added with C99 standard. This is an unsigned integer type and we can assign an integer value to that type. stdbool lib provides macros inside it to write "bool flag = true;" but this is actually the same as \_Bool. That leads the logic operators and the comparison operators are also result integer value in C, not the exact bool value. But in C++, bool is real. There are keywords bool, true, and false. Therefore in C++, we shouldn't use int instead of bool. #Cpp_keyword_bool
2. In C++, another type has an implicit type conversion to bool. The rule is that if the value is zero then the bool value is false and in other ways, it is always true.
3. In C++ bool type has implicit type conversion to other types. true bool value is converted to integer 1 or real 1. false bool value is converted to 0.
4. In C++, there are automatic type conversions from pointer types to bool types. Except for nullptr, all pointers are converted to bool as true.
5. In C++, that is guaranteed to bool variables are 1 byte. But if we want to use a bool value as 1 bit, C++ offers different tools to do it.
6. In C++, the increment operator (++) can't be used with a bool object. This has been allowed in older standards of C++, but in modern C++ this is not allowed. (With C++17 and later, that is not allowed) #Cpp17

## Pointer differences
In C++, we have other options that are alternatives to pointers. One of them is reference semantics. But of course, we can use pointer semantics in C++ too. Some alternatives to pointers in C++ are the following:
- reference semantics
- smart pointers
- std::reference_wrapper

## Array differences
In C++, classical (C style) arrays are not widely used. It can be used at low-level codes, but in general std::vector, and std::array are preferred.

## Null pointer differences
In C, NULL is a macro, not a keyword. And NULL is actually "(void*)0". Also assigning 0 as an integer literal to a pointer means the same thing. But in C++, this differs. **nullptr** is a keyword that is added to Modern C++. Don't use NULL macro instead of nullptr in C++, if you are not dealing with legacy code. Do not use 0 either. #Cpp_keword_nullptr

nullptr is a keyword in C++. This means we do not need to include a header file to use it. nullptr is a literal type that is **nullptr_t**. nullptr_t is not an address type but nullptr can be assigned to addresses. It can transform to a pointer type. The advantage of nullptr, it can't be transformed into not pointer types. For example:
```cpp

int* ptr = nullptr; // OK
int ptr2 = nullptr; // ! NOT OK. Syntax ERROR.
```
nullptr is also important to the function overloading concept. But we will mention it later.

## User-defined type differences
User-defined type differences between C++ C core and C:
1. Underlying type difference of enum
2. Define style difference of the enum object
3. There is no implicit type conversion from other types to enums and between enum types in C++
4. There is implicit type conversion from classical enums to other types in C++ like C.
5. Enum underlying type can be defined in C++
6. There is a scoped enum (enum class) approach in C++

 1. In C, the default enumerator underlying type is integer. But in C++, the enumerator underlying type is determined during compile time. #Cpp_enum
 ```c
enum Color {Blue, Red, Black};
// underlying type
int main()
{
	sizeof(enum Color) == sizeof(int); // True in C, but nor always true in C++
}
```

2. In C we declare an enum object with the enum keyword: "enum Color mycolor = Red;". But in C++ there is no need to enum keyword (using it is optional): "Color mycolor = Red;". 
int underlying type enum codes written in C are not compatible with C++.

3. In C, there is implicit type conversion between enums and arithmetic types. But in C++, there is no implicit (automatic) type conversion from other types to enum types. And also there is no type conversion between enum types.
```cpp
enum Color {Blue, Red, Black};
int main(void)
{
	Color c = Blue;
	c = 3; // This is NOT allowed in C++
}
```

4. But in C++, there is implicit (automatic) type conversion from classical enums to arithmetic types (Reverse not allowed) only with classical enum types. One of the reasons for the existence of enum class is this. This conversion is not allowed with enum classes.
```cpp
enum Color {Blue, Red, Black};
int main(void)
{
	Color c = Blue;
	int x = c; // This is allowed in C++
}
```

5. We can define an enum's underlying type in C++. This must be an integer type and can't be a real number type. This feature is included with modern C++. We can choose the underlying type for classical enum types and scoped enum (enum class) types. #Cpp11
```cpp
enum Color : unsigned char {Blue, Red, Black};
```

6. In C++, there is another approach to enums which is scoped enum. Both C and C++ classical enum is in scope that it is defined. For that reason, we can't use the same enum member name in two different enums in the same scope. To overcome this problem, scoped enums (enum class) are added in C++. For example:
```cpp
enum class Color {Blue, Red, Black};
enum class TrafficLight {Yellow, Red, Green};
// It is possible to define red in two enum classes

int main()
{
	Color c = Color::Blue; // Scope resolution operator must bu used
	TrafficLight tl = TrafficLight::Red;
}
```

## Another difference
Array size can't be less than necessary when a string literal is assigned to a char array in C++, and this is a syntax error. But in C this is not a syntax error, it is undefined behavior in C.
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
- eclipses token
- bool/true/false keywords
- nullptr keyword
---
Return: [[00_Course_Files]] 