[[00_Course_Files]]
#Cpp_Course 

# When are The Constructor and The Destructor Called

**Global Object**
 Global objects are created before the 'main'.  And, global objects are created so that they are declared in the file. That's guaranteed by the language. The destructor will be called last for the object which its constructor called first.
 ```cpp
class Myclass{
public:
	Myclass() // Constructor
	{
		std::cout << "Myclass ctor\n";
	} 
	~Myclass(); // Destructor
	{
		std::cout << "Myclass dtor\n";
	}
};

class Data{
public:
	Data() // Constructor
	{
		std::cout << "Data ctor\n";
	} 
	~Data(); // Destructor
	{
		std::cout << "Data dtor\n";
	}
};

Myclass m;
Data d;

int main()
{
	std::cout << "main\n";
}

-------- Output:

Myclass ctor
Data ctor
main
Data dtor
Myclass dtor
```

> [!warning]  Warning: Static Initialization Problem
In a case, where we declare global objects inside different source files, it is not certain which global object was created first. It will be an undefined behavior if being life of a global object depends upon another global object in another source file. Because there is no guarantee the needed object for creating another one is created before the object. The constructor of the needed object hasn't been called yet when the object is created. We have be consider this when writing code.
#Cpp_StaticInitializationProblem

**Static Local Object**
A static local object is created when a call is made to the function. So, the constructor is called when the function call is made. The destructor is called after the end of the main.
```cpp
class Data{
public:
	Data() // Constructor
	{
		std::cout << "Data ctor\n";
	} 
	~Data(); // Destructor
	{
		std::cout << "Data dtor\n";
	}
};

void func()
{
	static int count = 0;
	std::cout << ++count << "\n";
	static Data d; // The object is created one time when a call to 'func' is made.
}

int main()
{
	std::cout << "Start of main\n";
	func();
	func();
	func();
	std::cout << "End of main\n";
}

-------- Output:

Start of main
1
Data ctor
2
3
End of main
Data dtor
```

**Local Objects**
The constructor is called when a call to the function is made, and the destructor is called at the end of the function.
```cpp
class Data{
public:
	Data() // Constructor
	{
		std::cout << "Data ctor\n";
	} 
	~Data(); // Destructor
	{
		std::cout << "Data dtor\n";
	}
};

void func()
{
	Data d; // The object is created one time when a call to 'func' is made.
}

int main()
{
	std::cout << "Start of main\n";
	func();
	func();
	func();
	std::cout << "Start of main\n";
}

-------- Output:
Start of main
Data ctor
Data dtor
Data ctor
Data dtor
Data ctor
Data dtor
Start of main
```
This is the same in the case of loops:
```cpp
for(int i=0; i<10; ++i)
{
	Data d; // Constructor and destructor called 10 times.
}
```

> [!question] Question: Print 0 to 100 without using a loop.
> A: It can be done with the help of an array and a constructor.
> #Cpp_Interview_Question 

```cpp
class Data{
public:
	Data() // Constructor
	{
		static int x = 0;
		std::cout << x++ <<"\n";
	} 
};

int main()
{
	Data a[100];
}
```

**What kind of call is made to call the default constructor?**
Objects that have automatic life span (local variables) are initialized with garbage value when they are initialized with default initialization. However, objects that have a static life span are initialized with zero initialization before the default initialization.
```cpp
int x; // Initialized with zero initialization.
int main()
{
	int x; // Initialized with a garbage value. (Default initialization)
}
```

However, the default constructor is called when we initialize a class object with default initialization
And, the default constructor is called when we initialize a class object with value initialization.
```cpp
class Data{
public:
	Data(); // Constructor
};

int main()
{
	Data d;    // Default initialization. The destructor is called.
	Data dd{}; // Value initialization. The destructor is called.
}
```

Can we say initialization of a class object with default initialization and value initialization are the same because both of them conclude with a call to the destructor? No, there are differences between them. We will look at it later.

> [!warning]  Warning: We cannot use direct initialization for calling the default constructor.
> Because of the 'most vexing parse', The syntax is the same as the function declaration, and the function declaration is chosen due to the language rules.

```cpp
class Data{
public:
	Data(); // Constructor
};

int main()
{
	Data d(); // This is not the declaration of a class object. This is a function declaration.
}
```

But, we can use direct initialization to initialize a class object if the constructor is overloaded.
```cpp
class Data{
public:
	Data(); // Constructor
	Data(int x);
};

int main()
{
	Data d(10); // Direct initialization. 'Data(int x)' constructor is called.
}
```

We can use copy initialization to initialize a class object.
```cpp
class Data{
public:
	Data(); // Constructor
	Data(int x);
};

int main()
{
	Data d = 7; // Copy initialization. 'Data(int x)' constructor is called.
}
```

We can use uniform/brace initialization to initialize a class object.
```cpp
class Data{
public:
	Data(); // Constructor
	Data(int x);
};

int main()
{
	Data d{9}; // Direct list initialization. 'Data(int x)' constructor is called.
	Data dd{1.3}; // Syntax error due to narrowing conversation.
}
```

> [!note] Note: About brace/ uniform initialization
> Brace/uniform initialization is a comprehensive term that braces are used to initialize. Several initialization methods are named uniform/brace initialization. For example, the uniform/brace initialization that we used to initialize a class object is called '**direct list initialization**'.

> [!note] Note: Note that, all narrowing conversions are syntax errors with brace initialization.

> [!note] Note: Difference between direct initialization and copy initialization
> There is a concept called 'explicit constructor'. We will look at it later. We cannot use copy initialization to initialize an object if its constructor is explicit.

```cpp
class Data{
public:
	explicit Data(int x)
	{
	}
};

int main()
{
	Data d = 9; // Syntax Error. We cannot use copy initialization, because the constructor is 'explicit'.
}
```
# Constructor Initializer List
#Cpp_ConstructorInitializerList

'Constructor initializer list' is a special syntax that initializes the non-static data members of a class.
```cpp
class Data{
public:
	Data()
	: mx(0), my(0) // Constructor initializer list
	{
	}
private:
	int mx, my;
};
```

> [!note] Note: The block of the constructor has to be written even if it is empty when using the constructor initializer list.

> [!note] Note: Before modern C++ (C++11), we didn't use value initialization inside the constructor initializer list. But now, we can use value initialization with the constructor initializer list. #Cpp11

```cpp
class Data{
public:
	Data()
	: mx{}, my{} // Using braces inside the constructor initializer list is OK since the modern C++.
	{
	}
private:
	int mx, my;
};
```

> [!note] Note: Member Initializer List - MIL
> MIL means the constructor initializer list. It is an old term.

> [!warning]  Warning: 
> The data members are default initialized if we don't use the constructor initializer list (in case we do not use other initializer syntax when we declare them). That means the non-static data members will bring to life with garbage values.

```cpp
class Data{
public:
	Data()
	{
	}
private:
	int mx, my; // These variables will contain garbage values when we initialize a class object
};
```

We can assign values to the variables inside the constructor but, it will be different from using the constructor initializer list. The variables are first initialized with default initialization (that means they contain garbage values) then the values are assigned to them inside the constructor.
```cpp
class Data{
public:
	Data()
	{
		mx = 10; // Then the values are assigned to them
		my = 20;
	}
private:
	int mx, my; // First they have garbage values.
```
We were initializing the variables directly when we were using the constructor initializer list. What is the difference? There is no difference in this example. However, there would be a significant efficiency difference if the objects were complex types.

> [!note] Note: The advice is always to initialize all non-static data members of the class with the constructor initializer list if possible (it may not always be possible).

> [!note] Note: The order inside the constructor initializer list doesn't determine the creating order of the variables. Variables are created in order of their declaration order.

> [!example] Example: What is the problem with the following code?
> A: This is undefined behavior. The creating order depends on the declaration order, not the constructor initializer list order. The 'my' will contain a garbage value when the 'mx' is initialized using 'my'.
> #Cpp_Interview_Question 
```cpp
class Data{
public:
	Data()
	:my(5.), mx(my/3)
	{
	}
private:
	int mx, my;
};

int main()
{
	Myclass m; // Undefined behavior.
}
```

> [!note] Note: The advice is to make the initializing order the same as the declaration order.

 > [!note] Note: if the non-static data members of the class are types that cannot be default initialized (const objects, references, etc.), it will be a syntax error if we don't initialize them with the constructor initializer list.
 
 ```cpp
class Data{
public:
	Data()
	{
	}
private:
	const int x; // Syntax error. The const object didn't initialize. (Actually, default initialized)
	int& r; // Syntax error. The reference didn't initialize. (Actually, default initialized)
};

//---
int g {2};

class Data{
public:
	Data()
	: x{10}, r{g}
	{
	}
private:
	const int x; // OK, since it is initialized inside the constructor initializer list.
	int& r; // OK, since it is initialized inside the constructor initializer list.
};
```

> [!note] Note: Names of the data members of the class and names of the parameters can be the same in the constructor initializer list. It doesn't cause a syntax error. Because the names of the data members are searched in the class scope and the names of the parameters are searched in parameters first. However, using different naming conventions for the data members is the general approach to make the member's names different. This can be the usage of the "m" prefix, or "\_" post-fix.

```cpp
class Myclass {
public:
	Myclass(int x, int y) : x{x}, y{y} {} // OK
private:
	int x;
	int y;
};
```

> [!note] Note: But, in the following syntax, the x is assigned to itself and the y is assigned to itself. Because the names aren't found inside the parameters. This means they have garbage values. This is undefined behavior.

```cpp
class Myclass {
public:
	Myclass() : x{x}, y{y} {} // Undefined behavior
private:
	int x;
	int y;
};
```

> [!question] Question: What is the value of x?
> A: The right side of the assignment operator is inside the local scope. The x is assigned to itself in the below example. So the value of x is garbage. This is undefined behavior.
> #Cpp_Interview_Question 

```cpp
int x = 10;

int main()
{
	int x = x; // Undefined behavior. (Not syntax error). "x" is assigned to itself.
}
```

# Default Member Initializer / In-class Initializer
#Cpp_DefaultMemberInitializer
'**Default member initializer**' also called '**in-class initializer**' was added to the language with modern C++ (C++11). If the constructor didn't initialize the non-static data member that was initialized with the default member initializer, the data member will be initialized with the 'default member initializer'. #Cpp11 
```cpp
class Data{
public:
private:
	int mx = 10; // 'Default member initializer' or 'In-class initializer'
};
```
 
 This is very useful when we have more than one constructor.
 ```cpp
class Data{
public:
	Data() : my(0), mz(0) {}
	Data(int a, int b) : my(a), mz(b) {}
private:
	int mx = 0; // 'Default member initializer' or 'In-class initializer'
};
```

> [!warning]  Warning: We cannot use parentheses with the default member initializer. This is a syntax error.

```cpp
class Data{
public:
private:
	int mx = 10; // OK.
	int my{20}; // OK.
	int mz(30); // Syntax ERROR. We cannot use parentheses with the default member initializer.
};
```

# Special Member Function
#Cpp_SpecialMemberFunctions

There are some special functions that the compiler writes the code of these special functions under certain conditions if we don't write them. These functions are called '**special member functions**'. The process by which compilers write these functions is called "to default a special member function". This term wasn't used in old C++, "synthesize" was used instead of "default."

There are 6 special member functions. There were 4 special member functions in old C++. These special member functions are:
- default constructor
- copy constructor
- move constructor (since C++11)
- destructor 
- copy assignment
- move assignment (since C++11)

"Move constructor" and "move assignment" are added to the language with the addition of "**move semantic**" (after C++11). #Cpp11

A special member function can be in three different status. These statuses are:
- Implicitly declared
- User declared
- Not declared
## Implicitly Declared Special Member Functions
Special member functions can be "**implicitly declared**". This means the compiler writes them if we don't write one.
```cpp
class Myclass{

};

int main()
{
	Myclass x; // All six special member functions are declared implicitly.
}
```
If we write a constructor that takes a parameter, the compiler doesn't write the default constructor. This means a situation to call the default constructor would be a syntax error.
```cpp
class Myclass{
public:
	Myclass(int x); // We wrote a constructor so, the compiler won't write the default constructor
};

int main()
{
	Myclass x; // Syntax error because there is no default constructor.
}
```

## User-declared Special Member Functions
Special member functions can be written by the user. This is called "**user-declared**". Being user-declared is possible in three ways.
### User Declared
The user declares the constructor.
```cpp
class Myclass{
public:
	Myclass(); // user-declared (can be declared inline too)
};
```

### Used Declared but Defaulted
The user declares the constructor and demands to write the constructor by the compiler. This feature is added with modern C++. We use the "default" keyword to make this declaration. The "default" keyword is not related to the usage of it in the case syntax (so the "default" keyword is overloaded). #Cpp11
```cpp
class Myclass{
public:
	Myclass() = default; // user-declared, but we make a demand to the compiler to write the constructor.
};
```

> [!note] Note: Overloaded "default" keyword. #Cpp_keyword_default
> The "default" keyword is overloaded. Overloads are:
> - In case syntax
> - defaulting a special member function

> [!note] Note: The rules for how the compiler writes the constructor code are determined by the language and we will look at them.

> [!warning]  Warning: This "default" syntax is only valid for the special member functions of the class.

> [!note] Note: The count of functions that can be defaulted is increased in C++20. #Cpp20

### User Declared but Deleted
Deleting a member function of a class (or deleting a global function). This was added to the language after modern C++. We use the "delete" keyword to delete a function. This syntax is applicable to any function (global functions, member functions). #Cpp11
```cpp
void func(int) = delete;

int main()
{
	func(12); // Syntax error. The function is deleted.
}
```

> [!note] Note: Deleted Function. #Cpp_keyword_delete
> A deleted function is accepted exists. But when a call is made to it is a syntax error. Deleting a function is useful in function overload resolution. First, the function overload resolution is done, after that, a check to the selected function is made if it is deleted or not
> #Cpp_FunctionOverloadResolution 

```cpp
void func(int);
void func(double) = delete;
void func(long);
void func(char);

int main()
{
	func(1.2); // Syntax error. The function with the double argument is selected in function overload resolution. But the selected function is a deleted function.
}
```

> [!note] Note: Overloaded "default" keyword
> The "default" keyword is overloaded. Overloads are:
> - Deleting a function
> - Freeing a dynamically allocated memory

A special member function can be deleted.
```cpp
class Myclass{
public:
	Myclass() = delete; // Deleted default constructor.
	Myclass(int);
};

int main()
{
	Myclass m(12); // OK.
	Myclass m2(); // Syntax ERROR. The default constructor is deleted.
}
```
Deleting a default constructor is not a case that we encounter frequently.

## Not Declared Special Member Function
If we declare a constructor with parameters, the compiler doesn't implicitly declare the default constructor. The default constructor is "not declared" in this case.

# Default Constructor - Special Member Function
#Cpp_DefaultConstructor
The compiler writes the default constructor if it sees no constructor is written to the class. In this case, the constructor is in status "implicitly declared". How the compiler produces code for the special member functions was determined by the standards. That means this does not change depending on the compiler.

If the data members of the class aren't subjected to a special process when the life span of the class object ends, there isn't any drawback to writing the destructor by the compiler. The ideal approach is writing the special member functions by the compiler if we no need any special process.

The rule for the default constructor is that 
- the compiler writes the default constructor non-static, public, and inline for the class.
- the default constructor makes default initialize to all data members of the class. (default initialization to primitive types concludes with garbage value)

> [!question] Question: What are the values of "mx" and "my"?
> A: Since a constructor is not written to the class, the default constructor, the special member function of the class, is in status "implicitly declared". That means the compiler writes a constructor to the class. The constructor is the defaulted constructor by the compiler. Since the constructor written by the compiler initialized the data member of the class, the "mx" and "my" have garbage values. This can cause undefined behavior.

```cpp
class Myclass{
public:

private:
	int mx, my;
};

int main()
{
	Myclass x;
}
```

**What does the compiler do if a special member function that the compiler writes causes a syntax error?**
When the compiler makes the default of a special member function of the class if a situation occurs that causes a syntax error because of any reason the compiler doesn't throw a syntax error, however, makes the defaulted special member function deleted.

The syntax error doesn't occur until we make a call to the deleted function

```cpp
class Myclass{
public:
	// The compiler deletes the constructor: Myclass() = delete;
private:
	const int mx; // Compiler deleted the constructor because of this const data member
	int mx&; // Compiler deleted the constructor
};

int main()
{

}
```
There is no syntax error in the upper example. The compiler deletes the default constructor because the default initialization of a const variable causes a syntax error.

```cpp
class Myclass{
public:

private:
	const int mx; // Compiler deleted the constructor
	int mx&; // Compiler deleted the constructor
};

int main()
{
	Myclass m; // Syntax error, because we make a call to the deleted function
}
```
The upper example gets a syntax error because we make a call to the deleted constructor. What kind of error message does the compiler throw in this situation? The error message is like that: " 'Myclass::Myclass(void)': attempting to reference a deleted function ". It is hard to make sense of this error message if we don't know what is going on. #Cpp_Interview_Question 

> [!question] Question: Is there a syntax error?
> Q1: Is there a syntax error in example 1?
> A1: No. The constructor of B makes a call for the constructor of A to default initialize it. But this call causes a syntax error due to access control because the constructor of A is private. Since the constructor of A causes a syntax error, the constructor of B is deleted. However, there is no syntax error here because we didn't make a call to the constructor of B.
> Q2: Is there a syntax error in example 2?
> A2: Yes. Because we made a call to the constructor (which is deleted) of B.
> #Cpp_Interview_Question 

```cpp
// Example 1
class A {
private:
	A();
};

class B {
private:
	A ax;
};

// Example 2
class A {
private:
	A();
};

class B {
private:
	A ax;
};

int main()
{
	B bx; // Syntax error because the default constructor of B is deleted.
}
```


> [!question] Question: Is there a syntax error?
> Q1: Is there a syntax error in example 1?
> A1: No. The constructor of B makes a call for the constructor of A to default initialize it. But this call causes a syntax error because A doesn't have a default constructor. Since the constructor of A causes a syntax error, the constructor of B is deleted. However, there is no syntax error here because we didn't make a call to the constructor of B.
> Q2: Is there a syntax error in example 2?
> A2: Yes. Because we made a call to the constructor (which is deleted) of B.
> #Cpp_Interview_Question 

```cpp
// Example 1
class A {
public:
	A(int);
};

class B {
private:
	A ax;
};

// Example 2
class A {
public:
	A(int);
};

class B {
private:
	A ax;
};


int main()
{
	B bx; // Syntax error because the default constructor of B is deleted.
}
```

> [!warning]  Warning: Deleting the default constructor by the compiler is not only valid with implicitly declared, it is also valid when the default constructor is user-declared but defaulted by the user.

```cpp
class A {
public:
	A(int);
};

class B {
public:
	B() = default; // This is deleted due to syntax error (A doesn't have a default constructor)
private:
	A ax;
};
```

> [!important] Important: Deleting the default constructor by the compiler is valid for all special member functions. A special member function that is written by the compiler but takes syntax errors will be deleted by the compiler.

> [!important] Important: The importance of the "default member initializer" also called "in-class initializer" appears when the compiler is writing the default constructor. The constructor that is written by the compiler initializes the variables with the values mentioned with the default member initializer syntax if a default member initializer syntax is provided. That means a default initializer isn't used in this situation. #Cpp_DefaultMemberInitializer 

```cpp
class A {
public:

private:
	int mx = 10;
	int my = 20;
};

int main()
{
	A ax; 
}
```

# Destructor - Special Member Function
#Cpp_Destructor
 If we don't write a destructor, the compiler writes one. This destructor doesn't make a special operation, the data members of the class are destroyed. There is no drawback to writing the destructor by the compiler if no need to subject the data members of the function to a special operation end of the life span of the class object. 

The destructor that is written by the compiler is the non-static, public, inline member function of the class and doesn't have any code. If the data members of the class are types of other types, the destructor for these types is called in reverse order that they are created in the destructor of the class.

# Copy Constructor- Special Member Function
#Cpp_CopyConstructor
First, let's examine the following code.
```cpp
class Myclass {
public:
	Myclass()
	{
		std::cout << "default dtor " << this << "\n";
	}
	~Myclass()
	{
		std::cout << "default dtor " << this << "\n";
	}
};

void foo(Myclass m)
{
	std::cout << "in foo\n";
}

int main()
{
	Myclass x;

	foo(x);
}
```

The output of this example will be:
```
default ctor 0055FF00AA
in foo
default dtor 0055FFBBCC
default dtor 0055FF00AA
```

When we call the function "foo", a constructor is called to create the Myclass object which is a parameter of the function. This constructor is not the default constructor, this is the copy constructor.

The **copy constructor** is another special member function. The compiler wrote a copy constructor in the upper example and this copy constructor was called when we made a call to the "foo" function. When we call the "foo" function the parameter will bring to life and the parameter takes its value from a variable that is the same type. The copy constructor will called in this case regarding the language rules (instead of the constructor).

When the program flow came to the end of the "foo" function, the Myclass object that was created with the copy constructor was destroyed by making a call to the destructor. Because the life span of it ends at the end of the function "foo".

> [!note] Note: The copy constructor is abbreviated as "cc". For example, in "stackoverflow".

**When is a copy constructor called?**
The copy constructor is called when a class object is brought to life with another class object that is the same type. When does this happen?
- Initialization with another object
```cpp
class Myclass {
	
};

int main()
{
	Myclass m1;
	Myclass m2 = m1; // The copy constructor will be called.
	Myclass m3(m1); // The copy constructor will be called.
	Myclass m4{m1}; // The copy constructor will be called.
	auto m5{m1}; // The copy constructor will be called.
}
```

- Call-by-value function calls
```cpp
class Myclass {
	
};

void foo(Myclass);

int main()
{
	Myclass m1;

	foo(m1); // The copy constructor is called for the parameter variable of "foo"
}
```

- If a function return type is a class type (If the compiler doesn't do an optimization)
```cpp
class Myclass {
	
};

Myclass foo();

int main()
{
	Myclass m1;

	m1 = foo(); // The copy constructor is called for the return variable of "foo"
}
```

The copy constructor written by the compiler can't do the job or can cause undefined behavior in some cases. We write it ourselves in these cases. But we encounter these cases very rarely.

**How does the compiler write the copy constructor in case it is declared implicitly?**
The copy constructor that is written by the compiler has to be non-static, public, and inline. It doesn't have a return value, and its parameter is a const reference in the type of class.
```cpp
class Myclass {
public:
	Myclass(const Myclass& other) : tx(other.tx), ux(other.ux), mx(other.mx) {} // Representation of the copy constructor written by the compiler
private:
	T tx;
	U ux;
	M mx;
}
```
That means if the type T, U, or M are a class type, the copy constructor of these is called too.

The copy constructor copies the data members of the objects mutually. This is called "**member-wise copy**" or "**shallow copy**". 

**RAII - Resource Acquisition Is Initialization** #Cpp_Terms 
When we create a class object, resources are acquired. This resource can be a memory area (for example heap), a file, a mutex, or a database connection. The resource is given back when the life of the object ends. If we don't give back the resource, this will cause a "**resource leak**". The constructor acquires the resource and the destructor gives back the resource.

**When we write the copy constructor?**
If one or more data members of our class are a pointer or a reference, the copy constructor written by the compiler copies their addresses. So it makes shallow copies of pointers. This means there will be one instance of the object that pointed. Their data is not copied. The destructor frees the area. If we do a shallow copy on a pointer object, the second class object can free the area while the pointer in the other class is valid. So, What will happen if the first object uses this area? This is an undefined behavior. We have to write the copy constructor ourselves in this situation.

> [!example] Example: Implicitly declared copy constructor resource leak example:

```cpp
// sentence.h
#include <cstddef>

class Sentence {
public:
	Sentence(const char* p);
	~Sentence();
	void print()const;
	std::size_t length()const;

private:
	char* mp;
	std::size_t mlen;
};

// sentence.cpp
#include <cstring>
#include <iostream>
#include "sentence.h"

Sentence::Sentence(const char* p) : mlen(std::strlen(p)), mp(static_cast<char*>(std::malloc(mlen+1)))
{
	if(!mp) {
		std::cout << "malloc failure\n";
		exit(EXIT_FAILURE);
	}
	std::cout << "Sentence(const char *)\n";
	std::cout << "Address of the allocated area: " << (void *)mp << "\n";
	std::strcpy(mp, p);
}

Sentence::~Sentence()
{
	std::cout << "~Sentence()\n";
	std::cout << "Freeing area, address: " << (void *)mp << "\n";
	free(mp);
}

void Sentence::print() const
{
	std::cout << "[" << mp << "]\n";
}

std::size_t Sentence::length() const
{
	return mlen;
}

// main.cpp
#include <iostream>

void func(Sentence s) // Implicitly declared copy constructor is called
{
	std::cout << "func is called\n";
	s.print();
}

int main()
{
	using namespace std;

	Sentence s1 {"Hello message"};

	s1.print();
	cout << "length: " << s1.length() << "\n";

	func(s1); // Destructor frees the memory (Destructor is called when exiting foo)

	s1.print(); // Undefined Behavior. The pointer member of the object is a dangling pointer now.
}
```

We can deduce the meaning of if we are writing the destructor (instead of writing by the compiler), we are probably freeing one or more resources, thus we should write the copy constructor too (and the copy assignment, we will look it).

But we have another option instead of writing the copy constructor. We can prohibit the object copy. We can delete the copy constructor of the class.

We write the copy constructor to solve this issue. (only added part is given in below example)
```cpp
// sentence.h
#include <cstddef>

class Sentence {
public:
	Sentence(const char* p);
	~Sentence();
	Sentence(const Sentence& other); // Copy constructor added
	void print()const;
	std::size_t length()const;

private:
	char* mp;
	std::size_t mlen;
};

// sentence.cpp

// Copy constructor added
Sentence::Sentence(const Sentence& other) : mlen(other.mlen), mp(static_cast<char*>(std::malloc(mlen + 1)))
{
	if(!mp) {
		std::cout << "malloc failure\n";
		exit(EXIT_FAILURE);
	}
	std::cout << "Sentence(const Sentence&)\n";
	std::cout << "Address of the allocated area: " << (void *)mp << "\n";
	std::strcpy(mp, other.mp);
}
```  

---
# Terms 
- direct list initialization
- Constructor Initializer List
- Member Initializer List - MIL
- Default member initializer / In-class initializer
- special member functions
- move semantic
- default constructor
- copy constructor
- copy assignment
- destructor
- move constructor
- move assignment
- delete keyword
- default keyword
- member-wise copy / shallow copy
- resource leak
---
Return: [[00_Course_Files]]