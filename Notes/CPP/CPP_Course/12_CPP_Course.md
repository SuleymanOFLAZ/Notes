#Cpp_Course 
[[00_Course_Files]]
# Static Data Members of the Classes
#Cpp_StaticDataMembers
The data members of the classes are divided into two categories. These are non-static data members and **static data members**.

The non-static data members of the class are in the class instance. That means there are independent non-static data members for each class object. Each non-static data member increases the size of the class.

The static members of the class are not in the class instance. There is one member for the class for each static member. This means there are no independent static members for each class object. Static members exist, even if we don't declare an object. Static members are created before the main function and destroyed after the main function.

Actually, a static data member is not different from a global variable, made compatible with other tools of the language. All of the objects of the class can use the static data members.

The static members are declared with the **static keyword**. Note that, the meaning of the keyword is different from the other use cases of the keyword. #Cpp_keyword_static

```cpp
class Myclas {
	static int x; // Static data member
};
```

Why do we use a static data member instead of a global variable?
- Static data members are declared at the class scope. We have to qualify the static data member with the resolution, dot, or arrow operator to be able to use it. #Cpp_operator_resolution
- The static data members are subjected to access control. It can be public, private, or protected.

> [!note] Note: Some programming languages use "**instance variables**" naming instead of "static variables", and use "**class variables**" naming instead of "non-static variables", However, we don't use these namings in C++.

When do we use the static variables? They hold the data that is related to the class and all of the objects of the class can use that data. For example, lookup tables that all the class instances use.
```Cpp
class Account {
public:

private:
	interest_rate
};


```

Static data members are not a definition, they are declarations. Because the compiler doesn't allocate an area for the member when it allocates an area for the class. In general, we declare a static data member inside the class definition and we define it in the .cpp file.

For example. the following code has an error. The code is compiled but an error is taken at the linkage process. The reason for this, the static data member is declared but no area is allocated for it.
```cpp title:myclass.h
class Myclass {
public:
	static int x;
};
```
```cpp title:client.cpp
int main()
{
	Myclass::x = 10; // This is a linker ERROR
}
```

We define a static data member as follows:
```cpp title:myclass.h
class Myclass {
public:
	static int x;
};
```
```cpp title:myclass.cpp
int Myclass::x;
```
```cpp title:client.cpp
int main()
{
	Myclass::x = 10; // OK. The static data member is defined.
}
```

**There are some rules while defining a static data member:**
1. The static keyword will not be included in the definition.
2. We initialize the data member. First, it will be zero-initialized. If the static data member is a class type, the default constructor of the member will be called. #Cpp_InitializeMethods  For example:
```cpp title:"initializing a static data member"
int Myclass::x; // Default initialized. First, it will be zero-initialized.
int Myclass::x = 77; // OK
int Myclass::x(77); // OK
int Myclass::x{77}; // OK
```
3. If the static data member is an array, there is no obligation to state its array size inside the class declaration. Because, it is a declaration, not a definition. We can declare a static data member inside the class definition as an **incomplete type**. Note that, if the compiler uses the "**zero-sized array**" extension, it can give an error for that syntax. We will define the size at the definition of the member inside the .cpp file.
```cpp title:"Declaring a static array data member"
class Myclass {
public:
	static int a[]; // OK
	int x[]; // Syntax ERROR. A non-static data member can't be an incomplete type.
};
```
3. We can declare an incomplete static member inside the class definition.
```cpp title:"Incomplete static data member declaration"
class Data;

class Myclass {
	Data dx; // OK
};
```

## Incomplete Type
An incomplete type can't be a non-static member. This rule is valid both in C and C++. The most basic example is the following:

```cpp title:"incomplete type"
class Data; // A forward declaration

class Myclass{
public:
	Data(); // Syntax error, the data is an incomplete type.
};
```

```c title:"incomplete type"
struct Data; // A forward declaration

struct Mydata{
	struct Data dx; // Syntax error, the data is an incomplete type.
};
```

**What can we do with an incomplete type legally?**
1. We can declare a pointer variable.
2. We can use it at a function declaration (not definitions).
3. We can make an extern declaration.
4. We can make a typedef declaration.
5. We can use it at a type of alias declaration.

> [!note] Note: **Type alias declarations** are made by only typedef declarations in C. However, we can make a type alias declaration with the "using" keyword too in C++. The "using" has come with the C++11. #Cpp11 #Cpp_keyword_using #Cpp_TypeAliasDeclaration

```cpp title:"Using the incomplete type"
class Myclass; // A forward declaration, incomplete type

Myclass f1(Myclass*); // OK. We can declare a function with an incomplete type.
Myclass& f2(Myclass&); // OK.
Myclass f3(Myclass); // OK.

extern Data dx; // OK. We can make an extern declaration with an incomplete type.
extern Myclass a[]; // OK.

typedef Myclass* MyclassPtr; // OK. We can make a typedef declaration with an incomplete type.
typedef Myclass& MyclassRef; // OK.

using MyclassPtr = Myclass*; // OK. We can make a type alias declaration with an incomplete type.

int main()
{
	Myclass* p = nullptr; // OK. We can declare a pointer with an incomplete type.
	f1(p); // Syntax ERROR. Because the return of the "f1" is "Myclass", it has to be a reference or pointer to use an incomplete type
	f2(p); // OK.
}
```

```cpp title:"Type Alias Declarations"
typedef Myclass* MyclassPtr;
using MyclassPtr = Myclass*; // Both are the same
```


## Inline Variables
#Cpp11
A tool called "**inline variables**" is added to the language with the C++17 standard. We can define the inline static data member of the class inside the header file. However, defining the static data member inside the header file without this tool, that is before C++17, breaks the **One Definition Rule**. #Cpp_ODR
 
**Can we initialize the static data member inside the class definition?**
There are two ways to do that:
1. We use the inline variable that has come with C++17.
2. If the static member is a const and integer, we can initialize it with a constant expression inside the class definition. Both of the attributes have to be ensured to do that. This feature has been coming from the old C++.

```cpp title:"initializing a const and integer type static data member inside the class definition." valid:3 error:4,5,6
class Myclass{
public:
	const static int x = 5; // We can initialize it inside the class definition because it is a const and an integer.
	static int y = 5; // Syntax ERROR. We CANNOT initialize it inside the class definition because it is not a const. The error message: a member with an in-class initializer must be const.
	const static double d = 5; // Syntax ERROR. We CANNOT initialize it inside the class definition because it is not an integer. The error message: a member of "const double" cannot have an in-class initializer.
	const static int z = foo(); // Syntax ERROR. We have to initialize with a constant expression
};
```

The inline variables can be defined inside the header file just like the inline functions. An inline function doesn't break the ODR.

We can define an inline variable with the "**inline**" keyword. #Cpp_keyword_inline

```cpp title:"Defining an inline variable (After C++17)"
inline int x = 10; // After C++17

class Myclass {
public:
	static inline int x = 5; // After C++17
};
```

This is a very important concept because there is a module model that is called "**Header Only Library**". A lot of the library of the boots is a header-only library. The header-only libraries just consist of the header file. 

There was an issue that if the class has a global variable or static data member, a source file needed to implement the library because if they are defined inside the header file that breaks the ODR. We were doing some tricks to overcome this issue. Let's look at the following question.

> [!question] Question: How do we implement a header-only library if we have a global variable before C++17 (there is no inline variable concept)? #Cpp_Interview_Question 
> A: We could write an inline wrapper function for the global variable.

```cpp title:"Wrapping a global variable to use it inside a header-only library (Before C++17)"
inline int& get_instance()
{
	static int x = 10;

	return x;
}
```

## How Client Codes Can Access The Static Data Member

We have to use a qualified name to access the static data member of the class. Note that, static data members are subjected to access control. There are three ways to access the static data member:
1. Accessing by qualifying the name
2. Accessing by using the dot operator. This is not recommended because it may mislead the reader. The reader may evaluate the variable as a non-static data member.
3. Accessing by using the arrow operator. This is not recommended because it may mislead the reader.
```cpp title:"Accessing a static data member." error:Myclass::y
class Myclass {
public:
	static int x;
private:
	static int y;
};

int main()
{
	Myclass::x = 10; // 1. Accessing by qualifying the name. (There is no need for an instance to assess a static data member)
	Myclass::y = 20; // Syntax ERROR because of the access control. (Note that, the access control is done last)
	
	Myclass mx;
	Myclass* p{&mx};
	mx.x = 10; // 2. Accessing by the dot operator. (Not recommended)
	p->x = 20; // 3. Accessing by the arrow operator. (Not recommended)
}
```

Let's consider the error reasons when accessing a non-static data member.
```cpp title:"The error reasons when accessing a non-static data member." error:8,9 hl:3
class Myclass {
public:
	int x; // Non-static data member
};

int main()
{
	auto y = Myclass::z; // Syntax Error. Name-lookup error. The compiler can't find the name.
	auto b = Myclass::x; // Syntax Error. There is a need for an instance because x is a non-static data member. This is not a name-lookup example.
	sizeof(Myclass::x) // OK. Because it is an unevaluated context.
	decltype(Myclass::x) // OK. Because it is an unevaluated context.
}
```

## We can't initialize a static data member with a constructor
A constructor doesn't initialize a static data member. The member initializer-list an initializer syntax and a static data member isn't initialized with it. A static data member is created before calling the constructor.
```cpp error:x{10}
class Myclass{
public:
	Myclass() : x{10} {} // Syntax ERROR. A constructor doesn't initialize a static data member.
private:
	inline static int x;
};
```

Global variables and static data members of the classes are initialized before the main call.
```cpp
int foo()
{
	std::cout << "foo\n";
	return 5;
}

class Myclass {
	inline static int x = foo();
};

int main()
{
	std::cout << "main\n";
}
```
``` title:output
foo
main
```

**How Many Objects Are Alive**
We can increase a static data member inside the constructor to count how many objects are alive.

# Static Member Functions of the Classes
#Cpp_StaticMemberFunctions
Non-static data members are called for the class instances. They take the class instance address as a hidden parameter. However, the static member functions of the classes don't have the this pointer. They aren't called for a class instance. However, they are inside the class scope. They can access the private area of the class. They do operations for the class, not for a class instance. They have the access control. Mostly, they are good alternatives to global functions. #Cpp_thisPointer 

A static data member is defined or declared with the static keyword. #Cpp_keyword_static 
```cpp title:"myclass.h"
class Myclass{
	static int foo(); // Static member function. (Declaration)
	int func(); // Non-static member function.
};
```
```cpp title:myclass.cpp
int Myclass::foo() // Definition on the static member function. We can't use the static keyword here.
{

}
```

> [!warning]  Warning: We can't use the static keyword at the static member function declaration inside the .cpp file.

> [!warning]  Warning: We can't declare a static function as const because they don't take an instance address.

We can't figure out if the function is static or not just by looking at the function definition because we can't use the static keyword in the function definition. To overcome this situation, a macro can be used like the following:

```cpp title:myclass.cpp
#define STATIC

STATIC int Myclass::foo() // Definition on the static member function. We can't use the static keyword here.
{

}
```

Even "PUBLIC" and "PRIVATE" macros can be used just like mentioned in the upper example to indicate whether a static member function is private or public.

We can define a static member function inside the class definition as an implicitly inline function.
```cpp title:myclass.h
class Myclass{
	static int foo() // Static member function. Implicitly inline.
	{
		// ...
		return 1;
	}
};
```

> [!note] Note: We can use the inline keyword in the upper example. This makes it explicitly inline. #Cpp_keyword_inline 

We can define the static member function inside the header file and outside the class definition. We have to use the inline keyword here because it breaks the ODR if we don't use the inline keyword. #Cpp_ODR

```cpp title:myclass.h
class Myclass{
	static int foo(); // Static member function.
};

inline int Myclass::foo() // We have to use the inline keyword here. (The ODR)
{
	// ...

	return 1;
}
```

> [!warning]  Warning: We have to use the inline keyword if we define a static member function inside the header file and outside the class definition because it breaks the ODR if we don't use the inline keyword.

Static member functions of the classes can be accessed by the dot and the arrow operators. However, this is not a convenient approach. It can mislead the reader or is open to make mistakes. The resolution operator is a more convenient approach. #Cpp_operator_resolution 

> [!warning]  Warning: Due to a static member function being called by using the dot or arrow operator, we should be careful to not make a call to a static member function instead of a non-static member function by mistake.
## How can a static member function use the non-static members?
We can't use the non-static data members inside the static member function because to use a non-static data member we need an object instance.
```cpp error:"mx = 10"
class Myclass{
public:
	static int foo() // Static member function.
	{
		mx = 10; // Syntax Error. We need an object instance to use the non-static data member. The compiler error: illegal reference to non-static member 'Myclass'
	}
private:
	int mx, my;
};
```

We can write like the following. A static member function can access the private area of the class.
```cpp valid:"mx = 10"
class Myclass{
public:
	static int foo() // Static member function.
	{
		Myclass m;
		m.mx = 10; // OK.
	}
	static int func(Myclass m)
	{
		m.mx = 10; // OK.
	}
private:
	int mx, my;
};
```

We can write like the following:
```cpp valid:"mx = 10"
class Myclass{
public:
	static int foo();
private:
	int mx, my;
};

Myclass g;

inline int Myclass::foo()
{
	g.mx = 10; // OK.
	return 1;
}
```

These constraints about non-static data members are valid also on non-static member functions. There is a need for a class instance to call a non-static member function.

```cpp error:9
class Myclass {
public:
	void foo()
	{
	
	}
	static void func()
	{
		foo(); // Syntax ERROR. There is a need for a class instance to call a non-static member function.
	}
};
```

## Function Overload between static and non-static member functions
A static and a non-static member functions can be overloaded. #Cpp_FunctionOverloading 
```cpp
class Myclass {
public:
	void func(int)
	{
		std::cout << "func(int)\n";
	}

	static void func(double)
	{
		std::cout << "static func(double)\n";
	}

	void f()
	{
		func(3.4);
		func(5);
	}
};

int main()
{
	Myclass m;

	m.f();
}
```
``` title:"Output"
static func(double)
func(int)
```

## How can a static member function use the static data members?
The static member functions can use the static data members of the class.
```cpp valid:"x = 10"
class Myclass{
public:
	static int foo()
	{
		x = 10; // OK
	}
private:
	inline static int x;
};
```

In general, we don't make the non-const static data members public. But it is widespread the making const static data members public. We can give get facility to non-const static data members. The client codes can reach the non-const static data members via the get facility. The class can change the non-const static data member and give access facility to client codes.
```cpp
#include <iosteam>

class Myclass{
public:
	static int getx()
	{
		return x;
	}
	static void foo()
	{
		x = 10;
	}
private:
	inline static int x{};
};

int main()
{
	Myclass::foo();

	std::cout << Myclass::getx() << "\n";
}
```
## Private Constructor Example
The client codes can't access the constructor of the class and create an object if the constructor is private. However, a static member function can still access. #Cpp_PrivateConstructor
```cpp title:"Private Constructor Example" hl:Myclass() error:15 valid:10
class Myclass{
public:
	static void foo();
private:
	Myclass(); // Private default constructor.
};

inline int Myclass::foo()
{
	Myclass m; // OK. A static member function can access a private constructor.
}

int main()
{
	Myclass m; // Syntax ERROR. Because the constructor is private.
}
```

#  Name lookup rules for the names that initialize the static data members
The names in the expression that initializes the static data members of the classes are looked at in the class scope first, then they are looked at in the namespace scope.

> [!question] Question: What will be the output? #Cpp_Interview_Question 
> A: The output is "2". Because the names in the expression that initializes the static data members of the classes are looked at in the class scope first, then they are looked at in the namespace scope. The answer is related to the name lookup. #Cpp_name_lookup 

```cpp title:"Q: What will be the output? "
int foo()
{
	return 1;
}

class Myclass {
public:
	static int x;
private:
	static int foo()
	{
		return 2;	
	}
};

int Myclass::x = foo();

int main()
{
	std::cout << Myclass::x << '\n';
}
```
``` title:"Output"
2
```

> [!question] Question: What will be the output? #Cpp_Interview_Question 
> A: The output is "10". Because the names in the expression that initializes the static data members of the classes are looked at in the class scope first, then they are looked at in the namespace scope.

```cpp title:"Q: What will be the output? "

int g = 35;
int foo()
{
	return 1;
}

class Myclass {
public:
	inline static int g = 10;
	static int x;
private:
	static int foo()
	{
		return 2;	
	}
};

int Myclass::x = g;

int main()
{
	std::cout << Myclass::x << '\n';
}
```
``` title:"Output"
10
```

# Questions About The Static Members
> [!question] Question: What is the situation? #Cpp_Interview_Question 
> a. Syntax Error
> b. Undefined Behavior
> c. No problem
> A: No problem. the const keyword qualifies the this pointer.

```cpp valid:5
class Myclass {
public:
	void func() const
	{
		x = 43; // OK
	}
	inline static int x = 10;
};
```

> [!question] Question: Is there a syntax error?
> A: No. 

```cpp valid:m.foo,m.func
class Myclass {
public:
	void foo();
	static void func();
};

int main()
{
	Myclass m;

	m.foo(); // OK
	m.func(); // OK
}
```

# Some Typical Patterns That Use the Static Member Functions

## Named Constructor
#Cpp_NamedConstructor #Cpp_idiom
We sometimes prefer using a named function (that is a static member function) as a constructor instead of giving directly a constructor.

Why do we need this? Let's look at it with examples. 
###  A Class that represents two different representations of an object
Complex numbers can shown in two different notations, polar notation and cartesian notation. A typic "Complex class" allows both notation formats. There are two constructors. Consider we write it like the following. The constructor's signatures are the same. This is a syntax error.

```cpp error:double
class Complex{
public:
	Complex(double r, double i);
	Complex(double angle, double distance); // Syntax ERROR. The signatures of the constructors are the same.
};
```

What we can do? We can give the class static member functions to create objects instead of the constructors.

```cpp hl:3,4
class Complex{
public:
	static Complex create_cartasian(double r, double i); // Factory Function
	static Complex create_polar(double a, double d); // Factory Function
};

int main()
{
	auto c1 = Complex::create_polar(1.2, 4.5);
	auto c2 = Complex::create_cartasian(3., 3.4);
}
```

These kinds of functions (which create an object) are called "**factory functions**". We still need a constructor. We write the constructor as private. The constructor is closed to the client codes. Only the member functions can access the constructors. #Cpp_PrivateConstructor

The signatures of the constructors are still an issue. To make the signatures different we give a dummy parameter to one of the constructors. Even we don't need to give a name to that dummy parameter. This signature difference doesn't concern the client codes.

```cpp hl:3,4
class Complex{
private:
	Complex(double r, double i, int dummy);  // Private constructor (with a dummy parameter, to make its signature different)
	Complex(double a, double d); // Private constructor
public:
	static Complex create_cartasian(double r, double i); // Factory Function
	static Complex create_polar(double a, double d); // Factory Function
};

int main()
{
	auto c1 = Complex::create_polar(1.2, 4.5);
	auto c2 = Complex::create_cartasian(3., 3.4);
}
```

The static member functions are called the convenient constructor.
```cpp hl:return
class Complex{
private:
	Complex(double r, double i, int dummy);  // Private constructor (with a dummy parameter, to make its signature different)
	Complex(double a, double d); // Private constructor
public:
	static Complex create_cartasian(double r, double i);
	{
		return Complex{r, i, 0};
	}
	static Complex create_polar(double a, double d);
	{
		return Complex{a, d};
	}
};

int main()
{
	auto c1 = Complex::create_polar(1.2, 4.5);
	auto c2 = Complex::create_cartasian(3., 3.4);
}
```

### A Class that can only be dynamically created
#Cpp_idiom
Another use case of the names constructor is to create a class type that is only possible to create objects dynamically. We make the constructor private so the client codes can't create objects. We give a factory function (which is a static member function that creates objects) that creates objects dynamically (using the new operator).

```cpp
class DynamicOnly{
	DynamicOnly();
public:
	static DynamicOnly& create_object()
	{
		return *new DynamicOnly;
	}
};

int main()
{
	auto &x = DynamicOnly::create_object();
}
```

> [!note] Note: We would use the unique_ptr instead of reference or pointer if we want to use this technique. #Cpp_unique_ptr

## Object Count
#Cpp_Idiom
Sometimes we need to count the object instances that are alive at the point or have been created until now.

```cpp
class Myclass {
public:
	Myclass()
	{
		++num_of_alive;
		++num_of_lived;
	}
	~Myclass()
	{
		--num_of_alive;
	}
	static int get_lived_count()
	{
		return num_of_lived;
	}
	static int get_alive_count()
	{
		return num_of_alive;
	}

private:
	inline static int num_of_alive = 0;
	inline static int num_of_lived = 0;
};

int main()
{
	Myclass m1, m2, m3;
	Myclass* p1 = new Myclass;
	Myclass* p2 = new Myclass;

	if(1)
	{
		Myclass m4, m5;
	}

	delete p1;

	std::cout << "Lived: " << Myclass::get_lived_count() << '\n';
	std::cout << "Alive: " << Myclass::get_alive_count() << '\n';

	delete p2;
}
```

> [!note] Note: We need to define the copy constructor and increment the count inside it too to count the copied objects.
# Delegating Constructor
#Cpp_constructor #Cpp_DelegatingConstructor

The **delegating constructor** is added with C++11. #Cpp11

First, let's look why the delegating constructor is added. Most of the time, there are a common code that initializes the members of the class.
```cpp
class Myclass {
public:
	Myclass();
	Myclass(int);
	Myclass(int, int);
	Myclass(const char* p);
private:
	int m1, m2, m3;
};
```
Let's assume there is a common code of all these constructors and we want to collect that code in one place as a general programming principle. 

To achieve this we were declaring a private function and the constructors were calling it. (Before C++11)
```cpp
#include <cstdlib>
class Myclass {
public:
	Myclass()
	{
		init(0, 0, 0);
	}
	Myclass(int x)
	{
		init(x, 0, 0);
	}
	Myclass(int a, int b)
	{
		init(a, b, 0);
	}
	Myclass(const char* p)
	{
		auto i = std::atoi(p);
		init(i, i, i);
	}
private:
	void init(int, int, int);
	int m1, m2, m3;
};
```

What is the missing point of this approach?
- The common function doesn't do the initializing job in actuality. The object was already initialized when the control flow entered the common function. Why this is important? If the data members are other class types, this is very important because we could write such a code that first default initializes the class type data members and then assigns a value to them.
- Also, the matter of not actually initializing the data members with that syntax can be an issue with cost or **exception handling**. #Cpp_ExceptionHandling
- The common function is created to be called only by the constructors. We close it to the client codes by making it private. But there is no obstacle to calling the common code by the member functions.

Most of the time the common code of the constructors is the one of the constructor itself. So the other constructor can use the one of the constructor's code. However, the language rules weren't allowing this. The delegating constructor makes it possible.

We use the delegating constructor syntax, that is making a call to another constructor inside a constructor, by writing the constructor call inside the constructor initializer list as follows:
```cpp hl:3
class Myclass {
public:
	Myclass(): Myclass(0, 0) // Delegating constructor
	{
	
	}
	Myclass(int a, int b)
	{
	
	}
private:
	int m1, m2, m3;
};
```

This delegation can be more than one. A constructor that is called by another constructor can call another constructor.

> [!warning]  Warning: We can't initialize the data members anymore at the constructor initializer list of the constructor that delegates if it delegates to another constructor.

```cpp error:ma{10}
class Myclass {
public:
	Myclass(): Myclass(0, 0), ma{10} // Syntax ERROR. The constructor can't initialize the data member because it delegates to another constructor.
	{
	
	}
	Myclass(int a, int b)
	{
	
	}
private:
	int ma;
};
```

> [!warning]  Warning: Not that, a constructor can't be called by its name except the delegating constructor syntax.

# Parentheses and Brace Difference
#Cpp_UniformInitialization 
There are some differences between parentheses and braces after modern C++.
- We can initialize with braces at places where we can initialize with parentheses. This is called brace initialization or uniform initialization.
- There are some places that have differences between initializing with braces and parentheses. For example the vector class.

```cpp
#include <vector>

int main()
{
	std::vector<int> ivec1(10, 5); // Calls the fill constructor
	std::vector<int> ivec2{20, 6}; // Calls the constructor that takes std::initializer_list type as a parameter.
}
```

The constructors that are called in the upper example are different. 
The first constructor that is called with parentheses takes parameter types size_t and int creates the vector with 20 items and fills that with 5. The constructor is called a "**fill constructor**".
The second constructor that is called with braces creates 2 objects that have values of 20 and 5. This constructor takes **std::initializer_list** type which is a class type in the standard library as a parameter.

# Accessing All Other Objects inside An Object
#Cpp_idiom 
A class that all of its objects can communicate with each other. We add a static vector to the class that holds the this pointers of the objects.
```cpp
#include <string>
#include <vector>
#include <iostream>
#include <algorithm>

class Fighter {
public:
	Fighter(std::string name) : m_name(std::move(name))
	{
		svec.push_back(this);
	}
	~Figher()
	{
		if (auto iter = std::find(svec.begin(), svec.end(), this); iter != svec.end())
		{
			svec.erase(iter);
		}
	}
	void print()const;
	std::string get_name()const;

	void ask_for_help()
	{
		std::cout << "Asking for help\n";
		for (auto p :svec )
		{
			if( this != p)
			{
				std::cout << p->m_name << " ";
			}
		}
		std::cout << "\n";
	}
private:
	std::string m_name;
	static inline std::vector<Fighter*> svec;
};

int main()
{
	Fighter f1{"Ftr1"}, f2{"Ftr2"}, f3{"Ftr3"}, f4{"Ftr4"};
	auto f5 = new Fighter{"Ftr5"};

	// ...

	f4.ask_for_help();

	delete f5;
}
```

---
# Terms
- static data member
- inline variables
- zero-sized array extension
- header-only library
- Named Constructor
- factory functions
- delegating constructor
- exception handling
- fill constructor
- std::initializer_list

---
Return: [[00_Course_Files]]