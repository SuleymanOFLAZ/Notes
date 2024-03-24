#Cpp_Course 
[[00_Course_Files]]
# Overloading the Dereferencing "\*" Operator and the Arrow "->" Operator
#Cpp_OperatorOverloading_DereferenceOperator #Cpp_OperatorOverloading_ArrowOperator
The **dereferencing operator** allows us to access an object at an address. Why do we overload the dereferencing operator?  It has to be intuitive. The class has to be a **pointer-like class**. The classes that are used like a pointer. We mentioned about the pointer-like classes. There are two types of pointer-like classes these are iterator classes and smart pointer classes.
- The **iterator classes** hold the location information in the containers. #Cpp_Iterators
- The **smart pointer classes** are used to control the life of the dynamic objects. #Cpp_SmartPointers

Why do we need the classes where the objects of the classes act like pointers when the raw pointers exist?
1. Performing tasks that raw pointers cannot do with function calls.
2. Creating more safe code. For example, throwing an exception can be ensured in case of a wrong situation.

An example task of the raw pointers can't be done. Let's say we have a double-linked list. And we want to access the next element of the linked list with "++ptr" and the previous element of the linked list with "--ptr". Is this possible with the raw pointer? No, it isn't. Because it has to be consecutive.

 The dereferencing operator function can be global. However, the arrow operator function has to be a member function.

"\*ptr" expression is an L-value expression. Therefore the return type of the operator\* function has to be an L-value reference to make it possible. #Cpp_ValueCategory 
## unique_ptr
#Cpp_unique_ptr 
"unique_ptr" type an object is used as a pointer that controls the life of a dynamic object. The difference between the unique_ptr and the raw pointer is it deletes the dynamic object that the life of it is controlled when the life of the unique_ptr ends.

```cpp
#include <iostream>

class Myclass{
public:
	Myclass() = default;
	Myclass(int x) : mval{x} {}
	~Myclass()
	{
		std::cout << "Myclass Destructor releases resources\n";
	}
	friend std::ostream& operator<<(std::ostream& os, const Myclass& m)
	{
		return os << "(" << m.mval << ")";
	}
	void set(int x)
	{
		mval = x;
	}
private:
	int mval{};
};

int main()
{
	{
		Myclass mx{35};
		std::cout << mx << "\n";
		mc.set(111);
		std::cout << mx << "\n";
	} // The destructor for the mx object is called. (For an object that has an automaic lifespan)

	Myclass* pmx = new Myclass{123};
	std::cout << *pmx << "\n";
	pmx->set(111);
	std::cout << *pmx << "\n";

	delete pmx; // We have to delete the dynamic object.
}
```

The dynamic object has to be deleted at the end of its task. We didn't need to make a delete call if the "pmx" was a pointer-like a class object (just like unique_ptr). Because the dynamic object was to be deleted at the end of the pointer-like class object's life (The destructor of the pointer-like object does the deletion). The below code uses a pointer-like class (unique_ptr), therefore we don't need to delete the dynamic object.

The dynamic object is not deleted when the raw pointer life is ended. We have to delete it manually. However, a pointer-like object can be used instead of a raw pointer to gain the feature of deletion of the dynamic object when the pointer-like object's life is ended.

> [!note] Note: These kinds of pointer-like objects are called "**smart pointers**".

```cpp hl:5,11
#include <memory>
int main()
{
	{
		std::unique_ptr<Myclass> pmx(new Myclass{123}); // We use the unique_ptr.
		// std::unique_ptr<Myclass> pmx = new Myclass{123}; // This is a syntax error because the constructor is explicit and the copy-initialization is prohibited.
		
		std::cout << *pmx << "\n";
		pmx->set(111);
		std::cout << *pmx << "\n";
	} // The dynamic object is deleted here by the destructor of the unique_ptr.
	
	std::cout << "In main\n";
}
```

The destructor of the Myclass object is called before the "In main" output. First, the destructor of the unique_ptr object is called because it is an automatic lifespan object. Then the destructor of the unique_ptr deletes the dynamic object (destructor of it is called).

The unique_ptr overloads the dereferencing operator, therefore we could write "\*pmx". And overloads the arrow operator therefore we could write "pmx->set(111)".

> [!note] Note: The unique_ptr class is inside the "memory" header.

**Unique Ownership**
The strategy that the unique_ptr implements is called "**unique ownership**" in programming. Unique ownership means only one pointer points to a dynamic object. A second pointer can't point to the object. The first pointer must break its ownership to be able to point the object with the second pointer. #Cpp_UniqueOwnership #Cpp_unique_ptr 

The unique_ptr provides:
- It controls the life of the dynamic object.
- It protects against mistakes in using more than one pointer.
- It gives assurances about exception handling.
## Arrow Operator Overloading
#Cpp_OperatorOverloading_ArrowOperator 
The arrow operator is overloaded as unary even if it is a binary operator. Therefore, there isn't a parameter of the arrow operator function.

The compiler turns the "ptr->foo"  (ptr is a class object) expression into "ptr.operator->()->foo". So the compiler put the "foo" expression that is inside the "ptr->foo" expression to the right side of the second arrow in the turned expression. That means the arrow operator function isn't related to the right side expression in the expression "ptr->foo"

What should be the return of the arrow operator function for the code generated by the compiler ("ptr.operator->()->foo") to be legal? The left side of the second arrow in the code generated by the compiler has to be a pointer. Therefore the return of the arrow operator function has to be an address.
## Writing A Pointer-Like Object
We will write a pointer-like class. The class is "MyclassPtr" and can be used as a pointer that points to "Myclass" objects.

```cpp
class MyclassPtr {
public:
	MyclassPtr(Myclass*) : mp{p} {} // A constructor that takes "Myclass*" argument.
	~MyclassPtr()
	{
		delete mp;
	}
	Myclass& operator*() // The overload of the dereferencing operator.
	{
		return *mp;
	}
	Myclass* operator->() // The overload of the arrow operator.
	{
		return mp;
	}
private:
	Myclass* mp; // A raw pointer.
};

int main()
{
	{
		MyclassPtr p(new Myclass{12});
		std::cout << *p << "\n";

		p->set(2);
		// p.operator->()->set(2); // Same as the upper line.

		std::cout << *p << "\n";
	}
	std::cout << "In main\n";
}
```

A pointer-like class wraps a raw pointer. It has a raw pointer inside.
## Return Of The Arrow Operator Can Be A Class That Overloads The Arrow Operator
There is a second way to make an expression that is created with the arrow operator legal. For example, "a" is a class object and overloads the arrow operator. The return of the arrow operator function is not an address. Is this can be legal?
In a situation where the return of the arrow operator function is a class object and that class is also overloading the arrow operator, the second arrow operator that is in the expression generated by the compiler as a result of the usage of the arrow operator (with the class which the arrow operator function returns a class) is used to call the arrow operator of the second class that also overloads the arrow operator.

```cpp
class A {
public:
	void func();
};

class B{
public:
	A* operator->(); // Class B is also overloading the arrow operator.
};

class C {
public:
	B operator->(); // The arrow operator function is returning a class type.
};

int main()
{
	C cx;

	cx->func();

	/* The compiler return the "cx->func();" into
		cx.operator->()->func();

		And, this turns into:
		cx.operator->().operator->()->func();
	*/
}
```
# About Templates
#Cpp_Templates
We wrote the "MyclassPtr" class. The class only controls the life of dynamic "Myclass" objects. We have to write separate smart pointer classes for different classes. How can we make a smart pointer class common for all class types?

The language tool that makes it possible is **templates**. We can write a code to make the compiler write a class like "MyclassPtr". The code that makes the compiler write a code. This is called "**meta code**" or "**code formula**".

This approach is called "**generic programming**".

The purpose of the meta code is not to be compiled directly. The purpose of it is make write the code that the compiler will compile. C++ is the most advanced and equipped language for this. These codes are called "**templates**" in C++. 

If the purpose of the code is to make the compiler write a class code, this is called a "**class template**". If it is to make to write a function code, it is called a "**function template**". If it is to make the compiler declare a variable, it is called a "**variable template**". If it is to make the compiler write a type-alias, it is called an "**alias template**".
## Let's Turn Our "MyclassPtr" Class Into A Template
```cpp
template <typename T> // A class template
class SmartPtr {
public:
	SmartPtr(T*) : mp{p} {}
	~SmartPtr()
	{
		delete mp;
	}
	T& operator*() // The overload of the dereferencing operator.
	{
		return *mp;
	}
	T* operator->() // The overload of the arrow operator.
	{
		return mp;
	}
private:
	T* mp; // A raw pointer.
};

int main()
{
	{
		SmartPtr<Myclass> p(new Myclass{12});
		std::cout << *p << "\n";

		p->set(2);
		// p.operator->()->set(2); // Same as the upper line.

		std::cout << *p << "\n";
	}
	std::cout << "In main\n";
}
```
# Function Call Operator
#Cpp_OperatorOverloading_FunctionCallOperator
The **function call operator** is the most overloaded. This is mostly used with the templates.

What does the below line evoke in C (it is not a function declaration)?
```c
func();
```

It evokes:
- The "func" is the name of a function and the line is a function call.
- The "func" is a function pointer.
- The "func" is a functional macro.

```c title:"Function pointer example"
void foo(void)
{
	
}

int main()
{
	void (*func)(void) = &foo;
	func(); // the func is a function pointer.
}
```

```c title:"Functional macro example"
#define func() foo()

void foo(void)
{
	
}

int main()
{
	func(); // the func is a functional macro.
}
```

So, what does the line evoke in C++? #Cpp_C_difference 
- It can be a function
- It can be a function pointer
- It can be a functional macro
- It can be a class object (the function call operator overload)
- It can be a **closure object**. (obtained from a lambda expression) #Cpp_ClosureObject
- std::bind #Cpp_std_bind
- and so on ...

How can the "func();" be a class object? It can be if the function call operator is overloaded.

The "**callable**" (noun) term is frequently used in C++. A callable can be an operand of the function call operator and provide a function call. It can be one of the objects in the upper list. It can be various things, and as a general term it is called "callable". #Cpp_callable

One of the most typical examples and most used callable is the class object. A class object becomes an operand of a function call operator and the compiler turns this expression into a function call. The mechanism that provides this is overloading the function call operator of the class.

- $ Rules for the function call operator
- The function call operator can be overloaded.
- The function call operator cannot be a global operator function. It has to be a member-operator function. (just like the subscript operator and the arrow operator)
- It can be in any parametric structure. So it can take several parameters or none.
- It can take default arguments. (None of the other operator functions can take)
- It can be variadic.
- It can be a const member function or a non-const member function depending on the situation.

```cpp title:"Function call operator overload" hl:3-7,16-19
class Myclass {
public:
	void operator()() // The function call operator function
	{
		std::cout << "Myclass::operator()()\n";
		std::cout << "this = " << this << "\n";
	}
};

int main()
{
	Myclass m;

	std::cout << "&m = " << &m << "\n";
	
	m(); // The function call operator is called.
	/* The compiler turns the expression "m();" into
		m.operator()();
	*/
}
```

``` title:"The Output"
&m = 0097FB34
Myclass::operator()()
this = 0097FB34
```

We will better understand the existing reason for the function call operator overloading when we look at the templates. #Cpp_Templates 

Why do we use it? It is useful with generic programming (with templates).

Without the templates, the function call operator function only provides a syntax variation. Let's look at the usage of it without a template example.

```cpp
using namespace std;

class Random {
public:
	Random(int low, int high) : mlow{low}, mhigh{high} {}

	int operator()()
	{
		return std::rand() % (mhigh - mlow + 1) + mlow;
	}
private:
	int mlow, mhigh;
};

int main()
{
	Random rand1{ 0, 100 };
	Random rand2{ 50, 800 };
	Random rand3{ 20, 25 };

	for(int i = 0; i < 20; ++i)
	{
		cout << rand1() << " "; // The "rand1()" makes a call to the function call operator function and it returns a random value between the mlow and mhigh.
	}
	cout << "\n";

	for(int i = 0; i < 20; ++i)
	{
		cout << rand2() << " ";
	}
	cout << "\n";

	for(int i = 0; i < 20; ++i)
	{
		cout << rand3() << " ";
	}
	cout << "\n";
}
```

The functionality of the function call function could be implemented in a member function in the upper example. Let's say the functionality is implemented in the "rand() member function". We could us the "rand1.rand()" instead of "rand1()". The function call operator function just provides a syntax variation without the templates.

We will look at the templates later, but let's look at the usage of the function call operator function with templates.

```cpp
#include <vector>
#include <string>
#include <iostream>
#include <algorithm> // includes the "count_if()"
#include "nutility.h"

bool is_length_7(const std::string& s)
{
	return s.length() == 7;
}

int main()
{
	using namespace std;

	vector<string> svec;
	rfill(svec, 1000, rname); // Fills the object with 1000 random name

	cout << count_if(svec.begin(), svec.end(), is_length_7) << "\n"; // The count_if returns the total count of the names that have a length of 7.
}
```

Let's say we want to find how many names in the "svec" has a length of 7. We use the "count_if()" function of the standard library algorithm.

Now we want to find the count of the names that have a length of 5. We have to write another function same as the "is_length_7" function that looks for names that have a length of 5. Instead of writing another function, how do we make the function generic?

We create a class. Then we write a function call operator function for the class to use instead of the global function that we wrote ("is_length_7").

```cpp hl:23
class LenPred {
public:
	LenPred(std::size_t len) : mlen{len} {}
	bool operator()(const std::string& s)
	{
		return s.length() == mlen;
	}
private:
	std::size_t mlen;
};

int main()
{
	using namespace std;

	vector<string> svec;
	rfill(svec, 1000, rname); // Fills the object with 1000 random name

	size_t len;
	cout << "Enter the length: ";
	cin >> len;

	cout << count_if(svec.begin(), svec.end(), LenPred{len}) << "\n"; // The count_if returns the total count of the names that have a length of 7.
}
```

We give a temporary object that has the type "LenPred" to the function "count_if()" instead of a function pointer. The function "count_if()" invokes the function call operator function of the object.

> [!note] Note: The two terms are used to state the classes that overload the function call operator.  These terms are "**functor class**" and "**function object**".
# Type-cast Operators
#Cpp_OperatorOverloading_TheCastOperators
If a type conversion doesn't exist in the language rules, if it exists due to a function, if the compiler makes a function call to realize the type conversion, it is called "user-defined conversion". #Cpp_User-definedConversion 

The first method is the conversion constructor to make a user-defined conversion. #Cpp_ConversionConstructor 

```cpp
class Myclass {
public:
	Myclass() = default;
	Myclass(int); // Conversion constructor.
	explicit Myclass(double); // Implicit type conversion is prohibited with the "explicit" keyword. It can be used to type conversion but it has to be explicit. The conversion can be done with the static cast or C-type type conversion.
};
```

There is another way to user-defined conversion which is the "**type conversion operator function**". #Cpp_TypeConversionOperatorFunction

The type conversion operator functions are subjected to special rules and frequently used in the STL.

- $ Rules for the type conversion operator functions
- It can't be a global operator function. It has to be a member-operator function.
- The name convention of it is different from other operator functions. After the "operator" keyword the type that is converted into is written (operator int).
- The target type can be a reference type or a pointer type. It can be another class type.
- The type conversion operator is an unary operator. Therefore the type conversion operator function doesn't take a parameter.
- Because the type conversion operator hasn't a side effect, it is a const member function naturally. This is not mandatory in terms of syntax, however, it is a const member function in terms of semantic, the exceptions to this can exist.
- The return type is not written. This is not the same concept as with the constructor and destructor, they have no return concept. The type used in the type conversion operator function's name is the return type of the function. For example, the return type of the "operator int()const" function is "int".

```cpp
class A{
	// ...
};

class Myclass {
public:
	operator int() const; // Converts the object that is Myclass type into int type.
	operator double() const;
	operator bool() const;
	operator int&() const;
	operator int*() const;
	operator A() const;
};
```

The compiler calls these functions implicitly when a "Myclass" object needs to be converted to a type.

```cpp hl:5-9,16-19 title:"Type conversion operator function"
#include <iostream>

class Myclass {
public:
	operator int() const // Type conversion operator function
	{
		 std::cout << "Myclass::operator int()\n";
		 return 5;
	}
};

int main()
{
	Myclass m;

	int ival = m; // The type conversion operator function is called.
	/* The compiler turns the expression in the upper line into
		int ival = m.operator int();
	*/

	std::cout << ival << "\n"; // prints 5
}
```

Why do we need such a function? For some classes, this can be a wanted and natural feature. For example, in a counter class, If the class had a type conversion operator function that converts into an int, we would use it at places that require an int, and the compiler would call this function by taking charge of the situation.

```cpp
class Counter {
public:
	operator int() const;
};
```

However, implicit type conversion is very dangerous. Type conversion with a type conversion operator instead of implicit conversion is always a better idea, except in some scenarios.

**The "explicit" Keyword**
The "explicit" keyword had been used only for the conversion constructor before the modern C++. The "explicit" keyword can be used for the type conversion operator function since the C++11. We mostly make the type conversion operator explicit. #Cpp11 #Cpp98-03 #Cpp_keyword_explicit #Cpp_ConversionConstructor 

```cpp hl:3 error:ERROR valid:OK title:"explicit type conversion operator function"
class Counter {
public:
	explicit operator int() const; // Explicit type conversion operator function.
};

int main()
{
	Counter c;

	int ival = c; // Syntax ERROR. Because the type conversion operator function is explicit.

	int ival = static_cast<int>(c); // OK. The static-cast type conversion operator is used. It is valid even if the type conversion operator function is explicit.
}
```

The compiler can't make an implicit type conversion if the type conversion operator function is explicit.
We can use a static_cast type conversion operator to make a type conversion.

**Combination of User-defined Conversion and Standard Conversion**
The implicit conversion can be done by the compiler in the following situations:
- a user-defined conversion followed by a standard conversion
- a standard conversion followed by a user-defined conversion

However, a conversion can't be done in the situation of a user-defined conversion followed by a user-defined conversion.

For example, the implicit conversions inside the below example are done by the compiler. This is a very dangerous scenario. The implicit conversion wouldn't be legal if the type conversion constructor was explicit.

```cpp warn:10,11
class Counter {
public:
	operator int() const; // Type conversion operator function.
};

int main()
{
	Counter c;

	double dval = c; // The implicit conversion is done. Because the user-defined conversion is possible from "Counter" to "int". Then the standard conversion is possible from "int" to "double".
	bool flag = c; // The implicit conversion is done. Because the user-defined conversion is possible from "Counter" to "int". Then the standard conversion is possible from "int" to "bool".
}
```

> [!question] Question: What is the situation about the expression of the if statement in the below example? (a C question)
> A: The expression inside the if parentheses is stated to logical interpretation. The "if(x)" is the same as "if(x != 0)". (This is in C)
>  If it was a pointer, the logical interpretation of the pointer object is "false" for the NULL pointer, and addresses that are not NULL pointers are "true" in C. So the "if(ptr)" is the same as "if(ptr != NULL)".

```c
int main()
{
	int x = 10;

	if(x){
		// ...
	}
}
```

> [!warning]  Warning: In C++, where a logical expression is expected, an expression with boolean type is required. Also, there is implicit type conversion to boolean. This is different from C. #Cpp_ImplicitTypeConversions #Cpp_C_difference 

> [!question] Question: What is the situation about the expression of the if statement in the below example? (a C++ question)
> A:  In C++, where a logical expression is expected, an expression with a boolean type is required. Also, there is implicit type conversion to boolean. The compiler converts the x from "int" to "bool". Then the non-zero value will turn into true.
> If it was a pointer, the expression "if(ptr)" is the same as "if(ptr != nullptr)".

```cpp
int main()
{
	int x = 10;

	if(x){ // There is an implicit type conversion form "int" to "bool" in C++.
		// ...
	}
}
```

**What if the expression inside the if parentheses is a smart pointer object?**

```cpp warn:Not
class SmartPtr {

};

int main()
{
	SmartPtr ptr;

	if(ptr) // Not legal. There is no type conversion from the SmartPtr type to the bool type.
	{
	
	}
}
```

This code won't be legal because there is no implicit conversion from the class type to the bool type.

To be able to use the SmartPtr inside the if parentheses just like the normal pointers, the SmartPtr class has to have a type conversion operator function. Therefore, the most overloaded operator is the "operator bool" function.

```cpp 
class SmartPtr {
	explicit operator bool()const;
};

int main()
{
	SmartPtr ptr;

	if(ptr) // OK. The class has a boolean type conversion operator function.
	{
	
	}
}
```

The conclusion, if a class object is needed to be used in a place where a logical interpretation is expected, for example, the following conditions, the class has to have a type conversion operator function that converts the class type into the bool type (this is first choice) or has to have a type conversion operator function that converts the class type into a type that can be convertible to the bool type.
- the object is an operand of the condition operator ( x ? )
- the object is an operand of the logic and operator ( x && y )
- the object is an operand of the logic or operator ( x || y )
- the object is an operand of the logic-not operator ( !x )
- the object is used inside the if parentheses ( if ( x ) )
- the object is used inside the while parentheses ( while( x ) )
- the object is used inside the do-while parentheses ( while( x ) )
- the object is used inside the for parentheses and between the semicolons ( for( ; x ; ) )

> [!note] Note: These kinds of types which can be used in a boolean context are called "**nullable types**" in programming.

> [!warning]  Warning: We should make the boolean type conversion function explicit ( "explicit operator bool()const" )

> [!important] Important: The implicit type conversion is very dangerous. Therefore we should declare the boolean type conversion operator as explicit. If it is declared as explicit, there is no need for a typecast operator to use it in an expression that a logical interpretation is expected, however, the typecast operator is needed when making a specific conversion to other types.

```cpp hl:2 valid:OK error:ERROR title:"Explicit operator bool function"
class SmartPtr {
	explicit operator bool()const;
};

int main()
{
	SmartPtr ptr;

	if(ptr) // OK. The conversion is done even if the type conversion operator is explicit.
	{
	
	}

	int ival;

	ival = ptr; // Syntax ERROR. Because the type conversion operator function is explicit.

	ival = static_cast<bool>(ptr); // OK. A cast operator is used. 
}
```

The following are legal to use with an explicit type conversion operator function because they are places where logical interpretation is expected.

```cpp
class SmartPtr {
	explicit operator bool()const;
};

int main()
{
	SmartPtr ptr;

	if(ptr) // OK. The conversion is done even if the type conversion operator is explicit.
	{
	
	}

	!ptr; // OK
	ptr && ptr; // OK
}
```

If we declare a variable inside the parentheses of the if, the variable that is declared is subjected to logical interpretation. This comes from before Modern C++. This feature does not exist in C. #Cpp98-03 #Cpp_C_difference 

For example:

```cpp valid:OK
int bar();
int* baz();

int main()
{
	if(int x = bar()) { // OK. The "x" is subjected to logical interpretation. (Since C++98)
	
	}

	if(int* p = bar()) { // OK. The "p" is subjected to logical interpretation. (Since C++98)
	
	}
}
```

It is valid with the type conversion operator function. Let's say there is a function that returns the "SmartPtr" type. We can declare a "SmartPtr" object inside the if parentheses and assign the return of the function to it. This is valid because the object inside the if parentheses is subjected to logical interpretation and the type conversion operator function exists.

```cpp valid:OK
class SmartPtr {
	explicit operator bool()const;
};

SmartPtr foo();

int main()
{
	if (auto p = foo()){ // OK.
	
	}
}
```

Some classes have the "operator bool" functions inside the STL. For example,

```cpp
#include <iostream>
#include <memory>
#include <string>

int main()
{
	using namespace std;

	unique_ptr<string> uptr; // In this situation the uptr doesn't control the life of a dynamic object. (an empty unique_ptr)

	if(uptr) { // The "operator bool" function of the unique_ptr class will be called. The "operator bool" function returns "false" because the unique_ptr is empty.
		cout << "Not empty\n";
	}
	else {
		cout << "Empty\n";
	}

	unique_ptr<string> uptr2{ new string{"Hello"} };

	if(uptr2) { // The "operator bool" function of the unique_ptr class will be called. The "operator bool" function returns "true" because the unique_ptr is not empty.
		cout << "Not empty\n";
	}
	else {
		cout << "Empty\n";
	}
}
```

> [!question] Question: Is the following code legal? What is the value of "x"? #Cpp_Interview_Question 
> A: The code is legal because the "operator bool" function is not explicit. Therefore the class objects inside the "m1 + m2" expression are converted into bool type. The compiler turns the expression "m1 + m2" into the expression "m1.operator boo() + m2.operator bool()". The value of "x" is "2" because the "operator bool" function returns 1 for each object. The type of the "x" is deducted as "int" because of the integral promotion. The type of expression that adds two "bool" is "int". #Cpp_IntegralPromotion 
> Note: The syntax in line 16 is valid even if the addition operator function does not exist because the objects are converted into booleans and then added.

```cpp
class Myclass {
public:
	Myclass(int val) : mval{val} {}
	operator bool()const
	{
		return mval != 0;
	}
private:
	int mval;
};

int main()
{
	Myclass m1{7}, m2{13};

	auto x = m1 + m2; // Valid because the "operator bool" function is not explicit. The class objects are turned into booleans by the compiler. The compiler turns the expression "m1 + m2" into the expression "m1.operator bool() + m2.operator bool()". The type of "x" is deducted as int because of the integral promotion.

	std::cout << "x = " << x << "\n"; // The value of "x" is 2 because the "operator bool" function returns 1 for each object.
}
```

> [!question] Question: The following code prints the hex value of the given input and terminates when a non-number character is entered. How does this code work?
> A: The class that "cin" belongs to has an "operator bool" function. There is another operator overloading in the expression inside the while parentheses. The operator ">>" is overloaded. The compiler converts the expression "cin >> x" into the "cin.operator>> (x)". The type of the expression "cin.operator>>(x)" (which is inside the while parentheses) is "istream" because the return of it is "iostream&". However, the compiler looks for if it can convert it into a bool because it was used in a logical context. And, the compiler can convert it into the bool because the class has the "operator bool" function. Therefore the final expression that is converted by the compiler is "cin.operator>>(x).operator bool()". The type of the final expression is the bool.
> The "operator bool" returns true if the stream is in good condition. If the stream is in fail state, the function returns false. Therefore, if the input characters (inside the standard input buffer) are not characters that a decimal number is obtained, cin comes into the failure state, thus the "operator bool" function returns false.

```cpp
#include <iostream>

int main()
{
	using namespace std;

	int x;

	while(cin >> x)
	{
		cout << hex << x << '\n';
	}
}
```

> [!question] Question: Why is line 9 in the below code a syntax error?
> A: Because the "operator bool" function is explicit.

```cpp error:ERROR
#include <iostream>

int main()
{
	using namespace std;

	bool flag;

	flag = cin; // Syntax ERROR because the "operator bool" function is explicit.
}
```

> [!question] Question: Is there a syntax error in the below code?
> A: No, there isn't. Because both the "cin" and "cout" have the "operator bool" function. And they are used inside the logic content.

```cpp
#include <iostream>

int main()
{
	if(cin && cout)
	{
	}
}
```

> [!note] Note: The "operator bool" function was added to the language with C++11. It was "operator void\*" before the C++11. #Cpp11 #Cpp98-03 
# Quick Reminder About The Type of A Name and Type of An Expression

```cpp
int main()
{
	int x = 10;
	int& r =x;
}
```

What is the type of "r" in the upper example?
A: This means the type of name that is introduced as "r". It means the type of entity a noun refers to. The type of "r" is "int&".  The decltype does this job. If we write "decltype(r)" the result of it will be "int&". #Cpp_decltype 

"r" is an expression also and each expression has a type. What is the type of the expression "r"?
A: There are two important qualifying of an expression. These are "data type" and "value category".

What is the data type of the expression "r"?
A: The data type of the expression "r" is "int" (not "int&"). A type of expression can be a reference type in C++.

What is the value category of the expression "r"? #Cpp_ValueCategory 
A: L-value.

```cpp
int main()
{
	int x = 10;
	int&& r = x+5;
}
```

What is the type of "r" in the upper example?
A: "int&&"

What is the data type of the expression "r"?
A: "int"

What is the value category of the expression "r"?
A: "L-value."

---
# Terms
- unique ownership
- unique_ptr
- smart pointers
- dereferencing operator
- smart pointer classes
- iterator classes
- pointer-like class
- templates
- meta code
- code formula
- generic programming
- function call operator
- closure object
- callable
- functor class
- function object (class)
- type conversion operator function
- nullable type
---
Return: [[00_Course_Files]]