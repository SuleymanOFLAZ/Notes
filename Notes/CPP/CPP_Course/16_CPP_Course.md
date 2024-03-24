#Cpp_Course 
[[00_Course_Files]]
# A Counter Class Example

```cpp title:counter.h
class Counter {
public:
	explicit Counter(int val = 0) : mc{cal} {} // Explicit converter constructor
	friend std::ostream& operator<<(std::ostream& os, const Counter& c) // Insertor function
	{
		return os << '|' << c.mc << '|';
	}
	Counter& operator++()
	{
		++mc;
		return *this;
	}
	Counter operator++(int)
	{
		Counter ret{*this};
		++ *this;
		return ret;
	}
	Counter& operator--()
	{
		--mc;
		return *this;
	}
	Counter operator--(int)
	{
		Counter ret{*this};
		-- *this;
		return ret;
	}
	explicit operator int()const // Type conversion operator function.
	{
		return mc;
	}
private:
	int mc;
};
```
```cpp title:main.c
#include <iostream>
#include "counter.h"

int main()
{
	Counter c;

	for(int i = 0; i < 10; ++i) {
		std::cout << c++ << "\n";
	}

	int ival = static_cast<int>(c);

	std::cout << "ival = " << ival << "\n";
}
```
# Operator Overloading and Enum Type
#Cpp_OperatorOverloading 
The increment and decrement operators can't be used with enum types in C++(can be used in C). We cannot directly give an enum object to the output stream (we can do it with the static_cast). #Cpp_enumClass #Cpp_enum #Cpp_C_difference 

```cpp error:ERROR valid:OK
#include <iostream>

enum class Weekday{
	Sunday,
	Monday,
	Tuesday,
	Wednesday,
	Thursday,
	Friday,
	Saturday
};

int main()
{
	Weekday wd{Weekday::Monday};

	++wd; // Syntax ERROR.

	std::cout << wd; // Syntax ERROR.

	std::cout << static_cast<int>(wd); // OK.
}
```

Incrementing or decrementing an enum object can't be meaningful most of the time. However, it can be meaningful sometimes, for example, an enum holds the days of the week.

These features can be implemented for class. Let's write increment and decrement operator functions and a inserter function for an enum.

We cannot write member functions for enums because enums are not classes. We have to write these functions as free (global) functions.

```cpp
#include <iostream>

enum class Weekday{
	Sunday,
	Monday,
	Tuesday,
	Wednesday,
	Thursday,
	Friday,
	Saturday
};

Weekday& operator++(Weekday& wd)
{
	using enum Weekday; // Only after C++20

	return wd = (wd == Saturday ? Sunday : static_cast<Weekday>(static_cast<int>(wd) + 1));
}

Weekday& operator++(Weekday& wd, int)
{
	Weekday ret{wd};
	++wd;
	return ret;
}

Weekday& operator--(Weekday& wd)
{
	using enum Weekday; // Only after C++20

	return wd = (wd == Sunday ? Saturday : static_cast<Weekday>(static_cast<int>(wd) - 1));
}

Weekday& operator--(Weekday& wd, int)
{
	Weekday ret = wd;
	--wd;
	return ret;
}

std::ostream& operator<<(std::ostream& os, const Weekday& wd)
{
	static const char* const pdays[] = {
		"Sunday", 
		"Monday", 
		"Tuesday", 
		"Wednesday", 
		"Thursday", 
		"Friday", 
		"Saturday"
	};
	
	return os << pdays[static_cast<int>(wd)];
}

int main()
{
	Weekday wd{Weekday::Monday};

	for(;;) {
		std::cout << wd++;
		(void)getchar();
	}
}
```


# Compiler Attributes
#Cpp_CompilerAttributes
There are many tools that are in the syntax of C++ and aren't in the syntax of C. One of them is the "**attributes**". The attributes don't exist in C standard however, the C compilers can provide it as the extension (for example the gcc compiler provides).

Attributes are tools that direct the compiler at compile time and enable the compiler to give or not give a warning message in many cases, but they are not extensions and have become the standard of the language.

The attributes are added with modern C++. #Cpp11

The syntax of attributes is writing pre-defined identifiers inside double square brackets. Some of them can take more than one parameter.
## Compiler Attribute: nodiscard
#Cpp_CompilerAttribute_nodiscard
For example "nodiscard" is one of the important attributes.

The return type of a function can be in one of the following states. (C)
- The return type of the function is complementary information and the return value of it isn't used by the callee and this is not a logic error. For example, the "int printf(const char\* p, ...)" in C. The return of the function is the character count that is written to the standard output. The callees of the "printf" don't use the return value most of the time.
- There are functions that it a logic error if its return value is not used. For example, the function "bool isprime(int val);" returns true if the given value is a prime number and, returns false if the given value is not a prime number. It is a logic error if the return of the function is discarded.

The compiler can't understand if discarding the return value is a logical error or not. So, we can state it with the "nodiscard" attribute.

```cpp hl:1 warn:9
[[nodiscard]] bool isprime(int val);

int main()
{
	int x;

	x = 43253;

	isprime(x); // The compiler throws an warning message now because the "isprime" function is a nodiscard function and we didn't use return value of it. The warning is: discarding return value of function with 'nodisdard' attribute
}
```

The standard library frequently uses these compiler attributes.
# Namespace
#Cpp_NameSpace 
The "**namespace**" is a keyword.

The programs that are written in C++ are very big in comparison with the programs that are written in C. The big programs bring the "**Global Namespace Pollution Problem - GNPP**". The C has one main namespace which is called "global space". The same name can't be used again in a namespace. This is a very big problem in languages like C++ because only the standard library has a couple thousand names. The languages such as C++, C#, and Java have different tool sets to solve the GNPP problem. C doesn't have such a tool.  #Cpp_C_difference 

The namespaces are coding regions to prevent names from overlapping each other.

The global area is a namespace also and it is called "**global namespace**". The other namespaces have to be inside the global namespace. We can create nested namespaces.

The syntax of a namespace consists of the namespace keyword followed by the name of the namespace and the curly braces. There is no semicolon after the braces, however, it wouldn't be a syntax error if we wrote a semicolon after the braces, it would be a null statement. We can write a namespace without giving it a name which is called "**unnamed namespace**", but this is for a special case, we will look at it later.

```cpp
namespace Myspace { // The syntax of a namespace

}

namespace { // The unnamed namespace, used for a special case, we look at it later.

}
```

Namespaces are scopes, thus the name lookup is related to the namespaces. #Cpp_name_lookup 

```cpp
int g = 10; // It is in the namespace scope (global namespace scope).

namespace Myspace {
int g = 10;
}
```

Q1: Which scope does the name in line 1 belong to in the upper example?
A1: It is in the namespace.
Q2: Which namespace does it belong to?
A2: It is in the global namespace

The "Myspace" namespace is a nested namespace in the global namespace.

There is no access control between the namespaces.

AT 1:53






---
# Terms
- Attributes
- namespace
- global namespace
- Global Namespace Pollution Problem
- unnamed namespace

---
Return: [[00_Course_Files]]