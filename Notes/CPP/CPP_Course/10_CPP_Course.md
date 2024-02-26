[[00_Course_Files]]
#Cpp_Course 


```cpp
class Myclass{
public:
	Myclass(const Myclass&); // Copy Constructor
	Myclass(Myclass&&); // Move Constructor

	Myclass& operator=(const Myclass&); // Copy assignment
	Myclass& operator=(Myclass&&); // Move assignment
};
```

The move-semantic comes into play in the below code.
```cpp
class Myclass{
public:
	//...
};

Myclass foo();

int main()
{
	Myclass mx;

	mx = foo(); // Move semantic, because the "foo()" is a PR value expression.
}
```

Before modern C++, We were hesitating to make a function return type a class type. Because there was a copying cost. But now, no need to worry about it. Because the move-semantic will come into play. #Cpp11 
```cpp
// Modern C++ syntax (with the move semantic)
std::vector<int> foo();

int main()
{
	std::vector<int> ivec;

	ivec = foo();
}

// Old C++ syntax (without the move-semantic). We were using an out-parameter
void foo(std::vector<int> &);
```

# When does the compiler write the special member functions?
#Cpp_SpecialMemberFunctions 

## States of a Special Member Function 

![[001_statesOfSMF|900]]

## Defaulted Table

![[002_tableSMF|900]]

> [!note] Note: Deprecated
> Deprecated means the standard warns us to not use the feature. May be removed from the future standard. However, it is still valid. We shouldn't use a deprecated feature.

If we declare one of the below functions, the move functions of the class are not written by the compiler (not declared). We should write the move functions if we want them in this case, or we make it written by the compiler by using the default syntax.
- destructor
- copy constructor
- copy assignment

If we declare one of the below functions, the other ones become deprecated. So, we have to be careful about it.
- destructor
- copy constructor
- copy assignment

If we declare one of the move members, the compiler deletes the copy members.

If we don't declare the destructor, the compiler defaults the destructor in any case.

The compiler writes the special member functions as follows:
```cpp
class Myclass{
public:
	Myclass(); // Default init members
	Myclass(const Myclass& other) : tx(other.tx), uy(other.uy) {}

	Myclass(Myclass&& other) : tx(std::move(other.tx)), uy(std::move(other.uy)) {}

	Myclass& operator=(const Myclass& other)
	{
	tx = other.tx;
	uy = other.uy;
	return *this;
	}

	Myclass& operator=(Myclass&& other)
	{
		tx = std::move(other.tx);
		uy = std::move(other.uy);
		return *this;
	}

private:
	T tx;
	U uy;
};
```

# Move-only Classes
Move-only means there is the move-semantic but there is no copy-semantic. For example, the "unique_ptr" class.
```cpp
#include <memory>

int main()
{
	using namespace std;

	unique_ptr<int> uptr{new int};

	auto upx = uptr; // Syntax ERROR. Because the "unique_ptr" is a move-only class. The compiler will say the copy constructor is deleted.

	auto upy = move(uptr); // OK. The move-semantic is used.
}
```

**How can we define a move-only class?**
```cpp
class Myclass { // Move-only class
public:
	Myclass();

	Myclass(Myclass&&);
	Myclass& Myclass(Myclass&&);
};
```
The upper syntax is enough to declare a move-only class because if we declare a move function the copy functions will be deleted.

But the programmers write the delete code too to make it clear for readers.
```cpp
class Myclass { // Move-only class
public:
	Myclass();
	Myclass(const Myclass&) = delete;
	Myclass& operator=(const Myclass&) = delete;

	Myclass(Myclass&&);
	Myclass& operator=(Myclass&&);
};

```

# Non-copyable and Non-moveable Classes
If we want to declare a non-copyable and non-moveable class, deleting the copy functions is enough. The move members will be not declared because of the user declared copy members.
```cpp
class Myclass{
public:
	Myclass();

	Myclass(const Myclass&) = delete;
	Myclass& operator=(cosnt Myclass&) = delete;
};
```

# Copy-only Classes
If we want to declare a copy-only class, declaring the copy members and not declaring the move members i enough. If we create a situation that calls move members, the "fallback" will happen and the copy members will be called instead.
```cpp
class Myclass{
public:
	Myclass();

	Myclass(const Myclass&);
	Myclass& operator=(cosnt Myclass&);
};
```

> [!warning]  Warning: Never delete the move members
> Because in cases where the move is needed, the move members will be called and this will be ended with syntax error. Because the move members were deleted. So, the copy members won't be called instead of the move members. This is not a case that we want. If we want a copy-only class, declaring the copy members and not declaring the move members is the solution. Don't delete the move members.

> [!important] Important: Developer of the move-semantic: Howard Hinnant
> A slide show: Everything You Ever Wanted To Know About Move Semantics

> [!warning]  Warning: Defaulted move members defined as delete, actually behave as not declared.

# Conversion Constructor
#Cpp_ConversionConstructor #Cpp_FunctionOverloadResolution 
Constructors that take one argument are called "**conversion constructors**". Because it provides to make the compiler implicit type conversion. For example, the below code provides the conversion from "int" to "Myclass".
```cpp
class Myclass {
public:
	Myclass() = default;
	Myclass(int); // Conversion constructor
};

int main()
{
	Myclass x;

	x = 5; // OK since a conversion constructor that makes the conversion from "int" to "Myclass" possible has existed.
	/*
	Myclass temp(5);
	x = temp;
	temp.~Myclass();
	*/
 }
```
The compiler first created a temporary "Myclass" object using the "5" (the constructor that takes the int parameter is called to create the temporary object). Then it assigned the temporary object to the "x". The assigning is done with the move constructor if it exists, otherwise with the copy constructor.

> [!note] Note: Temporary Object
> The objects that do not have a name in the source code but exist in the running code are called "**temporary objects**".

```cpp
#include <iostream>

class Myclass{
public:
	Myclass()
	{
		std::cout << "myclass default constructor this: " << this << "\n";
	}
	~Myclass()
	{
		std::cout << "myclass destructor this: " << this << "\n";
	}
	Myclass(int x)
	{
		std::cout << "Myclass(int x) x = " << x << " this: " << this << "\n";
	}
	Myclass& operator=(const Myclass& other)
	{
		std::cout << "myclass copy assignment this: " << this << " &other: " << &other << "\n";
		return *this;
	}
};

int main()
{
	Myclass x;

	x = 5;
 }
```

Output of upper code: #Cpp_TemporaryObject 
```
myclass default constructor this: 00EFFB47
Myclass(int x) x = 5 this: 00EFFB46
myclass copy assignment this: 00EFFB47 &other: 00EFFB46
myclass destructor this: 00EFFB46
myclass destructor this: 00EFFB47
```

In conclusion, a constructor that takes one parameter of type "T" provides implicit type conversion from the type of "T" to the class type in addition to its actual job.

The following syntaxes are valid with the conversion constructor.
```cpp
class Myclass {
public:
	Myclass() = default;
	Myclass(int); // Conversion constructor
};

void func(Myclass m);

void foo(const Myclass& m);

Myclass bar()
{

	return 10; // OK
}

int main()
{
	Myclass x(10); // OK
	Myclass y = 10; // OK

	func(10); // OK
	foo(10); // OK
 }
```

# Explicit Constructor
#Cpp_ExplicitConstructor #Cpp_keyword_explicit
Is this implicit conversion a good thing?
Converting a value that is not a class type to a class type can be a wanted feature in some situations. For example, consider a counter class that will serve as a counter. If we write a constructor like "Counter(int);" that means an "int" value can be converted into a "Counter" type. For example, we can pass an "int" value to a function like "void func(Counter x);"
```cpp
class Counter{
public:
	Counter(int);
};

void func(Counter x); // an int value can be passed to the function

int main()
{
	  
}
```

However, the done of an implicit conversion can cause a lot of problems. For example, we assign a variable to the Counter object by mistake. This type of mistake is hard to determine. It can escape tests. If the conversion constructor does not exist that will be a syntax error, but now the compiler will make the conversion.
```cpp
int main()
{
	Counter c(0);
	int i = 1022;

	c = i;
}
```

In conclusion, type conversions pose serious risks if it was done out of our knowledge. Thus, the implicit conversion done by the conversion constructor is not wanted mostly. We want to make the conversion explicitly when it is needed with the type conversion operator. The tool that provides this is the "**explicit constructor**".

The "**explicit**" is a keyword that qualifies a constructor. The keyword can be used only at the declaration of the constructor. If we use it in the definition of the constructor that would be a syntax error.
```cpp
class Counter {
public:
	explicit Counter(int);
};
```

```cpp
explicit Counter::Counter(int) // Syntax ERROR. The explicit keyword can't used in the definition
{

}
```

An explicit constructor only allows the explicit conversions. It doesn't allow the implicit conversions.
```cpp
class Counter{
public:
	explicit Counter(int);
};

int main()
{
	Counter c(0);

	c = 45; // Syntax ERROR. Because the conversion constructor is explicit.
}
```

We can use type conversion operators to make the syntax legal. Or we can use other syntaxes:
```cpp
int main()
{
	Counter c(0);

	c = static_cast<Counter>(45); // OK
	c = (Counter)(45); // OK
	c = Counter{10}; // OK
}
```

> [!important] Important: Advice for conversion constructors
> Make the constructor that takes one parameter explicit unless there is a compelling reason to the contrary. The constructors that take one parameter inside the classes of the standard libraries are mostly explicit.

Explicit constructor examples inside the standard library:
```cpp
#include <memory>
#include <vector>

using namespace std;

int main()
{
	unique_ptr<int> uptr(new int); // unique_ptr has a constructor that takes "int*". So we can initialize it like that.
	uptr = new int; // Syntax ERROR. Because the constructor that takes "int *" is explicit. There is no implicit conversion from "int*" to "unique_ptr".

	vector<int> ivec(100);
	ivec = 100; // Syntax ERROR. Because the constructor is explicit.
}
```

## Explicit and Initialization
#Cpp_Initialize 
We were initializing a class object with the following syntaxes.
```cpp
class Myclass{
public:
	Myclass(int);
};

int main()
{
	Myclass m1(10); // Direct initializer
	Myclass m2{10};  // Brace list initializer
	Myclass m3 = 10; // Copy initializer
}
```

However, if the constructor is explicit, we cannot use the copy initialization.
```cpp
class Myclass{
public:
	explicit Myclass(int);
};

int main()
{
	Myclass m1(10); // OK
	Myclass m2{10};  // OK
	Myclass m3 = 10; // Syntax ERROR, Because the constructor is explicit.
}
```

The following syntaxes are invalid with the explicit conversion constructor.
The following syntaxes are valid with the conversion constructor.
```cpp
class Myclass {
public:
	Myclass() = default;
	explicit Myclass(int); // Explicit conversion constructor
};

void func(Myclass m);

void foo(const Myclass& m);

Myclass bar()
{

	return 10; // Syntax ERROR, Because the constructor is explicit.
}

int main()
{
	Myclass x(10); // OK
	Myclass y = 10; // Syntax ERROR, Because the constructor is explicit.

	func(10); // Syntax ERROR, Because the constructor is explicit.
	foo(10); // Syntax ERROR, Because the constructor is explicit.
 }
```

We can use the explicit keyword with the default constructor and the constructor that takes more than one parameter (the constructors that are not considered conversion constructors). The rule of can't initialize the explicit constructor with copy initialization is always valid.
```cpp
// non-explicit example
class Myclass {
public:
	Myclass();
	Myclass(int, int);
};

int main()
{
	Myclass x = {}; // OK
	Myclass y = {10, 12}; // OK
}

// explicit example
class Myclass {
public:
	explicit Myclass();
	explicit Myclass(int, int);
};

int main()
{
	Myclass x = {}; // Syntax ERROR, Because the constructor is explicit.
	Myclass y = {10, 12}; // Syntax ERROR, Because the constructor is explicit.
}
```

> [!question] Question:  Is there an error in the below code?
> A: Yes, because of the ambiguity. The explicit constructor isn't included in the function overload resolution. The reason for the error is not related to the explicit constructor. The ambiguity between the constructor that takes a "double" parameter and the constructor that takes a "long" parameter causes error.
> #Cpp_Interview_Question #Cpp_FunctionOverloadResolution 

```cpp
class Myclass{
public:
	explicit Myclass(int);
	Myclass(double);
	Myclass(long);
};

int main()
{
	Myclass x = 14; // Syntax ERROR.
}
```


> [!question] Question:  Is there an error in the below code?
> A: No. The explicit constructor isn't included in the function overload resolution. The constructor that takes a "double" parameter is called due to implicit type conversion from "int" to "double".
> #Cpp_Interview_Question 

```cpp
class Myclass{
public:
	explicit Myclass(int);
	Myclass(double);
};

int main()
{
	Myclass x = 14; // OK
}
```

> [!warning]  Warning: An explicit constructor isn't included in the function overload set. When an object is created with the copy initialization syntax, the explicit constructor isn't included in the function overload set. #Cpp_FunctionOverloadResolution 


> [!question] Question:  Is there an error in the below code?
> A: No. Because there is implicit type conversion from "double" to "int" and "int" to "Myclass".
> #Cpp_Interview_Question 

```cpp
class Myclass{
public:
	Myclass(int);
};

int main()
{
	Myclass x = 3.4; // OK
}
```

# User-defined Conversions
#Cpp_User-definedConversion #Cpp_FunctionOverloadResolution 
If a conversion is a syntax error, however the conversion becomes legal due to a function that we define, and the compiler makes the conversion by making a call to the function that we defined, these types of conversions are called "**user-defined conversions**".
```cpp
class Myclass{
public:
	Myclass(int);
};

int main()
{
	Myclass mx(12);
	mx = 20; // User-defined conversion
}
```

> [!note] Note: Standard Conversion
> If a conversion is already legal due to the language rules, these types of conversions are called "standard conversions".

## User-defined Conversion Rules
Whether a conversion is done or not is subject to the rules mentioned below:
- If the conversion can done with one of the below sequences, the conversion is done (implicit conversion).
	- First the standard conversion, then the user-defined conversion
	- First the user-defined conversion, then the standard conversion
- However, if the conversion needs more than one user-defined conversion, the conversion isn't done. Note that, we can do it using type cast.

```cpp
class Myclass{
public:
	Myclass(int);
};

int main()
{
	Myclass mx(12);
	mx = 4.5; // OK. "double" -> "int" : standard conversion. "int" -> "Myclass" : user-defined conversion. So we passed the value of 4 (not 4.5) to the constructor.
}
```

> [!warning]  Warning: The conversions that are the combination of standard conversion and user-defined conversion are very dangerous. Thus, we should consider making the conversion constructors explicit. For example, consider the below example.

```cpp
class Myclass{
public:
	Myclass(){}
	Myclass(bool);
};

int main()
{
	Myclass mx;
	int ival;

	mx = &ival; // Legal but probably not ok.
}
```

```cpp
class A{
public:

};

class B{
public:
	B(){}
	B(A);

};

class C{
public:
	C(){}
	C(B);

};

int main()
{
	A ax;
	C cx;

	cx = ax; // Syntax ERROR. Because two user-defined conversions are needed.
	cx = static_cast<B>(ax); // OK.
	cx = static_cast<C>(static_cast<B>(ax)); // OK. This is even valid if the "C(B)" conversion constructor is explicit.
}
```

> [!note] Note: A conversion like user-defined + standard + user-defined is done implicitly.
# Temporary Object
#Cpp_TemporaryObject
The objects that do not have a name in the source code but exist in the running code, and end their life depending on certain rules are called "**temporary objects**".
```cpp
class Myclass {
public:
	Myclass(){}
	Myclass(int);
};

int main()
{
	Myclass m;

	m = 20; // There is a temporary object here.
}
```

Temporary objects are subjected to these rules:
- Their life ends when the execution of the expression that includes the object is ended.
- If we bind a temporary object to a reference, the life of the temporary object lasts for the scope of the reference. This is called "**lifetime extension**" or "**life extension**".

## Lifetime Extension
> [!note] Note: **Lifetime Extension**
> If we bind a temporary object to a reference, the life of the temporary object lasts for the scope of the reference. This is called "lifetime extension"

```cpp
class Myclass {
public:
	Myclass(){}
	Myclass(int);
};

int main()
{
	const Myclass& r = 12; // Lifetime extension
}
```

```cpp
class Myclass {
public:
	Myclass(){}
	Myclass(int);
};

int main()
{
	{
		const Myclass& r = 12; // Lifetime extension
		// ...
	} // The life of the temporary that is bound to the reference object is ended here (end of the scope of the reference).
}
```

When is a temporary object created:
- The compiler can create a temporary object depending on the situation.
- We can intentionally create a temporary object with a certain syntax

How we can create a temporary object?
```cpp
int main()
{
	Myclass(12); // Temporary object
	Myclass{12}; // Temporary object
}
```

What is the benefit of creating a temporary object?
- If an object will be used one time, it is not meaningful to insert a name to the name list or create scope leakage.

The expressions that create temporary objects are PR values. Thus, we cannot bind them to an L-value reference. However, It can be bound to a const L-value reference or an R-value reference. #Cpp_ValueCategory 
```cpp
class Myclass {
public:
	//...
};

int main()
{
	Myclass& r = Myclass{}; // Syntax ERROR. Because a PR-value reference cannot bind to an L-value reference.
	const Myclass& rr = Myclass{}; // OK and this is a life extension
	Myclass&& rrr = Myclass{}; // OK and this is a life extension
}
```

Making a call to a function with a temporary object is explained in the below code.
```cpp
void func(Myclass&);
void foo(const Myclass&);
void bar(Myclass&&);

int main()
{
	func(Myclass{}); // Syntax ERROR.
	foo(Myclass{}); // OK.
	bar(Myclass{}); // OK.
}
```

For a better understanding of the temporary objects, let's consider what would happen if the concept of the temporary object didn't exist. The below code represents creating an object instead of using a temporary one considering the object won't be used after the function call.
```cpp
#include <string>

using namespace std;

void func(const std::string& s);

int main()
{
	///
	string str{"Hello"};

	func(str);

	/*
	We could write this: func(string{"Hello"});
	*./
}
```
1.  The reader would have an expectation that the object will be used after the function call.
2. There would be a scope leakage. If we write the name of the object by mistake after the function call, the object will be found as a result of the name lookup.
3. The resources of the object last for the end of the scope of the object. We use the resources in vain.

# Copy Elision
#Cpp_CopyElision 
**Copy elision** is a name given to an optimization technique that is done by the compiler. The compiler generates code without the copy operation at certain places, by determining an optimization can be done, in places where copy operation is done. Copy elision is a very effective optimization.

There are three well-known copy-elision techniques that are independent of the programming languages:

1. The parameter of the function is of a class type (not reference or pointer) and the function is called with an R-value value class object.
```cpp
class Myclass {
public:
	Myclass()
	{
		std::cout << "default ctor\n";
	}
	Myclass(const Myclass&)
	{
		std::cout << "copy ctor\n";
	}
	Myclass(Myclass&&)
	{
		std::cout << "move ctor\n";
	}
};

void foo(Myclass)
{

}

int main()
{
	foo(Myclass{}); // Only the default constructor will be called due to copy elision.
}
```
The default constructor will be called for the temporary object. Then, the move constructor will be called if it exists, otherwise, the copy constructor will be called, because we pass the object to the function.

However, only the default constructor will be called because of the copy elision. This is an optimization. However, this is also added to the language rules with C++17, thus it is a rule (not an optimization) now. The rule added with C++17 is called "**mandatory copy elision**". #Cpp17 #Cpp_MandatoryCopyElision

The following syntax is an error before C++17. We delete the copy constructor and pass an object as a parameter. However, it is not an error with C++17, because it is guaranteed that the copy or move constructor won't be called.
```cpp
class Myclass {
public:
	Myclass()
	{
		std::cout << "default ctor\n";
	}
	Myclass(const Myclass&) = delete;

};

void foo(Myclass)
{

}

int main()
{
	foo(Myclass{}); // Copy Elision. Syntax error before C++17, but ok after c++17 because the copy ctor is deleted.
}
```

> [!note] Note: We did not prefer to use struct as a function argument in C (we were using a pointer). Because this was a costly process, but in this case, this is not a bad thing. In fact, we prefer this in many cases.

So, there are three possibilities if we write a function that takes a class as a parameter. These are represented in below example:
```cpp
class Myclass {
public:
	Myclass() {}
	Myclass(const Myclass&) {}
	Myclass(Myclass&&) {}
};

void func(Myclass val)
{
	Myclass x = std::move(val);
}

int main()
{
	Myclass x;
	
	func(x); // The copy constructor will be called.
	func(std::move(x)); // The move constructor will be called.
	func(Myclass{}); // Only the default constructor will be called one time. Because of the copy elision 
}
```
The copy constructor can be called. This is the worst possibility because of the copying cost.
The move constructor can be called.
The move or copy constructors won't be called (mandatory copy elision) because we used a temporary object.

Should we use "T" instead of "const T&" as a parameter by trusting the mandatory copy elision? Mostly yes. In many cases, the "T" is preferred because of the move-semantic and the copy elision. #Cpp17

2. The return value of the function is a class type and the return expression is a PR-value.
```cpp
class Myclass {
public:
	Myclass()
	{
		std::cout << "default ctor\n";
	}
	Myclass(const Myclass&) = delete;

};

Myclass foo()
{
	// code

	return Myclass{};
}

int main()
{
	foo(); // RVO. Syntax error before C++17, but ok after c++17.
	Myclass m = foo(); // RVO. Syntax error before C++17, but ok after c++17 because the copy ctor is deleted.
}
```
Only the default constructor will be called. This is called "**return value optimization - (RVO)**" in general. This is become mandatory with C++17. #Cpp17 #Cpp_ReturnValueOptimization

3. "**Named Return Value Optimization - NRVO**" is not mandatory, so if we delete the copy constructor, this will be a syntax error. The compilers make NRVO as long as there are no obstacles. We will look at these obstacles later. #Cpp_NRVO 
```cpp
class Myclass {
public:
	Myclass()
	{
		std::cout << "default ctor\n";
	}
	Myclass(const Myclass&) = delete;

};

Myclass foo()
{
	Myclass m;
	/// Code

	return m;
}

int main()
{
	Myclass c = foo(); // NRVO
}
```
We create a local object and use it. Then we return the object. Normally, the default constructor and the copy constructor would be called. However, only the default constructor will be called due to NRVO. Note that, the existence of the copy constructor is mandatory (shouldn't be deleted) here because this is an optimization and does not exist in standards.
Note that, the move constructor will be called instead of the copy constructor in the normal case in the upper example if the move constructor exists. So, it wouldn't be a syntax error if we delete the copy constructor in case the move constructor exists. But if the syntax requires the copy constructor (not the move constructor) and we delete it, it will be the syntax error.

In conclusion, the NRVO depends on the compiler knowing the local variable address and creating the object directly at the address where the object is copied.

Some Cases that NRVO can't Be Done
- If a parameter of the function is used as the return, the NRVO can't be done. Because the same area can't be used for the parameter and the return. These are two different areas that are certain at before.
```cpp
Myclass func(Myclass x)
{
	// Code
	
	return x; // The NRVO can't be done.
}
```
- If the object has a static life span, the NRVO can't done. Because a static object already has an area, it can't be the same as the object that the return is assigned.
```cpp
Myclass func()
{
	static Myclass x;
	// Code
	
	return x; // The NRVO can't be done.
}
```
- In case of multiple return statements, the NRVO can't done.
```cpp
Myclass func()
{
	Myclass x;

	if (expr1)
		return Myclass{};
	if (expr2)
		return x;
}
```

## Temporary Materialization
#Cpp_TemporaryMaterialization
The standards define a situation named "temporary materialization". The compiler generates code to create a class object based on an expression in the form of a PR value expression. When does this occur? If we bind an R-value expression to an R-value reference.
```cpp
Myclass&& r = Myclass{}; // Temporary materialization
const Myclass& r = Myclass{}; // Temporary materialization
```
Actually, the standards don't consider a PR-value expression as a temporary object. PR-value expression is an expression that can provide the creation of an object. But it is not the object itself with the C++17 standards. However, the compiler generates a code that creates an object that makes the PR-value expression an object in certain situations depending on the temporary materialization rules. #Cpp17

C++17 standards say a PR-value expression turns into an object only in case of temporary materialization. So a temporary object isn't created with the initialization syntax. The "mandatory copy elision" is a wrong name because there is no temporary object (temporary materialization) here. #Cpp17

```cpp
Myclass x = Myclass{}; // No temporary materialization
```
Normally we expect that the default constructor will be called for the temporary object then the copy constructor called for the x. However regarding the C++17 standards, although we use the "mandatory copy elision" term, there is no copy operation here because there is no object here. The R-value expression isn't done as a temporary materialization because it is used to initialize an object. Thus the variable x is directly become to the life with the default constructor. This is not an optimization, a rule of the language. This is valid even in the below code.
```cpp
Myclass x = Myclass{ Myclass{ Myclass{} } }; // No temporary materialization
```
Only one default constructor is called for upper syntax because there is only one object that is become to the life. Or this is valid in the below code too.
```cpp
Myclass foo()
{
	return Myclass{};
}

int main()
{
	Myclass x = Myclass{ Myclass{ foo() } }; // No temporary materialization
}
```

In conclusion, regarding the C++17 standard, a PR-value expression is not a class object itself, it is like information that is used to bring a class object to life. However, a PR-value expression is used to create an object by the compiler in some cases depending on the temporary materialization rule. One situation is binding the PR-value expression to a reference (an R-value reference or a const L-value reference). Also, there are more situations but we will mention these later.
# Small Buffer Optimization - SBO
#Cpp_SmallBufferOptimization
Does the string class make allocation at the heap? 

There are implementations by the compilers about the string class because the string class is widely used. Dynamic memory management is very costly. 

There are three-pointer inside the string class to keep the addresses of the area that is allocated at the heap. A size more than the string size is allocated at the heap. Because we can add an element to the end of the string at constant time. One of the pointers keeps the start address of the string and the other keeps the address of the end of the string. The last one keeps the end address of the allocated area.

Lots of the strings are small. Thus, the string class has a buffer and uses it in case the string is small to escape the dynamic allocation. The size of the buffer depends on the implementation. This kind of optimization is called "**small buffer optimization**" but if it is done with the string class it is called "**string buffer optimization**".

A representation of the string class:
```cpp
class String {

private:
	char* ps;
	char* pe;
	char* pa;
	char buufer[32]; // To make SBO
};
```

There is no standard for this but all compilers implement this.

So, the answer to the question that has been asked at the beginning of the topic: depends on the optimization done by the compiler and the size of the string. It keeps the string at the buffer if the size of the string is small and makes allocation on the heap if the size of the string is large.

We can understand if an allocation is made on the heap with the following code:
```cpp
#include <string>

void* operator new(std::size_t sz)
{
	std::cout << "operator new called for the size of : " << sz << "\n";

	if(sz == 0)
		++sz;
		
	if (void* ptr = std::malloc(sz))
		return ptr;

	throw std::bad_alloc{};
}

void operator delete(void* ptr) noexcept
{
	std::cout << "operator delete called for the address of : " << ptr << "\n";
	std::free(ptr);
}

int main()
{
	string s("Hello"); // The function is not called
	string s("Today is a good day, tomorrow will be rainy."); // The function is called. (the size depends on implementation)
}
```

# Object Swap Example
The "Biggie" is a big (as size) class and uses the move-semantic. So, there are significant differences between copying and moving the biggie object.

```cpp
class Biggie {
public:
	//
};

void bswap(Biggie& b1, Biggie& b2)
{
	Biggie temp = std::move(b1); // move constructor
	b1 = std::(b2); // move assignment 
	b2 = std::(temp); // move assignment
}

/* DON'T DO THIS, (very costly)

void bswap(Biggie& b1, Biggie& b2)
{
	Biggie temp = b1; // copy constructor
	b1 = b2; // copy assignment
	b2 = temp; // copy assignment 
}
*/

int main()
{
	Biggie x, y;

	bswap(x, y);
}
```

Consider we have a function to swap two Biggie objects. We have to use the "move" function inside the swap function to make the swap operation efficient. When we use the "move" on an abject, the object becomes moved-from stare. The upper example is valid because the moved-from state object is valid.

No need to write a function like this. Because "swap" is a function template in the STL.
```cpp
std::swap(x, y);
```

---
# Terms
- deprecated
- move-only class
- conversion constructor
- temporary objects
- explicit keyword
- explicit constructor
- user-defined conversion
- standard conversion
- temporary object
- lifetime extension
- copy elision
- mandatory copy elision
- small buffer optimization - SBO
- string buffer optimization
- return value optimization - RVO

---
Return: [[00_Course_Files]]